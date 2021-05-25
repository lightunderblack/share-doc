# 课前辅导时间本地调试

```ts
// 必选命令

// 预加载资源
window.preload(501, false, () => {
  // 初始化游戏
  window.initGame(501);
  // 初始化游戏配置
  window.__test.TestUtils.initConfig({ maxCountDown: 60 });
  // 启动游戏
  window.start();
});
```