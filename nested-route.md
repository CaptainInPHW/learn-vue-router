---
description: 如何在路由中定义路由？
---

# 路由嵌套

实际生活中的应用界面，通常由多层嵌套的组件组合而成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层组件，例如 `/user` 路由下有两个子路由 `profile` 和 `posts`，注意 `/user` 后面的 `foo` 是动态参数，不是确指路由：

```text
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

我们知道 router 控制的视图（views）是在 `<router-view></router-view>` 中显示的，那么要实现路由嵌套，则在组件中放入 `<router-view></router-view>` 即可，同时在路由配置中将嵌套路由放入 `children` 字段：

```markup
<div id="app">
  <router-view></router-view>
</div>
```

```javascript
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view> // 在这里放入二级视图容器 <router-view></router-view>
    </div>
  `，
}；
const Profile = { template: '<div>profile</div>' };
const Posts = { template: '<div>posts</div>' };

const routes = [
  {
    path: '/user:id',
    component: User,
    // 加入 children 字段即可
    children: [
      {
        path: 'profile',
        component: Profile,
      },
      {
        path: 'posts',
        component: Posts,
      },
    ],
  },
];

const router = new VueRouter({
  routes,
});
```

注意以 `/` 开头的都是根路径。

进行上述配置后，还有一个问题，当以 `/user/foo/` 去访问的时候，页面将不会显示出任何内容，因为和 `/user/foo` 下的 `profile` 和 `posts` 都不匹配，针对这种情况，需要提供一个空的路由：

```javascript
const routes = [
  {
    path: '/user:id',
    component: User,
    // 加入 children 字段即可
    children: [
      {
        path: '', // 空字符串
        component: Profile 或 Posts，// 空路由默认显示哪个组件可自行取舍
      },
      {
        path: 'profile',
        component: Profile,
      },
      {
        path: 'posts',
        component: Posts,
      },
    ],
  },
];

const router = new VueRouter({
  routes,
});
```

那么当 `/user:id` 匹配成功后，就会显示空路由下指定的组件。

{% hint style="info" %}
若有错误，欢迎指正！Base64 Email：Y2FwdGFpbmlucGh3QGdtYWlsLmNvbQ==
{% endhint %}

