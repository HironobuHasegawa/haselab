---
title: "ImageMagick"
tags: ["image", "ImageMagick", "osx", "linux"]
---

# 

画像を操作したり，表示するソフトウェアの集合体

## composite 画像の差分を取る

参考 <http://blog.mirakui.com/entry/20110326/1301111196>

    composite -compose difference 1.jpg 2.jpg difference.jpg

## 画像のサイズ・フォーマット変換

サイズ変換（origin.jpegを320x240サイズのconv.jpegに変換）

    convert -geometry 320x240 origin.jpeg conv.jpeg

フォーマット変換（1.jpegを1.gifに変換）

    convert 1.jpeg 1.gif

## 複数画像のフォーマット変換

現在のディレクトリ内のtiffファイル全てをjpegに一括変換

    mogrify -format jpeg *.tiff

## Exif情報

ざっくりとした情報を表示するには， \`\`\` identify photo.jpg \`\`\`

より詳細を表示するには -verbose \`\`\` identify -verbose photo.jpg
\`\`\`

撮影日時など，特定の情報を表示するには -format \`\`\` identify -format
"%\[EXIF:DateTimeOriginal\]" photo.jpg \`\`\`

![](tag>image tool)
