# Egret游戏开发规范
## 0  版本
版本|内容|作者|更新时间
-|-|-|-|
v1.0.0|制定游戏开发规范初版|陈会能|2020-12-08
v1.0.1|添加优先级A两项：提供测试脚本；输出复盘文档|陈会能|2020-12-08
## 1 前言
本规范并非`Egret`官方指南，仅做为`games-platform`项目游戏开发实践指导建议。游戏开发对于项目成员都是陌生的，都是摸着石头过河，我们都一样。有些建议可能不是最佳，可能存在疏漏和误导。
## 2 规范等级
- `优先级A`：强制执行
- `优先级B`：推荐执行
- `优先级C`：选择执行
## 3 优先级A
### 3.1 目录规范
目录按游戏划分，资源(代码)按游戏放置。为了确保游戏的独立性，从目录上就要做好隔离，尤其是资源代码等不可随意放置，必须放到对应游戏目录下。具体规则，请查看[游戏开发流程](./develop_game_step.md)。
### 3.2 创建module
游戏代码必须在对应的`module`下编写，以`抢红包`为例，它的`module`名为`redEnvelopes`。在创建组件类或者其他源码文件，必须包含`module`声明。
```ts
module redEnvelopes {
  export class Config {
    ...
  }
}
```
### 3.3 合成纹理
游戏会涉及到很多图片，而且大概有70%的图片小于`50kb`。由于浏览器网络请求有并发有限制，若单独请求，可能会造成请求拥挤，导致加载资源时间延长，影响用户体验。游戏发布前，必须将碎图合成纹理。详细操作指南，请查看[生成纹理集操作手册](./guide/generate_sprite_sheet_guide.md)。还有请查看，之前生成纹理集踩过坑，[碎图合成纹理集后，有时无法显示](./project_post_mortem/v1.12.0.md#1.2)。
### 3.4 资源分组
游戏按需运行，所以资源也需要按需加载。资源分组是跟随游戏来划分的，建议是将资源文件(除音频以外的资源)放置在一个分组即可。分组命名规则：
- 以`抢红包`为例，游戏名称为`redEnvelopes`，则资源分组名称为`red_envelopes_preload`
- 其他游戏分组名称依次类推，比如`课堂题`的名称为`lecturesQuestion`，则资源分组名称为`lectures_question_preload`。
### 3.5 音频加载
音频文件不能放置在资源分组里加载，切记！具体原因，请查看[音频文件加载阻塞导致游戏无法弹起](./project_post_mortem/v1.12.0.md#1.1)。

通过异步单独加载音频。如果是使用时，才加载音频文件，可能导致等待时间较长影响音频播放效果。所以推荐在预加载游戏资源时，异步预加载所有音频文件。在使用时，再请求就会从缓存读取，较少等待时间。具体做法如下：
#### 3.5.1 配置
在游戏资源配置文件，列出需要音频文件，以`抢红包`为例(`redEnvelopes`)，在`common/config.ts`配置音频文件名称：

```ts
// 抢红包游戏
[Config.redEnvelopes]: {
  preload: 'red_envelopes_preload',
  res: 'resource/redEnvelopes.res.json',
  thm: 'resource/redEnvelopes.thm.json',
  // 列出需要的音频文件名称
  sounds: [
    'double_mp3',
    'ludi_mp3',
    'redpacket_open_mp3',
    'box_open_mp3',
    'end_bg_mp3',
    'start_bg_mp3'
  ]
}
```
#### 3.5.2 加载
注意，加载是在`Main.ts`进行的，只要调用`preload`方法，就会执行，不需要单独调用。代码如下：

```ts
private async preload(gameName = common.Config.defaultGameName, isInitGame: boolean = true, success, fail) {
  isInitGame && this.initGame(gameName);
  // 拿到资源配置信息
  const resources = common.Config.gameNameToResourceMap[gameName];
  try {
    await RES.loadConfig(resources.res, "resource/");
    // 异步加载音频文件，注意这里不会导致阻塞
    this.preloadSounds(resources.sounds);
    await common.Utils.loadTheme(resources.thm, this.stage, this);
    await RES.loadGroup(resources.preload, 0);
    success && success(gameName);
  } catch (e) {
    fail && fail(gameName, e);
    console.error('redEnvelopes-loadResourcesFail', JSON.stringify(e));
  }
}

private preloadSounds(soundsName: string[] = []) {
  // 使用RES.getResAsync异步加载音频文件
  soundsName.forEach((soundName) => { RES.getResAsync(soundName); });
}
```
#### 3.5.3 使用
建议在`Scene`或`Sprites`组件初始化时，就实例化音频对象。以`抢红包`的`sprites/DoubleShine.ts`为例：

```ts
private doubleSound: egret.Sound;
private doubleSoundChannel: egret.SoundChannel;

public constructor() {
  ...
  this.doubleSound = RES.getRes('double_mp3'); // 初始化音频实例
}

public play() {
  ...
  if (this.doubleSound) {
    this.doubleSoundChannel = this.doubleSound.play(0, 1); // 播放音频
  }
}
```
### 3.6 释放资源
游戏结束后，应该释放资源，不然可能会内存泄漏，CPU和内存居高不下，严重的话导致客户端崩溃。[前面](#34)已提到，只划分一个资源分组，所以销毁资源时，只要销毁一个资源组即可。以`抢红包`为例：

```ts
private onLeaveSceneFinished() {
  ...
  const gameId = common.Config.redEnvelopes;
  ...
  const resources = common.Config.gameNameToResourceMap[gameId]; // 拿到资源配置信息
  ...
  resources && resources.preload && RES.destroyRes(resources.preload); // 销毁资源
}
```
### 3.7 事件处理
当对各种对象(尤其是动画组`TweenGroup`)绑定事件，当使用完成，必须清除事件，防止内存泄漏。这里列举比较常见事件处理的场景。
#### 3.7.1 帧事件
具体介绍，请查看[帧事件的使用](http://developer.egret.com/cn/article/index/id/875)。该方法在需要精准控制动画时使用，以`common/components/AirShipTracker.ts`为例：

- 监听`帧事件`

```ts
private timestamp: number;

// 开玩
private play() {
  this.timestamp = egret.getTimer();
  egret.startTick(this.onStartTick, this);
}

private onStartTick(currentTimestamp: number): boolean {
  const times = currentTimestamp - this.timestamp; // 获取时间间隔
  this.timestamp = currentTimestamp; // 存放当前时间戳
  this.airShips.forEach((airShip) => {
    if (!airShip.canFly || airShip.isFadeAway) { return; }
    airShip.floatAirShip(times * airShip.speed); // 时间*速度=飞船的飞行距离
    if (this.isTimeToFadeAway(airShip)) { return; }
    airShip.canFadeAway = true;
    airShip.fadeAway();
  });
  return false;
}
```

- 清除`帧事件`：使用完成后，必须清除，不然会导致内存泄漏

```ts
public stopTick() {
  egret.stopTick(this.onStartTick, this); // 清除计时
}
```
#### 3.7.2 Timer计时器
具体介绍，请查看[Timer计时器](http://developer.egret.com/cn/article/index/id/154)。该方法一般用来倒计时。以`games/lecturesQuestion/scenes/SettlementScene.ts`为例：

- 开始计时

```ts
private timer: egret.Timer;
private timerDelay: number = 1000;
private timerRepeatCount: number = 10;

private initTimer() {
  this.timer = new egret.Timer(this.timerDelay, 0);
  this.timer.addEventListener(egret.TimerEvent.TIMER, this.onTimeCount, this);
  this.timer.start();
}

private onTimeCount(e: egret.TimerEvent) {
  const timer = <egret.Timer>e.target;
  const remainCount = this.timerRepeatCount - timer.currentCount;
  if (remainCount > 0) { return; }
  this.fadeAway();
}
```

- 停止计时：使用完成后需要停止，并且清除事件绑定，不然会导致内存泄漏

```ts
private clearTimer() {
  this.timer && this.timer.stop();
  this.timer && this.timer.removeEventListener(egret.TimerEvent.TIMER, this.onTimeCount, this);
}
```
#### 3.7.3 对象事件
对象的范围就大了，可能是EUI组件，可能是动画组(`TweenGroup`)等等。因动画组(`TweenGroup`)使用较广，就以它为例，`games/redEnvelopes/scenes/OpeningScene.ts`

- 监听动画播放完成

```ts
private openScreen: egret.tween.TweenGroup;

protected childrenCreated ():void {
  super.childrenCreated();
  // 监听开场动画组播放完成事件
  this.openScreen.addEventListener('complete', this.onTweenGroupComplete, this);
  // 播放开场动画
  this.openScreen.play(0);
}

// 开场动画播放完成
private onTweenGroupComplete () {
  const event: common.CustomerEvent = new common.CustomerEvent(common.CustomerEvent.OPENING_SCENE_ANIMATION_FINISHED);
  common.EventManager.dispatchEvent(event); // 广播开场动画播放结束事件
}
```

- 清除事件：使用完成后，必须清除，否则会导致内存泄漏

```ts
public clear () {
  this.openScreen.removeEventListener('complete', this.onTweenGroupComplete, this);
}
```
### 3.8 实现clear
各个EUI组件必须实现`clear`方法，该方法实现组件内的解绑事件，清除`Timer`，释放引用等逻辑。当对象被弃用或销毁时，必须先调用该方法。这样可有效防止内存泄漏。以`抢红包`为例：

- 红包`Sprite`：

```ts
// 打开红包动效,普通红包和翻倍红包共用一套皮肤
private openRedPacket: redEnvelopes.OpenRedPacket;
// 打开宝箱
private openBox: redEnvelopes.OpenBox;

// 露迪对象
private luDi: redEnvelopes.LuDi;
// 打开露迪动效
private openLuDi: redEnvelopes.OpenLuDi;

public clear() {
  if (this.openRedPacket) {
    this.openRedPacket.clear();
    this.openRedPacket = null;
  }
  if (this.luDi) {
    this.luDi.clear();
    this.luDi = null;
  }
  if (this.openLuDi) {
    this.openLuDi.clear();
    this.openLuDi = null;
  }
  if (this.openBox) {
    this.openBox.clear();
    this.openBox = null;
  }
}
```

- 打开红包`Sprite`：

```ts
private openRedPacket: egret.tween.TweenGroup;
private redPacketOpenSound: egret.Sound;
private redPacketOpenSoundChannel: egret.SoundChannel;

public clear() {
  // 清除音频
  this.clearSound();
  // 解绑动画组事件
  this.openRedPacket.removeEventListener('complete', this.onCompleted, this);
}

private clearSound() {
  if (this.redPacketOpenSoundChannel) {
    this.redPacketOpenSoundChannel.stop();
    this.redPacketOpenSound = null;
    this.redPacketOpenSoundChannel = null;
  }
}
```

后面的几个`Sprite`也都各自实现了`clear`，篇幅有限，不在这里贴代码。感兴趣的，请查看源代码。

- 打开宝箱`Sprite`：
- 路迪`Sprite`：
- 打开路迪`Sprite`：
### 3.9 读取EUI Component的皮肤元素
`EUI`组件一般由两部分组成：负责逻辑处理事件绑定的`Component Class`；负责布局样式布局的`EXML`。对`EUI`的详细介绍，请查看[课中互动开发-白鹭引擎（开发篇）之EUI](./guide/develop_game_by_egret.md#4)。

当然`Component Class`需要获取`EXML`声明的元素对象时，必须放在`childrenCreated`方法进行，以`抢红包`为例：

- `EXML`皮肤文件里有个`id`为`countDown`的`BitmapLabel`元素

```xml
<?xml version="1.0" encoding="utf-8"?>
<e:Skin class="DecorateRedPacket" width="1720" height="1040" xmlns:e="http://ns.egret.com/eui" xmlns:w="http://ns.egret.com/wing" xmlns:tween="egret.tween.*">
	<e:BitmapLabel id="countDown" width="69" height="54" x="851" y="54" anchorOffsetX="0" anchorOffsetY="0" font="daojishi_fnt" text="03"/>
</e:Skin>
```

- `Component Class`获取名称为`countDown`的`BitmapLabel`对象：

```ts
private countDown: eui.BitmapLabel;
private timerDelay: number = redEnvelopes.Config.TIME_COUNT_DELAY; // 计时器事件间的延时，ms
private timerRepeatCount: number = redEnvelopes.Config.TIME_COUNT_REPEAT; // 计时器运行总次数
private warningCountMap = { 0: 'a', 1: 'b', 2: 'c', 3: 'd', 4: 'e', 5: 'f' }; // Bitmap Font映射关系

protected childrenCreated():void {
  super.childrenCreated();
  this.countDown.text = this.formatCountDown(this.timerRepeatCount); // 设置BitmapLabel对象countDown的值
}

private formatCountDown(count: number): string {
  const isWarning: boolean = count < 6; // 5秒开始变黄提醒
  const text: string = count < 10 ? `0${count}` : `${count}`;
  return isWarning ? text.split('').map(number => this.warningCountMap[number]).join('') : text;
}
```
### 3.10 提供测试脚本
游戏开发完成后，需提供独立于接口，独立于客户端，可运行的测试脚本。以游戏命名的下划线分割命名的测试文件，放置在`docs/local_debug`目录下，以抢红包为例，命名为`redEnvelopes`，测试脚本文件为[red_envelopes.md](./local_debug/red_envelopes.md)。

```ts
// 必选命令

// 预加载资源
window.preload(2, false, () => {
  // 初始化游戏
  window.initGame(2);
  // 模拟答题信号
  window.__test.TestUtils.mockEnterMachine();
  // 传入游戏初始化配置数据
  window.__test.TestUtils.initConfig({
    "redEnvelopeGameId": "a7f2855ae243400da865b75a60ce230b",
    "redEnvelopeVoList": [
      {
          "redEnvelopeId": "0-0",
          "redEnvelopeType": 1,
          "integral": 5,
          "status": 2
      },
      {
          "redEnvelopeId": "1-0",
          "redEnvelopeType": 1,
          "integral": 10,
          "status": 2
      },
      {
          "redEnvelopeId": "2-0",
          "redEnvelopeType": 1,
          "integral": 10,
          "status": 2
      },
      {
          "redEnvelopeId": "3-0",
          "redEnvelopeType": 1,
          "integral": 5,
          "status": 2
      },
      {
          "redEnvelopeId": "4-0",
          "redEnvelopeType": 1,
          "integral": 5,
          "status": 2
      }
    ],
    "backupRedEnvelopeVoList": [],
    "classId": "2c96844676229691017622efdda901e3",
    "courseId": "MIN000000719513",
    "redPacketCount": 1
  });
  // 模拟游戏端接收到消息推送
  window.receiveMessageFromGame = (e) => {
    const { data, type, namespace } = e;
    if (!type || namespace !== 'from-games-platform') { return; }
    if (type === 'submit-score') {
      return handleSubmitScore();
    }
  };
  // 模拟获取抢红包游戏结果
  const handleSubmitScore = () => {
    return window.__test.TestUtils.submitScore([
      {
        "studentExtId": "STU200817000032",
        "studentName": "张三",
        "point": 15,
        "labelTypeList": [
          {
            "labelType": 2,
            "labelTypeValue": "手气王"
          }
        ],
        "isLuckyStar": false,
        "index": 1
      },
      {
        "studentExtId": "STU200811000002",
        "studentName": "爱迪生爱迪",
        "point": 10,
        "labelTypeList": [],
        "isLuckyStar": false,
        "index": 2
      }
    ]);
  };
  // 开始游戏
  window.start();
});

// 可选命令
// 结束锚点进入结算页
window.__test.TestUtils.enterSettlement();
```
### 3.11 输出复盘文档
游戏每次版本迭代过程中，应该会遇到新问题，有些新想法。是的，我们建议把它们记下来，积累下来，把它们输出到迭代复盘文档。回顾的过程，既可以锻炼思维和语言组织能力，又可以帮助到其他项目成员学习经验，是双赢的做法。所以，我们强烈希望，完成迭代后，将复盘文档输出放置在`docs/local_debug/project_post_mortem`
，以版本号命名，比如`v1.12.0`版本，则文档名称为[v1.12.0.md](./project_post_mortem/v1.12.0.md)
## 4 优先级B
### 4.1 划分场景
如果把游戏类比话剧，它有分幕。游戏分幕就是分场景(`Scene`)，它由多个场景组成。合理地划分场景，可更好控制逻辑和维护代码。一般的，会有以下场景：

- 开场场景：`OpeningScene`
- 结算场景：`SettlementScene`
- 离场场景：`LeaveScene`
### 4.2 划分Sprites
话剧会有道具，演员等，对应游戏就是各种对象(尤其是`EUI`对象)，称之为`Sprites`。可以把`Sprite`当作`Scene`功能的基本单位，是的，`Scene`的功能可以细分派发到对应的`Sprite`上。`Scene`当作游戏容器的基本单位，起到粘合各个`Sprite`对象的作用。我们可以把这个过程，当作组件化思路在游戏上的实现。这个道理是相通的。

所以，建议尽可能把组件粒度控制在合理的水平，可极大提升代码的复用性和可维护性。
### 4.3 配置参数
游戏会涉及到各种配置参数，不要将这些参数散落在各个对象里。因为有可能，这些参数由后台动态传入，到时就不好修改和维护。我们的建议是，尽量将游戏使用到的配置数据，放置在统一地方，并且使用常量描述它，添加必要注释。我们建议是放在游戏目录的`config.ts`文件里，以`抢红包`为例，[games/redEnvelopes/config.ts]：

```ts
module redEnvelopes {
  export class Config {
    public static MAX_ROUND_COUNT: number = 5; // 红包轮数
    public static TRACK_GUTTER_COUNT: number = 4; // 跑道于舞台左右间距的判断基数
    public static TOUCH_BOTTOM_GUTTER: number = 110; // 触底间距
    public static MIN_TRACK_COUNT: number = 4;
    public static MAX_TRACK_COUNT: number = 7; // 最多跑道数
    public static TIME_COUNT_DELAY: number = 1000; //计时器事件间隔，单位ms
    public static TIME_COUNT_REPEAT: number = 16/*13*/; // 计时器运行总次数
    public static ROUND_INTERVAL_TIME: number = 3000; // 没轮红包间隔时间，单位ms
    public static OPEN_RED_PACKET_FROM_BOX_TIME: number = 100; // 爆裂红包被分配打开时间，单位ms
    public static TRACK_GUTTER_LESS_COUNT: number = 320; // 红包跑道少于等于n个，跑道和舞台左右间距
    public static TRACK_GUTTER_MORE_COUNT: number = 200; // 红包跑道大于n个，跑道和舞台左右间距
    public static TRACK_GUTTER_TWO_COUNT: number = 574; // 红包跑道等于2个，跑道和舞台左右间距
    public static RED_PACKET_SIZE = { width: 154, height: 245 }; // 红包尺寸
    public static RED_PACKET_OPENED_BOX_SIZE = { width: Config.RED_PACKET_SIZE.width * 0.9, height: Config.RED_PACKET_SIZE.height * 0.9 }; // 爆裂红包尺寸
    public static RUN_OVER_TIMES = [1.8, 1.9, 2.0, 2.1, 2.25, 2.34, 2.4, 2.5, 2.65]; // 跑道运动时长，单位秒
    public static OPEN_TIMES = [1150, 1200, 1300, 1400, 1500, 1600, 1700, 1750]; // 红包可被打开时间
    public static MIN_RUN_OVER_TIME = 1.8; //跑道运动最短时间
    public static MAX_RUN_OVER_TIME = 2.65; //跑道运动最长时间

    public static RED_ENVELOPE_ID: string; // 红包游戏唯一id
    public static CLASS_ID: string | number; // 班次id
    public static COURSE_ID: string | number; // 课次id
    public static RED_PACKET_COUNT: number = 0; // 每轮红包数量
    public static BACKUP_RED_ENVELOPE_LIST: redEnvelopes.RedPacketVO[] = []; // 宝箱红包列表
    public static RED_ENVELOPE_LIST: redEnvelopes.RedPacketVO[] = []; // 宝箱红包列表
  }
}
```
## 5 优先级C
待补充
## 6 结语
该文档应该会持续更新，因为还有些我们没有遇到坑，还没摸索出最佳实践的点。这只是开始，经过项目地历练，会有更多地想法，希望大家将它们积累下来、写下来，给后来者铺平道路，提升团队开发质量和效率。