---
title: 'Schnellstart: Herstellen einer Verbindung mithilfe von PHP – Azure Database for MySQL Flexible Server'
description: Diese Schnellstartanleitung enthält mehrere PHP-Codebeispiele, mit deren Hilfe Sie eine Verbindung mit einer Azure Database for MySQL Flexible Server-Instanz herstellen und Daten abfragen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 3c49bb5c3e272e792d50c5f8fcadbacd9d6ebbe6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460677"
---
# <a name="use-php-with-azure-database-for-mysql-flexible-server"></a>Verwenden von PHP mit Azure Database for MySQL Flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]
In diesem Schnellstart wird gezeigt, wie Sie mithilfe einer [PHP](https://secure.php.net/manual/intro-whatis.php)-Anwendung eine Verbindung mit einer Azure Database for MySQL Flexible Server-Instanz herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. Bei diesem Artikel wird davon ausgegangen, dass Sie mit der PHP-Entwicklung vertraut sind und noch keine Erfahrung mit Azure Database for MySQL Flexible Server haben.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:

- [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mit dem Azure-Portal](./quickstart-create-server-portal.md)
- [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mit der Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Vorbereiten der Clientarbeitsstation
1. Wenn Sie Ihre Flexible Server-Instanz mit *Privater Zugriff (VNET-Integration)* erstellt haben, müssen Sie eine Verbindung mit Ihrem Server über eine Ressource innerhalb desselben virtuellen Netzwerks herstellen. Sie können einen virtuellen Computer erstellen und zum virtuellen Netzwerk hinzufügen, das mit Ihrer Flexible Server-Instanz erstellt wurde. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines virtuellen Netzwerks mit Azure Database for MySQL Flexible Server mithilfe der Azure CLI](./how-to-manage-virtual-network-cli.md).

2. Wenn Sie Ihre Flexible Server-Instanz mit *öffentlichem Zugriff (zulässige IP-Adressen)* erstellt haben, können Sie die lokale IP-Adresse der Liste der Firewallregeln auf Ihrem Server hinzufügen. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL Flexible Server mithilfe der Azure CLI](./how-to-manage-firewall-cli.md).

### <a name="install-php"></a>Installieren von PHP

Installieren Sie PHP auf Ihrem eigenen Server, oder erstellen Sie eine Azure-[Web-App](../../app-service/overview.md), die PHP enthält.  Unter [Erstellen und Verwalten von Firewallregeln](./how-to-manage-firewall-portal.md) erfahren Sie, wie Sie Firewallregeln erstellen.

#### <a name="macos"></a>macOS

