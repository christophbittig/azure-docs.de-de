---
title: Migrieren von VNet-Einschleusungscaches zu Private Link-Caches
description: Erfahren Sie, wie Sie Ihre Azure Cache for Redis-VNet-Caches zu Private Link-Caches migrieren.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 8ab6acaf1ff6742db0b6dc750e39803123829742
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340177"
---
# <a name="migrate-from-vnet-injection-caches-to-private-link-caches"></a>Migrieren von VNet-Einschleusungscaches zu Private Link-Caches
In diesem Artikel werden verschiedene Ansätze zum Migrieren von in ein Azure Cache for Redis-VNet eingefügten Cache-Instanzen in Private Link-Cache-Instanzen in Azure Cache for Redis beschrieben. 

Durch [Azure Private Link](../private-link/private-link-overview.md) wird die Netzwerkarchitektur vereinfacht und die Verbindung zwischen Endpunkten in Azure geschützt. Sie können aus Ihrem virtuellen Netzwerk über einen privaten Endpunkt, dem eine private IP-Adresse in einem Subnetz innerhalb des virtuellen Netzwerks zugewiesen ist, eine Verbindung mit einer Azure Cache-Instanz herstellen. Es folgen Vorteile von Azure Private Link für Azure Cache for Redis: 

* **Flexibilität bei Tarifen**: Azure Private Link wird in allen unseren Tarifen unterstützt: Basic, Standard, Premium, Enterprise und Enterprise Flash. Im Vergleich zur VM-Einschleusung, die nur im Premium-Tarif angeboten wird.   

* **Vereinfachte NSG-Regelverwaltung (Netzwerksicherheitsgruppe)** : NSG-Regeln müssen nicht so konfiguriert werden, dass sie den Anforderungen von Azure Cache for Redis entsprechen.

* **Azure Policy-Unterstützung**: Stellen Sie sicher, dass alle Caches in Ihrer Organisation mit Private Link erstellt werden, und überwachen Sie die vorhandenen Caches Ihrer Organisation, um sicherzustellen, dass alle Caches Private Link nutzen.

## <a name="migration-options"></a>Migrationsoptionen

Sie können auf verschiedene Weise von der VNet-Einschleusung zu Private Link wechseln. Je nachdem, wo der Cache sich befindet und wie Ihre Anwendung damit interagiert, ist eine Methode vermutlich sinnvoller als die anderen. Im Folgenden werden einige der häufig verwendeten Migrationsstrategien erläutert. 

### <a name="if-youre-using-any-combination-of-geo-replication-clustering-or-arm-vnet"></a>Wenn Sie eine Kombination aus Georeplikation, Clustering oder ARM-VNet verwenden:

   | Option       | Vorteile | Nachteile |
   | ------------ | ---------- | ------------- |
   | Duale Schreibvorgänge in zwei Caches | Kein Datenverlust und keine Ausfallzeiten Unterbrechungsfreie Vorgänge im vorhandenen Cache. Einfacheres Testen des neuen Caches. | Für einen längeren Zeitraum sind zwei Caches erforderlich. | 
   | Erstellen eines neuen Caches | Die am einfachsten zu implementierende Methode. | Daten müssen im neuen Cache wieder aufgefüllt werden, was bei vielen Anwendungen möglicherweise nicht funktioniert. |
   | Exportieren und Importieren von Daten über eine RDB-Datei | Eine Datenmigration ist erforderlich. | Einige Daten könnten verloren gehen, wenn sie nach dem Generieren der RDB-Datei in den vorhandenen Cache geschrieben werden. | 
   | Programmgesteuertes Migrieren von Daten | Vollständige Kontrolle über die Verlagerung der Daten. | Erfordert benutzerdefinierten Code. | 

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Gleichzeitiges Schreiben in zwei Redis Cache-Instanzen während der Migrationsphase

Anstatt Daten direkt zwischen Caches zu verschieben, können Sie Ihre Anwendung verwenden, um Daten sowohl in einen vorhandenen als auch in einen neuen, von Ihnen eingerichteten Cache zu schreiben. Die Anwendung liest Daten anfangs weiterhin aus dem vorhandenen Cache. Wenn der neue Cache über alle notwendigen Daten verfügt, konfigurieren Sie Ihre Anwendung für den neuen Cache und nehmen den alten außer Betrieb. Ein Beispiel: Sie verwenden Redis als Sitzungsspeicher, und die Anwendungssitzungen sind sieben Tage lang gültig. Nachdem zwei Wochen lang in beide Caches geschrieben wurde, können Sie sicher sein, dass der neue Cache alle nicht abgelaufenen Sitzungsinformationen enthält. Ab diesem Zeitpunkt können Sie sich auf diesen Cache verlassen, ohne sich Gedanken um Datenverluste machen zu müssen.

Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Erstellen Sie eine neue [Azure Cache for Redis-Instanz mit privaten Endpunkten](cache-private-link.md), die genauso groß oder größer als der vorhandene Cache ist.

2. Ändern Sie den Anwendungscode, sodass die Anwendung sowohl in die neue als auch in die ursprüngliche Cache-Instanz schreibt.

3. Verwenden Sie zum Lesen der Daten weiterhin die ursprüngliche Instanz, bis die neue Instanz ausreichend mit Daten aufgefüllt ist.

4. Aktualisieren Sie den Anwendungscode, sodass die Anwendung zum Lesen und Schreiben von Daten ausschließlich die neue Instanz verwendet.

5. Löschen Sie die ursprüngliche Instanz.

### <a name="create-a-new-azure-cache-for-redis"></a>Erstellen einer neuen Azure Cache for Redis-Instanz

