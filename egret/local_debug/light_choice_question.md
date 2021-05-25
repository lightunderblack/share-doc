# 主观题答题互动本地调试

```ts
// 必选命令

// 预加载资源并且初始游戏
window.preload(503, false, () => {
  window.initGame(503);
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
      "options": [
        { "option": "A", "desc": "选项A！"},
        { "option": "B", "desc": "这是选项B~"},
      ],
      "rightAnswer": 'B', // AB都对 "A,B"
      "startAnswerTime": 1607063400729
    }
  });

  // 启动游戏
  window.start();
});

// 结束锚点进入结算页
window.__test.TestUtils.enterSettlement();
```
