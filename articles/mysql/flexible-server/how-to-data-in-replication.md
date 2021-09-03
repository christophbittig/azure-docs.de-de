---
title: Konfigurieren der Datenreplikation – Azure Database for MySQL Flexible Server
description: In diesem Artikel wird beschrieben, wie die Datenreplikation für Azure Database for MySQL Flexible Server eingerichtet wird.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: ee0bafdfe7d7caae2d4ba65e9967d9c46e6b3e3c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346506"
---
# <a name="how-to-configure-azure-database-for-mysql-flexible-server-data-in-replication"></a>Konfigurieren der Datenreplikation in Azure Database for MySQL Flexible Server

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In diesem Artikel erfahren Sie, wie Sie die [Datenreplikation](concepts-data-in-replication.md) in Azure Database for MySQL Flexible Server einrichten, indem Sie Quell- und Replikatserver konfigurieren. In diesem Artikel wird davon ausgegangen, dass Sie über ein gewisses Maß an Erfahrung mit MySQL-Servern und -Datenbanken verfügen.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff _Slave_, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

Die [Datenreplikationsfunktion](concepts-data-in-replication.md) synchronisiert Daten von einem lokalen MySQL-Quellserver, virtuellen Computern (VMs) oder Clouddatenbankdiensten, um ein Replikat in Azure Database for MySQL Flexible Server zu erstellen. Die Datenreplikation basiert auf der Position der binären Protokolldatei (binlog). Weitere Informationen zur binlog-Replikation finden Sie unter [Binary Log File Position Based Replication Configuration Overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Konfiguration der auf der Position der binären Protokolldatei basierenden Replikation – Übersicht).

