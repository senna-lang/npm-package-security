# Dependabotの動作フロー

<div class="flex flex-col items-center justify-center h-full text-base">

<div class="bg-gray-100 dark:bg-gray-800 p-2 rounded-lg w-full max-w-xl text-center">
  1. リポジトリをスキャン
</div>

<div class="text-xl my-1">↓</div>

<div class="bg-gray-100 dark:bg-gray-800 p-2 rounded-lg w-full max-w-xl text-center">
  2. package.json と package-lock.json を解析
</div>

<div class="text-xl my-1">↓</div>

<div class="bg-gray-100 dark:bg-gray-800 p-2 rounded-lg w-full max-w-xl text-center">
  3. GitHub Advisory Database と照合
</div>

<div class="text-xl my-1">↓</div>

<div class="bg-red-100 dark:bg-red-900/30 p-2 rounded-lg w-full max-w-xl text-center border-2 border-red-500">
  4. 脆弱性 or 新バージョンを検知
</div>

<div class="text-xl my-1">↓</div>

<div class="bg-green-100 dark:bg-green-900/30 p-2 rounded-lg w-full max-w-xl text-center border-2 border-green-500 font-bold">
  5. Pull Request を自動作成
</div>

</div>
