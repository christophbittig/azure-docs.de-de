---
title: Verwenden des Azure Data Lake Storage Gen2-URI
description: Hier lernen Sie die URI-Syntax für den Schemabezeichner „abfs“ kennen, der den Azure Blob File System-Treiber (Hadoop-Dateisystemtreiber bei Azure Data Lake Storage Gen2) darstellt.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 9c1d861da7a312ce94bbc7bc4074037bdb27d02b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346572"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Verwenden des Azure Data Lake Storage Gen2-URI

Der mit Azure Data Lake Storage Gen2 kompatible [Hadoop-Dateisystemtreiber](https://www.aosabook.org/en/hdfs.html) ist unter seinem Schemabezeichner `abfs` (Azure Blob File System) bekannt. Wie andere Hadoop-Dateisystemtreiber verwendet auch der ABFS-Treiber ein URI-Format zur Angabe von Dateien und Verzeichnissen in einem Azure Data Lake Storage Gen2-fähigen Konto.

## <a name="uri-syntax"></a>URI-Syntax

Die URI-Syntax für Data Lake Storage Gen2 hängt davon ab, ob für Ihr Speicherkonto Data Lake Storage Gen2 als Standarddateisystem eingerichtet ist.

Wenn das zu adressierende Data Lake Storage Gen2-fähige Konto während der Kontoerstellung als Standarddateisystem **nicht festgelegt** wurde, lautet die URI-Syntax in der Kurzschreibweise wie folgt:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Schemabezeichner**: Das `abfs`-Protokoll wird als Schemabezeichner verwendet. Wenn Sie am Ende ein „s“ hinzufügen (abfs<b><i>s</i></b>), verwendet der ABFS-Hadoop-Clienttreiber <i>IMMER</i> TLS (Transport Layer Security), unabhängig von der ausgewählten Authentifizierungsmethode. Wenn Sie OAuth als Authentifizierung auswählen, verwendet der Clienttreiber immer TLS, auch wenn Sie „abfs“ anstelle von „abfss“ angeben, da OAuth ausschließlich auf der TLS-Ebene aufbaut. Wenn Sie schließlich die ältere Methode mit Speicherkontoschlüssel verwenden, interpretiert der Clienttreiber „abfs“ so, dass Sie TLS nicht verwenden möchten. 

2. **Dateisystem**: Der übergeordnete Speicherort, in dem sich die Dateien und Ordner befinden. Dies entspricht Containern in Azure Storage-Blobs.

3. **Kontoname**: Der Name, der Ihrem Speicherkonto während der Erstellung gegeben wurde.

4. **Pfade**: Eine durch Schrägstriche (`/`) getrennte Darstellung der Verzeichnisstruktur.

5. **Dateiname**: Der Name der jeweiligen Datei. Beim Verweisen auf ein Verzeichnis ist dieser Parameter optional.

Wenn jedoch das Konto, das Sie adressieren möchten, während der Kontoerstellung als Standarddateisystem festgelegt wurde, lautet die URI-Syntax in der Kurzschreibweise wie folgt:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Pfad**: Eine durch Schrägstriche (`/`) getrennte Darstellung der Verzeichnisstruktur.

2. **Dateiname**: Der Name der jeweiligen Datei.


## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
