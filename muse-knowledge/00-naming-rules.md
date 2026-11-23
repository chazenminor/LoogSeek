# muse-knowledge 文件编号与命名规则

> 版本：v1.0 · 制定日期：2026-11-23 · 适用范围：`chazenminor/LoogSeek` 仓库下的 `muse-knowledge/` 目录
>
> 原则：**内容优先，日期其次**。先按内容归类，再按日期排序；文件名只承载“是什么、在哪、哪一天”，版本、状态、来源放在文件头里，不塞进文件名。

## 1. 总体结构

```
muse-knowledge/
00-meta/
index.md # 总索引：按内容域、再按日期列出全部文档
naming-rules.md # 本文件
github-multi-muse.md # GitHub 多 Muse 授权与安装说明
changelog.md # 规则本身的变更记录
01-autoresearch/
2026-11-22-rsi-experimental-loop.md
02-methodology/
2026-11-22-control-theory-harness.md
03-harness/
phase1/ # 代码按阶段/版本管理，不按日期编号
harness.py
README.md
examples/
04-notes/
2026-11-23-metr-reward-hacking-verification.md
```

- 一级目录 = 内容域，二级文件 = 具体文档。
- 一个文件只讲一个主题；一个主题有重大修订时，优先在原文件里升级版本（见 §5），而不是另起一个同名文件。
- 代码、数据、二进制产物不按本文档编号，见 §6。

## 2. 内容域与编号

| 域编号 | 目录 | 域代码 | 收什么 | 不收什么 |
|---|---|---|---|---|
| 00 | `00-meta/` | MT | 索引、命名规则、仓库说明、变更记录 | 具体研究内容 |
| 01 | `01-autoresearch/` | AR | AutoResearch、AI Scientist、RSI、实验循环的文献调研与分析 | 纯方法论通论 |
| 02 | `02-methodology/` | EM | 实验方法论、控制论、统计、因果、贝叶斯优化、序贯检验 | 具体某个系统的评测结果 |
| 03 | `03-harness/` | HC | harness 设计、蓝图、代码、实验规格、运行记录 | 文献综述本身 |
| 04 | `04-notes/` | NT | 读书笔记、核验更正、短备忘、待办清单 | 已成型的正式报告 |

新增内容域时：取下一个未用的两位数编号，在本表追加一行，**不重排已有编号**。编号一旦分配就不再复用、不再改动，保证旧引用不失效。

## 3. 文件编号：稳定 ID

每个正式文档在文件头写一个**稳定 ID**，文件名变了、目录搬了，ID 不变，引用时用 ID。

格式：

```
MK-<YYYYMMDD>-<域代码>-<三位序号>
```

- `MK` = muse-knowledge 的固定前缀。
- `<YYYYMMDD>` = 文档首次成文的日期（研究/调研完成的日期），不是最后修改日期。
- `<域代码>` = 上表中的 AR / EM / HC / NT / MT。
- `<三位序号>` = 该域、该日期内的顺序号，从 001 开始，按成文顺序递增。

示例：

| 文档 | 稳定 ID |
|---|---|
| AutoResearch RSI 实验循环文献调研（2026-11-22） | `MK-20261122-AR-001` |
| 实验方法论与控制论 harness 分析（2026-11-22） | `MK-20261122-EM-001` |
| METR reward hacking 数字核验更正（2026-11-23） | `MK-20261123-NT-001` |

## 4. 文件命名规则

### 4.1 文件名格式

```
<YYYY-MM-DD>-<topic-slug>.md
```

- 日期在前：同一目录下按文件名排序，自然就是按时间排序。
- `<topic-slug>`：英文小写、用连字符连接、只含 `a-z`、`0-9`、`-`，不超过 40 个字符，能一眼看出主题。
- 文件名里**不写中文、不写空格、不写版本号**。中文标题写在文件正文第一行。
- 扩展名一律 `.md`（文档类）；代码、数据按各自语言/格式的扩展名。

示例：

```
01-autoresearch/2026-11-22-rsi-experimental-loop.md
02-methodology/2026-11-22-control-theory-harness.md
04-notes/2026-11-23-metr-reward-hacking-verification.md
```

### 4.2 文件头（每个文档必须有）

放在正文最前面，用一段简短的元信息块：

```markdown
# AutoResearch 与递归自我改进（RSI）实验循环：文献调研分析报告

> ID：MK-20261122-AR-001 · 版本：v1.0 · 创建：2026-11-22 · 更新：2026-11-22
> 状态：已发布 / 草稿 / 已核验 / 已废弃
> 来源：英文一手论文、官方博客、GitHub（见文末参考文献）
> 相关：MK-20261122-EM-001
```

