# webpack5 手动创建 vue 项目

## 添加基础配置

### 安装相关依赖

```sh
mkdir webpack-vue-ts && cd webpack-vue-ts
npm init -y
npm install webpack webpack-cli --save-dev
npm install webpack-dev-server webpack-merge  --save-dev
```

### 创建相关文件

项目根目录下创建文件夹`build`，并在 `build`下创建文件：`webpack.common.js`、`webpack.dev.js`、`webpack.prod.js`

webpack.common.js

```sh
const path = require('path')

module.exports = {
  entry: {
    app: './src/index.js'
  },
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, '../dist')
  }
}

```

webpack.dev.js

```sh
const { merge } = require('webpack-merge')
const common = require('./webpack.common.js')

module.exports = merge(common, {
  mode: 'development',
  devtool: 'inline-source-map',
  devServer: {
    static: './dist'
  }
})

```

webpack.prod.js

```sh
const { merge } = require('webpack-merge')
const common = require('./webpack.common.js')

module.exports = merge(common, {
  mode: 'production',
  plugins: []
})

```

创建 `src/index.js`

```js
function component() {
  var element = document.createElement('div')

  element.innerHTML = 'webpack vue!'

  return element
}

document.body.appendChild(component())
```

创建 `dist/index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>webpack-vue</title>
  </head>
  <body>
    <script src="app.bundle.js"></script>
  </body>
</html>
```

修改 package.json 中 scripts

```json
{
  "scripts": {
    "start": "webpack-dev-server --config build/webpack.dev.js",
    "build": "webpack --config build/webpack.prod.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

最后可以启动 `npm start` 命令

## 添加对 html 的支持

1. 添加依赖

```sh
npm install html-webpack-plugin clean-webpack-plugin --save-dev
```

2. 修改相关配置

2.1 在项目目录下添加`index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <!-- 如果浏览器禁止加载js脚本 -->
    <noscript>
      <strong>
        We're sorry but this site doesn't work properly without JavaScript enabled. Please enable it to continue.
      </strong>
    </noscript>
  </body>
</html>
```

2.2 删除 dist 下的 index.html
2.3 修改`webpack.common.js`

```diff
const path = require('path')
+ const HtmlWebpackPlugin = require('html-webpack-plugin')
+ const { CleanWebpackPlugin } = require('clean-webpack-plugin')
module.exports = {
  entry: {
    app: './src/index.js'
  },
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, '../dist')
  },
+ plugins: [
+   new CleanWebpackPlugin(),
+   new HtmlWebpackPlugin({
+     title: 'webpack vue',
+     template: './index.html'
+   })
+ ]
}
```

`HtmlWebpackPlugin`使用参考：

https://juejin.cn/post/6844903853708541959

## 配置 typescript

1. 添加依赖

```sh
npm install --save-dev @babel/core @babel/plugin-proposal-class-properties @babel/plugin-proposal-decorators @babel/plugin-transform-runtime @babel/preset-env @babel/preset-typescript babel-loader typescript tsconfig-paths-webpack-plugin
```

```sh
npm install @babel/polyfill @babel/runtime
```

2. 修改相关配置

webpack.common.js

```diff
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
module.exports = {
  entry: {
-    app: './src/index.js'
+    app: ['@babel/polyfill', './src/main.ts'],
  },
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, '../dist')
  },
+  module: {
+    rules: [
+      {
+        test: /\.ts|js$/,
+        exclude: /node_modules/,
+        use: [
+          {
+            loader: 'babel-loader'
+          }
+        ]
+      }
+    ]
+  },
+  resolve: {
+    extensions: ['.ts', '.js'],
+    plugins: [new TsconfigPathsPlugin()]
+  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: 'webpack vue',
      template: './index.html'
    })
  ]
}

