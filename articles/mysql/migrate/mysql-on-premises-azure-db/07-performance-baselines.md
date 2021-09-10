---
title: 'Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Leistungsbaselines'
description: Das Verständnis der vorhandenen MySQL-Workload ist eine der besten Investitionen in eine erfolgreiche Migration.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 2077ef62ddabf7910d5a634c07262c9d29905cf4
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084964"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-performance-baselines"></a>Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Leistungsbaselines

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Testpläne](06-test-plans.md)

## <a name="overview"></a>Übersicht

Das Verständnis der vorhandenen MySQL-Workload ist eine der besten Investitionen in eine erfolgreiche Migration. Hervorragende Systemleistung hängt von angemessener Hardware und gutem Anwendungsdesign ab. Elemente wie CPU, Arbeitsspeicher, Datenträger und Netzwerk müssen gemäß der erwarteten Last dimensioniert und konfiguriert werden. Hardware und Konfiguration sind Teil der Systemleistungsgleichung. Der Entwickler muss die Datenbankabfragelast und die teuersten auszuführenden Abfragen kennen. Sich auf die teuersten Abfragen zu konzentrieren, kann einen erheblichen Unterschied in den allgemeinen Leistungsmetriken ausmachen.

Das Erstellen von Baselines für die Abfrageleistung ist für ein Migrationsprojekt von entscheidender Bedeutung. Anhand von Leistungsbaselines kann die Konfiguration der Azure-Zielzone für die migrierten Datenworkloads überprüft werden. Die meisten Systeme werden rund um die Uhr ausgeführt und haben unterschiedliche Spitzenlastzeiten. Es ist wichtig, die Spitzenworkloads für die Baseline zu erfassen. Metriken werden mehrmals erfasst. Weiter unten in diesem Dokument werden die Quellserverparameter und ihre Bedeutung für das Gesamtbild der Leistungsbaseline untersucht. Die Serverparameter sollten während eines Migrationsprojekts nicht übersehen werden.

## <a name="tools"></a>Tools

