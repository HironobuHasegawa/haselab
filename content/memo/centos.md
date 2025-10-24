---
title: "CentOS"
tags: ["linux"]
---

**CentOS 5.4 64bit**

# インストール

メディアチェック後，インストール開始

「システムクロックでUTCを使用」のチェックは外した

追加リポジトリにチェックを入れると固まるので，入れない．

NTPサーバ名: ntp.nict.jp

インストール完了後再起動でとりあえずOK

# 諸設定

## proxy設定

システム→設定→ネットワークのプロキシ で設定可能

## ユーザhaseがsudo出来るように設定

bashからsuでルートにログイン

    /usr/sbin/visudo

でsudo設定ファイルにアクセス viなので，「i」キーで編集開始

    ## Allow root to run any commands anywhere
    root     ALL=(ALL)     ALL

の下に

    hase     ALL=(ALL)     ALL

を追記 エスケープキーを押して編集モードから抜ける

    :wq

で変更を保存して閉じる

## キーボード入力関連の設定

### CapsLockキーと左Ctrlキーを入れ替え

システム→設定→キーボード→レイアウトのオプション Ctrl key positionでSwap
Ctrl and CapsLock を選択し閉じる

### 日本語入力切り替え設定

言語バーを右クリックして，「SCIMを設定」を選択する。

フロントエンド→全体設定→オプション

-   キーボード配列を日本語に設定．
-   ホットキーの開始に「Henkan」キーを，終了に「Muhenkan」キーを設定

SCIMの再起動を促されるが，コマンドが分からなかったので，マシンを再起動した．

## sambaの設定

システム→管理→セキュリティレベルとファイアーウォールの設定でsambaにチェックを入れる．

ユーザ（既存）を追加

    pdbedit -a hase

sambaのパスワードを設定

    [hase@hasegawa02 ~]$ smbpasswd
    Old SMB password:
    New SMB password:
    Retype new SMB password:
    Password changed for user hase

## CUDA環境の構築

### ドライバのインストール

ドライバインストールには，kernel-develが必要なので，

    sudo yum install kernel-devel

で入れておく．

<http://jp.download.nvidia.com/XFree86/Linux-x86_64/195.36.15/NVIDIA-Linux-x86_64-195.36.15-pkg2.run>
をダウンロードし、実行可能にする。

    sudo /sbin/init 3

でXを切り、改めてルートでログインする。

    cd DownloadDir
    sh ./NVIDIA-Linux-x86_64-195.36.15-pkg2.run

yesばっかり選べばよい． 終わったら再起動し、

    reboot now

Xを使う設定に戻す。

    /sbin/init 5

元のユーザでログインし、

    nvidia-settings

でNVIDIAの管理面画が出てくることを確認する。

### ツールキットのインストール

<http://developer.download.nvidia.com/compute/cuda/2_3/toolkit/cudatoolkit_2.3_linux_64_rhel5.3.run>
をダウンロードし、実行可能にする。

    sh cudatoolkit_2.3_linux_64_rhel5.3.run

インストールディレクトリが見付からない、作成できないと怒られるので、sudoで実行

    sodo sh cudatoolkit_2.3_linux_64_rhel5.3.run

経過省略

-   Please make sure your PATH includes /usr/local/cuda/bin
-   Please make sure your LD_LIBRARY_PATH
-    for 32-bit Linux distributions includes /usr/local/cuda/lib
-    for 64-bit Linux distributions includes /usr/local/cuda/lib64
-   OR
-    for 32-bit Linux distributions add /usr/local/cuda/lib
-    for 64-bit Linux distributions add /usr/local/cuda/lib64
-   to /etc/ld.so.conf and run ldconfig as root

<!-- -->

-   Please read the release notes in /usr/local/cuda/doc/

<!-- -->

-   To uninstall CUDA, delete /usr/local/cuda
-   Installation Complete

パスを作れと言われている。現在のパスを確認

    echo $PATH

~~パスを追加する。 vi \~/.bash_profile
のパスの部分を以下のように書き換えた。
PATH=$PATH:$HOME/bin:/usr/local/cuda/bin:/usr/local/cuda/lib64
保存後、変更を適用する source .bash_profile 確認 echo $PATH
/usr/kerberos/bin:/usr/local/bin:/usr/bin:/bin:/usr/X11R6/bin:/usr/java/jdk1.6.0_19/bin:/home/hase/bin:/home/hase/bin:/usr/local/cuda/bin:/usr/local/cuda/lib64
~~

