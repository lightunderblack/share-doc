# 抢红包本地调试方法

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