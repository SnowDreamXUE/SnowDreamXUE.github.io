---
title: Vue.js 粒子连线动画组件 - FlyingLines
date: 2025-06-21 14:00:00
tags: 
  - Vue
  - JavaScript
---
# Vue.js 粒子连线动画组件 - FlyingLines 使用指南

## 🌟 简介

FlyingLines 是一个基于 Vue.js 的炫酷粒子连线动画组件，可以为您的网站添加动态的背景效果。该组件具有以下特点：

- ✨ **流畅动画**：基于 Canvas 的高性能渲染
- 🖱️ **交互效果**：鼠标跟随和粒子吸引
- 📱 **响应式设计**：支持移动端触摸交互
- 🎨 **高度可定制**：丰富的参数配置选项
- 🔧 **开箱即用**：简单的组件调用方式

## 📦 完整源码

### FlyingLines.vue 组件文件

```vue name=FlyingLines.vue
<template>
  <canvas ref="canvas" class="flying-lines-canvas"></canvas>
</template>

<script>
export default {
  name: 'FlyingLines',
  data() {
    return {
      // Canvas相关
      canvas: null,           // Canvas DOM元素
      ctx: null,              // Canvas 2D渲染上下文

      // 动画数据
      particles: [],          // 粒子数组
      mousePosition: {x: 0, y: 0},  // 鼠标位置
      animationId: null,      // 动画帧ID，用于取消动画

      // ==================== 可自定义参数 ====================
      // 粒子数量 (建议范围: 50-300)
      totalParticles: 120,

      // 粒子连接距离 (像素, 建议范围: 80-200)
      connectDistance: 150,

      // 粒子基础移动速度 (建议范围: 0.1-1.0)
      speed: 0.4,

      // 粒子大小范围 (建议范围: 0.5-3.0)
      particleMinSize: 0.5,   // 最小半径
      particleMaxSize: 2.0,   // 最大半径

      // 鼠标吸引力相关
      mouseAttractDistance: 225,  // 鼠标吸引距离 (connectDistance * 1.5)
      mouseAttractForce: 0.02,    // 鼠标吸引力强度 (建议范围: 0.01-0.05)

      // 粒子最大速度限制 (建议范围: 1.0-3.0)
      maxParticleSpeed: 1.5,

      // 颜色配置
      particleColor: {
        r: 100,     // 红色分量 (0-255)
        g: 118,     // 绿色分量 (0-255)
        b: 220,     // 蓝色分量 (0-255)
        minAlpha: 0.4,  // 最小透明度 (0-1)
        maxAlpha: 0.8   // 最大透明度 (0-1)
      },

      // 连接线颜色配置
      connectionLineColor: {
        r: 80,      // 红色分量 (0-255)
        g: 98,      // 绿色分量 (0-255)
        b: 200,     // 蓝色分量 (0-255)
        maxAlpha: 0.8,  // 最大透明度 (0-1)
        width: 1.2      // 线条宽度 (建议范围: 0.5-2.0)
      },

      // 鼠标连接线颜色配置
      mouseLineColor: {
        r: 220,     // 红色分量 (0-255)
        g: 120,     // 绿色分量 (0-255)
        b: 150,     // 蓝色分量 (0-255)
        maxAlpha: 0.85, // 最大透明度 (0-1)
        width: 1.5      // 线条宽度 (建议范围: 0.5-3.0)
      }
    };
  },

  mounted() {
    this.initCanvas();
    this.createParticles();
    this.addEventListeners();
    this.animate();
  },

  beforeUnmount() {
    this.cleanup();
  },

  methods: {
    /**
     * 初始化Canvas
     * 设置Canvas的2D渲染上下文并调整尺寸
     */
    initCanvas() {
      this.canvas = this.$refs.canvas;
      this.ctx = this.canvas.getContext('2d');

      this.resizeCanvas();
      // 监听窗口大小变化事件
      window.addEventListener('resize', this.resizeCanvas);
    },

    /**
     * 调整Canvas尺寸以适应窗口
     * 当窗口大小改变时自动调用
     */
    resizeCanvas() {
      this.canvas.width = window.innerWidth;
      this.canvas.height = window.innerHeight;
    },

    /**
     * 创建粒子数组
     * 根据totalParticles参数生成指定数量的粒子
     */
    createParticles() {
      this.particles = [];

      for (let i = 0; i < this.totalParticles; i++) {
        // 创建单个粒子对象
        this.particles.push({
          // 随机初始位置
          x: Math.random() * this.canvas.width,
          y: Math.random() * this.canvas.height,

          // 随机粒子大小 (在设定范围内)
          radius: Math.random() * (this.particleMaxSize - this.particleMinSize) + this.particleMinSize,

          // 随机初始速度 (在-speed/2 到 speed/2之间)
          vx: Math.random() * this.speed - this.speed / 2,
          vy: Math.random() * this.speed - this.speed / 2,

          // 随机颜色透明度
          color: `rgba(${this.particleColor.r}, ${this.particleColor.g}, ${this.particleColor.b}, ${
              Math.random() * (this.particleColor.maxAlpha - this.particleColor.minAlpha) + this.particleColor.minAlpha
          })`
        });
      }
    },

    /**
     * 添加事件监听器
     * 监听鼠标和触摸移动事件
     */
    addEventListeners() {
      window.addEventListener('mousemove', this.onMouseMove);
      window.addEventListener('touchmove', this.onTouchMove);
    },

    /**
     * 鼠标移动事件处理
     * @param {MouseEvent} e - 鼠标事件对象
     */
    onMouseMove(e) {
      this.mousePosition.x = e.clientX;
      this.mousePosition.y = e.clientY;
    },

    /**
     * 触摸移动事件处理 (移动端支持)
     * @param {TouchEvent} e - 触摸事件对象
     */
    onTouchMove(e) {
      if (e.touches.length > 0) {
        this.mousePosition.x = e.touches[0].clientX;
        this.mousePosition.y = e.touches[0].clientY;
      }
    },

    /**
     * 主动画循环
     * 使用requestAnimationFrame实现流畅动画
     */
    animate() {
      this.animationId = requestAnimationFrame(this.animate);

      // 清空画布
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);

      // 更新粒子位置和状态
      this.updateParticles();

      // 绘制粒子
      this.drawParticles();

      // 绘制粒子间的连接线
      this.connectParticles();
    },

    /**
     * 更新所有粒子的位置和状态
     * 包括移动、边界检测、鼠标吸引等
     */
    updateParticles() {
      for (let i = 0; i < this.particles.length; i++) {
        const p = this.particles[i];

        // 1. 基础移动 - 根据粒子的速度向量移动
        p.x += p.vx;
        p.y += p.vy;

        // 2. 边界检测和反弹
        // 当粒子碰到画布边缘时反向移动
        if (p.x < 0 || p.x > this.canvas.width) p.vx = -p.vx;
        if (p.y < 0 || p.y > this.canvas.height) p.vy = -p.vy;

        // 3. 鼠标吸引效果
        // 计算粒子到鼠标的距离
        const dx = this.mousePosition.x - p.x;
        const dy = this.mousePosition.y - p.y;
        const distance = Math.sqrt(dx * dx + dy * dy);

        // 如果在吸引范围内，施加吸引力
        if (distance < this.mouseAttractDistance) {
          // 计算归一化的方向向量
          const forceDirectionX = dx / distance;
          const forceDirectionY = dy / distance;

          // 计算吸引力强度 (距离越近力越大)
          const force = (this.mouseAttractDistance - distance) / this.mouseAttractDistance;

          // 应用吸引力到粒子速度
          p.vx += forceDirectionX * force * this.mouseAttractForce;
          p.vy += forceDirectionY * force * this.mouseAttractForce;
        }

        // 4. 速度限制
        // 防止粒子移动过快，保持动画稳定
        const speed = Math.sqrt(p.vx * p.vx + p.vy * p.vy);
        if (speed > this.maxParticleSpeed) {
          p.vx = (p.vx / speed) * this.maxParticleSpeed;
          p.vy = (p.vy / speed) * this.maxParticleSpeed;
        }
      }
    },

    /**
     * 绘制所有粒子
     * 在画布上渲染每个粒子为小圆点
     */
    drawParticles() {
      for (let i = 0; i < this.particles.length; i++) {
        const p = this.particles[i];

        // 绘制圆形粒子
        this.ctx.beginPath();
        this.ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
        this.ctx.fillStyle = p.color;
        this.ctx.fill();
      }
    },

    /**
     * 绘制粒子间的连接线
     * 包括粒子之间的连线和粒子到鼠标的连线
     */
    connectParticles() {
      // 1. 绘制粒子间的连接线
      for (let i = 0; i < this.particles.length; i++) {
        for (let j = i + 1; j < this.particles.length; j++) {
          const p1 = this.particles[i];
          const p2 = this.particles[j];

          // 计算两粒子间距离
          const dx = p1.x - p2.x;
          const dy = p1.y - p2.y;
          const distance = Math.sqrt(dx * dx + dy * dy);

          // 如果距离小于连接距离，绘制连线
          if (distance < this.connectDistance) {
            // 根据距离计算透明度 (距离越近越不透明)
            const opacity = (1 - distance / this.connectDistance) * this.connectionLineColor.maxAlpha;

            // 设置线条样式
            this.ctx.strokeStyle = `rgba(${this.connectionLineColor.r}, ${this.connectionLineColor.g}, ${this.connectionLineColor.b}, ${opacity})`;
            this.ctx.lineWidth = this.connectionLineColor.width;

            // 绘制连线
            this.ctx.beginPath();
            this.ctx.moveTo(p1.x, p1.y);
            this.ctx.lineTo(p2.x, p2.y);
            this.ctx.stroke();
          }
        }

        // 2. 绘制粒子到鼠标的连线
        const p = this.particles[i];
        const dx = p.x - this.mousePosition.x;
        const dy = p.y - this.mousePosition.y;
        const distance = Math.sqrt(dx * dx + dy * dy);

        // 如果在鼠标吸引范围内，绘制连线
        if (distance < this.mouseAttractDistance) {
          // 根据距离计算透明度
          const opacity = (1 - distance / this.mouseAttractDistance) * this.mouseLineColor.maxAlpha;

          // 设置鼠标连线样式 (通常颜色不同，更显眼)
          this.ctx.strokeStyle = `rgba(${this.mouseLineColor.r}, ${this.mouseLineColor.g}, ${this.mouseLineColor.b}, ${opacity})`;
          this.ctx.lineWidth = this.mouseLineColor.width;

          // 绘制到鼠标的连线
          this.ctx.beginPath();
          this.ctx.moveTo(p.x, p.y);
          this.ctx.lineTo(this.mousePosition.x, this.mousePosition.y);
          this.ctx.stroke();
        }
      }
    },

    /**
     * 清理资源
     * 移除事件监听器并取消动画，防止内存泄漏
     */
    cleanup() {
      // 移除所有事件监听器
      window.removeEventListener('mousemove', this.onMouseMove);
      window.removeEventListener('touchmove', this.onTouchMove);
      window.removeEventListener('resize', this.resizeCanvas);

      // 取消动画帧
      cancelAnimationFrame(this.animationId);
    }
  }
}
</script>

<style scoped>
/*
  Canvas样式设置
  - position: fixed - 固定定位，覆盖整个屏幕
  - z-index: 1 - 在背景之上，但不会遮挡其他内容
  - pointer-events: none - 不响应鼠标事件，允许点击穿透
*/
.flying-lines-canvas {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  pointer-events: none;
}
</style>
```

