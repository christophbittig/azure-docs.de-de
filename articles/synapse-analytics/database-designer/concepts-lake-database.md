---
title: Konzepte der Azure Synapse-Lake-Datenbank
description: Lernen Sie das Konzept der Lake-Datenbank kennen, und erfahren Sie, wie es zur Strukturierung von Daten beiträgt.
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 5b030bc1551bc8d21101b127c123507f08abf62c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510426"
---
# <a name="lake-database"></a>Lake-Datenbank

Die Lake-Datenbank in Azure Synapse Analytics ermöglicht es Kunden, Datenbankdesign und Metainformationen zu den gespeicherten Daten zusammenzuführen, gemeinsam mit einer Möglichkeit zur Beschreibung, wie und wo die Daten gespeichert werden sollen. Lake-Datenbanken sind die Antwort auf die Herausforderungen moderner Data Lakes, bei denen es schwierig ist, die Struktur der Daten zu verstehen.  

![Lake-Datenbanken – Übersicht](./media/concepts-lake-database/lake-database-overview.png)


## <a name="database-designer"></a>Datenbank-Designer

Der neue Datenbank-Designer bietet Ihnen die Möglichkeit, ein Datenmodell für Ihre Lake-Datenbank zu erstellen und ihr zusätzliche Informationen hinzuzufügen. Jede Entität und jedes Attribut kann beschrieben werden, um weitere Informationen über das Modell zu erhalten, das nicht nur Entitäten, sondern auch Beziehungen umfasst. Insbesondere die fehlende Möglichkeit zum Modellieren von Beziehungen stellte eine Herausforderung für die Interaktion mit einem Data Lake dar. Diese Problematik wird nun mit einem integrierten Designer gelöst, der Möglichkeiten bietet, die bislang für Datenbanken, aber nicht für einen Data Lake zur Verfügung standen. Durch die Möglichkeit, dem Modell Beschreibungen und mögliche Beispielwerte hinzuzufügen, können Benutzer bei der späteren Interaktion mit dem Modell Informationen dort abrufen, wo sie sie benötigen, um ein besseres Verständnis der Daten zu erhalten. 

## <a name="data-storage"></a>Datenspeicher 

Lake-Datenbanken verwenden einen Data Lake im Azure Storage-Konto, um die Daten der Datenbank zu speichern. Die Daten können im Parquet- oder CSV-Format gespeichert werden, und der Speicher kann mithilfe verschiedener Einstellungen optimiert werden. Jede Lake-Datenbank verwendet einen verknüpften Dienst, um den Speicherort des Stammdatenordners zu definieren. Für jede Entität werden in diesem Datenbankordner im Data Lake standardmäßig separate Ordner erstellt. Per Voreinstellung verwenden alle Tabellen in einer Lake-Datenbank dasselbe Format, aber die Formate und der Speicherort der Daten können bei Bedarf pro Entität geändert werden. 


## <a name="database-compute"></a>Datenbank-Computeressourcen

Die Lake-Datenbank wird in einem serverlosen Synapse-SQL-Pool und Apache Spark verfügbar gemacht, um Benutzern die Möglichkeit zu bieten, Speicher- von Computeressourcen zu entkoppeln. Die mit der Lake-Datenbank verknüpften Metadaten erleichtern es den verschiedenen Compute-Engines, nicht nur ein integriertes Erlebnis zu bieten, sondern auch zusätzliche Informationen (z. B. Beziehungen) zu nutzen, die ursprünglich im Data Lake nicht unterstützt wurden. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Möglichkeiten des Database Designers, indem Sie den Link unten verwenden.
- [Schnellstart: Erstellen einer Lake-Datenbank](quick-start-create-lake-database.md)
- [Konzept: Datenbankvorlagen](concepts-database-templates.md)
