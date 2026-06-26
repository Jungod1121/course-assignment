# Course Assignment — 课程作业自动化

<p align="center">
  <img src="assets/banner.png" alt="Course Assignment — 奶龙备战期末周" width="600">
</p>

<p align="center">
  <a href="https://claude.ai/code"><img src="https://img.shields.io/badge/Claude_Code-Skill-4da6ff?logo=claude&logoColor=white" alt="Claude Code Skill"></a>
  <a href="https://github.com/Jungod1121/html2docx"><img src="https://img.shields.io/badge/submodule-html2docx-blue" alt="html2docx"></a>
  <a href="https://github.com/op7418/Humanizer-zh"><img src="https://img.shields.io/badge/submodule-Humanizer--zh-8A2BE2" alt="Humanizer-zh"></a>
  <a href="https://www.python.org/"><img src="https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white" alt="Python"></a>
  &nbsp;
  <a href="https://github.com/Jungod1121/course-assignment/stargazers"><img src="https://img.shields.io/github/stars/Jungod1121/course-assignment?style=social" alt="GitHub stars"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-yellow.svg" alt="License: MIT"></a>
</p>

从作业要求到 DOCX 报告的完整流水线。Actor 写 → Critic 审 → 去 AI 味 → Critic 复核 → DOCX。

## 架构

```
Phase 1-3: 读题 + 选语言 + 跑代码
Phase 4:   Web 搜索（中英文并行，GB/T 7714 引用）
Phase 5:   Actor 生成 HTML → 🛑 Critic Agent 独立审查 → 用户审阅
Phase 6:   去 AI 味（Humanizer-zh） → 🛑 Critic Agent 复核技术完整性
Phase 7:   html2docx 转 DOCX
```

两条 Critic 门禁是质量核心——写报告的人和审查报告的人不能是同一个人（角色）。Actor 写的时候沉浸于组织内容容易忽略遗漏，Critic 独立从挑刺角度审视，能抓到 Actor 看不到的问题。去味后的第二轮复核确保"去 AI 味"没有把公式、数据、专业术语改坏。

## 功能

- 接收作业要求（文字/PDF/截图/`task.md`）
- 交互式确认是否需要代码、用什么语言（Python / MATLAB / C++ / ...）
- 自动编写代码、运行、捕获输出和图表
- **Web 搜索增强**：中英文并行搜索，所有数据有据可查，不凭空编造
- **GB/T 7714-2015** 参考文献格式
- 生成含公式/图表/代码附录的 HTML 报告
- **Critic Agent** 独立审查 + 用户审阅双重把关
- 内置 AI 味检测与去味（Humanizer-zh + 纯规则 fallback）+ 去味后技术复核
- 通过 html2docx 转换为排版好的 DOCX（封面+目录+页码+原生 OMML 公式）

## 安装

```bash
# 一键克隆（含全部依赖）
git clone --recursive https://github.com/Jungod1121/course-assignment.git ~/.claude/skills/course-assignment
```

如果已经克隆忘了加 `--recursive`：
```bash
cd ~/.claude/skills/course-assignment
git submodule update --init --recursive
```

**前置依赖**：

| 依赖 | 说明 |
|------|------|
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

然后粘贴/描述作业要求（也可以先在目录下创建 `task.md` 写详细要求）。技能会依次：

1. 确认课程名称、作业标题、学生信息
2. 询问是否需要写代码、用什么语言
3. 编写并运行代码，保存结果
4. **Web 搜索**，中英文并行，用真实数据写报告
5. Actor 生成 HTML 报告 → **Critic Agent 独立审查** → 给你审阅
6. 去 AI 味 → **Critic Agent 复核技术完整性**
7. 转换为 DOCX

两条 Critic 门禁：一轮查任务完成度和数据准确性，二轮查去味有没有改坏公式/数据/术语。

## 文件结构

```
~/.claude/skills/course-assignment/
├── SKILL.md                    # 主技能文件
├── templates/
│   └── report.html             # HTML 报告模板
├── lib/                        # git submodules
│   ├── html2docx/              # HTML→DOCX 转换器
│   └── humanizer-zh/           # 中文 AI 味检测与去味
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
