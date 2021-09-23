---
title: Bereitstellen der Notfallwiederherstellung mithilfe von JetStream
description: Hier erfahren Sie, wie Sie die JetStream-Notfallwiederherstellung (Disaster Recovery, DR) für Ihre private Azure VMware Solution-Cloud und lokale VMware-Workloads bereitstellen.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: a26cbaa4f8c37f3148a86005a637b006572685dc
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315122"
---
# <a name="deploy-disaster-recovery-using-jetstream"></a>Bereitstellen der Notfallwiederherstellung mithilfe von JetStream

[JetStream Disaster Recovery (DR)](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) wird in einer VMware vSphere-Umgebung installiert und über eine vCenter-Plug-In-Appliance verwaltet. Es bietet cloudnativen fortlaufenden Datenschutz (Continuous Data Protection, CDP), der E/A-Vorgänge virtueller Computer (Virtual Machines, VMs) ständig repliziert. Statt Momentaufnahmen in regelmäßigen Abständen zu erfassen, werden Daten kontinuierlich erfasst und repliziert, während sie in den primären Speicher geschrieben werden – mit minimalen Auswirkungen auf gerade ausgeführte Anwendungen.  Infolgedessen können Sie VMs mit deren Daten schnell wiederherstellen und so ein niedrigeres RPO (Recovery Point Objective) erreichen.  

Mit Azure VMware Solution können Sie Daten direkt in einem Wiederherstellungscluster in vSAN oder einem angefügten Dateisystem wie Azure NetApp Files speichern. Die Daten werden über E/A-Filter erfasst, die in vSphere ausgeführt werden. Der zugrunde liegende Datenspeicher kann VMFS, VSAN, vVol oder eine beliebige HCI-Plattform sein. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-disaster-recovery-diagram.png" alt-text="Diagramm der Integration von JetStream DR und Azure VMware Solution." border="false":::

JetStream DR-Software besteht aus drei Hauptkomponenten: 

- Management Server Virtual Appliance (MSA) wird vor dem DR-Schutz installiert und konfiguriert. 

- DR Virtual Appliance (DRVA) ist ein ISO-Image, das vom JetStream DR-MSA automatisch bereitgestellt wird.

- Hostkomponenten (E/A-Filterpakete) 

Die MSA wird zum Installieren und Konfigurieren von Hostkomponenten auf dem Computecluster und danach zum Verwalten von JetStream DR-Software verwendet. Das DRVA führt die DR-Komponenten für den Datenpfad aus. Zur Verbesserung der Skalierbarkeit können mehrere DRVAs gleichzeitig ausgeführt werden. Jedes DRVA hat eine oder mehrere dedizierte Partitionen, die als iSCSI-LUN oder latenzarmer VDISK angefügt sind. Die Partitionen werden zum Verwalten von Replikationsprotokollen und Repositorys für persistente Metadaten verwendet.

In diesem Artikel werden Sie JetStream DR in Ihrer privaten Azure VMware Solution-Cloud und lokalen VMware-Workloads bereitstellen und erfahren, wie Sie sie verwenden.


## <a name="supported-scenarios"></a>Unterstützte Szenarios
Je nach den erforderlichen Schutzdiensten und dem Typ der geschützten privaten Cloud können Sie JetStream DR auf zwei Arten bereitstellen:

- Lokal zu Cloud

- Cloud zu Cloud

### <a name="on-premises-to-cloud-deployment"></a>Lokal-zu-Cloud-Bereitstellung
VMs, die im VMware-basierten Rechenzentrum einer Organisation ausgeführt werden, werden kontinuierlich in Microsoft Azure repliziert. Daher können VMs den Betrieb in Azure VMware Solution fortsetzen, wenn es im lokalen Rechenzentrum zu einem Vorfall kommt. Während die VMs in der Wiederherstellungsumgebung ausgeführt werden, replizieren sie weiterhin Daten, um den Schutz fortzusetzen. Nachdem das lokale Rechenzentrum wiederhergestellt wurde, können die VMs und deren Daten (einschließlich aller neuen Daten, die von den VMs in der Wiederherstellungsumgebung generiert werden) ohne Unterbrechung in ihr ursprüngliches Rechenzentrum zurückkehren.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Diagramm der JetStream-Bereitstellung von lokal zur privaten Azure VMware Solution-Cloud." border="false":::

