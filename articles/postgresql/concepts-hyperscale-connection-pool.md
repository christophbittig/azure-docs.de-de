---
title: 'Erstellen von Verbindungspools – Hyperscale (Citus): Azure Database for PostgreSQL'
description: Skalieren von Verbindungen mit Clientdatenbanken
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 8331cd2f0fa0df52f550acfdac1d8d3506e415f2
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318609"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-connection-pooling"></a>Azure Database for PostgreSQL: Verbindungspooling für Hyperscale (Citus)

Das Einrichten neuer Verbindungen nimmt Zeit in Anspruch. Dies spricht gegen die meisten Anwendungen, die viele kurzlebige Verbindungen anfordern. Wir empfehlen die Verwendung eines Verbindungspoolers, um Transaktionen im Leerlauf zu verringern und vorhandene Verbindungen wiederzuverwenden. Weitere Informationen finden Sie in unserem [Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

Sie können Ihre eigene Verbindungspoolfunktion ausführen oder die von Azure verwaltete Lösung PgBouncer verwenden.

## <a name="managed-pgbouncer"></a>Verwaltete PgBouncer-Instanz

Verbindungspoollösungen wie PgBouncer ermöglichen es einer größeren Anzahl von Clients, gleichzeitig eine Verbindung mit dem Koordinatorknoten herzustellen. Anwendungen stellen eine Verbindung mit der Poollösung her, und die Poollösung leitet Befehle an die Zieldatenbank weiter.

Die Anzahl von Verbindungen, die aktiv in der Datenbank ausgeführt werden können, ändert sich nicht, wenn Clients eine Verbindung über PgBouncer herstellen. Stattdessen reiht PgBouncer über den Grenzwert hinausgehende Verbindungen in die Warteschlange ein und führt sie aus, wenn die Datenbank bereit ist.

Hyperscale (Citus) bietet jetzt für Servergruppen eine verwaltete Instanz von PgBouncer. Sie unterstützt bis zu 2.000 gleichzeitige Clientverbindungen.  Führen Sie die folgenden Schritte aus, um eine Verbindung über PgBouncer herzustellen:

1. Wechseln Sie im Azure-Portal zur Seite **Verbindungszeichenfolgen** für Ihre Servergruppe.
2. Aktivieren Sie das Kontrollkästchen **PgBouncer connection strings** (PgBouncer-Verbindungszeichenfolgen). (Die aufgelisteten Verbindungszeichenfolgen ändern sich.)

   > [!IMPORTANT]
   >
   > Wenn das Kontrollkästchen nicht vorhanden ist, ist PgBouncer noch nicht für Ihre Servergruppe aktiviert. Der verwaltete PgBouncer wird in allen [unterstützten Regionen](concepts-hyperscale-configuration-options.md#regions) eingeführt.  Nach der Aktivierung in einer Region wird er während eines [geplanten Wartungsereignisses](concepts-hyperscale-maintenance.md) in der Region vorhandenen Servergruppen hinzugefügt.

3. Aktualisieren Sie Clientanwendungen, sodass sie die neue Zeichenfolge zum Herstellen einer Verbindung verwenden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Grenzwerte und Einschränkungen](concepts-hyperscale-limits.md) von Hyperscale (Citus).
