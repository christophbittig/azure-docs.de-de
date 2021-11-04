---
title: Sekundäre Hyperscale-Replikate
description: In diesem Artikel werden die unterschiedlichen Arten sekundärer Replikate beschrieben, die auf der Hyperscale-Dienstebene verfügbar sind.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.topic: overview
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 9/24/2021
ms.openlocfilehash: b87c8dcfab561a010019a71f59307bdc8943761d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439517"
---
# <a name="hyperscale-secondary-replicas"></a>Sekundäre Hyperscale-Replikate
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Wie unter [Hyperscale-Dienstebene](service-tier-hyperscale.md) beschrieben bietet die Hyperscale-Dienstebene in Azure SQL-Datenbank zwei verschiedene Arten von Computeknoten, die auch als „Replikate“ bezeichnet werden:

- Primär: Dieser Knoten wird für Lese- und Schreibvorgänge verwendet.
- Sekundär: Dieser Knoten bietet [horizontale Leseskalierung](read-scale-out.md), [Hochverfügbarkeit](high-availability-sla.md) und [Georeplikation](active-geo-replication-overview.md).

Sekundäre Replikate sind immer schreibgeschützt. Es gibt drei verschiedene Arten:

- Hochverfügbarkeitsreplikat
- Benanntes Replikat ([Vorschauversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/))
- Georeplikation ([Vorschauversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/))

Jeder Typ verfügt über eine unterschiedliche Architektur und über verschiedene Features, Zwecke und Kosten. Je nach Features, die Sie benötigen, können Sie nur einen oder alle drei Typen zusammen verwenden.

## <a name="high-availability-replica"></a>Hochverfügbarkeitsreplikat