Überprüfen Sie die [Einschränkungen und Anforderungen](concepts-data-in-replication.md#limitations-and-considerations) der Datenreplikation, bevor Sie die Schritte in diesem Artikel ausführen.

## <a name="create-an-azure-database-for-mysql-flexible-server-instance-to-use-as-a-replica"></a>Erstellen Sie eine Azure Database for MySQL Flexible Server-Instanz, die als Replikat verwendet werden soll.

1. Erstellen Sie eine neue Instanz von Azure Database for MySQL Flexible Server (z. B. „replica.mysql.database.azure.com“). Informationen zur Servererstellung finden Sie unter [Schnellstart: Verwenden des Azure-Portals zum Erstellen einer Azure Database for MySQL Flexible Server-Instanz](quickstart-create-server-portal.md). Dieser Server ist der Replikatserver für die Datenreplikation.

2. Erstellen Sie dieselben Benutzerkonten und entsprechenden Berechtigungen.

   Benutzerkonten werden nicht vom Quellserver auf den Replikatserver repliziert. Wenn Sie Benutzern Zugriff auf den Replikatserver gewähren möchten, müssen Sie alle Konten und entsprechenden Berechtigungen für diese neu erstellte Azure Database for MySQL Flexible Server-Instanz manuell erstellen.

## <a name="configure-the-source-mysql-server"></a>Konfigurieren des MySQL-Quellservers

Mit den folgenden Schritten wird der MySQL-Server, der lokal, auf einem virtuellen Computer oder von einem von anderen Cloudanbietern gehosteten Datenbankdienst gehostet wird, für die Datenreplikation vorbereitet und konfiguriert. Dieser Server ist die „Quelle“ bei der Datenreplikation.

1. Überprüfen Sie die [Anforderungen für den Quellserver](concepts-data-in-replication.md#requirements), bevor Sie fortfahren.

2. Netzwerkanforderungen 
    * Stellen Sie sicher, dass der Quellserver sowohl eingehenden als auch ausgehenden Datenverkehr an Port 3306 zulässt und über eine **öffentliche IP-Adresse** verfügt, und dass der DNS öffentlich zugänglich ist, oder über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) verfügt.

    * Wenn privater Zugriff verwendet wird, stellen Sie sicher, dass Konnektivität zwischen dem Quellserver und dem VNet besteht, in dem der Replikatserver gehostet wird. 
    * Stellen Sie Site-to-Site-Konnektivität zu Ihren lokalen Quellservern sicher, indem Sie entweder [ExpressRoute](../../expressroute/expressroute-introduction.md) oder [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) verwenden. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.
    * Wenn privater Zugriff auf dem Replikatserver verwendet wird und Ihre Quelle Azure-VM ist, stellen Sie sicher, dass die VNet-zu-VNet-Konnektivität hergestellt ist. VNet-Peering wird unterstützt. Sie können auch andere Konnektivitätsmethoden (etwa eine Verbindung zwischen VNets) verwenden, um die regionsübergreifende Kommunikation zwischen VNets zu ermöglichen. Weitere Informationen finden Sie unter [Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung über das Azure-Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
    * Vergewissern Sie sich, dass die Netzwerksicherheitsgruppen-Regeln Ihres virtuellen Netzwerks den ausgehenden Port 3306 nicht blockieren (auch nicht in eingehender Richtung, wenn MySQL auf einem virtuellen Azure-Computer ausgeführt wird). Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../../virtual-network/virtual-network-vnet-plan-design-arm.md).
    * Konfigurieren Sie die Firewallregeln Ihres Quellservers so, dass die IP-Adresse des Replikatservers zulässig ist.

  
3. Aktivieren Sie die binäre Protokollierung.

   Überprüfen Sie, ob die binäre Protokollierung auf der Quelle aktiviert wurde, indem Sie den folgenden Befehl ausführen:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Wenn die Variable [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) mit dem Wert „ON“ zurückgegeben wird, ist die binäre Protokollierung auf Ihrem Server aktiviert.

   Wenn `log_bin` mit dem Wert „OFF“ zurückgegeben und der Quellserver lokal oder auf virtuellen Computern ausgeführt wird, wo Sie auf die Konfigurationsdatei (my.cnf) zugreifen können, können Sie die folgenden Schritte ausführen:
   1. Suchen Sie Ihre MySQL-Konfigurationsdatei („my.cnf“) auf dem Quellserver. (Zum Beispiel: „/etc/my.cnf“)
   2. Öffnen Sie die Konfigurationsdatei, um sie zu bearbeiten und in der Datei nach dem Abschnitt **mysqld** zu suchen.
   3. Fügen Sie die folgende Zeile im Abschnitt „mysqld“ hinzu:

       ```bash
       log-bin=mysql-bin.log
       ```

   4. Starten Sie den MySQL-Dienst auf dem Quellserver neu, damit die Änderungen wirksam werden.
   5. Stellen Sie nach dem Neustart des Servers sicher, dass die binäre Protokollierung aktiviert ist, indem Sie dieselbe Abfrage wie zuvor ausführen:

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. Konfigurieren Sie die Quellservereinstellungen.

   Der Parameter `lower_case_table_names` muss bei der Datenreplikation zwischen Quell- und Replikatserver konsistent sein. Dieser Parameter ist bei Azure Database for MySQL Flexible Server standardmäßig „1“.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Erstellen Sie eine neue Replikationsrolle, und richten Sie Berechtigungen ein.

   Erstellen Sie auf dem Quellserver ein Benutzerkonto, das mit Replikationsberechtigungen konfiguriert ist. Das können Sie über SQL-Befehle oder ein Tool wie MySQL Workbench tun. Treffen Sie die Entscheidung, ob Sie eine Replikation mit SSL durchführen möchten, da dies bei der Erstellung des Benutzers angegeben werden muss. Wie [Ihrem Quellserver Benutzerkonten hinzugefügt werden](https://dev.mysql.com/doc/refman/5.7/en/user-names.html), erfahren Sie in der MySQL-Dokumentation.

   Bei Verwendung der folgenden Befehle kann die neu erstellte Replikationsrolle nicht nur vom Computer, auf dem die Quelle selbst gehostet wird, sondern von jedem Computer aus auf die Quelle zugreifen. Hierfür muss „syncuser@'%'“ im Befehl zum Erstellen von Benutzern angegeben werden. Weitere Informationen finden Sie in der MySQL-Dokumentation unter [Specifying Account Names](https://dev.mysql.com/doc/refman/5.7/en/account-names.html) (Angeben von Kontonamen).

   **SQL-Befehl**

   *Replikation mit SSL*

   Um für alle Benutzerverbindungen SSL zu erzwingen, verwenden Sie den folgenden Befehl zum Erstellen eines Benutzers:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikation ohne SSL*

   Wenn SSL nicht für alle Verbindungen erforderlich ist, verwenden Sie den folgenden Befehl zum Erstellen eines Benutzers:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Um die Replikationsrolle in MySQL Workbench zu erstellen, öffnen Sie im Bereich **Verwaltung** den Bereich **Benutzer und Berechtigungen**, und wählen Sie dann **Konto hinzufügen** aus.

   :::image type="content" source="./media/how-to-data-in-replication/users-privileges.png" alt-text="Benutzer und Berechtigungen":::

   Geben Sie den Benutzernamen in das Feld **Anmeldename** ein.

   :::image type="content" source="./media/how-to-data-in-replication/sync-user.png" alt-text="Benutzersynchronisierung":::

   Wählen Sie den Bereich **Administratorrollen** aus, und wählen Sie dann in der Liste **Globale Berechtigungen** den Eintrag **Replikationsslave** aus. Wählen Sie anschließend **Übernehmen** aus, um die Replikationsrolle zu erstellen.

   :::image type="content" source="./media/how-to-data-in-replication/replication-slave.png" alt-text="Replikationsslave":::

6. Versetzen Sie den Quellserver in den schreibgeschützten Modus.

   Bevor Sie mit dem Sichern der Datenbank beginnen, muss der Server in den schreibgeschützten Modus versetzt werden. Im schreibgeschützten Modus kann die Quelle keine Schreibtransaktionen verarbeiten. Werten Sie die Auswirkungen auf Ihr Unternehmen aus, und planen Sie das Fenster für den schreibgeschützten Modus bei Bedarf in der Nebenzeit.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Rufen Sie den Namen und das Offset der binären Protokolldatei ab.

   Führen Sie den Befehl [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) aus, um den aktuellen Namen und das Offset der binären Protokolldatei zu ermitteln.

   ```sql
    show master status;
   ```
   Die Ergebnisse sollten in etwa wie folgt aussehen. Notieren Sie sich unbedingt den Namen der Binärdatei für die nachfolgenden Schritte.

   :::image type="content" source="./media/how-to-data-in-replication/master-status.png" alt-text="Statusergebnisse des Masters":::

## <a name="dump-and-restore-the-source-server"></a>Sichern und Wiederherstellen des Quellservers

1. Legen Sie fest, welche Datenbanken und Tabellen in Azure Database for MySQL Flexible Server repliziert werden sollen, und führen Sie die Sicherung vom Quellserver aus.

    Mit „mysqldump“ können Sie Datenbanken von Ihrem primären Server sichern. Weitere Informationen finden Sie unter [Migrieren der MySQL-Datenbank auf Azure-Datenbank für MySQL durch Sicherungen und Wiederherstellungen](../concepts-migrate-dump-restore.md). Die MySQL-Bibliothek und die Testbibliothek müssen nicht gesichert werden.


3. Versetzen Sie den Quellserver in den Lese-/Schreibmodus.

   Versetzen Sie den MySQL-Quellserver nach dem Sichern der Datenbank wieder in den Lese-/Schreibmodus.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. Stellen Sie die Sicherungsdatei auf dem neuen Server wieder her.

   Stellen Sie die Sicherungsdatei auf dem Server wieder her, der im Dienst „Azure Database for MySQL Flexible Server“ erstellt wurde. Informationen zum Wiederherstellen einer Sicherungsdatei auf einem MySQL-Server finden Sie unter [Migrieren der MySQL-Datenbank auf Azure-Datenbank für MySQL durch Sicherungen und Wiederherstellungen](../concepts-migrate-dump-restore.md). Handelt es sich um eine große Sicherungsdatei, laden Sie sie auf einen virtuellen Computer in Azure in der gleichen Region wie Ihr Replikatserver hoch. Stellen Sie sie auf dem Server mit Azure Database for MySQL Flexible Server vom virtuellen Computer wieder her.


## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Verknüpfen des Quell- und Replikatservers zum Starten der Datenreplikation

1. Legen Sie den Quellserver fest.

   Alle Datenreplikationsfunktionen werden von gespeicherten Prozeduren ausgeführt. Alle Prozeduren finden Sie unter [Gespeicherte Prozeduren für die Azure Database for MySQL-Verwaltung](../reference-stored-procedures.md). Die gespeicherten Prozeduren können in der MySQL-Shell oder in MySQL Workbench ausgeführt werden.

   Um zwei Server zu verknüpfen und die Replikation zu starten, melden Sie sich beim Zielreplikatserver im Dienst Azure Database for MySQL an, und legen Sie die externe Instanz als Quellserver fest. Hierfür verwenden Sie die gespeicherte Prozedur `mysql.az_replication_change_master` auf dem Azure Database for MySQL-Server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

 
   - master_host: Hostname des Quellservers
   - master_user: Benutzername des Quellservers
   - master_password: Kennwort des Quellservers
   - master_port: Portnummer, auf der der Quellserver auf Verbindungen lauscht. (3306 ist der Standardport, an dem MySQL lauscht.)
   - master_log_file: Name der binären Protokolldatei durch Ausführung von `show master status`
   - master_log_pos: Position des binären Protokolls durch Ausführung von `show master status`
   - master_ssl_ca: Der Kontext des Zertifizierungsstellenzertifikats. Wenn SSL nicht verwendet wird, übergeben Sie eine leere Zeichenfolge.

     Es wird empfohlen, diesen Parameter als Variable zu übergeben. Weitere Informationen finden Sie in den folgenden Beispielen.

   > [!NOTE]
   > Wenn der Quellserver auf einer Azure-VM gehostet wird, legen Sie „Zugriff auf Azure-Dienste erlauben“ auf „EIN“ fest, damit Quell- und Replikatserver miteinander kommunizieren können. Diese Einstellung kann über die Optionen für die **Verbindungssicherheit** geändert werden. Weitere Informationen finden Sie unter [Verwalten von Firewallregeln im Portal](how-to-manage-firewall-portal.md).

   **Beispiele**

   *Replikation mit SSL*

   Die Variable `@cert` wird durch Ausführung der folgenden MySQL-Befehle erstellt:

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   Eine Replikation mit SSL wird zwischen einem Quellserver, der in der Domäne companya.com gehostet wird, und einem Replikatserver eingerichtet, der in Azure Database for MySQL Flexible Server gehostet wird. Diese gespeicherte Prozedur wird auf dem Replikat ausgeführt.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Replikation ohne SSL*

   Eine Replikation ohne SSL wird zwischen einem Quellserver, der in der Domäne companya.com gehostet wird, und einem Replikatserver eingerichtet, der in Azure Database for MySQL Flexible Server gehostet wird. Diese gespeicherte Prozedur wird auf dem Replikat ausgeführt.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```
2. Starten Sie die Replikation.

   Rufen Sie die gespeicherte Prozedur `mysql.az_replication_start` auf, um die Replikation zu starten.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Überprüfen Sie den Replikationsstatus.

   Rufen Sie den Befehl [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) auf dem Replikatserver auf, um den Replikationsstatus anzuzeigen.

   ```sql
   show slave status;
   ```

   Wenn der Status von `Slave_IO_Running` und `Slave_SQL_Running` „yes“ lautet und der Wert von `Seconds_Behind_Master` „0“ ist, funktioniert die Replikation ordnungsgemäß. `Seconds_Behind_Master` gibt an, wie stark das Replikat verzögert ist. Wenn der Wert nicht „0“ ist, bedeutet dies, dass das Replikat Updates verarbeitet.

## <a name="other-useful-stored-procedures-for-data-in-replication-operations"></a>Andere nützliche gespeicherte Prozeduren für Datenreplikationsvorgänge

### <a name="stop-replication"></a>Beenden der Replikation

Um die Replikation zwischen dem Quell- und Replikatserver zu beenden, verwenden Sie die folgende gespeicherte Prozedur:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Entfernen der Replikationsbeziehung

Um die Replikationsbeziehung zwischen Quell- und Replikatserver zu entfernen, verwenden Sie die folgende gespeicherte Prozedur:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Überspringen des Replikationsfehlers

Um einen Replikationsfehler zu überspringen und die Fortsetzung der Replikation zuzulassen, verwenden Sie die folgende gespeicherte Prozedur:

```sql
CALL mysql.az_replication_skip_counter;
```

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/how-to-data-in-replication/show-binary-log.png" alt-text="Binäre Protokollergebnisse anzeigen":::

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Datenreplikation](concepts-data-in-replication.md) für Azure Database for MySQL Flexible Server.
