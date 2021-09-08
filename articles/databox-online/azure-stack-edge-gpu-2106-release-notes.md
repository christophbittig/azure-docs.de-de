---
title: Versionshinweise zu Azure Stack Edge 2106
description: In diesem Artikel werden schwerwiegende ungelöste Probleme und Lösungen für das Release 2106 von Azure Stack Edge beschrieben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/19/2021
ms.author: alkohli
ms.openlocfilehash: 81c6f9eb55a44be49cfdbde5e171ce431f4f3cfd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481440"
---
# <a name="azure-stack-edge-2106-release-notes"></a>Versionshinweise zu Azure Stack Edge 2106

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In den folgenden Versionshinweisen werden schwerwiegende ungelöste und behobene Probleme für das Release 2106 für Azure Stack Edge-Geräte beschrieben. Diese Versionshinweise gelten nur für Geräte vom Typ „Azure Stack Edge Pro – GPU“, „Azure Stack Edge Pro R“ und „Azure Stack Edge Mini R“. Funktionen und Probleme, die nur für ein bestimmtes Modell gelten, werden jeweils entsprechend gekennzeichnet.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres Geräts die Informationen in den Versionshinweisen sorgfältig durch.

Dieser Artikel bezieht sich auf das **Release 2106 von Azure Stack Edge**, das der Softwareversionsnummer **2.2.1636.3457** zugeordnet ist. Diese Software kann auf Ihr Gerät angewendet werden, wenn Sie mindestens Azure Stack Edge 2010-Software (2.1.1377.2170) ausführen.

## <a name="whats-new"></a>Neues

Im Release 2106 von Azure Stack Edge sind die folgenden neuen Funktionen verfügbar. 

- **Windows-Updates und Sicherheitskorrekturen**: Das [neueste kumulative Update für Windows und die Sicherheitskorrekturen vom Juni](https://support.microsoft.com/en-us/topic/june-8-2021-kb5003697-monthly-rollup-457aa997-18a0-46e9-8612-497f01ccaa54) wurden in das Updatepaket für Azure Stack Edge eingebunden.
- **Fehlerbehebungen für privates Multi-Access Edge Computing in Azure**: Mehrere Probleme wurden für Bereitstellungen von privatem MEC in Azure behoben.

    - Probleme im Zusammenhang mit der Integritätsüberwachung von Gast-VMs, z. B. Verknüpfungsfluktuation, Fehler im Startprotokoll und Neustarts
    - Verbrauch von Arbeitsspeicherressourcen im Lauf der Zeit
    - Mellanox-Treiber, Firmware und Tools
    - Tools zum Debuggen von VM-bezogenen Problemen und Überprüfen der Netzwerkintegrität
    - Probleme, die dazu führten, dass ausgehende Pakete der VM mit E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) oder der Datenverkehr von LAN/WAN-VM-NetAdaptern verworfen wurden
- **Verbesserungen der Protokollerfassung**: Dieses Release enthält Verbesserungen der Protokollerfassung im Zusammenhang mit Azure Stack Edge-Updateszenarien.



## <a name="issues-fixed-in-2106-release"></a>Im Release 2106 behobene Probleme

In der folgenden Tabelle sind die Probleme aufgelistet, die in vorherigen Releases festgestellt und im aktuellen Release behoben wurden.

