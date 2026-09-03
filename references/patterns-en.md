# 英文专属规则详表

配合 `SKILL.md` 使用。

## 先说证据强度

本项目收集的英文语料**全部是人写文本**：本科教科书、研究生专著、上市公司
年报（10-K）、政府白皮书、国际组织文件。**没有英文 AI 生成语料。**

后果是：

- **语域阶梯部分有实测支撑**，可以放心用
- **机器痕迹部分没有**，只能沿用上游规则，标注为未验证
- 本项目的人写语料**直接否定**了三条上游规则，见下文

中文侧的情况相反（有机翻语料做对照），所以两个文件的可信度结构不同。
详见第三阶段的研究说明文档。

---

## 实测否定的规则

这一节比新增规则更重要。以下三条常见的"AI 检测"判据在英文人写语料里
站不住脚，**不要用**。

### 破折号频率不携带信号

| 语料 | em dash 密度 |
|---|---|
| 本科教科书 A | 0.98 |
| 本科教科书 B | **0.00** |
| 本科教科书 C | **2.34** |
| 研究生专著 | 0.47 |
| 10-K（两家） | 0.06 – 0.10 |
| 政府白皮书 | 0.00 |

同为本科商科教科书，一本完全不用，一本用到 2.34，差距超过 20 倍。
**破折号密度是作者个人习惯，不是机器指纹。**

保留的判据只有一条：用破折号做**戏剧性揭示**（`The answer is clear —
costs must fall.`）在商务学术语域里不合适，改用冒号或分号。这是语域问题，
不是频率问题。

### 被动语态不携带信号

| 语料 | 被动式密度 |
|---|---|
| 本科教科书 | 4.71 – 12.00 |
| 研究生专著 | 10.75 |
| 10-K | 7.84 – 12.39 |
| 政府白皮书 | 4.71 |
| 国际组织文件 | 6.79 |

区间完全重叠，无区分度。而且方法学章节、法律文本、财务附注的被动语态是
**规范要求**。不要一律改主动。

### 名词化不是机器痕迹

| 语料 | 名词化密度 |
|---|---|
| 本科教科书 | 34.8 – 38.6 |
| 研究生专著 | 50.0 |
| 10-K | 49.6 – 50.6 |
| 国际组织文件 | 64.2 |
| 政府白皮书 | 74.7 |

这是**语域标记**：越正式、越机构化，名词化越高。两家不同行业的 10-K
分别是 49.6 和 50.6，一致性极高，说明它由文体决定而非作者决定。

所以名词化归 B 族，按目标语域调整，不作身份判据。

---

## 语域阶梯（实测，可放心用）

| 语料 | 句长均值(词) | p90/p50 | 名词化 | 抽象名词 | AI 词表 | 认知限定 |
|---|---|---|---|---|---|---|
| 政府白皮书 | 23.5 | 1.77 | 74.7 | 3.50 | 2.05 | 0.36 |
| 国际组织文件 | 24.3 | 1.77 | 64.2 | 2.03 | 1.56 | 2.19 |
| 10-K | 28.5 – 30.4 | 1.77 – 1.78 | 49.6 – 50.6 | 0.40 – 1.16 | 0.30 – 0.35 | 5.41 – 9.43 |
| 本科教科书 | 24.2 – 30.3 | 1.85 – 2.05 | 34.8 – 38.6 | 0.09 – 0.32 | 0.06 – 0.09 | 4.03 – 4.58 |
| 研究生专著 | 37.2 | 2.27 | 50.0 | 1.16 | 0.09 | 1.55 |

**p90/p50 是唯一对样本量稳健的句长指标。** 两份 10-K 样本量差 5 倍
（663 句对 3504 句），比值却是 1.78 对 1.77。变异系数 CV 做不到这一点：
同一本教科书全样本 CV 是 0.953，降采样到 300 句就掉到 0.609。所以
**不要用 CV 做跨文本比较**。

### 怎么用这张表

- 目标 L3（商务报告、执行摘要、教科书）：句长 24–30 词，p90/p50 约 1.9，
  名词化 35–39
- 目标 L1（期刊投稿）：句长可到 37，p90/p50 约 2.3，名词化 50
- 从 L1 降到 L3：句长砍约三成，长句（>50 词）占比从 19% 压到 6%，
  抽象名词砍到十分之一

### AI 词表的正确读法

delve、intricate、pivotal、robust、seamless、testament、underscore、
showcase、foster、comprehensive、navigate、unlock、harness、elevate、
myriad、vibrant

人写教科书里这些词几乎不出现（0.06–0.09），但**人写的政府白皮书是 2.05，
国际组织文件是 1.56**，比教科书高 20 到 30 倍。

所以这张表测的是**宣传—机构语域**，不是机器身份。规则要写成"你这份文档
该不该用这个语域"，不是"用了就是 AI"。

