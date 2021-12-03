---
title: Verschieben von Azure Cache for Redis-Instanzen in verschiedene Regionen
description: Informationen, wie Sie Azure Cache for Redis-Instanzen in verschiedene Regionen verschieben.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 8/27/2021
ms.openlocfilehash: 931c4aec854b2512c649570b5fe8fc24bfe11c81
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894934"
---
# <a name="move-azure-cache-for-redis-instances-to-different-regions"></a>Verschieben von Azure Cache for Redis-Instanzen in verschiedene Regionen

In diesem Artikel erfahren Sie, wie Sie Azure Cache for Redis-Instanzen in verschiedene Regionen verschieben. Sie können Ihre Ressourcen aus verschiedenen Gründen in eine andere Region verschieben:
- Um die Vorteile einer neuen Azure-Region zu nutzen.
- Um Features bereitzustellen, die nur in bestimmten Regionen verfügbar sind.
- Um interne Richtlinien- und Governanceanforderungen zu erfüllen.
- Um auf Kapazitätsplanungsanforderungen zu reagieren.

Die Dienstebene von Azure Cache for Redis, die Sie verwenden, bestimmt die Option, die für Sie am besten geeignet ist.

| Cacheebene | Optionen  | 
| ------------ |  ------- | 
| Premium | Georeplikation, Erstellen eines neuen Caches, Duales Schreiben in zwei Caches oder Exportieren und Importieren von Daten über eine RDB-Datei| 
| „Basic“ oder „Standard“ | Erstellen eines neuen Caches oder duales Schreiben in zwei Caches| 
| „Enterprise“ oder „Enterprise Flash“ | Erstellen eines neuen Caches oder Exportieren und Importieren von Daten mit einer RDB-Datei | 

## <a name="geo-replication-premium"></a>Georeplikation (Premium)

### <a name="prerequisites"></a>Voraussetzungen 

Um die Georeplikation zwischen zwei Caches zu konfigurieren, müssen die folgenden Voraussetzungen erfüllt sein:

