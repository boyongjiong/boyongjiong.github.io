---
title: 人生，重在参与
date: 2020-10-20 16:22:53
---
> 人生，重在参与

## 大学时间
参加学校电子设计大赛
参加全国飞思卡尔智能车大赛
参加校内外的科研竞赛，具有一定的软硬件开发和调试经验

## 大连元合科技有限公司 2016.01 - 2017.06 Web 前端开发

重构 MediTO Cardio 心脏专科数据平台 - （jsp 项目(java 做逻辑处理) -> js 组件处理交互逻辑）
 1. bootstrap 源码学习，基于 bootstrap 开发栅格化布局、各种控件如Accordion、DatePicker、Table等样式编码
 2. 开发了多维度数据检索组件，多选框加载所有可检索条件，选中某个条件后加载其对应的条件检索组件，最后组装检索条件，通过 ajax 调用接口，获取检索结果展示。该组件减少了和后台代码的耦合，维护简单，可拓展性强

心之家后台管理平台：订单管理，新订单提醒（轮询，websocket 主动推送），医生管理，医院管理，病例管理
 1. 利用 Node.js Express 进行前后端分离实践，利用 Express Route methods 做路由管理，利用 Node.js 做登录验证、缓存等功能
 2. 利用 bootstrap-table、select2、summernote 等组件完成功能开发，在实践过程中通过 Github issues 和 Stack Overflow 解决遇到的问题，在此过程中，也通过阅读源码，根据具体需求为插件编写新的功能
 3. 前后端分离开发，前端调用后端接口获取数据，并在页面中展示，在此过程中理解并解决了跨域请求问题

MediTO MDG关键病种统一数据平台：通过对专病组数据进行采集、管理、分析以展开专业化医学课题研究的系统
 1. 项目依旧采用Node.js Express 作为框架开发，利用 swig 模板设计 layout.html 做通用布局，精简代码，加强前端代码可读性；在此基础上添加Gulp 作为前端自动化构建工具辅助开发，使用 LESS 作为样式开发语言
 2. 使用 jQuery UI Portlets 构建具有多种交互功能的磁力板控件，根据当前登录用户展示其对应的某专病组或课题组详细数据面板，面板的添加、移除、拖动排序、重命名等功能；创建专病组/课题组项目流程，从课题样本选择、展示，研究方案设置，到数据分组与统计项设置复杂功能的开发。
 3. 在开发过程中，由于业务逻辑复杂，开发量巨大，开始尝试以模块为单元开发，通过 IIFE 编写模块，把一些属性和相关的 dom 操作，还有事件绑定都放到对应的函数里，并且使用一个模块来挂载这些属性和方法，说白了就是一个 js 版本的单例模式（什么是单例模式？？？），但是本质上还是对 dom 进行的手工操作。
 4. 遇到的算法问题：树形结构数据转换成一维数组；

## 深圳道乐科技有限公司
 1. 华夏基金微信小程序：为华夏基金开发用来做基金交易，包括充值、卖出、取现、定投、开户、撤单、申购、认购、转换等完全功能的小程序；和一名同事共花费一个月的时间完成了整个小程序的开发及测试后的 bug 修复，本人主要负责了其中首页、基金列表、搜索、基金详情、转换、定投等 25 个页面的开发，并开发了通用的定投日期选择组件（周定投、月定投：选择每周几或者每月几号进行定投，定投方式修改的更新与取消对应已选值的保存，修改时之前已选定时间值与本次操作选择时间值状态的分别保存，在调用更新接口并更新到后台之后，关闭组件）。
 2. 基于 Promise，封装 Restful apiBuilder 方法，将该系统中调用的所有 api 做统一管理，只需要初始化调用一次，即可将其接口返回值缓存起来。例如基金列表的数据信息不回经常变动，所以缓存下来，提快加载速度；方法也提供了强制更新的标志字段，就可以通过下拉刷新的操作获取到最新的值，并进行缓存的更新。
 3. 华泰基金 H5 页面的开发，使用 Vue(vue + vue-router + vuex) + axios + highcharts + mint-ui + sass 技术栈进行开发，根据文档学习了 vue 相关的知识，并利用业余时间使用 vue 全家桶开发了知乎日报的 H5，加深了对 vue 的了解。

