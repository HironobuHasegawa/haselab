---
title: R
weight: 2
---


![](tag>R tips windows osx)

# Rに関するあれこれ

## VimでR

<http://qiita.com/kirishima274/items/56233d5cd830d099b28f>

Vim-R-pluginを使う．

    NeoBundle 'vim-scripts/Vim-R-plugin'

以下は，
<http://www.lepem.ufc.br/jaa/r-plugin.html#r-plugin-installation>
を参考．

### vimcomを入れる

ダウンロード

    download.file("http://www.lepem.ufc.br/jaa/vimr/vimcom_1.2-6.tar.gz",
                  destfile = "vimcom_1.2-6.tar.gz")

#### MacVimを使わない

vimcomインストール

    install.packages("vimcom_1.2-6.tar.gz", type = "source",
                     configure.args="--enable-clientserver",
                     repos = NULL)

失敗したら，XQuartzを一旦アンインストールして，Homebrew
caskから入れ直す．

    brew cask install xquartz

VimもHomebrewから入れる

    brew install vim --with-client-server --with-lua

#### MacVimを使う

    brew install macvim --with-cscope --with-client-server --with-lua --HEAD

#### MacVim-Kaoriyaを使う

    install.packages("vimcom_1.2-6.tar.gz", type = "source",
                     repos = NULL)

### その他のRパッケージをインストール

Rのコンソールの出力を色付けて表示

    download.file("http://cran.r-project.org/src/contrib/Archive/colorout/colorout_0.9-9.tar.gz", destfile = "colorout_0.9-9.tar.gz")

    install.packages("colorout_0.9-9.tar.gz", type = "source",
                   repos = NULL)

    install.packages("setwidth")

### .Rprofileに追記

     if(interactive()){
         library(colorout)
         library(setwidth)
         options(vimcom.verbose = 1) # optional
         library(vimcom)
     }

### エイリアスを設定

.zshrcに追記

    # vim-rでHomebrewで入れたVimをVim-R-pluginのオプション付きで使う
    alias vim-r='reattach-to-user-namespace /usr/local/bin/vim --servername VIM'

### tmuxを入れる

    brew install tmux
    brew install reattach-to-user-namespace

なお、reattach-to-user-namespaceはtmuxのコピーコマンドでのクリップボード共有に使う。

設定は，<https://github.com/b4b4r07/dotfiles/blob/master/.tmux.conf>
を流用させてもらう．この設定の解説は
<http://qiita.com/b4b4r07/items/01359e8a3066d1c37edc> にある．

## Proxyの設定

プロクシ，プロクシー，プロキシ

Windowsではシステム設定を利用するために，Ｒの起動オプションに

    internet2

を付ければ良い（頭に半角のハイフン２つ付けること）．

osxでは，ホームディレクトリの.Rprofileに

    Sys.setenv("http_proxy"="http://proxy.com:8080")

を書いて置いておけば良い．
逆に，プロクシを使いたくなければ上記の記述をコメントアウトすればよい．
（UNSWで秋田高専のプロクシ設定を使おうとして困った．システム設定やbash_profileやらなんやら見直したけれどどうにもならず．たまたま見かけた記事で起動オプションをRprofileに書くことを見かけて解決
2012/10/10）

osxでのRprofileの設定例を奥村先生がさらしてくれている．
<http://oku.edu.mie-u.ac.jp/~okumura/stat/Rprofile.html>

## xdr形式での保存

    save(オブジェクト, file="******.xdr")

でオブジェクトをまるっとバイナリで保存できる．
バイナリなので，当然テキストエディタで編集は出来ないが，それを補って余りある利点がある．

1.  ファイルサイズがかなり抑えられ，そのため読み込み・書き出しが早い（特に，疎行列みたいなデータだと効果大．多分aaaaaをAみたいな圧縮かけているんだろう）．一々テキストファイルを読み込むとすごく時間がかかる．もちろん保存も同様．
2.  プラットフォーム非依存のため，Linux-Windows間のデータのやりとりが楽．学生に渡すためにnkf使ってエンコード変更とか正直めんどくさかった．

読み込みは

    load(file="******.xdr")

ドクターの頃使っていたのに，何故今まで思い出さなかったんだろうか．．．

と思っていたら，去年北海道支部に後輩の連名として出した時のスクリプトで使っていた．
ぼけてる

## Rguiの設定

### ヘルプの表示方法を変更

インストール後にヘルプの表示方法を変更するには，

    C:\R\R-*.*.*\etc\Rprofile.site

