# humanizer-formal

降低商务与学术文本的 AI 生成感，中英文均适用。规则取值来自对约 56 万词英文、
184 万字中文公开语料的实测，不是凭印象设定。

> **衍生声明**
> 本项目是 [op7418/Humanizer-zh](https://github.com/op7418/Humanizer-zh) 的衍生作品，
> 后者译改自 [blader/humanizer](https://github.com/blader/humanizer)，并参考了
> [hardikpandya/stop-slop](https://github.com/hardikpandya/stop-slop)。
> 模式分类法的思想源头是维基百科的
> [Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing)。
> 依 MIT 许可发布，四层署名见 [`LICENSE`](LICENSE)，逐项改动记录见
> [`NOTICE.md`](NOTICE.md)。

---

## 和上游的区别

本项目不替代上游，两者可以并存。上游面向通用散文与随笔，本项目面向商务与
学术书面语。

| | humanizer / Humanizer-zh | humanizer-formal |
|---|---|---|
| 目标文体 | 通用散文、博客、随笔 | 商务报告、学术论文、执行摘要 |
| 语言 | 分别为英文 / 中文 | 中英双语，规则按语言分开 |
| 对"注入个性" | 鼓励第一人称、幽默、锋芒 | **明令禁止**，在此语域会损害可信度 |
| 规则依据 | 维基百科社区观察 | 上游规则 + 本项目实测语料 |
| 判定架构 | 单一模式清单 | 机器痕迹与语域标记**分离判定** |

最后一行是核心差异。实测发现**机器翻译的中文里抽象名词密度是全部语料中最低
的**（0.12–0.50‰），比教科书还低；而人写的咨询研究报告最高（7.12‰）。
所以看到「赋能、闭环、生态、体系」就判定是 AI 写的，方向是反的——那些词
标记的是宣传机构语域，与作者是人还是机器无关。

规则因此分成两族：

- **A 族　机器痕迹**：人写不出来的东西。连接词过密、限定语泛滥、
  「的」字堆叠、破折号乱用。一律要改。
- **B 族　语域标记**：人也这么写，但不该出现在你这份文档里。抽象名词、
  buzzword、名词化。按目标语域调整，不是一律删。

混为一谈必然误判。

---

## 适用与不适用

**适用**：商务报告、执行摘要、投资备忘录、尽调报告、董事会材料、年报、
行业分析、学术论文、学位论文、文献综述、会议摘要。

**不适用**：随笔、营销文案、社交媒体、创意写作、代码注释。这些场景请用上游。

---

## 安装

以下命令来自上游文档与 Claude Code 惯例。工具链更新较快，安装失败时
请对照 [官方文档](https://docs.claude.com) 确认当前写法。

### Skills CLI

```bash
npx skills add jiutianxvanyin/humanizer-formal --global
```

去掉 `--global` 则只安装到当前项目。加 `--agent <name>` 指定接收的 agent，
之后重新加载 skills。

### Claude Code 插件

```text
/plugin marketplace add jiutianxvanyin/humanizer-formal
/plugin install humanizer-formal@humanizer-formal
```

调用命令是 `/humanizer-formal:humanizer-formal`。仓库已带
`.claude-plugin/plugin.json` 与 `marketplace.json`。

### Claude Desktop 与 claude.ai

下载 Releases 里的 `humanizer-formal.skill` 直接上传，或把仓库下载为 ZIP 上传。

### 手动安装

**必须复制整个目录，不能只复制 `SKILL.md`。** 本 skill 用渐进披露，
规则详表在 `references/` 下，缺了会失效。

```bash
git clone https://github.com/jiutianxvanyin/humanizer-formal.git \
  ~/.claude/skills/humanizer-formal
```

Windows 路径为 `%USERPROFILE%\.claude\skills\`。安装后目录结构应为：

```
~/.claude/skills/humanizer-formal/
├── SKILL.md
├── LICENSE
├── NOTICE.md
└── references/
    ├── patterns-zh.md
    ├── patterns-en.md
    ├── register-business.md
    └── register-academic.md
```

---

## 使用

### 两种模式

**审校模式（默认）**　给它已写好的文本，它先判定目标语域，标出 A 类问题，
改完再对照语域检查 B 类，最后自查有没有增删事实。

**起草模式**　写之前先定语域，按结论前置、每个判断挂数据的方式写，
写完自查最易犯的三条。

### 三种输出

| 调用方式 | 返回 |
|---|---|
| 粘贴文本 | 改写稿 + 变更清单（注明命中规则与理由） |
| 指定文件 | 只把最终文本写进文件，只改散文，代码块与数据不动 |
| 被其他任务调用 | 只返回最终文本 |

### 示例

```
用 humanizer-formal 审校这段执行摘要：

在当今数字化转型不断深入的背景下，公司面临前所未有的挑战。因此，我们
对三条产品线进行了全面评估。此外，评估涵盖多个维度。综上所述，B 线
可能在一定程度上存在改进空间。
```

改写方向是结论前置、砍掉连接词、把无数据的程度词换成具体数字：

> 建议关停 B 线。B 线毛利率 4.2%，低于资金成本；产能利用率 51%，
> 三年未改善。A 线和 C 线均在正常区间。

---

## 语域标尺

整套规则挂在一把五级标尺上。默认目标是 L3。

| 级别 | 中文样例 |
|---|---|
| L1 论文腔／公文腔 | 本研究基于多维指标体系，对目标企业经营绩效进行了系统性实证考察，结果表明其在成本控制维度呈现显著的边际改善态势。 |
| L2 标准学术 | 我们用五项指标评估了该公司的经营绩效。结果显示，其成本控制在样本期内显著改善。 |
| **L3 默认目标** | **我们用五项指标衡量这家公司的经营情况。2023 到 2025 年，它的单位生产成本从 42 元降到 31 元。** |
| L4 口语（禁止） | 我们看了五个指标，这家公司省钱省得挺明显，单位成本从 42 块掉到 31 块。 |
| L5 闲聊（严禁） | 说实话，这公司控成本这块儿真挺猛的，42 到 31，你敢信？ |

降级降的是**句法复杂度和抽象名词密度**，不是降正式度。L3 始终是书面语，
不出现语气词、人称抒情、市井词汇、缩略口语。"更简单"不等于"更随便"。

---

## 规则概览

中文侧 34 条，英文侧另有专属条目与对照表。

| 族 | 条数 | 内容 |
|---|---|---|
| A 机器痕迹 | 21 | 连接词过密、无差别限定、「的」字堆叠、长定语前置、「被」字滥用、轻动词结构、破折号、「请注意」祈使句、开场公式、段落形状同质化、标题公式、结论回音、假装揭示深层真相、预告下一个要点、标题在首句被复述、强行金句、公式化警句、回答无人提出的反驳、否决虚构的备选方案、点名背书代替证据、描述上一个版本 |
| B 语域标记 | 3 | 抽象名词与 buzzword、增强语堆砌、名词化后缀 |
| C 内容质量 | 6 | 无数据的程度词、口径与基准缺失、相关当因果、建议不可执行、引用可核查性、图表与正文重复 |
| D 反向规则 | 4 | 不要磨掉四字格排比、不要动术语、不要动数字口径、不要注入情绪 |

每条 A 族规则标注了可信度：`实测` 表示本项目语料验证过，`沿用` 表示继承
上游、未经验证。中文侧 21 条中多数为实测，英文侧的机器痕迹规则全部标注
`沿用`——因为本项目没有英文 AI 生成语料。

### 判定纪律

**单个指标不构成判定依据。** 几乎每个指标的人写区间与机器区间都相邻，
有些直接重叠。一个破折号、一次「此外」、一个弯引号都说明不了任何事；
同一段里出现三四种痕迹才是证据。

skill 内含假阳性防护清单，明确列出哪些特征**不该**被当成 AI 痕迹，
以及哪些人写痕迹必须保留。

---

## 规则从哪来

`RESEARCH.md` 记录完整的语料构成、量化方法、研究过程与局限。要点：

**语料覆盖七种中文语域、五种英文语域**，包括本科教科书、研究生专著、
政府白皮书、企业年报、央行监管报告、咨询研究报告、国际组织文件、
专业人工翻译与机器翻译。

**关键材料是两份机器翻译的双语教科书**，与英文原版页级或段级严格对齐。
内容、主题、语域全部固定，唯一变量是"人写英文"对"机器中文"，因此风格
差异可以干净地归因到生成过程。

**研究过程中有三个结论被数据推翻**，全部记录在案：中文四字格排比的方向
判断反了、句长变异系数被样本量混淆、连接词变化率的方向搞错了。保留这些
记录是为了让读者能判断哪些规则经过检验。

**规则本身不依赖任何脚本。** 实测数字只用于说明规则的把握程度，skill 给出
的是可读的启发式判据，比如"连接词自然频率大约每 10 句一次"。开发期用过的
量化脚本不在发行包内，因为它比不上专业检测工具，而且会让 skill 在没有
运行环境的地方失效。

---

## 已知局限

- **英文侧没有 AI 生成语料**，机器痕迹规则未经本项目验证，已逐条标注。
- **机器语料只有英译中一种生成方式**，不覆盖中文直接生成。
- **中文原创语料是医学与法学领域**，只用于标定句法基线，术语词表不适用。
- **上市公司年报只剩两个行业**，一份因字体损坏剔除。
- **中文咨询研究体样本来自同一家机构**，不能代表全部同类报告。

完整清单见 `RESEARCH.md`。

---

## 文件结构

```
humanizer-formal/
├── SKILL.md                    技能入口，含语域标尺与核心规则
├── LICENSE                     MIT，四层署名
├── NOTICE.md                   衍生关系与逐项改动记录
├── README.md                   本文件
├── RESEARCH.md                 语料、方法与局限（不打包进发行版）
├── CHANGELOG.md                变更记录
├── .github/ISSUE_TEMPLATE/     误伤反馈与语料贡献模板
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
└── references/
    ├── patterns-zh.md          中文条目全表
    ├── patterns-en.md          英文条目全表，含被实测否定的三条规则
    ├── register-business.md    商务文体
    └── register-academic.md    学术文体
```

`SKILL.md` 是给模型读的指令，本文件是给人读的介绍，分工不同。

---

## 贡献

最需要的两类贡献：

1. **英文人机平行语料**，用于补齐英文侧的机器痕迹验证
2. **中文直接生成的语料**，用于验证四字格堆砌在这条路径下的表现

发现规则误伤了正常文本，欢迎提 Issue 并附上原文片段。

---

## 许可

MIT。四层署名见 [`LICENSE`](LICENSE)。本项目的全部说明文字与示例均为重写，
未复制上游或维基百科的具体表达。

---

## English summary

`humanizer-formal` removes AI-writing patterns from **business and academic
prose** in both Chinese and English. It is a sibling of, not a replacement for,
[blader/humanizer](https://github.com/blader/humanizer).

Two things set it apart:

**Rules are calibrated against measured corpora** — roughly 563k English words
and 1.84M Chinese characters spanning undergraduate textbooks, graduate
monographs, SEC 10-K filings, government white papers, central bank reports,
consulting reports, UN documents, professional human translation, and machine
translation.

**Machine fingerprints and register markers are judged separately.** Abstract
nouns and buzzwords turned out to be *lowest* in the machine-translated corpus
(0.12–0.50 per 1,000 characters) and *highest* in human-written consulting
reports (7.12). Those words mark institutional and promotional register, not
machine authorship. Treating them as an AI tell gets the direction backwards.

The skill also inverts one upstream rule: human Chinese writers use three- and
four-character parallel constructions 2 to 10 times more often than machine
translation does. Flattening them makes good Chinese worse.

See `RESEARCH.md` for corpus composition, method, three retracted conclusions,
and known limitations. Instructions are in Chinese; examples are bilingual.