## 牛法信息科技有限公司 - 牛二智能法务团队
### 项目技术背景
 1. 利用自然语言学习更好的理解普通用户的法律问题，并提供专业解释及相应的解决方案的 app。基于 CNN，LSTM 等深度神经网络，研发了国内首个法律语言模型。利用基础法律语言模型，开发了国内首个可以理解用户自然语言的法律咨询机器人。尤其是在用户表达不明确的情况下，可以识别出用户的目的、问题的限制条件及逻辑关系，然后提供解答。
 2. 法律咨询机器人在用户提问后，会自动标识出缺少的必要条件，在宽泛解答的同时进行提问，提高用户的参与度和对话轮次。
 3. 从流量大的法律问答网站实时爬取用户提问，对问题分类并在 5 秒内自动回复，其中 20%的用户认为答案是最佳，以此提高平台品牌的曝光率和后续的案源转化。

### 转化目标
 1. 根绝 NLP 解析的字段信息，对用户的提问做价值判断，在判断有潜在价值的情况下，引导后续转化；实践中为律师筛选出多个家产过亿、数十家产过千万的客户，在同样数量的线索下，高净值客户占比远超同行业水平；
 2. 律师转接平台：用户致电 400 电话后，会自动根据归属地、历史提问、律师当前电话数量等多个维度，将电话转接至最合适的律师；
 3. 律师有需要针对聊天纪律截图进行排序和检索需求，使用 OCR 和基于上下文的排序算法，自动对聊天记录截图排序、打标签；在该类事物的处理上提升 90%的效率。

### 前端具体实现
智能法务 App：为了快速开发和更快上线迭代试错，我们选择了 React Native 作为应用开发框架，使用 RN 可以一套代码直接生成 iOS Android 的应用程序，项目开发中，使用 Redux 做状态管理，让 state 的变化变得可预测。
  1. 使用 react-native-navigation 作为导航解决方案，完成了各种页面跳转、转场动画、弹窗等需求；同设计沟通，将图标资源转换成 iconfont 文件，使用 react-native-vector-icons 实现 各种图标、图片资源的快速加载，优化用户体验；通过 react-native-permission 处理权限获取流程，保证用户在体验需要额外权限的功能时，能够有正确流畅的操作；由于当时 aliyun 为提供 oss 上传的 RN SDK，所以我们通过学习相关的编码语言，阅读相关的文档，封装了 aliyun-OSS 的 RN SDK，完成了用户上传文件直传的需求；进行了 A/B 测试技术调研，最后使用 CodePush 完成了程序的热更新功能；
  2. 在开发需求的过程中，通过学习了解别的 RN 团队在开发中的一些优化实践，比如可取消的异步操作，项目的 bundle 拆分以及首屏渲染的最佳实践等等。

牛二智能法务官网、数据分析后台管理平台、法条检索、案例检索 H5：在这些项目中，为了团队技术栈的统一，决定使用 react 作为 web 前端的开发框架，通过对社区开源项目的了解，我们觉得蚂蚁金服前端团队开源的 ant-design 及相关的 dva 框架很适合快速的需求开发，因为有完整现成的组件设计规范，我们也不需要再花费时间和资源在这种内部系统上。

### 团队管理能力
  1. 团队使用 JIRA 和 Teambition 工具分配工作任务，我主要负责需求分析和任务拆解，组织团队成员预估任务开发时长，以及主持每日任务进度汇报、问题交流以及当日任务规划；并负责每日 app 版本发布，按时提交测试。
  2. 在项目开发过程中负责 code review，在项目 eslint 配置的基础上，对成员发布的代码进行 CR，共同进步。
工作态度认真负责，入职一月转正，年底获取公司**年度优秀员工**的荣誉。

## 深圳碳云智能科技有限公司
### 活力圈后台管理平台
  1. 活力圈是为碳云智能规划的健康管理社区进行用户、住宅、运营信息管理以及通过硬件采集的健康数据的展示，项目基于 Vue + Vuex + Element UI + echarts 进行开发，在此项目的开发过程中，主要完成的工作任务有 腾讯聊天 SDK 封装新的 UI及相关 API 的联调，完成功能的开发；各种类型图表的开发，包括饼图、柱状图、散点图等等，通过阅读文档及相关的示例代码，高效快速的完成了需求的开发。
  2. 最后完成了所有需求的开发、功能测试以及部署工作，确保了系统按计划上线。

