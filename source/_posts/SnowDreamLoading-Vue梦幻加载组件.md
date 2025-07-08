---
title: SnowDream Loading - Vue 梦幻加载组件
date: 2025-07-08 16:16:49
tags: 
    - Vue
    - JavaScript
---
# SnowDream Loading - Vue 梦幻加载组件

一个功能丰富、视觉效果出色的 Vue.js 全局页面加载组件，如雪花般轻盈美丽，如梦境般炫彩动人。支持路由切换加载、图片预加载、自定义配置等功能。

## ✨ 特性

- 🎨 **炫酷动效**：渐变背景、浮动装饰、旋转圆环、跳跃文字
- 🛣️ **路由集成**：自动在路由切换时显示/隐藏加载动画
- 🖼️ **智能预加载**：等待页面图片资源加载完成
- ⚙️ **灵活配置**：支持自定义延迟时间、超时时间、排除路由等
- 📱 **响应式设计**：适配移动端设备
- 🎯 **单例模式**：全局统一的加载状态管理

## 📁 项目结构

```
src/
├── components/
│   └── PageLoading.vue     # 加载组件
└── utils/
    └── loading.js          # 加载工具类

```

## 🚀 快速开始

### 第一步：创建组件文件

### 1. 创建 `src/components/PageLoading.vue`

**👇 在此处放置 PageLoading.vue 的完整代码**

