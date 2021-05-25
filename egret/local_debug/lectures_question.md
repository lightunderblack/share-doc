# 课堂题答题互动本地调试

```ts
// 必选命令

// 预加载资源并且初始游戏
window.preload(504, false, () => {
  window.initGame(504);
  // 模拟答题信号
  window.__test.TestUtils.mockEnterMachine();
  // 初始化游戏配置信息
  window.__test.TestUtils.initConfig({
    "classId": "2c96844676229691017622efdda901e3",
    "courseId": "MIN000000719513",
    "boundStudentMachineList": [
      {
        "studentId": "STU200811000002",
        "studentName": "爱迪生爱迪",
        "bound": true,
        "machineId": "1339107945",
        "point": 16890
      },
      {
        "studentId": "STU200817000032",
        "studentName": "张三",
        "bound": true,
        "machineId": "135503911",
        "point": 13309
      }
    ],
    "anchor": {
      "beginTime": 78.80871,
      "type": 1,
      "duration": 30,
      "dMin": 0,
      "dSec": 30,
      "questionId": 1820209568,
      "subQuestionId": null,
      "idx": 3,
      "cutscene": 13,
      "isDoing": true
    },
    "question": {
      "questionId": 1820209568,
      "subQuestionId": 0,
      "trueOrFalseQuestionFlag": false,
      "startAnswerTime": 1606904218279
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
      "rightAnswer": "B",
      "stuAnswerInfo": [
        {
          "studentId": "STU200811000002",
          "studentName": "爱迪生爱迪",
          "studentAnswer": "B",
          "answerStatus": 1,
          "studentCostTime": "13",
          "upCount": 0,
          "isFirstUp": false,
          "point": 20,
          "extraPoint": 0,
          "rank": 2,
          "firstUp": false
        },
        {
          "studentId": "STU200817000032",
          "studentName": "张三",
          "studentAnswer": "B",
          "answerStatus": 1,
          "studentCostTime": "12",
          "upCount": 0,
          "isFirstUp": false,
          "point": 30,
          "extraPoint": 0,
          "rank": 1,
          "firstUp": false
        }
      ],
      "rightStuAnswerInfo": [
        {
          "studentId": "STU200817000032",
          "studentName": "张三",
          "studentAnswer": "B",
          "answerStatus": 1,
          "studentCostTime": "12",
          "upCount": 0,
          "isFirstUp": false,
          "point": 30,
          "extraPoint": 0,
          "rank": 1,
          "firstUp": false
        },
        {
          "studentId": "STU200811000002",
          "studentName": "爱迪生爱迪",
          "studentAnswer": "B",
          "answerStatus": 1,
          "studentCostTime": "13",
          "upCount": 0,
          "isFirstUp": false,
          "point": 20,
          "extraPoint": 0,
          "rank": 2,
          "firstUp": false
        }
      ],
      "inClassSelectedInfo": [
        {
          "choiceName": "A",
          "choiceSelectedNum": 0,
          "choiceSelectedPercent": 0,
          "choiceStatus": 0
        },
        {
          "choiceName": "B",
          "choiceSelectedNum": 2,
          "choiceSelectedPercent": 0.09,
          "choiceStatus": 1
        },
        {
          "choiceName": "C",
          "choiceSelectedNum": 0,
          "choiceSelectedPercent": 0,
          "choiceStatus": 0
        },
        {
          "choiceName": "D",
          "choiceSelectedNum": 0,
          "choiceSelectedPercent": 0,
          "choiceStatus": 0
        },
        {
          "choiceName": "unSelected",
          "choiceSelectedNum": 21,
          "choiceSelectedPercent": 0.91,
          "choiceStatus": -1
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