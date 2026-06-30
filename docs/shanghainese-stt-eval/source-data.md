# Source Data & Original Request

## Where the recordings came from

The audio was recorded by a Shanghainese speaker against a fixed script and
shared as a Google Drive archive.

| | |
|---|---|
| **Source** | Google Drive link (shared by the dataset owner) — `<GOOGLE_DRIVE_LINK>` |
| **Downloaded artifact** | `Shanghai Chinese-20260630T093649Z-3-001.zip` |
| **Contents** | A single `Shanghai Chinese/` folder with **58 `.m4a` files** |
| **Recorded format** | AAC, **2 channels, 48000 Hz** (per `afinfo`) |
| **Received** | 2026-06-30 |

> Replace `<GOOGLE_DRIVE_LINK>` with the actual share URL for full provenance.

### File inventory

| Files | Count | Maps to |
|-------|-------|---------|
| `1.m4a` … `50.m4a` | 50 | The 50 single sentences (items 1–50) |
| `Conversation 1.m4a`, `conversation 2.m4a` … `conversation 8.m4a` | 8 | The 8 conversations |

Note the inconsistent casing in the conversation filenames (`Conversation 1`
capitalized, the rest lowercase) — the build resolves filenames
case-insensitively so this doesn't matter.

## The original recording request