\<del>2010/04/19修正：
PATHへのパス（ややこしいな）はコマンド検索のために使用するので，

    /usr/local/cuda/bin

を追加する．

LD_LIBRARY_PATHへのパスはdll（ダイナミックリンク・ライブラリ）を使うために

    /usr/local/cuda/lib64

を追加する．

結局，

    vi ~/.bash_profile

の

    # User specific environment and startup programs

以下を PATH=$PATH:$HOME/bin:/usr/local/cuda/bin:/sbin:/user/sbin
LD_LIBRARY_PATH=/usr/local/cuda/lib64

export PATH export LD_LIBRARY_PATH に書き換えれば良い感じ． \</del>
2010/04/19再修正：
\~/.bash_profileへの記入だと，そのユーザがログインした時にしか読み込まないので，
複数ユーザで使いたい場合，それぞれの\~/.bash_profileに書き込む必要がある
（ついでに書いておくと，\~/.bashrcだとシェル起動毎に読み込まれる）．
今後，学生も含めて何人かが使用することになりそうなので，以下の内容を
/etc/profileの末尾に書き込んでおく（root権限が必要）．

    # For CUDA(2010/04/19)
    export LD_LIBRARY_PATH=/usr/local/cuda/lib64
    export PATH=$PATH:/usr/local/cuda/bin
    export MANPATH=$MANPATH:/usr/local/cuda/man

上から順に，dll（ダイナミックリンク・ライブラリ）へのパス，コマンド検索パス，
マニュアルのパスだそーです（青木・額田：はじめてのCUDAプログラミング，p.220より）．

    source /etc/profile

で設定内容を適用．

bashの環境変数の設定についてはhttp://www.atmarkit.co.jp/flinux/rensai/theory09/theory09b.htmlが分かりやすい．

### SDKのインストール

<http://developer.download.nvidia.com/compute/cuda/2_3/sdk/cudasdk_2.3_linux.run>
をダウンロードし，一般ユーザでインストール（shコマンド）する．
インストール先を聞かれるが，デフォルト

    ~/NVIDIA_GPU_Computing_SDK

で良いならEnterキーを押せばよい．

インストール完了後，コンパイルする必要がある。

    [hase@hasegawa02 ~]$ cd ~/NVIDIA_GPU_Computing_SDK/C
    [hase@hasegawa02 C]$ make

したところ、

    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' から出ます
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' に入ります
    ./../common/inc/paramgl.h:24 から include されたファイル中,
                     src/paramgl.cpp:19 から:
    ./../common/inc/GL/glut.h:60:20: error: GL/glu.h: そのようなファイルやディレクトリはありません
    make[1]: *** [obj/release/paramgl.cpp.o] エラー 1
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' から出ます
    make: *** [lib/libparamgl.so] エラー 2

と言われて、コンパイルできない。
/home/hase/NVIDIA_GPU_Computing_SDK/C/common/inc/paramgl.hの24行目でインクルードしようとしている
glu.hというファイルが無い！と怒られているので， glu.hで検索してみる．
nvidiaのフォーラムhttp://forums.nvidia.com/lofiversion/index.php?t30464.htmlに
似たよーなエラーが出ている人がいた。freeglutが必要そうなので、

    sudo yum install freeglut freeglut-devel

