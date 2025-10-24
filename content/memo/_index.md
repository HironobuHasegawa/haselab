---
title: メモ
weight: 20
---

下の階層を含めて以下のメモはだいぶ古くなっており，長谷川の思い出以上の価値はありません（2021/09/20追記）．


以下に記す内容は，
-   少し気になったことを
-   ちょこっと調べて
-   ろくに精査もしないで適当に書いている

だけのものであり，とりとめのない私的なメモ以上の意味を持ちません．
従いまして，記述している内容には私の勘違い・思い違い・思い込み・希望等が含まれており，その内容について保証は致しかねます．

と堅苦しく書くほどの内容はないのですが，念のため．

------------------------------------------------------------------------
## R

[Rに関する覚え書き](/memo/r)


## Mac OSX 関係

[osx](/memo/osx)

## Ubuntu 関係

[ubuntu](/memo/ubuntu)

## テキスト編集

-   [VIM](/memo/Vim)
-   [awk](/memo/awk)

## 画像ファイル関係

[image](/memo/image)

## GIS関係

-   [QGIS](/memo/QGIS)
-   [GTFS(General Transit Feed Specification)](/memo/GTFS)

## eclipse

[eclipse](/memo/eclipse)

## 調べ物

特許 <http://www6.ipdl.inpit.go.jp/Tokujitu/tjsogodbk.ipdl>

## Office

MSオフィスやLibreOffice，OpenOfficeなど [office](/memo/office)

## 研究室内のPC設定

[研究室内のPC設定](/memo/PcSettings)

### ファイラ

Windows7機をメインマシンに変更するのに伴い，ファイラをMDIEからX-Finderに乗り換えた．
MDIEはかなり長く使っており，特に不満は無かった．
7でも普通に使用できそうだったが，UACがうるさいのとSynergyが上手く動かないという点がストレスで変更を決意．

マウスジェスチャに以下を追加した

|                        |                  |                                      |
|------------------------|------------------|--------------------------------------|
| 最後に閉じたタブを復元 | TF:Shift Closed: | 動作はパスを実行，新規タブにチェック |

## firefox

gmailに自動でBCCを入れるgreasemonkeyスクリプト

作者サイト <http://jaidev.info/home/hacks/gmailAutoBcc>

日本語解説 <http://devilog.net/2010/08/uncategorized-20223424.html>

## Thunderbird

### 本文中にスペースが入る

2013/09/30にMac OSX版のThunderbird24.0で発生を確認

<http://phreeride.blogspot.jp/2012/05/thunderbird.html>
に書いてある通り，「環境設定」→「詳細」→「設定エディタ」からabout:configを呼び出し，editor.htmlWrapColumnを整数値型で新規に作成し，値に0を設定する．
念のため，再起動した．

今のところ上手くいっている．

### 使用しているアドオン

