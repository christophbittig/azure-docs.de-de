---
title: Lesereplikate – Azure Database for MySQL – Flexible Server
description: 'Weitere Informationen zu Lesereplikaten in Azure Database for MySQL Flexible Server: Erstellen von Replikaten, Herstellen einer Verbindung mit Replikaten und Beenden der Replikation.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 7def4d2afac4a1f52db89defb226857b7f8b41fa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468345"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Lesereplikate in Azure Database for MySQL – Flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

MySQL ist eine der beliebtesten Datenbank-Engines für die Ausführung von Web- und mobilen Anwendungen im Internet. Viele unserer Kunden verwenden es für ihre Onlinebildungsdienste, Videostreamingdienste, digitalen Zahlungslösungen, E-Commerce-Plattformen, Gamingdienste, Nachrichtenportale sowie für Websites für Behörden und das Gesundheitswesen. Diese Dienste müssen in dem Maße, wie der Datenverkehr im Web oder bei mobilen Anwendungen zunimmt, genutzt und skaliert werden.

Auf der Anwendungsseite wird die Anwendung typischerweise in Java oder PHP entwickelt und migriert, um auf Azure-VM-Skalierungsgruppen oder Azure App Services ausgeführt zu werden, oder sie wird containerisiert, um unter Azure Kubernetes Service (AKS) ausgeführt zu werden. Mit VM-Skalierungsgruppen, App Service oder AKS als zugrunde liegende Infrastruktur wird die Anwendungsskalierung vereinfacht, indem neue virtuelle Computer sofort bereitgestellt und die zustandslosen Komponenten von Anwendungen repliziert werden, um die Anforderungen zu erfüllen, aber oft wird die Datenbank als zentrale zustandsbehaftete Komponente zum Engpass.

Mithilfe des Lesereplikatfeatures können Sie Daten von einer Azure Database for MySQL Flexible Server-Instanz auf einem schreibgeschützten Server replizieren. Sie können vom Quellserver auf bis zu **10** Replikate replizieren. Replikate werden asynchron mithilfe des auf der Position der nativen, binären Protokolldatei (binlog) basierenden Replikationsverfahrens der MySQL-Engine aktualisiert. Weitere Informationen zur binlog-Replikation finden Sie unter [Binary Log File Position Based Replication Configuration Overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Konfiguration der auf der Position der binären Protokolldatei basierenden Replikation – Übersicht).

