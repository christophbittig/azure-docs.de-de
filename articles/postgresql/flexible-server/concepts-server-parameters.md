---
title: Serverparameter – Azure Database for PostgreSQL – Flexible Server
description: Beschreibung der Serverparameter in Azure Database for PostgreSQL – Flexible Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 526a06c1ffd110fa02fd3d412ab62882bd74f9fa
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387371"
---
# <a name="server-parameters-in-azure-database-for-postgresql---flexible-server"></a>Serverparameter in Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

Azure Database for PostgreSQL bietet für jeden Server einen Satz konfigurierbarer Parameter. Weitere Informationen zu Postgres-Parametern finden Sie in der [PostgreSQL-Dokumentation](https://www.postgresql.org/docs/13/config-setting.html).

## <a name="an-overview-of-postgresql-parameters"></a>Übersicht über PostgreSQL-Parameter 

Azure Database for PostgreSQL-Server werden beim Erstellen mit optimalen Standardwerten für die einzelnen Parameter vorkonfiguriert. Statische Parameter erfordern einen Serverneustart, und Parameter, die Superuserzugriff erfordern, können vom Benutzer nicht konfiguriert werden. 

Um zu überprüfen, welche Parameter angezeigt oder geändert werden können, empfiehlt es sich, die Seite Serverparameter auf dem Azure-Portal aufzurufen. Mithilfe der `ALTER DATABASE`- oder `ALTER ROLE`-Befehle können Sie Parameter auch für individuelle Benutzer oder Datenbanken konfigurieren.

>[!NOTE]
> Da Azure Database for PostgreSQL ein verwalteter Datenbankdienst ist, erhalten Benutzer keinen Host- oder Betriebssystemzugriff, um Konfigurationsdateien wie `postgresql.conf` anzuzeigen oder zu ändern. Der Inhalt der Datei wird anhand Parameteränderungen auf der Seite „Serverparameter“ automatisch aktualisiert.

:::image type="content" source="./media/concepts-server-parameters/server-parameters.png" alt-text="Serverparameter – Portal":::

Liste mit einigen der Parameter:

| Parametername             | Beschreibung |
|----------------------|--------|
| **max_connections** | Sie können max_connections in Postgres Flexible Server anpassen, wo es auf 5.000 Verbindungen gesetzt werden kann. Weitere Informationen finden Sie in der [Dokumentation zu Grenzwerten](concepts-limits.md). | 
| **shared_buffers**    | Die Einstellung shared_buffers ändert sich abhängig von der ausgewählten SKU. (Die SKU bestimmt den verfügbaren Arbeitsspeicher.) Universelle Server verfügen über 2 GB an „shared_buffers“ für zwei V-Kerne. Speicheroptimierte Server verfügen über 4 GB an „shared_buffers“ für zwei V-Kerne. Die Einstellung „shared_buffers“ wird linear (ungefähr) skaliert, wenn sich die Anzahl von V-Kernen in einem Tarif erhöht. | 
| **shared_preload_libraries** | Dieser Parameter ist für die Konfiguration mit einem vordefinierten Satz unterstützter Erweiterungen verfügbar. Beachten Sie, dass wir stets die Erweiterung `azure` (die für Wartungsaufgaben verwendet wird) sowie die Erweiterung `pg_stat_statements` laden  (Sie können den Parameter pg_stat_statements.track verwenden, um zu kontrollieren, ob die Erweiterung aktiv ist). |
| **connection_throttling** | Diese Einstellung aktiviert die temporäre Verbindungsdrosselung für einzelne IP-Adressen bei zu vielen Anmeldefehlern durch ungültige Kennwörter. |
| **require_secure_transport** | Wenn Ihre Anwendung keine SSL-Konnektivität mit dem Server unterstützt, können Sie optional den gesicherten Transport von Ihrem Client deaktivieren, indem Sie diesen Parameterwert auf `OFF` setzen. |
 
## <a name="next-steps"></a>Nächste Schritte

Informationen zu unterstützten PostgreSQL-Erweiterungen finden Sie im [Dokument zu Erweiterungen](concepts-extensions.md).