唯一可用的硬阈值：如果一份**面向专业读者的分析文档**（备忘录、尽调报告、
论文）AI 词表密度超过 1，几乎确定语域跑偏了，不论作者是谁。

### 连字符词对堆砌

高频词：third-party、cross-functional、client-facing、data-driven、
decision-making、high-quality、real-time、long-term、end-to-end

商务英文里这批词本身就常见，问题是**位置不分**地一律加连字符。
规则是：**名词前加，名词后不加。**

> `a high-quality report` 正确
> `the report is high-quality` 错误，应为 `the report is high quality`

> 改前：The cross-functional team delivered a data-driven report. The team
> is cross-functional and the methodology is data-driven.
>
> 改后：The cross-functional team delivered a data-driven report. The team
> is cross functional and the methodology is data driven.

### 其他条目的英文对照

以下模式中英通用，中文版的完整说明与示例见 `patterns-zh.md` 对应条目。
这里只给英文触发词。

| 模式 | 英文触发词 | 中文版条目 |
|---|---|---|
| 假装揭示深层真相 | the real question is、at its core、in reality、what really matters、fundamentally、the heart of the matter | A13 |
| 预告下一个要点 | let's dive in、let's explore、here's what you need to know、let's break this down、without further ado | A14 |
| 标题在首句被复述 | 标题后跟一句只复述标题的短句 | A15 |
| 强行金句与短句堆叠 | 连续三个以上戏剧性短句 | A16 |
| 公式化警句 | X is the Y of Z、X becomes a trap、the language of、the currency of、the architecture of | A17 |
| 回答无人提出的反驳 | this isn't really about、I'm not saying、to be clear、don't get me wrong、some might say... but | A18 |
| 否决虚构的备选方案 | a tempting approach would be、one might be tempted to、you might think... but、it would be easy to just | A19 |
| 点名背书代替证据 | 机构名或人名充当论证本身且无具体出处 | A20 |
| 描述上一个版本 | 正文叙述"原来如何、后来改成如何" | A21 |
| 伪坦白开场 | Honestly?、Look、Here's the thing、Let's be honest、Real talk（作为独立开场钩子） | 由语域下限规则覆盖 |

「伪坦白开场」在本 skill 里不单独立条，因为它属于 L4 以下语域，
主文件的语域下限规则已经禁止。

---

## A 族：沿用上游，未经验证

以下规则来自上游 skill，本项目无英文 AI 语料可供验证。它们在直觉上成立，
但请知道它们没有本项目的数据支撑。

### 人写基线接近零的两项

这两项虽未用 AI 语料验证，但**人写基线极低**，所以出现即可改：

| 项 | 人写实测 |
|---|---|
| 弱动词 + 名词化（`conduct an analysis`） | 0.00 – 0.14 |
| 虚位主语（`there is` / `it is ... that`） | 0.25 – 2.03 |

- `conduct an analysis` → `analyze`
- `perform an evaluation` → `evaluate`
- `provide an explanation` → `explain`
- `There is a need to reduce costs` → `Costs must fall`
- `It is important to note that X` → `X`

### of 链

`the improvement of the efficiency of the process` →
`gains in process efficiency`

人写实测 0.12 – 0.49，本来就低，超过 0.5 值得检查。

### 标题大写

正文标题用 sentence case，不用 Title Case。

**例外**：许多期刊、机构和公司的 house style 明确要求 Title Case。
有既定规范时服从规范，不要按本规则改。

### 引号与连字符

- 引号在同一篇内保持一致（全用弯引号或全用直引号）
- 连字符 `-`、短破折号 `–`、长破折号 `—` 各有用途，不要混用
- 数值区间用 `–`（`2019–2024`），不用 `-`

### 三段式滥用

`X, Y, and Z` 结构连续出现三次以上就该打散。这一条上游有，
本项目未测。

### 聊天残留

- 删掉 `I hope this helps`、`Here's a breakdown`、`Let me analyze`
- 删掉知识截止声明与能力免责
- 正文不出现 emoji

---

## 英文限定语的特殊性

英文侧的限定语必须单独讲，因为**它和中文的规范完全相反**。

| 语料 | 认知限定密度 |
|---|---|
| 英文本科教科书 | 4.03 – 4.58 |
| 英文 10-K | 5.41 – 9.43 |
| 中文人写各语域 | 0.29 – 0.91 |

英文学术与商务写作用 `may / could / suggest` 是**常态甚至义务**：

- 10-K 风险因素章节的 `may adversely affect` 是法律责任要求
- 学术论文中超出证据的断言是学术不端

所以英文侧**不要削减限定语**，只检查一件事：限定强度是否对齐证据强度。
无差别地给每句话套软垫是问题，该限定的地方限定不是问题。

中英互译时这是最大的陷阱：英文的 4.5 搬进中文会显得畏畏缩缩，
中文的 0.5 搬进英文会显得武断。**限定密度必须按目标语言重设，不能照搬。**
