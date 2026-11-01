# 持久记录协议

仅在需要保存、续接或查询时读。本技能文件夹存说明，学习记录放用户的学习库/工作目录，不混入技能安装目录。普通 Markdown 即可；Obsidian、Dataview 都可选。

## 定位与最小读取

优先用用户指定路径、当前任务已知路径或当前目录已有学习库索引。缺少路径且用户希望持久学习时只问一次；不能扫描整台电脑寻找笔记。没有可写目录可先在对话中学习。

索引只是主题定位表，不存能力状态。只创建实际需要的目录。

```text
学习库/
  index.md
  topics/
    python-csv/
      topic.md
      concepts/C001.md
      resources/R001.md
      sessions/2026-10-30-01.md
      reviews/2026-10-30.md
```

主题 ID 使用稳定的小写英文、数字和短横线；同名但目标不同的主题使用不同 ID。主题内 C001、R001 按现有最大编号递增，标题改动不重命名 ID。日期示例仅演示格式，实际使用用户当地日期。

读取当前 topic、最近一份 session 和本次相关概念。查到期项先扫概念元数据，再挑预算容得下的正文，默认最多五张。不要每次读取全部历史、所有主题或全部资源。

## 权威记录

| 信息 | 唯一权威位置 | 其他文件的角色 |
|---|---|---|
| 目标、偏好、长期预算 | topic.md | index 只存链接 |
| 当前掌握、复习日期 | concepts/Cxxx.md | 日记记录当时快照；总览由卡片生成 |
| 资料链接、使用状态 | resources/Rxxx.md | 其他位置只链接 |
| 每次尝试和计划执行历史 | sessions/日期-序号.md | 能力卡通过证据路径指向它 |

先写会话证据，再更新相关卡片。中断导致不一致时，根据尚未同步的有效证据修复卡片；不把旧快照覆盖新状态。发现无法解释的冲突就保留原件并说明，不能猜。

## 主题档案

```yaml
---
type: learning-topic
schema_version: 2
topic_id: python-csv
title: "用 Python 清洗 CSV"
status: active
updated: 2026-10-30
---
```

正文保留：现实用途；成功标准；前置与已知基础（区分自评/证实）；长期时间预算；用户偏好；范围外内容；目标变更记录。`status` 为 active/paused/completed；完成主题不会自动取消复习，暂停主题才不纳入主动队列。

## 能力卡：一项可检验能力一张

```yaml
---
type: learning-concept
schema_version: 2
topic_id: python-csv
concept_id: C001
title: "识别并处理缺失值"
mastery: 未验证
review_stage: 0
next_review: null
last_assessed: null
last_success: null
success_streak: 0
transfer_passed: false
evidence: []
updated: 2026-10-30
---
```

正文：验收标准；必要前置（能力 ID）；当前缺口/错因假设；适用边界；可选速查说明。不要存一套与会话证据重复且独立维护的错题总表。

字段语义：

- `mastery` 对应 [评估](assessment.md) 五种状态。
- `review_stage` 为 0–4；`next_review` 是当地日期或 null。
- `last_assessed` 为最后一次可判定实际作答日；`last_success`、`success_streak` 是最近一次失败/依赖提示之后，连续独立通过的最近日期和不同日期数；失败时清空日期并归零次数。历史成功仍在会话证据中。
- `transfer_passed` 仅在这段连续通过的证据含有效新变式/迁移时为 true；失败时重置。如果相关证据被撤销，重新计算。
- `evidence` 放会话相对路径加稳定尝试锚点，如 `sessions/2026-10-30-01.md#attempt-a01`；序号在会话内唯一。

无证据不补造日期。自定义复习节奏存 `review_intervals` 数组并记录用户选择；未提供则使用评估文件默认值。其他字段按实际用途添加，不预建空表。

## 资源卡

```yaml
---
type: learning-resource
schema_version: 2
topic_id: python-csv
resource_id: R001
title: "资源实际标题"
url: null
status: 候选
link_status: 未核实
checked_on: null
concepts: [C001]
---
```

正文只需：为什么现在用；已核实的阅读范围；验收动作；来源与核查说明。选填费用、预估时长和优先级；未知不编。`status` 见资源规则；`link_status` 为未核实/可访问/失效。checked_on 只在实际核查后填写；可访问不等于内容已被全面验证。

## 会话记录

会话使用 `日期-序号` ID，沿用当前会话文件，不每轮新建。同一天重开独立学习会话递增序号，跨午夜的同一会话仍沿用 ID，具体尝试另记实际日期。

元数据：type=learning-session、schema_version=2、topic_id、session_id、started、updated；只有真实计时/用户报告时才写 actual_minutes。计划预算单独记 budget_minutes。

正文按需要生成：

- **行动记录：** 稳定 action_id、承诺日期、任务、验收标准、估计分钟、状态（planned/done/partial/cancelled/replaced）、状态变更及原因。不能用预算推算完成。
- **尝试证据：** 使用 `## attempt-a01` 标题；保存实际日期、concept_id、题目、回答或作品路径、提示与否、结果、判定依据、是否新变式/迁移。看过答案后重答需明确注明。
- **续接摘要：** 已证实的能力、未完任务、下次第一件事。
- **可选反思：** 观察事实、原因假设、验证动作。

用户没有作答时可以记录“未测”和待完成任务，不能生成仿真的用户答案。证据更正追加到同一尝试下并注明原因和日期；相关卡片重算。

## 安全增量写入与去重

写前读现有文件，保留用户手写内容。当前状态可原位更新，历史尝试只追加或追加更正；同一 attempt_id/action_id 重试写入不重复计数。能力卡引用已有尝试时先检查是否已经处理。

同一 session 可以追加多次，但不重复计入同一尝试。每次将能力卡所有受影响字段和 evidence 引用作为同一次完整卡片更新，不能先加次数后写证据链接。重试时按证据 ID 去重；若字段可能只更新了一半，则按日期与尝试顺序从该能力的有效证据重建当前状态，不能再盲目递增。

写完读回确认；多文件更新中断且仍能写会话时，在会话尾记“待同步到 Cxxx”，恢复后据证据补齐。完全不可写时在回复中说明未完成项。不能将失败写入声称为成功。

## 可选 Dataview

仅在用户已有插件且需要自动总览时添加。下面查询供当前 topic 目录中的总览使用，以卡片 frontmatter 为数据源；无插件直接读同样字段。

```dataview
TABLE title AS 能力, mastery AS 掌握, next_review AS 复习日
FROM ""
WHERE type = "learning-concept"
  AND topic_id = this.topic_id
  AND next_review != null
  AND date(next_review) <= date(today)
SORT next_review ASC
```

总览文件需带正确 topic_id；可将 FROM 收窄到用户实际学习库路径以降低扫描量。仅在用户选择当前主题时使用此视图；全库视图若另建需关联主题暂停状态。普通 Markdown 表格行不是独立能力记录。
