---
title: Serverparameter – Azure Database for MySQL – Flexibler Server
description: In diesem Thema werden Richtlinien für die Konfiguration von Serverparametern in Azure Database for MySQL – Flexibler Server beschrieben.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 59bb5a6a2a544eb72d1438c38ad3040c2ac43476
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468364"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Serverparameter in Azure Database for MySQL – Flexibler Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Dieser Artikel enthält Überlegungen und Richtlinien zur Konfiguration von Serverparametern in Azure Database for MySQL – Flexibler Server.

## <a name="what-are-server-variables"></a>Was sind Servervariablen?

Die MySQL-Engine bietet viele verschiedene [Servervariablen/-parameter](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html), die zur Konfiguration und Optimierung des Engine-Verhaltens verwendet werden können. Einige der Parameter können dynamisch während der Laufzeit festgelegt werden, während andere „statisch“ sind und ihre Anwendung einen Neustart des Servers erfordert.

Azure Database for MySQL – Flexibler Server bietet die Möglichkeit, den Wert verschiedener MySQL-Serverparameter unter Verwendung des [Azure-Portals](./how-to-configure-server-parameters-portal.md) und der [Azure CLI](./how-to-configure-server-parameters-cli.md) zu ändern, um die Anforderungen Ihrer Workload zu erfüllen.

## <a name="configurable-server-parameters"></a>Konfigurierbare Serverparameter

Sie können die Konfiguration flexibler Azure Database for MySQL-Server über Serverparameter verwalten. Die Serverparameter werden beim Erstellen des Servers mit einem Standardwert und einem empfohlenen Wert konfiguriert. Auf dem Blatt „Serverparameter“ im Azure-Portal werden sowohl änderbare als auch nicht änderbare Serverparameter angezeigt. Die nicht änderbaren Serverparameter sind ausgegraut.

Die Liste der unterstützten Serverparameter wächst ständig. Sie können die vollständige Liste im Azure-Portal auf der Registerkarte „Serverparameter“ anzeigen und die Werte der Serverparameter dort konfigurieren.

In den folgenden Abschnitten erfahren Sie mehr über die Grenzen der verschiedenen häufig aktualisierten Serverparameter. Die Grenzwerte werden durch den Computetarif und die Größe der virtuellen Kerne des Servers bestimmt.

> [!NOTE]
> Wenn Sie einen Serverparameter ändern möchten, der nicht änderbar ist, in Ihrer Umgebung aber möglichst geändert werden sollte, öffnen Sie einen [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0)-Beitrag oder geben Ihre Stimme ab, wenn bereits Feedback gegeben wurde. So helfen Sie uns bei der Priorisierung.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

In Azure Database for MySQL – Flexibler Server sind binäre Protokolle immer aktiviert (d. h., `log_bin` ist auf ON festgelegt). log_bin_trust_function_creators ist in flexiblen Servern standardmäßig auf ON festgelegt.

Das Format für binäre Protokollierung ist immer **ROW** (Zeile), und für alle Verbindungen mit dem Server wird **IMMER** die zeilenbasierte binäre Protokollierung verwendet. Bei der zeilenbasierten binären Protokollierung gibt es keine Sicherheitsprobleme, und die binäre Protokollierung kann nicht unterbrochen werden, sodass [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) sicher auf **ON** bleiben kann.

Wenn [`log_bin_trust_function_creators`] auf OFF gesetzt ist, und Sie versuchen, Trigger zu erstellen, erhalten Sie möglicherweise eine Fehlermeldung wie *Sie verfügen nicht über die SUPER-Berechtigung, und die binäre Protokollierung ist aktiviert (es kann ratsam sein, die weniger sichere Variable `log_bin_trust_function_creators` zu verwenden)* .

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Weitere Informationen zu diesem Parameter finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size).

