---
description: 导航到新路由时，让页面滚动到你想要的位置
---

# 滚动行为

使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。 `vue-router` 能做到，而且更好，它让你可以自定义路由切换时页面如何滚动。

{% hint style="info" %}
这个功能只在支持 `history.pushState` 的浏览器中可以使用
{% endhint %}

当创建一个 Router 实例时，你可以提供一个 `scrollBehavior` 方法：

```javascript
const router = new VueRouter({
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // 第三个参数 savedPosition 当且仅当 popstate 导航 (通过浏览器的前进/后退按钮触发) 时才可用
    // return 期望滚动到哪个的位置
  }
})
```

该方法返回的滚动位置对象形式可如下：

```javascript
{ x: number, y: number }
// or
{ selector: string, x: number, y: number }
// 在 2.6.0+ 中可以使用 offset
{ selector: string, offset : { x: number, y: number } }
```

{% hint style="info" %}
如果返回一个 `falsy` 值（0、NaN、false、undefined、空字符串），或者是一个空对象，那么就不会发生滚动。
{% endhint %}

### 回到顶部

如果要使所有的导航，使页面滚动到顶部时，可以这样做

```javascript
scrollBehavior (to, from, savedPosition) {
  return { x: 0, y: 0 }
}
```

### 回到指定位置

返回 `savedPosition` 时，使用浏览器的前进或者后退按钮，就会像浏览器原生表现的那样：

```javascript
scrollBehavior (to, from, savedPosition) {
  if (savedPosition) {
    return savedPosition
  } else {
    return { x: 0, y: 0 }
  }
}
```

如果你要模拟“滚动到锚点”的行为：

```javascript
scrollBehavior (to, from, savedPosition) {
  if (to.hash) {
    return {
      selector: to.hash
    }
  }
}
```

可以结合路由元信息更细颗粒度地控制滚动，比如将进入每个路由对象时需要滚动的位置放在 `meta` 里面。请看这个[官网例子](https://github.com/vuejs/vue-router/blob/next/examples/scroll-behavior/app.js)。

### 异步滚动

你也可以返回一个 Promise resovled  出来想要滚动到的位置：

```javascript
scrollBehavior (to, from, savedPosition) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({  selector：to.hash })
    }, 1000)
  })
}
```

{% hint style="info" %}
上述例子的具体表现就是导航成功后页面保持 1s，然后滚动到指定的锚点处。但是使用官网的 demo 试了下这样做有一个问题，那就是页面会直接滚动至你预期的位置，并没有任何的过渡效果，用户体验比较差，这个问题有待研究。
{% endhint %}



