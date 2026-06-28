# Headless Machine Mode（D-017）

> hotelbyte fork 的二次开发：让 hermes-workspace 桌面 app 以**后台 headless Machine** 形态运行。
> 对应 hotel-be notebook 决策 **D-017**（`docs/architecture/hermes-loop/2026-06-28-hermes-loop-roadmap-investigation.md`）。

## 为什么

loop.pingkai.cn 的模型是「一个桌面 app = 一个 Machine」。但 MVP 阶段不需要桌面 app 自带完整本地 UI——用户交互全部走 `loop.hotelbyte.com` 中央控制台（D-011/D-012）。

因此桌面 app 收敛为**纯后台 agent runtime**：

- 不创建 `BrowserWindow`
- 仍启动内嵌 local server / gateway（agent runtime 依赖）
- macOS 隐藏 Dock 图标
- 进程常驻，不因「无窗口」退出

好处：实现最简、状态单真相源（中央服务器）、与「控制台可见」理念天然一致。

## 怎么用

```bash
# 环境变量
LOOP_HEADLESS=1 electron .

# 或 CLI flag
electron . --headless
```

启动后日志会出现：

```
[loop] headless machine mode active (D-017); BrowserWindow suppressed
```

## 改了什么

仅 `electron/main.cjs`：

1. 新增 `LOOP_HEADLESS` 开关（env 或 argv）
2. `app.whenReady`：headless 时调 `startLocalServer()` 但跳过 `createWindow()`，并 `app.dock.hide()`
3. `window-all-closed`：headless 时不再触发退出

## 不在本次范围

- 连接 `loop.hotelbyte.com` 中央服务器（Machine 客户端、指令队列、事件流）——后续 M2
- AgentView 脱敏投影推送——后续 M2
- 本地状态存储（AgentState）——依赖 hermes-loop runtime，M2

## 验证

- `node --check electron/main.cjs` ✅
- 真实 Electron 启动验证需桌面环境（CI / 本地 macOS），本次仅静态校验
