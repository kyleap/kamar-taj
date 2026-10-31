---
# 路徑必須是 content/posts/<slug>/index.md（page bundle，圖片放同一層）。
# 會公開的文章 draft 必須是 false。本機草稿可暫時改成 true。
title: ""
date: {{ .Date }}
slug: "{{ path.Base (strings.TrimSuffix "/" .File.Dir) }}"
draft: false
summary: ""
# 恰好一個，且必須出現在 data/post_contract.yaml 的 categories。
categories:
  - 心得隨筆
# 3 到 5 個。含英文者一律小寫、不要空白。Anthropic 來源必須包含 anthropic。
tags:
  - sample
source_title: ""
source_url: ""
source_publisher: ""
approved_at: {{ .Date }}
# 按下核准的人的顯示代號。不是密鑰，也不必等於 GitHub 帳號。
approved_by: ""
---

摘要與重點寫在這裡。圖片放在這個檔案旁邊，用相對路徑引用，例如 `![說明](figure.png)`。站內連結用 relref，不要寫死網域，寫法見 README。

有原文時，文章最後留下下面這個區塊。標題任意；有原文時建議「💡 三個學習」。省略標題會變成「我的見解」。沒有 source_url 就整段刪掉。

{{ `{{< insight "💡 三個學習" >}}` }}

這裡寫核准人自己的判斷，不要把原文摘要再貼一次。

{{ `{{< /insight >}}` }}