```

babel.config.json

```diff
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": false
      }
    ],
    [
      "@babel/preset-typescript",
      {
        "allExtensions": true
      }
    ]
  ],
  "plugins": [
    [
      "@babel/plugin-proposal-decorators",
      {
        "legacy": true
      }
    ],
    [
      "@babel/plugin-proposal-class-properties"
    ],
    [
      "@babel/plugin-transform-runtime"
    ]
  ]
}
```

初始化 tsconfig.json

```sh
./node_modules/.bin/tsc -init
```

tsconfig.json

```json
{
  "compilerOptions": {
    /* Visit https://aka.ms/tsconfig.json to read more about this file */

    "target": "es5" /* Set the JavaScript language version for emitted JavaScript and include compatible library declarations. */,
    "experimentalDecorators": true /* Enable experimental support for TC39 stage 2 draft decorators. */,
    "module": "commonjs" /* Specify what module code is generated. */,
    "rootDir": "./" /* Specify the root folder within your source files. */,
    "baseUrl": "./" /* Specify the base directory to resolve non-relative module names. */,
    "allowJs": true /* Allow JavaScript files to be a part of your program. Use the `checkJS` option to get errors from these files. */,
    "esModuleInterop": true /* Emit additional JavaScript to ease support for importing CommonJS modules. This enables `allowSyntheticDefaultImports` for type compatibility. */,
    "forceConsistentCasingInFileNames": true /* Ensure that casing is correct in imports. */,
    "strict": true /* Enable all strict type-checking options. */,
    "skipLibCheck": true /* Skip type checking all .d.ts files. */
    /* Projects */
    // "incremental": true,                              /* Enable incremental compilation */
    // "composite": true,                                /* Enable constraints that allow a TypeScript project to be used with project references. */
    // "tsBuildInfoFile": "./",                          /* Specify the folder for .tsbuildinfo incremental compilation files. */
    // "disableSourceOfProjectReferenceRedirect": true,  /* Disable preferring source files instead of declaration files when referencing composite projects */
    // "disableSolutionSearching": true,                 /* Opt a project out of multi-project reference checking when editing. */
    // "disableReferencedProjectLoad": true,             /* Reduce the number of projects loaded automatically by TypeScript. */

    /* Language and Environment */
    // "lib": [],                                        /* Specify a set of bundled library declaration files that describe the target runtime environment. */
    // "jsx": "preserve",                                /* Specify what JSX code is generated. */
    // "emitDecoratorMetadata": true,                    /* Emit design-type metadata for decorated declarations in source files. */
    // "jsxFactory": "",                                 /* Specify the JSX factory function used when targeting React JSX emit, e.g. 'React.createElement' or 'h' */
    // "jsxFragmentFactory": "",                         /* Specify the JSX Fragment reference used for fragments when targeting React JSX emit e.g. 'React.Fragment' or 'Fragment'. */
    // "jsxImportSource": "",                            /* Specify module specifier used to import the JSX factory functions when using `jsx: react-jsx*`.` */
    // "reactNamespace": "",                             /* Specify the object invoked for `createElement`. This only applies when targeting `react` JSX emit. */
    // "noLib": true,                                    /* Disable including any library files, including the default lib.d.ts. */
    // "useDefineForClassFields": true,                  /* Emit ECMAScript-standard-compliant class fields. */

    /* Modules */
    // "moduleResolution": "node",                       /* Specify how TypeScript looks up a file from a given module specifier. */
    // "paths": {},                                      /* Specify a set of entries that re-map imports to additional lookup locations. */
    // "rootDirs": [],                                   /* Allow multiple folders to be treated as one when resolving modules. */
    // "typeRoots": [],                                  /* Specify multiple folders that act like `./node_modules/@types`. */
    // "types": [],                                      /* Specify type package names to be included without being referenced in a source file. */
    // "allowUmdGlobalAccess": true,                     /* Allow accessing UMD globals from modules. */
    // "resolveJsonModule": true,                        /* Enable importing .json files */
    // "noResolve": true,                                /* Disallow `import`s, `require`s or `<reference>`s from expanding the number of files TypeScript should add to a project. */

    /* JavaScript Support */
    // "checkJs": true,                                  /* Enable error reporting in type-checked JavaScript files. */
    // "maxNodeModuleJsDepth": 1,                        /* Specify the maximum folder depth used for checking JavaScript files from `node_modules`. Only applicable with `allowJs`. */

    /* Emit */
    // "declaration": true,                              /* Generate .d.ts files from TypeScript and JavaScript files in your project. */
    // "declarationMap": true,                           /* Create sourcemaps for d.ts files. */
    // "emitDeclarationOnly": true,                      /* Only output d.ts files and not JavaScript files. */
    // "sourceMap": true,                                /* Create source map files for emitted JavaScript files. */
    // "outFile": "./",                                  /* Specify a file that bundles all outputs into one JavaScript file. If `declaration` is true, also designates a file that bundles all .d.ts output. */
    // "outDir": "./",                                   /* Specify an output folder for all emitted files. */
    // "removeComments": true,                           /* Disable emitting comments. */
    // "noEmit": true,                                   /* Disable emitting files from a compilation. */
    // "importHelpers": true,                            /* Allow importing helper functions from tslib once per project, instead of including them per-file. */
    // "importsNotUsedAsValues": "remove",               /* Specify emit/checking behavior for imports that are only used for types */
    // "downlevelIteration": true,                       /* Emit more compliant, but verbose and less performant JavaScript for iteration. */
    // "sourceRoot": "",                                 /* Specify the root path for debuggers to find the reference source code. */
    // "mapRoot": "",                                    /* Specify the location where debugger should locate map files instead of generated locations. */
    // "inlineSourceMap": true,                          /* Include sourcemap files inside the emitted JavaScript. */
    // "inlineSources": true,                            /* Include source code in the sourcemaps inside the emitted JavaScript. */
    // "emitBOM": true,                                  /* Emit a UTF-8 Byte Order Mark (BOM) in the beginning of output files. */
    // "newLine": "crlf",                                /* Set the newline character for emitting files. */
    // "stripInternal": true,                            /* Disable emitting declarations that have `@internal` in their JSDoc comments. */
    // "noEmitHelpers": true,                            /* Disable generating custom helper functions like `__extends` in compiled output. */
    // "noEmitOnError": true,                            /* Disable emitting files if any type checking errors are reported. */
    // "preserveConstEnums": true,                       /* Disable erasing `const enum` declarations in generated code. */
    // "declarationDir": "./",                           /* Specify the output directory for generated declaration files. */

    /* Interop Constraints */
    // "isolatedModules": true,                          /* Ensure that each file can be safely transpiled without relying on other imports. */
    // "allowSyntheticDefaultImports": true,             /* Allow 'import x from y' when a module doesn't have a default export. */
    // "preserveSymlinks": true,                         /* Disable resolving symlinks to their realpath. This correlates to the same flag in node. */

    /* Type Checking */
    // "noImplicitAny": true,                            /* Enable error reporting for expressions and declarations with an implied `any` type.. */
    // "strictNullChecks": true,                         /* When type checking, take into account `null` and `undefined`. */
    // "strictFunctionTypes": true,                      /* When assigning functions, check to ensure parameters and the return values are subtype-compatible. */
    // "strictBindCallApply": true,                      /* Check that the arguments for `bind`, `call`, and `apply` methods match the original function. */
    // "strictPropertyInitialization": true,             /* Check for class properties that are declared but not set in the constructor. */
    // "noImplicitThis": true,                           /* Enable error reporting when `this` is given the type `any`. */
    // "useUnknownInCatchVariables": true,               /* Type catch clause variables as 'unknown' instead of 'any'. */
    // "alwaysStrict": true,                             /* Ensure 'use strict' is always emitted. */
    // "noUnusedLocals": true,                           /* Enable error reporting when a local variables aren't read. */
    // "noUnusedParameters": true,                       /* Raise an error when a function parameter isn't read */
    // "exactOptionalPropertyTypes": true,               /* Interpret optional property types as written, rather than adding 'undefined'. */
    // "noImplicitReturns": true,                        /* Enable error reporting for codepaths that do not explicitly return in a function. */
    // "noFallthroughCasesInSwitch": true,               /* Enable error reporting for fallthrough cases in switch statements. */
    // "noUncheckedIndexedAccess": true,                 /* Include 'undefined' in index signature results */
    // "noImplicitOverride": true,                       /* Ensure overriding members in derived classes are marked with an override modifier. */
    // "noPropertyAccessFromIndexSignature": true,       /* Enforces using indexed accessors for keys declared using an indexed type */
    // "allowUnusedLabels": true,                        /* Disable error reporting for unused labels. */
    // "allowUnreachableCode": true,                     /* Disable error reporting for unreachable code. */

    /* Completeness */
    // "skipDefaultLibCheck": true,                      /* Skip type checking .d.ts files that are included with TypeScript. */
  },
  "paths": {
    "@/*": ["src/*"]
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx"],
  "exclude": ["node_modules", "dist"]
}
```

在 src 目录下添加 main.ts

```ts
interface Person {
  firstName: string
  lastName: string
}

export function greeter(person: Person) {
  return 'Hello, ' + person.firstName + ' ' + person.lastName
}
```

index.js

```diff
+ import { greeter } from '@/main.ts'

let user = { firstName: 'Jane', lastName: 'User' }

function component() {
  var element = document.createElement('div')

-  element.innerHTML = 'webpack vue!'
+  element.innerHTML = 'webpack vue!' + greeter(user)

  return element
}

document.body.appendChild(component())
```

运行 `npm start` 或者 打包 `npm run build` 测试 ts

## 添加对 vue 的支持

1. 添加依赖

```sh
npm install vue vue-class-component vue-property-decorator
npm install vue-loader vue-template-compiler  --save-dev
```

2. 修改`webpack.common.js`配置

```diff
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const TsconfigPathsPlugin = require('tsconfig-paths-webpack-plugin')
+ const VueLoaderPlugin = require('vue-loader/lib/plugin-webpack5')
+ const Webpack = require('webpack')
module.exports = {
  entry: {
    app: './src/index.js',
+   // 添加测试
+    apptest: './src/app.ts'
  },
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, '../dist')
  },
  module: {
    rules: [
      {
        test: /\.ts|js$/,
        exclude: /node_modules/,
        use: [
          {
            loader: 'babel-loader'
          }
        ]
      },
+      {
+        test: /\.vue$/,
+        use: [
+          {
+            loader: 'vue-loader'
+          }
+        ]
+      }
    ]
  },
  resolve: {
    alias: {
      '@': path.resolve('src')
    },
    extensions: ['.ts', '.js'],
    plugins: [new TsconfigPathsPlugin()]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: 'webpack vue',
      template: './index.html'
    }),