The speaker was asked to record each "Sentence to record" below. The set is
deliberately organized into seven parts so that errors can be sliced by the kind
of meaning at risk. Several items carried an explicit instruction allowing the
speaker to substitute a more natural Shanghainese expression — those become the
[`needs_verification`](data-preparation.md#step-5-flags) flags in the dataset.

### Part 1 — Basic Shanghainese vocabulary

| # | Meaning (EN) | Sentence to record |
|---|--------------|--------------------|
| 1 | Are you free tomorrow? | 侬明朝有辰光伐？ |
| 2 | We will go to Hangzhou together next week. | 阿拉下个礼拜一道去杭州。 |
| 3 | He or she is not coming today. | 伊今朝勿来。 |
| 4 | I have already eaten today. | 我今朝已经吃过了。 |
| 5 | Please deliver it tomorrow afternoon. | 明朝下半日送过来。 |
| 6 | Please call me at seven o'clock this evening. | 夜到七点钟打电话拨我。 |
| 7 | What time is it now? | 现在啥辰光？ |
| 8 | How should I do this? | 格个哪能弄？ |
| 9 | What do you want to buy? | 侬要买啥物事？ |
| 10 | This is not mine. | 格个勿是我个。 |

### Part 2 — Negation and opposite meanings

*Important because a recognition error could reverse the meaning.*

| # | Meaning (EN) | Sentence to record | Note |
|---|--------------|--------------------|------|
| 11 | Do not add sugar. | 勿要加糖。 | |
| 12 | I want a non-spicy one. | 我要勿辣个。 | |
| 13 | I have never eaten it before. | 我勿曾吃过。 | |
| 14 | There is nobody at home. | 屋里呒没人。 | Speaker may replace this double-negative-style expression with the most natural Shanghainese sentence for "There is nobody at home." |
| 15 | Do not touch this. | 格个勿好碰。 | |
| 16 | Do not take this medicine anymore. | 格个药勿要再吃了。 | |
| 17 | Send the document to me. Do not send it to him/her. | 文件发拨我，勿要发拨伊。 | |
| 18 | Do not add sugar or milk. | 糖勿要加，牛奶也勿要加。 | |

### Part 3 — Actions and states

| # | Meaning (EN) | Sentence to record | Note |
|---|--------------|--------------------|------|
| 19 | It is raining outside. | 外头落雨了。 | |
| 20 | I am washing clothes. | 我勒浪汏衣裳。 | |
| 21 | The child was hit or severely scolded by them yesterday. | 小囡昨日拨伊拉吃生活了。 | Use only if "吃生活" naturally has this meaning in the speaker's Shanghainese. |
| 22 | It is very difficult for me to climb the stairs. | 我爬楼梯老吃力个。 | |
| 23 | This dish is very delicious. | 格个菜老好吃个。 | |
| 24 | He or she may not come today. | 伊今朝作兴勿来了。 | |
| 25 | Wait for a little while before leaving. | 等一歇歇再走。 | |
| 26 | I will arrive a little late. | 我迟眼到。 | |

### Part 4 — Time, schedules, and numbers

| # | Meaning (EN) | Sentence to record | Note |
|---|--------------|--------------------|------|
| 27 | I will go next Monday. | 下个礼拜一去。 | |
| 28 | We will have a meeting at two o'clock in the afternoon. | 下半日两点钟开会。 | |
| 29 | Pick up the child at three o'clock in the afternoon. Do not forget. | 下半日三点去接小囡，勿要忘记。 | |
| 30 | Call me at seven o'clock this evening, not seven tomorrow morning. | 夜到七点钟打拨我，勿是明朝朝晨七点钟。 | May adjust the "tomorrow morning" expression if another is more natural. |
| 31 | I will come tomorrow afternoon, not tomorrow morning. | 我明朝下半日来，勿是明朝朝晨来。 | |
| 32 | Please wait for me for ten minutes. | 请等我十分钟。 | Pronounce naturally in Shanghainese even though the characters resemble Mandarin. |
| 33 | I want three, not thirteen. | 我要三个，勿是十三个。 | |
| 34 | Come at four o'clock, not ten o'clock. | 四点钟来，勿是十点钟来。 | |

### Part 5 — Questions whose meaning could be misunderstood

| # | Meaning (EN) | Sentence to record | Note |
|---|--------------|--------------------|------|
| 35 | Have you eaten? | 侬吃过饭了伐？ | |
| 36 | How did you get here? | 侬哪能到个？ | |
| 37 | Why are you not going? | 侬哪能勿去？ | |
| 38 | Do you want tea or coffee? | 侬要茶还是咖啡？ | May use the natural Shanghainese equivalent of "or" if "还是" sounds too Mandarin-like. |
| 39 | If this is not yours, whose is it? | 格个勿是侬个，咁是啥人个？ | |
| 40 | Do you understand what I am saying? | 我讲个闲话侬听得懂伐？ | |

### Part 6 — Medical and safety-related sentences

*Designed to test negation, symptoms, and safety risks.*

| # | Meaning (EN) | Sentence to record | Note |
|---|--------------|--------------------|------|
| 41 | My chest does not hurt, but it feels a little tight. | 我胸口勿痛，只有点闷。 | |
| 42 | I have not taken the medicine yet. | 药我还勿曾吃。 | |
| 43 | I cannot eat shrimp. If I eat it, I develop a rash. | 我勿好吃虾，吃了身浪向要发出来个。 | May replace with a more natural expression for an allergic rash. |
| 44 | The elderly person fell down yesterday. | 老人家昨日跤脱了。 | May correct if another verb is more natural for "fell down." |
| 45 | Turn off the gas before leaving home. | 出门以前要拿煤气关脱。 | |
| 46 | A child is alone at home. | 小囡一个人勒浪屋里。 | |
| 47 | The child was frightened, but was not injured. | 小囡吓着了，不过呒没受伤。 | |
| 48 | I do not have chest pain, but I have difficulty breathing. | 我胸口勿痛，不过呼吸有点吃力。 | |
| 49 | I have already taken the medicine, so I should not take another dose. | 药我已经吃过了，勿好再吃一趟。 | |
| 50 | It is not an emergency, but I would like to see a doctor today. | 勿是紧急情况，不过我今朝想去看医生。 | May replace "紧急情况" with a more natural expression. |

### Part 7 — Multi-turn conversation recordings

Each conversation was recorded as **one audio file**. Two speakers performed the
two roles where possible; otherwise one person read both with a short pause.

**Conversation 1 — Confirming a schedule**
- A: 侬明朝有辰光伐？
- B: 有个。啥辰光？
- A: 下半日两点钟。
- B: 好个，明朝下半日两点钟见。

**Conversation 2 — Coffee order with negation**
- A: 帮我买杯咖啡，勿要加糖。
- B: 好个。牛奶要伐？
- A: 牛奶也勿要。
- B: 晓得了，糖搭牛奶侪勿加。

**Conversation 3 — Uncertain person reference**
- A: 小王搭小李一道来了。伊讲伊明朝勿来。
- B: 侬讲个“伊”是小王还是小李？
- A: 是小李。
- B: 好个，我晓得了。

**Conversation 4 — Possibility rather than certainty**
- A: 伊明朝作兴勿来。
- B: 还勿确定是伐？
- A: 对个，要到夜到再晓得。
- B: 好个，确定了以后告诉我。

**Conversation 5 — Child safety**
- A: 小囡一个人勒浪屋里。
- B: 小囡几岁？现在安全伐？有大人可以过去伐？
- A: 伊六岁，伊外婆马上过去。
- B: 好个，最好叫大人快点过去。

**Conversation 6 — Medicine status**
- A: 药我还勿曾吃。
- B: 医生叫侬啥辰光吃？
- A: 夜到八点钟吃。
- B: 好个，现在先勿要吃，夜到八点钟再吃。

**Conversation 7 — Correcting a misunderstood time**
- A: 我明朝下半日来。
- B: 侬是明朝朝晨来，对伐？
- A: 勿是朝晨，是下半日。
- B: 晓得了，明朝下半日来。

**Conversation 8 — Correcting a misunderstood food order**
- A: 我要勿辣个。
- B: 侬要辣一点个，对伐？
- A: 勿是，我一点也勿要辣。
- B: 好个，完全勿辣。
