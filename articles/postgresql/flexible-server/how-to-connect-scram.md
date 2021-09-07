---
title: 'Konnektivität mit SCRAM in Azure Database for PostgreSQL: Flexibler Server'
description: Hier finden Sie Anweisungen und Informationen zum Herstellen einer Verbindung unter Verwendung von SCRAM in Azure Database for PostgreSQL – flexibler Server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/23/2021
ms.openlocfilehash: 7fc67ab7162c7fe62df226fd39aefcd350bbdf82
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112897244"
---
# <a name="scram-authentication-in-azure-database-for-postgresql---flexible-server"></a>SCRAM-Authentifizierung in Azure Database for PostgreSQL :Flexibler Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

Salted Challenge Response Authentication Mechanism (SCRAM) ist ein kennwortbasiertes Protokoll für die gegenseitige Authentifizierung. Es ist ein Abfrage/Rückmeldung-Schema, das mehrere Sicherheitsebenen hinzufügt und die Kennwortermittlung bei nicht vertrauenswürdigen Verbindungen verhindert. SCRAM unterstützt das Speichern von Kennwörtern auf dem Server in Form eines kryptografischen Hash, der erweiterte Sicherheit bietet. 

Um mit der SCRAM-Authentifizierungsmethode auf den PostgreSQL-Datenbankserver zugreifen zu können, müssen Ihre Clientbibliotheken SCRAM unterstützen.  Weitere Informationen finden Sie in der [Liste der Treiber](https://wiki.postgresql.org/wiki/List_of_drivers), die SCRAM unterstützen.

## <a name="configuring-scram-authentication"></a>Konfigurieren der SCRAM-Authentifizierung

1. Ändern Sie „password_encryption“ in „SCRAM-SHA-256“. PostgreSQL unterstützt SCRAM derzeit nur mit SHA-256.
        :::image type="content" source="./media/how-to-configure-scram/1-password-encryption.png" alt-text="Aktivieren der SCRAM-Kennwortverschlüsselung"::: 
2. Legen Sie SCRAM-SHA-256 als zulässige Authentifizierungsmethode fest.
        :::image type="content" source="./media/how-to-configure-scram/2-auth-method.png" alt-text="Auswählen der Authentifizierungsmethode"::: 
    >[!Important]
    > Sie können die ausschließliche SCRAM-Authentifizierung erzwingen, indem Sie nur die Methode „SCRAM-SHA-256“ auswählen. In diesem Fall können Benutzer mit MD5-Authentifizierung keine Verbindung mit dem Server mehr herstellen. Bevor Sie SCRAM erzwingen, sollten Sie daher sowohl MD5 als auch SCRAM-SHA-256 als Authentifizierungsmethoden verwenden, bis Sie alle Benutzerkennwörter auf SCRAM-SHA-256 aktualisieren. Sie können den Authentifizierungstyp für Benutzer mithilfe der Abfrage in Schritt 7 überprüfen.
3. Speichern Sie die Änderungen. Es handelt sich hierbei um dynamische Eigenschaften, die keinen Serverneustart erfordern.
4. Stellen Sie auf Ihrem Postgres-Client eine Verbindung mit dem Postgres-Server her. Beispiel:
   
    ```bash
    psql "host=myPGServer.postgres.database.azure.com port=5432 dbname=postgres user=myDemoUser password=MyPassword sslmode=require"

    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 12.6)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.
    ```

5. Überprüfen Sie die Kennwortverschlüsselung.
   
    ```SQL
    postgres=> show password_encryption;
     password_encryption
    ---------------------
    scram-sha-256
    (1 row)
    ```

6. Anschließend können Sie das Kennwort für Benutzer aktualisieren.

    ```SQL
    postgres=> \password myDemoUser
    Enter new password:
    Enter it again:
    postgres=>
    ```

7. Sie können die Benutzerauthentifizierungstypen mithilfe der Funktion `azure_roles_authtype()` überprüfen. 

    ``` SQL
    postgres=> SELECT * from azure_roles_authtype();
            rolename          | authtype
    ---------------------------+-----------
    azuresu                   | NOLOGIN
    pg_monitor                | NOLOGIN
    pg_read_all_settings      | NOLOGIN
    pg_read_all_stats         | NOLOGIN
    pg_stat_scan_tables       | NOLOGIN
    pg_read_server_files      | NOLOGIN
    pg_write_server_files     | NOLOGIN
    pg_execute_server_program | NOLOGIN
    pg_signal_backend         | NOLOGIN
    replication               | NOLOGIN
    myDemoUser                | SCRAM-256
    azure_pg_admin            | NOLOGIN
    srtest                    | SCRAM-256
    sr_md5                    | MD5
    (14 rows)
    ```

8. Anschließend können Sie vom Client, der die SCRAM-Authentifizierung unterstützt, eine Verbindung mit Ihrem Server herstellen.

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for PostgreSQL Flexible Server mithilfe der Azure CLI](./how-to-manage-virtual-network-cli.md)
- Erfahren Sie mehr über [Netzwerke in Azure Database for PostgreSQL: Flexible Server](./concepts-networking.md).
- Erfahren Sie mehr über [Firewallregeln für Azure Database for PostgreSQL: Flexible Server](./concepts-networking.md#public-access-allowed-ip-addresses).