+    new VueLoaderPlugin(),
+    new Webpack.ProvidePlugin({
+      Vue: ['vue/dist/vue.esm.js', 'default']
+    })
  ]
}

```

3. 添加 app.ts

```ts
import Vue from 'vue'
// @ts-ignore
import App from '@/App.vue'
var app = new Vue({
  el: '#app',
  render: h => h(App)
})
app.$mount(App)
```

4. 添加 Vue core 针对 TypeScript 的官方类型声明

参考： https://github.com/vuejs/vue/tree/dev/types

在`src`下创建`types`目录并创建一下文件

global.d.ts

```ts
import Vue from 'vue'

declare global {
  const Vue: typeof Vue
}
```

images.d.ts

```ts
declare module '*.png'
declare module '*.jpg'
declare module '*.jpeg'
declare module '*.gif'
declare module '*.svg'
```

vue.d.ts

```ts
declare module '*.vue' {
  import Vue from 'vue'
  export default Vue
}

declare module 'vue/types/vue' {
  interface Vue {}
}
```

5. 在`src`文件夹下创建`App.vue`，打包测试

```vue
<template>
  <div id="app-1">{{ message }}</div>
</template>
<script lang="ts">
  import { Vue, Component } from 'vue-property-decorator'

  @Component
  export default class App extends Vue {
    public message = 'welcome to webpack5 and typescript vue2'
  }
