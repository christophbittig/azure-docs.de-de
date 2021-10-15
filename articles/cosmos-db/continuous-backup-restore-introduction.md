---
title: Fortlaufende Sicherung mit dem Feature „Zeitpunktwiederherstellung“ von Azure Cosmos DB
description: Das Feature „Zeitpunktwiederherstellung“ von Azure Cosmos DB hilft beim Wiederherstellen von Daten nach einem versehentlichen Schreib- oder Löschvorgang sowie beim Wiederherstellen von Daten in einer beliebigen Region. Erfahren Sie mehr über Preise und aktuelle Einschränkungen.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: e7d46d1680e11307eb873383e91e6e682f545549
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546537"
---
# <a name="continuous-backup-with-point-in-time-restore-in-azure-cosmos-db"></a>Fortlaufende Sicherung mit der Zeitpunktwiederherstellung von Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Das Feature „Zeitpunktwiederherstellung“ von Azure Cosmos DB unterstützt verschiedene Szenarien wie die folgenden:

* Wiederherstellung nach einem versehentlichen Schreib- oder Löschvorgang in einem Container
* Wiederherstellung eines gelöschten Kontos, einer Datenbank oder eines Containers
* Wiederherstellung in einer beliebigen Region (in der Sicherungen vorhanden waren) zum Wiederherstellungszeitpunkt

