---
title: Bereitstellen der Zerto-Notfallwiederherstellung auf Azure VMware Solution (Anfangsverfügbarkeit)
description: Erfahren Sie, wie Sie Zerto Disaster Recovery für lokale VMware oder VMs mit Azure VMware Solution einsetzen.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 9e919f8fa22965bdd7a170db2c19bd921de030cd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475293"
---
# <a name="deploy-zerto-disaster-recovery-on-azure-vmware-solution-initial-availability"></a>Bereitstellen der Zerto-Notfallwiederherstellung auf Azure VMware Solution (Anfangsverfügbarkeit)

In diesem Artikel wird erläutert, wie Sie die Notfallwiederherstellung (DR) für lokale virtuelle VMware-Computer (VMs) oder Azure VMware Solution-basierte VMs einsetzen. Die Lösung in diesem Artikel verwendet [Zerto Disaster Recovery](https://www.zerto.com/solutions/use-cases/disaster-recovery/). Instanzen von Zerto werden sowohl am geschützten Standort als auch am Wiederherstellungsstandort bereitgestellt. 

Zerto wurde entwickelt, um Ausfallzeiten der VMs bei einem Notfall zu minimieren. Die Zerto-Plattform ist aufgebaut auf Continuous Data Protection (CDP), die minimale oder fast keinen Datenverlust ermöglicht. Sie bietet die Schutzebene, die für viele unternehmenskritische und einsatzkritische Unternehmensanwendungen gewünscht ist. Zerto automatisiert und orchestriert auch Failover und Failback und sorgt so für eine minimale Downtime bei einem Notfall. Insgesamt vereinfacht Zerto die Verwaltung durch Automatisierung und stellt schnelle und hochgradig vorhersagbare Wiederherstellungszeiten sicher.


## <a name="core-components-of-the-zerto-platform"></a>Kernkomponenten der Zerto-Plattform

| Komponente | Beschreibung |
| --- | --- |
| **Zerto Virtual Manager (ZVM)**   | Verwaltungsanwendung für Zerto, die als Windows Service auf einem virtuellen Computer mit Windows eingesetzt ist. Der Administrator der privaten Cloud installiert und verwaltet die Windows VM. Die ZVM aktiviert die Konfiguration für die Notfallwiederherstellung von Tag 0 und Tag 2. Beispiel: Konfigurieren von primären Standorten und Standorten zur Notfallwiederherstellung, Schützen von VMs, Wiederherstellen von VMs und so weiter. Die Replikationsdaten der geschützten Kunden-VMs werden jedoch nicht verarbeitet.     |
| **Virtual Replication Appliance (vRA)**   | Linux-VM zum Verarbeiten der Datenreplikation von der Quelle zum Replikationsziel. Pro ESXi-Host wird eine Instanz von vRA installiert, die eine echte Skalierungsarchitektur bietet, die zusammen mit den Hosts der privaten Cloud wächst und sich verkleinert. Die VRA verwaltet die Datenreplikation zu und von geschützten VMs zu ihrem lokalen oder Remoteziel und speichert die Daten im Journal.    |
| **Zerto ESXi Hosttreiber**   | Wird auf jedem VMware ESXi Host installiert, der für Zerto DR konfiguriert ist. Der Hosttreiber fängt die E/A eines virtuellen vSphere-Computers ab und sendet die Replikationsdaten an die ausgewählte vRA für diesen Host. Die vRA ist dann für die Replikation der Daten des virtuellen Computers auf ein oder mehrere DR-Ziele verantwortlich.    |
| **Zerto Cloud Appliance (ZCA)**   | Windows VM wird nur verwendet, wenn Zerto zum Wiederherstellen von vSphere-VMs als native Azure-IaaS-VMs verwendet wird. Die ZCA besteht aus den folgenden Elementen:<ul><li>**ZVM:** Ein Windows-Dienst, der die Benutzeroberfläche hostet und zur Verwaltung und Orchestrierung in die nativen APIs von Azure integriert wird.</li><li>**VRA:** Ein Windows-Dienst, der die Daten von oder zu Azure repliziert.</li></ul>Die ZCA lässt sich nativ in die Plattform integrieren, auf der er bereitgestellt wird, sodass Sie Azure Blob Storage in einem Speicherkonto auf Microsoft Azure benutzen können. Dadurch wird die kostengünstigste Bereitstellung auf jeder dieser Plattformen sichergestellt.   |
| **Virtuelle Schutzgruppe (VPG)**   | Logische Gruppe von VMs, die auf der ZVM erstellt wurden. Zerto ermöglicht das Konfigurieren von DR-, Sicherungs- und Mobilitätsrichtlinien in einer VPG. Dieser Mechanismus ermöglicht die Anwendung konsistenter Richtlinien auf eine Gruppe von VMs.  |


Weitere Informationen zur Zerto-Plattformarchitektur finden Sie im [Zerto Plattform-Architekturleitfaden](https://www.zerto.com/wp-content/uploads/2021/07/Zerto-Platform-Architecture-Guide.pdf). 


## <a name="supported-zerto-scenarios"></a>Unterstützte Zerto-Szenarien

Sie können Zerto mit Azure VMware Solution mit den folgenden drei Notfallwiederherstellungsszenarien verwenden. 

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>Szenario 1: Lokale VMware zur Azure VMware Solution Notfallwiederherstellung

In diesem Szenario ist der primäre Standort eine lokale vSphere-basierte Umgebung. Der Standort für die Notfallwiederherstellung ist eine private Cloud einer Azure VMware Solution. 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-1.png" alt-text="Diagramm, das das Szenario 1 für die Zerto-Notfallwiederherstellungslösung on Azure VMware Solution darstellt." border="false":::



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-cloud-dr"></a>Szenario 2:Azure VMware Solution zur Azure VMware Solution Cloud-Notfallwiederherstellung

In diesem Szenario ist der primäre Standort eine private Cloud einer Azure VMware Solution in einer Azure-Region. Während der Standort für die Notfallwiederherstellung eine private Cloud einer Azure VMware Solution in einer anderen Azure-Region ist.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2.png" alt-text="Diagramm, das das Szenario 2 für die Zerto-Notfallwiederherstellungslösung on Azure VMware Solution darstellt." border="false":::


### <a name="scenario-3-azure-vmware-solution-to-iaas-vms-cloud-dr"></a>Szenario 3: AZure VMware Solution zur IaaS-VMs Cloud-Notfallwiederherstellung

In diesem Szenario ist der primäre Standort eine private Cloud einer Azure VMware Solution in einer Azure-Region. Und Azure-Blobs und Azure-IaaS-VMs (Hyper-V-basiert) werden in Notzeiten verwendet.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-3.png" alt-text="Diagramm zeigt das Szenario 3 für die Zerto-Lösung zur Notfallwiederherstellung auf Azure VMware Solution." border="false":::



## <a name="prerequisites"></a>Voraussetzungen

### <a name="on-premises-vmware-to-azure-vmware-solution-dr"></a>Lokale VMware zur Azure VMware Solution Notfallwiederherstellung

- Private Azure VMware Solution Cloud, die als sekundäre Region bereitgestellt wird.

- VPN oder ExpressRoute-Konnektivität zwischen lokalem System und VMware-Lösung in Azure.



### <a name="azure-vmware-solution-to-azure-vmware-solution-cloud-dr"></a>Azure VMware Solution zur Azure VMware Solution Cloud-Notfallwiederherstellung

- Die private Azure VMware Solution Cloud muss in der primären und sekundären Region bereitgestellt werden.

   :::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2a-prerequisite.png" alt-text="Diagramm zeigt die erste Voraussetzung für Szenario 2 der Zerto-Lösung zur Notfallwiederherstellung auf Azure VMware Solution.":::
 
- Konnektivität, z. B. ExpressRoute-Global Reach, zwischen der privaten Quell- und Ziel-AVS-Cloud.



### <a name="azure-vmware-solution-iaas-vms-cloud-dr"></a>Azure VMware Solution IaaS-VMs Cloud-Notfallwiederherstellung

- Netzwerkkonnektivität auf Grundlage von ExpressRoute, von Azure VMware Solution VNET, das für die Notfallwiederherstellung verwendet wird.   

- Für die restlichen Voraussetzungen befolgen Sie die [Zerto Virtual Replication Azure Enterprise Guidelines](http://s3.amazonaws.com/zertodownload_docs/Latest/Zerto%20Virtual%20Replication%20Azure%20Enterprise%20Guidelines.pdf).



## <a name="install-zerto-on-azure-vmware-solution"></a>Installieren von Zerto auf Azure VMware Solution

Derzeit befindet sich Zerto DR auf Azure VMware Solution in der Phase der anfänglich verfügbaren Bereitstellung (Initial Availability, IA). In der IA-Phase müssen Sie sich an Microsoft wenden, um IA-Support anzufordern und sich dafür zu qualifizieren.

Um IA-Support für Zerto auf Azure VMware Solution anzufordern, senden Sie eine E-Mail-Anforderung an zertoonavs@microsoft.com. In der IA-Phase unterstützt Azure VMware Solution nur die manuelle Installation und das Onboarding von Zerto. Microsoft arbeitet jedoch mit Ihnen zusammen, damit Sie Zerto manuell in Ihrer privaten Cloud installieren können.

>[!NOTE]
>Sobald Azure VMware Solution allgemein verfügbar wird, können Sie Zerto und die Tag-2-Vorgänge auch eigenständig auf Azure VMware Solution installieren.


## <a name="configure-zerto-for-disaster-recovery"></a>Konfigurieren von Zerto für die Notfallwiederherstellung

Informationen zum Konfigurieren von Zerto für die lokale VMware-Azure VMware Solution und Azure VMware Solution in Azure VMware Solution Cloud-DR-Szenarien finden Sie im[Zerto Virtual Manager Verwaltungshandbuch für vSphere Environment](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/Zerto%20Virtual%20Manager%20vSphere%20Administration%20Guide.pdf?cb=1629311409).


Weitere Informationen finden Sie in der [technischen Dokumentation zu Zerto](https://www.zerto.com/myzerto/technical-documentation/). Alternativ können Sie alle Zerto-Handbücher als Teil des [Dokumentationspakets des v8.5 Suchtools für Zerto Software PDFs](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/SEARCH_TOOL.zip?cb=1629311409) herunterladen.



## <a name="ongoing-management-of-zerto"></a>Fortlaufende Verwaltung von Zerto

- Wenn Sie Ihre privaten Cloudvorgänge in Azure VMware Solution skalieren, müssen Sie möglicherweise neue Azure VMware Solution-Hosts für den Zerto-Schutz hinzufügen oder Zerto DR zu neuen vSphere Azure VMware Solution-Clustern konfigurieren. In beiden Szenarien müssen Sie, damit Sie Zerto vRAs Ihren neuen Hosts hinzufügen können, in der Phase „Erstverfügbarkeit“ eine Supportanfrage beim Azure VMware Solution-Team öffnen. Sie können die [Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support) für diese Day 2-Konfigurationen über das Azure-Portal öffnen.   

   :::image type="content" source="media/zerto-disaster-recovery/support-request-zerto-disaster-recovery.png" alt-text="Screenshot, der die Supportanfragen für die Zerto-DR- Konfigurationen von Tag 2 zeigt.":::

- Aufgrund der Art der Zerto-vSphere-Integration (mit harter vSphere-DRS-Affinität) wird erwartet, dass Vorgänge zum Herunterskalieren von Clustern fehlschlagen. Azure VMware Solution SRE-Teams werden diese Fehler in der IA-Phase offline behandeln.

- In der GA-Phase werden alle oben genannten Vorgänge automatisiert und zur Selbstbedienung aktiviert.


## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="can-i-use-a-pre-existing-zerto-product-license-on-azure-vmware-solution"></a>Kann ich eine bereits vorhandene Zerto-Produktlizenz auf einer Azure VMware Solution verwenden?

Sie können bereits vorhandene Zerto-Produktlizenzen für Azure VMware Solution wiederverwenden. Wenn Sie neue Zerto-Lizenzen brauchen, schicken Sie eine E-Mail an Zerto unter **sales@zerto.com** , um neue Lizenzen zu erwerben. 

### <a name="how-is-zerto-supported"></a>Wie wird Zerto unterstützt?

Zerto Disaster Recovery (Zerto Notfallwiederherstellung) ist eine Lösung, die von Zerto verkauft und unterstützt wird. Bei allen Problemen und Anliegen zu Support wenden Sie sich immer an[Zerto Support](https://www.zerto.com/company/support-and-service/support/).

Die Supportteams von Zerto und Microsoft treten bei Bedarf miteinander in Kontakt, um Probleme betreffend Zerto auf Azure VMware Solution zu beheben.

