# cw项目开发规范

## 目录

- [**NodeJs**](#nodejs)
- [**开发工具**](#开发工具)
- [**项目初始化**](#项目初始化)
- [**调试**](#调试)
- [**一些代码规范风格**](#一些代码规范风格)
- [**性能相关**](#性能相关)
- [**git版本分支**](#git版本分支)
- [**git提交代码**](#git提交代码)
- [**框架相关简介**](#框架相关简介)
- [**环境变量**](#环境变量)
- [**组件**](#组件)
- [**单元测试**](#单元测试)
- [**代码覆盖率**](#代码覆盖率)

## NodeJs
------
- [NodeJs](https://nodejs.org/en/)版本变化比较快，建议使用LTS版本（现在为8.9.*），详情请参考[LTS Schedule](https://github.com/nodejs/Release#release-schedule)
- nodejs和npm的仓库托管在S3上，在国内访问十分困难，这里可以用[淘宝的镜像](npm.taobao.org)站代替

```
npm config set registry https://registry.npm.taobao.org
```

- [npm](https://www.npmjs.com/)主页：https://www.npmjs.com/  ，判断一个包的好坏可简单通过下载数量和github star数

## 开发工具
------
- 安装vscode

- 安装格式化插件 Beautify

- 安装语法检查插件 ESLint

- 安装 EditorConfig插件,把[.editorconfig](./.editorconfig)拷贝到项目根目录

- tab配置制表符大小为2, 并将缩进转换为tab制表符（可通过EditorConfig插件控制）
```javascript
// 在用户设置默认
{ "editor.tabSize": 2 }
```

- 换行, linux 默认 LF, windows 默认 CRLF, 在windows下也设置为LF, 与linux兼容（可通过EditorConfig插件控制）
```javascript
// 在用户设置默认
// 默认行尾字符。使用 \n 表示 LF，\r\n 表示 CRLF。
{ "files.eol": "\n"}
```

## 项目初始化

([cw-init工具](https://www.npmjs.com/package/cw-init))
------

- 安装工具
```bash
$ npm install cw-init -g
```

- 初始化项目, 可选择cw-egg或cw-koa-ts
```bash
$ cw-init  # 提示可选择cw-egg或cw-koa-ts. [dir]可指定项目目录，不指定则在当前目录创建
```

- 启动
```bash
$ cd [dir] # [dir]是项目目录
$ npm i # 安装依赖
$ npm run dev # 开发环境
$ npm run test # 单元测试
$ npm run cov # 代码覆盖率
$ npm run start # 生产环境
$ npm run lint # 检查语法
```

## 调试
------
vscode开发环境下, 按f5

## 一些代码规范风格

参照 [es6](http://es6.ruanyifeng.com/#docs/style)

### 强烈建议通看一遍[es6](http://es6.ruanyifeng.com), 以下的规范大都参照es6
------
- 使用tab缩进, 设置制表符大小为2

- 函数的代码注释
使用vscode自带的生成。输入'/**', 再按回车

```javascript
/**
 * 分配workerId
 * @param {Array} alivePids
 * @param {Object} workers
 * @param {Array} workerIds
 */
function allocateWorkerId(alivePids, workers, workerIds) {
  const r = {};
  const sendTo = {};

  const alivePKs = alivePids.map(p => `w_${p}`);
  Object.keys(workers).forEach((k) => {
    if (alivePKs.indexOf(k) < 0) {
      const wid = workers[k];
      workerIds.push(wid);
    } else {
      r[k] = workers[k];
    }
  })
}
```

- 块级作用域

使用let取代var

优先使用const常量定义, 所有函数都应该设置为常量

- 字符串

静态字符串一律使用单引号或反引号，不使用双引号。动态字符串使用反引号

- 解构赋值

- 数组  

使用扩展运算符(...)拷贝数组

```javascript
// bad
const len = items.length;
const itemsCopy = [];
let i;
for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i];
}
// good
const itemsCopy = [...items];
```

- 函数  

尽量用箭头函数代替, 简洁且绑定了this, 箭头函数取代Function.prototype.bind, 不再用 self/_this/that

```javascript
const obj = {
  t1: function(){
    const d1 = function(){
      console.log('d1', this);
    }
    const d2 = () => {
      console.log('d2', this);
    }
    d1();  // this 不是 obj
    d2();  // this = obj
  }
}
obj.t1();
```

使用默认值语法设置函数参数的默认值

```javascript
// bad
function handleThings(opts) {
  opts = opts || {};
}
// good
function handleThings(opts = {}) {
  // ...
}
```

异步函数使用async await

- Class

总是用 Class，取代需要 prototype 的操作

- import/export, 目前cw-egg框架未支持此语法特性

- 命名规范

避免单字母命名。命名应具备描述性

使用驼峰式命名对象、函数和实例

函数驼峰式, 首字母小写, 私有函数可以简单使用下划线_作为前缀区分

属性字段驼峰式, 首字母小写。

私有字段可以简单使用下划线_作为前缀区分。建议使用symbol定义私有字段

class类名采用驼峰式, 首字母大写

- 安装格式化工具 js-beautify for VS Code

- [ESLint](https://eslint.org/)

一个语法规则和代码风格的检查工具，可以用来保证写出语法正确、风格统一的代码

[airbnb-base](https://www.npmjs.com/package/eslint-config-airbnb-base)配置

```bash
npm install --save-dev eslint
npm install --save-dev eslint-config-airbnb-base eslint-plugin-import
```

新建一个.eslintrc文件，配置[ESLint](https://eslint.org/)

```javascript
{
  "extends": "standard",
  "rules": {
    "semi": [ 2, "always"],
    "semi-spacing": 1,
    "space-before-function-paren": 0,
    "no-trailing-spaces": 1,
    "eol-last": 0,
    "indent": [2, "tab"],
    "comma-dangle": 0,
    "curly": 0,
    "spaced-comment": 1,
    "no-tabs": 0,
    "generator-star-spacing": 0
  }
}
```

```bash
# 检查指定文件
eslint index.js
```

## 性能相关
------

- 尽量避免使用全局变量

```javascript
// cache为全局变量
global.cache
```

- 对于复杂的回调函数, 建议抽取出来, 定义在外部

```javascript
fs.read(function(err, data){...});
// ==>
const f1 = function(err, data){...};
fs.read(f1);
```

- for/forEach里面的定义的函数, 要抽取出来, 定义在外部

```javascript
// 这里的函数f1, 要抽取出来定义在外部, 否则在大循环/高并非, 容易导致内存占用高和泄露 
// bad
arr.forEach(function(){
  const f1 = function(...){ ... };
  ...
  f1();
})

// ==>
// good
const f1 = function(...){ ... };
...
arr.forEach(function(){
  f1();
})
```

- try catch 内部的代码块不会被node v8优化(如缓存), 若使用了trycatch则内部代码要封装成函数放在外部

## git版本分支
------

- 项目目前暂未按严格的git工作流, 只分dev和master分支, dev开发, master发布

## git提交代码
------

- 添加必要的提交描述(功能, bug相关)

- 先拉取, 检查语法, 再推送
```bash
# 检查语法
npm run lint
```

## 框架相关简介
------
使用cw-init初始化, 可选择[cw-egg](https://github.com/maidol/egg-sample)或[cw-koa-ts](https://github.com/zubincheung/koa-ts)。其中的cw-egg[基于egg框架](https://eggjs.org/zh-cn/)

- 底层都是基于koa2

- 语言与框架并无必然的关系, typescript/javacript 都只是一种语言

在应用上层, 有需求的话, 框架都可以用typescript/javascript

## 环境变量 
------
- NODE_ENV

- EGG_SERVER_ENV

## 组件
------

- 日志组件 [cw-logger](https://github.com/maidol/cw-logger)

- 数据库 [mysql](https://github.com/mysqljs/mysql), [promise-mysql](https://github.com/maidol/node-promise-mysql)

- 数据校验 [joi](https://github.com/hapijs/joi)


## 单元测试

[cw-egg]
------
### 对于一些关键代码最好附加上单元测试
------

```bash
npm run test
```

- 测试用例执行时，应用是以 env: unittest 启动的，读取的配置也是 config.default.js 和 config.unittest.js 合并的结果
- 运行 npm run test 时会自动执行 test 目录下的以 .test.js 结尾的文件（默认 glob 匹配规则 test/**/*.test.js
- 在编写用例时往往想单独执行正在编写的用例，可以通过以下方式指定特定用例文件
```bash
TESTS=test/x.test.js npm test
```

- 指定 reporter
```bash
TEST_REPORTER=dot npm test
```

- 指定用例超时时间, 默认30s
```bash
TEST_TIMEOUT=5000 npm test
```

- 通过 argv 方式传参; egg-bin test 除了环境变量方式，也支持直接传参，支持 mocha 的所有参数，参见: [mocha usage](https://mochajs.org/#usage)
```bash
$ # npm 传递参数需额外加一个 `--`，参见 https://docs.npmjs.com/cli/run-script
$ npm test -- --help
$
$ # 等同于 `TESTS=test/**/test.js npm test`，受限于 bash，最好加上双引号
$ npm test "test/**/test.js"
$
$ # 等同于 `TEST_REPORTER=dot npm test`
$ npm test -- --reporter=dot
$
$ # 支持 mocha 的参数，如 grep / require 等
$ npm test -- -t 30000 --grep="should GET"
```

- 执行顺序。每个用例(describe)会按 before -> beforeEach -> it -> afterEach -> after 的顺序执行，而且可以定义多个

## 代码覆盖率

[cw-egg]
------
- egg-bin 已经内置了 [nyc](https://github.com/istanbuljs/nyc) 来支持单元测试自动生成代码覆盖率报告
```bash
npm run cov
```

- 环境配置
```txt
和 test 命令一样，cov 命令执行时，应用也是以 env: unittest 启动的，读取的配置也是 config.default.js 和 config.unittest.js 合并的结果
```
