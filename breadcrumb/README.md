# 面包屑实现方案

## 1 前言

之前实现了公共面包屑，但效果不理想，存在以下问题：

- 仅支持两层路径信息显示
- 路径名称固定，无法动态设定。比如通过id进入到详情页后，面包屑上对应的路径信息需显示详情名称
- 当路由来回切换时，面包屑路径信息会混乱

面包屑作为多个应用的标配，有必要好好研究，实现适应不同场景的组件。故做了如下技术方案。

## 2 方案

### 2.1 功能

面包屑需有如下基本功能：

- **路径信息**：可配置，统一维护
- **路径层数**：理论上无限制
- **路径与路由一一对应**：路由地址可映射转换成路径信息，路由层级即是路径层级
- **路径超链接**：是由它对应的路由地址是否有页面决定
- **路径信息可动态设置**：比如通过id进入到详情页后，可动态设置详情名称到对应路径信息中

### 2.2 设计

#### 2.2.1 route、page、breadcrumb关系

![router-page-breadcrumb](/breadcrumb/images/router-page-breadcrumb.jpg)

如图所示，路由是核心，维护视图、路径映射信息。面包屑作为页面的一部分又独立于页面，两者唯一交互是在需要动态设置路径信息时，通过appStore对象，避免了耦合。

#### 2.2.2 router数据结构

为了表达页面/路径层级关系，使用`树形`数据结构来存储`router`信息非常合适，树节点字段如下所示：

#### 2.2.2.1 RouterTreeNode

