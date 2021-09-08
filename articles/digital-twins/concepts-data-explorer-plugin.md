---
title: Abfragen mit Azure Data Explorer
titleSuffix: Azure Digital Twins
description: Grundlegendes zum Azure Digital Twins-Abfrage-Plug-In für Azure Data Explorer
author: baanders
ms.author: baanders
ms.date: 5/19/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8b0c6558be0022d8cb72ede5b665023f2b3f138d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438708"
---
# <a name="azure-digital-twins-query-plugin-for-azure-data-explorer"></a>Azure Digital Twins-Abfrage-Plug-In für Azure Data Explorer

Mit dem Azure Digital Twins-Plug-In für [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) können Sie Azure Data Explorer-Abfragen ausführen, die auf Daten in Azure Digital Twins-Graph- und Azure Data Explorer-Zeitreihendatenbanken zugreifen und diese kombinieren. Verwenden Sie das Plug-In, um unterschiedliche Zeitreihendaten zu kontextualisieren, indem Sie über digitale Zwillinge und deren Beziehungen hinweg schlussfolgern, um Erkenntnisse über das Verhalten modellierter Umgebungen zu erhalten.

Mit diesem Plug-In können Sie z. B. eine Kusto-Abfrage schreiben, die:
1. für Sie interessante digitale Zwillinge über das Azure Digital Twins-Abfrage-Plug-In auswählt,
2. diese Zwillinge mit der jeweiligen Zeitreihe in Azure Data Explorer verknüpft und dann 
3. erweiterte Zeitreihenanalysen mit diesen Zwillingen ausführt.  

Das Kombinieren von Daten aus einem Zwillingsgraphen in Azure Digital Twins mit Zeitreihendaten in Azure Data Explorer kann Ihnen helfen, das Betriebsverhalten verschiedener Teile Ihrer Lösung zu verstehen. 

## <a name="using-the-plugin"></a>Verwenden des Plug-Ins

Sie können das Plug-In mit dem folgenden Befehl in einer Kusto-Abfrage aufrufen. Es gibt zwei Platzhalter, `<Azure-Digital-Twins-endpoint>` und `<Azure-Digital-Twins-query>`, bei denen es sich um Zeichenfolgen handelt, die den Azure Digital Twins-Instanzendpunkt bzw. die Azure Digital Twins-Abfrage darstellen. 

```kusto
evaluate azure_digital_twins_query_request(<Azure-Digital-Twins-endpoint>, <Azure-Digital-Twins-query>) 
```

Das Plug-In funktioniert so, dass es die [Azure Digital Twins-Abfrage-API](/rest/api/digital-twins/dataplane/query) aufruft, wobei die [Struktur der Abfragesprache](concepts-query-language.md) mit der bei Verwendung der API bis auf zwei Ausnahmen identisch ist: 
* Der Platzhalter `*` wird in der `SELECT`-Klausel nicht unterstützt. Stattdessen sollten bei Azure Digital Twin-Abfragen, die mit dem Plug-In ausgeführt werden, in der `SELECT`-Klausel Aliase verwendet werden.

    Sehen Sie sich beispielsweise die folgende Azure Digital Twins-Abfrage an, die mithilfe der API ausgeführt wird:
    
    ```SQL
    SELECT * FROM DIGITALTWINS
    ```
    
    Um diese Abfrage bei Verwendung des Plug-Ins auszuführen, sollte sie wie folgt umgeschrieben werden:
    
    ```SQL
    SELECT T FROM DIGITALTWINS T
    ```
* Spaltennamen, die vom Plug-In zurückgegeben werden, beginnen möglicherweise nicht mit `$`. Die Verwendung von Aliasen in der `SELECT`-Klausel hilft auch dabei, dieses Szenario zu vermeiden.

    Sehen Sie sich beispielsweise die folgende Azure Digital Twins-Abfrage an, die mithilfe der API ausgeführt wird:
    
    ```SQL
    SELECT T.$dtId, T.Temperature FROM DIGITALTWINS T
    ```
    
    Um diese Abfrage bei Verwendung des Plug-Ins auszuführen, sollte sie wie folgt umgeschrieben werden:
    
    ```SQL
    SELECT T.$dtId as tid, T.Temperature FROM DIGITALTWINS T
    ```


