---
title: Status des Datenbankmigrationsszenarios
titleSuffix: Azure Database Migration Service
description: Informationen zum Status von Migrationsszenarien, die in Azure Database Migration Service unterstützt werden.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: 81803facdff8012ee01b6cca0c408755c81f5233
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853907"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status von Migrationsszenarien, die in Azure Database Migration Service unterstützt werden

Azure Database Migration Service wurde zur Unterstützung verschiedener Migrationsszenarien (Quelle-Ziel-Paare) und sowohl für die Offline- (einmalig) als auch die Onlinemigration (fortlaufende Synchronisierung) konzipiert. Der in Azure Database Migration Service bereitgestellte Szenarioumfang wird im Lauf der Zeit erweitert. In regelmäßigen Abständen werden neue Szenarios hinzugefügt. In diesem Artikel werden die Migrationsszenarien, die derzeit in Azure Database Migration Service unterstützt werden, und der Status der einzelnen Szenarien (private Vorschau, öffentliche Vorschau oder allgemein verfügbar) definiert.

## <a name="offline-versus-online-migrations"></a>Offline- und Onlinemigrationen

Mit Azure Database Migration Service können Sie eine Offline- oder eine Onlinemigration durchführen. Bei Migrationen des Typs *Offline* beginnt die Ausfallzeit der Anwendung mit dem Start der Migration. Führen Sie eine Migration des Typs *Online* durch, um die Ausfallzeit auf die Zeit zu begrenzen, die bei Abschluss der Migration für die Umstellung auf die neue Umgebung erforderlich ist. Es empfiehlt sich, eine Offlinemigration zu testen, um zu ermitteln, ob die Ausfallzeit akzeptabel ist. Wenn dies nicht der Fall ist, sollten Sie eine Onlinemigration durchführen.

## <a name="migration-scenario-status"></a>Status von Migrationsszenarios

Der Status von Migrationsszenarien, die in Azure Database Migration Service unterstützt werden, kann sich im Lauf der Zeit ändern. Szenarien werden im Allgemeinen zuerst als **private Vorschau** veröffentlicht. Nach der privaten Vorschau ändert sich der Szenariostatus in **Öffentliche Vorschau**. Benutzer von Azure Database Migration Service können die in der öffentlichen Vorschau verfügbaren Migrationsszenarien direkt über die Benutzeroberfläche testen. Eine Registrierung ist nicht erforderlich.  Allerdings sind Migrationsszenarien in der öffentlichen Vorschau möglicherweise nicht in allen Regionen verfügbar, und vor der endgültigen Veröffentlichung werden gegebenenfalls weitere Änderungen vorgenommen. Nach der öffentlichen Vorschau ändert sich der Szenariostatus in **Allgemeine Verfügbarkeit**. Die allgemeine Verfügbarkeit (General Availability, GA) ist der endgültige Veröffentlichungsstatus. Der Funktionsumfang ist vollständig und für alle Benutzer zugänglich.

## <a name="migration-scenario-support"></a>Unterstützung von Migrationsszenarios

In den folgenden Tabellen sind die Migrationsszenarien aufgeführt, die bei Verwendung von Azure Database Migration Service unterstützt werden.

> [!NOTE]
> Wenn ein Szenario, das im Folgenden als unterstützt aufgeführt wird, auf der Benutzeroberfläche aber nicht angezeigt wird, wenden Sie sich an den Alias für [Azure-Datenbankmigrationen](mailto:AskAzureDatabaseMigrations@service.microsoft.com), um weitere Informationen zu erhalten.

### <a name="offline-one-time-migration-support"></a>Unterstützung der Offlinemigration (einmalig)

Die folgende Tabelle enthält die Azure Database Migration Service-Unterstützung für Offlinemigrationen.

| Ziel  | `Source` | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-Datenbank** | SQL Server | ✔ | Allgemein verfügbar |
|   | RDS SQL | ✔ | Allgemein verfügbar |
|   | Oracle | X |  |
| **Azure SQL-Datenbank MI** | SQL Server | ✔ | Allgemein verfügbar |
|   | RDS SQL | ✔ | Allgemein verfügbar |
|   | Oracle | X |   |
| **Virtueller Azure SQL-Computer** | SQL Server | ✔ | Allgemein verfügbar |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Allgemein verfügbar |
| **Azure DB for MySQL – Single Server** | MySQL | ✔ | Allgemein verfügbar  |
|   | RDS MySQL | ✔ | Allgemein verfügbar  |
|   | Azure DB for MySQL* | ✔ | Allgemein verfügbar  |
| **Azure DB for MySQL – Flexible Server** | MySQL | ✔ | Allgemein verfügbar  |
|   | RDS MySQL | ✔ | Allgemein verfügbar  |
|   | Azure DB for MySQL* | ✔ | Allgemein verfügbar  |
| **Azure DB for PostgreSQL – Einzelserver** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB for PostgreSQL – Flexible Server** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB for PostgreSQL – Hyperscale (Citus)** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>Unterstützung der Onlinemigration (fortlaufende Synchronisierung)

Die folgende Tabelle enthält die Azure Database Migration Service-Unterstützung für Onlinemigrationen.

| Ziel  | `Source` | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-Datenbank** | SQL Server | X |  |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL-Datenbank MI** | SQL Server | ✔ | Allgemein verfügbar |
|   | RDS SQL | ✔ | Allgemein verfügbar |
|   | Oracle | X |  |
| **Virtueller Azure SQL-Computer** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Allgemein verfügbar |
| **Azure-Datenbank für MySQL** | MySQL | X |  |
|   | RDS MySQL | X |  |
| **Azure DB for PostgreSQL – Einzelserver** | PostgreSQL | ✔ | Allgemein verfügbar |
|   | Azure DB for PostgreSQL – Einzelserver* | ✔ | Allgemein verfügbar |
|   | RDS PostgreSQL | ✔ | Allgemein verfügbar |
| **Azure DB for PostgreSQL – Flexible Server** | PostgreSQL | ✔ | Allgemein verfügbar |
|   | Azure DB for PostgreSQL – Einzelserver* | ✔ | Allgemein verfügbar |
|   | RDS PostgreSQL | ✔ | Allgemein verfügbar |
| **Azure DB for PostgreSQL – Hyperscale (Citus)** | PostgreSQL | ✔ | Allgemein verfügbar |
|   | RDS PostgreSQL | ✔ | Allgemein verfügbar |

> [!NOTE]
> Wenn sich Ihre Quelldatenbank bereits in Azure PaaS befindet (z. B. Azure DB for MySQL oder Azure DB for PostgreSQL), wählen Sie beim Erstellen Ihrer Migrationsaktivität die entsprechende Engine aus. Wenn Sie z. B. von Azure DB for MySQL – Single Server zu Azure DB for MySQL – Flexible Server migrieren, wählen Sie bei der Szenarioerstellung mySQL als Quell-Engine aus. Wenn Sie von Azure DB for PostgreSQL – Single Server zu Azure DB for PostgreSQL – Flexible Server migrieren, wählen Sie während der Szenarioerstellung PostgreSQL als Quell-Engine aus. 

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über Azure Database Migration Service und Informationen zur regionalen Verfügbarkeit finden Sie im Artikel [Was ist Azure Database Migration Service?](dms-overview.md).
