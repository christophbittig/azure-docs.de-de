---
title: 'Azure Synapse Analytics: Migrationsleitfaden'
description: Befolgen Sie diesen Leitfaden, um Ihre Datenbanken zu dedizierten SQL-Pools in Azure Synapse Analytics zu migrieren.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 601b31aa4941ba3a971ebbc9be03178593b5f565
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129055284"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrieren Sie ein Data Warehouse zu einem dedizierten SQL-Pool in Azure Synapse Analytics

In den folgenden Abschnitten erhalten Sie einen Überblick über die Migration einer bestehenden Data-Warehouse-Lösung zu einem dedizierten SQL-Pool in Azure Synapse Analytics.

## <a name="overview"></a>Übersicht

Vor dem Beginn der Migration sollten Sie sicherstellen, dass Azure Synapse Analytics die beste Lösung für Ihre Workload ist. Azure Synapse Analytics ist ein verteiltes System, das für die Durchführung von Analysen großer Datenmengen konzipiert wurde. Die Migration zu Azure Synapse Analytics erfordert einige Entwurfsänderungen, die zwar nicht komplex sind, aber etwas Zeit in Anspruch nehmen. Wenn Ihr Unternehmen ein professionelles Data Warehouse benötigt, sind die Vorteile den Aufwand wert. Wenn Sie jedoch nicht die Leistung von Azure Synapse Analytics benötigen, ist es kostengünstiger, [SQL Server](/sql/sql-server/) oder [Azure SQL-Datenbank](../../azure-sql/index.yml) zu verwenden.

Ziehen Sie Azure Synapse Analytics in Betracht, wenn Folgendes gegeben ist:

- Sie verfügen über mindestens ein Terabyte an Daten.
- Sie planen Analysen großer Mengen von Daten.
- Sie müssen Compute- und Speicherressourcen skalieren können.
- Sie möchten Kosten sparen, indem Sie Computeressourcen anhalten, wenn Sie sie nicht benötigen.

Ziehen Sie für Betriebsworkloads (OLTP) mit folgenden Eigenschaften andere Optionen als Azure Synapse Analytics in Betracht:

