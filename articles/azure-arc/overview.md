---
title: Übersicht über Azure Arc
description: Hier erfahren Sie, was Azure Arc ist und wie es Kunden bei der Verwaltung und Governance ihrer Hybridressourcen mit anderen Azure-Diensten und -Features unterstützt.
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: 62395b53fea3f86e42726f1bb2d35b12d8a0bdca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345736"
---
# <a name="azure-arc-overview"></a>Übersicht über Azure Arc

Heutzutage ist es schwierig, immer komplexere Umgebungen zu kontrollieren und zu steuern. Diese Umgebungen umfassen Rechenzentren, mehreren Clouds und den Edgebereich. Jede Umgebung und Cloud besitzt ihre eigenen separaten Verwaltungstools, mit denen Sie sich vertraut machen müssen, um sie verwenden zu können.

Hinzu kommt, dass sich neue DevOps- und ITOps-Betriebsmodelle nicht so ohne Weiteres implementieren lassen, da neue cloudnative Muster von den bereits vorhandenen Tools nicht unterstützt werden.

Azure Arc bietet eine konsistente Verwaltungsplattform für mehrere Clouds und die lokale Umgebung und vereinfacht so die Governance und Verwaltung. Mit Azure Arc können Sie folgende Aktionen durchführen:
* Sie können Ihre gesamte Umgebung über eine zentralisierte Benutzeroberfläche verwalten, indem Sie Ihre vorhandenen Ressourcen (Ressourcen, die nicht aus Azure stammen, lokale Ressourcen oder jene aus anderen Clouds) in Azure Resource Manager projizieren. 
* Sie verwalten virtuelle Computer, Kubernetes-Cluster und Datenbanken so, als würden sie in Azure ausgeführt. 
* Sie können ortsunabhängig vertraute Azure-Dienste und Verwaltungsfunktionen verwenden. 
* Sie können herkömmliche ITOps-Methoden weiterverwenden und gleichzeitig DevOps-Methoden einführen, um neue cloudnative Muster in Ihrer Umgebung zu unterstützen.
* Konfigurieren Sie benutzerdefinierte Speicherorte als Abstraktionsschicht auf der Grundlage von Kubernetes-Clustern mit Azure Arc-Unterstützung, Cluster Connect und Clustererweiterungen.  

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagramm: Steuerungsebene der Azure Arc-Verwaltung" border="false":::

Aktuell können mit Azure Arc folgende außerhalb von Azure gehostete Ressourcentypen verwaltet werden:

* Server: Sowohl physische als auch virtuelle Computer unter Windows oder Linux
* Kubernetes-Cluster: Unterstützung mehrerer Kubernetes-Distributionen
* Azure-Datendienste: Azure SQL Managed Instance und PostgreSQL Hyperscale
* SQL Server: Registrieren Sie Instanzen mit [SQL Server auf Azure Arc-fähigen Servern](/sql/sql-server/azure-arc/overview) von einem beliebigen Standort aus.

## <a name="what-does-azure-arc-deliver"></a>Was bietet Azure Arc?

Zu den wichtigsten Features von Azure Arc zählen folgende:

* Implementieren einer konsistenten Bestands-, Verwaltungs-, Governance- und Sicherheitslösung für die Server in Ihrer gesamten Umgebung

* Konfigurieren von [Azure-VM-Erweiterungen](./servers/manage-vm-extensions.md) für die Verwendung von Azure-Verwaltungsdiensten zur Überwachung, zum Schutz und zur Aktualisierung Ihrer Server

* Verwalten und Steuern von Kubernetes-Clustern im großen Stil

* Verwenden von GitOps zur Bereitstellung von Konfigurationen in einem oder mehreren Clustern aus Git-Repositorys.

*  Zero Touch-Compliance und -Konfiguration für Ihre Kubernetes-Cluster mit Azure Policy.

* Die Ausführung von [Azure Data Services](../azure-arc/kubernetes/custom-locations.md) in einer beliebigen Kubernetes-Umgebung ist wie die Ausführung in Azure möglich (insbesondere Azure SQL Managed Instance und Azure Database for PostgreSQL Hyperscale mit Vorteilen wie Upgrades/Updates, Sicherheit und Überwachung). Nutzen Sie die elastische Skalierung, und wenden Sie Updates ganz ohne Anwendungsdowntime an, sogar ohne kontinuierliche Verbindung mit Azure.

* Sie können [benutzerdefinierter Standorte](./kubernetes/custom-locations.md) auf Grundlage Ihrer [Azure Arc-fähigen Kubernetes-Cluster](./kubernetes/overview.md) erstellen, wobei diese als Zielstandorte für die Bereitstellung von Azure-Dienstinstanzen verwendet werden. Sie können Ihre Azure-Dienstclustererweiterungen für [Azure Arc-fähige Datendienste](./data/create-data-controller-direct-azure-portal.md), [App Services in Azure Arc](../app-service/overview-arc-integration.md) (einschließlich Web-, Funktions- und Logik-Apps) und für [Event Grid in Kubernetes](../event-grid/kubernetes/overview.md) bereitstellen.

* Sie profitieren von einer einheitlichen Anzeige Ihrer Azure Arc-fähigen Ressourcen bei Verwendung von Azure-Portal, Azure CLI, Azure PowerShell oder Azure-REST-API.

## <a name="how-much-does-azure-arc-cost"></a>Wie viel kostet Azure Arc?

Im Anschluss finden Sie Preisinformationen für die Features, die aktuell mit Azure Arc verfügbar sind:

### <a name="azure-arc-enabled-servers"></a>Server mit Azure Arc-Unterstützung

Die folgende Funktionalität der Azure Arc-Steuerungsebene wird ohne Zusatzkosten angeboten:

* Ressourcenorganisation über Azure-Verwaltungsgruppen und -Tags.

* Suche und Indizierung über Azure Resource Graph.

* Zugriff und Sicherheit durch Azure RBAC und Abonnements.

* Umgebungen und Automatisierung über Vorlagen und Erweiterungen.

* Updateverwaltung

Alle auf Azure Arc-fähigen Servern verwendeten Azure-Dienste, z. B. Microsoft Defender für Cloud oder Azure Monitor, werden zu den Preisen für den jeweiligen Dienst abgerechnet. Weitere Informationen finden Sie auf der Seite [Azure-Preise](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-fähiges Kubernetes

Alle in Kubernetes mit Azure Arc-Unterstützung verwendeten Azure-Dienste, z. B. Microsoft Defender für Cloud oder Azure Monitor, werden zu den Preisen für den jeweiligen Dienst abgerechnet. Weitere Informationen zu Preisen für Konfigurationen auf der Grundlage von Azure Arc-fähigem Kubernetes finden Sie auf der Seite [Azure-Preise](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-data-services"></a>Azure Arc-fähige Datendienste

Weitere Informationen finden Sie auf der Seite [Azure-Preise](https://azure.microsoft.com/pricing/).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure Arc-fähigen Servern finden Sie in [dieser Übersicht](./servers/overview.md).

* Weitere Informationen zu Azure Arc-fähigem Kubernetes finden Sie in [dieser Übersicht](./kubernetes/overview.md).

* Weitere Informationen zu Azure Arc-fähigen Datendiensten finden Sie in [dieser Übersicht](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/).

* Weitere Informationen zu SQL Server auf Azure Arc-fähigen Servern finden Sie in [dieser Übersicht](/sql/sql-server/azure-arc/overview).

* Informieren Sie sich über Azure Arc-fähige Dienste unter [Schnelleinstieg; Proof of Concept](https://azurearcjumpstart.io/azure_arc_jumpstart/).
