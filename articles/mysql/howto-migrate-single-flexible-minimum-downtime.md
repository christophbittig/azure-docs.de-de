---
title: 'Tutorial: Migrieren von Azure Database for MySQL Single Server zu Azure Database for MySQL Flexible Server mit minimaler Downtime'
description: In diesem Artikel wird beschrieben, wie Sie eine Migration von Azure Database for MySQL Single Server zu Azure Database for MySQL Flexible Server mit minimaler Downtime durchführen können.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: 3bc4ee8d7d7491e577fba2e29d905262e37764cb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339540"
---
# <a name="tutorial-migrate-azure-database-for-mysql--single-server-to-azure-database-for-mysql--flexible-server-with-minimal-downtime"></a>Tutorial: Migrieren von Azure Database for MySQL Single Server zu Azure Database for MySQL Flexible Server mit minimaler Downtime

Sie können eine Instanz von Azure Database for MySQL Single Server zu Azure Database for MySQL Flexible Server mit minimaler Downtime auf Ihren Anwendungen migrieren, indem Sie eine Kombination aus Open-Source-Tools wie mydumper/myloader und der Datenreplikation nutzen.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

Die Datenreplikation ist ein Verfahren, bei dem basierend auf der position-Methode für binäre Protokolldateien Datenänderungen vom Quellserver auf den Zielserver repliziert werden. In diesem Szenario schreibt die MySQL-Instanz, die als Quelle fungiert (und von der die Datenbankänderungen stammen), Updates und Änderungen als „Ereignisse“ in das binäre Protokoll. Die Informationen im binären Protokoll werden je nach aufgezeichneten Datenbankänderungen in unterschiedlichen Protokollierungsformaten gespeichert. Replikate sind so konfiguriert, dass sie das binäre Protokoll aus der Quelle lesen und die Ereignisse im binären Protokoll in der lokalen Datenbank des Replikats ausführen.

Wenn Sie die Datenreplikation einrichten, um Daten aus einer Azure Database for MySQL-Instanz mit einer anderen zu synchronisieren, können Sie eine selektive Übernahme Ihrer Anwendungen vom primären Server (oder Quelldatenbank) auf den Replikatserver (oder die Zieldatenbank) durchführen.

