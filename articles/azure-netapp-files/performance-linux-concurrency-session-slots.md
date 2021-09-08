---
title: 'Bewährte Methoden für Linux-Parallelität für Azure NetApp Files: Sitzungsslots und Slottabelleneinträge | Microsoft-Dokumentation'
description: Erfahren Sie mehr zu bewährte Methoden für Sitzungsslots und Slottabelleneinträge für das NFS-Protokoll für Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: b-juche
ms.openlocfilehash: 522c9e590f1f63a12bd4f52f56eac0798ba78aa7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346114"
---
# <a name="linux-concurrency-best-practices-for-azure-netapp-files---session-slots-and-slot-table-entries"></a>Bewährte Methoden für Linux-Parallelität für Azure NetApp Files: Sitzungsslots und Slottabelleneinträge

In diesem Artikel erfahren Sie mehr zu bewährten Methoden für Parallelität für Sitzungsslots und Slottabelleneinträge des NFS-Protokolls für Azure NetApp Files. 

## <a name="nfsv3"></a>NFSv3

NFSv3 bietet keinen Mechanismus zum Aushandeln von Parallelität zwischen Client und Server. Client und Server legen jeweils ihren Grenzwert fest, ohne einander zu konsultieren.  Um die beste Leistung zu erzielen, sollten Sie die maximale Anzahl der clientseitigen Einträge in der Slottabelle `sunrpc` mit derjenigen in Einklang bringen, die ohne Pushback auf den Server unterstützt wird.  Wenn ein Client die Fähigkeit des Netzwerkstapels des Servers zur Verarbeitung einer Workload überfordert, reagiert der Server mit einer Verkleinerung des Zeitfensters für die Verbindung, was kein ideales Szenario für Leistung ist.

Bei modernen Linux-Kerneln ist pro Verbindung die Eintragsgröße `sunrpc.max_tcp_slot_table_entries` der Slottabelle `sunrpc` standardmäßig so festgelegt, dass 65.536 ausstehende Vorgänge unterstützt werden, wie in der folgenden Tabelle dargestellt. 

| NFSv3-Server für Azure NetApp Files <br> Maximale Anzahl von Ausführungskontexten pro Verbindung | Linux-Client <br> Standardmäßige Maximalanzahl von Einträgen in der Slottabelle `sunrpc` pro Verbindung |
|-|-|
| 128 | 65.536 |

Diese Slottabelleneinträge bestimmen die Grenzwerte für Parallelität. Derart hohe Werte sind unnötig.  Wenn Sie z. B. eine Warteschlangentheorie mit Namen *Littles Gesetz* zu Rate ziehen, stellen Sie fest, dass die E/A-Rate von Parallelität (d. h. ausstehender E/A) und Latenz bestimmt wird. Damit beweist der Algorithmus, dass 65.536 Slots um Größenordnungen über dem liegen, was selbst für extrem anspruchsvolle Workloads benötigt wird.

`Littles Law: (concurrency = operation rate × latency in seconds)`

Ein Parallelitätsgrad von maximal 155 reicht aus, um 155.000 Oracle DB-NFS-Vorgänge pro Sekunde mithilfe Oracle Direct NFS zu erreichen. Dies ist eine Technologie, die dem Konzept der Einbindungsoption `nconnect` ähnelt:

* Bei einer Latenz von 0,5 ms ist eine Parallelität von 55 erforderlich, um 110.000 IOPS zu erreichen.
* Bei einer Latenz von 1 ms ist eine Parallelität von 155 erforderlich, um 155.000 IOPS zu erreichen.

