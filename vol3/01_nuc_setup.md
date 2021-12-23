1. [メインへ](./README.md)
2. [NUC の初期設定](./01_nuc_setup.md)
3. [ESXi インストーラの作成](./02_esxi_custom_installer.md)
4. [ESXi のインストールと基本設定](./03_esxi_setup.md)
5. [vCenter のデプロイと基本設定](./04_vcenter_setup.md)
6. [Nested ESXi のデプロイ](./05_nested_esxi.md)
7. [vSphere Cluster の作成](./06_vsphere_cluster.md)
8. [自宅 vSphere らぼの Expert をご紹介](./07_expert.md)


# NUC の初期設定

## BIOS 基本設定
NUC にメモリ、SSD 等を組み込みディスプレイとキーボードを接続して起動してみましょう。
起動直後に F2 キーを押下することで BIOS 設定メニューに入ります。

以下、実際の Intel 11th NUC の BIOS 設定画面からの情報を例に設定値をまとめています。

ESXi では NUC の "Audio"、"Microphone"、"WLAN (WirelessLAN)"、"Bluetooth"、"SD
Card" 等は利用しないので無効化します。

```
Advanced > Onboard Devices > HD Audio	: Disabled
Advanced > Onboard Devices > Audio DSP	: ☐
Advanced > Onboard Devices > Digital Microphone : ☐
Advanced > Onboard Devices > LAN		: Enabled
Advanced > Onboard Devices > Thunderbolt Support : Enabled
Advanced > Onboard Devices > WLAN		: ☐
Advanced > Onboard Devices > Bluetooth	: ☐
Advanced > Onboard Devices > Wireless Charging : ☐
Advanced > Onboard Devices > SDCard 3.0 Controller : ☐
Advanced > Onboard Devices > GNA		: ☐
Advanced > Onboard Devices > Enhanced Consumer IR : ☐
Advanced > Onboard Devices > HDMI CEC Control : ☐
Advanced > Onboard Devices > Boot NumLock State : Off
Advanced > Onboard Devices > Failsafe Watchdog : ☑

Cooling > Fan Control Mode		: Cool または Balanced
Cooling > Fan off capability		: ☑
```

また、CPU 設定などでは "Hyper-Threading"、"Intel Turbo Boost Technology"、仮想化支援機能の "Intel Virtualization Technology (Intel VT)"、 "Intel VT-d" などは vSphere ESXi として利用する際には必須・推奨な項目なので有効にします。

```
Performance > Processor > Hyper-Threading		: Enabled
Performance > Processor > Intel Turbo Boost Technology	: ☑
Performance > Processor > Active Processor Cores	: All
Performance > Processor > Debug Interface		: Disabled

Security > Security Features > Allow UEFI 3rd party driver loaded	: ☐
Security > Security Features > Intel Virtualization Technology	: Enabled
Security > Security Features > Intel VT for Directed I/O (VT-d)	: Enabled
Security > Security Features > Intel Platform Trust Technology	: ☐

Power > Max Performance Enabled			: ☑
Power > Intel Dynamic Power Technology		: Energy Efficient Perform
Power > Secondary Power Settings > After Power Failure	: Last State
Power > Secondary Power Settings > Wake on LAN from S4/S5	: Power On - Normal Boot

Boot > Secure Boot > Secure Boot 			: Disabled
```

これら設定で参考となる情報を vExpert の Florian さんが自身のブログにまとめてくださっているので併せてご参照ください。
[Intel NUC Recommended BIOS Settings for VMware ESXi](https://www.virten.net/2020/03/intel-nuc-recommended-bios-settings-for-vmware-esxi/)


## NUC BIOS Update

出荷状態の NUC は BIOS・Firmwear が古い状態で既知の不具合を含んでいる可能性が高いため、最新版がリリースされている場合は必ず更新しましょう。
今回利用している NUC11PAHi5 ならば [Intel のサポートサイト](https://via.vmw.com/EacX) から "BIOS アップデート" の ZIPファイルをダウンロード、含まれる拡張子 .cap ファイルを空の USB メモリなどにコピーします。
※ 必ずご利用のモデル名で Intel サポートサイトを検索して、適切なファイルをダウンロードして下さい。

<img src="./images/01_NUC_BIOS.png" width="80%">

NUC への更新ファイルの適用は NUC 起動直後に F7 キーを押下し、BIOS メンテナンスモードを起動、USB メモリにコピーした .cap ファイルを読み込ませて更新します。


次は **[ESXi インストーラの作成](./02_esxi_custom_installer.md)** です。