>[!IMPORTANT]
>Dem Benutzer des Plug-Ins muss die Rolle **Azure Digital Twins-Datenleser** oder **Azure Digital Twins-Datenbesitzer** gewährt werden, da das Azure AD-Token des Benutzers für die Authentifizierung verwendet wird. Informationen zum Zuweisen dieser Rolle finden Sie unter [Sicherheit für Azure Digital Twins-Lösungen](concepts-security.md#authorization-azure-roles-for-azure-digital-twins).

Weitere Informationen zur Verwendung des Plug-Ins finden Sie in der [Kusto-Dokumentation für das azure_digital_twins_query_request-Plug-In](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin).

Beispielabfragen und eine exemplarische Vorgehensweise mit Beispieldaten finden Sie unter [Azure Digital Twins query plugin for Azure Data Explorer: Sample queries and walkthrough](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries) (Azure Digital Twins-Abfrage-Plug-In für Azure Data Explorer: Beispielabfragen und exemplarische Vorgehensweise) auf GitHub.

## <a name="using-azure-data-explorer-iot-data-with-azure-digital-twins"></a>Verwenden von Azure Data Explorer-IoT-Daten mit Azure Digital Twins

Es gibt verschiedene Möglichkeiten zum Erfassen von IoT-Daten in Azure Data Explorer. Im Folgenden finden Sie zwei, die Sie nutzen können, wenn Sie Azure Data Explorer mit Azure Digital Twins verwenden:
* Historisieren von Eigenschaftswerten für digitale Zwillinge in Azure Data Explorer mit einer Azure-Funktion, die Zwillingsänderungsereignisse verarbeitet und die Zwillingsdaten in Azure Data Explorer schreibt, ähnlich wie in dem in [Integrieren in Azure Time Series Insights](how-to-integrate-time-series-insights.md) verwendeten Prozess. Dieser Pfad eignet sich für Kunden, die Telemetriedaten verwenden, um ihre digitalen Zwillinge zum Leben zu erwecken.
* [Erfassen von IoT-Daten direkt in Ihrem Azure Data Explorer-Cluster aus IoT Hub](/azure/data-explorer/ingest-data-iot-hub) oder aus anderen Quellen. Anschließend wird der Azure Digital Twins-Graph verwendet, um die Zeitreihendaten mithilfe verknüpfter Azure Digital Twins/Azure Data Explorer-Abfragen zu kontextualisieren. Dieser Pfad eignet sich möglicherweise für Workloads mit direkter Erfassung. 

### <a name="mapping-data-across-azure-data-explorer-and-azure-digital-twins"></a>Zuordnen von Daten zwischen Azure Data Explorer und Azure Digital Twins

Wenn Sie Zeitreihendaten direkt in Azure Data Explorer erfassen, müssen Sie diese unformatierten Zeitreihendaten wahrscheinlich in ein Schema konvertieren, das für verknüpfte Azure Digital Twins/Azure Data Explorer-Abfragen geeignet ist.

Mit einer [Updaterichtlinie](/azure/data-explorer/kusto/management/updatepolicy) in Azure Data Explorer können Sie Daten automatisch transformieren und an eine Zieltabelle anfügen, wenn neue Daten in eine Quelltabelle eingefügt werden. 

Sie können eine Updaterichtlinie verwenden, um Ihre rohen Zeitreihendaten mit der entsprechenden **Zwillings-ID** aus Azure Digital Twins anzureichern und in einer Zieltabelle dauerhaft zu speichern. Unter Verwendung der Zwillings-ID kann die Zieltabelle dann mit den vom Azure Digital Twins-Plug-In ausgewählten digitalen Zwillingen verknüpft werden. 

Angenommen, Sie haben beispielsweise die folgende Tabelle erstellt, um die unformatierten Zeitreihendaten aufzunehmen, die in Ihrer Azure Data Explorer-Instanz eingehen. 

```kusto
.create-merge table rawData (Timestamp:datetime, someId:string, Value:string, ValueType:string)  
```

Sie könnten eine Zuordnungstabelle erstellen, um Zeitreihen-IDs mit Zwillings-IDs und anderen optionalen Feldern in Beziehung zu setzen. 

```kusto
.create-merge table mappingTable (someId:string, twinId:string, otherMetadata:string) 
```

Dann erstellen Sie eine Zieltabelle, um die angereicherten Zeitreihendaten aufzunehmen. 

```kusto
.create-merge table timeseriesSilver (twinId:string, Timestamp:datetime, someId:string, otherMetadata:string, ValueNumeric:real, ValueString:string)  
```

Als Nächstes erstellen Sie eine Funktion `Update_rawData`, um die Rohdaten anzureichern, indem Sie sie mit der Zuordnungstabelle verknüpfen. Dadurch wird die Zwillings-ID der resultierenden Zieltabelle hinzugefügt. 

```kusto
.create-or-alter function with (folder = "Update", skipvalidation = "true") Update_rawData() { 
rawData 
| join kind=leftouter mappingTable on someId 
| project 
    Timestamp, ValueNumeric = toreal(Value), ValueString = Value, ... 
} 
```

Als Letztes erstellen Sie eine Updaterichtlinie zum Aufrufen der Funktion sowie zum Aktualisieren der Zieltabelle. 

```kusto
.alter table timeseriesSilver policy update 
@'[{"IsEnabled": true, "Source": "rawData", "Query": "Update_rawData()", "IsTransactional": false, "PropagateIngestionProperties": false}]' 
```

Nachdem die Zieltabelle erstellt wurde, können Sie das Azure Digital Twins-Plug-In verwenden, um die für Sie interessanten Zwillinge auszuwählen und sie dann mit Zeitreihendaten in der Zieltabelle zu verknüpfen. 

### <a name="example-schema"></a>Beispielschema

Im Folgenden finden Sie ein Beispiel für ein Schema, das verwendet werden kann, um gemeinsam genutzte Daten darzustellen.

| timestamp | twinId | modelId | name | value | relationshipTarget | relationshipID |
| --- | --- | --- | --- | --- | --- | --- |
| 2021-02-01 17:24 | ConfRoomTempSensor | dtmi:com:example:TemperatureSensor;1 | Temperatur | 301,0 |  |  |

Eigenschaften digitaler Zwillinge werden als Schlüssel-Wert-Paare (`name, value`) gespeichert. `name` und `value` werden als dynamische Datentypen gespeichert. 

Das Schema unterstützt auch das Speichern von Eigenschaften für Beziehungen in den Feldern `relationshipTarget` und `relationshipID`. Das Schlüssel-Wert-Schema vermeidet, für jede Zwillingseigenschaft eine Spalte erstellen zu müssen.

### <a name="representing-properties-with-multiple-fields"></a>Darstellen von Eigenschaften mit mehreren Feldern 

Möglicherweise möchten Sie eine Eigenschaft in Ihrem Schema mit mehreren Feldern speichern. Diese Eigenschaften werden dargestellt, indem ein JSON-Objekt als `value` in Ihrem Schema gespeichert wird.

Wenn Sie beispielsweise eine Eigenschaft mit drei Feldern für „roll“ (rollen), „pitch“ (nicken) und „yaw“ (gieren) darstellen möchten, sähe das Wertobjekt wie folgt aus: `{"roll": 20, "pitch": 15, "yaw": 45}`.

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die Plug-In-Dokumentation für die Kusto-Abfragesprache in Azure Data Explorer: [azure_digital_twins_query_request-Plug-In](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin).

* Sehen Sie sich Beispielabfragen an, die das Plug-In verwenden, einschließlich einer exemplarischen Vorgehensweise zum Ausführen der Abfragen in einem Beispielszenario: [Azure Digital Twins-Abfrage-Plug-In für Azure Data Explorer: Beispielabfragen und exemplarische Vorgehensweise](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries). 

* Lesen Sie etwas zu einer anderen Strategie für die Analyse von Verlaufsdaten in Azure Digital Twins: [Integrieren in Azure Time Series Insights](how-to-integrate-time-series-insights.md).
