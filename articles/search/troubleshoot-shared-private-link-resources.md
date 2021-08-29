---
title: Problembehandlung freigegebener Private Link-Ressourcen
titleSuffix: Azure Cognitive Search
description: Leitfaden zur Behandlung häufiger Probleme bei der Verwaltung freigegebener Private Link-Ressourcen.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/30/2021
ms.openlocfilehash: 81bae18cdc4a977ef03ddf807f9277037d939bd0
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122356437"
---
# <a name="troubleshooting-common-issues-with-shared-private-link-resources"></a>Behandlung von häufigen Problemen freigegebener Private Link-Ressourcen

Freigegebene Private Link-Ressourcen ermöglichen es Azure Cognitive Search, sichere ausgehende Verbindungen für den Zugriff auf Kundenressourcen herzustellen. Während der Verwaltung (Erstellen, Löschen oder Aktualisieren) dieser Ressourcen können jedoch verschiedene Arten von Fehlern auftreten.

## <a name="creating-a-shared-private-link-resource"></a>Erstellen einer freigegebenen Private Link-Ressource

Beim Erstellen einer freigegebenen privaten Link-Ressource sind vier verschiedene Schritte erforderlich:

1. Der Kunde ruft die [CreateOrUpdate-API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) der Verwaltungsebene für den Suchressourcenanbieter (Search Resource Provider, RP) mit Details zur freigegebenen Private Link-Ressource auf, die erstellt werden soll.

2. Der Such-RP überprüft die Anforderung und startet bei Erfolg einen asynchronen Azure Resource Manager-Vorgang (dessen Status vom Kunden abgefragt werden kann).

3. Die Suche fragt den Abschluss des Vorgangs ab (was in der Regel einige Minuten dauert). An diesem Punkt weist die freigegebene Private Link-Ressource den Bereitstellungsstatus „Updating“ (Wird aktualisiert) auf.

4. Nachdem der Vorgang erfolgreich abgeschlossen wurde, wird ein privater Endpunkt (zusammen mit DNS-Zonen und Zuordnungen) erstellt. Wenn der Kunde den Zustand der freigegebenen Private Link-Ressource an diesem Punkt abfragt, erhält er den Bereitstellungsstatus „Succeeded“ (Erfolgreich).

![Schritte beim Erstellen freigegebener Private Link-Ressourcen ](media\troubleshoot-shared-private-link-resources\shared-private-link-states.png)

Einige häufige Fehler, die während der Erstellungsphase auftreten können, werden unten aufgeführt.

### <a name="request-validation-failures"></a>Anforderungsüberprüfungsfehler

+ Nicht unterstützte SKU: Freigegebene Private Link-Ressourcen können nur für kostenpflichtige SKUs erstellt werden. Dienste im Free-Tarif werden nicht unterstützt.

+ Namensüberprüfung: Freigegebene Private Link-Ressourcennamen sind auf einen bestimmten Satz von Zeichen beschränkt. Wenn der Ressourcenname ungültige Zeichen enthält, wird die Anforderung zum Erstellen der Ressource nicht akzeptiert.
Die Regeln zum Benennen einer freigegebenen Private Link-Ressource sind:
  
  + Länge zwischen 1 und 60 Zeichen lang sein.
  + Darf nur alphanumerische Zeichen oder die Zeichen Unterstrich (_), Punkt (.) oder Bindestrich (-) enthalten.

+ `groupId`-Überprüfung: Die als Teil der Anforderung zum Erstellen einer freigegebenen Private Link-Ressource angegebene `groupId` sollte (sowohl in der Schreibweise als bezüglich Groß-/Kleinschreibung) mit der folgenden Tabelle übereinstimmen:

| Azure-Ressource | Gruppen-ID | Erste verfügbare API-Version |
| --- | --- | --- |
| Azure Storage: Blob (oder) ADLS Gen2 | `blob`| `2020-08-01` |
| Azure Storage: Tabellen | `table`| `2020-08-01` |
| Azure Cosmos DB: SQL-API | `Sql`| `2020-08-01` |
| Azure SQL-Datenbank | `sqlServer`| `2020-08-01` |
| Azure Database for MySQL (Vorschau) | `mysqlServer`| `2020-08-01-Preview` |
| Azure-Schlüsseltresor | `vault` | `2020-08-01` |
| Azure Functions (Vorschau) | `sites` | `2020-08-01-Preview` |

Ressourcen, die mit „(preview)“ gekennzeichnet sind, sind nur in API-Vorschauversionen der Verwaltungsebene und noch nicht allgemein verfügbar. Jede andere `groupId` (oder eine in einer API-Version verwendete `groupId`, die dies nicht unterstützt) würde zu einem Validierungsfehler führen.

