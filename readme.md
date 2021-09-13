# 自治体別：最古・最近の鉄道駅設置年をWikipediaから抽出する
## 概要
MANDARA10に同封されている日本市町村人口.csvの自治体名一覧を使って, 各自治体別に駅データをweb（主にwikipedia）から取得し, 最古・最近の駅設置年度をデータとして出力する.

## 実行
`python main.py`でOK.
乗降場, 臨時駅などは収集に含めない.

## ログの解析
想定されるエラーが何種類かある.
+ link is not wikipedia : chromeで検索して一番上のリンクを取ってくるが, それがwikipediaの記事ではない場合. 優先データに項目を作成し, 適切なurlを記載する.
+ railroad section not found : 鉄道駅リンクがwikipediaに見つからない場合. 廃線は路線名だけ書いてあったりするので一括して触れないようにしている. 手動でデータを調べて優先データのdata項目に書くか, 本当に存在しない場合はnodata: trueを記述する.
+ no date column in webpage : 開業年月日のデータがないとき. これも優先データにデータを直接書く.
+ abandoned line may exist : 廃線についての記述が自治体のページにある（かもしれないとき）. 項目の名前も一緒に書かれる. 確認してみてなければ放置でいい. webページを巡回したときにしか検出できないので, データが実際に取れた場合には廃線が存在してもそれ以降の実行では飛ばされてしまうので注意（forceフラグなどを使うとうまくいく...かも）.

## priority_data.jsonの書き方
自治体名を属性名にしたオブジェクトを並べる.
+ url属性を書いておくとWikipediaのリンクを探す代わりにそのリンクを読み込む（一度取ってきたら保存されるので以降はスクレイピングはしない）.
+ data属性に`{"sta_data", ["駅名", "駅名", ...], "max": ["駅名", 年], "min": ["駅名", 年]}`のようなオブジェクトを書いておくと, webからデータを集める代わりにそれをそのまま使う.
一部しかない場合も適切に上書きする. ただし, 初めてデータ収集する自治体の場合はすべて揃っていなければならず, そうでない場合は普通にwebから収集する.
+ nodata属性にtrueを書いておくと, 鉄道駅がないものとみなされて決められた書式で結果が出される.
+ [未使用]all_obsolete属性：すべての駅が廃止済みである（現在鉄道駅がない）ことを示す.
