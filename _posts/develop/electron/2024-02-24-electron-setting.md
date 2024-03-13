---
title: Electron 환경설정
---

1. Vue Cli를 전역으로 설치한다
```console
npm install -g @vue/cli
```

2. 적당한 위치에서 관리자권한으로 cmd를 실행 후 프로젝트를 생성한다
```console
vue create waaz
```

3. electron builder 를 설치한다
```
cd waaz
vue add electron-builder
```


4. vscode 로 `/src/router/index.js` 파일 open
```
import { createRouter, createWebHashHistory } from 'vue-router'	// createWebHistory 를 createWebHashHistory 로 변경

const router = createRouter({
  // history: createWebHistory(process.env.BASE_URL),
  history: createWebHashHistory(),
  routes
})
```
{: file="/src/router/index.js"}

5. vscode 터미널에서 electron-edge-js 추가
```console
npm install electron-edge-js
npm install --save electron-progressbar electron-updater
```


6. /src 폴더 하위에 front(vuejs 폴더), background(electron 폴더) 폴더 생성


7. /background.js 파일을 제외 한 나머지 파일들을 `front` 폴더로 이동

8. 폴더 이동에 따른 @ alias 와 build 옵션 변경을 위해 `/front/vue.config.js` 파일 수정
```
const { defineConfig } = require('@vue/cli-service')
const path 	= require('path');
module.exports = defineConfig({
	transpileDependencies: true,
	
	configureWebpack: {
		resolve: {
			alias: {
				'@': path.join(__dirname, 'src/front')
			}
		},
	},

	css: {
		loaderOptions: {
			scss: {
				additionalData: `@import "@/assets/css/_variables.scss";`
			}
		}
	},
	
	pluginOptions: {
		electronBuilder: {
			customFileProtocol: './',
			mainProcessFile: 'src/background/main.js',
			rendererProcessFile: 'src/front/main.js',

			externals: ['electron-edge-js'],
			builderOptions: {
				asar: true,
				appId: 'com.zerock.waaz',
				productName: 'Windows Auto Activation',
				extraMetadata: {
					author: 'zerock@naver.com',
					description: 'Windows Auto Activation ZeRock ElectronJS',
					repository: {
						type: 'git',
						url : 'https://github.com/zerock75/waaz-v1',
					},
				},
				// icon: './resources/t9.ico',
				nsis: {
					perMachine: true,
					language: 1042,
					shortcutName: 'WAAZE',
				},
				win: {
					target: [
						{
							target: 'nsis',
							// arch: 'ia32'
							arch: 'x64'
						}
					],
				},
				publish: {
					provider: 'generic',
					url: 'http://update.zerock.net/waaz/',
					channel: 'latest'
				},			
			},
		}
	}
	
})
```
{: file="/vue.config.js"}



9. /public 폴더 하위에 preload.js 작성
```
const { contextBridge, ipcRenderer } = require('electron');

const validChannels = [''];

contextBridge.exposeInMainWorld(
	'ipc', {
		send: (channel, data) => {
			if (validChannels.includes(channel)) {
				ipcRenderer.send(channel, data);
			}
		},
		on: (channel, func) => {
			if (validChannels.includes(channel)) {
				// Strip event as it includes `sender` and is a security risk
				ipcRenderer.once(channel, (event, ...args) => func(...args));
			}
		},
	},
	
);
```
{: file="/public/preload.js"}



10. `/.eslintrc.js` 파일에 아래 내용 추가
```
globals: {
		__static: 'readonly',
	},
```
{: file='/.eslintrc.js'}


