---
title: Migrieren von Amazon RDS for MySQL zu Azure Database for MySQL mit Datenreplikation
description: Dieser Artikel beschreibt die Migration von Amazon RDS for MySQL zu Azure Database for MySQL unter Verwendung von Datenreplikation.
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: d05806b9d69543fdd4f5584661a6b8b74ac1f01e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647155"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>Migrieren Sie Amazon RDS for MySQL zu Azure Database for MySQL unter Verwendung von Datenreplikation

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

Sie können verschiedene Methoden verwenden, wie z. B. MySQL Sicherungskopie und Wiederherstellung, MySQL Workbench Export/lmport oder Migration Service (DMS), um Ihre MySQL-Datenbanken zu Azure Database for MySQL zu migrieren. Wenn Sie jedoch eine Kombination aus Open-Source-Tools wie MySQL Sicherungskopie und Wiederherstellung oder mydumper/myloader mit Datenreplikation verwenden, können Sie Ihre Workloads mit minimaler Ausfallzeit migrieren.

Die Datenreplikation ist ein Verfahren, bei dem basierend auf der position-Methode für binäre Protokolldateien Datenänderungen vom Quellserver auf den Zielserver repliziert werden. In diesem Szenario schreibt die MySQL-Instanz, die als Quelle fungiert (und von der die Datenbankänderungen stammen), Updates und Änderungen als „Ereignisse“ in das binäre Protokoll. Die Informationen im binären Protokoll werden je nach aufgezeichneten Datenbankänderungen in unterschiedlichen Protokollierungsformaten gespeichert. Replikate sind so konfiguriert, dass sie das binäre Protokoll aus der Quelle lesen und die Ereignisse im binären Protokoll in der lokalen Datenbank des Replikats ausführen.

Wenn Sie die [Datenreplikation](/azure/mysql/flexible-server/concepts-data-in-replication) einrichten, um Daten aus einer Azure Database for MySQL-Instanz mit einer anderen zu synchronisieren, können Sie eine selektive Übernahme Ihrer Anwendungen vom primären Server (oder Quelldatenbank) auf den Replikatserver (oder die Zieldatenbank) durchführen.

In diesem Tutorium lernen Sie, wie Sie die Datenreplikation zwischen einem Quellserver mit Amazon RDS for MySQL und einem Zielserver mit Azure Database for MySQL einrichten.

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Bevor Sie mit diesem Tutorium beginnen, sollten Sie die Auswirkungen des Standorts und der Kapazität des Client-Computers, den Sie für die Durchführung des Vorgangs verwenden, auf die Leistung berücksichtigen.

### <a name="client-location"></a>Clientstandort

Führen Sie Sicherheitskopie/Wiederherstellungsoperationen von einem Client-Computer aus, der am selben Standort wie der Datenbankserver eingesetzt wird:

- Bei Azure Database for MySQL-Servern sollte sich der Client-Computer im gleichen VNet und in der gleichen Verfügbarkeitszone befinden wie der Zieldatenbankserver.
- Bei Amazon RDS-Quelldatenbankinstanzen sollte sich die Client-Instanz in derselben Amazon Virtual Private Cloud (Amazon VPC) und Verfügbarkeitszone wie der Quelldatenbankserver befinden.
Im obigen Fall können Sie Sicherungsdateien zwischen Client-Computern mithilfe von Dateiübertragungsprotokollen (z. B. FTP oder SFTP) verschieben oder sie in Azure Blob Storage hochladen. Um die Gesamtmigrationszeit zu verkürzen, können Sie die Dateien vor der Übertragung komprimieren.

### <a name="client-capacity"></a>Clientkapazität

Unabhängig davon, wo sich der Clientcomputer befindet, ist eine ausreichende Compute-, E/A- und Netzwerkkapazität erforderlich, um die angeforderten Vorgänge ausführen zu können. Die allgemeinen Empfehlungen lauten wie folgt:

