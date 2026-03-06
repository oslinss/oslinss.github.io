---
title: Nuxt3
description: 这是一篇关于Nuxt3的学习笔记
date: 2025-10-09 00:00:00+0000
image: cover.jpg
categories:
    - 学习
tags:
    - 前端
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

# Nuxt3

## 1.安装

### 新建项目

`1.npm create nuxt <project-name>`
`2.code <project-name>`
`3.cd <project-name>`

### 运行

`npm run dev -- -o`

## 2.配置

### Nuxt配置

`nuxt.config.ts `  文件位于 Nuxt 项目的根目录，可用于覆盖或扩展应用的默认行为。

一个最小的配置文件需要导出一个 `defineNuxtConfig` 函数，该函数包含一个配置对象。`defineNuxtConfig` 辅助函数无需导入即可全局使用。

`nuxt.config.ts`

`export default defineNuxtConfig({
  // 我的 Nuxt 配置
})
`
用于添加自定义脚本、注册模块或更改渲染模式

### 环境覆盖

可以在 `nuxt.config` 中配置完全类型化的、按环境区分的覆盖。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  $production: {
    routeRules: {
      '/**': { isr: true }
    }
  },
  $development: {
    //
  },
  $env: {
    staging: {
      // 
    }
  },
})
```

要在运行 Nuxt CLI 命令时选择环境，只需通过 `--envName` 标志传递环境名称，例如：`nuxi build --envName staging`。

### 环境变量和私有令牌

`runtimeConfig` API 将类似环境变量的值暴露给应用的其余部分。默认情况下，这些键仅在服务器端可用。
`runtimeConfig.public` 和 `runtimeConfig.app`（Nuxt 内部使用）中的键也可以在客户端使用。

这些值应在 `nuxt.config` 中定义，并可通过环境变量覆盖。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  runtimeConfig: {
    // 仅在服务器端可用的私有键
    apiSecret: '123',
    // public 中的键也会暴露到客户端
    public: {
      apiBase: '/api'
    }
  }
})
```

这些变量通过 `useRuntimeConfig()` 组合式函数暴露给应用的其余部分。

`pages/index.vue`

```
<script setup lang="ts">
const runtimeConfig = useRuntimeConfig()
</script>
```

### 应用配置

位于源目录（默认是项目根目录）的 `app.config.ts` 文件用于暴露可在构建时确定的公共变量。 <font color="red">与 `runtimeConfig` 选项不同，这些变量无法通过环境变量覆盖</font>。

一个最小的配置文件需要导出一个 `defineAppConfig` 函数，该函数包含一个配置对象。`defineAppConfig` 辅助函数无需导入即可全局使用。

`app.config.ts`

```
export default defineAppConfig({
  title: 'Hello Nuxt',
  theme: {
    dark: true,
    colors: {
      primary: '#ff0000'
    }
  }
})
```

这些变量通过 useAppConfig 组合式函数暴露给应用的其余部分。

`pages/index.vue`

```
<script setup lang="ts">
const appConfig = useAppConfig()
</script>
```

## 3.配置

### `app.vue`

默认情况下，Nuxt 将此文件视为 <font color="red">入口点</font>，并在应用的每个路由中渲染其内容。

```
<template>
  <div>
   <h1>欢迎来到首页</h1>
  </div>
</template>
```

### 组件

在 `components/` 目录中创建组件，<font color="red">它们将在整个应用中自动可用，无需显式导入</font>。

`app.vue`

```<template>
  <div>
    <h1>欢迎来到首页</h1>
    <AppAlert>
      这是一个自动导入的组件。
    </AppAlert>
  </div>
</template>
```

 ` components/AppAlert.vue`

```
<template>
  <span>
    <slot />
  </span>
</template>
```

### 页面

<font color="red">页面表示每个特定路由模式的视图</font>。`pages/` 目录中的每个文件代表一个不同的路由，显示其内容。

要使用页面，需创建 `pages/index.vue` 文件，并在 `app.vue` 中添加 `<NuxtPage />` 组件
可以通过在 `pages/` 目录中添加新文件来创建更多页面及其对应的路由。

<font color="red">它的主要作用是用于定义页面组件，同时实现更加灵活和现代的路由管理。</font>

`pages/index.vue`

```
<template>
  <div>
    <h1>欢迎来到首页</h1>
    <AppAlert>
      这是一个自动导入的组件
    </AppAlert>
  </div>
</template>
```

`pages/about.vue`

```
<template>
  <section>
    <p>此页面将在 /about 路由下显示。</p>
  </section>
</template>
```

### 布局

<font color="red">布局是围绕页面的包装器，包含多个页面共用的用户界面</font>，例如头部和底部显示。
布局是使用 `<slot />` 组件来显示 页面 内容的 Vue 文件。`layouts/default.vue` 文件将作为默认布局。自定义布局可以通过页面元数据进行设置。

`app.vue`

```
<template>
  <div>
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>
```

`layouts/dafault.vue`

```
<template>
  <div>
    <p>跨所有页面共享的默认布局内容</p>
    <slot /> //在布局文件中，页面的内容将在此组件中显示
  </div>
</template>
```

#### 命名布局

```
-| layouts/
---| default.vue
---| custom.vue
```

在页面中使用`custom`布局

`pages/about.vue`

```
<script setup lang="ts">
definePageMeta({
  layout: 'custom'
})
</script>
```

`name`属性覆盖所有页面的默认布局

`app.vue`

```
<script setup lang="ts">
// 可以根据 API 调用或登录状态选择
const layout = "custom";
</script>

<template>
  <NuxtLayout :name="layout">
    <NuxtPage />
  </NuxtLayout>
</template>
```

#### 动态更改布局

`setPageLayout`辅助函数动态更改布局

```
<script setup lang="ts">
function enableCustomLayout () {
  setPageLayout('custom')
}
definePageMeta({
  layout: false,
});
</script>

<template>
  <div>
    <button @click="enableCustomLayout">更新布局</button>
  </div>
</template>
```

## 4.资产

Nuxt 使用两个目录来处理样式表、字体或图片等资产：

* `public/` 目录的内容会直接以服务器根路径的形式提供。

* `assets/` 目录按惯例包含希望构建工具（Vite 或 webpack）处理的所有资产。

### 公共目录

`public/` 目录用作静态资产的公共服务器，这些资产可以在应用定义的 URL 下公开访问。

可以通过应用的代码或浏览器使用根 URL / 来获取 `public/` 目录中的文件。

#### 示例

引用位于 `public/img/` 目录中的图像文件，可通过静态 URL `/img/nuxt.png` 访问：
`app.vue`

```
<template>
  <img src="/img/nuxt.png" alt="探索 Nuxt" />
</template>
```

### 资产目录

Nuxt 使用 Vite（默认）或 webpack 来构建和打包的应用。这些构建工具的<font color="red">主要功能是处理 JavaScript 文件</font>，但可以通过 插件（用于 Vite）或 加载器（用于 webpack）扩展，以处理其他类型的资产，例如样式表、字体或 SVG。这一过程主要为了性能或缓存目的转换原始文件（例如样式表压缩或浏览器缓存失效）。

在应用代码中，可以通过 \~/assets/ 路径引用位于 assets/ 目录中的文件。

#### 示例

引用一个图像文件，如果构建工具配置为处理此文件扩展名，该文件将被处理：

`app.vue`

```
<template>
  <img src="~/assets/img/nuxt.png" alt="探索 Nuxt" />
</template>
```

## 5.样式

### 本地样式表

如果要编写本地样式表，推荐将它们放在 `assets/` 目录 中。

#### 在组件中导入

可以在页面、布局和组件中直接导入样式表。
可以使用 JavaScript 导入，或使用 CSS 的 `@import` 语句。

`pages/index.vue`

```
<script>
// 为兼容服务器端，使用静态导入
import '~/assets/css/first.css'

// 注意：动态导入与服务器端不兼容
import('~/assets/css/first.css')
</script>

<style>
@import url("~/assets/css/second.css");
</style>
```

#### 使用CSS属性

