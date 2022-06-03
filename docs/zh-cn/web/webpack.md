
### 审查项目的 webpack 配置

https://cli.vuejs.org/zh/guide/webpack.html#%E5%AE%A1%E6%9F%A5%E9%A1%B9%E7%9B%AE%E7%9A%84-webpack-%E9%85%8D%E7%BD%AE

> vue inspect module.rules.0  :只审查第一条规则

> vue inspect --rule vue :指向一个规则或插件的名字

### Webpack + Babel-plugin-import 按需加载

> Deps: babel-plugin-import


### ISSUE 解决笔记

#### ConcurrentCompilationError: You ran Webpack twice

> ConcurrentCompilationError: You ran Webpack twice. Each instance only supports a single concurrent compilation at a time.

google 了很多文章,最后通过error stack 查看源码找到问题所在.

<div align="center">
    <img width="550px" src="/assets/img/webpack-error-210625.png" />
</div>

 - 第一步先找到错误根源

```bash
 at Object.<anonymous> (E:\EWork\aventador_work\quick-website-4rantd\ci\webserve.j
s:75:16)
```
 
看配置JS 

```javascript
const { webpack } = require('webpack');
const WebpackDevServer = require('webpack-dev-server');

const { R, join, dist } = require('./paths');

var config = require('./webpack/webpack.config');

const HMROptions = {
  contentBase: join(__dirname, 'dist', TARGET_TYPE),
  port: DEV_PORT,
  hot: true,
  open: false,
  // openPage: ['index.html'],
  injectClient: false,
  writeToDisk: true,
  historyApiFallback: true,
  publicPath: `http://localhost:${DEV_PORT}`,
  lazy: false,
  headers: {
    'Access-Control-Allow-Origin': '*',
  },
  disableHostCheck: true,
};

WebpackDevServer.addDevServerEntrypoints(config, HMROptions);   // 对应报错 line 75:16
var compiler = webpack(config, function (err, stats) {
  if (err) {
    console.error('>>>>>>>>>>>>>>>Error>>>>>\n', err);
  }
});

// console.log('>>>>WebpackDevServer>>>>>DEBUG>>>>>', config);
const server = new WebpackDevServer(compiler, HMROptions);
```

** 于是跟进去看做了什么? ** 

```javascript
// Export this logic,
// so that other implementations,
// like task-runners can use it
Server.addDevServerEntrypoints = require('./utils/addEntries');

```

> 看不出什么来, 继续往上查, 

```bash
at new Server ** \webpack-dev-server\lib\Server.js:118:10
```

**webpack-dev-server 源码**

```javascript

// line 118
    this.setupDevMiddleware();
                ‖
                ‖
                ︾
  setupDevMiddleware() {
    // middleware for serving webpack bundle
    this.middleware = webpackDevMiddleware(
      this.compiler,  // 还是看不出原因来
      Object.assign({}, this.options, { logLevel: this.log.options.level })
    );
  }
```

> 继续向上查找:  at Compiler.watch *\node_modules\webpack\lib\Compiler.js:383:19

**webpack 源码**

```javascript
	watch(watchOptions, handler) {                          // line: 383
		if (this.running) { // 这里报错了
			return handler(new ConcurrentCompilationError());
		}

		this.running = true;
		this.watchMode = true;
		this.watching = new Watching(this, watchOptions, handler);
		return this.watching;
	}
```

** 分析原因: **
> 应该是 : new WebpackDevServer 时,webpack instance 已经存在,这说明webpack 配置某个地方会自动实例化. 

> 所以继续检查配置文件

```javascript
// 严重怀疑这里有问题,看一下源码去
var compiler = webpack(config, function (err, stats) {
  if (err) {
    console.error('>>>>>>>>>>>>>>>Error>>>>>\n', err);
  }
});
```

**webpack 源码**

```javascript
	(options, callback) => {
		const create = () => {
			if (!webpackOptionsSchemaCheck(options)) {
				getValidateSchema()(webpackOptionsSchema, options);
			}
			/** @type {MultiCompiler|Compiler} */
			let compiler;
			let watch = false;
			/** @type {WatchOptions|WatchOptions[]} */
			let watchOptions;
			if (Array.isArray(options)) {
				/** @type {MultiCompiler} */
				compiler = createMultiCompiler(options, options);
				watch = options.some(options => options.watch);
				watchOptions = options.map(options => options.watchOptions || {});
			} else {
				/** @type {Compiler} */
				compiler = createCompiler(options);
				watch = options.watch;
				watchOptions = options.watchOptions || {};
			}
			return { compiler, watch, watchOptions };
		};
		if (callback) {   // 看看这里,webpack 你都干了些啥???🤢🤢🤢🤢
			try {
				const { compiler, watch, watchOptions } = create();
				if (watch) {
					compiler.watch(watchOptions, callback);
				} else {
					compiler.run((err, stats) => {
						compiler.close(err2 => {
							callback(err || err2, stats);
						});
					});
				}
				return compiler;
			} catch (err) {
				process.nextTick(() => callback(err));
				return null;
			}
		} else {
			const { compiler, watch } = create();
			if (watch) {
				util.deprecate(
					() => {},
					"A 'callback' argument need to be provided to the 'webpack(options, callback)' function when the 'watch' option is set. There is no way to handle the 'watch' option without a callback.",
					"DEP_WEBPACK_WATCH_WITHOUT_CALLBACK"
				)();
			}
			return compiler;
		}
	}
);
```

### Webpack 捆绑的工作原理

> 假设 Webpack 配置中有一个叫作 main.js 的文件被指定为入口点，那么它就是依赖图的根。这个文件要导入的每个 JS 模块都将成为图的叶子，而这些叶子中导入的每个模块都将成为叶子的叶子

<div align="center">
    <img width="550px" src="/assets/img/webpack-source-work.png" />
</div>

### 动态导入

使用 Webpack 动态导入（ https://webpack.js.org/guides/code-splitting/）来加载应用程序的某些部分


- 标准的 JS 模块导入

```js
// main.js
import ModuleA from './module_a.js'
ModuleA.doStuff()

```

  它将作为 main.js 的叶子被添加到依赖图中，并被捆绑到捆绑包中。
  
  
```js
//main.js
const getModuleA = () => import('./module_a.js')
// invoked as a response to some user interaction
getModuleA()
  .then({ doStuff } => doStuff())

```

创建了一个返回 import() 函数的函数，而不是直接导入 module_a.js。现在 Webpack 会将动态导入模块的内容捆绑到一个单独的文件中，除非调用了这个函数，否则 import() 也不会被调用，也就不会下载这个文件。在后面的代码中，我们下载了这个可选的代码块，作为对某些用户交互的响应。
通过使用动态导入，我们基本上隔离了将被添加到依赖图中的叶子（在这里是 module_a），并在需要时下载它（这意味着我们也切断了在 module_a.js 中导入的模块）。


- 看另一个可以更好地说明这种机制的例子

假设我们有 4 个文件：main.js、module_a.js、module_b.js 和 module_c.js。要了解动态导入的原理，我们只需要 main 和 module_a 的源代码

```js
//main.js
import ModuleB from './mobile_b.js'
const getModuleA = () => import('./module_a.js')
getModuleA()
  .then({ doStuff } => doStuff()
)
//module_a.js
import ModuleC from './module_c.js'

```

通过让 module_a 成为一个动态导入的模块，可以让 module_a 及其所有子文件从依赖图中分离。当 module_a 被动态导入时，其中导入的所有子模块也会被加载

<div align="center">
    <img width="550px" src="/assets/img/optimization/lazy-import.png" />
</div>
