# 第1層: バージョン固定 (Pinning)

**目的**: 意図しないバージョンの変更を防ぐ

<div class="grid grid-cols-2 gap-4">

<div>

### 対応
- `package.json`:  バージョンの範囲指定をしない
  `❌"^3.5.24", ⭕️"3.5.24"`
- `.npmrc`: `save-exact=true`
- `package-lock.json`: **必ずコミット**

</div>

<div>

### 効果
- **意図しない破壊的変更の防止**
- **悪意あるバージョンの自動混入を阻止**
- **開発・CI環境の完全な再現性**

</div>

</div>
