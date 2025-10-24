---
title: "画像関連のあれこれ"
tags: ["image", "osx"]
---


公式サイト <http://www.sno.phy.queensu.ca/~phil/exiftool/>

## インストール

\`\`\` brew install exiftool \`\`\`

## Exif情報の表示

\`\`\` exiftool DCSA0001.jpg exiftool -DateTimeOriginal DCSA0001.jpg
\`\`\`

## Exif情報の編集

<http://muibrog.hatenablog.com/entry/20100729/p1> によれば， \`\`\`
exiftool -\[追加・変更したいタグ名\]="exifに加えたい内容" photo.jpg
\`\`\` とすれば，編集できるようだ．

### ファイルの変更日時を撮影日時で変更

重複ファイルを検出・削除できるPhotos Duplicate
Cleanerを使っても，上手くいかない画像ファイルがある．
おそらくはファイルの変更日時やExif情報などを見て判断しているのだろうが，画像ファイルを特に編集した覚えはないのに変更日時が変更されていることがある．

検出できるように，ファイルの変更日時を撮影日時で変更したい．

あるディレクトリ以下のjpgファイルに対してexif情報を使って，一括で処理する方法が
<http://www.tuxradar.com/answers/433> に書いてあった．
ファイル名の変更は不要なので，その処理を削除し，sedのパターンを変えるなどして，以下のようなものを作った．

\`\`\` find . -name "\*.jpg" \| while read PHOTO; do DATE=$(exiftool
-DateTimeOriginal $PHOTO \| sed 's/\[Date/Time Original : \]//g') touch
-t $(echo $DATE \| sed 's/\\(..$\\)/\\.\\1/') $PHOTO done \`\`\`

1.  1行目は現在のディレクトリで".jpg"を含むファイルを探している
2.  2行目はexiftoolを使って撮影日時を出力
3.  3行目はexiftoolの出力から邪魔な部分をsedで削除（空文字と置換）
4.  4行目はtouchを使ってファイルのタイムスタンプを撮影日時で更新

exiftoolで上手くいったか確認（タイムスタンプならFinderでも確認出来るが）すると，Modify
Dateが変わっていない（Exif情報なので，タイムスタンプの変更とは関係ない）ので，修正．

\`\`\` find . -name "\*.jpg" \| while read PHOTO; do DATE=$(exiftool
-DateTimeOriginal $PHOTO \| sed 's/\[Date/Time Original : \]//g')
exiftool -ModifyDate=$(echo $DATE \| sed 's/\\(..$\\)/\\.\\1/') $PHOTO
touch -t $(echo $DATE \| sed 's/\\(..$\\)/\\.\\1/') $PHOTO done \`\`\`

1.  1行目は現在のディレクトリで".jpg"を含むファイルを探している
2.  2行目はexiftoolを使って撮影日時をDATEに代入
3.  3行目はexiftoolの出力から邪魔な部分をsedで削除（空文字と置換）
4.  4行目はexiftoolを使ってModify Dateを撮影日時で更新
5.  5行目はtouchを使ってファイルのタイムスタンプを撮影日時で更新

これでPhotos Duplicate
Cleanerで重複する画像ファイルが削除できる．よかったよかった．

# ImageMagick

[imagemagick](imagemagick)

# Mac OS Xでスクリーンショット

<http://inforati.jp/apple/mac-tips-techniques/system-hints/how-to-capture-a-specific-window-with-macos-screen-shot-function.html>

## 保存する画像形式を変更する

<http://inforati.jp/apple/mac-tips-techniques/system-hints/how-to-change-macos-screen-capture-file-type.html>

## ファイル名の命名規則を変える

<http://d.hatena.ne.jp/RyoAnna/20120320/1332233892>

## 影を無くす

<http://inforati.jp/apple/mac-tips-techniques/system-hints/how-to-disable-shadow-effect-of-macos-screen-capture.html>

# 画像から動画作成

シミュレーション過程や解析の結果を画像として出力し，それらから動画を作りたい場合がよくある．

## 動画

参考記事：<http://k-rash.blogspot.com.au/2010/05/mac.html>

Mac OSXにはじめからインストールされているiMovieを使えば良い

-   「共有」→「ムービーを書き出す」でmov形式
-   「共有」→「QuickTimeを使って書き出す」でaviやmpeg4形式を選択できる

116枚のjpeg画像（大きさ480×480，1枚当たりのファイルサイズ28kb程度で合計2.7MB）を使って動画を作成した．
画像1枚当たりの表示時間は0.1秒に設定し，mov形式（HDサイズ）で作成した場合は784KB，avi形式（オプション指定無し）では4MBとなった．それぞれ15秒の動画．
驚いたことにファイルサイズの小さなmov形式の方が明らかに画質が良い（ただし，Mac環境で再生）．

Windows環境でも再生するならavi形式が無難だが，ファイルサイズが大きい＆画質が良くないのは嬉しくない．自前のコンピュータ以外でプレゼンする場合には注意が必要だけど，movでいいや．

追記：Parallels上のWindows7（何のコーデックも入れていない）のWMPでもmov形式が再生できた．画質はMacでの再生と同様にmovの方がずっときれい．

## GIFアニメ作成

GIFfun

公式 <http://www.stone.com/GIFfun/>

日本語紹介記事
<http://force4u.cocolog-nifty.com/skywalker/2010/09/giffunmacgif-d5.html>

OSX 10.8.4で動作を確認

### 使い方

ファイルを一つずつ追加するか，あるいはフォルダを指定してその中に含まれるファイル全てを一括で追加する．
なお，後者の場合はファイルの並びを確認しないと，おかしな順番で作成されるので注意（pic1.jpeg,
pic1.jpeg, ... , pic10.jpeg
といった連番ファイルの場合，あらかじめ0埋めして桁を揃えておく必要がある）．

# その他

連番ファイル名を0埋め桁揃えしたい場合，zshのzmvを使うのが便利．

参考 <http://qiita.com/ponko2@github/items/31af073608aa20fa57e4>

zmvを使うため .zshrc に

    autoload zmv

を記述

4桁数字で埋めるには，

    zmv 'pic(*).jpeg' 'pic${(l:4::0:)1}.jpeg'

とすれば良い．
