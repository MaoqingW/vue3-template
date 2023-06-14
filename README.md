# Vue3 Template

> 总结在配置各种项目配置时的标准流程。参考 [掘金文章](https://juejin.cn/post/7118294114734440455#heading-19)
>
> 技术栈：Vue3 + Vite + TS
>
> 包管理工具：pnpm
>
> 规范工具：husky + commitlint + eslint + stylelint + prettier + lint-staged

#### 1. 项目初始化

```sh
$ pnpm create vite project-template --template vue-ts
```

#### 2. 配置 Eslint

```shell
$ pnpm add eslint -D

$ pnpm eslint --init
```

1. 修改 `.eslintrc.cjs` 中的配置。主要为以下两方面：

   1. 修改 `extends` 中的顺序，保证 `plugin:vue/vue3-essential` 在 `plugin:@typescript-eslint/recommended` 之前。（待议）
   2. 添加 `"parser": 'vue-eslint-parser'` 。作用是赋能解析 `.vue` 后缀的文件。

2. 安装 Eslint 插件，在 .`vscode` 文件夹下新建 `.settings.json` .

   ```json
   {
     "editor.codeActionsOnSave": {
       "source.fixAll": true,
       "source.fixall.eslint": true
     }
   }
   ```

3. 在 `package.json` 中的 `script` 中添加以下命令

   ```json
   {
     "lint": "eslint . --ext .vue,.js,.ts,.jsx,.tsx --fix"
   }
   ```

#### 3. 配置 Prettier

```sh
# 安装
$ pnpm add prettier -D

# 初始化配置，生成 .prettierrc.yaml
$ pnpm create prettier
```

1. 在 `package.json` 中的 `script` 中添加以下命令

   ```json
   {
     "format": "prettier --write \"./**/*.{html,vue,ts,js,json,md}\""
   }
   ```

2. 安装 Prettier 插件，在 `.vscode/settings.json` 中添加以下内容

   ```json
   {
     "editor.formatOnSave": true,
     "editor.defaultFormatter": "esbenp.prettier-vscode"
   }
   ```

#### 4. 解决 Eslint 与 Prettier 的冲突

在 Eslint 与 Prettier 的规则有冲突时，可能会出现一些问题，可以通过安装特定的包解决。

```sh
$ pnpm add eslint-config-prettier eslint-plugin-prettier -D
```

然后，在 `.eslintrc.json` 中 `extends` 的 **最后** 添加一个配置。并重启。

```json
{
	'plugin:prettier/recommended'
}
```

#### 5. 配置 Stylelint

1. 安装相关包，并生成配置文件

   ```sh
   # 安装相关包
   $ pnpm add stylelint postcss postcss-scss postcss-html stylelint-config-prettier stylelint-config-recommended-scss stylelint-config-standard stylelint-config-standard-vue stylelint-scss stylelint-order -D
   # 生成配置文件 .styleintrc.json
   $ pnpm create stylelint

   # 安装 stylelint-config-clean-order
   $ pnpm add stylelint-config-clean-order -D
   ```

   `stylelint-config-clean-order` 是用来规范 CSS 的属性顺序的，其所依赖的 `stylelint-order` 在安装相关包时已经安装。

   在 `stylelint-order` 的 npm 主页有推荐若干个类似的 extends,相比于其他几个，`stylelint-config-clean-order` 会覆盖 Stylelint 中的两个用于控制空行的属性，所以可以实现将 CSS 属性分块的效果。而别的几个要么配置复杂一些，要么没有增加空行的功能，所以选择 `stylelint-config-clean-order`.

2. 修改 `.stylelintrc.json`

   ```json
   {
     "extends": [
       "stylelint-config-standard",
       "stylelint-config-prettier",
       "stylelint-config-recommended-scss",
       "stylelint-config-standard-vue",
       "stylelint-config-clean-order"
     ],
     "plugins": ["stylelint-order"],
     "overrides": [
       {
         "files": ["** /*.(scss|css|vue|html)"],
         "customSyntax": "postcss-scss"
       },
       {
         "files": ["**/*.(html|vue)"],
         "customSyntax": "postcss-html"
       }
     ],
     "ignoreFiles": [
       "** /*.js",
       "**/*.jsx",
       "** /*.tsx",
       "**/*.ts",
       "** /*.json",
       "**/*.md",
       "**/*.yaml"
     ],
     "rules": {
       "no-descending-specificity": null,
       "selector-pseudo-element-no-unknown": [
         true,
         {
           "ignorePseudoElements": ["v-deep"]
         }
       ],
       "selector-pseudo-class-no-unknown": [
         true,
         {
           "ignorePseudoClasses": ["deep"]
         }
       ]
     }
   }
   ```

3. 在 `package.json` 中添加指令

   ```json
   {
     "lint:style": "stylelint \"./**/*.{css,less,vue,html}\" --fix"
   }
   ```

4. 安装 Stylelint 插件，并在 `settings.json` 添加配置

   ```json
   {
     // 开启自动修复
     "editor.codeActionsOnSave": {
       "source.fixAll": false,
       "source.fixAll.eslint": true,
   +   "source.fixAll.stylelint": true
     },
     // 保存的时候自动格式化
     "editor.formatOnSave": true,
     // 默认格式化工具选择prettier
     "editor.defaultFormatter": "esbenp.prettier-vscode",
     // 配置该项，新建文件时默认就是space：2
     "editor.tabSize": 2,
     // stylelint校验的文件格式
   + "stylelint.validate": ["css", "scss", "vue", "html"]
   }
   ```

#### 6. 安装 Sass

```sh
$ pnpm add sass sass-loader -D
```

#### 7. 配置 Husky

安装

```sh
# 安装命令
$ pnpm add husky -D
```

在 `package.json` 中新增一条脚本命令，该命令会在 `pnpm install` 后执行。

```json
{
  "scripts": {
    "prepare": "husky install"
  }
}
```

运行脚本命令之后，会在根目录新增 `.husky` 目录，运行以下命令新增 `pre-commit` 钩子

```sh
$ pnpm husky add .husky/pre-commit "pnpm lint && pnpm format && pnpm lint:style"
```

执行之后，会在 `.husky` 下自动生成一个 `pre-commit` 文件。可以在文件中自由修改钩子响应时执行的脚本命令（引号中的部分）。

#### 8. 配置 commitlint

安装依赖

```sh
$ pnpm add @commitlint/cli @commitlint/config-conventional -D
```

跟目录下新建 `commitlint.config.js`

```javascript
module.exports = {
  extends: ["@commitlint/config-conventional"],
  rules: {
    "type-enum": [
      2,
      "always",
      [
        "build", // 主要目的是修改项目构建系统(例如 glup，webpack，rollup 的配置等)的提交
        "ci", // 主要目的是修改项目继续集成流程(例如 Travis，Jenkins，GitLab CI，Circle等)的提交
        "docs", // 文档更新
        "feat", // 新增功能
        "fix", // bug 修复
        "perf", // 性能优化
        "refactor", // 重构代码(既没有新增功能，也没有修复 bug)
        "style", // 不影响程序逻辑的代码修改(修改空白字符，补全缺失的分号等)
        "test", // 新增测试用例或是更新现有测试
        "revert", // 回滚某个更早之前的提交
        "chore" // 不属于以上类型的其他类型(日常事务)
      ]
    ],
    "type-case": [0],
    "type-empty": [0],
    "scope-empty": [0],
    "scope-case": [0],
    "subject-full-stop": [0],
    "subject-case": [0, "never"],
    "header-max-length": [0, "always", 72]
  }
};
```

在 `package.json` 中新增一条脚本命令

```json
{
  "script": {
    "commitlint": "commitlint -e -V"
  }
}
```

增加 commit-msg hooks

```sh
$ npx husky add .husky/commit-msg 'npm run commitlint'
```