でインストールする。 もう一度makeすると、

    [hase@hasegawa02 C]$ make
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' から出ます
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' に入ります
    a - obj/release/paramgl.cpp.o
    a - obj/release/param.cpp.o
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' から出ます
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' に入ります
    src/rendercheck_gl.cpp:54: 警告: unused parameter ‘bQAReadback’
    src/rendercheck_gl.cpp:54: 警告: unused parameter ‘bUseFBO’
    src/rendercheck_gl.cpp: In member function ‘virtual bool CheckRender::PGMvsPGM(const char*, const char*, float, float)’:
    src/rendercheck_gl.cpp:144: 警告: フォーマットへの引数が多すぎます
    src/rendercheck_gl.cpp: In member function ‘virtual bool CheckRender::PPMvsPPM(const char*, const char*, float, float)’:
    src/rendercheck_gl.cpp:190: 警告: フォーマットへの引数が多すぎます
    src/rendercheck_gl.cpp:183: 警告: unused variable ‘src_data’
    src/rendercheck_gl.cpp:183: 警告: unused variable ‘ref_data’
    src/rendercheck_gl.cpp: In member function ‘virtual bool CheckRender::compareBin2BinUint(const char*, const char*, unsigned int, float, float)’:
    src/rendercheck_gl.cpp:226: 警告: フォーマットへの引数が少なすぎます
    src/rendercheck_gl.cpp:228: 警告: フォーマットへの引数が多すぎます
    src/rendercheck_gl.cpp: In member function ‘virtual bool CheckRender::compareBin2BinFloat(const char*, const char*, unsigned int, float, float)’:
    src/rendercheck_gl.cpp:288: 警告: フォーマットへの引数が少なすぎます
    src/rendercheck_gl.cpp:290: 警告: フォーマットへの引数が多すぎます
    src/rendercheck_gl.cpp: At global scope:
    src/rendercheck_gl.cpp:518: 警告: unused parameter ‘Bpp’
    src/rendercheck_gl.cpp:545: 警告: unused parameter ‘width’
    src/rendercheck_gl.cpp:545: 警告: unused parameter ‘height’
    src/rendercheck_gl.cpp:545: 警告: unused parameter ‘Bpp’
    src/rendercheck_gl.cpp:560: 警告: unused parameter ‘width’
    src/rendercheck_gl.cpp:560: 警告: unused parameter ‘height’
    src/rendercheck_gl.cpp:560: 警告: unused parameter ‘Bpp’
    src/rendercheck_gl.cpp:702: 警告: unused parameter ‘width’
    src/rendercheck_gl.cpp:702: 警告: unused parameter ‘height’
    a - obj/release/rendercheck_gl.cpp.o
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' から出ます
    make -C src/clock/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/clock' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/clock' から出ます
    make -C src/simpleCUFFT/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/simpleCUFFT' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/simpleCUFFT' から出ます
    make -C src/matrixMul/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/matrixMul' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/matrixMul' から出ます
    make -C src/fastWalshTransform/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/fastWalshTransform' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/fastWalshTransform' から出ます
    make -C src/simpleAtomicIntrinsics/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/simpleAtomicIntrinsics' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/simpleAtomicIntrinsics' から出ます
    make -C src/dxtc/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/dxtc' に入ります
    ./dxtc.cu(101): Advisory: Loop was not unrolled, unexpected control flow construct
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/dxtc' から出ます
    make -C src/marchingCubes/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/marchingCubes' に入ります
    marchingCubes.cpp: In function ‘void runAutoTest(int, char**)’:
    marchingCubes.cpp:327: 警告: format ‘%d’ expects type ‘int’, but argument 2 has type ‘long unsigned int’
    /usr/bin/ld: cannot find -lXi
    collect2: ld はステータス 1 で終了しました
    make[1]: *** [../../bin/linux/release/marchingCubes] エラー 1
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/marchingCubes' から出ます
    make: *** [src/marchingCubes/Makefile.ph_build] エラー 2

って出た。

その後，何の気無しにPoderosaから

    [hase@hasegawa02 C]$ make

