---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-16"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Red Hat Enterprise Linux でのフルディスク暗号化のための LUKS の使用

Linux Unified Key Setup-on-disk-format (LUKS) を使用すると、Red Hat Enterprise Linux 6 (サーバー) 上のパーティションを暗号化できます。このことは、モバイル・コンピューターおよび取り外し可能メディアでは特に重要です。 LUKS を使用すると、パーティションのバルク暗号化に使用されたマスター鍵を、複数のユーザー鍵で暗号化解除できます。

## LUKS にある機能

- ブロック・デバイス全体を暗号化するので、取り外し可能ストレージ・メディアやラップトップ・ディスク・ドライブなどのモバイル・デバイスの内容を保護するのに適している。
    - 暗号化ブロック・デバイスは、基礎となる内容が任意なので、スワップ・デバイスの暗号化に役立ちます。 暗号化は、データ・ストレージ用の特殊なフォーマットのブロック・デバイスを使用する特定のデータベースにも役立ちます。
- 既存のデバイス・マッパー・カーネル・サブシステムを使用する。
- パスフレーズを強化して、辞書攻撃から保護する。
- LUKS デバイスは複数の鍵スロットを含んでいるため、ユーザーはバックアップ鍵やパスフレーズを追加できる。


## LUKS にない機能

- 多数 (8 人を超える) のユーザーが同じデバイスに対して異なるアクセス・キーを持つことを必要とするアプリケーションを許可する。
- ファイル・レベルの暗号化を必要とするアプリケーションを処理する ([詳細情報](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Encryption.html){:new_window})。

## Endurance {{site.data.keyword.blockstorageshort}}を使用した新規の LUKS 暗号化ボリュームをセットアップする方法

以下のステップでは、フォーマット設定もマウントもされていない新規の非暗号化 {{site.data.keyword.blockstoragefull}} ボリュームに対し、サーバーが既にアクセス権限を持っていることを想定しています。 Linux で{{site.data.keyword.blockstorageshort}}にアクセスする方法については、[ここ](accessing_block_storage_linux.html) をクリックしてください。

データ暗号化を実行すると、ホストに負荷がかかり、パフォーマンスに影響する可能性があることに注意してください。

1. root としてシェル・プロンプトに以下を入力し、必要なパッケージをインストールします。   <br/>
   ```
   # yum install cryptsetup-luks
   ```
   {: pre}
2. ディスク ID を取得します。<br/>
   ```
   # fdisk –l | grep /dev/mapper
   ```
   {: pre}
3. リスト内で該当するボリュームを見つけます。
4. ブロック・デバイスを暗号化します。 

   1. 次のコマンドは、ボリュームを初期化し、パスフレーズを設定できるようにします。 <br/>
   
      ```
      # cryptsetup -y -v luksFormat /dev/mapper/3600a0980383034685624466470446564
      ```
      {: pre}
      
   2. YES (すべて大文字) で応答します。
   
   3. これにより、デバイスが暗号化されたボリュームとして表示されます。 
   
      ```
      # blkid | grep LUKS
      /dev/mapper/3600a0980383034685624466470446564: UUID="46301dd4-035a-4649-9d56-ec970ceebe01" TYPE="crypto_LUKS"
      ```
      
5. ボリュームを開き、マッピングを作成します。   <br/>
   ```
   # cryptsetup luksOpen /dev/mapper/3600a0980383034685624466470446564 cryptData
   ```
   {: pre}
6. 前に指定したパスワードを入力します。
7. 以下のようにして、マッピングを検証し、暗号化されたボリュームの状況を表示します。   <br/>
   ```
   # cryptsetup -v status cryptData
   /dev/mapper/cryptData is active.
     type:  LUKS1
     cipher:  aes-cbc-essiv:sha256
     keysize: 256 bits
     device:  /dev/mapper/3600a0980383034685624466470446564
     offset:  4096 sectors
     size:    41938944 sectors
     mode:    read/write
     Command successful
   ```
8. /dev/mapper/cryptData 暗号化デバイスにランダム・データを書き込みます。 これにより、外部からはこのデータがランダムなデータとして見えるようになります。それは、データの使用パターンが開示されないように保護されることを意味します。 このステップには少し時間がかかる場合があることに注意してください。<br/>
    ```
    # shred -v -n1 /dev/mapper/cryptData
    ```
    {: pre}
9. ボリュームをフォーマット設定します。<br/>
   ```
   # mkfs.ext4 /dev/mapper/cryptData
   ```
   {: pre}
10. ボリュームをマウントします。<br/>
   ```
   # mkdir /cryptData
   ```
   {: pre}
   ```
   # mount /dev/mapper/cryptData /cryptData
   ```
   {: pre}
   ```
   # df -H /cryptData
   ```
   {: pre}

### 暗号化されたボリュームを安全にアンマウントし、クローズする方法
   ```
   # umount /cryptData
   # cryptsetup luksClose cryptData
   ```
   {: codeblock}

### 既存の LUKS 暗号化パーティションを再マウントおよびマウントする方法
   ```
   # cryptsetup luksOpen /dev/mapper/3600a0980383034685624466470446564 cryptData
      前に指定したパスワードを入力します。
   # mount /dev/mapper/cryptData /cryptData
   # df -H /cryptData
   # lsblk
   NAME                                         MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
   xvdb                                         202:16   0    2G  0 disk
   └─xvdb1                                    202:17   0    2G  0 part  [SWAP]
   xvda                                         202:0    0   25G  0 disk
   ├─xvda1                                    202:1    0  256M  0 part  /boot
   └─xvda2                                    202:2    0 24.8G  0 part  /
   sda                                          8:0      0   20G  0 disk
   └─3600a0980383034685624466470446564 (dm-0) 253:0    0   20G  0 mpath
   └─cryptData (dm-1)                         253:1    0   20G  0 crypt /cryptData
   sdb                                          8:16     0   20G  0 disk
   └─3600a0980383034685624466470446564 (dm-0) 253:0    0   20G  0 mpath
   └─cryptData (dm-1)                         253:1    0   20G  0 crypt /cryptData　
   ```