### <a name="cloud-to-cloud-deployment"></a>Cloud-zu-Cloud-Bereitstellung

Bei dieser Konfiguration hostet Azure VMware Solution Ihre primäre Umgebung in einem einzigen Rechenzentrum. Es schützt die VMs und die Daten, indem sie kontinuierlich in eine andere private Cloud in einem anderen seiner Rechenzentren repliziert werden. Bei einem Vorfall werden VMs und Daten im zweiten Rechenzentrum wiederhergestellt. Dieser Schutz kann bidirektional sein, wobei Rechenzentrum „A“ Rechenzentrum „B“ schützt und umgekehrt.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Diagramm der JetStream-Bereitstellung von privater Azure VMware Solution-Cloud zu einer anderen privaten Cloud." border="false":::


## <a name="prerequisites"></a>Voraussetzungen

- Eine private Azure VMware Solution-Cloud, die als sekundäre Region bereitgestellt wird. 

- Eine Ubuntu Linux-Jumpbox mit einer ExpressRoute-Verbindung zu Ihrer privaten Azure VMware Solution-Cloud. 

- Neueste PowerShell auf der Linux-Jumpbox installiert.

- Neuestes Drittanbietermodul aus dem PowerShell-Katalog installiert.

### <a name="protected-site"></a>Geschützter Standort

Der geschützte Standort hostet einen **Dienstcluster** für Verwaltungsdienste (z. B. vCenter, DNS, Active Directory) und **Computercluster**, in denen geschützte Branchenanwendungen ausgeführt werden. Der geschützte Standort kann lokal sein oder in Azure VMware Solution gehostet werden.

Jeder der folgenden Typen kann verwendet werden:

- Azure Blob Storage

- Azure VMware Solution vSAN

- Ein an Azure VMware Solution angefügtes Dateisystem, z. B. Azure NetApp Files


