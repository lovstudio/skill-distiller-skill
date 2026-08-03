# lovstudio-skill-discovery

![Version](https://img.shields.io/badge/version-0.1.0-CC785C)

从项目历史与重复交付中发现值得产品化的 Agent Skill，并输出有证据、优先级和验收标准的候选地图。

## 本地安装

```bash
ln -s "$(pwd)/skill-discovery-skill" \
  "$HOME/.codex/skills/lovstudio-skill-discovery"
```

## 使用

- “梳理一下这个仓库中哪些经验应该做成 Skill。”
- “从最近的更新、重启和 AI 集成工作里，发现可复用的能力。”
- “Discover reusable skills from this project history.”

可先生成证据摘要：

```bash
python3 scripts/scan_project_evidence.py /path/to/project \
  --output skill-discovery-evidence.md
```

随后由 Agent 结合项目上下文输出候选地图；候选获确认后，使用 `lovstudio-skill-creator` 创建实现。

## 质量门

```bash
python3 scripts/validate_skill.py .
python3 scripts/scan_project_evidence.py . --output /tmp/skill-discovery-evidence.md
```

## 依赖

- Python 3.8+
- Git（可选）

## License

MIT
