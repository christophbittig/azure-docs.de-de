---
title: Einschränkungen – Azure Database for MySQL
description: Dieser Artikel beschreibt die Einschränkungen in Azure Database for MySQL, z.B. die Anzahl der Verbindungs- und Speichermoduloptionen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 5ba7559cdcfbb4f02ee99be6dce7997d7451f3e1
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674416"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Beschränkungen in Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

In den folgenden Abschnitten werden die Kapazitäts- und funktionalen Beschränkungen sowie Beschränkungen bei der Unterstützung der Speicher-Engine und von Datenmanipulationsanweisungen im Datenbankdienst beschrieben. Sehen Sie sich auch die [allgemeinen Einschränkungen](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) an, die für die MySQL-Datenbank-Engine gelten.

## <a name="server-parameters"></a>Serverparameter

> [!NOTE]
> Wenn Sie nach Minimal-/Maximalwerten für Serverparameter wie `max_connections` und `innodb_buffer_pool_size` suchen, finden Sie diese Informationen nun im Artikel zu **[Serverparametern](./concepts-server-parameters.md)** .

Azure Database für MySQL unterstützt das Anpassen einiger Serverparameter. Die Minimal- und Maximalwerte einiger Parameter (z. B. `max_connections`, `join_buffer_size`, `query_cache_size`) werden durch den Tarif und die virtuellen Kerne des Servers bestimmt. Im Artikel [Serverparameter](./concepts-server-parameters.md) finden Sie weitere Informationen zu diesen Grenzwerten.

Bei der ersten Bereitstellung enthält ein Azure for MySQL-Server Systemtabellen für Zeitzoneninformationen, aber diese Tabellen sind nicht gefüllt. Die Zeitzonentabellen können durch Aufrufen der gespeicherten Prozedur `mysql.az_load_timezone` über ein Tool wie die MySQL-Befehlszeile oder MySQL Workbench aufgefüllt werden. Informationen zum Aufrufen der gespeicherten Prozedur und zum Festlegen der globalen Zeitzonen oder Zeitzonen auf Sitzungsebene finden Sie in den Artikeln für das [Azure-Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) und die [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter).

Kennwort-Plug-Ins wie „validate_password“ und „caching_sha2_password“ werden vom Dienst nicht unterstützt.

## <a name="storage-engines"></a>Speicher-Engines

MySQL unterstützt viele Speicher-Engines. Für Azure Database for MySQL werden die folgenden Speicher-Engines unterstützt und nicht unterstützt:

### <a name="supported"></a>Unterstützt
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nicht unterstützt
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Berechtigungen und Unterstützung der Datenbearbeitung

Viele Serverparameter und -einstellungen können unbeabsichtigterweise die Serverleistung beeinträchtigen oder ACID-Eigenschaften des MySQL-Servers verschlechtern. Um die Dienstintegrität und die SLA auf Produktebene aufrechtzuerhalten, macht dieser Dienst nicht mehrere Rollen verfügbar. 

Der MySQL-Dienst gestattet keinen direkten Zugriff auf das zugrunde liegende Dateisystem. Einige Befehle zur Datenbearbeitung werden nicht unterstützt. 

### <a name="unsupported"></a>Nicht unterstützt

Folgendes wird nicht unterstützt:
- DBA-Rolle: Eingeschränkt. Alternativ können Sie den Administratorbenutzer verwenden (der bei Erstellung eines neuen Servers erstellt wird), der Ihnen die Ausführung der meisten DDL- und DML-Anweisungen ermöglicht. 
- SUPER-Berechtigung: Auf ähnliche Weise ist die [SUPER-Berechtigung](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) eingeschränkt.
- DEFINER: Erfordert erhöhte Berechtigungen zum Erstellen und ist beschränkt. Entfernen Sie beim Importieren von Daten mithilfe einer Sicherung die `CREATE DEFINER`-Befehle manuell oder mithilfe des Befehls `--skip-definer`, wenn Sie einen [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html) ausführen.
- Systemdatenbanken: Die [Systemdatenbank mysql](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) ist schreibgeschützt und wird zur Unterstützung verschiedener PaaS-Funktionen eingesetzt. Die `mysql`-Systemdatenbank kann nicht geändert werden.
- `SELECT ... INTO OUTFILE`: Wird im Dienst nicht unterstützt.
- `LOAD_FILE(file_name)`: Wird im Dienst nicht unterstützt.

### <a name="supported"></a>Unterstützt
- `LOAD DATA INFILE` wird unterstützt, jedoch muss der Parameter `[LOCAL]` angegeben und an einen UNC-Pfad (über das SMB-Protokoll eingebundene Azure Storage-Instanz) weitergeleitet werden. Wenn Sie eine MySQL-Clientversion ab 8.0 verwenden, müssen Sie außerdem den Parameter `-–local-infile=1` in der Verbindungszeichenfolge angeben.


## <a name="functional-limitations"></a>Funktionale Beschränkungen

### <a name="scale-operations"></a>Skalierungsvorgänge
- Die dynamische Skalierung auf den oder vom Basic-Tarif aus wird zurzeit nicht unterstützt.
- Die Verringerung der Größe des Serverspeichers wird nicht unterstützt.

### <a name="major-version-upgrades"></a>Upgrades von Hauptversionen
- [Ein Upgrade der Hauptversion werden nur für Upgrades von Version 5.6 auf 5.7 unterstützt](how-to-major-version-upgrade.md). Upgrades auf Version 8.0 werden noch nicht unterstützt.

### <a name="point-in-time-restore"></a>Point-in-Time-Wiederherstellung
- Wenn Sie das PITR-Feature verwenden, wird der neue Server mit den gleichen Konfigurationen erstellt wie der Server, auf dem er basiert.
- Die Wiederherstellung eines gelöschten Servers wird nicht unterstützt.

### <a name="vnet-service-endpoints"></a>VNET-Dienstendpunkte
- VNET-Dienstendpunkte werden nur für Server vom Typ „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.

### <a name="storage-size"></a>Speichergröße
- Informationen zu den Beschränkungen der Speichergröße pro Tarif finden Sie unter [Tarife](concepts-pricing-tiers.md#storage).

## <a name="current-known-issues"></a>Aktuelle bekannte Probleme
- Die MySQL-Serverinstanz zeigt die falsche Serverversion an, nachdem die Verbindung hergestellt wurde. Um die richtige Engine-Version der Serverinstanzen abzurufen, verwenden Sie den Befehl `select version();`.

## <a name="next-steps"></a>Nächste Schritte
- [Verfügbaren Funktionen auf den einzelnen Dienstebenen](concepts-pricing-tiers.md)
- [Unterstützte MySQL-Datenbankversionen](concepts-supported-versions.md)
