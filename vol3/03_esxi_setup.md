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

インストーラが立ち上がらない時は NUC のブート画面で F10 を押して起動ディスクを指定してください。
後は kickstart が読み込まれて自動で設定されます。

kickstart を設定していない場合はウィザードに沿ってインストールデバイスの指定、パスワードの設定などを行ってください。

## ESXi の初期設定
kickstart がうまく走って初期設定が完了すれば、ESXi の FQDN 設定、パスワード含めて設定済みの環境が立ち上がります。
設定が反映されているか、ESXi のコンソール（黄色と黒の画面）にログインするか、

<img src="./images/11_ESXi_console.png" width="50%">

Web ブラウザで ESXi の IP アドレスを https://<ip address> で入力し Host Client に接続して確認します。

<img src="./images/12_ESXi_Host_Client.png" width="50%">


次へ **[vCenterのデプロイと基本設定](./04_vcenter_setup.md)**