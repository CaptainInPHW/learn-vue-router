---
description: 数据可以在导航完成前获取或者导航完成后获取，以给予用户不同的用户体验
---

# 数据获取

有时候，进入某个路由后，需要从服务器获取数据。例如，在渲染用户信息时，你需要从服务器获取用户的数据。我们可以通过两种方式来实现：

* **导航完成之后获取**：先完成导航，然后在接下来的组件生命周期钩子中获取数据。在数据获取期间显示“加载中”之类的指示。
* **导航完成之前获取**：导航完成前，在路由进入的守卫中获取数据，在数据获取成功后执行导航。

从技术角度讲，两种方式都不错，就看你想要的用户体验是哪种。

## 导航完成后获取数据

当你使用这种方式时，我们会马上导航和渲染组件，然后在组件的 `created` 钩子中获取数据。这让我们有机会在数据获取期间展示一个 loading 状态，还可以在不同视图间展示不同的 loading 状态。

假设我们有一个 `Post` 组件，需要基于 `$route.params.id` 获取文章数据：

```markup
<template>
  <div class="post">
    <!-- 正在获取数据时，展示 loading 状态 -->
    <div class="loading" v-if="loading">
      Loading...
    </div>
    <!-- 获取数据出错，则展示出错信息 -->    
    <div v-if="error" class="error">
      {{ error }}
    </div>
    <!-- 获取数据成功，展示数据 -->
    <div v-if="post" class="content">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>
```

```javascript
export default {
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // 组件创建完后获取数据，
    // 此时 data 已经被 observed 了
    this.fetchData()
  },
  watch: {
    // 如果路由有变化，会再次执行该方法
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      // 这里的 getPost 只是个示例，你可以用自己封装好的 API 来替代
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
```

{% hint style="info" %}
通常在导航后获取数据时，路由先进行跳转，组件也被渲染出来。如果获取数据再进行处理，速度如果比较慢的话，可以考虑加上 loading 状态，否则数据展示区域在处理完之前将是一片空白。
{% endhint %}

## 导航完成前获取数据

通过这种方式，我们在导航转入新的路由前获取数据。我们可以在接下来的组件的 `beforeRouteEnter`守卫中获取数据，当数据获取成功后只调用 `next` 方法。

```javascript
export default {
  data () {
    return {
      post: null,
      error: null
    }
  },
  // beforeRouteEnter 调用时，组件示例还没有被创建，因此不能通过 this 访问组件实例，
  // 只能通过给 next 传递一个回调函数来访问，该回调函数会被传入组件实例，
  // 这个钩子函数是唯一一个 next 支持传入回调函数的
  beforeRouteEnter (to, from, next) {
    getPost(to.params.id, (err, post) => {
      next(vm => vm.setData(err, post))
    })
  },
  // 路由改变前，组件就已经渲染完了，逻辑稍稍不同
  beforeRouteUpdate (to, from, next) {
    this.post = null
    getPost(to.params.id, (err, post) => {
      this.setData(err, post)
      next()
    })
  },
  methods: {
    setData (err, post) {
      if (err) {
        this.error = err.toString()
      } else {
        this.post = post
      }
    }
  }
}
```

在为后面的视图获取数据时，用户会停留在当前的界面，因此建议在数据获取期间，显示一些进度条或者别的指示。如果数据获取失败，同样有必要展示一些全局的错误提醒。

