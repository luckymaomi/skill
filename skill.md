---
name: project-inception
description: 通用项目初始化与持续交付 skill。用于在任何项目开始或继续工作前扫描事实、确认阶段、建立项目规则与文档、维护单文件计划，并完成实现、验证和收口；内置可直接运行且不会覆盖已有文件的 Python 初始化器。
---

# Project Inception

你是项目的执行 Agent。你的任务不是套用一份空模板，而是先确认事实和当前阶段，再把项目建立成边界清楚、可以运行、可以验证、可以持续维护的当前主干。

本 skill 自包含全部规则、示例和初始化代码，不依赖其他 skill、模板或脚本。

## 一、必须遵守的执行协议

每次使用本 skill，严格按以下顺序执行：

1. 定位本 `skill.md`，确认目标项目根目录。
2. 在目标项目根目录运行“只扫描”命令，不修改任何项目文件。
3. 阅读扫描结果和已有项目规则，不凭目录名称猜测产品事实。
4. 向用户报告已确认事实、未知项和建议阶段，请用户确认当前阶段。
5. 用户确认后运行初始化命令。已有文件只能跳过，禁止覆盖。
6. 阅读新建文件与已有文件，消除占位项；无法确认的内容必须明确标为待确认，不能伪装成事实。
7. 按确认阶段继续实际工作，不能只创建文档就停止。
8. 运行与风险相称的验证，更新计划，给出完成事实和剩余风险。

如果用户已经明确说出当前阶段，并且仓库事实没有冲突，不要重复询问；报告扫描结果后直接按该阶段继续。

如果工作目录有未提交改动，默认这些改动属于用户。只处理当前任务相关文件，不回滚、不覆盖、不顺手整理无关内容。

## 二、直接执行命令

下面命令假设本文件位于目标项目根目录且文件名是 `skill.md`。如果文件在其他位置，只替换命令中的路径，不改其余内容。

### 1. 扫描项目，不写文件

```powershell
python -c "import pathlib,re; p=pathlib.Path('skill.md'); s=p.read_text(encoding='utf-8'); m=re.search(r'<!-- PROJECT_INIT_START -->\s*\x60{3}python\s*(.*?)\s*\x60{3}\s*<!-- PROJECT_INIT_END -->',s,re.S); exec(compile(m.group(1),str(p),'exec'))" --scan
```

### 2. 用户确认阶段后初始化

把 `<阶段>` 替换成扫描结果中的阶段值：

```powershell
python -c "import pathlib,re; p=pathlib.Path('skill.md'); s=p.read_text(encoding='utf-8'); m=re.search(r'<!-- PROJECT_INIT_START -->\s*\x60{3}python\s*(.*?)\s*\x60{3}\s*<!-- PROJECT_INIT_END -->',s,re.S); exec(compile(m.group(1),str(p),'exec'))" --init --stage <阶段>
```

公开项目需要贡献与安全文档时使用：

```powershell
python -c "import pathlib,re; p=pathlib.Path('skill.md'); s=p.read_text(encoding='utf-8'); m=re.search(r'<!-- PROJECT_INIT_START -->\s*\x60{3}python\s*(.*?)\s*\x60{3}\s*<!-- PROJECT_INIT_END -->',s,re.S); exec(compile(m.group(1),str(p),'exec'))" --init --stage <阶段> --visibility public
```

用户已明确选择 MIT License 时使用：

```powershell
python -c "import pathlib,re; p=pathlib.Path('skill.md'); s=p.read_text(encoding='utf-8'); m=re.search(r'<!-- PROJECT_INIT_START -->\s*\x60{3}python\s*(.*?)\s*\x60{3}\s*<!-- PROJECT_INIT_END -->',s,re.S); exec(compile(m.group(1),str(p),'exec'))" --init --stage <阶段> --visibility public --license mit --copyright "<版权人>"
```

不要在用户未确认授权边界时自动添加开源协议。

## 三、通用事实纪律

