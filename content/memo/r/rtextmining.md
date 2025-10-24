---
title: Rでテキストマイニング
weight: 2
tags: ["R", "textmining", "osx", "windows"] 
---


## MeCabとRMeCabのインストール

### MeCab

Windows環境ではバイナリが公開されている．
<http://sourceforge.net/projects/mecab/files/>
からmecab-0.98.exe（バージョンは2009/10/15現在）をダウンロードし，インストール（僕はC:\\MeCabに入れた）．

\_\_\_\_ 2012/04/28追記 osx
lionでは自分でビルドするか，homebrewでのインストールが便利．

ビルドはgccが使えさえすればマニュアル通りで大丈夫．

homebrewのインストール過程でアンインストールしたので，改めてhomebrewを使ってMeCabをインストールする．

```
    $ brew install mecab
    ==> Downloading http://mecab.googlecode.com/files/mecab-0.993.tar.gz
    ######################################################################## 100.0%
    ==> ./configure --prefix=/usr/local/Cellar/mecab/0.993
    ==> make install
    Error: The linking step did not complete successfully
    The formula built, but is not symlinked into /usr/local
    You can try again using `brew link mecab'
    ==> Summary
    /usr/local/Cellar/mecab/0.993: 17 files, 2.9M, built in 46 seconds
```

すごく楽．辞書もインストール

```
    $ brew install mecab-ipadic
    Error: You must `brew link mecab' before mecab-ipadic can be installed
```

指示に従い実行

```
    $ brew link mecab
    Linking /usr/local/Cellar/mecab/0.993... 
    Error: Could not symlink file: /usr/local/Cellar/mecab/0.993/etc/mecabrc
    Target /usr/local/etc/mecabrc already exists. You may need to delete it.
```

前に自分でビルドした時の残骸が残っているみたいだ．はい，消します．

```
    $ rm -f /usr/local/etc/mecabrc 
    $ brew link mecab
    Linking /usr/local/Cellar/mecab/0.993... 
    Error: Could not symlink file: /usr/local/Cellar/mecab/0.993/bin/mecab-config
    Target /usr/local/bin/mecab-config already exists. You may need to delete it.
```

はい，消します．

```
    $ rm -f /usr/local/bin/mecab-config
```

同じ流れで

```
    $ rm -f /usr/local/share/man/man1/mecab.1
```

も削除．改めて

```
    $ brew link mecab
    Linking /usr/local/Cellar/mecab/0.993... 4 symlinks created
```

OK．さあ辞書だ

```
    $ brew install mecab-ipadic
    ==> Downloading http://downloads.sourceforge.net/project/mecab/mecab-ipadic/2.7.
    ######################################################################## 100.0%
    ==> ./configure --disable-debug --prefix=/usr/local/Cellar/mecab-ipadic/2.7.0-20
    ==> make install
    /usr/local/Cellar/mecab-ipadic/2.7.0-20070801: 5 files, 16K, built in 38 seconds
```

いやー，楽だ．早速試してみる．

```
    $ mecab
    日本語の文章を形態素解析するとどうなるか？？
    日本語   名詞,一般,*,*,*,*,日本語,ニホンゴ,ニホンゴ
    の 助詞,連体化,*,*,*,*,の,ノ,ノ
    文章    名詞,一般,*,*,*,*,文章,ブンショウ,ブンショー
    を 助詞,格助詞,一般,*,*,*,を,ヲ,ヲ
    形態素   名詞,一般,*,*,*,*,形態素,ケイタイソ,ケイタイソ
    解析    名詞,サ変接続,*,*,*,*,解析,カイセキ,カイセキ
    する    動詞,自立,*,*,サ変・スル,基本形,する,スル,スル
    と 助詞,接続助詞,*,*,*,*,と,ト,ト
    どう    副詞,助詞類接続,*,*,*,*,どう,ドウ,ドー
    なる    動詞,自立,*,*,五段・ラ行,基本形,なる,ナル,ナル
    か 助詞,副助詞／並立助詞／終助詞,*,*,*,*,か,カ,カ
    ？ 記号,一般,*,*,*,*,？,？,？
    ？ 記号,一般,*,*,*,*,？,？,？
    EOS
```

OK.あ，「ctrl+c」で抜け出せる．

### RMeCab

<http://groups.google.co.jp/group/rmecab/files>
からRMeCab_0.84.zip（バージョンは2009/10/15現在）をダウンロードし，Rのライブラリに追加（Rコンソール＞パッケージ＞ローカルにあるzip～）．

コンソールに

```
    library(RMeCab)
```

と入力し，RMeCabパッケージを使おうとすると，**libmecab.dllが見つからなかったため～**というエラーが出るので，OKを押し，**C:\\MeCab\\bin**にある**libmecab.dll**を**C:\\R\\R-2.9.2\\library\\RMeCab\\libs**にコピーする．

以上でRからMeCabを使う準備は完了

### UniDic

MeCabには最初からipa辞書が入っているが，人工知能学会誌にunidicの記事があり，気になったのでさっそく使ってみる．

[http://www.tokuteicorpus.jp/dist/からMeCab版バイナリ辞書（Shift_JIS）をダウンロード・解凍する](http://www.tokuteicorpus.jp/dist/からMeCab版バイナリ辞書（Shift_JIS）をダウンロード・解凍する)．
なお，ダウンロードにはユーザ登録が必要となる．

中身を**C:\\MeCab\\dic\\unidic**（長谷川環境の場合）にコピー

使用方法は，

-   MeCab実行時に-dオプションを使って使用する辞書を指定する．

<!-- -->

    mecab -d "C:\MeCab\dic\unidic" 入力ファイル

一々指定するのが面倒だったり，RMeCabから使う場合には，

-   MeCabの設定ファイル（**C:\\MeCab\\etc**にある**mecabrc**）を以下のように書き換える．

<!-- -->

    dicdir =  $(rcpath)\..\dic\ipadic

を

    dicdir =  $(rcpath)\..\dic\unidic

## 形態素解析

試しに青空文庫のテキストファイル（有島武郎の生まれいずる悩み）を解析してみる．

```r
    > setwd("C:/eclipse/workspace/RMeCabTest")
    > library(RMeCab)
    > 
    > res <- RMeCabFreq("umareizuru_nayami.txt")
    file = umareizuru_nayami.txt 
    mode(res) #結果はリスト
    length = 4871 
    > [1] "list"
    > summary(res)
         Term              Info1              Info2                Freq         
     Length:4871        Length:4871        Length:4871        Min.   :   1.000  
     Class :character   Class :character   Class :character   1st Qu.:   1.000  
     Mode  :character   Mode  :character   Mode  :character   Median :   1.000  
                                                              Mean   :   7.375  
                                                              3rd Qu.:   3.000  
                                                              Max.   :1627.000  
    > res[1000:1010,] #1000から1010までを抜き出してみる
             Term Info1 Info2 Freq
    1000 気に入る  動詞  自立    2
    1001     祈る  動詞  自立    5
    1002   起きる  動詞  自立    2
    1003   起こす  動詞  自立   10
    1004   起こる  動詞  自立   15
    1005     輝く  動詞  自立    2
    1006   戯れる  動詞  自立    2
    1007     疑う  動詞  自立    7
    1008   逆らう  動詞  自立    1
    1009     休む  動詞  自立    1
    1010   休める  動詞  自立    1
```

リスト内の要素に個別にアクセスするには，

```r
    res$Term #形態素
    res$Info1 #品詞
    res$Info2 #品詞細分類
    res$Freq #頻度
```
