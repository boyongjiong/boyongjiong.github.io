---
title: GDC Data Portal 技术调研及 GraphQL 学习实践
date: 2019-07-30 10:10:59
tags:
categories: 工作向
---

## 项目搭建（技术栈及需要配置的基础项）
	+ 项目脚手架（Create-React-App or Other scaffold）
	+ 开发语言 ✔️ TypeScript or JavaScript + Flow(Static type checker for JavaScript)
	- 项目文件结构规划
	+ ESLint（ESLint-TypeScript or TSLint）/ husky / lint-staged / prettier
	+ 规范 `commit-message` 
	- React App style language 选型 (styled-components ❎ less sass css-in-javascript❎（结构不太好控制，需要把组件拆分的特别好，不然感觉写出来的都是一坨屎）)
	- 配置 iconfont 项目，之后小的图标直接用 iconfont 维护（可跟设计沟通提供 svg 资源）

## 涉及的新技术学习
[Relay](https://facebook.github.io/relay/) - JavaScript framework for building data-driven React Applications
[Redux](http://redux.js.org/) - Predictable state container for JavaScript Apps
[Recompose](https://github.com/acdlite/recompose) - React utility belt for function components and higher-order components（now we can use react hooks to do these things）::No more new features::
[React Hooks Doc](https://reactjs.org/docs/hooks-intro.html) - Hooks are a new addition in React 16.8. They let you use state and other React features without writing a class ::Recommend::
[Jest](https://facebook.github.io/jest/) - Delightful JavaScript testing
[D3](https://d3js.org/) - Data-Driven Documents

**Graphql-node 相关**
[graphql-yoga](https://github.com/prisma/graphql-yoga) - Fully-featured GraphQL Server with focus on easy setup, performance & great developer experience
[Prisma](https://www.prisma.io/) - Database Tools for modern application development
[Graphcool](https://www.graph.cool/docs/) - Open-source and self-hosted backend-as-a-service to develop serverless GraphQL backends. The Graphcool Framework offers an opionated backend setup, including GraphQL database mapping, subscriptions & permission system.
[DataLoader](https://github.com/graphql/dataloader) - DataLoader is a generic utility to be used as part of your application’s data fetching layer to provide a consistent API over various backends and reduce requests to those backends via batching and catching.

**Elasticsearch 相关**
[Elasticsearch Reference 6.0](https://www.elastic.co/guide/en/elasticsearch/reference/6.0/getting-started.html#getting-started) - Elasticsearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time.
[appbaseio/dejavu](https://github.com/appbaseio/dejavu) - The Missing Web UI for Elasticsearch: Import, browse and edit data with rich filters and query views, create search UIs visually.
![112BA68F-561B-4DBF-933A-44B8B841B6E8](https://tvax2.sinaimg.cn/large/a9034e0egy1gjvm9l7jjej20oq0eomzk.jpg)

## 项目中遇到的新技术
[Travis CI](https://travis-ci.org/) - Test and Deploy with Confidence. Easily sync your GitHub projects with Travis CI and you’ll be testing your code in minutes!

## 具体的实践及 Demo 开发任务
	+ 阅读 [GraphQL](https://graphql.org/learn/) 官网学习文档，并整理笔记
	+ React + Apollo 开发方式实践 (~/Documents/Github/GraphQL/hackernews-react-apollo)
	- React + Relay 开发方式实践 【WIP】
对上面两种方式进行对比，分别做出数据流动图，并主要分析其优劣，使用体验做对比
*区别：*
	1. 配置方便程度：Apollo【easy】— Relay-with typescript【hard】
	2. 使用方便程度：
	3. 代码可读性：
	4. 开发配置流程：

	+ 后端实践 GraphQL + Node.js (~/Documents/Github/GraphQL/hackernews-node)
	- 整理 protal-ui & portal-graphql 项目架构及细节实现
	- elasticsearch 文档阅读

## 相关参考文章
1. [Running Jest Tests Before Each Git Commit](https://benmccormick.org/2017/02/26/running-jest-tests-before-each-git-commit/) 提供了测试的思路，代码提交时对修改的模块相关功能进行测试，这样就可以保证高效性，不会阻塞工作流
2. [The FullStack Tutorial for GraphQL](https://www.howtographql.com/) - The free and open-source tutorial to learn all around GraphQL to go from zero to production.  [中文文档](http://howtographql.cn/) 但好像更新不及时啊这个中文文档，所以最好还是看英文的
3. [GraphQL-BFF：微服务背景下的前后端数据交互方案](https://www.infoq.cn/article/xZ0ws6_A5jmrJ6ZTPOz8)
4. [2019年13大 graphql 库和工具](https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=2651017395&idx=3&sn=ab0b3c87c20d4cdaad82321764195210&chksm=bdbea8e08ac921f6daca34dcb359d77fff2dcfd7fc6d256245dbb3cb2c504755c62066cc0f6f&scene=27#wechat_redirect)[another source](https://www.infoq.cn/article/bl*EA59lRYDE9XlkJGg0)
5. [Getting Started with Relay Modern](https://www.prisma.io/blog/getting-started-with-relay-modern-46f8de6bd6ec/) - This is a step-by-step tutorial with the goal of building a simple Instagram application from scratch using create-react-app.
6. [GraphQL server Basics: GraphQL Schemas, TypeDefs & Resolvers Explained](https://www.prisma.io/blog/graphql-server-basics-the-schema-ac5e2950214e)
7. [A React based WhatsApp Clone](https://www.tortilla.academy/Urigo/WhatsApp-Clone-Tutorial/master/next/step/0) - WhatsApp Clone is a free and open-source tutorial that will guide you step-by-step on how to create a full-stack, mobile, hybrid web application from scratch.

![graph-before-after-eca5096e70676c1116c7a579d9db128e](https://tvax4.sinaimg.cn/large/a9034e0egy1gjvm9lkxcaj20ss0v4408.jpg)

## GDC 相关资源
### GDC API EndPoints
Communicating with the GDC API involves making calls to API endpoints. Each GDC API endpoint represents specific API functionality, as summarized in the following table:

| Endpoint | Type | Description |
| — | — | — |
| **status** | Status | Get the API status and version information |
| **projects** | Search & Retrieval | Search all data generated by a project |
| **cases** | Search & Retrieval | Find all files related to a specific case, or sample donor |
| **files** | Search & Retrieval | Find all files related to a specific characteristics such as file_name, md5sum, data_format and others |
| **annotations** | Search & Retrieval | Search annotations added to data after curation |
| **data** | Download | Used to download GDC data |
| **manifest** | Download | Generates manifests for use with GDC Data Transfer Tool |
| **slicing** | BAM Slicing | Allows remote slicing of BAM format objects |
| **submission** | Submission | Returns the available resources at the top level above programs i.e., registered programs |

## Portal-graphql
All of the graphql related code has been migrated to [arranger](https://github.com/overture-stack/arranger) - Generate and manage your own genomic data portal.

![68747470733a2f2f692e696d6775722e636f6d2f596c6d396472722e706e67](https://tva1.sinaimg.cn/large/a9034e0egy1gjvm9lcq2yj21dk0pcdlc.jpg)

### Roadmap
#### Short Term
	* cli tool for bootstrapping new projects
	* Provide all neceaasry modules to implement searching functionality
		* Dynamic GraphQL schema generation
		* API Server (GraphQL endpoint)
		* Query / Aggregation building middleware
		* Response middleware (i.e. removing null aggregations)
		* UI Components
			* Aggregations
				* Simple View
				* Advanced View
			* Results Table
			* SQON Display
	* Provide editor interface to expose common transformations (similar to the Babel or bodybuilder REPLs)
		* Elasticsearch Mappings -> GraphQL Schema
		* GraphQL Query -> Elasticsearch Queries

#### Medium Term
	* Authentication
	* Sets
	* Analysis

#### Long Term 
	* Kibana Plugin
	* Hosted Data Portal generating service