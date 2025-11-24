# 第3層: 依存関係レビュー (Dependency Review)

### Dependency Reviewとは
- PRで**変更された依存関係のみ**をスキャンする機能
- 脆弱性だけでなくライセンス違反もチェック可能

<div class="flex my-4">
  <img src="/dependency-review.png" class="h-40 rounded shadow-lg" />
</div>


<div class="my-8"></div>

### npm audit との違い
- **npm audit**: プロジェクト全体の脆弱性をチェック
- **Dependency Review**: **今回追加・変更された分**の脆弱性をチェック
