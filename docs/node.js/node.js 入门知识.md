# node.js 入门知识


## node.js

Node.js 是一种 JavaScript 运行环境。它可以运行 JavaScript 代码，这样就使得 JavaScript 能够脱离浏览器运行。Node.js 是基于 Google 的 V8 引擎的封装。

[官网下载](https://nodejs.org/en/download/package-manager)  提供多种下载方式。其中

`Prebuilt Installer` 包含了安装向导的可执行文件（例如 .exe 或 .msi 文件），可以引导用户完成软件的安装过程。

`Prebuilt Binaries` 不包含安装向导。用户需要手动将这些文件放置在适当的位置。用户需要手动解压、配置环境变量等。

这两种方式都包含了 [[#npm]] 。


``` bash
node -v

npm -v
```
## nvm

`nvm (Node Version Manager)` 是用来管理 Node.js 的版本的，方便切换不同版本的 Node.js 。

[nvm 下载地址](https://github.com/coreybutler/nvm-windows)

`node.js` 有多种版本管理工具，需要自己下载安装。

`nvm` 需要设置环境变量 `NVM_HOME` 和 `NVM_SYMLINK` 。 一般安装会自己设置好。


``` bash

# 显示所有可以下载的 Node.js 版本： 
nvm list available

# 显示已安装的 Node.js 版本： 
nvm list

# 安装指定的 Node.js 版本： 
nvm install 18.20.1

# 安装最新的 Node.js 版本： 
nvm install latest

# 删除某个版本的 Node.js：  
nvm uninstall 18.20.0

# 切换到某个版本的 Node.js： 
nvm use 18.20.1

```


## npm

`npm (Node Package Manager)` 是 JavaScript 的包管理工具，并且也是 Node.js 默认的包管理工具。

`npm` 由三个不同的组件组成：
- 网站
- 命令行接口 (CLI)
- 注册表

使用 [网站](https://npmjs.com/) 发现包、设置配置、以及管理 npm 体验的其他方面。例如，你可以设置 [组织](https://www.npmjs.com/features) 来管理对公共或私有包的访问。

[CLI](https://npm.nodejs.cn/cli/npm) 从终端运行，是大多数开发者与 npm 交互的方式。

[注册表](https://npm.nodejs.cn/misc/registry) 是 JavaScript 软件及其周围元信息的大型公共数据库。


[npm 中文网](https://npm.nodejs.cn/about-npm)


npm 的服务器部署在国外，有时候访问的速度非常慢，甚至无法访问。所以配置淘宝镜像可以提高下载速度。

一般直接命令配置即可: 

``` bash
# 设置镜像
npm config set registry https://registry.npmmirror.com/

# 安装包
npm install xxx
npm i xxx  # 简写. 下面只用这个简写


# 一键安装所有依赖，用于拉取别人代码时的操作
npm i

# 指定包名
npm i jquery@3.7.1

# 删除
npm remove xxx
npm r xxx    # 简写. 下面只用这个简写 

# 全局删除
# 有点类似于git, 默认是在工程文件下操作的
npm r -g xxx  

```

包依赖分为`生成依赖`和 `开发依赖`。

``` bash

# 生产依赖, -S, 等效于--save， -S 是默认选项
# 包信息保存在 package.json 中 dependencies 属性
npm i -S xxx 

# 开发依赖。 -D 等效于--save-dev
# 包信息保存在 package.json 中 devDependencies 属性
npm i -D xxx
```


![](docs/assets/d39b10a3a6e540c0ab7e8760876d9bd6.png)

### 配置命令别名

通过配置命令别名可以更简单的执行命令。主要是针对 package.json 文件中的 scripts 标签来配置。
比如我们配置了 server 和 start 标签，都是指向 testArr.js 文件：

``` json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "server": "node testArr.js",
    "start": "node testArr.js"
  },
```

配置完之后，我们就可以使用别名来执行命令：
``` bash

npm run server
npm run start   

# 或者, start 别名比较特别，可以省略 run
npm start 
```


补充说明：
1、npm start 是项目中常用的一个命令，一般用来启动项目。
2、npm run 有自动向上级目录查找的特性，跟 require 函数也一样。向上级目录查找这个特性很重要，否则在多层级的目录下，要重复创建相同的包。有了向上查找特别，只需要在最外层创建一个包即可。
3、对于陌生的项目，我们可以通过查看 scripts 属性来参考项目的一些操作。


> [!quote]
> 1. [系列学习前端之第 9 章：一文搞懂 Node.js 和 nvm，掌握 npm\_为什么要切换node-CSDN博客](https://blog.csdn.net/BiandanLoveyou/article/details/137361246)