| **Element**  | **Beschreibung**  |  
| --- | --- |  
| **vCenter Server**  | <ul><li>Unterstützte Version: 6.7</li><li>HTTPS-Port: Bei Verwendung einer Firewall muss HTTPS-Port 443 geöffnet sein.</li><li>Konnektivität: Der JetStream DR Management Server-Appliance-FQDN muss von vCenter aus erreichbar sein. Andernfalls schlägt die Plug-In-Installation fehl.</li><li>Zeit: Die MSA-Uhren von vCenter und JetStream DR müssen synchronisiert werden.</li></ul>  |
| **Distributed Resource Scheduler (DRS)**  | Er wird auf dem Computecluster für den Ressourcenausgleich empfohlen.  |
| **Cluster**  | vSphere-Hosts: VMs, die durch JetStream DR geschützt werden sollen, müssen Teil eines Clusters sein.  |
| **vSphere-Host**  | <ul><li>Unterstützte Version: 6.7U1 (Build #10302608) oder höher</li><li>Konnektivität: vCenter Server-FQDN muss vom Host aus erreichbar sein. Andernfalls schlägt die Hostkonfiguration fehl.</li><li>Zeit: Die MSA-Uhren von vSphere-Hosts und JetStream DR müssen synchronisiert werden.</li><li>CIM-Dienst: Der CIM-Server muss aktiviert sein. Dies ist die Standardeinstellung.</li></ul>  |
| **JetStream DR-MSA**  | <ul><li>CPU: 64 Bit, 4 vCPUs</li><li>Memory: 4 GB</li><li>Speicherplatz: 60 GB</li><li>Netzwerk: Statische oder dynamisch zugewiesene IP-Adressen (DHCP) können verwendet werden. Der FQDN muss bei DNS registriert werden.</li><li>DNS: DNS-Namensauflösung für vSphere-Hosts und vCenter Server</li></ul>  |
| **JetStream DRVA**  | <ul><li>CPU: 4 Kerne</li><li>Memory: 8 GB</li><li>Netzwerk: Statische oder dynamisch zugewiesene IP-Adressen (DHCP) können verwendet werden.</li></ul>  |
| **Replikationsprotokollspeicher**  | Der geschützte Standort sollte ein latenzarmes Flashspeichergerät verfügbar machen, das die Hosts im Cluster gemeinsam nutzen, um eine optimale Leistung zu erzielen. Dieses Gerät kann durch die JetStream DR-Software gesteuert oder von einem Drittanbieter bereitgestellt werden. Es wird als Repository für das Replikationsprotokoll genutzt. Das DRVA und der/die ESXi-Host(s) müssen über iSCSI direkten Zugriff auf diesen Speicher haben.  |
| **Ports**  | Wenn JetStream DR-Software installiert wurde, wird ein Bereich von Ports auf den ESXi-Quellhosts automatisch geöffnet. Deshalb ist bei den meisten Benutzern keine weitere Aktion erforderlich. Wenn aber das lokale/Quell-Setup spezielle Firewallregeln enthält, die diese Ports blockieren, müssen Sie die Ports manuell öffnen.<br /><br />Portbereich: 32873-32878  |



### <a name="recovery-site"></a>Wiederherstellungsstandort

Für die Failoverwiederherstellung wird ein Azure VMware Solution-Cluster des Typs _pilot light_ eingerichtet. Obwohl der Wiederherstellungsstandort im Rahmen des Installationsvorgangs erstellt wird, wird der Wiederherstellungsstandortcluster während des normalen Betriebs nicht vollständig aufgefüllt oder genutzt. Computecluster, für die ein Failover ausgeführt wurde, werden dem Wiederherstellungsstandort bei Bedarf als Reaktion auf ein Notfallereignis hinzugefügt.

### <a name="network"></a>Netzwerk

Zwischen dem geschützten Standort und dem Wiederherstellungsstandort muss ein Netzwerk mit den folgenden Merkmalen eingerichtet werden.

| **Elemente** | **Beschreibung** |
| --- | --- |
| **JetStream DR-MSA**  | Für das MSA ist ein Verwaltungsnetzwerk erforderlich. Dieses Netzwerk wird für den Zugriff auf die JetStream DR RESTful-APIs und für andere Datenpfadaufrufe genutzt. Wenn ein privates Netzwerk zum Herstellen einer Verbindung mit dem Objektspeicher zur Verfügung steht, sollte es der MSA-VM als separates Netzwerk hinzugefügt werden. Wenn kein privates Netzwerk zur Verfügung steht, stellen Sie sicher, dass das Verwaltungsnetzwerk zum Herstellen einer Verbindung mit dem Objektspeicher genutzt werden kann. <br /><br />Ein dediziertes externes Netzwerk kann für den Objektspeicherzugriff genutzt werden. Andernfalls wird der Datenverkehr über das Verwaltungsnetzwerk gesendet.  | 
| **JetStream DRVA**  | Wenn das einzige genutzte Netzwerk das Verwaltungsnetzwerk ist, stellen Sie sicher, dass es Zugriff auf E/A-Filter und den Objektspeicher hat. Wenn innerhalb des Clusters mehrere Netzwerke vorhanden sind, müssen alle an die DRVA-VMs angefügt werden.  | 
| **Wiederherstellung von einem virtuellen Objekt-Cloudgerät (Recovery from Object Cloud Virtual Appliance, RocVA)**  | Wenn das einzige genutzte Netzwerk das Verwaltungsnetzwerk ist, stellen Sie sicher, dass es Zugriff auf den/die ESXi-Host(s) und den Objektspeicher hat. Wenn innerhalb des Clusters mehrere Netzwerke vorhanden sind, müssen alle an die RocVA-VMs angefügt werden. Die RocVA ist eine temporäre VM, die bei Bedarf für die VM-Wiederherstellung automatisch erstellt und entfernt wird, wenn sie nicht mehr benötigt wird.  | 
| **Objektspeicher/Blobspeicher**  | Der Objektspeicher/Blobspeicher sollte sowohl für den geschützten Standort als auch den Wiederherstellungsstandort zugänglich sein.  | 
| **Replikationsprotokollspeicher**  | DRVAs und ESXi-Host(s) müssen über iSCSI direkten Zugriff auf diesen Speicher haben.  | 


## <a name="install-jetstream-dr"></a>Installieren von JetStream DR

Die Installation der JetStream DR steht über die Funktion „Skriptausführung“ im Azure VMware Solution-Portal zur Verfügung. Sie führen die Installation in drei Schritten aus.

:::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="Screenshot: Zugriff auf die verfügbaren JetStream-Skriptausführungen." lightbox="media/run-command/run-command-overview-jetstream.png":::


>[!NOTE]
>Skriptausführungen erfolgen nacheinander in der übermittelten Reihenfolge.

### <a name="check-the-current-state-of-the-system"></a>Überprüfen des aktuellen Systemstatus

Sie führen das Cmdlet `Invoke-PreflightJetDRSystemCheck` aus, um den Status Ihres Systems zu überprüfen und zu ermitteln, ob die Mindestanforderungen für das Skript erfüllt werden. Außerdem wird damit die erforderliche vCenter-Konfiguration zum Ausführen anderer Cmdlets überprüft.  

Das Cmdlet überprüft Folgendes:

- PowerShell
- vCenter-FQDN
- CloudAdmin-Rolle
- VMware-Module

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Skriptausführung** > **Pakete** > **Invoke-PreflightJetDRSystemCheck** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert beträgt ist 60.  |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **checkDRsystem**.  |
   | **Timeout**  | Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie **Benachrichtigungen**, um den Fortschritt anzuzeigen. 



### <a name="check-cluster-configuration"></a>Überprüfen der Clusterkonfiguration

Sie führen das Cmdlet `Invoke-PreflightJetDRInstall` aus, um die folgende Clusterkonfiguration zu überprüfen:

- Ob die Clusterdetails korrekt sind 
- Ob es mindestens vier Hosts (Minimum) gibt
- Ob eine VM mit demselben Namen vorhanden ist, der für die Installation von MSA angegeben wurde
- Ob es in vCenter ein **jetdr**-Plug-In gibt


1. Wählen Sie **Skriptausführung** > **Pakete** > **Invoke-PreflightJetDRInstall** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **VMName** | Name der MSA-VM. Beispiel: **jetstreamServer**. |
   | **Cluster** | Name des Clusters, in dem MSA bereitgestellt wird. Beispiel: **Cluster-1**. |
   | **ProtectedCluster** | Der zu schützende Cluster. Beispiel: **Cluster-1**. |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert beträgt ist 60.  |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **check_jetserverdetails**.  |
   | **Timeout**  | Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie **Benachrichtigungen**, um den Fortschritt anzuzeigen.  

1. Wenn Fehler gemeldet werden, können Sie zum nächsten Schritt wechseln, um JetDR-MSA bereitzustellen.


### <a name="deploy-jetdr-msa"></a>Bereitstellen von JetDR-MSA

Sie führen das Cmdlet `Install-JetDR` zum Bereitstellen des JetDR-MSA, Registrieren von vCenter beim JetDR-MSA und Konfigurieren von Clustern aus. Die Bereitstellung lädt das JetDR-Paket von Microsoft Server Media (MMS) herunter und erstellt einen neuen Benutzer mit zugewiesenen erhöhten Rechten. 

1. Wählen Sie **Skriptausführung** > **Pakete** > **Install-JetDR** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **Network** | Netzwerkzuordnung für das bereitzustellende MSA. Beispiel: **VM-Netzwerk**.  |
   | **HostName** | Hostname (FQDN) des bereitzustellenden MSA.  |
   | **Credential** | Anmeldeinformationen des Stammbenutzers des bereitzustellenden MSA.   |
   | **Gateway** | Gateway des bereitzustellenden MSA. Lassen Sie das Feld für DHCP leer.  |
   | **Dns** | DNS-IP, die MSA verwenden sollte. Lassen Sie das Feld für DHCP leer.  |
   | **MSIp** | IP-Adresse der bereitzustellenden MSA. Lassen Sie das Feld für DHCP leer.  |
   | **Netzmaske** | Netzmaske des bereitzustellenden MSA. Lassen Sie das Feld für DHCP leer.  |
   | **Cluster** | Name des Clusters, in dem MSA bereitgestellt wird. Beispiel: **Cluster-1**. |
   | **VMName** | Name der MSA-VM. Beispiel: **jetstreamServer**. |
   | **Datenspeicher** | Datenspeicher, in dem MSA bereitgestellt wird.  |
   | **ProtectedCluster** | Der zu schützende Cluster. Beispiel: **Cluster-1**. |
   | **RegisterWithIp** | Registrieren Sie MSA mit „Ip“ statt des Hostnamens. <ul><li>„True“, wenn der Hostname des MSA im DNS nicht registriert ist.</li><li>„False“, wenn der Hostname des MSA im DNS registriert ist. </li></ul> |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert beträgt ist 60.  |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **check_jetserverdetails**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie **Benachrichtigungen**, um den Fortschritt anzuzeigen.  


## <a name="uninstall-jetstream-dr"></a>Deinstallieren von JetStream DR

Sie deinstallieren JetStream DR in zwei Schritten. 


### <a name="check-current-state-of-the-jetstream-appliance"></a>Überprüfen des aktuellen Status der JetStream-Appliance

Sie führen das Cmdlet `Invoke-PreflightJetDRUninstall` aus, um die vorhandene MSA-VM- und Clusterkonfiguration zu diagnostizieren. Es überprüft den aktuellen Status der JetStream DR-Appliance und ob die minimalen Anforderungen für das Skript erfüllt werden:

- Ob die Clusterdetails korrekt sind 
- Ob es mindestens vier Hosts (Minimum) gibt
- Ob vCenter beim MSA registriert wurde

1. Wählen Sie **Skriptausführung** > **Pakete** > **Invoke-PreflightJetDRUninstall** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **MSIp** | IP-Adresse der MSA-VM.  |
   | **Credential** | Anmeldeinformationen des Stammbenutzers der MSA-VM. Sie müssen zum Zeitpunkt der Installation identisch sein.  |
   | **ProtectedCluster** | Name des geschützten Clusters, z. B. **Cluster-1**.  Dies muss der Cluster sein, der zum Zeitpunkt der Installation bereitgestellt wurde. |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert beträgt ist 60.  |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **uninstallcheck_jetserverdetails**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie **Benachrichtigungen**, um den Fortschritt anzuzeigen.  




### <a name="uninstall-jetdr"></a>Deinstallieren von JetDR

Sie führen das Cmdlet `Uninstall-JetDR` aus, um JetStream DR und die zugehörigen Komponenten zu deinstallieren.  Die Konfiguration des Clusters wird aufgehoben, die Registrierung von vCenter beim MSA wird aufgehoben, und dann wird der Benutzer entfernt.

1. Wählen Sie **Skriptausführung** > **Pakete** > **Uninstall-JetDR** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **MSIp** | IP-Adresse der MSA-VM.  |
   | **Credential** | Anmeldeinformationen des Stammbenutzers der MSA-VM. Sie müssen zum Zeitpunkt der Installation identisch sein.   |
   | **ProtectedCluster** | Name des geschützten Clusters, z. B. **Cluster-1**.  Dies muss der Cluster sein, der zum Zeitpunkt der Installation bereitgestellt wurde. |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert beträgt ist 60.  |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **uninstallcheck_jetserverdetails**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie **Benachrichtigungen**, um den Fortschritt anzuzeigen.  


## <a name="next-steps"></a>Nächste Schritte

- [JetStream DR für Azure VMware Solution – Vollständige Demo](https://vimeo.com/475620858/2ce9413248)
   
   - [Erste Schritte mit JetStream DR für Azure VMware Solution](https://vimeo.com/491880696/ec509ff8e3)

   - [Konfiguration und Schutz von VMs](https://vimeo.com/491881616/d887590fb2)

   - [Failover zu Azure VMware Solution](https://vimeo.com/491883564/ca9fc57092)

   - [Failback zu lokal](https://vimeo.com/491884402/65ee817b60)

- [Infrastruktureinrichtung für JetStream DR für Azure VMware Solution](https://vimeo.com/480574312/b5386a871c) (*technische Details, keine Sprachspur*)
