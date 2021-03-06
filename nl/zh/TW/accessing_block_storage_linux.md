---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-09"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 在 Linux 上連接至 MPIO iSCSI LUN

這些指示適用於 RHEL6/Centos6。如果您是使用其他 Linux 作業系統，請參閱特定發行套件的文件以取得配置資訊，並確保多路徑針對路徑優先順序支援 ALUA。

開始之前，請確定已透過 [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window} 授權正在存取 {{site.data.keyword.blockstoragefull}} 磁區的主機：

1. 從 {{site.data.keyword.blockstorageshort}} 清單頁面中，按一下與剛佈建的磁區相關聯的**動作**。
2. 按一下**授權主機**。
3. 從清單中選取所需的主機，然後按一下**提交**；這會授權主機存取磁區。

## 裝載 {{site.data.keyword.blockstorageshort}} 磁區

以下是將 Linux 型「{{site.data.keyword.BluSoftlayer_full}} 運算」實例連接至多路徑輸入/輸出 (MPIO)「網際網路小型電腦系統介面 (iSCSI)」邏輯裝置號碼 (LUN) 所需的步驟。

我們的範例是以 **Red Hat Enterprise Linux 6** 為基礎。您應該根據作業系統 (OS) 供應商文件來調整其他 Linux 發行套件的步驟。我們已為其他 OS 新增附註，但本文件**並未**涵蓋所有 Linux 發行套件。例如，若為 Ubuntu，請按一下[這裡](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html){:new_window:}，以取得「iSCSI 起始器配置」指示，並按一下[這裡](https://help.ubuntu.com/lts/serverguide/multipath-setting-up-dm-multipath.html){:new_window}，以取得 DM-Multipath 設定的相關資訊。

**附註：**指示中所參照的「主機 IQN」、使用者名稱、密碼及目標位址，可從 [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window} 的 **{{site.data.keyword.blockstorageshort}} 詳細資料**畫面中取得。

**附註：**我們建議的最佳作法是在略過防火牆的 VLAN 上執行儲存空間資料流量。透過軟體防火牆執行儲存空間資料流量，將會增加延遲，而且對儲存空間效能有不利的影響。

1. 將 iSCSI 及多路徑公用程式安裝至主機：
   - RHEL/CentOS：

   ```
   yum install iscsi-initiator-utils device-mapper-multipath
   ```
   {: pre}

   - Ubuntu/Debian：

   ```
   sudo apt-get update
   sudo apt-get install multipath-tools
   ```
   {: pre}

2. 建立或編輯您的多路徑配置檔。
   - 使用下列指令中所提供的最小配置，編輯 **/etc/multipath.conf**。<br /><br /> **附註：**請注意，對於 RHEL7/CentOS7，`multipath.conf` 可以是空白，因為 OS 具有內建配置。Ubuntu 不會使用 multipath.conf，因為它是內建在多路徑工具中。

   ```
   defaults {
   user_friendly_names no
   max_fds max
   flush_on_last_del yes
   queue_without_daemon no
   dev_loss_tmo infinity
   fast_io_fail_tmo 5
   }
   # All data under blacklist must be specific to your system.
   blacklist {
   wwid "SAdaptec*"
   devnode "^hd[a-z]"
   devnode "^(ram|raw|loop|fd|md|dm-|sr|scd|st)[0-9]*"
   devnode "^cciss.*"
   }
   devices {
   device {
   vendor "NETAPP"
   product "LUN"
   path_grouping_policy group_by_prio
   features "3 queue_if_no_path pg_init_retries 50"
   prio "alua"
   path_checker tur
   failback immediate
   path_selector "round-robin 0"
   hardware_handler "1 alua"
   rr_weight uniform
   rr_min_io 128
   }
   }
   ```
   {: codeblock}

3. 載入多路徑模組、啟動多路徑服務，並將其設為在開機時啟動。
   - RHEL 6：
     ```
     modprobe dm-multipath
     ```
     {: pre}

     ```
     service multipathd start
     ```
     {: pre}

     ```
     chkconfig multipathd on
     ```
     {: pre}
   
   - CentOS 7： 
     ```
     modprobe dm-multipath
     ```
     {: pre}

     ```
     systemctl start multipathd
     ```
     {: pre}

     ```
     systemctl enable multipathd
     ```
     {: pre}
     
   - Ubuntu：
     ```
     service multipath-tools start
     ```
     {: pre}
    
   - 若為其他發行套件，請參閱 OS 供應商文件。

4. 驗證多路徑正在運作中。
   - RHEL 6：
     ```
     multipath -l
     ```
     {: pre}
     
     如果此時傳回空白，則表示它正在運作中。 
   
   - CentOS 7：
     ```
     multipath -ll
     ```
     {: pre}
     
     RHEL 7/CentOS 7 可能傳回「無 fc_host 裝置」，可以忽略此訊息。 

5. 使用來自 {{site.data.keyword.slportal}} 的 IQN 更新 **/etc/iscsi/initiatorname.iscsi** 檔案。請以小寫字體輸入此值。
   ```
   InitiatorName=<value-from-the-Portal>
   ```
   {: pre}
6. 使用來自 {{site.data.keyword.slportal}} 的使用者名稱及密碼，編輯 **/etc/iscsi/iscsid.conf** 中的 CHAP 設定。請對 CHAP 名稱使用大寫字體。
   ```
    node.session.auth.authmethod = CHAP
    node.session.auth.username = <Username-value-from-Portal>
    node.session.auth.password = <Password-value-from-Portal>
    discovery.sendtargets.auth.authmethod = CHAP
    discovery.sendtargets.auth.username = <Username-value-from-Portal>
    discovery.sendtargets.auth.password = <Password-value-from-Portal>
   ```
   {: codeblock}

   **附註：**請將其他 CHAP 設定保持註解狀態。{{site.data.keyword.BluSoftlayer_full}} 儲存空間僅會使用單向鑑別。

7. 將 iSCSI 設為在開機時啟動，並在此時啟動。
   - RHEL 6：

      ```
      chkconfig iscsi on
      ```
      {: pre}
      ```
      chkconfig iscsid on
      ```
      {: pre}

      ```
      service iscsi start
      ```
      {: pre}

      ```
      service iscsid start
      ```
      {: pre}

   - CentOS 7：

      ```
      systemctl enable iscsi
      ```
      {: pre}

      ```
      systemctl enable iscsid
      ```
      {: pre}

      ```
      systemctl start iscsi
      ```
      {: pre}

      ```
      systemctl start iscsid
      ```
      {: pre}

   - 其他發行套件：請參閱 OS 供應商文件。
   
8. 使用從 {{site.data.keyword.slportal}} 取得的「目標」IP 位址來探索裝置。

     a. 針對 iSCSI 陣列執行探索：
     ```
     iscsiadm -m discovery -t sendtargets -p <ip-value-from-SL-Portal>
     ```
     {: pre}

     b. 將主機設為自動登入 iSCSI 陣列：
     ```
     iscsiadm -m node -L automatic
     ```
     {: pre}

9. 驗證主機已登入 iSCSI 陣列，並維護其階段作業。
   ```
   iscsiadm -m session
   ```
   {: pre}

   ```
   multipath -l
   ```
   {: pre}

   此時這應該會報告路徑。

10. 驗證裝置已連接。依預設，裝置將連接至 /dev/mapper/mpathX，其中 X 是所連接裝置產生的 ID。
    ```
    fdisk -l | grep /dev/mapper
    ```
    {: pre}
  應該會報告類似下列內容：
    ```
    Disk /dev/mapper/3600a0980383030523424457a4a695266: 73.0 GB, 73023881216 byte
    ```
  磁區現在已裝載且可在主機上存取。

## 建立檔案系統（選用）

以下是在新裝載的磁區之上建立檔案系統的步驟。大部分應用程式都需要檔案系統，才能利用磁區。請對小於 2 TB 的磁碟使用 **fdisk**，而對大於 2 TB 的磁碟使用 **parted**。

### fdisk

1. 取得磁碟名稱。
   ```
   fdisk -l | grep /dev/mapper
   ```
   {: pre}
   傳回的磁碟名稱應該類似於 /dev/mapper/XXX。

2. 使用 fdisk 在磁碟上建立分割區。

   ```
   fdisk /dev/mapper/XXX
   ```
   {: pre}

   XXX 代表步驟 1 中傳回的磁碟名稱。<br />

   **附註**：進一步向下捲動，以取得此區段下 Fdisk 指令表格中列出的指令程式碼。

3. 在新的分割區上建立檔案系統。

   ```
   fdisk -l /dev/mapper/XXX
   ```
   {: pre}

   - 新的分割區應該與磁碟一起列出，類似於 XXXlp1，後面接著大小、類型 (83) 及 Linux。
   - 記下分割區名稱，您將在下一步中需要它。（XXXlp1 代表分割區名稱。）
   - 建立檔案系統：

     ```
     mkfs.ext3 /dev/mapper/XXXlp1
     ```
     {: pre}

4. 為檔案系統建立裝載點，並裝載它。
   - 建立一個分割區名稱 PerfDisk，或您要裝載檔案系統的位置：

     ```
     mkdir /PerfDisk
     ```
     {: pre}

   - 使用分割區名稱裝載儲存空間：
     ```
     mount /dev/mapper/XXXlp1 /PerfDisk
     ```
     {: pre}

   - 確認您在清單中看到新的檔案系統：
     ```
     df -h
     ```
     {: pre}

5. 將新的檔案系統新增至系統的 **/etc/fstab** 檔案，以在開機時啟用自動裝載。
   - 將下行附加至 **/etc/fstab** 的底端（使用步驟 3 中的分割區名稱）。<br />

     ```
     /dev/mapper/XXXlp1    /PerfDisk    ext3    defaults,_netdev    0    1
     ```
     {: pre}

#### Fdisk 指令表格
<table border="0" cellpadding="0" cellspacing="0">
 <tbody>
	<tr>
		<td style="width:40%;"><div>指令</div></td>
		<td style="width:60%;">結果</td>
	</tr>
	<tr>
		<td><li>&#42; <code>Command: n</code></li>	</td>
		<td>建立新的分割區。</td>
	</tr>
	<tr>
		<td><li><code>Command action: p</code></li></td>
		<td>使分割區成為主要分割區。</td>
	</tr>
	<tr>
		<td><li><code>Partition number (1-4): 1</code></li></td>
		<td>變成磁碟上的分割區 1。</td>
	</tr>
	<tr>
		<td><li><code>First cylinder (1-8877): 1 (default)</code></li></td>
		<td>從磁柱 1 開始。</td>
	</tr>
	<tr>
		<td><li><code>Last cylinder, +cylinders or +size {K, M, G}: 8877 (default)</code></li></td>
		<td>按 Enter 鍵以移至最後一個磁柱。</td>
	</tr>
	<tr>
		<td><li>&#42; <code>Command: t</code></li></td>
		<td>設定分割區的類型。</td>
	</tr>
	<tr>
		<td><li><code>Select partition 1.</code></li></td>
		<td>選取要設為特定類型的分割區 1。</td>
	</tr>
	<tr>
		<td><li>&#42;&#42; <code>Hex code: 83</code></li></td>
		<td>選取 Linux 作為「類型」（83 是適用於 Linux 的十六進位碼）。</td>
	 </tr>
	<tr>
		<td><li>&#42; <code>Command: w</code></li></td>
		<td>將新的分割區資訊寫入磁碟中。</td>
	</tr>
 </tbody>
</table>

  (`*`) 鍵入 m 以取得「說明」。

  (`**`) 鍵入 L 以列出十六進位碼。

### parted

在許多 Linux 發行套件上，會預先安裝 **parted**。若其未包含在您的發行套件中，則可以使用下列方式來安裝它：
- Debian/Ubuntu
  ```
  sudo apt-get install parted
  ```
  {: pre}

- RHEL/CentOS：
  ```
  yum install parted
  ```
  {: pre}


若要使用 **parted** 建立檔案系統，請遵循下列步驟：

1. 執行 parted。

   ```
   parted
   ```
   {: pre}

2. 在磁碟上建立分割區。
   1. 除非另有指定，否則 parted 會使用您的主要磁碟機，在大部分情況下，指的是 **/dev/sda**。使用 **select** 指令，切換至您要分割的磁碟。將 **XXX** 取代為新的裝置名稱。

      ```
      (parted) select /dev/mapper/XXX
      ```
      {: pre}

   2. 執行 **print**，以確認您位在正確的磁碟上。

      ```
      (parted) print
      ```
      {: pre}

   3. 建立新的 GPT 分割區表格。 
   
      ```
      (parted) mklabel gpt
      ```
      {: pre}

   4. Parted 可以用來建立主要及邏輯磁碟分割區，涉及的步驟相同。若要建立新的分割區，parted 會使用 **mkpart**。您可以為它提供其他參數，如 **primary** 或 **logical**，視您想要建立的分割區類型而定。
   <br /> **附註**：列出的單位預設為百萬位元組 (MB)。若要建立 10 GB 分割區，您應該從 1 開始並以 10000 結束。想要的話，您也可以輸入 `(parted) unit TB`，將大小單位變更為兆位元組 (TB)。

      ```
      (parted) mkpart
      ```
      {: pre}

   5. 使用 **quit** 來結束 parted。

      ```
      (parted) quit
      ```
      {: pre}

3. 在新的分割區上建立檔案系統。

   ```
   mkfs.ext3 /dev/mapper/XXXlp1
   ```
   {: pre}

   **附註**：在執行上述指令時，務必選取正確的磁碟及分割區！請列印分割區表格來驗證結果。在檔案系統直欄下，您應該會看到 ext3。

4. 為檔案系統建立裝載點，並裝載它。
   - 建立一個分割區名稱 PerfDisk，或您要裝載檔案系統的位置：

     ```
     mkdir /PerfDisk
     ```
     {: pre}

   - 使用分割區名稱裝載儲存空間：

     ```
     mount /dev/mapper/XXXlp1 /PerfDisk
     ```
     {: pre}

   - 確認您在清單中看到新的檔案系統：

     ```
     df -h
     ```
     {: pre}

5. 將新的檔案系統新增至系統的 **/etc/fstab** 檔案，以在開機時啟用自動裝載。
   - 將下行附加至 **/etc/fstab** 的底端（使用步驟 3 中的分割區名稱）。<br />

     ```
     /dev/mapper/XXXlp1    /PerfDisk    ext3    defaults    0    1
     ```
     {: pre}




## 如何驗證是否在 *NIX OS 中正確地設置 MPIO

若要檢查多路徑是否正在挑選裝置，則應該只會顯示 NETADP 裝置，且應該有兩個。

```
# multipath -l
```
{: pre}

```
root@server:~# multipath -l
3600a09803830304f3124457a45757067 dm-1 NETAPP,LUN C-Mode size=20G features='1 queue_if_no_path' hwhandler='0' wp=rw
|-+- policy='round-robin 0' prio=-1 status=active`
6:0:0:101 sdd 8:48 active undef running `-+- policy='round-robin 0' prio=-1 status=enabled`
7:0:0:101 sde 8:64 active undef running
```

確認磁碟已存在，且應該有兩個具有相同 ID 的磁碟，以及一個大小相同且具有相同 ID 的 /dev/mapper 清單。/dev/mapper 裝置是多路徑設定的裝置：

```
# fdisk -l | grep Disk
```
{: pre}

```
root@server:~# fdisk -l | grep Disk
Disk /dev/sda: 500.1 GB, 500107862016 bytes Disk identifier: 0x0009170d
Disk /dev/sdc: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/sdb: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/mapper/3600a09803830304f3124457a45757066: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
```

如果未正確設定，它看起來如下：
```
No multipath output root@server:~# multipath -l root@server:~#
```

這會顯示列入黑名單的裝置：
```
# multipath -l -v 3 | grep sd <date and time>
```
{: pre}

```
root@server:~# multipath -l -v 3 | grep sd Feb 17 19:55:02
| sda: device node name blacklisted Feb 17 19:55:02
| sdb: device node name blacklisted Feb 17 19:55:02
| sdc: device node name blacklisted Feb 17 19:55:02
| sdd: device node name blacklisted Feb 17 19:55:02
| sde: device node name blacklisted Feb 17 19:55:02
```

fdisk 只會顯示 `sd*` 裝置，不會顯示 `/dev/mapper`

```
# fdisk -l | grep Disk
```
{: pre}

```
root@server:~# fdisk -l | grep Disk
Disk /dev/sda: 500.1 GB, 500107862016 bytes Disk identifier: 0x0009170d
Disk /dev/sdc: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/sdb: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
```
