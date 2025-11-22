# npmエコシステムにおけるセキュリティ対策

## 発表概要

- 発表時間: 10分
- テーマ: 依存パッケージのセキュリティリスクと多層防御アプローチ

---

## 背景: 依存パッケージのセキュリティインシデント

### インシデントの発生パターン

#### 1. 既存パッケージへの脆弱性発見

すでに広く使われているパッケージに、後から脆弱性が発見されるケース。prototype pollutionやReDoSなど。`npm audit`で検出できるタイプ。

#### 2. サプライチェーン攻撃（悪意あるコードの混入）

- **アカウント乗っ取り**: メンテナのnpmアカウントが侵害され、マルウェア入りバージョンが公開される（例: `ua-parser-js`、`event-stream`事件）
- **タイポスクワッティング**: `lodash`に似た`1odash`のような偽パッケージを公開
- **依存関係の乗っ取り**: 放棄されたパッケージのメンテナンス権を取得して悪用

#### 3. 意図的なサボタージュ

メンテナ自身が意図的に破壊的変更を加えるケース（`colors.js`、`faker.js`事件など）

---

## 多層防御アプローチ

4つの層を組み合わせて、依存パッケージのセキュリティリスクに対処する。

---

## 第1層: バージョン固定 (Pinning + Lockfile)

### 目的

意図しない依存パッケージの自動更新による不具合やサプライチェーン攻撃を防ぐ。

### 実装内容

#### .npmrc による自動バージョン固定

```
save-exact=true
```

この設定により、パッケージインストール時に自動的にバージョンが固定される。

**動作例:**
```bash
npm install lodash@^4.17.0
```

範囲指定 `^4.17.0` でインストールしても、その時点で解決された具体バージョン（例: `4.17.21`）が `package.json` に記録される。範囲指定はバージョン解決にのみ使われ、保存時には `^` なしの厳密なバージョンになる。

#### package.json のバージョン指定

```json
// NG: 範囲指定（自動更新される）
"lodash": "^4.17.0"

// OK: 厳密固定
"lodash": "4.17.21"
```

#### package-lock.json の役割

- 直接依存だけでなく、**間接依存（依存の依存）も含めて**全てのバージョンを記録
- `npm install`時に同じバージョンツリーを再現
- integrity（ハッシュ）も記録されており、パッケージの改ざんを検出可能

### 運用ポイント

- `package-lock.json`は必ずコミットする
- CI環境では`npm ci`を使う（lockfileを厳密に再現）

### この層が防ぐ攻撃

- アカウント乗っ取りによる悪意あるバージョンの自動取り込み
- 意図しないbreaking changeの混入

---

## 第2層: 脆弱性検知 (Dependabot Alerts)

### 目的

固定したバージョンに後から脆弱性が見つかったときに即座に検知する。

### 仕組み

```
GitHub Advisory Database（脆弱性DB）
        ↓ 照合
package-lock.json
        ↓ 検出
Dependabot Alert発行 → 通知（GitHub/メール/Slack）
```

### 設定方法

リポジトリのSettings > Code security and analysisで以下を有効化:

- Dependabot alerts: ON
- Dependabot security updates: ON
- 通知先設定（チームで受け取る設定推奨）

### アラートの見方

- Severity: Critical / High / Medium / Low
- 影響を受けるパッケージと修正バージョン
- CVE番号、攻撃経路の説明

### 実装内容

#### .github/SECURITY.md

```markdown
# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| latest  | :white_check_mark: |

## Reporting a Vulnerability

Please open an issue or contact the maintainer directly.
```

#### README バッジ

```markdown
![GitHub Dependabot](https://img.shields.io/badge/dependabot-enabled-brightgreen)
```

### この層が防ぐこと

- 「知らないうちに脆弱なまま放置」を防止
- 第1層でPinningしていても、**後から発覚した脆弱性**に対応できる

---

## 第3層: CIによるガード (CI Security Audit)

### 目的

脆弱性を含む依存パッケージのマージを未然に防ぐ（ゲートキーパー）。

### GitHub Actions設定例

`.github/workflows/security-audit.yml`:

```yaml
name: Security Audit

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]
  schedule:
    # 毎週月曜 9:00 JST (日曜 24:00 UTC)
    - cron: '0 0 * * 1'

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run security audit
        run: npm audit --audit-level=high

      - name: Check for known vulnerabilities
        run: npm audit --json > audit-results.json || true

      - name: Upload audit results
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: audit-results
          path: audit-results.json
          retention-days: 30
```

