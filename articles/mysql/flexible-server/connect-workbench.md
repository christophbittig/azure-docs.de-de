---
title: 'Schnellstart: Herstellen einer Verbindung – MySQL Workbench – Azure Database for MySQL Flexible Server'
description: In diesem Schnellstart wird die Verwendung von MySQL Workbench beschrieben – einem Tool, mit dem Sie eine Verbindung mit Azure Database for MySQL Flexible Server herstellen und Daten daraus abfragen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 7beb058e51010bc148d9cde31e4053d3de3cf598
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445519"
---
# <a name="use-mysql-workbench-with-azure-database-for-mysql-flexible-server"></a>Verwenden von MySQL Workbench mit Azure Database for MySQL Flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In diesem Schnellstart wird gezeigt, wie Sie mit MySQL Workbench eine Verbindung mit Azure Database for MySQL Flexible Server herstellen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:

- [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mit dem Azure-Portal](./quickstart-create-server-portal.md)
- [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mit der Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Vorbereiten der Clientarbeitsstation
- Wenn Sie Ihre Flexible Server-Instanz mit *Privater Zugriff (VNET-Integration)* erstellt haben, müssen Sie eine Verbindung mit Ihrem Server über eine Ressource innerhalb desselben virtuellen Netzwerks herstellen. Sie können einen virtuellen Computer erstellen und zum virtuellen Netzwerk hinzufügen, das mit Ihrer Flexible Server-Instanz erstellt wurde. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines virtuellen Netzwerks mit Azure Database for MySQL Flexible Server mithilfe der Azure CLI](./how-to-manage-virtual-network-cli.md).
- Wenn Sie Ihre Flexible Server-Instanz mit *öffentlichem Zugriff (zulässige IP-Adressen)* erstellt haben, können Sie die lokale IP-Adresse der Liste der Firewallregeln auf Ihrem Server hinzufügen. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL Flexible Server mit der Azure CLI](./how-to-manage-firewall-cli.md).

- Laden Sie MySQL-Workbench von der [MySQL-Website](https://dev.mysql.com/downloads/workbench/) auf Ihren Computer herunter, und installieren Sie die Anwendung.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

Halten Sie die Verbindungsinformationen bereit, die zum Herstellen einer Verbindung mit dem flexiblen Server erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im Azure-Portal im linken Menü **Alle Ressourcen** aus, und suchen Sie dann nach dem Server, den Sie erstellt haben (z.B. **mydemoserver**).
3. Wählen Sie den Servernamen aus.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Herstellen einer Verbindung mit dem Server unter Verwendung von MySQL Workbench

So stellen Sie mithilfe von MySQL Workbench eine Verbindung mit Azure Database for MySQL Flexible Server her:

1. Starten Sie die Anwendung MySQL Workbench auf dem Computer.

2. Geben Sie im Dialogfeld **Setup New Connection** (Neue Verbindung einrichten) auf der Registerkarte **Parameter** folgende Informationen ein:

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="Einrichten einer neuen Verbindung":::

    | **Parameter** | **Empfohlener Wert** | **Feldbeschreibung** |
    |---|---|---|
    |    Verbindungsname | Beispielverbindung | Geben Sie eine Bezeichnung für diese Verbindung an. |
    | Verbindungsmethode | Standard (TCP/IP) | Standard (TCP/IP) ist ausreichend. |
    | Hostname | *Servername* | Geben Sie den Servernamen an, den Sie zuvor beim Erstellen von Azure-Datenbank für MySQL verwendet haben. Unser Beispielserver heißt „mydemoserver.mysql.database.azure.com“. Verwenden Sie den vollqualifizierten Domänennamen (\*.mysql.database.azure.com), wie im Beispiel gezeigt. Sollten Sie sich nicht an Ihren Servernamen erinnern, ermitteln Sie die Verbindungsinformationen gemäß den Schritten aus dem vorherigen Abschnitt.  |
    | Port | 3306 | Verwenden Sie immer den Port 3306, wenn Sie eine Verbindung mit Azure-Datenbank für MySQL herstellen. |
    | Username |  *Anmeldename des Serveradministrators* | Geben Sie den Anmeldename des Serveradministrators ein, den Sie zuvor beim Erstellen von Azure-Datenbank für MySQL verwendet haben. Der Beispielbenutzername lautet „myadmin“. Sollten Sie sich nicht an Benutzernamen erinnern, ermitteln Sie die Verbindungsinformationen gemäß den Schritten aus dem vorherigen Abschnitt.
    | Kennwort | Ihr Kennwort | Klicken Sie auf die Schaltfläche **In Vault speichern**, um das Kennwort zu speichern. |

3. Klicken Sie auf **Verbindung testen**, um zu testen, ob alle Parameter richtig konfiguriert wurden.

4. Klicken Sie zum Speichern der Verbindung auf **OK**.

5. Klicken Sie in der Auflistung der **MySQL-Verbindungen** auf die Kachel Ihres Servers, und warten Sie, bis die Verbindung hergestellt wurde.

    Es wird eine neue SQL-Registerkarte mit einem leeren-Editor geöffnet, auf der Sie Ihre Abfragen eingeben können.

> [!NOTE]
> Eine verschlüsselte Verbindung mithilfe von TLS 1.2 ist erforderlich und wird auf Ihrer Azure Database for MySQL Flexible Server-Instanz erzwungen. In der Regel ist mit TLS-/SSL-Zertifikaten keine zusätzliche Konfiguration erforderlich, damit MySQL Workbench eine Verbindung mit Ihrem Server herstellen kann. Es wird jedoch empfohlen, die Zertifizierung der TLS-/SSL-Zertifizierungsstelle MySQL Workbench zuzuordnen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mithilfe von TLS/SSL](./how-to-connect-tls-ssl.md).

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Erstellen einer Tabelle, Einfügen von Daten, Lesen von Daten, Aktualisieren von Daten, Löschen von Daten

1. Kopieren Sie den SQL-Beispielcode, und fügen Sie ihn auf einer leeren SQL-Registerkarte ein, um Beispieldaten zu veranschaulichen.

    Dieser Code erstellt die leere Datenbank „quickstartdb“ und anschließend die Beispieltabelle „inventory“. Er fügt einige Zeilen ein und liest sie dann aus. Er ändert die Daten mit einer update-Anweisung und liest die Zeilen erneut aus. Zum Schluss löscht er eine Zeile und liest die Zeilen noch einmal aus.

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Der Screenshot zeigt ein Beispiel für SQL-Code in SQL Workbench und die Ausgabe nach der Ausführung.

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="SQL-Registerkarte in MySQL Workbench für die Ausführung von SQL-Beispielcode":::

2. Um den SQL-Beispielcode auszuführen, klicken Sie auf der Registerkarte **SQL-Datei** auf der Symbolleiste auf das Blitzsymbol.
3. Beachten Sie die drei im Registerkartenformat angezeigten Ergebnisse im Abschnitt **Ergebnisraster** in der Mitte der Seite.
4. Beachten Sie auch die Liste **Ausgabe** am unteren Rand der Seite. Der Status der einzelnen Befehle wird angezeigt.

Sie haben mithilfe von MySQL Workbench eine Verbindung mit Azure Database for MySQL Flexible Server hergestellt und Daten mit SQL abgefragt.

## <a name="next-steps"></a>Nächste Schritte
- [Verschlüsselte Konnektivität mit der Transport Layer Security (TLS 1.2) in Azure Database for MySQL Flexible Server](./how-to-connect-tls-ssl.md)
- In diesem Artikel erfahren Sie mehr über [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
- [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-firewall-portal.md).
- [Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-virtual-network-portal.md).
