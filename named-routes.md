---
description: 给你的路由起个名字
---

# 命名路由

## 如何使用？

可以通过一个名称来标识一个路由，特别是在链接一个路由，或者是执行一些跳转的时候。可以在创建 Router 实例的时候，在 `routes` 配置中给某个路由设置名称。

```javascript
// component
const User = { template: '<div>User</div>' };
// Vue Router instance
const router = new VueRouter({
  routes: [
    {
      name: 'user',
      path: '/user/:id',
      component: User,
    },
  ],
});
// Vue instance
const app = new Vue({
  router
});
```

要链接到一个命名路由，可以给 `router-link` 的 `to` 属性传一个对象：

```markup
<!-- 需要使用 Vue 的 v-bind 语法，因为 to 后面是个 JS 对象，而不是字符串 -->
<router-link :to="{ name: 'user', params: { id: 123 }}">User</router-link>
```

此处的 `params` 为作为参数，将会将其值放到路由名称 `user` 的后面，把路由导航至 `/user/123` 路径。

{% hint style="info" %}
此处需要注意的是**路由命名不能重复**，需要是唯一的。
{% endhint %}

## 存在的问题？

路由名称只是路由的一个名字，可以用路由名称去指代你想要的路由，这样比起写具体的路径更加方便一些。上述的例子如果不使用命名的话，和下面的例子是等效的：

```javascript
// component
const User = { template: '<div>User</div>' };
// Vue Router instance
const router = new VueRouter({
  routes: [
    {
      name: 'user',
      path: '/user/:id',
      component: User,
    },
  ],
});
// Vue instance
const app = new Vue({
  data: { 
    id: 123, 
  },
  template: `
    <router-link :to="{ path: `/user/${ this.id }`}">User</router-link>
  `;
});
```

上述的例子中 Vue 配置的 `template` 为什么不写为 `<router-link :to="{ path: '/user', params: { id: 123 } }"></router-link>` ，而是将 `id` 拼接到路由后面呢？因为根据文档的描述，**`path` 和 `params` 一同出现的时候，`params` 会被忽略掉，**你需要**提供路由的 `name`** 或者**提供完整的带有参数的 `path`，**此处需要注意。

{% hint style="info" %}
在使用[**编程式导航**](https://learn-vue.gitbook.io/vue-router/~/edit/drafts/-LFR6aCzSRIyrqMItu1c/ming-ming-lu-you)时，使用 `this.$router.push()` 改变当前路由也需要注意，`path` 和 `params` 是不能共存的：

```javascript
const id = 123;

router.push({ name: 'user', params: { id } }); // -> /user/123
router.push({ path: `/user/${ id }` }); // -> /user/123
// 这里的 params 不生效，path 和 params 不能共存
router.push({ path: '/user', params: { id } }); // -> /user
```
{% endhint %}

{% hint style="info" %}
不能和 `path` 共存的只有 `params`，其他的 `this.$route` 属性不存在这种情况。
{% endhint %}

{% hint style="info" %}
在使用命名路由进行导航时，如果想要导航至某个嵌套路由下的子路由时，可能会产生警告：

```javascript
const routes = [
  {
    path: '/',
    name: 'home',
    component: Home,
  },
  {
    name: 'user',
    path: '/user/:id',
    component: User,
    children: [
      // 空路由，即默认子路由，当以 '/user/123' 访问时，显示 Profile 视图
      {
        path: '',
        component: Profile,
      },
      {
        name: 'profile',
        path: 'profile',
        component: Profile,
      },
      {
        name: 'emails',
        path: 'emails',
        component: Emails,
      },
    ],
];
```

假设当前路由为 `/`，当想要跳转至 `/user/123/emails` 时，我们使用编程式导航进行操作：

```javascript
this.$router.push({ name: 'emails', params: { id: 123 } });
```

路由跳转正常，但是在控制台上显示了一条警告：

> **\[vue-router\] Named Route 'user' has a default child route. When navigating to this named route \(:to="{name: 'user'"\), the default child route will not be rendered. Remove the name from this route and use the name of the default child route for named links instead.**
>
> 命名路由 'user' 包含一个默认的子路由，当导航至该命名路由 'user' 时，其下的默认子路由将不会被渲染。移除掉 'user' 路由的名字，使用其下子路由的名字进行跳转/导航。

意思就是**既然你要做成默认子路由的形式，那不如直接去掉根路由的名字，给你的默认子路由一个名字就行了**。将路由配置改为如下：

```javascript
const routes = [
  {
    path: '/',
    name: 'home',
    component: Home,
  },
  {
    // 将嵌套路由的根路由 name 移除
    path: '/user/:id',
    component: User,
    children: [
      {
        // 将 name: 'user' 放置于默认的子路由（空路由）上
        // 当访问 '/user/:123' 时，将直接跳转至该路由
        // 或者以 $router.push({ name: 'user', params: { id: 123 } }) 形式访问也是没有问题的
        // 这样改动之后，和前面的配置效果一样，但是意义更加明显
        // 以 '/user/123' 访问的时候，我就是在访问这个子命名路由，而不是上级的命名根路由了
        name: 'user',
        path: '',
        component: Profile,
      },
      {
        name: 'profile',
        path: 'profile',
        component: Profile,
      },
      {
        name: 'emails',
        path: 'emails',
        component: Emails,
      },
    ],
];
```
{% endhint %}

