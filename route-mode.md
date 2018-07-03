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
因为直接以 `http://oursite.com/user/id` 访问或者刷新页面时，请求的抓包地址 `Request URL` 是 `http://oursite.com/user/id` 去访问的，而 `/user/id` 是前端路由，不后端路由，后端解析不到该路径，因此会返回 404。
{% endhint %}

所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 `index.html` 页面，这个页面就是你 app 依赖的页面。

这样一来，当你以按照正常的操作顺序打开网页去访问时，没有问题，假设你在某个路由下，点击刷新，由于此时是以前端的路由去请求数据的，而该路由后端不匹配，就会返回 `index.html` 页面。用户的感受就是，我登录进去进行操作到了某个页面之后，然后刷新，先是加载了首页，然后加载执行了 js 文件，发现当前的路由是 `/user/id` 后，就显示  `/user/id` 下的试图。这么做以后，你的服务器就不再返回 404 错误页面，因为对于所有的以前端路由的请求都会返回 `index.html` 页面。

那么前端的 404 页面该怎么显示呢？用户以 `http://oursite.com/user/123` 访问是正常的，但是假设用户此时敲错了一个字母，以 `http://oursite.com/usr/123` 去访问时，前端的所有路由都不匹配，那么此时可以显示给用户 404 页面，具体的配置就是，前端考虑到所有的路由情况之后，再匹配所有路径，给出一个 404 页面：

```javascript
const routes = [
  {
    name: 'route1',
    path: 'route1',
    component: route1,
  },
  ...
  // 根据路由的优先级，前面的所有路由都不匹配的情况下，就会匹配 * 路由，返回 404 页面
  {
    name: 'not-found',
    path: '*',
    component: NotFound,
  },
];
```

或者进一步的，还可以参考 [Vue SSR 服务端渲染方案](https://ssr.vuejs.org/zh/)。

