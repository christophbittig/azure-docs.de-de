---
title: Übersicht über Connectors
titleSuffix: Azure Data Factory & Azure Synapse
description: Lernen Sie die unterstützten Connectors in Azure Data Factory- und Azure Synapse Analytics-Pipelines kennen.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 9233c17070d5a4f60311501e0f42072b1e4ea125
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767677"
---
# <a name="azure-data-factory-and-azure-synapse-analytics-connector-overview"></a>Übersicht über die Connectors in Azure Data Factory und Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory- und Azure Synapse Analytics-Pipelines unterstützen die folgenden Datenspeicher und Formate über die Kopieraktivität, den Datenfluss, die Lookup-Aktivität, die Aktivität „Metadaten abrufen“ und die Löschaktivität. Klicken Sie auf die einzelnen Datenspeicher, um mehr über die unterstützten Funktionen und die entsprechende Konfiguration zu erfahren.

## <a name="supported-data-stores"></a>Unterstützte Datenspeicher

[!INCLUDE [Connector overview](includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integrieren in weitere Datenspeicher

Azure Data Factory- und Azure Synapse Analytics-Pipelines können sich mit einer größeren Gruppe von Datenspeichern als mit denen in der obigen Liste erwähnten verbinden. Wenn Sie Daten in einen/aus einem Datenspeicher verschieben müssen, der nicht in der Liste der integrierten Connectors für die Dienste enthalten ist, finden Sie hier einige Erweiterungsmöglichkeiten:
- Für Datenbanken und Data Warehouses finden Sie in der Regel einen entsprechenden ODBC-Treiber, mit dem Sie den [generischen ODBC-Connector](connector-odbc.md) verwenden können.
- Für SaaS-Anwendungen:
    - Sie können den [generischen REST-Connector](connector-rest.md) verwenden, sofern dieser RESTful-APIs bereitstellt.
    - Sie können den [generischen OData-Connector](connector-odata.md) verwenden, wenn dieser einen OData-Feed bietet.
    - Sie können den [generischen HTTP-Connector](connector-http.md) verwenden, sofern dieser SOAP-APIs bereitstellt.
    - Sie können den [generischen ODBC-Connector](connector-odbc.md) verwenden, sofern dieser einen ODBC-Treiber bietet.
- Prüfen Sie für andere, ob Sie Daten in einen unterstützten Datenspeicher laden oder verfügbar machen können, z. B. Azure Blob/File/FTP/SFTP usw. Lassen Sie den Dienst anschließend vor dort weitermachen. Sie können einen benutzerdefinierten Datenlademechanismus über [Azure Function](control-flow-azure-function-activity.md), [Benutzerdefinierte Aktivität](transform-data-using-dotnet-custom-activity.md), [Datenbanken](transform-data-databricks-notebook.md)/[HDInsight](transform-data-using-hadoop-hive.md), [Webaktivität](control-flow-web-activity.md) usw. aufrufen.

## <a name="supported-file-formats"></a>Unterstützte Dateiformate

Die folgenden Dateiformate werden unterstützt. Informationen zu formatbasierten Einstellungen finden Sie in den jeweiligen Artikeln.

- [Avro-Format](format-avro.md)
- [Binärformat](format-binary.md)
- [Common Data Model-Format](format-common-data-model.md)
- [Textformat mit Trennzeichen](format-delimited-text.md)
- [Delta-Format](format-delta.md)
- [Excel-Format](format-excel.md)
- [JSON-Format](format-json.md)
- [ORC-Format](format-orc.md)
- [Parquet-Format](format-parquet.md)
- [XML-Format](format-xml.md)

## <a name="next-steps"></a>Nächste Schritte

- [Kopieraktivität](copy-activity-overview.md)
- [Zuordnungsdatenfluss](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Aktivität löschen](delete-activity.md)
