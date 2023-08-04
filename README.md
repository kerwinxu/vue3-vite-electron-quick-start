# 摘要

方便用vue3构造桌面应用程序的

# 使用步骤

```
# Clone this repository
git clone https://github.com/kerwinxu/vue3-vite-electron-quick-start.git
# Go into the repository
cd vue3-vite-electron-quick-start
# Install dependencies
npm install
# show app
npm run electron:dev

```

# 制造步骤

1. ```npm create vit```
   - project name 项目名称
   - select a framework ，用键盘方向键选择vue，
   - select a variant   , 我选择的是javascript
2. ```cd 到项目目录```
3. ```npm install -D concurrently cross-env electron electron-builder wait-on```
4. 在package.json中添加如下的
```
"build": {
    "appId": "com.my-website.my-app",
    "productName": "MyApp",
    "copyright": "Copyright © 2019 ${author}",
    "mac": {
      "category": "public.app-category.utilities"
    },
    "nsis": {
      "oneClick": false,
      "allowToChangeInstallationDirectory": true
    },
    "files": [
      "dist/**/*",
      "electron/**/*"
    ],
    "directories": {
      "buildResources": "assets",
      "output": "dist_electron"
    }
  }
  ```
5. 修改 package.json 的 scripts 属性
```
"scripts": {
      "dev": "vite --host",
      "build": "vite build",
      "serve": "vite preview",
      "electron": "wait-on tcp:5173 && cross-env IS_DEV=true electron .",
      "electron:dev": "concurrently -k \"cross-env BROWSER=none npm run dev\" \"npm run electron\"",
      "electron:build.win": "npm run build && electron-builder --win --dir",
      "electron:build.linux": "npm run build && electron-builder --linux appImage",
      "electron:build.test": "npm run build && electron-builder --dir",
      "electron:build.exe": "npm run build && electron-builder --win"
  },
  ```
6. 修改package.json的顶部属性,重要的是那个main入口
```
  "author": "github@lathesky",
  "main": "electron/electron.js", 
  ```
  且删除 ```"type": "module",``` 这一行。
7. 修改vite.config.js
```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  base: process.env.ELECTRON=="true" ? './' : "./",
  plugins: [vue()]
})
```
8. 在项目的顶级目录中建立"electron"文件夹
9. 在这个文件夹建立这个文件 electron/electron.js
```
// electron/electron.js
const path = require('path');
const { app, BrowserWindow } = require('electron');

const isDev = process.env.IS_DEV == "true" ? true : false;

function createWindow() {
  // Create the browser window.
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js'),
      nodeIntegration: true,
    },
  });

  // and load the index.html of the app.
  // win.loadFile("index.html");
  mainWindow.loadURL(
    isDev
      ? 'http://localhost:5173'
      : `file://${path.join(__dirname, '../dist/index.html')}`
  );
  // Open the DevTools.
  if (isDev) {
    mainWindow.webContents.openDevTools();
  }
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(() => {
  createWindow()
  app.on('activate', function () {
    // On macOS it's common to re-create a window in the app when the
    // dock icon is clicked and there are no other windows open.
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
});

// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});
```
10. 建立这一个文件 electron/preload.js
```
// electron/preload.js

// All of the Node.js APIs are available in the preload process.
// It has the same sandbox as a Chrome extension.
window.addEventListener('DOMContentLoaded', () => {
    const replaceText = (selector, text) => {
      const element = document.getElementById(selector)
      if (element) element.innerText = text
    }

    for (const dependency of ['chrome', 'node', 'electron']) {
      replaceText(`${dependency}-version`, process.versions[dependency])
    }
  })
```
11. 完成，用 ``` npm run electron:dev``` 看看效果。
12. 打包命令是如下的
```
vite build
electron:builder
```
# 引用
   - [2021年最前卫的跨平台开发选择！vue3 + vite + electron](https://zhuanlan.zhihu.com/p/424202065)

# Vue 3 + Vite

This template should help get you started developing with Vue 3 in Vite. The template uses Vue 3 `<script setup>` SFCs, check out the [script setup docs](https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup) to learn more.

## Recommended IDE Setup

- [VS Code](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) + [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).