|**Tarif**|**vCore(s)**|**Arbeitsspeichergröße (GiB)**|**Standardwert (Bytes)**|**Mindestwert (Bytes)**|**Höchstwert (Bytes)**|
|---|---|---|---|---|---|
|Burstfähig (B1s)|1|1|134217728|33554432|134217728|
|Burstfähig (B1ms)|1|2|536870912|134217728|536870912|
|Burstfähig|2|4|2147483648|134217728|2147483648|
|Universell|2|8|6442450944|134217728|6442450944|
|Universell|4|16|12884901888|134217728|12884901888|
|Universell|8|32|25769803776|134217728|25769803776|
|Universell|16|64|51539607552|134217728|51539607552|
|Universell|32|128|103079215104|134217728|103079215104|
|Universell|48|192|154618822656|134217728|154618822656|
|Universell|64|256|206158430208|134217728|206158430208|
|Arbeitsspeicheroptimiert|2|16|12884901888|134217728|12884901888|
|Arbeitsspeicheroptimiert|4|32|25769803776|134217728|25769803776|
|Arbeitsspeicheroptimiert|8|64|51539607552|134217728|51539607552|
|Arbeitsspeicheroptimiert|16|128|103079215104|134217728|103079215104|
|Arbeitsspeicheroptimiert|32|256|206158430208|134217728|206158430208|
|Arbeitsspeicheroptimiert|48|384|309237645312|134217728|309237645312|
|Arbeitsspeicheroptimiert|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL speichert die InnoDB-Tabelle in verschiedenen Tabellenbereichen, basierend auf der Konfiguration, die Sie während der Tabellenerstellung angegeben haben. Der [Systemtabellenbereich](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) ist der Speicherbereich für das InnoDB-Datenwörterbuch. Ein [file-per-table-Tabellenbereich](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) enthält die Daten und Indizes für eine einzelne InnoDB-Tabelle und wird im Dateisystem in einer eigenen Datendatei gespeichert. Dieses Verhalten wird vom `innodb_file_per_table`-Serverparameter gesteuert. Durch Festlegen von `innodb_file_per_table` auf `OFF` werden Tabellen von InnoDB im Systemtabellenbereich erstellt. Andernfalls werden die Tabellen im Tabellen-Tabellenbereich erstellt.

Azure Database for MySQL – Flexibler Server unterstützt bis zu **4 TB** in einer einzelnen Datendatei. Wenn die Datenbankgröße 4 TB überschreitet, sollten Sie die Tabelle im Tabellenbereich [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) erstellen. Wenn eine einzelne Tabelle größer als 4 TB ist, sollten Sie die Partitionstabelle verwenden.

### <a name="innodb_log_file_size"></a>innodb_log_file_size

[innodb_log_file_size](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size) ist die Größe in Bytes jeder [Protokolldatei](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_log_file) in einer [Protokollgruppe](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_log_group). Die kombinierte Größe der Protokolldateien [(innodb_log_file_size](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size) * [innodb_log_files_in_group](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_files_in_group)) darf einen Maximalwert von etwas weniger als 512 GB nicht überschreiten. Eine größere Protokolldateigröße ist für die Leistung besser, hat jedoch den Nachteil, dass die Wiederherstellungszeit nach einem Absturz hoch ist. Sie müssen zwischen der Wiederherstellungszeit im seltenen Fall einer Absturzwiederherstellung und der Maximierung des Durchsatzes während Spitzenvorgängen abwägen. Dies kann auch zu längeren Neustartzeiten führen. Sie können „ innodb_log_size“ auf jeden dieser Werte konfigurieren: 256 MB, 512 MB, 1 GB oder 2 GB für Azure Database für MySQL – Flexibler Server. Der Parameter ist statisch und erfordert einen Neustart.

> [!NOTE]
> Wenn Sie den Parameter „innodb_log_file_size“ vom Standardwert geändert haben, überprüfen Sie, ob der Wert von „show global status like“ wie „innodb_buffer_pool_pages_dirty“ dreißig Sekunden lang auf 0 bleibt, um eine Verzögerung des Neustarts zu vermeiden.



