---
title: Übersicht über Kubernetes mit Azure Arc-Aktivierung
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: overview
description: Dieser Artikel enthält eine Übersicht zu Kubernetes mit Azure Arc-Aktivierung.
keywords: Kubernetes, Arc, Azure, Container
ms.openlocfilehash: 7338664698e40a1e4a1280cc08ee57a922518d79
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312836"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Was ist Kubernetes mit Azure Arc-Unterstützung?

Mithilfe von Kubernetes mit Azure Arc-Unterstützung können Sie Kubernetes-Cluster, die sich entweder innerhalb oder außerhalb von Azure befinden, anfügen und konfigurieren. Wenn Sie eine Verbindung von einem Kubernetes-Cluster mit Azure Arc herstellen, tritt Folgendes für den Cluster auf:
* Er wird im Azure-Portal mit einer Azure Resource Manager-ID und einer verwalteten Identität angezeigt. 
* Er wird in einem Azure-Abonnement und einer Ressourcengruppe platziert.
* Er empfängt Tags genau wie jede andere Azure-Ressource. 

Zum Herstellen einer Verbindung zwischen einem Kubernetes-Cluster und Azure muss ein Clusteradministrator entsprechende Agents bereitstellen. Folgendes gilt für diese Agents:
* Sie werden im Kubernetes-Namespace `azure-arc` als Kubernetes-Standardbereitstellungen ausgeführt.
* Sie verwalten die Konnektivität mit Azure.
* Sie sammeln Azure Arc-Protokolle und -Metriken.
* Sie achten auf Konfigurationsanforderungen. 

Kubernetes mit Azure Arc-Aktivierung unterstützt branchenübliches SSL zum Absichern von Daten während der Übertragung. Außerdem werden ruhende Daten verschlüsselt und in einer Azure Cosmos DB-Datenbank gespeichert, um ihre Vertraulichkeit zu gewährleisten.

## <a name="supported-kubernetes-distributions"></a>Unterstützte Kubernetes-Distributionen

Kubernetes mit Azure Arc-Unterstützung funktioniert mit jedem Kubernetes-Cluster, der eine CNCF-Zertifizierung (Cloud Native Computing Foundation) besitzt. Das Azure Arc-Team hat mit [wichtigen Partnern dieser Branche](./validation-program.md) zusammengearbeitet, um die Konformität ihrer Kubernetes-Distributionen mit Kubernetes mit Azure Arc-Unterstützung zu überprüfen.

## <a name="supported-scenarios"></a>Unterstützte Szenarios 

Kubernetes mit Azure Arc-Unterstützung unterstützt die folgenden Szenarien: 

* Verbinden von außerhalb von Azure ausgeführtem Kubernetes für Bestand, Gruppierung und Kennzeichnung

* Bereitstellen von Anwendungen und Anwenden der Konfiguration mithilfe der GitOps-basierten Konfigurationsverwaltung 

* Anzeigen und Überwachen von Clustern mithilfe von Azure Monitor für Container

* Erzwingen von Bedrohungsschutz mit Microsoft Defender für Kubernetes

* Anwenden von Richtliniendefinitionen mithilfe von Azure Policy für Kubernetes

* Erstellen von [benutzerdefinierten Standorten](./custom-locations.md) als Zielstandorte für die Bereitstellung von Datendiensten mit Azure Arc-Unterstützung, [App Services in Azure Arc](../../app-service/overview-arc-integration.md) (einschließlich Web-, Funktions- und Logik-Apps) und [Event Grid in Kubernetes](../../event-grid/kubernetes/overview.md)

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie einen Cluster mit Azure Arc verbinden.
> [!div class="nextstepaction"]
> [Verbinden eines Clusters mit Azure Arc](./quickstart-connect-cluster.md)
