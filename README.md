# WSLにおける実験環境構築
WSLのUbuntu環境で、Python3.10のtensorflow2やpytorchなどからGPUを利用できるようにするための手順を解説します

## 参考
> [Python3.11 + TF2.12 + GPUの環境を構築した(Windows11 + WSL + VSCode)](https://qiita.com/pocokhc/items/0a759130941090b1d231)

> [Wslconfigを使用したWSL2の設定](https://zenn.dev/big_tanukiudon/articles/622d07bb20f0bc)

> [UbuntuでCUDA，NVIDIAドライバ，cudnnをインストールし，PyTorchでGPU環境を使えるようにするまで](https://qiita.com/tf63/items/0c6da72fe749319423b4)

## 1.Hyper-Vの有効化
- スタートメニュー(設定)から「Windowsの機能の有効化または無効化」を検索して開きます
- 以下を選択し再起動
   - `Hyper-V`
   - `Linux 用 Windows サブシステム`
   - `仮想マシンプラットフォーム`

## 2.Ubuntuのインストール
コマンドプロンプトを管理者として実行してください。
>
次のコマンドでUbuntuをインストールします。インストール完了後、ユーザ名とパスワードを登録してください。
```bash
C:\Users\user> wsl --install -d Ubuntu-22.04
インストール中: Ubuntu 22.04 LTS
[                           0.0%                           ]
```
以下のコマンドでインストールされたことを確認しましょう
```bash
C:\Users\user> wsl --list -v
  NAME                   STATE           VERSION
* Ubuntu-22.04           Stopped         2
```
VERSIONが1の場合、以下のコマンドでバージョンを変更してください
```bash
C:\Users\user> wsl --set-version Ubuntu-22.04 2
```

## 3.NVIDIAドライバーのインストール
コマンドプロンプトを管理者として実行してください。
>
以下のコマンドでUbuntuを起動します
```bash
C:\Users\user> wsl
```
以下のURLからドライバーを手動検索し、LinuxではなくWindows用のNVIDIAドライバーをダウンロード＆インストールしてください
> [NVIDIA 公式ドライバーのダウンロード](https://www.nvidia.com/ja-jp/drivers/)

## 3.Ubuntu上でcudaをセットアップ
以下のURLから、linux用のcudaをダウンロード&インストールします
> [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)
> 
私の場合、tensorflow2.14に対応したバージョンのcudaをインストールしたかっため、`CUDA Toolkit 11.8.0`を選択しました。
>
(以下のURLから、各tensorflowのバージョンに対応したcuda, cuDNN, pythonのバージョンを確認できます。)
>[ソースからのビルド](https://www.tensorflow.org/install/source?hl=ja)
>
Target Platformの選択は以下の通りにしましょう
   - Operating System: Linux
   - Architecture : x86_64
   - Distribution : WSL-Ubuntu
   - Version : 2.0
   - Install Type : deb(local)
>
インストールコマンドが表示されるので、ubuntu上で実行します。
>
次のコマンドでインストールされたかどうかを確認できます
```bash
$ /usr/local/cuda/bin/nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2022 NVIDIA Corporation
Built on Wed_Sep_21_10:33:58_PDT_2022
Cuda compilation tools, release 11.8, V11.8.89
Build cuda_11.8.r11.8/compiler.31833905_0
```

## 3.Ubuntu上でcuDNNをセットアップ
以下のURLから、linux用のcuDNNをダウンロード&インストールします
> [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive)
> 
私の場合、tensorflow2.14に対応したバージョンのcudaをインストールしたかっため、`Download cuDNN v8.7.0 (November 28th, 2022), for CUDA 11.x`を選択しました。
>
cuDNNのバージョン選択後、`Local Installer for Ubuntu22.04 x86_64 (Deb)`を選択し、nvidiaのアカウント登録をしてからdebファイルをダウンロードします。
>
ダウンロード後、次のコマンドをubuntu上で実行しインストールします。
```bash
sudo dpkg -i (ダウンロードしたdebファイルのパス)
sudo cp /var/cudnn-local-repo-*/cudnn-local-*-keyring.gpg /usr/share/keyrings/
sudo apt update
sudo apt install libcudnn8
sudo apt install libcudnn8-dev
```

## 4.WSL2のconfigの設定
WSLの設定を変更することが可能です。デフォルトで割り当てられているメモリの量では足りない場合などは、以下の設定を行います
- `C:\Users\<yourUserName>\`で新しいテキストファイルを作成します
- 例えば、WSLが利用できるメモリの上限を250、プロセッサ数を1にしたい場合は、次のようにします
```
[wsl2]
memory=250GB
processors=1
```
- その他の設定は、以下のURLから調べることができます
>[WSL2のグローバル設定ファイル .wslconfigについて学んでみた。](https://dev.classmethod.jp/articles/learn_wsl2_wslconfig/)
>


## 5.完了
これで、WSLのUbuntu環境でGPUを利用できるようになったはずです
