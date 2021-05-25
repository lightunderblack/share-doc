# 统一工具库规划

## 第一阶段(2020-09-18~2020-12-04)
- 调研和输出技术方案：[工具库和静态资源统一管理平台开发计划](https://xiaojiaoyukeji.yuque.com/xiaojiaoxue/front/ygnrsb)
- 创建管理通用静态资源和第三方插件代码仓库：[teaching-sdk](https://gitlab.xinghuolive.com/teaching-fe/teaching-sdk)
- 部署CDN服务器：[teaching-sdk](https://cr.console.aliyun.com/repository/cn-shenzhen/spark-teaching/teaching-sdk/build)
- 收集常用静态资源和第三方插件：
  - `formulaSVG2HTML`
  - `img`
  - `aliplayer/v2.8.7`
  - `aliyun-oss-sdk`
  - `paho-mqtt`
  - `pdf-worker/2.5.207`

## 第二阶段(2020-12-25~2020-02-25)
- CDN环境支持`test`、`pre`(待讨论)
- 收集`班课`或其他产品线用到的`第三方插件`和公共静态资源，放置到[teaching-sdk](https://gitlab.xinghuolive.com/teaching-fe/teaching-sdk)，并且引用新`cdn`地址
- 研究部署公司公用`npm`仓库可行性和必要性，输出调研文档
- 提供创建`npm module`脚手架支持`cli`命令，类似[nwb](https://github.com/insin/nwb)或[create-react-library](https://github.com/transitive-bullshit/create-react-library)
- 脚手架提供支持发布`cdn`和发布`npm`命令，`cdn`和`npm`支持版本管理

## 第三阶段(2020-02-25~2020-04-25)
- 创建分组，统一管理通用组件仓库
- 输出脚手架使用指南、调试指南、发布规范等文档
- 前端组内推广脚手架
- 收集部分公共工具、方法或组件封装成`npm module`(作为示范)

## 第四阶段(2020-04-25~?)
- 逐步将各个产品线项目存在可复用代码封装成`npm module`(产品线负责人推动)
- 逐步将统一工具方法引入到旧项目，完成替换(产品线负责人推动)
- 中心推广最佳实践