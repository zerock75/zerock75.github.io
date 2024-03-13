---
title: Electron + Vite + Vue3 설정
categories: ['Electron']
tags: [electron, vite, vue3, scss]
---

공식사이트: <https://electron-vite.org/guide/>{: target="_blank"}

> Pre-requisites
> Requires Node.js version 18+, 20+ and Vite version 4.0+


## 1. Electron Vite 설치

```console
npm create @quick-start/electron
-----------------------------------------------
Need to install the following packages:
@quick-start/create-electron@1.0.23
Ok to proceed? (y)
√ Project name: ... WAAZEV
√ Package name: ... waazev
√ Select a framework: » vue
√ Add TypeScript? ... No / Yes
√ Add Electron updater plugin? ... No / Yes
√ Enable Electron download mirror proxy? ... No / Yes

Scaffolding project in D:\Project\Zerock\test2\WAAZEV...

Done. Now run:

	cd WAAZEV
	npm install
	npm run dev

```

## 2. 실행 
```console
npm run dev
```

### 2.1 오류 시
```console
error during start dev server and electron app:
Error: listen EACCES: permission denied ::1:5173
at Server.setupListenHandle [as _listen2] (node:net:1855:21)
at listenInCluster (node:net:1920:12)
at GetAddrInfoReqWrap.doListen [as callback] (node:net:2075:7)
at GetAddrInfoReqWrap.onlookup [as oncomplete] (node:dns:109:8)

```

**관리자 권한으로 cmd 실행**
```console
net stop winnat
net start winnat
```

## 3. 폴더구조
```
.
├──src
│  ├──main
│  │  ├──index.ts
│  │  └──...
│  ├──preload
│  │  ├──index.ts
│  │  └──...
│  └──renderer    # with vue, react, etc.
│     ├──src
│     ├──index.html
│     └──...
├──electron.vite.config.ts
├──package.json
└──...
```
{: .nolineno}

## 4. vscode tab 오류 해결
```
useTabs: auto
```
{: file='/.prettierrc.yaml'}

## 5. scss 설치
```console
npm install sass
npm install sass-loader
```

### 5.1 sass 변수설정
```
$danger: #fc6161;
```
{: file='/src/renderer/src/asssets/_variables.scss'}


### 5.2 sass 변수 전역설정
```
import { resolve } from 'path'
import { defineConfig, externalizeDepsPlugin } from 'electron-vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  main: {
    plugins: [externalizeDepsPlugin()]
  },
  preload: {
    plugins: [externalizeDepsPlugin()]
  },
  renderer: {
    resolve: {
      alias: {
        '@renderer': resolve('src/renderer/src')
      }
    },
    plugins: [vue()],

		// 추가
		css: {
			preprocessorOptions: {
				scss: {
					additionalData: `@import "@renderer/assets/_variables.scss";`
				},
			}
		},
  },
	
})
```
{: file='/electron.vite.config.mjs'}


### 5.3 main.css를 sass로 교체 `/src/render/src/assets/main.css` -> `/src/render/src/assets/main.scss`
#### 5.3.1 바꾼 main.scss 를 main.js에 적용 
```
import './assets/main.scss'	// 교체

import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```
{: file='/src/renderer/src/main.js'}




