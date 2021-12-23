1. [メインへ](./README.md)
2. [NUC の初期設定](./01_nuc_setup.md)
3. [ESXi インストーラの作成](./02_esxi_custom_installer.md)
4. [ESXi のインストールと基本設定](./03_esxi_setup.md)
5. [vCenter のデプロイと基本設定](./04_vcenter_setup.md)
6. [Nested ESXi のデプロイ](./05_nested_esxi.md)
7. [vSphere Cluster の作成](./06_vsphere_cluster.md)
8. [自宅 vSphere らぼの Expert をご紹介](./07_expert.md)

# vCenter のデプロイと基本設定

vCenter のインストールについては以下の公式ドキュメントも参照願います。  
**[vCenter Server のインストールとセットアップについて](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.vcenter.install.doc/GUID-8DC3866D-5087-40A2-8067-1361A2AF95BD.html)**

## vCenter を導入する前の事前準備

vCenter を導入する前に、自宅らぼ環境に DNS サーバーが無い場合はセットアップした ESXi 上に簡易なもので構わないので DNS サーバーをたてましょう。

私は Windows Server の評価版で DNS と NTP の機能を持たせた VM を導入して使っていますが、Linux で dnsmasq などで簡易なものをたてるのでも十分です。

## vCenter Server Appliance のダウンロード
ESXi と同じく vCenter Server Appliance のダウンロードは [製品評価センター](https://www.vmware.com/jp/try-vmware.html) から入手可能です。
※ 評価版と通常製品版にバイナリの違いはありません。

ESXi と同じか、ESXi より新しいバージョンの vCenter をダウンロードしてください。

## vCenter Server Appliance のインストール

ダウンロードした ISO イメージファイルを PC にマウントします。  
最近の PC であれば以下の様に CD/DVD ドライブとして自動的に認識されるはずです。  
各種 OS に対応した GUI のインストールツールと CLI でのインストールツールが用意されています。

今回は簡単な GUI でのインストールを行うので "vcsa-ui-installer" を開き、ご利用の OS のフォルダの中のインストーラ "Installer" を実行してください。

<img src="./images/04_vCSA_Install01.png" width="50%">

インストーラを立ち上げるといくつかのメニューが選べます。ここでは「インストール」を選びます。  
※ 環境によっては日本語表示でない場合がありますのでその際は右上の言語設定から選択してください。

<img src="./images/04_vCSA_Install02.png" width="50%">

vCenter のインストールは仮想アプライアンスのデプロイと、その後の vCenter としての初期設定の二つのステージで実行されます。

<img src="./images/04_vCSA_Install03.png" width="50%">

EULA の承諾にチェックを入れて進みます。

<img src="./images/04_vCSA_Install04.png" width="50%">

vCenter をデプロイする先の ESXi を指定します。  
今回は用意した ESXi on NUC のアドレスとアカウントを入力します。

<img src="./images/04_vCSA_Install05.png" width="50%">

vCenter の任意の仮想マシン名 (vSphere Client 上で表示される名称) と root アカウントのパスワードを指定します。

<img src="./images/04_vCSA_Install06.png" width="50%">

vCenter のデプロイサイズを指定しますが、自宅らぼ環境であれは "極小" で十分足ります。  
目安となる VM 数や管理する ESXi 台数が提示されていますので適切なものを選択します。

<img src="./images/04_vCSA_Install07.png" width="50%">

vCenter を展開するデータストアを選択します。

<img src="./images/04_vCSA_Install08.png" width="50%">

ネットワーク設定などを入力します。  
ここで予め DNS サーバーに登録した vCenter の FQDN が必要になります。
IP アドレスで登録する事もできますが、FQDN で登録した方が後々の運用が楽です。

<img src="./images/04_vCSA_Install09.png" width="50%">

IP アドレスなど必要情報を入力します。

<img src="./images/04_vCSA_Install10.png" width="50%">

最後に設定確認をして「完了」をクリック。

<img src="./images/04_vCSA_Install11.png" width="50%">

5分 ~ 10分ほどでインストールのステージ 1 は完了します。

<img src="./images/04_vCSA_Install12.png" width="50%">


完了したら「続行」をクリック。

<img src="./images/04_vCSA_Install13.png" width="50%">

続いてステージ2。

<img src="./images/04_vCSA_Install14.png" width="50%">

まずは時刻設定。

<img src="./images/04_vCSA_Install15.png" width="50%">

ESXi で設定した NTP サーバーと同じ参照先が推奨されます。

<img src="./images/04_vCSA_Install16.png" width="50%">

vSphere 環境を操作するための Single Sign On (SSO) の設定です。  
ドメイン名は任意のものを利用できます(展開する vCenter の範囲だけで利用されますので、デフォルトの vsphere.local でも問題ありません)。

<img src="./images/04_vCSA_Install17.png" width="50%">

CEIP (VMware カスタマー エクスペリエンス 向上 プログラム) を有効にするか否かを聞かれます。  
vSAN クラスタを組むときなどは CEIP を有効にしているとオンライン健全性チェックに活用できますが、自宅らぼ運用の場合無効化(チェックを外す)しても問題ありません。  

**[CEIPについてはこちらを参照](https://www.vmware.com/jp/solutions/trustvmware/ceip.html)**

<img src="./images/04_vCSA_Install18.png" width="50%">

設定内容確認したら「完了」をクリック。

<img src="./images/04_vCSA_Install19.png" width="50%">

設定は 10分 ~ 20分で完了します。

<img src="./images/04_vCSA_Install20.png" width="50%">

完了したら vCenter へ Web ブラウザで接続しましょう。

<img src="./images/04_vCSA_Install21.png" width="50%">


## vSphere Client を利用した基本操作

vCenter のインストールについては以下の公式ドキュメントも参照願います。  
**[VMware vCenter Server およびホスト管理について](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.vsphere.vcenterhost.doc/GUID-3B5AF2B1-C534-4426-B97A-D14019A8010F.html)**

vCenter の初期インストールが完了したら vCenter の FQDN (または IP アドレス) を Web ブラウザに入力してください。  
「vSphere Client (HTML5) の起動」をクリックします。

<img src="./images/04_vCSA_Setting02.png" width="50%">

vSphere Client へのアクセスは SSO ユーザーを入力します。  
※ ドメイン名はステージ2 で指定したドメイン名です。

デフォルト設定の場合は **administrator@vsphere.local** です。

<img src="./images/04_vCSA_Setting03.png" width="50%">

vSphere Client の基本画面が立ち上がります。

<img src="./images/04_vCSA_Setting04.png" width="50%">

まだ何もないので、まずは仮想データセンターを追加します。  
左メニュー欄の vCenter のアイコンを右クリックすると操作可能なメニューが選べます。

<img src="./images/04_vCSA_Setting06.png" width="50%">

任意の名称を設定します。

<img src="./images/04_vCSA_Setting07.png" width="50%">
<img src="./images/04_vCSA_Setting08.png" width="50%">

続いて ESXi ホストを追加します。作成した仮想データセンターを右クリックします。  
※ 一般的にはここで「クラスタ」を作成しますが今回はスキップしています。

<img src="./images/04_vCSA_Setting09.png" width="50%">

ESXi の FQDN (または IP アドレス) を入力します。  
※ ここで入力した文字列で vSphere Client 上で表示されます。

<img src="./images/04_vCSA_Setting11.png" width="50%">

root アカウントのパスワードを入力。

<img src="./images/04_vCSA_Setting12.png" width="50%">

ホストを確認し、

<img src="./images/04_vCSA_Setting13.png" width="50%">

ライセンスは評価モード期間をそのまま選択。

<img src="./images/04_vCSA_Setting14.png" width="50%">

ロックダウンモードは利用しません。

<img src="./images/04_vCSA_Setting15.png" width="50%">

「次へ」をクリック。

<img src="./images/04_vCSA_Setting16.png" width="50%">

入力内容に問題が無ければ「完了」をクリック。

<img src="./images/04_vCSA_Setting17.png" width="50%">

以上で vCenter 配下に ESXi が加わりました。

<img src="./images/04_vCSA_Setting01.png" width="50%">

## vCenter のもう一つの管理画面 Virtual Appliance Management Interface (VAMI)

vCenter には vSphere Client を利用して操作する仮想化基盤の運用以外にも、  
vCenter Server Appliance 自体の設定を操作する専用の画面があり、
Virtual Appliance Management Interface (VAMI) 等と呼ばれています。  
※ 私は VAMI = ヴァミ と読んてます

詳細は以下の公式ドキュメントを参照願います。

**[vCenter Server管理インターフェイスを使用して vCenter Server を設定する](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.vsphere.vcenter.configuration.doc/GUID-9831B635-DFFA-40FA-9DA9-CEF8A1729E54.html)**


VAMI へのアクセスは vCenter のアドレスにポート番号 5480 を追加します。  
http://<vCenter IP or FQDN>:5480/

vCenter 導入時に設定した root アカウントのパスワードを入力します。  
※ vCenter 7.0 以降では vSphere Client にアクセスする際の SSO ユーザー (administrator@vsphere.local) でもログイン可能ですが操作が一部制限されます。

<img src="./images/04_VAMI1.png" width="50%">

時刻設定やバックアップ設定、アップデートパッチの適用などもこの画面から行います。

<img src="./images/04_VAMI2.png" width="50%">

以上、vCenter の基本的な導入のご紹介でした。

次は **[Nested ESXiのデプロイ](./05_nested_esxi.md)** です。