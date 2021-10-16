---
title: Überprüfen von Kopierprotokollen aus Importen und Exporten in Azure Import/Export | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Fehler-/Kopierprotokolle von Importen und Exporten auf Probleme beim Kopieren und Hochladen von Daten überprüfen.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 03f4cf19922f808decfd84fe0538930dee807b5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709249"
---
# <a name="review-copy-logs-from-imports-and-exports-via-azure-importexport"></a>Überprüfen von Kopierprotokollen von Importen und Exporten über Azure Import/Export
Wenn der Dienst „Microsoft Azure Import/Export“ Laufwerke im Rahmen eines Import- oder Exportauftrags verarbeitet, schreibt er Kopierprotokolldateien in das Speicherkonto, in das oder aus dem Sie Blobs importiert bzw. exportiert haben. 

Die Protokolldatei enthält ausführliche Statusinformationen zu den einzelnen importierten oder exportierten Dateien. 

Der Dienst gibt die URL für jede Kopierprotokolldatei zurück, wenn Sie den Status eines abgeschlossenen Auftrags abfragen. Weitere Informationen finden Sie unter [Abrufen von Aufträgen](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>Beispiel-URLs

Im Anschluss finden Sie Beispiel-URLs für Kopierprotokolldateien für einen Importauftrag mit zwei Laufwerken:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 <!--See [Import/Export service Log File Format](/previous-versions/azure/storage/common/storage-import-export-file-format-log) for the format of copy logs and the full list of status codes. ARCHIVED-->  

## <a name="next-steps"></a>Nächste Schritte

<!--* [Setting Up the Azure Import/Export Tool](storage-import-export-tool-setup-v1.md) ARCHIVED-->
 * [Vorbereiten von Festplatten für einen Importauftrag](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
<!--* [Repairing an import job](./storage-import-export-tool-repairing-an-import-job-v1.md)-->  
<!--* [Repairing an export job](./storage-import-export-tool-repairing-an-export-job-v1.md)-->