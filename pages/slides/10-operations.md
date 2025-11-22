# 運用手順

### 脆弱性検知時
1. **検知**: CI失敗 / アラート
2. **調査**: `npm audit`
3. **対応**:
   - `npm update`
   - `overrides` (解決できない場合)

### 新規追加時
- `npm install` → 自動的に固定バージョン (`save-exact=true`)
- `package-lock.json` をコミット
