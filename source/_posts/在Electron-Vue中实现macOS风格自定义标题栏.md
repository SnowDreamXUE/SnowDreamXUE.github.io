---
title: 在Electron-Vue中实现macOS风格自定义标题栏
date: 2025-03-13 17:56:02
tags: 
  - electron
  - Vue.js
---
## 在Electron-Vue中实现macOS风格自定义标题栏

在Electron应用开发中，自定义标题栏是实现个性化界面设计的重要环节。本文将介绍如何在electron-vue项目中实现macOS风格的自定义标题栏，包含窗口控制、状态同步和样式优化等关键功能。

### 一. 核心实现步骤

#### 1. 主进程配置

在创建BrowserWindow时关闭默认框架：

```javascript
// main.js
function createWindow() {
  const mainWindow = new BrowserWindow({
    frame: false,
    titleBarStyle: "hidden",
    //其他配置
  });
    
  // 监听窗口最大化状态变化
  mainWindow.on("maximize", () => {
    mainWindow.webContents.send("window-maximized", true);
  });

  mainWindow.on("unmaximize", () => {
    mainWindow.webContents.send("window-maximized", false);
  });

  // 处理窗口操作的IPC消息
  ipcMain.on("window-minimize", () => {
    mainWindow.minimize();
  });

  ipcMain.on("window-maximize", () => {
    if (mainWindow.isMaximized()) {
      mainWindow.unmaximize();
    } else {
      mainWindow.maximize();
    }
  });

  ipcMain.on("window-close", () => {
    mainWindow.close();
  });

  ipcMain.handle("window-is-maximized", () => {
    return mainWindow.isMaximized();
  });

  //其他配置
}
```

窗口状态事件监听：

```javascript
mainWindow.on("maximize", () => {
  mainWindow.webContents.send("window-maximized", true);
});

mainWindow.on("unmaximize", () => {
  mainWindow.webContents.send("window-maximized", false);
});
```

#### 2. 预加载脚本通信

暴露安全的窗口控制API：

```javascript
// preload.js
const windowAPI = {
  minimize: () => ipcRenderer.send('window-minimize'),
  maximize: () => ipcRenderer.send('window-maximize'),
  close: () => ipcRenderer.send('window-close'),
  onMaximizeChange: (callback) => {
    ipcRenderer.on('window-maximized', (_, isMaximized) => callback(isMaximized))
  }
}

if (process.contextIsolated) {
  try {
    contextBridge.exposeInMainWorld('electron', {
      ...electronAPI,
      window: windowAPI
    })
    contextBridge.exposeInMainWorld('api', api)
  } catch (error) {
    console.error(error)
  }
} else {
  window.electron = {
    ...electronAPI,
    window: windowAPI
  }
  window.api = api
}
```

#### 3. 自定义标题栏组件

##### 模板结构

```html
<template>
  <div class="titlebar" :class="{ 'is-maximized': isMaximized }">
    <el-row class="titlebar-row">
      <el-col :span="16" class="title-col">
        <div class="window-title">{{ title }}</div>
      </el-col>
      <el-col :span="4" class="control-col">
        <div class="window-controls">
          <button @click="minimize"></button>
          <button @click="toggleMaximize"></button>
          <button @click="close"></button>
        </div>
      </el-col>
    </el-row>
  </div>
</template>
```

##### 核心逻辑

```javascript
export default {
  data() {
    return { isMaximized: false }
  },
  mounted() {
    window.electron.window.onMaximizeChange((isMax) => {
      this.isMaximized = isMax
    })
    
    this.$el.addEventListener('dblclick', this.handleDoubleClick)
  },
  methods: {
    minimize() {
      window.electron.window.minimize()
    },
    toggleMaximize() {
      window.electron.window.maximize()
    },
    handleDoubleClick(e) {
      if (!e.target.closest('.window-controls')) {
        this.toggleMaximize()
      }
    }
  }
}
```

##### 关键样式

```css
.titlebar {
  height: 30px;
  -webkit-app-region: drag; /* 可拖拽区域 */
}

.window-controls {
  -webkit-app-region: no-drag; /* 非拖拽区域 */
}

.control-button {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  transition: all 0.2s ease;
}

/* macOS风格按钮配色 */
.minimize { background-color: #ffbd2e; }
.maximize { background-color: #28c940; }
.close { background-color: #ff5f57; }
```

### 二. TitleBar完整代码

```vue
<template>
  <div
    class="titlebar"
    :class="{ 'is-maximized': isMaximized }"
    data-platform="darwin"
  >
    <el-row :gutter="0" class="titlebar-row">
      <el-col :span="4" class="left-col"></el-col>
      <el-col :span="16" class="title-col">
        <div class="window-title">{{ title }}</div>
      </el-col>
      <el-col :span="4" class="control-col">
        <div class="window-controls" @dblclick.stop>
          <!-- 按钮顺序调整为：最小化、最大化、关闭 -->
          <button class="control-button minimize" @click="minimize"></button>
          <button
            class="control-button maximize"
            :class="{ restored: isMaximized }"
            @click="toggleMaximize"
          ></button>
          <button class="control-button close" @click="close"></button>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'TitleBar',
  props: {
    title: {
      type: String,
      default: 'Electron App'
    }
  },
  data() {
    return {
      platform: window.api.platform,
      isMaximized: false
    }
  },
  mounted() {
    window.electron.window.onMaximizeChange((isMaximized) => {
      this.isMaximized = isMaximized
    })

    // 添加双击标题栏最大化/还原的功能
    this.$el.addEventListener('dblclick', this.handleDoubleClick)
  },
  beforeUnmount() {
    this.$el.removeEventListener('dblclick', this.handleDoubleClick)
  },
  methods: {
    minimize() {
      window.electron.window.minimize()
    },
    toggleMaximize() {
      window.electron.window.maximize()
    },
    close() {
      window.electron.window.close()
    },
    handleDoubleClick(event) {
      // 确保双击的是标题栏区域，而不是控制按钮
      if (!event.target.closest('.window-controls')) {
        this.toggleMaximize()
      }
    }
  }
}
</script>

<style scoped>
.titlebar {
  height: 30px;
  background-color: #f1f1f1;
  user-select: none;
  -webkit-app-region: drag;
  width: 100%;
}

.titlebar-row {
  height: 100%;
  margin: 0 !important;
}

.control-col, .title-col, .left-col {
  height: 100%;
  display: flex;
  align-items: center;
}

.title-col {
  justify-content: center;
}

.window-title {
  font-size: 14px;
  font-weight: 500;
  color: #333;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  line-height: 30px;
}

.window-controls {
  display: flex;
  align-items: center;
  justify-content: flex-end; /* 按钮靠右对齐 */
  -webkit-app-region: no-drag;
  height: 100%;
  padding-right: 12px;
}

.control-col {
  justify-content: flex-end;
}

.control-button {
  width: 16px; /* 增大按钮尺寸 */
  height: 16px;
  margin: 0 6px;
  border-radius: 50%;
  border: none;
  cursor: pointer;
  position: relative;
  transition: all 0.2s ease;
}

/* macOS 风格按钮颜色 */
.control-button.minimize {
  background-color: #ffbd2e;
}
.control-button.minimize:hover {
  background-color: #ffcd45;
  transform: scale(1.1);
}

.control-button.maximize {
  background-color: #28c940;
}
.control-button.maximize:hover {
  background-color: #3dd958;
  transform: scale(1.1);
}

.control-button.close {
  background-color: #ff5f57;
}
.control-button.close:hover {
  background-color: #ff7b72;
  transform: scale(1.1);
}
</style>
```