### LIMS（Laboratory Information Management System） 系统功能开发（前端微服务架构 - qiankun、single-spa原理理解及源码阅读）
  1. 碳云研究院信息管理系统，主要是用来管理实验室各实验流程数据，实现实验数据的全链化追踪、保存、查看，对实验样品也进行严格的管控，对样品流转精细控制
  2. 整个项目的架构师基于 iframe 做的微服务架构的尝试，实现了各个子模块的独立开发、部署，有效提高开发效率
  3. 开发 lims 样本管理模块，主要包括包裹管理、入库管理、异常管理三个模块，利用两周时间开发共 16 个复杂页面，高质量的完成了产品的需求，得到了一致的好评；开发客服管理系统；开发免疫表征系统
  4. iframe 虽然在我们的项目中很好的实现了之前的需求，但是这种微前端方案已经是过时的解决方案了，所以我了解学习的最新的解决方案，去阅读文档、开发 Demo ，对 Qiankun、single-spa 的原理进行了深入的学习，为了方便以后的项目开发中能够在最开始有更多更好的选择
 
### LifeBit 项目技术调研 - GraphQL 项目开发技术调研，文档输出
  1. 因为 LifeBit 项目规划就是类似 GDC Data Portal 类似的基因组数据共享数据平台（Genomic Data Commons Data Portal），GDC 数据门户是一个强大的数据驱动平台，可以让癌症研究人员和生物信息学家搜索和下载癌症数据进行分析。基于对数据门户网站数据量巨大以及字段繁复的特性，我们觉得 GraphQL 相对于 Restful API 在数据检索，获取，页面加载等方面会有比较明显的优势，所以最后决定采取 Portal-ui 类似的架构开发 LifeBit 项目
  2. 技术选型：
    * React - JavaScript library for building user interfaces
    * Relay - JavaScript framework for building data-driven React applications
    * Redux - Predictable state container for JavaScript apps
    * Recompose - React utility belt for function components and higher-order components
    * TypeScript
    * Jest - Delightful JavaScript testing
    * D3 - Data-Driven Documents
  3. 为了准备项目开发，我对上面需要用到的技术通过阅读文档和 Demo 编码的方式进行了学习。在阅读 GDC Data Portal 项目源码的过程中，发现在整个架构中，有些项目代码提交依旧十分频繁，但是 Data Portal 项目的代码却很久没有更新了，通过与代码维护者的邮件沟通，了解到他们为了能帮助别人快速的搭建类似的数据检索平台，开源了另一个项目 - Arranger。
  4. [Arranger](https://www.overture.bio/products/arranger)  is a collection of reusable components for creating centric search portals with [Elastic Search](https://www.elastic.co/products/elasticsearch/) .  Arranger consists of the following components:
    * Arranger search API provides a layer that sits above your Elasticseach cluster to expose a data-model aware GraphQL API, generated from your own Elassticsearch index mapping.
    * Arranger Components provides a rich set of UI components that are configured to speak to the search API.
    * Arranger Admin provides the API and UI for configuring the search API and content management for the search portal. 
  5. 该库目的就是创建一个框架作为任何给定数据门户的核心库，类似于 Elastic 的 Kibana 所实现的功能，这样做能有以下这些好处：
    * Reduce duplicate code(减少重复代码)
    * Ability to fix bugs and add features to many projects at once(能够同时为多个项目修复 bug 和添加功能)
    * Pool developer resources(集中开发者的资源)
    * Increase cross-team communication(加强跨团队沟通)
    * Encourage open source contribution(鼓励开源代码贡献)
  6. 在学习这个的过程中，又了解了 Elasticsearch、docker 相关的知识 ，学习项目的架构设计。
![架构设计](https://image.baidu.com/search/down?url=https://tva1.sinaimg.cn/large/a9034e0egy1gjvm9lcq2yj21dk0pcdlc.jpg)

### 碳云报告平台（front-report/front-report-admin）系统架构设计（服务端渲染实践 Nuxt.js && Next.js 实践）
  1. 碳云报告平台是碳云各个检测报告的产出平台，使用 front-report-admin 管理和维护各个通用组件，统一公司报告的设计规范，通过 fromt-report-admin 创建报告，并配置对应的数据源，生成报告。
  2. front-report 是使用 Nuxt.js 开发的服务端渲染项目，相对于客户端渲染，页面加载速度提升了 50%
  3. 最近使用 Next.js 开发了益生菌报告，熟悉了 Next.js 的各种 API，并在项目实践中学习，加深理解