### <a name="max_connections"></a>max_connections

Der Wert von max_connections wird durch die Speichergröße des Servers bestimmt.

|**Tarif**|**vCore(s)**|**Arbeitsspeichergröße (GiB)**|**Standardwert**|**Mindestwert**|**Höchstwert**|
|---|---|---|---|---|---|
|Burstfähig (B1s)|1|1|85|10|171|
|Burstfähig (B1ms)|1|2|171|10|341|
|Burstfähig|2|4|341|10|683|
|Universell|2|8|683|10|1365|
|Universell|4|16|1365|10|2731|
|Universell|8|32|2731|10|5461|
|Universell|16|64|5461|10|10923|
|Universell|32|128|10923|10|21845|
|Universell|48|192|16384|10|32768|
|Universell|64|256|21845|10|43691|
|Arbeitsspeicheroptimiert|2|16|1365|10|2731|
|Arbeitsspeicheroptimiert|4|32|2731|10|5461|
|Arbeitsspeicheroptimiert|8|64|5461|10|10923|
|Arbeitsspeicheroptimiert|16|128|10923|10|21845|
|Arbeitsspeicheroptimiert|32|256|21845|10|43691|
|Arbeitsspeicheroptimiert|48|384|32768|10|65536|
|Arbeitsspeicheroptimiert|64|504|43008|10|86016|

Wenn Verbindungen den Grenzwert übersteigen, erhalten Sie möglicherweise den folgenden Fehler:
> FEHLER 1040 (08004): Zu viele Verbindungen

> [!IMPORTANT]
>Für eine optimale Erfahrung empfehlen wir, dass Sie einen Verbindungsspooler wie ProxySQL verwenden, um Verbindungen effizient zu verwalten.