- 事实只能来自用户明确输入、当前仓库、命令输出、真实运行结果或已核验的权威资料。
- 用户目标、历史记录、旧实现、旧文档和测试结果都是证据来源，不自动等于当前事实。
- 任何尚未实现或尚未验证的能力，在任何载体中都不能表述为已经实现或已经通过。
- 必须区分五种状态：已确认事实、用户提供但未验证的事实、未验证推测、计划事项、已完成并验证事项。
- 新事实推翻原计划时，先更新计划，再继续执行；不能只在回复中解释。
- 当前实现只服务当前生效语义。除非用户明确要求，否则不保留旧入口、旧名称、旧数据形态、转发层或过渡文件。
- 不使用机械替换、跨文件无审查批量替换或大小写不受控的命名迁移。
- 不读取、打印、提交或上传与任务无关的密钥、令牌、个人数据和私有配置。
- commit、push、部署、发布、生产数据变更、权限扩大和付费操作必须得到用户明确授权。

事实表达示例：

```text
正确：仓库中存在 package.json，其中 scripts.test 为 "vitest run"；尚未实际运行。
错误：项目测试已通过。

正确：用户要求支持离线使用；当前代码中尚未找到离线存储实现。
错误：项目支持离线使用。

正确：计划采用 SQLite；这是待实现设计，不是当前数据事实。
错误：项目使用 SQLite。

正确：已运行完整验证命令，退出码为 0；核心路径另行实测通过。
错误：从代码看应该没问题。
```

## 四、项目阶段

阶段描述工作当前主要矛盾，不是版本号，也不是永久标签。

| 阶段值 | 阶段名称 | 进入条件 | 本阶段完成条件 |
| --- | --- | --- | --- |
| `requirements` | 需求确认 | 产品目标、用户、主路径或边界仍有关键未知项 | 需求、边界、验收方式得到用户确认 |
| `foundation` | 项目骨架 | 需求已确认，但规则、结构、运行入口或验证入口尚未成立 | 最小真实骨架可运行，项目规则和事实文档成立 |
| `implementation` | 实现执行 | 目标和设计明确，当前主要工作是实现当期范围 | 当期范围实现，针对性测试通过，文档同步 |
| `verification` | 验证验收 | 实现基本完成，当前主要工作是证明目标环境可用 | 完整验证和核心路径通过，失败项已修复或明确阻塞 |
| `closure` | 交付收口 | 验证已完成，当前主要工作是核对范围、状态和外部交付 | 计划收口，风险披露，授权的外部操作得到远端确认 |

阶段不是由文件数量机械决定。扫描器只提供建议，最终阶段必须结合用户目标和真实运行结果确认。

### 阶段确认示例

```text
已确认事实：仓库已存在源代码和测试配置；AGENTS.md 与 spec.md 缺失；当前测试尚未运行。
未知项：当期交付范围、目标环境和完整验证命令。
建议阶段：foundation（项目骨架）。
原因：实现已经存在，但项目规则、当前产品事实和验证入口尚未建立。
请确认当前按 foundation 阶段继续；如果不准确，请直接回复正确阶段和已经完成的事实。
```

```text
已确认事实：需求、设计和任务清单已存在；计划中仍有 6 项实现任务未完成。
建议阶段：implementation（实现执行）。
我将继续完成计划内实现、测试与文档同步。这个阶段判断是否准确？
```

```text
已确认事实：实现任务全部完成，针对性测试通过；尚未运行项目完整验证命令。
建议阶段：verification（验证验收）。
我将先运行完整验证和核心路径检查，再根据真实失败修复。这个阶段判断是否准确？
```

## 五、需求确认

先从用户已经提供的信息和仓库事实中找答案，不重复提问。只询问无法自行确认且不同答案会实质改变结果的问题，每轮优先问一到三个。

必须建立的需求事实：

- 项目是什么，服务谁，解决什么问题。
- 最重要的端到端用户路径是什么。
- 当前必须完成什么，明确不做什么。
- 输入、输出、状态和数据的来源、归属与生命周期。
- 权限、隐私、安全、成本、版权、合规和失败恢复边界。
- 目标运行环境、交付方式和不可改变的约束。
- 怎样验证完成，哪些观察结果代表失败。

提问示例：

```text
我已经确认目标是让用户完成 [主路径]。现在唯一会改变数据边界的问题是：数据只保存在当前设备，还是需要跨设备同步？
```

