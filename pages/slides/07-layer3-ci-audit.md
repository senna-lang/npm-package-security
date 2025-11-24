# 第3層: CIによるガード (Audit)
**目的**: 脆弱性を含むパッケージの混入を未然に防ぐ

```yaml
- name: Run security audit
  run: npm audit --audit-level=high
```

<div class="my-8"></div>

### 対応
- **PR作成時にCIで検証**
  - 新しく追加された依存パッケージをチェック
- **定期的なAudit実行**
  - 既存の依存パッケージの脆弱性をチェック
