# Reuse Guardian

**一个为 AI 编程 Agent 设计的、带人工审批门禁的代码复用与简化审计 Skill。**

[English](README.md)

> **积极搜索，对抗性审计；未经明确人工批准绝不修改；用证据完成验证。**
>
> Search aggressively. Audit adversarially. Modify only with explicit human approval. Verify with evidence.

Reuse Guardian 用于保护已有代码库，避免 AI 编程 Agent 重复实现已有能力、制造不必要抽象、引入第二套代码模式，或在“简化代码”的过程中破坏原有语义。

它与自动 `code simplifier` 最大的区别是：**Audit First（审计优先）**。第一次运行严格只读。Agent 必须先搜索代码库、输出有证据的审计报告，然后停止。只有用户明确批准具体问题 ID 后，才允许修改代码。

## 为什么需要 Reuse Guardian？

AI 编程 Agent 很擅长快速产出能够工作的代码，但也容易让代码库不断膨胀，例如：

- 已经存在 helper，却重新创建一个；
- 给已有 API 再包装一层，但没有增加任何业务语义；
- 在已有架构旁边重新发明第二套模式；
- 因为两段代码长得相似，就抽象出错误的公共方法；
- 为了“减少方法数量”而破坏清晰的领域边界；
- 为了架构看起来更整洁，改变真正的权威数据源。

Reuse Guardian 在“实现完成”和“允许继续修改”之间增加一道明确的人工审查门禁。

## 工作流

```text
AUDIT（只读审计）
  ↓
REPORT（RG-001、RG-002、KEEP-001...）
  ↓
STOP —— 必须人工审批
  ↓
只 APPLY 明确批准的问题
  ↓
使用最新证据 VERIFY
  ↓
FINAL REPORT
```

### 1. Audit

Agent 检查当前 diff，并主动搜索现有代码库，寻找：

- 可以复用的已有实现；
- 重复业务逻辑；
- 无意义 wrapper/helper；
- 不必要抽象；
- 与项目既有模式不一致的新实现；
- 错误的抽象合并；
- 权威数据源被本地状态替代等风险。

每个可执行问题获得稳定 ID，例如 `RG-001`。合理且应该保留的新抽象可以记录为 `KEEP-001`，避免审计器陷入“既然叫简化，就必须删代码”的偏见。

**Audit 阶段禁止修改生产代码、测试、配置以及任何其他文件。**

### 2. Human Approval

报告完成后 Agent 必须停止。由你明确决定哪些问题允许修改：

```text
批准 RG-001、RG-003。
```

没有批准的问题继续保持只读。批准某一个问题，也不代表允许 Agent 顺手整理附近代码。

### 3. Apply

尽可能逐项执行已批准的问题。存在行为风险的修改默认采用 TDD。能够证明是纯机械、行为保持的修改可以不强制新写失败测试，但必须说明理由，而且仍然要运行相关已有测试。

### 4. Verify

验证至少检查：

- 是否超出批准范围；
- 目标行为是否正确；
- 更大范围是否出现回归；
- 项目支持的 build/typecheck/lint/static analysis；
- 最终 semantic diff；
- 权威数据源是否仍然保持不变。

没有最新证据，就不能声称“已经完成”“测试通过”或“行为没有变化”。

## 核心原则

- **Reuse before creating。** 新建函数、类、helper、utility、wrapper、query 或抽象之前先搜索代码库。
- **不以方法数和代码行数作为优化目标。** 有意义的领域概念和边界应该保留。
- **DRY 不是绝对目标。** 少量重复优于错误抽象。
- **遵循已有项目。** 优先沿用既有模式，而不是创建理论上更漂亮的第二套架构。
- **尊重权威数据源。** 不要为了简化代码，用本地推断替代必须以平台、服务器、数据库或 SDK 返回值为准的状态。
- **默认保守。** 无法证明语义等价时，报告不确定性，而不是强行复用。
- **未经批准不修改。** 审计和执行必须明确分离。

## 审计报告

每个问题至少包含：

```text
ID
Severity（严重程度）
Confidence（置信度）
Category
Location
Finding
Evidence
Existing implementation（如适用）
Recommendation
Risk / trade-off
Proposed change scope
```

Severity 与 Confidence 必须独立。例如 `High Severity + Low Confidence` 表示：如果判断成立会很严重，但当前证据不足，不能贸然执行。

## 安装

Clone 仓库，然后把 `reuse-guardian` 目录复制到 Agent Skills 目录：

```bash
git clone https://github.com/LewLIu/reuse-guardian-skill.git
cp -r reuse-guardian-skill/reuse-guardian ~/.agents/skills/
```

安装后：

```text
~/.agents/skills/
└── reuse-guardian/
    ├── SKILL.md
    ├── references/
    └── tests/
```

然后可以对 Coding Agent 说：

```text
使用 reuse-guardian 审计当前未提交的修改。
```

正确的第一次调用应该是：搜索代码库 → 输出 RG/KEEP 报告 → 明确说明没有修改代码 → 停止并等待审批。

## 推荐的全局 Agent 规则

可以在全局或项目级 Agent 指令中加入一小段：

```markdown
完成非简单的功能开发、Bug 修复或重构后，在最终完成前使用
`reuse-guardian` skill。

第一次审计严格只读。输出审计报告后停止，等待用户明确批准具体的
RG finding ID。只有获批问题允许修改。存在行为风险的修改默认采用
TDD。最终声称完成前必须执行最新验证。
```

不要把整个 Skill 内容重复塞进全局规则，完整工作流应由 Skill 自身维护。

## 仓库结构

```text
reuse-guardian-skill/
├── README.md
├── README.zh-CN.md
├── LICENSE
└── reuse-guardian/
    ├── SKILL.md
    ├── references/
    │   ├── audit-protocol.md
    │   ├── apply-protocol.md
    │   └── verification-protocol.md
    └── tests/
        ├── duplicate-helper.md
        ├── thin-wrapper.md
        ├── justified-method.md
        ├── wrong-abstraction.md
        └── existing-pattern.md
```

## TDD 策略

Reuse Guardian 不要求所有机械性清理都为了形式强行 TDD。

当修改可能影响业务规则、校验、异常、状态推导、持久化、API 处理、异步行为、副作用，或者无法确定新旧实现完全等价时，默认采用 TDD。

如果能够证明只是机械性的行为保持修改，可以不新增失败测试，但最终报告必须解释为什么不需要 TDD，并运行相关已有测试。

## License

MIT License，详见 [LICENSE](LICENSE)。

## 项目状态

Reuse Guardian 有意采用保守策略。一次完全正确的审计结果可以是：

```text
No actionable findings. No simplification needed.
```

欢迎贡献真实失败案例，尤其是 AI Agent 重复实现已有能力、过度抽象，或者在简化过程中破坏业务语义的案例。