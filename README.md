## プロジェクト作成

```
npx nuxi init shinsen-tools
cd shinsen-tools
npm install
```

## ローカル実行

```
npm run dev
```

## eslint Prettier の導入

### eslint 導入

```
npx eslint --init

✔ How would you like to use ESLint? · problems
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · vue
✔ Does your project use TypeScript? · No / Yes
✔ Where does your code run? · browser
✔ What format do you want your config file to be in? · JavaScript
Local ESLint installation not found.
The config that you've selected requires the following dependencies:

eslint-plugin-vue@latest @typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest eslint@latest
✔ Would you like to install them now? · No / Yes
✔ Which package manager do you want to use? · npm
```

@nuxtjs/eslint-config-typescript も追加

```
npm install -D @nuxtjs/eslint-config-typescript
```

### prettier 導入

```
npm install -D prettier eslint-config-prettier @vue/eslint-config-prettier
```

`.prettierrc` 作成

```
{
  "singleQuote": true,
  "semi": true,
  "vueIndentScriptAndStyle": true,
  "trailingComma": "none"
}
```

`.eslintrc.js` を修正

```
module.exports = {
  env: {
    node: true,
    es2021: true,
  },
  extends: [
    'eslint:recommended',
    'plugin:vue/vue3-recommended', // 削除(@nuxtjs/eslint-config-typescript で適用済み)
    '@nuxtjs/eslint-config-typescript', // 追加
    'prettier', // 追加
    '@vue/prettier', // 追加
  ],
  overrides: [],
  parser: '@typescript-eslint/parser', // 削除(@nuxtjs/eslint-config-typescript で適用済み)
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
  plugins: ['vue', '@typescript-eslint'],
  rules: {},
};
```

`package.json` の scripts 下記を追記

```
"lint": "eslint \"./**/*.ts\" --ignore-path .gitignore . && prettier --write \"**/*.ts\" --ignore-path .gitignore"
```

### husky 導入

コミット時に自動的に eslint の解析と prettier の整形を実行させる

```
npm install -D husky
```

`package.json` に下記を追記

```
  "scripts": {
    --- 中略 ---
    "prepare": "husky install",
  },
  --- 中略 ---
  "lint-staged": {
    "*.{ts}": [
      "eslint --fix --ext \".ts\" --ignore-path .gitignore .",
      "prettier --write \"**/*.ts\" --ignore-path .gitignore"
    ]
  }
```

`.husky/pre-commit` ファイル作成

```
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

`.gitignore` に以下追加

```
!.husky/pre-commit
.husky/*
```

※この時点で一旦 `node_modules/` を削除し、`npm ci` を実行しておくと良い。