也可以在 Nuxt 配置中使用 css 属性。 样式表通常存放在 `assets/` 目录 中。可以引用其路径，Nuxt 会将其应用到应用的所有页面。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  css: ['~/assets/css/main.css']
})
```

#### 处理字体

将本地字体文件放在 `~/public/` 目录中，例如 `~/public/fonts`。然后可以在样式表中使用 `url()` 引用它们。

`assets/css/main.css`

```
@font-face {
  font-family: 'FarAwayGalaxy';
  src: url('/fonts/FarAwayGalaxy.woff') format('woff');
  font-weight: normal;
  font-style: normal;
  font-display: swap;
}
```

在样式表、页面或组件中通过字体名称引用：

```
<style>
h1 {
  font-family: 'FarAwayGalaxy', sans-serif;
}
</style>
```

### 通过NPM分发的样式表

引用通过 npm 分发的样式表。
以流行的 animate.css 库为例。
`npm install animate.css
`
然后在页面、布局和组件中直接引用：

`app.vue`

```
<script>
import 'animate.css'
</script>

<style>
@import url("animate.css");
</style>
```

也可以在 Nuxt 配置的 css 属性中以字符串形式引用该包。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  css: ['animate.css']
})
```

### 外部样式表

可以通过在 `nuxt.config` 文件的 `head` 部分添加 `link` 元素来引入外部样式表。

可以使用 Nuxt 配置的 `app.head` 属性来操作 `head`：

`nuxt.config.ts`

```
export default defineNuxtConfig({
  app: {
    head: {
      link: [{ rel: 'stylesheet', href: 'https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css' }]
    }
  }
})
```

#### 动态添加样式表

可以使用 `useHead` 组合式函数在代码中动态设置 `head` 的值。

```
useHead({
  link: [{ rel: 'stylesheet', href: 'https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css' }]
})
```

#### 使用 Nitro 插件修改渲染的 Head

如果需要更高级的控制，可以通过钩子拦截渲染的 HTML 并以编程方式修改 head。

在 `~/server/plugins/my-plugin.ts` 中创建一个插件`server/plugins/my-plugin.ts`

```
export default defineNitroPlugin((nitro) => {
  nitro.hooks.hook('render:html', (html) => {
    html.head.push('<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css">')
  })
})
```

外部样式表是渲染阻塞资源：它们必须在浏览器渲染页面之前加载和处理。包含不必要的大型样式的网页渲染时间会更长。

### 使用预处理器

使用像 SCSS、Sass、Less 或 Stylus 这样的预处理器

`Aass&SCSS`
`npm install -D sass`

`Less`
`npm install -D less`

`Stylus`
`npm install -D stylus`

编写样式表的推荐位置是 `assets` 目录。 可以在 `app.vue`（或布局文件）中使用预处理器的语法导入源文件。

`pages/app.vue`

```
<style lang="scss">
@use "~/assets/scss/main.scss";
</style>
```

或者可以在 Nuxt 配置中使用 css 属性。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  css: ['~/assets/scss/main.scss']
})
```

如果需要在预处理文件中注入代码，例如包含颜色变量的 sass 局部文件，可以使用 Vite 的 预处理器选项。

在 assets 目录中创建一些局部文件：

`assets/_colors.scss`

```
$primary: #49240F;
$secondary: #E4A79D;
```

`assets/_colors.sass`

```
$primary: #49240F
$secondary: #E4A79D
```

然后在`nuxt.config`中`SCSS`

```
export default defineNuxtConfig({
  vite: {
    css: {
      preprocessorOptions: {
        scss: {
          additionalData: '@use "~/assets/_colors.scss" as *;'
        }
      }
    }
  }
})
```

`SASS`

```
export default defineNuxtConfig({
  vite: {
    css: {
      preprocessorOptions: {
        sass: {
          additionalData: '@use "~/assets/_colors.sass" as *\n'
        }
      }
    }
  }
})
```

### 单文件组件（SFC）样式

Vue 和单文件组件（SFC）的一大优点是它们在处理样式方面的出色表现。<font color="red">可以直接在组件文件的 `style` 块中编写 CSS 或预处理器代码</font>，从而获得极佳的开发者体验，无需使用 CSS-in-JS 等技术。如果希望使用 CSS-in-JS，可以找到支持它的第三方库和模块，例如 pinceau。

#### 类和样式绑定

可以使用 Vue SFC 的功能，通过 `class` 和 `style` 属性为组件添加样式。

Ref和Reactive

```
<script setup lang="ts">
const isActive = ref(true)
const hasError = ref(false)
const classObject = reactive({
  active: true,
  'text-danger': false
})
</script>

<template>
  <div class="static" :class="{ active: isActive, 'text-danger': hasError }"></div>
  <div :class="classObject"></div>
</template>
```

Computed

```
<script setup lang="ts">
const isActive = ref(true)
const error = ref(null)

const classObject = computed(() => ({
  active: isActive.value && !error.value,
  'text-danger': error.value && error.value.type === 'fatal'
}))
</script>

<template>
  <div :class="classObject"></div>
</template>
```

Array

```
<script setup lang="ts">
const isActive = ref(true)
const errorClass = ref('text-danger')
</script>

<template>
  <div :class="[{ active: isActive }, errorClass]"></div>
</template>
```

Style

```
<script setup lang="ts">
const activeColor = ref('red')
const fontSize = ref(30)
const styleObject = reactive({ color: 'red', fontSize: '13px' })
</script>

<template>
  <div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
  <div :style="[baseStyles, overridingStyles]"></div>
  <div :style="styleObject"></div>
</template>
```

#### 使用 v-bind 动态样式

可以在 `style` 块中使用 `v-bind` 函数引用 JavaScript 变量和表达式。<font color="red">绑定是动态的</font> ，这意味着如果变量值发生变化，样式会相应更新。

```
<script setup lang="ts">
const color = ref("red")
</script>

<template>
  <div class="text">hello</div>
</template>

<style>
.text {
  color: v-bind(color);
}
</style>
```

#### 作用域样式

`scoped` 属性允许隔离组件的样式。<font color="red">使用此属性声明的样式仅适用于当前组件</font>。

```
<template>
  <div class="example">hi</div>
</template>

<style scoped>
.example {
  color: red;
}
</style>
```

#### CSS 模块

可以通过 module 属性使用 CSS 模块。通过注入的 \$style 变量访问。

```
<template>
  <p :class="$style.red">This should be red</p>
</template>

<style module>
.red {
  color: red;
}
</style>
```

#### 预处理器支持

SFC 的 `style` 块支持预处理器语法。Vite 内置支持 `.scss`、`.sass`、`.less`、`.styl` 和 `.stylus` 文件，无需额外配置。只需先安装它们，就可以通过 `lang` 属性直接在 SFC 中使用。

SCSS

```
<style lang="scss">
  /* 在此编写 scss */
</style>
```

### 使用 PostCSS

Nuxt 内置了 PostCSS。可以在 `nuxt.config` 文件中进行配置。

```
export default defineNuxtConfig({
  postcss: {
    plugins: {
      'postcss-nested': {},
      'postcss-custom-media': {}
    }
  }
})
```

为了在 SFC 中获得正确的语法高亮，可以使用 `postcss` 的 `lang` 属性。

```
<style lang="postcss">
  /* 在此编写 postcss */
</style>
```

默认情况下，Nuxt 已预配置以下插件：

* <font color="7CFC00">postcss-import：</font>改进 @import 规则

* <font color="#7CFC00">postcss-url：</font>转换 url() 语句

* <font color="#7CFC00">autoprefixer：</font>自动添加供应商前缀

* <font color="#7CFC00">cssnano：</font>压缩和清除

### 为多样式使用布局

<font color="red">如果需要为应用的不同部分完全不同的样式，可以使用布局</font>。 为不同的布局使用不同的样式。

```
<template>
  <div class="default-layout">
    <h1>默认布局</h1>
    <slot />
  </div>
</template>

<style>
.default-layout {
  color: red;
}
</style>
```

## 6.路由

pages/ 目录中的每个 Vue 文件都会生成一个对应的 URL（或路由），显示该文件的内容。

### 页面

Nuxt 的路由基于 vue-router，根据 pages/ 目录 中创建的每个组件的文件名生成路由。

这种文件系统路由使用命名约定来创建动态和嵌套路由：

目录结构

```
-| pages/
---| about.vue
---| index.vue
---| posts/
-----| [id].vue
```

生成的路由文件

```
{
  "routes": [
    {
      "path": "/about",
      "component": "pages/about.vue"
    },
    {
      "path": "/",
      "component": "pages/index.vue"
    },
    {
      "path": "/posts/:id",
      "component": "pages/posts/[id].vue"
    }
  ]
}
```

#### 动态路由

如果在文件名或者文件夹名称里面包含了方括号，它们将被转换为动态路由参数。

```
-| pages/
---| users-[group]/
-----| [id].vue
```

#### 嵌套路由

目录和文件同名，就制造了嵌套路由。

```
-| pages/
---| parent/
------| child.vue
---| parent.vue
```

### 导航

`<NuxtLink>` 组件用于在页面之间建立链接。它会渲染一个带有 `href` 属性设置为页面路由的 `<a>` 标签。一旦应用完成水合，页面过渡将通过 JavaScript 更新浏览器 URL 来执行。这可以避免整页刷新，并允许实现动画过渡。

当客户端的 `<NuxtLink>` 进入视口时，Nuxt 会自动提前预取链接页面的组件和数据（生成页面），从而加快导航速度。

`pages/app.vue`

```
<template>
  <header>
    <nav>
      <ul>
        <li><NuxtLink to="/about">关于</NuxtLink></li>
        <li><NuxtLink to="/posts/1">文章 1</NuxtLink></li>
        <li><NuxtLink to="/posts/2">文章 2</NuxtLink></li>
      </ul>
    </nav>
  </header>
