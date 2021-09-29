---
title: Parametrisieren verknüpfter Dienste
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie in Azure Data Factory und Azure Synapse Analytics-Pipelines verknüpfte Dienste parametrisieren und dynamische Werte zur Laufzeit übergeben.
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
author: chez-charlie
ms.author: chez
ms.openlocfilehash: 6500e743644cc505a15e44be02358d943fa5baa4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750508"
---
# <a name="parameterize-linked-services-in-azure-data-factory-and-azure-synapse-analytics"></a>Parametrisieren verknüpfter Dienste in Azure Data Factory und Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sie können jetzt einen verknüpften Dienst parametrisieren und dynamische Werte zur Laufzeit übergeben. Wenn Sie beispielsweise Verbindungen mit verschiedenen Datenbanken auf demselben logischen SQL-Server herstellen möchten, können Sie nun den Datenbanknamen in der verknüpften Dienstdefinition parametrisieren. Dadurch wird vermieden, dass Sie für jede Datenbank auf dem logischen SQL-Server einen verknüpften Dienst erstellen müssen. Sie können auch andere Eigenschaften in der Definition des verknüpften Diensts parametrisieren, z.B. den *Benutzernamen*.

Sie können verknüpfte Dienste mithilfe der Benutzeroberfläche im Azure-Portal oder über eine Programmierschnittstelle parametrisieren.

> [!TIP]
> Es wird empfohlen, Kennwörter oder Geheimnisse nicht zu parametrisieren. Speichern Sie stattdessen alle Geheimnisse in Azure Key Vault, und parametrisieren Sie den *geheimen Namen*.

> [!Note]
> Es gibt derzeit noch einen ungelösten Fehler bei Verwendung von „-“ in Parameternamen. Es wird empfohlen, Namen ohne „-“ zu verwenden, bis der Fehler behoben wurde.

Das folgende Video enthält eine siebenminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Unterstützte Arten verknüpfter Dienste

Alle verknüpften Diensttypen werden für die Parametrisierung unterstützt.

**Nativ in der Benutzeroberfläche unterstützt**: Für die Erstellung verknüpfter Dienste in der Benutzeroberfläche bietet der Dienst eine integrierte Parametrisierungsfunktion für die folgenden Arten verknüpfter Dienste. Im Blatt zur Erstellung/Bearbeitung von verknüpften Diensten finden Sie Optionen für neue Parameter und können dynamische Inhalte hinzufügen. Siehe [Benutzeroberfläche](#ui-experience).

- Amazon Redshift
- Amazon S3
- Amazon S3 Compatible Storage
- Azure Blob Storage
- Azure Cosmos DB (SQL-API)
- Azure Data Lake Storage Gen2
- Azure Database for MySQL
- Azure Databricks
- Azure-Schlüsseltresor
- Azure SQL-Datenbank
- Verwaltete Azure SQL-Instanz
- Azure Synapse Analytics 
- Azure Table Storage
- Generisches HTTP
- Generisches REST
- MySQL
- Oracle
- Oracle Cloud Storage
- SQL Server

**Erweiterte Erstellung**: Für andere Arten verknüpfter Dienste, die nicht in der obigen Liste enthalten sind, können Sie den verknüpften Dienst parametrisieren, indem Sie die JSON über die Benutzeroberfläche bearbeiten:

- Im Blatt zur Erstellung/Bearbeitung von verknüpften Diensten erweitern Sie die Option „Erweitert“ am unteren Rand. Aktivieren Sie dann das Kontrollkästchen „Dynamische Inhalte im JSON-Format angeben“, und geben Sie die JSON-Nutzlast für den verknüpften Dienst an. 
- Oder, nachdem Sie einen verknüpften Dienst ohne Parametrisierung erstellt haben, suchen Sie in [Verwaltungshub](author-visually.md#management-hub) -> Verknüpfte Dienste den bestimmten verknüpften Dienst, und klicken Sie auf „Code“ (Schaltfläche „{}“), um den JSON-Code zu bearbeiten. 

Informationen zum Definieren von Parametern und Verweisen auf den Parameter mithilfe von ` @{linkedService().paramName} ` finden Sie im Abschnitt mit dem [JSON-Beispiel](#json) zum Hinzufügen von ` parameters`.

## <a name="ui-experience"></a>Benutzeroberfläche

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-1.png" alt-text="Hinzufügen dynamischen Inhalts zur Definition des verknüpften Diensts":::

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-2.png" alt-text="Erstellen eines neuen Parameters":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-1-synapse.png" alt-text="Hinzufügen dynamischen Inhalts zur Definition des verknüpften Diensts":::

:::image type="content" source="media/parameterize-linked-services/parameterize-linked-services-image-2-synapse.png" alt-text="Erstellen eines neuen Parameters":::

---

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
