---
title: Data Wrangling in Azure Data Factory
description: Hier finden Sie eine Übersicht über Data Wrangling in Azure Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 996b91040d16a3c2b966bb1b68e83f578d42f583
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354970"
---
# <a name="what-is-data-wrangling"></a>Was ist Data Wrangling?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Organisationen müssen in der Lage sein, Ihre wichtigen Geschäftsdaten für die Datenaufbereitung und das Wrangling zu untersuchen, um eine exakte Analyse komplexer Daten zu ermöglichen, die täglich weiter anwachsen. Die Datenaufbereitung ist erforderlich, damit Organisationen die Daten in verschiedenen Geschäftsprozessen verwenden und die Amortisationszeit verkürzen können.

Data Factory ermöglicht Ihnen das iterative Vorbereiten von Daten ohne Code für die Cloud mithilfe von Power Query. Data Factory kann in [Power Query Online](/power-query/) integriert werden und stellt Power Query M-Funktionen als eine Pipelineaktivität bereit.

Data Factory übersetzt M-Code, der vom Power Query Online-Mashup-Editor generiert wird, in Spark-Code für die Cloudskalierungsausführung. Dazu wird M-Code in Azure Data Factory-Datenflüsse übersetzt. Das Wrangling von Daten mit Power Query und Datenflüsse sind besonders nützlich für Datentechniker oder für Datenintegratoren ohne Programmiererfahrung.

## <a name="use-cases"></a>Anwendungsfälle

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Schnelle interaktive Untersuchung und Aufbereitung von Daten

Mehrere Datentechniker und Datenintegratoren ohne Programmiererfahrung können Datasets in der Cloud interaktiv untersuchen und aufbereiten. Mit zunehmender Menge, Vielfalt und Geschwindigkeit der Daten in Data Lakes benötigen Benutzer eine effektive Möglichkeit zum Untersuchen und Aufbereiten von Datasets. Beispielsweise müssen Sie möglicherweise ein Dataset erstellen, das über alle demografischen Informationen zu neuen Kunden seit 2017 verfügt. Sie nehmen keine Zuordnung zu einem bekannten Ziel vor. Vor dem Veröffentlichen im Lake unterziehen Sie Datasets der Untersuchung, dem Wrangling und der Aufbereitung, damit sie eine Anforderung erfüllen. Wrangling wird häufig für weniger formale Analyseszenarien verwendet. Die aufbereiteten Datasets können für Downstreamtransformationen und -Machine Learning-Vorgänge verwendet werden.

### <a name="code-free-agile-data-preparation"></a>Codefreie agile Datenaufbereitung

Die Datenintegratoren ohne Programmiererfahrung verbringen mehr als 60 % ihrer Zeit mit dem Suchen nach und Aufbereiten von Daten. Sie versuchen, dies ohne Code durchzuführen, um die operative Produktivität zu verbessern. Indem Datenintegratoren ohne Programmiererfahrung die Möglichkeit erhalten, Daten mit bekannten Tools wie Power Query Online skalierbar zu erweitern, zu strukturieren und zu veröffentlichen, wird ihre Produktivität erheblich verbessert. Das Wrangling in Azure Data Factory aktiviert den vertrauten Power Query Online-Mashup-Editor, damit Datenintegratoren ohne Programmiererfahrung schnell Fehler beheben, Daten standardisieren und qualitativ hochwertige Daten zur Unterstützung von Geschäftsentscheidungen produzieren können.

### <a name="data-validation-and-exploration"></a>Datenüberprüfung und -untersuchung

Überprüfen Sie Ihre Daten visuell ohne Code, um alle Ausreißer sowie Anomalien zu entfernen und sie in eine Form zu bringen, die eine schnelle Analyse ermöglicht.

## <a name="supported-sources"></a>Unterstützte Quellen

| Connector | Datenformat | Authentifizierungsart |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet, Excel | Kontoschlüssel, Dienstprinzipal, MSI |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV, Parquet, Excel | Dienstprinzipal, MSI |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet, Excel | Kontoschlüssel, Dienstprinzipal, MSI |
| [Azure SQL-Datenbank](connector-azure-sql-database.md) | - | SQL-Authentifizierung, MSI, Dienstprinzipal |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-Authentifizierung, MSI, Dienstprinzipal |

## <a name="the-mashup-editor"></a>Mashup-Editor

Wenn Sie eine Power Query-Aktivität erstellen, werden alle Quelldatasets zu Datasetabfragen, die im Ordner **ADFResource** platziert werden. Standardmäßig zeigt die UserQuery auf die erste Datasetabfrage. Alle Transformationen sind für die UserQuery durchzuführen, da Änderungen an Datasetabfragen weder unterstützt noch beibehalten werden. Das Umbenennen, Hinzufügen und Löschen von Abfragen werden derzeit nicht unterstützt.

:::image type="content" source="media/wrangling-data-flow/editor.png" alt-text="Wrangling":::

Derzeit werden nicht alle Power Query M-Funktionen für Data Wrangling unterstützt, obwohl sie während der Erstellung verfügbar sind. Beim Erstellen von Power Query-Aktivitäten wird die folgende Fehlermeldung angezeigt, wenn eine Funktion nicht unterstützt wird:

`The Power Query Spark Runtime does not support the function`

Weitere Informationen zu unterstützten Transformationen finden Sie unter den [Data Wrangling-Funktionen in Power Query](wrangling-functions.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [ein Data Wrangling für Power Query-Mashups](wrangling-tutorial.md) erstellen.
