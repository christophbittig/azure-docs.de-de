---
title: Grundlegendes zur Abfragesprache
description: Beschreibt Resource Graph-Tabellen und die verfügbaren Kusto-Datentypen, -Operatoren und -Funktionen, die mit Azure Resource Graph verwendet werden können.
ms.date: 09/03/2021
ms.topic: conceptual
ms.openlocfilehash: 8d537d0816ef4d32d6ce85cf363e08bece28ec84
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536083"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Grundlegendes zur Azure Resource Graph-Abfragesprache

Die Abfragesprache für Azure Resource Graph unterstützt eine Reihe von Operatoren und Funktionen. Diese arbeiten und funktionieren auf Grundlage der [Abfragesprache Kusto (Kusto Query Language, KQL)](/azure/data-explorer/kusto/query/index). Wenn Sie mehr über die von Resource Graph verwendete Abfragesprache erfahren möchten, beginnen Sie mit dem [Tutorial für KQL](/azure/data-explorer/kusto/query/tutorial).

In diesem Artikel werden die von Resource Graph unterstützten Sprachkomponenten behandelt:

- [Resource Graph-Tabellen](#resource-graph-tables)
- [Benutzerdefinierte Sprachelemente in Resource Graph](#resource-graph-custom-language-elements)
- [Unterstützte KQL-Sprachelemente](#supported-kql-language-elements)
- [Abfragebereich](#query-scope)
- [Escapezeichen](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph-Tabellen

Resource Graph umfasst mehrere Tabellen für die Daten, die in Bezug auf Azure Resource Manager-Ressourcentypen und deren Eigenschaften gespeichert werden. Einige Tabellen können mit den Operatoren `join` oder `union` verwendet werden, um Eigenschaften von verknüpften Ressourcentypen zu erhalten. Es folgt eine Liste der in Resource Graph verfügbaren Tabellen:

|Resource Graph-Tabelle |Ist ein `join` für andere Tabellen möglich? |BESCHREIBUNG |
|---|---|---|
|Ressourcen |Ja |Die Standardtabelle, wenn keine in der Abfrage definiert ist. Die meisten Resource Manager-Ressourcentypen und -Eigenschaften sind hier enthalten. |
|ResourceContainers |Ja |Umfasst die Ressourcentypen und Daten der Verwaltungsgruppe (`Microsoft.Management/managementGroups`), des Abonnements (`Microsoft.Resources/subscriptions`) und der Ressourcengruppe (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AdvisorResources |Ja (Vorschau) |Umfasst Ressourcen, die mit `Microsoft.Advisor`_in Zusammenhang stehen_. |
|AlertsManagementResources |Ja (Vorschau) |Umfasst Ressourcen, die mit `Microsoft.AlertsManagement`_in Zusammenhang stehen_. |
|DesktopVirtualizationResources |Ja |Umfasst Ressourcen, die mit `Microsoft.DesktopVirtualization`_in Zusammenhang stehen_. |
|ExtendedLocationResources |Nein |Umfasst Ressourcen, die mit `Microsoft.ExtendedLocation`_in Zusammenhang stehen_. |
|GuestConfigurationResources |Nein |Umfasst Ressourcen, die mit `Microsoft.GuestConfiguration`_in Zusammenhang stehen_. |
|HealthResources|Ja |Umfasst Ressourcen, die mit `Microsoft.ResourceHealth/availabilitystatuses`_in Zusammenhang stehen_. |
|IoTSecurityResources |Nein |Umfasst Ressourcen, die mit `Microsoft.IoTSecurity`_in Zusammenhang stehen_. |
|KubernetesConfigurationResources |Nein |Umfasst Ressourcen, die mit `Microsoft.KubernetesConfiguration`_in Zusammenhang stehen_. |
|MaintenanceResources |Teilweise, nur Beitritt _zu_. (Vorschauversion) |Umfasst Ressourcen, die mit `Microsoft.Maintenance`_in Zusammenhang stehen_. |
|PatchAssessmentResources|No |Umfasst Ressourcen _im Zusammenhang mit_ der Azure Virtual Machines-Patchbewertung. |
|PatchInstallationResources|Nein |Umfasst Ressourcen _im Zusammenhang mit_ der Azure Virtual Machines-Patchinstallation. |
|PolicyResources |Ja |Umfasst Ressourcen, die mit `Microsoft.PolicyInsights`_in Zusammenhang stehen_. |
|RecoveryServicesResources |Teilweise, nur Beitritt _zu_. (Vorschauversion) |Umfasst Ressourcen _im Zusammenhang mit_ `Microsoft.DataProtection` und `Microsoft.RecoveryServices`. |
|SecurityResources |Ja (Vorschau) |Umfasst Ressourcen, die mit `Microsoft.Security`_in Zusammenhang stehen_. |
|ServiceHealthResources |Keine (Vorschau) |Umfasst Ressourcen, die mit `Microsoft.ResourceHealth/events`_in Zusammenhang stehen_. |
|WorkloadMonitorResources |Nein |Umfasst Ressourcen, die mit `Microsoft.WorkloadMonitor`_in Zusammenhang stehen_. |

Eine komplette Liste, einschließlich Ressourcentypen, finden Sie unter [ Azure Resource Graph-Tabelle und Ressourcentypreferenz](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resources_ ist die Standardtabelle. Beim Abfragen der Tabelle _Resources_ muss der Tabellenname nicht angegeben werden, es sei denn, `join` oder `union` wird verwendet. Es wird jedoch empfohlen, immer die anfängliche Tabelle in die Abfrage einzubeziehen.

Verwenden Sie den Resource Graph-Explorer im Portal, um zu ermitteln, welche Ressourcentypen in den einzelnen Tabellen verfügbar sind. Sie können auch eine Abfrage verwenden (z.B. `<tableName> | distinct type`), um eine Liste von Ressourcentypen zu erhalten, die von der angegebenen Resource Graph-Tabelle unterstützt werden und in Ihrer Umgebung vorhanden sind.

Die folgende Abfrage zeigt eine einfache Verwendung von `join`. Im Abfrageergebnis sind die Spalten kombiniert, und an alle doppelten Spaltennamen aus der verknüpften Tabelle (_ResourceContainers_ in diesem Beispiel) wird **1** angehängt. Da die Tabelle _ResourceContainers_ Typen für Abonnements und Ressourcengruppen enthält, können beide Typen zum Verknüpfen der Ressource aus der Tabelle _Resources_ verwendet werden.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Die folgende Abfrage zeigt eine komplexere Verwendung von `join`. Zuerst verwendet die Abfrage `project`, um die Felder aus _Resources_ für den Ressourcentyp „Azure Key Vault-Tresore“ abzurufen. Im nächsten Schritt werden die Ergebnisse mithilfe von `join` mit _ResourceContainers_ zusammengeführt, wobei es sich bei dem Typ um ein Abonnement _für_ eine Eigenschaft handelt, die sich sowohl im `project` der ersten Tabelle als auch im `project` der verbundenen Tabelle befindet. Durch das Umbenennen des Felds wird das Hinzufügen als _name1_ bei `join` vermieden, da die Eigenschaft bereits unter _Resources_ projiziert ist. Das Abfrageergebnis ist ein einzelner Schlüsseltresor, der den Typ, den Namen, den Speicherort und die Ressourcengruppe des Schlüsseltresors sowie den Namen des Abonnements anzeigt, in dem er sich befindet.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| project name, type, location, subscriptionId, resourceGroup
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, location, resourceGroup, SubName
| limit 1
```

> [!NOTE]
> Wenn Sie die Ergebnisse von `join` mit `project` einschränken, muss die Eigenschaft, die von `join` zum Verknüpfen der beiden Tabellen verwendet wird (_subscriptionId_ im obigen Beispiel), in `project` enthalten sein.

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Erweiterte Eigenschaften (Vorschau)

Als _Previewfunktion_ verfügen einige der Ressourcentypen in Resource Graph über zusätzliche typbezogene Eigenschaften, die über die von Azure Resource Manager bereitgestellten Eigenschaften hinaus abgefragt werden können. Dieser Satz von Werten, bekannt als _erweiterte Eigenschaften_, existiert auf einem unterstützten Ressourcentyp in `properties.extended`. Verwenden Sie die folgende Abfrage, um zu ermitteln, welche Ressourcentypen über _erweiterte Eigenschaften_ verfügen:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Beispiel: Abrufen der Anzahl virtueller Computer über `instanceView.powerState.code`:

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Benutzerdefinierte Sprachelemente in Resource Graph

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Syntax für freigegebene Abfragen (Vorschauversion)

Als Previewfunktion kann der Zugriff auf eine [freigegebene Abfrage](../tutorials/create-share-query.md) direkt in einer Resource Graph-Abfrage erfolgen. Dieses Szenario ermöglicht es, Standardabfragen als freigegebene Abfragen zu erstellen und wiederzuverwenden. Verwenden Sie die Syntax `{{shared-query-uri}}`, um eine freigegebene Abfrage innerhalb einer Resource Graph-Abfrage aufzurufen. Der URI der freigegebenen Abfrage entspricht der _Ressourcen-ID_ der freigegebenen Abfrage auf der Seite **Einstellungen** für diese Abfrage. In diesem Beispiel ist `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` der URI für die freigegebene Abfrage.
Dieser URI verweist auf das Abonnement, die Ressourcengruppe und den vollständigen Namen der freigegebenen Abfrage, auf die in einer anderen Abfrage verwiesen werden soll. Diese Abfrage ist identisch mit der Abfrage, die unter [Tutorial: Erstellen und Freigeben einer Abfrage](../tutorials/create-share-query.md) erstellt wird.

> [!NOTE]
> Eine Abfrage, die auf eine freigegebene Abfrage verweist, kann nicht als freigegebene Abfrage gespeichert werden.

Beispiel 1: Alleiniges Verwenden der freigegebenen Abfrage

Die Ergebnisse dieser Resource Graph-Abfrage sind identisch mit denen der in der freigegebenen Abfrage gespeicherten.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Beispiel 2: Einfügen der freigegebenen Abfrage als Teil einer größeren Abfrage

Bei dieser Abfrage wird zunächst die freigegebene Abfrage und dann `limit` verwendet, um die Ergebnisse weiter einzugrenzen.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Unterstützte KQL-Sprachelemente

Resource Graph unterstützt eine Teilmenge von [Datentypen](/azure/data-explorer/kusto/query/scalar-data-types/), [Skalarfunktionen](/azure/data-explorer/kusto/query/scalarfunctions), [Skalaroperatoren](/azure/data-explorer/kusto/query/binoperators) und [Aggregationsfunktionen](/azure/data-explorer/kusto/query/any-aggfunction) von KQL. Es werden bestimmte [tabellarische Operatoren](/azure/data-explorer/kusto/query/queries) von Resource Graph unterstützt, von denen einige unterschiedliche Verhaltensweisen aufweisen.

### <a name="supported-tabulartop-level-operators"></a>Unterstützte tabellarische Operatoren und Operatoren auf oberster Ebene

Es folgt eine Liste der von Resource Graph unterstützten tabellarischen KQL-Operatoren mit spezifischen Beispielen:

|KQL |Resource Graph-Beispielabfrage |Notizen |
|---|---|---|
|[count](/azure/data-explorer/kusto/query/countoperator) |[Anzahl von Schlüsseltresoren](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/data-explorer/kusto/query/distinctoperator) |[Anzeigen von Ressourcen, die Speicher enthalten](../samples/starter.md#show-storage) | |
|[extend](/azure/data-explorer/kusto/query/extendoperator) |[Anzahl von virtuellen Computern nach Betriebssystemtyp](../samples/starter.md#count-os) | |
|[join](/azure/data-explorer/kusto/query/joinoperator) |[Schlüsseltresor mit Abonnementname](../samples/advanced.md#join) |Unterstützte Joinvarianten: [innerunique](/azure/data-explorer/kusto/query/joinoperator#default-join-flavor), [inner](/azure/data-explorer/kusto/query/joinoperator#inner-join), [leftouter](/azure/data-explorer/kusto/query/joinoperator#left-outer-join). Begrenzt auf 3 `join` in einer einzelnen Abfrage, von denen einer ein tabellenübergreifender `join` sein kann. Wenn alle tabellenübergreifenden `join`-Vorgänge zwischen _Resource_ und _ResourceContainers_ stattfinden, sind drei tabellenübergreifende `join` zulässig. Benutzerdefinierte Joinstrategien wie Broadcastjoin sind nicht zulässig. Welche Tabellen `join` verwenden können, erfahren Sie unter [Resource Graph-Tabellen](#resource-graph-tables). |
|[limit](/azure/data-explorer/kusto/query/limitoperator) |[Liste der öffentlichen IP-Adressen](../samples/starter.md#list-publicip) |Synonym für `take`. Funktioniert nicht für [Skip](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/data-explorer/kusto/query/mvexpandoperator) | | Legacy-Operator, verwenden Sie stattdessen `mv-expand`. _RowLimit_ maximal 400. Der Standardwert ist 128. |
|[mv-expand](/azure/data-explorer/kusto/query/mvexpandoperator) |[Auflisten von Cosmos DB mit bestimmten Schreibstandorten](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ maximal 400. Der Standardwert ist 128. Maximal zwei `mv-expand`-Elemente in einer einzelnen Abfrage.|
|[order](/azure/data-explorer/kusto/query/orderoperator) |[Auflisten von Ressourcen nach Namen sortiert](../samples/starter.md#list-resources) |Synonym für `sort` |
|[parse](/azure/data-explorer/kusto/query/parseoperator) |[Abrufen von virtuellen Netzwerken und Subnetzen von Netzwerkschnittstellen](../samples/advanced.md#parse-subnets) |Es ist optimal, direkt auf Eigenschaften zuzugreifen, wenn sie vorhanden sind, anstatt `parse` zu verwenden. |
|[project](/azure/data-explorer/kusto/query/projectoperator) |[Auflisten von Ressourcen nach Namen sortiert](../samples/starter.md#list-resources) | |
|[project-away](/azure/data-explorer/kusto/query/projectawayoperator) |[Entfernen von Spalten aus den Ergebnissen](../samples/advanced.md#remove-column) | |
|[sort](/azure/data-explorer/kusto/query/sortoperator) |[Auflisten von Ressourcen nach Namen sortiert](../samples/starter.md#list-resources) |Synonym für `order` |
|[summarize](/azure/data-explorer/kusto/query/summarizeoperator) |[Anzahl der Azure-Ressourcen](../samples/starter.md#count-resources) |Nur vereinfachte erste Seite |
|[take](/azure/data-explorer/kusto/query/takeoperator) |[Liste der öffentlichen IP-Adressen](../samples/starter.md#list-publicip) |Synonym für `limit`. Funktioniert nicht für [Skip](./work-with-data.md#skipping-records). |
|[top](/azure/data-explorer/kusto/query/topoperator) |[Anzeigen der ersten fünf virtuellen Computer nach Name und BS-Typ](../samples/starter.md#show-sorted) | |
|[union](/azure/data-explorer/kusto/query/unionoperator) |[Vereinen von Ergebnissen aus zwei Abfragen in einem einzigen Ergebnis](../samples/advanced.md#unionresults) |Einzelne Tabelle zulässig: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. Maximal drei `union`-Verzweigungen in einer einzelnen Abfrage. Fuzzyauflösung von `union`-Verzweigungstabellen ist nicht zulässig. Kann innerhalb einer einzelnen Tabelle oder zwischen den Tabellen _Resources_ und _ResourceContainers_ verwendet werden. |
|[where](/azure/data-explorer/kusto/query/whereoperator) |[Anzeigen von Ressourcen, die Speicher enthalten](../samples/starter.md#show-storage) | |

Es gibt eine Standardbegrenzung auf 3 `join` und 3 `mv-expand`-Operatoren in einer einzelnen Resource Graph-SDK-Abfrage. Sie können eine Erhöhung dieser Grenzwerte für Ihren Mandanten über **Hilfe und Support** anfordern.

Zur Unterstützung der Portalfunktion „Abfrage öffnen“ weist der Azure Resource Graph-Explorer einen höheren globalen Grenzwert als das Resource Graph-SDK auf.

## <a name="query-scope"></a>Abfragebereich

Der Bereich des Abonnements oder der [Verwaltungsgruppen](../../management-groups/overview.md), aus dem Ressourcen von einer Abfrage zurückgegeben werden, ist standardmäßig eine Liste mit Abonnements, basierend auf dem Kontext des autorisierten Benutzers. Wenn weder eine Verwaltungsgruppe noch eine Abonnementliste definiert ist, umfasst der Abfragebereich alle Ressourcen, einschließlich delegierter [Azure Lighthouse](../../../lighthouse/overview.md)-Ressourcen.

Die Liste der abzufragenden Abonnements oder Verwaltungsgruppen kann manuell definiert werden, um den Bereich der Ergebnisse zu ändern. Die REST-API-Eigenschaft `managementGroups` übernimmt beispielsweise die Verwaltungsgruppen-ID, die sich von dem Namen der Verwaltungsgruppe unterscheidet. Wenn `managementGroups` angegeben ist, werden Ressourcen aus den ersten 5.000 Abonnements in oder unter der angegebenen Verwaltungsgruppenhierarchie eingeschlossen. `managementGroups` kann nicht gleichzeitig mit `subscriptions` verwendet werden.

Beispiel: Abfragen aller Ressourcen in der Hierarchie der Verwaltungsgruppe mit Namen „My Management Group“ mit der ID „myMG“

- REST-API-URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2021-03-01
  ```

- Anforderungstext

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroups": ["myMG"]
  }
  ```

## <a name="escape-characters"></a>Escape-Zeichen

Einige Eigenschaftennamen, etwa diejenigen, die einen `.` oder ein `$` enthalten, müssen in einer Abfrage umhüllt oder mit Escapezeichen versehen sein. Andernfalls wird der jeweilige Eigenschaftenname falsch interpretiert, sodass nicht die erwarteten Ergebnisse bereitgestellt werden.

- `.` – Umhüllen Sie den Namen der jeweiligen Eigenschaft: `['propertyname.withaperiod']`

  Beispielabfrage, in der die Eigenschaft _odata.type_ umhüllt ist:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` – Versehen Sie den Namen der jeweiligen Eigenschaft mit einem Escapezeichen. Welches Escapezeichen verwendet wird, hängt von der Shell, in der Ressource Graph ausgeführt wird.

  - **Bash** - `\`

    Beispielabfrage, in der die _\$type_-Eigenschaft in Bash mit einem Escapezeichen versehen ist:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – Versehen Sie das `$`-Zeichen nicht mit einem Escapezeichen.

  - **PowerShell** - ``` ` ```

    Beispielabfrage, in der die Eigenschaft _\$type_ in PowerShell mit einem Escapezeichen versehen ist:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur verwendeten Sprache finden Sie unter [Einfache Abfragen](../samples/starter.md).
- Informationen zur anspruchsvolleren Nutzung finden Sie unter [Erweiterte Abfragen](../samples/advanced.md).
- Erfahren Sie mehr über das [Erkunden von Ressourcen](explore-resources.md).