Ein Hochverfügbarkeitsreplikat verwendet dieselben Seitenserver wie das primäre Replikat, es sind also keine Datenkopien erforderlich, um ein Hochverfügbarkeitsreplikat hinzuzufügen. Hochverfügbarkeitsreplikate werden hauptsächlich verwendet, um die Datenbankverfügbarkeit zu erhöhen, da sie zu Failoverzwecken als unmittelbar betriebsbereite Standbyserver verwendet werden können. Wenn die Verfügbarkeit des primären Replikats nicht mehr gegeben ist, erfolgt das Failover auf eins der vorhandenen Hochverfügbarkeitsreplikate schnell und automatisch. Die Verbindungszeichenfolge muss nicht geändert werden. Während eines Failovers kann für Anwendungen eine minimale Downtime auftreten. Dies ist aktiven Verbindungen geschuldet, die getrennt werden. Wie gewöhnlich wird für dieses Szenario eine geeignete Wiederholungslogik empfohlen. Mehrere Treiber bieten zu einem gewissen Grad bereits eine automatische Wiederholungslogik. Wenn Sie .NET verwenden, bietet die aktuelle Version der Bibliothek [Microsoft.Data.SqlClient](https://devblogs.microsoft.com/azure-sql/configurable-retry-logic-for-microsoft-data-sqlclient/) nativ vollständige Unterstützung für eine konfigurierbare automatische Wiederholungslogik.

Hochverfügbarkeitsreplikate verwenden denselben Server- und Datenbanknamen wie das primäre Replikat. Das Servicelevelziel dieser Replikate ist oft identisch mit dem des primären Replikats. Hochverfügbarkeitsreplikate können nicht als eigenständige Ressource über das Portal oder eine API angezeigt oder verwaltet werden. 

Es kann null bis vier Hochverfügbarkeitsreplikate geben. Die Anzahl kann während der Erstellung einer Datenbank oder nach Erstellung einer Datenbank über den üblichen Verwaltungsendpunkt und die üblichen Verwaltungstools (z. B. PowerShell, Azure CLI, Azure-Portal, REST-API) geändert werden. Das Erstellen oder Entfernen von Hochverfügbarkeitsreplikaten wirkt sich nicht auf aktive Verbindungen auf dem primären Replikat aus.

### <a name="connecting-to-an-ha-replica"></a>Herstellen einer Verbindung zu einem Hochverfügbarkeitsreplikat

Bei Hyperscale-Datenbanken bestimmt das Argument `ApplicationIntent` in der vom Client verwendeten Verbindungszeichenfolge, ob die Verbindung an das primäre Replikat mit Schreibzugriff oder an ein schreibgeschütztes Hochverfügbarkeitsreplikat weitergeleitet wird. Wenn `ApplicationIntent` auf `ReadOnly` festgelegt ist und die Datenbank kein sekundäres Replikat aufweist, wird die Verbindung an das primäre Replikat weitergeleitet, und sie weist standardmäßig das Verhalten `ReadWrite` auf.

```csharp
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Alle Hochverfügbarkeitsreplikate sind in ihrer Ressourcenkapazität identisch. Wenn mehr als ein Hochverfügbarkeitsreplikat vorhanden ist, wird die Workload mit Leseabsicht willkürlich auf alle Hochverfügbarkeitsreplikate verteilt. Wenn es mehrere Hochverfügbarkeitsreplikate gibt, denken Sie daran, dass jedes im Hinblick auf am primären Replikat vorgenommene Datenänderungen über eine unterschiedliche Datenlatenz verfügen könnte. Jedes Hochverfügbarkeitsreplikat verwendet dieselben Daten wie das primäre Replikat für dieselben Seitenserver. Lokale Datencaches auf den einzelnen Hochverfügbarkeitsreplikaten spiegeln jedoch die am primären Replikat vorgenommenen Änderungen über den Transaktionsprotokolldienst wider, der Protokolldatensätze vom primären Replikat an Hochverfügbarkeitsreplikate weiterleitet. Dies führt dazu, dass die Anwendung von Protokolldatensätzen je nach Workload, die von einem Hochverfügbarkeitsreplikat verarbeitet wird, mit unterschiedlicher Geschwindigkeit durchgeführt werden kann. Daher können verschiedene Replikate eine unterschiedliche Datenlatenz im Hinblick auf das primäre Replikat aufweisen.

## <a name="named-replica-in-preview"></a>Benanntes Replikat (Vorschauversion)

Ein benanntes Replikat verwendet genau wie ein Hochverfügbarkeitsreplikat dieselben Seitenserver wie das primäre Replikat. Ähnlich wie Hochverfügbarkeitsreplikate sind keine Datenkopien erforderlich, um ein benanntes Replikat hinzuzufügen. 

Benannte Replikate unterscheiden sich in den folgenden Punkten von Hochverfügbarkeitsreplikaten: 

- Benannte Replikate werden im Portal und in API-Aufrufen (Azure CLI, PowerShell, T-SQL) als reguläre (schreibgeschützte) Azure SQL-Datenbank-Instanzen angezeigt.
- Benannte Replikate können Datenbanknamen haben, die sich vom primären Replikat unterscheiden. Optional können sie sich auf einem anderen logischen Server befinden, solange es sich um dieselbe Region wie die des primären Replikats handelt.
- Benannte Replikate verfügen über ein eigenes Servicelevelziel, das unabhängig vom primären Replikat festgelegt und geändert werden kann.
- Es werden bis zu 30 benannte Replikate unterstützt (pro primärem Replikat).
- Es werden verschiedene Authentifizierungslösungen für jedes benannte Replikat unterstützt, indem verschiedene Anmeldungen für logische Server erstellt werden, die benannte Replikate hosten.

Daher bieten benannte Replikate gegenüber Hochverfügbarkeitsreplikaten mehrere Vorteile, was schreibgeschützte Workloads betrifft:

- Bei Benutzern, die mit einem benannten Replikat verbunden sind, wird die Verbindung nicht getrennt, wenn das primäre Replikat hoch- oder herunterskaliert wird. Gleichzeitig sind Benutzer, die mit dem primären Replikat verbunden sind, vom Hoch- oder Herunterskalieren der benannten Replikate nicht betroffen.
-   Workloads, die auf einem beliebigen Replikat (primär oder benannt) ausgeführt werden, sind von zeitintensiven Abfragen auf anderen Replikaten nicht betroffen.

Das Hauptziel benannter Replikate besteht darin, umfangreiche OLTP-Szenarien mit [horizontaler Leseskalierung](read-scale-out.md) zu ermöglichen und HTAP-Workloads (Hybrid Transactional and Analytical Processing, hybride Transaktions- und Analyseverarbeitung) zu optimieren. Beispiele für das Erstellen solcher Lösungen finden Sie hier:

- [OLTP: Beispiel für horizontale Skalierung](https://github.com/Azure-Samples/azure-sql-db-named-replica-oltp-scaleout)
- [HTAP: Beispiel für horizontale Skalierung](https://github.com/Azure-Samples/azure-sql-db-named-replica-htap)

Abgesehen von den oben aufgeführten Hauptszenarios bieten benannte Replikate Flexibilität und Elastizität, sodass sie auch für viele anderen Anwendungsfälle geeignet sind:
- [Zugriffsisolation:](hyperscale-named-replica-security-configure.md) Sie können Zugriff auf ein bestimmtes benanntes Replikat gewähren, allerdings nicht auf das primäre Replikat oder andere benannte Replikate.
- Workloadabhängige Servicelevelziele: Da ein benanntes Replikat über ein eigenes Servicelevelziel verfügen kann, ist es möglich, andere benannte Replikate für andere Workloads und Anwendungsfälle zu verwenden. Ein benanntes Replikat könnte beispielsweise für Power BI-Anforderungen verwendet werden, während ein anderes für Daten für Data-Science-Aufgaben in Apache Spark verwendet werden kann. Jedes kann über ein unabhängiges Servicelevelziel verfügen und unabhängig skaliert werden.
- Workloadabhängiges Routing: Mit bis zu 30 benannten Replikaten ist es möglich, benannte Replikate in Gruppen zu verwenden, damit eine Anwendung von einer anderen isoliert werden kann. Sie könnten beispielsweise eine Gruppe vier benannter Replikate verwenden, um Anforderungen zu verarbeiten, die von mobilen Anwendungen stammen. Eine andere aus zwei benannten Replikaten bestehende Gruppe kann verwendet werden, um Anforderungen zu verarbeiten, die von einer Webanwendung stammen. Dieser Ansatz ermöglicht eine genaue Anpassung der Leistung und der Kosten der einzelnen Gruppen.

Im folgenden Beispiel wird das benannte Replikat `WideWorldImporters_NR` für die Datenbank `WideWorldImporters` erstellt. Das primäre Replikat verwendet das Servicelevelziel „HS_Gen5_4“, das benannte Replikat hingegen „HS_Gen5_2“. Beide verwenden denselben logischen Server `MyServer`. Wenn Sie lieber direkt eine REST-API verwenden möchten, ist auch die folgende Option möglich: [Datenbanken: Erstellen einer Datenbank als benanntes sekundäres Replikat](/rest/api/sql/2020-11-01-preview/databases/createorupdate#creates-a-database-as-named-replica-secondary).

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters]
ADD SECONDARY ON SERVER [MyServer] 
WITH (SERVICE_OBJECTIVE = 'HS_Gen5_2', SECONDARY_TYPE = Named, DATABASE_NAME = [WideWorldImporters_NR]);
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
New-AzSqlDatabaseSecondary -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters" -PartnerResourceGroupName "MyResourceGroup" -PartnerServerName "MyServer" -PartnerDatabaseName "WideWorldImporters_NR" -SecondaryType Named -SecondaryServiceObjectiveName HS_Gen5_2
```
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyServer --secondary-type named --partner-database WideWorldImporters_NR --partner-server MyServer --service-objective HS_Gen5_2
```

---

Da keine Datenverschiebungen erforderlich sind, kann ein benanntes Replikat in den meisten Fällen in etwa einer Minute erstellt werden. Sobald das benannte Replikat verfügbar ist, kann es im Portal oder mit einem beliebigem Befehlszeilentool wie der Azure CLI oder PowerShell angezeigt werden. Ein benanntes Replikat kann als reguläre schreibgeschützte Datenbank verwendet werden. 

> [!NOTE]
> Antworten auf häufig gestellte Fragen zu benannten Hyperscale-Replikaten finden Sie unter [Benannte Hyperscale-Replikate in Azure SQL-Datenbank: Häufig gestellte Fragen](service-tier-hyperscale-named-replicas-faq.yml).

### <a name="connecting-to-a-named-replica"></a>Herstellen einer Verbindung zu einem benannten Replikat

Zum Herstellen einer Verbindung mit einem benannten Replikat müssen Sie die Verbindungszeichenfolge für dieses benannte Replikat verwenden und auf die Server- und Datenbanknamen verweisen. Die Option „ApplicationIntent=ReadOnly“ muss nicht angegeben werden, da benannte Replikate immer schreibgeschützt sind.

Wie bei Hochverfügbarkeitsreplikaten werden die Datencaches auf den einzelnen benannten Replikaten mit dem primären Replikat über den Transaktionsprotokolldienst synchronisiert, obwohl das primäre Replikat, die Hochverfügbarkeitsreplikate und die benannten Replikate dieselben Daten auf denselben Seitenservern gemeinsam nutzen. Der Transaktionsprotokolldienst leitet Protokolldatensätze vom primären Replikat an die benannten Replikate weiter. Dies führt dazu, dass die Anwendung von Protokolldatensätzen je nach Workload, die von einem benannten Replikat verarbeitet wird, mit unterschiedlicher Geschwindigkeit durchgeführt werden kann. Daher können verschiedene Replikate eine unterschiedliche Datenlatenz im Hinblick auf das primäre Replikat aufweisen.

### <a name="modifying-a-named-replica"></a>Ändern eines benannten Replikats

Sie können das Servicelevelziel eines benannten Replikats bei seiner Erstellung definieren. Dies erfolgt über den Befehl `ALTER DATABASE` oder auf andere unterstützte Weise (Azure CLI, PowerShell, REST-API). Wenn Sie das Servicelevelziel ändern möchten, nachdem das Replikat erstellt wurde, können Sie dies mithilfe des Befehls `ALTER DATABASE ... MODIFY` für das benannte Replikat selbst tun. Wenn es sich beispielsweise bei `WideWorldImporters_NR` um das benannte Replikat der Datenbank `WideWorldImporters` handelt, können Sie die unten dargestellte Vorgehensweise verwenden. 

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters_NR] MODIFY (SERVICE_OBJECTIVE = 'HS_Gen5_4')
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Set-AzSqlDatabase -ResourceGroup "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR" -RequestedServiceObjectiveName "HS_Gen5_4"
```
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az sql db update -g MyResourceGroup -s MyServer -n WideWorldImporters_NR --service-objective HS_Gen5_4
```

---

### <a name="removing-a-named-replica"></a>Entfernen eines benannten Replikats

Wenn Sie ein benanntes Replikat entfernen möchten, ist die Vorgehensweise identisch mit der für reguläre Datenbanken. Sorgen Sie dafür, dass Sie mit der Datenbank `master` des Servers verbunden sind, der das benannte Replikat enthält, das Sie löschen möchten. Verwenden Sie anschließend den folgenden Befehl:

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
DROP DATABASE [WideWorldImporters_NR];
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Remove-AzSqlDatabase -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR"
```
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az sql db delete -g MyResourceGroup -s MyServer -n WideWorldImporters_NR
```
---

> [!IMPORTANT]
> Benannte Replikate werden automatisch entfernt, wenn das primäre Replikat, auf deren Grundlage sie erstellt wurden, gelöscht wird.

### <a name="known-issues"></a>Bekannte Probleme

#### <a name="partially-incorrect-data-returned-from-sysdatabases"></a>Teilweise fehlerhafte von sys.databases zurückgegebene Daten
Während der öffentlichen Vorschau ist es möglich, dass Zeilenwerte, die von `sys.databases` zurückgegeben werden, für benannte Replikate in Spalten außer `name` und `database_id` nicht konsistent oder korrekt sind. Für die Spalte `compatibility_level` eines benannten Replikats könnte beispielsweise der Wert 140 gemeldet werden, selbst wenn die primäre Datenbank, auf deren Grundlage das benannte Replikat erstellt wurde, auf 150 festgelegt ist. Eine Umgehung, sofern möglich, ist das Abrufen derselben Daten mithilfe der Funktion `DATABASEPROPERTYEX()`, die die korrekten Daten zurückgibt.

## <a name="geo-replica-in-preview"></a>Georeplikation (Vorschauversion)

Mit [aktiver Georeplikation](active-geo-replication-overview.md) können Sie ein lesbares sekundäres Replikat der primären Hyperscale-Datenbank in derselben oder einer anderen Azure-Region erstellen. Georeplikate müssen auf einem anderen logischen Server erstellt werden. Der Datenbankname eines Georeplikats stimmt immer mit dem Datenbanknamen des primären Replikats überein.

Wenn ein Georeplikat erstellt wird, werden alle Daten vom primären Replikat auf andere Seitenserver kopiert. Ein Georeplikat teilt seine Seitenserver nicht mit dem primären Replikat, selbst wenn sie sich in derselben Region befinden. Diese Architektur bietet die notwendige Redundanz für Geofailover.

Mit Georeplikaten wird eine hinsichtlich der Transaktionen konsistente Kopie der Datenbank über asynchrone Replikation erstellt. Wenn sich ein Georeplikat in einer anderen Azure-Region befindet, kann es bei einem Notfall oder Ausfall in der primären Region für die Notfallwiederherstellung verwendet werden. Georeplikate können auch für geografische Szenarios mit horizontaler Leseskalierung verwendet werden.

In Hyperscale muss ein geografisches Failover manuell initiiert werden. Nach dem Failover verfügt das primäre Replikat über einen anderen Verbindungsendpunkt, der auf den logischen Servername verweist, der das neue primäre Replikat hostet. Weitere Informationen finden Sie unter [Erstellen und Verwenden der aktiven Georeplikation: Azure SQL-Datenbank](active-geo-replication-overview.md).

Die Georeplikation für Hyperscale-Datenbanken befindet sich aktuell in der Vorschau und weist die folgenden Einschränkungen auf:
- Nur ein Georeplikat kann (in derselben oder einer anderen Region) erstellt werden.
- Failovergruppen werden nicht unterstützt. 
- Geplante Failover werden nicht unterstützt.
- Die Point-in-Time-Wiederherstellung von Georeplikaten wird nicht unterstützt.
- Das Erstellen einer Datenbankkopie des Georeplikats wird nicht unterstützt. 
- Ein sekundäres Replikat eines sekundären Replikats (auch bekannt als „Verkettung von Georeplikaten“) wird nicht unterstützt. 

## <a name="next-steps"></a>Nächste Schritte

- [Hyperscale-Dienstebene](service-tier-hyperscale.md)
- [Aktive Georeplikation](active-geo-replication-overview.md)
- [Konfigurieren von isoliertem Zugriff auf ein benanntes Hyperscale-Replikat](hyperscale-named-replica-security-configure.md)
- [Benannte Hyperscale-Replikate in Azure SQL-Datenbank: Häufig gestellte Fragen](service-tier-hyperscale-named-replicas-faq.yml)