| 参数 | 说明 | 类型 | 必填 | 默认值 |
| ---- | ---- | ---- | ---- | ------ |
| id | 唯一id，小写英文 | string | 是 | - |
| name | 路径中文名称 | string | 是 | - |
| path | 路径英文名称，注意不是路径全称，是标识当前层级的 | string | 是 | - |
| exact | 标识路由是否精确匹配 | boolean | 否 | true |
| isMenu | 标识路由是否用于导航栏菜单 | boolean | 否 | false |
| hasMainLayout | 标识是否需要主框架 | boolean | 否 | true |
| hasBreadcrumb | 页面是否包含面包屑，不是所有页面都需要面包屑 | boolean | 否 | true |
| componentPath | 路由映射视图所需渲染组件，路径相对于项目src/pages | string | 否 | - |
| routes | 子路由信息 | Array< [RouterTreeNode](#2-2-2-1-RouterTreeNode) > | 否 | - |

#### 2.2.3 router数据存放位置

处于如下考量：

- 放在统一地方，便于寻找
- 按模块划分，一个模块对应一个目录，模块下的页面路由统一放置在目录下，便于扩展
- 只需按照约定俗成放置目录文件，无需关心最终路由数据输出

故将`router`数据按照模块放置在项目的`config/routers`下，目录层级即是路由层级，借助`Node.js`读取文件数据，将分块的数据拼接成[router数据结构](#2-2-2-router数据结构) ，当作`Webpack`的环境变量注入到项目使用。

#### 2.2.4 router数据预处理

为了方便，配置信息是简化版数据，不能直接使用，需通过程序预处理，包含如下逻辑处理：

- 必填字段校验
- 选填字段，若允许默认值，则将默认值填充
- 与层级有关字段`id`、`path`，按照层级拼接输出全称，`id`用`_`拼接，`path`用`/`拼接
- 通过层级关系生成`breadcrumb`，存储不同层级路径信息
- 判断是否包含面包屑即`hasBreadcrumb`为`true`，若包含则生成`breadcrumbComponent`，将面包屑组件`ExBreadcrumb`导入，使用按需加载组件[Loadable](https://github.com/jamiebuilds/react-loadable)包裹
- 判断是否需要主框架即`hasMainLayout`为`true`，若包含则生成`mainLayout`，将主框架组件`MainLayout`导入，使用按需加载组件[Loadable](https://github.com/jamiebuilds/react-loadable)包裹
- 判断路由是否映射视图即`componentPath`不为`null`，若有值则生成`component`，将对应的视图组件导入，使用按需加载组件[Loadable](https://github.com/jamiebuilds/react-loadable)
- 移除多余字段

最终输出[RealRouterTree](#2-2-4-1-RealRouterTreeNode)类型数据`routerTree`和[RealRouterList](#2-2-4-2-RealRouterListItem)类型数据`routerFlatten`。

`routerTree`为`RealRouterTree`类型的树形结构数据，每个节点属性如下所示：

##### 2.2.4.1 RealRouterTreeNode

| 参数 | 说明 | 类型 | 必填 | 默认值 |
| ---- | ---- | ---- | ---- | ------ |
| id | 唯一id，小写英文，按照层级拼接输出全称，用`_`拼接 | string | 是 | - |
| name | 路径中文名称 | string | 是 | - |
| path | 路径英文名称，按照层级拼接输出全称，用`/`拼接 | string | 是 | - |
| breadcrumb | 面包屑各个层级路径信息 | Array< [BreadcrumbItem](#2-2-4-3-BreadcrumbItem) > | 否 | [] |
| exact | 标识路由是否精确匹配 | boolean | 否 | true |
| isMenu | 标识路由是否用于导航栏菜单 | boolean | 否 | false |
| hasBreadcrumb | 页面是否包含面包屑，不是所有页面都需要面包屑 | boolean | 否 | true |
| component | 路由映射页面对应视图组件 | Loadable< Component > | 否 | null |
| mainLayout | 主框架组件 | Loadable< MainLayout > | 否 | null |
| breadcrumbComponent | 面包屑组件 | Loadable< ExBreadcrumb > | 否 | null |
| routes | 子路由信息 | Array< [RealRouterTreeNode](#2-2-4-1-RealRouterTreeNode) > | 否 | - |

`routerFlatten`是通过`routerTree`转换，将树形结构碾平成一维的类型为`RealRouterList`的对象数组，每个对象属性如下所示：

##### 2.2.4.2 RealRouterListItem

| 参数 | 说明 | 类型 | 必填 | 默认值 |
| ---- | ---- | ---- | ---- | ------ |
| id | 唯一id，小写英文，按照层级拼接输出全称，用`_`拼接 | string | 是 | - |
| name | 路径中文名称 | string | 是 | - |
| path | 路径英文名称，按照层级拼接输出全称，用`/`拼接 | string | 是 | - |
| breadcrumb | 面包屑各个层级路径信息 | Array< [BreadcrumbItem](#2-2-4-3-BreadcrumbItem) > | 否 | [] |
| exact | 标识路由是否精确匹配 | boolean | 否 | true |
| isMenu | 标识路由是否用于导航栏菜单 | boolean | 否 | false |
| hasBreadcrumb | 页面是否包含面包屑，不是所有页面都需要面包屑 | boolean | 否 | true |
| component | 路由映射页面对应视图组件 | Loadable< Component > | 否 | null |
| mainLayout | 主框架组件 | Loadable< MainLayout > | 否 | null |
| breadcrumbComponent | 面包屑组件 | Loadable< ExBreadcrumb > | 否 | null |

##### 2.2.4.3 BreadcrumbItem

| 参数 | 说明 | 类型 | 必填 | 默认值 |
| ---- | ---- | ---- | ---- | ------ |
| id | 唯一id，小写英文，按照层级拼接输出全称，用`_`拼接 | string | 是 | - |
| name | 路径中文名称 | string | 是 | - |
| path | 路径英文名称，按照层级拼接输出全称，用`/`拼接 | string | 是 | - |

#### 2.2.5 面包屑渲染流程

![breadcrumb-render-process](/breadcrumb/images/breadcrumb-render-process.jpg)

### 2.3 实现

#### 2.3.1 router配置信息

![router-catalog-tree](/breadcrumb/images/router-catalog-tree.jpg)

如上图所示，按模块划分不同目录，所有目录下的`index.js`文件放置的是该模块的根路径节点信息，其中`routers/index.js`是所有模块路径的根路径。以`materiel`模块为例，详细说明路由是如何按模块配置的：

- `routers/index.js`：存放根路径节点信息

```js
module.exports = {
  path: "/",
  id: 'cpdm',
  name: "首页",
  hasBreadcrumb: false,
  componentPath: "portal/home/Home"
};
```

- `routers/materiel/index.js`：存放`materiel`模块根路径节点信息

```js
module.exports = {
  name: "物料管理",
  path: "materiel",
  isMenu: true,
};
```

- `routers/materiel/manage.js`：存放`materiel`模块下`manage`子模块的路径节点信息

```js
module.exports = [
  {
    name: "物料管理",
    path: "manage",
    isMenu: true,
    routes: [
      {
        name: "物料映射关系绑定",
        path: "mappingrelation",
        isMenu: true,
        componentPath: "portal/materiel/relationBinding/RelationBinding",
      }, {
        name: "物料映射关系修改记录",
        path: "changerecord",
        isMenu: true,
        componentPath: "portal/materiel/changeRecord/Index",
      }
    ]
  }
];
```

#### 2.3.2 router预处理后

经过预处理后，程序会暴露出两个字段：树形结构`routerTree`和一维数组`routerFlatten`。用`materiel`模块为例，展示预处理后数据：

- `routerTree`：

```js
[
   {
      "path": "/",
      "id": "cpdm",
      "name": "首页",
      "exact": true,
      "isMenu": false,
      "hasBreadcrumb": false,
      "breadcrumbComponent": null,
      "component": Loadable<Component>,
      "breadcrumb":[
         {
            "path": "/",
            "id": "cpdm",
            "name": "首页"
         }
      ],
      "routes":[
         {
            "exact": true,
            "isMenu": true,
            "component": null,
            "name": "物料管理",
            "path": "/materiel",
            "id": "cpdm_materiel",
            "breadcrumbComponent": Loadable<ExBreadcrumb>,
            "breadcrumb":[
               {
                  "path":"/",
                  "id":"cpdm",
                  "name":"首页",
               },
               {
                  "name":"物料管理",
                  "path":"/materiel",
                  "id":"cpdm_materiel"
               }
            ],
            "routes":[
               {
                  "exact": true,
                  "isMenu": true,
                  "name": "物料管理",
                  "id": "cpdm_materiel_manage",
                  "path": "/materiel/manage",
                  "component": Loadable<Component>,
                  "breadcrumbComponent": Loadable<ExBreadcrumb>,
                  "breadcrumb":[
                     {
                        "path": "/",
                        "id": "cpdm",
                        "name": "首页",
                     },
                     {
                        "name": "物料管理",
                        "path": "/materiel",
                        "id": "cpdm_materiel"
                     },
                     {
                        "name": "物料管理",
                        "path": "/materiel/manage",
                        "id": "cpdm_materiel_manage"
                     }
                  ],
                  "routes": [
                     {
                        "isMenu": true,
                        "exact": true,
                        "name": "物料映射关系绑定",
                        "component": Loadable<Component>,
                        "breadcrumbComponent": Loadable<ExBreadcrumb>,
                        "path": "/materiel/manage/mappingrelation",
                        "id": "cpdm_materiel_manage_mappingrelation",
                        "breadcrumb": [
                           {
                              "path": "/",
                              "id": "cpdm",
                              "name": "首页"
                           },
                           {
                              "name": "物料管理",
                              "path": "/materiel",
                              "id": "cpdm_materiel"
                           },
                           {
                              "name": "物料管理",
                              "path": "/materiel/manage",
                              "id": "cpdm_materiel_manage"
                           },
                           {
                              "name": "物料映射关系绑定",
                              "path": "/materiel/manage/mappingrelation",
                              "id": "cpdm_materiel_manage_mappingrelation"
                           }
                        ]
                     },
                     {
                        "isMenu": true,
                        "exact": true,
                        "name": "物料映射关系修改记录",
                        "component": Loadable<Component>,
                        "breadcrumbComponent": Loadable<ExBreadcrumb>,
                        "path": "/materiel/manage/changerecord",
                        "id": "cpdm_materiel_manage_changerecord",
                        "breadcrumb": [
                           {
                              "path": "/",
                              "id": "cpdm",
                              "name": "首页",
                           },
                           {
                              "name": "物料管理",
                              "path": "/materiel",
                              "id": "cpdm_materiel"
                           },
                           {
                              "name": "物料管理",
                              "path": "/materiel/manage",
                              "id": "cpdm_materiel_manage"
                           },
                           {
                              "name": "物料映射关系修改记录",
                              "path": "/materiel/manage/changerecord",
                              "id": "cpdm_materiel_manage_changerecord"
                           }
                        ]
                     }
                  ],
               }
            ]
         },
      ],
   }
]
```

- `routerFlatten`：

```js
[
   {
      "isMenu": true,
      "exact": true,
      "component": null,
      "name": "物料管理",
      "path": "/materiel",
      "id": "cpdm_materiel",
      "breadcrumbComponent": Loadable<ExBreadcrumb>,
      "breadcrumb":[
         {
            "path": "/",
            "id": "cpdm",
            "name": "首页",
         },
         {
            "name": "物料管理",
            "path": "/materiel",
            "id": "cpdm_materiel"
         }
      ],
   },
   {
      "exact": true,
      "isMenu": true,
      "name": "物料管理",
      "component": null,
      "path": "/materiel/manage",
      "id": "cpdm_materiel_manage",
      "breadcrumbComponent": Loadable<ExBreadcrumb>,
      "breadcrumb":[
         {
            "path": "/",
            "id": "cpdm",
            "name": "首页",
         },
         {
            "name": "物料管理",
            "path": "/materiel",
            "id": "cpdm_materiel"
         },
         {
            "name": "物料管理",
            "path": "/materiel/manage",
            "id": "cpdm_materiel_manage"
         }
      ]
   },
   {
      "isMenu": true,
      "exact": true,
      "name": "物料映射关系绑定",
      "component": Loadable<Component>,
      "breadcrumbComponent": Loadable<ExBreadcrumb>,
      "path": "/materiel/manage/mappingrelation",
      "id": "cpdm_materiel_manage_mappingrelation",
      "breadcrumb":[
         {
            "path": "/",
            "id": "cpdm",
            "name": "首页",
         },
         {
            "name": "物料管理",
            "path": "/materiel",
            "id": "cpdm_materiel"
         },
         {
            "name": "物料管理",
            "path": "/materiel/manage",
            "id":"cpdm_materiel_manage"
         },
         {
            "name": "物料映射关系绑定",
            "path": "/materiel/manage/mappingrelation",
            "id": "cpdm_materiel_manage_mappingrelation"
         }
      ]
   },
   {
      "isMenu": true,
      "exact": true,
      "name": "物料映射关系修改记录",
      "component": Loadable<Component>,
      "breadcrumbComponent": Loadable<ExBreadcrumb>,
      "path": "/materiel/manage/changerecord",
      "id": "cpdm_materiel_manage_changerecord",
      "breadcrumb":[
         {
            "path": "/",
            "id": "cpdm",
            "name": "首页",
         },
         {
            "name": "物料管理",
            "path": "/materiel",
            "id": "cpdm_materiel"
         },
         {
            "name": "物料管理",
            "path": "/materiel/manage",
            "id": "cpdm_materiel_manage"
         },
         {
            "name": "物料映射关系修改记录",
            "path": "/materiel/manage/changerecord",
            "id": "cpdm_materiel_manage_changerecord"
         }
      ]
   }
]
```

#### 2.3.2 ExBreadcrumb：面包屑组件

```jsx
import React, { Component } from 'react';
import { inject, observer } from 'mobx-react';
import PropTypes from 'prop-types';
import { Breadcrumb } from 'antd';
import { Link } from 'react-router-dom';

import './ExBreadcrumb.less';

const { Item : BreadcrumbItem } = Breadcrumb;

@inject('app')
@observer
export default class ExBreadcrumb extends Component {
  //动态替换面包屑名称
  replaceNames (breadcrumbs) {
    const { breadcrumb } = this.props.app;
    return breadcrumbs.map(item => {
      const { id } = item;
      const match = breadcrumb[id];
      return match == null ? { ...item } : { ...item, name: match };
    });
  }

  //替换面包屑路径参数
  replaceParams (breadcrumbs, params) {
    return breadcrumbs.map(breadcrumb => {
      breadcrumb.path = breadcrumb.path.replace(/:(_*[a-zA-Z0-9]+_*)/g, (match, name) => {
        const value = params[name];
        return value ? value : `:${name}`;
      });
      return ({ ...breadcrumb });
    });
  }

  render () {
    const { 
      match: { params },
      route: { breadcrumb }
    } = this.props;
    const props = { 
      separator: '>', 
      className: 'ex-bread-crumb-wrapper global-body'
    };
    return (
      <Breadcrumb {...props}>
        { 
          this.replaceNames(this.replaceParams(breadcrumb, params)).map(({ id, name, path, componentPath }, index) => {
            const showLink = index !== (breadcrumb.length - 1) && componentPath;
            const link = (({ '': 1, '/': 1 })[path] ? '' : `/page${path}`).replace(/\/+/g, '/');
            return (
              <BreadcrumbItem key={id}>
                { showLink ? (<Link to={link} style={{textDecoration: 'underline', color: '#1890ff'}}>{name}</Link>) : name }
              </BreadcrumbItem>
            );
          })
        }
      </Breadcrumb>
    );
  }

  static propTypes = {
    route: PropTypes.object.isRequired
  }
}
```

#### 2.3.3 实现效果

应用效果如图所示：

![real-breadcrumb](/breadcrumb/images/real-breadcrumb.jpg)

动态设置路径信息效果：

![dynamic-set-real-breadcrumb](/breadcrumb/images/dynamic-set-real-breadcrumb.jpg)

## 3 结语

基本实现可以覆盖90%场景的面包屑组件，但也存在不足以及缺陷：

- 由于配置信息放置在`config`，`webpack-dev-server`无法监控修改，需手动重启`server`才看得到效果
- 预处理后路由信息包含字段冗余，需精简
- 不支持`nested router`