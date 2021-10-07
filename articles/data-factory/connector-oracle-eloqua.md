---
title: Kopieren von Daten aus Oracle Eloqua (Vorschauversion)
description: Erfahren Sie, wie Sie Daten aus Oracle Eloqua mithilfe einer Copy-Aktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline in unterstützte Sink-Datenspeicher kopieren können.
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e987244624939a5582523317b732527d99d5a2e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831682"
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-or-synapse-analytics-preview"></a>Kopieren von Daten aus Oracle Eloqua mit Azure Data Factory oder Synapse Analytics (Vorschau)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel beschreibt, wie Sie die Copy-Aktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline verwenden, um Daten aus Oracle Eloqua zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

> [!IMPORTANT]
> Dieser Connector befindet sich derzeit in der Vorschauversion. Sie können ihn ausprobieren und Feedback geben. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/), wenn Sie in Ihrer Lösung eine Abhängigkeit von Connectors verwenden möchten, die sich in der Vorschauphase befinden.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Oracle Eloqua-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Oracle Eloqua in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Der Dienst bietet einen eingebauten Treiber, um die Verbindung zu ermöglichen, so dass Sie keinen Treiber manuell installieren müssen.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-oracle-eloqua-using-ui"></a>Erstellen eines verknüpften Diensts mit Oracle Eloqua über die Benutzeroberfläche

Verwenden Sie die folgenden Schritte, um einen verknüpften Dienst mit Oracle Eloqua auf der Azure-Portal Benutzeroberfläche zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Synapse Benutzeroberfläche zeigt.":::

2. Suchen Sie nach Oracle, und wählen Sie den Oracle Eloqua-Connector aus.

   :::image type="content" source="media/connector-oracle-eloqua/oracle-eloqua-connector.png" alt-text="Ein Screenshot, der den Oracle Eloqua-Connector zeigt":::.    


1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

   :::image type="content" source="media/connector-oracle-eloqua/configure-oracle-eloqua-linked-service.png" alt-text="Ein Screenshot, der die Konfiguration des verknüpften Diensts für Oracle Eloqua zeigt.":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Oracle Eloqua-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Oracle Eloqua verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Eloqua** | Ja |
| endpoint | Der Endpunkt des Eloqua-Servers. Eloqua unterstützt mehrere Rechenzentren. Um den Endpunkt zu bestimmen, melden Sie sich mit Ihren Anmeldeinformationen bei https://login.eloqua.com an, und kopieren Sie dann den Teil mit der **Basis-URL** aus der umgeleiteten URL nach dem Muster `xxx.xxx.eloqua.com`. | Ja |
| username | Website- und Benutzername Ihres Eloqua-Kontos im Format `SiteName\Username` (Beispiel: `Eloqua\Alice`)  | Ja |
| password | Das Kennwort, das zum Benutzernamen gehört. Markieren Sie dieses Feld als einen „SecureString“, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Gibt an, ob die Endpunkte der Datenquelle mit HTTPS verschlüsselt sind. Der Standardwert lautet „true“.  | Nein |
| useHostVerification | Gibt an, ob der Hostname im Zertifikat des Servers mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |
| usePeerVerification | Gibt an, ob die Identität des Servers überprüft werden soll, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert lautet „true“.  | Nein |

**Beispiel:**

```json
{
    "name": "EloquaLinkedService",
    "properties": {
        "type": "Eloqua",
        "typeProperties": {
            "endpoint" : "<base URL e.g. xxx.xxx.eloqua.com>",
            "username" : "<site name>\\<user name e.g. Eloqua\\Alice>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Oracle Eloqua-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus Oracle Eloqua die „type“-Eigenschaft des Datasets auf **EloquaObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **EloquaObject** | Ja |
| tableName | Der Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "EloquaDataset",
    "properties": {
        "type": "EloquaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Eloqua linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Oracle Eloqua-Quelle unterstützt werden.

### <a name="eloqua-as-source"></a>Eloqua als Quelle

Legen Sie zum Kopieren von Daten aus Oracle Eloqua den Quelltyp in der Kopieraktivität auf **EloquaSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **EloquaSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM Accounts"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromEloqua",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Eloqua input dataset name>",
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
                "type": "EloquaSource",
                "query": "SELECT * FROM Accounts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der unterstützten Datenspeicher des Dienstes finden Sie unter [unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