```text
当前仓库同时存在两种运行入口，但文档没有说明哪一个生效。请确认本次保留的唯一入口；确认后我会删除不再生效的入口，不建立兼容转发。
```

```text
当期范围和成功路径已经清楚。还缺一个验收事实：完成标准以自动验证命令通过为准，还是还必须包含目标环境中的人工主路径验收？
```

不合格提问示例：

```text
请一次性告诉我产品名、技术栈、颜色、数据库、部署平台、协议、用户画像、所有页面和全部未来功能。
```

问题在于它没有利用已有事实，也没有按对架构和边界的影响排序。

## 六、调查顺序

按以下顺序调查，先广后深：

1. 读取目标目录内所有适用的工作规则。
2. 查看目录结构、Git 状态、分支、远端和未提交改动。
3. 读取项目入口、事实文档、计划、依赖、运行配置和验证配置。
4. 从核心路径追踪真实状态、规则、数据和外部边界。
5. 运行最小只读检查，验证文档与实现是否一致。
6. 记录已确认事实、冲突、未知项和建议阶段。

调查结果示例：

```text
已确认事实
- 当前分支跟踪 origin/main，工作区有 2 个与本任务无关的未提交文件。
- 项目完整验证入口定义在 package scripts 的 verify 命令中。
- 主路径会写入本地数据库，写入失败会返回显式错误。

冲突
- README 仍描述已经删除的入口，与当前实现不一致。

未知项
- 目标环境中的权限拒绝路径尚未实测。

行动
- 保留无关未提交改动；先建立失败测试，再修正当前入口和 README，最后运行 verify 与主路径实测。
```

## 七、文件职责与完整示例

初始化器会按需创建以下文件。代码区中的模板是完整示例，不存在省略内容；生成后仍必须用真实事实替换明确标注的待确认项。

### `AGENTS.md`

只记录长期工作规则与协作纪律。产品能力变化不应直接导致它变化。

应包含：交流语言、事实纪律、工作区保护、计划规则、工程纪律、验证命令、安全边界和交付标准。

### `spec.md`

只记录当前生效的产品事实与边界。建议结构：产品定位、当前事实、用户路径、业务边界、技术边界、数据边界、当前不做、验收标准、协议事实。

待确认内容必须这样写：

```markdown
## 待确认

- [待用户确认，不是当前事实] 目标用户范围。
- [待仓库验证，不是当前事实] 数据持久化方式。
```

不能把它改写成没有证据的肯定句。

### `plan.md`

中大型任务使用一个持续更新的执行合同，固定顺序如下：

```markdown
# 当前任务

## 需求文档
- 用户要求与可验收解释。

## 当前事实
- 已由仓库或运行结果确认的事实。

## 失败测试
- 当前问题仍存在时可以观察到的结果。

## 目标
- 本次交付完成后的终局。

## 不做范围
- 本次明确排除的内容。

## 设计
- 职责、数据流、接口、错误与关键取舍。

## 实施任务
- [ ] research
- [ ] 实现
- [ ] 测试
- [ ] 文档同步
- [ ] 完整验证
- [ ] 收口

## 验证计划
- 要运行的真实命令、场景、环境和预期结果。

## 收口
- 完成事实、实际命令、通过项、未验证项和剩余风险。
```

写完计划再改实现；checklist 随进度更新；新事实推翻计划时先改计划。

### `README.md`

面向用户和新读者，只描述项目是什么、当前能做什么、如何开始、如何验证和适用的授权边界。不得承担内部工作日志或未来路线图职责。

### `.gitignore`

只忽略当前环境真实产生且不应进入版本控制的内容。不得用忽略规则掩盖应该清理或应该提交的文件。

### `CONTRIBUTING.md`

仅在项目接受外部贡献时创建，说明贡献前提、分支与变更边界、验证要求、提交信息和安全问题入口。

### `SECURITY.md`

仅在需要公开安全报告方式时创建，说明报告渠道、需要提供的信息、禁止公开披露的内容和敏感数据处理方式。

### `LICENSE`

只有授权边界明确时才创建。不得覆盖已有协议，不得根据“通常如此”猜测版权人。

## 八、设计与实现规则

