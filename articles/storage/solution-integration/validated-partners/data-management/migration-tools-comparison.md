---
title: 'Vergleich von Azure Storage-Migrationstools: Unstrukturierte Daten'
description: Grundlegende Funktionen und Vergleich zwischen Tools für die Migration von unstrukturierten Daten
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 08/04/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: d266f059869bb0f25df10dcc4fad317d3d3da7c3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426704"
---
# <a name="comparison-matrix"></a>Vergleichsmatrix

In der folgenden Vergleichsmatrix sind die grundlegenden Funktionen der unterschiedlichen Tools aufgeführt, die für die Migration von unstrukturierten Daten genutzt werden können. 

## <a name="supported-azure-services"></a>Unterstützte Azure-Dienste

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Lösungsname**  | [Azure-Dateisynchronisierung](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Datenmobilität und Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Datenverwaltung](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files-Unterstützung (alle Tarife)** | Ja                          | Ja                      | Ja            | Ja                            |
| **Azure NetApp Files-Unterstützung**      | Nein                           | Ja                      | Ja            | Ja                            |
| **Azure-Blob: Unterstützung für heiße/kalte Ebenen**   | Nein                           | Ja (per NFS-Vorschau)    | Ja            | Ja                            |
| **Unterstützung für Azure-Blob-Archivebene** | Nein                           | Nein                       | Nein             | Ja (als Migrationsziel) |
| **Azure Data Lake Storage-Unterstützung** | Nein                           | Nein                       | Nein             | Nein                             |
| **Unterstützte Quellen**      | Windows Server 2012 R2 und höher | NAS und Clouddateisysteme | Beliebige Art von NAS und S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Unterstützte Protokolle (Quelle/Ziel)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösungsname**   | [Azure-Dateisynchronisierung](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Datenmobilität und Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Datenverwaltung](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Ja | Ja | Ja | Ja |
| **SMB 3.0**       | Ja | Ja | Ja | Ja |
| **SMB 3.1**       | Ja | Ja | Ja | Ja |
| **NFS v3**        | Nein  | Ja | Ja | Ja |
| **NFS v4.1**      | Nein  | Ja | Nein  | Ja |
| **Blob-REST-API** | Nein  | Nein  | Ja | Ja |
| **S3**            | Nein  | Ja | Ja | Ja |

## <a name="extended-features"></a>Erweiterte Funktionen

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Lösungsname**  | [Azure-Dateisynchronisierung](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Datenmobilität und Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Datenverwaltung](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID-/SID-Neuzuordnung**                   | Nein  | Ja                        | Ja | Nein                             |
| **Neuzuordnung der Protokoll-ACL**                | Nein  | Nein                         | Nein  | Nein                             |
| **DFS-Unterstützung**                           | Ja | Ja                        | Ja | Ja                            |
| **Unterstützung für Drosselung**                    | Ja | Ja                        | Ja | Ja                            |
| **Dateimusterausschlüsse**               | Nein  | Ja                        | Ja | Ja (per Kopierfunktion) |
| **Unterstützung für selektive Dateiattribute** | Ja | Ja                        | Ja | Ja (für erweiterte Attribute)  |
| **Löschen von Weitergaben**                   | Ja | Ja                        | Ja | Ja                            |
| **Folgen von NTFS-Verbindungen**                 | Nein  | Ja                        | Nein  | Ja                            |
| **Überschreiben von SMB-Besitzer und -Gruppenbesitzer**    | Ja | Ja                        | Ja | Nein                             |
| **Berichterstellung zur Rückverfolgbarkeit**            | Nein  | Ja                        | Nein  | Ja                            |
| **Unterstützung alternativer Datenströme**    | Nein  | Ja                        | Ja | Nein                             |
| **Planen der Migration**              | Nein  | Ja                        | Ja | Ja                            |
| **Beibehalten der ACL**                        | Ja  | Ja                        | Ja | Ja                            |
| **DACL-Unterstützung**                          | Ja | Ja                        | Ja | Ja                            |
| **SACL-Unterstützung**                          | Ja | Ja                        | Ja | Nein                             |
| **Beibehalten der Zugriffszeit**                | Ja | Ja                        | Ja | Ja                            |
| **Beibehalten der Änderungszeit**              | Ja | Ja                        | Ja | Ja                            |
| **Beibehalten der Erstellungszeit**              | Ja  | Ja                        | Ja | Ja                            |
| **Azure Data Box-Unterstützung**       | Ja | Ja                        | Nein  | Nein                             |
| **Migration von Momentaufnahmen**                | Nein  | Manuell                     | Ja | Nein                             |
| **Unterstützung von symbolischen Verknüpfungen**                 | Nein  | Ja                        | Nein  | Ja                            |
| **Unterstützung fester Links**                     | Nein  | Als separate Dateien migriert | Ja | Ja                            |
| **Unterstützung für geöffnete/gesperrte Dateien**       | Ja | Ja                        | Ja | Ja                            |
| **Inkrementelle Migration**                 | Ja | Ja                        | Ja | Ja                            |
| **Unterstützung für Wechsel**                    | Nein  | Ja                        | Ja | Nein (nur manuell)               |
| **[Weitere Features](#other-features)**         | [Link](#azure-file-sync)| [Link](#datadobi-dobimigrate) | [Link](#data-dynamics-data-mobility-and-migration) | [Link](#komprise-intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Bewertung und Berichterstellung

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösungsname**   | [Azure-Dateisynchronisierung](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Datenmobilität und Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Datenverwaltung](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Capacity**                        | Nein      | Ja | Ja | Ja            |
| **Anzahl von Dateien/Ordnern**            | Nein      | Ja | Ja | Ja            |
| **Altersverteilung in Abhängigkeit der Zeit**      | Nein      | Ja | Ja | Ja            |
| **Zugriffszeit**                     | Nein      | Ja | Ja | Ja            |
| **Änderungszeit**                   | Nein      | Ja | Ja | Ja            |
| **Erstellungszeit**                   | Nein      | Ja | Ja | Ja            |
| **Berichtsstatus pro Datei/Objekt** | Partial | Ja | Ja | Ja            |

## <a name="licensing"></a>Lizenzierung

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Lösungsname**   | [Azure-Dateisynchronisierung](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Datenmobilität und Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Datenverwaltung](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | Nicht verfügbar | Ja | Ja | Ja |
| **Azure-Verpflichtung** | Ja   | Ja | Ja | Ja |

## <a name="other-features"></a>Weitere Features

### <a name="azure-file-sync"></a>Azure-Dateisynchronisierung

- Interne Hashüberprüfung

> [!TIP]
> Die Azure-Dateisynchronisierung ist als permanente Hybridlösung für die lokale Zwischenspeicherung/Synchronisierung einer Reihe von Azure-Dateifreigaben vorgesehen. In dieser Funktion ermöglicht sie die Cloudmigration ohne Ausfallzeiten. Wenn Sie nicht planen, Ihre Azure-Dateifreigaben lokal zwischenzuspeichern, wird die Azure-Dateisynchronisierung als Migrationstool nicht empfohlen. Weitere Informationen finden Sie in der [Übersicht über die Migration von Azure-Dateifreigaben](../../../files/storage-files-migration-overview.md) oder unter den anderen in diesem Artikel beschriebenen Partnertools.

### <a name="datadobi-dobimigrate"></a>Datadobi DobiMigrate

- Vorabprüfungen vor der Migration
- Migrationsplanung
- Trockenlauf für Übernahmetest
- Erkennen von und Warnen vor Benutzeraktivität auf der Zielseite vor der Übernahme
- Richtliniengesteuerte Migrationen
- Geplante Kopieriterationen
- Konfigurierbare Optionen für die Sicherheit des Stammverzeichnisses
- Bedarfsgesteuerte Überprüfungsläufe
- Überprüfung der Einlesung von Daten auf Quelle und Ziel
- Grafischer interaktiver Workflow für die Fehlerbehandlung
- Ausschließen bestimmter Vorgänge von der Weitergabe, z. B. Lösch- und Aktualisierungsvorgänge
- Beibehalten der Zugriffszeit auf der Quelle (zusätzlich zum Ziel)
- Ausführen eines Rollbacks auf die Quelle während des Migrationswechsels
- Migrieren von ausgewählten SMB-Dateiattributen
- Bereinigen von NTFS-Sicherheitsbeschreibungen
- Überschreiben von NFSv3-Berechtigungen und Schreiben von neuen Modusbits auf das Ziel
- Konvertieren von NFSv3 POSIX-Entwurfs-ACLs in NFSv4-ACLs
- SMB 1 (CIFS)
- Support rund um die Uhr

### <a name="data-dynamics-data-mobility-and-migration"></a>Data Dynamics Data Mobility and Migration

- Hashüberprüfung

### <a name="komprise-intelligent-data-management"></a>Komprise Intelligent Data Management

- Projekt-/Verzeichnisbasierte Migrationen
- Automatische Wiederholung bei Fehlern
- Bewertung/Berichterstellung: Dateitypen, Dateigröße, projektbasiert
- Bewertung/Berichterstellung: Benutzerdefinierte metadatenbasierte Suchvorgänge
- Vollständige Lösung für die Verwaltung von Datenlebenszyklen in Bezug auf Archivierung, Replikation, Analyse
- Auf Zugriffszeit basierende Analysen auf Blob, S3-Daten
- Kennzeichnung (Tagging)
- Support rund um die Uhr
- Hashüberprüfung

*Letzte Überprüfung der Liste am 31. März 2021.*

## <a name="see-also"></a>Siehe auch

- [Übersicht über die Azure Storage-Migration](../../../common/storage-migration-overview.md)
- [Auswählen einer Azure-Lösung für die Datenübertragung](../../../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Migrieren zu Azure-Dateifreigaben](../../../files/storage-files-migration-overview.md)
- [Migrieren zu Data Lake Storage mit WANdisco LiveData Platform für Azure](../../../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [Kopieren oder Verschieben von Daten in Azure Storage mit AzCopy](../../../common/storage-use-azcopy-v10.md)
- [Migrieren von großen Datasets zu Azure Blob Storage mit AzReplicate (Beispielanwendung)](/samples/azure/azreplicate/azreplicate/)