- Wenn die Sicherungskopie oder die Wiederherstellung eine Echtzeitverarbeitung von Daten beinhaltet, z. B. Komprimierung oder Dekomprimierung, wählen Sie eine Instanzklasse mit mindestens einem CPU-Kern pro Sicherungskopie/Wiederherstellungs-Thread.
- Stellen Sie sicher, dass der Clientinstanz eine ausreichende Netzwerkbandbreite zur Verfügung steht. Wir empfehlen die Verwendung von Instanztypen, die beschleunigte Netzwerke unterstützen. Weitere Informationen finden Sie im Abschnitt „beschleunigter Netzwerkbetrieb“ im [Azure Virtual Machine Networking Guide](/azure/virtual-network/create-vm-accelerated-networking-cli).
- Stellen Sie sicher, dass die Speicherebene des Clientcomputers die erwartete Lese-/Schreibkapazität bietet; wir empfehlen einen virtuellen Azure-Computer mit SSD Premium-Speicher.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [mysqlclient](https://dev.mysql.com/downloads/) auf Ihrem Clientcomputer, um eine Sicherungskopie zu erstellen und einen Wiederherstellungsvorgang auf Ihrem Azure Database for MySQL-Zielserver durchzuführen.
- Für größere Datenbanken installieren Sie [mydumper/myloader](https://centminmod.com/mydumper.html) für paralleles Erstellen von Sicherheitskopien und Wiederherstellen von Datenbanken.

    > [!NOTE]
    > Mydumper kann nur auf Linux-Distributionen ausgeführt werden, siehe [Installieren von mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader).

- Erstellen Sie eine Instanz von Azure Database for MySQL Server mit der Version 5.7 oder 8.0.

    > [!IMPORTANT]
    > Wenn Ihr Ziel ein Azure Database for MySQL Flexible Server mit zonenredundanter Hochverfügbarkeit ist, beachten Sie, dass die Datenreplikation für diese Konfiguration nicht unterstützt wird. Als Ausweichlösung können Sie bei der Servererstellung zonenredundante Hochverfügbarkeit einrichten, indem Sie die folgenden Schritte ausführen:
    >
    > 1. Erstellen Sie den Server mit aktivierter zonenredundanter Hochverfügbarkeit.
    > 2. Deaktivieren Sie die Hochverfügbarkeit.
    > 3. Befolgen Sie die Anweisungen im Artikel zum Einrichten der Datenreplikation.
    > 4. Entfernen Sie die Konfiguration der Datenreplikation nach der Übernahme.
    > 5. Aktivieren Sie die Hochverfügbarkeit.

Es wird auch empfohlen, dass Sie sicherstellen, dass mehrere Parameter und Funktionen wie unten beschrieben richtig konfiguriert und eingerichtet sind.

- Aus Kompatibilitätsgründen empfehlen wir, dass die Quell- und Zieldatenbankserver dieselbe MySQL-Version verwenden.
- Haben Sie in jeder Tabelle einen Primärschlüssel. Ein Fehlen von Primärschlüsseln in Tabellen kann den Replikationsprozess verlangsamen.
- Stellen Sie sicher, dass der Zeichensatz der Quell- und der Zieldatenbank identisch ist.
- Stellen Sie den Parameter `wait_timeout` auf eine angemessene Zeit ein, die von der Datenmenge oder der Arbeitsauslastung abhängt, die Sie importieren oder migrieren möchten.
- Vergewissern Sie sich, dass alle Ihre Tabellen InnoDB verwenden, da Azure Database for MySQL Server nur die InnoDB-Speicher-Engine unterstützt.
- Bei Tabellen mit vielen sekundären Indizes oder bei sehr großen Tabellen sind die Auswirkungen des Leistungs-Overheads während der Wiederherstellung besonders sichtbar. Ändern Sie die Sicherheitskopie-Dateien so, dass die `CREATE TABLE`-Anweisungen keine sekundären Schlüsseldefinitionen enthalten, und erstellen Sie nach dem Importieren der Daten sekundäre Indizes neu, um Leistungseinbußen während des Wiederherstellungsprozesses zu vermeiden.

Um schließlich die Datenreplikation vorzubereiten:

- Überprüfen Sie, ob der Azure Database for MySQL-Zielserver über Port 3306 mit dem Amazon RDS for MySQL-Quellserver verbunden werden kann.
- Stellen Sie sicher, dass der Amazon RDS for MySQL-Quellserver sowohl eingehenden als auch ausgehenden Datenverkehr auf Port 3306 zulässt.
- Stellen Sie sicher, dass Sie [Site-to-Site Konnektivität](/azure/vpn-gateway/tutorial-site-to-site-portal) zu Ihrem Quellserver bereitstellen, indem Sie entweder [ExpressRoute](/azure/expressroute/expressroute-introduction) oder [VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways) verwenden. Weitere Informationen zur Erstellung eines virtuellen Netzes finden Sie in der Dokumentation zu [Virtual Network](/azure/virtual-network/) und insbesondere in den Quickstart-Artikeln mit Schritt-für-Schritt-Einzelheiten.
- Konfigurieren Sie die Netzsicherheitsgruppen Ihres Quelldatenbank-Servers so, dass die Server-IP-Adresse der Azure Database for MySQL zugelassen wird.

> [!IMPORTANT]
> Wenn die Quellinstanz von Amazon RDS for MySQL den GTID_mode auf EIN gesetzt hat, muss die Zielinstanz von Azure Database for MySQL–Flexible Server ebenfalls den GTID_mode auf EIN gesetzt haben.

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Konfigurieren Sie die Zielinstanz von Azure Database for MySQL

Um die Zielinstanz von Azure Database for MySQL (das Ziel für die Datenreplikation) zu konfigurieren, führen Sie die folgenden Schritte durch:

1. Setzen Sie den Parameterwert `max_allowed_packet` auf den Höchstwert von **1073741824** (z. B. 1 GB), um Überlaufprobleme im Zusammenhang mit langen Zeilen zu vermeiden.
2. Setzen Sie die Parameter `slow_query_log`, `general_log`, `audit_log_enabled` und `query_store_capture_mode` während der Migration auf **AUS**, um jeglichen Overhead im Zusammenhang mit der Abfrageprotokollierung zu vermeiden.
3. Skalieren Sie die Computegröße des Azure Database for MySQL-Zielservers auf das Maximum von 64 virtuellen Kernen hoch, um zusätzliche Rechenressourcen für die Wiederherstellung der Datenbank-Sicherungskopie vom Quellserver bereitzustellen.

    Sie können die Computegröße nach Abschluss der Migration jederzeit wieder an die Anforderungen Ihrer Anwendung anpassen.

4. Erhöhen Sie die Speichergröße, um mehr IOPS während der Migration zu erhalten, oder erhöhen Sie die maximalen IOPS für die Migration.

   > [!NOTE]
   > Die verfügbaren maximalen IOPS werden durch die Computegröße bestimmt. Weitere Informationen finden Sie im Abschnitt IOPS in [Compute und Speicheroptionen in Azure Database for MySQL–Flexible Server](/azure/mysql/flexible-server/concepts-compute-storage#iops).

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>Konfigurieren Sie den Amazon RDS for MySQL-Quellserver

Um den in Amazon RDS gehosteten MySQL-Server (die "Quelle" für die Datenreplikation) vorzubereiten und zu konfigurieren, führen Sie die folgenden Schritte durch.

1. Stellen Sie sicher, dass die binäre Protokollierung auf dem Amazon RDS for MySQL-Quellserver aktiviert ist, indem Sie überprüfen, ob automatische Backups aktiviert sind, oder stellen Sie sicher, dass ein Lesereplikat für den Amazon RDS for MySQL-Quellserver existiert.

2. Stellen Sie sicher, dass die binären Protokolldateien auf dem Quellserver so lange beibehalten werden, bis die Änderungen auf der Zielinstanz von Azure Database for MySQL angewendet wurden.

    Bei der Datenreplikation verwaltet Azure Database for MySQL den Replikationsprozess nicht.

3. Um die Aufbewahrung der Binärprotokolle auf dem Amazon RDS-Quellserver zu überprüfen, um die Anzahl der Stunden zu bestimmen, für die die Binärprotokolle aufbewahrt werden, rufen Sie die gespeicherte Prozedur "mysql.rds_show_configu ration" auf:

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
4. Um die Aufbewahrungsdauer der Binärprotokolle zu konfigurieren, führen Sie die `rds_set_configuration` gespeicherte Prozedur aus, um sicherzustellen, dass die Binärprotokolle auf dem Quellserver für die gewünschte Zeitdauer aufbewahrt werden, zum Beispiel:

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours’, 96);
    ```

    Dies ist hilfreich, wenn Sie eine Sicherungskopie erstellen und dann wiederherstellen, um die Deltaänderungen schnell nachzuholen.

   > [!NOTE]
   > Vergewissern Sie sich, dass auf dem Quellserver je nach der festgelegten Aufbewahrungsdauer ausreichend Speicherplatz für die Speicherung der Binärprotokolle vorhanden ist.

Es gibt zwei Möglichkeiten, eine Sicherungskopie der Daten vom Amazon RDS for MySQL-Quellserver zu erstellen. Ein Ansatz besteht darin, eine Sicherheitskopie der Daten direkt vom Quellserver zu erstellen, während der andere Ansatz darin besteht, eine Sicherheitskopie von einem Amazon RDS for MySQL-Lesereplikat zu erfassen.

- Wenn Sie eine Sicherungskopie der Daten direkt vom Quellserver erfassen, führen Sie die folgenden Schritte durch:

    1. Stellen Sie sicher, dass Sie die Schreibvorgänge von der Anwendung heraus für einige Minuten stoppen, um eine transaktional konsistente Sicherungskopie der Daten zu erhalten.

       Sie können auch den Parameter `read_only` vorübergehend auf einen Wert von **1** setzen, damit bei der Erfassung einer Sicherungskopie keine Schreibvorgänge verarbeitet werden.

    2. Nachdem Sie die Schreibvorgänge auf dem Quellserver angehalten worden sind, erfassen Sie den Namen der binären Protokolldatei und die Versetzung, indem Sie den Befehl `Mysql> Show master status;` ausführen.
    3. Speichern Sie diese Werte, um die Replikation von Ihrem Azure Database for MySQL starten.
    4. Lassen Sie zum Erstellen einer Sicherheitskopie der Daten mysqldump laufen und führen Sie den folgenden Befehl aus:

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- Wenn das Anhalten von Schreibvorgängen auf dem Quellserver keine Option ist oder die Leistung des Erstellen von Sicherheitskopien von Daten auf dem Quellserver nicht akzeptabel ist, erfassen Sie eine Sicherheitskopie auf einem Replikatserver, indem Sie die folgenden Schritte durchführen:

    1. Erstellen Sie ein Amazon MySQL-Lesereplikat mit der gleichen Konfiguration wie der Quellserver und erstellen Sie dann die Sicherheitskopie dort.
    2. Lassen Sie das Amazon RDS for MySQL-Lesereplikat mit dem Amazon RDS for MySQL-Quellserver gleichziehen.
    3. Wenn die Replikationsverzögerung auf dem Lesereplikat **0** erreicht, halten Sie die Replikation durch Aufruf der gespeicherten Prozedur `mysql.rds_stop_replication` an.

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    4. Verbinden Sie sich bei angehaltener Replikation mit dem Replikat und führen Sie dann den Befehl `SHOW SLAVE STATUS` aus, um den aktuellen binären Protokolldateinamen aus dem Feld **Relay_Master_Log_File** und die Position der Protokolldatei aus dem Feld **Exec_Master_Log_Pos** abzurufen.
    5. Speichern Sie diese Werte, um die Replikation von Ihrem Azure Database for MySQL starten.
    6. Um eine Sicherheitskopie der Daten von dem Amazon RDS for MySQL-Lesereplikat zu erstellen, führen Sie mysqldump aus, indem Sie den folgenden Befehl ausführen:

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > Sie können auch mydumper verwenden, um eine parallelisierte Sicherheitskopie Ihrer Daten aus Ihrer Amazon RDS for MySQL-Quelldatenbank zu entnehmen. Weitere Informationen finden Sie unter [Migrieren einer großen Datenbank zu Azure Database for MySQL mit mydumper/myloader](/azure/mysql/concepts-migrate-mydumper-myloader).

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Verknüpfen des Quell- und Replikatservers zum Starten der Datenreplikation

1. Um die Datenbank mit mysql nativer Wiederherstellung wiederherzustellen, führen Sie den folgenden Befehl aus:

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > Wenn Sie stattdessen myloader verwenden, siehe [Migrieren einer großen Datenbank zu Azure Database for MySQL mit mydumper/myloader](/azure/mysql/concepts-migrate-mydumper-myloader).

2. Melden Sie sich am Amazon RDS for MySQL-Quellserver an, richten Sie einen Replikationsbenutzer ein und erteilen Sie diesem Benutzer die erforderlichen Berechtigungen.

    - Wenn Sie SSL verwenden, führen Sie die folgenden Befehle aus:

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - Wenn Sie SSL nicht verwenden, führen Sie den folgenden Befehl aus:

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    Alle Datenreplikationsfunktionen werden von gespeicherten Prozeduren ausgeführt. Informationen zu allen Prozeduren finden Sie unter [Stored Procedures für die Datenreplikation](/azure/mysql/reference-stored-procedures#data-in-replication-stored-procedures). Sie können diese gespeicherten Prozeduren in der MySQL-Shell oder der MySQL Workbench ausführen.

3. Um den Amazon RDS for MySQL-Quellserver und den Azure Database for MySQL-Zielserver zu verbinden, melden Sie sich am Azure Database for MySQL-Zielserver an und legen den Amazon RDS for MySQL-Server als Quellserver fest, indem Sie den folgenden Befehl ausführen:

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

4. Um die Replikation zwischen dem Amazon RDS for MySQL-Quellserver und dem Azure Database for MySQL-Zielserver zu starten, führen Sie den folgenden Befehl aus:

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

5. Um den Status der Replikation zu überprüfen, führen Sie auf dem Replikationsserver den folgenden Befehl aus:

    ```
    Mysql> show slave status\G
    ```

    Wenn der Status der Parameter `Slave_IO_Running` und `Slave_SQL_Running` **Yes** ist, wurde die Replikation gestartet und ist am Laufen.

6. Überprüfen Sie den Wert des Parameters `Seconds_Behind_Master`, um festzustellen, wie weit der Zielserver verzögert ist.

    Ist der Wert **0**, hat der Zielserver alle Aktualisierungen vom Quellserver verarbeitet. Beträgt der Wert etwas anderes als **0**, verarbeitet der Zielserver noch Updates.

## <a name="ensure-a-successful-cutover"></a>Sicherstellen einer erfolgreichen Übernahme

Führen Sie die folgenden Aufgaben aus, um eine erfolgreiche Übernahme sicherzustellen:

1. Konfigurieren Sie die entsprechenden Anmeldungen und Berechtigungen auf Datenbankebene auf dem Azure Database for MySQL-Zielserver.
2. Halten Sie die Schreibvorgänge auf dem Amazon RDS for MySQL-Quellserver an.
3. Stellen Sie sicher, dass der Azure Database for MySQL-Zielserver mit dem Quellserver gleichgezogen hat und dass der Wert `Seconds_Behind_Master` ist **0** von `show slave status`.
4. Rufen Sie die gespeicherte Prozedur `mysql.az_replication_stop` auf, um die Replikation anzuhalten, da alle Änderungen auf den Azure Database for MySQL-Zielserver repliziert wurden.
5. Rufen Sie `mysql.az_replication_remove_master` auf, um die Konfiguration der Datenreplikation zu entfernen.
6. Leiten Sie Clients und Client-Anwendungen auf den Azure Database for MySQL-Zielserver um.

Zu diesem Zeitpunkt ist die Migration abgeschlossen und Ihre Anwendungen sind mit dem Server verbunden, auf dem Azure Database for MySQL läuft.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Sehen Sie sich das Video [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (in englischer Sprache) an, das Ihnen anhand einer Demo erläutert, wie man MySQL-Anwendungen nach Azure Database for MySQL migriert.