- Sehr häufige Lese- und Schreibvorgänge
- Große Anzahl an Singleton Select-Anweisungen
- Große Mengen einzelner Zeileneinfügungen
- Zeilenweise Verarbeitung ist erforderlich
- Nicht kompatible Formate (z. B. JSON und XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Eine der größten Hürden für Kunden ist die Übersetzung ihrer Datenbankobjekte bei der Migration von einem System zu einem anderen. [Azure Synapse Pathway](/sql/tools/synapse-pathway/azure-synapse-pathway-overview) unterstützt Sie beim Upgrade auf eine moderne Data-Warehouse-Plattform durch Automatisierung der Objektübersetzung für Ihr bestehendes Data Warehouse. Es handelt sich dabei um ein kostenloses, intuitives und leicht zu verwendendes Tool, das die Codeübersetzung automatisiert und dadurch eine schnellere Migration zu Azure Synapse Analytics ermöglicht.

## <a name="prerequisites"></a>Voraussetzungen

# <a name="migrate-from-sql-server"></a>[Migrieren von SQL Server](#tab/migratefromSQLServer)

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, um Ihr Data Warehouse in SQL Server zu Azure Synapse Analytics zu migrieren:

- Sie verfügen über eine Data-Warehouse- oder Analytics-Workload.
- Laden Sie die neueste Version von [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061) herunter, um SQL Server-Objekte zu Azure Synapse-Objekten zu migrieren.
- Sie verfügen über einen [dedizierten SQL-Pool](../get-started-create-workspace.md) im Azure Synapse-Arbeitsbereich.

# <a name="migrate-from-netezza"></a>[Migration von Netezza](#tab/migratefromNetezza)

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, um Ihr Netezza-Data-Warehouse zu Azure Synapse Analytics zu migrieren:

- Laden Sie die neueste Version von [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061) herunter, um SQL Server-Objekte zu Azure Synapse-Objekten zu migrieren.
- Sie verfügen über einen [dedizierten SQL-Pool](../get-started-create-workspace.md) im Azure Synapse-Arbeitsbereich.

Weitere Informationen finden Sie unter [Azure Synapse Analytics-Lösungen und Migration von Netezza](/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migration von Snowflake](#tab/migratefromSnowflake)

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, um Ihr Snowflake-Data-Warehouse zu Azure Synapse Analytics zu migrieren:

- Laden Sie die neueste Version von [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061) herunter, um Snowflake-Objekte zu Azure Synapse-Objekten zu migrieren.
- Sie verfügen über einen [dedizierten SQL-Pool](../get-started-create-workspace.md) im Azure Synapse-Arbeitsbereich.

# <a name="migrate-from-oracle"></a>[Migration von Oracle](#tab/migratefromOracle)

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, um Ihr Oracle-Data-Warehouse zu Azure Synapse Analytics zu migrieren:

- Sie verfügen über eine Data-Warehouse- oder Analytics-Workload.
- Laden Sie SQL Server Migration Assistant für Oracle herunter, um Oracle-Objekte in SQL Server zu konvertieren. Weitere Informationen finden Sie unter [Migrieren von Oracle-Datenbanken zu SQL Server (OracleToSQL)](/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Laden Sie die neueste Version von [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=103061) herunter, um SQL Server-Objekte zu Azure Synapse-Objekten zu migrieren.
- Sie verfügen über einen [dedizierten SQL-Pool](../get-started-create-workspace.md) im Azure Synapse-Arbeitsbereich.

Weitere Informationen finden Sie unter [Azure Synapse Analytics-Lösungen und Migration eines Oracle-Data-Warehouse](/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Vor der Migration

Nachdem Sie die Entscheidung getroffen haben, eine vorhandene Lösung zu Azure Synapse Analytics zu migrieren, müssen Sie die Migration zu planen, bevor Sie beginnen. Ein Hauptziel der Planung besteht darin, sicherzustellen, dass Ihre Daten, Ihre Tabellenschemas und Ihr Code mit Azure Synapse Analytics kompatibel sind. Zwischen Ihrem aktuellen System und Azure Synapse Analytics bestehen gewisse Kompatibilitätsunterschiede, die Sie umgehen müssen. Darüber hinaus ist die Migration großer Datenmengen zu Azure zeitintensiv. Mithilfe sorgfältiger Planung können Sie die Datenverschiebung zu Azure beschleunigen.

Ein weiteres wichtiges Ziel der Planung besteht darin, Ihren Entwurf anzupassen, um sicherzustellen, dass Ihre Lösung die hohe Abfrageleistung von Azure Synapse Analytics optimal nutzt. Das Entwerfen von Data Warehouses für die Skalierung umfasst einzigartige Entwurfsmuster, weshalb herkömmliche Ansätze nicht immer optimal sind. Zwar können einige Anpassungen am Entwurf auch nach der Migration vorgenommen werden, allerdings spart es Ihnen Zeit, wenn Sie Änderungen frühzeitig vornehmen.

## <a name="migrate"></a>Migrieren

Eine erfolgreiche Migration erfordert, dass Sie Ihre Tabellenschemas, Ihren Code und Ihre Daten migrieren. Ausführlichere Anleitungen zu diesen Themen finden Sie in den folgenden Artikeln:

- [Tabellenentwurf in Erwägung ziehen](../sql-data-warehouse/sql-data-warehouse-tables-overview.md)
- [Codeänderung in Erwägung ziehen](../sql-data-warehouse/sql-data-warehouse-overview-develop.md#development-recommendations-and-coding-techniques)
- [Migrieren Ihrer Daten](../sql-data-warehouse/design-elt-data-loading.md)
- [Workloadverwaltung in Erwägung ziehen](../sql-data-warehouse/sql-data-warehouse-workload-management.md)

## <a name="more-resources"></a>Weitere Ressourcen

Das Customer Advisory Team hat einige hervorragende Anleitungen für Azure Synapse Analytics (zuvor Azure SQL Data Warehouse) in Form von Blogbeiträgen veröffentlicht. Weitere Informationen zur Migration finden Sie unter [Migrieren von Daten zu Azure SQL Data Warehouse in der Praxis](/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Migrationsressourcen aus echten Projekten

Weitere Unterstützung bei der Durchführung dieses Migrationsszenarios finden Sie in den folgenden Ressourcen. Sie wurden für die Unterstützung eines echten Migrationsprojekts entwickelt.

| Titel/Link                              | BESCHREIBUNG                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Data Workload Assessment Model and Tool](https://www.microsoft.com/download/details.aspx?id=103130) | Dieses Tool stellt für eine bestimmte Arbeitsauslastung Informationen zu empfohlenen optimalen Zielplattformen, zur Cloudbereitschaft und zum Korrekturbedarf für Anwendungen oder Datenbanken bereit. Es bietet eine einfache Berechnung und Berichterstellung mit nur einem Klick, die Ihnen durch einen automatisierten und einheitlichen Entscheidungsprozess für die Zielplattform dabei helfen, Bewertungen von umfangreichen Datenbeständen zu beschleunigen. |
| [Behandeln von Datencodierungsproblemen beim Laden von Daten in Azure Synapse Analytics](https://azure.microsoft.com/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Dieser Blogbeitrag bietet Einblicke in einige der Datencodierungsprobleme, die möglicherweise auftreten, wenn Sie PolyBase zum Laden von Daten in SQL Data Warehouse verwenden. Außerdem werden in diesem Artikel einige Möglichkeiten genannt, wie Sie solche Probleme lösen und die Daten erfolgreich laden können. |
| [Abrufen von Tabellengrößen in einem dedizierten SQL-Pool in Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Eine der wichtigsten Aufgaben, die ein Architekt ausführen muss, besteht darin, Metriken zu einer neuen Umgebung nach der Migration zu erhalten. Beispiele hierfür sind das Erfassen von Ladezeiten von der lokalen zur cloudbasierten Lösung und das Erfassen von PolyBase-Ladezeiten. Eine der wichtigsten dieser Aufgaben besteht darin, die Speichergröße in SQL Data Warehouse im Vergleich zur aktuellen Plattform des Kunden zu ermitteln. |
| [Hilfsprogramm zum Verschieben lokaler SQL Server-Anmeldungen zu Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Ein PowerShell-Skript erstellt ein T-SQL-Befehlsskript, um Anmeldungen und ausgewählte Datenbankbenutzer aus einer lokalen SQL Server-Instanz in einem Azure SQL-Plattform as a Service (PaaS)-Dienst neu zu erstellen. Das Tool ermöglicht die automatische Zuordnung von Windows Server Active Directory-Konten zu Azure Active Directory-Konten. Alternativ können UPN-Lookups für jede Anmeldung in der lokalen Windows Server Active Directory-Instanz durchgeführt werden. Das Tool kann optional auch native SQL Server-Anmeldeinformationen verschieben. Benutzerdefinierte Server- und Datenbankrollen sowie Rollenmitgliedschaften, Datenbankrollen und Benutzerberechtigungen werden erstellt. Eigenständige Datenbanken werden noch nicht unterstützt, und nur eine Teilmenge der möglichen SQL Server-Berechtigungen werden erstellt. Weitere Informationen finden Sie im Hilfsdokument. Das Skript enthält außerdem Kommentare mit Erläuterungen. |

Das Data SQL Engineering-Team hat diese Ressourcen entwickelt. Die Hauptanwendung dieses Teams besteht darin, die komplexe Modernisierung für Datenplattform-Migrationsprojekte auf der Azure-Datenplattform von Microsoft freizugeben und zu beschleunigen.

## <a name="videos"></a>Videos

Hier erfahren Sie, wie [Walgreens sein Einzelhandelsinventursystem](https://www.youtube.com/watch?v=86dhd8N1lH4), das Daten im Umfang von ungefähr 100 TB umfasst, in Rekordzeit von Netezza zu Azure Synapse Analytics migriert hat.