</template>
```

### 路由参数

`useRoute()`组合式函数可以在 Vue 组件的 `<script setup>` 块或 `setup()` 方法中使用，以访问当前路由的详细信息。

`pages/posts/[id].vue`

```
<script setup lang="ts">
const route = useRoute()

// 当访问 /posts/1 时，route.params.id 将为 1
console.log(route.params.id)
</script>
```

### 路由中间件

路由中间件有三种类型：

1. 匿名（或内联）路由中间件，直接在使用的页面中定义。
2. 命名路由中间件，放置在 `middleware/` 目录中，并在页面使用时通过异步导入自动加载。
3. 全局路由中间件，放置在 `middleware/` 目录 中（带有 `.global` 后缀），会在每次路由更改时自动运行。

以下是一个保护 `/dashboard` 页面的 `auth` 中间件示例：
`middleware/auth.ts`

```
function isAuthenticated(): boolean { return false }
// ---cut---
export default defineNuxtRouteMiddleware((to, from) => {
  // isAuthenticated() 是一个示例方法，用于验证用户是否已认证
  if (isAuthenticated() === false) {
    return navigateTo('/login')
  }
})
```

`pages/dashboard.vue`

```
<script setup lang="ts">
definePageMeta({
  middleware: 'auth'
})
</script>

<template>
  <h1>欢迎体验你的仪表板</h1>
</template>
```

### 路由验证

Nuxt 通过在每个需要验证的页面中使用 `definePageMeta()` 的 `validate` 属性提供路由验证。

`validate` 属性接受 `route` 作为参数。可以返回一个布尔值来确定该路由是否是使用此页面渲染的有效路由。
如果返回 `false`，并且找不到其他匹配项，将触发 404 错误。
返回一个包含 `statusCode`/`statusMessage` 的对象，以立即响应错误（不会检查其他匹配项）。

如果用例更复杂，可以使用匿名路由中间件。

`pages/posts/[id].vue`

```
<script setup lang="ts">
definePageMeta({
  validate: async (route) => {
    // 检查 id 是否由数字组成
    return typeof route.params.id === 'string' && /^\d+$/.test(route.params.id)
  }
})
</script>
```

## 7.SEO和元数据

### Nuxt配置

在 `nuxt.config.ts` 中提供 `app.head` 属性，可以<font color=red>静态</font>地为整个应用定制头部。

<font color="red">此方法不允许提供响应式数据</font>。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  app: {
    head: {
      title: 'Nuxt', // 默认备用标题
      htmlAttrs: {
        lang: 'en',
      },
      link: [
        { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' },
      ]
    }
  }
})
```

#### 默认标签

Nuxt 默认提供了一些标签，以确保网站开箱即用效果良好：

* `viewport`: `width=device-width,initial-scale=1`

* `charset`: `utf-8`

`nuxt.config.ts`

```
export default defineNuxtConfig({
  app: {
    head: {
      // 更新 Nuxt 默认值
      charset: 'utf-16',
      viewport: 'width=device-width, initial-scale=1, maximum-scale=1',
    }
  }
})
```

#### `useHead`

`useHead` 组合式函数支持响应式输入，允许以编程方式管理头部标签。

`app.vue`

```
<script setup lang="ts">
useHead({
  title: '我的应用',
  meta: [
    { name: 'description', content: '我的精彩网站。' }
  ],
  bodyAttrs: {
    class: 'test'
  },
  script: [ { innerHTML: 'console.log(\'Hello world\')' } ]
})
</script>
```

#### `useSeoMeta`

`useSeoMeta` 组合式函数允许以<font color=red>对象形式</font>定义站点的 SEO 元数据标签，并提供完整的类型安全。

这可以避免拼写错误和常见错误，例如使用 `name` 而不是 `property`。

`app.vue`

```
<script setup lang="ts">
useSeoMeta({
  title: '我的精彩网站',
  ogTitle: '我的精彩网站',
  description: '这是我的精彩网站，让我为你详细介绍。',
  ogDescription: '这是我的精彩网站，让我为你详细介绍。',
  ogImage: 'https://example.com/image.png',
  twitterCard: 'summary_large_image',
})
</script>
```

### 组件

Nuxt 提供了以下组件：`<Title>`、`<Base>`、`<NoScript>`、`<Style>`、`<Meta>`、`<Link>`、`<Body>`、`<Html>` 和 `<Head>`。可以在模板中使用组件来定义头部标签。<font color="red">这些组件的首字母大写，以确保不使用无效的原生 HTML 标签。</font>

`<Head>` 和 `<Body>` 可以接受嵌套的元数据标签，但不会影响嵌套元数据标签在最终 HTML 中的渲染位置。

`app.vue`

```
<script setup lang="ts">
const title = ref('Hello World')
</script>

<template>
  <div>
    <Head>
      <Title>{{ title }}</Title>
      <Meta name="description" :content="title" />
      <Style>
      body { background-color: green; }
      </Style>
    </Head>

    <h1>{{ title }}</h1>
  </div>
</template>
```

建议将组件包裹在 `<Head>` 或 `<Html>` 组件中，因为这样标签去重会更直观。

### 类型

以下是用于 `useHead`、`app.head` 和组件的非响应式类型。

```
interface MetaObject {
  title?: string
  titleTemplate?: string | ((title?: string) => string)
  templateParams?: Record<string, string | Record<string, string>>
  base?: Base
  link?: Link[]
  meta?: Meta[]
  style?: Style[]
  script?: Script[]
  noscript?: Noscript[];
  htmlAttrs?: HtmlAttributes;
  bodyAttrs?: BodyAttributes;
}
```

### 功能

#### 响应式

所有属性都支持响应式，可以提供计算属性、getter 或响应式对象。

`useHead`

```
<script setup lang="ts">
const description = ref('我的精彩网站。')

useHead({
  meta: [
    { name: 'description', content: description }
  ],
})
</script>
```

`useSeoMeta`

```
<script setup lang="ts">
const description = ref('我的精彩网站。')

useSeoMeta({
  description
})
</script>
```

`Components`

```
<script setup lang="ts">
const description = ref('我的精彩网站。')
</script>

<template>
  <div>
    <Meta name="description" :content="description" />
  </div>
</template>
```

#### 标题模板

可以使用 `titleTemplate` 选项提供动态模板来自定义站点的标题。例如，可以将站点名称添加到每个页面的标题中。

`titleTemplate` 可以是一个字符串，其中 `%s` 会被标题替换，或者是一个函数。

如果想使用函数（以获得完全控制），则无法在 `nuxt.config` 中设置。建议在 `app.vue` 文件中设置，它将应用于站点上的所有页面：

`useHead`

```
<script setup lang="ts">
useHead({
  titleTemplate: (titleChunk) => {
    return titleChunk ? `${titleChunk} - 站点标题` : '站点标题';
  }
})
</script>
```

如果在站点其他页面使用 `useHead` 将标题设置为 `我的页面`，浏览器标签中的标题将显示为“我的页面 - 站点标题”。也可以传递 `null` 以默认显示“站点标题”。

#### 模板参数

可以使用 `templateParams` 在 `titleTemplate` 中提供除默认 `%s` 之外的额外占位符，从而实现更动态的标题生成。

`useHead`

```
<script setup lang="ts">
useHead({
  titleTemplate: (titleChunk) => {
    return titleChunk ? `${titleChunk} %separator %siteName` : '%siteName';
  },
  templateParams: {
    siteName: '站点标题',
    separator: '-'
  }
})
</script>
```

#### Body标签

对于适用的标签，可以使用 `tagPosition: 'bodyClose'` 选项将它们追加到 `<body>` 标签的末尾。

