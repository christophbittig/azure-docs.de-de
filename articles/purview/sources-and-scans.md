---
title: Unterstützte Datenquellen und Dateitypen
description: Dieser Artikel enthält konzeptionelle Details zu den Datenquellen und Dateitypen, die in Purview unterstützt werden.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.custom: references_regions
ms.openlocfilehash: 3a06bf01ec9afa62a656eb5590a7ae283063ec88
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074226"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Unterstützte Datenquellen und Dateitypen in Azure Purview

In diesem Artikel sind die Datenquellen, Dateitypen und Überprüfungskonzepte erläutert, die in Purview unterstützt werden.

## <a name="supported-data-sources"></a>Unterstützte Datenquellen

Purview unterstützt alle [hier aufgeführten Datenquellen](purview-connector-overview.md).

## <a name="file-types-supported-for-scanning"></a>Dateitypen, die für Überprüfungen unterstützt werden

Die folgenden Dateitypen werden für Überprüfungen, Schemaextraktion und Klassifizierung unterstützt, wo dies zutrifft:

- Dateierweiterungen der unterstützten strukturierten Dateiformate: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
 > [!Note]
 > * In den Dateitypen AVRO, ORC und PARQUET unterstützt der Purview-Scanner keine komplexen Datentypen (z. B. MAP, LIST, STRUCT) für die Schemaextraktion. 
 > * Die Purview-Überprüfung unterstützt das Überprüfen von mit Snappy komprimierten PARQUET-Dateitypen auf Schemaextraktion und -klassifizierung. 
 > * Bei GZIP-Dateitypen muss die GZIP-Datei einer einzelnen enthaltenen CSV-Datei zugeordnet werden. 
 > GZIP-Dateien unterliegen System- und benutzerdefinierten Klassifizierungsregeln. Das Scannen einer GZIP-Datei, die mehreren enthaltenen Dateien oder einem anderen Dateityp als CSV zugeordnet ist, wird derzeit nicht unterstützt. 
- Dateierweiterungen der unterstützten Dokumentdateiformate: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Purview unterstützt auch benutzerdefinierte Dateierweiterungen und benutzerdefinierte Parser.

## <a name="sampling-within-a-file"></a>Sampling (Überprüfung) innerhalb einer Datei

In der Purview-Terminologie:
- L1-Überprüfung (Scan): Extrahiert grundlegende Informationen und Metadaten wie Dateiname, Dateigröße und vollqualifizierter Name
- L2-Überprüfung: Extrahiert das Schema für strukturierte Dateitypen und Datenbanktabellen
- L3-Überprüfung: Extrahiert das Schema, sofern verfügbar, und wendet System- und benutzerdefinierte Klassifizierungsregeln auf die überprüfte Datei an

Bei allen strukturierten Dateiformaten werden Dateien vom Purview-Scanner auf folgende Weise überprüft:

- Bei strukturierten Dateitypen werden bei der Überprüfung die obersten 128 Zeilen jeder Spalte oder die ersten 1 MB überprüft, je nachdem, welcher Wert kleiner ist.
- Bei Dokumentdateiformaten werden die ersten 20 MB jeder Datei überprüft.
    - Ist eine Dokumentdatei größer als 20 MB, wird sie keiner tiefen Überprüfung unterzogen (sie wird nicht klassifiziert). In diesem Fall erfasst Purview nur grundlegende Metadaten, z. B. Dateiname und vollqualifizierter Name.
- Für **tabellarische Datenquellen (SQL, Cosmos DB)** werden die obersten 128 Zeilen als Stichprobe genommen. 

## <a name="resource-set-file-sampling"></a>Ressourcensatzüberprüfung

Ein Ordner oder eine Gruppe von Partitionsdateien wird in Purview als *Ressourcensatz* erkannt, wenn der Ordner oder die Gruppe mit einer Systemressourcensatzrichtlinie oder einer benutzerdefinierten Ressourcensatzrichtlinie übereinstimmt. Wird ein Ressourcensatz erkannt, überprüft Purview jeden darin enthaltenen Ordner. Weitere Informationen zu Ressourcensätzen finden Sie [hier](concept-resource-sets.md).

Dateiüberprüfung für Ressourcensätze nach Dateitypen:

- **Dateien mit Trennzeichen (CSV, PSV, SSV, TSV)** : 1 aus 100 Dateien wird in einem Ordner oder einer Gruppe von Partitionsdateien, die als „ Ressourcensatz“' angesehen werden, überprüft (L3-Überprüfung).
- **Data Lake-Dateitypen (Parquet, Avro, Orc)** : 1 aus 18446744073709551615 (long max) Dateien wird in einem Ordner oder einer Gruppe von Partitionsdateien, die als *Ressourcensatz* angesehen werden, überprüft (L3-Überprüfung).
- **Andere strukturierte Dateitypen (JSON, XML, TXT)** : 1 aus 100 Dateien wird in einem Ordner oder einer Gruppe von Partitionsdateien, die als „ Ressourcensatz“' angesehen werden, überprüft (L3-Überprüfung).
- **SQL-Objekte und CosmosDB-Entitäten**: Jede Datei wird einer L3-Überprüfung unterzogen.
- **Dokumentdateitypen**: Jede Datei wird einer L3-Überprüfung unterzogen. Ressourcensatzmuster gelten für diese Dateitypen nicht.

## <a name="classification"></a>Klassifizierung

Alle 206 Systemklassifizierungsregeln gelten für strukturierte Dateiformate. Nur die MCE-Klassifizierungsregeln gelten für Dokumentdateitypen (nicht die nativen RegEx-Muster der Datenüberprüfung, sondern Bloomfilter-basierte Erkennung). Weitere Informationen zu den unterstützten Klassifizierungen finden Sie unter [Unterstützte Klassifizierungen in Azure Purview](supported-classifications.md).

## <a name="next-steps"></a>Nächste Schritte

- [Scans und Einnahme in Übersicht](concept-scans-and-ingestion.md)
- [Verwalten von Datenquellen in Azure Purview](manage-data-sources.md)
