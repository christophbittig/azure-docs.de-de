---
title: Versionshinweise zu Azure Stack Edge Pro 2103
description: In diesem Artikel werden schwerwiegende ungelöste Probleme und Lösungen für das Release 2103 von Azure Stack Edge Pro beschrieben.
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: bb1250b4ed5c2b447f0f0f8edb5ec50d59ba079f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356271"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Versionshinweise zu Azure Stack Edge 2103

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In den folgenden Versionshinweisen werden schwerwiegende ungelöste und behobene Probleme für das Release 2103 für Azure Stack Edge-Geräte beschrieben. Diese Versionshinweise gelten nur für Geräte vom Typ „Azure Stack Edge Pro – GPU“, „Azure Stack Edge Pro R“ und „Azure Stack Edge Mini R“. Funktionen und Probleme, die nur für ein bestimmtes Modell gelten, werden jeweils entsprechend gekennzeichnet.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres Geräts die Informationen in den Versionshinweisen sorgfältig durch.

Dieser Artikel bezieht sich auf das Release von **Azure Stack Edge Pro 2103**, das der Softwareversionsnummer **2.2.1540.2890** zugeordnet ist. Diese Software kann auf Ihr Gerät angewendet werden, wenn Sie mindestens Azure Stack Edge 2010-Software (2.1.1377.2170) ausführen.

## <a name="whats-new"></a>Neuigkeiten

Die folgenden neuen Funktionen sind im Release Azure Stack Edge 2103 verfügbar. 
 
- **Neue Features für VMs**: Ab diesem Release können Sie die folgenden Verwaltungsvorgänge auf den VMs über das Azure-Portal ausführen:
    - Hinzufügen mehrerer Netzwerkschnittstellen zu einer vorhandenen VM oder deren Entfernen.
    - Hinzufügen mehrerer Datenträger zu einer vorhandenen VM oder deren Entfernen.
    - Ändern der Größe der VM.
    - Hinzufügen benutzerdefinierter Daten beim Bereitstellen einer Windows- oder Linux-VM.

  Sie können auch eine [Verbindung mit der VM-Konsole auf Ihrem Gerät herstellen](azure-stack-edge-gpu-connect-virtual-machine-console.md) und jegliche VM-Probleme behandeln.

- **Herstellen einer Verbindung mit der PowerShell-Schnittstelle über HTTPS**: Ab diesem Release können Sie keine PowerShell-Remotesitzung mehr über *http* auf einem Gerät öffnen. Standardmäßig wird *https* für alle Sitzungen verwendet. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

