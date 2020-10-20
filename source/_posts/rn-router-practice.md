---
title: React Native Router 技术选型
date: 2018-05-18 10:54:50
tags:
categories: 实践向
---

## 备选方案
 1. **[react-navigation](https://reactnavigation.org/docs/en/getting-started.html)** **★11614** - Easy to use Navigation for React Native
 2. **[react-native-navigation](https://wix.github.io/react-native-navigation/#/)** **★7335** - App-wide support for 100% native navigation with an easy cross-platform interface.
 3. **[react-native-router-flux](https://github.com/aksonov/react-native-router-flux)** **★6974** - React Native Router based on new React Native Navigation API

这三个是目前 github 中 star 数量最多的三个路由管理库，第三个方案是当时朱琛他们使用的方案，我看了一下文档，感觉这个定义页面的方式不是很酷，就没用这个，也没有深入了解（有需要可以自己了解一下）。

下面就只一二两种对比一下，重点说 react-native-navigation（因为我们用的是这个）

## 方案决策
1. *react-native-navigation*:
  * GitHub ★11614  issue - 118 Open / 3253 Closed（感觉不管是更新还是处理 issue 都比较勤快）
  * 初始化：耗时（1h）难易：★★★  我了解了这个的一些原理的东西，它是在 react-native 之上又封装了一层自己的东西，它提供给我们 API 去设置一些常见的设置，比如锁定竖屏之类的操作（所以需要认真去看文档）；初始化的过程中涉及到 react-native link 之类的操作，如果是第一次接触 react-native 可能得花费更多的时间在这个上面。
  ![2CA23C83-F1FD-4368-B4C1-B2763F692828](https://tva2.sinaimg.cn/large/a9034e0egy1gjvnf76euhj21z418g4ck.jpg)

  * 在startTabBasedApp(params) 这个方法调用时，如果遇到自己觉得奇怪的样式（比如 Android 应用启动时，页面总是从下往上 Slide-Up，就是 animationType 默认设置是 slide-up；或者 appStyle 中 orientation 就是设置横竖屏的方法…），多看两遍文档中的属性设置，兴许就 ok了
  ![4CDDB420-4D52-4A89-9B82-589678A3A460](https://tva4.sinaimg.cn/large/a9034e0egy1gjvnf7hyl6j21qk13waq7.jpg)

  * 用了这个框架，splashScreen 可能会有点问题，iOS 直接上传对应的 splashScreen 就 OK，但安卓，如果需要自定义，文档中也提供了，照着这个改下应该就好，也可以照着我们机器人的项目改，黄伟强搞的那个，应该没毛病。
  ![A065F5CC-2A7F-4DD2-A2C6-FC10FE674EDE](https://tva4.sinaimg.cn/large/a9034e0egy1gjvnf8b2f3j21z418gdt6.jpg)
  
  * 最麻烦的坑是有些情况我们得重写安卓物理返回键的逻辑，逻辑提前沟通清楚，在一些需要重写物理按键返回逻辑的页面，注意从前一个页面跳转过来的时候，需要设置对应的 overrideBackPress 为 true，
  ![89EC4992-9C1F-430F-940D-39FCA1F8D2C8](https://tva3.sinaimg.cn/large/a9034e0egy1gjvnf80a09j20vc0r6wik.jpg)

  * 然后按照我们项目中的下面方法绑定对应的逻辑
  ![5A084147-CD3A-4434-9E51-F1DCBACCA809](https://tvax3.sinaimg.cn/large/a9034e0egy1gjvnf7ra5hj20vc0wqq7j.jpg)

  * 对了，这个 startTabBasedApp 会同时初始化四个 tab 页的内容，所以我建议用它提供的 willAppear、didAppear、willDisappear、didDisappear 来监听对应的，做对应的获取数据、初始化的操作（这个在我们项目中做的不是特别好，所以建议提前优化逻辑）

  * 不建议直接在 tab 过去就是聊天页、应该有聊天列表的入口，点击再进去初始化，有明确的 unmount 事件，将页面的所有事件都消除掉，保证不会有内存泄漏的问题（好吧，我也不确定是不是内存泄漏，但一定要确保绑定事件离开页面时 unlisten 掉，不然重复绑定就成了肉眼可见的 bug 了）。

2. **react-navigation**
  * GitHub ★7369  issue - 719 Open / 1488 Closed (这个Open 的 issue 比较多，这个因为架构原因（我所理解的问题时无法获取是从具体哪个页面跳转过来的)，他们使用新的架构来重写 V2，现在状态时 WIP，所以可能好多 issue 没有精力来解决，或者说没有人来解决… 
  * 初始化：耗时（5m）难易：★ 这个我创建了新项目，只需要 yarn add react-navigation 之后，照着文档说明，设置自己想要 的东西即可。
  ![1AA3FAF9-96CF-4557-8FBD-02F88E059582](https://tvax3.sinaimg.cn/large/a9034e0egy1gjvnf6u26lj21z418gh1b.jpg)

## 建议
  1. 在设计过程中，设计应该明确指出对应的页面时通过 push、pop 的操作，还是通过 showModal 的方式展示，导航条中的按钮应该根据不同的屏幕展示方式，显示不同的操作按钮，比如合理的 push、pop 的操作，就用正常的箭头显示返回，在 iOS 上默认可以通过右滑返回；而 showModal 的屏幕，更多合理的是通过 “X” 号来关闭（具体情况具体对待，就是产品和设计必须对整个 App 页面跳转的逻辑十分清楚）

