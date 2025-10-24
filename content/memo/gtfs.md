---
title: "GTFS"
tags: ["gtfs", "公共交通"]
---

# GTFSとは

<https://developers.google.com/transit/gtfs/?hl=ja> より，

> GTFS（General Transit Feed
> Specification）には、公共交通機関の時刻表とその地理的情報に使用される共通形式が定義されています。GTFS
> の「フィード」を利用することで、交通機関は乗換案内データを公開し、デベロッパーはそのデータを相互運用可能な方法で利用するアプリケーションを作成することができます。

作成したフィードをGoogleに送ると，Googleマップのルート検索に反映されるらしい．
秋田県内だとあまり整備されていないが，シドニーにいた頃は大変お世話になった．

# フィードの作成

-   フィードは複数のテキストファイルをzipで固めたもの
-   テキストファイルのエンコーディングはUTF8

フィードに含まれるテキストファイルは以下の通り（<https://developers.google.com/transit/gtfs/reference?hl=ja>
から抜粋）

|                        |          |                                                                                                                                                                                         |
|------------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ファイル名             | 必須     | 定義                                                                                                                                                                                    |
| agency.txt             | 必須     | このフィードのデータを提供する 1 社以上の交通機関．                                                                                                                                     |
| stops.txt              | 必須     | 乗客が乗り降りする停車地点．                                                                                                                                                            |
| routes.txt             | 必須     | 交通機関のルート．ルートとは利用者に 1 つのサービスとして表示される，旅程のグループのことです．                                                                                         |
| trips.txt              | 必須     | 各ルートの旅程．旅程とは一定の時間に 2 回以上の停車を伴う移動行程を指します．                                                                                                           |
| stop\\\_times.txt      | 必須     | 各旅程の個々の停車地への到着時刻と出発時刻．                                                                                                                                            |
| calendar.txt           | 必須     | 週間時刻表に基づいたサービス ID の日付．サービスの開始日と終了日に加えて，サービスが利用できる曜日を指定します．                                                                        |
| calendar\\\_dates.txt  | 省略可能 | calendar.txt ファイルで定義されたサービス ID の例外．この calendar\\\_dates.txt にサービスのすべての日付を含める場合，calendar.txt の代わりにこのファイルだけを指定することも可能です． |
| fare\\\_attributes.txt | 省略可能 | 交通機関のルートの料金情報．                                                                                                                                                            |
| fare\\\_rules.txt      | 省略可能 | 交通機関のルートの料金情報を適用するための規則．                                                                                                                                        |
| shapes.txt             | 省略可能 | 交通機関のルートを示す線を地図に描くための規則．                                                                                                                                        |
| frequencies.txt        | 省略可能 | サービスの間隔が一定でないルートの運行間隔（旅程の間隔）．                                                                                                                              |
| transfers.txt          | 省略可能 | 乗換地点で別のルートに乗り継ぐための規則．                                                                                                                                              |
| feed\\\_info.txt       | 省略可能 | フィード自体に関するその他の情報（フィード提供者，バージョン，有効期間などの情報）．                                                                                                    |

