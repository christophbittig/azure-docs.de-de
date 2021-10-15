---
title: Power Query-Aktivität in Azure Data Factory
description: Erfahren Sie, wie Sie die Power Query-Aktivität für Data Wrangling-Funktionen in einer Data Factory-Pipeline verwenden.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: be5c2cbe0d4f364abb68ad8b01f00ca2c8396806
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358548"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Power Query-Aktivität in Azure Data Factory

Mit der Power Query-Aktivität können Sie Power Query-Mashups erstellen und ausführen, um so Data Wrangling im großen Stil in einer Data Factory-Pipeline vorzunehmen. Sie können ein neues Power Query-Mashup über die Menüoption „Neue Ressourcen“ erstellen oder durch Hinzufügen einer Power Query-Aktivität zur Pipeline.

![Screenshot mit Power Query im Bereich der Factoryressourcen](media/data-flow/power-query-activity-1.png)

Sie können direkt im Power Query-Mashup-Editor arbeiten, um eine interaktive Datenuntersuchung vorzunehmen und dann Ihre Arbeit zu speichern. Sobald der Vorgang beendet ist, können Sie die Power Query-Aktivität übernehmen und zu einer Pipeline hinzufügen. Azure Data Factory nimmt automatisch eine horizontale Hochskalierung vor und operationalisiert das Data Wrangling mithilfe der Spark-Umgebung für Datenflüsse in Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Übersetzung in Datenflussskript

Zur Skalierung für die Power Query-Aktivität übersetzt Azure Data Factory das ```M```-Skript in ein Datenflussskript, sodass Sie Power Query im großen Stil mithilfe der Spark-Umgebung für Azure Data Factory-Datenflüsse ausführen können. Erstellen Sie den Wranglingdatenfluss mithilfe der codefreien Datenvorbereitung. Die Liste der verfügbaren Funktionen finden Sie unter [Transformationsfunktionen](wrangling-functions.md).

## <a name="settings"></a>Einstellungen

* Power Query: Wählen Sie eine vorhandene Power Query-Instanz für die Ausführung aus, oder erstellen Sie eine neue.
* Ausführen auf Azure IR: Wählen Sie eine vorhandene Azure Integration Runtime aus, um die Compute-Umgebung für Power Query zu definieren, oder erstellen Sie eine neue.
* Computetyp: Wenn Sie die Standard-Integration Runtime für die automatische Auflösung auswählen, können Sie den Computetyp auswählen, der auf das Spark-Clustercomputing für die Power Query-Ausführung angewandt werden soll.
* Anzahl der Kerne: Wenn Sie die Standard-Integration Runtime für die automatische Auflösung auswählen, können Sie die Anzahl der Kerne auswählen, die auf das Spark-Clustercomputing für die Power Query-Ausführung angewandt werden soll.

## <a name="sink"></a>Senke

Wählen Sie das Dataset aus, das Sie als Ausgangspunkt Ihrer transformierten Daten verwenden möchten, nachdem das Power Query M-Skript in Spark ausgeführt wurde. Weitere Informationen zum Konfigurieren von Senken finden Sie in der Dokumentation zu [Datenflusssenken](data-flow-sink.md).

## <a name="mapping"></a>Zuordnung

Auf der Registerkarte „Zuordnung“ können Sie die Spaltenzuordnung von der Ausgabe Ihrer Power Query-Aktivität zum Zielschema der ausgewählten Senke konfigurieren. Weitere Informationen zur Spaltenzuordnung finden Sie in der [Dokumentation zur Zuordnung von Datenflusssenken](data-flow-sink.md#field-mapping).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Data Wrangling-Konzepte mithilfe von [Power Query in Azure Data Factory](wrangling-tutorial.md).
