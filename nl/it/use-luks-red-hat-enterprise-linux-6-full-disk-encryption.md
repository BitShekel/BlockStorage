---

copyright:
  years: 2014, 2018
lastupdated: "2018-02-12"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Utilizzo di LUKS in Red Hat Enterprise Linux per la crittografia totale del disco

LUKS (Linux Unified Key Setup-on-disk-format) ti consente di crittografare le partizioni sul tuo Red Hat Enterprise Linux 6 (server), cosa particolarmente importante nel caso di computer mobili e supporti rimovibili. LUKS consente a più chiavi utente di decrittografare una chiave master che viene utilizzata per la crittografia di massa della partizione.

## Cosa fa LUKS:

- Crittografa interi dispositivi a blocchi ed è pertanto adatto per proteggere i contenuti di dispositivi mobili quali i supporti di archiviazione rimovibili o le unità disco dei laptop.
    - I contenuti sottostanti del dispositivo a blocchi crittografato sono arbitrari, rendendolo utile per la crittografia di dispositivi di swap. La crittografia può anche essere utile con specifici database che usano dispositivi a blocchi con una formattazione speciale per l'archiviazione di dati.
- Utilizza il sottosistema kernel di device mapper esistente.
- Fornisce il rafforzamento delle passphrase, che protegge dagli attacchi di dizionario.
- Consente agli utenti di aggiungere delle chiavi di backup o delle passphrase perché i dispositivi LUKS contengono più slot della chiave.


## Cosa non fa LUKS:

- Consente alle applicazioni che richiedono molti utenti (più di otto) di avere delle chiavi di accesso distinte agli stessi dispositivi.
- Opera con le applicazioni che richiedono la crittografia a livello di file, [ulteriori informazioni](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Encryption.html){:new_window}.

## Come configurare il nuovo volume con crittografia LUKS con {{site.data.keyword.blockstorageshort}} Endurance

Questa procedura presuppone che il server abbia già accesso a un nuovo volume {{site.data.keyword.blockstoragefull}} non crittografato che non è stato formattato o montato. Fai clic [qui](accessing_block_storage_linux.html) per informazioni su come accedere a {{site.data.keyword.blockstorageshort}} con Linux.

Nota: l'esecuzione della crittografia dei dati crea un carico sull'host che potrebbe, potenzialmente, avere un impatto sulle prestazioni.

1. Immetti quanto segue a un prompt della shell come root per installare il pacchetto richiesto:   <br/>
   ```
   # yum install cryptsetup-luks
   ```
   {: pre}
2. Ottieni l'ID disco:<br/>
   ```
   # fdisk –l | grep /dev/mapper
   ```
   {: pre}
3. Individua il tuo volume nell'elenco.
4. Crittografa il dispositivo a blocchi: 
      1. Questo comando inizializza il volume e ti consente di impostare una passphrase:<br/>
      ```
      # cryptsetup -y -v luksFormat /dev/mapper/3600a0980383034685624466470446564
      ```
      {: pre}
      
      2. Rispondi con YES (tutto in lettere maiuscole).
      
      3. Il dispositivo sarà ora visualizzato come un volume crittografato: 
      ```
      # blkid | grep LUKS
      /dev/mapper/3600a0980383034685624466470446564: UUID="46301dd4-035a-4649-9d56-ec970ceebe01" TYPE="crypto_LUKS"
      ```
      {: pre}
      
5. Apri il volume e crea un'associazione:   <br/>
   ```
   # cryptsetup luksOpen /dev/mapper/3600a0980383034685624466470446564 cryptData
   ```
   {: pre}
6. Immetti la password fornita precedentemente.
7. Verificare l'associazione e lo stato delle vista del volume crittografato: <br/>
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
8. Scrivi dati casuali sul dispositivo crittografato /dev/mapper/cryptData. Ciò garantisce che il mondo esterno li vedrà come dati casuali; questo significa che sono protetti dalla diffusione di modelli di utilizzo. Tieni presente che questo passo può richiedere del tempo.<br/>
    ```
    # shred -v -n1 /dev/mapper/cryptData
    ```
    {: pre}
9. Formatta il volume:<br/>
   ```
   # mkfs.ext4 /dev/mapper/cryptData
   ```
   {: pre}
10. Monta il volume:<br/>
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

### Come smontare e chiudere il volume crittografato in modo sicuro
   ```
   # umount /cryptData
   # cryptsetup luksClose cryptData
   ```
   {: codeblock}

### Come rimontare e montare una partizione con crittografia LUKS esistente
   ```
   # cryptsetup luksOpen /dev/mapper/3600a0980383034685624466470446564 cryptData
      Enter the password previously provided.
   # mount /dev/mapper/cryptData /cryptData
   # df -H /cryptData
   # lsblk
   NAME                                       MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
   xvdb                                       202:16   0    2G  0 disk
   └─xvdb1                                    202:17   0    2G  0 part  [SWAP]
   xvda                                       202:0    0   25G  0 disk
   ├─xvda1                                    202:1    0  256M  0 part  /boot
   └─xvda2                                    202:2    0 24.8G  0 part  /
   sda                                          8:0    0   20G  0 disk
   └─3600a0980383034685624466470446564 (dm-0) 253:0    0   20G  0 mpath
   └─cryptData (dm-1)                       253:1    0   20G  0 crypt /cryptData
   sdb                                          8:16   0   20G  0 disk
   └─3600a0980383034685624466470446564 (dm-0) 253:0    0   20G  0 mpath
   └─cryptData (dm-1)                       253:1    0   20G  0 crypt /cryptData
   ```