| Nein. | Funktion | Problem | 
| --- | --- | --- |
|**1.**|Azure Private MEC |Der Verknüpfungsstatus des VM-Netzwerkadapters ändert sich beim Starten und in regelmäßigen Abständen.|
|**2.**|Azure Private MEC  |Das DHCP-Umleitungsflag für VFtoPF kann bei Verwendung auf Mellanox-Netzwerkschnittstellen dazu führen, dass die Pakete verworfen werden.|
|**3.**|Azure Private MEC  |Für den Mellanox-Netzwerkschnittstellentreiber, die Firmware und die Tools muss ein Upgrade auf Version 2.60 durchgeführt werden.|
|**4.**|VM |Bei dem zum Erfassen von VM-Gastprotokollen verfügbaren Cmdlet `Get-VMInguestLogs` tritt beim Herstellen einer Verbindung über die PowerShell-Schnittstelle des Geräts ein Fehler auf.|
|**5.**|Azure Private MEC  |Wenn der Webproxy konfiguriert ist, bewirkt die Einstellung für die Webproxyumgehung einen Fehler bei der VM-Bereitstellung. |
|**6.**|Azure Private MEC |Bei MEC-/NFM-Bereitstellungen vor dem Update 2105 kann in seltenen Fällen das Problem auftreten, dass Datenverkehr von LAN/WAN-VM-NetAdaptern verworfen wird. In 2106 wird dieses Problem behoben, indem „enableIPForwarding“ auf LAN/WAN-Netzwerkschnittstellen von VMs auf „True“ festgelegt wird, unabhängig davon, ob die VMs vor oder nach Release 2105 erstellt wurden.   |
|**7.**|Azure Private MEC  |Ausgehende Pakete der VM mit E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) können von den Mellanox-Netzwerkschnittstellen (Port 5 und Port 6 auf dem Gerät) verworfen werden, wenn eine Kombination aus Mellanox-Treiber, virtuellen SR-IOV-Funktionen (VF) und der Funktion vftopfDHCPRedirect verwendet wird. In 2106 wird das Problem behoben, indem die Funktion vftopfDHCPRedirect deaktiviert wird.  |


## <a name="known-issues-in-2106-release"></a>Bekannte Probleme im Release 2106

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme im Release 2106.

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
|**1.**|Previewfunktionen |In diesem Release sind die folgenden Funktionen als Vorschauversionen verfügbar: Lokaler Azure Resource Manager, VMs, Cloudverwaltung von VMs, Kubernetes-Cloudverwaltung, Kubernetes mit Azure Arc-Unterstützung, VPN für Azure Stack Edge Pro R und Azure Stack Edge Mini R, Multiprozessdienst (MPS) und Multi-Access Edge Computing (MEC) für Azure Stack Edge Pro GPU.  |Diese Funktionen werden in einem späteren Release allgemein verfügbar sein. |


## <a name="known-issues-from-previous-releases"></a>Bekannte Probleme aus vorherigen Releases