サンプルは
<https://developers.google.com/transit/gtfs/examples/gtfs-feed?hl=ja>
を参照のこと（すぐに試せる[zipで固めたもの](https://developers.google.com/transit/gtfs/examples/sample-feed.zip?hl=ja)も入手可能）．

## stops.txtの作成

乗客が乗り降りする停車地点を表し，フィードに必須． 詳細は
<https://developers.google.com/transit/gtfs/reference?hl=ja#stops_fields>
を参照のこと．

\[フィードの例\]([https://developers.google.com/transit/gtfs/examples/gtfs-feed?hl=ja)のstops.txtの内容を抜粋](https://developers.google.com/transit/gtfs/examples/gtfs-feed?hl=ja)のstops.txtの内容を抜粋)

\`\`\`txt stop_id,stop_name,stop_desc,stop_lat,stop_lon,zone_id,stop_url
FUR_CREEK_RES,Furnace Creek Resort (Demo),,36.425288,-117.133162,,
BEATTY_AIRPORT,Nye County Airport (Demo),,36.868446,-116.784582,,
BULLFROG,Bullfrog (Demo),,36.88108,-116.81797,, STAGECOACH,Stagecoach
Hotel & Casino (Demo),,36.915682,-116.751677,, NADAV,North Ave / D Ave N
(Demo),,36.914893,-116.76821,, NANAA,North Ave / N A Ave
(Demo),,36.914944,-116.761472,, DADAN,Doing Ave / D Ave N
(Demo),,36.909489,-116.768242,, EMSI,E Main St / S Irving St
(Demo),,36.905697,-116.76218,, AMV,Amargosa Valley
(Demo),,36.641496,-116.40094,, \`\`\` 必須なのは，以下の4つ（
[https://developers.google.com/transit/gtfs/reference?hl=ja#stops_fieldsから抜粋](https://developers.google.com/transit/gtfs/reference?hl=ja#stops_fieldsから抜粋)）．

|              |      |                                                                                                                                                    |
|--------------|------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| stop\\\_id   | 必須 | stop\\\_id フィールドには停車地や駅を一意に識別する ID を指定します。複数のルートに同じ停車地を使用できます。stop\\\_id は一意なデータセットです。 |
| stop\\\_name | 必須 | stop\\\_name フィールドには停車地や駅の名前を指定します。その地域の住民や旅行者が理解できる名前を使用してください。                                |
| stop\\\_lat  | 必須 | stop\\\_lat フィールドには停車地や駅の緯度を指定します。値には有効な WGS 84 の緯度を指定します。                                                   |
| stop\\\_lon  | 必須 | stop\\\_lon フィールドには停車地や駅の経度を指定します。値には有効な WGS 84 の経度値（-180～180）を指定します。                                    |

### バス停の位置情報を取得
\[国土数値情報バス停留所データ\]([http://nlftp.mlit.go.jp/ksj/gml/datalist/KsjTmplt-P11.html)を用いて，バス停の位置情報を取得する](http://nlftp.mlit.go.jp/ksj/gml/datalist/KsjTmplt-P11.html)を用いて，バス停の位置情報を取得する)．
国土数値情報については[qgis](/memo/qgis) を参照のこと．
なお，座標系を国土数値情報で用いられているJGD2000からGFTSで用いられているWGS84に変換する必要があるが，2014/12/03時点では未着手．

#### 国土数値情報バス停留所データをCSVに変換する

1\. ダウンロードしたzipファイルを解凍 2.
シェイプファイルをベクタレイヤとしてQGISで読み込む（エンコーディングはShift_JIS，座標系はJGD2000）．秋田県のシェイプファイルはP11-10_05-jgd-g_BusStop.shp
3.
レイヤパネルからレイヤを右クリックし，「名前を付けて保存」で「コンマ区切りファイル」形式を選び，P11-10_05-jgd-g_BusStop.csvとしてCSVでエクスポート（以下に秋田県の2行だけを抜粋）

\`\`\`csv
X,Y,P11_001,P11_002,P11_003_1,P11_003_2,P11_003_3,P11_003_4,P11_003_5,P11_003_6,P11_003_7,P11_003_8,P11_003_9,P11_003_10,P11_003_11,P11_003_12,P11_003_13,P11_003_14,P11_003_15,P11_003_16,P11_003_17,P11_003_18,P11_003_19,P11_004_1,P11_004_2,P11_004_3,P11_004_4,P11_004_5,P11_004_6,P11_004_7,P11_004_8,P11_004_9,P11_004_10,P11_004_11,P11_004_12,P11_004_13,P11_004_14,P11_004_15,P11_004_16,P11_004_17,P11_004_18,P11_004_19
140.06536198,39.62948181,長浜,"3,1","秋田市,羽後交通（株）",,,,,,,,,,,,,,,,,,,"下浜線,（急行）本荘・秋田線",,,,,,,,,,,,,,,,,,
\`\`\`
左から順に，経度，緯度，バス停名，バス路線情報バス区分，バス路線情報事業者名．．．となる．
同一バス停に複数路線が乗り入れている場合，バス路線情報（バス区分・事業者名・バス系統）が二重引用符『“』でくくられ，その中でカンマ区切りで列挙される．

#### バス会社Aのみを抜き出す
\<del>P11-10_05-jgd-g_BusStop.csvを[Vim](/memo/Vim)で開き，ノーマルモードで以下の操作を行い，「バス会社A」が含まれる行をqレジスタにヤンクする
\`\`\` /バス会社A qqq:g//y Q \`\`\`

新規バッファを開き，ノーマルモードで以下の操作を行い，qレジスタの内容を貼り付ける

\`\`\` "qp \`\`\`

適当な名前でファイル（ここでは仮にBusStop.csvとする）を保存する．\</del>

[awk](/memo/awk)をつかえば，もっと簡単だった（\[この記事\](<http://antibayesian.hateblo.jp/entry/2014/09/15/162605>)）

\`\`\` awk /バス会社A/ P11-10_05-jgd-g_BusStop.csv \> BusStop.csv \`\`\`

### 必要な情報のみを抜き出す stops.txtに必須のstop\\\_id, stop\\\_name,
stop\\\_lat,
stop\\\_lonのうち，stop\\\_id以外の3つはBusStop.csvに含まれている．

[awk](/memo/awk)を使って，BusStop.csvに含まれるバス停名称，緯度，経度を抜き出し，結果をstops.txtに書き出す．

\`\`\`awk awk -F"," '{ print $3 "," $2 "," $1}' BusStop.csv \> stops.txt
\`\`\`

### stop_idの生成
面倒なので，上から順番にS0001，S0002．．．といった連番にする．
QGISなら，フィールド計算機で \`\`\`format ('S%1', lpad($rownum, 4, '0')
)\`\`\` といった具合にすればよい．

以下の記述は念のために残しておく．

エクセルのふりがな作成機能を使ってバス停名称にふりがなをつけ，それを英字にしてIDとする．※同一名称のバス停がある（該当するバス停を使う路線もくっつれば対応可能だと思う）

-   stops.txtをエクセルかLibreOffic
    Calceなどで開き（エクセルはUTF8を自動認識しないので注意．また，余計ことをしがちなので，LibreOffice
    Calcを推奨），バス停名称をコピーする．
-   \[エクセルの学校\]([http://www.excel.studio-kazu.jp/DL/index.html#q08)にて公開されている，http://www.excel.studio-kazu.jp/DL/kana2roma_4.xls](http://www.excel.studio-kazu.jp/DL/index.html#q08)にて公開されている，http://www.excel.studio-kazu.jp/DL/kana2roma_4.xls)
    をダウンロードし，Sheet2のA列にコピーしたバス停名称を貼り付ける．

#### ふりがなを振る
ふりがなを振りたいセルを選択し，`Alt + Shift + 矢印の上`でふりがなが振られる．
しかし，複数セルに対して一括では行えないので，マクロを使う．

\`\`\`vba Sub 振り仮名を付ける()

      Selection.SetPhonetic
      Range("A1").Select

End Sub \`\`\`

ふりがなを振りたいセルを選択して上記のマクロを実行すれば，一括でふりがなが振られる．
なお，後述する半角カタカナからローマ字への変換のため，「ふりがなの設定」で半角カタカナを選択しておくこと．

#### ふりがなを取り出す

-   PHONETIC関数を使って，ふりがなをB列に取り出す（例えば，B1セルに`=PHONETIC(A1)`と入力して，セルの右下をドラッグ）

#### ふりがなを半角カタカナから英字に変換する

-   kana2roma_4.xls内で定義されているRomanConv関数を使って，ふりがなを半角カタカナから英字に変換する（例えば，C1セルに`=RomanConv(B1)`と入力して，セルの右下をドラッグ）
-   C列全体をコピーし，stops.txtのA列に「形式を選択して貼り付け」で値のみを貼り付ける

### ヘッダーの記述 1行目に， \`\`\` stop_id, stop_name, stop_lat,
stop_lon \`\`\` を追加

## shapes.txtの作成
shapes.txtは交通機関のルートを示す線を地図に描くための規則で，省略可能（が，無いとバス停間を直線で結ぶことになる）．

詳細は
<https://developers.google.com/transit/gtfs/reference?hl=ja#stops_fields>
を参照のこと．

\[フィードの例\]([https://developers.google.com/transit/gtfs/examples/gtfs-feed?hl=ja)のshapes.txtの内容を抜粋](https://developers.google.com/transit/gtfs/examples/gtfs-feed?hl=ja)のshapes.txtの内容を抜粋)

\`\`\`txt
shape_id,shape_pt_lat,shape_pt_lon,shape_pt_sequence,shape_dist_traveled
A_shp,37.61956,-122.48161,1,0 A_shp,37.64430,-122.41070,2,6.8310
A_shp,37.65863,-122.30839,3,15.8765 \`\`\`

必須のフィールドは以下の4つ（<https://developers.google.com/transit/gtfs/reference?hl=ja#shapes_fields>
から抜粋）

|                         |      |                                                                                                                                                                                                                                  |
|-------------------------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| shape\\\_id             | 必須 | shape\\\_id フィールドには、1 つのシェイプを一意に識別する ID を指定します。                                                                                                                                                     |
| shape\\\_pt\\\_lat      | 必須 | shape\\\_pt\\\_lat フィールドを使用して、シェイプ ID にシェイプ ポイントの緯度を関連付けます。値には有効な WGS 84 の緯度を指定します。shapes.txt の各行が、シェイプを定義する 1 つのシェイプ ポイントを表します。                |
| shape\\\_pt\\\_lon      | 必須 | shape\\\_pt\\\_lon フィールドを使用して、シェイプ ID にシェイプ ポイントの経度を関連付けます。値には有効な WGS 84 の経度値（-180～180）を指定します。shapes.txt の各行が、シェイプを定義する 1 つのシェイプ ポイントを表します。 |
| shape\\\_pt\\\_sequence | 必須 | shape\\\_pt\\\_sequence フィールドを使用して、シェイプ ポイントの緯度と経度をそのシェイプに沿った進行順序に関連付けます。shape\\\_pt\\\_sequence の値には 0 以上の整数を使用し、旅程の進行に合わせて値を増やします。             |

> たとえば、シェイプ「A_shp」の定義に 3
> つのポイントが含まれる場合、shapes.txt
> ファイルにはこのシェイプを定義するために次のような行を指定します:

-   A\\\_shp,37.61956,-122.48161,0
-   A\\\_shp,37.64430,-122.41070,6
-   A\\\_shp,37.65863,-122.30839,11

------------------------------------------------------------------------

省略可能なフィールドとして，shape\\\_dist\\\_traveledがある

## テキストファイルをzipで固める
余計なサブディレクトリが出来ないように気をつける必要がある．

Macで圧縮する場合，\[WinArchiver
Lite\]([https://itunes.apple.com/jp/app/winarchiver-lite/id414855915?mt=12)に全テキストファイルをドラッグするか，以下のようにコマンドラインで圧縮する](https://itunes.apple.com/jp/app/winarchiver-lite/id414855915?mt=12)に全テキストファイルをドラッグするか，以下のようにコマンドラインで圧縮する)．

\`\`\` zip -r feedname \*.txt \`\`\`

Windows7の場合は全てのテキストを選択して右クリック，「送る」「zipで圧縮」みたいな感じで大丈夫

# 作成したフィードの検証

-   作成したフィードが仕様に準拠したものかどうかを確認するには\[feedvalidator\]([https://github.com/google/transitfeed/wiki/FeedValidator)を使う](https://github.com/google/transitfeed/wiki/FeedValidator)を使う)
-   フィードデータが地図にどのように表示されるかを確認するには，\[schedule_viewer\]([https://github.com/google/transitfeed/wiki/ScheduleViewer)を使う](https://github.com/google/transitfeed/wiki/ScheduleViewer)を使う)

## インストール

\`\`\`sh cd git clone <https://github.com/google/transitfeed.git> \`\`\`
makeが上手くいかないので，pythonスクリプトをそのまま使う．

Windowsなら，コンパイル済みのものが公開されているので，<https://github.com/google/transitfeed/releases/download/1.2.13/transitfeed-windows-binary-1.2.13.zip>
からダウンロードして，解凍すればOK.

## 地図上での表示のされ方を検証

作成したフィードを適当な場所に保存（以下はtransitfeedの下にfeedsディレクトリを作成して，そこにtest.zipを保存した場合）し，スクリプトを実行する．

\`\`\` cd \~/transitfeed/ python ./schedule_viewer.py --feed_filename
feeds/test.zip \`\`\` Webサーバが立ち上がるので，ブラウザで
<http://localhost:8765/> にアクセス

もし，`ImportError: No module named simplejson`というエラーが出たら，
\`\`\` easy_install simplejson \`\`\` でsimplejsonをインストールする．

## 仕様に準拠しているかどうかを検証

\`\`\` python ./feedvalidator.py feeds/test.zip \`\`\`

<img src="tag&gt;GIS transportation" class="align-left" alt="tag&gt;GIS transportation" />
