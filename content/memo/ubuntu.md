---
title: "Ubuntu"
---

# apt

aptでユーザ認証付きのプロキシサーバを使用する場合， /etc/apt/apt.conf
を編集する．
なお，username，password，proxy.addressはそれぞれ適切なものに書き替える．

```
Aciquire::<http::Proxy>
"<http://username:password@proxy.address:8080>" Aciquire::<https::Proxy>
"<http://username:password@proxy.address:8080>" Aciquire::<ftp::Proxy>
"<http://username:password@proxy.address:8080>" Aciquire::socks::Proxy
"<http://username:password@proxy.address:8080>"
```

Parallels Desktopに入れたUbuntu
16.04のaptで使用するサーバがアメリカだったので，日本のサーバに変更
<https://launchpad.net/ubuntu/+archivemirrors>
によると，山形大学の回線が太いので，山形大学のミラーを使用させてもらう．

```
sudo sed -i.bak -e
"s%[http://us.archive.ubuntu.com/ubuntu/%http://linux.yz.yamagata-u.ac.jp/pub/linux/ubuntu-archive/%g](http://us.archive.ubuntu.com/ubuntu/%http://linux.yz.yamagata-u.ac.jp/pub/linux/ubuntu-archive/%g)"
/etc/apt/sources.list
```

# Nvidia GTX-1080

NvidiaのGTX-1080をUbuntuで使う際にハマったのでメモ。

