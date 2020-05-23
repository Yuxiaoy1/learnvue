## 使用 JS 做些有趣的事，在网站 favicon 图标里播放视频

今天跟大家分享个使用 JS 实现的有趣的小应用，如何在网站的 favicon 图标里播放视频。

favicon 是一个网站的图标（也称网站头像），本质为服务器上的一个位图文件，一般格式为.ico，配置了 favicon 的网站，我们在浏览器打开后，可以在标签页的网站标题前看到这个图标，当收藏某个网页后，favicon 也会作为浏览器收藏夹的图标。

当前有一些可以在线制作 favicon 的网站，这里推荐[favicon.io](https://favicon.io/favicon-generator/)，对于小型网站或个人站来说已经够用。

关于本次分享，如何在 favicon 里播放视频，实现思路如下：

- 添加 a 标签，提供功能入口
- 通过 link 标签配置 favicon
- 通过 video 标签加载目标视频
- 设置定时器，并获取固定时间间隔的视频截图
- 将实时获取的视频截图赋值给 favicon

下面将按照如上思路来实现本次分享的趣应用。

### 配置 favicon

这个比较简单，通过引入 link 标签并按照如下格式设置即可：

```html
<link rel="shortcut icon" href="./favicon.ico" type="image/x-icon" />
```

配置成功后，刷新页面，即可看到类似如下效果：

![favicon](https://cdn.nlark.com/yuque/0/2020/png/1076531/1590152903141-f3482373-ca43-4516-84cc-8b66a8658f9a.png)

### 加载目标视频

使用 video 标签可以加载目标视频并实现视频播放功能，在这里我们通过 js 代码来动态添加该标签，并且设置 video 相关属性（如 width 为 0，页面上不可见），同时通过 addEventListener 来添加 video 的 canplay 及 play 事件，这 2 个事件相关代码如下：

```js
function initVideo() {
  // 加载视频并添加视频播放事件
  let hasVideo = document.querySelector('video')
  if (hasVideo) return
  video = document.createElement('video')
  video.width = 0
  video.src = './src/tmac.mp4'
  video.crossOrigin = 'anonymous'
  document.body.appendChild(video)
  videoHandler()
}

function videoHandler() {
  // 添加视频播放事件
  video.addEventListener('canplay', () => {
    video.volume = 0.5
    setTimeout(() => {
      video.play()
    }, 300)
  })
  video.addEventListener('play', () => {
    videoToFavicon()
  })
}
```

### 定时获取视频图像到 favicon

video 的 play 事件中，执行了 videoToFavicon 方法，该方法的核心逻辑为定时获取视频图像到 canvas，并使用 canvas 的 API 将 canvas 画布转换为图像格式，然后赋值给 favicon 的 href 属性。

> Canvas 为 html5 标准中新增的画布元素， 可使用 JavaScript 在网页上绘制图像，相关使用请参考[Canvas@MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial)

了解了该逻辑，代码实现比较简单，可参考如下：

```js
function videoToFavicon() {
  // 使用canvas定时获取视频图像，并赋给favicon
  if (video.ended) return

  const context = canvas.getContext('2d')
  context.clearRect(0, 0, faviconSize, faviconSize)
  context.drawImage(video, 0, 0, faviconSize, faviconSize)
  setFavicon()

  setTimeout(() => {
    videoToFavicon()
  }, 50)
}

function setFavicon() {
  // 将canvas赋给favicon
  let url = canvas.toDataURL('image/png')
  let favicon = document.head.querySelector('link[rel="shortcut icon"]')
  if (favicon) {
    favicon.href = url
  } else {
    let favicon = document.createElement('link')
    favicon.rel = 'shortcut icon'
    favicon.href = url
    document.head.appendChild(favicon)
  }
}
```

这里使用了 setTimeout 函数，来实现 favicon 的实时更新操作，同时需要添加 if 判断，当视频结束时，停止对 favicon 的更新操作。

大家可以[点击]()查看整个应用的最终效果，玩下来还比较有趣。

完整的应用代码已经上传 github，感兴趣的伙伴也可以访问[yuxiaoy1@github]()进行查看。
