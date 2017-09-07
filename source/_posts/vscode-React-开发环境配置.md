---
title: vscode React 开发环境配置
date: 2017-09-07 22:06:07
tags: [vscode, React]
---

最近开始学习 `React`，配置了下 `vscode` 的 `eslint` 和格式化

我的 `es6` 格式：

* 结尾加分号
* 2个空格缩进
* 用单引号

## 依赖

### npm

* [eslint](https://github.com/eslint/eslint)
* [babel-eslint](https://github.com/babel/babel-eslint)

使用 [cnpm](https://npm.taobao.org/) 或者 `npm config set registry https://registry.npm.taobao.org` 可以加快 `npm install` 速度

### vscode 插件

* [eslint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
* [Prettier - JavaScript formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
* [EditorConfig for VS Code](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig) (用来控制不同语言的文件的格式）

## 配置

### 配置 eslint 解析器、规则

在项目根目录创建 `.eslintrc.json` 文件

```json
// 中文文档：http://eslint.cn/docs/rules/
// 英文文档：https://eslint.org/docs/rules/
{
  "parser": "babel-eslint",
  "env": {
    "browser": true,
    "commonjs": true,
    "es6": true,
    "node": true
  },
  "parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true,
      "classes": true,
      "defaultParams": true
    }
  },
  "rules": {
    "no-const-assign": 1,
    "no-extra-semi": 0,
    "no-fallthrough": 0,
    "no-empty": 0,
    "no-mixed-spaces-and-tabs": 0,
    "no-redeclare": 0,
    "no-this-before-super": 1,
    "no-undef": 1,
    "no-unreachable": 1,
    "no-use-before-define": 0,
    "constructor-super": 1,
    "curly": 0,
    "eqeqeq": 0,
    "func-names": 0,
    "valid-typeof": 1,
    "semi": 1, // 结尾有分号
    "quotes": [1, "single", { "avoidEscape": true }], // 使用单引号
    "indent": [1, 2, { "SwitchCase": 1 }] // 使用2个空格缩进
  }
}
```

### 配置 eslint 忽略的文件

有些文件不需要使用到 `eslint` 减少编辑器报错

在项目根目录创建 `.eslintignore`

```text
build/*.js
config/*.js
node_modules/*.js
```

### 配置 Prettier

在项目根目录创建 `.prettierrc`

```json
// 更多配置见 https://github.com/prettier/prettier
{
  "singleQuote": true // 格式化使用单引号
}
```

### 配置 EditorConfig

配置不同语言文件的不同缩进格式

在 `HOME` 目录下，创建文件。`vim ~/.editorconfig` 

```conf
# EditorConfig is awesome: http://EditorConfig.org

# top-most EditorConfig file
root = true

# Unix-style newlines with a newline ending every file
[*]
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true

# Matches multiple files with brace expansion notation
# Set default charset
[*.{js,py}]
charset = utf-8

# 4 space indentation
[*.py]
indent_style = space
indent_size = 4


[*.{js, html, css}]
indent_style = space
indent_size = 2

[Makefile]
indent_style = space
indent_size = 4

# Indentation override for all JS under lib directory
[lib/**.js]
indent_style = space
indent_size = 2

# Matches the exact files either package.json or .travis.yml
[{package.json,.travis.yml}]
indent_style = space
indent_size = 2
```

### 配置 vscode

编辑 vscode 的配置文件

#### 保存自动格式化

```json
{
    "editor.formatOnSave": true
}
```

#### 在资源管理器中不显示 node_modules

```json
{
    "files.exclude": {
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/CVS": true,
        "**/.DS_Store": true,
        "**/node_modules": true
    }
}
```