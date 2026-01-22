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

- 任务数据内嵌在 `checklist.html` 的 `tasks[]` 数组中（207 项任务）
- 用户进度通过 `localStorage` 持久化：
  - `hd_homework_progress` - 已完成任务
  - `hd_homework_stats` - 统计数据（连续打卡等）

### 设计系统

使用 CSS 变量统一主题（暖色纸张风格）：
- 背景：`--bg-warm`, `--bg-paper`
- 强调色：`--accent-coral`, `--accent-sage`, `--accent-gold`
- 响应式断点：900px, 600px

### PDF 文件

大型 PDF 文件托管在阿里云 OSS（已在 .gitignore 中排除）：
- 域名：`xsha511.oss-cn-shanghai.aliyuncs.com`
- 本地预处理版本存放在 `/pdf-linearized/`

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

## 部署

静态文件直接部署到任意托管服务。Git 仓库：`github.com/xsha511/homework-tracker`
