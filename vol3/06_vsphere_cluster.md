1. [メインへ](./README.md)
2. [NUC の初期設定](./01_nuc_setup.md)
3. [ESXi インストーラの作成](./02_esxi_custom_installer.md)
4. [ESXi のインストールと基本設定](./03_esxi_setup.md)
5. [vCenter のデプロイと基本設定](./04_vcenter_setup.md)
6. [Nested ESXi のデプロイ](./05_nested_esxi.md)
7. [vSphere Cluster の作成](./06_vsphere_cluster.md)
8. [自宅 vSphere らぼの Expert をご紹介](./07_expert.md)

# vSphere Cluster の作成

Nested ESXi を 3 台作成したので続いて vSAN クラスタとして構成したいと思います。  
※ vSAN はあくまでオプションです、NAS など外部ストレージをご利用可能な場合はそれらをデータストアとして利用して頂けます。

**今回は vSphere 6.7 以降で実装された 「クラスタクイックスタート」 を利用して vSAN クラスタを Nested 構成で組みたいと思います。**


詳細については公式ドキュメント  
**[クイックスタートを使用した vSAN クラスタの構成](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.vsphere.vsan-planning.doc/GUID-7BC09239-46BD-46FD-B803-95244CCBC8F4.html)**  
も併せてご参照ください。


まずは仮想データセンターを右クリックして 「新規クラスタ」 をクリックします。

<img src="./images/06_NestedCluster01.png" width="50%">

vSphere HA や vSAN などのオプションを有効化できますが、後で編集もできるので一先ずこのまま続行して問題ないです。

<img src="./images/06_NestedCluster02.png" width="50%">

「完了」をクリックします。

<img src="./images/06_NestedCluster03.png" width="50%">

作成したクラスタを選択し、「構成」 > 「クラスタクイックスタート」 を表示します。

<img src="./images/06_NestedCluster04.png" width="50%">

vSphere HA や DRS、vSAN の有効有無は「クラスタの基本」から編集可能です。  
今回は改めて全ての機能を有効化します。

<img src="./images/06_NestedCluster05.png" width="50%">

続いて「ホストの追加」をクリックします。

<img src="./images/06_NestedCluster06.png" width="50%">

作成した Nested ESXi 3台の情報を入力します。

<img src="./images/06_NestedCluster07.png" width="50%">

<img src="./images/06_NestedCluster08.png" width="50%">

<img src="./images/06_NestedCluster09.png" width="50%">

問題なければ「完了」をクリックします。

<img src="./images/06_NestedCluster10.png" width="50%">


<img src="./images/06_NestedCluster11.png" width="50%">

しばらくするとホストの追加が完了します。
この時点ではクラスタにホストが追加されただけなので、下の方に画面をスクロールして、

<img src="./images/06_NestedCluster12.png" width="50%">

「クラスタの構成」をクリックします。

<img src="./images/06_NestedCluster13.png" width="50%">

分散スイッチ構成 (vDS) は標準設定で進めますが、Nested ESXi に割り当てた 2 つの vNIC を割り当ててネットワークアダプタの冗長構成を組みたいので、

<img src="./images/06_NestedCluster14.png" width="50%">

下までスクロールしたら アダプタ0 (vmnic0) と アダプタ1 (vmnic1) が両方とも DSwitch 二割当たっているかを確認します。

<img src="./images/06_NestedCluster15.png" width="50%">

続いて vMotion ネットワークと vSAN ネットワークを構成しますが、  
VLAN 機能のないネットワークスイッチを利用している場合は VLAN のチェックを外してください。

※ キャプチャで表示されている "VLAN 0" は ESXi が認識する デフォルト VLAN を示しているのでこの設定のままでも問題ありません。  
L2 スイッチをご利用で、ネットワークを分ける場合は適宜設定を修正してください。


任意の IP アドレスを Nested ESXi 3 台分割り当てます。

<img src="./images/06_NestedCluster16.png" width="50%">

vSAN ネットワークについても同様です。

<img src="./images/06_NestedCluster17.png" width="50%">

HA、DRS、vSAN のオプションはデフォルトのままで進みます。  

※ vSphere HA など高可用性の仕組みについては公式ドキュメントなどをご参照ください。  
**[vSphere HA クラスタの作成と使用](https://docs.vmware.com/jp/VMware-vSphere/7.0/com.vmware.vsphere.avail.doc/GUID-5432CA24-14F1-44E3-87FB-61D937831CF6.html)**

<img src="./images/06_NestedCluster18.png" width="50%">

vSAN データストアに追加するドライブをキャッシュとキャパシティそれぞれを設定します。  
デフォルトでは容量が少ない方がキャッシュと判断されますので、  
今回は 80GB と 300GB でそれぞれ 1 つずつ仮想ディスクを追加したのでそのまま正しく割り当てられているはずです。

<img src="./images/06_NestedCluster19.png" width="50%">

続いて vSAN Support Insight の有効無効について確認されます。  
後から変更もできますので、ここではチェックを外して無効化で問題ないです。

<img src="./images/06_NestedCluster20.png" width="50%">

<img src="./images/06_NestedCluster21.png" width="50%">

設定の最後に「完了」をクリックします。

<img src="./images/06_NestedCluster22.png" width="50%">

構成に問題が無ければ数分でクラスタのセットアップが完了します。  
※ 途中、タスクにいくつか警告やエラーが表示されますが、一先ず完了するまで見守ってください。

<img src="./images/06_NestedCluster23.png" width="50%">

無事にクラスタクイックスタートが完了すると vSAN クラスタも有効になり、  
vSAN クラスタとしての UI も各種確認可能です。

<img src="./images/06_NestedCluster24.png" width="50%">

分散スイッチ(vDS) も自動的にセットアップされます。

<img src="./images/06_NestedCluster25.png" width="50%">

ここまででクラスタの基本セットアップは完了です。  
Nested Cluster なので CPU やメモリに制限があるため、通常と同じパフォーマンスの仮想マシンを動作させる事は難しいですが、ゲスト OS をインストールして各種クラスタとしての動作確認をする事は可能です。


## まとめ

以上で Nested ESXi を利用した vSAN クラスタのセットアップは完了です。
かなり詳細を省いた形でセットアップをご紹介しましたが、  
クラスタクイックスタートなどを利用する事で従来はかなり面倒だったクラスタの初期設定や vDS の作成、vSAN クラスタのセットアップも、ウィザード形式で簡単に作成できる事が伝わったかなと思います。

たった 1 台の NUC だけですが、Nested ESXi を利用する事で vSphere Cluster としての構築も可能です。

この後の連載で K8s などコンテナのソリューションもこの上で動かせますので利活用していただければ幸いです。

最後に、とても参考になる**[自宅 vSphere らぼの Expert をご紹介](./07_expert.md)**します。