字段说明：

- `ID`：§3 的稳定 ID，全库唯一。
- `版本`：`v<主版本>.<次版本>`，主版本 = 结论或结构大改，次版本 = 纠错、补来源、小幅补充。
- `创建`：首次成文日期，与文件名日期一致。
- `更新`：最后一次实质修改日期。
- `状态`：`草稿`（未完成）、`已发布`（可引用）、`已核验`（关键数字已对原文核对）、`已废弃`（被新文档取代，注明取代者 ID）。
- `相关`：列出直接相关的文档 ID，方便沿着内容跳转。

### 4.3 什么不放进文件名

- 不放版本号：`rsi-experimental-loop-v2.md` 是错的，版本在文件头里。
- 不放作者、状态、优先级：`final`、`latest`、`important` 这类词不进文件名。
- 不放中文、空格、下划线（目录名除外，目录用 `NN-domain` 这种带连字符的形式）。

## 5. 修订与更正

- **小修订**（纠错、补来源、改措辞）：不改文件名、不改 ID，只升级文件头版本（v1.0 → v1.1），在文末或文件头下方加一行变更记录：`2026-11-23：更正 METR 提示词实验的说法，见 §6.3.1。`
- **大修订**（结论变了、结构重写）：升级主版本（v1.x → v2.0），同样不改文件名和 ID，变更记录里写清改了什么、为什么。
- **被取代**：旧文档不删除，状态改为 `已废弃`，文件头注明 `被 MK-xxxxxxxx-xx-xxx 取代`，新文档在 `相关` 里回指旧文档。
- **核验更正**（如 METR 数字）：在原文件里直接更正并标注 ``，同时在 `04-notes/` 下留一篇短的核验备忘，ID 单独编号，两处互相引用。

## 6. 代码与实验产物的编号

代码不按日期命名文件，改用目录 + git 提交管理：

```
03-harness/
phase1/ # 第 1 阶段：先做到诚实
harness.py # validate / lock / run / report / reproduce
README.md
examples/
exp-lr-demo.yaml
phase2/ # 第 2 阶段：BO 控制器 + mSPRT + held-out（待建）
```

- 阶段目录名固定为 `phase1`、`phase2`、`phase3`，与报告里的三阶段路线对应。
- 每次实验运行的产物（`events.jsonl`、`summary.json`、`report.md`）放在 `experiments/<exp-id>/` 下，`exp-id` 格式为 `exp-YYYY-MM-DD-NNN`，例如 `exp-2026-11-22-001`。
- 二进制文件（`.db`、`.pt`、图片等）不直接进知识库文档目录，需要时在文档里写清存放位置和复现命令。

## 7. 现有文件的迁移对照

| 现有位置（2026-11-23 前） | 新位置（按本规则） | 稳定 ID |
|---|---|---|
| `muse-knowledge/reports/autoresearch-rsi-experimental-loop.md` | `muse-knowledge/01-autoresearch/2026-11-22-rsi-experimental-loop.md` | MK-20261122-AR-001 |
| `muse-knowledge/reports/experimental-methodology-harness.md` | `muse-knowledge/02-methodology/2026-11-22-control-theory-harness.md` | MK-20261122-EM-001 |
| `muse-knowledge/harness-mvp-phase1/` | `muse-knowledge/03-harness/phase1/` | —（代码，按 §6 管理） |

迁移原则：先在新位置建好文件、确认内容一致，再删旧位置的文件，不留两个版本并存。删除前在旧文件所在提交里留一条迁移记录。

## 8. 索引维护

- `00-meta/index.md` 是唯一的总索引，按域编号、再按日期列出每个文档的 ID、标题、版本、状态和一句话摘要。
- 每新增或废弃一个文档，就同步更新索引；索引本身也按 §5 的规则记版本。
- 不在多个地方各维护一份清单，避免分叉。

## 9. 速查：新建一个文档时

1. 先定内容域：它属于 01 / 02 / 03 / 04 中的哪一个？没有合适的再新建域。
2. 再定日期：用首次成文的日期，格式 `YYYY-MM-DD`。
3. 写 slug：英文、连字符、40 字符以内。
4. 分配 ID：`MK-<YYYYMMDD>-<域代码>-<下一序号>`，查索引确认序号没被用过。
5. 按 §4.2 写文件头，状态先写 `草稿`，完成后再改为 `已发布`。
6. 更新 `00-meta/index.md`。
