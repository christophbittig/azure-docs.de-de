---
title: Migrieren von Amazon RDS for MySQL zu Azure Database for MySQL mit Datenreplikation
description: Dieser Artikel beschreibt die Migration von Amazon RDS for MySQL zu Azure Database for MySQL unter Verwendung von Datenreplikation.
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 01d3832d8c643a6870239534377cd282feef01f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082472"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>Migrieren von Amazon RDS for MySQL zu Azure Database for MySQL mit Datenreplikation

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

Sie können Methoden wie die Sicherung und Wiederherstellung mit MySQL, MySQL Workbench-Export und -lmport oder Azure Database Migration Service verwenden, um Ihre MySQL-Datenbanken zu Azure Database for MySQL zu migrieren. Wenn Sie eine Kombination aus Open-Source-Tools wie mysqldump oder mydumper und myloader mit Datenreplikation verwenden, können Sie Ihre Workloads mit minimaler Ausfallzeit migrieren.

Die Datenreplikation ist ein Verfahren, bei dem basierend auf der position-Methode für binäre Protokolldateien Datenänderungen vom Quellserver auf den Zielserver repliziert werden. In diesem Szenario schreibt die MySQL-Instanz, die als Quelle fungiert (und von der die Datenbankänderungen stammen), Aktualisierungen und Änderungen als *Ereignisse* in das binäre Protokoll. Die Informationen im binären Protokoll werden je nach aufgezeichneten Datenbankänderungen in unterschiedlichen Protokollierungsformaten gespeichert. Replikate sind so konfiguriert, dass sie das binäre Protokoll aus der Quelle lesen und die Ereignisse im binären Protokoll in der lokalen Datenbank des Replikats ausführen.

Wenn Sie die [Datenreplikation](../mysql/flexible-server/concepts-data-in-replication.md) einrichten, um Daten von einem MySQL-Quellserver auf einen MySQL-Zielserver zu synchronisieren, können Sie eine selektive Übernahme Ihrer Anwendungen vom primären Server (oder aus der Quelldatenbank) auf den Replikatserver (oder in die Zieldatenbank) durchführen.

In diesem Tutorial erfahren Sie, wie Sie die Datenreplikation zwischen einem Quellserver mit Amazon Relational Database Service (RDS) for MySQL und einem Zielserver mit Azure Database for MySQL einrichten.

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Bevor Sie mit diesem Tutorial beginnen, sollten Sie die Auswirkungen des Standorts und der Kapazität des Clientcomputers, den Sie für die Durchführung des Vorgangs verwenden, auf die Leistung berücksichtigen.

### <a name="client-location"></a>Clientstandort

Führen Sie Sicherungs- oder Wiederherstellungsvorgänge von einem Clientcomputer aus, der am selben Standort wie der Datenbankserver eingesetzt wird:

- Bei Azure Database for MySQL-Servern sollte sich der Clientcomputer im gleichen virtuellen Netzwerk und in der gleichen Verfügbarkeitszone wie der Zieldatenbankserver befinden.
- Bei Amazon RDS-Quelldatenbankinstanzen sollte sich die Clientinstanz in derselben Amazon Virtual Private Cloud und Verfügbarkeitszone wie der Quelldatenbankserver befinden.
Im zuvor genannten Fall können Sie Sicherungsdateien zwischen Clientcomputern mithilfe von Dateiübertragungsprotokollen wie FTP oder SFTP verschieben oder sie in Azure Blob Storage hochladen. Zur Reduzierung der Gesamtmigrationszeit komprimieren Sie Dateien vor der Übertragung.

### <a name="client-capacity"></a>Clientkapazität

Ganz gleich, wo sich der Clientcomputer befindet, ist eine ausreichende Compute-, E/A- und Netzwerkkapazität erforderlich, um die angeforderten Vorgänge ausführen zu können. Die allgemeinen Empfehlungen lauten wie folgt:

