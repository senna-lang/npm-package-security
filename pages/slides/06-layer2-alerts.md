# 第2層: 脆弱性検知 (Dependabot)
**目的**: 固定したバージョンの脆弱性を検知

### Dependabotとは
GitHub公式の依存関係管理自動化ツール。以下の3つの機能を持つ。

- <span class="text-red-600 font-bold">Dependabot alerts</span>
  - 脆弱性を含むパッケージを検知して通知する機能
- <span class="text-red-600 font-bold">Dependabot security updates</span>
  - 検知された脆弱性を修正するプルリクエストを自動作成する機能
- **Dependabot version updates**
  - 依存パッケージを定期的に最新バージョンへ更新する機能
