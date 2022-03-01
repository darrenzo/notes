# FAQs

- electron版本有 4 升至 5以上时，会出现 nodeIntegration 相关报错
  - electron 5+版本创建窗口时， 默认把 webPreferences.nodeIntegration 设置为false, 需要手动在 options 里设置为true
- 解决 NODE_MODULE_VERSION 值不一致导致的运行失败问题 (项目根目录下运行)
  - `npm rebuild --runtime=electron --target=6.1.5 --disturl=https://atom.io/download/atom-shell --abi=73`
    - target 是指 electron的版本
    - abi 是指所要的NODE_MODULE_VERSION值
- 制作web动画的插件和软件
  - https://lottiefiles.com/
  - https://www.live2d.com/
  - http://zh.esotericsoftware.com/spine-videos
<!-- TODO 待续 -->
