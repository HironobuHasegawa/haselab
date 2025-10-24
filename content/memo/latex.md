---
title: "LaTeX覚え書き"
tags: ["LaTeX","tips"]
---

# LaTeX

## 文書の設定（プリアンブル）

余白（margin）を上を30mm，それ以外を25mmとする（2012/11/13）

    \usepackage{geometry}
    \geometry{left=25truemm,right=25truemm,bottom=25truemm,top=30truemm} % Mergins

------------------------------------------------------------------------

インデント（indent）の量を10mmにする（2012/11/13）

    \parindent=10truemm

------------------------------------------------------------------------

Timesフォントを使う．ただし，amsmathを使う場合は先にamsmathパッケージを読み込むこと（2012/11/13）

    \usepackage{txfonts}

## 脚注と傍注

脚注は\\footnote{}

傍注は\\marginpar{}

ただし，\\marginpar{}ではコメントは本文の出現箇所と高さを揃えて出力するが，本文中に記号も番号も出ないため，どこに対するコメントなのかイマイチ分かりづらい．

<https://oku.edu.mie-u.ac.jp/~okumura/texfaq/qa/9020.html> と
<https://oku.edu.mie-u.ac.jp/tex/mod/forum/discuss.php?d=66>
を参考（つなげただけですが）に プリアンブルに

    %傍注に番号を付けるための設定
      \newcounter{marginpar}% 新しいカウンタ
      \makeatletter
        \let\org@marginpar\marginpar% オリジナルを退避
      %%
      \renewcommand{\marginpar}[1]{%
        \refstepcounter{marginpar}%  カウンタを一つ増やす
        \kern0pt$^{\themarginpar}$% 本文での傍注番号
        \org@marginpar{\footnotesize\themarginpar~#1}% 傍注
      }%
      \makeatother
    %傍注\marginpar{}の本分出現箇所に記号を付けるコマンド\marginnote{}を新たに定義
      \newcommand{\marginnote}[2][*]{%
        \inhibitglue\kern0pt #1\marginpar{\footnotesize
          \xkanjiskip=0.1zw plus 0.1zw minus 0.05zw\relax #1\ #2}}

と記述すれば，\\marginnote{}で記号と番号付きで出現箇所が分かるような傍注が出力される．

## 図表

### figure環境

図を入れる領域を準備する

#### EPS画像の取り込み

    \begin{figure}[!htb]  % here, top, bottom
    \begin{center}
    \includegraphics[width=22zw, clip]{filename.eps}  % 22文字文の幅で，空白を切り抜き
    \end{center}
    \caption{図のキャプションは図の下に}\label{fig:figlabel}  % 文章中で参照するためにラベルを付けておく
    \end{figure}

#### PDF画像の取り込み

MS Officeで作成した図について

|                                |                           |                     |                                                                 |                 |              |            |
|--------------------------------|---------------------------|---------------------|-----------------------------------------------------------------|-----------------|--------------|------------|
| OS                             | TeXディストリビューション | MS Office           | PDF作成方法                                                     | PDFのバージョン | 結果         | 確認日     |
| Windows 7 Enterprise 64bit SP1 | W32TeX                    | PowerPoint 2010     | 「名前を付けて保存」でPDF形式を選択して保存（ファイル名日本語） | 1.5             | ダメ（空欄） | 2012/04/06 |
| Windows 7 Enterprise 64bit SP1 | W32TeX                    | PowerPoint 2010     | 「名前を付けて保存」でPDF形式を選択して保存（ファイル名英語）   | 1.5             | ダメ（空欄） | 2012/04/06 |
| Windows 7 Enterprise 64bit SP1 | W32TeX                    | PowerPoint 2010     | CubePDF 1.0.0RC2で印刷                                          | 1.7             | ダメ（空欄） | 2012/04/06 |
| Mac OS X Lion                  | MacTeX                    | Excel:mac 2011      | グラフエリアをCtrl+クリックで「図として保存」でPDFを選択        | 1.3             | OK           | 2012/04/06 |
| Mac OS X Lion                  | MacTeX                    | PowerPoint:mac 2011 | グラフエリアをCtrl+クリックで「図として保存」でPDFを選択        | 1.3             | ダメ（空欄） | 2012/04/06 |
| Mac OS X Lion                  | MacTeX                    | PowerPoint:mac 2011 | 「名前を付けて保存」でPDF形式を選択して保存（ファイル名日本語） | 1.3             | だめ         | 2012/04/06 |
| Mac OS X Lion                  | MacTeX                    | PowerPoint:mac 2011 | 「名前を付けて保存」でPDF形式を選択して保存（ファイル名英語）   | 1.3             | OK           | 2012/04/06 |
| Windows 7 Enterprise 64bit SP1 | W32TeX                    | PowerPoint 2010     | CubePDF 1.0.0RC2で印刷(PDF1.3指定)                              | 1.3             | 図が90度回転 | 2012/04/06 |

TeXworksのコンパイルログから関係ありそうなエラーメッセージを抜粋すると，

    WARNING  PDF version of input file more recent than in output file.
    WARNING  Use "-V" switch to change output PDF version.
    WARNING  pdf: image inclusion failed for "ファイル名.pdf".
    WARNING  Failed to read image file: ファイル名.pdf''

PDFのバージョンが関係しているようだ．

CubePDFでPDFのバージョンを1.3（1.2も）にすると，図が回転した．ログに

    WARNING << /Rotate 90 >> found. (Not supported yet)

という警告も出る．

PDFをテキストエディタで開くと，10行目くらいに**Rotate
90**という記述がある．

OfficeとCubePDFの組合せが悪いのかと考え，他のアプリケーションとCubePDFの組合せも試した．
その結果，CubePDFを使えば必ず，**Rotate 90**となるわけではなく，
例えばテキストエディタに適当な文字を打ってPDF化すると**Rotate
0**のPDFになった（回転しない）．

また，CubePDFで作成した**Rotate
90**のPDFを再度CubePDFで印刷すると，90度回転済みのPDF（**Rotate
0**）が得られる．PDFの仕様がよく分からないけれど，どうすればどうなるかという挙動は何となく分かった．

PowerPoint:mac
2011の場合，日本語ファイル名で保存した場合はダメで，英語ファイル名で保存した場合はOK.
Excel:mac
2011の場合，日本語ファイル名でも英語ファイル名でもどっちでもOK.
この違いは何なんだろう．．．とは言え，MacTeXは日本語ファイル名をコンパイル出来ないようなので，
常に英語ファイル名にすれば良い． 2012/04/06

MacのPPT「で図として保存」しても印刷すると線が消える場合もある．．．
2012/06/19

### Table環境

表を入れる領域を準備する．

実際の表はtablar環境やtablarx環境，longtable環境などで作成する．

エクセルやOpenOffice
Calcなどで作成した表をLaTeXで扱える用に変換するツールも数多く公開されており，TeX
Wikiにまとめられている．<https://oku.edu.mie-u.ac.jp/~okumura/texwiki/?%E5%A4%89%E6%8F%9B%E3%83%84%E3%83%BC%E3%83%AB#wf7315cb>

最近（2012/06/21現在）試したものでは，[Excel2LaTeX](https://launchpad.net/excel2latex)というエクセルアドインの出来が良いと感じた．

エクセル等で作成した表を図として保存し，includegraphicsで取り込むことも可能

## 箇条書き

[https://oku.edu.mie-u.ac.jp/\~okumura/texfaq/qa/56668.htmlより](https://oku.edu.mie-u.ac.jp/~okumura/texfaq/qa/56668.htmlより)

itemize環境のitem間にスペースを開けたいとき，

    \begin{itemize}\itemsep=1zw
      \item
      \item
    \end{itemize}

知らなかった．2012/02/23

# 環境設定他

## MacTeX

スタイルファイル（例えばjsclasses）は以下に保存

    usr/local/texlive/texmf-local

容量も軽いのでSugarSyncで同期すると便利

## クラスファイルなどの場所を検索

    kpsewhich jsarticle.cls

### beamerでプレゼンテーション作成

beamerでbibtexを使おうとしたら，

    ! LaTeX Error: File `beamericonarticle.xbb' not found. Use -shell-escape option
     to generate automatically.

というエラーが出た．以下で対処する（参考
<http://todo.issp.u-tokyo.ac.jp/ja/members/wistaria/log/tex-on-mac>）．

    cd /usr/local/texlive/2012/texmf-dist/tex/latex/beamer/art
    sudo ebb -x beamericonarticle.pdf
    sudo ebb -x beamericonbook.pdf
    sudo mktexlsr

## スタイルファイル

\<del>土木計画学研究・講演集と土木計画学研究・論文集のスタイルファイルって
ないのだろーか．．．

土木学会論文集のスタイルファイルはあるけど，結構手を入れないとダメっぽい．

[こんな記述](http://oku.edu.mie-u.ac.jp/~okumura/texfaq/qa/44261.html)を見つけるにつれ，現行のフォーマットも．．．と思ってしまう．

（土木学会に対して）組版から印刷，製本までをセットにして売り込む出版社ってないのかな？\</del>

今年（2011年）の春大会から土木学会論文集フォーマットになった．いきなりフルペーパーはちょっとつらいが，LaTeXで書けるのはありがたい．秋大会はどうなるんだろう？？

------------------------------------------------------------------------

東北支部の技術研究発表会の原稿に取りかかろうと思い，書式を確認した．

レギュレーションは示されているが，MSワードのスケルトンもLaTeXスタイルファイルもない．
一からワードのページ設定をするのは面倒なので，スタイルファイルを探す．

東北大学の岩熊先生が公開してくださっているのを発見http://www.civil.tohoku.ac.jp/\~bear/node13.html#sec002005
以前からこのサイトは知っていたものの，まさか支部に使えるスタイルファイルがあるとは．．．ありがたく使わせていただく．道具が良くても内容が悪ければ意味がないので，頑張って書こう．でも来月中旬までは身動きできそうにない．．．

### 脚注の線を段の幅と等しく変更

~~jsarticleとjsce（土木学会論文集）で使えた．
他のクラスファイルでも基本的に同じだと思う．~~ ~~
\\renewcommand{\\footnoterule}{% \\kern-3\\p@ \\hrule width
.4\\columnwidth \\kern 2.6\\p@} を \\renewcommand{\\footnoterule}{%
\\kern-3\\p@ \\hrule width 1.0\\columnwidth \\kern 2.6\\p@}
に変更すれば良い．~~

[参考にしたサイト](http://muses.muses.tottori-u.ac.jp/faculty/ogata/latex.html#no7)

プリアンブルに以下を記述する方がスマートな気がする．

    \makeatletter
    \renewcommand{\footnoterule}{%
      \kern-3\p@
      \hrule width \columnwidth
      \kern 2.6\p@}
    \makeatother 

## bstファイル

土木学会向けのbstファイルを京都大学の高橋先生が公開してくださっている．ありがたい．
<http://wwwcatfish.dpri.kyoto-u.ac.jp/~yos/tex/jsce.bst>
そのままだと刊行（開催）月を表示するので，368行目の（コメントは長谷川が追加したもの）

    { month " " * year * } % 月を表示する場合

を

    { ", " * year * } % 月を表示しない場合

に書き換えると（僕にとって）いい感じ．2011/03/17

あと，articleのnoteが出力されるので，730行目の

    note output

をコメントアウトすると更にいい感じ．2011/3/18

## pbibtex

書きかけ
コンパイル時にやけに参考文献回りでワーニングが出るのでTexlipseのコンソールへの出力を確認したところ，
色々と警告されている．jsce.bst由来の所は放っておき，bibデータベース内をチェックしておかしな所（{}で囲まれていない等）を
修正．

pbibtex（pBibTeX 0.99d-j0.33 (sjis.euc)）で作成したbblファイルを見ると，

    \begin{thebibliography}{10}

となっている．
[http://cns-guide.sfc.keio.ac.jp/1999/8/3/6.htmlによると](http://cns-guide.sfc.keio.ac.jp/1999/8/3/6.htmlによると)，
thebibliography環境の

    \begin{thebibliography}{num}

のnumは「2つめの引数である
numには参考文献番号の桁数分だけ9を記述する．つまり1桁であれば9と，2桁であれば99と記述する必要がある」
とのことなので，おかしい．

## WinShell

WinShellのPDFビューアに[Sumatra
PDF](http://blog.kowalczyk.info/software/sumatrapdf/index.html)を使うと，
閲覧中のPDFを閉じなくてもPDFが更新できる．地味だけどとても嬉しい．

岩熊先生のサイトでフォント埋め込みPDF作成のTipsを発見
<http://www.civil.tohoku.ac.jp/~bear/node13.html#sec002006>

WinShellだと，PDFLaTeXのコマンドラインに

    dvipdfmx -f msembed.map -f dlbase14.map "%s.dvi"

とすれば良い．

### WinShellとSumatra PDFの連携

Sumatra PDFの「設定」→「オプション」→「逆順検索コマンドラインの設定」に

    "C:\Program Files (x86)\WinShell\WinShell.exe" -c "%f" -l %l

とすれば，PDFをダブルクリックでソースの該当箇所に飛ぶことが出来る．
2012/02/21追記

## パスを通す

ある日，WinshellからdvipdfmxでPDFを作成出来なくなっていることに気がついた．
環境変数を見てみると，MySQLのパスに上書きされていた．TeX実行ファイルへのパスを追加したところ，PDFが作成できるようになった．めでたしめでたし．

|      |                                                              |
|------|--------------------------------------------------------------|
| 変数 | 値                                                           |
| Path | C:\\Program Files\\MySQL\\MySQL Server 5.1\\bin;C:\\tex\\bin |

## jbibtexで参照するbibtexデータベースへのハードリンク

jbibtexがbibtexデータベースを読みに行く先は，

1.  メインのtexソースと同じ場所
2.  C:\\tex\\share\\texmf\\jbibtex\\bib以下のディレクトリ

らしい．

一方，僕は参考文献を全て特定のフォルダ以下に保存し，
そのフォルダ内にbibtexデータベースを作って管理している．

博士論文を書くときには，参考文献データベースを更新する度に
texソースと同じ場所にbibデータベースをコピーしていたけれど，
これはかなり面倒だった．

\<del>さっきふと，ハードリンクを使えば良いことに気がついた．

WindowsXPでハードリンクを作成するには，コマンドプロンプトで

    fsutil hardlink create <コピー先ファイル> <コピー元ファイル>

と入力する．
なお，パスにスペースを含む場合，全体を引用符でくくる必要がある．

僕の環境では，

    fsutil hardlink create C:\tex\share\texmf\jbibtex\bib\HasegawaRefDB.bib C:\MyDocuments\References\HasegawaRefDB.bib

参考URL
<http://www.atmarkit.co.jp/fwin2k/win2ktips/982hardlink/hardlink.html>
\</del>

これはかなり筋が悪いことに今更ながらに気がついた（2010/12/19 11:11）．
後できちんと調べるが，どこかにある（はずの）設定ファイルにbibデータベースの参照先を追加するか，あるいはtexソースのプリアンブルかどこかに書き加えれば対応可能なはずだ．先人がこんなファイル管理が面倒になるようなことをしてきたはずがない．

------------------------------------------------------------------------

結局，一つのファイルで管理して，そこへ絶対パスで指定する方法に落ち着いた（2013/04/28追記）

# TeX2img

TeXで作った数式をPPTに貼り付けたいときに有用．配布サイトは
<http://island.geocities.jp/loveinequality/>

似たようなWebサービス（[http://maru.bonyari.jp/texclip/texclip.php）もあるが，これは出力がpng形式とeps形式のみ](http://maru.bonyari.jp/texclip/texclip.php）もあるが，これは出力がpng形式とeps形式のみ)

## 使い方

例えば，align環境で数式番号なしで使う場合は

    \begin{align*}
      \sum_{i=1}^n a_i
    \end{align*}

とする．pngで出力した結果が以下の画像

![equation.png](/memo/equation.png)

配布サイトの
`「日本語はワープロやプレゼンソフトで書くから，数式だけ画像化してくれればいいんだ」という場合は，文書全体が \[ \] で囲まれるように，プリアンブルでこのように設定しておくと，毎回 \[ \] で囲う必要がなくて便利かもしれません。 `
「このように」の部分が画像だったのでテキスト化しておく（僕は使わないけど）．

    \documentclass[fleqn]{jarticle}
    \usepackage{amsmath,amssymb}
    \usepackage{enumerate}
    \pagestyle{empty}

    %%%%% 以下，文書全体を \[ \] で囲むための設定．
    \makeatletter

    \AtBeginDocument{\[}

    \expandafter\expandafter\expandafter\def
    \expandafter\expandafter\expandafter\@enddocumenthook
    \expandafter\expandafter\expandafter{%
    \expandafter\]\@enddocumenthook}

    \makeatother

TeX2imgはMac版もあるが，MacではLaTeXiTを使う予定．alignなどの環境を簡単に指定できるのが便利そう．

# pdfのフォント埋め込み状況の確認

    pdffonts filename.pdf

を使う．以下はRで作成したグラフに対して適用した結果

    name                                 type              encoding         emb sub uni object ID
    ------------------------------------ ----------------- ---------------- --- --- --- ---------
    ZapfDingbats                         Type 1            ZapfDingbats     no  no  no      10  0
    Helvetica                            Type 1            Custom           no  no  no      11  0
    Helvetica-Bold                       Type 1            Custom           no  no  no      12  0

左端がフォント名．emb（embedの略）と書いてあるところがnoなので，埋め込まれていない．

論文の書式校正で印刷会社さんからフォントを埋め込むかフォント情報無しにするように指摘を頂いたので，フォントを埋め込む．
色々方法はあるようだが，フォントが埋め込まれていないpdfをプレビュー.appで開いて，「ファイル」→「印刷」→ダイアログの左下「PDF」→「PDFとして保存」でも埋め込まれる．

再度pdffontsで確認

    name                                 type              encoding         emb sub uni object ID
    ------------------------------------ ----------------- ---------------- --- --- --- ---------
    TLJFRR+Helvetica-Bold                TrueType          MacRoman         yes yes no      11  0
    PIAION+ZapfDingbatsITC               TrueType          WinAnsi          yes yes yes     10  0
    QUYFWW+Helvetica                     TrueType          MacRoman         yes yes no       8  0

埋め込まれた．

# dvipdfmxでpdfにアクセス制限

<http://d.hatena.ne.jp/cou929_la/20100225/1267073048>

# Beamer classでプレゼンテーション

現在国際会議のプレゼンテーションを準備中．
英語なので不具合も出にくいだろうと思い，ついにBeamerに手を出してみる．

> マニュアルの抜粋と私的コメントを混ぜたもの

<http://neurodynamics.jp/etc/beamer>

## テンプレート

標準で用意されているテーマと色の組合せをマトリクスで整理している<http://www.hartwork.org/beamer-theme-matrix/>

スッキリして見やすいので，今回はこれを使おう<http://www.inference.phy.cam.ac.uk/ph347/beamer.html>

Appleの製品発表っぽいテンプレートもある<https://sites.google.com/site/tokeijikakenoringo/Home/keynote-like-gradient-theme-for-latex-beamer>

## uplatexでbeamer（2024/11/04追加）
TeX Live2024くらいから，

以下のような（おそらくは）一般的な設定でコンパイルすると，

```
\documentclass[dvipdfmx]{beamer}
\usepackage{graphicx}
```

次のような警告が出るようになった．

```
/usr/local/texlive/2024/texmf-dist/tex/latex/pgf/utilities/xxcolor.sty|| LaTeX Warning: Package "xcolor" has already been loaded: ignoring load-time option "dvipdfmx".
```


しかも，Vim-LaTeX（'vim-latex/vim-latex'）でコンパイルすると，編集中のファイルからxxcolorパッケージに遷移するのでとても不便だ．
silenceパッケージで警告を消そうとしてもうまくいかない．
lualatexへの乗り換えも検討したが，手持ちのuplatex資産を引き続き使いたい．

ここ数ヶ月思い出しては検索したり試行錯誤したりしていたが，解決出来ず．
TeXフォーラムでお知恵を拝借しようかと思い，最小のサンプルを作りながら念のためと検索したところ，とりあえずは警告が出ない設定を見つけた．
正直に言って中身は理解していない．

具体的な設定は，クラスファイルの読み込み前に以下のコマンドでgraphicxパッケージにdvipdfmxオプションを渡し，
**\PassOptionsToPackage{dvipdfmx}{graphicx}**

クラスファイル（beamer）のオプションにはdvipdfmxを入れない．
graphicxパッケージはオプションをつけずに読み出す．

TeX Live2024のuplatexでbeamerを使っている人は多くないと思われるが，誰かの役に立つかも知れないので，備忘録を兼ねて記録する（Hugoの使い方もすっかりと忘れている）．


簡単なサンプルコードは以下の通り
```
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\RequirePackage{plautopatch} % Automated patches for pLaTeX/upLaTeX
\PassOptionsToPackage{dvipdfmx}{graphicx}

\documentclass{beamer}
\usepackage{graphicx}


%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% タイトルページ設定
\title{タイトル}
\author{著者}
\date{}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% 文書開始
\begin{document}

% タイトルページ
\frame{\titlepage}

% 図入りのページ
\begin{frame}{フレームタイトル}

PDFの図\ref{fig_tiger}を入れる．

\alert{強調}

数式：$a = b^2 = c$

\begin{figure}[!htb]
\centering
    \includegraphics[height=0.5\textheight,keepaspectratio,clip]{tiger.pdf}
\caption{Tiger}
\label{fig_tiger}
\end{figure}

\end{frame}

\end{document}
```
