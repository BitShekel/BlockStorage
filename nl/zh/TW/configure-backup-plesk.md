---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-16"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:pre: .pre}
 
# 利用 Plesk 配置 {{site.data.keyword.blockstorageshort}} 進行備份

在這篇文章中，我們旨在提供指示，以在 Plesk 中配置 {{site.data.keyword.blockstoragefull}} 進行備份。我們假設可以使用 root 或 sudo SSH 及完整管理層次 Plesk 存取權。此範例以 CentOS7 主機為基礎。

**附註**：您可以在[這裡](https://docs.plesk.com/en-US/12.5/administrator-guide/backing-up-and-restoration.59256/){:new_window}找到 Plesk 的備份及還原文件。

1. 透過 SSH 連接至主機。

2. 確定裝載點目標已存在。<br />
   **附註**：Plesk 有兩個用於儲存備份的選項，一個是內部 Plesk 儲存空間（位於 Plesk 伺服器上的備份儲存空間），另一個是外部 FTP 儲存空間（位於 Web 或本端網路中某個外部伺服器上的備份儲存空間）。通常，在 Plesk 機器上，內部備份儲存在 `/var/lib/psa/dumps` 中，並使用 `/tmp` 作為暫存目錄。在我們的範例中，我們將暫存目錄保留在本端，但將傾出目錄移至 STaaS 目標 (`/backup/psa/dumps`)。不需要任何 FTP 使用者認證。
   
3. 依照[在 Linux 上連接至 MPIO iSCSI LUN](accessing_block_storage_linux.html) 的說明，配置您的 {{site.data.keyword.blockstorageshort}}。將 {{site.data.keyword.blockstorageshort}} 裝載至 `/backup`，並配置 `/etc/fstab` 以啟用在開機時進行裝載。

4. **選用**：將現有備份複製到新的儲存空間。請使用 `rsync`，例如：
   ```
   rsync -avz /var/lib/psa/dumps /backup/psa/dumps
   ```
   {: pre}
    
    **附註**：此指令會傳輸壓縮的資料，同時保留儘可能多的內容（但硬式鏈結除外），並提供要傳送哪些檔案的相關資訊，也會在尾端附上簡短摘要。
    
5. 編輯 `/etc/psa/psa.conf`，以將 `DUMP_D` 值指向新目標。 
    -  應該顯示為：`DUMP_D /backup/psa/dumps`。 

6. **選用**：根據您的特定使用案例和商業需要，從伺服器中移除舊的儲存空間並從帳戶中取消。