- 优先沿用已经成立的技术栈、命名、模块边界和本地辅助能力。
- 抽象只有在消除真实复杂度、减少有意义的重复或匹配既有模式时才成立。
- 单一职责按变化原因判断，不按行数机械判断；文件超过 300 行时审查职责，但不自动拆分。
- 状态、规则、数据读写、展示、外部接线和错误处理混在一起且变化原因不同时，拆清边界。
- 结构化数据使用结构化工具处理，不用脆弱的文本拼接代替。
- 错误在拥有处理能力的边界解决；不能静默吞掉，也不能泄露敏感上下文。
- 测试保护真实规则、边界、协作合同和核心路径，不保护已经废弃的行为。
- 修改共享行为、跨边界合同、持久数据、安全规则或核心路径时，扩大验证范围。
- 文档、实现、测试和配置必须在同一次交付中描述同一套当前事实。

实现决策示例：

```text
事实：两个调用方共享同一组独立规则，并且第三个调用方将在本次范围内接入。
决定：提取可测试的规则模块，调用方只负责接线。
```

```text
事实：一个 340 行文件只有一个变化原因，内部状态与规则高度内聚。
决定：记录职责审查结果并保留，不为了行数拆分。
```

```text
事实：旧名称只被旧测试引用，当前产品事实已经统一为新名称。
决定：删除旧测试与旧名称，不增加别名导出。
```

## 九、验证与收口

验证必须证明结果，不只是证明代码可以被读取。

验证层次：

1. 与改动最接近的格式、静态、规则或单元检查。
2. 真实模块协作、数据流和错误路径。
3. 项目定义的完整验证命令。
4. 核心用户路径在目标环境中的运行结果。
5. 最终差异、文件清单、工作区状态和授权外部状态。

验证记录示例：

```text
已运行
- <完整命令 A>：退出码 0。
- <完整命令 B>：共 42 项通过，0 项失败。
- <核心路径>：在 <目标环境> 实测成功。

未验证
- <场景>：因 <客观原因> 无法运行。

剩余风险
- <风险及影响>；如果没有，明确写“没有已知剩余风险”。
```

错误示例：

```text
测试应该能过。
代码看起来没问题。
后面再继续优化。
```

完整交付必须回填计划中的完成事实、实际命令、通过项、未验证项和剩余风险。不能把未完成范围包装成“后续优化”。

## 十、初始化器完整源码

初始化器只使用 Python 标准库。它不会修改任何已有文件；每个已有路径都会输出 `skipped`。它根据可见仓库事实建议阶段和验证命令，但建议不是最终事实。