```
<script setup lang="ts">
useHead({
  script: [
    {
      src: 'https://third-party-script.com',
      // 有效选项为：'head' | 'bodyClose' | 'bodyOpen'
      tagPosition: 'bodyClose'
    }
  ]
})
</script>
```

### 示例

#### 使用`definePageMeta`

在 `pages/` 目录 中，可以使用 `definePageMeta` 结合 `useHead` 根据当前路由设置元数据。

例如，可以先设置当前页面标题（此标题通过宏在构建时提取，因此无法动态设置）：

`pages/some-page.vue`

```
<script setup lang="ts">
definePageMeta({
  title: '某个页面'
})
</script>
```

然后在布局文件中，可以使用之前设置的路由元数据：

`layouts/default.vue`

```
<script setup lang="ts">
const route = useRoute()

useHead({
  meta: [{ property: 'og:title', content: `应用名称 - ${route.meta.title}` }]
})
</script>
```

#### 动态标题

在下面的示例中，`titleTemplate` 被设置为带有 `%s` 占位符的字符串或一个函数，这为 Nuxt 应用的每个路由动态设置页面标题提供了更大的灵活性：

`app.vue`

```
<script setup lang="ts">
useHead({
  // 作为字符串，
  // 其中 `%s` 会被标题替换
  titleTemplate: '%s - 站点标题',
})
</script>
```

`app.vue`

```
<script setup lang="ts">
useHead({
  // 或作为函数
  titleTemplate: (productCategory) => {
    return productCategory
      ? `${productCategory} - 站点标题`
      : '站点标题'
  }
})
</script>
```

`nuxt.config` 也可以用来设置页面标题。然而，`nuxt.config` 不允许动态设置页面标题。因此，建议在 `app.vue` 文件中使用 `titleTemplate` 来添加动态标题，然后将其应用于 Nuxt 应用的所有路由。

#### 外部CSS

使用 `useHead` 组合式函数的 `link` 属性或使用 `<Link>` 组件启用 Google Fonts：

`useHead`

```
<script setup lang="ts">
useHead({
  link: [
    {
      rel: 'preconnect',
      href: 'https://fonts.googleapis.com'
    },
    {
      rel: 'stylesheet',
      href: 'https://fonts.googleapis.com/css2?family=Roboto&display=swap',
      crossorigin: ''
    }
  ]
})
</script>
```

`Components`

```
<template>
  <div>
    <Link rel="preconnect" href="https://fonts.googleapis.com" />
    <Link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Roboto&display=swap" crossorigin="" />
  </div>
</template>
```

## 8.过渡

使用 Vue 或原生浏览器视图过渡 API 在页面和布局之间应用过渡效果。

### 页面过渡

可以启用页面过度，为所有页面自动应用过渡效果。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  app: {
    pageTransition: { name: 'page', mode: 'out-in' }
  },
})
```

要在页面之间添加过渡效果，需在 `app.vue` 中添加以下 CSS：

`app.vue`

```
<template>
  <NuxtPage />
</template>

<style>
.page-enter-active,
.page-leave-active {
  transition: all 0.4s;
}
.page-enter-from,
.page-leave-to {
  opacity: 0;
  filter: blur(1rem);
}
</style>
```

`pages/index.vue`

```
<template>
  <div>
    <h1>首页</h1>
    <NuxtLink to="/about">关于页面</NuxtLink>
  </div>
</template>
```

`pages/about.vue`

```
<template>
  <div>
    <h1>关于页面</h1>
    <NuxtLink to="/">首页</NuxtLink>
  </div>
</template>
```

要为某个页面设置不同的过渡效果，可以在该页面的 `definePageMeta` 中设置 `pageTransition` 键：

`pages/about.vue`

```
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'rotate'
  }
})
</script>
```

`app.vue`

```
<template>
  <NuxtPage />
</template>

<style>
/* ... */
.rotate-enter-active,
.rotate-leave-active {
  transition: all 0.4s;
}
.rotate-enter-from,
.rotate-leave-to {
  opacity: 0;
  transform: rotate3d(1, 1, 1, 15deg);
}
</style>
```

### 布局过渡

可以启用布局过渡，为所有布局自动应用过渡效果。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  app: {
    layoutTransition: { name: 'layout', mode: 'out-in' }
  },
})
```

要在页面和布局之间添加过渡效果，请在 `app.vue` 中添加以下 CSS：

`app.vue`

```
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>

<style>
.layout-enter-active,
.layout-leave-active {
  transition: all 0.4s;
}
.layout-enter-from,
.layout-leave-to {
  filter: grayscale(1);
}
</style>
```

`layouts/default.vue`

```
<template>
  <div>
    <pre>默认布局</pre>
    <slot />
  </div>
</template>

<style scoped>
div {
  background-color: lightgreen;
}
</style>
```

`layouts/orange.vue`

```
<template>
  <div>
    <pre>橙色布局</pre>
    <slot />
  </div>
</template>

<style scoped>
div {
  background-color: #eebb90;
  padding: 20px;
  height: 100vh;
}
</style>
```

`pages/index.vue`

```
<template>
  <div>
    <h1>首页</h1>
    <NuxtLink to="/about">关于页面</NuxtLink>
  </div>
</template>
```

`pages/about.vue`

```
<script setup lang="ts">
definePageMeta({
  layout: 'orange'
})
</script>

<template>
  <div>
    <h1>关于页面</h1>
    <NuxtLink to="/">首页</NuxtLink>
  </div>
</template>
```

与 `pageTransition` 类似，你可以使用 `definePageMeta` 为页面组件应用自定义 `layoutTransition`：

`pages/about.vue`

```
<script setup lang="ts">
definePageMeta({
  layout: 'orange',
  layoutTransition: {
    name: 'slide-in'
  }
})
</script>
```

### 全局设置

可以使用 `nuxt.config` 全局自定义这些默认过渡名称。

`pageTransition` 和 `layoutTransition` 键接受 `TransitionProps` 作为 JSON 可序列化的值，可以在其中传递 `name`、`mode` 和其他有效的自定义 CSS 过渡属性。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  app: {
    pageTransition: {
      name: 'fade',
      mode: 'out-in' // 默认值
    },
    layoutTransition: {
      name: 'slide',
      mode: 'out-in' // 默认值
    }
  }
})
```

要覆盖全局过渡属性，可以使用 `definePageMeta` 为单个 Nuxt 页面定义页面或布局过渡，并覆盖在 `nuxt.config` 文件中全局定义的任何页面或布局过渡。

`pages/some-page.vue`

```
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'bounce',
    mode: 'out-in' // 默认值
  }
})
</script>
```

### 禁用过渡

可以为特定路由禁用 `pageTransition` 和 `layoutTransition`：

`pages/some-page.vue`

```
<script setup lang="ts">
definePageMeta({
  pageTransition: false,
  layoutTransition: false
})
</script>
```

或在 `nuxt.config` 中全局禁用：

```
export default defineNuxtConfig({
  app: {
    pageTransition: false,
    layoutTransition: false
  }
})
```

### JavaScript 钩子

对于高级用例，可以使用 JavaScript 钩子为 Nuxt 页面创建高度动态和自定义的过渡效果。

`pages/some-page.vue`

```
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'custom-flip',
    mode: 'out-in',
    onBeforeEnter: (el) => {
      console.log('进入之前...')
    },
    onEnter: (el, done) => {},
    onAfterEnter: (el) => {}
  }
})
</script>
```

### 动态过渡

要使用条件逻辑应用动态过渡，可以利用内联 中间件 为 `to.meta.pageTransition` 分配不同的过渡名称。

`pages/[id].vue`

```
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'slide-right',
    mode: 'out-in'
  },
  middleware (to, from) {
    if (to.meta.pageTransition && typeof to.meta.pageTransition !== 'boolean')
      to.meta.pageTransition.name = +to.params.id! > +from.params.id! ? 'slide-left' : 'slide-right'
  }
})
</script>

<template>
  <h1>#{{ $route.params.id }}</h1>
</template>

<style>
.slide-left-enter-active,
.slide-left-leave-active,
.slide-right-enter-active,
.slide-right-leave-active {
  transition: all 0.2s;
}
.slide-left-enter-from {
  opacity: 0;
  transform: translate(50px, 0);
}
.slide-left-leave-to {
  opacity: 0;
  transform: translate(-50px, 0);
}
.slide-right-enter-from {
  opacity: 0;
  transform: translate(-50px, 0);
}
.slide-right-leave-to {
  opacity: 0;
  transform: translate(50px, 0);
}
</style>
```

`layouts/default.vue`

```
<script setup lang="ts">
const route = useRoute()
const id = computed(() => Number(route.params.id || 1))
const prev = computed(() => '/' + (id.value - 1))
const next = computed(() => '/' + (id.value + 1))
</script>

