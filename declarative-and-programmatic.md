---
description: 什么是声明式导航与编程式导航？有什么区别？
---

# 声明式与编程式

## 声明式导航

使用 `<router-link></router-link>` 创建 `a` 标签来定义导航链接的称为**声明式导航**，`to` 显示的指明了导航的目的地。

## 编程式导航

编程式导航，顾名思义，就是**通过代码来进行路由的跳转**。想要导航到不同的 URL，可使用 `router.push` 方法。在 Vue 实例内部，你可以通过 `$router` 访问路由实例，因此使用 `$router.push('/foo')` 进行路由的跳转与以下声明式跳转等效：

```markup
<!-- 点击后将在内部执行 router.push('/foo') -->
<router-link to="/foo"></router-link>
```

该方法的参数可以是一个**字符串路径**，或者**一个描述地址的对象**。例如：

```javascript
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { id: 123 }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

## 常用的 API

### router.push\(\)

函数签名：

```javascript
router.push(location, onComplete?, onAbort?)// onComplete 与 onAbort 可选
```

这个方法会向 history 栈添加一个新的记录，当用户点击浏览器后退按钮时，则回到之前的 URL。

### router.replace\(\)

函数签名：

```javascript
router.replace(location, onComplete?, onAbort?)// onComplete 与 onAbort 可选
```

跟 `router.push` 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样，**替换掉**当前的 history 记录。

如果在命令式导航使用 `replace`，你应该在 `router-link` 组件上添加 `replace` 属性：

```markup
<!-- 点击后将在内部执行 router.replace('/foo') -->
<router-link to="/foo" replace></router-link>
```

{% hint style="info" %}
在 `router.push` 和 `router.replace` 中提供了 `onComplete` 和 `onAbort` 两个可选的回调函数。onComplete 将会在**导航成功** \(在**所有的异步钩子被解析之后**\)， onAbort 将会在**导航终止** \(**导航到相同的路由**、或在**当前导航完成之前导航到另一个不同的路由**\) 的时候进行相应的调用。
{% endhint %}

{% hint style="info" %}
如果**目的路由和当前路由相同**，**只有参数发生了改变** \(比如从一个用户资料到另一个 `/users/1`-&gt; `/users/2`\)，你需要使用 **`beforeRouteUpdate`** 守卫来监听/捕获/响应这个变化（例如获取用户信息），这一点在[动态路由匹配](https://learn-vue.gitbook.io/vue-router/~/edit/drafts/-LFQpvCHFzjdt-mVUoNc/declarative-and-programmatic)一节有所提及。
{% endhint %}

### replace.go\(\)

该方法的参数是一个整数，指在 history 记录中向前或者后退多少步，类似 `window.history.go(n)`。

```javascript
// 在浏览器记录中前进一步，等同于 window.history.forward()
router.go(1)

// 后退一步记录，等同于 window.history.back()
router.go(-1)
```

{% hint style="info" %}
`router.push`、 `router.replace` 和 `router.go` 跟 `window.history.pushState`、 `window.history.replaceState` 和 `window.history.go`好像， 实际上它们确实是效仿了`window.history` 的 API，这使得在 Vue Router 中操作 history 变得简单。 此外，Vue Router 的导航方法 \(`push`、`replace`、`go`\) 在各类路由模式 \(`history`、 `hash` 和 `abstract`\) 下表现一致。
{% endhint %}

{% hint style="info" %}
若有错误，欢迎指正！Base64 Email：Y2FwdGFpbmlucGh3QGdtYWlsLmNvbQ==
{% endhint %}