## 📦 安装和基础使用

### 1. 组件文件结构

首先，将组件文件保存为 `FlyingLines.vue`：

```
src/
  components/
    FlyingLines.vue
```

### 2. 基础调用方式

在您的 Vue 组件中引入和使用：

```vue
<template>
  <div class="app">
    <!-- 粒子动画背景 -->
    <FlyingLines />
    
    <!-- 您的页面内容 -->
    <div class="content">
      <h1>欢迎来到我的网站</h1>
      <p>这里是页面内容，粒子动画在背景中运行</p>
    </div>
  </div>
</template>

<script>
import FlyingLines from '@/components/FlyingLines.vue'

export default {
  name: 'App',
  components: {
    FlyingLines
  }
}
</script>

<style>
.app {
  position: relative;
  min-height: 100vh;
}

.content {
  position: relative;
  z-index: 10; /* 确保内容在粒子动画之上 */
  padding: 50px;
  text-align: center;
}
</style>
```

### 3. 在路由页面中使用

```vue
<template>
  <div class="home-page">
    <FlyingLines />
    <div class="page-content">
      <!-- 页面内容 -->
    </div>
  </div>
</template>

<script>
import FlyingLines from '@/components/FlyingLines.vue'

export default {
  name: 'HomePage',
  components: {
    FlyingLines
  }
}
</script>
```