<template>
  <div>
    <slot />
    <div v-if="$route.params.id">
      <NuxtLink :to="prev">⬅️</NuxtLink> |
      <NuxtLink :to="next">➡️</NuxtLink>
    </div>
  </div>
</template>
```

页面现在会在前往下一个 id 时应用 slide-left 过渡，前往上一个 id 时应用 slide-right。

### 使用 NuxtPage 的过渡

当在 `app.vue` 中使用 `<NuxtPage />` 时，可以通过 `transition` 属性配置过渡效果，以全局启用过渡。

```
<template>
  <div>
    <NuxtLayout>
      <NuxtPage :transition="{
        name: 'bounce',
        mode: 'out-in'
      }" />
    </NuxtLayout>
  </div>
</template>
```

## 9.数据获取

Nuxt 提供了两个组合式函数和一个内置库，用于在浏览器或服务器环境中执行数据获取：`useFetch`、`useAsyncData` 和 `$fetch`。

* `$fetch` 是发起网络请求的最简单方式。

* `useFetch` 是对 `$fetch` 的封装，仅在 通用渲染 中获取一次数据。

* `useAsyncData` 与 `useFetch` 类似，但提供了更精细的控制。

##### useFetch

页面、组件或者插件中可以使用useFetch获取任意URL资源。

```
const {
  data: Ref<DataT>,
  pending: Ref<boolean>,
  refresh: (force?: boolean) => Promise<void>,
  error?: any
} = useFetch(url: string, options?)
```

##### useAsyncData

在页面、组件或插件中都可以使用`useAsyncData`获取那些异步数据。

```
const {
  data: Ref<DataT>, // 返回的数据
  pending: Ref<boolean>, // 加载状态指示器
  refresh: (force?: boolean) => Promise<void>, // 强制刷新函数
  error?: any // 请求失败的错误信息
} = useAsyncData(
  key: string,// 唯一键用于多次请求结果去重
  fn: () => Object,// 返回数值的异步函数
  // lazy - 是否在路由之后才请求数据，server - 是否在服务端请求数据
  options?: { lazy: boolean, server: boolean } 
)
```

获取待办事项数据，index.vue

```
<template>
  <div>
    <!-- 待办列表 -->
    <div v-for="todo in todos" :key="todo.id">
      <input type="checkbox" v-model="todo.completed">
      <strong>{{todo.title}}</strong>
    </div>
  </div>
</template>
<script setup lang="ts">
const { data: todos } = await useAsyncData(
  'count', () => $fetch('/api/todos'))
</script>
```

#### Suspense

Nuxt 在底层使用了 Vue 的 `<Suspense>` 组件<font color="red">以防止在所有异步数据准备好之前进行导航</font>。数据获取的组合式函数（composables）可以更好地利用这一特性，并根据每次调用的具体情况选择最合适的方式。

#### `$fetch`

Nuxt 包含了 ofetch 库，并将其作为 \$fetch 别名在整个应用中全局自动导入。

`pages/todos.vue`

```
<script setup lang="ts">
async function addTodo() {
  const todo = await $fetch('/api/todos', {
    method: 'POST',
    body: {
      // 我的待办事项数据
    }
  })
}
</script>
```

#### 将客户端头部传递给 API

在服务器端调用 `useFetch` 时，Nuxt 会使用 `useRequestFetch` 来代理客户端的头部和 `cookie`。

```
<script setup lang="ts">
const { data } = await useFetch('/api/echo');
</script>
```

```
// /api/echo.ts
export default defineEventHandler(event => parseCookies(event))
```

或者，以下示例展示了如何使用 `useRequestHeaders` 从服务器端请求（源自客户端）访问并发送 cookie 给 API。使用同构的 `$fetch` 调用，确保 API 端点能够访问用户浏览器最初发送的相同 `cookie` 头部。如果不使用 `useFetch`，则需要此操作。

```
<script setup lang="ts">
const headers = useRequestHeaders(['cookie'])

async function getCurrentUser() {
  return await $fetch('/api/me', { headers })
}
</script>
```

在某些情况下，使用 `useFetch` 组合式函数可能不合适，例如当 CMS 或第三方提供自己的查询层时。
在这种情况下，可以使用 `useAsyncData` 来包装调用，同时保留组合式函数提供的优势。

`pages/users.vue`

```
<script setup lang="ts">
const { data, error } = await useAsyncData('users', () => myGetFunction('users'))

// 这也是可以的：
const { data, error } = await useAsyncData(() => myGetFunction('users'))
</script>
```

<font color=skyblue>`useAsyncData` 的第一个参数是一个用于缓存第二个参数（查询函数）响应的唯一键。如果直接传递查询函数，可以忽略此键，键将自动生成。

由于自动生成的键仅考虑调用 `useAsyncData` 的文件和行号，建议始终创建自己的键，以避免意外行为，例如在创建包装 `useAsyncData` 的自定义组合式函数时。

设置键对于在组件之间共享相同数据（使用 `useNuxtData`）或 刷新特定数据 非常有用。 </font>

`pages/users/[id].vue`

```
<script setup lang="ts">
const { id } = useRoute().params

const { data, error } = await useAsyncData(`user:${id}`, () => {
  return myGetFunction('users', { id })
})
</script>
```

`useAsyncData` 组合式函数是包装并等待多个 `$fetch` 请求完成并处理结果的好方法。

```
<script setup lang="ts">
const { data: discounts, status } = await useAsyncData('cart-discount', async () => {
  const [coupons, offers] = await Promise.all([
    $fetch('/cart/coupons'),
    $fetch('/cart/offers')
  ])

  return { coupons, offers }
})
// discounts.value.coupons
// discounts.value.offers
</script>
```

<font color=skyblue>

`useAsyncData` 用于获取和缓存数据，而不是触发副作用，例如调用 Pinia 操作，因为这可能导致意外行为，如重复执行并返回空值。如果需要触发副作用，请使用 `callOnce` 工具。

</font>

```
<script setup lang="ts">
const offersStore = useOffersStore()

// 你不能这样做
await useAsyncData(() => offersStore.getOffer(route.params.slug))
</script>
```

### 返回值

`useFetch` 和 `useAsyncData` 返回以下相同的返回值：

* `data`：传入的异步函数的结果。

* `refresh/execute`：一个可用于刷新`handler` 函数返回数据的函数。

* `clear`：一个可用于将 `data` 设置为 `undefined`、将 `error` 设置为 `null`、将 `status` 设置为 `idle` 并将任何当前待处理的请求标记为取消的函数。

* `error`：如果数据获取失败，则返回错误对象。

* `status`：一个指示数据请求状态的字符串（`"idle"`、`"pending"`、`"success"`、`"error"`）。
  默认情况下，Nuxt 会等待 `refresh` 完成后再执行下一次。

### 选项

`useAsyncData` 和 `useFetch` 返回相同的对象类型，并接受一组通用的选项作为最后一个参数。这些选项可以帮助控制组合式函数的行为，例如导航阻止、缓存或执行。

#### 延迟加载

默认情况下，数据获取的组合式函数会在导航到新页面之前，使用 Vue 的 Suspense 等待其异步函数的解析结果。如果你不想在客户端导航时等待加载完成，可以使用 `lazy` 选项来忽略这一特性。这种情况下，你需要通过 `status` 值手动处理加载状态。

`app.vue`

```
<script setup lang="ts">
const { status, data: posts } = useFetch('/api/posts', {
  lazy: true
})
</script>

<template>
  <!-- 你需要处理加载状态 -->
  <div v-if="status === 'pending'">
    加载中 ...
  </div>
  <div v-else>
    <div v-for="post in posts">
      <!-- 进行操作 -->
    </div>
  </div>
</template>
```

也可以使用 `useLazyFetch` 和 `useLazyAsyncData` 作为便捷方法来实现相同的功能。

```
<script setup lang="ts">
const { status, data: posts } = useLazyFetch('/api/posts')
</script>
```

#### 仅客户端获取

默认情况下，数据获取组合式函数会在客户端和服务器环境中执行其异步函数。将 `server` 选项设置为 `false` 可以仅在客户端执行调用。在初始加载时，数据不会在水合完成之前获取，因此必须处理待处理状态，但在后续客户端导航时，数据会在加载页面之前等待。

```
/* 此调用在水合之前执行 */
const articles = await useFetch('/api/article')

