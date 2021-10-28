---
title: Wiederherstellungsoptionen mit dem MARS-Agent (Microsoft Azure Recovery Services)
description: Hier erfahren Sie mehr über die Wiederherstellungsoptionen, die Ihnen mit dem MARS-Agenten (Microsoft Azure Recovery Services) zur Verfügung stehen.
ms.reviewer: mepand
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: d2d6868c787080ddd78b574d1b20434fa27d8574
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250329"
---
# <a name="about-restore-using-the-microsoft-azure-recovery-services-mars-agent"></a>Informationen zur Wiederherstellung mithilfe des MARS-Agenten (Microsoft Azure Recovery Services) 

In diesem Artikel erfahren Sie mehr über die Wiederherstellungsoptionen, die Ihnen mit dem MARS-Agenten (Microsoft Azure Recovery Services) zur Verfügung stehen.

## <a name="before-you-begin"></a>Voraussetzungen

- Stellen Sie sicher, dass die neueste Version des [MARS-Agenten](https://aka.ms/azurebackup_agent) installiert ist.
- Stellen Sie sicher, dass die [Bandbreiteneinschränkung](backup-windows-with-mars-agent.md#enable-network-throttling) deaktiviert ist.
- Stellen Sie sicher, dass Hochgeschwindigkeitsspeicher mit ausreichend Speicherplatz für den [Agent-Cacheordner](./backup-azure-file-folder-backup-faq.yml) verfügbar ist.
- Überwachen Sie die Arbeitsspeicherressourcen und CPU-Ressourcen, und stellen Sie sicher, dass ausreichend Ressourcen für das Dekomprimieren und Entschlüsseln von Daten verfügbar sind.
- Wenn Sie das Feature **Sofortige Wiederherstellung** zum Einbinden eines Wiederherstellungspunkts als Datenträger verwenden, verwenden Sie **Robocopy** mit der Multithread-Kopieroption (/MT-Option), um Dateien effizient aus dem eingebundenen Wiederherstellungspunkt zu kopieren.

## <a name="restore-options"></a>Wiederherstellungsoptionen

Der MARS-Agent bietet mehrere Wiederherstellungsoptionen. Jede Option umfasst einzigartige Vorteile, durch die sie sich für bestimmte Szenarios eignen.

Der MARS-Agent bietet Ihnen folgende Möglichkeiten:

- **[Windows Server-Systemstatussicherung wiederherstellen](backup-azure-restore-system-state.md):** Mit dieser Option können Sie den Systemstatus in Form von Dateien anhand eines Wiederherstellungspunkts in Azure Backup wiederherstellen und diese mithilfe des Hilfsprogramms für die Windows Server-Sicherung auf die Windows Server-Instanz anwenden.  
- **[Alle gesicherten Dateien in einem Volume wiederherstellen](restore-all-files-volume-mars.md):** Mit dieser Option können Sie alle gesicherten Daten in einem Volume über den Wiederherstellungspunkt in Azure Backup wiederherstellen. Diese Option ermöglicht eine schnellere Übertragungsgeschwindigkeit (bis zu 40 MBit/s).<br>Es wird empfohlen, diese Option für die Wiederherstellung großer Datenmengen oder ganzer Volumes zu verwenden.
- **[Bestimmte gesicherte Dateien und Ordner mithilfe von PowerShell in einem Volume wiederherstellen](backup-client-automation.md#restore-data-from-azure-backup):** Wenn die Pfade zu den Dateien und Ordnern in Relation zum Stammverzeichnis des Volumes bekannt sind, können Sie diese Option verwenden, um die spezifischen Dateien und Ordner mit der schnelleren Übertragungsgeschwindigkeit der Wiederherstellung eines ganzen Volumes über einen Wiederherstellungspunkt wiederherstellen. Diese Option bietet jedoch nicht den Komfort zum Durchsuchen der Dateien und Ordner im Wiederherstellungspunkt mithilfe der Option „Sofortige Wiederherstellung“.
- **[Einzelne Dateien und Ordner mithilfe der sofortigen Wiederherstellung wiederherstellen](backup-azure-restore-windows-server.md):** Mit dieser Option können Sie schnell auf die Sicherungsdaten zugreifen, indem das Volume im Wiederherstellungspunkt als Datenträger eingebunden wird. Anschließend können Sie die Dateien und Ordner durchsuchen und kopieren. Diese Option bietet eine Kopiergeschwindigkeit von bis zu 6 MBit/s, was für die Wiederherstellung einzelner Dateien und Ordner mit einer Gesamtgröße von weniger als 80 GB geeignet ist. Nachdem die erforderlichen Dateien kopiert wurden, können Sie die Einbindung des Wiederherstellungspunkts aufheben.

## <a name="next-steps"></a>Nächste Schritte

- Weitere häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen zum MARS-Agent](backup-azure-file-folder-backup-faq.yml).
- Informationen über unterstützte Szenarios und Einschränkungen finden Sie unter [Unterstützungsmatrix für die Sicherung mit dem MARS-Agent](backup-support-matrix-mars-agent.md).
