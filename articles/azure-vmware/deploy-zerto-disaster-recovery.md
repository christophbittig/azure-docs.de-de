---
title: Bereitstellen der Zerto-Notfallwiederherstellung in Azure VMware Solution (Anfangsverfügbarkeit)
description: Hier erfahren Sie, wie Sie Zerto-Notfallwiederherstellung für lokale virtuelle VMware- oder Azure VMware Solution-Computer implementieren.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 8f7fcd27cdde9915f2e0c9c2467576aa59a0a478
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016901"
---
# <a name="deploy-zerto-disaster-recovery-on-azure-vmware-solution-initial-availability"></a>Bereitstellen der Zerto-Notfallwiederherstellung in Azure VMware Solution (Anfangsverfügbarkeit)

In diesem Artikel wird erläutert, wie Sie die Notfallwiederherstellung für lokale virtuelle VMware-Computer oder Azure VMware Solution-basierte virtuelle Computer einsetzen. Die Lösung in diesem Artikel verwendet die [Zerto-Notfallwiederherstellung](https://www.zerto.com/solutions/use-cases/disaster-recovery/). Instanzen von Zerto werden sowohl am geschützten Standort als auch am Wiederherstellungsstandort bereitgestellt. 

Zerto ist eine Notfallwiederherstellungslösung, die die Downtime der virtuellen Computer bei einem Notfall minimiert. Die Zerto-Plattform ist aufgebaut auf Continuous Data Protection (CDP), die minimale oder fast keinen Datenverlust ermöglicht. Sie bietet die Schutzebene, die für viele unternehmenskritische und einsatzkritische Unternehmensanwendungen gewünscht ist. Zerto automatisiert und orchestriert auch Failover und Failback und sorgt so für eine minimale Downtime bei einem Notfall. Insgesamt vereinfacht Zerto die Verwaltung durch Automatisierung und stellt schnelle und hochgradig vorhersagbare Wiederherstellungszeiten sicher.


## <a name="core-components-of-the-zerto-platform"></a>Kernkomponenten der Zerto-Plattform

| Komponente | Beschreibung |
| --- | --- |
| **Zerto Virtual Manager (ZVM)**   | Verwaltungsanwendung für Zerto, die als Windows Service auf einem virtuellen Computer mit Windows eingesetzt ist. Der Administrator der privaten Cloud installiert und verwaltet die Windows VM. ZVM aktiviert die Konfiguration für die Notfallwiederherstellung von Tag 0 und Tag 2. Beispiel: Konfigurieren von primären Standorten und Standorten zur Notfallwiederherstellung, Schützen von VMs, Wiederherstellen von VMs und so weiter. Die Replikationsdaten der geschützten Kunden-VMs werden jedoch nicht verarbeitet.     |
| **Virtual Replication Appliance (vRA)**   | Linux-VM zum Verarbeiten der Datenreplikation von der Quelle zum Replikationsziel. Pro ESXi-Host wird eine Instanz von vRA installiert, die eine echte Skalierungsarchitektur bietet, die zusammen mit den Hosts der privaten Cloud wächst und sich verkleinert. Die VRA verwaltet die Datenreplikation zu und von geschützten VMs zu ihrem lokalen oder Remoteziel und speichert die Daten im Journal.    |
| **Zerto ESXi Hosttreiber**   | Wird auf jedem VMware ESXi-Host installiert, der für Zerto-Notfallwiederherstellung konfiguriert ist. Der Hosttreiber fängt die E/A eines virtuellen vSphere-Computers ab und sendet die Replikationsdaten an die ausgewählte vRA für diesen Host. Die vRA ist dann für die Replikation der Daten des virtuellen Computers auf einem oder mehreren Notfallwiederherstellungszielen verantwortlich.    |
| **Zerto Cloud Appliance (ZCA)**   | Windows VM wird nur verwendet, wenn Zerto zum Wiederherstellen von vSphere-VMs als native Azure-IaaS-VMs verwendet wird. Die ZCA besteht aus den folgenden Elementen:<ul><li>**ZVM:** Ein Windows-Dienst, der die Benutzeroberfläche hostet und zur Verwaltung und Orchestrierung in die nativen APIs von Azure integriert wird.</li><li>**VRA:** Ein Windows-Dienst, der die Daten von oder zu Azure repliziert.</li></ul>Die ZCA lässt sich nativ in die Plattform integrieren, auf der er bereitgestellt wird, sodass Sie Azure Blob Storage in einem Speicherkonto auf Microsoft Azure benutzen können. Dadurch wird die kostengünstigste Bereitstellung auf jeder dieser Plattformen sichergestellt.   |
| **Virtuelle Schutzgruppe (VPG)**   | Logische Gruppe von VMs, die auf der ZVM erstellt wurden. Zerto ermöglicht das Konfigurieren von Notfallwiederherstellungs-, Sicherungs- und Mobilitätsrichtlinien in einer VPG. Dieser Mechanismus ermöglicht die Anwendung konsistenter Richtlinien auf eine Gruppe von VMs.  |


Weitere Informationen zur Zerto-Plattformarchitektur finden Sie im [Zerto Plattform-Architekturleitfaden](https://www.zerto.com/wp-content/uploads/2021/07/Zerto-Platform-Architecture-Guide.pdf). 


## <a name="supported-zerto-scenarios"></a>Unterstützte Zerto-Szenarien

Sie können Zerto mit Azure VMware Solution mit den folgenden drei Notfallwiederherstellungsszenarien verwenden. 

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-disaster-recovery"></a>Szenario 1: Notfallwiederherstellung einer lokalen VMware-Instanz in Azure VMware Solution

In diesem Szenario ist der primäre Standort eine lokale vSphere-basierte Umgebung. Der Standort für die Notfallwiederherstellung ist eine private Azure VMware Solution-Cloud. 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-1.png" alt-text="Diagramm, das das Szenario 1 für die Zerto-Notfallwiederherstellungslösung on Azure VMware Solution darstellt.":::


### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-cloud-disaster-recovery"></a>Szenario 2: Notfallwiederherstellung von Azure VMware Solution in einer Azure VMware Solution-Cloud

In diesem Szenario ist der primäre Standort eine private Cloud einer Azure VMware Solution in einer Azure-Region. Der Standort für die Notfallwiederherstellung ist eine private Azure VMware Solution-Cloud in einer anderen Azure-Region.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2.png" alt-text="Diagramm: Szenario 2 für die Zerto-Notfallwiederherstellungslösung in Azure VMware Solution" border="false":::


### <a name="scenario-3-azure-vmware-solution-to-iaas-vms-cloud-disaster-recovery"></a>Szenario 3: Notfallwiederherstellung von Azure VMware Solution in einer IaaS-VMs Cloud

In diesem Szenario ist der primäre Standort eine private Cloud einer Azure VMware Solution in einer Azure-Region. Azure-Blobs und Azure-IaaS-VMs (Hyper-V-basiert) werden bei Notfällen verwendet.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-3.png" alt-text="Diagramm: Szenario 3 für die Zerto-Notfallwiederherstellungslösung in Azure VMware Solution" border="false":::



## <a name="prerequisites"></a>Voraussetzungen

### <a name="on-premises-vmware-to-azure-vmware-solution-disaster-recovery"></a>Notfallwiederherstellung einer lokalen VMware-Instanz in Azure VMware Solution

- Private Azure VMware Solution Cloud, die als sekundäre Region bereitgestellt wird.

- VPN oder ExpressRoute-Konnektivität zwischen lokalem System und VMware-Lösung in Azure.



### <a name="azure-vmware-solution-to-azure-vmware-solution-cloud-disaster-recovery"></a>Notfallwiederherstellung von Azure VMware Solution in einer Azure VMware Solution-Cloud

- Die private Azure VMware Solution Cloud muss in der primären und sekundären Region bereitgestellt werden.

   :::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2a-prerequisite.png" alt-text="Diagramm: Erste Voraussetzung für Szenario 2 der Zerto-Notfallwiederherstellungslösung in Azure VMware Solution":::
 
- Konnektivität, z. B. ExpressRoute-Global Reach, zwischen der privaten Quell- und Ziel-AVS-Cloud.

### <a name="azure-vmware-solution-iaas-vms-cloud-disaster-recovery"></a>Notfallwiederherstellung von Azure VMware Solution in einer IaaS-VMs Cloud

- Netzwerkkonnektivität auf Grundlage von ExpressRoute, von Azure VMware Solution VNET, das für die Notfallwiederherstellung verwendet wird.   

- Für die restlichen Voraussetzungen befolgen Sie die [Zerto Virtual Replication Azure Enterprise Guidelines](http://s3.amazonaws.com/zertodownload_docs/Latest/Zerto%20Virtual%20Replication%20Azure%20Enterprise%20Guidelines.pdf).



## <a name="install-zerto-on-azure-vmware-solution"></a>Installieren von Zerto auf Azure VMware Solution

Derzeit befindet sich die Zerto-Notfallwiederherstellung in Azure VMware Solution in der Phase der Anfangsverfügbarkeit (Initial Availability, IA). In der IA-Phase müssen Sie sich an Microsoft wenden, um IA-Support anzufordern und sich dafür zu qualifizieren.

Um IA-Support für Zerto auf Azure VMware Solution anzufordern, senden Sie eine E-Mail-Anforderung an zertoonavs@microsoft.com. In der IA-Phase unterstützt Azure VMware Solution nur die manuelle Installation und das Onboarding von Zerto. Microsoft arbeitet jedoch mit Ihnen zusammen, damit Sie Zerto manuell in Ihrer privaten Cloud installieren können.

> [!NOTE]
> Im Rahmen der manuellen Installation erstellt Microsoft ein neues vCenter-Benutzerkonto für Zerto. Dieses Benutzerkonto ist nur für Zerto Virtual Manager (ZVM) zum Ausführen von Vorgängen in Azure VMware Solution vCenter vorgesehen. Wählen Sie bei der Installation von ZVM in Azure VMware Solution nicht die Option „Select to enforce roles and permissions using Zerto vCenter privileges“ (Auswählen, um Rollen und Berechtigungen mithilfe von Zerto-vCenter-Berechtigungen zu erzwingen) aus. 


Wählen Sie nach der ZVM-Installation in Zerto Virtual unter **Site Settings** (Siteeinstellungen) die folgenden Optionen aus: 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-install-5.png" alt-text="Screenshot: Abschnitt „Workload Automation“ (Workloadautomatisierung), in dem die Auswahl aller Optionen (blaue Kontrollkästchen) gezeigt ist":::

>[!NOTE]
>Sobald Azure VMware Solution allgemein verfügbar wird, können Sie Zerto und die Tag-2-Vorgänge auch eigenständig auf Azure VMware Solution installieren.


## <a name="configure-zerto-for-disaster-recovery"></a>Konfigurieren von Zerto für die Notfallwiederherstellung

Informationen zum Konfigurieren von Zerto für die Szenarien für die Notfallwiederherstellung der lokalen VMware-Instanz in Azure VMware Solution und von Azure VMware Solution in der Azure VMware Solution-Cloud finden Sie im [Zerto Virtual Manager-Verwaltungshandbuch für die vSphere-Umgebung](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/Zerto%20Virtual%20Manager%20vSphere%20Administration%20Guide.pdf?cb=1629311409).


Weitere Informationen finden Sie in der [technischen Dokumentation zu Zerto](https://www.zerto.com/myzerto/technical-documentation/). Alternativ können Sie alle Zerto-Handbücher als Teil des [Dokumentationspakets des v8.5 Suchtools für Zerto Software PDFs](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/SEARCH_TOOL.zip?cb=1629311409) herunterladen.



## <a name="ongoing-management-of-zerto"></a>Fortlaufende Verwaltung von Zerto

- Wenn Sie Ihre privaten Cloudvorgänge in Azure VMware Solution skalieren, müssen Sie möglicherweise neue Azure VMware Solution-Hosts für den Zerto-Schutz hinzufügen oder Zerto-Notfallwiederherstellung in neuen Azure VMware Solution-vSphere-Clustern konfigurieren. In beiden Szenarien müssen Sie in der Phase „Anfangsverfügbarkeit“ eine Supportanfrage an das Azure VMware Solution-Team senden. Sie können die [Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support) für diese Konfigurationen für Tag 2 über das Azure-Portal öffnen. 

   :::image type="content" source="media/zerto-disaster-recovery/support-request-zerto-disaster-recovery.png" alt-text="Screenshot: Supportanfrage für die Zerto-Notfallwiederherstellungskonfigurationen für Tag 2":::

- In der GA-Phase werden alle oben genannten Vorgänge automatisiert und zur Selbstbedienung aktiviert.


## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="can-i-use-a-pre-existing-zerto-product-license-on-azure-vmware-solution"></a>Kann ich eine bereits vorhandene Zerto-Produktlizenz auf einer Azure VMware Solution verwenden?

Sie können bereits vorhandene Zerto-Produktlizenzen für Azure VMware Solution wiederverwenden. Wenn Sie neue Zerto-Lizenzen brauchen, schicken Sie eine E-Mail an Zerto unter **info@zerto.com** , um neue Lizenzen zu erwerben. 

### <a name="how-is-zerto-supported"></a>Wie wird Zerto unterstützt?

Die Zerto-Notfallwiederherstellung ist eine Lösung, die von Zerto verkauft und unterstützt wird. Bei allen Problemen und Anliegen im Zusammenhang mit der Zerto-Notfallwiederherstellung wenden Sie sich immer an den [Support von Zerto](https://www.zerto.com/company/support-and-service/support/).

Die Supportteams von Zerto und Microsoft treten bei Bedarf miteinander in Kontakt, um Probleme im Zusammenhang mit der Zerto-Notfallwiederherstellung in Azure VMware Solution zu beheben.

