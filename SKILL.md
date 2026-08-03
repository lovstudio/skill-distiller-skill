---
name: lovstudio-skill-discovery
description: >
  从项目历史、问题记录和重复交付中识别值得沉淀的 Agent Skill，输出带证据、优先级、边界和验收标准的候选清单；适用于“梳理 skill”“提炼可复用能力”或 “discover reusable skills”。
license: MIT
metadata:
  author: lovstudio
  version: "0.1.0"
  tags:
    - skill-discovery
    - knowledge-distillation
    - productization
    - project-history
  compatibility: "Python 3.8+; git is optional but enables history evidence."
  dependencies: []
---

# lovstudio-skill-discovery — 从经验发现可复用能力

把项目中反复出现、代价高或已经验证有效的解决过程，转化为一份可决策的 Skill 候选地图。输出面向产品和交付，而非复述内部聊天、私人背景或一次性细节。

## Triggers

### Activate when

- 用户说“梳理一下哪些可以做成 skill”“把这段开发经验沉淀成 Skill”或“找出值得复用的能力”。
- 用户提供一个仓库、项目历史、事故复盘、需求记录或多轮任务记录，希望提炼可发布的技能候选。
- Use this when the user says “discover reusable skills”, “turn project history into skills”, or “find skill opportunities”.

### Do not activate when

- 用户已经明确要创建某一个 Skill：交给 `lovstudio-skill-creator` 实现、校验和本地安装。
- 用户要求优化既有 Skill：交给 `lovstudio-skill-optimizer`。
- 用户只需要复盘文章、团队知识库或博客稿件，不需要 Skill 候选与产品边界。

## Workflow (MANDATORY)

### Step 0: 定义扫描范围

1. 优先使用用户提供的项目路径、历史记录和明确问题；缺失时以当前项目为范围。
2. 把人名、客户信息、聊天背景和临时调试细节当作内部证据，默认不写入候选的用户可见名称、描述或示例。
3. 明确输出是“候选地图”，不是自动创建或发布；只有用户确认某项后才进入 `lovstudio-skill-creator`。

### Step 1: 采集可验证证据

运行证据扫描器，先得到可复核的线索，而非凭印象罗列：

```bash
python3 "$SKILL_DIR/scripts/scan_project_evidence.py" PROJECT_PATH \
  --output skill-discovery-evidence.md
```

若范围并非 Git 仓库，扫描 README、设计文档、故障记录、任务清单与用户提供的材料；清楚标注没有原始证据支撑的判断。

参照 [证据信号](references/evidence-signals.md) 将线索归为：反复摩擦、稳定方案、跨项目需求、发布/验收关卡和高价值用户结果。

### Step 2: 形成候选，而非把每个任务都命名为 Skill

每个候选都要回答：

1. **用户结果**：用户获得什么确定结果？
2. **触发边界**：哪些输入适合，哪些相邻任务不适合？
3. **可复用核心**：流程、检查表、脚本、模板或决策规则是什么？
4. **证据**：至少一条来自项目历史、重复请求或已验证结果的证据。
5. **交付形态**：Instruction-only、带 Python CLI 的 Single Skill，或有明确独立阶段的 Skill Kit。

禁止把单次文件名、特定人物、项目代号或内部上下文直接变成公开候选名称。

### Step 3: 评分与排序

按 [候选评分卡](references/candidate-scorecard.md) 给每项 1–5 分，计算总分并排序。高分并不自动创建：还要检查是否已有等价 Skill、是否可定义验收、以及是否会把私有逻辑或凭据带入可分发内容。

合并范围相同的候选；将只差具体项目参数的需求收敛成一项可配置的通用能力。

### Step 4: 交付候选地图

以中文输出以下结构，默认保留前 3–7 项：

```markdown
# Skill 候选地图：<项目或主题>

## 优先创建
| 优先级 | 候选名称 | 用户结果 | 证据 | 形态 | 验收标准 |
| --- | --- | --- | --- | --- | --- |

## 暂缓或合并
| 候选 | 原因 | 后续触发条件 |
| --- | --- | --- |

## 创建建议
- 推荐先创建：<候选>
- 原因：<价值、复用度、可验收性>
- 下一步：使用 `lovstudio-skill-creator` 创建并本地安装。
```

每一项用面向终端用户的中文名称和一句话价值说明；不把证据中的私人信息带入表格。

### Step 5: 创建前质量关

在建议创建某项前确认：

- 至少有一个可复现的触发场景和一个不触发的相邻场景；
- 有清楚的输入、输出和验收信号；
- 已识别用户配置、外部凭据和私有逻辑的边界；
- 与已有 Skill 不重叠，或已定义合并方式。

用户选择候选后，交接给 `lovstudio-skill-creator`；用户要求分发时，再交接给 `lovstudio-skill-publish`。

## Dependencies

- Python 3.8+（仅标准库）
- Git（可选，用于提交与历史证据扫描）
