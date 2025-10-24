---
title: "Mac"
---


![](tag>osx)

# Mac OS X

## MS Office 2011

先週末のアップデート後にExcelが落ちるようになり，セーフモードを試すも意味が無いので，仕方なく再インストールした．

単純なアプリケーションフォルダからの削除・再インストールではダメで，AppClearnerでの削除でもダメで，MS公式の完全なアンインストール方法実施後の再インストールでようやく使用可能になった．

<http://support.microsoft.com/kb/2398768/ja>

疲れた．

# メンテナンス

## ディスクのアクセス権の検証と修復

OS
Xをアップデートしたらしておいたほうが良いとのことなので，ディスクユーティリティから実行した．

参考 <http://www.ttcbn.net/no_second_life/archives/22570>

2012/05/14

## Yosemite 10.10.5からEl Capitan 10.11.6にアップグレード

0\. Time Machineでバックアップを取っておく 1. App
Storeからアップグレード 2. 再起動し，起動時にcmd+Rを押し続けて「OS X
復元」を起動 3. 「ディスクユーティリティ」でシステムドライブを削除 4.
「OS Xを再インストール」でEl Capitanをネットワークインストール 5.
インストール後に起動し，データやアプリをTime Machineから復元する

\`\`\` brew cask install qlcolorcode quicklook-csv qlmarkdown \`\`\`

# vim

## インストール

日本語環境で使うのに便利な設定がされているものが公開されているので，それを使う
<http://code.google.com/p/macvim-kaoriya/>

## 設定

シェルから起動させるための，.bash_profileへの設定は本ページ下部の[シェル設定](http://akita-nct.jp/hasegawa/wiki/doku.php?id=memo:osx#%E3%82%B7%E3%82%A7%E3%83%AB%E8%A8%AD%E5%AE%9A)を参照

[Vimで挿入モードから抜ける時に英数入力に切り替える](http://r7kamura.hatenablog.com/entry/20110217/1297910068)を参考に，[KeyRemap4MacBook](KeyRemap4MacBook)を設定

.vimrcへの設定は
<http://yuroyoro.hatenablog.com/entry/20101104/1288879591>
を参考にした．大変充実した内容だが，ほとんど理解できないので，取りあえず

    " ;でコマンド入力( ;と:を入れ替)
    noremap ; :
    noremap : ;

だけを設定しておく． 以下を追加（2012/11/07）

    " 行番号を表示（set nonumberで非表示） 
    set number

以下を追加（2012/11/09）参考
<http://nanasi.jp/articles/others/xmllint.html#mac>

    " xmlを開いたときにxmllintを使って整形する
    au FileType xml exe ":silent 1,$!xmllint --format --recover - 2>/dev/null"

## vimコマンド

### コマンドモードで使うコマンド

#### 移動

|     |                |
|-----|----------------|
| h   | 左に移動       |
| j   | 上に移動       |
| k   | 下に移動       |
| l   | 右に移動       |
| w   | 次の単語に移動 |
| 0   | 行頭に移動     |
| $   | 行末に移動     |

#### 編集

|     |                              |
|-----|------------------------------|
| x   | カーソル位置にある文字を消去 |
| dd  | カーソルのある行全体を消去   |

#### 入力モードへの切り替え

|     |                          |
|-----|--------------------------|
| i   | カーソルの左から入力開始 |
| a   | カーソルの右から入力開始 |
| I   | 行頭から入力開始         |
| A   | 行末から入力開始         |

#### 検索

|            |                      |
|------------|----------------------|
| /\<文字列> | 文字列を順方向に検索 |
| ?\<文字列> | 文字列を逆方向に検索 |
| n          | 順方向に検索         |
| N          | 逆方向に検索         |

#### その他

|              |                              |
|--------------|------------------------------|
| :scriptnames | 読み込まれたスクリプトを確認 |

# 諸設定

## ネットワーク

## 入力デバイス関係

### キーボード関係

[KeyRemap4MacBook](http://pqrs.org/macosx/keyremap4macbook/index.html.ja)

### トラックパッド関係

BetterTouchTool：トラックパッドの機能を拡張するアプリ

ダウンロード：<http://blog.boastr.net/?page_id=1722>

参考：<http://nori510.com/archives/8117>

参考：<http://d.hatena.ne.jp/RyoAnna/20110731/1312100591>

## Finder

2012/03/27 ファインダーの拡張 XtraFinder
<http://mag.torumade.nu/?p=6602>

2012/11/08追記
XtraFinderがバージョンアップを繰り返し，気がついたらタブ表示にも対応していた．しかも無料のまま．素晴らしい．

半年ほど前に買ったTotalFinderは封印し，しばらくはXtraFinderで行こうと思う．

### タイトルバーにパスを表示

<http://inforati.jp/apple/mac-tips-techniques/system-hints/how-to-display-the-full-path-of-folder-in-macos-finder-titlebar.html>

便利かと思ったけど，パスバー表示＆タブ環境では使いにくい．パスが右寄せでカレントディレクトリが表示されるような形になれば便利かも．

## その他の環境整備

### プリンタの設定

モノクロをデフォルトにしたい場合

<http://d.hatena.ne.jp/pcmaster/20111203/p2>

を参考に設定する．

ただし，ブラウザはFirefoxではアクセス出来なかったのと，シェルから

    cupsctl WebInterface=yes

を実行する必要があった．

### 起動音を消す

2012/11/08

<http://appdrill.net/60641/mac-boot-mute.html> 経由
<http://osxdaily.com/2012/11/04/disable-mac-boot-chime/> より

消すとき

    sudo nvram SystemAudioVolume=%80

戻すとき

    sudo nvram -d SystemAudioVolume

### メールリンクの処理

2013/04/18

Firefoxでmailtoのリンクを踏んだとき，デフォルトではOSの標準メールアプリケーションとなる．
<https://support.mozilla.org/ja/kb/Changing%20the%20e-mail%20program%20used%20by%20Firefox>

標準メールアプリケーションが設定したつもりのないアプリになっていたので，変更したい．

OS標準のメーラー・ブラウザの変更方法は以下を参照
<http://support.apple.com/kb/HT1637?viewlocale=ja_JP&locale=ja_JP>

まさか，Safari.appとMail.appからしか変更できないとはビックリした．Mail.appなんか一度も使ってないから，アカウントの設定をしなくちゃならなかった．下手にきちんと登録して受信されても迷惑なので，適当なサーバ情報で実際はアカウントにアクセス出来ない状態に設定した．この設定方法はポンコツだろう．

# homebrew

2012/04/17

homebrewを入れようと思い，インストール用のrubyスクリプト

    /usr/bin/ruby -e "$(/usr/bin/curl -fksSL https://raw.github.com/mxcl/homebrew/master/Library/Contributions/install_homebrew.rb)"

を実行するも，該当するファイルにアクセス出来ないというようなエラーが出る．
curlでシステム設定のプロクシを使ってくれないみたいなので，

    ~/.bash_profile

にcurlでproxyを使うために

    export http_proxy="http://proxy.com:8080/"
    export https_proxy="http://proxy.com:8080/"
    export ftp_proxy="http://proxy.com:8080/"

を記述した（プロクシアドレスとポート番号は適宜変更）．

curlのマニュアルを読むと，<http://www.hcn.zaq.ne.jp/___/unix/curl_manpage.html#O-proxy>
にproxyのオプションがあった．これを使って，

    /usr/bin/ruby -e "$(/usr/bin/curl -fksSL --proxy "http://proxy.com:8080/" https://raw.github.com/mxcl/homebrew/master/Library/Contributions/install_homebrew.rb)"

でいいような気もする（2012/04/28追記，後日検証する）．というか，ルビースクリプトダウンロードして実行すればいいような気もする．

シェルを再起動してから上記スクリプトを実行すると，

    ==> This script will install:
    /usr/local/bin/brew
    /usr/local/Library/Formula/...
    /usr/local/Library/Homebrew/...
    ==> The following directories will be made group writable:
    /usr/local/.
    /usr/local/bin
    /usr/local/etc
    /usr/local/include
    /usr/local/lib
    /usr/local/share
    /usr/local/share/man
    /usr/local/share/man/man1
    ==> The following directories will have their group set to admin:
    /usr/local/.
    /usr/local/bin
    /usr/local/etc
    /usr/local/include
    /usr/local/lib
    /usr/local/share
    /usr/local/share/man
    /usr/local/share/man/man1

    Press enter to continue

と出て，エンターを押すと，パスワードを求められる．ログインパスワードを入力すると．．．

    ==> Downloading and Installing Homebrew...
    ==> Installation successful!
    You should run `brew doctor' *before* you install anything.
    Now type: brew help

