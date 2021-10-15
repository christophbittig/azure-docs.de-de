---
title: PgBouncer - Azure Database for PostgreSQL – Flexible Server
description: Dieser Artikel bietet eine Übersicht mit der integrierten PgBouncer-Erweiterung.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: d49deec6a782e0b36f110b7c2046b89e314e4011
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387333"
---
# <a name="pgbouncer-in-azure-database-for-postgresql---flexible-server"></a>PgBouncer in Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

Azure Database for PostgreSQL: Flexible Server bietet [PgBouncer](https://github.com/pgbouncer/pgbouncer) als integrierte Lösung für Verbindungspooling. Dies ist ein optionaler Dienst, der pro Datenbankserver aktiviert werden kann und sowohl mit öffentlichem als auch privatem Zugriff unterstützt wird. PgBouncer läuft in der gleichen virtuellen Maschine wie der Postgres-Datenbankserver. Postgres verwendet ein prozessbasiertes Modell für Verbindungen, was es teuer macht, viele ungenutzte Verbindungen zu unterhalten. Postgres selbst stößt also auf Ressourcenbeschränkungen, sobald der Server mehr als ein paar tausend Verbindungen hat. Der primäre Nutzen von PgBouncer ist die Verbesserung von Leerlaufverbindungen und kurzlebigen Verbindungen am Datenbankserver.

PgBouncer verwendet ein leichtgewichtiges Modell, das asynchrone E/A nutzt und nur dann tatsächliche Postgres-Verbindungen verwendet, wenn dies erforderlich ist, d. h. wenn es sich um eine offene Transaktion handelt oder wenn eine Abfrage aktiv ist. Dieses Modell kann Tausende von Verbindungen leichter mit geringem Overhead unterstützen und ermöglicht eine Skalierung auf bis zu 10.000 Verbindungen mit geringem Overhead.

Wenn aktiviert, läuft PgBouncer auf Port 6432 auf Ihrem Datenbankserver. Sie können die Datenbankverbindungskonfiguration Ihrer Anwendung so ändern, dass derselbe Hostname verwendet wird, aber der Port auf 6432 geändert wird, um PgBouncer zu verwenden und von der verbesserten Skalierung der Leerlaufverbindungen zu profitieren.

> [!Note]
> PgBouncer wird auf den Computeebenen „Universell“ und „Arbeitsspeicheroptimiert“ sowohl in Netzwerken mit öffentlichem als auch mit privatem Zugriff unterstützt. 

## <a name="enabling-and-configuring-pgbouncer"></a>Aktivieren und Konfigurieren von Protokollen

Um PgBouncer zu aktivieren, können Sie im Azure-Portal zum Blade „Server Parameter“ navigieren und nach „PgBouncer“ suchen und die Einstellung pgbouncer.enabled auf "true" ändern, damit PgBouncer aktiviert wird. Sie müssen den Computer nicht neu starten. Um jedoch andere PgBouncer-Parameter einzustellen, lesen Sie den Abschnitt „Einschränkungen“.

Sie können PgBouncer und Einstellungen mit den folgenden Parametern konfigurieren:

| Parametername             | BESCHREIBUNG | Standard | 
|----------------------|--------|-------------|
| pgbouncer.default_pool_size | Setzen Sie diesen Parameterwert auf die Anzahl der Verbindungen pro Benutzer/Datenbankpaar      | 50       | 
| pgBouncer.max_client_conn | Legen Sie diesen Parameterwert auf die höchste Anzahl von Clientverbindungen mit PgBouncer fest, die Sie unterstützen möchten.     | 5.000     | 
| pgBouncer.pool_mode | Setzen Sie diesen Parameterwert für Transaktions-Pooling auf TRANSACTION (dies ist die empfohlene Einstellung für die meisten Workloads).      | TRANSACTION     |
| pgBouncer.min_pool_size | Fügen Sie dem Pool weitere Serververbindungen hinzu, wenn diese Zahl unterschritten wird.    |   0 (Deaktiviert)   |
| pgbouncer.ignore_startup_parameters | Durch Trennzeichen getrennte Liste von Parametern, die PgBouncer ignorieren soll. Beispielsweise können Sie den Parameter `extra_float_digits` durch PgBouncer ignorieren lassen.|   |
| pgbouncer.query_wait_timeout | Abfragen dürfen maximal diese Zeit (in Sekunden) auf die Ausführung warten. Wenn die Abfrage während dieser Zeit keinem Server zugewiesen wurde, wird die Verbindung mit dem Client getrennt. | 120 s |
| pgBouncer.stats_users | Optional. Legen Sie diesen Parameterwert auf den Namen eines vorhandenen Benutzers fest, damit dieser sich bei der speziellen PgBouncer-Statistikdatenbank („PgBouncer“) anmelden kann.    |      |

Weitere Informationen zu den PgBouncer-Konfigurationen finden Sie unter [pgbouncer.ini](https://www.pgbouncer.org/config.html).

> [!Note] 
> Das Upgraden von PgBouncer wird von Azure verwaltet.

## <a name="switching-your-application-to-use-pgbouncer"></a>Umschalten Ihrer Anwendung auf die Verwendung von PgBouncer

Um PgBouncer zu verwenden, führen Sie die folgenden Schritte aus:
1. Stellen Sie eine Verbindung mit Ihrem Datenbankserver her, verwenden Sie jedoch Port **6432** anstelle des regulären Ports 5432. Vergewissern Sie sich, dass diese Verbindung funktioniert
```azurecli-interactive
psql "host=myPgServer.postgres.database.azure.com port=6432 dbname=postgres user=myUser password=myPassword sslmode=require"
```
2. Testen Sie Ihre Anwendung in einer QA-Umgebung gegen PgBouncer, um sicherzustellen, dass Sie keine Kompatibilitätsprobleme haben. Das PgBouncer-Projekt stellt eine Kompatibilitätsmatrix bereit, und es wird empfohlen, **Transaktionspooling** für die meisten Benutzer zu verwenden: https://www.PgBouncer.org/features.html#sql-feature-map-for-pooling-modes .
3. Ändern Sie Ihre Produktionsanwendung so, dass sie eine Verbindung mit Port **6432** anstelle von **5432** herstellt, und überwachen Sie alle anwendungsseitigen Fehler, die auf Kompatibilitätsprobleme hinweisen können.

> [!Note] 
> Auch wenn Sie PgBouncer aktiviert haben, können Sie über Port 5432 eine direkte Verbindung mit dem Datenbankserver herstellen, indem Sie den gleichen Hostnamen verwenden.

## <a name="pgbouncer-in-zone-redundant-high-availability"></a>PgBouncer in zonenredundanter Hochverfügbarkeit

Bei zonenredundant konfigurierten Servern mit hoher Verfügbarkeit führt der primäre Server den PgBouncer aus. Sie können sich über Port 6432 mit dem PgBouncer des Primärservers verbinden. Nach einem Failover wird der PgBouncer auf dem neu heraufgestuften Standby-Server, der der neue primäre Server ist, neu gestartet. So bleibt die Verbindungszeichenfolge Ihrer Anwendung nach dem Failover gleich. 

## <a name="using-pgbouncer-with-other-connection-pools"></a>Verwendung von PgBouncer mit anderen Verbindungspools

In einigen Fällen haben Sie möglicherweise bereits einen anwendungsseitigen Verbindungspool oder haben PgBouncer auf Ihrer Anwendungsseite eingerichtet, wie z. B. einen AKS-Seitenwagen. In diesen Fällen kann es dennoch nützlich sein, den integrierten PgBouncer zu verwenden, da er Vorteile bei der Skalierung von Verbindungen im Leerlauf bietet.

Die Verwendung eines anwendungsseitigen Pools zusammen mit PgBouncer auf dem Datenbankserver kann von Vorteil sein. Hier bietet der anwendungsseitige Pool den Vorteil einer reduzierten anfänglichen Verbindungslatenz (da der anfängliche Roundtrip zum Initialisieren der Verbindung viel schneller ist), und der datenbankseitige PgBouncer ermöglicht die Skalierung von Verbindungen im Leerlauf.

## <a name="limitations"></a>Einschränkungen
 
* PgBouncer wird derzeit nicht mit Burstable Server Compute Tier unterstützt. 
* Wenn Sie die Berechnungsschicht von "General Purpose" oder „arbeitsspeicheroptimiert“ auf „burstfähig“ ändern, verlieren Sie die PgBouncer-Funktion.
* Immer wenn der Server während Skalierungsvorgängen, HA-Failover oder einem Neustart neu gestartet wird, wird auch der PgBouncer zusammen mit der virtuellen Maschine des Servers neu gestartet. Daher müssen die vorhandenen Verbindungen erneut hergestellt werden.
* Aufgrund eines bekannten Problems zeigt das Portal nicht alle PgBouncer-Parameter an. Nachdem Sie PgBouncer aktiviert und den Parameter gespeichert haben, müssen Sie den Bildschirm „Parameter“ verlassen (z. B. durch Klicken auf „Übersicht“) und dann zur Seite „Parameter“ zurückkehren. 
* Die [SCRAM-Authentifizierung](how-to-connect-scram.md) wird nicht von PgBouncer unterstützt.
  
## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über[Netzwerkkonzepte](./concepts-networking.md)
- Flexible Server: [Übersicht](./overview.md)
