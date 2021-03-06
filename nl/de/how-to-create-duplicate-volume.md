---

copyright:
  years: 2014, 2017
lastupdated: "2017-10-09"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# Duplikat eines Blockdatenträgers erstellen

{{site.data.keyword.BluSoftlayer_full}} bietet die Möglichkeit, ein Duplikat eines vorhandenen {{site.data.keyword.blockstoragefull}} zu erstellen. Das Duplikat übernimmt standardmäßig die Kapazitäts- und Leistungsoptionen der Original-LUN bzw. des Originaldatenträgers und enthält bis zum Zeitpunkt eines Snapshots eine Kopie der Daten.   

Da das Duplikat auf den Daten eines Snapshots zu einem bestimmten Zeitpunkt basiert, wird ein Snapshotbereich auf dem Originaldatenträger benötigt, damit das Duplikat erstellt werden kann.  Weitere Informationen zu Snapshots und zum Bestellen von Snapshotbereichen finden Sie in der [Snapshotdokumentation](snapshots.html).  

Duplikate können sowohl für den Primär- als auch für den Replikatdatenträger erstellt werden, wobei das neue Duplikat in demselben Rechenzentrum wie der Originaldatenträger erstellt wird.  Wenn Sie beispielsweise ein Duplikat aus einem Replikatdatenträger erstellen, wird der neue Datenträger in demselben Rechenzentrum erstellt wie der Replikatdatenträger.    

Der Lese- und Schreibzugriff auf duplizierte Datenträger kann durch einen Host erfolgen, sobald der Speicher bereitgestellt wurde.  Snapshots und Replikation sind dagegen erst zulässig, nachdem die Daten vollständig vom Original auf das Duplikat kopiert wurden. 

Sobald die Datenkopie abgeschlossen ist, kann das Duplikat als komplett vom Original unabhängiger Datenträger verwaltet und verwendet werden. 

Diese Funktion ist nur für Speicher verfügbar, der mit Verschlüsselung bereitgestellt wird. Klicken Sie [hier](new-ibm-block-and-file-storage-location-and-features.html), um eine Liste mit den verfügbaren Rechenzentren anzuzeigen. 

Nachstehend einige gängige Anwendungen für duplizierte Datenträger:
- **Disaster-Recovery-Tests**: Erstellen Sie ein Duplikat Ihres Replikatdatenträgers, um zu überprüfen, ob die Daten intakt sind und im Fall einer Katastrophe ohne Unterbrechung der Replikation verwendet werden können. 
- **Goldene Kopie**: Verwenden Sie einen Speicherdatenträger als goldene Kopie, um damit mehrere Instanzen für verschiedene Anwendungen zu erstellen. 
- **Datenaktualisierung**: Erstellen Sie eine Kopie Ihrer Produktionsdaten, um diese zu Testzwecken an Ihre nicht für die Produktion verwendete Umgebung anzuhängen. 
- **Wiederherstellung aus Snapshot**: Stellen Sie Daten auf dem Originaldatenträger aus einem Snapshot wieder her, ohne den gesamten Originaldatenträger mit einer Snapshotwiederherstellungsfunktion zu überschreiben. 
- **Entwicklung/Test**: Erstellen Sie bis zu vier Duplikate eines Datenträgers gleichzeitig, um Datenträger mit duplizierten Daten zu Entwicklungs- und Testzwecken zu erstellen. 
- **Größenänderung des Speichers**: Erstellen Sie einen neuen Datenträger mit der neuen Größe und/oder IOPS, ohne eine hostbasierte Migration Ihrer Daten durchführen zu müssen.  
	

Nachstehend finden Sie einige Methoden zum Erstellen eines duplizierten Datenträgers über das [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}: 

## Vorgehensweise zum Erstellen eines Duplikats von einem bestimmen Datenträger in der Speicherliste

Navigieren Sie zu Ihrer {{site.data.keyword.blockstorageshort}}-Liste:

Klicken Sie im Kundenportal auf **Speicher**, **{{site.data.keyword.blockstorageshort}}** ODER klicken Sie im {{site.data.keyword.BluSoftlayer_full}}-Katalog auf **Infrastruktur -> Speicher -> {{site.data.keyword.blockstorageshort}}**. 


1. Wählen Sie in der Liste eine LUN aus und klicken Sie auf **Aktionen** -> **Duplizierte LUN (Datenträger)**. 
2. Wählen Sie Ihre Snapshotoption aus: 
    - Bei einer Bestellung über einen Nichtreplikatdatenträger:
      - Wählen Sie **Aus neuem Snapshot erstellen** aus. Damit wird ein neuer Snapshot erstellt, der für das Duplikat verwendet wird. Verwenden Sie diese Option, wenn gerade keine Snapshots für Ihren Datenträger vorhanden sind und Sie zu diesem Zeitpunkt ein Duplikat erstellen wollen.
    
      ODER 
      - Wählen Sie **Aus letztem Snapshot erstellen** aus. Damit wird ein Duplikat aus dem letzten für diesen Datenträger vorhandenen Snapshot erstellt. 
    - Bei einer Bestellung über einen Replikatdatenträger: Die einzige Option für Snapshot ist die Verwendung des letzten verfügbaren Snapshots. 
