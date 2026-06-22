---
name: course-assignment
description: |
  接收大学课程作业要求，自动完成代码编写与运行（Python/MATLAB MCP）、
  结果采集与图表保存，生成含公式/图表/代码附录的 HTML 报告，
  最后通过 html2docx 输出排版好的 DOCX 文档。
  触发词：作业、课程报告、大作业、实验报告、结课作业、仿真、assignment
---

# Course Assignment — 课程作业自动化

从作业要求到 DOCX 报告的完整流水线。5 个阶段，不可跳过。

## 触发条件

用户提供以下任一形式时调用：
- 粘贴/描述作业要求
- PDF 作业文件
- 作业截图/图片
- 明确说「帮我做这个作业」「写实验报告」「/course-assignment」

关键触发词：`作业` `课程报告` `大作业` `实验报告` `结课作业` `仿真` `assignment`

---

## 学生信息

首次使用时向用户确认以下信息，之后可记住复用：

| 字段 | 示例 |
|------|------|
| 姓　　名 | 张三 |
| 学　　号 | 2023300001 |
| 班　　级 | HC002301 |
| 专　　业 | 计算机科学与技术 |

每次开始前确认：**课程名称**、**作业标题**、**学生信息**（首次使用或信息有变化时）。

---

## Phase 1: 读取并解析作业要求 ⛔

| 输入形式 | 处理 |
|------|------|
| 文字粘贴/聊天描述 | 直接解析 |
| PDF 文件 | `Read` 工具完整读取 |
| 截图/图片 | `Read` 工具读取图片内容 |

从作业要求中提取：
1. **作业主题** — 核心问题是什么
2. **具体问题列表** — 几道题、每道题要求什么
3. **格式要求** — 有无字数/页数/公式/图表/代码要求
4. **课程背景** — 什么课、什么专业阶段

**确认**：作业标题、课程名称、输出目录。
默认输出目录：`<当前工作目录>/<作业简称>/`
创建目录后所有产物都在此处。

---

## Phase 2: 工具判定 ⛔

先向用户确认，不要自行决定：

**第一步：询问是否需要代码**
> 这个作业需要写代码吗？

- 用户说**不需要** → 跳到 Phase 4，直接写报告
- 用户说**需要** → 继续第二步

**第二步：询问用什么语言/工具**
> 你希望用什么？Python / MATLAB / C / C++ / 其他？

用户指定后，给出针对性建议：

| 用户选择 | 建议的库/工具 | 执行方式 |
|------|------|------|
| **Python** | numpy, scipy, matplotlib, sympy, sklearn, pytorch | `python3 code/script.py` (Bash) |
| **MATLAB** | Control Toolbox, Simulink, Symbolic Math | `mcp__matlab__run_matlab_file` / `evaluate_matlab_code` |
| **C / C++** | 标准库、Eigen、OpenCV | `gcc/clang` 编译 → 运行可执行文件 (Bash) |
| **其他** (R, Julia, Mathematica...) | 按需 | 相应执行方式 |

**以下决策表作为参考，辅助用户做选择：**

| 作业类型 | 关键词信号 | 推荐工具 |
|------|------|------|
| 纯理论/论述/综述 | 论述、分析、综述、比较 | 无代码 |
| 数值计算/数据处理 | 计算、求解、拟合、插值 | Python |
| 控制系统/动力学仿真 | 控制、仿真、PID、传递函数 | MATLAB 优先 |
| 流体/CFD | N-S、CFD、边界层 | Python 或 MATLAB |
| 信号处理 | 信号、滤波、FFT、频谱 | MATLAB 优先 |
| 统计/数据分析 | 回归、方差、假设检验 | Python |
| 符号推导 | 推导、证明、化简 | sympy 或直接 LaTeX |
| 机器学习/AI | 分类、回归、神经网络 | Python |
| 有限元/结构 | 有限元、应力、模态 | MATLAB 或 Python |
| 绘图/可视化 | 画图、绘制、可视化 | Python (matplotlib) |

**用户确认语言后再进入 Phase 3。**

---

## Phase 3: 代码执行 ⛔

### 3.1 准备
1. 创建 `code/` 和 `figures/` 子目录
2. 编写代码到 `code/<name>.py` 或 `code/<name>.m`

### 3.2 Python 执行
```bash
python3 code/script.py
```
- 捕获 stdout/stderr，保存到 `code/output.txt`
- 所有 `plt.savefig()` 写入 `figures/xxx.png`（dpi≥150）
- 代码中 print 的数值结果留存，用于报告中的数据表格

### 3.3 C / C++ 执行
```bash
gcc -o code/program code/main.c -lm       # C
g++ -std=c++17 -O2 -o code/program code/main.cpp  # C++
./code/program
```
- 编译报错 → 修复 → 重试，最多 3 轮
- 运行输出写入 `code/output.txt`
- 若需绘图，建议在代码中输出数据文件（.csv/.txt），然后用 Python 脚本读取绘图
- 依赖的外部库（Eigen, OpenCV 等）需确认为 header-only 或系统已安装

### 3.4 MATLAB 执行（通过 MCP）
```
mcp__matlab__check_matlab_code  → 静态检查
mcp__matlab__evaluate_matlab_code  → 交互探索/调试
mcp__matlab__run_matlab_file  → 运行完整 .m 文件
```
- MATLAB 图表通过 `saveas(gcf, 'figures/xxx.png')` 保存
- 运行前确保 MATLAB 已打开且 MCP 连接正常
- MATLAB 不可用时，尝试用 Python 替代并在报告中说明

