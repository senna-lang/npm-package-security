# 第4層: 自動更新 (Version Updates)

**目的**: 定期更新で健全性を維持(予防)

### `.github/dependabot.yml`

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    schedule:
      interval: "weekly"
```

### メリット
- 技術的負債の解消
- 脆弱性公表前の更新 (予防効果)