## 🎨 自定义配置

### 基础参数调整

您可以通过修改组件内的 `data()` 方法来自定义动画效果：

```javascript
// 在 FlyingLines.vue 的 data() 中修改以下参数

// 粒子相关
totalParticles: 80,        // 减少粒子数量（性能优化）
speed: 0.6,                // 增加移动速度
connectDistance: 120,      // 减少连接距离

// 颜色配置
particleColor: {
  r: 255,     // 改为红色粒子
  g: 100,
  b: 100,
  minAlpha: 0.3,
  maxAlpha: 0.7
},

connectionLineColor: {
  r: 255,     // 红色连线
  g: 150,
  b: 150,
  maxAlpha: 0.6,
  width: 1.0
}
```

### 预设主题配置

#### 🌙 深夜主题
```javascript
// 深蓝色主题，适合深色背景
particleColor: {
  r: 100, g: 150, b: 255,
  minAlpha: 0.4, maxAlpha: 0.8
},
connectionLineColor: {
  r: 80, g: 130, b: 255,
  maxAlpha: 0.7, width: 1.2
},
mouseLineColor: {
  r: 150, g: 200, b: 255,
  maxAlpha: 0.9, width: 1.8
}
```

#### 🌸 温暖主题
```javascript
// 暖色调主题
particleColor: {
  r: 255, g: 180, b: 120,
  minAlpha: 0.5, maxAlpha: 0.8
},
connectionLineColor: {
  r: 255, g: 150, b: 100,
  maxAlpha: 0.6, width: 1.0
},
mouseLineColor: {
  r: 255, g: 120, b: 80,
  maxAlpha: 0.8, width: 1.5
}
```

