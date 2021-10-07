---
title: Lesereplikate – Azure Database for MySQL
description: 'Weitere Informationen zu Lesereplikaten in Azure Database for MySQL: Auswählen von Regionen, Erstellen von Replikaten, Herstellen einer Verbindung mit Replikaten und Beenden der Replikation.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 06/17/2021
ms.custom: references_regions
ms.openlocfilehash: bb061fb11fbc770d751f60e15c81ce31c6a07440
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666324"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Lesereplikate in Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Mithilfe des Lesereplikatfeatures können Sie Daten von einem Azure Database for MySQL-Server auf einen schreibgeschützten Server replizieren. Sie können vom Quellserver auf bis zu fünf Replikate replizieren. Replikate werden asynchron mithilfe des auf der Position der nativen, binären Protokolldatei (binlog) basierenden Replikationsverfahrens der MySQL-Engine aktualisiert. Weitere Informationen zur binlog-Replikation finden Sie unter [Binary Log File Position Based Replication Configuration Overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Konfiguration der auf der Position der binären Protokolldatei basierenden Replikation – Übersicht).

Replikate sind neue Server, die Sie ähnlich wie normale Azure Database for MySQL-Server verwalten. Für jedes Lesereplikat werden Ihnen die bereitgestellten Computeressourcen in Form von virtuellen Kernen sowie der Speicher in GB/Monat in Rechnung gestellt.

