# Workflow Engine v3.0

Hermes版MetaSkill — 目标驱动的多步骤任务编排系统。

## 功能

- **DAG编排** — 拓扑排序、环检测、依赖管理
- **并行执行** — 同层步骤自动并行，delegate_task batch模式
- **故障转移** — 三级降级：用户fallback→同类工具→缓存/跳过
- **资源监控** — token/时间/API预算，超80%预警，超100%暂停
- **版本管理** — 版本化存储、对比、回滚、变更日志
- **模式检测** — 扫描历史session发现重复任务，自动生成工作流提案
- **可视化面板** — HTML实时执行状态
- **社区共享** — 导出/导入、ClawHub同步
- **六模式触发** — 关键词、意图、工具序列、时间规律、历史模式、事件驱动

## 快速开始

```bash
# 列出工作流
python3 run.py list

# 验证YAML
python3 run.py validate examples/ai-news-daily.yaml

# 查看执行计划
python3 run.py plan examples/ai-news-daily.yaml

# 执行工作流
python3 run.py execute examples/ai-news-daily.yaml '{"topic":"AI最新进展"}'
```

## CLI命令

| 命令 | 说明 |
|------|------|
| `list` | 列出所有工作流 |
| `validate <yaml>` | 验证YAML格式和依赖 |
| `plan <yaml>` | 查看执行计划和并行层 |
| `next <yaml>` | 获取下一批可执行步骤 |
| `parallel <yaml>` | 查看并行执行计划 |
| `execute <yaml> [inputs]` | 生成执行指令 |
| `delegate <yaml> [inputs]` | delegate_task格式输出 |
| `detect [days] [min_confidence]` | 模式检测 |
| `dashboard [run_dir]` | 生成可视化面板 |
| `versions <name>` | 列出版本 |
| `diff <name> <v1> <v2>` | 版本对比 |
| `rollback <name> <version>` | 回滚版本 |
| `community list [tag]` | 列出社区工作流 |
| `community search <keyword>` | 搜索社区 |
| `community publish <name>` | 发布到社区 |
| `community install <name>` | 从社区安装 |
| `community rate <name> <1-5>` | 评分 |
| `trigger <message>` | 分析消息意图 |

## 工作流YAML格式

```yaml
name: my-workflow
version: "1.0"
description: "工作流描述"
author: 绪哥
tags: [tag1, tag2]

trigger:
  type: manual | cron | event
  schedule: "0 8 * * *"

resources:
  max_tokens: 50000
  max_time: 300

steps:
  - id: step1
    name: "步骤名称"
    type: terminal | skill | subagent | user_input | llm
    depends: []
    config:
      command: "shell命令"
      skill_name: "技能名"
      prompt: "提示词"
      goal: "目标"
    output:
      type: text | file
      variable: "输出变量名"
    timeout: 120
    retry:
      max_attempts: 3
      on_fail: abort | skip | pause
      fallbacks:
        - type: terminal
          command: "备选命令"
```

## 项目结构

```
workflow-engine/
  ├─ engine.py              # DAG核心：解析、拓扑排序、环检测、状态管理
  ├─ executor.py            # 执行器：5种步骤类型、故障转移、并行执行
  ├─ resource_monitor.py    # 资源监控：token/时间/API预算控制
  ├─ pattern_detector.py    # 模式检测：历史扫描、聚类分析、提案生成
  ├─ version_manager.py     # 版本管理：版本化存储、对比、回滚、变更日志
  ├─ dashboard.py           # 可视化面板：HTML实时执行状态
  ├─ community.py           # 协作共享：导出/导入、社区库、评分、同步
  ├─ auto_trigger.py        # 六模式自动触发器
  ├─ run.py                 # CLI入口：18个命令
  ├─ FORMAT.md              # YAML工作流格式规范
  ├─ test_engine.py         # 测试脚本
  ├─ examples/              # 示例工作流
  ├─ workflows/             # 版本化存储
  ├─ runs/                  # 运行记录
  └─ _community/            # 社区库
```

## 技能系统集成

SKILL.md已发布到ClawHub：
```bash
clawhub install hermes-workflow-engine
```

## License

MIT

## Author

绪哥 & 小狗 🐶
