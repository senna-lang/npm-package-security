# 第1層: バージョン固定 (Pinning)

**目的**: 意図しない変更・攻撃を防ぐ(土台)

<div class="grid grid-cols-2 gap-4">

<div>

### 実装
- `.npmrc`: `save-exact=true`
- `package.json`: 厳密固定 (`"4.17.21"`)
- `package-lock.json`: **必ずコミット**

</div>

<div>

### 効果
- 自動更新による不具合防止
- サプライチェーン攻撃のリスク低減
- `npm ci` で再現性確保

</div>

</div>
