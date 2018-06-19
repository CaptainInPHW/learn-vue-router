---
description: route 的基本概念以及 vue-router 的基本使用
---

# 起步

## What is route?

路由就是导航。好比你走到某个分岔路口，指路牌上写着，A 村庄请走左边的小路，B 村庄请走右边的小路。那么很明显，根据你要去的地方，你就能知道要走哪边的小路。在代码中我们也常用到路由，比如 `if else` 或者 `switch case` 语句，根据条件的不同，代码走不同的逻辑，这也称做路由。当然，我们常谈及的路由不是指代码中的逻辑，而是与 URL 有关。根据领域的不同，路由分为前端路由以及后端路由。

## Why we use route?

我们想在不同的条件下在页面中显示不同的内容。这个条件显示在当前页面的 URL 中，而切换不同的视图（views）就由路由器（router）负责。当 router 匹配到了正确的路由时，就会将该路由下的视图（views）显示出来。

## How to use route?

Vue Router 是 [Vue.js](https://vuejs.org) 的官方路由管理器，与 Vue.js 的核心深度集成，使构建单页面应用变得简单。使用 Vue.js ，我们已经可以通过组合组件来组成应用程序，当我们引入 Vue Router 后，我们需要做的是，将组件 \(components\) 映射到路由 \(routes\)，然后告诉 Vue Router 在哪里渲染它们（&lt;router-link&gt; & &lt;router-view&gt;）。

```markup
<!-- 相当于 <a></a> 链接，点击切换路由（routes），从而切换视图（views）-->
<router-link to="/foo">go to foo component</router-link>
<router-link to="/bar">go to bar component</router-link>
<!-- 视图（views）容器 -->
<router-view></router-view>
```

```javascript
import Vue form 'vue';
import VueRouter form 'vue-router';

// inject Vue Router to Vue
Vue.use(VueRouter);

// components
const Foo = { template: '<div>foo</div>' };
const Bar = { template: '<div>bar</div>' };

// router configuration
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
];

// Vue Router instance
const router = new VueRouter({
  routes
});

// Vue instance
const app = new Vue({
  router
}).$mount('#app');
```

将 Vue Router 注入 Vue 的好处是我们可以在任意的地方使用 `this.$router` 访问到页面的路由器，以及使用 `this.$route` 访问到当前页面的路由，而不用在每个页面都引入一次 router。可以参照官方的在线例子：[https://jsfiddle.net/yyx990803/xgrjzsup](https://jsfiddle.net/yyx990803/xgrjzsup/)。

