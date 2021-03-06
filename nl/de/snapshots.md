---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-16"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# Snapshots

Snapshots sind eine Funktion von {{site.data.keyword.blockstoragefull}}. Ein Snapshot stellt den Inhalt eines Datenträgers zu einem bestimmten Zeitpunkt dar. Snapshots geben Ihnen die Möglichkeit, Ihre Daten ohne Leistungsprobleme und mit minimalem Speicherverbrauch zu schützen und gelten als erste Verteidigungslinie beim Datenschutz. Mit der Snapshotfunktion können Daten schnell und bequem von einer Snapshotkopie wiederhergestellt werden, wenn ein Benutzer wichtige Daten auf einem Datenträger versehentlich ändert oder löscht.

{{site.data.keyword.blockstorageshort}} bietet Ihnen zwei Möglichkeiten, Snapshots zu machen - über einen konfigurierbaren Snapshotplan, der Snapshotkopien automatisch für die einzelnen Speicherdatenträger erstellt und löscht. Je nach Ihren Anforderungen können Sie auch weitere Snapshotpläne erstellen, Kopien manuell löschen und Pläne verwalten. Die zweite Möglichkeit besteht in einer manuellen Snapshotaufnahme.

Eine Snapshotkopie ist eine schreibgeschützte Abbildung einer {{site.data.keyword.blockstorageshort}}-LUN, die den Zustand des Datenträgers zu einem bestimmten Zeitpunkt erfasst. Snapshotkopien sind äußerst effizient, was die benötigte Zeit zur Erstellung und den Speicherplatz angeht. Die Erstellung einer {{site.data.keyword.blockstorageshort}}-Snapshotkopie dauert nur wenige Sekunden - in der Regel sogar weniger als 1 Sekunde, unabhängig von der Größe des Datenträgers und der Aktivitätsstufe auf dem Datenträger. Nachdem eine Snapshotkopie erstellt wurde, werden Änderungen an Datenobjekten in Aktualisierungen der aktuellen Version der Objekte abgebildet, so als wären keine Snapshotkopien vorhanden. Gleichzeitig bleibt die Kopie der Daten völlig stabil. 

Eine Snapshotkopie verursacht keine Leistungseinbußen. Benutzer können ohne großen Aufwand bis zu 50 geplante Snapshots pro {{site.data.keyword.blockstorageshort}}-Datenträger speichern, die alle als schreibgeschützte und Onlineversionen der Daten zugänglich sind.


Mit Snapshots können Benutzer

- zeitpunktgesteuerte Wiederherstellungspunkte ohne Betriebsunterbrechung erstellen
- Datenträger auf frühere Zeitpunkte zurücksetzen.

Sie müssen für Ihren Datenträger einen bestimmten Snapshotbereich kaufen, um Snapshots davon machen zu können. Der Snapshotbereich kann bei der Erstbestellung des Datenträgers oder nach der Erstbereitstellung hinzugefügt werden, indem Sie auf der Seite **Datenträgerdetails** auf die Dropdown-Schaltfläche **Aktionen** klicken und **Snapshotbereich hinzufügen** auswählen. Beachten Sie, dass geplante und manuelle Snapshots den Snapshotbereich gemeinsam nutzen, und bestellen Sie Ihren Speicherplatz entsprechend. Im Artikel [Snapshots bestellen](ordering-snapshots.html) finden Sie dazu genauere Informationen und Anweisungen.

## Snapshots - bewährte Verfahren

Die Snapshotgestaltung richtet sich nach der Umgebung des Kunden. Die folgenden konzeptionellen Überlegungen sollen Ihnen helfen, Snapshotkopien zu planen und zu implementieren: 
- 	Bis zu 50 Snapshots können über den Plan und bis zu 50 können manuell auf den einzelnen Datenträgern oder LUNs erstellt werden. 
- 	Verwenden Sie nicht zu viele Snapshots. Stellen Sie sicher, dass Ihre geplante Snapshothäufigkeit Ihre RTO- und RPO-Bedürfnisse sowie Ihre Anwendungsgeschäftsanforderungen erfüllt, indem Sie stündliche, tägliche oder wöchentliche Snapshots planen. 
- 	Nutzen Sie die automatische Löschung von Snapshots, um die Zunahme der Speicherbelegung zu steuern. <br/>
    **Hinweis**: Der Schwellenwert für die automatische Löschung liegt bei 95%.
    
Beachten Sie, dass Snapshots keinen Ersatz für eine tatsächliche dezentrale DR-Replikation oder eine lange Aufbewahrungssicherung darstellen.
    
## Wie verbrauchen Snapshotkopien Plattenspeicher?

Snapshotkopien minimieren den Datenträgerverbrauch, indem Sie einzelne Blöcke anstelle von ganzen Dateien beibehalten. Snapshotkopien verbrauchen nur dann zusätzlichen Speicherplatz, wenn Dateien im aktiven Dateisystem geändert oder gelöscht werden. Wenn das passiert, werden die Originaldateiblöcke als Teil einer oder mehrerer Snapshotkopien weiter beibehalten.
Die geänderten Blöcke werden im aktiven Dateisystem an verschiedenen Positionen auf dem Datenträger neu erstellt oder als aktive Dateiblöcke komplett entfernt. Somit wird zusätzlich zu dem von Blöcken im geänderten aktiven Dateisystem verwendeten Plattenspeicher der von den Originalblöcken verwendete Plattenspeicher weiter reserviert, um den Status des aktiven Dateisystems vor der Änderung abzubilden.

<table>
    <colgroup>
      <col style="width: 33.3%;"/>
      <col style="width: 33.3%;"/>
      <col style="width: 33.3%;"/>
    </colgroup>
    <tbody>
      <tr>
        <th colspan="3" style="border: 0.0px;text-align: center;">Plattenspeicherplatzbelegung vor und nach der Snapshotkopie</th>
     </tr><tr>
        <td style="border: 0.0px;text-align: center;"><img src="/images/bfcircle1.png" alt="Vor der Snapshotkopie"></td>
        <td style="border: 0.0px;text-align: center;"><img src="/images/bfcircle3.png" alt="Nach der Snapshotkopie"></td>
        <td style="border: 0.0px;text-align: center;"><img src="/images/bfcircle2.png" alt="Änderungen nach der Snapshotkopie"></td>
     </tr><tr>
        <td style="border: 0.0px;">Bevor eine Snapshotkopie erstellt wird, wird Plattenspeicher nur vom aktiven Dateisystem verbraucht.</td>
        <td style="border: 0.0px;">Nachdem eine Snapshotkopie erstellt wurde, verweisen das aktive Dateisystem und die Snapshotkopie auf dieselben Plattenblöcke. Die Snapshotkopie verbraucht keinen zusätzlichen Plattenspeicher.</td>
        <td style="border: 0.0px;">Nachdem die Datei <i>myfile.txt</i> aus dem aktiven Dateisystem gelöscht wurde, umfasst die Snapshotkopie weiterhin die Datei und verweist auf ihre Plattenblöcke. Deswegen bedeutet das Löschen von Daten des aktiven Dateisystems nicht immer eine Freigabe von Plattenspeicher.</td>
      </tr>
    </tbody>
</table>

Führen Sie die Anweisungen im Dokument [Snapshots verwalten](working-with-snapshots.html) aus, um anzuzeigen, wie viel Snapshotbereich verwendet wird.






