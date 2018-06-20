---
description: 页面存在多个视图容器怎么办？如何将每个组件显示在正确的容器里？
---

# 命名视图

实际的开发中存在这样一种情况，某个路由下有多个**视图容器**\(`<router-view>`\)，那么多个视图容器必然需要多个组件去填充，如何将每个组件放置在正确的容器里呢？很简单，**使用一种手段将视图容器与组件关联起来即可**\(`router-view` 的 `name` 属性\)。

```javascript
import Vue from 'vue';
import VueRouter from 'vue-router';

// inject Vue Router to Vue
Vue.use(VueRouter);

// components
const Foo = { template: '<div>Foo</div>' };
const Bar = { template: '<div>Bar</div>' };
const Asd = { template: '<div>Asd</div>' };

// Vue Router configuration
const routes = [
  {
    name: 'user',
    path: '/user',
    // 注意，此处是 components，不是 component，末尾的 's' 需要注意
    components: {
      default: Foo, // <router-view> 没有使用 name 指定组件时就显示 default 指定的组件
      a: Bar,
      b: Asd,
    }，
  },
];

// Vue Router instance
const router = new VueRouter({
  routes,
});

// Vue instance
const app = new Vuw({
  router,
  template: `
    <div>
      <!-- 该试图容器没有 name 属性，则默认显示 default 指定的组件 Foo -->
      <router-view class="view first"></router-view>
      <!-- 该试图容器 name 属性指向 a，则显示 components 下 a 指定的组件 Bar -->
      <router-view class="view second" name="a"></router-view>
      <!-- 该试图容器 name 属性指向 b，则显示 components 下 b 指定的组件 Asd -->
      <router-view class="view third" name="b"></router-view>
    </div>
  `,
});
```

## 嵌套命名视图

当使用命名视图创建嵌套视图的复杂布局时，就需要结合[路由的嵌套](https://learn-vue.gitbook.io/vue-router/~/edit/drafts/-LFR9F_5yY13V1aFl5A1/named-views)来完成。我们以一个设置面板为例：

```text
/settings/emails                                       /settings/profile
+-----------------------------------+                  +------------------------------+
| UserSettings                      |                  | UserSettings                 |
| +-----+-------------------------+ |                  | +-----+--------------------+ |
| | Nav | UserEmailsSubscriptions | |  +------------>  | | Nav | UserProfile        | |
| |     +-------------------------+ |                  | |     +--------------------+ |
| |     |                         | |                  | |     | UserProfilePreview | |
| +-----+-------------------------+ |                  | +-----+--------------------+ |
+-----------------------------------+                  +------------------------------+
```

上面的示例图中，`/settings` 路由下面是 `UserSettings` 组件，`UserSettings` 组价中又引入了 `Nav` 组件，此外 `/setting/emails` 和 `/settings/profile` 是两个子路由，这两个子路由下显示的内容不一样，这是由 `<router-view>` 以及命名路由来控制的。代码结构大致如下：

```javascript
import Vue from 'vue';
import VueRouter from 'vue-router';

// inject Vue Router to Vue
Vue.use(VueRouter);

// components
const UserSettings = {
  template: `
    <div>
      <!-- 使用引入的 Nav 组件 -->
      <Nav/>
      <router-view><router-view>
      <router-view name="helper"><router-view>
    </div>
  `,
  components: { Nav }, // 引入 Nav 组件，组件内容不是重点，可参考官网例子
};
const UserEmailsSubscriptions = { template: '<div>UserEmailsSubscriptions</div>' };
const UserProfile = { template: '<div>UserProfile</div>' };
const UserProfilePreview = { template: '<div>UserProfilePreview</div>' };

// Vue Router Configuration
const routes = [
  {
    name: 'settings',
    path: '/settings',
    component: UserSettings,
    children: [
      // 添加一个空路由，防止跳转至 /settings 路由时页面什么都不显示
      {
        name: 'default',
        path: '',
        component: UserEmailsSubscriptions,
      }，
      {
        name: 'emails',
        path: 'emails'
        component: UserEmailsSubscriptions,
      },
      {
        name: 'profile',
        path: 'profile',
        components: {
          default: UserProfile,
          helper: UserProfilePreview,
        },
    ],
];

//Vue Router instance
const router = new VueRouter({
  routes,
});

// Vue instance
const app = new Vue({
  router,
  template: `
    <div>
      <router-view></router-view>
    </div>
  `,
}).$mount('#app');
```

#### 路由导航至 `/settings/emails`

当路由切换至 `/settings/emails` 时，`emails` 下的组件默认显示在 `UserSettings` 组件下的第一个 `<router-view>` 中，因为第二个 `<router-view>`  指定了组件名称 `name="helper"`，而 `/settings/emails` 路由下没有命名为 `helper` 的组件，所以不显示。

#### 路由导航至 `/settings/profile`

当路由切换至 `/settings/profile` 时，`profile` 路由配置中命名为 `default` 的组件默认显示在 `UserSettings` 组件下的第一个 `<router-view>` 中（没有 `name` 属性的显示命名为 `default` 的组件），命名为 `helper` 的组件显示在第二个 `<router-view name="helper">`  中。

{% hint style="info" %}
若有错误，欢迎指正！Base64 Email：Y2FwdGFpbmlucGh3QGdtYWlsLmNvbQ==
{% endhint %}

