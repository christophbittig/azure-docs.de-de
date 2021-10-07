---
title: Unterstützte Datenspeicher in Azure Data Share
description: Erfahren Sie mehr über die Datenspeicher, die zur Verwendung in Azure Data Share unterstützt werden.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 605405965e4c6bc17321401245866f9b21f3a988
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760059"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Unterstützte Datenspeicher in Azure Data Share

Azure Data Share bietet eine offene und flexible Datenfreigabe, einschließlich der Möglichkeit zur Freigabe aus und in unterschiedliche Datenspeicher. Datenanbieter können Daten aus einem Datenspeichertyp freigeben, und Datenconsumer können den Datenspeicher auswählen, in dem Daten empfangen werden sollen. 

In diesem Artikel erfahren Sie mehr über die verschiedenen Azure-Datenspeicher, die in Azure Data Share unterstützt werden. Sie erfahren auch, wie Datenanbieter und Datenconsumer verschiedene Datenspeicher kombinieren können. 

## <a name="supported-data-stores"></a>Unterstützte Datenspeicher 

Die folgende Tabelle erläutert die Datenspeicher, die von Azure Data Share unterstützt werden. 

| Datenspeicher | Freigabe basierend auf vollständigen Momentaufnahmen | Freigabe basierend auf inkrementellen Momentaufnahmen | Direkte Freigabe 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL-Datenbank |✓ | | |
| Azure Synapse Analytics (ehemals Azure SQL Data Warehouse) |✓ | | |
| Dedizierter SQL-Pool von Azure Synapse Analytics (Arbeitsbereich) |✓ | | |
| Azure-Daten-Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Datenspeicher-Unterstützungsmatrix

In Azure Data Share können Datenconsumer einen Datenspeicher für die Aufnahme von Daten auswählen. Beispielsweise können Daten, die von Azure SQL-Datenbank freigegeben werden, in Azure Data Lake Storage Gen2, Azure SQL-Datenbank oder Azure Synapse Analytics empfangen werden. Wenn Kunden eine empfangende Datenfreigabe einrichten, können sie das Format auswählen, in dem die Daten empfangen werden sollen. 

Die folgende Tabelle erläutert die Kombinationen und Optionen, aus denen Datenconsumer auswählen können, wenn sie eine Datenfreigabe akzeptieren und konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren einer Datasetzuordnung](how-to-configure-mapping.md).

| Datenspeicher | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL-Datenbank | Synapse Analytics (früher SQL Data Warehouse) | Dedizierter SQL-Pool für Synapse Analytics (Arbeitsbereich) | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL-Datenbank | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (früher SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Dedizierter SQL-Pool für Synapse Analytics (Arbeitsbereich) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Freigeben über ein Speicherkonto
Azure Data Share unterstützt die Freigabe von Dateien, Ordnern und Dateisystemen aus Azure Data Lake Storage Gen1 und Azure Data Lake Storage Gen2. Darüber hinaus wird die Freigabe von Blobs, Ordnern und Containern aus Azure Blob Storage unterstützt. Sie können Block-, Anfüge- oder Seitenblobs freigeben, die als Blockblobs empfangen werden.

Wenn Dateisysteme, Container oder Ordner in einer auf Momentaufnahmen basierenden Freigabe freigegeben werden, können Datenconsumer wahlweise eine vollständige Kopie der Freigabedaten erstellen. Alternativ dazu kann die Funktion für inkrementelle Momentaufnahmen verwendet werden, um nur neue oder aktualisierte Dateien zu kopieren. 

Eine inkrementelle Momentaufnahme basiert auf dem Zeitpunkt der letzten Änderung der Dateien. Vorhandene Dateien mit denselben Namen wie Dateien in den empfangenen Daten werden in einer Momentaufnahme überschrieben. Aus der Quelle gelöschte Dateien werden im Ziel nicht gelöscht. 

Weitere Informationen finden Sie unter [Freigeben und Empfangen von Daten aus Azure Blob Storage und Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Freigeben über eine SQL-basierte Quelle
Azure Data Share unterstützt die Freigabe von Tabellen oder Sichten aus Azure SQL-Datenbank und Azure Synapse Analytics (früher Azure SQL Data Warehouse). Auch das Freigeben von Tabellen aus einem dedizierten SQL-Pool für Azure Synapse Analytics (Arbeitsbereich) wird unterstützt. Die Freigabe aus einem serverlosen SQL-Pool für Azure Synapse Analytics (Arbeitsbereich) wird derzeit nicht unterstützt. 

Datenconsumer können die Daten wahlweise in Azure Data Lake Storage Gen2 oder Azure Blob Storage als CSV- oder Parquet-Datei akzeptieren. Sie können Daten auch als Tabellen für Azure SQL-Datenbank und Azure Synapse Analytics akzeptieren.

Wenn Consumer Daten in Azure Data Lake Storage Gen2 oder Azure Blob Storage akzeptieren, überschreiben vollständige Momentaufnahmen den Inhalt der Zieldatei, sofern die Datei bereits vorhanden ist. Wenn Daten in einer Tabelle empfangen werden und die Zieltabelle noch nicht vorhanden ist, erstellt Azure Data Share anhand des Quellschemas eine SQL-Tabelle. Wenn bereits eine Zieltabelle vorhanden ist, die denselben Namen aufweist, wird sie gelöscht und mit der letzten vollständigen Momentaufnahme überschrieben. Inkrementelle Momentaufnahmen werden derzeit nicht unterstützt.

Weitere Informationen finden Sie unter [Freigeben und Empfangen von Daten aus Azure SQL-Datenbank und Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Freigeben aus Data Explorer
Azure Data Share unterstützt die Möglichkeit zur direkten Freigabe von Datenbanken über Azure Data Explorer-Cluster. Eine Freigabe durch einen Datenanbieter kann auf der Ebene der Datenbank oder des Clusters erfolgen. Wenn Sie die Data Share-API zum Freigeben von Daten verwenden, können Sie auch bestimmte Tabellen freigeben.  

Bei der Freigabe auf Datenbankebene können Datenconsumer nur auf die Datenbanken zugreifen, die vom Datenanbieter freigegeben wurden. Wenn ein Anbieter Daten auf Clusterebene freigegeben hat, können Datenconsumer auf alle Datenbanken im Cluster des Anbieters zugreifen, einschließlich zukünftiger Datenbanken, die vom Datenanbieter erstellt werden.

Für den Zugriff auf freigegebene Datenbanken benötigen Datenconsumer einen eigenen Azure Data Explorer-Cluster. Der Cluster muss sich im gleichen Azure-Rechenzentrum befinden wie der Azure Data Explorer-Cluster des Datenanbieters. 

Beim Herstellen einer Freigabebeziehung erstellt Azure Data Share eine symbolische Verknüpfung zwischen den Clustern des Anbieters und des Consumers. Daten, die im Batchmodus in den Quellcluster übertragen werden, werden innerhalb weniger Minuten im Zielcluster angezeigt.

Weitere Informationen finden Sie unter [Verwenden von Azure Data Share zum Freigeben von Daten für Azure Data Explorer](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Freigeben von Daten finden Sie im Tutorial zum [Freigeben Ihrer Daten](share-your-data.md).
