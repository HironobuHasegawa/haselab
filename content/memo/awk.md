---
title: "awk"
tags: ["text", "osx"]
---


2012/11/07
1行1レコード形式のデータ，特に大きなデータを取り扱うのに便利なのだということを某MLで知る．

今扱っている固定長データ（大きいのでも600MBくらいだけど）から分析に使うフィールドを切り出すのに便利そうだ．

※[改行コード](https://ja.wikipedia.org/wiki/%E6%94%B9%E8%A1%8C%E3%82%B3%E3%83%BC%E3%83%89)はLFを前提としていることに注意（2012/11/27追記）

網羅的なガイドは以下
<http://www.kt.rim.or.jp/~kbk/gawk-30/gawk_toc.html>

こちらの方が取っつきやすいかも
<http://antibayesian.hateblo.jp/entry/2014/09/15/162605>

## フィールドの指定方法

awk.txtの全レコードに対して，2番目のフィールドを（標準）出力するには

``` awk
awk '{ print $2 }' awk.txt
```

awk.txtの全レコードに対して，10番目と3番目のフィールドを（標準）出力するには

``` awk
awk '{ print $10 $3 }' awk.txt
```

これだと連結されるので，間にブランクを入れる

``` awk
awk '{ print $10 " " $3 }' awk.txt
```

詳しい解説は以下

<http://www.ibm.com/developerworks/jp/linux/library/l-awk1/>
<http://shellscript.sunone.me/awk.html>

2012/11/22 追記

## パターンによるレコード範囲指定

### レコード番号NRを使った範囲指定

組み込み変数のNRはレコード番号を指定する．ただし，NRとNFは変数とはいっても通常，値は代入はせず読み取り専用で用いる

awk.txtのレコード番号3（3行目）の，2番目のフィールドを（標準）出力するには

``` awk
awk 'NR == 3 { print $2 }' awk.txt
```

awk.txtのレコード番号が5未満（1～4）の，1番目のフィールドを（標準）出力するには

``` awk
awk 'NR < 5 { print $1 }' awk.txt
```

awk.txtのレコード番号が5未満（1～4）またはレコード番号が7の，全てのフィールドをout.txtに出力するには

``` awk
awk 'NR < 5 || NR == 7 { print }' awk.txt > out.txt
```

※これは一行目がラベルになっているデータを分割する際に有用

## フィールド区切り文字の指定

-Fオプションにより，フィールドの区切り文字を指定することが出来る．

awk.txtの区切り文字をスペースに指定して，全レコードに対して，2番目のフィールドを（標準）出力するには

``` awk
awk -F" " '{ print $2 }' awk.txt
```

awk.csvの区切り文字をカンマに指定して，全レコードに対して，2番目のフィールドを（標準）出力するには

``` awk
awk -F"," '{ print $2 }' awk.csv
```

## ファイルへの出力

awk.txtのレコード番号が5未満（1～4）の，1番目と2番目のフィールドをカンマで区切ってoutput.csvに出力するには

``` awk
awk 'NR < 5 { print $1 "," $2 }' awk.txt > output.csv
```

出力されたoutput.csvの中身

    1,B1
    2,B2
    3,B3
    4,B4

## 固定長レコードの読込

参考：<http://www.geocities.jp/cygnus_odile/awkpage/14_awk_fixed_length.html>

組み込み変数のFIELDWIDTHSを使って固定長レコードの各フィールド幅を指定可能（らしいが，上手くいかない）

## このページで使っているデータ

awk.txt \`\`\`text 1 B1 C1 2 B2 C2 3 B3 C3 4 B4 C4 5 B5 C5 6 B6 C6 7 B7
C7 8 B8 C8 9 B9 C9 10 B10 C10 11 B11 C11 12 B12 C12 13 B13 C13 14 B14
C14 15 B15 C15 16 B16 C16 17 B17 C17 18 B18 C18 19 B19 C19 20 B20 C20 21
B21 C21 22 B22 C22 23 B23 C23 24 B24 C24 25 B25 C25 26 B26 C26 27 B27
C27 28 B28 C28 29 B29 C29 30 B30 C30 31 B31 C31 32 B32 C32 33 B33 C33
\`\`\`

awk.csv

\`\`\`csv 1,B1,C1 2,B2,C2 3,B3,C3 4,B4,C4 5,B5,C5 6,B6,C6 7,B7,C7
8,B8,C8 9,B9,C9 10,B10,C10 11,B11,C11 12,B12,C12 13,B13,C13 14,B14,C14
15,B15,C15 16,B16,C16 17,B17,C17 18,B18,C18 19,B19,C19 20,B20,C20
21,B21,C21 22,B22,C22 23,B23,C23 24,B24,C24 25,B25,C25 26,B26,C26
27,B27,C27 28,B28,C28 29,B29,C29 30,B30,C30 31,B31,C31 32,B32,C32
33,B33,C33 \`\`\`

fixedlength.txt \`\`\`txt fixedlength 12345678900 abcdefghijk
12345678900 12345678900 abcdefghijk abcdefghijk 12345678900 fixedlength
\`\`\`

![](tag>linux mac)