Die folgende Tabelle enthält eine Zusammenfassung der bereits aus früheren Releases bekannten Probleme.

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | Zum Erstellen einer SQL-Datenbank ist Administratorzugriff erforderlich.   |Gehen Sie anstelle der Schritte 1-2 in [Erstellen der SQL-Datenbank](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) wie folgt vor. <ul><li>Aktivieren Sie in der lokalen Benutzeroberfläche des Geräts die Computeschnittstelle. Wählen Sie **Compute > Portnr. > Für Compute aktivieren > Anwenden** aus.</li><li>Laden Sie `sqlcmd` aus dem [SQL-Befehlshilfsprogramm](/sql/tools/sqlcmd-utility) auf Ihren Clientcomputer herunter. </li><li>Stellen Sie eine Verbindung mit der IP-Adresse der Computeschnittstelle (dem aktivierten Port) her, und fügen Sie am Ende der Adresse „,1401“ hinzu.</li><li>Der endgültige Befehl sieht wie folgt aus: sqlcmd -S {IP-Adresse-der-Schnittstelle},1401 -U SA -P "Starkes!Kennw0rt".</li>Danach sollten die Schritte 3 und 4 aus der aktuellen Dokumentation identisch sein. </li></ul> |
| **2.** |Aktualisieren| Inkrementelle Änderungen an Blobs, die über **Aktualisieren** wiederhergestellt werden, sind NICHT möglich. |Bei Blobendpunkten können Teilaktualisierungen von Blobs nach dem erneuten Laden dazu führen, dass die Aktualisierungen nicht in die Cloud hochgeladen werden. Dies betrifft beispielsweise eine Sequenz folgender Aktionen:<ul><li>Erstellen eines Blobs in der Cloud. Oder Löschen eines zuvor hochgeladenen Blobs auf dem Gerät.</li><li>Aktualisieren des Blobs mithilfe der Aktualisierungsfunktion aus der Cloud auf der Appliance.</li><li>Aktualisieren nur eines Teils des Blobs mithilfe von Azure SDK-REST-APIs.</li></ul>Diese Aktionen können dazu führen, dass die aktualisierten Abschnitte des Blobs nicht in der Cloud aktualisiert werden. <br>**Problemumgehung**: Verwenden Sie Tools wie Robocopy, oder führen Sie reguläre Dateikopiervorgänge über Explorer oder die Befehlszeile durch, um ganze Blobs zu ersetzen.|
|**3.**|Drosselung|Wenn während der Drosselung neue Schreibvorgänge auf das Gerät nicht zulässig sind, tritt für Schreibvorgänge des NFS-Clients der Fehler „Zugriff verweigert“ auf.| Der Fehler wird wie folgt angezeigt:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: Verzeichnis „test“ kann nicht erstellt werden: Zugriff verweigert|
|**4.**|Blob Storage-Erfassung|Wenn Sie AzCopy Version 10 für die Erfassung von Blob Storage verwenden, führen Sie AzCopy mit folgendem Argument aus: `Azcopy <other arguments> --cap-mbps 2000`| Wenn diese Grenzwerte für AzCopy nicht angegeben werden, wird möglicherweise eine große Anzahl von Anforderungen an das Gerät gesendet. Dies kann zu Problemen mit dem Dienst führen.|
|**5.**|Mehrstufige Speicherkonten|Bei der Verwendung von mehrstufigen Speicherkonten gilt Folgendes:<ul><li> Es werden nur Blockblobs unterstützt. Seitenblobs werden nicht unterstützt.</li><li>Momentaufnahmen- oder Kopieren-APIs werden nicht unterstützt.</li><li> Die Erfassung von Hadoop-Workloads über `distcp` wird nicht unterstützt, da dabei der Kopiervorgang intensiv genutzt wird.</li></ul>||
|**6.**|Verbindung mit NFS-Freigabe|Wenn mehrere Prozesse auf dieselbe Freigabe kopiert werden und das Attribut `nolock` nicht verwendet wird, werden beim Kopiervorgang möglicherweise Fehler angezeigt.|Zum Kopieren von Dateien in die NFS-Freigabe muss das Attribut `nolock` an den mount-Befehl übergeben werden. Beispiel: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Kubernetes-Cluster|Wenn Sie auf Ihrem Gerät, auf dem ein Kubernetes-Cluster ausgeführt wird, ein Update anwenden, werden die virtuellen Kubernetes-Computer neu gestartet. In diesem Fall werden nur Pods, die mit angegebenen Replikaten bereitgestellt werden, nach einem Update automatisch wiederhergestellt.  |Wenn Sie einzelne Pods außerhalb eines Replikationscontrollers ohne Angabe einer Replikatgruppe erstellt haben, werden diese Pods nach dem Geräteupdate nicht automatisch wiederhergestellt. Diese Pods müssen wiederhergestellt werden.<br>Eine Replikatgruppe ersetzt Pods, die aus einem bestimmten Grund gelöscht oder beendet werden, z. B. Knotenfehler oder Aktualisierung eines unterbrochenen Knotens. Aus diesem Grund empfiehlt es sich, eine Replikatgruppe auch dann zu verwenden, wenn in der Anwendung nur ein einziger Pod erforderlich ist.|
|**8.**|Kubernetes-Cluster|Kubernetes auf Azure Stack Edge Pro wird nur mit Helm v3 oder höher unterstützt. Weitere Informationen finden Sie unter [Frequently asked questions: Removal of Tiller](https://v3.helm.sh/docs/faq/) (Häufig gestellte Fragen: Entfernen von Tiller).|
|**9.**|Kubernetes mit Azure Arc-Aktivierung |Im allgemein verfügbaren Release wird Azure Arc-fähiges Kubernetes von Version 0.1.18 auf 0.2.9. Da das Update für Azure Arc-fähiges Kubernetes nicht auf Azure Stack Edge-Geräten unterstützt wird, müssen Sie Azure Arc-fähiges Kubernetes erneut bereitstellen.|Führen Sie die folgenden Schritte durch:<ol><li>[Führen Sie Updates für Gerätesoftware und Kubernetes durch](azure-stack-edge-gpu-install-update.md).</li><li>Stellen Sie eine Verbindung mit der [PowerShell-Schnittstelle des Geräts](azure-stack-edge-gpu-connect-powershell-interface.md) her.</li><li>Entfernen Sie den vorhandenen Azure Arc-Agent. Geben Sie Folgendes ein: `Remove-HcsKubernetesAzureArcAgent`.</li><li>Stellen Sie [Azure Arc auf einer neuen Ressource](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md) bereit. Verwenden Sie keine vorhandene Azure Arc-Ressource.</li></ol>|
|**10.**|Kubernetes mit Azure Arc-Aktivierung|Azure Arc-Bereitstellungen werden nicht unterstützt, wenn auf dem Azure Stack Edge Pro-Gerät ein Webproxy konfiguriert ist.||
|**11.**|Kubernetes |Port 31000 ist für das Kubernetes-Dashboard reserviert. Port 31001 ist für die Edgecontainerregistrierung reserviert. Entsprechend sind die IP-Adressen 172.28.0.1 und 172.28.0.10 in der Standardkonfiguration jeweils für den Kubernetes-Dienst bzw. den Core DNS-Dienst reserviert.|Reservierte IP-Adressen dürfen nicht verwendet werden.|
|**12.**|Kubernetes |In Kubernetes sind derzeit keine Lastenausgleichsdienste für mehrere Protokolle zulässig, beispielsweise ein DNS-Dienst, der auf TCP sowie auf UDP lauschen soll. |Zum Umgehen dieser Einschränkung von Kubernetes mit MetalLB können für die gleiche Podauswahl zwei Dienste (einer für TCP, einer für UDP) erstellt werden. Diese Dienste verwenden denselben Freigabeschlüssel und denselben Wert spec.loadBalancerIP und nutzen so die gleiche IP-Adresse gemeinsam. IP-Adressen können auch gemeinsam genutzt werden, wenn Sie über mehr Dienste als verfügbare IP-Adressen verfügen. <br> Weitere Informationen finden Sie unter [IP Address Sharing](https://metallb.universe.tf/usage/#ip-address-sharing) (Freigeben von IP-Adressen).|
|**13.**|Kubernetes-Cluster|Vorhandene Marketplace-Module für Azure IoT Edge können Änderungen für die Ausführung in IoT Edge auf Azure Stack Edge-Geräten erfordern.|Weitere Informationen finden Sie unter „Ändern von Azure IoT Edge-Modulen aus dem Marketplace zum Ausführen auf dem Azure Stack Edge-Gerät“.<!-- insert link-->|
|**14.**|Kubernetes |Dateibasierte Bindungseinbindungen werden mit Azure IoT Edge in Kubernetes auf dem Azure Stack Edge-Gerät nicht unterstützt.|IoT Edge verwendet eine Übersetzungsebene, um `ContainerCreate`-Optionen in Kubernetes-Konstrukte zu übersetzen. Bei Erstellung von `Binds` wird das Verzeichnis `hostpath` zugeordnet. Daher können dateibasierte Bindungseinbindungen nicht an Pfade in IoT Edge-Containern gebunden werden. Ordnen Sie nach Möglichkeit das übergeordnete Verzeichnis zu.|
|**15.**|Kubernetes |Wenn Sie eigene Zertifikate für IoT Edge verwenden und diese Ihrem Azure Stack Edge-Gerät hinzufügen möchten, nachdem die Computeressource auf dem Gerät konfiguriert wurde, werden die neuen Zertifikate nicht übernommen.|Zum Umgehen dieses Problems sollten Sie die Zertifikate hochladen, bevor Sie die Computeressource auf dem Gerät konfigurieren. Wenn die Computeressource bereits konfiguriert ist, [stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her, und führen Sie IoT Edge-Befehle aus](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Starten Sie die Pods `iotedged` und `edgehub` neu.|
|**16.**|Zertifikate |In bestimmten Fällen kann es einige Sekunden dauern, bis der Zertifikatsstatus in der lokalen Benutzeroberfläche aktualisiert ist. |Dies kann sich in den folgenden Szenarios auf die lokale Benutzeroberfläche auswirken:<ul><li>Spalte **Status** auf der Seite **Zertifikate**</li><li>Kachel **Sicherheit** auf der Seite **Erste Schritte**</li><li>Kachel **Konfiguration** auf der Seite **Übersicht**</li></ul>  |
|**17.**|IoT Edge |Über IoT Edge bereitgestellte Module können das Hostnetzwerk nicht verwenden. | |
|**18.**|Compute + Kubernetes |Der NTLM-Webproxy wird nicht von Compute/Kubernetes unterstützt. ||
|**19.**|Kubernetes und Updates |Bei früheren Softwareversionen, z. B. Release 2008, treten bei einem Update Probleme durch eine Racebedingung auf, die beim Update zum Fehler ClusterConnectionException führen. |Dieses Problem kann vermieden werden, indem die neueren Builds genutzt werden. Falls dieses Problem weiterhin besteht, sollten Sie erneut versuchen, das Upgrade durchzuführen. Anschließend sollte es funktionieren.|
|**20**|Internet Explorer|Wenn erweiterte Sicherheitsfunktionen aktiviert sind, können Sie möglicherweise nicht auf Seiten der lokalen Webbenutzeroberfläche zugreifen. | Deaktivieren Sie die erweiterte Sicherheit, und starten Sie den Browser neu.|
|**21.**|Kubernetes-Dashboard | Der *HTTPS*-Endpunkt für das Kubernetes-Dashboard mit SSL-Zertifikat wird nicht unterstützt. | |
|**22.**|Kubernetes |Kubernetes unterstützt für Umgebungsvariablen, die von .NET-Anwendungen verwendet werden, keine Namen mit Doppelpunkt („:“). Dies ist auch erforderlich, damit das IoT Edge-Modul für Event Grid auf Azure Stack Edge-Geräten und in anderen Anwendungen funktioniert. Weitere Informationen finden Sie in der [ASP.NET Core-Dokumentation](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration#environment-variables).|Ersetzen Sie „:“ durch einen doppelten Unterstrich. Weitere Informationen finden Sie unter [Probleme mit Kubernetes](https://github.com/kubernetes/kubernetes/issues/53201).|
|**23.** |Azure Arc und Kubernetes-Cluster |Wenn `yamls` für Ressourcen aus dem Git-Repository gelöscht werden, werden die entsprechenden Ressourcen standardmäßig nicht aus dem Kubernetes-Cluster entfernt.  |Um das Löschen von Ressourcen zu erlauben, wenn sie aus dem Git-Repository gelöscht werden, legen Sie in Arc OperatorParams `--sync-garbage-collection` fest. Weitere Informationen finden Sie unter [Löschen einer Konfiguration](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters). |
|**24.**|NFS |Anwendungen, die auf Ihrem Gerät eingebundene NFS-Freigaben zum Schreiben von Daten verwenden, sollten exklusiven Schreibzugriff verwenden. Dies stellt sicher, dass die Schreibvorgänge auf dem Datenträger erfolgen.| |
|**25.**|Computekonfiguration |In Netzwerkkonfigurationen, in denen Gateways, Switches oder Router auf ARP-Anforderungen (Address Resolution Protocol) für Systeme antworten, die nicht im Netzwerk vorhanden sind, tritt bei der Computekonfiguration ein Fehler auf.| |
|**26.**|Compute und Kubernetes |Wenn Kubernetes als Erstes auf dem Gerät eingerichtet wird, beansprucht es alle verfügbaren GPUs. Daher ist es nicht möglich, nach dem Einrichten von Kubernetes Azure Resource Manager-VMs mit GPUs zu erstellen. |Wenn Ihr Gerät über zwei GPUs verfügt, können Sie eine VM erstellen, die die GPU verwendet, und dann Kubernetes konfigurieren. In diesem Fall verwendet Kubernetes die verbleibende verfügbare GPU. |
|**27.**|Benutzerdefinierte VM-Skripterweiterung |Bei den Windows-VMs, die in einem früheren Release erstellt und auf 2103 aktualisiert wurden, ist ein Problem bekannt. <br> Wenn Sie diesen VMs eine benutzerdefinierte Skripterweiterung hinzufügen, wird der Windows-VM-Gast-Agent (nur Version 2.7.41491.901) beim Update hängen bleiben, was zu einem Timeout bei der Erweiterungsbereitstellung führt. | So umgehen Sie dieses Problem: <ul><li> Stellen Sie mithilfe des Remotedesktopprotokolls (RDP) eine Verbindung mit der Windows-VM her. </li><li> Stellen Sie sicher, dass `waappagent.exe` auf dem Computer ausgeführt wird: `Get-Process WaAppAgent`. </li><li> Wenn `waappagent.exe` nicht ausgeführt wird, starten Sie den `rdagent`-Dienst neu: `Get-Service RdAgent` \| `Restart-Service`. Warten Sie 5 Minuten.</li><li> Beenden Sie den `WindowsAzureGuest.exe`-Prozess, während `waappagent.exe` ausgeführt wird. </li><li>Nachdem Sie den Prozess beendet haben, wird der Prozess erneut mit der neueren Version gestartet.</li><li>Überprüfen Sie mithilfe des folgenden Befehls, ob die Version des Windows VM-Gast-Agents 2.7.41491.971 lautet: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion`.</li><li>[Richten Sie die benutzerdefinierte Skripterweiterung auf der Windows-VM ein](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> |
|**28.**|Virtuelle Computer mit GPU |Vor diesem Release wurde der GPU-VM-Lebenszyklus nicht im Updateflow verwaltet. Daher werden GPU-VMs beim Aktualisieren auf Release 2103 nicht automatisch während des Updates angehalten. Sie müssen die GPU-VMs manuell mit einem `stop-stayProvisioned`-Flag anhalten, bevor Sie Ihr Gerät aktualisieren. Weitere Informationen finden Sie unter [Anhalten oder Herunterfahren des virtuellen Computers](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Alle GPU-VMs, die vor dem Update ausgeführt werden, werden nach dem Update gestartet. In diesen Fällen werden die Workloads, die auf den VMs ausgeführt werden, nicht ordnungsgemäß beendet. Außerdem können die VMs nach dem Update möglicherweise einen unerwünschten Status aufweisen. <br>Alle GPU-VMs, die über `stop-stayProvisioned` vor dem Update angehalten werden, werden nach dem Update automatisch gestartet. <br>Wenn Sie die GPU-VMs über das Azure-Portal anhalten, müssen Sie die VM nach dem Geräteupdate manuell starten.| Wenn Sie GPU-VMs mit Kubernetes ausführen, halten Sie die GPU-VMs direkt vor dem Update an. <br>Wenn die GPU-VMs angehalten werden, übernimmt Kubernetes die GPUs, die ursprünglich von VMs verwendet wurden. <br>Je länger die GPU-VMs sich im angehaltenen Status befinden, desto höher ist die Wahrscheinlichkeit, dass Kubernetes die GPUs übernimmt. |
|**29.**|Multiprozessdienst (Multi-Process Service, MPS) |Wenn die Gerätesoftware und der Kubernetes-Cluster aktualisiert werden, wird die MPS-Einstellung für die Workloads nicht beibehalten.   |[Aktivieren Sie den MPS erneut](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface), und stellen Sie die Workloads erneut bereit, die MPS verwendet haben. |


## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren des Geräts](azure-stack-edge-gpu-install-update.md)