Replikate sind neue Server, die Sie ähnlich wie Ihre flexiblen Azure Database for MySQL-Quellserver verwalten. Für jedes gelesene Replikat fallen Gebühren an, die auf der bereitgestellten Compute-Instanz hinsichtlich der virtuellen Kerne und dem Speicherplatz in GB/Monat basieren. Weitere Informationen finden Sie unter [Azure Data Lake Storage – Preise](./concepts-compute-storage.md#pricing).

> [!NOTE]
> Das Feature für Lesereplikate ist nur für flexible Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.

Weitere Informationen zu Features und Problemen der MySQL-Replikation finden Sie in der [Dokumentation zur MySQL-Replikation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

## <a name="common-use-cases-for-read-replica"></a>Allgemeine Anwendungsfälle für Lesereplikate

Das Feature für Lesereplikate kann die Leistung und Skalierung von leseintensiven Workloads verbessern. Leseworkloads können in den Replikaten isoliert werden, während Schreibworkloads an den Quellserver weitergeleitet werden können.

Häufige Szenarien:

* Skalierung von Leseworkloads, die von der Anwendung stammen, unter Verwendung eines einfachen Verbindungsproxys wie [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) oder von auf Microservices basierenden Mustern zum Aufskalieren Ihrer Leseabfragen, die von der Anwendung stammen, um Replikate zu lesen.
* BI- oder analytische Workloads zur Berichterstellung können Lesereplikate als Datenquelle für die Berichterstellung verwenden.
* Für ein IoT- oder Produktionsszenario, bei dem Telemetrieinformationen in der MySQL-Datenbank-Engine erfasst werden, während mehrere Lesereplikate für die Datenberichterstellung verwendet werden.

Da Replikate schreibgeschützt sind, führen sie nicht direkt zu einer verringerten Auslastung der Schreibkapazität auf der Quelle. Dieses Feature ist nicht auf schreibintensive Workloads ausgerichtet.

Das Lesereplikatfeature verwendet die asynchrone MySQL-Replikation. Das Feature ist nicht für synchrone Replikationsszenarien vorgesehen. Zwischen der Quelle und dem Replikat entsteht eine messbare Verzögerung. Letztendlich sind die Daten auf dem Replikat mit den Daten auf dem Quellserver konsistent. Verwenden Sie das Feature für Workloads, für die diese Verzögerung akzeptabel ist.

## <a name="create-a-replica"></a>Erstellen eines Replikats

Wenn ein Quellserver keine vorhandenen Replikatserver aufweist, wird die Quelle zunächst neu gestartet, um sich auf die Replikation vorzubereiten.

Wenn Sie den Workflow zum Erstellen von Replikaten starten, wird ein leerer Azure Database for MySQL-Server erstellt. Der neue Server wird mit den Daten gefüllt, die auf dem Quellserver vorhanden waren. Die Erstellungszeit hängt von der Datenmenge in der Quelle und der verstrichenen Zeit seit der letzten wöchentlichen vollständigen Sicherung ab. Dieser Zeitraum kann wenige Minuten bis zu mehrere Stunden umfassen.

> [!NOTE]
> Lesereplikate werden mit der gleichen Serverkonfiguration wie die Quelle erstellt. Die Replikatserverkonfiguration kann nach der Erstellung geändert werden. Der Replikatserver wird immer in derselben Ressourcengruppe, am selben Standort und im selben Abonnement wie der Quellserver erstellt. Wenn Sie einen Replikatserver in einer anderen Ressourcengruppe oder einem anderen Abonnement erstellen möchten, können Sie nach der Erstellung den [Replikatserver verschieben](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Quellserver, damit das Replikat über genügend Kapazität verfügt.

[Erfahren Sie, wie Sie ein Lesereplikat im Azure-Portal erstellen](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Herstellen einer Verbindung mit einem Replikat

Bei der Erstellung erbt ein Replikat die Konnektivitätsmethode des Quellservers. Sie können die Konnektivitätsmethode des Replikats nicht ändern. Wenn der Quellserver z. B. über **privaten Zugriff (VNet-Integration)** verfügt, kann das Replikat nicht den **öffentlichen Zugriff (zulässige IP-Adressen)** aufweisen.

Das Replikat erbt das Administratorkonto vom Quellserver. Alle Benutzerkonten auf dem Quellserver werden auf die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Quellserver verfügbar sind.

Sie können eine Verbindung zum Replikat herstellen, indem Sie wie bei einer normalen Azure Database for MySQL Flexible Server-Instanz seinen Hostnamen und ein gültiges Benutzerkonto verwenden. Für einen Server namens **myreplica** mit dem Administratorbenutzernamen **myadmin** können Sie eine Verbindung mit dem Replikat herstellen, indem Sie die mysql-Befehlszeilenschnittstelle verwenden:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

Geben Sie an der Eingabeaufforderung das Kennwort für das Benutzerkonto ein.

## <a name="monitor-replication"></a>Überwachen der Replikation

Azure Database for MySQL Flexible Server stellt die Metrik „Replication lag in seconds“ (Replikationsverzögerung in Sekunden) in **Azure Monitor** bereit. Diese Metrik steht nur für Replikate zur Verfügung. Diese Metrik wird mithilfe der `seconds_behind_master`-Metrik berechnet, die im MySQL-Befehl `SHOW SLAVE STATUS` verfügbar ist. Legen Sie eine Warnung fest, um Sie zu benachrichtigen, wenn die Replikationsverzögerung einen Wert erreicht, der für Ihre Workload nicht annehmbar ist.

Wenn Sie eine längere Replikationsverzögerung beobachten, lesen Sie [Behandeln von Problemen mit der Replikationswartezeit](./../howto-troubleshoot-replication-latency.md), um mögliche Ursachen zu verstehen und zu beheben.

## <a name="stop-replication"></a>Beenden der Replikation

Sie können die Replikation zwischen einer Quelle und einem Replikat beenden. Sobald die Replikation zwischen einem Quellserver und einem Lesereplikat beendet wurde, wird das Replikat zu einem eigenständigen Server. Bei den Daten auf diesem eigenständigen Server handelt es sich um die Daten, die zu dem Zeitpunkt, als der Befehl zum Beenden der Replikation gestartet wurde, auf dem Replikatserver vorhanden waren. Der eigenständige Server wird nicht zusammen mit dem Quellserver aktualisiert.

Wenn Sie die Replikation zu einem Replikat stoppen, gehen alle Links zu seiner vorherigen Quelle und zu anderen Replikaten verloren. Zwischen einer Quelle und seinem Replikat gibt es kein automatisiertes Failover.

> [!IMPORTANT]
>Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.
> Stellen Sie vor dem Beenden der Replikation auf einem Lesereplikat sicher, dass das Replikat alle erforderlichen Daten enthält.

Erfahren Sie, wie Sie die [Replikation auf ein Replikat beenden](how-to-read-replicas-portal.md).

## <a name="failover"></a>Failover

Zwischen Quell- und Replikatservern erfolgt kein automatisiertes Failover.

Lesereplikate sind für die Skalierung leseintensiver Workloads gedacht und sind nicht dafür ausgelegt, die Hochverfügbarkeitsanforderungen eines Servers zu erfüllen. Das Beenden der Replikation bei einem Lesereplikat, um es im Lese-/Schreibmodus online zu schalten, ist die Methode, mit der dieser manuelle Failover durchgeführt wird.

Da die Replikation asynchron erfolgt, gibt es eine Verzögerung zwischen der Quelle und dem Replikat. Die Verzögerungsdauer kann durch viele Faktoren beeinflusst werden, z. B. durch den Umfang der Workload auf dem Quellserver und die Wartezeit zwischen Rechenzentren. In den meisten Fällen beträgt die Replikatverzögerung einige Sekunden bis zu einigen Minuten. Sie können die tatsächliche Replikatverzögerung mithilfe der Metrik *Replikatverzögerung* nachverfolgen, die für jedes Replikat verfügbar ist. Diese Metrik zeigt die seit der letzten wiedergegebenen Transaktion verstrichene Zeit an. Es wird empfohlen, die durchschnittliche Verzögerung zu ermitteln, indem Sie die Replikatverzögerung über einen bestimmten Zeitraum hinweg beobachten. Sie können eine Warnung für die Replikatverzögerung festlegen, sodass Sie Maßnahmen ergreifen können, wenn sie sich außerhalb des erwarteten Bereichs befindet.

> [!Tip]
> Wenn Sie ein Failover auf das Replikat durchführen, zeigt die Verzögerung zum Zeitpunkt der Trennung des Replikats von der Quelle an, wie viel Daten verloren gehen.

Gehen Sie folgendermaßen vor, nachdem Sie sich für ein Failover auf ein Replikat entschieden haben:

1. Beenden der Replikation auf das Replikat<br/>
   Dieser Schritt ist erforderlich, damit der Replikatserver Schreibvorgänge akzeptieren kann. Im Rahmen dieses Vorgangs wird der Replikatserver von der Quelle getrennt. Nachdem Sie das Beenden der Replikation initiiert haben, dauert der Back-End-Prozess in der Regel etwa zwei Minuten. Informationen zu den Auswirkungen dieser Aktion finden Sie im Abschnitt [Beenden der Replikation](#stop-replication) in diesem Artikel.

2. Verweisen der Anwendung auf das (ehemalige) Replikat<br/>
   Jeder Server verfügt über eine eindeutige Verbindungszeichenfolge. Aktualisieren Sie Ihre Anwendung so, dass Sie auf das (ehemalige) Replikat und nicht auf die Quelle verweist.

Wenn Ihre Anwendung Lese- und Schreibvorgänge erfolgreich verarbeitet, haben Sie das Failover abgeschlossen. Die Ausfallzeit Ihrer Anwendung hängt davon ab, ob Sie ein Problem erkennen und die oben beschriebenen Schritte 1 und 2 ausführen.

## <a name="global-transaction-identifier-gtid"></a>Globaler Transaktionsbezeichner (GTID)

Der globale Transaktionsbezeichner (GTID) ist ein eindeutiger Bezeichner, der mit jeder Transaktion auf einem Quellserver, für die ein Commit erfolgt, erstellt wird. Er ist für den flexiblen Azure Database for MySQL-Server standardmäßig deaktiviert. GTID wird von den Versionen 5.7 und 8.0 unterstützt. Weitere Informationen zum GTID und seiner Verwendung in der Replikation finden Sie unter [Replikation mit GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) in der MySQL-Dokumentation.

Die folgenden Serverparameter sind für die Konfiguration des globalen Transaktionsbezeichners (GTID) verfügbar:

|**Serverparameter**|**Beschreibung**|**Standardwert**|**Werte**|
|--|--|--|--|
|`gtid_mode`|Gibt an, ob GTIDs zur Identifizierung von Transaktionen verwendet werden. Änderungen zwischen Modi können nur nacheinander in aufsteigender Reihenfolge durchgeführt werden (z. B. `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF*`|`OFF`: Sowohl neue als auch replizierte Transaktionen müssen anonym sein <br> `OFF_PERMISSIVE`: Neue Transaktionen sind anonym. Replizierte Transaktionen können entweder anonyme Transaktionen oder GTID-Transaktionen sein. <br> `ON_PERMISSIVE`: Neue Transaktionen sind GTID-Transaktionen. Replizierte Transaktionen können entweder anonyme Transaktionen oder GTID-Transaktionen sein. <br> `ON`: Sowohl neue als auch replizierte Transaktionen müssen GTID-Transaktionen sein.|
|`enforce_gtid_consistency`|Erzwingt die GTID-Konsistenz, indem die Ausführung nur der Anweisungen zugelassen wird, die auf transaktionssichere Weise protokolliert werden können. Dieser Wert muss vor dem Aktivieren der GTID-Replikation auf `ON` festgelegt werden. |`OFF*`|`OFF`: Alle Transaktionen können die GTID-Konsistenz verletzen.  <br> `ON`: Keine Transaktion darf die GTID-Konsistenz verletzen. <br> `WARN`: Alle Transaktionen können gegen GTID-Konsistenz verstoßen, aber es wird eine Warnung generiert. |

**Für flexible Azure Database for MySQL-Server mit aktiviertem Feature „Hochverfügbarkeit“ ist der Standardwert auf `ON` festgelegt.*
> [!NOTE]
>
> * Wenn GTID aktiviert ist, können Sie ihn nicht wieder deaktivieren. Wenn Sie GTID deaktivieren müssen, wenden Sie sich an den Support.
>
> * Das Ändern der GTIDs von einem Wert in einen anderen kann nur schrittweise in aufsteigender Reihenfolge der Modi erfolgen. Wenn z. B. gtid_mode derzeit auf OFF_PERMISSIVE festgelegt ist, ist eine Änderung in ON_PERMISSIVE möglich, aber nicht in ON.
>
> * Um die Replikation konsistent zu halten, können Sie die Einstellung nicht für einen Master-/Replikatserver aktualisieren.
>
> * Es wird empfohlen, enforce_gtid_consistency auf ON festzulegen, bevor Sie gtid_mode=ON festlegen können.

Um GTID zu aktivieren und das Konsistenzverhalten zu konfigurieren, aktualisieren Sie die Serverparameter `gtid_mode` und `enforce_gtid_consistency` über das [Azure-Portal](how-to-configure-server-parameters-portal.md) oder die [Azure CLI](how-to-configure-server-parameters-cli.md).

Wenn GTID auf einem Quellserver aktiviert ist (`gtid_mode` = ON), wird für neu erstellte Replikate GTID ebenfalls aktiviert und die GTID-Replikation verwendet. Um sicherzustellen, dass die Replikation konsistent ist, kann `gtid_mode` nicht geändert werden, nachdem der Master- oder die Replikatserver mit aktivierter GTID erstellt wurde.

## <a name="considerations-and-limitations"></a>Überlegungen und Einschränkungen

| Szenario | Einschränkung/Überlegung |
|:-|:-|
| Replikat auf Server mit aktivierter Hochverfügbarkeit | Nicht unterstützt |
| Replikat auf Server im Tarif „Burstfähig“| Nicht unterstützt |
| Regionsübergreifende Lesereplikation | Nicht unterstützt |
| Preise | Die Kosten für den Betrieb des Replikatservers richten sich nach der Region, in der der Replikatserver betrieben wird. |
| Quellserverneustart | Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch. |
| Neue Replikate | Ein Lesereplikat wird als neue Azure Database for MySQL Flexible Server-Instanz erstellt. Ein vorhandener Server kann nicht in ein Replikat umgewandelt werden. Es kann kein Replikat eines anderen Lesereplikats erstellt werden. |
| Replikatkonfiguration | Ein Replikat wird mit der gleichen Serverkonfiguration wie der Quellserver erstellt. Nachdem ein Replikat erstellt wurde, können mehrere Einstellungen unabhängig vom Quellserver geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher und der Aufbewahrungszeitraum für Sicherungen. Die Computeebene kann auch unabhängig geändert werden.<br> <br> **WICHTIG**  <br> – Bevor Sie die Konfiguration eines Quellservers mit neuen Werten aktualisieren, sollten Sie die Replikatkonfiguration in die gleichen oder höhere Werte ändern. Durch diese Aktion wird sichergestellt, dass das Replikat mit allen Änderungen, die auf dem Quellserver durchgeführt werden, Schritt halten kann. <br/> Konnektivitätsmethode und Parametereinstellungen werden beim Erstellen eines Replikats vom Quellserver geerbt. Danach sind die Regeln des Replikats unabhängig. |
| Beendete Replikate | Wenn Sie die Replikation zwischen einem Quellserver und einem Lesereplikat beenden, wird das beendete Replikat zu einem eigenständigen Server, der sowohl Lese- als auch Schreibzugriffe akzeptiert. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden. |
| Gelöschte Quellserver und eigenständige Server | Wenn ein Quellserver gelöscht wird, wird die Replikation an alle Lesereplikate beendet. Diese Replikate werden automatisch zu eigenständigen Servern und können sowohl Lese- als auch Schreibvorgänge akzeptieren. Der Quellserver selbst wird gelöscht. |
| Benutzerkonten | Benutzer auf dem Quellserver werden an die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Quellserver verfügbar sind. |
| Serverparameter | Um zu verhindern, dass Daten nicht synchronisiert werden und um einen möglichen Datenverlust oder eine Beschädigung zu vermeiden, sind einige Serverparameter bei der Verwendung von Lesereplikaten für die Aktualisierung gesperrt. <br> Die folgenden Serverparameter sind sowohl auf dem Quell- als auch auf dem Replikatserver gesperrt:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> Der Parameter [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) ist auf den Replikatservern gesperrt. <br> Um einen der oben genannten Parameter auf dem Quellserver zu aktualisieren, löschen Sie Replikatserver, aktualisieren Sie den Parameterwert für die Quelle, und erstellen Sie Replikate neu.
<br> Beim Konfigurieren von Parametern auf Sitzungsebene wie foreign_keys_checks für das Lesereplikat ist darauf zu achten, dass die für das Lesereplikat festgelegten Parameterwerte mit denen des Ausgangsservers übereinstimmen.|
| Sonstige | – Die Erstellung des Replikats eines Replikats wird nicht unterstützt. <br> – In-Memory-Tabellen können dazu führen, dass Replikate nicht mehr synchron sind. Dies ist eine Einschränkung der MySQL-Replikationstechnologie. Weitere Informationen finden Sie in der [MySQL-Referenzdokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html). <br>– Stellen Sie sicher, dass die Quellservertabellen über Primärschlüssel verfügen. Das Fehlen von Primärschlüsseln kann zu Replikationslatenz zwischen der Quelle und den Replikaten führen.<br>– Eine vollständige Liste aller Einschränkungen der MySQL-Replikation finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html). |

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Lesereplikate über das Azure-Portal erstellen und verwalten](how-to-read-replicas-portal.md).
* Erfahren Sie, wie Sie [Lesereplikate über die Azure CLI erstellen und verwalten](how-to-read-replicas-cli.md).
