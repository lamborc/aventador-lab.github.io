# Nodejs

## yarn command

### yarn add local

## NPM 发布

### private package to public

> 如果发布 项目名称 @<npmuser>/<projectName> 默认为 private 发布,可通过 --access publish 参数改为 public

```bash
npm login # login
npm publish --access public #
```

### NPM package.json 属性

> module :es6 发布模块

> files : "files"属性的值是一个数组，内容是模块下文件名或者文件夹名，如果是文件夹名，则文件夹下所有的文件也会被包含进来（除非文件被另一些配置排除了）
> 你也可以在模块根目录下创建一个".npmignore"文件（，写在这个文件里边的文件即便被写在 files 属性里边也会被排除在外，这个文件的写法".gitignore"类似。

> main: main 属性指定了程序的主入口文件。意思是，如果你的模块被命名为 foo，用户安装了这个模块并通过 require("foo")来使用这个模块，那么 require 返回的内容就是 main 属性指定的文件中 module.exports 指向的对象。
> 它应该指向模块根目录下的一个文件。对大对数模块而言，这个属性更多的是让模块有一个主入口文件，然而很多模块并不写这个属性。

> bin : 很多模块有一个或多个需要配置到 PATH 路径下的可执行模块，npm 让这个工作变得十分简单（实际上 npm 本身也是通过 bin 属性安装为一个可执行命令的）
> 如果要用 npm 的这个功能，在 package.json 里边配置一个 bin 属性。bin 属性是一个已命令名称为 key，本地文件名称为 value 的 map 如下

```js
{"bin":{"myapp":"./cli.js"}}
```

> > > 模块安装的时候，若是全局安装，则 npm 会为 bin 中配置的文件在 bin 目录下创建一个软连接（对于 windows 系统，默认会在 C:\Users\username\AppData\Roaming\npm 目录下），若是局部安装，则会在项目内的./node_modules/.bin/目录下创建一个软链接。
> > > 因此，按上面的例子，当你安装 myapp 的时候，npm 就会为 cli.js 在/usr/local/bin/myapp 路径创建一个软链接。
> > > 如果你的模块只有一个可执行文件，并且它的命令名称和模块名称一样，你可以只写一个字符串来代替上面那种配置，例如：

## NVM 管理 nodejs version

> 常用命令

```bash
nvm ls
nvm ls-remote --lts  # 選擇安裝 LTS (Long-term support，長期支援) [not window]
nvm ls available     # window

nvm on

```

### Yarn

> 查看当前源

```bash
yarn config get registry                                    #
https://registry.npmjs.org/                                 # 查看当前源
npm config get cache                                        # 查看
npm cache clean --force                                     # 删除缓存
npm cache verify                                            # 验证缓存数据的有效性和完整性


npm install --registry=https://registry.npm.taobao.org      # 单次使用源
npm config set registry https://registry.npmjs.org/         # 还原 npm 源
yarn install --force                                        # 强制重新下载所有包
```


### NPM

> npm 常用

```bash

```

### Node-sass ISSUE

> install failed .

```bash
npm i -g node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```

### Browserlist query 

```bash 
 npx browserslist "last 1 version, >1%"

```