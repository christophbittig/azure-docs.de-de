---
title: Ressourcennormalisierung
description: Erfahren Sie, wie Azure Purview durch Ressourcennormalisierung duplizierte Ressourcen in Ihrer Data Map verhindert.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 07/23/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 470bfa9fc111002dbb7518833d71f918df265b45
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010971"
---
# <a name="asset-normalization"></a>Ressourcennormalisierung

Beim Erfassen von Ressourcen in der Data Map von Azure Purview können verschiedene Quellen, die dieselbe Datenressource aktualisieren, ähnliche, aber leicht unterschiedliche qualifizierte Namen übermitteln. Obwohl diese qualifizierten Namen denselben Ressourcen repräsentieren, können geringfügige Unterschiede, wie z. B. ein zusätzliches Zeichen oder eine unterschiedliche Großschreibung, dazu führen, dass diese Ressourcen auf der Oberfläche unterschiedlich angezeigt werden. Um zu vermeiden, dass duplizierte Einträge gespeichert werden und beim Nutzen des Datenkatalogs Verwirrung stiften, wendet Azure Purview während der Erfassung eine Normalisierung an, um sicherzustellen, dass alle vollqualifizierten Namen desselben Entitätstyps dasselbe Format haben.

Beispiel: Sie überprüfen ein Azure-Blob mit dem qualifizierten Namen `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet`. Dieses Blob wird auch von einer Azure Data Factory-Pipeline genutzt, die dann der Ressource Herkunftsinformationen hinzufügt. Die ADF-Pipeline kann so konfiguriert werden, dass sie die Datei als `https://myAccount.file.core.windows.net//myshare/folderA/folderB/my-file.parquet` liest. Zwar ist der qualifizierte Name anders, aber diese ADF-Pipeline nutzt dasselbe Datenelement. Durch Normalisierung wird sichergestellt, dass alle Metadaten sowohl aus Azure Blob Storage als auch aus Azure Data Factory für eine einzige Ressource, `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet`, sichtbar sind.

## <a name="normalization-rules"></a>Normalisierungsregeln

Es folgen die von Azure Purview angewendeten Normalisierungsregeln.

### <a name="encode-curly-brackets"></a>Codieren geschweifter Klammern
Gilt für: Alle Ressourcen

Vorher: `https://myaccount.file.core.windows.net/myshare/{folderA}/folder{B/`

Nachher: `https://myaccount.file.core.windows.net/myshare/%7BfolderA%7D/folder%7BB/`

### <a name="trim-section-spaces"></a>Kürzen von Abschnittsräumen
Gilt für: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Data Factory, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL-Pool, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

Vorher: `https://myaccount.file.core.windows.net/myshare/  folder A/folderB   /`

Nachher: `https://myaccount.file.core.windows.net/myshare/folder A/folderB/`

### <a name="remove-hostname-spaces"></a>Entfernen von Hostnamespaces
Gilt für: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL-Pool, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

Vorher: `https://myaccount .file. core.win dows. net/myshare/folderA/folderB/`

Nachher: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="remove-square-brackets"></a>Entfernen eckiger Klammern 
Gilt für: Azure SQL-Datenbank, Azure SQL Managed Instance, Azure SQL-Pool

Vorher: `mssql://foo.database.windows.net/[bar]/dbo/[foo bar]`

Nachher: `mssql://foo.database.windows.net/bar/dbo/foo%20bar`

> [!NOTE]
> Leerzeichen zwischen zwei eckigen Klammern werden codiert

### <a name="lowercase-scheme"></a>Schema in Kleinbuchstaben
Gilt für: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL-Pool, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Amazon S3

Vorher: `HTTPS://myaccount.file.core.windows.net/myshare/folderA/folderB/`

Nachher: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-hostname"></a>Hostname in Kleinbuchstaben
Gilt für: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL-Pool, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Amazon S3

Vorher: `https://myAccount.file.Core.Windows.net/myshare/folderA/folderB/`

Nachher: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-file-extension"></a>Dateierweiterung in Kleinbuchstaben
Gilt für: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Amazon S3

Vorher: `https://myAccount.file.core.windows.net/myshare/folderA/data.TXT`

Nachher: `https://myaccount.file.core.windows.net/myshare/folderA/data.txt`

### <a name="remove-duplicate-slash"></a>Entfernen doppelter Schrägstriche
Gilt für: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Data Factory, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL-Pool, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

Vorher: `https://myAccount.file.core.windows.net//myshare/folderA////folderB/`

Nachher: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-adf-sections"></a>ADF-Abschnitte in Kleinbuchstaben
Gilt für: Azure Data Factory

Vorher: `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/fooBar/providers/Microsoft.DataFactory/factories/fooFactory/pipelines/barPipeline/activities/barFoo`

Nachher: `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/foobar/providers/microsoft.datafactory/factories/foofactory/pipelines/barpipeline/activities/barfoo`

### <a name="convert-to-adl-scheme"></a>Konvertieren in ADL-Schema
Gilt für: Azure Data Lake Storage Gen1

Vorher: `https://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

Nachher: `adl://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen eines Azure Blob Storage-Kontos](register-scan-azure-blob-storage-source.md) für die Data Map von Azure Purview. 
