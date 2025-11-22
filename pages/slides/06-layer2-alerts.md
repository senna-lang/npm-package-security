# 第2層: 脆弱性検知 (Alerts)

**目的**: 固定したバージョンの脆弱性を即座に検知(監視)

### 仕組み
`GitHub Advisory DB` ⇄ `package-lock.json`

### 設定 (GitHub)
- Dependabot alerts: **ON**
- Dependabot security updates: **ON**

### Output
- 通知 (Slack/Email)
- READMEバッジ: `![Dependabot](...)`
