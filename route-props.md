---
description: 通过传参将组件与路由解耦，使得组件的使用更加灵活
---

# 路由组件传参

在组件中使用 `$route` 会使之与其对应路由形成高度耦合，从而使组件只能在某些特定的 URL 上使用，限制了其灵活性。未使用 `props` 时：

```javascript
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```

通过 `props` 将组件与 `$route` 解耦的方法有以下几种：

## 布尔模式

在路由配置中设置 `props: true` 时默认将 $route.params 数据传给组件，组件需要通过自身的 `props` 属性取出 `params` 中的属性：

```javascript
const User = { 
  props: ['userId', 'postId'], // 类似于 ES6 的对象解构，将 params 中的属性取出来
  template: '<div>{{ userId }} {{ postId }}</div>',
};

const router = new VueRouter({
  routes: [
    { 
      props: true, // 默认将 $route.params 传进去
      path: '/user/:userId/post/:postId',
      component: User,
    },
  ],
};
```

## 对象模式

如果 `props` 是一个对象，其下所有属性均会被传入组件。需要注意的是当 `props` 必须是是静态的：

```javascript
const User = {
  props: ['name', 'gender'], // 类似于 ES6 的对象解构，将路由配置 props 中的属性取出来
  template: '<div>{{ name }} {{ gender }}</div>',
};

const router = new VueRouter({
  routes: [
    {
      props: {
        name: 'daijt',
        gender: 'male',
      };
      path: '/user',
      component: User,
    },
  ],
});
```

## 函数模式

`props` 也可以是一个函数的返回值，这样的就可以将动态的 `parmas` 与静态值结合起来。上述的布尔形式的 `props` 也可以改写为如下：

```javascript
const User = { 
  props: ['params'], // 此处接收路由配置 props 传进来对象的 params 属性，也是对象解构
  template: '<div>{{ params.userId }} {{ params.postId }}</div>',
};

const router = new VueRouter({
  routes: [
    { 
      // 将 $route.params 所有的属性传进去，形式为：
      // { params: { userId: xxx, postId: yyy } }
      props: ({ params }) => ({ params }), // 语法糖，函数调用时会传入当前路由 $route
      path: '/user/:userId/post/:postId',
      component: User,
    },
  ],
};
```

除此之外，还可以动态 `params` 与静态数据相结合：

```javascript
const User = { 
  props: ['year'], // 接收路由配置 props 函数返回对象的 year 属性，对象解构
  template: '<div>{{ year }}</div>',
};

const dynamicPropsFn = route => {
  const now = new Date();
  return {
    year: (now.getFullYear() + parseInt(route.params.years)) + '!';
  };
}

const router = new VueRouter({
  routes: [
    {
      props: dynamicPropsFn, // 函数模式
      path: '/user/:userId/post/:postId',
      component: User,
    },
  ],
};
```

{% hint style="info" %}
若有错误，欢迎指正！Base64 Email：Y2FwdGFpbmlucGh3QGdtYWlsLmNvbQ==
{% endhint %}

