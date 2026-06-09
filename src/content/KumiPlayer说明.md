---
title: KumiPlayer说明
published: 2026-06-09
tags:
  - Markdown
  - 博客
  - 演示
category: 文章示例
draft: false
---
# KumiPlayer

本地影视海报墙播放器。从 115 网盘目录树自动生成 strm 文件，自动刮削 TMDB/Bangumi 元数据和海报，通过 Web UI 海报墙浏览，调用 mpv 播放网盘视频。

---

## 功能一览

|功能|说明|
|---|---|
|strm 文件生成|从 115 导出的目录树生成 strm 文件，0.3 秒完成|
|元数据刮削|TMDB + Bangumi + DeepSeek AI 辅助搜索|
|海报下载|自动下载 poster / fanart / clearlogo|
|Web 海报墙|Vue 3 海报网格 + 详情弹窗 + 分类筛选 + 排序|
|视频播放|mpv 直接播放 strm（指向 H 盘真实视频路径）|
|分类管理|番剧 / 动画电影 / 剧集 / 电影 四类，自动检测 + 手动编辑|
|自定义标签|彩色标签系统，全局标签库快捷选择|
|AI 分析|对刮削失败的作品用 DeepSeek 分析原因|
|播放历史|SQLite 记录播放进度，最近播放横向展示|
|明暗主题|一键切换|
|删除作品|前端直接删除，清理镜像目录 + 标签 + 状态|
|增量更新|对比新旧目录树，只处理变化部分|
|导入目录树|前端上传目录树文件，自动保存并创建镜像|
|搜索功能|导航栏搜索框，支持按作品名/原名/文件夹名搜索|
|最近播放|导航栏按钮切换显示，点击继续播放|
|作品管理|设置页管理失败/待处理作品，支持一键清除媒体库|

---

## 技术栈

|层|技术|
|---|---|
|后端|Python 3.12, FastAPI, Uvicorn|
|前端|Vue 3, Vite 8, TypeScript 6, Pinia 3|
|数据源|TMDB API, Bangumi API, DeepSeek API|
|存储|SQLite（播放历史 + TMDB 缓存），JSON（标签 + 作品状态）|
|播放器|mpv（外部调用）|
|文件上传|python-multipart|

---

## 快速开始

### 安装

# 后端依赖  
pip install -r requirements.txt  
pip install python-multipart  
​  
# 前端构建  
cd frontend  
npm install  
npm run build

### 配置

编辑 `config.json`：

{  
  "mpv_path": "D:\\path\\to\\mpv.exe",  
  "h_drive": "H:\\115open\\动画",  
  "mirror_dir": "D:\\01_Software\\KumiPlayer\\data\\mirror\\test",  
  "tmdb_bearer_token": "你的 TMDB Token",  
  "deepseek_api_key": "你的 DeepSeek API Key"  
}

### 启动

# 方式一：双击 start.bat  
# 方式二：命令行  
python main.py serve

浏览器自动打开 `http://127.0.0.1:8520`

---

## CLI 命令

python main.py mirror full <目录树>           # 全量生成镜像  
python main.py mirror inc <旧树> <新树>       # 增量更新镜像  
python main.py mirror status                  # 查看镜像状态  
python main.py scrape                         # 批量刮削  
python main.py scrape <名称>                  # 刮削指定作品  
python main.py play <strm路径>                # 播放视频  
python main.py library                        # 扫描媒体库  
python main.py search <关键词>                # 搜索作品  
python main.py status                         # 查看作品库状态  
python main.py delete <名称> [--force]        # 删除作品  
python main.py serve [--port=8080]            # 启动 Web 服务

---

## 工作流程

1. 用户在 115 网盘整理文件夹  
2. 导出目录树（115 自带时间戳命名的 txt 文件）  
3. python main.py mirror full <目录树>    → 生成 strm 文件（0.3 秒）  
4. python main.py scrape                 → 刮削元数据 + 下载海报 + 重命名 strm  
5. python main.py serve                  → 启动 Web 海报墙

