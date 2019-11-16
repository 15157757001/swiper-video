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

swpier： 上下切换视频，多端兼容

## css3参数说明

| 参数 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| typeX | Boolean | false | 是否开启左右滑动 |
| playCount | Number | 2 | 剩余多少视频加载视频列表 |
| startDistance | Number | 5 | 开启左右滑动时有效,判断左右上下拖动的启动距离 px |
| minTime | Number | 300 | 判断为快速滑动的时间,该时间内无视回弹 ms |
| backDistance | Number | 200 | 上下滑动的回弹距离 px |

# 1.0.0更新内容
1.视频滑动效果

2.视频滑动后自动播放和暂停功能

3.点击暂停与播放

4.视频拖动效果

5.解决iostouch无效

6.点赞功能
# 1.1.0更新内容
1.更改uni-app编译模式下使用swiper组件实现。
# 1.2.0更新内容
1.新增css3实现效果

2.css3支持点击播放和暂停功能

3.移除swiper中点击视频播放和暂停事件，尽量减少swiper中的事件 

4.index索引首视频设置为自动播放

5.新增进度条

6.修复h5多个视频播放问题
# 1.3.0更新内容
1.css3: 引入BindingX优化拖动性能

2.修复进度条

3.优化css3样式

4.css3新增无限加载
# 1.4.0更新内容
1.swiper:修复下滑不灵敏的bug

2.css3:新增参数说明，新增左右滑动查看详情

3.video组件新增上下边框阴影


## 如果觉得插件不错，麻烦给个好评
