1. [メインへ](./README.md)
2. [NUCの初期設定](./01_nuc_setup.md)
3. [ESXi インストーラの作成](./02_esxi_custom_installer.md)
4. [ESXi のインストールと基本設定](./03_esxi_setup.md)
5. [vCenter のデプロイと基本設定](./04_vcenter_setup.md)
6. [Nested ESXi のデプロイ](./05_nested_esxi.md)
7. [vSphere Cluster の作成](./06_vsphere_cluster.md)
8. [自宅 vSphere らぼの Expert をご紹介](./07_expert.md)

# Nested ESXi のデプロイ

物理サーバーにインストールされた ESXi の上にさらに ESXi をインストールする事はサポートされる構成ではありませんが、検証用途では広く使われる手法です。

今回は Intel NUC 上に Nested ESXi サーバーを 3 台インストールし、その 3 台で vSAN クラスタを作成してみたいと思います。

## Nested ESXi を導入する準備

**[Nested ESXi を動かすための仕込み](https://github.com/tanzu-japan/ouchi-cloud/blob/main/vol3/03_esxi_setup.md#nested-esxi-%E3%82%92%E5%8B%95%E3%81%8B%E3%81%99%E3%81%9F%E3%82%81%E3%81%AE%E4%BB%95%E8%BE%BC%E3%81%BF)** の項でご紹介した ESXi での設定を事前に済ませてください。

## Nested ESXi 用の仮想マシンの作成

「新規仮想マシン」を作成します。

<img src="./images/05_NestedESXi01.png" width="80%">
<img src="./images/05_NestedESXi02.png" width="80%">

任意の仮想マシン名称を入力します。

<img src="./images/05_NestedESXi03.png" width="80%">

仮想マシンを配置するクラスタまたは ESXi ホストを指定します。

<img src="./images/05_NestedESXi04.png" width="80%">

仮想マシンを保存するデータストアを選択します。

<img src="./images/05_NestedESXi05.png" width="80%">

仮想マシンのハードウェアバージョンを選択します。  
以前の古いバージョンで動かす予定が無ければ選べる最新バージョンで問題ないです。

<img src="./images/05_NestedESXi07.png" width="80%">

仮想マシンのタイプを選択します。  
ここでは Nested ESXi を ESXi 7.0 でインストールする予定ですので ESXi 7.0 以降で設定します。

<img src="./images/05_NestedESXi08.png" width="80%">
<img src="./images/05_NestedESXi09.png" width="80%">

ハードウェアの設定を行います。  
後でも設定できますが、必須個所は CPU 設定の
- "ハードウェア仮想化" : "ハードウェア アシストによる仮想化をゲスト OS に公開"
- I/O MMU : "有効"
です。必ずチェックを入れてください。

※ 今回は親となる NUC が 64GB のメモリを搭載していますので、それぞれの Nested ESXi に 2 vCPU、18GB メモリ、インストール用ドライブに 40GB を割り当て、同じものを 3 台作成します。


<img src="./images/05_NestedESXi10.png" width="80%">

- 新規 SCSI コントローラ : "VMware 準仮想化"
- 新規ネットワークアダプタ アダプタタイプ : "VMXNET3"

であることを確認してください。

※ SCSI コントローラとネットワークアダプタは後ほど同じものを追加します。

<img src="./images/05_NestedESXi11.png" width="80%">

仮想マシンの構成に問題が無ければ「Finish」をクリックします。

<img src="./images/05_NestedESXi12.png" width="80%">


※ ESXi 7.0 以降のバージョンでは ESXi をインストールした後にクローンを実行すると内部で保持する ID に不整合が起きてしまい、クラスタ作成に支障があるため、  
Nested ESXi を作成する場合は ESXi をインストールする前にベースの ESXi 用仮想マシンをクローンしてください。

## VMRC で Nested ESXi に接続して ESXi のインストール

作成した仮想マシンをパワーオンして、VMRC を開きます。

<img src="./images/05_NestedESXi13.png" width="80%">

VMRC のメニューから「取り外し可能デバイス」 > 「CD/DVD ドライブ」 > 「ディスクイメージファイル(iso)に接続」 を選択し、  
[製品評価センター](https://www.vmware.com/jp/try-vmware.html) からダウンロードした ESXi の ISO イメージファイルをマウントします。

<img src="./images/05_NestedESXi14.png" width="80%">

ISO イメージを最初からロードするように "Ctrl + Alt + Del" を送ります。

※ VMRC を経由して仮想マシンに "Ctrl + Alt + Del" を送りたいとき
VMRC の画面上の "Ctrl + Alt + Del" ボタンをクリックする他、  
"Ctrl + Alt + Insert" を押下する事でも同じ事が可能です。

<img src="./images/05_NestedESXi15.png" width="80%">

ESXi のインストーラがロードされますので、この後は通常の ESXi のインストールと同じです。

<img src="./images/05_NestedESXi16.png" width="80%">

インストールが完了したら ESXi の ISO イメージファイルは切断します。

<img src="./images/05_NestedESXi17.png" width="80%">

仮想マシンとして ESXi が起動している事が確認できます。

<img src="./images/05_NestedESXi18.png" width="80%">

NIC や SCSI コントローラ、ドライブを追加する際は仮想マシンアイコンを右クリックするか、
画面上の「アクション」ボタンをクリックし「設定の編集」をクリックします。

<img src="./images/05_NestedESXi19.png" width="80%">

デバイスを追加する際は右上の「新規デバイスの追加」をクリックします。

<img src="./images/05_NestedESXi20.png" width="80%">

Nested ESXi 上では NIC を冗長化した構成を作ろうと思うので「新規デバイスの追加」 > 「ネットワークアダプタ」をクリックします。

<img src="./images/05_NestedESXi21.png" width="80%">

一つ目の NIC と同じく、アダプタタイプを "VMXNET3" を選択します。

<img src="./images/05_NestedESXi22.png" width="80%">

続いて vSAN 用ドライブを明示的に分けて接続する SCSI コントローラを追加します。  
「新規デバイスの追加」 > 「SCSI コントローラ」をクリックします。

<img src="./images/05_NestedESXi23.png" width="80%">

"VMware 準仮想化" であることを確認します。
ここでいったん「OK」を押して構成変更を確定します。

<img src="./images/05_NestedESXi24.png" width="80%">

再度、仮想マシンの「設定の編集」をクリックし、今度はハードディスクを追加します。


<img src="./images/05_NestedESXi25.png" width="80%">

ディスクプロビジョニングは容量節約のために「シンプロビジョニング」を選びます。

<img src="./images/05_NestedESXi27.png" width="80%">

ハードディスクを追加したら「仮想デバイスノード」を新規追加した "SCSI コントローラ 1" に変更します。

<img src="./images/05_NestedESXi26.png" width="80%">



今回 vSAN クラスタを作りたいので、キャッシュ用ドライブを 80GB、キャパシティ用ドライブを 300GB でそれぞれ 1本ずつ ESXi ホストに追加します。

<img src="./images/05_NestedESXi28.png" width="80%">

追加したドライブが SCSI コントローラ1 にある事を確認します。
"SCSI 1:0 ハードディスクXX" と仮想デバイスノードが表示されていれば OK です。

<img src="./images/05_NestedESXi30.png" width="80%">


追加のハードディスクの設定が完了したら続いて Nested ESXi からハードディスクが「SSD」として認識出来る様に設定します。  

※ Intel NUC の様にローカル接続の SSD を利用したデータストアの場合は以降の設定を入れなくても SSD として認識されている場合がありますので、ESXi の Host Client で確認可能です(後述)。

ハードディスクとして認識されている時は ESXi の Host Client で確認するとディスクタイプが以下の様に「ディスク」と表示されています。

<img src="./images/05_NestedESXi38.png" width="80%">

SSD として認識されている時は以下の様に「ディスク（SSD）」となっています。

<img src="./images/05_NestedESXi35.png" width="80%">


仮想マシン「設定の編集」 > 「仮想マシンオプション」 を開き、下の方にスクロールすると  
「詳細」メニューの下に「構成パラメータ」 > 「設定の編集」 メニューがクリック出来るので開きます。
<img src="./images/05_NestedESXi31.png" width="80%">

<img src="./images/05_NestedESXi32.png" width="80%">

右上の「新規パラメータの追加」を vSAN 用に追加したドライブ分クリックします。

<img src="./images/05_NestedESXi33.png" width="80%">

パラメータの追加フィールドに先程の仮想ドライブの SCSI 番号 「SCSI 1:0 」等を
```
scsiX:Y.virtualSSD  :  TRUE
```

の形式で入力します。

今回は2つのドライブを追加しているので
```
scsi1:0.virtualSSD : TRUE
scsi1:1.virtualSSD : TRUE
```
を入力しています。

<img src="./images/05_NestedESXi34.png" width="80%">

設定を完了し、Nested ESXi の仮想マシンを再起動すると設定が反映され、Nested ESXi 上のドライブが SSD として認識されているはずです。

<img src="./images/05_NestedESXi35.png" width="80%">


## クラスタ用に Nested ESXi を複数台作成する。

事前にクローンしておいた Nested ESXi 用仮想マシンに同様に ESXi をインストールして、  
最初の Nested ESXi と同じ構成のものを3台用意します。


### VMRC 画面操作から外部操作に戻すとき
VMware Tools がインストールされていない VM を VMRC で接続して操作していると画面の中からマウスが外に出せなくなります。
その時は "Ctrl + Alt" キーを押下してください。


### VMRC を経由して仮想マシンに "Ctrl + Alt + Del" を送りたいとき
VMRC の画面上の "Ctrl + Alt + Del" ボタンをクリックする他、"Ctrl + Alt + Insert" を押下する事でも同じ事が可能です。


次は導入した Nested ESXi を複数台組み合わせた **[vSphere Cluster の作成](./06_vsphere_cluster.md)** です。
