# 卡瑪泰姬的技術修煉所

繁體中文技術部落格。靜態站用 [Hugo](https://gohugo.io/) extended，主題是 [PaperMod](https://github.com/adityatelange/hugo-PaperMod/)（git submodule，疊自己的樣式，不 fork 主題）。

線上位置是專案頁路徑：<https://kyle.study4.tw/kamar-taj/>。`baseURL` 寫在 `hugo.toml`，GitHub Actions 用同一個值。這裡沒有寫死 GitHub 帳號；若以後改帳號，專案頁路徑裡的倉庫名稱 `kamar-taj` 以及這份網址要一起改。

## 本地預覽

需要 **Hugo extended 0.169.0 或更新**（PaperMod 目前要求至少 0.169 之前的 0.158，workflow 釘的是 0.169.0）。主題用 submodule：

```bash
git clone --recurse-submodules <這個倉庫的網址>
cd kamar-taj
hugo server --baseURL http://localhost:1313/kamar-taj/
```

已經 clone 但沒有主題時：

```bash
git submodule update --init --recursive
```

瀏覽器開 `http://localhost:1313/kamar-taj/`。預設是深色，右上角可切換淺色。

正式建置：

```bash
hugo --gc --minify
```

產出在 `public/`。不要把 `public/` 提交進 git。

## 寫一篇文章

文章是 page bundle，圖片和 `index.md` 放在一起：

```text
content/posts/<slug>/
  index.md
  figure.png      # 可選
```

產生骨架（slug 會帶進 front matter）：

```bash
hugo new content/posts/<slug>/index.md
```

內文用繁體中文。站內連結不要寫死網域，用：

```markdown
[歡迎]({{< relref "/posts/welcome" >}})
```

以 `/` 開頭的 Markdown 連結也會在建置時補上 `/kamar-taj/` 前綴。

### 發文契約

`draft: false` 的文章若不符合下面規則，`hugo` 會直接失敗。詞彙的機器可讀版本在 `data/post_contract.yaml`，檢查邏輯在 `layouts/_partials/post_contract.html`。

| 欄位 | 規則 |
| --- | --- |
| 路徑 | `content/posts/<slug>/index.md` |
| `title` | 必填 |
| `date` | 必填，公開用的日期 |
| `slug` | 必填，而且必須等於目錄名。只能小寫英文、數字、連字號 |
| `categories` | **恰好一個**，只能是下面的領域分類。來源不占分類 |
| `tags` | **3 到 5 個**。自由填。含英文字母者一律小寫，不要空白。中文可直接用。分類名稱可以原樣當標籤 |
| `summary` | 必填。列表與文章頁副標用這段 |
| `source_title` | 有原文才填，而且三個 source 欄位要一起填 |
| `source_url` | 有原文才填，`http` 或 `https` |
| `source_publisher` | 有原文才填。Anthropic 請寫 `Anthropic` |
| `approved_at` | 必填。按下核准的時間，ISO 8601。建議帶 `+08:00`，日期才會落在台灣 |
| `approved_by` | 必填。核准人的**顯示代號**（例如 `kyle`）。不是密鑰，也不必等於 GitHub 帳號 |
| `draft` | 會公開的文章必須是 `false` |

固定分類（一個都不能多、不能自創；只按領域，不按來源）：

- AI 與 LLM
- 軟體工程
- 測試與品質
- 產品與管理
- 心得隨筆

有原文的文章：

- slug 使用來源前綴加原始 slug，例如 `anthropic-<original-slug>`。
- `source_publisher: Anthropic` 時，前綴是 `anthropic-`，tags 裡必須有小寫 `anthropic`。分類仍選上面的領域（例如 `AI 與 LLM`），不要為來源另開分類。出處靠 front matter 的 `source_publisher` 與這個標籤。
- 其他已登錄的來源看 `data/post_contract.yaml` 的 `publishers`。還沒登錄的來源會用小寫、空白轉連字號的前綴（`Some Lab` → `some-lab-`），並要求同名標籤；推不出合法前綴時建置會失敗，這時把來源加進那個 yaml。
- 頁面頂端會出現：「本文為摘要與重點翻譯，原文：〈連結〉」。
- 文章**最後**必須是 `insight` shortcode。標題任意，沒寫標題時預設是「我的見解」。有原文的文章建議用「💡 三個學習」，內容是**恰好三個** Markdown 條列（一行一點）。條列少於或多於三個時，建置會失敗：

````markdown
{{< insight "💡 三個學習" >}}

- 第一個學習
- 第二個學習
- 第三個學習

{{< /insight >}}
````

沒給標題（`{{< insight >}}`）時，標題就是「我的見解」。樣式與標題文字無關，emoji 可以放在標題裡。條列在深色、淺色與手機寬度都會顯示金色項目符號。

沒有 `source_url` 的原創文章不要留這個區塊，三個 source 欄位也留空或整段刪掉。

`approved_at` 和 `approved_by` 只留在 front matter，當審核紀錄，不會畫在頁面上。

## 外部系統如何發佈

審核站不在這個倉庫。核准後，由外部系統把 Markdown **commit 到預設分支**（目前是 `main`）。push 會觸發 `.github/workflows/pages.yml`，建置並部署。這個 workflow **沒有任何自訂 secret**；部署用的是 GitHub 自己的 `GITHUB_TOKEN`。token 由外部系統保管，不要寫進本倉庫、workflow 或站台設定。

一次 commit 最好包含整個 page bundle（`index.md` 和圖片），這樣只會部署一次。

1. **直接寫入預設分支（最常見）**  
   用 GitHub Contents API，路徑例如 `content/posts/anthropic-example-slug/index.md`：

   ```http
   PUT /repos/{owner}/{repo}/contents/content/posts/anthropic-example-slug/index.md
   ```

   請求正文的 `content` 是檔案的 base64，`branch` 設為預設分支，`message` 寫清楚 slug。token 需要這個倉庫的 contents 寫入權限。  
   Contents API 一次只能寫一個檔案。圖片和本文要同一筆 commit 時，改用 Git Data API（blob → tree → commit → 更新 ref），或走下面的 PR。

2. **開 PR 再合併**  
   外部系統開分支、放入整個 `content/posts/<slug>/`、開 pull request。合併進預設分支後同樣會部署。適合想先看 diff 的時候。PR 上的 workflow 只建置、不部署。

3. **沒有新檔案、只想重建**  
   - Actions 頁面手動跑「Deploy Hugo site to Pages」（`workflow_dispatch`）。  
   - 或由外部系統送 `repository_dispatch`，`event_type` 用 `rebuild` 或 `publish`：

     ```http
     POST /repos/{owner}/{repo}/dispatches
     {"event_type":"rebuild"}
     ```

     這需要能觸發 workflow 的倉庫寫入權限。一般發文用第 1 或第 2 種即可，push 本身就會部署。

`{owner}` 和 `{repo}` 不要寫死在程式以外的說明裡；呼叫端用自己的設定組出來。

## 部署

推上預設分支就會跑 workflow。第一次要先在 GitHub 上手動改一項，否則 Actions 不會把網站發出去：

**Settings → Pages → Build and deployment → Source 改成 GitHub Actions。**

這個倉庫目前是從 `main` 分支的 `/` 直接當網站（legacy）。合併之後、改成 GitHub Actions 之前，線上首頁會暫時對不到新站。請在合併當下改這項設定。

改完之後：

- 網站：`https://kyle.study4.tw/kamar-taj/`
- RSS：`https://kyle.study4.tw/kamar-taj/index.xml`（文章列表另有 `posts/index.xml`）
- 搜尋用的索引：`https://kyle.study4.tw/kamar-taj/index.json`

不要在這個倉庫加 `CNAME`。自訂網域掛在使用者站上，這個專案頁跟著走 `/kamar-taj/`。若加上 `CNAME`，有機會把整個網域搶到這個專案。

若 GitHub 為 `github-pages` 環境開了保護，第一次部署可能要在 Actions 裡核准一次。

## 主題

`themes/PaperMod` 釘在 commit `118119693d8b5ac211b939775678310973bcb232`。不要改用發行版 v8.0，那個版本接不上現在的 Hugo 模板。升級時：

```bash
git submodule update --remote themes/PaperMod
```

看過建置沒問題再提交 submodule 指標。本站覆寫了 `layouts/single.html`（原文出處與發文契約）。升級主題後若文章頁版面壞了，用這份檔案和上游的 `layouts/single.html` 對一下。

樣式只加在 `assets/css/extended/kamar-taj.css` 和少數 partial，沒有改主題檔。

## 舊的修行紀錄

這個倉庫原本的首頁是一份有通行密語的「古一法師 × 凱爾 修行紀錄」（整頁在單一 HTML 裡，內容加密）。它和公開技術部落格搶同一個網址，所以從首頁移走，檔案原樣留在 `static/legacy/index.html`。

改成 GitHub Actions 之後，網址是：

`https://kyle.study4.tw/kamar-taj/legacy/`

公開導覽不會連過去。那份頁面自己帶了 `noindex`。若不再需要，刪掉 `static/legacy/` 即可。