/* 此调用仅在客户端执行 */
const { status, data: comments } = useFetch('/api/comments', {
  lazy: true,
  server: false
})
```

`useFetch` 组合式函数应在 `setup` 方法中调用或在生命周期钩子的函数顶层直接调用，否则你应使用 `$fetch` 方法。

#### 最小化负载大小

`pick` 选项可以帮助通过仅选择所需的字段来最小化存储在 HTML 文档中的负载大小。

```
<script setup lang="ts">
/* 仅选择模板中使用的字段 */
const { data: mountain } = await useFetch('/api/mountains/everest', {
  pick: ['title', 'description']
})
</script>

<template>
  <h1>{{ mountain.title }}</h1>
  <p>{{ mountain.description }}</p>
</template>
```

如果需要更多控制或映射多个对象，可以使用 `transform` 函数来更改查询结果。

```
const { data: mountains } = await useFetch('/api/mountains', {
  transform: (mountains) => {
    return mountains.map(mountain => ({ title: mountain.title, description: mountain.description }))
  }
})
```

#### 缓存和重新获取

#### 键

`useFetch` 和 `useAsyncData` 使用键来防止重复获取相同的数据。

* `useFetch` 使用提供的 URL 作为 key。或者，也可以在作为最后一个参数传入的 `options` 对象中提供一个 `key` 值。

* `useAsyncData` 如果第一个参数是字符串，则使用它作为 key；如果第一个参数是执行查询的处理函数（handler function），则会自动为你生成一个基于文件名和代码行号的唯一 key。

###### 共享状态和选项一致性

当多个组件使用相同的 key 调用 `useAsyncData` 或 `useFetch` 时，它们将共享相同的 `data`、`error` 和 `status` 引用。这保证了组件之间的一致性，但要求一些选项必须保持一致。

以下选项 必须在使用相同 key 的所有调用中保持一致：

* `handler` 函数

* `deep` 选项

* `transform` 函数

* `pick` 数组

* `getCachedData` 函数

* `default` 值

```
// ❌ 这将触发开发警告
const { data: users1 } = useAsyncData('users', () => $fetch('/api/users'), { deep: false })
const { data: users2 } = useAsyncData('users', () => $fetch('/api/users'), { deep: true })
```

以下选项 可以安全地不同，而不会触发警告：

* `server`

* `lazy`

* `immediate`

* `dedupe`

* `watch`

```
// ✅ 这是允许的
const { data: users1 } = useAsyncData('users', () => $fetch('/api/users'), { immediate: true })
const { data: users2 } = useAsyncData('users', () => $fetch('/api/users'), { immediate: false })
```

如果需要独立的实例，可以使用不同的 key：

```
// 这些是完全独立的实例
const { data: users1 } = useAsyncData('users-1', () => $fetch('/api/users'))
const { data: users2 } = useAsyncData('users-2', () => $fetch('/api/users'))
```

#### 响应式键

可以使用计算属性、普通引用或 getter 函数作为 key，这允许动态数据获取，并在依赖项变化时自动更新：

```
// 使用计算属性作为 key
const userId = ref('123')
const { data: user } = useAsyncData(
  computed(() => `user-${userId.value}`),
  () => fetchUser(userId.value)
)

// 当 userId 变化时，数据将自动重新获取
// 并且如果没有其他组件使用旧数据，它将被清除
userId.value = '456'
```

#### 刷新和执行

如果想手动获取或刷新数据，可以使用组合式函数提供的 `execute` 或 `refresh` 函数。

```
<script setup lang="ts">
const { data, error, execute, refresh } = await useFetch('/api/users')
</script>

<template>
  <div>
    <p>{{ data }}</p>
    <button @click="() => refresh()">刷新数据</button>
  </div>
</template>
```

`execute` 函数是 `refresh` 的别名，功能完全相同，但在 非立即执行 的情况下语义更明确。

#### 清除

如果出于任何原因想清除提供的数据，而无需知道传递给 `clearNuxtData` 的具体键，可以使用组合式函数提供的 `clear` 函数。

```
<script setup lang="ts">
const { data, clear } = await useFetch('/api/users')

const route = useRoute()
watch(() => route.path, (path) => {
  if (path === '/') clear()
})
</script>
```

#### 监视

要每次应用中的其他响应式值发生变化时重新运行你的获取函数，可以使用 `watch` 选项。你可以为一个或多个可监视的元素使用它。

```
<script setup lang="ts">
const id = ref(1)

const { data, error, refresh } = await useFetch('/api/users', {
  /* 更改 id 将触发重新获取 */
  watch: [id]
})
</script>
```

监视响应式值不会更改获取的 URL。例如，以下代码将始终获取用户的初始 ID，因为 URL 是在函数调用时构建的。

```
<script setup lang="ts">
const id = ref(1)

const { data, error, refresh } = await useFetch(`/api/users/${id.value}`, {
  watch: [id]
})
</script>
```

#### 计算 URL

需要根据响应式值计算 URL，并在这些值变化时刷新数据时可以将每个参数作为响应式值附加，Nuxt 会自动使用响应式值并在每次变化时重新获取。

```
<script setup lang="ts">
const id = ref(null)

const { data, status } = useLazyFetch('/api/user', {
  query: {
    user_id: id
  }
})
</script>
```

对于更复杂的 URL 构建，可以使用一个返回 URL 字符串的 计算 getter 作为回调。

每次依赖项发生变化时，将使用新构建的 URL 获取数据。结合 非立即执行，可以等待响应式元素变化后再进行获取。

```
<script setup lang="ts">
const id = ref(null)

const { data, status } = useLazyFetch(() => `/api/users/${id.value}`, {
  immediate: false
})

const pending = computed(() => status.value === 'pending');
</script>

<template>
  <div>
    <!-- 获取时禁用输入框 -->
    <input v-model="id" type="number" :disabled="pending"/>

    <div v-if="status === 'idle'">
      输入用户 ID
    </div>

    <div v-else-if="pending">
      加载中 ...
    </div>

    <div v-else>
      {{ data }}
    </div>
  </div>
</template>
```

如果需要强制在其他响应式值变化时刷新，还可以 监视其他值。

#### 非立即执行

`useFetch` 组合式函数在调用时会立即开始获取数据。可以通过设置 `immediate: false` 来阻止此行为，例如，等待用户交互。

为此，需要使用 `status` 来处理获取生命周期，以及 `execute` 来开始数据获取。

```
<script setup lang="ts">
const { data, error, execute, status } = await useLazyFetch('/api/comments', {
  immediate: false
})
</script>

<template>
  <div v-if="status === 'idle'">
    <button @click="execute">获取数据</button>
  </div>

  <div v-else-if="status === 'pending'">
    加载评论中...
  </div>

  <div v-else>
    {{ data }}
  </div>
</template>
```

为了更精细的控制，`status` 变量可以是：

* `idle`：当获取尚未开始时

* `pending`：当获取已开始但尚未完成时

* `error`：当获取失败时

* `success`：当获取成功完成时

### 传递头部和 Cookie

在浏览器中调用 `$fetch` 时，用户的头部（如 `cookie`）会直接发送到 API。

通常，在服务器端渲染期间，出于安全考虑，`$fetch` 不会包含用户浏览器的 cookie，也不会传递获取响应的 cookie。

然而，当在服务器端使用相对 URL 调用 `useFetch` 时，Nuxt 会使用 `useRequestFetch` 来代理头部和 cookie（不包括不应转发的头部，如 `host`）。

#### 在 SSR 响应中传递服务器端 API 调用的 Cookie

如果想反向传递/代理 cookie，从内部请求返回到客户端，需要自己处理。

`composables/fetch.ts`

```
import { appendResponseHeader } from 'h3'
import type { H3Event } from 'h3'

export const fetchWithCookie = async (event: H3Event, url: string) => {
  /* 从服务器端点获取响应 */
  const res = await $fetch.raw(url)
  /* 获取响应中的 cookie */
  const cookies = res.headers.getSetCookie()
  /* 将每个 cookie 附加到我们的传入请求 */
  for (const cookie of cookies) {
    appendResponseHeader(event, 'set-cookie', cookie)
  }
  /* 返回响应的数据 */
  return res._data
}
```

```
<script setup lang="ts">
// 此组合式函数会自动将 cookie 传递给客户端
const event = useRequestEvent()

const { data: result } = await useAsyncData(() => fetchWithCookie(event!, '/api/with-cookie'))