### 3.5 错误处理
- 代码报错 → 分析错误 → 修复 → 重试，最多 3 轮
- 3 轮后仍失败 → 记录错误信息，保留部分结果，**报告中注明"未完全求解"**

### 3.6 结果整理
- 所有图表必须为 `figures/` 下的 PNG 文件（html2docx 从文件系统读取图片）
- 数值结果整理为表格，供 Phase 4 使用

---

## Phase 4: 生成 HTML 报告 ⛔

### 4.1 模板
以 `templates/report.html` 为骨架，填写各部分内容。

### 4.2 html2docx 兼容性约束（⚠️ 必须遵守）

| HTML 元素 | 映射 | 关键要求 |
|------|------|------|
| `<div class="cover">` | 封面 | 占位即可，实际封面由 generate_docx.py 参数生成 |
| `<div class="toc">` | 目录 | 占位即可，Word 域代码自动生成 |
| `<h2>` | Heading 1 | 一级标题 |
| `<h3>` | Heading 2 | 二级标题 |
| `<h4>` | Heading 3 | 三级标题 |
| `<div class="figure">` > `<img>` + `<div class="caption">` | 插图 | `src="figures/xxx.png"` |
| `<div class="two-col">` > 两个 `div.figure` | 双栏图 | 用于对比图 |
| `<div class="note">` | 警告框 | ⚠ 前缀 |
| `<div class="info">` | 信息框 | ▸ 前缀 |
| `<table>` | Word 表格 | 第一行自动为表头（深蓝底白字） |
| **`\(...\)`** | 行内公式 | ⚠️ 必须用此定界符，**不可用 `$`** |
| **`\[...\]`** | 独立公式 | ⚠️ 同上 |
| `<pre><code>` | 代码块 | 等宽字体 |

### 4.3 报告标准结构

```
一、问题描述 (h2)
    - 作业要求原文复述
    - 问题拆解（子问题列表）
二、理论基础 (h2)
    - 控制方程 / 核心公式（用 \[...\]）
    - 变量说明
    - 方法原理简述
三、计算方法 (h3 可选多节)
    - 数值格式 / 算法流程
    - 关键代码片段（≤30 行，pre>code）
    - 参数设置（表格）
四、结果与分析 (h2)
    - 数值结果（表格）
    - 图表（div.figure，带图注）
    - 误差分析 / 收敛性讨论
    - 与理论对比
五、代码附录 (h2)
    - 完整代码（pre>code）
六、参考文献 (h2)
    - 教材 / 论文引用
```

### 4.4 格式规范
- 正文宋体小四(12pt)，1.5 倍行距，首行缩进 2 字符
- 标题黑体加粗，左对齐
- 表头深蓝底白字黑体加粗
- ⚠️ **不使用彩色字体**（正文始终默认黑色）
- 代码等宽字体，灰色背景

### 4.5 特殊内容处理

**无代码作业**：跳过"三、计算方法"和"五、代码附录"，报告聚焦理论分析和论述。

**MATLAB 作业**：代码附录贴 .m 文件内容，报告中提及"MATLAB R2025a 环境下运行"。

**多题作业**：每道题独立成章，结构为：
```
第 X 题：题目名称 (h2)
  问题描述 (h3)
  理论基础 (h3)
  计算方法 (h3)
  结果与分析 (h3)
```

---

## Phase 5: DOCX 转换 ⛔

### 5.1 复制脚本
```bash
cp <skill_dir>/html2docx/generate_docx.py <output_dir>/
cp <skill_dir>/html2docx/mml2omml.py <output_dir>/
```

### 5.2 编写转换脚本

在输出目录创建 `convert.py`：
```python
import sys
sys.path.insert(0, '.')
from generate_docx import convert_html_to_docx

convert_html_to_docx(
    html_path="report.html",
    output_path="report.docx",
    figures_dir="figures",
    course_name="考试科目：<课程名>",
    course_code="课程编号：<编号或留空>",
    report_title="<作业标题>",
    report_subtitle="课程作业报告",
    description="<关键词，逗号分隔>",
    student_info=[
        ("姓　　名", "<姓名>"),
        ("学　　号", "<学号>"),
        ("班　　级", "<班级>"),
        ("专　　业", "<专业>"),
        ("日　　期", "<YYYY.MM.DD>"),
    ],
)
```

### 5.3 运行
```bash
cd <output_dir> && python3 convert.py
```

### 5.4 验证
- 确认 `report.docx` 已生成
- 告知用户：在 WPS/Word 中打开 → Ctrl+A → F9 刷新域代码（目录和页码）
- 检查封面、章节、公式、图片、表格是否正常

---

## 错误处理速查

| 情况 | 处理 |
|------|------|
| 作业截图文字无法识别 | 请用户文字描述 |
| MATLAB MCP 不可用 | 尝试 Python 替代，告知局限 |
| 代码 3 次调试仍失败 | 记录错误，保留部分结果，报告中标注 |
| 公式过于复杂无法转 OMML | 标注该公式在 DOCX 中可能显示异常 |
| figures/ 为空 | 跳过 div.figure，不生成空图片引用 |
| 用户未提供学号 | 向用户确认 |
| Python 依赖缺失 | `pip3 install` 安装 |

---

## 成功标志

输出目录应包含：
```
<作业名>/
├── report.html          # HTML 报告（浏览器可看）
├── report.docx          # Word 文档（最终交付物）
├── convert.py           # DOCX 转换脚本（可复用）
├── generate_docx.py     # 转换器
├── mml2omml.py          # 公式引擎
├── figures/             # 图表 PNG
│   ├── result_1.png
│   └── ...
├── code/                # 源代码
│   ├── solver.py / .m
│   └── output.txt
```
