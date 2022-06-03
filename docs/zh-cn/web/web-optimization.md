# Website Optimization

Chrome ：使用chrome dev tool的代码覆盖率功能

- 使用命令菜单【ctrl + shift + p】打开命令菜单，输入coverage
- 点击relaod 按钮开始检测
- 点击相应文件即可查看具体的覆盖情况（绿色的为用到的代码，红色表示无用代码）

## Waiting TTFB时间过长？6个降低TTFB改善的方法

> 什么是TTFB?

> Time spent waiting for the initial response, also known as the Time To First Byte. This time captures the latency of a round trip to the server in addition to the time spent waiting for the server to deliver the response. [Google]

### 什么是 Waiting (TTFB) 时间

TTFB 是 Time to First Byte 的缩写，指的是浏览器开始收到服务器响应数据的时间（后台处理时间+重定向时间），是反映服务端响应速度的重要指标。就像你问朋友了一个问题，你的朋友思考了一会儿才给你答案，你朋友思考的时间就相当于 TTFB。你朋友思考的时间越短，就说明你朋友越聪明或者对你的问题越熟悉。对服务器来说，TTFB 时间越短，就说明服务器响应越快

### TTFB 时间多长算长？

因为每个服务器的硬件和网络环境都不尽相同，每个服务器的 TTFB 时间也不相同。如果想知道你的服务器优化可以到什么程度，大家可以上传一些静态的 HTML 页面到服务器，然后打开这些静态页面，看一些这些页面的 TTFB 时间，大多数服务器的 TTFB 时间都在 50 ms 以下，这个时间就是我们优化时候可以追求的时间。下面两个图中的 TTFB 时间分别是本站所在服务器的静态和动态网页 TTFB 等待时间

- 静态网页 Waiting （TTFB）时间


<div align="center">
    <img width="550px" src="/assets/img/optimization/devtool-1.png" />
</div>


- 动态网页 Waiting （TTFB）时间

<div align="center">
    <img width="550px" src="/assets/img/optimization/devtool-2.png" />
</div>


TTFB 时间如果超过了 500 ms，用户在打开网页的时候就会感觉到明显的等待。我么可以把 500 ms 以上认为是 TTFB 时间过长。可见，WordPress 智库的服务器还不算差

### TTFB 过长的原因

对于动态网页来说，服务器收到用户打开一个页面的请求时，首先要从数据库中读取该页面需要的数据，然后把这些数据传入到模版中，模版渲染后，再返回给用户。由于查询数据和渲染模版需要需要一定的时间，在这个过程没有完成之前，浏览器就一致处于等待接收服务器响应的状态。有些服务的性能比较低，或者优化没做好，这个时间就会比较长

如果服务器到用户之间的网络不好，（比如，服务器在欧洲，用户在中国，用户打开网页的时候，请求需要跨越千山万水才能达到服务器），服务器接收到用户请求的时间过长，也是导致 TTFB 时间过长的原因。

有时候，页面在用户的浏览器中保存了过多的 Cookie，每次请求，这些 Cookie 都要发送到服务器，服务器都要处理这些 Cookie，这也是导致 TTFB 时间过长的原因之一

### Waiting (TTFB) 时间过长的解决办法

知道了原因，解决办法就显而易见了，那就是缩短服务器响应时间，最简单直接并且有效的办法就是使用缓存，把 PHP 和 MySQL 的执行时间最小化，一些缓存插件可以把 SQL 查询结果缓存起来，把几十次查询结果转换为几次；一些缓存插件可以直接把用户所请求的页面静态化，用户打开网页时，相当于直接从服务器上下载了静态页面。

如果是网络原因，换一个服务器是比较直接的解决办法。如果因为一些原因不能换服务器，可以使用一个 CDN，把页面同步到离用户比较近的 CDN 节点上，也是一个不错的解决办法。

如果是 Cookie 的原因，可以通过修改应用程序，删除一些不必要的 Cookie，或者精简 Cookie 内容，缩短 Cookie 的有效期等，都是解决办法


## Vue 优化
 
本节内容摘自 https://blog.51cto.com/u_15057848/2567724

### 延迟加载 Vue 组件

```js
const lazyComponent = () => import('Component.vue')

// 现在只会在请求组件时才会下载它。以下是调用 Vue 组件动态加载的最常用方法
const lazyComponent = () => import('Component.vue')
lazyComponent()

// 请求渲染组件

<template>
<div> 
<lazy-component />
</div>
</template>
<script>
const lazyComponent = () => import('Component.vue')
export default {
components: { lazyComponent }
}
</script>


```

就不会动态导入组件，因为它没有被添加到 DOM（但一旦值变为 true 就会导入，这是一种条件延迟加载 Vue 组件的好方法)


### 应用程序增长

> vue-router 是一个可用于将 Web 应用程序拆分为单独页面的库。每个页面都变成与某个特定 URL 路径相关联的路由。
假设我们有一个简单的组合应用程序，具有以下结构

<div align="center">
    <img width="550px" src="/assets/img/optimization/optimize-router.png" />
</div>


```js
// 不要静态引入
// import RouteComponent form './RouteComponent.vue'
// const routes = [{ path: /foo', component: RouteComponent }]

// 动态引入
const routes = [
  { path: /foo', component: () => import('./RouteComponent.vue') }
]
```

### Vue 生态系统中的代码拆分

> 使用 Nuxt 或 vue-cli 来创建应用程序,它们都有一些与代码拆分有关的自定义行为

- 在 vue-cli 3 中，默认情况下将预取所有延迟加载的块
- 在 Nuxt 中，如果我们使用了 Nuxt 路由系统，所有页面路由默认都是经过代码拆分的

通过反模式，来减小基于路由拆分

#### 第三方捆绑反模式

> 第三方捆绑通常被用在单独 JS 文件包含 node_modules 模块的上下文中

> 虽然把所有东西放在一个地方并缓存它们可能很诱人，但这种方法也引入了我们将所有路由捆绑在一起时遇到的问题

<div align="center">
    <img width="550px" src="/assets/img/optimization/opt-vendors-1.png" />
</div>

问题在于，即使我们只在一个路由中使用 lodash，它也会与所有其他依赖项一起被捆绑在 vendor.js 中，因此它总是会被加载。
将所有依赖项捆绑在一个文件中看起来很诱人，但这样会导致应用程序加载时间变长。但我们可以做得更好！
让应用程序使用基于路由的代码拆分就足以确保只下载必要的代码，只是这样会导致一些重复代码。
假设 Home.vue 也需要 lodash


<div align="center">
    <img width="550px" src="/assets/img/optimization/opt-vendors-2.png" />
</div>

在这种情况下，从 /about（About.vue）导航到 /（Home.vue）需要下载 lodash 两次。
不过这仍然比下载大量的冗余代码要好，但既然已经有了同样的依赖项，就应该重用它，不是吗？
这个时候可以使用 splitChunksPlugin（ https://webpack.js.org/plugins/split-chunks-plugin/）。
只需在 Webpack 配置中添加几行代码，就可以将公共依赖项分组到一个单独的包中，并共享它们


```js
// webpack.config.js
optimization: {
  splitChunks: {
    chunks: 'all'
  }
}

````