onMounted(() => console.log(document.cookie))
</script>
```

### Options API 支持

Nuxt 提供了在 Options API 中执行 `asyncData` 获取的方式。你必须将组件定义包裹在 `defineNuxtComponent` 中才能使用此功能。

```
<script>
export default defineNuxtComponent({
  /* 使用 fetchKey 选项提供唯一键 */
  fetchKey: 'hello',
  async asyncData () {
    return {
      hello: await $fetch('/api/hello')
    }
  }
})
</script>
```

## 10.状态管理

Nuxt 提供了 `useState` 组合式函数，用于创建跨组件的响应式且 SSR 友好的共享状态。

`useState` 是对 `ref` 的 SSR 友好替代。其值在服务器端渲染后（在客户端水合期间）会被保留，并通过唯一键key在所有组件之间共享。

由于 `useState` 内的数据会被序列化为 JSON，因此重要的是它不能包含无法序列化的内容，如类、函数或符号。

```
useState<T>(key: string, init?: () => T): Ref<T>
```

* key：唯一键用于去重

* init：提供初始值的函数

### 状态共享

使用`useState`可以定义简单的共享状态 <font color="red">只允许在生命周期中使用</font>
例如在`composables`中定义 `useCounter`

```
// composables/useCounter.ts
// composables 下模块的同名导出将被自动按需引入
export const useCounter = () => useState('counter', () => 100)
```

`useState` 的第一参数为 `key`，第二参数为初始化的工厂函数

此时在`index`页面中可以直接使用 `useCounter`

```
<!-- pages/index.vue -->
<script lang="ts" setup>
    const conuter = useCounter() // 这是按需自动引入的
</script>

<template>
    <div> <!-- 顶层的 div 包裹是必需的，不然跳转会丢失状态 -->
        <div @click="conuter++">{{ conuter }}</div>
        <NuxtLink to="/home">to home</NuxtLink>
    </div>
</template>
```

`home`页面中也可直接使用

```
<!-- pages/home.vue -->
<script lang="ts" setup>
	const conuter = useCounter()
</script>

<template>
	<div @click="conuter++">{{ conuter }}</div>
</template>
```

#### 初始化状态

用异步解析的数据初始化状态。可以在 `app.vue` 组件中结合 `callOnce` 工具来实现。

`app.vue`

```
<script setup lang="ts">
const websiteConfig = useState('config')

await callOnce(async () => {
  websiteConfig.value = await $fetch('https://my-cms.com/api/website-config')
})
</script>
```

## 11.错误处理

Nuxt 是一个全栈框架，这意味着在不同上下文中可能会发生多种不可避免的用户运行时错误：

* Vue 渲染生命周期中的错误（SSR 和 CSR）

* 服务器和客户端启动错误（SSR + CSR）

* Nitro 服务器生命周期中的错误（server/ 目录）

* 下载 JS 块时的错误

<font color="00FF00">SSR表示服务器端渲染，CSR表示客户端渲染</font>

### Vue 错误

可以使用 `onErrorCaptured` 钩子捕获 Vue 错误。

此外，Nuxt 提供了一个 `vue:error` 钩子，当任何错误传播到顶层时会被调用。

如果使用的是错误报告框架，可以通过 `vueApp.config.errorHandler` 提供一个全局处理程序。它将接收所有 Vue 错误，即使这些错误已被处理。

`plugins/error-handler.ts`

```
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.config.errorHandler = (error, instance, info) => {
    // 处理错误，例如报告给服务
  }

  // 也可以这样做
  nuxtApp.hook('vue:error', (error, instance, info) => {
    // 处理错误，例如报告给服务
  })
})
```

### 启动错误

如果 Nuxt 应用启动时发生任何错误，Nuxt 将调用 `app:error` 钩子。

这包括：

* 运行 Nuxt 插件

* 处理 `app:created` 和 `app:beforeMount` 钩子

* 将 Vue 应用渲染为 HTML（在 SSR 期间）

* 挂载应用（在客户端），不过这种情况应通过 `onErrorCaptured` 或 `vue:error` 处理

* 处理 `app:mounted` 钩子

### Nitro 服务器错误

目前无法为这些错误定义服务器端处理程序，但可以渲染一个错误页面

### JS 块错误

由于网络连接失败或新部署（导致旧的哈希 JS 块 URL 失效），可能会遇到块加载错误。Nuxt 提供内置支持，通过在路由导航期间块加载失败时执行硬重载来处理这些错误。

可以通过将 `experimental.emitRouteChunkError` 设置为 `false`（完全禁用对此类错误的钩子）或 `manual`（如果你想自己处理）来更改此行为。

### 错误页面

错误可能在以下服务器生命周期中发生：

* 处理 Nuxt 插件

* 将 Vue 应用渲染为 HTML

* 服务器 API 路由抛出错误

也可能在客户端发生：

* 处理 Nuxt 插件

* 在挂载应用之前（`app:beforeMount` 钩子）

* 如果错误未通过 `onErrorCaptured` 或 `vue:error` 钩子处理，则在挂载应用时

* Vue 应用在浏览器中初始化并挂载（`app:mounted`）。

通过在应用源目录中添加 `~/error.vue`（与 `app.vue` 同级）来自定义默认错误页面。

`error.vue`

```
<script setup lang="ts">
import type { NuxtError } from '#app'

const props = defineProps({
  error: Object as () => NuxtError
})

const handleError = () => clearError({ redirect: '/' })
</script>

<template>
  <div>
    <h2>{{ error.statusCode }}</h2>
    <button @click="handleError">清除错误</button>
  </div>
</template>
```

对于自定义错误，强烈建议使用可在页面/组件 `setup` 函数中调用的 `onErrorCaptured` 组合式函数，或在 Nuxt 插件中配置的 `vue:error` 运行时钩子。

`plugins/error-handler.ts`

```
export default defineNuxtPlugin(nuxtApp => {
  nuxtApp.hook('vue:error', (err) => {
    //
  })
})
```

### 错误工具

#### `useError`

TS 签名

```
function useError (): Ref<Error | { url, statusCode, statusMessage, message, description, data }>
```

此函数将返回正在处理的全局 Nuxt 错误。

#### `createError`

TS签名

```
function createError (err: string | { cause, data, message, name, stack, statusCode, statusMessage, fatal }): Error
```

创建带有额外元数据的错误对象。可以传递一个字符串作为错误 `message`，或传递一个包含错误属性的对象。它可在应用的 Vue 和服务器部分使用，并应被抛出。

如果抛出使用 `createError` 创建的错误：

* 在服务器端，它将触发全屏错误页面，你可以使用 `clearError` 清除。

* 在客户端，它将抛出非致命错误供你处理。如果需要触发全屏错误页面，可以通过设置 `fatal: true` 实现。

`pages/movies/[slug].vue`

```
<script setup lang="ts">
const route = useRoute()
const { data } = await useFetch(`/api/movies/${route.params.slug}`)

if (!data.value) {
  throw createError({
    statusCode: 404,
    statusMessage: '页面未找到'
  })
}
</script>
```

#### `showError`

TS签名

```
function showError (err: string | Error | { statusCode, statusMessage }): Error
```

可以在客户端的任何时候调用此函数，或者在服务器端的中间件、插件或 `setup()` 函数中直接调用。它将触发全屏错误页面，可以使用 `clearError` 清除。

建议使用 `throw createError()` 代替。

#### `clearError`

TS 签名

```
function clearError (options?: { redirect?: string }): Promise<void>
```

此函数将清除当前处理的 Nuxt 错误。它还接受一个可选的重定向路径（例如，如果想导航到“安全”页面）。

### 在组件中渲染错误

`<NuxtErrorBoundary>` 组件，允许在应用中处理客户端错误，而无需用错误页面替换整个站点。

此组件负责处理其默认插槽中发生的错误。在客户端，它将阻止错误冒泡到顶层，并渲染 `#error` 插槽。

`#error` 插槽将接收 `error` 作为 prop。（如果将 `error = null`，它将触发重新渲染默认插槽；需要确保错误已完全解决，否则错误插槽将再次渲染。）

<font color="00FF00">如果你导航到另一个路由，错误将自动清除。</font>

`pages/index.vue`

```
<template>
  <!-- 一些内容 -->
  <NuxtErrorBoundary @error="someErrorLogger">
    <!-- 使用默认插槽渲染你的内容 -->
    <template #error="{ error, clearError }">
      你可以在这里本地显示错误：{{ error }}
      <button @click="clearError">
        这将清除错误。
      </button>
    </template>
  </NuxtErrorBoundary>
</template>
```