+ `privateLinkResourceId`-Typvalidierung`groupId`: Azure Cognitive Search überprüft, ob der „richtige“ Ressourcentyp in `privateLinkResourceId` angegeben ist. Folgende Ressourcentypen sind gültig:

| Azure-Ressource | Ressourcentyp | Erste verfügbare API-Version |
| --- | --- | --- |
| Azure Storage | `Microsoft.Storage/storageAccounts`| `2020-08-01` |
| Azure Cosmos DB | `Microsoft.DocumentDb/databaseAccounts`| `2020-08-01` |
| Azure SQL-Datenbank | `Microsoft.Sql/servers`| `2020-08-01` |
| Azure Database for MySQL (Vorschau) | `Microsoft.DBforMySQL/servers`| `2020-08-01-Preview` |
| Azure-Schlüsseltresor | `Microsoft.KeyVault/vaults` | `2020-08-01` |
| Azure Functions (Vorschau) | `Microsoft.Web/sites` | `2020-08-01-Preview` |

Darüber hinaus muss die angegebene `groupId` für den angegebenen Ressourcentyp gültig sein. Beispielsweise ist `groupId` „blob“ für den Typ „Microsoft.Storage/storageAccounts“ gültig, kann aber nicht mit einem anderen Ressourcentyp verwendet werden. Für eine bestimmte Version der Suchverwaltungs-API können Kunden die unterstützte `groupId` und Ressourcentypdetails herausfinden, indem sie die [Liste der unterstützten APIs](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported) verwenden.