```vue
<template>
  <transition name="loading-fade">
    <div v-if="visible" class="page-loading-overlay">
      <!-- 背景装饰 -->
      <div class="bg-decoration">
        <div class="floating-star" v-for="n in 12" :key="'star-' + n" :style="getStarStyle(n)">
          ✦
        </div>
        <div class="floating-heart" v-for="n in 6" :key="'heart-' + n" :style="getHeartStyle(n)">
          ♡
        </div>
      </div>

      <div class="loading-content">
        <!-- 圆圈动画 -->
        <div class="circle-animation">
          <div class="rotating-circle outer-ring"></div>
          <div class="rotating-circle middle-ring"></div>
          <div class="rotating-circle inner-ring"></div>
        </div>

        <!-- Loading文字 -->
        <div class="loading-text">
          <div class="text-container">
            <span class="text-letter" v-for="(letter, index) in 'Loading'" :key="'letter-' + index" :style="{ animationDelay: index * 0.1 + 's' }">
              {{ letter }}
            </span>
          </div>
          <div class="loading-dots">
            <span class="dot" v-for="n in 3" :key="'dot-' + n" :style="{ animationDelay: n * 0.2 + 's' }">
              ●
            </span>
          </div>
        </div>
      </div>

      <!-- 进度条 -->
      <div class="progress-bar">
        <div class="progress-fill" :style="{ width: progress + '%' }">
          <div class="progress-sparkle"></div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'PageLoading',
  data() {
    return {
      visible: false,
      progress: 0,
      progressTimer: null
    }
  },
  methods: {
    show() {
      this.visible = true
      this.progress = 0
      this.startProgress()
    },

    hide() {
      this.progress = 100
      setTimeout(() => {
        this.visible = false
        this.stopProgress()
      }, 300)
    },

    startProgress() {
      this.stopProgress()
      this.progress = 0

      this.progressTimer = setInterval(() => {
        if (this.progress < 90) {
          this.progress += Math.random() * 8
        }
      }, 120)
    },

    stopProgress() {
      if (this.progressTimer) {
        clearInterval(this.progressTimer)
        this.progressTimer = null
      }
    },

    getStarStyle(index) {
      const positions = [
        { top: '10%', left: '15%' }, { top: '20%', right: '10%' },
        { top: '30%', left: '8%' }, { top: '15%', right: '20%' },
        { bottom: '25%', left: '12%' }, { bottom: '15%', right: '15%' },
        { top: '45%', left: '5%' }, { top: '55%', right: '8%' },
        { bottom: '35%', left: '20%' }, { bottom: '45%', right: '25%' },
        { top: '65%', left: '10%' }, { top: '75%', right: '12%' }
      ]

      return {
        ...positions[index - 1],
        animationDelay: (index * 0.3) + 's',
        fontSize: (12 + Math.random() * 8) + 'px'
      }
    },

    getHeartStyle(index) {
      const positions = [
        { top: '25%', left: '25%' }, { top: '35%', right: '30%' },
        { bottom: '30%', left: '30%' }, { bottom: '40%', right: '35%' },
        { top: '60%', left: '25%' }, { top: '70%', right: '30%' }
      ]

      return {
        ...positions[index - 1],
        animationDelay: (index * 0.5) + 's'
      }
    }
  },

  beforeDestroy() {
    this.stopProgress()
  }
}
</script>

<style scoped>
.page-loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(135deg, #ff9a9e 0%, #a855f7 25%, #8b5cf6 50%, #3b82f6 75%, #06b6d4 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  z-index: 9999;
  overflow: hidden;
}

.bg-decoration {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
}

.floating-star {
  position: absolute;
  color: rgba(255, 255, 255, 0.8);
  animation: twinkle 2s ease-in-out infinite;
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.8);
}

.floating-heart {
  position: absolute;
  color: rgba(255, 255, 255, 0.6);
  font-size: 16px;
  animation: float 3s ease-in-out infinite;
}

@keyframes twinkle {
  0%, 100% { opacity: 0.3; transform: scale(0.8); }
  50% { opacity: 1; transform: scale(1.2); }
}

@keyframes float {
  0%, 100% { transform: translateY(0px) rotate(0deg); }
  50% { transform: translateY(-20px) rotate(10deg); }
}

.loading-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  z-index: 10;
}

.loading-text {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.text-container {
  display: flex;
  gap: 0.1rem;
}

.text-letter {
  display: inline-block;
  font-size: 2.5rem;
  font-weight: bold;
  color: white;
  text-shadow: 0 0 15px rgba(255, 255, 255, 0.9), 0 0 30px rgba(168, 85, 247, 0.8), 0 0 45px rgba(59, 130, 246, 0.6);
  animation: letterBounce 1.5s ease-in-out infinite;
  font-family: 'Comic Sans MS', cursive, sans-serif;
}

.loading-dots {
  display: flex;
  gap: 0.3rem;
}

.loading-dots .dot {
  color: white;
  font-size: 1.2rem;
  animation: dotPulse 1.2s ease-in-out infinite;
  text-shadow: 0 0 8px rgba(255, 255, 255, 0.8);
}

@keyframes letterBounce {
  0%, 100% { transform: translateY(0px) scale(1); }
  50% { transform: translateY(-8px) scale(1.1); }
}

@keyframes dotPulse {
  0%, 100% { opacity: 0.4; transform: scale(0.8); }
  50% { opacity: 1; transform: scale(1.2); }
}

.circle-animation {
  position: relative;
  width: 120px;
  height: 120px;
}

.rotating-circle {
  position: absolute;
  border-radius: 50%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-style: solid;
}

.outer-ring {
  width: 120px;
  height: 120px;
  border-width: 4px;
  border-color: rgba(255, 255, 255, 0.3) transparent rgba(255, 255, 255, 0.3) transparent;
  animation: rotateClockwise 2s linear infinite;
}

.middle-ring {
  width: 80px;
  height: 80px;
  border-width: 3px;
  border-color: transparent rgba(255, 255, 255, 0.6) transparent rgba(255, 255, 255, 0.6);
  animation: rotateCounterClockwise 1.5s linear infinite;
}

.inner-ring {
  width: 40px;
  height: 40px;
  border-width: 2px;
  border-color: rgba(255, 255, 255, 0.8) transparent rgba(255, 255, 255, 0.8) transparent;
  animation: rotateClockwise 1s linear infinite;
}

.progress-bar {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 6px;
  background: rgba(255, 255, 255, 0.2);
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #ff9a9e, #a855f7, #8b5cf6, #3b82f6, #06b6d4);
  transition: width 0.3s ease;
  position: relative;
  box-shadow: 0 0 15px rgba(168, 85, 247, 0.6);
}

@keyframes rotateClockwise {
  to {
    transform: translate(-50%, -50%) rotate(360deg);
  }
}

@keyframes rotateCounterClockwise {
  to {
    transform: translate(-50%, -50%) rotate(-360deg);
  }
}

.loading-fade-enter-active,
.loading-fade-leave-active {
  transition: all 0.5s ease;
}

.loading-fade-enter {
  opacity: 0;
  transform: scale(0.9);
}

.loading-fade-leave-to {
  opacity: 0;
  transform: scale(1.1);
}

@media (max-width: 768px) {
  .text-letter {
    font-size: 2rem;
  }

  .circle-animation {
    width: 100px;
    height: 100px;
  }

  .outer-ring {
    width: 100px;
    height: 100px;
  }
}
</style>

```

### 2. 创建 `src/utils/loading.js`

**👇 在此处放置 loading.js 的完整代码**

```jsx
import Vue from "vue"
import PageLoading from "@/components/PageLoading.vue"

class PageLoadingUtils {
    constructor() {
        this.loadingInstance = null
        this.imageLoadPromises = []
        this.isFirstLoad = true
        this.excludeRoutes = []
    }

    // 初始化加载组件
    init() {
        // 全局注册组件
        Vue.component("PageLoading", PageLoading)

        // 创建全局加载实例
        const LoadingConstructor = Vue.extend(PageLoading)
        this.loadingInstance = new LoadingConstructor()
        this.loadingInstance.$mount()
        document.body.appendChild(this.loadingInstance.$el)

        // 添加到Vue原型
        Vue.prototype.$pageLoading = this.loadingInstance

        return this.loadingInstance
    }

    // 设置路由守卫
    setupRouter(router, options = {}) {
        const defaultOptions = {
            routeChangeDelay: 300,
            waitForImages: true,
            maxWaitTime: 8000,
            excludeRoutes: [], // 排除的路由
        }
        const config = { ...defaultOptions, ...options }
        this.excludeRoutes = config.excludeRoutes

        router.beforeEach((to, from, next) => {
            // 检查是否需要排除当前路由
            const shouldExclude = this.shouldExcludeRoute(to.path)

            if (to.path !== from.path && !shouldExclude) {
                console.log(`路由切换: ${from.path} -> ${to.path}, 显示加载动画`)
                this.loadingInstance.show()
            }
            next()
        })

        router.afterEach((to, from) => {
            // 同样检查排除路由
            const shouldExclude = this.shouldExcludeRoute(to.path)

            if (!shouldExclude) {
                if (config.waitForImages) {
                    // 等待当前页面的图片加载完成
                    this.waitForPageImages().then(() => {
                        Vue.nextTick(() => {
                            setTimeout(() => {
                                console.log(`路由 ${to.path} 资源加载完成，隐藏加载动画`)
                                this.loadingInstance.hide()
                            }, config.routeChangeDelay)
                        })
                    })
                } else {
                    Vue.nextTick(() => {
                        setTimeout(() => {
                            console.log(`路由 ${to.path} 切换完成，隐藏加载动画`)
                            this.loadingInstance.hide()
                        }, config.routeChangeDelay)
                    })
                }
            }
        })
    }

    // 检查是否应该排除路由
    shouldExcludeRoute(path) {
        return this.excludeRoutes.some((route) => {
            if (typeof route === "string") {
                return path === route || path.startsWith(route)
            }
            if (route instanceof RegExp) {
                return route.test(path)
            }
            return false
        })
    }

    // 等待页面图片加载完成
    waitForPageImages() {
        return new Promise((resolve) => {
            const images = document.querySelectorAll("img")
            const imagePromises = []

            if (images.length === 0) {
                resolve()
                return
            }

            images.forEach((img) => {
                if (img.complete && img.naturalWidth > 0) {
                    // 图片已经加载完成
                    return
                }

                const promise = new Promise((imgResolve) => {
                    const onLoad = () => {
                        img.removeEventListener("load", onLoad)
                        img.removeEventListener("error", onError)
                        imgResolve()
                    }

                    const onError = () => {
                        img.removeEventListener("load", onLoad)
                        img.removeEventListener("error", onError)
                        imgResolve() // 即使加载失败也继续
                    }

                    img.addEventListener("load", onLoad)
                    img.addEventListener("error", onError)

                    // 设置超时，避免某些图片一直不加载
                    setTimeout(() => {
                        img.removeEventListener("load", onLoad)
                        img.removeEventListener("error", onError)
                        imgResolve()
                    }, 10000) // 10秒超时
                })

                imagePromises.push(promise)
            })

            // 等待所有图片加载完成，或者3秒后强制结束
            Promise.race([
                Promise.all(imagePromises),
                new Promise((resolve) => setTimeout(resolve, 3000)), // 3秒最大等待时间
            ]).then(resolve)
        })
    }

    // 等待所有资源加载完成（包括CSS、JS、图片等）
    waitForAllResources() {
        return new Promise((resolve) => {
            // 检查document.readyState
            if (document.readyState === "complete") {
                // 再等待图片加载
                this.waitForPageImages().then(resolve)
                return
            }

            // 监听load事件
            const onLoad = () => {
                window.removeEventListener("load", onLoad)
                // 页面加载完成后，再等待图片
                this.waitForPageImages().then(resolve)
            }

            window.addEventListener("load", onLoad)

            // 设置最大等待时间
            setTimeout(() => {
                window.removeEventListener("load", onLoad)
                resolve()
            }, 15000) // 15秒最大等待时间
        })
    }

    // 处理首次加载
    handleFirstLoad(options = {}) {
        const defaultOptions = {
            firstLoadDelay: 600,
            waitForImages: true,
            maxWaitTime: 10000, // 最大等待时间
        }
        const config = { ...defaultOptions, ...options }

        const handlePageLoad = () => {
            // 应用启动时显示加载动画
            if (document.readyState === "loading") {
                this.loadingInstance.show()
                console.log("应用启动，显示加载动画")
            }

            // 页面加载完成处理
            if (config.waitForImages) {
                // 等待所有资源加载完成
                Promise.race([
                    this.waitForAllResources(),
                    new Promise((resolve) => setTimeout(resolve, config.maxWaitTime)),
                ]).then(() => {
                    setTimeout(() => {
                        console.log("首次加载完成，隐藏加载动画")
                        this.loadingInstance.hide()
                        this.isFirstLoad = false
                    }, config.firstLoadDelay)
                })
            } else {
                // 只等待DOM加载完成
                if (document.readyState !== "complete") {
                    const handleLoad = () => {
                        setTimeout(() => {
                            console.log("首次DOM加载完成，隐藏加载动画")
                            this.loadingInstance.hide()
                            this.isFirstLoad = false
                        }, config.firstLoadDelay)
                        window.removeEventListener("load", handleLoad)
                    }
                    window.addEventListener("load", handleLoad)
                } else {
                    setTimeout(() => {
                        console.log("页面已完成加载，隐藏加载动画")
                        this.loadingInstance.hide()
                        this.isFirstLoad = false
                    }, 1000)
                }
            }
        }

        return handlePageLoad
    }

    // 手动显示加载
    show() {
        if (this.loadingInstance) {
            this.loadingInstance.show()
        }
    }

    // 手动隐藏加载
    hide() {
        if (this.loadingInstance) {
            this.loadingInstance.hide()
        }
    }

    // 获取加载实例
    getInstance() {
        return this.loadingInstance
    }
}

// 创建单例实例
const pageLoadingUtils = new PageLoadingUtils()

export default pageLoadingUtils
```

### 第二步：在项目中使用

### 1. 基础使用（推荐）

在 `main.js` 中导入并初始化：

```jsx
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import pageLoadingUtils from "@/utils/loading"

Vue.config.productionTip = false

// 初始化加载组件
pageLoadingUtils.init()

// 设置路由守卫
pageLoadingUtils.setupRouter(router, {
  routeChangeDelay: 500,     // 路由切换后延迟时间(ms)
  waitForImages: true,       // 是否等待图片加载完成
  maxWaitTime: 10000,       // 最大等待时间(ms)
  excludeRoutes: ["/login", "/auth"] // 排除的路由
})

new Vue({
  router,
  store,
  render: h => h(App),
  mounted() {
    // 处理首次页面加载
    const shouldExclude = pageLoadingUtils.shouldExcludeRoute(this.$route.path)

    if (!shouldExclude) {
      const handlePageLoad = pageLoadingUtils.handleFirstLoad({
        firstLoadDelay: 800,   // 首次加载延迟时间(ms)
        waitForImages: true,   // 等待图片加载
        maxWaitTime: 15000    // 首次加载最大等待时间(ms)
      })
      handlePageLoad()
    }
  }
}).$mount('#app')

```

### 2. 特殊页面自定义加载

对于需要特殊处理的页面（如登录页），可以自行控制加载逻辑：

**👇 特殊页面使用示例（如登录页面）**

```vue
<template>
  <div class="login-page">
    <!-- 登录页面内容 -->
  </div>
</template>

<script>
export default {
  name: 'LoginPage',
  data() {
    return {
      isLogin: true,
      backgroundLoaded: false,
      minLoadingTime: 1500, // 最少显示1.5秒
      loadingStartTime: null,
    }
  },

  mounted() {
    this.initializePageLoading()
  },

  methods: {
    // 初始化页面加载
    initializePageLoading() {
      this.loadingStartTime = Date.now()

      // 显示全局加载动画
      if (this.$pageLoading) {
        this.$pageLoading.show()
        // console.log('显示加载动画，开始加载背景图片...')
      }

      // 开始预加载背景图片
      this.preloadBackgroundImage()
    },

    // 预加载背景图片
    preloadBackgroundImage() {
      const img = new Image()
      img.crossOrigin = 'anonymous'

      img.onload = () => {
        // console.log('背景图片加载完成')
        this.backgroundLoaded = true
        this.checkLoadingComplete()
      }

      img.onerror = () => {
        // console.warn('背景图片加载失败，继续显示页面')
        // 图片加载失败时，仍然设置为已加载状态，让页面正常显示
        this.backgroundLoaded = true
        this.checkLoadingComplete()
      }

      // 设置超时，避免图片一直不加载
      setTimeout(() => {
        if (!this.backgroundLoaded) {
          // console.warn('背景图片加载超时，继续显示页面')
          this.backgroundLoaded = true
          this.checkLoadingComplete()
        }
      }, 30000) // 30秒超时

      // 开始加载图片
      try {
        img.src = require('../assets/background.jpg')
      } catch (error) {
        // console.warn('背景图片路径错误，继续显示页面')
        // 路径错误时也继续显示页面
        this.backgroundLoaded = true
        this.checkLoadingComplete()
      }
    },

    // 检查加载是否完成
    checkLoadingComplete() {
      if (!this.backgroundLoaded) return

      const elapsed = Date.now() - this.loadingStartTime
      const remaining = Math.max(0, this.minLoadingTime - elapsed)

      // console.log(`背景加载完成，等待 ${remaining}ms 后隐藏加载动画`)

      // 确保最少显示时间后隐藏加载动画
      setTimeout(() => {
        this.hideGlobalLoading()
      }, remaining)
    },

    // 隐藏全局加载动画
    hideGlobalLoading() {
      if (this.$pageLoading) {
        // console.log('隐藏加载动画，显示登录页面')
        this.$pageLoading.hide()
      }
    },
  }
}
</script>

```

## ⚙️ 配置选项

### setupRouter 配置

| 参数 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| `routeChangeDelay` | `number` | `300` | 路由切换完成后的延迟时间(ms) |
| `waitForImages` | `boolean` | `true` | 是否等待页面图片加载完成 |
| `maxWaitTime` | `number` | `8000` | 最大等待时间(ms) |
| `excludeRoutes` | `Array` | `[]` | 排除的路由列表，支持字符串和正则表达式 |

### excludeRoutes 示例

```jsx
// 字符串匹配
excludeRoutes: ["/login", "/auth"]

// 正则表达式匹配
excludeRoutes: [/^\\/admin/, /\\/test$/]

// 混合匹配
excludeRoutes: ["/login", /^\\/api/, "/dashboard"]

```

### handleFirstLoad 配置

| 参数 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| `firstLoadDelay` | `number` | `600` | 首次加载完成后的延迟时间(ms) |
| `waitForImages` | `boolean` | `true` | 是否等待图片加载完成 |
| `maxWaitTime` | `number` | `10000` | 最大等待时间(ms) |

## 🎨 样式定制

### 主要样式变量

组件采用渐变背景和多层动画效果，可以通过修改 `PageLoading.vue` 中的以下部分来自定义样式：

```css
/* 主背景渐变色 */
background: linear-gradient(135deg,
  #ff9a9e 0%,   /* 粉红色 */
  #a855f7 25%,  /* 紫色 */
  #8b5cf6 50%,  /* 蓝紫色 */
  #3b82f6 75%,  /* 蓝色 */
  #06b6d4 100%  /* 青色 */
);

/* 文字颜色和阴影 */
text-shadow:
  0 0 15px rgba(255, 255, 255, 0.9),
  0 0 30px rgba(168, 85, 247, 0.8),
  0 0 45px rgba(59, 130, 246, 0.6);

/* 动画时长配置 */
.text-letter {
  animation: letterBounce 1.5s ease-in-out infinite;
}

.rotating-circle {
  animation: rotateClockwise 2s linear infinite;
}

```

### 响应式断点

```css
@media (max-width: 768px) {
  .text-letter {
    font-size: 2rem;  /* 移动端文字大小 */
  }

  .circle-animation {
    width: 100px;     /* 移动端圆环大小 */
    height: 100px;
  }
}

```

## 🔧 API 方法

### 工具类方法

```jsx
// 初始化组件
pageLoadingUtils.init()

// 设置路由守卫
pageLoadingUtils.setupRouter(router, options)

// 处理首次加载
pageLoadingUtils.handleFirstLoad(options)

// 手动显示/隐藏
pageLoadingUtils.show()
pageLoadingUtils.hide()

// 获取组件实例
const instance = pageLoadingUtils.getInstance()

// 检查路由是否被排除
const shouldExclude = pageLoadingUtils.shouldExcludeRoute('/login')

```

### 组件实例方法

```jsx
// 在 Vue 组件中使用
this.$pageLoading.show()
this.$pageLoading.hide()

// 直接操作实例
const loading = pageLoadingUtils.getInstance()
loading.show()
loading.hide()

```

### 高级用法

```jsx
// 自定义图片预加载
pageLoadingUtils.waitForPageImages().then(() => {
  console.log('页面图片加载完成')
})

// 等待所有资源加载
pageLoadingUtils.waitForAllResources().then(() => {
  console.log('所有资源加载完成')
})

```

## 📱 响应式支持

组件内置响应式设计，在不同设备上的表现：

### 桌面端（> 768px）

- 完整动画效果
- 大尺寸文字和圆环
- 流畅的渐变和阴影效果

### 移动端（≤ 768px）

- 适配小屏幕的文字大小
- 调整后的圆环尺寸
- 优化的触摸体验

## 🚨 注意事项

### 性能优化建议

1. **合理设置超时时间**：避免因资源加载过慢导致长时间显示加载动画
2. **使用排除路由**：对于不需要加载动画的页面设置排除
3. **图片优化**：确保页面图片已经过压缩优化

### 兼容性说明

- **Vue 版本**：支持 Vue 2.x
- **浏览器支持**：现代浏览器（IE 11+）
- **移动端**：支持 iOS Safari、Android Chrome

## 🐛 常见问题

### Q: 为什么某些页面的加载动画显示时间过长？

**A:** 检查以下几点：

- 页面是否有大图片或网络请求较慢的资源
- 调整 `maxWaitTime` 参数
- 考虑将该路由添加到 `excludeRoutes` 中

### Q: 如何禁用某个路由的加载动画？

**A:** 在 `setupRouter` 的 `excludeRoutes` 参数中添加该路由：

```jsx
excludeRoutes: ["/no-loading-page", /^\\/admin/]

```

### Q: 可以自定义加载动画的样式吗？

**A:** 可以，直接修改 `PageLoading.vue` 组件中的 CSS 样式，参考上面的"样式定制"部分。

### Q: 在单页应用中首次加载没有显示动画？

**A:** 确保在 `main.js` 的 `mounted` 钩子中调用了 `handleFirstLoad` 方法。

### Q: 如何调试加载流程？

**A:** 工具类已内置 console.log，可以在开发者工具中查看加载流程的日志。
