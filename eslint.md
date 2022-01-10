[官方文档] ()

# eslint 介绍

eslint 是一个插件化的检查工具(eslint 的设计是把检查工具和检查规则之间解耦了）;安装好 eslint 的开发依赖之后，我们还可以并且需要选择安装一个我们中意的检查规则。

# 检查的功能

- 语言语法检查(符串引号或者函数调用括号没有匹配)
- 编码错误检查(使用一个不存在的变量或者变量定义了却没有使用等问题)
- 代码风格检查(没有使用分号等问题)

# 检查的方式

1. 编码时检查:自动实时检查（vscode 的 eslint 代码检查功能）
   打开 vscode 的 setting -> 找到 eslint -> 打开 setting.json 这几个步骤来找到相关的配置文件

```
//配置eslint
    "editor.codeActionsOnSave": { // 保存时自动fix
        "source.fixAll.eslint": true
    },
    "eslint.quiet": true,   // warning时不报红色下划线，可用于处理no-console规则爆的warning
```

报的是代码触犯了规则的提示，那么就可以修改项目下的检查规则文件（.eslintrc.js）

2. 编码后检查:保存后自动检查当前文件

```
//检查js
module.exports = {
  env: {
    browser: true,
    es6: true,
  },
  extends: [
    'airbnb-base',
  ],
  globals: {
    Atomics: 'readonly',
    SharedArrayBuffer: 'readonly',
  },
  parserOptions: {
    ecmaVersion: 2018,
  },
  rules: {
  },
};
//检查ts
module.exports = {
    "env": {
        "browser": true,
        "es6": true
    },
    "extends": [
        "airbnb-base"
    ],
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly"
    },
    <!-- 语法解析器语法解析器 -->
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaVersion": 2018
    },
    "plugins": [
        "@typescript-eslint"
    ],
    "rules": {
    }
};
```

3. 构建前检查:构建执行前检查

gulp 思路：

- 安装 eslint 并初始化 eslint 配置文件；
  `yarn add eslint --dev eslint --init`
- 下载安装 gulp-eslint 插件；

```
yarn add gulp-eslint --dev
```

- 编写 js 文件处理的代码检查切面；

```
// ... 其它代码
const eslint = require('gulp-eslint');
const script = (0 => {
return src(['scripts/*.js'])
      .pipe(eslint()) // 代码检查
      .pipe(eslint.format()) // 将lint结果输出到控制台。
      .pipe(eslint.failAfterError()) // lint错误，进程退出，结束构建。
      .pipe(babel({ presets: ['@babel/preset-env']}))
      .pipe(dest('temp'))
      .pipe(bs.reload( { stream: true } ))
}
// ... 其它代码
```

webpack 思路：

- 安装 eslint 并初始化 eslint 配置文件
- 安装 eslint-loader
- 编写 webpack 配置文件，为 js 文件加上这个 eslint-loader

```
rules: [
 {
  test: /.js$/,
  exclude: /node_modules/,
  use: [
   'babel-loader',
   'eslint-loader' // 更后的先执行
  ]
 }
]
```

4. 提交前检查:git commit 前检查

# 检查工具

Eslint 工具来实现对 JavaScript 和 Typescript 代码的检查  
stylelint 工具对样式代码进行代码检查
# eslint 使用

1. 安装
2. eslint --init 命令来生成这个配置文件

| | | |
|:--:| | |
