# 本地调试游戏方法

## 1 测试工具类

系统暴露一个`TestUtils`类，挂载在游戏页面的`window.__test`下。它提供的工具方法有：

- `initConfig(config)`：游戏初始化数据
- `addTime(time: number)`：加时间
- `enterSettlement()`：接入结算页
- `submitScore(data)`：获取游戏结算数据
- `mockEnterMachine(isInterval = true, timeout: number = 1)`：模拟定时发送答题器信号
- `mockSendAnswerMachine(machine: object)`：模拟发送答题器信号
- `setGameConfig(gameId: number, config: object)`：设置游戏配置信息

### 1.1 initConfig(config)

该方法模拟客户端向游戏端传入初始化配置数据

### 1.2 addTime(time: number)

- time: 加时时长，单位`s`

该方法模拟客户端锚点加时操作通知到游戏端，游戏时间将延长

### 1.3 enterSettlement

该方法模拟客户端结束锚点操作通知到游戏端，游戏端进入结算场景

### 1.4 submitScore

该方法模拟游戏端向客户端获取游戏结果数据结果，返回`Promise`对象
### 1.5 mockEnterMachine(isInterval = true, timeout: number = 1)

- isInterval：计时器设置成`setInterval`还是`setTimeout`，默认是`setInterval`，会定时执行N次；设置为`false`，则只执行一次。
- timeout：设置延时时间，即延时多长时间执行发送，默认为`1ms`

使用方法，如

```ts
window.__test.TestUtils.mockEnterMachine(true, 10); // 定时10ms执行一次
```

该方法执行后，会随机获取内置的学生数据，模拟答题器信号发送通知给游戏端。

### 1.6 mockSendAnswerMachine(machine: object)

- machine：答题器信息，包含学生信息和答题器信息。一般只要传入学生信息即可，格式如：

```js
{ student: { studentId: 1, studentName: '陈会能', answer: 'C' } }
```

使用方法，如

```ts
window.__test.TestUtils.mockSendAnswerMachine({
  student: { studentId: 1, studentName: '陈会能', answer: 'C' }
});
```

### 1.3 setGameConfig(gameId: number, config: object)

- gameId：游戏唯一id，以`抢红包`为例，值为`2`
- config: 需要设置配置对象

使用方法，如

```ts
window.__test.TestUtils.setGameConfig(2, {
  RUN_OVER_TIMES : [1.8, 1.9, 2.0, 2.1, 2.3, 2.35, 2.4, 2.5, 2.65]; // 跑道运动时长，单位秒
});
```

通过该方法，可以动态调整游戏默认配置信息

## 2 本地模拟发送消息给游戏端

接入端是通过`receiveMessageFromParent`向游戏端推送消息。该方法已挂载在游戏页面的`window`上，可直接调用。具体的消息协议请查阅[项目架构设计-通信机制](../project_architecture.md#2.3);

## 3 本地模拟从游戏端接收消息

游戏端是通过`receiveMessageFromGame`，`games-platform`项目实现源码如下：

```ts
public static postMessageToParent(type: string, data: any, parentContext: any) {
  const e = { namespace: 'from-games-platform', type, data };
  const parentWindow = parentContext || window.parent as any;

  console.log('games-platform：redEnvelopes-toParent', JSON.stringify(e));
  if (!parentWindow || !parentWindow.receiveMessageFromGame) {
    return;
  }

  return parentWindow.receiveMessageFromGame(e); // 所有父级方法返回Promise对象
}
```

从上述代码可知，只要在游戏页面，在`window`上挂载实现一个`receiveMessageFromGame`方法即可。具体的消息协议请查阅[项目架构设计-通信机制](../project_architecture.md#2.3);

## 4 各游戏测试方法

以下列出各个游戏本地调试方法，大家可以直接使用，当然也可根据实际需要自行调整。后续开发的游戏，也需提供本地测试脚本。

- [抢红包游戏](../local_debug/red_envelopes.md)
- [课前辅导时间](../local_debug/before_class_coaching_time.md)
- [轻选择题答题互动](../local_debug/light_choice_question.md)
- [主观题答题互动](../local_debug/subjective_question.md)
- [课堂题答题互动](../local_debug/lectures_question.md)