の以下を編集する．

    # to prefer Compiled HTML help
    #options(chmhelp=TRUE)
    # to prefer HTML help
    options(htmlhelp=TRUE)

上記はブラウザでhtmlヘルプを見る場合の設定（Firefoxのアドオン[MouseoverDictionary](http://maru.bonyari.jp/mouseoverdictionary/)と組み合わせると幸せになる）

参考サイト：<http://www.okada.jp.org/RWiki/?%BD%E9%B5%E9%A3%D1%A1%F5%A3%C1%20%A5%A2%A1%BC%A5%AB%A5%A4%A5%D6(7)#pbadd398>

### Rで使用するメモリサイズの変更

1500MBに設定する場合，Rguiの起動オプションに以下を追加する．なお，Wiki構文の制限により引用符を入れているが，実際は不要

    ''--max-mem-size=1500M''

Rで作業中に使用しているメモリを確認するには，

    memory.size()

2012/11/26追記

Mac OSX 10.8.2，R version 2.15.2環境 シェルから

    R --max-mem-size=1500M 
      WARNING: unknown option '--max-mem-size=1500M'

Rを起動後，

    memory.size()
    [1] Inf
     警告メッセージ： 
     'memory.size()' は Windows 固有の関数です

使えないようだ．

## Rで統計・多変量解析

「Rの基本データ構造、よく使う関数紹介」というエントリが分かりやすくて，とっかかりに適していると思った．
<http://d.hatena.ne.jp/syou6162/20080725/1216968631>

### 推定・検定

正規分布におけるZ\_{\\alpha / 2}（パーセント点）の求め方

    alpha <- 0.05
    qnorm(1-alpha/2)

### Rで重回帰分析

    y <- iris[,1]
    x1 <- iris[,2]
    x2 <- iris[,3]
    x3 <- iris[,4]
    summary( lm(y~x1+x2+x3) )

## 制御文字

|     |            |
|-----|------------|
| \\' | 一重引用符 |
| \\" | 二重引用符 |
| \\n | 改行       |
| \\t | タブ文字   |

## データの取扱い

[Rでのデータの取扱い](/memo/r/datamanipulationwithr)

## グラフィックス関連

### 色

2013/07/25

標準で入っているカラーパレット

    rainbow(7)
    heat.colors(7)

に満足できなくて良いのがないか探したら，
<http://d.hatena.ne.jp/harapon1012/20120927/1348701606>
で良いものが紹介されていた．

    library(plotrix)
    smoothColors("blue",2,"white",2,"red")

で青と白の間の色をグラデーションで2つ，白と赤の間の色をグラデーションで2つ，元の3つとあわせて7色の色が用意される．

その他参考になるのは
<http://www.okada.jp.org/RWiki/?R%A4%CB%A4%AA%A4%B1%A4%EB%BF%A7%A4%CE%CC%BE%C1%B0>

### 軸ラベルの工夫

2010/02/01

任意の軸ラベルをつける方法

RjpWikiの記事
<http://www.okada.jp.org/RWiki/?%A5%B0%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF%A5%B9%A4%CE%A5%CF%A5%C3%A5%AF#content_1_1>
を参考に，Y君（あ，どっちもだ）の卒業研究に使えるように少しアレンジした．

    plot( iris[, 1], 
    xlab = "", 
    ylab = "Sepal.Length", 
    xaxt = "n",  # x軸を描かない
    cex.lab = 1, 
    type="h")
    axis( side = 1,   # x軸を描く
    at = seq(1, nrow(iris), by = 50),   # 1からnrow(iris)まで50刻みで軸ラベルを描く
    label = iris[ , 5][seq(1, nrow(iris), by = 50)],   # アヤメの種名を軸ラベルにする．"at"引数と長さを揃えること
    cex.axis = 0.8,  # 軸ラベル文字サイズの拡大率
    lwd.ticks = 0, 
    las = 2)

### plot関数の点プロットの記号

引数pchで指定する．

例

|        |                   |
|--------|-------------------|
| pch=1  | ○                 |
| pch=2  | △                 |
| pch=3  | ＋                |
| pch=4  | ×                 |
| pch=5  | ◇                 |
| pch=6  | ▽                 |
| pch=7  | □に＋を重ねたもの |
| pch=8  | ＊                |
| pch=9  | ◇に＋を重ねたもの |
| pch=10 | ○に＋を重ねたもの |
| pch=11 | ▽に△を重ねたもの  |
| pch=12 | □に＋を重ねたもの |
| pch=13 | ○に×を重ねたもの  |
| pch=14 | □にＶを重ねたもの |
| pch=15 | ■                 |
| pch=16 | ●                 |
| pch=17 | ▲                 |
| pch=18 | ◆                 |
| pch=19 | ●                 |
| pch=20 | ●(小さい)         |

### ggplot2

そのうち使おう

    library(ggplot2)
    quartz(width=6,height=6, family="HiraKakuPro-W3")
    origin.gg <- ggplot(data, aes(x = OriginLongitude, y = OriginLatitude)) + xlim(kRangeLong) + ylim(kRangeLat)
    ## 出発地点のヒートマップ
    print(
    origin.gg + geom_bin2d() + labs(x = "Longitude", y = "Latitude")
    )

    dest.gg <- ggplot(data, aes(x = DestLongitude, y = DestLatitude)) + xlim(kRangeLong) + ylim(kRangeLat)
    ## 到着地点のヒートマップ
    print(
    dest.gg + geom_bin2d() + labs(x = "Longitude", y = "Latitude")
    )

テキスト追加レイヤー

    annotate("text", label = "Map data(c)OpenStreetMap"
               , x = 141.350, y = 43.042, base_size = 0.8
    )

因子水準で色を変えた折れ線グラフ（グラフとして適当ではないが）

    print(
    ggplot(data.frame(iris, id = 1:nrow(iris)), aes(x = id, y = Petal.Length, colour = Species))
      + geom_point()
      + geom_line(aes(group = interaction(Species)))

とても参考になる <http://d.hatena.ne.jp/triadsou/20100528/1275042816>

### ループの途中で画像等を出力

ループを使って，パラメータ（実験条件）を変えながら計算させることがよくある．そしてその時に，各条件での計算結果（場合によっては過程も）を異なるファイル名で出力したくなる．

そのような場合にはeval(parse(text =
"実行される文字列"))を使えば良い．そして，textに渡す文字列はpaste関数などで用意する．

    for (i in 1:3) {
      # ファイル名をループ制御変数で変えながらCSVで書き出し
      eval(parse(text = paste("write.csv(iris[,1:4],file=\"IrisData", i, ".csv\", row.names=F)", sep = "")))

          # プロットはprintで囲った方が良い（囲わないと上手く動かないことがある）
      print(plot(iris[, 1:4]))

      # ファイル名をループ制御変数で変えながらPDFで保存
      eval(parse(text = paste("dev.copy(pdf, file=\"IrisData", i, ".pdf\")", sep = "")))

      # 作図デバイスを閉じる
      dev.off()
    }

実行すると，ワーキングディレクトリに
IrisData1.csv，IrisData2.csv，IrisData3.csv，IrisData1.pdf，IrisData2.pdf，IrisData3.pdfというファイルが出力される．

------------------------------------------------------------------------

連番ファイル名を0埋め桁揃えしたい場合，zshのzmvを使うのが便利．

参考 <http://qiita.com/ponko2@github/items/31af073608aa20fa57e4>

zmvを使うため .zshrc に

    autoload zmv

を記述

上記のPDFファイル（IrisData1.pdf，IrisData2.pdf，IrisData3.pdf）を4桁数字で埋める（IrisData0001.pdf，IrisData0002.pdf，IrisData0003.pdf）には，

    zmv 'IrisData(*).pdf' 'IrisData${(l:4::0:)1}.pdf'

とすれば良い． 2013/07/25追記

## EclipseでR

[EclipseでR](/memo/R/StatET)

## Rで文芸的プログラミング

[Rで文芸的プログラミング](/memo/R/sweave)

## RとLaTeX

Rで作成した表（に限らずオブジェクト）をHmiscパッケージのlatex関数でLaTeXで使えるテーブル形式に変換できる．

色々オプションがあるが，それらはマニュアルを参照．以下，fileオプションについてだけ書く

    file="filename.tex"

でファイルに保存出来るが，自動的にLaTeXでコンパイルして結果をdviウェアで開こうとする．
macで使っていると，X11が必要なxdviで開こうとするのでうっとうしい．

    file=""

とすると，コンソールへの出力のみでファイル保存もコンパイルもされない．
けれど，ファイルは保存して欲しい．

マニュアルに対処法が載っていた． 通常，latex関数は

    latex(obj, file="filename.tex")

のように関数を呼び出して使うが，

    a <- latex(obj, file="filename.tex")

のように，適当なオブジェクトに代入するとLaTeXからのメッセージが出なくなる．

## RでGIS

[RでGIS](/memo/R/rgis)

## Rでテキストマイニング

[Rでテキストマイニング](/memo/R/rtextmining)

## Rで機械学習

[Rで機械学習](/memo/R/RMachineLearning)