In diesem Tutorial verwenden Sie mydumper/myloader und die Datenreplikation zum Migrieren einer Beispieldatenbank ([classicmodels](https://www.mysqltutorial.org/mysql-sample-database.aspx)) aus einer Instanz von Azure Database for MySQL Single Server zu einer Instanz von Azure Database for MySQL Flexible Server. Anschließend synchronisieren Sie die Daten.

In diesem Tutorial lernen Sie Folgendes:

* Konfigurieren der Netzwerkeinstellungen für die Datenreplikation für verschiedene Szenarios
* Konfigurieren der Datenreplikation zwischen dem primären Server und dem Replikatserver
* Testen der Replikation
* Übernahme zum Abschließen der Migration

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Eine Instanz von Azure Database for MySQL Single Server mit Version 5.7 oder 8.0
    > [!Note]
    > Wenn Sie Azure Database for MySQL Single Server Version 5.6 nutzen, führen Sie für Ihre Instanz ein Upgrade auf Version 5.7 durch, und konfigurieren Sie dann die Datenreplikation. Weitere Informationen finden Sie unter [Hauptversionsupgrade in Azure Database for MySQL Single Server](how-to-major-version-upgrade.md).
* Eine Instanz von Azure Database for MySQL Flexible Server. Weitere Informationen finden Sie im Artikel [Schnellstart: Verwenden des Azure-Portals zum Erstellen einer Azure Database for MySQL Flexible Server-Instanz](./flexible-server/quickstart-create-server-portal.md).
    > [!Note]
    > Die Konfiguration der Datenreplikation für Server mit zonenredundanter Hochverfügbarkeit wird nicht unterstützt. Führen Sie die folgenden Schritte aus, wenn Sie zonenredundante Hochverfügbarkeit für Ihren Zielserver möchten:
    >
    > 1. Server mit aktivierter zonenredundanter Hochverfügbarkeit erstellen
    > 2. Hochverfügbarkeit deaktivieren
    > 3. Anweisungen im Artikel zum Einrichten der Datenreplikation befolgen
    > 4. Konfiguration der Datenreplikation nach der Übernahme entfernen
    > 5. Hochverfügbarkeit aktivieren
    >
    > *Stellen Sie sicher, dass **[GTID_Mode](./concepts-read-replicas.md#global-transaction-identifier-gtid)** auf den Quell- und Zielservern über die gleiche Einstellung verfügt.*

* Herstellen einer Verbindung und Erstellen einer Datenbank mithilfe von MySQL Workbench. Weitere Informationen finden Sie im Artikel [Schnellstart: Verwenden von MySQL Workbench zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL – Flexible Server](./flexible-server/connect-workbench.md).
* Sicherstellen, dass Sie in derselben Region (oder im gleichen VNet mit privatem Zugriff) über eine Azure-VM verfügen, auf der Linux ausgeführt wird und die Ihre Quell- und Zieldatenbanken hostet
* Installieren des mysql-Clients oder von MySQL Workbench (Clienttools) auf Ihrer Azure-VM. Stellen Sie sicher, dass Sie sowohl mit dem primären Server als auch dem Replikatserver eine Verbindung herstellen können. Im Rahmen dieses Artikels wird der MySQL-Client installiert.
* Installieren von mydumper/myloader auf Ihrer Azure-VM. Weitere Informationen finden Sie im Artikel zu [mydumper/myloader](concepts-migrate-mydumper-myloader.md).
* Herunterladen und Ausführen des Beispieldatenbankskripts für die [classicmodels](https://www.mysqltutorial.org/wp-content/uploads/2018/03/mysqlsampledatabase.zip)-Datenbank auf dem Quellserver

## <a name="configure-networking-requirements"></a>Konfigurieren von Netzwerkanforderungen

Sie müssen zum Konfigurieren der Datenreplikation sicherstellen, dass das Ziel über Port 3306 eine Verbindung mit der Quelle herstellen kann. Führen Sie basierend auf dem Typ des in der Quelle eingerichteten Endpunkts die entsprechenden folgenden Schritte aus.

* Wenn für die Quelle ein öffentlicher Endpunkt aktiviert ist, müssen Sie sicherstellen, dass das Ziel eine Verbindung mit der Quelle herstellen kann, indem Sie die Option „Zugriff auf Azure-Dienste erlauben“ in der Firewallregel aktivieren. Weitere Informationen finden Sie unter [Firewallregeln für einen Azure Database for MySQL-Server](./concepts-firewall-rules.md#connecting-from-azure). 
* Wenn für die Quelle ein privater Endpunkt und die Option [„Öffentlichen Zugriff verweigern“](concepts-data-access-security-private-link.md#deny-public-access-for-azure-database-for-mysql) aktiviert ist, installieren Sie die private Verbindung im gleichen VNet, in dem das Ziel gehostet wird. Weitere Informationen finden Sie unter [Private Link – Azure Database for MySQL](concepts-data-access-security-private-link.md).

## <a name="configure-data-in-replication"></a>Konfigurieren der Datenreplikation

Führen Sie die folgenden Schritte aus, um die Datenreplikation zu konfigurieren:

1. Melden Sie sich bei der Azure-VM an, auf der Sie das mysql-Clienttool installiert haben.

2. Stellen Sie mithilfe des mysql-Clienttools eine Verbindung zwischen Quelle und Ziel her.

3. Verwenden Sie das mysql-Clienttool, um zu bestimmen, ob log_bin für die Quelle aktiviert ist, indem Sie den folgenden Befehl ausführen:

    ```sql
    SHOW VARIABLES LIKE 'log_bin';
    ```

    > [!Note]
    > Im Fall von Azure Database for MySQL Single Server mit dem großen Speicher, der bis zu 16 TB unterstützt, ist diese Option standardmäßig aktiviert.

    > [!Tip]
    > Bei Azure Database for MySQL Single Server mit Unterstützung für bis zu 4 TB ist die Option nicht standardmäßig aktiviert. Wenn Sie jedoch ein [Lesereplikat](howto-read-replicas-portal.md) für den Quellserver höher stufen und dann das Lesereplikat löschen, wird der Parameter auf „ON“ festgelegt.

4. Erstellen Sie basierend auf der SSL-Erzwingung für den Quellserver einen Benutzer auf dem Quellserver mit der Replikationsberechtigung, indem Sie den entsprechenden Befehl ausführen.

    Führen Sie bei Verwendung von SSL den folgenden Befehl aus:

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
    ```

    Wenn Sie SSL nicht verwenden, führen Sie den folgenden Befehl aus:

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
    ```

5. Führen Sie zum Sichern der Datenbank mithilfe von mydumper den folgenden Befehl auf der Azure-VM aus, auf der mydumper\myloader installiert ist:

    ```bash
    $ mydumper --host=<primary_server>.mysql.database.azure.com --user=<username>@<primary_server> --password=<Password> --outputdir=./backup --rows=100 -G -E -R -z --trx-consistency-only --compress --build-empty-files --threads=16 --compress-protocol --ssl  --regex '^(classicmodels\.)' -L mydumper-logs.txt
    ```

    > [!Tip]
    > Die Option **--trx-consistency-only** ist für die Transaktionskonsistenz während der Sicherung erforderlich.
    >
    > * Dies ist das mydumper-Äquivalent für „--single-transaction“ von mysqldump.
    > * Die Option ist nützlich, wenn es sich bei all Ihren Tabellen um InnoDB handelt.
    > * Der Hauptthread muss die globale Sperre nur so lange speichern, bis die Sicherungsthreads eine Transaktion starten können.
    > * Die Option bietet die kürzeste Dauer der globalen Sperre.

    Der Hauptthread muss die globale Sperre nur so lange speichern, bis die Sicherungsthreads eine Transaktion starten können.

    Die Variablen in diesem Befehl werden im Folgenden erläutert:

    * **--host:** Dies ist der Name des primären Servers.
    * **--user:** Dies ist der Name eines Benutzers (im Format username@servername, da auf dem primären Server Azure Database for MySQL Single Server ausgeführt wird). Sie können den Serveradministrator oder einen Benutzer mit SELECT- und RELOAD-Berechtigungen verwenden.
    * **--Password:** Dies ist das Kennwort des zuvor genannten Benutzers.

    Weitere Informationen zur Verwendung von mydumper finden Sie im Artikel zu [mydumper/myloader](concepts-migrate-mydumper-myloader.md).

6. Lesen Sie die Metadatendatei, um den Namen und Versatz der binären Protokolldatei zu ermitteln, indem Sie den folgenden Befehl ausführen:

    ```bash
    $ cat ./backup/metadata 
    ```

    In diesem Befehl bezieht sich **./backup** auf das Ausgabeverzeichnis, das im vorherigen Schritt im Befehl verwendet wurde.

    Die Ergebnisse sollten wie in der folgenden Abbildung dargestellt angezeigt werden:

    :::image type="content" source="./media/howto-migrate-single-flexible-minimum-downtime/metadata.png" alt-text="Fortlaufende Synchronisierung mit dem Azure Database Migration Service":::

    Notieren Sie sich unbedingt den Namen der Binärdatei für die nachfolgenden Schritte.

7. Stellen Sie die Datenbank mithilfe von myloader wieder her, indem Sie den folgenden Befehl ausführen:

    ```bash
    $ myloader --host=<servername>.mysql.database.azure.com --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=100 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

    Die Variablen in diesem Befehl werden im Folgenden erläutert:

    * **--host:** Dies ist der Name des Replikatservers
    * **--user:** Dies ist der Name eines Benutzers. Sie können den Serveradministrator oder einen Benutzer mit Lese- bzw. Schreibberechtigung verwenden, um die Schemas und Daten in der Datenbank wiederherzustellen.
    * **--Password:** Dies ist das Kennwort für den zuvor genannten Benutzer.

8. Stellen Sie abhängig von der SSL-Erzwingung auf dem primären Server mithilfe des mysql-Clienttools eine Verbindung mit dem Replikatserver her, und führen Sie die folgenden Schritte aus.

    * Wenn die SSL-Erzwingung aktiviert ist, gilt Folgendes:

        i. Laden Sie [hier](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) das Zertifikat herunter, das für die Kommunikation mit Ihrem Azure Database for MySQL-Server über SSL erforderlich ist.

        ii. Öffnen Sie die Datei in Editor, und fügen Sie den Inhalt in den Abschnitt „PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE“ ein.

        ```sql
        SET @cert = '-----BEGIN CERTIFICATE-----
        PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE 
        -----END CERTIFICATE-----'
        ```

        iii. Führen Sie zum Konfigurieren der Datenreplikation den folgenden Befehl aus:

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '<username>@<primary_server>', '<Password>', 3306, '<File_Name>', <Position>, @cert);
        ```

        > [!Note]
        > Bestimmen Sie die Position und den Dateinamen anhand der Informationen, die Sie in Schritt 6 erhalten haben.

    * Wenn die SSL-Erzwingung nicht aktiviert ist, führen Sie den folgenden Befehl aus:

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '<username>@<primary_server>', '<Password>', 3306, '<File_Name>', <Position>, ‘’);
        ```

9. Rufen Sie die unten aufgeführte gespeicherte Prozedur auf, um die Replikation vom Replikatserver aus zu starten.

    ```sql
    call  mysql.az_replication_start;
    ```

10. Führen Sie auf dem Replikatserver den folgenden Befehl aus, um den Replikationsstatus zu überprüfen:

     ```sql
     show slave status \G; 
     ```

    > [!Note]
    > Wenn Sie MySQL Workbench verwenden, ist der \G-Modifizierer nicht erforderlich.

    Wenn der Status von *Slave_IO_Running* und *Slave_SQL_Running* „Yes“ (Ja) lautet und der Wert von *Seconds_Behind_Master* 0 entspricht, funktioniert die Replikation problemlos. Seconds_Behind_Master gibt an, welche Verzögerung das Replikat aufweist. Wenn der Wert nicht 0 ist, verarbeitet das Replikat Updates.

## <a name="testing-the-replication-optional"></a>Testen der Replikation (optional)

Sie können überprüfen, ob die Änderungen an den Tabellen auf dem primären Server in das Replikat repliziert wurden, um sicherzustellen, dass die Datenreplikation ordnungsgemäß funktioniert.

1. Wählen Sie eine Tabelle aus, die zum Testen verwendet werden soll (z. B. die customers-Tabelle), und vergewissern Sie sich dann, dass die Anzahl der darin enthaltenen Einträge auf dem primären Server und dem Replikatserver identisch ist, indem Sie auf jedem Server den folgenden Befehl ausführen:

    ```
    select count(*) from customers;
    ```

2. Notieren Sie sich die Anzahl der Einträge für einen späteren Vergleich.

    Versuchen Sie zum Testen der Replikation, den customers-Tabellen auf dem primären Server einige Daten hinzuzufügen, und überprüfen Sie dann, ob die neuen Daten repliziert werden. In diesem Fall fügen Sie einer Tabelle auf dem primären Server zwei Zeilen hinzu und überprüfen dann, ob sie auf den Replikatserver repliziert wurden.

3. Fügen Sie in der customers-Tabelle auf dem primären Server Zeilen ein, indem Sie den folgenden Befehl ausführen:

    ```sql
    insert  into `customers`(`customerNumber`,`customerName`,`contactLastName`,`contactFirstName`,`phone`,`addressLine1`,`addressLine2`,`city`,`state`,`postalCode`,`country`,`salesRepEmployeeNumber`,`creditLimit`) values 
    (<ID>,'name1','name2','name3 ','11.22.5555','54, Add',NULL,'Add1',NULL,'44000','country',1370,'21000.00');
    ```

4. Rufen Sie zum Überprüfen des Replikationsstatus *show slave status \G* auf, um zu bestätigen, dass die Replikation wie erwartet durchgeführt wird.

5. Führen Sie auf dem Replikatserver den folgenden Befehl aus, um zu bestätigen, dass die Anzahl identisch ist:

    ```sql
    select count(*) from customers;
    ```

## <a name="ensure-a-successful-cutover"></a>Sicherstellen einer erfolgreichen Übernahme

Führen Sie die folgenden Aufgaben aus, um eine erfolgreiche Übernahme sicherzustellen:

1. Konfigurieren Sie die entsprechenden Firewallregeln und VNET-Regeln auf Serverebene, um eine Verbindung mit dem Zielserver herzustellen. Sie können die Firewallregeln für die [Quelle](howto-manage-firewall-using-portal.md) und das [Ziel](./flexible-server/how-to-manage-firewall-portal.md#create-a-firewall-rule-after-server-is-created) über das Portal vergleichen.
2. Konfigurieren Sie die entsprechenden Anmeldedaten und Berechtigungen auf Datenbankebene auf dem Zielserver. Sie können *SELECT * FROM mysql.user;* auf den Quell- und Zielservern ausführen, die verglichen werden sollen.
3. Stellen Sie sicher, dass alle eingehenden Verbindungen mit Azure Database for MySQL Single Server beendet werden.
    > [!Tip]
    > Sie können für Azure Database for MySQL Single Server den schreibgeschützten Zugriff festlegen.
4. Stellen Sie sicher, dass die Vorgänge auf dem primären Server und dem Replikatserver ohne Zeitverzögerung ausgeführt werden, indem Sie *show slave status \G* ausführen und bestätigen, dass der Wert für den Parameter *Seconds_Behind_Master* 0 lautet.
5. Leiten Sie Clients und Clientanwendungen an die Zielinstanz von Azure Database for MySQL Flexible Server um.
6. Führen Sie die letzte Übernahme durch, indem Sie die gespeicherte Prozedur „mysql.az_replication_stop“ ausführen, die die Replikation vom Replikatserver beendet.
7. Verwenden Sie *Call mysql.az_replication_remove_master*, um die Konfiguration für die Datenreplikation zu entfernen.

An diesem Punkt sind Ihre Anwendungen mit der neuen Instanz von Azure Database for MySQL Flexible Server verbunden, und Änderungen in der Quelle werden nicht mehr auf den Zielserver repliziert.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Datenreplikation unter [Replizieren von Daten in Azure Database for MySQL Flexible Server](flexible-server/concepts-data-in-replication.md) und [Konfigurieren der Datenreplikation in Azure Database for MySQL Flexible Server](./flexible-server/how-to-data-in-replication.md)
* Weitere Informationen zur [Problembehandlung bei häufig während oder nach der Migration zu Azure Database for MySQL auftretenden Fehlern](howto-troubleshoot-common-errors.md)
* Weitere Informationen unter [Tutorial: Durchführen einer Offlinemigration von MySQL zu Azure Database for MySQL mithilfe von DMS](../dms/tutorial-mysql-azure-mysql-offline-portal.md)
