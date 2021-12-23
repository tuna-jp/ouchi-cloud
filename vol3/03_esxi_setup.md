1. [メインへ](./README.md)
2. [NUC の初期設定](./01_nuc_setup.md)
3. [ESXi インストーラの作成](./02_esxi_custom_installer.md)
4. [ESXi のインストールと基本設定](./03_esxi_setup.md)
5. [vCenter のデプロイと基本設定](./04_vcenter_setup.md)
6. [Nested ESXi のデプロイ](./05_nested_esxi.md)
7. [vSphere Cluster の作成](./06_vsphere_cluster.md)
8. [自宅 vSphere らぼの Expert をご紹介](./07_expert.md)

# ESXiのインストールと基本設定

vSphere ESXi のインストールについては以下の公式ドキュメントも参照願います。  
**[vSphere のインストールおよびセットアップの概要](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.esxi.install.doc/GUID-A71D7F56-6F47-43AB-9C4E-BAA89310F295.html)**

## NUC へ ESXi のインストール
おうちクラウドでの ESXi のインストールは作成した USB メモリのインストーラを NUC に挿して起動するだけのシンプルな作業です。
インストーラが立ち上がらない時は NUC のブート画面で F10 を押して起動ディスクを指定します。

ウィザードに沿ってインストールデバイスの指定、パスワードの設定などを行います。
※ インストーラ USB メモリに kickstart を設定している場合はインストールは自動的に進行します。

<img src="./images/03_ESXi_Install01.png" width="80%">

システム領域のサイズを指定する場合は上記画面で "Shift+O" を押下して、systemMediaSize=min などのパラメータを追記してください。詳細は **※ システム領域サイズのカスタマイズを行う場合は前項の [オプション : システム領域サイズのカスタマイズ]() を参照願います。**

"F11" キーを押下してインストールを続行します。

<img src="./images/03_ESXi_Install03.png" width="80%">

インストール先のドライブを指定します。  
複数のドライブが NUC にマウントされている場合は容量やインターフェースの違いでインストール先を識別してください。

<img src="./images/03_ESXi_Install04.png" width="80%">

キーボードレイアウトを指定します。

<img src="./images/03_ESXi_Install05.png" width="80%">

ESXi の root アカウントのパスワードを設定します。

<img src="./images/03_ESXi_Install06.png" width="80%">

最終確認をして ESXi のインストールを続行します。

<img src="./images/03_ESXi_Install07.png" width="80%">

1～2分でインストールは完了します。

<img src="./images/03_ESXi_Install08.png" width="80%">

最後に再起動を実行すればインストールは完了です。

<img src="./images/03_ESXi_Install09.png" width="80%">

<img src="./images/03_ESXi_Install10.png" width="80%">


## ESXi の初期設定
初期インストールが完了し、再起動すると ESXi のコンソール画面（黄色と黒の画面）が立ち上がります。
F2 キーを押下し、root アカウントでログインして ESXi の IP アドレスや FQDN、DNS サーバーを指定します。

※ ESXi のコンソール画面の事をダイレクト コンソール ユーザー インターフェイス (DCUI) と呼びます。

公式ドキュメント **[ESXi のダイレクト コンソール インターフェイスについて](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.esxi.install.doc/GUID-E64B4311-11E5-44E8-8DB5-B438B4A14289.html)**

<img src="./images/03_ESXi_console.png" width="80%">


### ESXi の IP アドレスの設定

インストールしたての ESXi のデフォルトネットワーク設定は DHCP となっているので、まずは固定 IP アドレス、ホスト名、DNS サーバー等を設定します。

"F2" キーを押下し root アカウントでログインし、設定画面に入ります。

"Configure Management Network" を選びます。

<img src="./images/03_ESXi_DCUI1.png" width="80%">


"Network Adapter"、"VLAN"、"IPv4 Configuration"、"IPv6 Configuration"、"DNS Configuration"、"Custom DNS Suffixes" など、カーソルを移動して設定していきます。

<img src="./images/03_ESXi_DCUI2.png" width="80%">

"IPv4 Configuration" では固定 IP を設定します。

<img src="./images/03_ESXi_DCUI3.png" width="80%">

"DNS Configuration" では DNS サーバーの指定と、ESXi のホスト名を指定します。

※ DNS サーバーが自宅らぼ環境にない場合は、一旦は空欄にするか仮の DNS サーバーの IP アドレスを入力してください。現在セットアップ中の ESXi に後ほど DNS サーバーを構築する事も可能なので DNS サーバーが用意でき次第に設定します。

<img src="./images/03_ESXi_DCUI4.png" width="80%">

"Custom DNS Suffixes" ではドメイン名を指定します。

