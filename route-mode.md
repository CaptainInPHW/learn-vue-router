---
description: History 模式以及 Hash 模式
---

# 路由模式

Vue Router 默认 `hash` 模式 ，使用 URL 的 hash 来模拟一个完整的 URL，当 URL 改变时，页面不会重新加载。hash 形式如下：

```text
// 网站根路径
https://yoursite.com/#/
// 某个路由
https://yoursite.com/#/user/id
```

如果不想要很丑的 hash，我们可以用路由的 **history 模式**，这种模式充分利用 `history.pushState` API 来完成 URL 跳转（前进与后退）而无须重新加载页面。

```javascript
const router = new VueRouter({
  mode: 'history', // 路由模式
  routes: [...]
})
```

当你使用 history 模式时，URL 就像正常的 url，例如 `http://yoursite.com/user/id`，链接形式比较漂亮。不过要使用这种模式，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 `http://oursite.com/user/id` 就会返回 404，这就不好看了。

{% hint style="info" %}
因为直接以 `http://oursite.com/user/id` 访问或者刷新页面时，
{% endhint %}

所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 `index.html` 页面，这个页面就是你 app 依赖的页面。

### [\#](https://router.vuejs.org/zh/guide/essentials/history-mode.html#%E5%90%8E%E7%AB%AF%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90) {#后端配置例子}

