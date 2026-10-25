
---
name: phone-control
description: 手机操控纪律：无障碍语义树优先、ref 语义点击/输入、动作后必校验、禁止盲点坐标与绕路。
---
# 手机操控流程（DSH 设备控制）

## 固定顺序
1. 会话档位必须是 danger-full-access，否则设备工具一律拒绝（切换入口：会话底部权限芯片 → 完全权限；**不要试图让工具自己提权**，也不要用 Termux/ADB 绕路）。
2. 长流程开始前跑一次 android_env_prepare（关动画 + 启用内嵌 ADB 键盘），之后 dump/tap 更稳。
3. 感知：android_ui_dump（无障碍语义树，首选）→ 若结果是 WebView 容器或目标是 DSH 自己的 Web UI，改用 android_web_dump（DOM 快照）。
4. 卡住时：**先 android_ui_global back**（返回上一级），或 home 回桌面重新进入——子菜单/弹窗/详情页出不来时这是第一步。
5. 动作：android_ui_click / android_ui_input，引用用 ref（id:nN / text:精确文本#k / desc: / rid: / wN / css: / text: / role:）。
6. 校验：工具自带回执（点击回报「已生效 / 未观察到界面变化」；输入回报「回读一致 / 未落地」）——不要假设动作成功。
7. 需要看画面时用 android_screenshot（图像直接随结果返回，不需要再 read_image）。

## 纪律
- 禁止盲点坐标点击；nx/ny 仅作兜底，且必须说明理由。
- 同名节点必须消歧：用 dump 里的 #k 序号（text:设置#2）。
- 抓到的包名与前台不一致时以 dumpsys 为准（uiautomator/无障碍可能抓到覆盖层）。
- 输入只走单次注入 + 回读断言；不要用 keyevent 打字母（中文 IME 会汉字化），不要拆成多段输入。
- dump 失败（重 UI / 播放页常见）时按提示走：先 back 退出重页面，或截图看画面；**不要转去尝试 Termux 或 ADB**（未配对时那条路不存在，只会浪费轮次）。
- 连续两次动作未产生预期变化时停下来重新 dump，并如实汇报当前界面状态，不要继续猜测。