- **Verbesserungen für Compute**: Mehrere Verbesserungen einschließlich der folgenden wurden vorgenommen:

    - **Gesamtqualität der Computeplattform**. Dieses Release enthält Fehlerbehebungen zur Verbesserung der Gesamtqualität der Computeplattform. Weitere Informationen finden Sie unter [Im Release 2103 behobene Probleme](#issues-fixed-in-2103-release). 
    - **Komponenten der Computeplattform**. Sicherheitsupdates wurden auf das Compute-VM-Image angewendet. IoT Edge- und Azure Arc für Kubernetes-Versionen wurden ebenfalls aktualisiert.
    - **Diagnose**: Zum Überprüfen von Ressourcen- und Netzwerkbedingungen wird eine neue API veröffentlicht. Sie können eine Verbindung mit der PowerShell-Schnittstelle des Geräts herstellen und mit dem `Test-HcsKubernetesStatus`-Befehl die Netzwerkbereitschaft des Geräts überprüfen.
    - Die **Protokollsammlung** verbessert das Debuggen. 
    - Mit der **Warnungsinfrastruktur** können Sie IP-Adressenkonflikte bei Compute-IP-Adressen erkennen. 
    - **Gemischte Workload** von Kubernetes und lokalem Azure Resource Manager. 

- **Standardmäßiges proaktives Protokollieren**: Ab diesem Release ist die proaktive Protokollsammlung auf Ihrem Gerät standardmäßig aktiviert. Mit diesem Feature kann Microsoft proaktiv Protokolle auf Basis der Systemintegritätsindikatoren sammeln, um die effiziente Behandlung von Geräteproblemen zu unterstützen. Weitere Informationen finden Sie unter [Proaktive Protokollsammlung auf Ihrem Gerät](azure-stack-edge-gpu-proactive-log-collection.md).

## <a name="issues-fixed-in-2103-release"></a>Im Release 2103 behobene Probleme

In der folgenden Tabelle sind die Probleme aufgelistet, die in vorherigen Releases festgestellt und im aktuellen Release behoben wurden.

| Nein. | Funktion | Problem | 
| --- | --- | --- |
|**1.**|Kubernetes |Die Edgecontainerregistrierung funktioniert nicht, wenn ein Webproxy aktiviert ist.|
|**2.**|Kubernetes |Die Edgecontainerregistrierung funktioniert nicht mit IoT Edge-Modulen.| 

## <a name="known-issues-in-2103-release"></a>Bekannte Probleme im Release 2103

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme im Release 2103.

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
|**1.**|Previewfunktionen |In diesem Release sind die folgenden Funktionen als Vorschauversionen verfügbar: Lokaler Azure Resource Manager, VMs, Cloudverwaltung von VMs, Kubernetes-Cloudverwaltung, Kubernetes mit Azure Arc-Unterstützung, VPN für Azure Stack Edge Pro R und Azure Stack Edge Mini R, Multiprozessdienst (MPS) für Azure Stack Edge Pro – GPU.  |Diese Funktionen werden in einem späteren Release allgemein verfügbar sein. |
|**2.**|Virtuelle Computer mit GPU |Vor diesem Release wurde der GPU-VM-Lebenszyklus nicht im Updateflow verwaltet. Daher werden GPU-VMs beim Aktualisieren auf Release 2103 nicht automatisch während des Updates angehalten. Sie müssen die GPU-VMs manuell mit einem `stop-stayProvisioned`-Flag anhalten, bevor Sie Ihr Gerät aktualisieren. Weitere Informationen finden Sie unter [Anhalten oder Herunterfahren des virtuellen Computers](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Alle GPU-VMs, die vor dem Update ausgeführt werden, werden nach dem Update gestartet. In diesen Fällen werden die Workloads, die auf den VMs ausgeführt werden, nicht ordnungsgemäß beendet. Außerdem können die VMs nach dem Update möglicherweise einen unerwünschten Status aufweisen. <br>Alle GPU-VMs, die über `stop-stayProvisioned` vor dem Update angehalten werden, werden nach dem Update automatisch gestartet. <br>Wenn Sie die GPU-VMs über das Azure-Portal anhalten, müssen Sie die VM nach dem Geräteupdate manuell starten.| Wenn Sie GPU-VMs mit Kubernetes ausführen, halten Sie die GPU-VMs direkt vor dem Update an. <br>Wenn die GPU-VMs angehalten werden, übernimmt Kubernetes die GPUs, die ursprünglich von VMs verwendet wurden. <br>Je länger die GPU-VMs sich im angehaltenen Status befinden, desto höher ist die Wahrscheinlichkeit, dass Kubernetes die GPUs übernimmt. |
|**3.**|Benutzerdefinierte VM-Skripterweiterung |Bei den Windows-VMs, die in einem früheren Release erstellt und auf 2103 aktualisiert wurden, ist ein Problem bekannt. <br> Wenn Sie diesen VMs eine benutzerdefinierte Skripterweiterung hinzufügen, wird der Windows-VM-Gast-Agent (nur Version 2.7.41491.901) beim Update hängen bleiben, was zu einem Timeout bei der Erweiterungsbereitstellung führt. | So umgehen Sie dieses Problem: <ul><li> Stellen Sie mithilfe des Remotedesktopprotokolls (RDP) eine Verbindung mit der Windows-VM her. </li><li> Stellen Sie sicher, dass `waappagent.exe` auf dem Computer ausgeführt wird: `Get-Process WaAppAgent`. </li><li> Wenn `waappagent.exe` nicht ausgeführt wird, starten Sie den `rdagent`-Dienst neu: `Get-Service RdAgent` \| `Restart-Service`. Warten Sie 5 Minuten.</li><li> Beenden Sie den `WindowsAzureGuest.exe`-Prozess, während `waappagent.exe` ausgeführt wird. </li><li>Nachdem Sie den Prozess beendet haben, wird der Prozess erneut mit der neueren Version gestartet.</li><li>Überprüfen Sie mithilfe des folgenden Befehls, ob die Version des Windows VM-Gast-Agents 2.7.41491.971 lautet: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion`.</li><li>[Richten Sie die benutzerdefinierte Skripterweiterung auf der Windows-VM ein](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> | 
|**4.**|Multiprozessdienst (Multi-Process Service, MPS) |Wenn die Gerätesoftware und der Kubernetes-Cluster aktualisiert werden, wird die MPS-Einstellung für die Workloads nicht beibehalten.   |[Aktivieren Sie den MPS erneut](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface), und stellen Sie die Workloads erneut bereit, die MPS verwendet haben. |


## <a name="known-issues-from-previous-releases"></a>Bekannte Probleme aus vorherigen Releases

Die folgende Tabelle enthält eine Zusammenfassung der bereits aus früheren Releases bekannten Probleme.

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | Zum Erstellen einer SQL-Datenbank ist Administratorzugriff erforderlich.   |Führen Sie anstelle der Schritte 1-2 unter [Tutorial: Speichern von Daten im Edge-Bereich mit SQL Server-Datenbanken](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) die folgenden Schritte aus. <ul><li>Aktivieren Sie in der lokalen Benutzeroberfläche des Geräts die Computeschnittstelle. Wählen Sie **Compute > Portnr. > Für Compute aktivieren > Anwenden** aus.</li><li>Herunterladen von `sqlcmd` aus dem [SQLCMD-Hilfsprogramm](/sql/tools/sqlcmd-utility) auf Ihren Clientcomputer </li><li>Stellen Sie eine Verbindung mit der IP-Adresse der Computeschnittstelle (dem aktivierten Port) her, und fügen Sie am Ende der Adresse „,1401“ hinzu.</li><li>Der endgültige Befehl sieht wie folgt aus: sqlcmd -S {IP-Adresse-der-Schnittstelle},1401 -U SA -P "Starkes!Kennw0rt".</li>Danach sollten die Schritte 3 und 4 aus der aktuellen Dokumentation identisch sein. </li></ul> |
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


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren des Geräts](azure-stack-edge-gpu-install-update.md)