---
description: 给你的路由起个名字
---

# 命名路由

可以通过一个名称来标识一个路由，特别是在链接一个路由，或者是执行一些跳转的时候。可以在创建 Router 实例的时候，在 `routes` 配置中给某个路由设置名称。

```javascript
const router = new VueRouter({
  routes: [
    {
      name: 'user',
      path: '/user:id',
      component: User,
    },
  ],
});
```

要链接到一个命名路由，可以给 `router-link` 的 `to` 属性传一个对象：

```markup
<router-link :to="{ name: 'user', params: { id: 123 }}">User</router-link>
```

此处的 `params` 为作为参数，将会将其值放到路由名称 `user` 的后面，把路由导航至 `/user/123` 路径。

