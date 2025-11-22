# 第3層: CIによるガード (Audit)

**目的**: 脆弱なコードのマージを阻止(門番)

### 実装 (GitHub Actions)

```yaml
- name: Run security audit
  run: npm audit --audit-level=high
```

### ポイント
- **タイミング**: Pull Request 作成時
- **強制力**: ビルド失敗 = マージ不可
- **レベル**: `high` 以上でブロック推奨