## 12.服务器

Nuxt的服务器是Nitro

功能：

* 完全控制应用的服务器端部分

* 在任何提供商上进行通用部署

* 混合渲染

Nitro内部使用h3，是一个为高性能和可移植性构建的极简HTTP框架。

### 服务器端点和中间件

可以轻松管理Nuxt应用的服务器端部分，从API端点到中间件。

端点和中间件都可以这样定义：

`server/api/test.ts`

```
export default defineEventHandler(async (event) => {
  // ... 在这里做任何你想做的事情
})
```

可以直接返回`test`、`json`、`html`、`stream`

开箱即用，支持热模块替换和自动导入，就像Nuxt应用的其他部分一样。

### 混合渲染

Nitro有一个强大的功能称为`routeRules`，允许定义一组规则来定制Nuxt应用中每个路由的渲染方式。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  routeRules: {
    // 为 SEO 目的在构建时生成
    '/': { prerender: true },
    // 缓存 1 小时
    '/api/*': { cache: { maxAge: 60 * 60 } },
    // 重定向以避免 404
    '/old-page': {
      redirect: { to: '/new-page', statusCode: 302 }
    }
    // ...
  }
})
```

## 13.层

### 使用场景

* 通过 `nuxt.config` 和 `app.config` 在项目之间共享可重用的配置预设

* 使用 `components/` 目录创建组件库

* 使用 `composables/` 和 `utils/` 目录创建工具和组合式函数库

* 创建 Nuxt 模块预设

* 在项目之间共享标准设置

* 创建 Nuxt 主题

* 通过实现模块化架构和支持大型项目中的领域驱动设计（DDD）模式来增强代码组织。

### 使用方法

默认情况下，项目中 \~\~/layers 目录下的任何层都会自动注册为项目的层。

此外，每个层的 srcDir 都会自动创建命名的层别名。例如，可以通过 `#layers/test` 访问 `~~/layers/test` 层。

此外，可以通过在 `nuxt.config` 文件中添加 `extends` 属性来扩展一个层。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  extends: [
    '../base',                     // 从本地层扩展
    '@my-themes/awesome',          // 从已安装的 npm 包扩展
    'github:my-themes/awesome#v1', // 从 git 仓库扩展
  ]
})
```

如果从私有 GitHub 仓库扩展，还可以传递一个认证令牌：

`nuxt.config.ts`

```
export default defineNuxtConfig({
  extends: [
    // 每层配置
    ['github:my-themes/private-awesome', { auth: process.env.GITHUB_TOKEN }]
  ]
})
```

可以通过在层源旁边的选项中指定别名来覆盖层的别名。

`nuxt.config.ts`

```
export default defineNuxtConfig({
  extends: [
    [
      'github:my-themes/awesome',
      { 
        meta: {
          name: 'my-awesome-theme',
        },
      },
    ],
  ]
})
```

Nuxt 使用 `unjs/c12` 和 `unjs/giget` 来扩展远程层。查看文档以获取更多信息和所有可用选项

## 14.预渲染

* Nuxt 支持通用渲染，客户端渲染和混合渲染等多种渲染模式。

* 通用渲染在服务器端运行 JavaScript 代码，返回完全渲染的 HTML 页面，提供更好的用户体验和搜索引擎优化。

* 客户端渲染在浏览器中生成 HTML 元素，适用于高度交互的 Web 应用，但可能影响性能和搜索引擎优化。

* 混合渲染允许通过路由规则为不同路由设置不同的缓存规则，改变渲染模式或基于路由分配缓存策略。

* 边缘渲染通过 CDN 边缘服务器在更靠近用户的位置渲染 Nuxt 应用，减少延迟并加快页面加载。

* 通用渲染的优点包括性能提升和搜索引擎优化，缺点是开发限制和成本较高。

* 客户端渲染的优点是开发速度快和成本较低，缺点是性能较差和不利于搜索引擎优化。

## 15.部署

Nuxt 应用可以部署在 Node.js 服务器上、预渲染为静态托管，或部署到无服务器或边缘（CDN）环境。

### Node.js 服务器

* 默认输出格式，如果未指定或未自动检测

* 仅加载渲染请求所需的最小块，以优化冷启动时间

* 适用于将 Nuxt 应用部署到任何 Node.js 托管

#### 入口点

当使用 Node 服务器预设运行 `nuxt build` 时，结果将是一个启动即用的 Node 服务器入口点。

Terminal

```
node .output/server/index.mjs
```

这将启动生产 Nuxt 服务器，默认监听 3000 端口。

它支持以下运行时环境变量：

* `NITRO_PORT` 或 `PORT`（默认为 `3000`）

* `NITRO_HOST` 或 `HOST`（默认为 `'0.0.0.0'`）

* `NITRO_SSL_CERT` 和 `NITRO_SSL_KEY` - 如果两者都存在，将以 HTTPS 模式启动服务器。在绝大多数情况下，除非用于测试，否则不应使用此选项，Nitro 服务器应运行在终止 SSL 的反向代理（如 nginx 或 Cloudflare）后面。

#### PM2

PM2（进程管理器 2）是一个快速且简单的解决方案，用于在你的服务器或虚拟机上托管 Nuxt 应用。

要使用 `pm2`，请使用 `ecosystem.config.cjs`：

`ecosystem.config.cjs`

```
module.exports = {
  apps: [
    {
      name: 'NuxtAppName',
      port: '3000',
      exec_mode: 'cluster',
      instances: 'max',
      script: './.output/server/index.mjs'
    }
  ]
}
```

#### 集群模式

可以使用 `NITRO_PRESET=node_cluster` 以利用 Node.js cluster 模块实现多进程性能。

默认情况下，工作负载将通过轮询策略分配给工作进程。

## 16.测试

Nuxt通过`@nuxt/test-utils`为应用提供一流的端到端和单元测试支持，该测试工具库目前支撑着Nuxt自身的测试以及整个模块生态系统的测试。

### 安装

为了便于管理其他测试依赖，`@nuxt/test-utils`提供了多种可选的对等依赖项。

* 可以选择`happy-dom`或`jsdom`作为Nuxt运行时环境

* 可以选择`vitest`、`cucumber`、`jest`或`playwright`作为端到端测试运行器

* 只有需要使用内置浏览器测试工具时（且未使用`@playwright/test`作为测试运行器）才需要`playwright-core`

`npm`

```
npm i --save-dev @nuxt/test-utils vitest @vue/test-utils happy-dom playwright-core
```

### 单元测试

目前仅支持`vitest`

#### 设置

1.在`nuxt.config`文件中添加`@nuxt/test-utils/module`（可选）。它会为Nuxt DevTools添加Vitest集成，支持在开发中运行单元测试。

```
export default defineNuxtConfig({
  modules: [
    '@nuxt/test-utils/module'
  ]
})
```

2.创建包含以下内容的`vitest.config.ts`：

```
import { defineVitestConfig } from '@nuxt/test-utils/config'

export default defineVitestConfig({
  // 任何您需要的自定义Vitest配置
})
```

#### 使用Nuxt运行时环境

默认情况下，`@nuxt/test-utils`不会改变默认的Vitest环境。

可以通过在测试文件名中添加.`nuxt.`（例如`my-file.nuxt.test.ts`或`my-file.nuxt.spec.ts`），或在测试文件中直接添加`@vitest-environment nuxt`注释来选择加入Nuxt环境。

```
// @vitest-environment nuxt
import { test } from 'vitest'

test('我的测试', () => {
  // ... 在Nuxt环境中测试！
})
```

或者，可以在Vitest配置中设置`environment: 'nuxt'`，为所有测试启用Nuxt环境。

```
// vitest.config.ts
import { fileURLToPath } from 'node:url'
import { defineVitestConfig } from '@nuxt/test-utils/config'

export default defineVitestConfig({
  test: {
    environment: 'nuxt',
    // 可选设置Nuxt特定的环境选项
    // environmentOptions: {
    //   nuxt: {
    //     rootDir: fileURLToPath(new URL('./playground', import.meta.url)),
    //     domEnvironment: 'happy-dom', // 'happy-dom'（默认）或'jsdom'
    //     overrides: {
    //       // 您想传递的其他Nuxt配置
    //     }
    //   }
    // }
  }
})
```

如果默认设置了`environment: 'nuxt'`，可以在每个测试文件中根据需要选择\_退出\_默认环境。

```
// @vitest-environment node
import { test } from 'vitest'

test('我的测试', () => {
  // ... 不在Nuxt环境中测试！
})
```

