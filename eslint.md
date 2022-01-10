[官方文档] ()

# 检查工具

Eslint 工具来实现对 JavaScript 和 Typescript 代码的检查
stylelint 工具对样式代码进行代码检查

# eslint 使用

1. 安装
2. eslint --init 命令来生成这个配置文件

| | | |
|:--:| | |

# eslint 介绍

eslint 是一个插件化的检查工具(eslint 的设计是把检查工具和检查规则之间解耦了）;安装好 eslint 的开发依赖之后，我们还可以并且需要选择安装一个我们中意的检查规则。

# 检查的功能

- 语言语法检查(符串引号或者函数调用括号没有匹配)
- 编码错误检查(使用一个不存在的变量或者变量定义了却没有使用等问题)
- 代码风格检查(没有使用分号等问题)

# 检查的方式

## 编码时检查:自动实时检查（vscode 的 eslint 代码检查功能）

打开 vscode 的 setting -> 找到 eslint -> 打开 setting.json 这几个步骤来找到相关的配置文件

```
//配置eslint
    "editor.codeActionsOnSave": { // 保存时自动fix
        "source.fixAll.eslint": true
    },
    "eslint.quiet": true,   // warning时不报红色下划线，可用于处理no-console规则爆的warning
```

报的是代码触犯了规则的提示，那么就可以修改项目下的检查规则文件（.eslintrc.js）

## 编码后检查:保存后自动检查当前文件

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

## 构建前检查:构建执行前检查

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

## 提交前检查:git commit 前检查

原理：通过 Git Hooks 实现的，Git Hook 也称之为 git 钩子，每个钩子都对应一个任务，通过 shell 脚本可以编写钩子任务触发时要具体执行的操作

1.  shell
    关注 git 提交前的代码检查，所以我们关注 git commit 这个钩子，使用步骤如下：

    - 编写 hook 任务：项目的 .git/hooks 文件夹下新建一个 pre-commit 文件

    ```
    #!/bin/sh
    echo "before commit"
    ```

    - 触发钩子：项目下执行 git commit 命令
      <u>git commit 命令执行后，可以发现 commit 操作不管是否成功，都可以看到输出的 before commit 信息。</u>

2.  使用 husky 实现：编写 node 代码替代 shell 代码(帮助我们达成以 js 代码的方式来编写 hook 任务的目的。)  
    (1): 安装 husky  
    安装好这个模块后，就可以在. git/hooks 文件夹下看到新添加的文件(pre-** post-** 等)  
    (2): 配置 husky 的 hook 任务：如下 package.json 任务

        "scripts": {
            "test1": "echo before commit",
            "test2": "node test2.js"
        },

        "husky": {
            "hooks": {
            "pre-commit": "yarn test2"
            }
        },

(3): 触发钩子：git add -> git commit[单任务===>任务流]

通过 lint-staged 来配合 husky 来实现
实现步骤如下：

- 安装 husky 和 lint-staged 模块

```
yarn add husky --dev
yarn add lint-staged --dev
```

- 配置 husky 的 hook 任务流：如下 package.json 任务
```
"scripts": {
    "precommit": "lint-staged"
},
"husky": {
    "hooks": {
        "pre-commit": "yarn precommit"
    }
},
"lint-staged": {
    "\*.js":[
    "eslint --fix",
    "git add"
    ]
}
```

实现单任务相比而言，使用 lint-staged 之后，git commit 命令只有成功执行才会触发 lint stage 中的操作，且这些操作(对 js 文件有效)。

- 在我们执行 git commit 之后，就会触发 husky 配置的 pre-commit 的 hook 任务，而这个 hook 任务又把任务 交给了 lint-staged 处理，进而通过 lint-staged 实现对 js 文件的代码检查以及自动风格修复后（还是错误则会中断提交）重新 add，而后再执行 commit 任务，保证了代码在提交前一定经过了代码检查 

