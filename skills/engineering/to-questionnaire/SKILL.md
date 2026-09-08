---
name: to-questionnaire
description: Turn a decision you can't fully answer into a questionnaire for someone else to fill in.
disable-model-invocation: true
---

Turn something the user can't answer alone into a **questionnaire**: a Markdown document they hand to one person to fill in async, or fill out together over a meeting. The recipient holds knowledge the user lacks; the questionnaire pulls it out of them.

**Grill the send, not the subject.** Interview the user only about the _send_, which they can always answer: who it goes to, and what they need back. The questions in the document then target the **gap** between what the recipient knows and what the user needs.

1. **Who is it going to?** Ask, in one exchange, the recipient's role, expertise, and relationship to the user. This fixes the questionnaire's tone and how much context it must carry. Done when you know who the recipient is and what they know that the user doesn't.

2. **What do you need back?** Ask, in one exchange, the specific decisions or facts the user can't resolve alone and needs from this person. Done when you have a concrete list of what the user must walk away able to do or decide.

3. **Write the questionnaire.** Draft questions aimed at the gap from steps 1–2, following the Document structure below. Write it to `to-questionnaire-<slug>.md` in the current directory (slug from the topic) and report the path. Done when the file exists and every item the user named in step 2 is covered by a question.

## Document structure

Frame the document as a **discovery questionnaire**: the user lacks context, the recipient holds it. Order questions most-important-first, since async means you may only get one pass, and group them under `##` headings by theme once there are more than a handful. Write it using the template below.

<questionnaire-template>

# <问卷标题>

**目的：** 这份问卷为什么存在，以及它要支撑的决策。

**发送人：** <用户> — **接收人：** <对方> — **你的回答将用于：** <去向>

## 背景

用一段话向未参与你思考过程的接收人说明背景。足以让 TA 答好即可，不要写成一页。

## 如何回答

截止时间和大致工作量。部分回答和“我不确定”同样有用——拿不准的地方请标明，而不是跳过。

## <主题标题>

每个主题一个 `##` 小节，问题按重要性从高到低排列。每个问题只表达一个想法——绝不复合——下方直接留出回答栏，只有可能被误解或可能收到敷衍回答的问题才加一行_为什么这很重要_。

<question-example>
### 系统上线时需要承受多大的负载？

_为什么这很重要：它决定我们是现在就为流量高峰扩容，还是以后再说。_

>
</question-example>

## 还有其他想补充的吗？

收尾兜底：我们没问到、但你认为我们应该知道的任何事情？

</questionnaire-template>
