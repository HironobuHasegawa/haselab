---
title: "Sweaveに関するメモ"
tags: ["R", "LaTeX", "osx"]
---
Sweave ![](tag>R LaTeX osx)


参考URL：<http://qh73xe.sakura.ne.jp/wordpress/?p=72>

## 設定

環境はMac OSX 10.8.4

### TeX本体

MacTeX2013を <http://oku.edu.mie-u.ac.jp/~okumura/texwiki/?Mac>
に従って設定

### ZXjatypeパッケージ

(公式サイト)\[<http://zrbabbler.sp.land.to/zxjatype.html>\]

Sweaveはチャンク内に記したRコードをTeXソース中でverbatim環境（のラッパーであるSinput環境・Soutput環境）に出力するが，XeLaTeXではverbatim環境で日本語を使えないようだ．
そこで，ZRさんが開発・公開しているZXjatypeパッケージを使う．

残った問題：長いコードの場合，折り返しが効かずに右側にはみ出る．ざっと調べたところ，皆悩んでいるようだ．

### Sweave.styとae.sty

<http://svn.r-project.org/R/trunk/share/texmf/tex/latex/Sweave.sty>

<http://web.mit.edu/ghudson/dev/nokrb/third/tetex/texmf/tex/latex/ae/ae.sty>

ダウンロードしたら

    /usr/local/texlive/texmf-local/tex/latex/local

に配置し，

    sudo mktexlsr

を実行

### Rstudio

-   Program defaults
    -   Weave Rnw files using: Sweave
    -   Typeset LaTeX into PDF using: XeLaTeX
-   LaTeX editing and compilation は全てチェックを入れる
-   PDF preview
    -   Preview は System Viewer
    -   Always enable Rnw concordance もチェックを入れる

## 実行例

\`\`\`

    \documentclass[a4paper]{bxjsarticle}
    \usepackage{zxjatype}
      \setCJKmainfont{HiraMinPro-W3}
      \setjamonofont{Ricty}  % 効いていないかも
      \setmonofont{Ricty}  % 効いていないかも
    \usepackage{mediabb}
    \XeTeXlinebreaklocale "ja"

    \title{タイトル}
    \author{著者}

    \begin{document}
    \SweaveOpts{concordance=TRUE}

    \maketitle

    \section{Sweaveで日本語}

    本文はもちろん，

    <<>>=
    cat("コード中の日本語出力も大丈夫")
    @

    \section{グラフにも日本語}

    <<>>=
    par(family="HiraKakuPro-W3")  # グラフに日本語を使う場合
    data <- iris
    summary(data)
    @

    echoオプションで実行したコードを出力するかどうかを制御できる．

    コードを出力しないでグラフだけを描画する場合（\tt{plot(data[,1:2])}だけを実行）

    <<fig = TRUE, echo = FALSE>>=
    plot(data[,1:2])
    title("チャンクオプションはfig = TRUE, echo = FALSE")
    @

    コードを出力する場合
    \begin{center}
    <<fig = TRUE, echo = TRUE>>=
    plot(data[,1:2])
    title("グラフにも日本語")
    text(x=7,y=2,"text()で日本語出力")
    @
    \end{center}


    \end{document}

\`\`\`

![作成したPDF](/memo/r/sweavetest.pdf)