こちらも DNS サーバーが用意されていない場合は、今後用意する前提でそのドメイン名を入れておきましょう。

<img src="./images/03_ESXi_DCUI5.png" width="80%">

IP アドレス関連の入力が完了後、"ESC" キーで設定画面を抜ける際に設定反映のためにネットワークの再起動を促されますので "Y" キーを押下して設定反映します。

<img src="./images/03_ESXi_DCUI6.png" width="80%">


※ オプション設定
ESXi のローカルシェルを利用する場合や SSH 接続で CLI で操作する場合には DCUI の "Troubleshooting Options" から設定します。

<img src="./images/03_ESXi_DCUI7.png" width="80%">


画面の見方は左側の黄色のメニューがこれから設定変更する内容、右側の灰色の表示が現在の設定内容を示しています。

<img src="./images/03_ESXi_DCUI8.png" width="80%">

上二つの "Enable ESXi Shell"、"Enable SSH" を必要に応じて有効化します。

<img src="./images/03_ESXi_DCUI9.png" width="80%">



## vSphere Host Client を利用した操作
ESXi の IP アドレスを設定した後は Web ブラウザで ESXi の IP アドレスを https://<ip address> と指定して Host Client に接続出来ます。この UI を用いて詳細な設定を行います。

<img src="./images/03_ESXi_Host_Client.png" width="80%">

### NTP 時刻同期設定

※ NTP サーバーの指定は必須ではありませんが、認証連携やログ時刻の一致等で同一時刻に揃っている事が重要なのでなるべく設定しましょう。  
※ 自宅らぼ用に展開する DNS サーバーに NTP サーバーの役割を持たせる事も可能です（私もそうしています）

Host Client で左上の ESXi ホストを開き、「管理」 > 「システム」 > 「日付と時刻」を開き、「NTP 設定の編集」を選択します。

<img src="./images/03_ESXi_NTP0.png" width="80%">

NTP 設定で「NTP サービス起動ポリシー」は「ホストと連動して起動および停止します」を選択  
NTP サーバーはアクセス可能なサーバーを指定します。

<img src="./images/03_ESXi_NTP1.png" width="80%">

設定しただけでは NTP サービスは稼働していないので、「管理」 > 「サービス」 > "ntpd" を選択し起動します。

<img src="./images/03_ESXi_NTP2.png" width="80%">

起動すると緑の三角マークで「実行中のステータスになります」

<img src="./images/03_ESXi_NTP3.png" width="80%">


もう一度「日付と時刻」の設定画面に戻ると時刻同期が開始されたことが確認できます。

<img src="./images/03_ESXi_NTP4.png" width="80%">


## 仮想マシンネットワークの追加、設定

vSphere のネットワーク設定は、ESXi で標準で利用可能な vSphere Standard Switch (vSS : 標準スイッチ) と、vCenter から作成可能な vSphere Distributed Switch (vDS : 分散スイッチ) があります。

今回は基本の vSS と vSS に割り当てるポートグループの設定を行います。

ネットワーク構成のイメージは、vSS とポートグループの関係は、  
「ネットワーク」 > 「仮想スイッチ」 > 「vSwitch0」 を選択するとスイッチのイメージが表示されます。
ESXi のデフォルト状態では 「vSwitch0」 という vSS が作成され、アップリンクの NIC と ESXi の管理ネットワーク用ポート(VMKernel ポート)、仮想マシン用のポートグループ 「VM Network」 が存在します。

ポートグループ単位で VLAN 設定やセキュリティ設定を行います。

<img src="./images/03_ESXi_NW1.png" width="80%">

<img src="./images/03_ESXi_NW5.png" width="80%">


デフォルトでは Native VLAN (ESXi から見ると VLAN 無し = VLAN 0) が設定されていますが、アップリンク側のスイッチと合わせてポートグループを追加して設定を変える事が可能です。

この後に構成する Nested ESXi を作成する時など、アップリンクスイッチから Trunk VLAN 設定をそのままポートグループに渡したい場合は 「VLAN 4095」を指定します。  
※ vDS 利用時は 「Trunk VLAN 」 の設定がメニューで指定可能。

<img src="./images/03_ESXi_NW4.png" width="80%">

また、Nested ESXi を ESXi 上に作成する時や、仮想ルータを ESXi 上に作成する場合にはポートグループのセキュリティ設定を緩和する必要があります。

上記の画面にある様に、Nested ESXi を接続するためのポートグループだけ追加で作成し、セキュリティ設定の「偽装転送」「MAC アドレス変更」「偽装転送」をそれぞれ「承諾」に変更します。。


詳細は [@syauichi](https://twitter.com/syauichi) さんのブログ、**[ネットワークチェンジニアとして Nested ESXiに必要なセキュリティ緩和設定（無差別モードの許可）](https://changineer.info/vmware/hypervisor/vmware_promiscuous_mode.html)** にとても分かり易く説明されておりますので、興味のある方はご参照願います。

## 追加ストレージのフォーマット

Intel NUC の場合、2.5inch の SSD（または HDD）と m.2 接続の SSD が利用可能です。
ESXi をインストールしたドライブはシステム領域以外は VMFS (Virtual Machine File System) と呼ばれる仮想マシン（VM）の実データを保存する為のファイルシステムでフォーマットされています。

**[VMFS データストアについて](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.vsphere.storage.doc/GUID-5EE84941-366D-4D37-8B7B-767D08928888.html)**


追加ドライブの VMFS へのフォーマットは 「ストレージ」 > 「デバイス」 を開くと搭載され認識されているドライブが表示されます。  
もし表示されない場合は「再スキャン」を実施します。それでも表示されない場合、デバイスドライバが含まれていない・対応していない可能性があります。  

特に、NVMe SSD はサポートされるモデルが限定されますので、先にご紹介した **[Flings Community NVMe Driver for ESXi](https://flings.vmware.com/community-nvme-driver-for-esxi)** をインストールして認識されるかの切り分けが必要です。

<img src="./images/03_ESXi_Storage0.png" width="80%">

追加のドライブが確認できる場合は「新しいデータストア」を選択します。

<img src="./images/03_ESXi_Storage1.png" width="80%">

対象のドライブを選択し、任意の名称を付けます。

<img src="./images/03_ESXi_Storage2.png" width="80%">

全てのパーティションを「VMFS6」でフォーマットします。  
※ パーティションを分ける事もできますが自宅らぼで使う上で意味が無いのでしません。

<img src="./images/03_ESXi_Storage3.png" width="80%">

最後に確認して完了です。

<img src="./images/03_ESXi_Storage4.png" width="80%">

ちなみに、他の環境で利用したことのあるドライブを流用する際は、ドライブ上に元のデータ（パーティション）が残っている場合があります。  
その際は対象のドライブを選択し「アクション」 > 「パーティションテーブルをクリア」を実行します。
※ この作業は元のデータへのアクセスが不可になる事を意味します、間違えて作業しないように慎重に確認をしてください。

<img src="./images/03_ESXi_Storage5.png" width="80%">


以上で ESXi の基本的な設定は完了しました。

ここまで設定できれば普通の仮想マシンをデプロイして操作する事は問題なく行えます。


## VMware Remote Console (VMRC) のインストール

仮想マシンを作成し、画面にアクセスする際に HTML5 のコンソールを利用する事もできますが、インストーラなどの ISO イメージをマウントしやすいように VMware Remote Console (VMRC) をインストールしておきましょう。

仮想マシンを新規に作成してパワーオンすると以下の様に "コンソール" が選べるようになります。

<img src="./images/03_VMRC1.png" width="80%">

「VMRC のダウンロード」を選択します。

<img src="./images/03_VMRC2.png" width="80%">

[VMware Remote Console (VMRC) ダウンロードページ](https://customerconnect.vmware.com/en/downloads/details?downloadGroup=VMRC1201&productId=876)

<img src="./images/03_VMRC3.png" width="80%">

VMRC がインストールされると、仮想マシンのコンソールを開く際に「リモートコンソールを起動」するを選べば専用のツールが起動します。

PC 上の ISO イメージファイルを直接仮想マシンに接続させるときも、VMRC のメニューから簡単に行えて便利です。

<img src="./images/03_VMRC4.png" width="80%">



## Nested ESXi を動かすための仕込み

この後、vCenter をインストールする前に、Nested ESXi 環境の利用を可能にする設定を入れておきましょう。

ESXi の上にさらに ESXi を仮想マシンとして動かす場合には /etc/vmware/config に vhv.allow = "TRUE" の設定を1行追加します。

ESXi に SSH で接続するかローカルコンソール(DCUI)から Alt＋F1 キーを押して ESXi Shell を起動して root でログインします。

以下の一文を投げれば config ファイルに設定が追加されます。

```shell
echo 'vhv.allow = "TRUE"' >> /etc/vmware/config
```

また、Inetl NUC だと現時点では実現が難しいですが、その他の PC や Server を利用して物理 ESXi を複数台組み合わせて vSAN 構成としている場合、  
vSAN 上に Nested ESXi を展開するには以下の設定を投入する必要があります。

```shell
esxcli system settings advanced set -o /VSAN/FakeSCSIReservations -i 1
```

設定後、再起動する事で反映されます。



次は **[vCenterのデプロイと基本設定](./04_vcenter_setup.md)** です