Dieser Ansatz ist technisch gesehen keine Migration. Wenn Datenverluste kein Problem sind, können Sie am einfachsten zu Azure Cache for Redis wechseln, indem Sie eine neue Cache-Instanz erstellen und Ihre Anwendung damit verbinden. Wenn Sie Redis beispielsweise als Suchcache für Datenbank-Datensätze verwenden, können Sie den Cache problemlos von Grund auf neu erstellen.

Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Erstellen Sie eine neue [Azure Cache for Redis-Instanz mit privaten Endpunkten](cache-private-link.md).

2. Aktualisieren Sie Ihre Anwendung, sodass die neue Instanz verwendet wird.

3. Löschen Sie die alte Redis-Instanz.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis-premium-tier-only"></a>Exportieren von Daten in eine RDB-Datei und Importieren der Datei in Azure Cache for Redis (nur Premium-Tarif)

Open-Source-Redis definiert einen Standardmechanismus, um eine Momentaufnahme des In-Memory-Datasets eines Caches zu erstellen und in einer Datei zu speichern. Diese als RDB bezeichnete Datei kann von einem anderen Redis-Cache gelesen werden. Der [Azure Cache for Redis-Premium-Tarif](cache-overview.md#service-tiers) unterstützt das Importieren von Daten in eine Cache-Instanz über RDB-Dateien. Sie können eine RDB-Datei verwenden, um Daten aus einem vorhandenen Cache nach Azure Cache for Redis zu übertragen.

> [!IMPORTANT]
> Das RDB-Dateiformat kann sich zwischen verschiedenen Redis-Versionen ändern und bietet möglicherweise keine Abwärtskompatibilität. Die Redis-Version des Caches, aus dem Sie die Daten exportieren, muss kleiner oder gleich der von Azure Cache for Redis bereitgestellten Version sein.
>

Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Erstellen Sie im Premium-Tarif eine neue [Azure Cache for Redis-Instanz mit privaten Endpunkten](cache-private-link.md), die genauso groß oder größer als der vorhandene Cache ist.

2. Speichern Sie eine Momentaufnahme des vorhandenen Redis-Caches. Sie können [Redis für die regelmäßige Speicherung von Momentaufnahmen konfigurieren](https://redis.io/topics/persistence) oder den Prozess mithilfe der Befehle [SAVE](https://redis.io/commands/save) oder [BGSAVE](https://redis.io/commands/bgsave) manuell ausführen. Die RDB-Datei erhält standardmäßig den Namen „dump.rdb“ und wird in dem Pfad gespeichert, der in der Konfigurationsdatei *redis.conf* angegeben ist.

    > [!NOTE]
    > Wenn Sie Daten innerhalb von Azure Cache for Redis migrieren, lesen Sie [diese Anweisungen zum Exportieren einer RDB-Datei](cache-how-to-import-export-data.md), oder verwenden Sie stattdessen das [PowerShell-Cmdlet für den Export](/powershell/module/azurerm.rediscache/export-azurermrediscache).
    >

3. Kopieren Sie die RDB-Datei in ein Azure-Speicherkonto in der Region, in der sich Ihr neuer Cache befindet. Hierfür können Sie AzCopy verwenden.

4. Importieren Sie die RDB-Datei mit diesen [Importanweisungen](cache-how-to-import-export-data.md) oder dem [PowerShell-Cmdlet für den Import](/powershell/module/azurerm.rediscache/import-azurermrediscache) in den neuen Cache.

5. Aktualisieren Sie Ihre Anwendung, sodass die neue Cache-Instanz verwendet wird.

### <a name="migrate-programmatically"></a>Programmgesteuertes Migrieren

Richten Sie einen benutzerdefinierten Migrationsprozess ein, indem Sie Daten programmgesteuert aus einem vorhandenen Cache lesen und in Azure Cache for Redis schreiben lassen. Dieses [Open-Source-Tool](https://github.com/deepakverma/redis-copy) kann zum Kopieren von Daten aus einer Azure Cache for Redis-Instanz in eine andere verwendet werden. Dieses Tool ist sehr nützlich, wenn Sie Daten zwischen Cache-Instanzen in verschiedenen Azure Cache-Regionen verschieben müssen. Eine [kompilierte Version](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) ist ebenfalls verfügbar. Wenn Sie selbst ein Migrationstool schreiben, kann auch der Quellcode ein nützlicher Leitfaden sein.

> [!NOTE]
> Dieses Tool wird von Microsoft nicht offiziell unterstützt. 
>

Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Erstellen Sie eine VM in der Region, in der sich der vorhandene Cache befindet. Wenn Ihr Dataset groß ist, wählen Sie eine relativ leistungsstarke VM aus, um die Kopierdauer zu verkürzen.

2. Erstellen Sie eine neue [Azure Cache for Redis-Instanz mit privaten Endpunkten](cache-private-link.md).

3. Leeren Sie den neuen Cache, um sicherzustellen, dass sich keine Daten darin befinden. Dieser Schritt ist erforderlich, weil das Kopiertool selbst keine vorhandenen Schlüssel im Zielcache überschreibt.

    > [!IMPORTANT]
    > Stellen Sie sicher, dass Sie NICHT den Quellcache leeren.
    >

4. Verwenden Sie eine Anwendung wie z. B. das oben erwähnte Open-Source-Tool, um das Kopieren der Daten vom Quell- zum Zielcache zu automatisieren. Denken Sie daran, dass der Kopiervorgang je nach Größe Ihres Datasets eine Weile dauern kann.


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Optionen zur Netzwerkisolation](cache-network-isolation.md). 
* Erfahren Sie, wie Sie [private Endpunkte für alle Azure Cache for Redis-Tarife](cache-private-link.md) konfigurieren.