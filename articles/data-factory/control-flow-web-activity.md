---
title: Webaktivität
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Webaktivität, eine der Ablaufsteuerungsaktivitäten, die von Azure Data Factory und Azure Synapse Analytics unterstützt werden, verwenden, um einen REST-Endpunkt aus einer Pipeline aufzurufen.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: c5656ffb4d8e129e334efc11e9a8e00be7227493
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798843"
---
# <a name="web-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Webaktivität in Azure Data Factory und Azure Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Die Webaktivität kann verwendet werden, um einen benutzerdefinierten REST-Endpunkt aus einer Azure Data Factory- oder Synapse-Pipeline aufzurufen. Sie können Datasets und verknüpfte Dienste zur Verwendung und für den Zugriff durch die Aktivität übergeben.

> [!NOTE]
> Webaktivitäten werden zum Aufrufen von URLs unterstützt, die in einem privaten virtuellen Netzwerk gehostet sind, sowie zum Nutzen selbstgehosteter Integration Runtimes. Die Integration Runtime sollte den URL-Endpunkt erreichen können. 

> [!NOTE]
> Die maximal unterstützte Payloadgröße für die Ausgabe von Antworten beträgt 4 MB.  

## <a name="syntax"></a>Syntax

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "connectVia": {
          "referenceName": "<integrationRuntimeName>",
          "type": "IntegrationRuntimeReference"
      }
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Name der Webaktivität | String | Ja
type | Muss auf **WebActivity** festgelegt sein. | String | Ja
method | REST-API-Methode für den Zielendpunkt. | Eine Zeichenfolge. <br/><br/>Unterstützte Typen: „GET“, „POST“, „PUT“ | Ja
url | Zielendpunkt und Pfad | Zeichenfolge (oder Ausdruck mit resultType der Zeichenfolge). Nach einer Minute tritt für die Aktivität ein Timeout mit einem Fehler auf, falls sie keine Antwort vom Endpunkt erhält. | Ja
headers | Header, die in der Anforderung gesendet werden. Verwenden Sie beispielsweise Folgendes, um Sprache und Typ für eine Anforderung festzulegen: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Zeichenfolge (oder ein Ausdruck mit resultType der Zeichenfolge) | Ja, der Content-Type-Header ist erforderlich. `"headers":{ "Content-Type":"application/json"}`
body | Stellt die an den Endpunkt gesendete Nutzlast dar.  | Zeichenfolge (oder Ausdruck mit resultType der Zeichenfolge). <br/><br/>Weitere Informationen finden Sie in den Details zum Schema der Anforderungsnutzlast im Abschnitt [Schema der Anforderungsnutzlast](#request-payload-schema). | Erforderlich für POST/PUT-Methoden.
authentication | Die zum Aufrufen des Endpunkts verwendete Authentifizierungsmethode. Unterstützte Typen sind „Basic“ oder „ClientCertificate“. Weitere Informationen finden Sie im Abschnitt [Authentifizierung](#authentication). Wenn keine Authentifizierung erforderlich ist, schließen Sie diese Eigenschaft aus. | Zeichenfolge (oder ein Ausdruck mit resultType der Zeichenfolge) | Nein
datasets | Liste der Datasets, die an den Endpunkt übergeben werden. | Array von Datasetverweisen. Kann ein leeres Array sein. | Ja
linkedServices | Liste der verknüpften Dienste, die an den Endpunkt übergeben werden. | Array von Verweisen auf verknüpfte Dienste. Kann ein leeres Array sein. | Ja
connectVia | Die [Integration Runtime](./concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, verwendet der Dienst die normale Azure Integration Runtime. | Der Verweis auf die Integration Runtime. | Nein 

> [!NOTE]
> REST-Endpunkte, die die Webaktivität aufruft, müssen eine Antwort vom Typ JSON zurückgeben. Nach einer Minute tritt für die Aktivität ein Timeout mit einem Fehler auf, falls sie keine Antwort vom Endpunkt erhält.

Die folgende Tabelle enthält die Anforderungen für JSON-Inhalt:

| Werttyp | Anforderungstext | Antworttext |
|---|---|---|
|JSON-Objekt | Unterstützt | Unterstützt |
|JSON-Array | Unterstützt <br/>(Derzeit funktionieren JSON-Arrays aufgrund eines Bugs nicht. Eine Korrektur ist in Arbeit.) | Nicht unterstützt |
| JSON-Wert | Unterstützt | Nicht unterstützt |
| Nicht-JSON-Typ | Nicht unterstützt | Nicht unterstützt |
||||

## <a name="authentication"></a>Authentifizierung

Im Folgenden finden Sie die unterstützten Authentifizierungstypen in der Webaktivität.

### <a name="none"></a>Keine

Wenn keine Authentifizierung erforderlich ist, schließen Sie die Eigenschaft „authentication“ nicht ein.

### <a name="basic"></a>Basic

Geben Sie Benutzername und Kennwort für die Standardauthentifizierung an.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Clientzertifikat

Geben Sie die Base64-codierten Inhalte einer PFX-Datei und das Kennwort an.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Verwaltete Identität

Geben Sie den Ressourcen-URI an, für den das Zugriffstoken mithilfe der verwalteten Identität für die Data Factory- oder Synapse-Arbeitsbereichsinstanz angefordert wird. Verwenden Sie zum Aufrufen der Azure-Ressourcenverwaltungs-API `https://management.azure.com/`. Weitere Informationen zur Funktion verwalteter Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Wenn Ihr Data Factory- oder Synapse-Arbeitsbereich mit einem Git-Repository konfiguriert ist, müssen Sie Ihre Anmeldeinformationen in Azure Key Vault speichern, damit Sie die Standardauthentifizierung oder die Clientzertifikatauthentifizierung verwenden können. Der Dienst speichert Kennwörter nicht in Git.

## <a name="request-payload-schema"></a>Schema der Anforderungsnutzlast
Wenn Sie die POST/PUT-Methode verwenden, stellt die Eigenschaft „body“ die Nutzlast dar, die an den Endpunkt gesendet wird. Sie können verknüpfte Dienste und Datasets als Teil der Nutzlast übergeben. Dies ist das Schema für die Nutzlast:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Beispiel
In diesem Beispiel ruft die Webaktivität in der Pipeline einen REST-Endpunkt auf. Sie übergibt einen verknüpften Azure SQL-Dienst und ein Azure SQL-Dataset an den Endpunkt. Der REST-Endpunkt verwendet die Azure SQL-Verbindungszeichenfolge für die Verbindung mit dem logischen SQL-Server und gibt den Namen der SQL Server-Instanz zurück.

### <a name="pipeline-definition"></a>Definition der Pipeline

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Parameterwerte der Pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Webdienst-Endpunktcode

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Nächste Schritte
Informationen zu weiteren unterstützten Ablaufsteuerungsaktivitäten:

- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