1. Laden Sie die [PHP-Version 7.1.4](https://secure.php.net/downloads.php) herunter.
2. Installieren Sie PHP, und informieren Sie sich im [PHP-Handbuch](https://secure.php.net/manual/install.macosx.php) über die weitere Konfiguration.

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Laden Sie die [PHP-Version 7.1.4 non-thread safe (x64)](https://secure.php.net/downloads.php) herunter.
2. Installieren Sie PHP, und informieren Sie sich im [PHP-Handbuch](https://secure.php.net/manual/install.unix.php) über die weitere Konfiguration.

#### <a name="windows"></a>Windows

1. Laden Sie die [PHP-Version 7.1.4 non-thread safe (x64)](https://windows.php.net/download#php-7.1) herunter.
2. Installieren Sie PHP, und informieren Sie sich im [PHP-Handbuch](https://secure.php.net/manual/install.windows.php) über die weitere Konfiguration.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure Database for MySQL Flexible Server-Instanz erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im Azure-Portal im linken Menü **Alle Ressourcen** aus, und suchen Sie dann nach dem Server, den Sie erstellt haben (z.B. **mydemoserver**).
3. Wählen Sie den Servernamen aus.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>Herstellen einer Verbindung mit einer Flexible Server-Instanz mithilfe von TLS/SSL in PHP

Sie können die folgenden Codebeispiele nutzen, um in Ihrer Anwendung über TLS/SSL eine verschlüsselte Verbindung mit Ihrer Flexible Server-Instanz einzurichten. Sie können das Zertifikat unter [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) herunterladen, das für die Kommunikation über TLS/SSL erforderlich ist.

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>Herstellen einer Verbindung und Erstellen einer Tabelle

Verwenden Sie den folgenden Code, um mit der SQL-Anweisung des Typs **CREATE TABLE** eine Verbindung herzustellen und eine Tabelle zu erstellen.

Im Code wird die Klasse mit der **verbesserten MySQL-Erweiterung** (mysqli) verwendet, die in PHP enthalten ist. Im Code werden die Methoden [mysqli_init](https://secure.php.net/manual/mysqli.init.php) und [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) aufgerufen, um eine Verbindung mit MySQL herzustellen. Anschließend wird die [mysqli_query](https://secure.php.net/manual/mysqli.query.php)-Methode aufgerufen, um die Abfrage auszuführen. Als Nächstes wird die [mysqli_close](https://secure.php.net/manual/mysqli.close.php)-Methode aufgerufen, um die Verbindung zu schließen.

Ersetzen Sie die Parameter „host“, „username“, „password“ und „db_name“ durch Ihre eigenen Werte.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Einfügen von Daten

Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und mit einer SQL-Anweisung des Typs **INSERT** Daten einzufügen.

Im Code wird die Klasse mit der **verbesserten MySQL-Erweiterung** (mysqli) verwendet, die in PHP enthalten ist. Im Code wird die [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)-Methode verwendet, um eine vorbereitete INSERT-Anweisung zu erstellen, und anschließend werden die Parameter für jeden eingefügten Spaltenwert mit der [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)-Methode gebunden. Im Code wird die Anweisung mit der [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)-Methode ausgeführt, und anschließend wird die Anweisung mit der [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)-Methode geschlossen.

Ersetzen Sie die Parameter „host“, „username“, „password“ und „db_name“ durch Ihre eigenen Werte.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Lesen von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **SELECT** zu verbinden und zu lesen.  Im Code wird die Klasse mit der **verbesserten MySQL-Erweiterung** (mysqli) verwendet, die in PHP enthalten ist. Im Code wird die [mysqli_query](https://secure.php.net/manual/mysqli.query.php)-Methode zum Durchführen der SQL-Abfrage genutzt, und mit der [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php)-Methode werden die sich ergebenden Zeilen abgerufen.

Ersetzen Sie die Parameter „host“, „username“, „password“ und „db_name“ durch Ihre eigenen Werte.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Aktualisieren von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **UPDATE** zu verbinden und zu aktualisieren.

Im Code wird die Klasse mit der **verbesserten MySQL-Erweiterung** (mysqli) verwendet, die in PHP enthalten ist. Im Code wird die [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)-Methode verwendet, um eine vorbereitete UPDATE-Anweisung zu erstellen, und anschließend werden die Parameter für jeden aktualisierten Spaltenwert mit der [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)-Methode gebunden. Im Code wird die Anweisung mit der [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)-Methode ausgeführt, und anschließend wird die Anweisung mit der [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)-Methode geschlossen.

Ersetzen Sie die Parameter „host“, „username“, „password“ und „db_name“ durch Ihre eigenen Werte.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **DELETE** zu verbinden und zu lesen.

Im Code wird die Klasse mit der **verbesserten MySQL-Erweiterung** (mysqli) verwendet, die in PHP enthalten ist. Im Code wird die [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)-Methode verwendet, um eine vorbereitete DELETE-Anweisung zu erstellen, und anschließend werden die Parameter für die WHERE-Klausel in der Anweisung mit der [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)-Methode gebunden. Im Code wird die Anweisung mit der [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)-Methode ausgeführt, und anschließend wird die Anweisung mit der [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)-Methode geschlossen.

Ersetzen Sie die Parameter „host“, „username“, „password“ und „db_name“ durch Ihre eigenen Werte.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>Nächste Schritte
- [Verschlüsselte Konnektivität mit der Transport Layer Security (TLS 1.2) in Azure Database for MySQL Flexible Server](./how-to-connect-tls-ssl.md)
- In diesem Artikel erfahren Sie mehr über [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
- [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-firewall-portal.md).
- [Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-virtual-network-portal.md).