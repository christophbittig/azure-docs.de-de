---
title: Was sind Datendienste mit Azure Arc-Unterstützung?
description: Bietet eine Einführung in Azure Arc-fähige Datendienste
ms.custom: references_regions
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 642448cab1fa6511d9d12eb4a3de894f3e5c1251
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056756"
---
# <a name="what-are-azure-arc-enabled-data-services"></a>Was sind Datendienste mit Azure Arc-Unterstützung?

Azure Arc ermöglicht das lokale Ausführen von Azure Data Services in Edgeumgebungen und in öffentlichen Clouds mithilfe von Kubernetes und der Infrastruktur Ihrer Wahl.

Derzeit sind die folgenden Azure Arc-fähigen Datendienste verfügbar: 

- Verwaltete SQL-Instanz
- PostgreSQL Hyperscale (Vorschau)

Eine Einführung dazu, wie Azure Arc-fähige Datendienste Ihre Hybridarbeitsumgebung unterstützen, finden Sie in diesem Einführungsvideo:

> [!VIDEO https://channel9.msdn.com/Shows//Inside-Azure-for-IT/Choose-the-right-data-solution-for-your-hybrid-environment/player?format=ny]

## <a name="always-current"></a>Immer aktuell

Azure Arc-fähige Datendienste wie Azure Arc-fähige SQL Managed Instance-Instanzen und PostgreSQL Hyperscale mit Azure Arc-Aktivierung umfassen regelmäßige Updates, einschließlich der Wartung von Patches und neuen Features, ähnlich wie in Azure. Es werden Ihnen Updates von Microsoft Container Registry bereitgestellt. Die Bereitstellungsintervalle werden von Ihnen gemäß Ihren Richtlinien festgelegt. Auf diese Weise bleiben die lokalen Datenbanken auf dem neuesten Stand, während Sie gleichzeitig die Kontrolle behalten. Da es sich bei Azure Arc-fähigen Datendiensten um einen Abonnementdienst handelt, läuft der Support für Ihre Datenbanken nicht aus.

## <a name="elastic-scale"></a>Elastische Skalierung

Mit der cloudähnlichen Flexibilität können Sie Datenbanken auf die gleiche Weise wie in Azure basierend auf der verfügbaren Kapazität Ihrer Infrastruktur zentral hoch- oder herunterskalieren. Dies ermöglicht die Ausführung von Burstszenarios mit unbeständigen Anforderungen. Dazu zählen Szenarios, in denen Daten jeden Umfangs in Echtzeit erfasst und mit Antwortzeiten von unter einer Sekunde abgefragt werden müssen. Außerdem können Sie Datenbankinstanzen mithilfe der einmaligen Hyperscale-Bereitstellungsoption Azure Database for PostgreSQL Hyperscale horizontal hochskalieren. Diese Option verbessert zusätzlich die Kapazitätsoptimierung der Datenworkloads durch die Verwendung einmaliger *horizontaler* Lese- und Schreibskalierung.

## <a name="self-service-provisioning"></a>Self-Service-Bereitstellung

Azure Arc bietet weitere Cloudvorteile, wie z. B. schnelle Bereitstellung und bedarfsorientierte Automatisierung. Dank der Kubernetes-basierten Orchestrierung können Sie eine Datenbank über GUI- oder CLI-Tools in Sekundenschnelle bereitstellen.

## <a name="unified-management"></a>Einheitliche Verwaltung

Mit vertrauten Tools wie dem Azure-Portal, Azure Data Studio und der Azure CLI (`az`) mit der Erweiterung `arcdata` erhalten Sie nun eine einheitliche Übersicht über alle Datenressourcen, die mit Azure Arc bereitgestellt werden. Neben der Möglichkeit, eine Vielzahl relationaler Datenbanken in Ihrer Umgebung und Azure anzuzeigen und zu verwalten, können Sie auch Protokolle und Telemetriedaten von Kubernetes-APIs abrufen, um die Kapazität und Integrität der zugrunde liegende Infrastruktur zu analysieren. Zusätzlich zur lokalisierten Protokollanalyse und Leistungsüberwachung können Sie nun Azure Monitor nutzen, um einen umfassenden Überblick über sämtliche IT-Ressourcen zu erhalten.

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="disconnected-scenario-support"></a>Unterstützung für nicht verbundenes Szenario

Viele Dienste wie die Self-Service-Bereitstellung, automatisierte Sicherungen/Wiederherstellungen und die Überwachung können lokal in Ihrer Infrastruktur mit oder ohne direkte Verbindung mit Azure ausgeführt werden. Wenn Sie eine direkte Verbindung mit Azure herstellen, können Sie zusätzliche Optionen für die Integration in andere Azure-Dienste wie Azure Monitor nutzen und das Azure-Portal und Azure Resource Manager-APIs an jedem beliebigen Ort auf der Welt zur Verwaltung Ihrer Azure Arc-fähigen Datendienste verwenden.

## <a name="supported-regions"></a>Unterstützte Regionen

In der folgenden Tabelle werden die Szenarien beschrieben, die derzeit für Azure Arc-fähige Datendienste unterstützt werden:

|Azure-Regionen  |Direkter Konnektivitätsmodus  |Indirekter Konnektivitätsmodus  |
|---------|---------|---------|
|East US|Verfügbar|Verfügbar
|USA (Ost) 2|Verfügbar|Verfügbar
|USA (Westen)|Verfügbar|Verfügbar
|USA, Westen 2|Verfügbar|Verfügbar
|USA, Westen 3|Verfügbar|Verfügbar
|USA Nord Mitte | Verfügbar | Verfügbar
|USA (Mitte)|Nicht verfügbar|Verfügbar
|USA Süd Mitte|Verfügbar|Verfügbar
|UK, Süden|Verfügbar|Verfügbar
|Frankreich, Mitte|Verfügbar|Verfügbar
|Europa, Westen |Verfügbar |Verfügbar
|Nordeuropa|Verfügbar|Verfügbar
|Japan, Osten|Nicht verfügbar|Verfügbar
|Korea, Mitte|Nicht verfügbar|Verfügbar
|Asien, Osten|Nicht verfügbar|Verfügbar
|Asien, Südosten|Verfügbar|Verfügbar
|Australien (Osten)|Verfügbar|Verfügbar

## <a name="next-steps"></a>Nächste Schritte

> **Möchten Sie es selbst ausprobieren?**  
> Mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte unternehmen.
>
>Stellen Sie außerdem [Jumpstart ArcBox](https://azurearcjumpstart.io/azure_jumpstart_arcbox/) – eine einfach bereitzustellende Sandbox für Azure Arc – bereit. ArcBox funktioniert in einem einzelnen Azure-Abonnement und einer einzelnen Ressourcengruppe vollständig eigenständig. Dadurch ist es ganz leicht, alle verfügbaren Azure Arc-fähigen Technologien mit nur einem Azure-Abonnement zu nutzen.

[Installieren der Clienttools](install-client-tools.md)

[Planen der Azure Arc-Datendienst-Bereitstellung](plan-azure-arc-data-services.md) (erfordert zunächst die Installation der Clienttools)

[Erstellen einer Azure SQL Managed Instance-Instanz in Azure Arc](create-sql-managed-instance.md) (erfordert zuvor die Erstellung eines Azure Arc-Datencontrollers)

[Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc](create-postgresql-hyperscale-server-group.md) (erfordert zuvor die Erstellung eines Azure Arc-Datencontrollers)
