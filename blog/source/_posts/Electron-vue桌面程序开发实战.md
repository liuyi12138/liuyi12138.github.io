---
title: Electron-vue桌面程序开发实战
date: 2020-03-09 18:36:16
tags: [vue,轮子]
categories: 前端
---

**很早之前就想把[博客迁移脚本(YoudaoToHexo)](https://liuyi12138.github.io/2019/03/21/Python%E8%87%AA%E5%8A%A8%E5%90%8C%E6%AD%A5%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0%E5%88%B0Hexo/)给做成一个应用程序，由于脚本是python写的，之前调研过pyqt、vxpython等，感觉做的都比较丑，正好看到了PicGo的开发使用的是Electron-vue，比较符合我的技术栈，所以就进行了一次尝试**

<!--more--> 

## Electron-vue简介
* Electron-vue这个东西就比较神奇了，其作用就是利用vue来开发桌面端程序，并且可以适配主流的三种平台。
* 这就很类似于之前见过的Hbuilder(有兴趣的同学可以了解一下，也是用web开发的方式快速开发应用，可以直接适配Web，Android，ios，微信小程序等平台)
* 这两者其实本质上都是给web套个壳，虽然可能不如原生流畅，但开发周期短，感觉挺适合个人开发者自己玩
* Electron-vue的好处就在于他可以调用操作系统的API，Hbuilder貌似是不能的，但坏处就在于要把node、vue等一套打包，所以做出来的程序会比较大

## 开发流程
* 目前BlogGo-1.0.0已经开发完成，在此简单地记录了一下开发流程

### 新建项目
`vue init simulatedgreg/electron-vue Exename`

### 组件介绍
* 在进行electron-vue开发的时候，为了实现需要的功能，找了不少小巧方便，即插即用的组件，为开发提供了极大的便利

#### element-ui
* element-ui为vue开发常见的组件模组
* `/src/renderer/main.js`添加如下代码
```
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI)
```

* `/.electron-vue/webpack.renderer.config.js`第22行添加element-ui模块
`let whiteListedModules = ['vue','element-ui']`

* 删除`package-lock.json`,在`package.json`中修改,重新npm install
```
//在此就把接下来用上的packets都写了
"dependencies":{
    "element-ui": "^2.13.0",
    "child_process": "^1.0.2",
    "electron-store": "^5.1.1",
}
```

#### electron-store
* electron-store用于信息存储
* 在vue文件中直接引用即可
```
const Store = require('electron-store')
const store = new Store()
let dataSet = store.set('data')
let dataGet = store.get('data')
```
* electron-store存储的信息在`C:\Users\User\AppData\Roaming\Exename\config.json`中，也是因此electron-store不适合存储大量数据，大量数据可采用文件或者数据库的方式存储

#### child_process
* electron好就好在可以调用操作系统的API，当有调用cmd的需求时可以使用child_process
* 使用方式与electron-store相似，直接在vue文件中使用即可
```
const exec = require('child_process').exec
//cmdStr为需要执行的语句，path为执行目录
let workerProcess = exec(cmdStr, {cwd: path})

//有警告或者错误输出时的回调函数
workerProcess.stderr.on('data', function (error) {
    console.log(error)
})
//正常输出时的回调函数
workerProcess.stdout.on('data', function (data) {
    console.log(data)
})
//程序执行结束时的回调函数
workerProcess.on('close', function (code) {
    console.log(code)
})
```

### 开发踩坑
由于本来就不太会vue，js写的也一般，所以在整个开发过程中还是踩了不少的坑，但最终还是把产品做出来了，很多模块之后可以复用，当然也可以给其他朋友们一点帮助

#### request请求乱码
* 在BlogGo的开发中我需要向有道云发起请求，使用的是nodejs的request模块如下
```
var request = require('request')
 request({
      url: url,
      method: 'POST',
      json: true,
      headers: HEADERS,
      form: data,
      gzip: true
    }, function (error, response, body) {
        console.log(body)
    })
```
* 遇到乱码主要是受到的responce是数据包，没有解压导致的，需要在request的参数中加入gzip

#### JS异步调用
* JS是我目前见过的最奇葩的一门语言了，因为我一般都是在做Web时使用js，这也就导致遇到的大部分函数调用都是异步的，之前一直是采用回调函数的方法来解决，也就是上面request请求所使用的方法，但如果逻辑比较复杂，回调函数就会写得及其恐怖，这次算是彻底解决了这个问题
* 主要用到的有async，await，Promise，下面将一一个例子来讲解用法，但别指望我能把这些东西讲清楚，能用就行，想具体了解自行百度
```
async myRequest() {
    return new Promise(function (resolve, reject) {
        var request = require('request')
         request({
              url: url,
              method: 'POST',
              json: true,
              headers: HEADERS,
              form: data,
              gzip: true
            }, function (error, response, body) {
                if(!error && response.statusCode === 200)
                    resolve(body)
                else
                    reject(error)
            })
    }
}

//main.js
let body = await myRequest()
```

#### 自定义标题栏
* Electron-vue自带的标题栏其实还行，但一定要绑定工具栏就巨难受，为了不显示工具栏，终于走上了自定义标题栏的不归路......
* 其实主要想法也很简单，就是在主页面上单独放置一个MyTitle.vue页面，这需要在APP.vue中进行修改
```
//修改后的APP.vue
<template>
  <div id="app">
    <Mytitle />
    <router-view></router-view>
  </div>
</template>

<script>
  import Mytitle from '@/components/MyTitle'
  export default {
    name: 'BlogGo',
    components: {
      Mytitle
    }
  }
</script>

<style>
html,
body,
div {
  margin: 0;
  padding: 0;
}
</style>

```
* 而对于MyTitle.vue，自然是想怎么设置就怎么设置，但需要注意两个点
* 一个是去除顶部栏无法拖动的问题，这个上百度其实大家都讲得很清楚，基本都是给顶部栏加上一个`-webkit-app-region: drag`的style就可以，但如果在顶部栏上有按键的话，需要给按钮设置不可拖动`style="-webkit-app-region: no-drag"`不然无法触发点击事件
* 另一个是点击事件的传递问题，由于窗口是由main管理的，页面是由renderer管理，所以需要对windows操作需要联系到main，网上的大部分方法都是使用ipcMain与main的index.js进行通信，但我确实是没跑通，最终采用的是在PicGo中抄过来的方法
```
//MyTitle.vue
<template>
  <div id="mytitle">
  <el-row>
    <el-col :span="8" :offset="8">
      <div class="view-title">
        {{name}}
      </div>
    </el-col>
    <el-col :span="8">
      <div class="titlebtn">
        <el-button type="text" size="medium" icon="el-icon-minus" circle @click="winmin" style="-webkit-app-region: no-drag"></el-button>
        <el-button type="text" size="medium" icon="el-icon-full-screen" circle @click="winmax" style="-webkit-app-region: no-drag"></el-button>
        <el-button type="text" size="medium" icon="el-icon-close" circle @click="winclose" style="-webkit-app-region: no-drag"></el-button>
    </div>
    </el-col>
  </el-row>
  </div>
</template>

<script>
  const {remote} = require('electron')
  export default {
    data () {
      return {
        name: 'BlogGo-1.0.0'
      }
    },
    methods: {
      winmin () {
        const window = remote.BrowserWindow.getFocusedWindow()
        window.minimize()
      },
      winmax () {
        const window = remote.BrowserWindow.getFocusedWindow()
        if (window.isMaximized()) {
          window.restore()
        } else {
          window.maximize()
        }
      },
      winclose () {
        const window = remote.BrowserWindow.getFocusedWindow()
        window.close()
      }
    }
  }
</script>
    
<style>
#mytitle {
    position: left;
    width: 100%;
    height: 30px;
    -webkit-app-region: drag;
}
.titlebtn{
  width: 100%;
  height: 30px;
  text-align: right;
}

.view-title{
  text-align: center;
}
</style>
```

#### 最终实现效果
![BlogGo](https://gitee.com/know_the_emperor/picture/raw/master/20200311195809.png)

### 打包及发布

* 打包及发布有两种形式
    * electron-packager用于简单地打包
    * electron-builder用于完整的安装程序、自动更新的支持等，也就是所谓的CI构建
* 使用electron-packager打包只需要npm run build一条命令然后等待就可以了，十分方便
* 具体的打包与构建可以参考[官方文档](https://simulatedgreg.gitbooks.io/electron-vue/content/cn/building_your_app.html)，其实也没写的多具体,一两句话...
* 还可以通过[PicGo开发笔记](https://molunerfinn.com/electron-vue-5/#%E8%AF%B4%E6%98%8E)来了解更多，一般比较需要的是一些基本的修改比如应用名称啊，版本号啊，图标啊等等，更多的可自行参考上述的参考资料

## 开发感想
* 有一说一花了四天时间使用一项新技术开发出一个作品是一件挺值得让人骄傲的事情，先高兴一下
* 主要是作为一名业余选手，无论是Web、APP还是PC桌面程序的开发其实都是为了使自己的生活更加便捷，所以在整个开发过程中基本都是需要什么技术就去找，找到了就用上，并不关心背后的技术原理以及是否有更好的解决方法，有时候连为什么bug通了都不知道，但其实也无所谓，能用就行23333
* 另一方面就是要开始注意版本管理与发布了，之前一直不懂Github的ReadMe怎么去写，现在也要开始着手去学了，之后估计并不会用上CI吧，毕竟是做给自己用的，不过大家提的issue还是需要尝试去改进
* 路漫漫其修远兮，继续加油吧

