デスクトップ環境構築
====
# PCスペック
iiyamaのDeep ∞　（ディープインフィニティ）  
導入OS:Ubuntu16.04(Ubuntu19.10も後に導入)  
CPU:core i7-8700  
GPU:Geforce GTX 1080 Ti 11GB  
SSD:240GB  
DDR4-2666 DIMM 16GB  
  
[NEW ALIENWARE AURORA](https://www.dell.com/ja-jp/work/shop/cty/pdp/spd/alienware-aurora-r9-desktop/caaawar914jp)  
導入OS:Ubuntu19.10  
CPU:core i9  
GPU:Geforce RTX 2080 11GB SUPER 2つ  
**導入OSがUbuntu19.10の場合については最下部の追記部分を参照**  
# GPUの認識まで
## 手順1
0. BIOS設定  
**セキュアブートの無効化**  
これをしないとUbuntuが起動しなくなったりする．  
1. インストール時  
ubuntuインストール時に「Try Ubuntu without install」を選択する画面で**eを押し**grubを編集する．  
`quit splash`を`pci=nomsi quiet splash nomodeset `に変更する．  
2. grubの編集  
/etc/default/grub をエディタで開き  
    `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"`  
    `GRUB_CMDLINE_LINUX=""`  
上記を以下に変更する．  
    `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pci=nomsi nomodeset"`  
    `GRUB_CMDLINE_LINUX="pci=noaer"`  
そして再起動する．  
## 手順2:GPUのドライバをダウンロード
以下のURLからドライバーをダウンロード  
[ドライバーダウンロード](//www.nvidia.com/Download/index.aspx)  
1. GeForce  
2. Geforce 10 Series  
3. Geforce GTX 1080 Ti  
4. Linux 64-bit  
5. Japanese  
## 手順3:Ubuntu自体のドライバを無効化(Ubuntu19.10ではこの作業はなくてもできた)
1. **/etc/modprobe.d/**内に**blacklist-nouveau.conf**というファイルを作成  
    `blacklist nouveau`  
    `options nouveau modeset=0`  
と書き込み保存  
`$ sudo update-initramfs -u`  
で再読み込みし**再起動**
## 手順4:インストーラの実行(Ubuntu19.10ではgdm3の設定を変えて対処)
1. **Ctrl + Alt + F1 でcuiモードに入る**  
`$ sudo service lightdm stop`  
でGUIを停止  
2. 実行権限を付与した後に実行．  
`$ sudo ./NVIDIA-3XXXXXX(任意の名前).run`  
`$ nvidia-smi`  
でGPUの認識を確認する  
3. `$ sudo service lightdm restart`  
でGUIモードに入り*再起動*  

# CUDAおよびcuDNNの設定
## 手順1:CUDAのダウンロード
[CUDA](https://developer.nvidia.com/cuda-90-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1604&target_type=runfilelocal)  
1. Linux  
2. x86_64  
3. Ubuntu  
4. 16.04  
5. deb(local)  
## 手順2:インストール
1. インストール  
`$ sudo dpkg -i cuda-repo-ubuntu1604-9-0-local_9.0.176-1_amd64.deb`  
`$ sudo apt-key add /var/cuda-repo-9-0-local/7fa2af80.pub`  
`$ sudo apt-get update`  
`$ sudo apt-get install cuda`  
その後，パッチをあてる  
`$ sudo dpkg -i パッチ`  
2. .bashrcに書き込み  
    `export PATH=/usr/local/cuda-9.0/bin:${PATH}`  
    `export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:${LD_LIBRARY_PATH}`  
再起動し**nvcc**，**nvidia-smi**などで確認する．  
## 手順3:cuDNNのダウンロード
[cuDNN](https://developer.nvidia.com/rdp/cudnn-download)  
1. CUDA9.0に対応するものをダウンロード．  
2. **Runtime Library for Ubuntu**と**Developer Library for Ubuntu**の両方をダウンロード  
`$ sudo dpkg -i `  
でインストール．  

# 追記(Ubuntu19.10インストール時)
Ubuntu19.10インストール時にも同様にログインループが発生  
## 解決手順:NVIDIA driverの導入
とりあえず，update，upgrade  
NVIDIA driverを探す．（必要ないらしいけどした）  
`$ sudo add-apt-repository ppa:graphics-drivers/ppa`  
`$ sudo apt update`  
`$ ubuntu-drivers devices`  
でNVIDIA driverを探す  
recommendedされているものを以下のコマンドでインストール  
`$ sudo apt install nvidia-driver-xxx`  
`$ nvidia-smi`  
で確認．  
**ただし，このまま reboot するとログインループになる**  
  そこで，以下を実行する(ドライバインストール直後に実行で解決した)  
1. gdm3を設定ごとアンインストール  
`$ sudo apt-get purge gdm3`  
2. gdm3を再インストールする  
`$ sudo apt-get install gdm3`  
その後 reboot  
### gdm3について
GDMはGNOME Display Managerの略  
ログインループの原因はこれにある．  
ログイン画面に影響を及ぼすファイルが以下のファイル．  
`$ /etc/gdm3custom.conf`  
以下の項目が自動ログインの項目とログインするIDの項目  
`AutomaticLoginEnable= Bool`  
`AutomaticLogin=UserID`  
**もし環境構築完了後に再起動した際にログインループに陥る場合もこの手順で解決可能**

## CUDA10.1の導入
[CUDA10.1 update1 Archive](https://developer.nvidia.com/cuda-10.1-download-archive-update1?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1810&target_type=deblocal)  
1. Linux  
2. x86_64  
3. Ubuntu  
4. 18.10(19.10にも対応)  
5. deb(local)