したら，

    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' から出ます
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' から出ます
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/common' から出ます
    make -C src/clock/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/clock' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/clock' から出ます
    make -C src/simpleCUFFT/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/simpleCUFFT' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/simpleCUFFT' から出ます
    make -C src/matrixMul/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/matrixMul' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/matrixMul' から出ます
    make -C src/fastWalshTransform/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/fastWalshTransform' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/fastWalshTransform' から出ます
    make -C src/simpleAtomicIntrinsics/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/simpleAtomicIntrinsics' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/simpleAtomicIntrinsics' から出ます
    make -C src/dxtc/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/dxtc' に入ります
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/dxtc' から出ます
    make -C src/marchingCubes/ 
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/marchingCubes' に入ります
    /usr/bin/ld: cannot find -lXi
    collect2: ld はステータス 1 で終了しました
    make[1]: *** [../../bin/linux/release/marchingCubes] エラー 1
    make[1]: ディレクトリ `/home/hase/NVIDIA_GPU_Computing_SDK/C/src/marchingCubes' から出ます
    make: *** [src/marchingCubes/Makefile.ph_build] エラー 2

って感じで，marchingCubesまではコンパイル出来た．何で？VNC経由だと何か違うの？？と思ったけど，
まあいいや．んで，

    cannot find -lXi

でググったらhttp://www.atmarkit.co.jp/flinux/rensai/linuxtips/a115makeerror.htmlを見つけた．
libXiというパッケージを入れればいいらしい．

    rpm -qa | grep libXi

でシステムにlibXiパッケージが入っているかを確認したら，入っていなかった．

    [hase@hasegawa02 marchingCubes]$ yum search libXi

で関係するパッケージを探すと，

    Loaded plugins: fastestmirror
    ================================ Matched: libXi =================================
    libXi.i386 : X.Org X11 libXi ランタイムライブラリ
    libXi.x86_64 : X.Org X11 libXi ランタイムライブラリ
    libXi-devel.i386 : X.Org X11 libXi 開発パッケージ
    libXi-devel.x86_64 : X.Org X11 libXi 開発パッケージ
    libXinerama.i386 : X.Org X11 libXinerama ランタイムライブラリ
    libXinerama.x86_64 : X.Org X11 libXinerama ランタイムライブラリ
    libXinerama-devel.i386 : X.Org X11 libXinerama 開発パッケージ
    libXinerama-devel.x86_64 : X.Org X11 libXinerama 開発パッケージ

とあるらしい．

    [hase@hasegawa02 marchingCubes]$ sudo yum install libXi libXi-devel

さっきこけたmarchingCubesディレクトリに入ってmake

    [hase@hasegawa02 marchingCubes]$ make
    /usr/bin/ld: cannot find -lXmu
    collect2: ld はステータス 1 で終了しました
    make: *** [../../bin/linux/release/marchingCubes] エラー 1

こんどはXmu関連のパッケージ「libXmu」が必要らしい．

    [hase@hasegawa02 marchingCubes]$ sudo yum install libXmu libXmu-devel

再度makeしたら無事にコンパイル出来た．良かった良かった．

改めて

    cd ~/NVIDIA_GPU_Computing_SDK/C

に入ってmakeすると，たくさんの警告を蹴散らしながら雄々しくコンパイルが進んでいく．
そして最後に，

    Finished building all

と表示され，無事（？）コンパイルが完了した．良かった良かった．
実行ファイルたちは，

    ~/NVIDIA_GPU_Computing_SDK/C/bin/linux/release

に入っている．

    [hase@hasegawa02 release]$ ls
    3dfd                  dxtc                  simpleAtomicIntrinsics
    BlackScholes          eigenvalues           simpleCUBLAS
    Mandelbrot            fastWalshTransform    simpleCUFFT
    MersenneTwister       fluidsGL              simpleGL
    MonteCarlo            histogram             simpleMultiGPU
    MonteCarloMultiGPU    imageDenoising        simplePitchLinearTexture
    SobelFilter           lineOfSight           simpleStreams
    SobolQRNG             marchingCubes         simpleTemplates
    alignedTypes          matrixMul             simpleTexture
    asyncAPI              matrixMulDrv          simpleTexture3D
    bandwidthTest         matrixMulDynlinkJIT   simpleTextureDrv
    bicubicTexture        nbody                 simpleVoteIntrinsics
    binomialOptions       oceanFFT              simpleZeroCopy
    boxFilter             particles             smokeParticles
    clock                 postProcessGL         sortingNetworks
    convolutionFFT2D      ptxjit                template
    convolutionSeparable  quasirandomGenerator  threadFenceReduction
    convolutionTexture    radixSort             threadMigration
    cppIntegration        recursiveGaussian     transpose
    dct8x8                reduction             transposeNew
    deviceQuery           scalarProd            volumeRender
    deviceQueryDrv        scan
    dwtHaar1D             scanLargeArray

壮観だ．

さっそく，deviceQueryを使ってGPU情報を取得しようとしたところ，

    [hase@hasegawa02 release]$ ~/NVIDIA_GPU_Computing_SDK/C/bin/linux/release/deviceQuery
    /home/hase/NVIDIA_GPU_Computing_SDK/C/bin/linux/release/deviceQuery: error while loading shared libraries: libcudart.so.2: cannot open shared object file: No such file or directory

と怒られた．[http://d.hatena.ne.jp/interleave/20090520/1242750937を参考にして](http://d.hatena.ne.jp/interleave/20090520/1242750937を参考にして)，

    [hase@hasegawa02 release]$ cd /etc/ld.so.conf.d/
    [hase@hasegawa02 ld.so.conf.d]$ ls
    R-x86_64.conf  qt-x86_64.conf
    [hase@hasegawa02 ld.so.conf.d]$ sudo vi cuda.conf

を

    /usr/local/cuda/lib64

の内容で作成し，

    [hase@hasegawa02 ld.so.conf.d]$ sudo ldconfig

でshared libraryのキャッシュを再構築

    [hase@hasegawa02 ld.so.conf.d]$ ~/NVIDIA_GPU_Computing_SDK/C/bin/linux/release/deviceQuery
    CUDA Device Query (Runtime API) version (CUDART static linking)
    There are 2 devices supporting CUDA

    Device 0: "Tesla C1060"
    CUDA Driver Version:                           3.0
    CUDA Runtime Version:                          2.30
    CUDA Capability Major revision number:         1
    CUDA Capability Minor revision number:         3
    Total amount of global memory:                 4294770688 bytes
    Number of multiprocessors:                     30
    Number of cores:                               240
    Total amount of constant memory:               65536 bytes
    Total amount of shared memory per block:       16384 bytes
    Total number of registers available per block: 16384
    Warp size:                                     32
    Maximum number of threads per block:           512
    Maximum sizes of each dimension of a block:    512 x 512 x 64
    Maximum sizes of each dimension of a grid:     65535 x 65535 x 1
    Maximum memory pitch:                          2147483647 bytes
    Texture alignment:                             256 bytes
    Clock rate:                                    1.30 GHz
    Concurrent copy and execution:                 Yes
    Run time limit on kernels:                     No
    Integrated:                                    No
    Support host page-locked memory mapping:       Yes
    Compute mode:                                  Default (multiple host threads can use this device simultaneously)

    Device 1: "GeForce GTS 250"
    CUDA Driver Version:                           3.0
    CUDA Runtime Version:                          2.30
    CUDA Capability Major revision number:         1
    CUDA Capability Minor revision number:         1
    Total amount of global memory:                 1073020928 bytes
    Number of multiprocessors:                     16
    Number of cores:                               128
    Total amount of constant memory:               65536 bytes
    Total amount of shared memory per block:       16384 bytes
    Total number of registers available per block: 8192
    Warp size:                                     32
    Maximum number of threads per block:           512
    Maximum sizes of each dimension of a block:    512 x 512 x 64
    Maximum sizes of each dimension of a grid:     65535 x 65535 x 1
    Maximum memory pitch:                          2147483647 bytes
    Texture alignment:                             256 bytes
    Clock rate:                                    1.84 GHz
    Concurrent copy and execution:                 Yes
    Run time limit on kernels:                     Yes
    Integrated:                                    No
    Support host page-locked memory mapping:       No
    Compute mode:                                  Default (multiple host threads can use this device simultaneously)

    Test PASSED

    Press ENTER to exit...

無事に動いた．良かった良かった．
でも，LD_LIBRARY_PATHで指定してあるはずなのにな．．．と思ってbashから

    echo $LD_LIBRARY_PATH

したら，パスが通っていない．一瞬何故？と思ったけど，多分，起動させっぱなしのシェルだったから
/etc/profileへの設定が反映されていなかったんだろう．

    [hase@hasegawa02 ~]$ source /etc/profile
    [hase@hasegawa02 ~]$ echo $LD_LIBRARY_PATH
    /usr/local/cuda/lib64

さっき作ったcuda.confを削除して、再度確認

    [hase@hasegawa02 ~]$ cd /etc/ld.so.conf.d/
    [hase@hasegawa02 ld.so.conf.d]$ ls
    R-x86_64.conf  cuda.conf  qt-x86_64.conf
    [hase@hasegawa02 ld.so.conf.d]$ sudo rm -f cuda.conf 
    パスワード:
    [hase@hasegawa02 ld.so.conf.d]$ sudo ldconfig
    [hase@hasegawa02 ld.so.conf.d]$ ~/NVIDIA_GPU_Computing_SDK/C/bin/linux/release/deviceQuery
    CUDA Device Query (Runtime API) version (CUDART static linking)
    There are 2 devices supporting CUDA

きちんと動いている。よかったよかった。

### 後で読む

<http://tekitobibouroku.blog42.fc2.com/blog-entry-175.html>

## SSHでリモート接続

<http://centossrv.com/poderosa-public.shtml>
の通りにやれば大丈夫．ただし，面倒なのでOpenSSHでなく，デフォルトインストールのSSHサーバプログラムを使っている．

## C++開発環境

JAVA環境のセッティング
参考サイト：<http://d.hatena.ne.jp/rdera/20090304/1236144748>

jdk-6u19-linux-x64-rpm.binをダウンロードし，

    chmod -x jdk-6u19-linux-x64-rpm.bin

で権限を与えて実行する．

JAVA関連の環境変数の指定 /etc/profileに以下を追記

    # For JAVA(2010/03/31)
    export JAVA_HOME=/usr/java/jdk1.6.0_19
    export PATH=$PATH:$JAVA_HOME/bin
    export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar

Eclipse3.4を使いたいが，RPMパッケージが提供されていないので，
tarボールを持ってきて解凍する．

公式サイトから eclipse-cpp-ganymede-SR2-linux-gtk.tar.gz
をダウンロードして解凍するとeclipseフォルダが出来るので，これを
/usr/local/eclipseに突っ込む．これだけでOK．

日本語化はPleiadesを使う．
pleiades-1.3.0.zipを/usr/local/eclipse/eclipse の中に展開する．
readme/eclipse.ini_sample.win.3.4をeclipse.iniに名前変更し，以下を修正。

    ''-javaagent:/usr/local/eclipse/eclipse/plugins/jp.sourceforge.mergedoc.pleiades/pleiades.jar''

ついでに使用するメモリも増やしておく

参考サイト：<http://www.02.246.ne.jp/~torutk/cxx/eclipse/memo.html#SEC10>

eclipseの起動

    /usr/local/eclipse/eclipse/eclipse -vm /usr/java/jdk1.6.0_19/bin/java &

いちいちこんなの打っていられないので，aliasを追加する．

aliasの追加

    ~/.bashrc 

に

    alias eclipse='/usr/local/eclipse/eclipse/eclipse -vm /usr/java/jdk1.6.0_19/bin/java &'

を追記する．
これでコマンドラインからeclipseと入力すれば起動するようになる。

無事に日本語化されていて嬉しい。

早速新規プロジェクトを作成するが以下のエラーが出てコンパイルできない。

    g++: command not found

ので、

    yum install gcc-c++

でインストールする。

参考：<http://d.hatena.ne.jp/plasticscafe/20080930/1222778825>

    !!!Hello World!!!

今更だが，実際にプログラム書くのは普段使っているマシンだから，
ワークステーションにIDEは要らないような気がする（2010/04/13追記）

## Rのインストール

cranからRPMを取ってくる．R-2.10.0-2.el5.x86_64.rpm

    [hase@hasegawa02 Desktop]$ rpm -ivh R-2.10.0-2.el5.x86_64.rpm 
    警告: R-2.10.0-2.el5.x86_64.rpm: ヘッダ V3 DSA signature: NOKEY, key ID 97d3544e
    エラー: 依存性の欠如:
          R-devel = 2.10.0-2.el5 は R-2.10.0-2.el5.x86_64 に必要とされています
          libRmath-devel = 2.10.0-2.el5 は R-2.10.0-2.el5.x86_64 に必要とされています

R-develとlibRmath-develが必要と怒られたので，とりあえずcranから
R-devel-2.10.0-2.el5.x86_64.rpm を取ってくる．

    [hase@hasegawa02 Desktop]$ rpm -ivh R-devel-2.10.0-2.el5.x86_64.rpm 
    警告: R-devel-2.10.0-2.el5.x86_64.rpm: ヘッダ V3 DSA signature: NOKEY, key ID 97d3544e
    エラー: 依存性の欠如:
          R-core = 2.10.0-2.el5 は R-devel-2.10.0-2.el5.x86_64 に必要とされています
          bzip2-devel は R-devel-2.10.0-2.el5.x86_64 に必要とされています
          gcc-gfortran は R-devel-2.10.0-2.el5.x86_64 に必要とされています
          libX11-devel は R-devel-2.10.0-2.el5.x86_64 に必要とされています
          pcre-devel は R-devel-2.10.0-2.el5.x86_64 に必要とされています
          tcl-devel は R-devel-2.10.0-2.el5.x86_64 に必要とされています
          tetex-latex は R-devel-2.10.0-2.el5.x86_64 に必要とされています
          tk-devel は R-devel-2.10.0-2.el5.x86_64 に必要とされています
          zlib-devel は R-devel-2.10.0-2.el5.x86_64 に必要とされています

色々足りないみたいなので，一つ一つ解決していく．

    [hase@hasegawa02 Desktop]$ sudo yum install bzip2-devel
    パスワード:
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
     * addons: ftp.oss.eznetsols.org
     * base: ftp.oss.eznetsols.org
     * extras: ftp.oss.eznetsols.org
     * updates: ftp.oss.eznetsols.org
    Setting up Install Process
    Resolving Dependencies
    --> Running transaction check
    ---> Package bzip2-devel.i386 0:1.0.3-4.el5_2 set to be updated
    ---> Package bzip2-devel.x86_64 0:1.0.3-4.el5_2 set to be updated
    --> Finished Dependency Resolution

    Dependencies Resolved

    ======================================================================================
     Package               Arch             Version                  Repository      Size
    ======================================================================================
    Installing:
     bzip2-devel           i386             1.0.3-4.el5_2            base            38 k
     bzip2-devel           x86_64           1.0.3-4.el5_2            base            38 k  

中略

    Complete!

他のパッケージも同様にインストールする．

R-coreはデフォルトのyumリポジトリにないと思ったから，cranから取ってくる．

    wget ftp://ftp.u-aizu.ac.jp/pub/lang/R/CRAN/bin/linux/redhat/el5/x86_64/R-core-2.10.0-2.el5.x86_64.rpm

    [hase@hasegawa02 Desktop]$ rpm -ivh R-core-2.10.0-2.el5.x86_64.rpm 
    警告: R-core-2.10.0-2.el5.x86_64.rpm: ヘッダ V3 DSA signature: NOKEY, key ID 97d3544e
    エラー: 依存性の欠如:
          perl(File::Copy::Recursive) は R-core-2.10.0-2.el5.x86_64 に必要とされています
          xdg-utils は R-core-2.10.0-2.el5.x86_64 に必要とされています

また怒られた． perl(<File::Copy>::Recursive)
って言うくらいだから，perl入れればいいのかと思ったらそうではなくって，
perl-File-Copy-Recursiveというrpmパッケージになっているのを入れればよいらしい．

    yum install perl-File-Copy-Recursive

でインストールできない場合は，「 perl-File-Copy-Recursive rpm centos
el5」とかでググってrpmを 探せばいいと思う．

再度

    [hase@hasegawa02 Desktop]$ sudo rpm -ivh R-core-2.10.0-2.el5.x86_64.rpm 

でインストール出来た．

    [hase@hasegawa02 Desktop]$ sudo rpm -ivh R-devel-2.10.0-2.el5.x86_64.rpm

    [hase@hasegawa02 Desktop]$ sudo rpm -ivh libRmath-2.10.0-2.el5.x86_64.rpm 
    警告: libRmath-2.10.0-2.el5.x86_64.rpm: ヘッダ V3 DSA signature: NOKEY, key ID 97d3544e
    準備中...                ########################################### [100%]
       1:libRmath               ########################################### [100%]
    [hase@hasegawa02 Desktop]$ sudo rpm -ivh libRmath-devel-2.10.0-2.el5.x86_64.rpm 
    警告: libRmath-devel-2.10.0-2.el5.x86_64.rpm: ヘッダ V3 DSA signature: NOKEY, key ID 97d3544e
    準備中...                ########################################### [100%]
       1:libRmath-devel         ########################################### [100%]
    [hase@hasegawa02 Desktop]$ sudo rpm -ivh R-2.10.0-2.el5.x86_64.rpm 
    警告: R-2.10.0-2.el5.x86_64.rpm: ヘッダ V3 DSA signature: NOKEY, key ID 97d3544e
    準備中...                ########################################### [100%]
       1:R                      ########################################### [100%]

以上でインストール完了，多分 起動

    [hase@hasegawa02 Desktop]$ R

    R version 2.10.0 (2009-10-26)
    Copyright (C) 2009 The R Foundation for Statistical Computing
    ISBN 3-900051-07-0

    Rは、自由なソフトウェアであり、「完全に無保証」です。 
    一定の条件に従えば、自由にこれを再配布することができます。 
    配布条件の詳細に関しては、'license()'あるいは'licence()'と入力してください。 

    Rは多くの貢献者による共同プロジェクトです。 
    詳しくは'contributors()'と入力してください。 
    また、RやRのパッケージを出版物で引用する際の形式については
    'citation()'と入力してください。 

    'demo()'と入力すればデモをみることができます。 
    'help()'とすればオンラインヘルプが出ます。 
    'help.start()'でHTMLブラウザによるヘルプがみられます。 
    'q()'と入力すればRを終了します。 

    > head(iris)
      Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    1          5.1         3.5          1.4         0.2  setosa
    2          4.9         3.0          1.4         0.2  setosa
    3          4.7         3.2          1.3         0.2  setosa
    4          4.6         3.1          1.5         0.2  setosa
    5          5.0         3.6          1.4         0.2  setosa
    6          5.4         3.9          1.7         0.4  setosa
    > q()
    Save workspace image? [y/n/c]: n

無事に動いた．

### Eclipseで使う設定

StatETのインストールはWindowsとほぼ同じ．

実行→実行構成→Rコンソール→R
Configで，Rのホームディレクトリを指定する必要がある．
Rを上記の方法でインストールした場合，Rホームディレクトリは

    /usr/lib64/R

となっているはずなので，これを用いる．

このまま実行してもエラーが出る．
R終了時に作業スペースを保存するかどうかの設定が必要らしいので，
実行→実行構成→Rコンソール→メインで引数を `--no-save` と与える．

## 固定IPの設定

ルータ使用せず、固定IPにするには、

    /etc/sysconfig/network-scripts/ifcfg-eth0 

を編集する。

編集前

    # Intel Corporation 82567LM-2 Gigabit Network Connection
    DEVICE=eth0
    BOOTPROTO=dhcp
    HWADDR=00:1C:C0:B1:BC:32
    ONBOOT=yes

編集後

    # Intel Corporation 82567LM-2 Gigabit Network Connection
    DEVICE=eth0
    #BOOTPROTO=dhcp
    BOOTPROTO=none
    HWADDR=00:1C:C0:B1:BC:32
    ONBOOT=yes
    DHCP_HOSTNAME=*.ipc.akita-nct.ac.jp
    IPADDR=*.*.*.*
    NETMASK=255.255.255.0
    GATEWAY=*.*.*.*

編集が終わったら、ネットワークを再起動

    /etc/rc.d/init.d/network restart

## VNCを使う設定

vnc-serverが入っていることを確認してから，

    /etc/sysconfig/vncservers 

を編集する．以下の例では，haseというユーザ名でウインドウ番号1にtcp接続する

    # VNCSERVERS="2:myusername"
    # VNCSERVERARGS[2]="-geometry 800x600 -nolisten tcp -nohttpd -localhost"
     VNCSERVERS="1:hase"
     VNCSERVERARGS[1]="-geometry 1280x800 -nolisten tcp -nohttpd"

ユーザ毎にパスワードを設定

    vncpasswd

でパスワードを2回入力する．VNCサーバ起動後じゃないとダメかもしれない．うろ覚え．

vnc-serverを再起動

    sudo /etc/rc.d/init.d/vncserver restart

VNCの接続に使用する（サーバの）ポートを解放する．上記
VNCSERVERS="1:hase" の数字+5900のポートを使用するので，
「システム→管理→セキュリティレベルとファイアーウォールの設定」を開き，
「その他のポート」でポート5901，プロトコルtcpを追加する．

GNOMEを使いたいので，

    ~/.vnc/xstartup

を編集する．

    twm &

をコメントアウト（#で）して，

    vncconfig -iconic &

を

    vncconfig -nowin &

に変更する．そして，

    exec /etc/X11/xinit/xinitrc

を最終行に追記する．

vncserverを再起動すればGNOMEが使えて、クリップボードが共有できて、日本語入力もできる。素敵。

VNCクライアントは学生時代からReal VNCを使っているので，これを使う．
クライアントを起動し，サーバの欄に

    IPアドレス:ウインドウ番号（例えば，202.220.0.***:1）

又は

    ホスト名:ウインドウ番号（例えば，hasegawa02:1）

と入力してからOKボタンを押すとパスワードを求められるので，vncpasswdで設定したパスワードを入力する．

## その他

サスペンドから復帰すると画面が表示されない．

sshでリモート接続する分には問題ないが，画面も見たい．

とりあえずの対処法として，sshでアクセス→vncサーバ起動→vncでリモート接続でしのぐ．

# UT-VPNを用いたVPN接続

## インストール

<http://www2u.biglobe.ne.jp/~akio-1/linux/centos/server.html#utvpn>
を参照のこと

公式のスタートアップガイドも用意されている
UT-VPN_Startup_Guide_for_UNIX.pdf

## 設定

注意：utvpncmdからUT-VPN Clientに接続する前に，

    sudo utvpnclient start

でUT-VPN Clientを起動させておくこと