Im Folgenden finden Sie Tools zum Sammeln von Servermetriken und Informationen zur Datenbankworkload. Verwenden Sie die erfassten Metriken, um die entsprechende Azure Database for MySQL-Ebene und die zugehörigen Skalierungsoptionen zu bestimmen.

  - [MySQL Enterprise Monitor:](https://www.mysql.com/products/enterprise/monitor.html) Dieses kostenpflichtige Enterprise Edition-Tool kann eine sortierte Liste der teuersten Abfragen, Servermetriken, Datei-E/A und Topologieinformationen bereitstellen.

  - [Percona Monitoring and Management (PMM):](https://www.percona.com/software/database-tools/percona-monitoring-and-management) eine bewährte Open-Source-Datenbanküberwachungslösung. Sie trägt unabhängig vom bereitgestellten Speicherort dazu bei, die Komplexität zu reduzieren, die Leistung zu optimieren und die Sicherheit unternehmenskritischer Datenbankumgebungen zu verbessern.

## <a name="server-parameters"></a>Serverparameter

Eine Workload wird von standardmäßigen MySQL-Serverkonfigurationen möglicherweise nicht ausreichend unterstützt. Es gibt eine Vielzahl von Serverparametern in MySQL, aber in den meisten Fällen sollte sich das Migrationsteam nur auf eine Handvoll konzentrieren. Die folgenden Parameter sollten in der **Quell-** und **Zielumgebung** ausgewertet werden. Falsche Konfigurationen können sich auf die Geschwindigkeit der Migration auswirken. Diese Parameter werden bei Ausführung der Migrationsschritte erneut überprüft.

  - **innodb\_buffer\_pool\_size**: Ein großer Wert stellt sicher, dass vor der Verwendung von Datenträger-E/A zuerst In-Memory-Ressourcen verwendet werden. Typische Werte liegen zwischen 80 und 90 % des verfügbaren Arbeitsspeichers. Beispielsweise sollten bei einem System mit 8 GB Arbeitsspeicher 5 bis 6 GB für die Poolgröße zugeordnet werden.

  - **innodb\_log\_file\_size**: Die Wiederholungsprotokolle werden verwendet, um schnelle dauerhafte Schreibvorgänge sicherzustellen. Diese Transaktionssicherung ist während eines Systemabsturzes hilfreich. Ab innodb\_log\_file\_size = 512M (also 1 GB für Wiederholungspotokolle) sollte ausreichend Platz für Schreibvorgänge vorhanden sein. Schreibintensive Anwendungen, die MySQL 5.6 oder höher verwenden, sollten mit innodb\_log\_file\_ = 4G beginnen.

  - **max\_connections:** Anhand dieses Parameters kann der Fehler `Too many connections` behoben werden. Der Standardwert ist 151 Verbindungen. Die Verwendung eines Verbindungspools auf Anwendungsebene wird bevorzugt, aber die Serververbindungskonfiguration muss möglicherweise ebenfalls erhöht werden.

  - **innodb\_file\_per\_table**: Diese Einstellung teilt InnoDB mit, ob Daten und Indizes im freigegebenen Tabellenbereich oder in einer separaten IBD-Datei für jede Tabelle gespeichert werden sollen. Bei einer Datei pro Tabelle kann der Server Speicherplatz freigeben, wenn Tabellen getrennt, abgeschnitten oder neu erstellt werden. Datenbanken, die eine große Anzahl von Tabellen enthalten, sollten die Konfiguration mit einer Tabelle pro Datei nicht verwenden. Ab MySQL 5.6 ist der Standardwert ON. Frühere Datenbankversionen sollten die Konfiguration vor dem Laden von Daten auf ON festlegen. Diese Einstellung wirkt sich nur auf neu erstellte Tabellen aus.

  - **innodb\_flush\_log\_at\_trx\_commit**: Die Standardeinstellung 1 bedeutet, dass InnoDB vollständig ACID-kompatibel ist. Diese Transaktionskonfiguration mit geringerem Risiko kann auf Systemen mit langsamen Datenträgern einen erheblichen Mehraufwand verursachen, da zusätzliche fsyncs erforderlich sind, um jede Änderung in die Wiederholungsprotokolle zu leeren. Das Festlegen des Parameters auf 2 ist etwas weniger zuverlässig, da Committransaktionen nur einmal pro Sekunde in die Wiederholungsprotokolle geleert werden. Das Risiko kann in einigen Mastersituationen akzeptabel sein, und es ist definitiv ein guter Wert für ein Replikat. Der Wert 0 ermöglicht eine bessere Systemleistung, aber es ist wahrscheinlicher, dass der Datenbankserver bei einem Ausfall Daten verliert. Fazit: Verwenden Sie nur den Wert 0 für ein Replikat.

  - **innodb\_flush\_method**: Diese Einstellung steuert, wie Daten und Protokolle auf den Datenträger geleert werden. Verwenden Sie `O_DIRECT`, wenn ein Hardware-RAID-Controller mit einem akkugeschützten Rückschreibecache vorhanden ist. Verwenden Sie `fdatasync` (Standardwert) für andere Szenarien.

  - **innodb\_log\_buffer\_size**: Diese Einstellung ist die Größe des Puffers für Transaktionen, für die noch kein Commit ausgeführt wurde. Der Standardwert (1 MB) ist in der Regel in Ordnung. Transaktionen mit großen Blob-/Textfeldern können den Puffer schnell füllen und zusätzliche E/A-Last auslösen. Sehen Sie sich die Statusvariable `Innodb_log_waits` an, und wenn sie nicht 0 ist, erhöhen Sie `innodb_log_buffer_size`.

  - **query\_cache\_size**: Der Abfragecache ist ein bekannter Engpass, der bei mittlerer Parallelität auftreten kann. Der Anfangswert sollte auf 0 festgelegt werden, um den Cache zu deaktivieren. Beispiel: `query_cache_size = 0`. Dies ist die Standardeinstellung für MySQL 5.6 und höher.

  - **log\_bin**: Mit dieser Einstellung wird die binäre Protokollierung aktiviert. Das Aktivieren der binären Protokollierung ist obligatorisch, wenn der Server als Replikationsmaster fungieren soll.

  - **server\_id**: Diese Einstellung ist ein eindeutiger Wert für Identitätsserver in Replikationstopologien.

  - **expire\_logs\_days**: Diese Einstellung steuert, nach wie vielen Tagen die binären Protokolle automatisch gelöscht werden.

  - **skip\_name\_resolve**: Benutzer zum Ausführen der Clienthostnamenauflösung. Wenn das DNS langsam ist, ist die Verbindung langsam. Beim Deaktivieren der Namensauflösung dürfen die GRANT-Anweisungen nur IP-Adressen verwenden. Alle zuvor ausgeführten GRANT-Anweisungen müssen erneut ausgeführt werden, um die IP-Adresse zu verwenden.

Führen Sie den folgenden Befehl aus, um die Serverparameter zur Überprüfung in eine Datei zu exportieren. Mithilfe einer einfachen Analyse können anhand der Ausgabe dieselben Serverparameter nach der Migration erneut verwendet werden, sofern sie für den Azure Database for MySQL-Server geeignet sind. Weitere Informationen finden Sie unter [Konfigurieren von Serverparametern in Azure Database for MySQL mit dem Azure-Portal](../../howto-server-parameters.md).

`mysql -u root -p -A -e "SHOW GLOBAL VARIABLES;" > settings.txt`

Die standardmäßig installierten Serverparameter für MySQL 5.5.60 finden Sie im [Anhang](15-appendix.md#default-server-parameters-mysql-55-and-azure-database-for-mysql).

Exportieren Sie vor Beginn der Migration die MySQL-Quellkonfigurationseinstellungen. Vergleichen Sie diese Werte mit den Einstellungen der Azure-Zielzoneninstanz nach der Migration. Wenn Einstellungen von der Standardeinstellung in der Azure-Zielzoneninstanz geändert wurden, stellen Sie sicher, dass diese nach der Migration zurückgesetzt werden. Außerdem sollte der Migrationsbenutzer überprüfen, ob die Serverparameter vor der Migration festgelegt werden können.

Eine Liste der Serverparameter, die nicht konfiguriert werden können, finden Sie unter [Nicht konfigurierbare Serverparameter](../../concepts-server-parameters.md#non-configurable-server-parameters).

### <a name="egress-and-ingress"></a>Ausgehend und Eingehend

Für jedes entsprechende Datenmigrationstool und jeden Pfad müssen die MySQL-Serverparameter für Quelle und Ziel geändert werden, um den schnellstmöglichen Ausgang und Eingang zu unterstützen. Je nach Tool können die Parameter unterschiedlich sein. Beispielsweise benötigt ein Tool, das eine Migration parallel ausführt, möglicherweise mehr Verbindungen zu Quelle und Ziel als ein Singlethreadtool.

Überprüfen Sie alle Timeoutparameter, die von den Datasets betroffen sein können. Dazu zählen unter anderem folgende Einstellungen:

  - [connect\_timeout](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_connect_timeout)

  - [wait\_timeout](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_wait_timeout)

Überprüfen Sie außerdem alle Parameter, die sich auf die Höchstwerte auswirken:

  - [max\_allowed\_packet](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_allowed_packet)

> [!NOTE]
> Ein häufiger Fehler bei der Migration ist `MySQL server has gone away`. Die hier genannten Parameter sind die typischen Ursachen dieses Fehlers.

## <a name="wwi-scenario"></a>WWI-Szenario

WWI hat die Workload der Konferenzdatenbank überprüft und festgestellt, dass die Last sehr gering ist. Obwohl ein Server der Basic-Ebene für das Unternehmen funktionieren würde, wollte es später keine Arbeit mit der Migration einer weiteren Ebene haben. Der bereitgestellte Server wird irgendwann die anderen MySQL-Datenworkloads hosten, und daher wählte WWI die Ebene `General Performance` aus.

Bei der Überprüfung der MySQL-Datenbank wird der MySQL 5.5-Server mit den Standardserverparametern ausgeführt, die während der Erstinstallation festgelegt werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Datenmigration](./08-data-migration.md)
