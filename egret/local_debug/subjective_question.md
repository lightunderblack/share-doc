# 主观题答题互动本地调试

```ts
// 必选命令

// 预加载资源并且初始游戏
window.preload(502, false, () => {
  window.initGame(502);
  // 模拟答题信号
  window.__test.TestUtils.mockEnterMachine();
  // 初始化游戏配置信息
  window.__test.TestUtils.initConfig({
    "classId": "2c9685d0762c2d7001762c6887b200fe",
    "courseId": "MIN000000719823",
    "boundStudentMachineList": [
      {
          "studentId": "STU200811000002",
          "studentName": "爱迪生爱迪",
          "bound": true,
          "machineId": "135503911",
          "point": 16975
      },
      {
          "studentId": "STU200817000032",
          "studentName": "张三",
          "bound": true,
          "machineId": "1339107945",
          "point": 13324
      }
    ],
    "anchor": {
      "beginTime": 75.525,
      "type": 8,
      "duration": 30,
      "dMin": 0,
      "dSec": 30,
      "questionId": 1675,
      "idx": 4,
      "cutscene": 13,
      "isDoing": true
    },
    "question": {
      "questionId": 1675,
      "questionType": 7,
      "questionContent": "编号2-1、编号2-2",
      "options": [],
      "rightAnswer": "",
      "startAnswerTime": 1607063400729
    }
  });
  // 模拟游戏端接收到消息推送
  window.receiveMessageFromGame = (e) => {
    const { data, type, namespace } = e;
    if (!type || namespace !== 'from-games-platform') { return; }
    if (type === 'get-result') {
      return handleSubmitScore();
    }
  };
  // 模拟获取游戏结果
  const handleSubmitScore = () => {
    return window.__test.TestUtils.submitScore({
      "rightStudentAnswers": [
          {
            "studentId": "STU200817000032",
            "studentName": "张三",
            "answer": null,
            "rightFlag": 1,
            "costTime": 0,
            "point": 20,
            "correctCostTime": 31532
          },
          {
            "studentId": "STU200811000002",
            "studentName": "爱迪生爱迪",
            "answer": null,
            "rightFlag": 1,
            "costTime": 0,
            "point": 20,
            "correctCostTime": 32825
          }
      ],
      "committedNumStudentAnswers": [],
      "unCommittedNumStudentAnswers": [
        {
          "studentId": "STU200817000034",
          "studentName": "李商隐李商隐李商隐李商隐",
          "answer": null,
          "rightFlag": -1,
          "costTime": 0,
          "point": 0,
          "correctCostTime": null
        }
      ]
    });
  };
  // 启动游戏
  window.start();
});


// 可选命令
// 加时
window.__test.TestUtils.addTime(5518.8); // 单位秒

// 结束锚点进入结算页
window.__test.TestUtils.enterSettlement();
```