- Für beide Caches gilt der [Premium-Tarif](cache-overview.md#service-tiers).
- Beide Caches müssen sich in demselben Azure-Abonnement befinden.
- Der sekundäre verknüpfte Cache hat entweder die gleiche Größe oder ist größer als der primäre verknüpfte Cache.
- Beide Caches sind bereits vorhanden und werden ausgeführt.

### <a name="prepare"></a>Vorbereiten

Um Ihre Cache-Instanz in eine andere Region zu verschieben, müssen Sie eine [zweite Premium-Cacheinstanz in der gewünschten Region erstellen](quickstart-create-redis.md). Sobald beide Caches ausgeführt werden, können Sie die Georeplikation zwischen den beiden Cache-Instanzen einrichten. 

> [!NOTE]
> Die Datenübertragung zwischen Azure-Regionen wird mit den Standard[bandbreitensätzen](https://azure.microsoft.com/pricing/details/bandwidth/) abgerechnet.

Einige Funktionen werden für die Georeplikation nicht unterstützt:

- Für die Georeplikation wird Zonenredundanz nicht unterstützt.
- Für die Georeplikation wird Persistenz nicht unterstützt.

Bedingungen für die Unterstützung von Georeplikation:

- Das Clustering wird unterstützt, wenn für beide Caches das Clustering aktiviert ist und jeweils die gleiche Anzahl von Shards vorhanden ist.
- Caches in unterschiedlichen VNets werden mit Einschränkungen unterstützt. Weitere Informationen finden Sie unter [Kann ich die Georeplikation bei meinen Caches in einem VNet verwenden?](cache-how-to-geo-replication.md#can-i-use-geo-replication-with-my-caches-in-a-vnet).

Nach der Konfiguration der Georeplikation gelten folgende Einschränkungen für Ihr verknüpftes Cachepaar:

- Der sekundäre verknüpfte Cache ist schreibgeschützt. Sie können daraus lesen, aber Sie können keine Daten darin schreiben. 
    - Falls Sie Daten aus der sekundären Geoinstanz lesen möchten: Wenn zwischen der primären und der sekundären Geoinstanz eine vollständige Datensynchronisierung stattfindet (z. B. bei einer Aktualisierung der primären oder sekundären Geoinstanz sowie auch in einigen Neustartszenarien), werden von der sekundären Geoinstanz bei jedem für sie ausgeführten Redis-Vorgang Fehler ausgelöst, bis die vollständige Datensynchronisierung zwischen der primären und der sekundären Geoinstanz abgeschlossen ist.
    - Anwendungen, die Daten aus der sekundären Geoinstanz lesen, müssen so gestaltet sein, dass sie auf die primäre Geoinstanz ausweichen, wenn von der sekundären Geoinstanz solche Fehler ausgelöst werden.
- Alle Daten, die vor dem Hinzufügen der Verknüpfung im sekundären verknüpften Cache enthalten waren, werden entfernt. Wenn die Georeplikation aber später entfernt wird, verbleiben die replizierten Daten im sekundären verknüpften Cache.
- Sie können keinen dieser Caches [skalieren](cache-how-to-scale.md), während die Caches verknüpft sind.
- Sie können die [Anzahl von Shards nicht ändern](cache-how-to-premium-clustering.md), wenn für den Cache das Clustering aktiviert ist.
- Für keinen der Caches kann Persistenz aktiviert werden.
- Der [Export](cache-how-to-import-export-data.md#export) aus den Caches ist möglich.
- Es ist nicht möglich, einen [Import](cache-how-to-import-export-data.md#import) in den sekundären verknüpften Cache durchzuführen.
- Sie können die verknüpften Caches oder die Ressourcengruppe, in denen diese enthalten sind, erst löschen, nachdem Sie die Verknüpfung der Caches aufgehoben haben. Weitere Informationen finden Sie unter [Warum ist bei dem Versuch, meinen verknüpften Cache zu löschen, ein Fehler beim Vorgang aufgetreten?](cache-how-to-geo-replication.md#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Wenn sich die Caches in unterschiedlichen Regionen befinden, fallen für die Daten, die zwischen Regionen verschoben werden, Kosten für ausgehenden Netzwerkdatenverkehr an. Weitere Informationen finden Sie unter [Wie viel kostet die Replikation meiner Daten über verschiedene Azure-Regionen hinweg?](cache-how-to-geo-replication.md#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Zwischen dem primären und sekundären verknüpften Cache wird kein automatisches Failover ausgeführt. Weitere Informationen und Informationen zum Ausführen eines Failovers für eine Clientanwendung finden Sie unter [Wie funktioniert ein Failover zum sekundären verknüpften Cache?](cache-how-to-geo-replication.md#how-does-failing-over-to-the-secondary-linked-cache-work).

### <a name="move"></a>Move

1. Klicken Sie zum Verknüpfen von zwei Caches für die Georeplikation zuerst im Menü „Ressource“ des Caches, der als primärer verknüpfter Cache verwendet werden soll, auf **Georeplikation**. Klicken Sie anschließend links unter **Georeplikation** auf **Link für Cachereplikation hinzufügen**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-menu.png" alt-text="Hinzufügen einer Verknüpfung":::

1. Klicken Sie in der Liste **Kompatible Caches** auf den Namen des gewünschten sekundären Caches. Wenn der sekundäre Cache nicht in der Liste angezeigt wird, sollten Sie sicherstellen, dass die [Voraussetzungen für die Georeplikation](#prerequisites) für den sekundären Cache erfüllt sind. Klicken Sie zum Filtern der Caches nach Region auf der Karte auf die Region, um nur Caches in der Liste **Kompatible Caches** anzuzeigen.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link.png" alt-text="Für die Georeplikation kompatible Caches":::

    Sie können mithilfe des Kontextmenüs auch den Verknüpfungsvorgang starten oder Details zum sekundären Cache anzeigen.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png" alt-text="Kontextmenü für die Georeplikation":::

1. Klicken Sie auf **Verknüpfen**, um zwei Caches zu verknüpfen und den Replikationsvorgang zu starten.
   
    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png" alt-text="Verknüpfen von Caches":::

### <a name="verify"></a>Überprüfung

1. Links mittels **Georeplikation** können Sie den Status des Replikationsvorgangs sehen.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-linking.png" alt-text="Verknüpfungsstatus":::

    Links unter **Übersicht** können Sie auch den Verknüpfungsstatus für den primären und sekundären Cache anzeigen.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-status.png" alt-text="Screenshot: Anzeigen des Verknüpfungsstatus für den primären Cache und sekundäre Caches.":::

    Nach Abschluss des Replikationsvorgangs wechselt der **Verknüpfungsstatus** zu **Erfolgreich**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-successful.png" alt-text="Cachestatus":::

    Der primäre verknüpfte Cache bleibt während des Verknüpfungsvorgangs für die Nutzung verfügbar. Der sekundäre verknüpfte Cache ist erst verfügbar, nachdem der Verknüpfungsvorgang abgeschlossen wurde.

### <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen 

Nachdem Ihr neuer Cache in der Zielregion mit allen erforderlichen Daten aufgefüllt wurde, entfernen Sie die Verknüpfung zwischen den beiden Caches, und löschen Sie die ursprüngliche Instanz.

1. Um die Verknüpfung zwischen zwei Caches zu entfernen und die Georeplikation zu beenden, klicken Sie links unter **Georeplikation** auf **Verknüpfung von Caches aufheben**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-unlink.png" alt-text="Aufheben der Verknüpfung von Caches":::

    Wenn der Vorgang zur Aufhebung der Verknüpfung abgeschlossen ist, ist der sekundäre Cache für Lese- und Schreibvorgänge verfügbar.

>[!NOTE]
>Wenn die Verknüpfung für die Georeplikation entfernt wird, bleiben die replizierten Daten aus dem primären verknüpften Cache im sekundären Cache.
>
>

2. Löschen Sie die ursprüngliche Instanz.

## <a name="create-a-new-cache-all-tiers"></a>Erstellen eines neuen Caches (alle Dienstebenen)

### <a name="prerequisites"></a>Voraussetzungen
- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)

### <a name="prepare"></a>Vorbereiten
Wenn Sie Ihre Daten während des Verschiebens nicht erhalten müssen, besteht die einfachste Möglichkeit zum Verschieben zwischen Regionen darin, in der Zielregion eine neue Cache-Instanz zu erstellen und Ihre Anwendung damit zu verbinden. Wenn Sie Redis beispielsweise als Suchcache für Datenbank-Datensätze verwenden, können Sie den Cache problemlos von Grund auf neu erstellen.

### <a name="move"></a>Move

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

Aktualisieren Sie schließlich Ihre Anwendung, damit sie die neuen Instanzen verwendet. 

### <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen 
Sobald Ihr neuer Cache in der Zielregion ausgeführt wird, löschen Sie die ursprüngliche Instanz.


## <a name="export-and-import-data-with-an-rdb-file-premium-enterprise-enterprise-flash"></a>Exportieren und Importieren von Daten mit einer RDB-Datei (Premium, Enterprise, Enterprise Flash)
Open-Source-Redis definiert einen Standardmechanismus, um eine Momentaufnahme des In-Memory-Datasets eines Caches zu erstellen und in einer Datei zu speichern. Diese als RDB bezeichnete Datei kann von einem anderen Redis-Cache gelesen werden. [Azure Cache for Redis Premium und Enterprise](cache-overview.md#service-tiers) unterstützen das Importieren von Daten in eine Cache-Instanz mittels RDB-Dateien. Sie können eine RDB-Datei verwenden, um Daten aus einem vorhandenen Cache nach Azure Cache for Redis zu übertragen.

> [!IMPORTANT]
> Das RDB-Dateiformat kann sich zwischen verschiedenen Redis-Versionen ändern und bietet möglicherweise keine Abwärtskompatibilität. Die Redis-Version des Caches, aus dem Sie exportieren, sollte mit der Version Ihrer neuen Cache-Instanz identisch oder niedriger sein.
>

### <a name="prerequisites"></a>Voraussetzungen
- Für beide Caches gilt der [Premium- oder Enterprise-Tarif](cache-overview.md#service-tiers).
- Der zweite Cache hat entweder dieselbe Cachegröße oder ist größer als der ursprüngliche Cache.
- Die Redis-Version des Caches, aus dem Sie exportieren, sollte mit der Version Ihrer neuen Cache-Instanz identisch oder niedriger sein.

### <a name="prepare"></a>Vorbereiten
Um Ihre Cache-Instanz in eine andere Region zu verschieben, müssen Sie eine [zweite Premium-Cache-Instanz](quickstart-create-redis.md) oder [eine zweite Enterprise-Cache-Instanz](quickstart-create-redis-enterprise.md) in der gewünschten Region erstellen.

### <a name="move"></a>Move
1. Weitere Informationen zum Importieren und Exportieren von Daten in bzw. aus Azure Cache for Redis finden Sie [hier](cache-how-to-import-export-data.md).

2. Aktualisieren Sie Ihre Anwendung, sodass die neue Cache-Instanz verwendet wird.

### <a name="verify"></a>Überprüfung
Sie können den Status des Importvorgangs überwachen, indem Sie die Benachrichtigungen im Azure-Portal verfolgen oder die Ereignisse im [Überwachungsprotokoll](../azure-monitor/essentials/activity-log.md) anzeigen.

### <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen 
Sobald Ihr neuer Cache in der Zielregion ausgeführt wird, löschen Sie die ursprüngliche Instanz.

## <a name="dual-write-to-two-caches-basic-standard-and-premium"></a>Duales Schreiben in zwei Caches (Basic, Standard und Premium)
Anstatt Daten direkt zwischen Caches zu verschieben, können Sie Ihre Anwendung verwenden, um Daten sowohl in einen vorhandenen als auch in einen neuen, von Ihnen eingerichteten Cache zu schreiben. Die Anwendung liest Daten anfangs weiterhin aus dem vorhandenen Cache. Wenn der neue Cache über alle notwendigen Daten verfügt, konfigurieren Sie Ihre Anwendung für den neuen Cache und nehmen den alten außer Betrieb. Ein Beispiel: Sie verwenden Redis als Sitzungsspeicher, und die Anwendungssitzungen sind sieben Tage lang gültig. Nachdem zwei Wochen lang in beide Caches geschrieben wurde, können Sie sicher sein, dass der neue Cache alle nicht abgelaufenen Sitzungsinformationen enthält. Ab diesem Zeitpunkt können Sie sich auf diesen Cache verlassen, ohne sich Gedanken um Datenverluste machen zu müssen.

### <a name="prerequisites"></a>Voraussetzungen
- Der zweite Cache hat entweder dieselbe Cachegröße oder ist größer als der ursprüngliche Cache.

### <a name="prepare"></a>Vorbereiten
Um Ihre Cache-Instanz in eine andere Region zu verschieben, müssen Sie eine [zweite Cache-Instanz in der gewünschten Region erstellen](quickstart-create-redis.md).

### <a name="move"></a>Move
Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Ändern Sie den Anwendungscode, sodass die Anwendung sowohl in die neue als auch in die ursprüngliche Cache-Instanz schreibt.

2. Verwenden Sie zum Lesen der Daten weiterhin die ursprüngliche Instanz, bis die neue Instanz ausreichend mit Daten aufgefüllt ist.

3. Aktualisieren Sie den Anwendungscode, sodass die Anwendung zum Lesen und Schreiben von Daten ausschließlich die neue Instanz verwendet.

### <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen 
Sobald Ihr neuer Cache in der Zielregion ausgeführt wird, löschen Sie die ursprüngliche Instanz.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure Cache for Redis-Features.
- [FAQs zur Georeplikation](cache-how-to-geo-replication.md#geo-replication-faq)
- [Azure Cache for Redis-Dienstebenen](cache-overview.md#service-tiers)
- [Hochverfügbarkeit für Azure Cache for Redis](cache-high-availability.md)