11. `/src/background/main.js` 생성 후 아래내용 추가
```
'use strict'

import { app, protocol, BrowserWindow,  } from 'electron'
import { createProtocol } from 'vue-cli-plugin-electron-builder/lib'
import installExtension, { VUEJS3_DEVTOOLS } from 'electron-devtools-installer'
import path from 'path';
import fs from 'fs';


import { autoUpdater } from "electron-updater"
import ProgressBar from 'electron-progressbar';

// import { exec } from 'child_process'



// 로그 파일의 경로를 설정합니다.
const logFilePath = path.join(__dirname, 'error.log');

// 오류를 로그 파일에 기록하는 함수
function logError(error) {
	const timestamp = new Date().toISOString();
	const message = `${timestamp} - Error: ${error}\n`;

	// 오류 메시지를 로그 파일에 추가합니다.
	fs.appendFileSync(logFilePath, message);
}

// 전역 오류 핸들러를 설정합니다.
process.on('uncaughtException', (error) => {
	logError(error);
	// 필요에 따라 추가적인 오류 처리를 수행합니다.
});



const isDevelopment = process.env.NODE_ENV !== 'production'

// Scheme must be registered before the app is ready
protocol.registerSchemesAsPrivileged([
	{ scheme: 'app', privileges: { secure: true, standard: true } }
])

async function createWindow() {

	let winOpt = {};

	if (isDevelopment === true) {
		winOpt.fullscreen = false;
		winOpt.width = 540;	// 540
		winOpt.height = 960;	// 960
		winOpt.autoHideMenuBar = false;
		winOpt.alwaysOnTop = false;
		winOpt.kiosk = false;
		winOpt.frame = true;
	}
	else {
		winOpt.fullscreen = true;
		winOpt.width = 1080;
		winOpt.height = 1920;
		winOpt.autoHideMenuBar = false;
		winOpt.alwaysOnTop = false;
		winOpt.kiosk = true;
		winOpt.frame = false;
	}


	const win = new BrowserWindow({
		width: winOpt.width,
		height: winOpt.height,
		useContentSize: false,
		webPreferences: {		
			nodeIntegration: true,
			contextIsolation: !process.env.ELECTRON_NODE_INTEGRATION,
			preload: path.resolve(__static, 'preload.js'),
		},
		frame: winOpt.frame,
		fullscreen: winOpt.fullscreen,
		autoHideMenuBar: winOpt.autoHideMenuBar,
		alwaysOnTop: winOpt.alwaysOnTop,
		kiosk: winOpt.kiosk,
	})

	if (process.env.WEBPACK_DEV_SERVER_URL) {
		// Load the url of the dev server if in development mode
		await win.loadURL(process.env.WEBPACK_DEV_SERVER_URL)
	if (!process.env.IS_TEST) win.webContents.openDevTools()
	} else {
		createProtocol('app')
		win.loadURL('app://./index.html')

		// win.loadURL('http://localhost:12001')
	}
}

// Quit when all windows are closed.
app.on('window-all-closed', () => {
	// On macOS it is common for applications and their menu bar
	// to stay active until the user quits explicitly with Cmd + Q
	if (process.platform !== 'darwin') {
		app.quit()
	}
})

app.on('activate', () => {
	// On macOS it's common to re-create a window in the app when the
	// dock icon is clicked and there are no other windows open.
	if (BrowserWindow.getAllWindows().length === 0) createWindow()
})

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on('ready', async () => {
	if (isDevelopment && !process.env.IS_TEST) {
		// Install Vue Devtools
		try {
			await installExtension(VUEJS3_DEVTOOLS)
		} catch (e) {
			console.error('Vue Devtools failed to install:', e.toString())
		}
	}
	createWindow()

	autoUpdater.checkForUpdates();
	
})

// Exit cleanly on request from parent process in development mode.
if (isDevelopment) {
	if (process.platform === 'win32') {
		process.on('message', (data) => {
			if (data === 'graceful-exit') {
				app.quit()
			}
		})
	} else {
		process.on('SIGTERM', () => {
			app.quit()
		})
	}
}



autoUpdater.autoDownload = false;
let progressBar;


autoUpdater.on('update-available', () => {
	autoUpdater.downloadUpdate();
});


autoUpdater.once('download-progress', () => {

	progressBar = new ProgressBar({
		text: '업데이트 다운로드...',
		detail: '업데이트 설치파일을 다운로드 하고 있습니다...',
	});

	progressBar.on('completed', function () {
		console.info(`completed...`);
		progressBar.detail = '업데이트를 설치 준비 중 입니다...';
	})
	.on('aborted', function () {
		console.info(`aborted...`);
	});
});

autoUpdater.on('update-downloaded', () => {
	progressBar.setCompleted();
	autoUpdater.quitAndInstall(false, true);
});
```


12. 실행
```
npm run electron:serve
```


14. 참고
https://gist.github.com/vikassaini01/802ebf2b445154692d103cb04fd2bb9b