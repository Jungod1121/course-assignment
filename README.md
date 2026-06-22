# Course Assignment — 课程作业自动化

从课程作业要求到 DOCX 报告的完整流水线。

## 功能

- 接收作业要求（文字/PDF/截图）
- 交互式确认是否需要代码、用什么语言（Python / MATLAB / C++ / ...）
- 自动编写代码、运行、捕获输出和图表
- 生成含公式/图表/代码附录的 HTML 报告
- 内置 AI 味检测与去味，让报告读起来像真人写的
- 通过 html2docx 转换为排版好的 DOCX（封面+目录+页码+原生 OMML 公式）

## 安装

```bash
# 克隆到 Claude Code 技能目录
git clone https://github.com/Jungod1121/course-assignment.git ~/.claude/skills/course-assignment
```

**前置依赖**：

| 依赖 | 说明 |
|------|------|
| [html2docx](https://github.com/Jungod1121/html2docx) | HTML→DOCX 转换器，必须安装到 `~/.claude/skills/html2docx/` |
| [Humanizer-zh](https://github.com/op7418/Humanizer-zh) | 中文 AI 味检测与去味，必须安装到 `~/.claude/skills/humanizer-zh/` |
| Python 3 | numpy, scipy, matplotlib, python-docx, beautifulsoup4, lxml, latex2mathml, pillow |
| MATLAB (可选) | 用于控制系统/信号处理类作业，需配置 MCP |

```bash
# Python 依赖
pip3 install numpy scipy matplotlib python-docx beautifulsoup4 lxml latex2mathml pillow
```

## 使用方法

```
/course-assignment
```

然后粘贴/描述作业要求。技能会依次：
1. 确认课程名称、作业标题、学生信息
2. 询问是否需要写代码、用什么语言
3. 编写并运行代码，保存结果
4. 生成 HTML 报告
5. 检查 AI 味并去味
6. 转换为 DOCX

## 文件结构

```
~/.claude/skills/course-assignment/
├── SKILL.md                  # 主技能文件
├── templates/
│   └── report.html           # HTML 报告模板
├── LICENSE
└── README.md
```

## 输出产物

```
<作业名>/
├── report.html               # HTML 报告
├── report.docx               # Word 文档（最终交付物）
├── convert.py                # DOCX 转换脚本
├── generate_docx.py          # 转换器（从 html2docx 复制）
├── mml2omml.py               # 公式引擎（从 html2docx 复制）
├── figures/                  # 图表 PNG
└── code/                     # 源代码 + 运行输出
```

## 支持的语言

| 语言 | 执行方式 |
|------|------|
| Python | `python3` |
| MATLAB | MCP (`mcp__matlab__run_matlab_file`) |
| C / C++ | `gcc` / `g++` 编译运行 |
| 无代码 | 直接生成报告 |

## License

MIT