Azure Cosmos DB erstellt die Datensicherung im Hintergrund, ohne zusätzlichen bereitgestellten Durchsatz (Anforderungseinheiten, RUs) zu beanspruchen oder die Leistung und Verfügbarkeit Ihrer Datenbank zu beeinträchtigen. Fortlaufende Sicherungen werden in jeder Region durchgeführt, in der das Konto vorhanden ist. Die folgende Abbildung zeigt, wie ein Container mit Schreibregion in „USA, Westen“ und Leseregionen in „USA, Osten“ und „USA, Osten 2“ in einem Azure Blob Storage-Remotekonto in den jeweiligen Regionen gesichert wird. Standardmäßig speichert jede Region die Sicherung in lokal redundanten Speicherkonten. Wenn für die Region [Verfügbarkeitszonen](high-availability.md#availability-zone-support) aktiviert sind, wird die Sicherung in zonenredundanten Speicherkonten gespeichert.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB-Datensicherung in Azure Blob Storage" lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

Das verfügbare Zeitfenster für die Wiederherstellung (auch Aufbewahrungszeitraum genannt) ist der niedrigere der beiden folgenden Werte: *30 Tage zurück in der Vergangenheit* oder *bis zum Zeitpunkt der Ressourcenerstellung*. Der Zeitpunkt für die Wiederherstellung kann ein beliebiger Zeitstempel innerhalb des Aufbewahrungszeitraums sein.

Derzeit können Sie das Azure Cosmos DB-Konto für einen Zeitpunkt der SQL-API oder MongoDB-Inhalte in einem anderen Konto mithilfe des [Azure-Portals](restore-account-continuous-backup.md#restore-account-portal), der [Azure-Befehlszeilenschnittstelle](restore-account-continuous-backup.md#restore-account-cli) (Azure CLI), [Azure PowerShell](restore-account-continuous-backup.md#restore-account-powershell) oder [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) wiederherstellen.

## <a name="backup-storage-redundancy"></a>Redundanz für Sicherungsspeicher

Standardmäßig speichert Azure Cosmos DB Sicherungsdaten im kontinuierlichen Modus in lokal redundanten Speicherblobs. In Regionen, in denen Zonenredundanz konfiguriert ist, wird die Sicherung in zonenredundanten Speicherblobs gespeichert. Im fortlaufenden Sicherungsmodus können Sie die Art der Sicherungsspeicherredundanz nicht ändern.

## <a name="what-is-restored"></a>Was wird wiederhergestellt?

Im stabilen Zustand werden alle Mutationsvorgänge, die für das Quellkonto durchgeführt werden (dies umfasst Datenbanken, Container und Elemente), innerhalb von 100 Sekunden asynchron gesichert. Falls das Sicherungsmedium (Azure-Speicher) nicht aktiv oder verfügbar ist, werden die Mutationsvorgänge lokal gespeichert, bis das Medium wieder verfügbar ist. Anschließend wird dieser Speicher geleert, um die Beeinträchtigung von Vorgängen zu verhindern, die wiederhergestellt werden können.

Sie können auswählen, ob eine beliebige Kombination von Containern für bereitgestellten Durchsatz, eine Datenbank mit gemeinsam genutztem Durchsatz oder das gesamte Konto wiederhergestellt werden soll. Bei Verwendung der Wiederherstellungsaktion werden alle Daten und die zugehörigen Indexeigenschaften unter einem neuen Konto wiederhergestellt. Mit dem Wiederherstellungsprozess wird sichergestellt, dass Daten, die für ein Konto, eine Datenbank oder einen Container wiederhergestellt werden, bis zum angegebenen Wiederherstellungszeitpunkt konsistent sind. Die Dauer des Wiederherstellungsvorgangs hängt von der Menge der Daten ab, die wiederhergestellt werden müssen.

> [!NOTE]
> Im fortlaufenden Sicherungsmodus werden die Sicherungen in jeder Region durchgeführt, in der Ihr Azure Cosmos DB-Konto verfügbar ist. Die für jedes Regionskonto erstellten Sicherungen sind standardmäßig lokal redundant und zonenredundant, wenn für Ihr Konto das Feature [Verfügbarkeitszone](high-availability.md#availability-zone-support) für diese Region aktiviert ist. Mit der Wiederherstellungsaktion werden Daten immer in einem neuen Konto wiederhergestellt.

## <a name="what-is-not-restored"></a>Was wird nicht wiederhergestellt?

Nach der Zeitpunktwiederherstellung werden die folgenden Konfigurationen nicht wiederhergestellt:

* Firewall, VNET, Einstellungen für privaten Endpunkt
* Konsistenzeinstellungen; das Konto wird standardmäßig mit Sitzungskonsistenz wiederhergestellt  
* Regionen
* Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen

Sie können diese Konfigurationen dem wiederhergestellten Konto hinzufügen, nachdem der Wiederherstellungsvorgang abgeschlossen ist.

## <a name="restore-scenarios"></a>Wiederherstellungsszenarien

Nachfolgend sind einige der wichtigsten Szenarien für das Feature „Zeitpunktwiederherstellung“ aufgeführt. Die Szenarien [a] bis [c] veranschaulichen, wie eine Wiederherstellung ausgelöst wird, wenn der Zeitstempel für die Wiederherstellung im Voraus bekannt ist.
Es kann jedoch Szenarien geben, in denen der genaue Zeitpunkt der versehentlichen Löschung oder Beschädigung nicht bekannt ist. In den Szenarien [d] und [e] wird gezeigt, wie Sie den Zeitstempel für die Wiederherstellung mithilfe der neuen Ereignisfeed-APIs für die wiederherstellbare Datenbank oder den Container _ermitteln_.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Lebenszyklusereignisse mit Zeitstempeln für ein wiederherstellbares Konto" lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

1. **Wiederherstellen eines gelöschten Kontos**: Alle gelöschten Konten, die wiederhergestellt werden können, werden im Bereich **Wiederherstellen** angezeigt. Angenommen, *Konto A* wird bei Zeitstempel T3 gelöscht. In diesem Fall reichen der Zeitstempel direkt vor T3, Speicherort, Ressourcengruppe und Zielkontoname für die Wiederherstellung über das [Azure-Portal](restore-account-continuous-backup.md#restore-deleted-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) oder die [CLI](restore-account-continuous-backup.md#trigger-restore-cli) aus.  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Lebenszyklusereignisse mit Zeitstempeln für eine wiederherstellbare Datenbank und einen wiederherstellbaren Container" lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

2. **Wiederherstellen von Daten eines Kontos in einer bestimmten Region**: Angenommen, *Konto A* ist bei Zeitstempel T3 in den beiden Regionen *USA, Osten* und *USA, Westen* vorhanden. Falls Sie eine Kopie von Konto A in *USA, Westen* benötigen, können Sie über das [Azure-Portal](restore-account-continuous-backup.md#restore-deleted-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) oder die [CLI](restore-account-continuous-backup.md#trigger-restore-cli) eine Zeitpunktwiederherstellung mit der Region „USA, Westen“ als Zielspeicherort durchführen.

3. **Wiederherstellen nach einem versehentlichen Schreib- oder Löschvorgang in einem Container mit einem bekannten Zeitstempel für die Wiederherstellung**: Angenommen, Sie **wissen**, dass der Inhalt von *Container 1* in *Datenbank 1* bei Zeitstempel T3 versehentlich geändert wurde. In diesem Fall können Sie eine Zeitpunktwiederherstellung über das [Azure-Portal](restore-account-continuous-backup.md#restore-live-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) oder die [CLI](restore-account-continuous-backup.md#trigger-restore-cli) in ein anderes Konto bei Zeitstempel T3 durchführen, um den gewünschten Containerzustand wiederherzustellen.

4. **Wiederherstellen eines Kontos zu einem früheren Zeitpunkt vor dem versehentlichen Löschen der Datenbank**: Im [Azure-Portal](restore-account-continuous-backup.md#restore-live-account) können Sie mithilfe des Bereichs für Ereignisfeeds feststellen, wann eine Datenbank gelöscht wurde, und den Zeitpunkt für die Wiederherstellung ermitteln. Ebenso können Sie mithilfe der [Azure CLI](restore-account-continuous-backup.md#trigger-restore-cli) und [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) das Datenbanklöschungsereignis ermitteln, indem Sie den Ereignisfeed der Datenbank auflisten und dann den Wiederherstellungsbefehl mit den erforderlichen Parametern auslösen.

5. **Wiederherstellen eines Kontos zu einem früheren Zeitpunkt vor dem versehentlichen Löschen oder Ändern der Containereigenschaften**: Im [Azure-Portal](restore-account-continuous-backup.md#restore-live-account) können Sie mithilfe des Bereichs für Ereignisfeeds feststellen, wann ein Container erstellt, geändert oder gelöscht wurde, um so den Zeitpunkt für die Wiederherstellung zu ermitteln. Ebenso können Sie mithilfe der [Azure CLI](restore-account-continuous-backup.md#trigger-restore-cli) und [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) alle Containerereignisse ermitteln, indem Sie den Ereignisfeed des Containers auflisten und dann den Wiederherstellungsbefehl mit den erforderlichen Parametern auslösen.

## <a name="permissions"></a>Berechtigungen

Mit Azure Cosmos DB können Sie die Wiederherstellungsberechtigungen für ein Konto für die fortlaufende Sicherung auf eine bestimmte Rolle oder einen Prinzipal isolieren und einschränken. Der Besitzer des Kontos kann eine Wiederherstellung auslösen und anderen Prinzipalen eine Rolle zuweisen, damit diese den Wiederherstellungsvorgang ausführen können. Weitere Informationen finden Sie im Artikel zu [Berechtigungen](continuous-backup-restore-permissions.md).

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Preise

Für Azure Cosmos DB-Konten mit aktivierter fortlaufender Sicherung fallen zusätzliche monatliche Gebühren für das *Speichern der Sicherung* und das *Wiederherstellen der Daten* an. Die Wiederherstellungskosten werden bei jedem Initiieren des Wiederherstellungsvorgangs hinzugefügt. Wenn Sie ein Konto mit fortlaufender Sicherung konfigurieren, die Daten aber nicht wiederherstellen, sind in Ihrer Rechnung nur Kosten für den Sicherungsspeicher enthalten.

Das folgende Beispiel basiert auf dem Preis für ein Azure Cosmos-Konto, das in einer Region in den USA bereitgestellt wird, die keine Government-Region ist. Die Preise und die Berechnung können je nach verwendeter Region variieren. Aktuelle Preisinformationen finden Sie auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

* Für alle Konten mit aktivierter Richtlinie für die fortlaufende Sicherung fallen zusätzliche monatliche Gebühren für den Sicherungsspeicher an, die wie folgt berechnet werden:

  0,20 USD/GB * Datengröße in GB im Konto * Anzahl der Regionen

* Jeder Aufruf der Wiederherstellungs-API ist mit einer einmaligen Gebühr verbunden. Die Höhe der Gebühr ergibt sich durch die Menge der wiederhergestellten Daten und wird wie folgt berechnet:

  0,15 USD/GB * Datengröße in GB

Wenn Sie beispielsweise über 1 TB Daten in zwei Regionen verfügen, sieht die Berechnung wie folgt aus:

* Die Kosten für den Sicherungsspeicher werden als (1000 * 0,20 × 2) = 400 USD pro Monat berechnet.

* Die Kosten für die Wiederherstellung werden als (1000 * 0,15) = 150 USD pro Wiederherstellung berechnet.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Für die Zeitpunktwiederherstellungsfunktion gelten derzeit die folgenden Einschränkungen:

* Für die fortlaufende Sicherung werden nur Azure Cosmos DB-APIs für SQL und MongoDB unterstützt. Cassandra-, Table- und Gremlin-APIs werden noch nicht unterstützt.

* Azure Sovereign- und Azure Government-Cloudregionen werden noch nicht unterstützt.

* Für Konten mit kundenseitig verwalteten Schlüsseln wird die fortlaufende Sicherung noch nicht unterstützt.

* Konten für Schreibvorgänge in mehreren Regionen werden nicht unterstützt.

* Azure Synapse Link und der regelmäßige Sicherungsmodus können in demselben Datenbankkonto gleichzeitig vorhanden sein. Analysespeicherdaten sind jedoch nicht in den Sicherungen und Wiederherstellungen enthalten. Wenn Synapse Link aktiviert ist, erstellt Azure Cosmos DB weiterhin automatisch im geplanten Sicherungsintervall Sicherungen Ihrer Daten im Transaktionsspeicher. 

* Azure Synapse Link und der fortlaufende Sicherungsmodus können nicht in demselben Datenbankkonto gleichzeitig vorhanden sein. Derzeit können Datenbankkonten mit Synapse Link-Aktivierung den fortlaufenden Sicherungsmodus nicht verwenden und umgekehrt.

* Das wiederhergestellte Konto wird in derselben Region erstellt, in der sich Ihr Quellkonto befindet. Es ist für Sie nicht möglich, ein Konto in einer Region wiederherzustellen, in der das Quellkonto nicht vorhanden war.

* Das Wiederherstellungsfenster beträgt nur 30 Tage und kann nicht geändert werden.

* Die Sicherungen sind nicht automatisch georedundant. Sie müssen explizit eine weitere Region hinzufügen, um Resilienz für das Konto und die Sicherung zu erzielen.

* Während ein Wiederherstellungsvorgang läuft, sollten Sie die Richtlinien für Identity & Access Management (IAM), über die die Berechtigungen für das Konto gewährt werden, nicht ändern oder löschen und auch die VNET- oder Firewallkonfiguration nicht ändern.

* Die Azure Cosmos DB-API für SQL oder MongoDB-Konten, für die nach der Erstellung des Containers ein eindeutiger Index erstellt wird, werden in Bezug auf die fortlaufende Sicherung nicht unterstützt. Es werden nur Container unterstützt, bei denen der eindeutige Index während der anfänglichen Containererstellung erstellt wird. Für MongoDB-Konten erstellen Sie den eindeutigen Index mit [Erweiterungsbefehlen](mongodb/custom-commands.md).

* Bei der Zeitpunktwiederherstellung erfolgt die Wiederherstellung immer in einem neuen Azure Cosmos-Konto. Die Wiederherstellung für ein vorhandenes Konto wird derzeit nicht unterstützt. Falls Sie uns Feedback zur direkten Wiederherstellung zukommen lassen möchten, können Sie sich über Ihren Kundenberater an das Azure Cosmos DB-Team wenden.

* Nach der Wiederherstellung ist es möglich, dass der konsistente Index für bestimmte Sammlungen ggf. neu erstellt wird. Sie können den Status des Vorgangs für die Neuerstellung über die Eigenschaft [IndexTransformationProgress](how-to-manage-indexing-policy.md) verfolgen.

* Beim Wiederherstellungsvorgang werden alle Eigenschaften eines Containers, einschließlich TTL-Konfiguration, wiederhergestellt. Daher ist es möglich, dass die wiederhergestellten Daten sofort gelöscht werden, wenn Sie dies konfiguriert haben. Um dies zu verhindern, muss der Zeitstempel vor dem Zeitpunkt liegen, zu dem die TTL-Eigenschaften dem Container hinzugefügt wurden.

* Eindeutige Indizes können in der API für MongoDB nicht hinzugefügt oder aktualisiert werden, wenn Sie ein Konto im fortlaufenden Sicherungsmodus erstellen oder ein Konto vom periodischen in den fortlaufenden Modus migrieren.

## <a name="next-steps"></a>Nächste Schritte

* Bereitstellen der fortlaufenden Sicherung über das [Azure-Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), die [Befehlszeilenschnittstelle](provision-account-continuous-backup.md#provision-cli) oder [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)
* Wiederherstellen des Kontos für die fortlaufende Sicherung über das [Azure-Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), die [Befehlszeilenschnittstelle](restore-account-continuous-backup.md#restore-account-cli) oder [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)
* [Migrieren eines Kontos von der regelmäßigen Sicherung zur fortlaufenden Sicherung](migrate-continuous-backup.md)
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)