3. Speichertyp (Endurance oder Performance) und Position bleiben mit dem Originaldatenträger identisch.
4. Stündliche oder monatliche Abrechnung: Sie können wählen, ob die duplizierte LUN mit stündlicher oder monatlicher Abrechnung bereitgestellt wird.  Der Abrechnungstyp für den Originaldatenträger wird automatisch ausgewählt. Wenn Sie jedoch für Ihren neuen duplizierten Speicher einen anderen Abrechnungstyp wählen wollen, können Sie das hier tun. 
5. Bei Bedarf können Sie IOPS oder ein IOPS-Tier für den neuen Datenträger angeben. Die IOPS-Bezeichnung wird für den Originaldatenträger standardmäßig festgelegt. 
    - Wenn Ihr Originaldatenträger ein Endurance-Tier mit 0,25 IOPS ist, können Sie keine neue Auswahl treffen. 
    - Wenn Ihr Originaldatenträger ein Endurance-Tier mit 2, 4 oder 10 IOPS ist, können Sie für den neuen Datenträger einen beliebigen Wert zwischen diesen Tiers auswählen. 
    - Die verfügbaren Kombinationen aus Leistung und Größe werden angezeigt. 
6. Bei Bedarf können Sie die Größe des neuen Datenträgers aktualisieren, sodass er größer als der Originaldatenträger ist.  Die Größe des Originaldatenträgers wird standardmäßig festgelegt. 
    - **Hinweis**: {{site.data.keyword.blockstorageshort}} kann nur auf die 10-fache Originalgröße des Datenträgers vergrößert werden. 
7. Bei Bedarf können Sie den Snapshotbereich für den neuen Datenträger aktualisieren und mehr, weniger oder keinen Snapshotbereich hinzufügen. Der Snapshotbereich des Originaldatenträgers wird standardmäßig festgelegt. 
8. Klicken Sie auf **Weiter**, um Ihre Bestellung des Duplikats abzusetzen. 



## Vorgehensweise zum Erstellen eines Duplikats von einem bestimmen Snapshot

Navigieren Sie zu Ihrer {{site.data.keyword.blockstorageshort}}-Liste:

1. Klicken Sie in der Liste auf **eine LUN / einen Datenträger**, um die Detailseite anzuzeigen. (Es kann sich um einen Replikatdatenträger oder einen Nichtreplikatdatenträger handeln.) 
2. Blättern Sie abwärts, wählen Sie in der Liste auf der Detailseite einen vorhandenen Snapshot aus und klicken Sie auf **Aktionen -> Duplikat**.   
3. Speichertyp (Endurance oder Performance) und Position bleiben mit dem Originaldatenträger identisch. 
4. Bei Bedarf können Sie IOPS oder ein IOPS-Tier für den neuen Datenträger angeben. Die IOPS-Bezeichnung wird für den Originaldatenträger standardmäßig festgelegt. 
    - Wenn Ihr Originaldatenträger ein Endurance-Tier mit 0,25 IOPS ist, können Sie keine neue Auswahl treffen. 
    - Wenn Ihr Originaldatenträger ein Endurance-Tier mit 2, 4 oder 10 IOPS ist, können Sie für den neuen Datenträger einen beliebigen Wert zwischen diesen Tiers auswählen. 
    - Die verfügbaren Kombinationen aus Leistung und Größe werden angezeigt. 
5. Bei Bedarf können Sie die Größe des neuen Datenträgers aktualisieren, sodass er größer als der Originaldatenträger ist.  Die Größe des Originaldatenträgers wird standardmäßig festgelegt. 
    - **Hinweis**: {{site.data.keyword.blockstorageshort}} kann nur auf die 10-fache Originalgröße des Datenträgers vergrößert werden. 
6. Bei Bedarf können Sie den Snapshotbereich für den neuen Datenträger aktualisieren und mehr, weniger oder keinen Snapshotbereich hinzufügen. Der Snapshotbereich des Originaldatenträgers wird standardmäßig festgelegt. 
7. Klicken Sie auf **Weiter**, um Ihre Bestellung des Duplikats abzusetzen. 


## Vorgehensweise zum Verwalten Ihres duplizierten Datenträgers

Während die Daten vom Originaldatenträger auf das Duplikat kopiert werden, wird auf der Detailseite der Status angezeigt, der angibt, dass die Duplizierung in Bearbeitung ist. In dieser Zeit können Sie eine Verbindung zu einem Host herstellen, von dem Datenträger lesen und auf ihn schreiben, jedoch keine Snapshotpläne erstellen. Sobald der Duplizierungsprozess abgeschlossen ist, ist der neue Datenträger völlig unabhängig vom Originaldatenträger und kann mit Snapshots, Replikation etc. ganz normal verwaltet werden. 
