---
title: Architektur der Azure Migrate-Appliance
description: Bietet einen Überblick über die Azure Migrate-Appliance, die bei der Serverermittlung, -bewertung und -migration verwendet wird.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 78b62c91148fc7a2c202cbf6792c1de442c142cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355605"
---
# <a name="azure-migrate-appliance-architecture"></a>Architektur der Azure Migrate-Appliance

In diesem Artikel werden die Architektur und Prozesse der Azure Migrate-Appliance beschrieben. Die Azure Migrate-Appliance ist eine einfache Appliance, die lokal bereitgestellt wird, um Server für die Migration zu Azure zu ermitteln.

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

Die Azure Migrate-Appliance wird in den folgenden Szenarien verwendet.

**Szenario** | **Tool** | **Verwendung für**
--- | --- | ---
**Ermittlung und Bewertung von Servern, die in einer VMware-Umgebung ausgeführt werden** | Azure Migrate: Ermittlung und Bewertung | Ermitteln von Servern, die in Ihrer VMware-Umgebung ausgeführt werden<br/><br/> Ermitteln Sie das installierte Softwareinventar, ASP.NET-Webanwendungen, SQL Server-Instanzen und -Datenbanken und Abhängigkeitsanalyse ohne Agent.<br/><br/> Sammeln Sie Serverkonfigurations- und Leistungsmetadaten für Bewertungen.
**Migration ohne Agent von Servern, die in VMware-Umgebungen ausgeführt werden** | Azure Migrate-Servermigration | Ermitteln Sie Server, die in Ihrer VMware-Umgebung ausgeführt werden.<br/><br/> Replizieren Sie Server ohne Installation von Agents.
**Ermittlung und Bewertung von Servern, die in einer Hyper-V-Umgebung ausgeführt werden** | Azure Migrate: Ermittlung und Bewertung | Ermitteln Sie Server, die in Ihrer Hyper-V-Umgebung ausgeführt werden.<br/><br/> Sammeln Sie Serverkonfigurations- und Leistungsmetadaten für Bewertungen.
**Ermittlung und Bewertung lokaler physischer oder virtualisierter Server** |  Azure Migrate: Ermittlung und Bewertung |  Erkennen Sie lokale physische oder virtualisierte Server.<br/><br/> Sammeln Sie Serverkonfigurations- und Leistungsmetadaten für Bewertungen.

## <a name="deployment-methods"></a>Bereitstellungsmethoden

Die Appliance kann mithilfe verschiedener Methoden bereitgestellt werden:

- Die Appliance kann mithilfe einer Vorlage für Server bereitgestellt werden, die in einer VMware- oder Hyper-V-Umgebung ausgeführt werden ([OVA-Vorlage für VMware](how-to-set-up-appliance-vmware.md) oder [VHD für Hyper-V](how-to-set-up-appliance-hyper-v.md)).
- Wenn Sie keine Vorlage verwenden möchten, können Sie die Appliance für eine VMware- oder Hyper-V-Umgebung mithilfe eines [PowerShell-Installationsskripts](deploy-appliance-script.md) bereitstellen.
- In Azure Government sollten Sie die Appliance mithilfe eines PowerShell-Installationsskripts bereitstellen. Die Schritte zur Bereitstellung finden Sie [hier](deploy-appliance-script-government.md).
- Für physische oder virtualisierte Server in der lokalen Umgebung oder in einer beliebigen anderen Cloud stellen Sie die Appliance immer mithilfe eines PowerShell-Installationsskripts bereit. Die Schritte zur Bereitstellung finden Sie [hier](how-to-set-up-appliance-physical.md).
- Die entsprechenden Downloadlinks finden Sie in den nachstehenden Tabellen.

## <a name="appliance-services"></a>Appliancedienste

Die Appliance verfügt über die folgenden Dienste:

- **Appliance-Konfigurations-Manager**: Dies ist eine Webanwendung, die mit Quelldetails konfiguriert werden kann, um die Ermittlung und Bewertung von Servern zu starten.
- **Ermittlungsagent**: Der Agent sammelt Serverkonfigurationsmetadaten, die zum Erstellen von lokalen Bewertungen verwendet werden können.
- **Bewertungsagent**: Der Agent sammelt Serverleistungsmetadaten, die zum Erstellen von leistungsbasierten Bewertungen verwendet werden können.
- **Dienst für automatische Aktualisierungen**: Der Dienst hält alle Agents, die auf der Appliance ausgeführt werden, auf dem neuesten Stand. Er wird automatisch alle 24 Stunden ausgeführt.
- **DRA-Agent**: Orchestriert die Serverreplikation und koordiniert die Kommunikation zwischen replizierten Servern und Azure. Wird nur bei der Replikation von Servern in Azure mithilfe der Migration ohne Agent verwendet.
- **Gateway**: Sendet replizierte Daten an Azure. Wird nur bei der Replikation von Servern in Azure mithilfe der Migration ohne Agent verwendet.
- **SQL Discovery and Assessment Agent**: Sendet die Konfigurations- und Leistungsmetadaten von SQL Server-Instanzen und -Datenbanken an Azure.
- **Ermittlungs- und Bewertungs-Agent für Web-Apps**: Sendet die Konfigurationsdaten der Web-Apps an Azure.

> [!Note]
> Die letzten vier Dienste sind nur auf der Appliance verfügbar, die für die Ermittlung und Bewertung von in Ihrer VMware-Umgebung ausgeführten Servern verwendet wird.

## <a name="discovery-and-collection-process"></a>Ermittlungs- und Sammlungsprozess

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Appliance-Architektur":::

Die Appliance kommuniziert über den folgenden Prozess mit den Ermittlungsquellen.

**Prozess** | **VMware-Appliance** | **Hyper-V-Appliance** | **Physisches Gerät**
---|---|---|---
**Ermittlung starten** | Die Appliance kommuniziert standardmäßig über TCP-Port 443 mit dem vCenter-Server. Wenn der vCenter-Server an einem anderen Port lauscht, können Sie ihn im Konfigurations-Manager der Appliance konfigurieren. | Die Appliance kommuniziert über WinRM-Port 5985 (HTTP) mit den Hyper-V-Hosts. | Die Appliance kommuniziert über WinRM-Port 5985 (HTTP) mit Windows-Servern und über Port 22 (TCP) mit Linux-Servern.
**Konfigurations- und Leistungsmetadaten sammeln** | Die Appliance sammelt die Metadaten von Servern, die über vSphere-APIs auf vCenter Server ausgeführt werden, indem sie über Port 443 (Standardport) oder einen anderen Port, an dem vCenter Server lauscht, eine Verbindung herstellt. | Die Appliance sammelt die Metadaten von Servern, die über eine CIM-Sitzung (Common Information Model) auf Hyper-V-Hosts ausgeführt werden, indem sie über Port 5985 eine Verbindung mit den Hosts herstellt.| Die Appliance sammelt Metadaten von Windows-Servern, die eine CIM-Sitzung (Common Information Model) und Port 5985 verwenden, und von Linux-Servern, die über SSH und Port 22 eine Verbindung herstellen.
**Ermittlungsdaten senden** | Die Appliance sendet die gesammelten Daten über SSL-Port 443 an die Azure Migrate-Ermittlung und -Bewertung sowie an die Azure Migrate-Servermigration.<br/><br/>  Die Verbindung der Appliance mit Azure kann über das Internet oder über Leitungen für das private ExpressRoute-Peering oder Microsoft-Peering erfolgen. | Die Appliance sendet die gesammelten Daten über SSL-Port 443 an die Azure Migrate-Ermittlung und -Bewertung.<br/><br/> Die Verbindung der Appliance mit Azure kann über das Internet oder über Leitungen für das private ExpressRoute-Peering oder Microsoft-Peering erfolgen. | Die Appliance sendet die gesammelten Daten über SSL-Port 443 an die Azure Migrate-Ermittlung und -Bewertung.<br/><br/> Die Verbindung der Appliance mit Azure kann über das Internet oder über Leitungen für das private ExpressRoute-Peering oder Microsoft-Peering erfolgen. 
**Häufigkeit der Datensammlung** | Konfigurationsmetadaten werden alle 30 Minuten erfasst und gesendet. <br/><br/> Leistungsmetadaten werden alle 20 Sekunden erfasst und so aggregiert, dass alle 10 Minuten ein Datenpunkt an Azure gesendet wird. <br/><br/> Softwarebestandsdaten werden alle 12 Stunden an Azure gesendet. <br/><br/> Abhängigkeitsdaten ohne Agent werden alle 5 Minuten gesammelt, auf der Appliance aggregiert und alle 6 Stunden an Azure gesendet. <br/><br/> Die SQL Server-Konfigurationsdaten werden alle 24 Stunden aktualisiert, und die Leistungsdaten werden alle 30 Sekunden aufgezeichnet. <br/><br/> Die Konfigurationsdaten von Web-Apps werden alle 24 Stunden aktualisiert. Leistungsdaten werden für Web-Apps nicht erfasst.| Konfigurationsmetadaten werden alle 30 Minuten erfasst und gesendet. <br/><br/> Leistungsmetadaten werden alle 30 Sekunden erfasst und so aggregiert, dass alle 10 Minuten ein Datenpunkt an Azure gesendet wird.|  Konfigurationsmetadaten werden alle 30 Minuten erfasst und gesendet. <br/><br/> Leistungsmetadaten werden alle 5 Minuten erfasst und so aggregiert, dass alle 10 Minuten ein Datenpunkt an Azure gesendet wird.
**Bewerten und Migrieren** | Sie können mithilfe des Azure Migrate-Tools zur Ermittlung und Bewertung aus den von der Appliance gesammelten Metadaten Bewertungen erstellen.<br/><br/>Darüber hinaus können Sie mithilfe der Azure Migrate-Serverbewertung jetzt auch mit der Migration von Servern beginnen, die in Ihrer VMware-Umgebung ausgeführt werden, um die Serverreplikation ohne Agent zu orchestrieren.| Sie können mithilfe des Azure Migrate-Tools zur Ermittlung und Bewertung aus den von der Appliance gesammelten Metadaten Bewertungen erstellen. | Sie können mithilfe des Azure Migrate-Tools zur Ermittlung und Bewertung aus den von der Appliance gesammelten Metadaten Bewertungen erstellen.

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen](migrate-appliance.md) Sie die Applianceunterstützungsmatrix.