- Wenn die Sicherung oder Wiederherstellung eine Echtzeitverarbeitung von Daten beinhaltet, z. B. Komprimierung oder Dekomprimierung, wählen Sie eine Instanzklasse mit mindestens einem CPU-Kern pro Sicherungs- oder Wiederherstellungsthread aus.
- Stellen Sie sicher, dass der Clientinstanz eine ausreichende Netzwerkbandbreite zur Verfügung steht. Verwenden Sie Instanztypen, die das Feature für beschleunigten Netzwerkbetrieb unterstützen. Weitere Informationen finden Sie im Abschnitt „Beschleunigter Netzwerkbetrieb“ im [Leitfaden für den Azure-VM-Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md).
- Stellen Sie sicher, dass die Speicherebene des Clientcomputers die erwartete Lese-/Schreibkapazität bietet. Es wird empfohlen, einen virtuellen Azure-Computer mit SSD Premium-Speicher zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [mysqlclient](https://dev.mysql.com/downloads/) auf Ihrem Clientcomputer, um eine Sicherungskopie zu erstellen und einen Wiederherstellungsvorgang auf Ihrem Azure Database for MySQL-Zielserver durchzuführen.
- Bei größeren Datenbanken installieren Sie [mydumper und myloader](https://centminmod.com/mydumper.html) zum parallelen Sichern und Wiederherstellen von Datenbanken.

    > [!NOTE]
    > Mydumper kann nur auf Linux-Distributionen ausgeführt werden. Weitere Informationen finden Sie unter [Installieren von mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader).

- Erstellen Sie eine Instanz des Azure Database for MySQL-Servers mit der Version 5.7 oder 8.0.

    > [!IMPORTANT]
    > Wenn Ihr Ziel eine Instanz von Azure Database for MySQL Flexible Server mit zonenredundanter Hochverfügbarkeit ist, beachten Sie, dass die Datenreplikation für diese Konfiguration nicht unterstützt wird. Als Problemumgehung können Sie bei der Servererstellung zonenredundante Hochverfügbarkeit auf folgende Weise einrichten:
    >
    > 1. Erstellen Sie den Server mit aktivierter zonenredundanter Hochverfügbarkeit.
    > 1. Deaktivieren Sie die Hochverfügbarkeit.
    > 1. Befolgen Sie die Anweisungen im Artikel zum Einrichten der Datenreplikation.
    > 1. Entfernen Sie die Konfiguration der Datenreplikation nach der Übernahme.
    > 1. Aktivieren Sie die Hochverfügbarkeit.

Vergewissern Sie sich, dass verschiedene Parameter und Features wie nachfolgend beschrieben konfiguriert und eingerichtet sind:

- Aus Kompatibilitätsgründen sollten Quell- und Zieldatenbankserver dieselbe MySQL-Version verwenden.
- Haben Sie in jeder Tabelle einen Primärschlüssel. Ein Fehlen von Primärschlüsseln in Tabellen kann den Replikationsprozess verlangsamen.
- Stellen Sie sicher, dass der Zeichensatz der Quell- und der Zieldatenbank identisch ist.
- Legen Sie den Parameter `wait_timeout` auf eine angemessene Zeit fest. Die Zeit hängt von der Datenmenge oder Workload ab, die Sie importieren oder migrieren möchten.
- Vergewissern Sie sich, dass alle Ihre Tabellen InnoDB verwenden. Der Azure Database for MySQL-Server unterstützt nur die InnoDB-Speicher-Engine.
- Bei Tabellen mit vielen sekundären Indizes oder bei großen Tabellen sind die Auswirkungen des Leistungsaufwands während der Wiederherstellung besonders sichtbar. Ändern Sie die Sicherungsdateien so, dass `CREATE TABLE`-Anweisungen keine Sekundärschlüsseldefinitionen enthalten. Erstellen Sie nach dem Importieren der Daten sekundäre Indizes neu, um Leistungseinbußen während des Wiederherstellungsprozesses zu vermeiden.

Zum Vorbereiten der Datenreplikation gehen Sie folgendermaßen vor:

- Überprüfen Sie, ob der Azure Database for MySQL-Zielserver über Port 3306 mit dem Amazon RDS for MySQL-Quellserver verbunden werden kann.
- Stellen Sie sicher, dass der Amazon RDS for MySQL-Quellserver sowohl eingehenden als auch ausgehenden Datenverkehr auf Port 3306 zulässt.
- Stellen Sie sicher, dass Sie [Site-to-Site Konnektivität](../vpn-gateway/tutorial-site-to-site-portal.md) zu Ihrem Quellserver bereitstellen, indem Sie entweder [Azure ExpressRoute](../expressroute/expressroute-introduction.md) oder [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) verwenden. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Azure Virtual Network](../virtual-network/index.yml). Sehen Sie sich auch die Schnellstartartikel mit Schritt-für-Schritt-Anleitungen an.
- Konfigurieren Sie die Netzwerksicherheitsgruppen Ihres Quelldatenbankservers so, dass die IP-Adresse des Azure Database for MySQL-Zielservers zulässig ist.

> [!IMPORTANT]
> Wenn für die Quellinstanz von Amazon RDS for MySQL der GTID-Modus (GTID_mode) auf EIN festgelegt ist, muss auch für die Zielinstanz von Azure Database for MySQL Flexible Server der GTID-Modus auf EIN festgelegt sein.

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Konfigurieren Sie die Zielinstanz von Azure Database for MySQL

Zum Konfigurieren der Zielinstanz von Azure Database for MySQL (das Ziel für die Datenreplikation) gehen Sie folgendermaßen vor:

1. Setzen Sie den Parameterwert für `max_allowed_packet` auf den Höchstwert von **1073741824**, also 1 GB. Dieser Wert verhindert Überlaufprobleme im Zusammenhang mit langen Zeilen.
1. Setzen Sie die Parameter `slow_query_log`, `general_log`, `audit_log_enabled` und `query_store_capture_mode` während der Migration auf **AUS**, um jeglichen Overhead im Zusammenhang mit der Abfrageprotokollierung zu vermeiden.
1. Skalieren Sie die Computegröße des Azure Database for MySQL-Zielservers auf das Maximum von 64 virtuellen Kernen hoch. Diese Größe bietet mehr Computeressourcen, wenn Sie die Datenbanksicherungskopie vom Quellserver wiederherstellen.

    Sie können die Computegröße nach Abschluss der Migration jederzeit wieder an die Anforderungen Ihrer Anwendung anpassen.

1. Erhöhen Sie die Speichergröße, um mehr IOPS während der Migration zu erhalten, oder erhöhen Sie die maximalen IOPS für die Migration.

   > [!NOTE]
   > Die verfügbaren maximalen IOPS werden durch die Computegröße bestimmt. Weitere Informationen finden Sie im Abschnitt IOPS in [Compute und Speicheroptionen in Azure Database for MySQL–Flexible Server](../mysql/flexible-server/concepts-compute-storage.md#iops).

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>Konfigurieren Sie den Amazon RDS for MySQL-Quellserver

Zum Vorbereiten und Konfigurieren des in Amazon RDS gehosteten MySQL-Servers (die *Quelle* für die Datenreplikation) gehen Sie folgendermaßen vor:

1. Vergewissern Sie sich, dass die binäre Protokollierung auf dem Amazon RDS for MySQL-Quellserver aktiviert ist. Überprüfen Sie, ob automatisierte Sicherungen aktiviert sind, oder stellen Sie sicher, dass ein Lesereplikat für den Amazon RDS for MySQL-Quellserver vorhanden ist.

1. Stellen Sie sicher, dass die binären Protokolldateien auf dem Quellserver so lange beibehalten werden, bis die Änderungen auf der Zielinstanz von Azure Database for MySQL angewendet werden.

    Bei der Datenreplikation verwaltet Azure Database for MySQL den Replikationsprozess nicht.

1. Wenn Sie die Aufbewahrung binärer Protokolle auf dem Amazon RDS-Quellserver überprüfen möchten, um die Anzahl der Stunden zu ermitteln, über die binäre Protokolle aufbewahrt werden, rufen Sie die gespeicherte Prozedur `mysql.rds_show_configuration` auf:

    ```
    mysql> call mysql.rds_show_configuration;
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | name                   | value | description                                                                                               |
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | binlog retention hours | 24    | binlog retention hours specifies the duration in hours before binary logs are automatically deleted.      |
    | source delay           | 0     | source delay specifies replication delay in seconds between current instance and its master.              |
    | target delay           | 0     | target delay specifies replication delay in seconds between current instance and its future read-replica. |
    +------------------------+-------            +-----------------------------------------------------------------------------------------------------------+
    3 rows in set (0.00 sec)
    ```
1. Zum Konfigurieren der Aufbewahrungsdauer für binäre Protokolle führen Sie die gespeicherte Prozedur `rds_set_configuration` aus, um sicherzustellen, dass die binären Protokolle auf dem Quellserver über die gewünschte Zeitdauer aufbewahrt werden. Beispiel:

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours', 96);
    ```

    Wenn Sie eine Sicherungskopie erstellen und dann wiederherstellen, können Sie mithilfe des oben genannten Befehls die Deltaänderungen schnell nachholen.

   > [!NOTE]
   > Vergewissern Sie sich, dass auf dem Quellserver basierend auf der festgelegten Aufbewahrungsdauer ausreichend Speicherplatz für die Speicherung der binären Protokolle vorhanden ist.

Es gibt zwei Möglichkeiten, eine Sicherungskopie der Daten vom Amazon RDS for MySQL-Quellserver zu erfassen. Ein Ansatz besteht darin, eine Sicherungskopie der Daten direkt vom Quellserver zu erfassen. Beim anderen Ansatz wird eine Sicherungskopie von einem Amazon RDS for MySQL-Lesereplikat erfasst.

- Wenn Sie eine Sicherungskopie der Daten direkt vom Quellserver erfassen möchten, gehen Sie folgendermaßen vor:

    1. Stellen Sie sicher, dass Sie die Schreibvorgänge der Anwendung für einige Minuten anhalten, um eine im Hinblick auf Transaktionen konsistente Sicherungskopie der Daten zu erhalten.

       Sie können auch den Parameter `read_only` vorübergehend auf den Wert **1** setzen, damit bei der Erfassung einer Sicherungskopie der Daten keine Schreibvorgänge verarbeitet werden.

    1. Nachdem Sie die Schreibvorgänge auf dem Quellserver angehalten haben, erfassen Sie den Namen und Offset der binären Protokolldatei, indem Sie den Befehl `Mysql> Show master status;` ausführen.
    1. Speichern Sie diese Werte, um die Replikation von Ihrem Azure Database for MySQL starten.
    1. Führen Sie zum Erstellen einer Sicherungskopie der Daten `mysqldump` mit dem folgenden Befehl aus:

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- Wenn das Anhalten von Schreibvorgängen auf dem Quellserver keine Option ist oder die Leistung der Datensicherung auf dem Quellserver nicht akzeptabel ist, erfassen Sie wie folgt eine Sicherungskopie auf einem Replikatserver:

    1. Erstellen Sie ein Amazon MySQL-Lesereplikat mit der gleichen Konfiguration wie der Quellserver. Erstellen Sie dann dort die Sicherungskopie.
    1. Lassen Sie das Amazon RDS for MySQL-Lesereplikat mit dem Amazon RDS for MySQL-Quellserver gleichziehen.
    1. Wenn die Replikationsverzögerung auf dem Lesereplikat **0** erreicht, halten Sie die Replikation durch Aufruf der gespeicherten Prozedur `mysql.rds_stop_replication` an.

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    1. Stellen Sie bei angehaltener Replikation eine Verbindung mit dem Replikat her. Führen Sie dann den Befehl `SHOW SLAVE STATUS` aus, um den aktuellen binären Protokolldateinamen aus dem Feld **Relay_Master_Log_File** und die Position der Protokolldatei aus dem Feld **Exec_Master_Log_Pos** abzurufen.
    1. Speichern Sie diese Werte, um die Replikation von Ihrem Azure Database for MySQL starten.
    1. Zum Erstellen einer Sicherungskopie der Daten aus dem Amazon RDS for MySQL-Lesereplikat führen Sie `mysqldump` mit dem folgenden Befehl aus:

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > Sie können auch mydumper verwenden, um eine parallelisierte Sicherungskopie Ihrer Daten aus Ihrer Amazon RDS for MySQL-Quelldatenbank zu erfassen. Weitere Informationen finden Sie unter [Migrieren großer Datenbanken zu Azure Database for MySQL mit mydumper/myloader](../mysql/concepts-migrate-mydumper-myloader.md).

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Verknüpfen des Quell- und Replikatservers zum Starten der Datenreplikation

1. Zum Wiederherstellen der Datenbank mithilfe der nativen Wiederherstellung von mysql führen Sie den folgenden Befehl aus:

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > Wenn Sie stattdessen myloader verwenden, finden Sie Informationen dazu unter [Migrieren großer Datenbanken zu Azure Database for MySQL mit mydumper/myloader](../mysql/concepts-migrate-mydumper-myloader.md).

1. Melden Sie sich beim Amazon RDS for MySQL-Quellserver an, und richten Sie einen Replikationsbenutzer ein. Ersteilen Sie diesem Benutzer dann die erforderlichen Berechtigungen.

    - Wenn Sie SSL verwenden, führen Sie die folgenden Befehle aus:

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - Wenn Sie SSL nicht verwenden, führen Sie die folgenden Befehle aus:

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    Alle Datenreplikationsfunktionen erfolgen über gespeicherte Prozeduren. Informationen zu allen Prozeduren finden Sie unter [Gespeicherte Prozeduren für die Datenreplikation](../mysql/reference-stored-procedures.md#data-in-replication-stored-procedures). Sie können diese gespeicherten Prozeduren in der MySQL-Shell oder der MySQL Workbench ausführen.

1. Um den Amazon RDS for MySQL-Quellserver und den Azure Database for MySQL-Zielserver zu verknüpfen, melden Sie sich beim Azure Database for MySQL-Zielserver an. Legen Sie den Amazon RDS for MySQL-Server als Quellserver fest, indem Sie den folgenden Befehl ausführen:

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

1. Um die Replikation zwischen dem Amazon RDS for MySQL-Quellserver und dem Azure Database for MySQL-Zielserver zu starten, führen Sie den folgenden Befehl aus:

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

1. Um den Status der Replikation zu überprüfen, führen Sie auf dem Replikationsserver den folgenden Befehl aus:

    ```
    Mysql> show slave status\G
    ```

    Wenn der Status der Parameter `Slave_IO_Running` und `Slave_SQL_Running` **Yes** ist, wurde die Replikation gestartet und befindet sich im Ausführungszustand.

1. Überprüfen Sie den Wert des Parameters `Seconds_Behind_Master`, um festzustellen, wie weit der Zielserver verzögert ist.

    Ist der Wert **0**, hat der Zielserver alle Aktualisierungen vom Quellserver verarbeitet. Beträgt der Wert etwas anderes als **0**, verarbeitet der Zielserver noch Updates.

## <a name="ensure-a-successful-cutover"></a>Sicherstellen einer erfolgreichen Übernahme

Zum Sicherstellen einer erfolgreichen Übernahme gehen Sie folgendermaßen vor:

1. Konfigurieren Sie die entsprechenden Anmeldungen und Berechtigungen auf Datenbankebene auf dem Azure Database for MySQL-Zielserver.
1. Halten Sie die Schreibvorgänge auf dem Amazon RDS for MySQL-Quellserver an.
1. Stellen Sie sicher, dass der Azure Database for MySQL-Zielserver mit dem Quellserver gleichgezogen hat und dass der Wert `Seconds_Behind_Master` ist **0** von `show slave status`.
1. Rufen Sie die gespeicherte Prozedur `mysql.az_replication_stop` auf, um die Replikation zu beenden, da alle Änderungen auf den Azure Database for MySQL-Zielserver repliziert wurden.
1. Rufen Sie `mysql.az_replication_remove_master` auf, um die Konfiguration der Datenreplikation zu entfernen.
1. Leiten Sie Clients und Client-Anwendungen auf den Azure Database for MySQL-Zielserver um.

An diesem Punkt ist die Migration abgeschlossen. Ihre Anwendungen sind mit dem Server verbunden, auf dem Azure Database for MySQL ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Sehen Sie sich das Video zum [Einfachen Migrieren von MySQL/PostgreSQL-Apps zum verwalteten Azure-Dienst](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) an. In diesem Video wird Ihnen anhand einer Demo gezeigt, wie MySQL-Apps zu Azure Database for MySQL migriert werden.