![Kurve für die Wartezeit mit Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

Einzelheiten finden Sie unter [Leistung von Oracle-Datenbanken auf einzelnen Azure NetApp Files-Volumes](performance-oracle-single-volumes.md).

Der optimierbare Parameter `sunrpc.max_tcp_slot_table_entries` ist ein Optimierungsparameter auf Verbindungsebene.  *Als bewährte Methode legen Sie diesen Wert pro Verbindung auf höchsten 128 fest, wobei 10.000 Slots in der Umgebung nicht überschritten werden sollten.*

### <a name="examples-of-slot-count-based-on-concurrency-recommendation"></a>Beispiele der Slotanzahl basierend auf der Parallelitätsempfehlung 

Die Beispiele in diesem Abschnitt veranschaulichen die Slotanzahl basierend auf der Parallelitätsempfehlung.  

#### <a name="example-1--one-nfs-client-65536-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128-based-on-the-server-side-limit-of-128"></a>Beispiel 1: ein NFS-Client, mit dem Wert 65.536 für `sunrpc.max_tcp_slot_table_entries` und ohne `nconnect` für eine maximale Parallelität von 128 basierend auf dem serverseitigen Grenzwert 128

Beispiel 1 basiert auf einer einzelnen Clientworkload mit dem Standardwert 65.536 für `sunrpc.max_tcp_slot_table_entry` und einer einzelnen Netzwerkverbindung, d. h. ohne `nconnect`.  In diesem Fall ist eine Parallelität von 128 erreichbar.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP`) 
        * Der Client kann pro Verbindung theoretisch nicht mehr als 65.536 In-Flight-Anforderungen an den Server stellen.
        * Der Server akzeptiert über diese Einzelverbindung nicht mehr als 128 In-Flight-Anforderungen.

#### <a name="example-2--one-nfs-client-128-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128"></a>Beispiel 2: ein NFS-Client, mit dem Wert 128 für `sunrpc.max_tcp_slot_table_entries` und ohne `nconnect` für eine maximale Parallelität von 128

Beispiel 2 basiert auf einer einzelnen Clientworkload mit dem Wert 128 für `sunrpc.max_tcp_slot_table_entry`, jedoch ohne die Einbindungsoption `nconnect`.  Bei dieser Einstellung ist eine Parallelität von 128 über eine einzelne Netzwerkverbindung erreichbar.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * Der Client stellt pro Verbindung nicht mehr als 128 In-Flight-Anforderungen an den Server.
        * Der Server akzeptiert über diese Einzelverbindung nicht mehr als 128 In-Flight-Anforderungen.

#### <a name="example-3--one-nfs-client-100-sunrpcmax_tcp_slot_table_entries-and-nconnect8-for-a-maximum-concurrency-of-800"></a>Beispiel 3: ein NFS-Client mit dem Wert 100 für `sunrpc.max_tcp_slot_table_entries` und `nconnect=8` für eine maximale Parallelität von 800

Beispiel 3 basiert auf einer einzelnen Clientworkload, jedoch mit einem niedrigeren Wert von 100 für `sunrpc.max_tcp_slot_table_entry`.  Dieses Mal wurde die Einbindungsoption `nconnect=8` zum Verteilen der Workload auf 8 Verbindungen verwendet.  Bei dieser Einstellung ist eine Parallelität von 800, verteilt auf die 8 Verbindungen, erzielbar.  Dieser Wert entspricht der Parallelität, die erforderlich ist, um 400.000 IOPS zu erreichen.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP), Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)… Connection 8 (10.10.10.10:2049, 10.10.10.11:7321,TCP)`
    * Verbindung 1
        * Der Client stellt über diese Verbindung nicht mehr als 100 In-Flight-Anforderungen an den Server.
        * Es wird erwartet, dass der Server für diese Verbindung nicht mehr als 128 In-Flight-Anforderungen vom Client akzeptiert.
    * Verbindung 2
        * Der Client stellt über diese Verbindung nicht mehr als 100 In-Flight-Anforderungen an den Server.
        * Es wird erwartet, dass der Server für diese Verbindung nicht mehr als 128 In-Flight-Anforderungen vom Client akzeptiert.
    * `…`
    * `…`
    * Verbindung 8
        * Der Client stellt über diese Verbindung nicht mehr als 100 In-Flight-Anforderungen an den Server.
        * Es wird erwartet, dass der Server für diese Verbindung nicht mehr als 128 In-Flight-Anforderungen vom Client akzeptiert.

#### <a name="example-4--250-nfs-clients-8-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-2000"></a>Beispiel 4: 250 NFS-Clients, mit dem Wert 8 für `sunrpc.max_tcp_slot_table_entries` und ohne `nconnect` für eine maximale Parallelität von 2.000

In Beispiel 4 wird pro Client der reduzierte Wert 8 für `sunrpc.max_tcp_slot_table_entry` für eine EDA-Umgebung mit 250 Computern verwendet. In diesem Szenario wird umgebungsweit eine Parallelität von 2000 erreicht, ein Wert, der mehr als ausreicht, um 4.000 MiB/s einer Back-End-EDA-Workload zu unterstützen.

* `NFS_Server=10.10.10.10, NFS_Client1=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * Der Client stellt pro Verbindung nicht mehr als 8 In-Flight-Anforderungen an den Server.
        * Der Server akzeptiert über diese Einzelverbindung nicht mehr als 128 In-Flight-Anforderungen.
* `NFS_Server=10.10.10.10, NFS_Client2=10.10.10.12`
    * `Connection (10.10.10.10:2049, 10.10.10.12:7820,TCP) `
        * Der Client stellt pro Verbindung nicht mehr als 8 In-Flight-Anforderungen an den Server.
        * Der Server akzeptiert über diese Einzelverbindung nicht mehr als 128 In-Flight-Anforderungen.
* `…`
* `…`
* `NFS_Server=10.10.10.10, NFS_Client250=10.10.11.13`
    * `Connection (10.10.10.10:2049, 10.10.11.13:4320,TCP) `
        * Der Client stellt pro Verbindung nicht mehr als 8 In-Flight-Anforderungen an den Server.
        * Der Server akzeptiert über diese Einzelverbindung nicht mehr als 128 In-Flight-Anforderungen.

Wenn Sie NFSv3 verwenden, *sollten Sie gemeinsam die Anzahl der Speicherendpunktslots auf maximal 10.000 belassen*. Es ist am besten, den Wert pro Verbindung für `sunrpc.max_tcp_slot_table_entries` auf weniger als 128 festzulegen, wenn eine Anwendung auf viele Netzwerkverbindungen horizontal aufskaliert wird (`nconnect` und HPC im Allgemeinen und EDA im Besonderen).  

### <a name="how-to-calculate-the-best-sunrpcmax_tcp_slot_table_entries"></a>Berechnen des besten Werts für `sunrpc.max_tcp_slot_table_entries` 

Anhand von *Littles Gesetz* können Sie die gesamte erforderliche Anzahl von Einträgen in der Slottabelle berechnen. Berücksichtigen Sie im Allgemeinen die folgenden Faktoren:  

* Aufskalierte Workloads sind häufig übermäßig sequenziell.
* Datenbankworkloads, insbesondere OLTP, sind oft zufallsgesteuert. 

Die folgende Tabelle zeigt eine Beispielstudie zur Parallelität mit angegebenen beliebigen Latenzen:

|     E/A-Größe    |     Wartezeit    |     E/A oder Durchsatz    |     Parallelität    |
|-|-|-|-|
|     8 KiB    |     0,5 ms    |     110.000 IOPS \| 859 MiB/s    |     55    |
|     8 KiB    |     2 ms    |     400.000 IOPS \| 3.125 MiB/s    |     800    |
|     256 KiB    |     2 ms    |     16.000 IOPS \| 4.000 MiB/s    |     32    |
|     256 KiB    |     4 ms    |     32.000 IOPS \| 8.000 MiB/s    |     128    |

### <a name="how-to-calculate-concurrency-settings-by-connection-count"></a>Berechnen von Parallelitätseinstellungen anhand der Verbindungsanzahl

Wenn es sich bei der Workload beispielsweise um eine EDA-Farm handelt und 1.250 Clients Workload zu demselben Speicherendpunkt leiten (ein Speicherendpunkt ist eine Speicher-IP-Adresse), berechnen Sie die erforderliche E/A-Rate und teilen die Parallelität auf die gesamte Farm auf.

Angenommen, die Workload beträgt 4.000 MiB/s bei einer durchschnittlichen Vorgangsgröße von 256 KiB und einer durchschnittlichen Latenz von 10 ms. Berechnen Sie die Parallelität mit der folgenden Formel:

`(concurrency = operation rate × latency in seconds)`

Die Berechnung ergibt eine Parallelität von 160: 
 
`(160 = 16,000 × 0.010)`

Angesichts des Bedarfs für 1.250 Clients könnten Sie `sunrpc.max_tcp_slot_table_entries` sicher auf 2 pro Client festlegen, um 4.000 MiB/s zu erreichen.  Sie können jedoch zusätzlichen Spielraum einräumen, indem Sie die Anzahl pro Client auf 4 oder sogar 8 festlegen, um deutlich unter der empfohlenen Obergrenze von 10.000 Slots zu bleiben. 

### <a name="how-to-set-sunrpcmax_tcp_slot_table_entries-on-the-client"></a>Festlegen von `sunrpc.max_tcp_slot_table_entries` auf dem Client

1.  Fügen Sie `sunrpc.max_tcp_slot_table_entries=<n>` zur Konfigurationsdatei `/etc/sysctl.conf` hinzu.   
    Wenn während der Optimierung ein Wert kleiner als 128 für optimal befunden wird, ersetzen Sie 128 durch die entsprechende Zahl.
2.  Führen Sie den folgenden Befehl aus:   
    `$ sysctl -p`
3.  Binden Sie alle NFS-Systeme (erneut) ein, da der optimierbare Wert nur für Einbindungsvorgänge gilt, die nach dessen Festlegung erfolgt sind.

## <a name="nfsv41"></a>NFSv4.1 

In NFSv4.1 bestimmen Sitzungen die Beziehung zwischen Client und Server. Unabhängig davon, ob die eingebundenen NFS-Dateisysteme zu einer Verbindung gehören oder zu mehreren (wie bei `nconnect`), gelten die Regeln für die Sitzung. Bei der Sitzungseinrichtung handeln Client und Server die maximale Anzahl von Anforderungen für die Sitzung aus, wobei der niedrigere der beiden unterstützten Werte verwendet wird. Azure NetApp Files unterstützt 180 ausstehende Anforderungen, während Linux-Clients standardmäßig 64 unterstützen. Die folgende Tabelle enthält die Sitzungsgrenzwerte: 

| NFSv4.1-Server für Azure NetApp Files <br>  Max. Befehle pro Sitzung | Linux-Client <br>  Standardmäßige max. Befehle pro Sitzung    | Ausgehandelte max. Befehle für die Sitzung |
|-|-|-|
|     180    |     64    |     64    |

Obwohl Linux-Clients standardmäßig maximal 64 Anforderungen pro Sitzung unterstützen, ist der Wert von `max_session_slots` optimierbar.  Ein Neustart ist erforderlich, damit die Änderungen wirksam werden.  Zeigen Sie mit dem Befehl `systool -v -m nfs` das aktuelle vom Client verwendete Maximum an.  Damit der Befehl funktioniert, muss mindestens eine NFSv4.1-Einbindung vorhanden sein:

```
$ systool -v -m nfs
{
Module = "nfs"
…
  Parameters:
…
    max_session_slots   = "64"   
…
}
```

Um `max_session_slots` zu optimieren, erstellen Sie unter `/etc/modprobe.d` eine entsprechende Konfigurationsdatei.  Stellen Sie sicher, dass in der Zeile in der Datei keine „Anführungszeichen“ vorhanden sind. Andernfalls wird die Option nicht wirksam.

`$ echo “options nfs max_session_slots=180” > /etc/modprobe.d/nfsclient.conf`
`$ reboot`

Azure NetApp Files beschränkt jede Sitzung auf maximal 180 Befehle. Daher ist 180 der derzeit maximal konfigurierbare Wert.  Der Client ist nicht in der Lage, eine Parallelität von mehr als 128 zu erreichen, es sei denn, die Sitzung wird auf mehr als eine Verbindung aufgeteilt, da Azure NetApp Files jede Verbindung auf maximal 128 NFS-Befehle beschränkt.  Um mehr als eine Verbindung zu erhalten, wird die Einbindungsoption `nconnect` empfohlen, und ein Wert von mindestens zwei ist erforderlich.

### <a name="examples-of-expected-concurrency-maximums"></a>Beispiele erwarteter Höchstwerte für Parallelität

Beispiele in diesem Abschnitt veranschaulichen die erwarteten Höchstwerte für Parallelität.  

#### <a name="example-1--64-max_session_slots-and-no-nconnect"></a>Beispiel für 1-64 `max_session_slots` ohne `nconnect`

Beispiel 1 basiert auf der Standardeinstellung 64 für `max_session_slots` ohne `nconnect`. Bei dieser Einstellung ist eine Parallelität von 64 über eine einzelne Netzwerkverbindung erreichbar.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * Der Client stellt in der Sitzung nicht mehr als 64 In-Flight-Anforderungen an den Server.
        * Der Server akzeptiert für die Sitzung nicht mehr als 64 In-Flight-Anforderungen vom Client. (64 ist der ausgehandelte Wert.)

#### <a name="example-2--64-max_session_slots-and-nconnect2"></a>Beispiel für 2-64 `max_session_slots` mit `nconnect=2` 

Beispiel 2 basiert auf maximal 64 für `session_slots`, jedoch mit der hinzugefügten Einbindungsoption `nconnect=2`.  Eine Parallelität von 64 ist erreichbar, aber auf zwei Verbindungen aufgeteilt.  Obwohl mehrere Verbindungen in diesem Szenario nicht mehr Parallelität bringen, wirkt sich die verringerte Warteschlangentiefe pro Verbindung positiv auf die Latenz aus.

Beachten Sie, dass wenn `max_session_slots` weiter auf 64 und `nconnect=2` festgelegt sind, die maximale Anzahl von Anforderungen auf die Verbindungen aufgeteilt wird.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * Verbindung 1
        * Der Client stellt über diese Verbindung nicht mehr als 32 In-Flight-Anforderungen an den Server.
        * Es wird erwartet, dass der Server für diese Verbindung nicht mehr als 32 In-Flight-Anforderungen vom Client akzeptiert.
    * Verbindung 2
        * Der Client stellt über diese Verbindung nicht mehr als 32 In-Flight-Anforderungen an den Server.
        * Es wird erwartet, dass der Server für diese Verbindung nicht mehr als 32 In-Flight-Anforderungen vom Client akzeptiert.

#### <a name="example-3--180-max_session_slots-and-no-nconnect"></a>Beispiel für 3-180 `max_session_slots` ohne `nconnect`

In Beispiel 3 wird die Einbindungsoption `nconnect` weggelassen und der Wert `max_session_slots` auf 180 festgelegt, was der maximalen NFSv4.1-Sitzungsparallelität des Servers entspricht.  In diesem Szenario, mit nur einer Verbindung und angesichts der 128 maximal ausstehenden Vorgänge pro NFS-Verbindung für Azure NetApp Files, ist die Sitzung auf 128 In-Flight-Vorgänge begrenzt.

Obwohl `max_session_slots` auf 180 festgelegt wurde, ist die einzelne Netzwerkverbindung auf maximal 128 Anforderungen beschränkt: 

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * Der Client stellt in der Sitzung nicht mehr als 180 In-Flight-Anforderungen an den Server.
        * Der Server akzeptiert für die Sitzung nicht mehr als 180 In-Flight-Anforderungen vom Client.
        * *Der Server akzeptiert über die Einzelverbindung nicht mehr als 128 In-Flight-Anforderungen.*

#### <a name="example-4--180-max_session_slots-and-nconnect2-"></a>Beispiel für 4-180 `max_session_slots` mit `nconnect=2 ` 

In Beispiel 4 wird die Einbindungsoption `nconnect=2` hinzugefügt und der Wert 180 für `max_session_slots` erneut verwendet. Da die gesamte Workload auf zwei Verbindungen aufgeteilt ist, sind 180 ausstehende Vorgänge möglich.

Bei zwei aktiven Verbindungen unterstützt die Sitzung die vollständige Zuteilung von 180 ausstehenden Anforderungen.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * Verbindung 1
        * Es wird erwartet, dass der Client über Verbindung 1 nicht mehr als 90 In-Flight-Anforderungen an den Server sendet.
        * *Es wird erwartet, dass der Server für diese Verbindung innerhalb der Sitzung nicht mehr als 90 In-Flight-Anforderungen vom Client akzeptiert.*
    * Verbindung 2
        * Es wird erwartet, dass der Client über Verbindung 1 nicht mehr als 90 In-Flight-Anforderungen an den Server sendet.
        * *Es wird erwartet, dass der Server für diese Verbindung innerhalb der Sitzung nicht mehr als 90 In-Flight-Anforderungen vom Client akzeptiert.*

> [!NOTE]
> Für maximale Parallelität legen Sie `max_session_slots` auf 180 fest, was die maximale Parallelität auf Sitzungsebene ist, die derzeit von Azure NetApp Files unterstützt wird.

### <a name="how-to-check-for-the-maximum-requests-outstanding-for-the-session"></a>Überprüfen der maximalen Anzahl ausstehender Anforderungen für die Sitzung 

Um die vom Client und Server unterstützten Größen von `session_slot` anzuzeigen, erfassen Sie den Einbindungsbefehl in einer Paketablaufverfolgung.  Suchen Sie nach dem Aufruf von `CREATE_SESSION` und der Antwort auf `CREATE_SESSION`, wie im folgenden Beispiel gezeigt.  Der Aufruf stammt vom Client, die Antwort vom Server.

Erfassen Sie den Einbindungsbefehl mit dem folgenden `tcpdump`-Befehl:

`$ tcpdump -i eth0 -s 900 -w /tmp/write.trc port 2049`

Untersuchen Sie mit Wireshark die folgenden relevanten Pakete:

![Screenshot der für Sie relevanten Pakete.](../media/azure-netapp-files/performance-packets-interest.png)  

Untersuchen Sie in diesen beiden Paketen das Feld `max_reqs` im mittleren Abschnitt der Ablaufverfolgungsdatei.

* Network File System
    * Operations
        * `Opcode`
            * `csa_fore_channel_attrs`
            * `max reqs`

Paket 12 (maximale Clientanforderungen) zeigt, dass der Client für `max_session_slots` den Wert 64 hatte.  Beachten Sie im nächsten Abschnitt, dass der Server eine Parallelität von 180 für die Sitzung unterstützt.  Die Sitzung endet mit der Aushandlung des niedrigeren der beiden angegebenen Werte.

![Screenshot mit der maximalen Anzahl der Sitzungsslots für Paket 12.](../media/azure-netapp-files/performance-max-session-packet-12.png)  

Das folgende Beispiel zeigt Paket 14 (maximale Anzahl der Serveranforderungen):

![Screenshot mit der maximalen Anzahl der Sitzungsslots für Paket 14.](../media/azure-netapp-files/performance-max-session-packet-14.png) 

## <a name="next-steps"></a>Nächste Schritte  

* [Bewährte Methoden in Bezug auf direkte E/A unter Linux für Azure NetApp Files](performance-linux-direct-io.md)
* [Bewährte Methoden in Bezug auf den Linux-Dateisystemcache für Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Einbindungsoptionen für NFS unter Linux: bewährte Methoden für Azure NetApp Files](performance-linux-mount-options.md)
* [Bewährte Methoden für NFS-Read-Ahead unter Linux](performance-linux-nfs-read-ahead.md)
* [Bewährte Methoden für SKUs für virtuelle Azure-Computer](performance-virtual-machine-sku.md) 
* [Leistungsbenchmarks für Linux](performance-benchmarks-linux.md) 
