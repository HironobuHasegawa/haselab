---
title: Rでのデータの取扱い
weight: 2
tags: ["R", "tips"] 
---


# データ型

## 日付型データ

Rデータ自由自在を参考にした

### 日付型データへの変換

|          |       |                |
|----------|-------|----------------|
| 年月日   | 時刻  | 備考           |
| 20090407 | 20:38 | 今             |
| 20090408 | 08:50 | 明日の集合時間 |
| 20090408 | 10:00 | 入学式開始時間 |

のようなデータフレーム"TT"が与えられた場合に
日付部分をうまく日付型データにするには，

    yyyymmddHHMM <- paste(TT[,1], TT[,2], sep='')
    DATE <- strptime(yyyymmddHHMM, format='%Y%m%d%H:%M')

とすれば良い．結果は

    "2009-04-07 20:38:00" "2009-04-08 08:50:00" "2009-04-08 10:00:00"

となる．

-   strptime関数はPOSIX日付を返し，オプションtzでタイムゾーンを指定可能（日本は"JST"）
-   POSIX日付の値は，内部的には1970年1月1日0時0分0秒からの経過秒数として保存されている（実数型に変換すると確認できるが，時差に注意（出てきた値を3600で除すれば，時差が出る））

### 日付型データにすると嬉しいこと

    DATE[1]-DATE[2] #時間差
    difftime(DATE[1], DATE[2], units="mins")  #時間差（分）
    format(DATE[1], '%A') #曜日を表示
    format(DATE[1], '%a') #曜日を表示
    format(DATE[1], '%b') #月を表示
    format(DATE[1], '%B') #月を表示
    format(DATE[1], '%H') #10進法で時間（24時間表示）を表示，Mで分，Sで秒

### 任意の期間および時間の日付型データを生成する

（例）2011年4月1日から2011年12月31日まで，7時から21時までのデータを生成

    # 指定した全ての値の組合せからなるデータフレームを作成
    temp2011 <- expand.grid(2011,c(4:12),c(1:31),c(7:21))

    # 日付型（ベクトル）に変換
    date <- strptime(paste(temp$Var1,temp$Var2,temp$Var3,temp$Var4,sep=" ")
                     , format="%Y %m %d %H", tz="JST")

    # 昇順に並べ替え
    date <- sort(date)

    # データフレームに変換
    date <- data.frame(Date=date)

    # NAを含む行を削除
    date <- na.omit(date)

日付型データに変換した時点であり得ない日付（例えば，11月31日など）はNAになる．これをna.omit()関数で削除するのがポイント

もし，2011年12月15日の13時までのデータが欲しければ，上記の手順で作成したデータからsubset()関数などで取り出せば良い

    date1 <- subset(date,Date <= strptime("2011-12-15 13:00:00", format="%Y-%m-%d %H", tz="JST"))

# ベクトルの生成

ループを回した結果を順にベクトル要素として保存したい場合がよくある．その場合，あらかじめnumeric()関数を使って繰り返し数と同じだけの長さで，全ての要素が0のベクトルを作れば良い

    res <- numeric(10)
    for (i in 1:10){
    res[i] <- i^2
    }
    res

2013/07/31追記 NULLで初期化した方が楽で良いようだ．

    res <- NULL
    for (i in 1:10){
    res[i] <- i^2
    }
    res

# データフレーム dataframe

## データフレームから任意の条件でデータを抽出

データフレームirisからSepal.Lengthの値が6より大きい行全体を抜き出す

    # subset関数を使う方法
    subset(x = iris, subset = Sepal.Length > 6)

    # データフレームの添字を使った要素へのアクセスを使う方法
    iris[iris$Sepal.Length > 6, ]

    # どちらも同じ結果を返す
    all.equal(subset(x = iris, subset = Sepal.Length > 6), iris[iris$Sepal.Length > 6, ])

データフレームirisからSepal.Lengthの値が6より大きい行の，Speciesだけを抜き出す

    # subset関数を使う方法
    subset(x = iris, subset = Sepal.Length > 6, select=c("Species"))
    subset(x = iris, subset = Sepal.Length > 6, select=c(5))  # "Species"の列番号が分かっていれば，数字で指定しても良い

    # データフレームの添字を使った要素へのアクセスを使う方法
    iris[iris$Sepal.Length > 6, "Species"]
    iris["Sepal.Length" > 6, "Species"]  # これはダメ
    iris[iris$Sepal.Length > 6, 5]  # "Species"の列番号が分かっていれば，数字で指定しても良い
    iris[1 > 6, 5]  # これはダメ

    # どちらも本質的には同じ結果であるが，subsetを使った方はデータフレームで，添字を使った方はベクトルで結果が帰ってくる
    all.equal(subset(x = iris, subset = Sepal.Length > 6, select=c("Species")), iris[iris$Sepal.Length > 6, "Species"])

