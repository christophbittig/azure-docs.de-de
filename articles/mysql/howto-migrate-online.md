---
title: Migration mit minimaler Ausfallzeit – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie eine MySQL-Datenbank mit minimaler Downtime zu Azure Database for MySQL migrieren.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: ''
ms.date: 6/19/2021
ms.openlocfilehash: 728c1d76539eb34efe5e5fc475837810ea5a5111
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088582"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migration nach Azure Database for MySQL mit minimaler Ausfallzeit

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Mit der Datenreplikation können Sie MySQL-Migrationen zu Azure Database for MySQL mit minimaler Downtime durchführen. Dadurch wird die von der Anwendung verursachte Downtime beschränkt.

Ausführliche Informationen und Anwendungsfälle für die Migration von Datenbanken zu Azure Database for MySQL finden Sie auch im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). Dieser Leitfaden bietet wichtige Informationen zur erfolgreichen Planung und Ausführung einer MySQL-Migration zu Azure.

## <a name="overview"></a>Übersicht

Mithilfe der Datenreplikation können Sie die Quelle als primäre Instanz und das Ziel als Replikat konfigurieren, sodass neue Transaktionen kontinuierlich mit Azure synchronisiert werden, während die Anwendung weiterhin ausgeführt wird. Nachdem die Daten auf der Azure-Zielseite erfasst wurden, stoppen Sie die Anwendung für einen kurzen Moment (minimale Ausfallzeit), warten auf den letzten Datenbatch (von dem Zeitpunkt an, an dem Sie die Anwendung stoppen, bis zu dem Moment, in dem die Anwendung tatsächlich nicht mehr verfügbar ist, um neuen Datenverkehr aufzunehmen), um die Erfassung im Ziel aufzuholen. Aktualisieren Sie dann Ihre Verbindungszeichenfolge, um auf Azure zu verweisen. Danach ist Ihre Anwendung in Azure live.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
