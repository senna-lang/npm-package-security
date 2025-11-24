# 第3層: CIによるガード (Audit)
**目的**: 脆弱性を含むパッケージの混入を未然に防ぐ

```yaml
- name: Run security audit
  run: npm audit --audit-level=high
```

<div class="my-8"></div>

<div class="grid grid-cols-2 gap-4">

<div>

### 対応
- **PR作成時にCIで検証**
  - 新しく追加された依存パッケージをチェック

</div>

<div class="flex items-center">
  <img src="/npm-audit-output.png" class="h-60 rounded shadow-lg" />
</div>

</div>

