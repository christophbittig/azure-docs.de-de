---
title: Bereitstellen der Notfallwiederherstellung mithilfe von JetStream DR (Preview)
description: Lernen Sie, wie Sie JetStream DR für Ihre Azure VMware Solution Private Cloud und lokale VMware-Workloads implementieren.
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: references_regions
ms.openlocfilehash: 75bb77ad575f96e7605fe6a19e12d7129c28dac6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128554437"
---
# <a name="deploy-disaster-recovery-using-jetstream-dr-preview"></a>Bereitstellen der Notfallwiederherstellung mithilfe von JetStream DR (Preview)

>[!IMPORTANT]
>JetStream DR auf Azure VMware Solution (Preview) befindet sich derzeit in der öffentlichen Vorschau.
>Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
>Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[JetStream DR](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) ist eine cloudnative Lösung für die Notfallwiederherstellung, mit der die Ausfallzeiten virtueller Computer (VMs) im Falle einer Katastrophe minimiert werden. Instanzen von JetStream DR werden sowohl an den geschützten als auch an den Wiederherstellungsstandorten bereitgestellt. 

JetStream basiert auf der Grundlage von Continuous Data Protection (CDP) unter Verwendung des [VMware VAIO-Frameworks](https://core.vmware.com/resource/vmware-vsphere-apis-io-filtering-vaio), das minimalen oder nahezu keinen Datenverlust ermöglicht. JetStream DR bietet das gewünschte Maß an Schutz für geschäfts- und unternehmenskritische Anwendungen. Darüber hinaus ermöglicht es eine kosteneffiziente DR durch den Einsatz minimaler Ressourcen am DR-Standort und die Verwendung kostengünstiger Cloud-Speicher wie [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/).

In diesem Artikel implementieren Sie JetStream DR für Ihre Azure VMware Solution Private Cloud und lokale VMware-Workloads.

Um mehr über JetStream DR zu lernen, siehe:

- [JetStream-Lösungskurzbeschreibung](https://www.jetstreamsoft.com/2020/09/28/solution-brief-disaster-recovery-for-avs/)

- [JetStream DR auf Azure Marketplace](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)

- [JetStream-Wissensbasisartikel](https://www.jetstreamsoft.com/resources/knowledge-base/)


## <a name="core-components-of-the-jetstream-dr-solution"></a>Kernkomponenten der JetStream DR-Lösung

| Elemente | Beschreibung |
| --- | --- |
| **JetStream Verwaltungsserver virtuelles Gerät (MSA)**  | MSA ermöglicht sowohl die Tag-0- als auch die Tag-2-Konfiguration, z.B. primäre Standorte, Schutzdomänen und die Wiederherstellung von VMs.  MSA wird vom Cloud-Administrator auf einem vSphere-Knoten installiert. Die MSA implementiert ein vCenter-Plugin, mit dem Sie JetStream DR nativ über vCenter verwalten können. Die MSA verwaltet keine Replikationsdaten geschützter VMs.  | 
| **JetStream DR virtuelles Gerät (DRVA)**  | Die Linux-basierte VM Appliance empfängt die Replikationsdaten geschützter VMs vom Quell-ESXi-Host. Sie ist für die Speicherung der Replikationsdaten am DR-Standort verantwortlich, normalerweise in einem Objektspeicher wie Azure Blob Storage. Abhängig von der Anzahl der geschützten VMs und der zu replizierenden Speichermenge kann der Administrator der privaten Cloud eine oder mehrere DRVA-Instanzen erstellen.  | 
| **JetStream ESXi-Hostkomponenten (E/A-Filterpakete)**  | JetStream-Software, die auf jedem ESXi-Host installiert ist, der für JetStream DR konfiguriert ist. Der Hosttreiber fängt eine vSphere-VMs-E/A ab und sendet die Replikationsdaten an das DRVA.   | 
| **JetStream-Schutzdomäne**  | Logische Gruppe von VMs, die zusammen mit den gleichen Richtlinien und Ausführungsvorschriften geschützt werden. Die Daten für alle VMs in einer Schutzdomäne werden in derselben Azure Blob-Containerinstanz gespeichert. Dieselbe DRVA-Instanz verarbeitet die Replikation zu Remote-DR-Speicherung für alle VMs in einer Schutzdomäne.   | 
| **Azure Blob Storage-Container**  | Die replizierten Daten der geschützten VMs werden in Azure-Blobs gespeichert. JetStream-Software erstellt eine Azure Blob-Containerinstanz für jede JetStream-Schutzdomäne.    | 



## <a name="jetstream-scenarios-on-azure-vmware-solution"></a>JetStream-Szenarien auf Azure VMware Solution
Sie können JetStream DR mit  der Azure VMware Solution für die folgenden zwei Szenarien benutzen:  

- Lokale VMware zur Azure VMware Solution Notfallwiederherstellung

- Azure VMware Solution zur Azure VMware Solution DR

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>Szenario 1: Lokale VMware zur Azure VMware Solution Notfallwiederherstellung

In diesem Szenario ist der primäre Standort Ihre lokale VMware-Umgebung, und der DR-Standort ist eine Azure VMware Solution private Cloud.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Diagramm der JetStream-Bereitstellung von lokal zur privaten Azure VMware Solution-Cloud." border="false" lightbox="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png":::

### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>Szenario 2:Azure VMware Solution zur Azure VMware Solution DR

In diesem Szenario ist der primäre Standort eine Azure VMware Solution private Cloud. Der Standort für die Notfallwiederherstellung eine private Cloud einer Azure VMware Solution in einer anderen Azure-Region ist. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Diagramm der JetStream-Bereitstellung von privater Azure VMware Solution-Cloud zu einer anderen privaten Cloud." border="false" lightbox="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png":::


## <a name="prerequisites"></a>Voraussetzungen


### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>Szenario 1: Lokale VMware zur Azure VMware Solution Notfallwiederherstellung 

- Eeine private Cloud einer Azure VMware Solution, die mit mindestens drei Knoten in der Zielregion für die DR bereitgestellt wird.  


   :::image type="content" source="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png" alt-text="Diagramm, das die erste Voraussetzung für die Notfallwiederherstellungslösung auf Azure VMware Solution zeigt." lightbox="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png":::


- Zwischen den JetStream-Geräten des primären Standorts und der Azure Storage Blobinstanz konfigurierte Netzwerkkonnektivität. 

- [Richten Sie JetStream DR](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) aus dem Azure Marketplace ein, und abonnieren Sie sie, um die JetStream DR-Software herunterzuladen.

- [Azure Blob Storage Konto,](../storage/common/storage-account-create.md) das entweder mit der Standard- oder Premium Performance-Ebene erstellt wurde. [Wählen Sie unter Zugriffsebene die Option **Heiß** aus.](../storage/blobs/storage-blob-storage-tiers.md) 

   >[!NOTE]
   >Die Option **Hierarchischen Namespace aktivieren** für das Blob wird nicht unterstützt.   

- Ein NSX-T-Netzwerksegment, das für Azure VMware Solution private Cloud konfiguriert ist und optional DHCP für das Segment für die virtuellen JetStream-Geräte aktiviert.  

- Ein DNS-Server, der zum Auflösen der IP-Adressen von Azure VMware Solution vCenter, Azure VMware Solution ESXi-Hosts, Azure Storage-Konto und des JetStream Marketplace-Diensts für die virtuellen JetStream-Geräte konfiguriert ist. 



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>Szenario 2:Azure VMware Solution zur Azure VMware Solution DR

- Azure VMware Solution private Cloud, die mit mindestens drei Knoten in der primären und sekundären Region bereitgestellt ist.  
 
- Zwischen den JetStream-Geräten des primären Standorts und der Azure Storage Blobinstanz konfigurierte Netzwerkkonnektivität. 

- [Richten Sie JetStream DR](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) aus dem Azure Marketplace ein, und abonnieren Sie sie, um die JetStream DR-Software herunterzuladen.

- [Azure Blob Storage Konto,](../storage/common/storage-account-create.md) das entweder mit der Standard- oder Premium Performance-Ebene erstellt wurde. [Wählen Sie unter Zugriffsebene die Option **Heiß** aus.](../storage/blobs/storage-blob-storage-tiers.md) 

   >[!NOTE]
   >Die Option **Hierarchischen Namespace aktivieren** für das Blob wird nicht unterstützt.   

- Ein NSX-T-Netzwerksegment, das für Azure VMware Solution private Cloud konfiguriert ist und optional DHCP für das Segment für die virtuellen JetStream-Geräte aktiviert.  

- Ein DNS-Server, der sowohl am primären als auch am DR-Standort konfiguriert ist, um die IP-Adressen von Azure VMware Solution vCenter, Azure VMware Solution ESXi-Hosts, Azure Storage-Konto und den JetStream Marketplace-Dienst für die virtuellen JetStream-Geräte aufzulösen. 


Weitere lokale JetStream-DR-Voraussetzungen finden Sie in der [Führungslinie zur JetStream-Vorinstallation.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/pre-installation-guidelines/)





## <a name="install-jetstream-dr-on-azure-vmware-solution"></a>Installieren von JetStream DR auf Azure VMware Solution  
 
Sie können diese Schritte für beide unterstützten Szenarien ausführen. 
 
1. Installieren Sie JetStream DR in Ihrem lokalen Rechenzentrum gemäß der [JetStream-Dokumentation.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/installing-jetstream-dr-software/)  

1. Installieren Sie JetStream DR in Ihrer privaten Azure VMware Solution Cloud mit einem Befehl zur Ausführung. Wählen Sie [im Azure-Portal](https://portal.azure.com)**Befehl zur Ausführung** > **Pakete** > **JSDR.Konfiguration** aus.  

   :::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="Screenshot: Zugriff auf die verfügbaren JetStream-Skriptausführungen." lightbox="media/run-command/run-command-overview-jetstream.png":::
 
   >[!NOTE]
   >Der standardmäßige CloudAdmin-Benutzer in Azure VMware Solution verfügt nicht über ausreichende Berechtigungen, um JetStream DR zu installieren.  Azure VMware Solution ermöglicht eine vereinfachte und automatisierte Installation von JetStream DR, indem der Befehl Azure VMware Solution Run für JetStream DR aufgerufen wird.  
 

1. Führen Sie das Cmdlet **Invoke-PreflightJetDRInstall** aus, das überprüft, ob die Voraussetzungen für die Installation von JetStream DR erfüllt sind. Beispielsweise überprüft.es die erforderliche Anzahl von Hosts, Clusternamen und eindeutigen VM-Namen. 

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **Network**  | Name des NSX-T-Netzwerksegments, in dem Sie den jetStream MSA bereitstellen müssen.  |
   | **Datenspeicher**  | Name des Datenspeichers, in dem Sie den MSA bereitstellen.  |
   | **ProtectedCluster** | Der Name des zu schützenden Azure VMware Solution privaten Cloudclusters, z.B. **Cluster-1**.  Sie können nur einen Clusternamen angeben. |
   | **Cluster** | Name des Azure VMware Solution privaten Clusters, in dem der JetStream-MSA bereitgestellt wird, z.B. **Cluster-1**. |
   | **VMName** | Name des JetStream-MSA-WMs, z.B. **jetstreamServer.** |
   | **Name für Ausführung angeben**  | Alphanumerischer Name der Ausführung, z. B. **Invoke-PreflightJetDRInstall-Exec1**. Er wird verwendet, um zu überprüfen, ob das Cmdlet erfolgreich ausgeführt wurde. |
   | **Timeout**  | Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. [Zeigen Sie den Status der Ausführung an.](concepts-run-command.md#view-the-status-of-an-execution)
   

## <a name="install-the-jetstream-dr-msa"></a>Installieren des JetStream DR MSA

Azure VMware Solution unterstützt die Installation von JetStream mit statischen IP-Adressen oder DHCP-basierten IP-Adressen.  

### <a name="static-ip-address"></a>Statische IP-Adresse

1. Wählen Sie **Befehl zur Ausführung** > **Pakete** > **Install-JetDRWithStaticIP** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.


   | **Feld** | **Wert** |
   | --- | --- |
   | **ProtectedCluster** | Der Name des zu schützenden Azure VMware Solution privaten Cloudclusters, z. B. **Cluster-1**.  Sie können während der Installation nur einen Clusternamen angeben. |
   | **Datenspeicher**  | Der Name des Datenspeichers, in dem Sie den JetStream-MSA bereitstellen.  |
   | **VMName** | Name des JetStream-MSA-WMs, z. B. **jetstreamServer**. |
   | **Cluster** | Name des Azure VMware Solution privaten Clusters, in dem der JetStream-MSA bereitgestellt wird, z. B. **Cluster-1**. |
   | **Netzmaske** | Netzmaske des bereitzustellenden MSA, z. B. **22** oder **24**. |
   | **MSIp** | IP-Adresse des JetStream MSA VM.   |
   | **Dns** | DNS-IP, die der JetStream-MSA-VM verwenden soll.   |
   | **Gateway** | IP-Adresse des Netzwerkgateways für den virtuellen JetStream-MSA-VM.  |
   | **Credential**  |  Anmeldeinformationen des Stammbenutzers des JetStream MSA VM.   |
   | **HostName** | Hostname (FQDN) des JetStream-MSA-VM.  |
   | **Network**  | Name des NSX-T-Netzwerksegments, in dem Sie den jetStream MSA bereitstellen müssen. |
   | **Name für Ausführung angeben**  | Alphanumerischer Name der Ausführung, z.B. **Install-JetDRWithStaticIP-Exec1**.   Er wird verwendet, um zu überprüfen, ob das Cmdlet erfolgreich ausgeführt wurde.  |


1. [Zeigen Sie den Status der Ausführung an](concepts-run-command.md#view-the-status-of-an-execution).


### <a name="dhcp-based-ip-address"></a>DHCP-basierte IP-Adresse

In diesem Schritt wird auch das JetStream vSphere Installation Bundle (VIB) auf den Clustern installiert, die DR-Schutz benötigen. 

1. WÄhlen Sie **Befehl zur Ausführung** > **Pakete** > **Install-JetDRWithDHCP** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **ProtectedCluster** | Der Name des zu schützenden Azure VMware Solution privaten Cloudclusters, z. B. **Cluster-1**.  Sie können während der Installation nur einen Clusternamen angeben. |
   | **Datenspeicher**  | Der Name des Datenspeichers, in dem Sie den JetStream-MSA bereitstellen.  |
   | **VMName** | Name des JetStream-MSA-WMs, z. B. **jetstreamServer**. |
   | **Cluster** | Name des Azure VMware Solution privaten Clusters, in dem der JetStream-MSA bereitgestellt wird, z. B. **Cluster-1**. |
   | **Credential**  |  Anmeldeinformationen des Stammbenutzers des JetStream MSA VM.   |
   | **HostName** | Hostname (FQDN) des JetStream-MSA-VM.  |
   | **Network**  | Name des NSX-T-Netzwerksegments, in dem Sie den jetStream MSA bereitstellen müssen. |
   | **Name für Ausführung angeben**  | Alphanumerischer Name der Ausführung, z. B. **Install-JetDRWithDHCP-Exec1**.   Er wird verwendet, um zu überprüfen, ob das Cmdlet erfolgreich ausgeführt wurde.  |
 
 
1. [Zeigen Sie den Status der Ausführung an](concepts-run-command.md#view-the-status-of-an-execution).
 
 
## <a name="add-jetstream-dr-to-new-azure-vmware-solution-clusters"></a>Hinzufügen von JetStream DR zu neuen Azure VMware Solution Clustern  


1. Wählen Sie **Befehl zur Ausführung** > **Pakete** > **Enable-JetDRForCluster** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **ProtectedCluster** | Der Name des zu schützenden Azure VMware Solution privaten Cloudclusters, z. B. **Cluster-1**.  Sie können während der Installation nur einen Clusternamen angeben. |
   | **Credential**  |  Anmeldeinformationen des Stammbenutzers des JetStream MSA VM.   |
   | **MSIp** | IP-Adresse des JetStream MSA VM.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name der Ausführung, z. B. **Enable-JetDRForCluster-Exec1**.   Er wird verwendet, um zu überprüfen, ob das Cmdlet erfolgreich ausgeführt wurde.  |
  
1. [Zeigen Sie den Status der Ausführung an](concepts-run-command.md#view-the-status-of-an-execution).
 
 
 

## <a name="configure-jetstream-dr"></a>Konfigurieren von JetStream DR 
 
In diesem Abschnitt wird nur eine Übersicht der Schritte gegeben, die zum Konfigurieren der JetStream-Notfallwiederherstellung erforderlich sind.  Ausführliche Beschreibungen und Schritte finden Sie in der Dokumentation [Konfigurieren von JetStream-DR](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/configuring-jetstream-dr/). 
 
Sobald JetStream DR MSA und JetStream VIB auf den Azure VMware Solution Clustern installiert sind, verwenden Sie das JetStream-Portal, um die verbleibenden Konfigurationsschritte auszuführen. 



1. Greifen Sie über die vCenter-Appliance auf das JetStream-Portal zu.

1. [Fügen Sie eine externe Speichersite hinzu.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/add-a-storage-site/)  

1. [Stellen Sie eine JetStream DRVA-Appliance bereit.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/deploy-a-dr-virtual-appliance/) 

1. [Erstellen Sie ein JetStream-Replikationsprotokollspeichervolumen](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-replication-log-store-volume/) mithilfe eines der Datenspeicher, die für den Azure VMware Solution Cluster verfügbar sind. 

   >[!TIP]
   >Für das Replikationsprotokoll wird ein schneller lokaler Speicher bevorzugt, z.B. ein vSAN-Datenspeicher. 
 
1. [Erstellen Sie eine mit JetStream geschützte Domäne.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-protected-domain/) Sie stellen den Azure Blob Storage-Standort, die JetStream-DRVA-Instanz und das Replikationsprotokoll bereit, die in den vorherigen Schritten erstellt wurden. 

1. [Wählen Sie die VMs aus](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/select-vms-for-protection/), die Sie schützen möchten, und starten Sie dann den [VM-Schutz](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/start-vm-protection/).

 
Weitere Konfigurationsschritte für JetStream DR, z.B. das Erstellen eines Failover-Runbooks, das Aufrufen eines Failovers zum DR-Standort und das Aufrufen des Failbacks zum primären Standort, finden Sie in der Dokumentation zum [JetStream-Administrator-Führungsliniendokumentation](https://www.jetstreamsoft.com/portal/jetstream-article-categories/product-manual/).  
 
## <a name="disable-jetstream-dr-on-an-azure-vmware-solution-cluster"></a>Deaktivieren von JetStream DR in einem Azure VMware Solution-Cluster  
 
Dieses Cmdlet deaktiviert JetStream DR nur in einem der Cluster und deinstalliert JetStream DR nicht vollständig. 

1. Wählen Sie **Befehl zur Ausführung** > **Pakete**  > **Disable-JetDRForCluster** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **ProtectedCluster** | Der Name des zu schützenden Azure VMware Solution privaten Cloudclusters, z. B. **Cluster-1**.  Sie können während der Installation nur einen Clusternamen angeben. |
   | **Credential**  |  Anmeldeinformationen des Stammbenutzers des JetStream MSA VM.   |
   | **MSIp** | IP-Adresse des JetStream MSA VM.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name der Ausführung, **z.B. Disable-JetDRForCluster-Exec1.**   Er wird verwendet, um zu überprüfen, ob das Cmdlet erfolgreich ausgeführt wurde.  |

1. [Zeigen Sie den Status der Ausführung an](concepts-run-command.md#view-the-status-of-an-execution).


 
## <a name="uninstall-jetstream-dr"></a>Deinstallieren von JetStream DR  

1. Wählen Sie **Skriptausführung** > **Pakete** > **Invoke-PreflightJetDRUninstall** aus. Dieses Cmdlet überprüft, ob der Cluster über mindestens vier Hosts verfügt (mindestens erforderlich). 

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **ProtectedCluster** | Der Name des zu schützenden Azure VMware Solution privaten Cloudclusters, z. B. **Cluster-1**.  Sie können während der Installation nur einen Clusternamen angeben. |
   | **Credential**  |  Anmeldeinformationen des Stammbenutzers des JetStream MSA VM.   |
   | **MSIp** | IP-Adresse des JetStream MSA VM.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name der Ausführung, z.B. **Invoke-PreflightJetDRUninstall-Exec1.**   Er wird verwendet, um zu überprüfen, ob das Cmdlet erfolgreich ausgeführt wurde.  |

1. [Zeigen Sie den Status der Ausführung an](concepts-run-command.md#view-the-status-of-an-execution).

1. Wählen Sie nach erfolgreichem Abschluss des Cmdlets "Preflight" **JetDR Deinstallieren**, geben Sie die erforderlichen Werte ein oder ändern Sie die standardmäßigen Werte, und wählen Sie **Ausführen**.

   | **Feld** | **Wert** |
   | --- | --- |
   | **ProtectedCluster** | Der Name des zu schützenden Azure VMware Solution privaten Cloudclusters, z. B. **Cluster-1**.  Sie können während der Installation nur einen Clusternamen angeben. |
   | **Credential**  |  Anmeldeinformationen des Stammbenutzers des JetStream MSA VM.   |
   | **MSIp** | IP-Adresse des JetStream MSA VM.   |
   | **Name für Ausführung angeben**  | Alphanumerischer Name der Ausführung, z. B.**Uninstall-JetDR-Exec1**.   Er wird verwendet, um zu überprüfen, ob das Cmdlet erfolgreich ausgeführt wurde.  |

 1. [Zeigen Sie den Status der Ausführung an](concepts-run-command.md#view-the-status-of-an-execution).


## <a name="region-availability"></a>Regionale Verfügbarkeit

JetStream DR for Azure VMware Solution ist in den folgenden Regionen verfügbar: 

- East US 

- Nordeuropa 

- USA (Süden) 

- Europa, Westen 

- USA (Mitte) 

- USA (Westen) 

- Asien, Osten 

- Japan, Osten 

- Brasilien Süd 

- Kanada, Osten 

- UK, Westen 


 
## <a name="support"></a>Support  
 
JetStream DR ist eine Lösung, die [JetStream Software](https://www.jetstreamsoft.com/) unterstützt. Wenden Sie sich bei Produkt- oder Supportproblemen mit JetStream an support-avs@jetstreamsoft.com.  
 
Azure VMware Solution verwendet den Befehl zum Ausführen (Vorschau), um die Installation und Deinstallation von JetStream DR zu automatisieren. Wenden Sie sich bei jedem Problem mit den Befehlen zur Ausführungs an den Microsoft-Support. Wenden Sie sich zum Support bei Problemen mit der Installation und Deinstallation von JetStream-Cmdlets an JetStream. 



## <a name="next-steps"></a>Nächste Schritte

- [Infrastruktureinrichtung: JetStream DR für Azure VMware Solution](https://vimeo.com/480574312/b5386a871c) 

- [JetStream DR für Azure VMware Solution (Vollständige Demo)](https://vimeo.com/475620858/2ce9413248)
   
   - [Erste Schritte mit JetStream DR für Azure VMware Solution](https://vimeo.com/491880696/ec509ff8e3)

   - [Konfigurieren und Schützen von VMs](https://vimeo.com/491881616/d887590fb2)

   - [Failover zu Azure VMware Solution](https://vimeo.com/491883564/ca9fc57092)

   - [Failback zu lokal](https://vimeo.com/491884402/65ee817b60)