<!-- PROJECT_INIT_START -->
```python
from __future__ import annotations

import argparse
import datetime as dt
import getpass
import json
import os
import re
from pathlib import Path


PHASES = {
    "requirements": "需求确认",
    "foundation": "项目骨架",
    "implementation": "实现执行",
    "verification": "验证验收",
    "closure": "交付收口",
}

IGNORED_DIRS = {
    ".git", ".hg", ".svn", ".idea", ".vscode", "node_modules",
    "vendor", "dist", "build", "target", ".next", ".nuxt",
    ".venv", "venv", "__pycache__", ".pytest_cache", ".mypy_cache",
    ".ruff_cache", "coverage", ".coverage",
}

CODE_SUFFIXES = {
    ".c", ".cc", ".cpp", ".cs", ".css", ".dart", ".ex", ".exs",
    ".go", ".html", ".java", ".js", ".jsx", ".kt", ".kts", ".php",
    ".py", ".rb", ".rs", ".scala", ".swift", ".ts", ".tsx", ".vue",
}

MANIFESTS = {
    "package.json", "pyproject.toml", "requirements.txt", "Pipfile",
    "poetry.lock", "go.mod", "Cargo.toml", "pom.xml", "build.gradle",
    "build.gradle.kts", "composer.json", "Gemfile", "mix.exs", "pubspec.yaml",
}


def visible_files(root: Path) -> list[Path]:
    result = []
    for path in root.rglob("*"):
        if not path.is_file():
            continue
        relative = path.relative_to(root)
        if any(part in IGNORED_DIRS for part in relative.parts):
            continue
        result.append(relative)
    return sorted(result, key=lambda item: item.as_posix().lower())


def read_text(path: Path) -> str:
    try:
        return path.read_text(encoding="utf-8")
    except (OSError, UnicodeError):
        return ""


def detect_validation(root: Path) -> list[str]:
    commands: list[str] = []
    package_json = root / "package.json"
    if package_json.exists():
        try:
            scripts = json.loads(read_text(package_json)).get("scripts", {})
        except (json.JSONDecodeError, AttributeError):
            scripts = {}
        for name in ("format:check", "lint", "typecheck", "test", "build", "verify"):
            if name in scripts:
                commands.append(f"npm run {name}")

    pyproject = read_text(root / "pyproject.toml")
    if pyproject:
        if "[tool.ruff" in pyproject:
            commands.append("python -m ruff check .")
        if "[tool.mypy" in pyproject:
            commands.append("python -m mypy .")
        if "[tool.pytest" in pyproject or "pytest" in pyproject:
            commands.append("python -m pytest")

    if (root / "go.mod").exists():
        commands.append("go test ./...")
    if (root / "Cargo.toml").exists():
        commands.extend(["cargo fmt --check", "cargo clippy --all-targets", "cargo test"])
    if any(root.glob("*.sln")) or any(root.glob("*.csproj")):
        commands.extend(["dotnet build", "dotnet test"])

    unique = []
    for command in commands:
        if command not in unique:
            unique.append(command)
    return unique


def inspect_project(root: Path) -> dict:
    files = visible_files(root)
    names = {path.as_posix() for path in files}
    code_files = [path for path in files if path.suffix.lower() in CODE_SUFFIXES]
    manifests = [path.as_posix() for path in files if path.name in MANIFESTS]
    tests = [
        path.as_posix() for path in files
        if "test" in path.name.lower() or any(part.lower() in {"test", "tests"} for part in path.parts)
    ]
    docs = [name for name in ("AGENTS.md", "spec.md", "plan.md", "README.md") if name in names]
    plan = read_text(root / "plan.md")
    unchecked = len(re.findall(r"^- \[ \]", plan, flags=re.MULTILINE))
    checked = len(re.findall(r"^- \[[xX]\]", plan, flags=re.MULTILINE))
    validation = detect_validation(root)

    if "spec.md" not in names and not code_files and not manifests:
        suggested = "requirements"
        reason = "尚未发现已确认需求文档、代码或技术栈入口。"
    elif any(name not in names for name in ("AGENTS.md", "spec.md", "README.md")):
        suggested = "foundation"
        reason = "项目已有内容，但长期规则、当前事实或使用入口尚未完整建立。"
    elif unchecked > 0:
        suggested = "implementation"
        reason = "当前计划仍有未完成任务。"
    elif code_files and not validation:
        suggested = "verification"
        reason = "项目已有实现，但尚未识别出完整验证入口。"
    elif checked > 0 and unchecked == 0:
        suggested = "closure"
        reason = "计划任务已全部勾选，需要核验真实结果并完成收口。"
    elif code_files:
        suggested = "implementation"
        reason = "项目已有实现，尚需根据当前目标确认当期执行范围。"
    else:
        suggested = "foundation"
        reason = "需求文档或项目入口已出现，下一步应建立可运行、可验证的真实骨架。"

    return {
        "root": str(root),
        "project_name": root.name,
        "git_repository": (root / ".git").exists(),
        "file_count": len(files),
        "documents": docs,
        "manifests": manifests,
        "code_file_count": len(code_files),
        "test_file_count": len(tests),
        "plan_checked": checked,
        "plan_unchecked": unchecked,
        "detected_validation_commands": validation,
        "suggested_stage": suggested,
        "suggested_stage_name": PHASES[suggested],
        "suggestion_reason": reason,
    }


def validation_block(commands: list[str]) -> str:
    if not commands:
        return "- [待仓库确认，不是当前事实] 项目完整验证命令。"
    return "\n".join(f"- `{command}`" for command in commands)


def agents_template(project: str, commands: list[str]) -> str:
    return f"""# {project} Agent 工作规约

本文件是本仓库的最高工作约束。产品事实看 `spec.md`，当前任务状态看 `plan.md`。

## 基础约束

- 始终使用简体中文和项目 owner 交流，除非 owner 明确要求其他语言。
- 阅读文本时使用正确编码；显示异常时先验证编码，不误判文件损坏。
- 不回滚 owner 已有改动，只处理当前任务相关文件。
- commit、push、发布和部署只在 owner 明确授权时执行。
- 禁止机械替换、无审查批量替换和大小写不受控的命名迁移。
- 禁止旧别名、兼容转发、旧语义包装、历史适配层和过渡文件。
- 当前实现只服务当前生效事实。

## 事实纪律

- 判断必须基于 owner 明确输入、当前仓库、命令输出或真实运行结果。
- 区分已确认事实、owner 提供但未验证的事实、推测、计划事项和已完成并验证事项。
- 任何未实现或未验证内容，在任何载体中都不能写成已经实现或已经通过。
- 新事实推翻计划时，先更新 `plan.md`，再继续。

## 文件职责

- `AGENTS.md`：工作规则和协作纪律。
- `spec.md`：当前产品事实、边界和验收标准。
- `plan.md`：当前中大型任务的单文件执行合同。
- `README.md`：面向用户和新读者的使用入口。

## 计划规则

- 中大型开发、跨模块修改、边界调整和生产级验收必须先维护 `plan.md`。
- 计划按需求、事实、失败测试、目标、不做范围、设计、任务、验证、收口组织。
- checklist 随进度更新，不用口头说明代替计划更新。

## 工程规则

- 优先沿用仓库已经成立的技术栈、命名和模块边界。
- 单一职责按变化原因判断；文件超过 300 行时审查职责，但不机械拆分。
- 状态、规则、数据、展示、外部接线和错误处理变化原因不同时，拆清边界。
- 测试保护真实规则、边界和核心路径，不保护废弃行为。
- 文档、实现、测试和配置在同一次交付中保持一致。

## 完整验证

{validation_block(commands)}

收尾前运行项目完整验证和核心路径检查。无法运行时说明具体命令、原因和剩余风险。

## 安全与交付

- 不泄露密钥、令牌、个人数据或私有配置。
- 破坏性操作、生产变更、权限扩大和付费操作需要明确授权。
- 交付必须说明完成事实、实际验证、未验证项和剩余风险，不交半成品。
"""


def spec_template(project: str) -> str:
    return f"""# {project} Spec

`spec.md` 只记录当前生效的产品事实与边界。所有待确认项都不是当前事实。

## 产品定位

- [待用户确认，不是当前事实] 项目是什么、服务谁、解决什么问题。

## 当前事实

- 项目名称：`{project}`。
- [待仓库确认，不是当前事实] 当前技术栈、运行入口和已实现能力。
- [待仓库确认，不是当前事实] 当前完整验证命令。

## 核心用户路径

1. [待用户确认，不是当前事实] 用户从哪里开始。
2. [待用户确认，不是当前事实] 用户执行什么关键动作。
3. [待用户确认，不是当前事实] 用户得到什么可观察结果。

## 业务边界

- [待用户确认，不是当前事实] 当期必须完成的范围。
- [待用户确认，不是当前事实] 权限、失败与恢复边界。

## 技术边界

- [待仓库确认，不是当前事实] 模块职责、外部依赖与运行环境。

## 数据边界

- [待用户与仓库共同确认，不是当前事实] 数据来源、归属、存储、生命周期和敏感级别。

## 当前不做

- [待用户确认，不是当前事实] 本期明确排除的内容。

## 验收标准

- [待用户确认，不是当前事实] 可复现的成功结果与失败结果。
- 项目完整验证命令通过。
- 文档、实现、测试和配置描述同一套当前事实。

## 授权边界

- [待用户确认，不是当前事实] 私有、公开或适用协议。
"""


def plan_template(project: str, stage: str, commands: list[str]) -> str:
    return f"""# {project} 当前任务

## 需求文档

- 当前阶段：`{stage}`（{PHASES[stage]}），已由用户确认后写入。
- [待用户确认，不是当前事实] 当期目标和可验收要求。

## 当前事实

- 项目根目录：`{project}`。
- [待调查] 当前结构、主路径、数据流、工作区状态和验证入口。

## 失败测试

- [待定义] 哪些可观察结果代表当前问题仍然存在。

## 目标

- [待定义] 本次完成后的唯一当前主干和可验证终局。

## 不做范围

- [待用户确认] 本次明确排除的内容。

## 设计

- [待 research 后填写] 职责、数据流、接口、错误、安全与关键取舍。

## 实施任务

- [ ] 确认需求、边界与当前阶段。
- [ ] 调查当前实现和真实运行事实。
- [ ] 建立失败测试或等价复现。
- [ ] 完成当期实现。
- [ ] 完成测试与文档同步。
- [ ] 运行针对性检查和完整验证。
- [ ] 回填收口并完成授权范围内的交付。

## 验证计划

{validation_block(commands)}
- [待确认] 核心路径、目标环境和预期结果。

## 收口

- 完成事实：待填写。
- 实际命令：待填写。
- 通过项：待填写。
- 未验证项：待填写。
- 剩余风险：待填写。
"""


def plan_example_template() -> str:
    return """# <任务名称>

## 需求文档
- 把用户目标转成明确、可验收的要求。

## 当前事实
- 只记录仓库、命令或运行结果已经确认的事实。

## 失败测试
- 描述问题仍存在时可以观察到的结果。

## 目标
- 描述本次完成后的终局。

## 不做范围
- 明确排除容易扩张或误解的内容。

## 设计
- 说明职责、数据流、接口、错误、安全和关键取舍。

## 实施任务
- [ ] research
- [ ] 实现
- [ ] 测试
- [ ] 文档同步
- [ ] 完整验证
- [ ] 收口

## 验证计划
- 列出真实命令、场景、环境和预期结果。

## 收口
- 完成事实、实际命令、通过项、未验证项和剩余风险。
"""


def readme_template(project: str, commands: list[str]) -> str:
    command_text = "\n".join(f"{index}. `{command}`" for index, command in enumerate(commands, 1))
    if not command_text:
        command_text = "项目完整验证命令尚待根据当前技术栈确认。"
    return f"""# {project}

[待用户确认，不是当前事实] 用一句话说明项目是什么、服务谁和解决什么问题。

## 当前能力

- [待仓库验证，不是当前事实] 当前已经实现且用户可以使用的能力。

## 开始使用

[待仓库确认，不是当前事实] 写出从干净环境开始可以直接执行的安装与运行步骤。

## 验证

{command_text}

## 授权

[待用户确认，不是当前事实] 说明项目的使用与分发边界；只有协议文件已经存在时才引用它。
"""


def gitignore_template(root: Path) -> str:
    lines = [
        "# Local secrets", ".env", ".env.*", "!.env.example",
        "", "# Editor and operating system", ".idea/", ".vscode/",
        ".DS_Store", "Thumbs.db", "", "# Logs", "*.log",
    ]
    if (root / "package.json").exists():
        lines.extend(["", "# JavaScript", "node_modules/", "dist/", "coverage/"])
    if (root / "pyproject.toml").exists() or (root / "requirements.txt").exists():
        lines.extend([
            "", "# Python", "__pycache__/", "*.py[cod]", ".venv/", "venv/",
            ".pytest_cache/", ".mypy_cache/", ".ruff_cache/", "dist/", "build/", "*.egg-info/",
        ])
    if (root / "Cargo.toml").exists():
        lines.extend(["", "# Rust", "target/"])
    if (root / "go.mod").exists():
        lines.extend(["", "# Go", "bin/", "coverage.out"])
    return "\n".join(dict.fromkeys(lines)) + "\n"


def contributing_template(commands: list[str]) -> str:
    return f"""# Contributing

## Before You Start

- Read `AGENTS.md`, `spec.md`, and the current `plan.md`.
- Open an issue or discussion before changing product boundaries.
- Never include secrets, private data, generated artifacts, or unrelated changes.

## Changes

- Keep each change focused on one current objective.
- Update implementation, tests, documentation, and configuration together.
- Do not add compatibility aliases or preserve behavior that is no longer current.

## Verification

{validation_block(commands)}

Run all applicable checks and report anything that could not be verified.

## Security

Do not disclose vulnerabilities in public issues. Follow `SECURITY.md`.
"""


def security_template() -> str:
    return """# Security Policy

## Reporting a Vulnerability

Report security issues privately to the repository owner through a private channel published by the project host. Do not open a public issue.

Include the affected version or commit, reproduction steps, impact, and any known mitigation. Remove secrets, personal data, and unrelated private information from the report.

The maintainer will acknowledge the report, verify the issue, coordinate a fix, and disclose it only after affected users have a reasonable mitigation path.
"""


def mit_license(copyright_holder: str) -> str:
    year = dt.datetime.now().year
    return f"""MIT License

Copyright (c) {year} {copyright_holder}

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
"""


def write_new(path: Path, content: str, results: dict[str, list[str]]) -> None:
    if path.exists():
        results["skipped"].append(path.as_posix())
        return
    path.parent.mkdir(parents=True, exist_ok=True)
    path.write_text(content.rstrip() + "\n", encoding="utf-8", newline="\n")
    results["created"].append(path.as_posix())


def initialize(root: Path, report: dict, args: argparse.Namespace) -> dict:
    project = args.project_name or report["project_name"]
    commands = report["detected_validation_commands"]
    results: dict[str, list[str]] = {"created": [], "skipped": []}

    files = {
        root / "AGENTS.md": agents_template(project, commands),
        root / "spec.md": spec_template(project),
        root / "plan.md": plan_template(project, args.stage, commands),
        root / "plan.example.md": plan_example_template(),
        root / "README.md": readme_template(project, commands),
        root / ".gitignore": gitignore_template(root),
    }
    if args.visibility == "public":
        files[root / "CONTRIBUTING.md"] = contributing_template(commands)
        files[root / "SECURITY.md"] = security_template()
    if args.license == "mit":
        holder = args.copyright.strip() or getpass.getuser()
        files[root / "LICENSE"] = mit_license(holder)

    for path, content in files.items():
        write_new(path, content, results)
    return results


def parse_args() -> argparse.Namespace:
    parser = argparse.ArgumentParser(description="Scan and initialize a project without overwriting files.")
    action = parser.add_mutually_exclusive_group()
    action.add_argument("--scan", action="store_true", help="Inspect only; this is the default.")
    action.add_argument("--init", action="store_true", help="Create missing project files.")
    parser.add_argument("--stage", choices=tuple(PHASES), help="User-confirmed current stage.")
    parser.add_argument("--project-name", help="Project name used in newly created documents.")
    parser.add_argument("--visibility", choices=("undecided", "private", "public"), default="undecided")
    parser.add_argument("--license", choices=("none", "mit"), default="none")
    parser.add_argument("--copyright", default="", help="Copyright holder for a new MIT License.")
    return parser.parse_args()


def main() -> None:
    args = parse_args()
    root = Path.cwd().resolve()
    report = inspect_project(root)
    output = {"scan": report}

    if args.init:
        if not args.stage:
            raise SystemExit("--init requires --stage after the user confirms the current stage.")
        output["initialization"] = initialize(root, report, args)
        output["next_action"] = (
            f"Read all project rules, replace confirmed placeholders with facts, "
            f"and continue the {args.stage} stage. Do not stop after creating documents."
        )
    else:
        output["next_action"] = (
            "Report confirmed facts and unknowns to the user, ask them to confirm the suggested stage, "
            "then run --init with the confirmed --stage."
        )

    print(json.dumps(output, ensure_ascii=False, indent=2))


if __name__ == "__main__":
    main()
```
<!-- PROJECT_INIT_END -->

## 十一、初始化后不能停止

文件创建成功只代表规则和事实入口已经建立，不代表项目完成。

初始化后立即执行：

1. 读取所有适用规则和已有实现。
2. 把可以由仓库确认的占位项改成事实。
3. 把必须由用户决定的关键未知项集中成一到三个问题。
4. 更新 `plan.md` 的事实、失败测试、目标、设计和任务。
5. 按确认阶段继续需求、骨架、实现、验证或收口工作。
6. 完成后运行真实验证并回填收口。

最终回复示例：

```text
当前阶段已确认为 implementation。已完成当期范围并同步事实文档；实际运行 `<命令>`，结果为 `<结果>`。`<未验证项>` 因 `<原因>` 未验证，剩余风险是 `<风险>`。
```

不要只回复“初始化完成”“模板已生成”或“后续可以继续开发”。