Weitere Informationen zu Features und Problemen der MySQL-Replikation finden Sie in der [Dokumentation zur MySQL-Replikation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.
>

## <a name="when-to-use-a-read-replica"></a>Einsatzmöglichkeiten von Lesereplikaten

Das Feature für Lesereplikate kann die Leistung und Skalierung von leseintensiven Workloads verbessern. Leseworkloads können in den Replikaten isoliert werden, während Schreibworkloads an den Quellserver weitergeleitet werden können.

In einem häufig anzutreffenden Szenario verwenden BI- und Analyseworkloads das Lesereplikat als Datenquelle für die Berichterstellung.

Da Replikate schreibgeschützt sind, führen sie nicht direkt zu einer verringerten Auslastung der Schreibkapazität auf der Quelle. Dieses Feature ist nicht auf schreibintensive Workloads ausgerichtet.

Das Lesereplikatfeature verwendet die asynchrone MySQL-Replikation. Das Feature ist nicht für synchrone Replikationsszenarien vorgesehen. Zwischen der Quelle und dem Replikat entsteht eine messbare Verzögerung. Letztendlich sind die Daten auf dem Replikat mit den Daten auf dem Quellserver konsistent. Verwenden Sie das Feature für Workloads, für die diese Verzögerung akzeptabel ist.

## <a name="cross-region-replication"></a>Regionsübergreifende Replikation

Sie können ein Lesereplikat erstellen, das sich in einer anderen Region als Ihr Quellserver befindet. Die regionsübergreifende Replikation kann beispielsweise hilfreich sein, um die Notfallwiederherstellung zu planen oder Daten näher beim Benutzer bereitzustellen.

Sie können einen Quellserver in jeder [Azure Database for MySQL-Region](https://azure.microsoft.com/global-infrastructure/services/?products=mysql) haben.  Ein Quellserver kann ein Replikat in der gekoppelten Region oder den universellen Replikatregionen besitzen. Die folgende Abbildung zeigt, welche Replikatregionen entsprechend Ihrer Quellregion verfügbar sind.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Lesereplikatregionen":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universelle Replikatregionen

Sie können ein Lesereplikat in einer der folgenden Regionen erstellen, unabhängig davon, wo sich der Quellserver befindet. Folgende universelle Replikatregionen werden unterstützt:

| Region | Replikatverfügbarkeit | 
| --- | --- | 
| Australien (Osten) | :heavy_check_mark: | 
| Australien, Südosten | :heavy_check_mark: | 
| Brasilien Süd | :heavy_check_mark: | 
| Kanada, Mitte | :heavy_check_mark: |
| Kanada, Osten | :heavy_check_mark: |
| USA (Mitte) | :heavy_check_mark: | 
| East US | :heavy_check_mark: | 
| USA (Ost) 2 | :heavy_check_mark: |
| Asien, Osten | :heavy_check_mark: | 
| Japan, Osten | :heavy_check_mark: | 
| Japan, Westen | :heavy_check_mark: | 
| Korea, Mitte | :heavy_check_mark: |
| Korea, Süden | :heavy_check_mark: |
| Nordeuropa | :heavy_check_mark: | 
| USA Nord Mitte | :heavy_check_mark: | 
| USA Süd Mitte | :heavy_check_mark: | 
| Asien, Südosten | :heavy_check_mark: | 
| UK, Süden | :heavy_check_mark: | 
| UK, Westen | :heavy_check_mark: | 
| USA, Westen-Mitte | :heavy_check_mark: | 
| USA (Westen) | :heavy_check_mark: | 
| USA, Westen 2 | :heavy_check_mark: | 
| Europa, Westen | :heavy_check_mark: | 
| Indien, Mitte* | :heavy_check_mark: | 
| Frankreich, Mitte* | :heavy_check_mark: | 
| VAE, Norden* | :heavy_check_mark: | 
| Südafrika, Norden* | :heavy_check_mark: |

> [!Note] 
> *Regionen, in denen sich der Speichertyp „Universell V2“ für Azure Database for MySQL in der öffentlichen Vorschau befindet  <br /> *Für diese Azure-Regionen haben Sie die Möglichkeit, Server mit dem Speichertyp „Universell V1“ und „Universell V2“ zu erstellen. Für Server, die in der öffentlichen Vorschauversion mit dem Speichertyp „Universell V2“ erstellt wurden, können Sie Replikatserver nur in den Azure-Regionen erstellen, die den Speichertyp „Universell V2“ unterstützen.

### <a name="paired-regions"></a>Regionspaare

Zusätzlich zu den universellen Replikatregionen können Sie ein Lesereplikat in der gekoppelten Azure-Region Ihres Quellservers erstellen. Sollte Ihnen Ihr Regionspaar nicht bekannt sein, lesen Sie den Artikel [Gekoppelte Azure-Regionen](../best-practices-availability-paired-regions.md).

Wenn Sie für die Notfallwiederherstellungsplanung regionsübergreifende Replikate verwenden, empfehlen wir Ihnen, das Replikat in der gekoppelten Region und nicht in einer der anderen Regionen zu erstellen. Regionspaare vermeiden gleichzeitige Aktualisierungen und priorisieren die physische Isolation und die Datenresidenz.  

Es gibt jedoch einige Einschränkungen: 

* Regionale Verfügbarkeit: Azure Database for MySQL ist in den Regionen „Frankreich, Mitte“, „VAE, Norden“ und „Deutschland, Mitte“ verfügbar. Die entsprechenden gekoppelten Regionen sind allerdings nicht verfügbar.

* Unidirektionale Paare: Einige Azure-Regionen werden nur in eine Richtung gekoppelt. Zu diesen Regionen zählen „Indien, Westen“, „Brasilien, Süden“ und „US Gov Virginia“.
   Das bedeutet, dass ein Quellserver in der Region „Indien, Westen“ ein Replikat in „Indien, Süden“ erstellen kann. Ein Quellserver in der Region „Indien, Süden“ kann jedoch kein Replikat in der Region „Indien, Westen“ erstellen. Der Grund: Die sekundäre Region von „Indien, Westen“ ist zwar „Indien, Süden“, die sekundäre Region von „Indien, Süden“ ist jedoch nicht „Indien, Westen“.

## <a name="create-a-replica"></a>Erstellen eines Replikats

> [!IMPORTANT]
> * Das Feature für Lesereplikate ist nur für Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.
> * Verfügt der Quellserver über keine Replikatserver, ist für den Quellserver möglicherweise ein Neustart erforderlich, um sich je nach verwendetem Speicher (V1/V2) auf die Replikation vorzubereiten. Starten Sie den Server möglichst neu, und führen Sie diesen Vorgang außerhalb der Spitzenzeiten aus. Weitere Informationen finden Sie unter [Quellserverneustart](./concepts-read-replicas.md#source-server-restart). 


Wenn Sie den Workflow zum Erstellen von Replikaten starten, wird ein leerer Azure Database for MySQL-Server erstellt. Der neue Server wird mit den Daten gefüllt, die auf dem Quellserver vorhanden waren. Die Erstellungszeit hängt von der Datenmenge in der Quelle und der verstrichenen Zeit seit der letzten wöchentlichen vollständigen Sicherung ab. Dieser Zeitraum kann wenige Minuten bis zu mehrere Stunden umfassen. Der Replikatserver wird immer in derselben Ressourcengruppe und demselben Abonnement wie der Quellserver erstellt. Wenn Sie einen Replikatserver in einer anderen Ressourcengruppe oder einem anderen Abonnement erstellen möchten, können Sie nach der Erstellung den [Replikatserver verschieben](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Jedes Replikat ist für die [automatische Vergrößerung](concepts-pricing-tiers.md#storage-auto-grow) von Speicher aktiviert. Das Feature der automatischen Vergrößerung sorgt dafür, dass im Replikat genügend Speicherplatz für die replizierten Daten vorhanden ist, und verhindert damit Unterbrechungen bei der Replikation durch Fehler aufgrund von unzureichendem Speicher.

[Erfahren Sie, wie Sie ein Lesereplikat im Azure-Portal erstellen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Herstellen einer Verbindung mit einem Replikat

Ein Replikat erbt bei der Erstellung die Firewallregeln des Quellservers. Danach sind diese Regeln vom Quellserver unabhängig.

Das Replikat erbt das Administratorkonto vom Quellserver. Alle Benutzerkonten auf dem Quellserver werden auf die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Quellserver verfügbar sind.

Sie können eine Verbindung zum Replikat herstellen, indem Sie wie bei einem normalen Azure Database for MySQL-Server seinen Hostnamen und ein gültiges Benutzerkonto verwenden. Für einen Server namens **myreplica** mit dem Administratorbenutzernamen **myadmin** können Sie eine Verbindung mit dem Replikat herstellen, indem Sie die mysql-Befehlszeilenschnittstelle verwenden:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Geben Sie an der Eingabeaufforderung das Kennwort für das Benutzerkonto ein.

## <a name="monitor-replication"></a>Überwachen der Replikation

Azure Database for MySQL bietet die Metrik **Replication lag in seconds** (Replikationsverzögerung in Sekunden) in Azure Monitor. Diese Metrik steht nur für Replikate zur Verfügung. Diese Metrik wird mithilfe der `seconds_behind_master`-Metrik berechnet, die im MySQL-Befehl `SHOW SLAVE STATUS` verfügbar ist. Legen Sie eine Warnung fest, um Sie zu benachrichtigen, wenn die Replikationsverzögerung einen Wert erreicht, der für Ihre Workload nicht annehmbar ist.

Wenn Sie eine längere Replikationsverzögerung beobachten, lesen Sie [Behandeln von Problemen mit der Replikationswartezeit](howto-troubleshoot-replication-latency.md), um mögliche Ursachen zu verstehen und zu beheben.

## <a name="stop-replication"></a>Beenden der Replikation

Sie können die Replikation zwischen einer Quelle und einem Replikat beenden. Sobald die Replikation zwischen einem Quellserver und einem Lesereplikat beendet wurde, wird das Replikat zu einem eigenständigen Server. Bei den Daten auf diesem eigenständigen Server handelt es sich um die Daten, die zu dem Zeitpunkt, als der Befehl zum Beenden der Replikation gestartet wurde, auf dem Replikatserver vorhanden waren. Der eigenständige Server wird nicht zusammen mit dem Quellserver aktualisiert.

Wenn Sie die Replikation zu einem Replikat stoppen, gehen alle Links zu seiner vorherigen Quelle und zu anderen Replikaten verloren. Zwischen einer Quelle und dem zugehörigen Replikat erfolgt kein automatisiertes Failover.

> [!IMPORTANT]
> Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.
> Stellen Sie vor dem Beenden der Replikation auf einem Lesereplikat sicher, dass das Replikat alle erforderlichen Daten enthält.

Erfahren Sie, wie Sie die [Replikation auf ein Replikat beenden](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Zwischen Quell- und Replikatservern erfolgt kein automatisiertes Failover.

Da die Replikation asynchron erfolgt, gibt es eine Verzögerung zwischen der Quelle und dem Replikat. Die Verzögerungsdauer kann durch viele Faktoren beeinflusst werden, z. B. durch den Umfang der Workload auf dem Quellserver und die Wartezeit zwischen Rechenzentren. In den meisten Fällen beträgt die Replikatverzögerung einige Sekunden bis zu einigen Minuten. Sie können die tatsächliche Replikatverzögerung mithilfe der Metrik *Replikatverzögerung* nachverfolgen, die für jedes Replikat verfügbar ist. Diese Metrik zeigt die seit der letzten wiedergegebenen Transaktion verstrichene Zeit an. Es wird empfohlen, die durchschnittliche Verzögerung zu ermitteln, indem Sie die Replikatverzögerung über einen bestimmten Zeitraum hinweg beobachten. Sie können eine Warnung für die Replikatverzögerung festlegen, sodass Sie Maßnahmen ergreifen können, wenn sie sich außerhalb des erwarteten Bereichs befindet.

> [!Tip]
> Wenn Sie ein Failover auf das Replikat durchführen, zeigt die Verzögerung zum Zeitpunkt der Trennung des Replikats von der Quelle an, wie viel Daten verloren gehen.

Gehen Sie folgendermaßen vor, nachdem Sie sich für ein Failover auf ein Replikat entschieden haben:

1. Beenden der Replikation auf das Replikat<br/>
   Dieser Schritt ist erforderlich, damit der Replikatserver Schreibvorgänge akzeptieren kann. Im Rahmen dieses Vorgangs wird der Replikatserver von der Quelle getrennt. Nachdem Sie das Beenden der Replikation initiiert haben, dauert der Back-End-Prozess in der Regel etwa zwei Minuten. Informationen zu den Auswirkungen dieser Aktion finden Sie im Abschnitt [Beenden der Replikation](#stop-replication) in diesem Artikel.

2. Verweisen der Anwendung auf das (ehemalige) Replikat<br/>
   Jeder Server verfügt über eine eindeutige Verbindungszeichenfolge. Aktualisieren Sie Ihre Anwendung so, dass Sie auf das (ehemalige) Replikat und nicht auf die Quelle verweist.

Wenn die Anwendung erfolgreich Lese- und Schreibvorgänge verarbeitet, haben Sie das Failover abgeschlossen. Die Downtime Ihrer Anwendung hängt davon ab, wann Sie ein Problem feststellen und die oben beschriebenen Schritte 1 und 2 ausführen.

## <a name="global-transaction-identifier-gtid"></a>Globaler Transaktionsbezeichner (GTID)

Der globale Transaktionsbezeichner (GTID) ist ein eindeutiger Bezeichner, der mit jeder Transaktion auf einem Quellserver, für die ein Commit ausgeführt wird, erstellt wird. Er ist in Azure Database for MySQL standardmäßig deaktiviert. GTID wird in den Versionen 5.7 und 8.0 unterstützt, und zwar nur auf Servern mit Speichergrößen bis zu 16 TB. Weitere Informationen zum GTID und seiner Verwendung in der Replikation finden Sie unter [Replikation mit GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) in der MySQL-Dokumentation.

MySQL unterstützt zwei Arten von Transaktionen: GTID-Transaktionen (identifiziert mit GTID) und anonyme Transaktionen (ohne zugeordneten GTID)

Die folgenden Serverparameter sind für die Konfiguration des globalen Transaktionsbezeichners (GTID) verfügbar: 

|**Serverparameter**|**Beschreibung**|**Standardwert**|**Werte**|
|--|--|--|--|
|`gtid_mode`|Gibt an, ob GTIDs zur Identifizierung von Transaktionen verwendet werden. Änderungen zwischen Modi können nur nacheinander in aufsteigender Reihenfolge durchgeführt werden (z. B. `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`: Sowohl neue als auch replizierte Transaktionen müssen anonym sein <br> `OFF_PERMISSIVE`: Neue Transaktionen sind anonym. Replizierte Transaktionen können entweder anonyme Transaktionen oder GTID-Transaktionen sein. <br> `ON_PERMISSIVE`: Neue Transaktionen sind GTID-Transaktionen. Replizierte Transaktionen können entweder anonyme Transaktionen oder GTID-Transaktionen sein. <br> `ON`: Sowohl neue als auch replizierte Transaktionen müssen GTID-Transaktionen sein.|
|`enforce_gtid_consistency`|Erzwingt die GTID-Konsistenz, indem die Ausführung nur der Anweisungen zugelassen wird, die auf transaktionssichere Weise protokolliert werden können. Dieser Wert muss vor dem Aktivieren der GTID-Replikation auf `ON` festgelegt werden. |`OFF`|`OFF`: Alle Transaktionen können die GTID-Konsistenz verletzen.  <br> `ON`: Keine Transaktion darf die GTID-Konsistenz verletzen. <br> `WARN`: Alle Transaktionen können gegen GTID-Konsistenz verstoßen, aber es wird eine Warnung generiert. | 

> [!NOTE]
> * Wenn GTID aktiviert ist, können Sie ihn nicht wieder deaktivieren. Wenn Sie GTID deaktivieren müssen, wenden Sie sich an den Support. 
>
> * Das Ändern der GTIDs von einem Wert in einen anderen kann nur schrittweise in aufsteigender Reihenfolge der Modi erfolgen. Wenn z. B. gtid_mode derzeit auf OFF_PERMISSIVE festgelegt ist, ist eine Änderung in ON_PERMISSIVE möglich, aber nicht in ON.
>
> * Um die Replikation konsistent zu halten, können Sie die Einstellung nicht für einen Master-/Replikatserver aktualisieren.
>
> * Es wird empfohlen, enforce_gtid_consistency auf ON festzulegen, bevor Sie gtid_mode=ON festlegen können.


Um GTID zu aktivieren und das Konsistenzverhalten zu konfigurieren, aktualisieren Sie die Serverparameter `gtid_mode` und `enforce_gtid_consistency` über das [Azure-Portal](howto-server-parameters.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)oder [PowerShell](howto-configure-server-parameters-using-powershell.md).

Wenn GTID auf einem Quellserver aktiviert ist (`gtid_mode` = ON), wird für neu erstellte Replikate GTID ebenfalls aktiviert und die GTID-Replikation verwendet. Um sicherzustellen, dass die Replikation konsistent ist, kann `gtid_mode` nicht geändert werden, nachdem der Master- oder die Replikatserver mit aktivierter GTID erstellt wurde. 

## <a name="considerations-and-limitations"></a>Überlegungen und Einschränkungen

### <a name="pricing-tiers"></a>Tarife

Lesereplikate sind zurzeit nur in den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ verfügbar.

> [!NOTE]
> Die Kosten für den Betrieb des Replikatservers richten sich nach der Region, in der der Replikatserver betrieben wird.

### <a name="source-server-restart"></a>Quellserverneustart

Bei Servern mit dem Speichertyp „Universell V1“ ist der Parameter `log_bin` standardmäßig deaktiviert. Sobald Sie das erste Lesereplikat erstellen, wird der Parameter aktiviert. Verfügt ein Quellserver über keine Lesereplikate, wird der Quellserver zunächst neu gestartet, um sich auf die Replikation vorzubereiten. Starten Sie den Server möglichst neu, und führen Sie diesen Vorgang außerhalb der Spitzenzeiten aus.

Bei Quellservern mit dem Speichertyp „Universell V2“ ist der Parameter `log_bin` standardmäßig aktiviert. Zum Hinzufügen eines Lesereplikats ist kein Neustart erforderlich. 

### <a name="new-replicas"></a>Neue Replikate

Ein Lesereplikat wird als neuer Azure Database for MySQL-Server erstellt. Ein vorhandener Server kann nicht in ein Replikat umgewandelt werden. Es kann kein Replikat eines anderen Lesereplikats erstellt werden.

### <a name="replica-configuration"></a>Replikatkonfiguration

Ein Replikat wird mit der gleichen Serverkonfiguration wie der Quellserver erstellt. Nachdem ein Replikat erstellt wurde, können mehrere Einstellungen unabhängig vom Quellserver geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher und der Aufbewahrungszeitraum für Sicherungen. Auch der Tarif kann unabhängig geändert werden, allerdings nicht in den oder aus dem Tarif „Basic“.

> [!IMPORTANT]
> Bevor Sie die Konfiguration eines Quellservers mit neuen Werten aktualisieren, sollten Sie die Replikatkonfiguration in die gleichen oder in höhere Werte ändern. Durch diese Aktion wird sichergestellt, dass das Replikat mit allen Änderungen, die auf dem Quellserver durchgeführt werden, Schritt halten kann.

Firewallregeln und Parametereinstellungen werden beim Erstellen eines Replikats vom Quellserver geerbt. Danach sind die Regeln des Replikats unabhängig.

### <a name="stopped-replicas"></a>Beendete Replikate

Wenn Sie die Replikation zwischen einem Quellserver und einem Lesereplikat beenden, wird das beendete Replikat zu einem eigenständigen Server, der sowohl Lese- als auch Schreibzugriffe akzeptiert. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

### <a name="deleted-source-and-standalone-servers"></a>Gelöschte Quellserver und eigenständige Server

Wenn ein Quellserver gelöscht wird, wird die Replikation an alle Lesereplikate beendet. Diese Replikate werden automatisch zu eigenständigen Servern und können sowohl Lese- als auch Schreibvorgänge akzeptieren. Der Quellserver selbst wird gelöscht.

### <a name="user-accounts"></a>Benutzerkonten

Benutzer auf dem Quellserver werden an die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Quellserver verfügbar sind.

### <a name="server-parameters"></a>Serverparameter

Um zu verhindern, dass Daten nicht synchronisiert werden und um einen möglichen Datenverlust oder eine Beschädigung zu vermeiden, sind einige Serverparameter bei der Verwendung von Lesereplikaten für die Aktualisierung gesperrt.

Die folgenden Serverparameter sind sowohl auf dem Quell- als auch auf dem Replikatserver gesperrt:

* [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
* [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

Der Parameter [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) ist auf den Replikatservern gesperrt.

Um einen der oben genannten Parameter auf dem Quellserver zu aktualisieren, löschen Sie Replikatserver, aktualisieren Sie den Parameterwert für die Quelle, und erstellen Sie Replikate neu.

### <a name="gtid"></a>GTID

GTID wird unterstützt:

* In den MySQL-Versionen 5.7 und 8.0.
* Auf Servern, die eine Speicherkapazität von bis zu 16 TB unterstützen. Im Artikel über die [Tarife](concepts-pricing-tiers.md#storage) finden Sie eine vollständige Liste der Regionen, die 16 TB-Speicher unterstützen.

Standardmäßig ist GTID deaktiviert. Wenn GTID aktiviert ist, können Sie ihn nicht wieder deaktivieren. Wenn Sie GTID deaktivieren müssen, wenden Sie sich an den Support.

Wenn GTID auf einem Quellserver aktiviert ist, wird für neu erstellte Replikate GTID ebenfalls aktiviert und die GTID-Replikation verwendet. Damit die Replikation konsistent bleibt, ist eine Aktualisierung von `gtid_mode` auf den Quell- oder Replikatservern nicht möglich.

### <a name="other"></a>Sonstiges

* Die Erstellung des Replikats eines Replikats wird nicht unterstützt.
* In-Memory-Tabellen können dazu führen, dass Replikate nicht mehr synchron sind. Dies ist eine Einschränkung der MySQL-Replikationstechnologie. Weitere Informationen finden Sie in der [MySQL-Referenzdokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html).
* Stellen Sie sicher, dass die Quellservertabellen über Primärschlüssel verfügen. Das Fehlen von Primärschlüsseln kann zu Replikationslatenz zwischen der Quelle und den Replikaten führen.
* Eine vollständige Liste aller Einschränkungen der MySQL-Replikation finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Lesereplikate über das Azure-Portal erstellen und verwalten](howto-read-replicas-portal.md).
* Erfahren Sie, wie Sie [Lesereplikate über die Azure CLI und REST-API erstellen und verwalten](howto-read-replicas-cli.md).