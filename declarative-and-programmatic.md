---
description: 什么是声明式导航与编程式导航？有什么区别？
---

# 声明式与编程式

## 声明式导航

使用 `<router-link></router-link>` 创建 `a` 标签来定义导航链接的称为**声明式导航**，`to` 显示的指明了导航的目的地。

## 编程式导航

编程式导航，顾名思义，就是**通过代码来进行路由的跳转**。想要导航到不同的 URL，可使用 `router.push` 方法。这个方法会向 history 栈添加一个新的记录，当用户点击浏览器后退按钮时，则回到之前的 URL。在 Vue 实例内部，你可以通过 `$router` 访问路由实例，因此使用 `$router.push('/foo')` 进行路由的跳转与以下声明式跳转等效：

```markup
<router-link to="/foo"></router-link> <!-- 内部也是调用 router.push 方法 -->
```