Das Erstellen neuer Clientverbindungen mit MySQL nimmt Zeit in Anspruch, und nach der Herstellung belegen diese Verbindungen Datenbankressourcen, auch wenn Sie sich im Leerlauf befinden. Die meisten Anwendungen fordern viele kurzlebige Verbindungen an, was diese Situation erschwert. Das Ergebnis sind weniger Ressourcen, die für ihre tatsächliche Workload verfügbar sind, was zu verringerter Leistung führt. Ein Verbindungspooler, der Verbindungen im Leerlauf reduziert und vorhandene Verbindungen wiederverwendet, hilft dabei, dies zu vermeiden. Weitere Informationen zum Einrichten von ProxySQL finden Sie in unserem [Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL ist ein Open Source-Communitytool. Es wird von Microsoft bestmöglich unterstützt. Wenden Sie sich an den [ProxySQL-Produktsupport](https://proxysql.com/services/support/), wenn Sie Produktionssupport mit autoritativen Hinweisen benötigen.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Wenn ein Fehler wie „Zeile zu groß (> 8126)“ angezeigt wird, sollten Sie den Parameter **innodb_strict_mode** auf OFF festlegen. Der Serverparameter **innodb_strict_mode** darf nicht global auf der Serverebene geändert werden, da die Daten bei Überschreitung einer Zeilendatengröße von 8.000 ohne Fehlermeldung gekürzt werden, was zu Datenverlusten führen kann. Es wird empfohlen, das Schema so zu ändern, dass es der Seitengrößenbeschränkung entspricht.

Dieser Parameter kann mithilfe von `init_connect` auf Sitzungsebene festgelegt werden. Informationen zum Festlegen von **innodb_strict_mode** auf Sitzungsebene finden Sie [Nicht aufgeführte Einstellungsparameter](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters).

> [!NOTE]
> Wenn Sie über einen Lesereplikatserver verfügen, wird die Replikation unterbrochen, wenn Sie **innodb_strict_mode** auf einem Quellserver auf Sitzungsebene auf OFF festlegen. Wir empfehlen, den Parameter auf OFF zu belassen, wenn Sie über Lesereplikate verfügen.

### <a name="time_zone"></a>time_zone

Bei der ersten Bereitstellung enthält ein flexibler Azure for MySQL-Server Systemtabellen für Zeitzoneninformationen, aber diese Tabellen sind nicht gefüllt. Die Zeitzonentabellen können durch Aufrufen der gespeicherten Prozedur `mysql.az_load_timezone` über ein Tool wie die MySQL-Befehlszeile oder MySQL Workbench aufgefüllt werden. Informationen zum Aufrufen der gespeicherten Prozedur und zum Festlegen der globalen Zeitzonen oder Zeitzonen auf Sitzungsebene finden Sie in den Artikeln für das [Azure-Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) und die [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter).

### <a name="binlog_expire_logs_seconds"></a>binlog_expire_logs_seconds 

In Azure Database for MySQL gibt dieser Parameter die Anzahl der Sekunden an, die der Dienst wartet, bevor die binäre Protokolldatei gelöscht wird.

Das binäre Protokoll enthält „Ereignisse“, die Datenbankänderungen beschreiben, z. B. Tabellenerstellungsvorgänge oder Änderungen an Tabellendaten. Es enthält auch Ereignisse für Anweisungen, die möglicherweise Änderungen vorgenommen haben. Das binäre Protokoll wird hauptsächlich zu zwei Zwecken verwendet: für Replikations- und Datenwiederherstellungsvorgänge.  In der Regel werden die binären Protokolle gelöscht, sobald das Handle von Dienst, Sicherung oder Replikatgruppe freigegeben wird. Bei mehreren Replikaten wird mit dem Löschen gewartet, bis das langsamste Replikat die Änderungen gelesen hat. Wenn binäre Protokolle für einen längeren Zeitraum beibehalten werden sollen, können Sie den Parameter „binlog_expire_logs_seconds“ konfigurieren. Wenn der Parameter „binlog_expire_logs_seconds“ auf 0 (Standardwert) festgelegt ist, erfolgt die Löschung, sobald das Handle für das binäre Protokoll freigegeben wird. Wenn „binlog_expire_logs_seconds“ > 0 ist, wird mit der Löschung gewartet, bis die konfigurierten Sekunden abgelaufen sind. Für Azure Database for MySQL werden verwaltete Features wie das Löschen von Binärdateien aus Sicherungen und Lesereplikaten intern behandelt. Wenn Sie die Daten aus dem Azure Database for MySQL-Dienst replizieren, muss dieser Parameter am primären Speicherort festgelegt werden, um zu vermeiden, dass binäre Protokolle gelöscht werden, bevor das Replikat die Änderungen des primären Speicherorts gelesen hat. Wenn Sie den Parameter „binlog_expire_logs_seconds“ auf einen höheren Wert festlegen, werden die binären Protokolle nicht schnell genug gelöscht und können zu einer Erhöhung der Speicherabrechnung führen. 

## <a name="non-modifiable-server-parameters"></a>Nicht änderbare Serverparameter

Auf dem Blatt „Serverparameter“ im Azure-Portal werden sowohl änderbare als auch nicht änderbare Serverparameter angezeigt. Die nicht änderbaren Serverparameter sind ausgegraut. Wenn Sie einen nicht änderbaren Serverparameter auf Sitzungsebene konfigurieren möchten, erfahren Sie im [Azure-Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)- oder [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters)-Artikel, wie Sie den Parameter auf Verbindungsebene mithilfe von `init_connect` festlegen.

## <a name="next-steps"></a>Nächste Schritte

- Konfigurieren von [Serverparametern im Azure-Portal](./how-to-configure-server-parameters-portal.md)
- Konfigurieren von [Serverparametern über die Azure-Befehlszeilenschnittstelle](./how-to-configure-server-parameters-cli.md)