とりあえずインストールは出来た バージョンチェック

    $ brew -v
    0.9

homebrewを使って何かをインストールする前に\`brew
doctor'を実行すれというので実行

    $ `brew doctor'

すると，Warningがたくさん出た．

    Warning: Some directories in /usr/local/share/man aren't writable.
    This can happen if you "sudo make install" software that isn't managed
    by Homebrew. If a brew tries to add locale information to one of these
    directories, then the install will fail during the link step.
    You should probably `chown` them:

      /usr/local/share/man/de
      /usr/local/share/man/de/man1

書き込めなくってエラーが出るから，所有者を変えろとのこと．変えましょう．

    $ chown -v hasegawa /usr/local/share/man/de
    chown: /usr/local/share/man/de: Operation not permitted

権限が足りないので，sudo

    $ sudo chown -v hasegawa /usr/local/share/man/de
    Password:
    /usr/local/share/man/de

OK．次

    $ sudo chown -v hasegawa /usr/local/share/man/de/man1
    Password:
    /usr/local/share/man/de/man1

OK．次の警告はXcodeが古いから新しくすれとのこと．

    Warning: You have Xcode 4.2.1, which is outdated.
    Please install Xcode 4.3.

App Store見ても更新がない．アップデート方法を検索すると，このサイト
<http://null.ly/post/17783037515/switch-xcode-4-2-1-to-4-3>
で別にXcode使わないならコマンドラインツールだけ別にインストール出来るとのこと．
Xcode使わないから，アンインストール．

    $ sudo /Developer/Library/uninstall-devtools --mode=all

