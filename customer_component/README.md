# 定制组件技术方案

## 背景

对象管理属性需要展示一种较复杂组合效果组件，比如:-10% 20～30kg等，用户根据需要定制呈现效果

## 分析

通过归纳总结，此类组件包含的元素：前缀、输入、链接符、后缀。将这些元素按照一定规则有序组合，就可以实现用户需求。涉及到场景有：

+ 前缀+输入
+ 前缀+输入+后缀
+ 前缀+输入+链接+输入+...
+ 前缀+输入+链接+输入+...+后缀
+ 输入+后缀
+ 输入+链接符+输入+...
+ 输入+链接符+输入+...+后缀

## 实现

1. 对象管理添加属性弹框，类型需要新增定制组件类型
2. 用户选择定制组件类型，显示定制按钮
3. 用户点击定制按钮显示浮框，在浮框里对组件进行定制
4. 定制组件浮框主要包含三部分：
- 预览区：展示定制组件效果
- 工具栏：前加(向前插入)、后加(向后插入)、删除
- 列表区：展示定制组件包含的元素
5. 定制组件包含的元素有四种：前缀、输入、链接、后缀
- 前缀/后缀：可以是预设值或选择值，对应组件类型为Label/Select，如后缀为单位，则设置为Select
- 输入：对应组件类型为Input/InputNumber，Input可限定字符长度；InputNumber限定最大/最小/精度(小数位)
- 链接符：预设值，对应组件类型为Label
6. 定制组件的元素列表展示字段

| 序号 | 类型 | 组件 | 值 | 值集 |
| ---- | ---- | ---- | ---- | ------ |
| 1 | 前缀/后缀/链接/输入 | Label/Input/InputNumber/Select | 针对Label/Input/InputNumber | 针对Select |

7. 定制组件添加元素弹框

| 类型 | 前缀/后缀/链接/输入 |
| ---- | ------ |
| 组件 | Label/Select/Input/InputNumber |
| 数据集 | 针对Select |
| 值集 | 针对Select |
| 字符长度 | 针对Input |
| 最大值 | 针对InputNumber |
| 最小值 | 针对InputNumber |
| 精度 | 针对InputNumber |
| 值 | 设置默认值,主要针对Label/Input/InputNumber |

8. 定制组件元素列表数据格式，字段

| 参数 | 说明 | 类型 | 例子 |
| ---- | ---- | ---- | ---- |
| id | | String | |
| type | 元素类型 | String | 前缀:"1" 输入:"2" 连接:"3" 后缀:"4" |
| attrType | 组件类型 | String | 标签:"999" 下拉单选:"8" 单行文本:"1" 数字:"3" |
| verificationRule | 校验规则(maxLength/max/min/accuracy) | JSON String | 数字校验："{\"max\":100,\"min\":0,\"accuracy\":2}"
文本校验："{\"maxLength\":500}" |
| defaultValue | 默认值/值 | Any | "KB" |
| availableList | 可选值 | Array | [ "V", "KB" ] |
| dictId | 数据集 | String | "CPLM_OBJ_LIFECYCLE" |

9. 定制组件类型属性数据格式，继承属性数据格式，新增一个字段存储定制组件信息

| 参数 | 说明 | 类型 |
| ---- | ---- | ---- |
| moduleConfigInfo | 存储定制组件信息 | String | JSON String |