</script>
<style scoped></style>
```

## 添加对 sass 的支持

1. 添加依赖

```sh
npm i -D sass sass-loader node-sass postcss postcss-loader postcss-preset-env mini-css-extract-plugin
```

如果 npm 使用淘宝镜像可能安装 node-sass 失败，需要临时更换原始源

```sh
npm i -D node-sass --registry=https://registry.npmjs.org
```

2. 配置相关文件

webpack.common.js

```diff
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const TsconfigPathsPlugin = require('tsconfig-paths-webpack-plugin')
const VueLoaderPlugin = require('vue-loader/lib/plugin-webpack5')
const Webpack = require('webpack')
+ const MiniCssExtractPlugin = require('mini-css-extract-plugin')
module.exports = {
  entry: {
    app: './src/index.js',
    // 添加测试
    apptest: './src/app.ts'
  },
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, '../dist')
  },
  module: {
    rules: [
      {
        test: /\.ts|js$/,
        exclude: /node_modules/,
        use: [
          {
            loader: 'babel-loader'
          }
        ],
        exclude: /node_modules/
      },
      {
        test: /\.vue$/,
        use: [
          {
            loader: 'vue-loader'
          }
        ]
      },
+      {
+       test: /\.css|sass|scss$/,
+        use: [
+          {
+            loader: MiniCssExtractPlugin.loader
+          },
+          {
+            loader: 'css-loader'
+          },
+          {
+            loader: 'postcss-loader',
+            options: {
+              postcssOptions: {
+                plugins: [['postcss-preset-env', {}]]
+              }
+            }
+          },
+          {
+            loader: 'sass-loader'
+          }
+        ]
+      }
    ]
  },
  resolve: {
    alias: {
      '@': path.resolve('src')
    },
    extensions: ['.ts', '.js'],
    plugins: [new TsconfigPathsPlugin()]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: 'webpack vue',
      template: './index.html'
    }),
    new VueLoaderPlugin(),
    new Webpack.ProvidePlugin({
      Vue: ['vue/dist/vue.esm.js', 'default']
    }),
