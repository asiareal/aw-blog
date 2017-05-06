---
title: mac 如何更优雅的安装nodejs
date: 2017-05-06 21:22:05
tags:
  Node.js
---

> 刚开始玩node时候就是官网上直接下载node的安装包，后来发现玩的东西多后，node版本切换是个大问题。
 用了`n`觉得并不好用，后来才知道mac有`nvm`，简直方便的一米啊。
 
## 卸载已安装的node（如果你还没安装过，可以直接跳过）

node 命令在 `/usr/local/bin/node` ，npm 命令在全局 node_modules 目录中，具体路径为 `/usr/local/lib/node_modules/npm`

删除node：
```bash
#查看已经安装在全局的模块，以便删除这些全局模块后再按照不同的 node 版本重新进行全局安装
npm ls -g --depth=0 

#删除全局 node_modules 目录
sudo rm -rf /usr/local/lib/node_modules 
sudo rm /usr/local/bin/node #删除 node

#删除全局 node 模块注册的软链
cd  /usr/local/bin && ls -l | grep "../lib/node_modules/" | awk '{print $9}'| xargs rm 
```

## 安装`nvm`

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.29.0/install.sh | bash
```

## `nvm`使用方法

### 安装切换各版本 node

```bash
nvm install stable #安装最新稳定版 node，现在是 5.0.0
nvm install 4.2.2 #安装 4.2.2 版本
nvm install 0.12.7 #安装 0.12.7 版本

# 特别说明：以下模块安装仅供演示说明，并非必须安装模块
nvm use 0 #切换至 0.12.7 版本

#安装 mz-fis 模块至全局目录，安装完成的路径是 /Users/<你的用户名>/.nvm/versions/node/v0.12.7/lib/mz-fis
npm install -g mz-fis 

nvm use 4 #切换至 4.2.2 版本

#安装 react-native-cli 模块至全局目录，安装完成的路径是 /Users/<你的用户名>/.nvm/versions/node/v4.2.2/lib/react-native-cli
npm install -g react-native-cli 

nvm alias default 0.12.7 #设置默认 node 版本为 0.12.7
```

### 使用 .nvmrc 文件配置项目所使用的 node 版本

如果你的默认 node 版本（通过 nvm alias 命令设置的）与项目所需的版本不同，则可在项目根目录或其任意父级目录中创建 .nvmrc 文件，在文件中指定使用的 node 版本号，例如：

```bash
cd <项目根目录>  #进入项目根目录
echo 4 > .nvmrc #添加 .nvmrc 文件
nvm use #无需指定版本号，会自动使用 .nvmrc 文件中配置的版本
node -v #查看 node 是否切换为对应版本
```

## 安装`nrm`
`nrm` 是一个管理 npm 源的工具。用过 ruby 和 gem 的同学会比较熟悉，
通常我们会把 gem 源切到国内的淘宝镜像，这样在安装和更新一些包的时候比较快。
`nrm` 同理，用来切换官方 npm 源和国内的 npm 源（如: cnpm），当然也可以用来切换官方 npm 源和公司私有 npm 源。

```bash
npm install -g nrm
```

## `nrm`使用方法
```bash
nrm ls #列出可选的源

* npm ---- https://registry.npmjs.org/
  cnpm --- http://r.cnpmjs.org/
  taobao - http://registry.npm.taobao.org/
  eu ----- http://registry.npmjs.eu/
  au ----- http://registry.npmjs.org.au/
  sl ----- http://npm.strongloop.com/
  nj ----- https://registry.nodejitsu.com/

nrm use taobao #切换到淘宝

nrm add  <registry> <url> [home] #增加源

nrm del <registry> #删除源

nrm test #测试速度 

  npm ---- 891ms
  cnpm --- 1213ms
* taobao - 460ms
  eu ----- 3859ms
  au ----- 1073ms
  sl ----- 4150ms
  nj ----- 8008ms
```

## 小结

安装 `nvm` 和 `nrm` 更加方便了我们做不同node版本的开发，如果你之前不是这样玩的就赶快卸载，重新安装一下吧。
