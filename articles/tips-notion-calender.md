---
title: "Notionカレンダーの右側サイドバーを非表示にする"
emoji: "📅"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["notion", "calendar"]
published: true
---

Notion のカレンダーを使っていると，右側にサイドバーが表示されてしまい，カレンダーの表示領域が狭くなってしまうことがあります．
この記事では，Notion のカレンダーの右側サイドバーを非表示にする方法について説明します．

## サイドバーを非表示にする方法

こちらの[記事](https://www.reddit.com/r/Notion/comments/19fizi6/how_to_remove_side_bar_in_notion_calendar/?rdt=39399)を参考にしました

1. Notion アプリの左上 --> 表示 --> トグル開発者ツール
2. <div id="main"> を展開し、子コンポーネント（私の場合は，<div class="sc-1448fr8-1 fPZeoH">）を選択する。
   ![](/images/tips-notion-calender/html.png)
3. 下部メニュー or 右側メニューの 「Styles」内の、.fPZeoH{}内のスタイリングを編集していく。
   ![](/images/tips-notion-calender/style.png)
4. --visible-context-panel-width: var(--context-panel-width);を見つけ，--context-panel-width をクリックする。
5. --context-panel-width: 256px;が表示されるので，256px を 0 に変更する。
   ![](/images/tips-notion-calender/parameter.png)
6. 変更が反映されるので，右側サイドバーが非表示になります．

何か間違いがあれば，コメント欄にお願いします．
私の環境では，この方法でサイドバーを非表示にすることができました．