#### 🌿 清新主题
```javascript
// 绿色清新主题
particleColor: {
  r: 100, g: 255, b: 150,
  minAlpha: 0.4, maxAlpha: 0.7
},
connectionLineColor: {
  r: 80, g: 200, b: 120,
  maxAlpha: 0.6, width: 1.1
},
mouseLineColor: {
  r: 120, g: 255, b: 180,
  maxAlpha: 0.8, width: 1.6
}
```

## 🔧 高级配置

### 性能优化配置

针对不同设备进行性能优化：

```javascript
// 移动端优化配置
totalParticles: 60,          // 减少粒子数量
connectDistance: 100,        // 减少连接距离
speed: 0.3,                  // 降低速度
maxParticleSpeed: 1.0,       // 限制最大速度

// 高性能设备配置
totalParticles: 200,         // 更多粒子
connectDistance: 180,        // 更大连接范围
speed: 0.5,
maxParticleSpeed: 2.0
```

### 交互效果调整

```javascript
// 强交互效果
mouseAttractDistance: 300,   // 更大的鼠标影响范围
mouseAttractForce: 0.04,     // 更强的吸引力

// 轻微交互效果
mouseAttractDistance: 150,   // 较小的影响范围
mouseAttractForce: 0.015,    // 较弱的吸引力
```

## 📱 响应式适配

### 根据设备类型自动调整

您可以在组件的 `mounted()` 钩子中添加设备检测：

```javascript
mounted() {
  // 检测设备类型并调整参数
  if (this.isMobileDevice()) {
    this.totalParticles = 60;
    this.connectDistance = 100;
    this.speed = 0.3;
  }
  
  this.initCanvas();
  this.createParticles();
  this.addEventListeners();
  this.animate();
},

methods: {
  isMobileDevice() {
    return window.innerWidth < 768 || /Android|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);
  },
  // ... 其他方法
}
```

## 🎯 使用场景

### 1. 首页背景
```vue
<!-- 网站首页 -->
<template>
  <div class="landing-page">
    <FlyingLines />
    <header class="hero-section">
      <h1>欢迎来到未来</h1>
      <p>科技感十足的粒子动画背景</p>
    </header>
  </div>
</template>
```

### 2. 登录页面
```vue
<!-- 登录页面 -->
<template>
  <div class="login-page">
    <FlyingLines />
    <div class="login-form">
      <form>
        <!-- 登录表单 -->
      </form>
    </div>
  </div>
</template>

<style>
.login-form {
  position: relative;
  z-index: 10;
  background: rgba(255, 255, 255, 0.9);
  padding: 40px;
  border-radius: 10px;
  backdrop-filter: blur(10px);
}
</style>
```

### 3. 作品展示页
```vue
<!-- 作品集页面 -->
<template>
  <div class="portfolio">
    <FlyingLines />
    <div class="portfolio-grid">
      <!-- 作品展示内容 -->
    </div>
  </div>
</template>
```

## ⚡ 性能优化建议

### 1. 粒子数量控制
- **桌面端**: 100-200 个粒子
- **移动端**: 50-80 个粒子
- **低端设备**: 30-50 个粒子

### 2. 连接距离优化
- 连接距离过大会导致大量线条绘制，影响性能
- 建议范围：80-150px

### 3. 动画帧率控制
如果需要进一步优化，可以添加帧率控制：

```javascript
animate() {
  // 限制为30FPS以节省性能
  setTimeout(() => {
    this.animationId = requestAnimationFrame(this.animate);
  }, 1000 / 30);
  
  // ... 动画逻辑
}
```

## 🎨 样式自定义

### 背景模糊效果
```css
.content {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
}
```

### 渐变遮罩
```css
.flying-lines-canvas::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(45deg, 
    rgba(0,0,0,0.1) 0%, 
    transparent 50%, 
    rgba(255,255,255,0.1) 100%);
  pointer-events: none;
}
```

## 🐛 常见问题

### Q: 粒子动画在某些浏览器中不显示？
A: 确保浏览器支持 Canvas API，对于老旧浏览器可以添加 polyfill。

### Q: 移动端性能较差怎么办？
A: 减少粒子数量，降低连接距离，或者在移动端禁用动画。

### Q: 如何在组件销毁时确保资源清理？
A: 组件已经在 `beforeUnmount` 钩子中处理了资源清理，无需额外操作。

### Q: 能否动态修改动画参数？
A: 可以通过 Vue 的响应式数据来动态修改参数，修改后调用 `createParticles()` 重新生成粒子。
