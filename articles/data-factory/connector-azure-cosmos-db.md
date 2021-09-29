---
title: Kopieren und Transformieren von Daten in Azure Cosmos DB (SQL-API)
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie mithilfe von Azure Data Factory und Azure Synapse Analytics Daten in und aus Azure Cosmos DB (SQL-API) kopieren sowie Daten in Azure Cosmos DB (SQL-API) transformieren.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 836ad2692dffbb6c4977f5757c6f305c6fd66d40
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761871"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopieren und Transformieren von Daten in Azure Cosmos DB (SQL-API) mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuelle Version](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Daten mithilfe der Kopieraktivität in Azure Data Factory aus und in Azure Cosmos DB (SQL-API) kopieren sowie Daten mithilfe von Datenfluss in Azure Cosmos DB (SQL API) transformieren. Weitere Informationen finden Sie in dem Einführungsartikel zu [Azure Data Factory](introduction.md) oder [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

>[!NOTE]
>Dieser Connector unterstützt nur Cosmos DB SQL-API. Informationen zur MongoDB-API finden Sie unter [Connector für Azure Cosmos DB-API für MongoDB](connector-azure-cosmos-db-mongodb-api.md). Andere API-Typen werden derzeit nicht unterstützt.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Connector für Azure Cosmos DB (SQL API) wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Im Rahmen der Kopieraktivität unterstützt dieser Azure Cosmos DB (SQL-API)-Connector folgende Aktivitäten:

- Das Kopieren von Daten aus und in die Azure Cosmos DB [SQL-API](../cosmos-db/introduction.md) mithilfe von Schlüssel-, Dienstprinzipal- oder verwalteten Identitäten für die Azure-Ressourcenauthentifizierungen.
- Schreiben in Azure Cosmos DB als **insert** oder **upsert**.
- Importieren und Exportieren von JSON-Dokumenten in ihrem jeweiligen Zustand oder Kopieren von Daten aus einem tabellarischen Dataset oder in ein tabellarisches Dataset. Die Beispiele zeigen eine SQL-Datenbank und eine CSV-Datei. Informationen zum Kopieren von Dokumenten in ihrem jeweiligen Zustand in bzw. aus JSON-Dateien oder in eine andere bzw. aus einer anderen Azure Cosmos DB-Sammlung finden Sie unter [Importieren und Exportieren von JSON-Dokumenten](#import-and-export-json-documents).

Data Factory- und Synapse Pipelines werden in die [Azure Cosmos DB-BuklExecutor-Bibliothek](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) integriert, um beim Schreiben in Azure Cosmos DB die beste Leistung zu erzielen.

> [!TIP]
> Das Video [Datenmigration](https://youtu.be/5-SRNiC_qOU) führt Sie schrittweise durch das Kopieren von Daten aus Azure Blob Storage in Azure Cosmos DB. Das Video beschreibt auch Überlegungen zur Leistungsoptimierung beim Erfassen von Daten in Azure Cosmos DB im Allgemeinen.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]
## <a name="create-a-linked-service-to-azure-cosmos-db-using-ui"></a>Erstellen eines verknüpften Diensts mit Azure Cosmos DB über die Benutzeroberfläche

Verwenden Sie die folgenden Schritte, um einen verknüpften Dienst für die Azure Cosmos DB auf der Azure-Portal-Benutzeroberfläche zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Synapse Benutzeroberfläche zeigt.":::

2. Suchen Sie nach Cosmos, und wählen Sie die Azure Cosmos DB (SQL-API) aus.

    :::image type="content" source="media/connector-azure-cosmos-db/azure-cosmos-db-connector.png" alt-text="Wählen Sie Azure Cosmos DB-Connector (SQL-API) aus":::.    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-azure-cosmos-db/configure-azure-cosmos-db-linked-service.png" alt-text="Ein Screenshot, der die Konfiguration des verknüpften Diensts für Azure Cosmos DB zeigt.":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration


Die folgenden Abschnitte enthalten Details zu Eigenschaften, die Sie zum Definieren von Entitäten verwenden können, die spezifisch für Azure Cosmos DB (SQL-API) sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der Azure Cosmos DB (SQL API) Connector unterstützt die folgenden Authentifizierungstypen. Weitere Informationen finden Sie in den entsprechenden Abschnitten:

- [Schlüsselauthentifizierung](#key-authentication)
- [Dienstprinzipalauthentifizierung](#service-principal-authentication)
- [Verwaltete Identitäten für Azure-Ressourcenauthentifizierung](#managed-identity)

### <a name="key-authentication"></a>Schlüsselauthentifizierung

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **CosmosDb** festgelegt werden. | Ja |
| connectionString |Geben Sie die zum Herstellen einer Verbinden mit der Azure Cosmos DB-Datenbank erforderlichen Informationen an.<br />**Hinweis**: Sie müssen Datenbankinformationen in der Verbindungszeichenfolge angeben, wie in den folgenden Beispielen gezeigt. <br/> Sie können auch den Kontoschlüssel in Azure Key Vault speichern und die `accountKey`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie in den folgenden Beispielen und im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Speichern des Kontoschlüssels in Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a><a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung

>[!NOTE]
>Derzeit wird die Dienstprinzipalauthentifizierung im Datenfluss nicht unterstützt.

Zum Verwenden der Dienstprinzipalauthentifizierung führen Sie die folgenden Schritte aus.

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), indem Sie die Schritte unter [Registrieren der Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) befolgen. Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Erteilen Sie dem Dienstprinzipal geeignete Berechtigungen. Beispiele zur Funktionsweise von Berechtigungen in der Cosmos DB finden Sie unter den [Zugriffssteuerungslisten für Dateien und Verzeichnisse](../cosmos-db/how-to-setup-rbac.md). Genauer gesagt, erstellen Sie eine Rollendefinition, und weisen die Rolle über die Dienstprinzipalobjekt-ID dem Dienstprinzipal zu. 

Diese Eigenschaften werden im verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die type-Eigenschaft muss auf **CosmosDb** festgelegt werden. |Yes |
| accountEndpoint | Geben Sie die Kontoendpunkt-URL für die Azure Cosmos DB an. | Ja |
| database | Geben Sie den Namen der Datenbank an. | Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja |
| servicePrincipalCredentialType | Die Art von Anmeldeinformationen, die für die Authentifizierung beim Dienstprinzipal verwendet werden. Gültige Werte sind **ServicePrincipalKey** und **ServicePrincipalCert**. | Ja |
| servicePrincipalCredential | Die Anmeldeinformationen für den Dienstprinzipal. <br/> Wenn Sie **ServicePrincipalKey** als Anmeldeinformationstyp verwenden, geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als einen **SecureString**, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). <br/> Wenn Sie **ServicePrincipalCert** als Anmeldeinformationen verwenden,verweisen Sie auf ein Zertifikat in Azure Key Vault. | Ja |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja |
| azureCloudType | Geben Sie für die Dienstprinzipalauthentifizierung die Art der Azure-Cloudumgebung an, bei der Ihre Azure Active Directory-Anwendung registriert ist. <br/> Zulässige Werte sind **AzurePublic**, **AzureChina**, **AzureUsGovernment** und **AzureGermany**. Standardmäßig wird die Cloudumgebung des Diensts verwendet. | Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel: Verwenden der Dienstprinzipal-Schlüsselauthentifizierung**

Sie können den Dienstprinzipalschlüssel auch in Azure Key Vault speichern.

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Verwenden der Dienstprinzipal-Zertifikatauthentifizierung**
```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Verwaltete Identitäten für Azure-Ressourcenauthentifizierung

>[!NOTE]
>Derzeit wird die verwaltete Identitätsauthentifizierung im Datenfluss nicht unterstützt.

Eine Data Factory- oder Synapse-Pipeline kann einer [verwalteten Identität für Azure-Ressourcen](data-factory-service-identity.md) zugeordnet werden, die diese spezielle Data Factory darstellt. Sie können diese verwaltete Identität für die Cosmos DB-Authentifizierung ähnlich wie bei der Verwendung Ihres eigenen Dienstprinzipals verwenden. Sie erlaubt dieser bestimmten Ressource den Zugriff und das Kopieren von Daten in oder aus Ihrer Microsoft Azure Cosmos DB.

Um verwaltete Identitäten für die Azure-Ressourcenauthentifizierung zu verwenden, gehen Sie folgendermaßen vor.

1. [Rufen Sie die Informationen zur verwalteten Identität ab](data-factory-service-identity.md#retrieve-managed-identity), indem Sie den Wert von **Objekt-ID der verwalteten Identität** kopieren, der zusammen mit Ihrem Dienst generiert wurde.

2. Erteilen Sie der verwalteten Entität geeignete Berechtigungen. Beispiele zur Funktionsweise von Berechtigungen in der Cosmos DB finden Sie unter den [Zugriffssteuerungslisten für Dateien und Verzeichnisse](../cosmos-db/how-to-setup-rbac.md). Genauer gesagt, erstellen Sie eine Rollendefinition, und weisen die Rolle der verwalteten Identität zu.

Diese Eigenschaften werden im verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| Typ | Die type-Eigenschaft muss auf **CosmosDb** festgelegt werden. |Yes |
| accountEndpoint | Geben Sie die Kontoendpunkt-URL für die Azure Cosmos DB an. | Ja |
| database | Geben Sie den Namen der Datenbank an. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel:**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md).

Folgende Eigenschaften werden für das Azure Cosmos DB (SQL-API)-Dataset unterstützt: 

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** des Datasets muss auf **CosmosDbSqlApiCollection** festgelegt werden. |Ja |
| collectionName |Der Name der Azure Cosmos DB-Dokumentsammlung. |Ja |

Wenn Sie ein Dataset vom Typ „DocumentDbCollection“ verwenden, wird es aus Gründen der Abwärtskompatibilität für die Kopier- und Lookup-Aktivität weiterhin unverändert unterstützt. Für Datenfluss wird es nicht unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden.

**Beispiel**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure Cosmos DB-Quelle und -Senke (SQL-API) unterstützt werden. Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) als Quelle

Legen Sie zum Kopieren von Daten aus Azure Cosmos DB (SQL-API) den **Quelltyp** in der Copy-Aktivität auf **DocumentDbCollectionSource** fest. 

Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** der Quelle für die Kopieraktivität muss auf **CosmosDbSqlApiSource** festgelegt werden. |Ja |
| Abfrage |Geben Sie die Azure Cosmos DB-Abfrage an, um Daten zu lesen.<br/><br/>Beispiel:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nein <br/><br/>Falls nicht angegeben, wird die folgende SQL-Anweisung ausgeführt: `select <columns defined in structure> from mycollection` |
| preferredRegions | Die bevorzugte Liste der Regionen, mit denen beim Abrufen von Daten aus Cosmos DB eine Verbindung hergestellt werden soll. | Nein |
| pageSize | Die Anzahl der Dokumente pro Seite des Abfrageergebnisses. Der Standardwert ist „-1“. Dies bedeutet, dass im Ergebnis die dienstseitige dynamische Seitengröße bis zu 1000 verwendet wird. | Nein |
| detectDatetime | Legt fest, ob datetime aus den Zeichenfolgenwerten in den Dokumenten erkannt werden soll. Zulässige Werte sind **true** (Standard) oder **false** | Nein |

Wenn Sie eine Quelle vom Typ „DocumentDbCollectionSource“ verwenden, wird sie aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, in Zukunft das neue Modell zu verwenden, das umfangreichere Funktionen zum Kopieren von Daten aus Cosmos DB enthält.

**Beispiel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als Methode zum Kopieren von Daten aus Cosmos DB hat es sich bewährt, die Zuordnung in der Kopieraktivität anzugeben, es sei denn, Sie möchten [JSON-Dokumente in ihrem jetzigen Zustand exportieren](#import-and-export-json-documents). Der Dienst Factory beachtet die Zuordnung, die Sie für die Aktivität angegeben haben. Wenn eine Zeile einen Wert für eine Spalte nicht enthält, wird für die Spalte ein NULL-Wert zurückgegeben. Wenn Sie keine Zuordnung angeben, leitet der Dienst das Schema anhand der ersten Zeile in den Daten ab. Wenn die erste Zeile nicht das vollständige Schema enthält, fehlen im Ergebnis der Aktivität einige Spalten.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) als Senke

Legen Sie zum Kopieren von Daten in Azure Cosmos DB (SQL-API) den **Senkentyp** in der Copy-Aktivität auf **DocumentDbCollectionSink** fest. 

Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** der Senke für die Kopieraktivität muss auf **CosmosDbSqlApiSink** festgelegt werden. |Ja |
| writeBehavior |Beschreibt, wie Daten in Azure Cosmos DB geschrieben werden. Zulässige Werte: **insert** und **upsert**.<br/><br/>Das Verhalten von **upsert** besteht darin, das Dokument zu ersetzen, wenn ein Dokument mit der gleichen ID bereits vorhanden ist. Andernfalls wird das Dokument eingefügt.<br /><br />**Hinweis**: Der Dienst generiert automatisch eine ID für ein Dokument, wenn eine ID weder im Originaldokument noch durch eine Spaltenzuordnung angegeben wird. Dies bedeutet, dass Sie sicherstellen müssen, dass Ihr Dokument eine ID besitzt, damit **upsert** wie erwartet funktioniert. |Nein<br />(der Standardwert ist **insert**) |
| writeBatchSize | Der Dienst verwendet die [Azure Cosmos DB-BulkExecutor-Bibliothek](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) zum Schreiben von Daten in Azure Cosmos DB. Die Eigenschaft **writeBatchSize** steuert die Größe der von dem Dienst in der Bibliothek bereitgestellten Dokumente. Sie können versuchen, den Wert für **writeBatchSize** zu erhöhen, um die Leistung zu verbessern, oder den Wert verringern, falls Ihre Dokumente groß sind. Weitere Tipps finden Sie weiter unten. |Nein<br />(der Standardwert ist **10.000**) |
| disableMetricsCollection | Der Dienst sammelt Metriken wie Cosmos DB-Anforderungseinheiten für die Leistungsoptimierung von Kopiervorgängen und Empfehlungen. Wenn Sie sich wegen dieses Verhaltens Gedanken machen, geben Sie `true` an, um es zu deaktivieren. | Nein (Standard = `false`) |
| maxConcurrentConnections |Die Obergrenze gleichzeitiger Verbindungen mit dem Datenspeicher während der Aktivitätsausführung. Geben Sie diesen Wert nur an, wenn Sie die Anzahl der gleichzeitigen Verbindungen begrenzen möchten.| No |


>[!TIP]
>Informationen zum Importieren von JSON-Dokumenten in ihrem jetzige Zustand finden Sie im Abschnitt [Importieren oder Exportieren von JSON-Dokumenten](#import-and-export-json-documents). Informationen zum Kopieren aus tabellarisch strukturierten Daten finden Sie unter [Migrieren von relationalen Datenbanken in Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB begrenzt die Größe der einzelnen Anforderung auf 2 MB. Die Formel lautet: Anforderungsgröße = Einzeldokumentgröße * Schreibbatchgröße. Sollte ein Fehler mit dem Hinweis auftreten, dass die **Anforderung zu groß ist**, **verringern Sie den `writeBatchSize`-Wert** in der Kopiersenkenkonfiguration.

Wenn Sie eine Quelle vom Typ „DocumentDbCollectionSink“ verwenden, wird sie aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, in Zukunft das neue Modell zu verwenden, das umfangreichere Funktionen zum Kopieren von Daten aus Cosmos DB enthält.

**Beispiel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Schemazuordnung

Informationen zum Kopieren von Daten aus Azure Cosmos DB in eine tabellarische Senke oder umgekehrt finden Sie unter [Schemazuordnung](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Beim Transformieren von Daten im Zuordnungsdatenfluss können Sie Sammlungen in Cosmos DB lesen und in diese schreiben. Weitere Informationen finden Sie unter [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) in Zuordnungsdatenflüssen.

### <a name="source-transformation"></a>Quellentransformation

Spezifische Einstellungen für Azure Cosmos DB sind auf der Registerkarte **Quellenoptionen** der Quellentransformation verfügbar. 

**Include system columns** (Systemspalten einschließen): Wenn für diese Einstellung TRUE gilt, werden ```id```, ```_ts``` und andere Systemspalten in die Datenflussmetadaten von Cosmos DB eingeschlossen. Beim Aktualisieren von Sammlungen ist es wichtig, dass diese eingeschlossen sind, damit Sie die vorhandene Zeilen-ID erfassen können.

**Seitengröße:** Die Anzahl der Dokumente pro Seite des Abfrageergebnisses. Der Standardwert ist „-1“, bei dem die dynamische Seite des Diensts mit bis zu 1000 verwendet wird.

**Durchsatz:** Legen Sie einen optionalen Wert für die Anzahl von RUs fest, die Sie für jede Ausführung dieses Datenflusses während des Lesevorgangs auf die CosmosDB-Sammlung anwenden möchten. Der Mindestwert ist 400.

**Bevorzugte Region:** Sie können die bevorzugten Leseregionen für diesen Prozess auswählen.

#### <a name="json-settings"></a>JSON-Einstellungen

**Einzeldokument**: Wählen Sie diese Option aus, wenn die gesamte Datei von dem Dienst wie ein einzelnes JSON-Dokument behandelt werden soll.

**Spaltennamen ohne Anführungszeichen:** Wählen Sie diese Option aus, wenn die Spaltennamen im JSON-Code nicht in Anführungszeichen gesetzt sind.

**Mit Kommentaren:** Verwenden Sie diese Auswahl, wenn Ihre JSON-Dokumente über Kommentare in den Daten verfügen.

**Einfache Anführungszeichen:** Diese Option sollte ausgewählt werden, wenn die Spalten und Werte in Ihrem Dokument in einfache Anführungszeichen gesetzt sind.

**Umgekehrter Schrägstrich mit Escapezeichen:** Wählen Sie diese Option aus, wenn Sie in Ihrem JSON-Code umgekehrte Schrägstriche als Escapezeichen verwenden.

### <a name="sink-transformation"></a>Senkentransformation

Spezifische Einstellungen für Azure Cosmos DB sind auf der Registerkarte **Einstellungen** der Senkentransformation verfügbar.

**Updatemethode:** Bestimmt, welche Vorgänge für das Datenbankziel zulässig sind. Standardmäßig sind lediglich Einfügevorgänge zulässig. Wenn Sie für Zeilen Update-, Upsert- oder Löschvorgänge verwenden möchten, fügen Sie zunächst eine Transformation zur Änderung von Zeilen hinzu, um Zeilen für diese Aktionen zu kennzeichnen. Für Update-, Upsert- und Löschvorgänge muss mindestens eine Schlüsselspalte festgelegt werden, um die Zeile zu bestimmen, die geändert werden soll.

**Collection action** (Aktion für die Sammlung): Bestimmt, ob die Zielsammlung vor dem Schreibvorgang neu erstellt werden soll.
* Keine: Es wird keine Aktion an der Sammlung vorgenommen.
* Neu erstellen: Die Sammlung wird gelöscht und neu erstellt.

**Batchgröße**: Eine ganze Zahl, die die Anzahl der Objekte darstellt, die in jedem Batch in die Cosmos DB-Sammlung geschrieben werden. Normalerweise reicht die Batchgröße als Startwert aus. Wenn Sie diesen Wert weiter optimieren möchten, beachten Sie Folgendes:

- Cosmos DB begrenzt die Größe der einzelnen Anforderung auf 2 MB. Die Formel lautet: „Anforderungsgröße = Einzeldokumentgröße * Batchgröße“. Wenn die Fehlermeldung „Anforderung ist zu groß“ angezeigt wird, verkleinern Sie den Wert für die Batchgröße.
- Je größer die Batchgröße, desto besser ist der Durchsatz von dem Dienst. Außerdem können Sie gleichzeitig sicherstellen, dass Sie genügend Anforderungseinheiten zuweisen, um ihre Arbeitsauslastung zu verbessern.

**Partitionsschlüssel:** Geben Sie eine Zeichenfolge ein, die den Partitionsschlüssel für Ihre Sammlung darstellt. Beispiel: ```/movies/title```

**Durchsatz:** Legen Sie einen optionalen Wert für die Anzahl von RUs fest, die Sie für jede Ausführung dieses Datenflusses auf die CosmosDB-Sammlung anwenden möchten. Der Mindestwert ist 400.

**Write throughput budget** (Schreibdurchsatz): Eine ganze Zahl zur Darstellung der RUs, die Sie für diesen Datenfluss-Schreibvorgang zuordnen möchten, und nicht nur des Gesamtdurchsatzes, der der Sammlung zugeordnet wurde.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importieren und Exportieren von JSON-Dokumenten

Sie können diesen Azure Cosmos DB-Connector (SQL-API) verwenden, um die folgenden Aufgaben auf einfache Weise zu erledigen:

* Kopieren von Dokumenten in ihrem jeweiligen Zustand zwischen zwei Azure Cosmos DB-Sammlungen.
* Importieren von JSON-Dokumenten aus verschiedenen Quellen in Azure Cosmos DB, z.B. aus Azure Blob Storage, Azure Data Lake Storage und anderen dateibasierten Speichern, die von dem Dienst unterstützt werden.
* Exportieren von JSON-Dokumenten aus einer Azure Cosmos DB-Sammlung in verschiedene dateibasierte Speicher.

So erhalten Sie eine vom Schema unabhängige Kopie:

* Aktivieren Sie bei Verwendung des Tools zum Kopieren von Daten die Option **Export as-is to JSON files or Cosmos DB collection** (Wie vorhanden in JSON-Dateien oder Cosmos DB-Sammlung exportieren).
* Wenn Sie die Aktivität „Dokumenterstellung“ verwenden, wählen Sie das JSON-Format mit dem entsprechenden Dateispeicher für Quelle oder Senke aus.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrieren aus relationalen Datenbanken in Cosmos DB

Beim Migrieren aus einer relationalen Datenbank, z. B. von SQL Server zu Azure Cosmos DB, kann die Kopieraktivität Tabellendaten aus der Quelle einfach zuordnen, um JSON-Dokumente in Cosmos DB zu vereinfachen. In manchen Fällen sollten Sie das Datenmodell umgestalten, um es für Anwendungsfälle außerhalb von NoSQL zu optimieren. Weitere Informationen dazu finden Sie im Artikel [Datenmodellierung in Azure Cosmos DB](../cosmos-db/modeling-data.md). Sie können die Daten beispielsweise denormalisieren, indem Sie alle verbundenen untergeordneten Elemente innerhalb eines JSON-Dokuments einbetten. [In diesem Artikel](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) finden Sie weitere Informationen für so einen Fall sowie eine exemplarische Vorgehensweise, wie Sie dieses Szenario mithilfe der Kopieraktivität umsetzen.

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die diese Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).