### --audit-level の選択肢

| レベル | 意味 |
|--------|------|
| `low` | 全ての脆弱性で失敗 |
| `moderate` | moderate以上で失敗 |
| `high` | high以上で失敗（推奨）|
| `critical` | criticalのみで失敗 |

### Dependabot Alertsとの違い

| 観点 | Dependabot Alerts | CI npm audit |
|------|-------------------|--------------|
| タイミング | 常時監視 | PR作成時 |
| 強制力 | 通知のみ | ビルド失敗でマージブロック |
| 対象 | mainブランチ | PRの変更内容 |

### この層が防ぐこと

- 開発者がアラートを見逃してマージしてしまう
- 新しく追加した依存が脆弱だった場合

---

## 第4層: 自動更新 (Dependabot Version Updates)

### 目的

依存パッケージを定期的に更新し、脆弱性が発生する前に最新状態を保つ（予防的メンテナンス・技術的負債の解消）。

### 設定ファイル

`.github/dependabot.yml`:

```yaml
version: 2
updates:
  # npm パッケージ
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
      time: "09:00"
      timezone: "Asia/Tokyo"
    open-pull-requests-limit: 10
    groups:
      dev-dependencies:
        dependency-type: "development"
        update-types:
          - "minor"
          - "patch"
      production-dependencies:
        dependency-type: "production"
        update-types:
          - "minor"
          - "patch"
    commit-message:
      prefix: "deps"
      include: "scope"
    labels:
      - "dependencies"
      - "automated"

  # GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
      time: "09:00"
      timezone: "Asia/Tokyo"
    commit-message:
      prefix: "ci"
    labels:
      - "ci"
      - "automated"
```

### Security Updates vs Version Updates

| 観点 | Security Updates | Version Updates |
|------|------------------|-----------------|
| トリガー | 脆弱性検出時 | スケジュール |
| 対象 | 脆弱なパッケージのみ | 全ての古い依存 |
| 有効化 | Settings画面でON | dependabot.yml |

### 運用Tips

- `groups`で関連パッケージをまとめてPRを減らす
- `open-pull-requests-limit`でPR洪水を防ぐ
- dev依存とprod依存で優先度を分ける

### この層の効果

- 古いバージョンに留まり続けるリスクを低減
- 脆弱性が公開される前に更新されていることも
- 「技術的負債」の蓄積防止

---

## まとめ: 4層の連携

```
[第1層] バージョン固定 (Pinning + Lockfile)
    → 土台。意図しない変更を防ぐ
    
[第2層] 脆弱性検知 (Dependabot Alerts)
    → 監視。固定したバージョンの脆弱性を検知
    
[第3層] CIによるガード (CI Security Audit)
    → 門番。脆弱な状態でのマージを阻止
    
[第4層] 自動更新 (Dependabot Version Updates)
    → 予防。定期更新で健全性を維持
```

### なぜ多層防御が必要か

- 第1層だけでは、固定したバージョン自体の脆弱性に気づけない
- 第2層だけでは、通知を見逃す可能性がある
- 第3層だけでは、既存コードの脆弱性は検知できない
- 第4層だけでは、更新PRがマージされなければ意味がない

**各層が互いの弱点を補完し合う構成が重要**

---

## 運用手順

### 脆弱性が検知された場合

1. **CIの失敗またはアラート通知**で脆弱性を検知。
2. ローカルで `npm audit` を実行し、詳細を確認。
3. **対応方法**:
   - **直接の依存の場合**: `npm update <package>` で修正されたバージョンに更新。
   - **間接依存（推移的依存）の場合**:
     - 親パッケージを更新できないか検討。
     - 更新できない場合、`npm audit fix --force` を試す（breaking changeの可能性あり）。
     - または `overrides` フィールドで強制的に解決する（npm 8.3+）。
     ```json
     "overrides": {
       "vulnerable-package": "^1.2.3"
     }
     ```
4. `npm audit` が通ることを確認してコミット。

### 新しいパッケージを追加する場合

1. 通常通り `npm install <package>` を実行。
2. `.npmrc` の `save-exact=true` 設定により、自動的に固定バージョンで `package.json` に保存される。
3. `package-lock.json` も更新されるので、合わせてコミットする。

---

## 参考リンク

- [npm audit documentation](https://docs.npmjs.com/cli/v10/commands/npm-audit)
- [GitHub Dependabot documentation](https://docs.github.com/en/code-security/dependabot)
- [GitHub Advisory Database](https://github.com/advisories)