アップルの開発者向けサイトhttps://developer.apple.com/ でユーザ登録し，
<https://developer.apple.com/downloads/index.action> でcommand
lineを検索して cltools_lion_latemarch12.dmg
をダウンロードしてインストールする．とりあえずgcc入っているかチェック

    $ gcc -v
    略
    gcc version 4.2.1 (Based on Apple Inc. build 5658) (LLVM build 2336.9.00)

入ってる．

（前の続き2012/04/28）

次，

    Warning: Unbrewed dylibs were found in /usr/local/lib.
    If you didn't put them there on purpose they could cause problems when
    building Homebrew formulae, and may need to be deleted.

    Unexpected dylibs:
      /usr/local/lib/libmecab.1.dylib
    Warning: Unbrewed .la files were found in /usr/local/lib.
    If you didn't put them there on purpose they could cause problems when
    building Homebrew formulae, and may need to be deleted.

    Unexpected .la files:
      /usr/local/lib/libmecab.la
    Warning: Unbrewed static libraries were found in /usr/local/lib.
    If you didn't put them there on purpose they could cause problems when
    building Homebrew formulae, and may need to be deleted.

    Unexpected static libraries:
      /usr/local/lib/libmecab.a

mecab関連のファイルが邪魔らしい．面倒なのでmecabをアンインストールする．

    $ cd mecab-0.98
    $ make uninstall

もう一度

    $ brew doctor

    Warning: Your Homebrew is outdated
    You haven't updated for at least 24 hours, this is a long time in brewland!
    Warning: Your Xcode is configured with an invalid path.
    You should change it to the correct path. Please note that there is no correct
    path at this time if you have *only* installed the Command Line Tools for Xcode.
    If your Xcode is pre-4.3 or you installed the whole of Xcode 4.3 then one of
    these is (probably) what you want:

      sudo xcode-select -switch /Developer
      sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer

    DO NOT SET / OR EVERYTHING BREAKS!

