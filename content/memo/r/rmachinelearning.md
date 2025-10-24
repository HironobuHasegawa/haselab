---
title: Rで機械学習
tags: ["R", "機械学習"] 
---




## 混合行列 Confusion Matrix

混同行列とも．
クラス分類結果を評価するときに，モデルによる推定値と観測値を表形式で整理した混合行列とそこから算出される各種の有効性指標が良く使われる（統計学での分割表
contingency table の2変数が推定値と観測値の場合と考えれば良い）．
日本語の文献では，例えば，元田ら：データマイニングの基礎，第1版，オーム社，2006．の201ページからが参考になる．

標準環境で混合行列を作るには，table()関数を使う．
また，library(caret)中のconfusionMatrix()関数を使えば，色々な有効性指標を自動的に算出してくれる．ただし，多クラス分類の場合は"one
versus all"アプローチによる算出であることに注意．

実行例

```r
library(randomForest);library(caret) data \<- iris

## データをランダムに並べ替え data \<-
data\[sample(1:nrow(data),replace=F),\]

## データの半分を学習用データ，残りの半分を検証用データに分割 data.learn
\<- data\[1:(nrow(data)/2),\] data.test \<-
data\[((nrow(data)/2)+1):nrow(data),\]

## ランダムフォレストで分類モデルを作成 model.rf \<-
randomForest(Species\~., data=data.learn, type="class")

## 作成したモデルで予測 predicted \<- predict(model.rf, data=data.test)

## 観測値 observed \<- factor(data.test$Species)

## \[Tips\] 観測値の因子水準を使って，予測値を因子に変換 predicted \<-
factor(predicted,levels=levels(observed))

## table関数を使って混合行列を作成 (cm \<- table(predicted,observed))

## confusionMatrix関数を使って混合行列を作成し，有効性指標も算出 cm2 \<-
confusionMatrix(predicted,observed)
```

実行結果


```
> table(predicted,observed)

              observed

predicted setosa versicolor virginica

    setosa         10          8         8
    versicolor      9          8         9
    virginica       5          9         9
```

```
> (cm2 \<- confusionMatrix(predicted,observed))

Confusion Matrix and Statistics

              Reference

Prediction setosa versicolor virginica

    setosa         10          8         8
    versicolor      9          8         9
    virginica       5          9         9

Overall Statistics

                 Accuracy : 0.36            
                   95% CI : (0.2523, 0.4791)
      No Information Rate : 0.3467          
      P-Value [Acc > NIR] : 0.4469          
                                            
                    Kappa : 0.0408          

Mcnemar's Test P-Value : 0.8611

Statistics by Class:

                       Class: setosa Class: versicolor Class: virginica

Sensitivity 0.4167 0.3200 0.3462 Specificity 0.6863 0.6400 0.7143 Pos
Pred Value 0.3846 0.3077 0.3913 Neg Pred Value 0.7143 0.6531 0.6731
Prevalence 0.3200 0.3333 0.3467 Detection Rate 0.1333 0.1067 0.1200
Detection Prevalence 0.3467 0.3467 0.3067 
```

混合行列をLaTeXで使う

```
cm2 <- confusionMatrix(predicted,observed)

latex( cm2$table , digits = 3 , file="cm2conf.tex" , title = "" #
表の1行1列目の内容 , label = "tab_ConfusionMatrix" # LaTeXの\\label ,
caption = "Confusion matrix" # LaTeXの\\caption , here = T #\\begin{table}\[H\]を指定
ctableだと効かないので，texファイルに手作業で追記する , center =
"centering" #
center環境の代わりに\\centerを使う．booktabだと適切に動作しない )

latex( t(cm2$byClass) # 表が横長過ぎるので，転置する , digits = 3 #
小数第3位まで表示 , ctable = T # 論文向きのctable ,
file="cm2indicator.tex" , title = "" # 表の1行1列目の内容 , label =
"tab_EffectivenessIndicator" # LaTeXの\\label , caption = "Effectiveness
indicator" # LaTeXの\\caption , here = T # \\begin{table}\[H\]を指定
ctableだと効かないので，texファイルに手作業で追記する , center =
"centering" #
center環境の代わりに\\centerを使う．booktabだと適切に動作しない ) 
```

\[Tips\] 観測値の因子水準を使って，予測値を因子に変換

```r
    predicted <- factor(predicted,levels=levels(observed))
```

こうしておくことで，分類結果にあるクラスが含まれないような場合にも，クラス数×クラス数の正方行列で出力させることが出来る．

    ## 周辺和が必要なら，addmargins()関数を使うか，colSums()，rowSums()関数を使う

```r
    addmargins(cm)
    cm2 <- rbind(cm,Sum=colSums(cm))
    cm2 <- cbind(cm2,Sum=rowSums(cm2))
    cm2

    ## データフレームへの変換
    ### 混合行列をそのままデータフレームに変換すると
    data.frame(cm)  # (観測値の水準数×予測値の水準数)行3列のデータフレーム
    ### 周辺和があると
    #### addmargins()で周辺和を付け足すと
    data.frame(addmargins(cm))
    #### colSums()，rowSums()関数で周辺和を付け足すと
    data.frame(cm2)  # 4行4列のデータフレーム（元の見た目通り)
    ### 一度行列に変換してから，行名と列名を付けなおす
    df <- data.frame(matrix(cm, nrow = nrow(cm)))
    colnames(df) <- colnames(cm)
    rownames(df) <- rownames(cm)
    df

    # str()関数で構造を見てみると
    str(cm)
    str(addmargins(cm))
    str(cm2)
```

混合行列（tableオブジェクト）から見た目そのままにデータフレームに変換する関数Table2Dataframeを作っておく

```r
    Table2Dataframe <- function(tbl){
    df <- data.frame(matrix(tbl, nrow = nrow(tbl)))
    colnames(df) <- colnames(tbl)
    rownames(df) <- rownames(tbl)
    return(df)
    }
    # 実行例
    (df <- Table2Dataframe(tbl = cm))
    is.data.frame(df)
```

## predict()関数の挙動

predict()関数は最低限モデル作成時に使用した説明変数があれば結果を返す．
その他に目的変数や余計な変数があっても，説明変数さえあれば結果を返す．

```r
    library("randomForest")

    data <- iris
    model <- randomForest(Species ~ ., data=data)

    # モデル作成時と同一の変数を持つデータ -> OK
    pred <- predict(model,data)
    table(observed = data$Species, estimated = pred)

    # 目的変数を削除したデータ -> OK
    data1 <- data[,1:4]
    pred1 <- predict(model,data1)
    table(observed = data$Species, estimated = pred1)

    # 新規に変数を追加したデータ -> OK
    data2 <- data
    data2$newvar <- data[,1]
    pred2 <- predict(model,data2)
    table(observed = data$Species, estimated = pred2)

    # 説明変数を削除したデータ -> NG
    data3 <- data[,2:5]
    pred3 <- predict(model,data3)

    # 説明変数の名前を変更したデータ -> NG
    data4 <- data
    names(data4) <- c("a","b","c","d","e")
    names(data4)
    pred4 <- predict(model,data4)
```
