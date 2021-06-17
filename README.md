# Prettier and ESLint Automation Cheat Sheet

## Why

👉 Preventing you from committing bad code. This is the cheapest thing you can do to make sure your code is valid

## Setup

-   Prettier
-   Eslint
-   StyleLint
-   Ability to validate code locally
-   Automatically run code validation with Husky and lint-staged

## 🛠 1. [Prettier](https://prettier.io/docs/en/install.html)

-   Make the code styling of the project more consistently
-   Stop argument about coding styles between
    developers

🐨 **Dependencies**

```script
npm install -D prettier
```

🐨 **Configuration**

Try the [Playground](https://prettier.io/playground/) and copy your preferred config by clicking the `Copy config JSON` button. Then put it in the `.prettierrc.json`

```script
{
    "arrowParens": "always",
    "bracketSpacing": true,
    "embeddedLanguageFormatting": "auto",
    "htmlWhitespaceSensitivity": "css",
    "insertPragma": false,
    "jsxBracketSameLine": false,
    "jsxSingleQuote": false,
    "printWidth": 80,
    "proseWrap": "preserve",
    "quoteProps": "as-needed",
    "requirePragma": false,
    "semi": false,
    "singleQuote": false,
    "tabWidth": 4,
    "trailingComma": "es5",
    "useTabs": false,
    "vueIndentScriptAndStyle": false
}
```

🐨 **Scripts**

Add this to `package.json`

```script
"scripts": {
    "prettier": "prettier --ignore-path .gitignore \"**/*.+(ts|tsx|js|jsx|json|css|md|mdx|html)\"",
    "format": "npm run prettier -- --write",
    "check-format": "npm run prettier -- --list-different",
}
```

To exclude files from formatting, create a `.prettierignore` file in the root of your project or you can use `--ignore-path` to ignore files listed in the gitignore [docs](https://prettier.io/docs/en/ignore.html)

## ⚒️ 2. [ESLint](https://eslint.org/)

-   Analyze your code to quickly find problems
-   Fix Automatically
-   You can customize ESLint to work exactly the way you need

🐨 **Dependencies**

```script
npm install -D eslint eslint-config-prettier eslint-plugin-react
```

🐨 **Configuration**

Put it in the `.eslintrc`

```script
{
  "parserOptions": {
    "ecmaVersion": 2021,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "extends": ["eslint:recommended", "eslint-config-prettier", "plugin:react/recommended"],
  "rules": {
    "no-unused-vars": ["error", {}],
  },
  "plugins": [
    "react"
  ],
  "env": {
    "browser": true
  }
}
```

Check list of Eslint rules [here](https://eslint.org/docs/rules/)

**Note that**

-   We use [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) to turns off all rules that are unnecessary or **might conflict with Prettier**
-   Feel free to remove [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react) if you don't use React

🐨 **Script**
Add this to `package.json`

```script
"scripts": {
    "lint": "eslint --ignore-path .gitignore \"**/*.+(js|jsx)\"",
}
```

### ❓️ QA:

🙋‍♂️ Differences between `eslint` and `prettier`?

-   Use **prettier** for formatting and **linters** for catching bugs
-   Check [Prettier vs. Linters](https://prettier.io/docs/en/comparison.html)

🙋‍♂️ Differences between `extends` and `plugins`?

-   `Extends`: existing set of predefined rules
-   `Plugins`: provides a set of new rules
-   Check [stackoverflow](https://stackoverflow.com/a/54522973/8737224)

## 🛡 3. Validate script

Now Eslint and Prettier are all set. We add a script that runs prettier and lint to validate our code

🐨 **Script**
Add this to `package.json`

```script
"scripts": {
    "validate": "npm run check-format & npm run lint"
}
```

We can run all these scripts in parallel by using [npm-run-all](https://github.com/mysticatea/npm-run-all)

🐨 **Dependencies**

```script
npm install -D npm-run-all
```

🐨 **Update Script**

```script
"scripts": {
    "validate": "npm-run-all --parallel lint check-format"
}
```

## 🔭 4. [Husky](https://github.com/typicode/husky)

-   We don't want to run the validate script `npm run validate` manually before committing our code.
-   Husky helps us run a script automatically before committing the code

🐨 **Dependencies**

```script
npm install -D husky
npm set-script prepare "husky install"
npx husky add .husky/pre-commit "npm run validate"
```

Basically we say that please automatically run `npm run validate` before every commit

Try to break the styling of your code then commit the files!

## 🔬 5. [Lint-staged](https://github.com/typicode/husky)

Running lint and styling check on the whole project is slow, you only want to lint files that will be committed.

🐨 **Dependencies**

```script
npm install -D lint-staged
```

🐨 **Configuration**

Add this to `package.json`

```script
"lint-staged": {
  "**/*.+(js|jsx)": [
    "eslint"
  ],
  "**/*.+(ts|tsx|js|jsx|json|css|md|mdx|html)": [
    "prettier --write",
    "git add"
  ]
}
```

🐨 **Update Husky pre-commit script**

```script
npx husky set .husky/pre-commit "npx lint-staged"
```

Try to break the styling of your code then commit the files!

## 🎁 6. [Stylelint](https://stylelint.io/)

🐨 **Dependencies**

```script
npm install -D stylelint stylelint-config-standard stylelint-config-prettier
```

🐨 **Configuration**

Add this to `.stylelintrc.json`

```script
{
  "extends": [
    "stylelint-config-standard",
    "stylelint-config-prettier"
  ]
}
```

We use [stylelint-config-prettier](https://github.com/prettier/stylelint-config-prettier) to turns off all rules that are unnecessary or **might conflict with Prettier**

🐨 **Script**

```script
"scripts": {
  "lint:css": "stylelint --ignore-path .gitignore --fix \"**/*.css\""
},
```

🐨 **Update lint-staged**

```script
"lint-staged": {
  // other configs ...
  "**/*.css": [
    "stylelint --fix"
  ],
}
```

## Resources

🍀 I learned this setup from Kent's [static-testing-tools](https://github.com/kentcdodds/static-testing-tools) repo
