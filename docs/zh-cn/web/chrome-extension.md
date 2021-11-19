# Manifest V3 changes for browser extensions

> 今年互联网浏览的一个更大变化是以备受讨论的 Manifest V3 的形式出现的。 新的清单版本允许浏览器 Chrome 限制某些旧 API 的工作，改变网络和随附扩展的工作方式，并最终改变广大用户体验互联网的方式。 经过数月的积极讨论和反馈，适用于 Chrome 扩展的 Manifest V3 现已在 Chrome 88 Beta 上推出，接下来的几个月会有更多变化.

## Manifest V3 的变化

### 安全方面(Security)

  在 Manifest V3 中，Google 禁止远程托管代码。 google声称，这种机制被用作攻击媒介来规避谷歌的恶意软件检测工具。 为了用户隐私和安全，这将被删除。 远程托管代码的删除也将使谷歌能够更彻底、更快速地审查提交给 Chrome 网上应用店的内容。

### 性能(Performance)

  在这个新版本中， Google引入了 service workers 作为后台页面的替代品。 后台页面在后台保持活动状态并消耗系统资源，无论扩展是否正在使用它。 Service Worker 是“短暂的”，因为它们与网页分开运行，为不需要网页或用户交互（如推送通知和后台同步）的功能打开了大门。 浏览器将能够根据需要启动和关闭 Service Worker，从而降低整体系统资源利用率。

  扩展 API 也正朝着更具声明性的模型发展。 Google表示，最终结果是为大多数扩展用户提供了更好的整体性能和更好的隐私保证。  

### 隐私 (Privacy)

    另一个重大变化来自新的扩展模型，它使更多权限可选。 用户现在可以在安装时保留敏感权限，让他们更好地了解和控制扩展程序如何使用和共享他们的数据。 因此，扩展开发人员应该期望用户随时选择加入和退出权限。

    然后对需要被动访问 Web 活动的扩展进行了更改，例如 Web 请求 API 和较新的声明性网络请求 API。 特别是声明性网络请求 API，自首次发布以来已经发生了变化，当前的推出考虑了开发者社区的广泛反馈，例如支持多个静态规则集、规则中的正则表达式、声明性标头修改等 .

### 可用性& 发布 (Availability and Rollout for Manifest V3)

  如前所述，Manifest V3 现在可以在 Chrome 88 Beta 上进行试验，预计在即将发布的版本中还会有其他功能。 Chrome 网上应用店将从 1 月中旬 Chrome 88 达到稳定分支时开始接受 Manifest V3 扩展。

Google 没有承诺取消对 Manifest V2 扩展的支持的确切日期，迁移期的粗略时间表可以估计为 Manifest V3 登陆稳定分支后的一年。 Google 将在未来几个月内提供有关时间表的更多详细信息。


## MV3 API


### contentSettings

> 使用chrome.contentsettings API更改控制网站是否可以使用cookie，JavaScript和插件等功能的设置。 确切的讲，内容设置允许您在每个站点的基础上自定义Chrome的行为而不是全局

```json
{
  "name": "My extension",
  ...
  "permissions": [
    "contentSettings"
  ],
  ...
}

- Pattern precedence

  http://www.example.com/*
  http://*.example.com/* (matching example.com and all subdomains)
  <all_urls> (matching every URL)

  ### chrome.events


  ### chrome.scripting

  > 您可以使用 chrome.scripting API 将 JavaScript 和 CSS 注入网站。 这类似于您可以使用内容脚本执行的操作，但是通过使用 chrome.scripting API，扩展程序可以在运行时做出决定。

  ### chrome.extension

  > Chrome.Extension API具有任何扩展页面可以使用的实用程序。 它包括对交换扩展和其内容脚本之间或扩展之间的消息的支持，如消息传递中详细描述的。

  ### chrome.notifications

  > 使用chrome.notifications API使用模板创建丰富的通知，并将这些通知显示给系统托盘中的用户

  ### chrome.runtime

  > 使用Chrome.Runtime API来检索“背景”页面，返回清单的详细信息，并侦听并响应应用程序或扩展生命周期中的事件。 您还可以使用此API将URL的相对路径转换为完全合格的URL