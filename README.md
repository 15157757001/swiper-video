# scroll-video
uniapp仿抖音视频滑动效果
# 说明
插件分别在两种不同编译模式下实现效果，uni-app编译模式(nvue+vue)和weex编译模式(subNVue+vue)。
默认为weex编译模式，在 manifest.json 的源码视图里配置是切换模式， manifest.json -> app-plus -> nvueCompiler 切换编译模式。
weex编译模式下采用transform实现，已多端兼容。
uni-app编译模式采用swiper实现，目前只支持h5+，swiper嵌入视频后，下拉不灵敏。
# 1.0.0更新内容
1.视频滑动效果
2.视频滑动后自动播放和暂停功能
3.点击暂停与播放
4.视频拖动效果
5.解决iostouch无效
6.点赞功能
# 1.2.0更新内容
1.新增uni-app编译模式下实现，目前只支持h5+
# 测试
安卓真机调试 小程序模拟器 Chrome ios真机
