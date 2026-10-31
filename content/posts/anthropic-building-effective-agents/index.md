---
title: "〔範例〕有效的代理人，仍然要人畫邊界"
date: 2026-10-29T09:00:00+08:00
slug: anthropic-building-effective-agents
draft: false
summary: "範例摘要：能用固定步驟解決的事，不必先做成一個會自己規劃的代理人。"
categories:
  - Anthropic
tags:
  - sample
  - anthropic
  - agents
  - AI 與 LLM
source_title: "Building effective agents"
source_url: "https://www.anthropic.com/engineering/building-effective-agents"
source_publisher: "Anthropic"
approved_at: 2026-10-29T21:00:00+08:00
approved_by: kyle
---

> **範例文章。** 這篇是網站初版的示範，用來確認「原文出處」和文末見解的版面。下面不是對原文的完整翻譯，請整篇換掉。

原文的方向可以收成一句話：先分清楚你要的是一條固定流程，還是一個會自己決定下一步的代理人。多數團隊現在缺的是穩定的步驟，不是更長的自主迴圈。

實務上可以先問三件事：

- 這件事的輸入與完成條件能不能寫成檢查清單？
- 失敗時，人要看的是哪一段軌跡，而不是一整包對話？
- 若拿掉「自己規劃」這一層，現有的工具呼叫是否已經夠用？

三個答案若都偏固定流程，就先把流程和測試做穩。代理人留下的不確定性是有代價的，不該當成預設架構。

{{< insight "💡 三個學習" >}}

審這類文章時，我會先問團隊現在的痛是「步驟不穩」，還是「目標本身不清楚」。前者用固定流程加上測得了的斷言就夠；後者才值得付代理人那種走偏的風險。

這段是範例見解。正式文章請改成核准人自己的判斷，並把這個區塊留在全文最後。
{{< /insight >}}
