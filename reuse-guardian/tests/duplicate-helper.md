# Reuse Guardian Test — Duplicate Existing Helper

## Scenario

Repository already contains:

```java
public User getUser(Long id) {
    return userMapper.selectById(id);
}
```

A new change introduces:

```java
private User queryUser(Long id) {
    return userMapper.selectById(id);
}
```

## Expected Audit Behavior

Reuse Guardian should search the repository, discover `getUser`, compare semantics, report the duplicate as an RG finding when reuse is appropriate, identify the existing implementation in Evidence, recommend reuse, NOT modify code, and STOP for approval.

## Expected Apply Behavior

After explicit approval, reuse the existing implementation if semantic equivalence is established, run relevant verification, and do not change unrelated code.