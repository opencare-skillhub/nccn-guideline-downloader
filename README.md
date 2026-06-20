# NCCN Guideline Downloader Skill

独立部署的 NCCN 临床指南下载技能，支持 65 种癌种、中英文筛选、PDF 清单选择。

## 功能特性

- **6 大主题**：癌症治疗指南、支持性护理、患者指南（英文/中文翻译/中文版本）
- **65 种癌种**：内置完整癌种列表，支持中英文关键词自动匹配
- **智能筛选**：语言筛选（中/英/其他/全部）+ 癌种筛选
- **PDF 清单选择**：解析后展示编号列表，支持 `1,3,5-8` 范围选择
- **跨语言匹配**：输入中文关键词自动扩展为英文别名（如"胰腺"→"pancreatic"）
- **安全下载**：域名白名单、PDF 验证、重试机制、断点续传

## 安装

### 1. 安装技能

将本目录复制到技能目录：

```bash
# ZCode/Codex 用户
cp -r nccn-guideline-downloader ~/.agents/skills/
```

### 2. 安装 Python 依赖

```bash
cd ~/.agents/skills/nccn-guideline-downloader
pip install -r scripts/requirements.txt
```

### 3. 配置认证

配置文件放在 `scripts/` 目录下（与下载脚本同级）：

```bash
cp assets/config.json.template scripts/config.json
# 编辑 scripts/config.json 填入你的 NCCN 认证信息
```

支持两种认证方式：
- **Cookie（推荐）**：设置 `method: "cookie"`，将 `extracted_cookies.txt` 放在 `scripts/` 目录
- **用户名密码**：设置 `method: "username_password"`，填写 `username` 和 `password`

也可通过环境变量配置（优先级更高）：
```bash
export NCCN_AUTH_METHOD="username_password"
export NCCN_USERNAME="your@email.com"
export NCCN_PASSWORD="your_password"
```

## 使用

### 方式一：交互式菜单

```bash
cd ~/.agents/skills/nccn-guideline-downloader
python3 scripts/download_nccn.py
```

### 方式二：通过 AI 助手

在支持技能的 AI 助手中输入：
- "下载胰腺癌的中文患者指南"
- "帮我下载乳腺癌英文治疗指南"
- "有哪些癌种的指南可以下载？"

## 目录结构

```
nccn-guideline-downloader/
├── SKILL.md                    # 技能说明（AI 读取）
├── README.md                   # 本文件
├── .gitignore                  # Git 忽略规则
├── agents/
│   └── openai.yaml             # UI 元数据
├── scripts/
│   ├── download_nccn.py        # 下载主脚本
│   ├── test_offline.py         # 离线测试（42项）
│   ├── requirements.txt        # Python 依赖
│   ├── config.json             # 你的配置（不提交到 Git）
│   └── extracted_cookies.txt   # Cookie文件（不提交到 Git）
├── references/
│   └── cancer_types.md         # 65种癌种列表
├── assets/
│   └── config.json.template    # 配置模板
└── nccn_downloads/             # 下载目录（不提交到 Git）
```

## 测试

运行 42 项离线回归测试：

```bash
python3 scripts/test_offline.py
```

## 安全说明

- `config.json` 和 `extracted_cookies.txt` 已加入 `.gitignore`，不会被提交
- 下载域名白名单限制为 `nccn.org` 及其子域名
- PDF 文件下载后验证 `%PDF` 文件头和最小大小（100KB）

## 版本

- v2.3 — 65种癌种、二级菜单、PDF清单选择、默认英文
- 基于 NCCN 网站 2026 年结构