確かに，I have \*only\* installed the Command Line Tools for
Xcode.なので，いろいろ調べるのも面倒なので，App
StoreからXcodeをインストールする．

    $ xcode-select -print-path
    /Developer

正しいpathに変更

    $ sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer
    Password:
    $ xcode-select -print-path
    /Applications/Xcode.app/Contents/Developer

さて，もう一度

    $ brew doctor
    Warning: Your Homebrew is outdated
    You haven't updated for at least 24 hours, this is a long time in brewland!
    hasegawa99:Developer hasegawa$

一応homebrewのインストールスクリプトを再実行したが，やはりbrew
doctorで同じ警告が出る．のでアップデート

    $ brew update
    Initialized empty Git repository in /usr/local/.git/
    remote: Counting objects: 64923, done.
    remote: Compressing objects: 100% (32992/32992), done.
    remote: Total 64923 (delta 42949), reused 50825 (delta 31137)
    Receiving objects: 100% (64923/64923), 9.21 MiB | 513 KiB/s, done.
    Resolving deltas: 100% (42949/42949), done.
    From https://github.com/mxcl/homebrew
     * [new branch]      gh-pages   -> origin/gh-pages
     * [new branch]      master     -> origin/master
    Checking out files: 100% (2219/2219), done.
    HEAD is now at 457c025 cassandra 1.1.0
    Already up-to-date.

もう一度確認．

    $ brew doctor
    Your system is raring to brew.

OK.

使い方はhttp://yosilove.blogspot.jp/2010/12/homebrew.htmlに非常に分かりやすくまとまっている．
（2012/09/14追記）

## wget

    $ brew install wget

## git

    $ brew install git
    ==> Downloading http://git-core.googlecode.com/files/git-1.7.10.tar.gz
    ######################################################################## 100.0%
    ==> make prefix=/usr/local/Cellar/git/1.7.10 CC=/usr/bin/clang CFLAGS=-Os -w -pi
    ==> make CC=/usr/bin/clang CFLAGS=-Os -w -pipe -march=native -Qunused-arguments
    ==> make clean
    ==> Downloading http://git-core.googlecode.com/files/git-manpages-1.7.10.tar.gz
    ######################################################################## 100.0%
    ==> Downloading http://git-core.googlecode.com/files/git-htmldocs-1.7.10.tar.gz
    ######################################################################## 100.0%
    ==> Caveats
    Bash completion has been installed to:
      /usr/local/etc/bash_completion.d

    The OS X keychain credential helper has been installed to:
      /usr/local/bin/git-credential-osxkeychain

    The 'contrib' directory has been installed to:
      /usr/local/share/git-core/contrib
    ==> Summary
    /usr/local/Cellar/git/1.7.10: 1166 files, 23M, built in 79 seconds

# シェル設定

2012/11/19

homebrew経由でzshをインストール

    brew install zsh

zshをデフォルトのシェルに設定

    sudo vi /etc/shells

末尾に

    /usr/local/bin/zsh

を追記．以下のコマンドを実行

    chpass -s /usr/local/bin/zsh

参考：<http://tukaikta.blog135.fc2.com/blog-entry-195.html>

------------------------------------------------------------------------

2011/12/20

    ~/.bash_profile

に <http://blog.cles.jp/item/1620> のコメント欄で見つけた

    alias cd='cdx'
    function cdx()
    {
    \cd ${1+"$@"}
    ls -al
    }

を追記し，

    source ~/.bash_profile

で適用

    cd

でディレクトリ移動と同時に

    ls -al

が実行されてとても便利

------------------------------------------------------------------------

2012/10/17

コンソールからmacvimを使用する（出典：
<http://code.google.com/p/macvim-kaoriya/wiki/Readme#%E3%82%B3%E3%83%B3%E3%82%BD%E3%83%BC%E3%83%AB%E3%81%8B%E3%82%89_MacVim_.app%E3%82%92%E8%B5%B7%E5%8B%95%E3%81%99%E3%82%8B>）

    alias vi='env LANG=ja_JP.UTF-8 /Applications/MacVim.app/Contents/MacOS/Vim "$@"'
