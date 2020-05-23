# scroll-video
uniapp仿抖音视频滑动效果
# 重要说明
最新版本以项目示例为准

插件分别用swiper实现(多端)和css3动画实现(只支持app,强烈推荐)，可自行切换。

插件在uni-app编译模式下编写。

默认为weex编译模式，在 manifest.json 的源码视图里配置是切换模式， manifest.json -> app-plus -> nvueCompiler 切换编译模式。

小广告: [您可能需要一款播放器](https://ext.dcloud.net.cn/plugin?id=785)
# 功能说明

ccs3：上下切换视频，左右查看看详情，weex-bindingx优化动画，无限加载，点击暂停，仅支持安卓和ios

swpier： 上下切换视频，无限加载，多端兼容

## css3参数说明

| 参数 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| typeX | Boolean | false | 是否开启左右滑动 |
| playCount | Number | 2 | 剩余多少视频加载视频列表 |
| startDistance | Number | 5 | 开启左右滑动时有效,判断左右上下拖动的启动距离 px |
| minTime | Number | 300 | 判断为快速滑动的时间,该时间内无视回弹 ms |
| backDistance | Number | 200 | 上下滑动的回弹距离 px |

# 常见问题

1.为什么自定基座和云打包后video不显示，标准基座运行就可以显示

VideoPlayer勾选 manifest.json -> app模板权限控制 -> VideoPlayer

2.手机端微信打开的话，屏幕的上下滑动变成了调节亮度和声音
H5页面用手机打开的时候视频层上面的所有东西都给覆盖了

微信浏览器视频播放时会调用它内置视频播放器。所以不支持。H5平台： 在部分浏览器中会强制调用原生播放器播放（如：微信内置浏览器、UC浏览器等

# 插件交流群 689566471


## 如果觉得插件不错，麻烦给个好评
