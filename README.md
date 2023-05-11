## 如何发布一个 npm 包

![参考](https://blog.csdn.net/BASK2312/article/details/128145705)
![package.json官网](https://docs.npmjs.com/cli/v9/configuring-npm/package-json)
![新包名规范](https://blog.csdn.net/wGL3k77y9fR1k61T1aS/article/details/79135673)
![版本号semver 规范](https://semver.org/)
![版本号测试工具](https://semver.npmjs.com/)

### 1. 准备 npm 账号 & 示例包代码；

#### 1. 准备工具

本地需要安装 Node.js 及 npm，npm 将随 Node.js 一起安装

#### 2. 注册 npm 账号

第一步先到 npm 注册个账号 `www.npmjs.com/signup`，跟着注册步骤操作就行

#### 3. 示例代码

`index`

```js
export const log = (msg) => {
	console.log('打印之前');
	console.log('msg');
	console.log('打印之后');
};
```

### 2. 初始化 & 配置 package.json 文件；

要发布一个 npm 包，需要创建一个 `package.json` 文件，用来描述包信息及依赖包。使用 `npm init -y` 命令创建一个默认的 package.json 文件，默认生成内容如下：

```js
{
  "name": "test-release-package-lyk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```

如果关联了 git 仓库就会多

```js

{
  "name": "utils", // 包名，必填且唯一
  "version": "1.0.0", // 版本号，必填，需遵循语义化版本规范
  "description": "", // 包描述信息，利于包检索
  "main": "index.js", // 入口文件，不设置默认为根文件夹下的 index.js
  "scripts": { // 脚本命令
    "test": "echo "Error: no test specified" && exit 1"
  },
  "repository": { // 项目代码仓库
    "type": "git",
    "url": "git+https://github.com/jizai1125/joker1125-utils.git"
  },
  "keywords": [], // 关键字，利于包检索
  "author": "", // 作者信息
  "license": "ISC", // 许可证协议
  "bugs": { // 问题反馈地址
    "url": "https://github.com/jizai1125/joker1125-utils/issues"
  },
  // 项目主页
  "homepage": "https://github.com/jizai1125/joker1125-utils#readme"
}
```

`修改后的package.json`

```js

{
  "name": "log-utils", // 包名，必填且唯一       npm上有没有重复的，有的话不行
  "version": "1.0.0", // 版本号，必填，需遵循语义化版本规范
  "description": "log utils", // 包描述信息，利于包检索
  "main": "index.js", // 入口文件，不设置默认为根文件夹下的 index.js
  "repository": { // 项目代码仓库
    "type": "git",
    "url": "git+https://github.com/jizai1125/joker1125-utils.git"
  },
  "keywords": ["log", "js-utils"],
  "author": "lyka3156", // 作者信息
  "license": "ISC", // 许可证协议
  "bugs": { // 问题反馈地址
    "url": "https://github.com/jizai1125/joker1125-utils/issues"
  },
  // 项目主页
  "homepage": "https://github.com/jizai1125/joker1125-utils#readme"
}
```

要发布一个 npm 包，name 和 version 字段是必填的；

包名（name 字段）命名规则：

1. 包名长度不能超过 214 个字符（命名空间也算在里面）；
2. 包名所有字符必须小写；
3. 包名可以由连字符 - 组成；
4. 包名不能包含空格，不能以 . 或者 \_ 开头，不能包含 ~)('!\* 中的任意一个字符；
5. 包名不能包含任何非 url 安全字符（因为包名将作为 url 的一部分）；
6. 包名不能与 Node.js / io.js 的核心模块、保留字或黑名单相同，例如 http。

-   可以使用 [validate-npm-package-name](https://github.com/npm/validate-npm-package-name) 包来检查包名是否合法

版本号（version 字段）则需要遵循 semver 规范。

-   可以使用 [node-semver](https://github.com/npm/node-semver) 来检查

### 3. 确定发布的包版本；

#### 1. 版本号格式

格式：MAJOR.MINOR.PATCH ，值非负整数，且禁止在数字前面补 0

1.  MAJOR：主版本号
2.  MINOR：次版本号
3.  PATCH:：修订号

#### 2. 版本号递增逻辑

1. 当有破坏性不兼容的 API 变更时，升级主版本号
2. 当新增一些功能特性时，升级次版本号
3. 当做一些 bug 修复时，升级修订号

当某个版本还不稳定的时候，还可能要先发布一个先行版本，具体可看 semver 规范。

`可以使用 npm version 命令来修改版本号`

```js
npm version [newversion  | major | minor | patch | premajor | preminor | prepatch | prerelease | from-git]
```

`例子`

```js
# 0.0.0 => 0.0.1
npm version patch
# 0.0.0 => 0.1.0
npm version minor
# 0.0.0 => 1.0.0
npm version major
# 0.0.0 => 0.0.1-0
# === 先行版本 ===
npm version prepatch
# 0.0.0 => 0.0.1-alpha.0
npm version prepatch --preid=alpha
# 0.0.1-alpha.0 => 0.0.1-alpha.1
npm version prerelease
```

`命令行选项`

-   --preid 指定先行版本的标识符，例如 1.2.3-rc.4 中的 rc
-   -m 或 --message 可以指定 commit 信息，例如 npm version patch -m "Upgrade to %s for reasons” ;
-   -no-git-tag-version 取消创建 git tag 和 commit 信息

`其他说明`

-   执行 npm version 命令时，会修改 package.json 、package-lock.json 的 version 字段为对应版本；若当前使用 git 来管理文件，还会创建一条 commit 信息和创建一个 tag，可通过指定 -no-git-tag-version 取消生成 commit 和 tag。
-   执行 npm version 前，git 工作区和暂存区确保没有文件，否则会执行失败。

### 4. 发布 npm 包；

#### 1. npm 登录 & 确定源地址

因为我们要发布到官方源上面，所以要确保源地址为官方地址 http://registry.npmjs.org 或 https://registry.npmjs.com，可以通过 npm config get registry 命令查看当前 registry 源

如需修改 registry 可通过以下四种方式：

1. 在全局配置 .npmrc ，可通过命令 npm config set registry= http://registry.npmjs.org/
2. 在当前项目配置，在当前项目中添加配置文件 .npmrc

```js
// .npmrc
registry = http://registry.npmjs.org/
```

3. 发布包时指定 --registry 选项，npm publish —registry=http://registry.npmjs.org/
4. 在当前项目的 package.json 中通过 publishConfig 字段指定

```js
// package.json
{
  "publicConfig": {
    "registry": "http://registry.npmjs.org/"
  }
}
```

#### 2. 发布版本

### 5. 使用 cli 工具 release-it 来自动管理版本、发布包等；

### 6. 学习 npm init release-it 原理
