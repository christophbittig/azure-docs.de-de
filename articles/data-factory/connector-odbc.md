---
title: Kopieren von Daten aus und in ODBC-Datenspeicher
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Daten von und zu ODBC-Datenspeichern mit Hilfe einer Kopieraktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline kopieren können.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 7eccf28b0d8c5791fc8f23f6453f1b139291c890
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787829"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory-or-synapse-analytics"></a>Kopieren von Daten aus und in ODBC-Datenspeicher mit Azure Data Factory oder Synapse Analytics
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-odbc-connector.md)
> * [Aktuelle Version](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einem bzw. in einen ODBC-Datenspeicher zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der ODBC-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer ODBC-Quelle in jeden unterstützten Senkendatenspeicher oder Daten aus jedem unterstützten Quelldatenspeicher in eine ODBC-Senke kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser ODBC-Connector unterstützt insbesondere das Kopieren von Daten aus **ODBC-kompatiblen Datenspeichern** bzw. in diese mithilfe der **Standard**- oder **anonymen** Authentifizierung. Ein **64-Bit-ODBC-Treiber** ist erforderlich. Für ODBC-Senken unterstützt der Dienst den ODBC-Standard Version 2.0.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zum Verwenden dieses ODBC-Connectors diese Schritte aus:

- Richten Sie eine selbstgehostete Integration Runtime ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.
- Installieren Sie den 64-Bit-ODBC-Treiber für den Datenspeicher auf dem Integration Runtime-Computer.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-odbc-data-store-using-ui"></a>Erstellen eines verknüpften Dienstes mit einem ODBC-Datenspeicher über die Benutzeroberfläche

Verwenden Sie die folgenden Schritte, um einen verknüpften Dienst mit einem ODBC-Datenspeicher in der Benutzeroberfläche des Azure-Portals zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Synapse Benutzeroberfläche zeigt.":::

2. Suchen Sie nach ODBC und wählen Sie den ODBC-Connector aus.

    :::image type="content" source="media/connector-odbc/odbc-connector.png" alt-text="Screenshot vom ODBC-Connectors.":::    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-odbc/configure-odbc-linked-service.png" alt-text="Screenshot von der Konfiguration des verknüpften Diensts für einen ODBC Datenspeicher.":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den ODBC-Connector verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit ODBC verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Odbc** | Ja |
| connectionString | Die Verbindungszeichenfolge, ausgenommen des Teils mit den Anmeldeinformationen. Sie können die Verbindungszeichenfolge mit einem Muster wie `Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;` angeben oder den System-DSN (Data Source Name) verwenden, den Sie mit `DSN=<name of the DSN on IR machine>;` auf dem Computer mit der Integrationslaufzeit eingerichtet haben. (Sie müssen nach wie vor den Teil mit den Anmeldeinformationen im verknüpften Dienst entsprechend angeben.)<br>Sie können auch ein Kennwort in Azure Key Vault speichern und die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md).| Ja |
| authenticationType | Typ der Authentifizierung für die Verbindung mit dem ODBC-Datenspeicher.<br/>Zulässige Werte sind: **Standard** und **Anonym**. | Ja |
| userName | Geben Sie den Benutzernamen an, wenn Sie die Standardauthentifizierung (Basic) verwenden. | Nein |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für „userName“ angegeben haben. Markieren Sie dieses Feld als einen „SecureString“, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| Anmeldeinformationen (credential) | Der zum Zugriff bestimmte Teil der Anmeldeinformationen in der Verbindungszeichenfolge. Er wird in einem treiberspezifischen Format in Eigenschaft und Wert angegeben. Beispiel: `"RefreshToken=<secret refresh token>;"`. Legen Sie für dieses Feld „SecureString“ fest. | Nein |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |Ja |

**Beispiel 1: Verwenden der Standardauthentifizierung**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 2: Verwenden der anonymen Authentifizierung**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom ODBC-Dataset unterstützt werden.

Zum Kopieren von Daten aus einem bzw. in einen ODBC-kompatiblen Datenspeicher werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **OdbcTable** | Ja |
| tableName | Der Name der Tabelle im ODBC-Datenspeicher. | Nein bei Quellen (wenn „query“ in der Aktivitätsquelle angegeben ist);<br/>ja bei Senken |

**Beispiel**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Wenn Sie das Datenset vom Typ `RelationalTable` verwenden, wird es weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der ODBC-Quelle unterstützt werden.

### <a name="odbc-as-source"></a>ODBC als Quelle

Zum Kopieren von Daten aus einem ODBC-kompatiblen Datenspeicher werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **OdbcSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Wenn Sie eine Quelle vom Typ `RelationalSource` verwenden, wird sie weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

### <a name="odbc-as-sink"></a>ODBC als Senke

Legen Sie zum Kopieren von Daten in ODBC-kompatible Datenspeicher den Senkentyp in der Kopieraktivität auf **OdbcSink** fest. Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf Folgendes festgelegt sein: **OdbcSink** | Ja |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird.<br/>Zulässige Werte: Zeitraum Beispiel: „00:30:00“ (30 Minuten). |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht.<br/>Zulässige Werte: Ganze Zahlen (Anzahl der Zeilen). |Nein (Standard ist 0 – automatisch erkannt) |
| preCopyScript |Geben Sie eine auszuführende SQL-Abfrage für die Kopieraktivität an, ehe Sie bei der jeder Ausführung Daten in Datenspeicher schreiben. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. |Nein |

> [!NOTE]
> Wenn „writeBatchSize“ nicht festgelegt (automatisch erkannt) wird, erkennt die Kopieraktivität zuerst, ob der Treiber Batchvorgänge unterstützt. Falls ja, wird die Anzahl der Batchvorgänge auf „10.000“ festgelegt, falls nicht auf „1“. Wenn Sie explizit einen Wert ungleich „0“ festlegen, berücksichtigt die Kopieraktivität den Wert. Zur Laufzeit tritt ein Fehler auf, wenn der Treiber Batchvorgänge nicht unterstützt.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="troubleshoot-connectivity-issues"></a>Behandeln von Konnektivitätsproblemen

Um Verbindungsprobleme zu behandeln, verwenden Sie die Registerkarte **Diagnose** im **Konfigurations-Manager für die Integrationslaufzeit**.

1. Starten Sie den **Konfigurations-Manager für die Integrationslaufzeit**.
2. Wechseln Sie zur Registerkarte **Diagnose** .
3. Wählen Sie im Abschnitt „Verbindung testen“ den **Typ** des Datenspeichers (verknüpfter Dienst).
4. Geben Sie die **Verbindungszeichenfolge** an, die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet wird, wählen Sie die **Authentifizierung** aus, und geben Sie **Benutzername**, **Kennwort** und/oder **Anmeldeinformationen** ein.
5. Klicken Sie auf **Verbindung testen** , um die Verbindung mit dem Datenspeicher zu testen.

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quelles und Senken für die Kopieraktivität unterstützt werden, finden Sie in der Dokumentation für [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
