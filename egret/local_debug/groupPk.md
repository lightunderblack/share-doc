# 分组PK本地调试

```ts
// 必选命令

// 预加载资源并且初始游戏
window.preload(601, false, () => {
  window.initGame(601);
  let scene = 'settlementScene'
  // 分组PK开场（学生分组场景）配置信息
  scene === 'openingScene' && window.__test.TestUtils.initConfig({
    "scene": "openingScene",
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
    "groupPKInfo": {
      "groups": [
        {
          "groupAvatar": "",
          "groupId": 1,
          "groupName": "路迪队",
          "members": [
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "路迪1"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "路迪2"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "路迪3"
            }
          ],
          "pkId": 1,
          "totalScore": 30,
          "winner": false
        },
        {
          "groupAvatar": "",
          "groupId": 2,
          "groupName": "DD队",
          "members": [
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪1"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪2"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪3"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪迪迪迪迪"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪迪迪3"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "AA"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "SDDFG"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪迪迪迪迪迪迪迪迪迪迪迪"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪232343"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪3啊啊啊"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪啊3"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪十三水3"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberType": 1,
              "point": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "3222"
            }
          ],
          "pkId": 1,
          "totalScore": 30,
          "winner": false
        }
      ],
      "pkId": 1,
      "pkStatus": 1,
      "progressBarLen": 50
    }
  });

  // 分组PK 额外结算场景 配置信息
  scene === 'extrsPointScene' && window.__test.TestUtils.initConfig({
    "scene": "extrsPointScene",
    "classId": "2c96844676229691017622efdda901e3",
    "courseId": "MIN000000719513",
    "groupPKInfo": {
      "groups": [
        {
          "groupAvatar": "",
          "groupId": 1,
          "groupName": 1,
          "pkId": 1,
          "preScore": 20,
          "rightMembers": [
            {
              "groupId": 1,
              "memberType": 1,
              "score": 4,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "路"
            },
            {
              "groupId": 1,
              "memberType": 1,
              "score": 4,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "路12"
            }
          ],
          "totalScore": 30
        },
        {
          "groupAvatar": "",
          "groupId": 1,
          "groupName": 1,
          "pkId": 1,
          "preScore": 20,
          "rightMembers": [
            {
              "groupId": 1,
              "memberType": 1,
              "score": 4,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪"
            },
            {
              "groupId": 1,
              "memberType": 1,
              "score": 0,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪1"
            },
            {
              "groupId": 1,
              "memberType": 1,
              "score": 2,
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪2"
            }
          ],
          "totalScore": 30
        }
      ],
      "pkId": 1,
      "pkStatus": 1,
      "progressBarLen": 50
    }
  });


  // 分组PK 额外结算场景 配置信息
  scene === 'settlementScene' && window.__test.TestUtils.initConfig({
    "scene": "settlementScene",
    "classId": "2c96844676229691017622efdda901e3",
    "courseId": "MIN000000719513",
    "groupPKInfo": {
      "classId": 1,
      "courseId": 1,
      "groups": [
        {
          "groupAvatar": "https://resource-teaching.xiaojiaoyu100.com/inclassgame/happyDragon.png",
          "groupId": 1,
          "groupName": "路迪队",
          "members": [
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "路迪A"
            },
            {
              "groupId": 1,
              "isExpert": true,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "路迪B"
            }
          ],
          "pkId": 1,
          "totalScore": 29,
          "winner": true
        },
        {
          "groupAvatar": "https://resource-teaching.xiaojiaoyu100.com/inclassgame/happyDragon.png",
          "groupId": 1,
          "groupName": "dd队",
          "members": [
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪A11"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": true,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": true,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": true,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": true,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            },
            {
              "groupId": 1,
              "isExpert": false,
              "memberId": 1,
              "memberType": 1,
              "point": 0,
              "stuAvatar": "路迪",
              "stuId": "5ce76e475016c800017ddd5c",
              "stuName": "迪B"
            }
          ],
          "pkId": 1,
          "totalScore": 30,
          "winner": true
        }
      ],
      "pkId": 1,
      "pkStatus": 1,
      "progressBarLen": 50,
      "students": [
        {
          "id": "",
          "name": "",
          "point": 0,
          "xfwId": ""
        }
      ]
    }
  });


  // 启动游戏
  window.start();
});