+    new MiniCssExtractPlugin({
+      filename: 'css/[name].[chunkhash:10].css'
    })
  ]
}

```

## css 压缩

1. 添加依赖

```sh
npm i -D css-minimizer-webpack-plugin
```

2. 修改相关配置

webpack.common.js

```diff
+ const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')

module.exports = {
  // .......
+  optimization: {
+      minimize: true,
+      minimizer: [
+        new CssMinimizerPlugin()
+      ]
+    },
    // .......
}
```

## 添加 ESlint

1. 添加依赖

```sh
npm install eslint eslint-plugin-vue vue-eslint-parser @typescript-eslint/eslint-plugin @typescript-eslint/parser --save-dev
```

2. 手动添加及配置.eslintrc.js

```js
module.exports = {
  env: {
    browser: true,
    commonjs: true,
    es2021: true
  },
  parser: 'vue-eslint-parser', // 解析 .vue 文件
  extends: ['eslint:recommended', 'plugin:vue/essential', 'plugin:@typescript-eslint/recommended'],
  parserOptions: {
    ecmaVersion: 13,
    parser: '@typescript-eslint/parser' // 解析 .vue 文件里面的 script 标签
  },
  plugins: ['vue', '@typescript-eslint'],
  rules: {
    // 定义其它校验规则
    '@typescript-eslint/no-extra-semi': ['error'],
    '@typescript-eslint/semi': ['error'],
    '@typescript-eslint/no-empty-interface': 0
  }
}
```

3. 补充

集成 eslint，可以先添加 eslint 然后初始化配置

```sh
npm install eslint --save-dev
npx eslint --init
```

## 添加 prettier

1. 添加依赖

```sh
npm i -D prettier eslint-config-prettier eslint-plugin-prettier
```

2. 配置相关文件

.eslintrc.js

```diff
module.exports = {
  env: {
    browser: true,
    commonjs: true,
    es2021: true
  },
  parser: 'vue-eslint-parser', // 解析 .vue 文件
  extends: [
    'eslint:recommended',
    'plugin:vue/essential',
    'plugin:@typescript-eslint/recommended',
+    'plugin:prettier/recommended',
+    'prettier/@typescript-eslint'
  ],
  parserOptions: {
    ecmaVersion: 13,
    parser: '@typescript-eslint/parser' // 解析 .vue 文件里面的 script 标签
  },
  plugins: ['vue', '@typescript-eslint'],
  rules: {
    // 定义其它校验规则
    '@typescript-eslint/no-extra-semi': ['error'],
    '@typescript-eslint/semi': ['error'],
    '@typescript-eslint/no-empty-interface': 0,
    '@typescript-eslint/ban-ts-comment': 'off'
  }
}
```

eeslint 配置项说明

- root 限定配置文件的使用范围
- parser 指定 eslint 的解析器
- parserOptions 设置解析器选项
- extends 指定 eslint 规范
- plugins 引用第三方的插件
- env 指定代码运行的宿主环境
- rules 启用额外的规则或覆盖默认的规则
- globals 声明在代码中的自定义全局变量

rule 中规则的开启关闭：

- "off" 或 0 - 关闭规则
- "warn" 或 1 - 开启规则
- "error" 或 2 - 开启规则

配置.prettierrc.js

```js
module.exports = {
  semi: true, // 语句后加分号
  trailingComma: 'none', // 尾随逗号(none |es5 | all)
  singleQuote: true, // 使用单引号
  printWidth: 80, // 每一行的最大长度, 尽量和编辑器保持一致
  tabWidth: 2, // Tab 缩进的长度
  useTabs: true, // 使用 Tab 缩进
  endOfLine: 'auto', // 文件尾部换行的形式
  arrowParens: 'always' // 箭头函数参数使用小括号包裹
}
```

`typescript`、`vue`、`sass`配置参考：

- https://zhuanlan.zhihu.com/p/339679136

- https://juejin.cn/post/6844904160375078925

* https://www.jianshu.com/p/6e01c7df0d9f

## eslintrc 示例

```js
  {
    "env": {
      "browser": true,
      "node": true,
      "commonjs": true
    },
    "ecmaFeatures": {
      // lambda表达式
      "arrowFunctions": true,
      // 解构赋值
      "destructuring": true,
      // class
      "classes": true,
      // http://es6.ruanyifeng.com/#docs/function#函数参数的默认值
      "defaultParams": true,
      // 块级作用域，允许使用let const
      "blockBindings": true,
      // 允许使用模块，模块内默认严格模式
      "modules": true,
      // 允许字面量定义对象时，用表达式做属性名
      // http://es6.ruanyifeng.com/#docs/object#属性名表达式
      "objectLiteralComputedProperties": true,
      // 允许对象字面量方法名简写
      /*var o = {
          method() {
            return "Hello!";
          }
       };

       等同于

       var o = {
         method: function() {
           return "Hello!";
         }
       };
      */
      "objectLiteralShorthandMethods": true,
      /*
        对象字面量属性名简写
        var foo = 'bar';
        var baz = {foo};
        baz // {foo: "bar"}

        // 等同于
        var baz = {foo: foo};
      */
      "objectLiteralShorthandProperties": true,
      // http://es6.ruanyifeng.com/#docs/function#rest参数
      "restParams": true,
      // http://es6.ruanyifeng.com/#docs/function#扩展运算符
      "spread": true,
      // http://es6.ruanyifeng.com/#docs/iterator#for---of循环
      "forOf": true,
      // http://es6.ruanyifeng.com/#docs/generator
      "generators": true,
      // http://es6.ruanyifeng.com/#docs/string#模板字符串
      "templateStrings": true,
      "superInFunctions": true,
      // http://es6.ruanyifeng.com/#docs/object#对象的扩展运算符
      "experimentalObjectRestSpread": true
    },

    "rules": {
      // 定义对象的set存取器属性时，强制定义get
      "accessor-pairs": 2,
      // 指定数组的元素之间要以空格隔开(,后面)， never参数：[ 之前和 ] 之后不能带空格，always参数：[ 之前和 ] 之后必须带空格
      "array-bracket-spacing": [2, "never"],
      // 在块级作用域外访问块内定义的变量是否报错提示
      "block-scoped-var": 0,
      // if while function 后面的{必须与if在同一行，java风格。
      "brace-style": [2, "1tbs", { "allowSingleLine": true }],
      // 双峰驼命名格式
      "camelcase": 2,
      // 数组和对象键值对最后一个逗号， never参数：不能带末尾的逗号, always参数：必须带末尾的逗号，
      // always-multiline：多行模式必须带逗号，单行模式不能带逗号
      "comma-dangle": [2, "never"],
      // 控制逗号前后的空格
      "comma-spacing": [2, { "before": false, "after": true }],
      // 控制逗号在行尾出现还是在行首出现
      // http://eslint.org/docs/rules/comma-style
      "comma-style": [2, "last"],
      // 圈复杂度
      "complexity": [2,9],
      // 以方括号取对象属性时，[ 后面和 ] 前面是否需要空格, 可选参数 never, always
      "computed-property-spacing": [2,"never"],
      // 强制方法必须返回值，TypeScript强类型，不配置
      "consistent-return": 0,
      // 用于指统一在回调函数中指向this的变量名，箭头函数中的this已经可以指向外层调用者，应该没卵用了
      // e.g [0,"that"] 指定只能 var that = this. that不能指向其他任何值，this也不能赋值给that以外的其他值
      "consistent-this": 0,
      // 强制在子类构造函数中用super()调用父类构造函数，TypeScrip的编译器也会提示
      "constructor-super": 0,
      // if else while for do后面的代码块是否需要{ }包围，参数：
      //    multi  只有块中有多行语句时才需要{ }包围
      //    multi-line  只有块中有多行语句时才需要{ }包围, 但是块中的执行语句只有一行时，
      //                   块中的语句只能跟和if语句在同一行。if (foo) foo++; else doSomething();
      //    multi-or-nest 只有块中有多行语句时才需要{ }包围, 如果块中的执行语句只有一行，执行语句可以零另起一行也可以跟在if语句后面
      //    [2, "multi", "consistent"] 保持前后语句的{ }一致
      //    default: [2, "all"] 全都需要{ }包围
      "curly": [2, "all"],
      // switch语句强制default分支，也可添加 // no default 注释取消此次警告
      "default-case": 2,
      // 强制object.key 中 . 的位置，参数:
      //      property，'.'号应与属性在同一行
      //      object, '.' 号应与对象名在同一行
      "dot-location": [2, "property"],
      // 强制使用.号取属性
      //    参数： allowKeywords：true 使用保留字做属性名时，只能使用.方式取属性
      //                          false 使用保留字做属性名时, 只能使用[]方式取属性 e.g [2, {"allowKeywords": false}]
      //           allowPattern:  当属性名匹配提供的正则表达式时，允许使用[]方式取值,否则只能用.号取值 e.g [2, {"allowPattern": "^[a-z]+(_[a-z]+)+$"}]
      "dot-notation": [2, {"allowKeywords": true}],
      // 文件末尾强制换行
      "eol-last": 2,
      // 使用 === 替代 ==
      "eqeqeq": [2, "allow-null"],
      // 方法表达式是否需要命名
      "func-names": 0,
      // 方法定义风格，参数：
      //    declaration: 强制使用方法声明的方式，function f(){} e.g [2, "declaration"]
      //    expression：强制使用方法表达式的方式，var f = function() {}  e.g [2, "expression"]
      //    allowArrowFunctions: declaration风格中允许箭头函数。 e.g [2, "declaration", { "allowArrowFunctions": true }]
      "func-style": 0,
      "generator-star-spacing": [2, { "before": true, "after": true }],
      "guard-for-in": 0,
      "handle-callback-err": [2, "^(err|error)$" ],
      "indent": [2, 2, { "SwitchCase": 1 }],
      "key-spacing": [2, { "beforeColon": false, "afterColon": true }],
      "linebreak-style": 0,
      "lines-around-comment": 0,
      "max-nested-callbacks": 0,
      "new-cap": [2, { "newIsCap": true, "capIsNew": false }],
      "new-parens": 2,
      "newline-after-var": 0,
      "no-alert": 0,
      "no-array-constructor": 2,
      "no-caller": 2,
      "no-catch-shadow": 0,
      "no-cond-assign": 2,
      "no-console": 0,
      "no-constant-condition": 0,
      "no-continue": 0,
      "no-control-regex": 2,
      "no-debugger": 2,
      "no-delete-var": 2,
      "no-div-regex": 0,
      "no-dupe-args": 2,
      "no-dupe-keys": 2,
      "no-duplicate-case": 2,
      "no-else-return": 0,
      "no-empty": 0,
      "no-empty-character-class": 2,
      "no-empty-label": 2,
      "no-eq-null": 0,
      "no-eval": 2,
      "no-ex-assign": 2,
      "no-extend-native": 2,
      "no-extra-bind": 2,
      "no-extra-boolean-cast": 2,
      "no-extra-parens": 0,
      "no-extra-semi": 0,
      "no-fallthrough": 2,
      "no-floating-decimal": 2,
      "no-func-assign": 2,
      "no-implied-eval": 2,
      "no-inline-comments": 0,
      "no-inner-declarations": [2, "functions"],
      "no-invalid-regexp": 2,
      "no-irregular-whitespace": 2,
      "no-iterator": 2,
      "no-label-var": 2,
      "no-labels": 2,
      "no-lone-blocks": 2,
      "no-lonely-if": 0,
      "no-loop-func": 0,
      "no-mixed-requires": 0,
      "no-mixed-spaces-and-tabs": 2,
      "no-multi-spaces": 2,
      "no-multi-str": 2,
      "no-multiple-empty-lines": [2, { "max": 1 }],
      "no-native-reassign": 2,
      "no-negated-in-lhs": 2,
      "no-nested-ternary": 0,
      "no-new": 2,
      "no-new-func": 0,
      "no-new-object": 2,
      "no-new-require": 2,
      "no-new-wrappers": 2,
      "no-obj-calls": 2,
      "no-octal": 2,
      "no-octal-escape": 2,
      "no-param-reassign": 0,
      "no-path-concat": 0,
      "no-process-env": 0,
      "no-process-exit": 0,
      "no-proto": 0,
      "no-redeclare": 2,
      "no-regex-spaces": 2,
      "no-restricted-modules": 0,
      "no-return-assign": 2,
      "no-script-url": 0,
      "no-self-compare": 2,
      "no-sequences": 2,
      "no-shadow": 0,
      "no-shadow-restricted-names": 2,
      "no-spaced-func": 2,
      "no-sparse-arrays": 2,
      "no-sync": 0,
      "no-ternary": 0,
      "no-this-before-super": 2,
      "no-throw-literal": 2,
      "no-trailing-spaces": 2,
      "no-undef": 2,
      "no-undef-init": 2,
      "no-undefined": 0,
      "no-underscore-dangle": 0,
      "no-unexpected-multiline": 2,
      "no-unneeded-ternary": 2,
      "no-unreachable": 2,
      "no-unused-expressions": 0,
      "no-unused-vars": [2, { "vars": "all", "args": "none" }],
      "no-use-before-define": 0,
      "no-var": 0,
      "no-void": 0,
      "no-warning-comments": 0,
      "no-with": 2,
      "object-curly-spacing": 0,
      "object-shorthand": 0,
      "one-var": [2, { "initialized": "never" }],
      "operator-assignment": 0,
      "operator-linebreak": [2, "after", { "overrides": { "?": "before", ":": "before" } }],
      "padded-blocks": 0,
      "prefer-const": 0,
      "quote-props": 0,
      "quotes": [2, "single", "avoid-escape"],
      "radix": 2,
      "semi": [2, "never"],
      "semi-spacing": 0,
      "sort-vars": 0,
      "space-after-keywords": [2, "always"],
      "space-before-blocks": [2, "always"],
      "space-before-function-paren": [2, "always"],
      "space-in-parens": [2, "never"],
      "space-infix-ops": 2,
      "space-return-throw-case": 2,
      "space-unary-ops": [2, { "words": true, "nonwords": false }],
      "spaced-comment": [2, "always", { "markers": ["global", "globals", "eslint", "eslint-disable", "*package", "!"] }],
      "strict": 0,
      "use-isnan": 2,
      "valid-jsdoc": 0,
      "valid-typeof": 2,
      "vars-on-top": 0,
      "wrap-iife": [2, "any"],
      "wrap-regex": 0,
      "yoda": [2, "never"]
    }
  }
```

### vue 参考

https://juejin.cn/post/6844904080335355918

https://segmentfault.com/a/1190000011853167

## 错误补充

1. TypeError: merge is not a function

使用 `webpack-merge`时进行环境分区时编译报错

```js
const { merge } = require('webpack-merge')
```

`webpack5`正确写法是

```js
const { merge } = require('webpack-merge')
```

2. 进行 lint 检查时，报错：

`error Do not use "@ts-ignore" because it alters compilation errors @typescript-eslint/ban-ts-comment`

在.eslintrc.js 中添加规则

```js
rules: {
    // ......
    '@typescript-eslint/ban-ts-comment': 'off'
  }
```
