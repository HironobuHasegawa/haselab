---
title: "Pandoc"
---

\[Pandoc公式サイト\](<http://johnmacfarlane.net/pandoc/>)

参考 <http://qiita.com/items/80bcd0f353ef5b8980ee>

Markdown記法を使って文書を書いて，それをPandocを使ってLaTeXフォーマットに変換することが出来る．それ以外のも様々なフォーマットの入出力に対応している．

Markdown から LaTeX

```shell
    pandoc input.md -s -o output.tex
```

LaTeX から Markdown
```
    pandoc -s input.tex -o output.md
```

出力されたファイルはeptex+dvipdfmxを使えるように設定済みのTeXShopで何ら調整することなく普通にコンパイル出来た．プリアンブルを含めて色々気になるところはあるけれど，document環境内だけ取り出せばいいので問題なし．
見出しがレベルに応じてsectionやsubsectionに，箇条書きも数字のあるなしでenumerateとitemizeそれぞれに．斜体は\\emphに，太字は\\textbfにきちんとなった．他は試していないけれど，これだけでも十分だ．もちろん日本語も問題なかった．
いやー，凄い．
ここ数年は論文やレジメは再利用性を考えてLaTeXで，ちょっとしたメモ書きや調べ物をまとめるには書式が簡単なWiki（DokuWikiかTiddlyWiki）をメインに使っていた．
数式が必要なもの以外は全部Markdownに移行してしまおうか．

MacでMarkdownを書く方法としては，

-   Coteditor + Marked
-   Vim + Marked
-   \[Kobito\](<http://kobito.qiita.com/ja>)
-   Mou

などがある．
