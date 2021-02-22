# Nodejs


## yarn command

### yarn add local

## NPM 发布

### private package to public 

> 如果发布 项目名称 @<npmuser>/<projectName> 默认为private 发布,可通过 --access publish 参数改为public

```bash
npm login # login
npm publish --access public # 
```

### NPM package.json 属性

>  module :es6 发布模块

> files : "files"属性的值是一个数组，内容是模块下文件名或者文件夹名，如果是文件夹名，则文件夹下所有的文件也会被包含进来（除非文件被另一些配置排除了）
你也可以在模块根目录下创建一个".npmignore"文件（，写在这个文件里边的文件即便被写在files属性里边也会被排除在外，这个文件的写法".gitignore"类似。

> main: main属性指定了程序的主入口文件。意思是，如果你的模块被命名为foo，用户安装了这个模块并通过require("foo")来使用这个模块，那么require返回的内容就是main属性指定的文件中 module.exports指向的对象。
它应该指向模块根目录下的一个文件。对大对数模块而言，这个属性更多的是让模块有一个主入口文件，然而很多模块并不写这个属性。

> bin : 很多模块有一个或多个需要配置到PATH路径下的可执行模块，npm让这个工作变得十分简单（实际上npm本身也是通过bin属性安装为一个可执行命令的）
如果要用npm的这个功能，在package.json里边配置一个bin属性。bin属性是一个已命令名称为key，本地文件名称为value的map如下

```js
{"bin":{"myapp":"./cli.js"}}
```

>>> 模块安装的时候，若是全局安装，则npm会为bin中配置的文件在bin目录下创建一个软连接（对于windows系统，默认会在C:\Users\username\AppData\Roaming\npm目录下），若是局部安装，则会在项目内的./node_modules/.bin/目录下创建一个软链接。
因此，按上面的例子，当你安装myapp的时候，npm就会为cli.js在/usr/local/bin/myapp路径创建一个软链接。
如果你的模块只有一个可执行文件，并且它的命令名称和模块名称一样，你可以只写一个字符串来代替上面那种配置，例如：