1. [メインへ](./README.md)
2. [NUCの初期設定](./01_nuc_setup.md)
3. [ESXiインストーラの作成](./02_esxi_custom_installer.md)
4. [ESXiのインストールと基本設定](./03_esxi_setup.md)
5. [vCenterのデプロイと基本設定](./04_vcenter_setup.md)
6. [Nested ESXiのデプロイ](./05_nested_esxi.md)
7. [vSphere Clusterの作成](./06_vsphere_cluster.md)

# ESXiのインストールと基本設定



## ESXi のインストール
インストールは特別な事はせず、作成した USB メモリのインストーラを NUC に挿して起動します。

インストーラが立ち上がらない時は NUC のブート画面で F10 を押して起動ディスクを指定します。

kickstart を設定していない場合はウィザードに沿ってインストールデバイスの指定、パスワードの設定などを行います。


## ESXi の初期設定
初期インストールが完了し、再起動すると ESXi のコンソール画面（黄色と黒の画面）が立ち上がります。
F2 キーを押下し、ESXi の IP アドレスや FQDN、DNS サーバーを指定します。

※ ESXi のコンソール画面の事をダイレクト コンソール ユーザー インターフェイス (DCUI) と呼びます。

公式ドキュメント [ESXi のダイレクト コンソール インターフェイスについて](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.esxi.install.doc/GUID-E64B4311-11E5-44E8-8DB5-B438B4A14289.html)

<img src="./images/11_ESXi_console.png" width="50%">

## vSphere Host Client を利用した操作
Web ブラウザで ESXi の IP アドレスを https://<ip address> で入力し Host Client に接続してより詳細な設定を行います。

<img src="./images/12_ESXi_Host_Client.png" width="50%">


次へ **[vCenterのデプロイと基本設定](./04_vcenter_setup.md)**