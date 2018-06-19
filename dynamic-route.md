---
description: 如何将多路径映射至同一组件？如何在该组件下监听路径的变化？
---

# 动态路由匹配

## 如何将多路径映射至同一视图？

在某些时候，我们需要将某些具有相同特征的路由（routes）映射至同一视图（component）下。例如我们有一个 `User` 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 `vue-router` 的路由路径中使用“动态路径参数”（dynamic segment）来达到这个效果：

```javascript
const user = { template: '<div>User</div>' };
const router = new VueRouter({
    routes: [{
        path: '/user:id', // 现在，像 '/user/foo' 和 '/user/bar' 都能匹配到，会使用同一个 user 组件渲染
        component: user,
    }],
});
```

一个“路径参数”使用冒号 `:` 标记。当匹配到一个路由时，参数值会被设置到 `this.$route.params`，可以在每个组件内访问到。同样的，也可以在一个路由中设置多段“路径参数”，对应的值都会设置到 `$route.params` 中。例如：

| 路由模式 | 匹配到的路径 | this.$route.params |
| --- | --- |
| /user:user\_name/post:post\_id | /user/daijt/post/123 | `{ user_name: 'daijt', post_id: 123 }` |

## 如何监听/响应动态参数的变化？

当我们使用了动态参数来使多个路径都映射至同一组件时，根据文档的描述，会将之前的**组件进行复用**，即当路由从 `/user/foo` 切换至 `/user/bar` 时，会将复用之前的组件，而不是销毁再创建，这样显得更加高效。但是**组件的复用会导致组件的钩子函数不会被调用**。

要监听/响应动态参数的变化，有两种方法：

#### 在组件中对 `$route` 进行 watch（监听）：

```javascript
export default {
  watch: {
    '$route'(to, from) {
      // do something...
      // console.log(this.$toute.params)
    }
  },
}
```

#### 使用 beforeRouteUpdate 钩子函数：

```javascript
export default {
  beforeRouteUpdate(to, from, next) {
    // do something...
    // console.log(this.$toute.params)
    next(); // do not forget to call next()
  }
}
```

{% hint style="info" %}
`beforeRouteUpdate` 钩子函数仅对因**动态参数**发生的路由变化有效，函数中可以使用 `this`
{% endhint %}

## 更复杂的路由匹配

提到更复杂、更高级的匹配，必然要涉及正则表达式。Vue Router 使用 [path-to-regexp](https://github.com/pillarjs/path-to-regexp) 作为高级路由匹配引擎，可参考[官方文档](https://github.com/pillarjs/path-to-regexp#parameters)使用。

## 路由匹配的优先级

有时候，同一个路径可以匹配多个路由，此时，匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高。

{% hint style="warning" %}
官网上的解释过于简单，我从网上看到的一个采坑经历如下，放在这里谨防自己在以后的开发中碰到，[点此跳转至原文](http://zongzi531.com/2017/06/26/%E5%85%B3%E4%BA%8Evuerouter%E8%B7%AF%E7%94%B1%E5%8C%B9%E9%85%8D%E7%9A%84%E8%B8%A9%E5%9D%91%E4%BA%8B%E4%BB%B6/)。

问题代码如下：

```javascript
import VueRouter from 'vue-router';
Vue.use(VueRouter);

const routes = [
  {
    path: '/app/:id',
  },
  {
    path: '/app/page1',
    meta: {
      title: 'page1',
    }，
  },
  {
    path: '/app/page2',
    meta: {
      title: 'page2',
    }，
  }，
];

const router = new VueRouter({
  routes
});

router.beforeEach((to, from, next) => {
  document.title = to.meta.title;
  next();
});
```

出现的问题是：`meta.title` 一直为 `undefined。`为什么会出现这种错误呢？依据官方文档上的匹配优先级的解释，因为 `/app/:id` 将 '/app/page1' 以及 '/app/page2' 都能匹配成功，因此不会对后面的 `/app/page1` 进行匹配验证。而 `/app/:id` 没有 `meta: { title: 'page1' }`，因此会导致 `meta.title` 一直是 `undefined`。

如何修复呢？将特殊的路由模式放在前面，通用的路由模式放后面即可：

```javascript
const routes = [
  {
    path: '/app/page1',
    meta: {
      title: 'page1',
    }，
  },
  {
    path: '/app/page2',
    meta: {
      title: 'page2',
    }，
  },
  {
    path: '/app/:id',
  },
];
```

或者更加简洁的：

```javascript
const routes = [
  {
    path: '/app/:id',
  },
];

const router = new VueRouter({
  routes
});

router.beforeEach((to, from, next) => {
  document.title = to.params.id; // 动态参数键值对在 params 字段中
  next();
});
```
{% endhint %}

{% hint style="info" %}
若有错误，欢迎指正！Base64 Email：Y2FwdGFpbmlucGh3QGdtYWlsLmNvbQ==
{% endhint %}



