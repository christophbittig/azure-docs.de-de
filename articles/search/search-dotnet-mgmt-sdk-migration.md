---
title: Upgrade auf das Azure Search .NET Management SDK
titleSuffix: Azure Cognitive Search
description: Aktualisieren Sie von Vorgängerversionen auf das Azure Search-.NET Management SDK. Hier erfahren Sie mehr über neue Features und die Codeänderungen, die für die Migration erforderlich sind.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: 823b758c24ab9b66c47ea76803eb54af8d7c1fb1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631721"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Upgrade der Versionen des Azure Search .NET Management SDK

In diesem Artikel wird erläutert, wie Sie zu höheren Versionen des Azure Search .NET Management SDK migrieren, um Suchdienste bereitzustellen bzw. deren Bereitstellung aufzuheben, die Kapazität anzupassen und API-Schlüssel zu verwalten.

Verwaltungs-SDKs zielen auf eine bestimmte Version der Verwaltungs-REST-API ab. Weitere Informationen zu Konzepten und Vorgängen finden Sie unter [Verwaltung von Suchdiensten](/rest/api/searchmanagement/).

## <a name="versions"></a>Versionen

| SDK-Version | Zugehörige REST-API-Version | Neues Feature oder geändertes Verhalten |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | api-version=2020-30-20 | Fügt Endpunktsicherheit hinzu (IP-Firewalls und Integration mit [Azure Private Link](../private-link/private-endpoint-overview.md)) |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | api-version=2019-10-01 | Verbesserungen der Benutzerfreundlichkeit Breaking Change für [Abfrageschlüssel auflisten](/rest/api/searchmanagement/2021-04-01-preview/query-keys/list-by-search-service) (GET wird nicht mehr unterstützt) |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | api-version=2015-08-19  | Erste Version |

## <a name="how-to-upgrade"></a>Aktualisieren

1. Aktualisieren Sie die NuGet-Referenz für `Microsoft.Azure.Management.Search`, indem Sie entweder die NuGet-Paket-Manager-Konsole verwenden oder mit der rechten Maustaste auf die Projektverweise klicken und in Visual Studio „NuGet-Pakete verwalten...“ auswählen.

1. Nachdem NuGet die neuen Pakete und deren Abhängigkeiten heruntergeladen hat, erstellen Sie Ihr Projekt neu. Je nachdem, wie Ihr Code strukturiert ist, verläuft die Neuerstellung erfolgreich. In diesem Fall sind Sie jetzt fertig.

1. Wenn der Buildvorgang scheitert, kann dies daran liegen, dass Sie einige der SDK-Schnittstellen implementiert haben (z.B. für Komponententests), die geändert wurden. Zum Beheben dieses Problems müssen Sie neuere Methoden implementieren, z. B. `BeginCreateOrUpdateWithHttpMessagesAsync`.

1. Wenn Sie alle Buildfehler behoben haben, können Sie Änderungen an Ihrer Anwendung vornehmen, um die neue Funktionalität zu nutzen. 

## <a name="upgrade-to-30"></a>Upgrade auf 3.0

In Version 3.0 wird der Schutz von privaten Endpunkten hinzugefügt, indem der Zugriff auf IP-Adressbereiche eingeschränkt wird. Außerdem ist optional die Integration mit Azure Private Link für Suchdienste möglich, die im öffentlichen Internet nicht sichtbar sein sollen.

### <a name="new-apis"></a>Neue APIs

| API | Category| Details |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#networkruleset) | IP-Firewall | Einschränkung des Zugriffs auf einen Dienstendpunkt auf eine Liste zulässiger IP-Adressen. Konzepte und Portal-Anweisungen finden Sie unter [Konfigurieren einer IP-Firewall](service-configure-firewall.md). |
| [Freigegebene Private Link-Ressource](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources) | Private Link | Erstellen einer freigegebenen Private Link Ressource, die von einem Suchdienst verwendet werden soll.  |
| [Private Endpunktverbindungen](/rest/api/searchmanagement/2021-04-01-preview/private-endpoint-connections) | Private Link | Einrichten und Verwalten von Verbindungen mit einem Suchdienst über einen privaten Endpunkt. Informationen zu Konzepten und Portal-Anweisungen finden Sie unter [Erstellen eines privaten Endpunkts](service-create-private-endpoint.md).|
| [Private Link-Ressourcen](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources) | Private Link | Für einen Suchdienst, der über eine private Endpunktverbindung verfügt, Abrufen einer Liste aller Dienste, die in demselben virtuellen Netzwerk verwendet werden. Wenn Ihre Suchlösung Indexer enthält, die aus Azure-Datenquellen pullen (Azure Storage, Cosmos DB, Azure SQL) oder Cognitive Services oder Key Vault verwenden, sollten alle diese Ressourcen über Endpunkte im virtuellen Netzwerk verfügen, und diese API sollte eine Liste zurückgeben. |
| [PublicNetworkAccess](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#publicnetworkaccess)| Private Link | Eigenschaft für Anforderungen zum Erstellen oder Aktualisieren von Diensten. Wenn diese Option deaktiviert ist, ist Private Link die einzige Zugriffsmöglichkeit. |

### <a name="breaking-changes"></a>Aktuelle Änderungen

Sie können GET nicht mehr für eine [List Query Keys](/rest/api/searchmanagement/2021-04-01-preview/query-keys/list-by-search-service)-Anforderung verwenden. In früheren Versionen konnten Sie entweder GET oder POST verwenden, in diesem Release und in allen zukünftigen Releases wird nur POST unterstützt. 

## <a name="upgrade-to-20"></a>Upgrade auf 2.0

Version 2 des Azure Search .NET Management SDK ist ein Nebenversionsupgrade, sodass das Ändern des Codes nur minimalen Aufwand erfordert. Die Änderungen am SDK sind ausschließlich clientseitige Änderungen, um die Benutzerfreundlichkeit des SDK selbst zu verbessern. Zu diesen Änderungen gehören folgende:

* `Services.CreateOrUpdate` und entsprechende asynchrone Versionen rufen jetzt automatisch den bereitstellenden `SearchService` ab und werden erst nach der Dienstbereitstellung abgeschlossen. Dadurch müssen Sie einen solchen Abrufcode nicht selbst schreiben.

* Wenn Sie die Dienstbereitstellung weiterhin manuell abrufen möchten, können Sie die neue `Services.BeginCreateOrUpdate`-Methode oder eine der entsprechenden asynchronen Versionen verwenden.

* Dem SDK wurden neue `Services.Update`-Methoden und entsprechende asynchrone Versionen hinzugefügt. Diese Methoden verwenden HTTP PATCH, um inkrementelle Aktualisierung eines Diensts zu unterstützen. Beispielsweise können Sie nun einen Dienst skalieren, indem Sie an diese Methoden eine `SearchService`-Instanz übergeben, die nur die gewünschten Eigenschaften `partitionCount` und `replicaCount` enthält. Die alte Variante, `Services.Get` aufzurufen und den zurückgegebenen `SearchService` zu ändern und an `Services.CreateOrUpdate` zu übergeben, wird weiterhin unterstützt, ist jedoch nicht mehr notwendig. 

## <a name="next-steps"></a>Nächste Schritte

Wenn Probleme auftreten, ist das beste Forum für Fragen [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Wenn Sie einen Fehler finden, können Sie das Problem im [Azure .NET SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/issues)melden. Stellen Sie sicher, dass Sie den Titel des Problems mit „[search]“ kennzeichnen.