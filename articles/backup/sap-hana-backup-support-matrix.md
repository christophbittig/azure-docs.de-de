---
title: Unterstützungsmatrix für SAP HANA-Sicherungen
description: Dieser Artikel enthält Informationen zu den unterstützten Szenarien und zu Einschränkungen beim Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern unter Verwendung von Azure Backup.
ms.topic: conceptual
ms.date: 11/10/2021
ms.custom: references_regions
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 310fc7ba4753656015c15001b23e5a953aa76adf
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488447"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Unterstützungsmatrix für die Sicherung von SAP HANA-Datenbanken auf virtuellen Azure-Computern

Azure Backup unterstützt die Sicherung von SAP HANA-Datenbanken in Azure. In diesem Artikel werden die unterstützten Szenarien sowie Einschränkungen beim Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern unter Verwendung von Azure Backup zusammengefasst.

> [!NOTE]
> Die Häufigkeit von Protokollsicherungen kann nun auf einen Mindestwert von 15 Minuten festgelegt werden. Protokollsicherungen werden erst nach dem erfolgreichen Abschluss einer vollständigen Sicherung der Datenbank übertragen.

## <a name="scenario-support"></a>Unterstützung von Szenarien

| **Szenario**               | **Unterstützte Konfigurationen**                                | **Nicht unterstützte Konfigurationen**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | Nur SAP HANA-Ausführung auf virtuellen Azure-Computern unter Linux                    | SAP HANA (große Instanzen)                                   |
| **Regionen**                   | **GA:**<br> **Nord- und Südamerika** – USA, Mitte; USA, Osten 2; USA, Osten; USA, Norden-Mitte; USA, Süden-Mitte; USA, Westen 2; USA, Westen 3; USA, Westen-Mitte; USA, Westen; Kanada, Mitte; Kanada, Osten; Brasilien, Süden <br> **Asien-Pazifik** – Australien, Mitte; Australien, Mitte 2; Australien, Osten; Australien, Südosten; Japan, Osten; Japan, Westen; Südkorea, Mitte; Südkorea, Süden; Asien, Osten; Asien, Südosten; Indien, Mitte; Indien, Süden; Indien, Westen; China, Osten; China, Norden; China Osten 2; China, Norden 2 <br> **Europa** – Europa, Westen; Europa, Norden; Frankreich, Mitte; Vereinigtes Königreich, Süden; Vereinigtes Königreich, Westen; Deutschland, Norden; Deutschland, Westen-Mitte; Schweiz, Norden; Schweiz, Westen; Schweiz, Norden; Norwegen, Osten; Norwegen, Westen <br> **Afrika/Naher Osten** – Südafrika, Norden; Südafrika, Westen; VAE, Norden; VAE, Mitte  <BR>  **Azure Government-Regionen** | Frankreich, Süden; Deutschland, Mitte; Deutschland, Nordosten; US Gov IOWA |
| **Betriebssystemversionen**            | SLES 12 mit SP2, SP3, SP4 und SP5; SLES 15 mit SP0, SP1, SP2 und SP3 <br><br>  RHEL 7.4, 7.6, 7.7, 7.9, 8.1, 8.2 und 8.4                |                                             |
| **HANA-Versionen**          | SDC unter HANA 1.x, MDC unter HANA 2.x SPS04, SPS05 Rev <= 55 (auch für Szenarien mit aktivierter Verschlüsselung überprüft)      |                                                            |
| **Verschlüsselung** | SSLEnforce, HANA-Datenverschlüsselung |            |
| **HANA-Bereitstellungen**       | SAP HANA auf einem einzelnen virtuellen Azure-Computer: nur zentrales Hochskalieren. <br><br> Bei Bereitstellungen mit hoher Verfügbarkeit werden beide Knoten auf den beiden verschiedenen Computern als einzelne Knoten mit separaten Datenketten behandelt.               | Horizontales Skalieren <br><br> Bei Bereitstellungen mit hoher Verfügbarkeit wird kein automatisches Failover der Sicherung auf den sekundären Knoten ausgeführt. Das Konfigurieren der Sicherung sollte für jeden Knoten separat durchgeführt werden.                                           |
| **HANA-Instanzen**         | Eine einzelne SAP HANA-Instanz auf einem einzelnen virtuellen Azure-Computer: nur Hochskalieren | Mehrere SAP HANA-Instanzen auf einem einzelnen virtuellen Computer. Sie können nur eine dieser mehreren Instanzen gleichzeitig schützen.                  |
| **HANA-Datenbanktypen**    | Container mit Einzeldatenbank (Single Database Container, SDC) unter 1.x, Container mit mehreren Datenbanken (Multi-Database Container, MDC) unter 2.x | MDC unter HANA 1.x                                              |
| **HANA-Datenbankgröße**     | HANA-Datenbanken mit einer Größe von <= 8 TB (dies ist nicht die Arbeitsspeichergröße des HANA-Systems)               |                                                              |
| **Sicherungstypen**           | Vollständig, Differenziell, Inkrementell und Protokollsicherungen                          |  Momentaufnahmen                                       |
| **Wiederherstellungstypen**          | Informationen zu den unterstützten Wiederherstellungstypen finden Sie im SAP HANA-Hinweis [1642148](https://launchpad.support.sap.com/#/notes/1642148). |                                                              |
| **Grenzwerte für Sicherungen**          | Vollständige Sicherungen bis zu einer Größe von 8 TB pro SAP HANA-Instanz (weicher Grenzwert)         |                                                              |
| **Anzahl vollständiger Sicherungen pro Tag**     |   Eine geplante Sicherung.  <br><br>   Drei bedarfsgesteuerte Sicherungen. <br><br> Es wird empfohlen, nicht mehr als drei Sicherungen pro Tag auszulösen. Um jedoch Wiederholungen von Benutzern bei fehlgeschlagenen Versuchen zuzulassen, wird die harte Grenze für bedarfsorientierte Sicherungen auf neun Versuche festgelegt.  |
| **Besondere Konfigurationen** |                                                              | SAP HANA + Dynamic Tiering <br>  Klonen über LaMa        |

------

>[!NOTE]
>Azure Backup passt Änderungen an der Sommer- oder Winterzeit bei der Sicherung einer auf einem virtuellen Azure-Computer ausgeführten SAP HANA-Datenbank nicht automatisch an.
>
>Ändern Sie die Richtlinie nach Bedarf manuell.

> [!NOTE]
> Sie können jetzt [Sicherungs- und Wiederherstellungsaufträge](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) (auf demselben Computer) überwachen, die von nativen HANA-Clients (SAP HANA Studio/Cockpit/DBA Cockpit) im Azure-Portal ausgelöst wurden.

## <a name="next-steps"></a>Nächste Schritte

* [Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern](./backup-azure-sap-hana-database.md)
* Erfahren Sie, wie Sie [nur auf virtuellen Azure-Computern ausgeführte SAP HANA-Datenbanken wiederherstellen können](./sap-hana-db-restore.md).
* [Verwalten und Überwachen gesicherter SAP HANA-Datenbanken](sap-hana-db-manage.md)
* [Behandeln von Problemen beim Sichern von SAP HANA-Datenbanken in Azure](./backup-azure-sap-hana-database-troubleshoot.md)
