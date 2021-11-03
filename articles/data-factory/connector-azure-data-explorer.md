---
title: Kopieren und Transformieren von Daten in Azure Data Explorer
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Daten in Azure Data Explorer mithilfe von Data Factory oder Azure Synapse Analytics kopieren und transformieren.
ms.author: orspodek
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/14/2021
ms.openlocfilehash: 8aafbbd0cc7063fdccf3ae44e9442fa86cfe5f94
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005763"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory-or-synapse-analytics"></a>Kopieren von Daten in oder aus Azure Data Explorer mithilfe von Azure Data Factory oder Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Copy-Aktivität in Azure Data Factory- und Synapse Analytics-Pipelines verwenden, um Daten in oder aus [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!TIP]
>Weitere allgemeine Informationen zur Integration von Azure Data Explorer mit dem Dienst finden Sie unter [Integrieren von Azure Data Explorer](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Data Explorer-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus jedem unterstützten Quelldatenspeicher in Azure Data Explorer kopieren. Zudem können Sie Daten aus Azure Data Explorer in jeden unterstützten Senkendatenspeicher kopieren. Die für die Kopieraktivität als Quellen oder Senken unterstützten Datenspeicher finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

>[!NOTE]
>Das Kopieren von Daten in oder aus Azure Data Explorer über einen lokalen Datenspeicher mithilfe der selbstgehosteten Integration Runtime wird in Version 3.14 und höher unterstützt.

Mit dem Azure Data Explorer-Connector ist Folgendes möglich:

* Kopieren von Daten mithilfe der Azure Active Directory-Anwendungstokenauthentifizierung (Azure AD) mit einem **Dienstprinzipal**
* Als Quelle: Abrufen von Daten mithilfe einer KQL-Abfrage (Kusto)
* Als Senke: Anfügen von Daten an eine Zieltabelle

## <a name="getting-started"></a>Erste Schritte

>[!TIP]
>Eine exemplarische Vorgehensweise zur Verwendung des Azure Data Explorer-Connectors finden Sie unter [Kopieren von Daten in/aus Azure Data Explorer](/azure/data-explorer/data-factory-load-data) und [Massenkopieren aus einer Datenbank zu Azure Data Explorer](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-data-explorer-using-ui"></a>Erstellen eines verknüpften Diensts für Azure Data Explorer über die Benutzeroberfläche

Führen Sie die folgenden Schritte aus, um einen verknüpften Dienst für Azure Data Explorer auf der Benutzeroberfläche im Azure-Portal zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Synapse Benutzeroberfläche zeigt.":::

2. Suchen Sie nach Explorer, und wählen Sie den Connector für Azure Data Explorer (Kusto) aus.

    :::image type="content" source="media/connector-azure-data-explorer/azure-data-explorer-connector.png" alt-text="Screenshot des Azure Data Explorer-Connectors (Kusto)":::    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-azure-data-explorer/configure-azure-data-explorer-linked-service.png" alt-text="Screenshot der Konfiguration des verknüpften Diensts für Azure Data Explorer":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Entitäten speziell für den Azure Data Explorer-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der Azure Data Explorer-Connector unterstützt die folgenden Authentifizierungstypen. Weitere Informationen finden Sie in den entsprechenden Abschnitten:

- [Dienstprinzipalauthentifizierung](#service-principal-authentication)
- [Authentifizierung mit einer systemseitig zugewiesenen verwalteten Identität](#managed-identity)
- [Authentifizierung mit einer benutzerseitig zugewiesenen verwalteten Identität](#user-assigned-managed-identity-authentication)

### <a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung

Zur Verwendung der Dienstprinzipalauthentifizierung führen Sie die folgenden Schritte aus, um einen Dienstprinzipal zu erstellen und Berechtigungen zu erteilen:

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory durch Ausführen der Schritte unter [Registrieren der Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Erteilen Sie dem Dienstprinzipal die geeigneten Berechtigungen in Azure Data Explorer. Unter [Verwalten der Berechtigungen für Datenbanken in Azure Data Explorer](/azure/data-explorer/manage-database-permissions) finden Sie ausführliche Informationen zu Rollen und Berechtigungen sowie zur Verwaltung von Berechtigungen. Gehen Sie wie folgt vor:

    - Weisen Sie der Datenbank **als Quelle** mindestens die Rolle **Database viewer** (Anzeigender Datenbankbenutzer) zu.
    - Weisen Sie der Datenbank **als Senke** mindestens die Rolle **Database ingestor** (Datenbankerfasser) zu.

>[!NOTE]
>Wenn Sie die Benutzeroberfläche für die Erstellung verwenden, wird standardmäßig Ihr Anmeldebenutzerkonto verwendet, um Azure Data Explorer-Cluster, -Datenbanken und -Tabellen aufzulisten. Sie können die Objekte mithilfe des Dienstprinzipals auflisten, indem Sie auf das Dropdownfeld neben der Aktualisierungsschaltfläche klicken, oder den Namen manuell eingeben, wenn Sie für diese Vorgänge nicht berechtigt sind.

Folgende Eigenschaften werden für den mit Azure Data Explorer verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureDataExplorer** festgelegt werden. | Ja |
| endpoint | Endpunkt-URL des Azure Data Explorer-Clusters im Format `https://<clusterName>.<regionName>.kusto.windows.net` | Ja |
| database | Name der Datenbank | Ja |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Dies wird in der [Kusto-Verbindungszeichenfolge](/azure/kusto/api/connection-strings/kusto#application-authentication-properties) als „Autoritäts-ID“ bezeichnet. Sie können ab abrufen, indem Sie im Azure-Portal mit dem Mauszeiger auf den Bereich oben rechts zeigen. | Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. Dies wird in der [Kusto-Verbindungszeichenfolge](/azure/kusto/api/connection-strings/kusto#application-authentication-properties) als „AAD-Anwendungsclient-ID“ bezeichnet. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Dies wird in der [Kusto-Verbindungszeichenfolge](/azure/kusto/api/connection-strings/kusto#application-authentication-properties) als „AAD-Anwendungsschlüssel“ bezeichnet. Markieren Sie dieses Feld als **SecureString**, um es sicher zu speichern, oder [verweisen Sie auf sicher in Azure Key Vault gespeicherte Daten](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel: Verwenden der Dienstprinzipal-Schlüsselauthentifizierung**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

### <a name="system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> Authentifizierung mit einer systemseitig zugewiesenen verwalteten Identität

Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).

Führen Sie die folgenden Schritte zum Gewähren von Berechtigungen aus, um die Authentifizierung mit einer systemseitig zugewiesenen verwalteten Identität zu verwenden:

1. [Rufen Sie die Informationen zur verwalteten Identität ab](data-factory-service-identity.md#retrieve-managed-identity), indem Sie den Wert der **Objekt-ID der verwalteten Identität** kopieren, der zusammen mit Ihrer Factory oder Ihrem Synapse-Arbeitsbereich generiert wurde.

2. Erteilen Sie der verwalteten Identität die geeigneten Berechtigungen in Azure Data Explorer. Unter [Verwalten der Berechtigungen für Datenbanken in Azure Data Explorer](/azure/data-explorer/manage-database-permissions) finden Sie ausführliche Informationen zu Rollen und Berechtigungen sowie zur Verwaltung von Berechtigungen. Gehen Sie wie folgt vor:

    - Weisen Sie der Datenbank **als Quelle** mindestens die Rolle **Database viewer** (Anzeigender Datenbankbenutzer) zu.
    - Weisen Sie der Datenbank **als Senke** mindestens die Rolle **Database ingestor** (Datenbankerfasser) zu.

>[!NOTE]
>Wenn Sie die Benutzeroberfläche für die Erstellung verwenden, wird Ihr Anmeldebenutzerkonto verwendet, um Azure Data Explorer-Cluster, -Datenbanken und -Tabellen aufzulisten. Geben Sie den Namen manuell ein, wenn Sie keine Berechtigung für diese Vorgänge besitzen.

Folgende Eigenschaften werden für den mit Azure Data Explorer verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureDataExplorer** festgelegt werden. | Ja |
| endpoint | Endpunkt-URL des Azure Data Explorer-Clusters im Format `https://<clusterName>.<regionName>.kusto.windows.net` | Ja |
| database | Name der Datenbank | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel: Verwenden der Authentifizierung mit einer systemseitig zugewiesenen verwalteten Identität**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
        }
    }
}
```

### <a name="user-assigned-managed-identity-authentication"></a>Authentifizierung mit einer benutzerseitig zugewiesenen verwalteten Identität
Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md)

Führen Sie die folgenden Schritte aus, um die Authentifizierung mit einer benutzerseitig zugewiesenen verwalteten Identität zu verwenden:

1. [Erstellen Sie eine oder mehrere benutzerseitig zugewiesene verwaltete Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), und gewähren Sie ihnen Berechtigungen in Azure Data Explorer. Unter [Verwalten der Berechtigungen für Datenbanken in Azure Data Explorer](/azure/data-explorer/manage-database-permissions) finden Sie ausführliche Informationen zu Rollen und Berechtigungen sowie zur Verwaltung von Berechtigungen. Gehen Sie wie folgt vor:

    - Weisen Sie der Datenbank **als Quelle** mindestens die Rolle **Database viewer** (Anzeigender Datenbankbenutzer) zu.
    - Weisen Sie der Datenbank **als Senke** mindestens die Rolle **Database ingestor** (Datenbankerfasser) zu.
     
2. Weisen Sie Ihrer Data Factory oder Ihrem Synapse-Arbeitsbereich eine oder mehrere benutzerseitig zugewiesene verwaltete Identitäten zu, und [erstellen Sie Anmeldeinformationen](credentials.md) für jede benutzerseitig zugewiesene verwaltete Identität.

Folgende Eigenschaften werden für den mit Azure Data Explorer verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureDataExplorer** festgelegt werden. | Ja |
| endpoint | Endpunkt-URL des Azure Data Explorer-Clusters im Format `https://<clusterName>.<regionName>.kusto.windows.net` | Ja |
| database | Name der Datenbank | Ja |
| Anmeldeinformationen | Geben Sie die benutzerseitig zugewiesene verwaltete Identität als Anmeldeinformationsobjekt an. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel: Verwenden der Authentifizierung mit einer benutzerseitig zugewiesenen verwalteten Identität**
```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält die Eigenschaften, die mit dem Azure Data Explorer-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten in Azure Data Explorer die type-Eigenschaft des Datasets auf **AzureDataExplorerTable** fest.

Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureDataExplorerTable** festgelegt werden. | Ja |
| table | Der Name der Tabelle, auf die der verknüpfte Dienst verweist. | Quelle: Ja, Senke: Nein |

**Beispiel für Dataseteigenschaften:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste der verfügbaren Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines und Aktivitäten](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die mit Azure Data Explorer-Quellen und -Senken unterstützt werden.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer als Quelle

Legen Sie zum Kopieren von Daten aus Azure Data Explorer die **type**-Eigenschaft in der Quelle der Kopieraktivität auf **AzureDataExplorerSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **AzureDataExplorerSource** | Ja |
| Abfrage | Eine in einem [KQL-Format](/azure/kusto/query/) angegebene schreibgeschützte Anforderung. Verwenden Sie die benutzerdefinierte KQL-Abfrage als Verweis. | Ja |
| queryTimeout | Die Wartezeit vor dem Timeout der Abfrageanforderung. Der Standardwert ist 10 Minuten (00:10:00), der zulässige maximale Wert 1 Stunde (01:00:00). | Nein |
| noTruncation | Gibt an, ob das zurückgegebene Resultset abgeschnitten werden soll. Standardmäßig wird das Ergebnis nach 500.000 Datensätzen oder 64 Megabyte (MB) abgeschnitten. Das Abschneiden wird dringend empfohlen, um das richtige Verhalten für die Aktivität sicherzustellen. |Nein |

>[!NOTE]
>Standardmäßig ist die Azure Data Explorer-Quelle auf 500.000 Datensätze oder 64 MB begrenzt. Um alle Datensätze ohne Abschneiden abzurufen, können Sie `set notruncation;` am Anfang Ihrer Abfrage angeben. Weitere Informationen finden Sie unter [Abfragegrenzwerte](/azure/kusto/concepts/querylimits).

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer als Senke

Legen Sie zum Kopieren von Daten in Azure Data Explorer die type-Eigenschaft in der Senke der Kopieraktivität auf **AzureDataExplorerSink** fest. Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf Folgendes festgelegt werden: **AzureDataExplorerSink**. | Ja |
| ingestionMappingName | Der Name einer vorab erstellten [Zuordnung](/azure/kusto/management/mappings#csv-mapping) für eine Kusto-Tabelle. Zum Zuordnen der Spalten aus der Quelle zu Azure Data Explorer (gilt für [alle unterstützten Quellspeicher und -formate](copy-activity-overview.md#supported-data-stores-and-formats), einschließlich der Formate CSV, JSON und Avro) können Sie die Kopieraktivität [Spaltenzuordnung](copy-activity-schema-and-type-mapping.md) (implizit anhand des Namens oder explizit wie konfiguriert) und/oder Azure Data Explorer-Zuordnungen verwenden. | Nein |
| additionalProperties | Ein Eigenschaftenbehälter, mit dem Sie beliebige Erfassungseigenschaften angeben können, die nicht bereits von der Azure Data Explorer-Senke festgelegt sind. Dies kann besonders nützlich sein, um Erfassungstags anzugeben. Weitere Informationen finden Sie in der [Dokumentation zur Datenerfassung im Azure Data Explorer](/azure/data-explorer/ingestion-properties). | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Beim Transformieren von Daten im Zuordnungsdatenfluss können Sie in Tabellen Azure Data Explorer lesen und in diese schreiben. Weitere Informationen finden Sie unter [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) in Zuordnungsdatenflüssen. Sie können ein Azure Data Explorer-Dataset oder ein [Inlinedataset](data-flow-source.md#inline-datasets) als Quelle und Senkentyp verwenden.

### <a name="source-transformation"></a>Quellentransformation

In der folgenden Tabelle werden die von der Azure Data Explorer-Quelle unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Quelloptionen** bearbeiten.

| Name | BESCHREIBUNG | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabelle | Wenn Sie „Tabelle“ als Eingabe auswählen, holt der Datenfluss alle Daten aus der Tabelle, die im Azure Data Explorer-Datenset oder in den Quelloptionen angegeben ist, wenn ein Inlinedataset verwendet wird. | Nein | String | *(nur für Inlinedataset)*<br>tableName |
| Abfrage | Eine in einem [KQL-Format](/azure/data-explorer/kusto/query/) angegebene schreibgeschützte Anforderung. Verwenden Sie die benutzerdefinierte KQL-Abfrage als Verweis.  | Nein | String | Abfrage |
| Timeout | Die Wartezeit vor dem Timeout der Abfrageanforderung. Die Standardeinstellung ist 172.000 Minuten (2 Tage).  | Nein | Integer | timeout |

#### <a name="azure-data-explorer-source-script-examples"></a>Azure Data Explorer-Quellskriptbeispiele

Wenn Sie ein Azure Data Explorer-Dataset als Quelltyp verwenden, sieht das zugehörige Datenflussskript wie folgt aus:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'table | take 10',
    format: 'query') ~> AzureDataExplorerSource

```

Wenn Sie ein Inlinedataset verwenden, sieht das zugehörige Datenflussskript wie folgt aus:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'table | take 10',
    store: 'azuredataexplorer') ~> AzureDataExplorerSource

```

### <a name="sink-transformation"></a>Senkentransformation

In der folgenden Tabelle werden die von der Azure Data Explorer-Senke unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Einstellungen** bearbeiten. Bei Verwendung eines Inlinedatasets werden zusätzliche Einstellungen angezeigt. Diese entsprechen den Eigenschaften, die im Abschnitt zu den [Dataseteigenschaften](#dataset-properties) beschrieben sind. 

| Name | BESCHREIBUNG | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Aktion table | Bestimmt, ob die Zieltabelle vor dem Schreiben neu erstellt werden soll oder alle Zeilen aus der Zieltabelle entfernt werden sollen.<br>- **Keine**: Es wird keine Aktion an der Tabelle vorgenommen.<br>- **Neu erstellen**: Die Tabelle wird gelöscht und neu erstellt. Erforderlich, wenn eine neue Tabelle dynamisch erstellt wird.<br>- **Abschneiden**: Alle Zeilen werden aus der Zieltabelle entfernt. | Nein | `true` oder `false` | Neu erstellen<br/>truncate |
| Pre- und Post-SQL-Skripts | Geben Sie mehrere Skripte mit [Kusto-Steuerungsbefehlen](/azure/data-explorer/kusto/query/#control-commands) an, die ausgeführt werden, bevor Daten in Ihre Senkendatenbank geschrieben werden (Vorverarbeitung) und nachdem dies geschieht (Nachbearbeitung). | Nein | String | preSQLs; postSQLs |
| Timeout | Die Wartezeit vor dem Timeout der Abfrageanforderung. Die Standardeinstellung ist 172.000 Minuten (2 Tage). | Nein | Integer | timeout |


#### <a name="azure-data-explorer-sink-script-examples"></a>Azure Data Explorer-Senkenskriptbeispiele

Wenn Sie ein Azure Data Explorer-Dataset als Senkentyp verwenden, sieht das zugehörige Datenflussskript wie folgt aus:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    preSQLs:['pre SQL scripts'],
    postSQLs:['post SQL script'],
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureDataExplorerSink

```

Wenn Sie ein Inlinedataset verwenden, sieht das zugehörige Datenflussskript wie folgt aus:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    store: 'azuredataexplorer',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureDataExplorerSink

```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Weitere Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte

* Eine Liste der Datenspeicher, die die Copy-Aktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

* Weitere Informationen finden Sie unter [Kopieren von Daten in Azure Data Explorer mithilfe von Azure Data Factory und Synapse Analytics](/azure/data-explorer/data-factory-load-data).