時と場合によって使い分ければ良い

## リストと一致するものを取り出す

    # iris$Sepal.Lengthの度数分布
    table(iris$Sepal.Length)

    # iris$Sepal.Lengthが4.3, 4.4, 4.5, 4.6のいずれかに一致する行を抽出
    iris[iris$Sepal.Length %in% c(4.3, 4.4, 4.5, 4.6), ]

実行結果

       Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    4           4.6         3.1          1.5         0.2  setosa
    7           4.6         3.4          1.4         0.3  setosa
    9           4.4         2.9          1.4         0.2  setosa
    14          4.3         3.0          1.1         0.1  setosa
    23          4.6         3.6          1.0         0.2  setosa
    39          4.4         3.0          1.3         0.2  setosa
    42          4.5         2.3          1.3         0.3  setosa
    43          4.4         3.2          1.3         0.2  setosa
    48          4.6         3.2          1.4         0.2  setosa

    # iris$Sepal.Lengthが4.3, 4.4, 4.5, 4.6のいずれかに一致しない行を抽出
    iris[!(iris$Sepal.Length %in% c(4.3, 4.4, 4.5, 4.6)), ]

## データフレーム行の並べ替え

    iris[order(iris$Sepal.Length), ]  # "Sepal.Length"の値で昇順に並べ替え
    iris[order(iris$Sepal.Length, decreasing=TRUE), ]  # "Sepal.Length"の値で降順に並べ替え
    iris[sample(nrow(iris)), ]  # ランダムに並べ替え

## データフレームの行と列の入れ替え

matrixの場合はt()関数が用意されているが，データフレームにはない（t()関数を適用出来るが，列名が変になることがある）．簡単な処理なので関数にするまでもないが，あえて関数にすればこんな感じ．

    # データフレーム df の行と列を入れ替える関数
    TransposeDf <- function(df){
      # データフレームを転置
      tdf <- data.frame(t(df))
      # 行名と列名を付けなおす
      rownames(tdf) <- colnames(df)
      colnames(tdf) <- rownames(df)
      return(tdf)
    }

    # 実行例
    TransposeDf(df = iris)

# 行列 matirix

行列をMとする．

    diag(M)  # 行列の対角成分を抽出
    sum(diag(M))  # 行列の対角成分の和を計算

クラス分類（判別分析など）を使った後，結果をconfusion
matrixで整理することが多い．confision matrixからerror
ratioを計算するには，上記の対角成分の和を利用して

    (sum(M)-sum(diag(M)))/sum(M)

とすればよい．

# 標本抽出 sampling

## 無作為抽出 random sampling

irisデータから重複しない10個の標本を抽出

    sample(data=iris, size=10, replace=FALSE)

## 層化抽出 stratified sampling

モデルパラメータを求めたり，モデルの評価・比較を行うために交差検証法
cross validation を行うことがよくある．
~~データを分割する際に，元のデータ分布に近いような分割を行いたいときにstrata()関数が使える（sample()関数とsplit()関数を駆使して実装したら，見事に車輪の再発明だった）~~誤りだった．strata()関数では，以下のようなサンプリングを（単発で）行うことは出来るが，データを分割するのには適していない．無駄じゃなかった．

irisデータから"Species"を層化変数（層を分ける基準）として，Speciesがsetosaのデータを7個，versicolorを8個，virginicaを9個，合計24個のサンプルを持つ標本集合を得る．

    s <- strata(data=iris
    , stratanames=c("Species")
    , size=c(7,8,9)
    , method="srswor"  # simple random sampling without replacement 
    )
    stratified.data <- getdata(data=iris
    ,m=s
    )

元々のデータフレームに"ID_unit"，"Prob"，"Stratum"列が追加され，"Stratum"が分割されたデータの属する層（この場合Species）を数字で表す．よって，SpeciesとStratumはサンプル毎に対応が取れている．

------------------------------------------------------------------------

strata()関数について勘違いして，sizeオプションで分割後の各分割データに含まれる標本数を指定すると思っていたときに，元のデータ数と分割数に応じてsizeオプションに渡す数値ベクトルを計算するためのスクリプト．今となっては意味が無いが，一応残しておく

    data <- iris
    k <- 7

    if (sum(rep(as.integer(nrow(data)/k),k))==nrow(data)){
      strata.size <- rep(as.integer(nrow(data)/k),k)
    }else{
      temp <- rep(as.integer(nrow(data)/k),k)
      temp[1] <- temp[1] + nrow(data) - sum(rep(as.integer(nrow(data)/k),k))
      strata.size <- temp
      rm(temp)
    }

    strata.size

実行結果

    [1] 24 21 21 21 21 21 21
