# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

寒假学习大礼包 (Winter Holiday Study Package) - 上海赫贤学校 IGCSE 新生学习资源网站。纯静态 HTML/CSS/JS 项目，无构建工具。

## Development

本地开发只需用浏览器打开 HTML 文件，或使用简单 HTTP 服务器：
```bash
python3 -m http.server 8000
```

## Architecture

### 核心文件

- **index.html** - 主页，展示8个学科的学习资料和作业要求
- **checklist.html** - 交互式任务清单，包含进度追踪、等级系统、徽章成就
- **pdf-viewer.html** - PDF 在线预览器，使用 PDF.js + 阿里云 OSS

### 数据存储

- 任务数据内嵌在 `checklist.html` 的 `tasks[]` 数组中（93 项任务，84 必做 + 9 选做）
- 用户进度通过 `localStorage` 持久化：
  - `hd_homework_progress` - 已完成任务
  - `hd_homework_stats` - 统计数据（连续打卡等）

### 云端同步 (Supabase)

用户进度通过 Supabase 实现多设备同步，配置在 `checklist.html` 中：
- **URL**: `https://kaliqpyebqxjxbxggdyp.supabase.co`
- **Anon Key**: 硬编码在 `checklist.html:1125`
- **User ID**: `family_shared`（固定值，家庭共享模式）
- **策略**: 写时同步（本地操作立即更新 localStorage，异步同步到 Supabase；加载时云端优先）

数据库表：
- `progress` - 任务完成记录（user_id, task_id, completed, completed_at）
- `user_stats` - 用户统计（level, streak_days, last_active, badges, total_completed）

备份：修改任务结构前，先通过 Supabase REST API 导出数据到本地 JSON 文件。

### 设计系统

使用 CSS 变量统一主题（暖色纸张风格）：
- 背景：`--bg-warm`, `--bg-paper`
- 强调色：`--accent-coral`, `--accent-sage`, `--accent-gold`
- 响应式断点：900px, 600px

### PDF 文件

大型 PDF 文件托管在阿里云 OSS（已在 .gitignore 中排除）：
- 域名：`xsha511.oss-cn-shanghai.aliyuncs.com`
- 本地预处理版本存放在 `/pdf-linearized/`

### 作业数据来源

任务内容必须以老师原始 PDF 文档为准，不能仅依赖 `index.html`：
- `英语/春季班英语寒假作业.pdf`
- `中文/中文寒假作业.pdf`
- `数学/上海赫贤高中IG 0580数学作业指南.pdf`
- `综合科学-物理/0653 物理学科预习说明.pdf`
- `综合科学-化学/化学作业说明.pdf`
- `综合科学-生物/文件1-生物寒假作业-必看.pdf`
- `经济/上海赫贤高中IG经济作业指南.pdf`
- `地理/上海赫贤高中4.5年地理寒假作业.pdf`

添加/修改任务时，务必核对原始 PDF 确认必做/选做状态。

## 添加/修改任务

编辑 `checklist.html` 中的 `tasks` 数组：
```javascript
{
    id: 'task_id',
    subject: '英语',  // 学科名称
    title: '任务标题',
    required: true,   // true=必做, false=选做
    date: '2026-02-01',
    time: 30,         // 预计时间（分钟）
    detail: '详细说明'
}
```

## 注意事项

### CSS 透明度与子元素

父元素的 `opacity` 会影响所有子元素。例如 `.badge-item.locked` 设置 `opacity: 0.4` 会导致其子元素 tooltip 也变半透明。解决方案：hover 时恢复 `opacity: 1`。

### 移动端测试

修改 CSS 后务必在手机上测试，电脑正常不代表手机正常。常见问题：
- tooltip 被其他元素遮挡（z-index 或 overflow 问题）
- 透明度导致内容穿透显示

### 代码提交

每次修改代码后自动提交并推送到 GitHub，无需用户提醒。

## 部署

静态文件直接部署到任意托管服务。Git 仓库：`github.com/xsha511/homework-tracker`
