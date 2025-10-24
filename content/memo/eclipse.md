---
title: "eclipse"
tags: ["IDE"]
---


# インストール

[Pleiades All in One
C/C++のJRE付き](http://mergedoc.sourceforge.jp/pleiades-redirect/3.6/pleiades_cpp_jre.zip.html)
上記ファイルをダウンロード後，Cドライブ直下に解凍し，eclipseとリネームする．

# 設定

## MinGW

[MinGW](http://ja.wikipedia.org/wiki/MinGW)が同梱されており，eclipse内からはすぐに使用できる．だが，外部（例えばコマンドプロンプト）から使用する場合は，Pathが通っていない．そのため，makeやgcc,sed,~~nkf~~等のツールを使う際にはあらかじめPathを設定するか，実行ファイルまでの絶対パスを記述する必要がある．
パスを追加する場合は以下を追加すればよい．

    C:\eclipse\mingw\bin
    C:\eclipse\msys\bin

バージョンによってbinディレクトリまでのパスが異なる可能性があるが，その場合は適宜読み替えのこと

※nkfは入ってなかった
