# 冰墩墩和雪容融 Web AR版

使用 [AR.js](https://ar-js-org.github.io/AR.js-Docs/) + [A-Frame.js](https://aframe.io/) 实现。

## 预览

静态服务是使用的 Google 的 [Firebase](https://firebase.google.com/) 国内网络访问或许有些慢，应该需要加载一会~

1. 使用手机浏览器扫码以下二维码或者打开 https://bingdundun.web.app 并授权摄像头权限。<div align=center><img alt="冰墩墩二维码" src="/public/assets/pattern.png" /></div>

2. 在网页加载完成后将摄像头对准下面冰墩墩和雪容融的图片或者上面的二维码图片（要将周围黑色区域包含在内）。<div align=center><img alt="冰墩墩和雪容融" src="/public/assets/mark.jpg" /></div>

这个技术呢兼容性不咋样，在手机上兼容性就更不咋样了，iPhone 手机可以直接使用相机扫码，打开自带的 Safari 浏览器，并授权就可以使用；安卓手机部分自带的应该也可以，或者也可以使用 Chrome、Firefox 试试。

## 教程

先简单介绍一下用的库吧：

- [AR.js](https://ar-js-org.github.io/AR.js-Docs)：这是一个纯 Web 的库，适用于支持[webgl](https://caniuse.com/webgl)的所有设备，它可以通过图像追踪（Image Tracking）、基于位置（Location Based AR）、标记追踪（Marker Tracking），我这里用到了图像追踪和标记追踪两种方法，后续会详细介绍。AR.js 两种版本一种是基于 [Therr.js](https://threejs.org/) 一种是基于 [A-Frame](https://aframe.io/) 实现的。 Therr.js 是什么相信很多人都了解过，这里就不讲了，我这里讲一下我弄到的 A-Frame 库。
- [A-Frame](https://aframe.io/docs/1.3.0/introduction)：也是基于 Therr.js 开发的，他将 Three.js 进一步封装，使其可以像写 HTML 标签的方式一样来实现 3D、AR、VR 场景等，还针对跨平台、WebVR 性能优化方面等做了很大的提升。

话不多说直接开写，分分钟写一个属于自己的冰墩墩！

### 获取 3D 建模

这里要做一个 AR 的、立体的冰墩墩，肯定是需要 3D 建模的，我在 [Sketchfab](https://sketchfab.com/3d-models/069d276a8b334a32b4993ec5dd2e278b) 找到了一个可以下载的冰墩墩和雪容融建模。

![下载建模](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/57f447a9acb647a99769e1a6082dbd78~tplv-k3u1fbpfcp-watermark.image?)

点击左下角 Download ，选择 **Autoconverted format (glTF)** 下载压缩包并解压得到如下文件：

![mark目录](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/81d037f0a11c40cbb14002c804eb969f~tplv-k3u1fbpfcp-watermark.image?)

这里要注意在他这个建模里面 `textures` 文件夹中有三个图片的文件名是以 `.` 开头的，这个在大多系统中默认是**隐藏文件**，需要开启隐藏文件查看才可以看得到，而且后续有其他操作有可能会丢失这三个图片，所以可以直接将这三个文件名前面的 `.` 去掉，并吧 `scene.gltf` 文件中的三个路径也改掉，这文件随便用一个文本编辑器就可以打开；也可以直接看这里：[Github](https://github.com/jaceyi/bingdundun/tree/main/public/assets)。

### 图像追踪

先确定好要用什么图片来做图像追踪，然后可以使用 [NFT Marker Creator](https://carnaux.github.io/NFT-Marker-Creator/#/) 创建**图像描述文件**。

![创建图形描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6475182c31a74f14905e737e6f1c58a4~tplv-k3u1fbpfcp-watermark.image?)

点击左侧上传好图片（不是正方形图片会变形，不过不碍事），然后点击右侧 Generate 稍等会就会下载三个文件 `bdd.fset` `bdd.fset3` `bdd.iset`，bdd 是原本的文件名，这三个文件名要保持一致不要修改，可以将其放在一个文件夹下面。

有了建模和图片追踪的描述文件，接下来就是写代码了，按照 AR.js 和 A-Frame 的文档来，引入 A-Frame 库和图形追踪脚本：

```html
<script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/aframe/1.0.4/aframe.min.js"></script>
<script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar-nft.js"></script>
```

然后使用 `<a-scene>` 标签创建场景，使用 `<a-nft>` 标签的 `url` 属性来引入图形描述文件，这里的 `<a-nft>` 标签是 AR.js 自定义的组件。再使用 `<a-entity>` 的 `gltf-model` 属性引入建模文件，最后定义照相机 `<a-entity camera>` 。

这样就完成啦！找个服务部署上线，调用摄像头还有使用一些传感器好像是只能再 HTTPS 的网站上才能用，这里测试的时候可以使用 [Codepen](https://codepen.io/jaceyi/pen/LYOLPNV) 不过他这个上传文件好像还要钱，我就先把文件丢在了别的静态服务上。

```html
<a-scene
  vr-mode-ui="enabled: false;"
  device-orientation-permission-ui="enabled: false"
  renderer="logarithmicDepthBuffer: true;"
  embedded
  arjs="trackingMethod: best; sourceType: webcam; debugUIEnabled: false;"
>
  <a-nft type="nft" smooth="true" smoothCount="0.1" url="/assets/mark/bdd">
    <a-entity
      rotation="-90 0 0"
      scale="120 120 120"
      position="55 -130 0"
      gltf-model="/assets/scene.gltf"
    >
    </a-entity>
  </a-nft>
  <a-entity camera></a-entity>
</a-scene>
```

标签具体属性参数请参考：[AR.js](https://ar-js-org.github.io/AR.js-Docs/image-tracking/)

我把所有的文件都放在了 `/assets` 目录下，`mark` 文件夹里面放的是上面生成的**图像描述文件** `bdd.fset` `bdd.fset3` `bdd.iset` 。

![assets目录](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c1724ef76e71475683f5318a2e0cdb86~tplv-k3u1fbpfcp-watermark.image?)

描述文件加载的或许会有点慢，这里可以定义一个 `<div class="arjs-loader">` 自己再写一下 CSS 样式，这个标签会在图像追踪的文件加载完成后自动隐藏。

整体代码差不多就这样，自己写不到50行代码：

```html
<head>
  <style>
    /* css 样式 */
  </style>
  <script src="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/aframe/1.0.4/aframe.min.js"></script>
  <script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar-nft.js"></script>
</head>
<body>
  <div class="arjs-loader">
    <div class="content">
      <div class="image"></div>
      <div class="text">
        <span>冰墩墩正在赶来的路上…</span>
      </div>
    </div>
  </div>
  <div class="tip">请将摄像头对准冰墩墩和雪容融的图片</div>
  <a-scene
    vr-mode-ui="enabled: false;"
    device-orientation-permission-ui="enabled: false"
    renderer="logarithmicDepthBuffer: true;"
    embedded
    arjs="trackingMethod: best; sourceType: webcam; debugUIEnabled: false;"
  >
    <a-nft type="nft" smooth="true" smoothCount="0.1" url="/assets/mark/bdd">
      <a-entity
        rotation="-90 0 0"
        scale="120 120 120"
        position="55 -130 0"
        gltf-model="/assets/scene.gltf"
      >
      </a-entity>
    </a-nft>
    <a-entity camera></a-entity>
  </a-scene>
</body>
```

### 标记追踪

标记追踪是现在 Web AR 中比较常见的一种，或许有挺多人都见过下面这张图，周围的大黑框就是用来识别的。

<div align=center>
  <img alt="Hiro" src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9122964d8cbb410684c3d3fd603eaf24~tplv-k3u1fbpfcp-watermark.image" />
</div>

可以直接使用 `<a-marker>` 标签，再加上 `preset="hiro"` 的属性，相当于是默认情况，AR.js 就会识别上面这张图片来展示内容。

```html
<a-marker preset="hiro">
  <a-entity
    rotation="-100 0 0"
    scale="0.5 0.5 0.5"
    position="0 0.3 -0.2"
    gltf-model="/assets/scene.gltf"
  >
  </a-entity>
</a-marker>
```

也可以通过这个网页自定义标记图片，[AR.js Marker Training](https://ar-js-org.github.io/AR.js/three.js/examples/marker-training/examples/generator.html) 。

![生成标记文件](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69ca5ec2763745059582ba765329c7af~tplv-k3u1fbpfcp-watermark.image?)

左边 Upload 上传自定义的图片并调整大小，然后中间的两个 Download 按钮分别下载 `pattern.patt` `pattern.png` 两个文件，`patt` 是给程序告诉如何识别标记的文件， `png` 文件是用于给摄像头扫描的。下面是我自定义的标记追踪。

```html
<a-marker type="pattern" smooth="true" url="/assets/pattern.patt">
  <a-entity
    rotation="-100 0 0"
    scale="0.5 0.5 0.5"
    position="0 0.3 -0.2"
    gltf-model="/assets/scene.gltf"
  >
  </a-entity>
</a-marker>
```

上述代码也是要放在 `<a-scene>`中运行的。相比图片追踪来说标记追踪的文件体积是要小很多，而且追踪的也更精确，尤其是再角度偏移的时候所带来的体验会很流畅。

## License

3D模型来源：[Sketchfab](https://sketchfab.com/3d-models/069d276a8b334a32b4993ec5dd2e278b)
License：[License](https://github.com/jaceyi/bingdundun/blob/main/public/assets/license.txt)