+ Erzwingung der Kontingentgrenze: Für Suchdienste gelten Kontingente für die unterschiedliche Anzahl von freigegebenen Private Link-Ressourcen, die erstellt werden können, und für die Anzahl der verschiedenen verwendeten Zielressourcentypen (basierend auf `groupId`). Diese sind im Abschnitt [Ressourcenlimits für freigegebene Private Link-Ressourcen](search-limits-quotas-capacity.md#shared-private-link-resource-limits) auf der Seite zu den Azure Cognitive Search Services-Grenzwerten dokumentiert.

### <a name="azure-resource-manager-deployment-failures"></a>Azure Resource Manager-Bereitstellungsfehler

Ein Suchdienst initiiert die Anforderung zum Erstellen eines freigegebenen Private Link, aber Azure Resource Manager übernimmt die eigentliche Arbeit. Sie können [den Status der Bereitstellung im Portal oder per Abfrage überprüfen](search-indexer-howto-access-private.md#step-3-check-the-status-of-the-private-endpoint-creation) und etwaige Fehler beheben.

Freigegebene Private Link-Ressourcen, bei denen die Azure Resource Manager-Bereitstellung fehlgeschlagen ist, werden in [List](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/list-by-service)- und [Get](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get)-API-Aufrufen angezeigt, weisen jedoch den „Bereitstellungsstatus“ `Failed` auf. Sobald der Grund für den Azure Resource Manager-Bereitstellungsfehler festgestellt wurde, löschen Sie die Ressource `Failed`, und erstellen Sie sie neu, nachdem Sie die entsprechende Auflösung aus der folgenden Tabelle angewendet haben.

| Grund für den Bereitstellungsfehler | BESCHREIBUNG | Lösung |
| --- | --- | --- |
| Netzwerkressourcenanbieter nicht im Abonnement der Zielressource registriert | Ein privater Endpunkt (und zugehörige DNS-Zuordnungen) wird für die Zielressource (Speicherkonto, CosmosDB, SQL Server usw.) über den `Microsoft.Network`-Ressourcenanbieter erstellt. Wenn das Abonnement, das die Zielressource hostet („Zielabonnement“), nicht beim `Microsoft.Network`-Ressourcenanbieter registriert ist, kann die Azure Resource Manager-Bereitstellung fehlschlagen. | Kunden müssen diesen Ressourcenanbieter in ihrem Zielabonnement registrieren. In der Regel kann dies entweder über das Azure-Portal, über PowerShell oder die CLI erfolgen, wie in [diesem Leitfaden](../azure-resource-manager/management/resource-providers-and-types.md) beschrieben. |
| Ungültige `groupId` für die Zielressource | Wenn CosmosDB-Konten erstellt werden, können Kunden den API-Typ für das Datenbankkonto angeben. CosmosDB bietet zwar verschiedene API-Typen, aber Azure Cognitive Search unterstützt nur „Sql“ als `groupId` für freigegebene Private Link-Ressourcen. Wenn eine freigegebene Private Link-Ressource vom Typ „Sql“ für eine `privateLinkResourceId` erstellt wird, die auf ein Nicht-SQL-Datenbankkonto verweist, schlägt die Azure Resource Manager-Bereitstellung aufgrund des `groupId`-Konflikts fehl. Die Azure-Ressourcen-ID eines CosmosDB-Kontos reicht nicht aus, um den verwendeten API-Typ zu bestimmen. Azure Cognitive Search versucht, den privaten Endpunkt zu erstellen, was dann von CosmosDB verweigert wird. | Kunden sollten sicherstellen, dass die `privateLinkResourceId` der angegebenen CosmosDB-Ressource für ein Datenbankkonto vom API-Typ „Sql“ bestimmt ist. |
| Zielressource nicht gefunden | Das Vorhandensein der in `privateLinkResourceId` angegebenen Zielressource wird nur während des Beginns der Azure Resource Manager-Bereitstellung überprüft. Wenn die Zielressource nicht mehr verfügbar ist, tritt bei der Bereitstellung ein Fehler auf. | Der Kunde muss sicherstellen, dass die Zielressource im angegebenen Abonnement und in der angegebenen Ressourcengruppe vorhanden ist und nicht verschoben/gelöscht wird. |
| Vorübergehende/andere Fehler | Die Azure Resource Manager-Bereitstellung kann bei einem Ausfall der Infrastruktur oder aus anderen unerwarteten Gründen fehlschlagen. Dies sollte selten vorkommen und deutet normalerweise auf einen vorübergehenden Zustand hin. | Versuchen Sie zu einem späteren Zeitpunkt erneut, diese Ressource zu erstellen. Wenn das Problem weiterhin besteht, wenden Sie sich an den Azure-Support. |

### <a name="resource-stuck-in-updating-or-incomplete-state"></a>Ressource verbleibt im Zustand „Updating“ (Wird aktualisiert) oder „Incomplete“ (Unvollständig)

In der Regel sollte eine freigegebene Private Link-Ressource in wenigen Minuten in den Endzustand (`Succeeded` oder `Failed`) wechseln, nachdem die Anforderung vom Suchressourcenanbieter akzeptiert wurde.

In seltenen Fällen kann es vorkommen, dass Azure Cognitive Search den Status der freigegebenen Private Link-Ressource nicht richtig auf einen Endzustand (`Succeeded` oder `Failed`) festlegt. Dies tritt in der Regel aufgrund eines unerwarteten oder schwerwiegenden Fehlers im Suchressourcenanbieter auf. Freigegebene Private Link-Ressourcen werden automatisch in einen Zustand `Failed` versetzt, wenn sie seit mehr als 8 Stunden in einem Zustand verblieben sind, der kein Endzustand ist.

Wenn Sie feststellen, dass die freigegebene Private Link-Ressource nicht in einen Endzustand versetzt wurde, warten Sie 8 Stunden, um sicherzustellen, dass sie als `Failed` gekennzeichnet wird, bevor Sie sie löschen und neu erstellen. Anstatt zu warten, können Sie alternativ versuchen, eine weitere freigegebene Private Link-Ressource mit einem anderen Namen zu erstellen (alle anderen Parameter bleiben dabei gleich).

## <a name="updating-a-shared-private-link-resource"></a>Aktualisieren einer freigegebenen Private Link-Ressource

Eine vorhandene freigegebene Private Link-Ressource kann mithilfe der [API zum Erstellen oder Aktualisieren](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) aktualisiert werden. Der Suchressourcenanbieter ermöglicht nur eingeschränkte Aktualisierungen der freigegebenen Private Link-Ressource. Nur die Anforderungsnachricht kann über diese API geändert werden.

+ Es ist nicht möglich, die „Kerneigenschaften“ einer vorhandenen freigegebenen Private Link-Ressource (z. B. `privateLinkResourceId` oder `groupId`) zu aktualisieren. Dies wird niemals unterstützt. Wenn eine andere Eigenschaft außer der Anforderungsnachricht geändert werden muss, empfehlen wir Kunden, die freigegebene Private Link-Ressource zu löschen und dann neu zu erstellen.

+ Der Versuch, die Anforderungsnachricht einer freigegebenen Private Link-Ressource zu aktualisieren, ist nur möglich, wenn sie den Bereitstellungsstatus `Succeeded` erreicht hat.

## <a name="deleting-a-shared-private-link-resource"></a>Löschen einer freigegebenen Private Link-Ressource

Kunden können eine vorhandene freigegebene Private Link-Ressource über die [Lösch-API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/delete) löschen. Ähnlich wie bei der Erstellung (oder Aktualisierung) ist auch dies ein asynchroner Vorgang mit vier Schritten:

1. Der Kunde fordert den Suchressourcenanbieter auf, die freigegebene Private Link-Ressource zu löschen.

2. Der Suchressourcenanbieter überprüft, ob die Ressource vorhanden ist und sich in einem Zustand befindet, in dem ein Löschvorgang gültig ist. Wenn dies der Fall ist, initiiert er einen Azure Resource Manager-Löschvorgang, um die Ressource zu entfernen.

3. Die Suche fragt den Abschluss des Vorgangs ab (was in der Regel einige Minuten dauert). An diesem Punkt weist die freigegebene Private Link-Ressource den Bereitstellungsstatus „Deleting“ (Wird gelöscht) auf.

4. Nachdem der Vorgang erfolgreich abgeschlossen wurde, werden der unterstützende private Endpunkt und alle zugehörigen DNS-Zuordnungen entfernt. Die Ressource wird nicht als Teil des [List](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/list-by-service)-Vorgangs angezeigt, und der Versuch, einen [Get](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get)-Vorgang für diese Ressource auszuführen, führt zu 404 Not Found (Nicht gefunden).

![Schritte beim Löschen freigegebener Private Link-Ressourcen ](media\troubleshoot-shared-private-link-resources\shared-private-link-delete-states.png)

Einige häufige Fehler, die während der Löschphase auftreten können, werden unten aufgeführt.

| Fehlertyp | BESCHREIBUNG | Lösung |
| --- | --- | --- |
| Ressource befindet sich in keinem Endzustand | Eine freigegebene Private Link-Ressource, die sich nicht in einem Endzustand (`Succeeded` oder `Failed`) befindet, kann nicht gelöscht werden. Es ist möglich (selten), dass eine freigegebene Private Link-Ressource bis zu 8 Stunden lang in einem Zustand verbleibt, der kein Endzustand ist. | Warten Sie, bis die Ressource einen Endzustand erreicht hat, und wiederholen Sie die Löschanforderung. |
| Fehler beim Löschvorgang mit Fehlerursache „Conflict“ (Konflikt) | Der Azure Resource Manager-Vorgang zum Löschen einer freigegebenen Private Link-Ressource greift auf den Ressourcenanbieter der in `privateLinkResourceId` angegebenen Zielressource („Zielressourcenanbieter“) zu, bevor er den privaten Endpunkt und die DNS-Zuordnungen entfernen kann. Kunden können [Azure-Ressourcensperren](../azure-resource-manager/management/lock-resources.md) verwenden, um Änderungen an ihren Ressourcen zu verhindern. Wenn Azure Resource Manager den Zielressourcenanbieter erreicht, muss der Zielressourcenanbieter den Zustand der Zielressource ändern (um Details zum privaten Endpunkt aus seinen Metadaten zu entfernen). Wenn für die Zielressource eine Sperre konfiguriert ist (oder für ihre Ressourcengruppe/ihr Abonnement), schlägt der Azure Resource Manager-Vorgang mit einem „Conflict“ (und entsprechenden Details) fehl. Die freigegebene Private Link-Ressource wird nicht gelöscht. | Kunden sollten die Sperre für die Zielressource entfernen, bevor sie den Löschvorgang wiederholen. **Hinweis**: Dieses Problem kann auch auftreten, wenn Kunden versuchen, einen Suchdienst mit freigegebenen Private Link-Ressourcen zu löschen, die auf „gesperrte“ Zielressourcen verweisen. |
| Fehler bei Löschvorgang | Der asynchrone Azure Resource Manager-Löschvorgang kann in seltenen Fällen fehlschlagen. Wenn dieser Vorgang fehlschlägt, erhalten Kunden beim Abfragen des Zustands des asynchronen Vorgangs eine Fehlermeldung und entsprechende Details. | Wiederholen Sie den Vorgang zu einem späteren Zeitpunkt, oder wenden Sie sich an den Azure-Support, wenn das Problem weiterhin besteht.
| Ressource verbleibt im Zustand „Deleting“ (Wird gelöscht) | In seltenen Fällen kann eine freigegebene Private Link-Ressource bis zu 8 Stunden lang im Zustand „Deleting“ (Wird gelöscht) verbleiben, wahrscheinlich aufgrund eines schwerwiegenden Fehlers im Suchressourcenanbieter. | Warten Sie 8 Stunden, nach denen die Ressource in den Zustand `Failed` übergeht, und wiederholen Sie die Anforderung dann.|

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über freigegebene Private Link-Ressourcen und deren Verwendung für sicheren Zugriff auf geschützte Inhalte.

+ [Zugreifen auf geschützte Inhalte über Indexer](search-indexer-howto-access-private.md)
+ [REST-API-Referenz](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources)