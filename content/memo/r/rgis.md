---
title: RでGIS
weight: 2
tags: ["R", "GIS"] 
---


GISというか地図表示
[RjpWikiの記事](http://www.okada.jp.org/RWiki/?ShapeFile%A5%E9%A5%A4%A5%D6%A5%E9%A5%EA)を参考（というかほとんどそのまま）．下記データを使用した場合に書き換えただけ．

~~よりmaptoolsに特化した記事は以下．
<http://www.okada.jp.org/RWiki/?maptools%20%A4%C7%C3%CF%BF%DE%C9%BD%BC%A8>~~無くなってた．

[http://www.okada.jp.org/RWiki/?Rmap%A4%F2%BB%C8%A4%C3%A4%BF%C3%CF%BF%DE%C9%BD%BC%A8にRを使った地図表示についてまとめられている](http://www.okada.jp.org/RWiki/?Rmap%A4%F2%BB%C8%A4%C3%A4%BF%C3%CF%BF%DE%C9%BD%BC%A8にRを使った地図表示についてまとめられている)．

## 使用パッケージ

[maptools](http://cran.r-project.org/web/packages/maptools/index.html)
と[sp](http://cran.r-project.org/web/packages/sp/index.html)

## データの入手

[みんなの地球地図ホーム ＞ ダウンロードするには（簡易版）＞
Shape形式データ](http://www.globalmap.org/download/kanni01.html)
<http://www.globalmap.org/download/data/shp/1_1/shape_jp_1_1.zip>
を「C:/GIS_mapdata/」に解凍した場合を記す．
すなわち，「C:/GIS_mapdata/shape_jp_1\_1」に色々データが入っている状態．

## 表示

行政域を表示

    library(maptools)
    setwd("C:/GIS_mapdata/shape_jp_1_1")
    getwd()

    jpn <- read.shape("bnda_1_1.shp")
    jpn_poly <- Map2poly(jpn)
    plot(jpn_poly)

行政域以外の地図を表示させたい場合，適宜

    jpn <- read.shape("bnda_1_1.shp")

の.shpファイルを変えればよい．

## maptoolsでShapeFileを切り出す方法

日本全体は不要なので，北海道だけの.shpファイルが欲しい．

    library(maptools)
    setwd("C:/GIS_mapdata/shape_jp_1_1")
    getwd()

    jpn <- read.shape("bnda_1_1.shp")
    jpn_poly <- Map2poly(jpn)
    #plot(jpn_poly)
    jpn_df <- jpn$att.data

    hokkaido_poly <- subset(jpn_poly,jpn_df$nam=="HOKKAIDO")
    hokkaido_df <- subset(jpn_df,jpn_df$nam=="HOKKAIDO")

    write.polylistShape(hokkaido_poly,hokkaido_df,"hokkaido")

北海道だけのシェープファイルを作成後，

    library(maptools)
    setwd("C:/GIS_mapdata/shape_jp_1_1")
    hokkaido <- read.shape("hokkaido.shp")
    hokkaido_poly <- Map2poly(hokkaido)

    plot(hokkaido_poly)

で北海道だけが表示される．

札幌市ならsubsetの条件をlaa=="SAPPORO-SHI"にすればよい．
