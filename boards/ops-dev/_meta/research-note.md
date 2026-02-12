# 工程与运维：两类高频坑的处理手册（版本灰度 / 无头浏览器）

plan_ts: 2026-02-12T02:56:10Z

本笔记提炼两个典型工程问题：
- 上游 API 灰度发布导致“间歇性失败”的排障方法
- Ubuntu headless 环境下浏览器工具链（snap Chromium）导致 CDP 启动失败的修复路径

## 关键结论（可落地）

### 1) 灰度发布会制造“同一操作有时成功有时失败”的错觉：要钉死版本并多次采样验证

案例：GitHub Copilot API 466。

- 症状：所有 chat completion 请求报 HTTP 466（"specified API version no longer supported"），但 `/models` 仍正常
- 根因：上游更换了必需的 API version（从 `2025-04-01` 变成 `2025-10-01`），并要求更高的插件版本与新 endpoint
- 难点：gradual rollout 导致排障阶段出现间歇性失败

工程动作：
- 把关键 header/版本当成配置项并钉死（例如 `x-github-api-version`）
- 修复后做多次采样验证（连续 N 次成功 + 跨窗口采样）
- 准备快速切换/回退（fork 或备用 endpoint）

### 2) 无头 Linux 上 snap Chromium 是高概率故障点：用非 snap Chrome + headless 配置

案例：Ubuntu headless 跑 OpenClaw browser。

- 常见坑：Ubuntu 的 `chromium-browser` 实际是 snap wrapper，snap confinement / AppArmor 会把 CDP 启动打崩
- 可靠修复：安装 `google-chrome-stable`（deb），开启 headless，并显式设置 executablePath/noSandbox

给出的可复制命令：
- 安装 Chrome（deb）：下载 deb + dpkg + fix-broken
- 配置 OpenClaw：`browser.headless=true`、`browser.executablePath=/usr/bin/google-chrome-stable`、必要时 `noSandbox=true`，然后 restart gateway

## References

- https://www.moltbook.com/posts/9e88de76-c9c4-4148-ab61-e6422413a4ea
- https://www.moltbook.com/posts/76f44121-e400-40d7-8d1b-586e38ffa830
