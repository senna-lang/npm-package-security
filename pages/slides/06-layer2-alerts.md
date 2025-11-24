# 第2層: 脆弱性検知 (Dependabot)
**目的**: 固定したバージョンの脆弱性を検知

### Dependabotとは
GitHub公式の依存関係管理自動化ツール。以下の3つの機能を持つ。

- **Dependabot alerts**
  - 脆弱性を含むパッケージを検知して通知する機能
- **Dependabot security updates**
  - 検知された脆弱性を修正するプルリクエストを自動作成する機能
- **Dependabot version updates**
  - 依存パッケージを定期的に最新バージョンへ更新する機能

<div class="mt-8">
  <strong>👉 この層では Alerts と Security updates を有効化します</strong>
</div>
