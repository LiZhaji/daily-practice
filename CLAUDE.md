# CLAUDE.md — 每日跟练项目上下文

给未来 Claude 会话的完整背景。更换设备后 `git clone https://github.com/LiZhaji/daily-practice` 即可接续工作。

## 项目目的

用户（GitHub: LiZhaji）每天有规律的锻炼需求，跟练内容来自多个平台（B站、Keep、抖音），此前杂乱无提醒。本项目交付一个统一跟练主页 + 到点提醒体系，已部署上线。

**上线地址**：https://lizhaji.github.io/daily-practice/
**仓库**：https://github.com/LiZhaji/daily-practice（GitHub Pages 开启，main 分根路径）

## 用户画像与约束

- **手机为 iPhone**，访问形态是 Safari → 添加到主屏幕（PWA 式使用，非原生 App）
- 膝盖不好，有专门的膝盖强化训练；早上练八段锦（需要口令视频），另有背部/腿部/手臂动作和口令式动作
- 视频来源：B站、Keep、抖音。B站链接可页内嵌入；Keep/抖音无法嵌入，方案是「跳转原 App 按钮 + 建议替换为可嵌入的同款视频」；后续用户会自己贴链接
- 用户担心的「非联网使用」实际诉求是**加载快**（点开秒开），用 B 站嵌入 + 轻量页面满足，未做完整离线
- 视频链接尚未提供——用户明确说「先做框架，之后自己编辑填入」，页面预置的是示例内容

## 已确认的需求决策（对话中逐条确认）

1. 提醒到点后要**直达具体内容**，不要自己在各个 App 里找
2. 跟练内容**混合形态**：可嵌入的用视频，其余用自建动作清单 + 倒计时 + 语音口令
3. 时间安排**分多个时段**分别提醒；设计上要支持将来扩展为「每天内容有变化」（已实现：每时段可勾选每周重复日）
4. iPhone 上原生 App 需 $99/年开发者账号，用户**选择网页方案起步**；曾问过借开发者账号侧载，已解释成本后放弃
5. 页面要**公开可分享**，且「每个人可以编辑自己的」——已用浏览器 localStorage 实现（数据各自本机，有导出/导入备份功能）
6. 提醒用 iPhone 日历多事件 + URL 附件实现（曾问「日历能一天提醒多次吗」——可以，多事件每日重复）

## 技术实现要点

- 单文件 `daily-practice.html`（约 750 行，无框架无构建），`index.html` 是其同步副本
- localStorage 键：`genlian.data.v1`（清单数据）、`genlian.done.YYYY-MM-DD`（每日完成状态）、`genlian.voice`（口令开关）
- 语音口令：`SpeechSynthesisUtterance`，lang zh-CN；提示音：Web Audio oscillator（880Hz 倒计时末 3 秒、660Hz 完结）
- B站视频页内播放：`player.bilibili.com/player.html?bvid=...`，`bvid()` 函数从 URL 提取 BV 号；非 B 站链接显示跳转按钮
- 屏幕常亮 Wake Lock、可暂停、组间休息自动循环、自动滚动到当前时段（尊重 prefers-reduced-motion）
- 修改 `daily-practice.html` 后必须 `Copy-Item daily-practice.html index.html` 同步再 push（README 有部署命令）

## 环境备忘

- 开发机：Windows 10，PowerShell；git 已配置；GitHub CLI 在 `C:\Program Files\GitHub CLI\gh.exe`（新 shell 的 PATH 里可能没有，用全路径调用），已用账号 LiZhaji 登录
- 本项目 git 提交统一用 `LiZhaji / LiZhaji@users.noreply.github.com` 身份（仓库级未配置，靠 `-c` 参数，建议后续 `git config` 固化）

## 后续可能的方向（用户未承诺，仅备选）

- 用户填入真实视频链接后，可帮助批量替换示例条目
- 国内直连 GitHub Pages 偶尔慢，如体验不好可迁移 Cloudflare Pages
- 若「先进 Safari 再跳转」体验刺痛用户，重新讨论原生 App/侧载路线
- 完整离线缓存（下载视频到本地部署）——版权边界：仅自用缓存，公开页面以 B 站正版嵌入为主