播放时：strm 文件内容 → H 盘真实视频路径 → mpv 打开 → CloudDrive 挂载的网盘视频

---

## 项目结构

KumiPlayer/  
├── main.py                          # 统一 CLI 入口  
├── start.bat                        # 一键启动  
├── config.json                      # 运行时配置  
├── requirements.txt                 # Python 依赖  
│  
├── kumplayer/                       # 后端源码  
│   ├── config.py                    # 配置管理（带缓存）  
│   ├── logger.py                    # 日志  
│   ├── models.py                    # 数据模型  
│   ├── tree_parser.py               # 目录树解析  
│   ├── name_parser.py               # 文件名解析引擎  
│   ├── cache_manager.py             # SQLite 缓存  
│   ├── show_manager.py              # 作品状态管理  
│   ├── scraper.py                   # 刮削核心  
│   ├── batch_scraper.py             # 批量刮削  
│   ├── mirror.py                    # 镜像生成  
│   ├── library.py                   # 媒体库扫描  
│   ├── player.py                    # mpv 播放  
│   ├── api/  
│   │   ├── app.py                   # FastAPI 实例  
│   │   ├── schemas.py               # Pydantic 模型  
│   │   └── routes/  
│   │       ├── library.py           # 媒体库 CRUD + 标签 + AI 分析  
│   │       ├── scrape.py            # 刮削触发  
│   │       ├── mirror.py            # 镜像生成 + 目录树导入  
│   │       ├── play.py              # 播放 + 历史  
│   │       ├── config.py            # 配置读写  
│   │       └── image.py             # 图片代理  
│   └── services/  
│       ├── history.py               # 播放历史（SQLite）  
│       └── tags.py                  # 自定义标签（JSON）  
│  
├── frontend/                        # Vue 3 前端  
│   └── src/  
│       ├── App.vue                  # 根组件  
│       ├── api/                     # API 客户端  
│       ├── components/              # 组件  
│       │   ├── NavBar.vue           # 导航栏  
│       │   ├── PosterCard.vue       # 海报卡片  
│       │   ├── PosterGrid.vue       # 海报网格  
│       │   ├── DetailModal.vue      # 详情弹窗  
│       │   ├── RecentPlay.vue       # 最近播放  
│       │   └── SettingsPage.vue     # 设置页面  
│       ├── stores/                  # Pinia 状态  
│       ├── views/                   # 页面视图  
│       └── styles/                  # CSS 样式  
│  
├── data/                            # 数据目录  
│   ├── cache/                       # 缓存（SQLite + JSON）  
│   ├── tags.json                    # 自定义标签  
│   └── mirror/test/                 # 媒体库镜像  
│  
└── logs/                            # 日志

---

## 作品分类

|show_type|说明|自动检测|
|---|---|---|
|`anime_series`|番剧|genres 含 Animation + 多集|
|`anime_movie`|动画电影|genres 含 Animation + 单集|
|`live_series`|剧集|无 Animation + 多集|
|`live_movie`|电影|无 Animation + 单集|

---

## 当前状态

### 已完成

- 全部后端功能（镜像、刮削、播放、标签、AI 分析、删除）
    
- Web 海报墙（海报网格、详情弹窗、分类筛选、排序、标签编辑）
    
- 海报卡片：信息叠加在海报左下角
    
- 详情弹窗：横幅图片完整展示 + 内容区在下方
    
- 安全：路径遍历保护、XSS 防护
    
- **导入目录树功能** — 后端 + 前端已完成
    
- **目录树文件管理** — `data/trees/` 目录已创建，文件已迁移
    
- **requirements.txt** — 已包含 `python-multipart`
    

### 待完成

- 暂无
    

---

## API 文档

启动后访问 `http://127.0.0.1:8520/api/docs` 查看 Swagger 文档。