|                                       |                                                        |                                                                                                                                                                                                                                                                                                                             |          |
|---------------------------------------|--------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| 名称                                  | 機能                                                   | URL                                                                                                                                                                                                                                                                                                                         | コメント |
| Auto Address Cleaner                  | 宛先メールアドレスから表示名を取り除く                 | [https://addons.mozilla.org/ja/thunderbird/addon/7148/\|表示名を「Gonbe](https://addons.mozilla.org/ja/thunderbird/addon/7148/%7C表示名を「Gonbe) Nanashi」みたいに設定している人への返信時に，呼び捨ては失礼だからアドレス帳にわざわざ「○×社 名無権兵衛 様」みたいに登録する文化，についていけなくなってきた時に使うと便利 |          |
| Address Close Button                  | メール作成時に送信先を削除するボタンを追加             | <https://addons.mozilla.org/de/thunderbird/addon/11604/>                                                                                                                                                                                                                                                                    |          |
| CorrectLink                           | メールに記述されたリンクを修正                         | <https://addons.mozilla.org/ja/thunderbird/addon/106452/>                                                                                                                                                                                                                                                                   |          |
| Attachment Sizes                      | 添付ファイルのサイズを表示                             | <https://addons.mozilla.org/de/thunderbird/addon/75780/>                                                                                                                                                                                                                                                                    |          |
| ConfigDate                            | 日付表示形式をカスタマイズ                             | <https://addons.mozilla.org/de/thunderbird/addon/901/>                                                                                                                                                                                                                                                                      |          |
| MinimizeToTray Plus                   | ウィンドウをシステムトレイに最小化する                 | <https://addons.mozilla.org/de/thunderbird/addon/2831/>                                                                                                                                                                                                                                                                     |          |
| Extra Folder Columns                  | フォルダペインにカラムを追加                           | [https://addons.mozilla.org/de/thunderbird/addon/9716/\|フォルダ毎のサイズとメール数を表示している](https://addons.mozilla.org/de/thunderbird/addon/9716/%7Cフォルダ毎のサイズとメール数を表示している)                                                                                                                     |          |
| QuickFolders                          | 任意のフォルダをブックマークツールバーのように表示する | [https://addons.mozilla.org/de/thunderbird/addon/3254/\|メール整理派には欠かせないが，アーカイブ＆検索派には不要かも](https://addons.mozilla.org/de/thunderbird/addon/3254/%7Cメール整理派には欠かせないが，アーカイブ＆検索派には不要かも)                                                                                 |          |
| Quote Colors                          | 引用文の表示をカスタマイズ                             | <https://addons.mozilla.org/de/thunderbird/addon/170/>                                                                                                                                                                                                                                                                      |          |
| Display Mail User Agent               | ヘッダに送信者の使用メーラーを表示                     | [https://addons.mozilla.org/ja/thunderbird/addon/562/\|官公庁のLotus](https://addons.mozilla.org/ja/thunderbird/addon/562/%7C官公庁のLotus) Notes率はすごい．情報統制に気を使っているんだろうな．それに比べてアカデミック（の事務も含めて）の自由さはラクだけど，危うさも感じる．自戒を込めて．                             |          |
| Country Lookup                        | ヘッダに発信国を表示                                   | <https://addons.mozilla.org/ja/thunderbird/addon/3595/>                                                                                                                                                                                                                                                                     |          |
| Remove Duplicate Messages (Alternate) | 重複メールを削除                                       | <https://addons.mozilla.org/en-US/thunderbird/addon/4654/>                                                                                                                                                                                                                                                                  |          |
| Duplicate Contact Manager             | アドレス帳内の重複を整理                               | <https://addons.mozilla.org/ja/thunderbird/addon/4631/>                                                                                                                                                                                                                                                                     |          |
| Signature Switch                      | 署名の有無を切り替えるボタン追加                       | <https://addons.mozilla.org/ja/thunderbird/addon/611/>                                                                                                                                                                                                                                                                      |          |

LightningとかProvider for Google
Calendarのように入れてはいるけど使っていないものは除いて，必須のものだけまとめた．
シングルディスプレイ環境だとまた少し違ってくるんだろう．

GMailUIが復活すると嬉しいなー

Ruler Barもhttp://piro.sakura.ne.jp/xul/\_rulerbar.html

## Google Chorome

Gmailで自動的にBccに自分のアドレスを入れる．
<http://blog.0stage.jp/article/179978968.html>

## 研究室内のネットワーク設定

[研究室内のネットワーク設定](/memo/NWSettings)

## ワークステーションの設定

[CentOS5.4_64bit](/memo/CentOS)

## CUDA

[CUDA](/memo/CUDA)

## Python

easy_installがproxy関連で失敗するときは，

`set http_proxy=`

で一旦proxy設定をリセットすると，上手くいくことがある．

## Trac Lightning

### 新規プロジェクトの作成とTortoiseSVNの連携

1.  「スタート->全てのプログラム->Trac->コマンドプロンプト」を実行
2.  コマンドプロンプトで「create-project ProjectName」を入力・実行
3.  適当な場所に作業フォルダを作成し，フォルダを右クリックして「SVNチェックアウト」を実行
4.  リポジトリのURLを`file:/ / / C:/TracLight/projects/svn/ProjectName`にして「OK」をクリック（Wikiの表示が崩れるため，スラッシュの後ろにスペースを入れている）
5.  もう一度作業フォルダを右クリックして「SVNコミット」を実行
6.  以後，作業フォルダ以下のファイル・フォルダを，右クリック「TortoiseSVN->追加」でバージョン管理下に，「TortoiseSVN->無視リストに追加」でバージョン管理外に設定できる．管理下に設定後は作業フォルダに対するコミットだけでOK

## MySQL

### パスを通す

コマンドプロンプトからMySQLを起動するとき，一々フルパスを打つのは面倒なので，環境変数Pathを通しておく．

インストール後，システム環境変数Pathに以下を追加（xamppでインストールした場合）

    c:\xampp\mysql\bin

既にPathがある場合，「;」で区切る

Windows7 64bitにインストーラを使ってMySQL
Server5.5をインストールした場合は

    C:\Program Files\MySQL\MySQL Server 5.5\bin

いずれにしても，実行ファイルの入っている場所にパスを通す（2011/11/10追記）

### rootパスワードを設定

（MySQL）サービスを起動させた後，コマンドプロンプトからrootパスワードを設定

    mysqladmin -u root password

Windows7 64bitにインストーラを使ってMySQL
Server5.5をインストールした場合，インストールの段階でルートのパスワードを設定するようになっている．

### エラーメッセージへの対応

|                                                        |                                                               |                                                               |
|--------------------------------------------------------|---------------------------------------------------------------|---------------------------------------------------------------|
| 状況                                                   | エラーメッセージ                                              | 対応                                                          |
| `LOAD DATA INFILE`でデータをインポートしようとしたとき | ERROR 1265 (01000): Data truncated for column '列名' at row 1 | LINES節に改行コードを指定するorファイルの改行コードを変更する |

### phpMyadmin

phpMyAdminと連携させるために

    C:\xampp\phpMyAdmin\config.inc.php

の

    /* Authentication type and info */
    $cfg['Servers'][$i]['auth_type'] = 'config';
    $cfg['Servers'][$i]['user'] = 'root';
    $cfg['Servers'][$i]['password'] = '';
    $cfg['Servers'][$i]['AllowNoPasswordRoot'] = true;

でmysqlのルートパスワードを設定する．

## LaTeX

[LaTeX](/memo/latex)

## Cygwin

### 初期設定

#### bash内でlsを使った時に日本語ファイル名が文字化けするときの対処（ついでに色分けも）

以下を「\~/.bashrc」に記述し，保存する．なお，「\~/」はホームディレクトリを表し，標準インストールなら「C:\\cygwin\\home\\ユーザ名\\」となる．

    alias ls='ls --show-control-chars --color=auto' 

「\~/.bash_profile」がないと「.bashrc」に記した設定が読み込まれない．その場合は「C:\\cygwin\\etc\\skel\\.bash_profile」をホームディレクトリにコピーすればよい．
以下のエイリアスも有効にしておくとハッピー

    alias ll='ls -l' #1ファイル，1ディレクトリを一行毎に詳細に表示
    alias la='ls -A' #隠しファイルも表示

#### bashでの日本語入力

<http://pinoki.la.coocan.jp/wiki/?Cygwin%2FInstall%2F%C6%FC%CB%DC%B8%EC%B2%BD>
を参考にした

「\~/ .inputrc」に以下を追加

    set kanji-code sjis
    set convert-meta off
    set meta-flag on
    set output-meta on 

Poderosaからだと普通にATOKのon/off設定が使えた．

## フォルダ結合

[Combine](http://hp.vector.co.jp/authors/VA032597/Software/index.html)というソフトを使うと，複数の
フォルダを1つにまとめてくれる．

## dokuwiki

<http://www.cmsresume.com/dokuwiki/tips/searchresult>
<http://www.cmsresume.com/dokuwiki/plugin/editor>
<http://www.cmsresume.com/dokuwiki/plugin/orphanswanted>
<http://www.cmsresume.com/dokuwiki/plugin/tagentry> を後で試す

### plugin

#### blog

ブログプラグインと↓のディスカッションプラグインを使うと，各エントリにコメントを付けられるようになる．

#### Poll Plugin

アンケート機能を追加するプラグイン <http://www.dokuwiki.org/plugin:poll>