ドライバーを提供しているリポジトリ
(<https://launchpad.net/~graphics-drivers/+archive/ubuntu/ppa>)
を登録するために

```
sudo add-apt-repository ppa:graphics-drivers/ppa
```
としたが、

```
Cannot add PPA: 'ppa:\~graphics-drivers/ubuntu/ppa'. ERROR:
'\~graphics-drivers' user or team does not exist.
```
というエラーが出て追加登録できない。
aptのproxy設定は上述の通り済ませてある。

<https://qiita.com/aKenjiKato/items/18f9b8553f8ad4117d79> にあるように、

> add-apt-repository は apt.confのプロキシ設定を見ていないかもしれない

ので、.bashrcに

```
http_proxy=<http://username:password@proxy.address:8080>
https_proxy=<https://username:password@proxy.address:8080>
ftp_proxy=<ftp://username:password@proxy.address:8080>
```
を記述して、sudoに環境変数を渡す*-E*オプションを付けて，

```
sudo -E add-apt-repository ppa:graphics-drivers/ppa
```
で無事に追加できた。

```
sudo -E add-apt-repository ppa:graphics-drivers/ppa sudo -E
apt-get update sudo -E apt-get install nvidia-384 #2017/10/12現在 sudo
apt-get install mesa-common-dev sudo apt-get install freeglut3-dev
```

## CUDAのインストール

※Cuda toolkit 9 と CuDNN 7 では
Tensorflow（2017/10/18のバージョン）が動かない。

<https://developer.nvidia.com/cuda-toolkit>
からOSなどを選択してcuda_8.0.61_375.26_linux.runをダウンロード（2017/10/12のバージョン）

- CTRL+ALT+F1を押してXを抜ける
- CUI環境になるので、アカウントにloginする
- sudo init 3を実行して非X環境にする（これをしないとXがファイルをロックして失敗する）
- cuda_8.0.61_375.26_linux.run（インストーラ）を実行

```
cd Downloads sudo sh ./cuda_8\*
```

全部Yes、場所は初期設定のまま。終わったら再起動

```
sudo reboot
```

ログインしようとしても失敗してログイン画面に戻されるので、
CTRL+ALT+F1を押してXを抜けCUI環境に行く。
グラフィックボードのドライバを再インストールする。

```
sudo apt-get install nvidia-384 --reinstall #2017/10/12現在
```

再起動して、無事にログインできるようになった。

.bashrcの末尾に以下を記述してパスを通す

```
# CUDA export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export
LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
export CUDA_HOME=/usr/local/cuda
```
他のユーザでも使えるようにパスを通す。

環境変数パス

```
sudo touch /etc/profile.d/cuda.sh sudo vi /etc/profile.d/cuda.sh
```
中身は
```
## set PATH for CUDA
PATH="/usr/local/cuda-8.0/bin${PATH:+:${PATH}}"
```

同様にライブラリの検索パス
```
sudo touch /etc/ld.so.conf.d/cuda.conf
sudo vi /etc/ld.so.conf.d/cuda.conf
```

中身は

```
## CUDA support /usr/local/cuda-8.0/lib64
```

```
sudo ldconfig
```

## cuDNN

<https://developer.nvidia.com/rdp/cudnn-download> から

- cuDNN v6.0 Runtime Library for Ubuntu16.04 (Deb) - cuDNN v6.0
Developer Library for Ubuntu16.04 (Deb) - cuDNN v6.0 Code Samples and
User Guide for Ubuntu16.04 (Deb)
をダウンロードして、以下のコマンドでインストールする。

```
sudo dpkg -i libcudnn6_6.0\*+cuda8.0_amd64.deb sudo dpkg -i
libcudnn6-dev_6.0\*+cuda8.0_amd64.deb sudo dpkg -i
libcudnn6-doc_6.0\*+cuda8.0_amd64.deb
```

# Python環境の構築

pyenvをGitHubのリポジトリをクローンして導入し、

```
git clone <https://github.com/yyuu/pyenv.git> \~/.pyenv pyenv
install --list
```

最新のAnacondaを入れる。2017/10/18現在はanaconda3-5.0.0

```
pyenv install anaconda3-5.0.0
```

設定

```
pyenv global anaconda3-5.0.0 echo 'export
PATH="$PYENV_ROOT/versions/anaconda3-5.0.0/bin:$PATH"' \>\> \~/.bashrc
source \~/.bashrc
```
確認

```
python --version
```
Python 3.6.2 :: Anaconda, Inc.と出ればOK

Ubuntu 16.04
LTSではPython3.5.2までしか対応していないため、anaconda3-5.0.0（Pythonのバージョンは3.6）を単純に入れるだけではpython3.5-devを使うOpenCVのビルド時にPython3対応ができない。
詳しくはOpenCVのところを参照

# Tensorflowのインストール

公式サイトのAnaconda使用の場合を参照

<https://www.tensorflow.org/install/install_linux#InstallingAnaconda>

# OpenCV

<https://qiita.com/mix_dvd/items/4bf7f6e7fa19068ea1d4> を参考にしたが、

```
Built target opencv_cudafilters Makefile:160: ターゲット 'all'
のレシピで失敗しました make: \*\*\* \[all\] エラー 2
```
というエラーが出たので、
<http://answers.opencv.org/question/100907/not-able-to-install-opencv31-in-ubuntu1604-cuda-80/>
を参考に cmakeの引数に\*-D CUDA_ARCH_BIN=6.1\*を追加（GTX-1080なので）

今度は
```
Built target opencv_cvv Makefile:160: ターゲット 'all'
のレシピで失敗しました make: \*\*\* \[all\] エラー 2
```

```
cmake -D CMAKE_BUILD_TYPE=RELEASE \\

        -D CUDA_ARCH_BIN=6.1 \
        -D CUDA_ARCH_PTX=6.1 \
        -D CUDA_FAST_MATH=1 \
        -D WITH_CUBLAS=ON \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D INSTALL_C_EXAMPLES=ON \
        -D BUILD_NEW_PYTHON_SUPPORT=ON \
        -D INSTALL_PYTHON_EXAMPLES=ON \
        -D WITH_TBB=ON \
        -D WITH_V4L=ON \
        -D WITH_QT=ON \
        -D WITH_OPENGL=ON ENABLE_FAST_MATH=1 \
        -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
        -D BUILD_EXAMPLES=ON ..

```

面倒になったので、Anacondaで入れた。Python以外からも使えるのかなどはわからない。

```
conda install -c menpo opencv3
```

<https://qiita.com/PonDad/items/cbef5dca04a1c1a201b0>
によると、自分でビルドしないと動画を取り扱えないとのこと。
諦めて同記事を参考に入れてみる。

が、結局ダメで、 <http://mashup.hatenablog.com/entry/2017/05/16/200000>
にあるとおりにOpenCV3.2でEXTRA_MODULESなしでどうにか入った。

と思ったらpython3からOpenCV3.2が使えず、Anacondaで入れた3.1しか見つからない。
cmakeの結果を見てもUnavailable: python3となっている。

以下は基本的に
<https://qiita.com/Kumapapa2012/items/7f0acaa504de6e87c7f0#conda-%E4%BD%9C%E6%A5%AD%E7%92%B0%E5%A2%83%E3%81%AE%E4%BD%9C%E6%88%90>

に従うが、LD_LIBRARY_PATHの設定は不要

```
echo $LD_LIBRARY_PATH
```
出力は **/usr/local/cuda-8.0/lib64**

python352-MLという名前でPython3.5.2を含むAnaconda環境を作る

```
conda create -n python352-ML python=3.5.2 source activate
python352-ML
```
```
sudo apt install python3.5-dev
```

```
conda install -n python352-ML curl numpy cmake
```

ここからはconda環境から抜ける

```
source deactivate python352-ML
```

EXTRA_MODULESを入れるとコケるので、再び
<http://mashup.hatenablog.com/entry/2017/05/16/200000>
にあるとおりにOpenCV3.2でEXTRA_MODULESなしで以下のcmakeオプションでうまく行った。

```
cmake -D CMAKE_BUILD_TYPE=RELEASE \\

      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D INSTALL_C_EXAMPLES=OFF \
      -D INSTALL_PYTHON_EXAMPLES=ON \
      -D WITH_CUDA=ON \
      -D ENABLE_FAST_MATH=1 \
      -D CUDA_FAST_MATH=1 \
      -D WITH_CUBLAS=1 ..

```

cmakeの結果のPython3関係は以下の通り

```
-- Python 3: -- Interpreter:
/home/haselab/.pyenv/versions/anaconda3-5.0.0/envs/python352-ML/bin/python3
(ver 3.5.2) -- Libraries:
/home/haselab/.pyenv/versions/anaconda3-5.0.0/envs/python352-ML/lib/libpython3.5m.so
(ver 3.5.2) -- numpy:
/home/haselab/.local/lib/python3.5/site-packages/numpy/core/include (ver
1.13.3) -- packages path:
/home/haselab/.pyenv/versions/anaconda3-5.0.0/envs/python352-ML/lib/python3.5/site-packages
-- -- Python (for build):
/home/haselab/.pyenv/versions/anaconda3-5.0.0/envs/python352-ML/bin/python3
```

```
make -j $(nproc) sudo make install
```

確認
```
haselab@g-hasegawa09:\~/opencv/opencv-3.2.0/build$ source
activate python352-ML (python352-ML)
haselab@g-hasegawa09:\~/opencv/opencv-3.2.0/build$ python3 Python 3.5.2
\|Continuum Analytics, Inc.\| (default, Jul 2 2016, 17:53:06) \[GCC
4.4.7 20120313 (Red Hat 4.4.7-1)\] on linux Type "help", "copyright",
"credits" or "license" for more information.

> > > import cv2
> > >
> > > cv2.<u>version</u>

'3.2.0'
```

python352-ML環境を標準で使って欲しいので、.bashrcのPATHの部分を一部書き換える。

```
#export PATH="$PYENV_ROOT/versions/anaconda3-5.0.0/bin:$PATH"
export
PATH="$PYENV_ROOT/versions/anaconda3-5.0.0/envs/python352-ML/bin:$PATH"
```

Jupyter Notebookでpython352-ML環境を使用可能に設定する

現状確認
```
jupyter kernelspec list
```

以下のカーネルが使用可能
```
Available kernels:

    python3    /home/haselab/.pyenv/versions/anaconda3-5.0.0/share/jupyter/kernels/python3

```

python352-MLをアクティベイトして、追加
```
source activate
python352-ML ipython kernel install --user --name=python352-ML
--display-name=python352
```
実行結果
```
Installed kernelspec
python352-ML in /home/haselab/.local/share/jupyter/kernels/python352-ml
```

# R

aptでは古いRが入るので，新しいRを入れる．

<http://qiita.com/JeJeNeNo/items/43fc95c4710c668e86a2> を参考に，

```
echo -e "\\n## For R package" \| sudo tee -a
/etc/apt/sources.list echo "deb
<https://cran.rstudio.com/bin/linux/ubuntu> $(lsb_release -cs)/" \| sudo
tee -a /etc/apt/sources.list
```

gpgコマンドでの公開鍵の取得がプロキシ環境では上手くいかないので，apt-keyコマンドを使う

```
#sudo apt-key adv --keyserver-option
http-proxy=<http://username:password@proxy.address:8080> --keyserver
hkp:*keyserver.ubuntu.com:80 --recv-keys E084DAB9 sudo -E apt-key adv
--keyserver hkp:*keyserver.ubuntu.com:80 --recv-keys E084DAB9 #こっちでいけた
apt-key export E084DAB9 \| sudo apt-key add -
```

```
sudo apt update sudo apt install r-base
```

.Rprofileを作成し，R起動オプションにプロキシを設定

```
touch .Rprofile echo
"Sys.setenv(http_proxy=\\"http:*username:password@proxy.address:8080/\\")"
\| sudo tee -a \~/.Rprofile echo
"Sys.setenv(https_proxy=\\"https:*username:password@proxy.address:8080/\\")"
\| sudo tee -a \~/.Rprofile echo
"Sys.setenv(ftp_proxy=\\"ftp://username:password@proxy.address:8080/\\")"
\| sudo tee -a \~/.Rprofile
```

**\\"http中略8080/\\"**のように，プロキシアドレスをくくる引用符はエスケープしておく必要がある．

Rに追加パッケージをインストール

パッケージ保存先のパーミッションの兼ね合いで，sudoを付けて起動する．

```
sudo -E R
```

パッケージが入るか確認

```
install.packages("RColorBrewer") install.packages("dichromat")
install.packages("viridis")
```

# Bash on Ubuntu on Windows関係

ホームディレクトリを変更

```
sudo vi /etc/passwd
```

```
username:x:1000:1000:"",,,:/home/username:/bin/bash
```
を

```
username:x:1000:1000:"",,,:/mnt/c/Users/username:/bin/bash
```

に変更する．exitする前に，既存ファイルを新しいホームディレクトリに移動する．

```
cd \~ cp -p .\* /mnt/c/Users/username
```

Rでグラフを描画する等，GUIを使うためには，Xmingを使う必要がある．

手順は <http://qiita.com/makky0620/items/e31edc90f22340d791ff> を参照

## NeoVimとNvim-R

<https://github.com/neovim/neovim/wiki/Installing-Neovim>
時間が取れたら考える．
