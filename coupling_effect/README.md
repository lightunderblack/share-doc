# 流程节点属性控制和属性间联动关系

## 1 基本概念

### 1.1 属性特性

用于描述属性的数据称为特性。它包括两类：

- 必填性、可视性、编辑性、可选值
- 值

### 1.2 Source、Target

#### 1.2.1 释义

- Source：联动关系的发起方
- Target：联动关系的接收方

#### 1.2.2 组成元素

- 一个属性不同特性
- 多个属性的特性

#### 1.2.3 元素间逻辑链接

- Source：支持或且非
- Target：支持且

### 1.3 流程节点控制属性

#### 1.3.1 流程节点作为源对象

流程节点值作为触发源，控制属性的特性

#### 1.3.2 流程节点作为前置条件

流程节点作为前置条件，联动关系在流程符合的节点下才会生效

## 2 配置管理

### 2.1 字段

#### 2.1.1 联动关系集合的字段

| 名称 | 类型 | 描述 | 必填 | 备注 |
| ---- | ------ | ------ | ------ | ------ |
| id | string | 唯一键 | - | - |
| classId | string | 分类id | 是 | - |
| flowNodes | array[object] | 流程节点对象信息。流程节点对象包含两个字段：id：工作流id，变更分类关联的工作流；nodes：流程节点值数组 | 流程节点作为前置条件时必填 | 配置联动关系，流程节点作为前置条件 |
| description | string | 描述 | 否 | - |
| sources | array[source] | 源列表 | 是 | - |
| targets | array[target] | 目标列表 | 是 | - |
| status | string | 状态:启用/禁用 | 是 | - |
| crtUser | string | 创建人 | - | - |
| crtTime | number | 创建时间 | - | - |
| updUser | string | 更新人 | - | - |
| updTime | number | 更新时间 | - | - |

#### 2.1.2 流程节点控制的字段

| 名称 | 类型 | 描述 | 必填 | 备注 |
| ---- | ------ | ------ | ------ | ------ |
| id | string | 唯一键 | - | - |
| classId | string | 分类id | 是 | - |
| flowNodes | array[object] | 流程节点对象信息。流程节点对象包含两个字段：id：工作流id，变更分类关联的工作流；nodes：流程节点值数组 | 是 | 流程节点作为触发源 |
| description | string | 描述 | 否 | - |
| targets | array[target] | 目标列表 | 是 | - |
| status | string | 状态:启用/禁用 | 是 | - |
| crtUser | string | 创建人 | - | - |
| crtTime | number | 创建时间 | - | - |
| updUser | string | 更新人 | - | - |
| updTime | number | 更新时间 | - | - |

#### 2.1.3 Source的字段

| 名称 | 类型 | 描述 | 必填 | 备注 |
| ---- | ------ | ------ | ------ | ------ |
| id | string | 唯一键 | - | - |
| attrId | string | 属性id | 是 | 属性的数据源为属性页签和受影响物件页签的所有属性 |
| attrName | string | 属性名称 | 是 |  |
| groupId | string | 分组id | 是 |  |
| groupName | string | 分组名称 | 是 |  |
| attributeTagId | string | 页签id | 是 |  |
| metaName | string | 特性 可视性：isVisible 必填性：isRequired 编辑性：isEditable 可选值：availableList 值：value | 是 |  |
| metaValue | string/array/number | 特性值 | 是 | 根据所选的特性来动态显示输入框：选择特性为可视/必填/编辑时，为下拉单选是/否 ;选择特性为可选值：根据属性配置的数据字典对应选型值，为下拉多选；选择特性为值：根据属性配置的组件类型动态展示 |
| operator | string | 比较运算符 | 是 | 和高级搜索相同，根据当前属性的类型动态展示不同的运算符 |
| logicNon | string | 逻辑非 | 否 | |
| left | string | 左括号 | 否 | |
| right | string | 右括号 | 否 | |

#### 2.1.4 Target的字段

| 名称 | 类型 | 描述 | 必填 | 备注 |
| ---- | ------ | ------ | ------ | ------ |
| id | string | 唯一键 | - | - |
| attrId | string | 属性id | 是 | 属性的数据源为属性页签和受影响物件页签的所有属性 |
| attrName | string | 属性名称 | 是 |  |
| groupId | string | 分组id | 是 |  |
| groupName | string | 分组名称 | 是 |  |
| attributeTagId | string | 页签id | 是 |  |
| metaName | string | 特性 可视性：isVisible 必填性：isRequired 编辑性：isEditable 可选值：availableList 值：value | 是 |  |
| metaValue | string/array/number | 特性值 | 是 | 根据所选的特性来动态显示输入框：选择特性为可视/必填/编辑时，为下拉单选是/否 ;选择特性为可选值：根据属性配置的数据字典对应选型值，为下拉多选；选择特性为值：根据属性配置的组件类型动态展示 |

### 2.2 技术实现

#### 2.2.1 解析

- 将对象详情属性页签的属性元数据值，对应填充到source数据中，最终生成一条逻辑表达式
- 若逻辑表达式为true，则将target的数据，作用于对象详情属性，将对应的元数据值填充进去
- 若逻辑表达试为false，则不处理

#### 2.2.2 轮动

如配置联动关系：A->B；B->C；C→D；联动关系处理若只考虑用户输入，用户选中A，最终效果B，而不是D。需要将每次联动关系处理后的输出，当作下次输入，如此轮动循环，才能最终达到效果。

#### 2.2.3 闭环

如配置联动关系：A->B；B->C；C→A；按照上述配置关系轮动，最终会导致死循环，需要一种检测闭环的机制，防止出现上述情况；以防万一，在联动关系处理程序中也需加入死循环机制，设置循环次数阀值，超过阀值时，终止循环。

#### 2.2.4 优先

- 如配置联动关系：A→B(必填)；C→B(不必填)；同时满足上述关系，则后一个配置覆盖前一个配置
- 流程节点控制的优先级高于联动关系

#### 2.2.5 流程节点控制

- 只对变更分类有效
- 流程节点值可通过变更分类关联工作流获取，但只能获取已走完流程的节点值。（荣华建议，若无法取值，则创建一条流程走完）
- 不能在父分类上设置流程节点，只能在具体变更子分类上设置
- 考虑到这种场景：某流程节点之后所有流程都有相同控制。（配置节点值支持多选；）

#### 2.2.6 前端触发，联动处理流程图

![属性联动流程图](/couplingEffect/images/couplingEffect.png)

## 3 实现效果

### 3.1 属性联动关系列表

![属性联动关系列表](/couplingEffect/images/couplingEffectList.png)

### 3.2 添加属性联动关系

![添加属性联动关系](/couplingEffect/images/addCouplingEffect.jpeg)

### 3.3 流程控制关系列表

![流程控制关系列表](/couplingEffect/images/flowControllList.png)

### 3.4 添加流程控制关系

![添加流程控制关系](/couplingEffect/images/addFlowcontroll.png)