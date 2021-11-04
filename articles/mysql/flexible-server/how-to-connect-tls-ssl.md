---
title: Verschlüsselte Konnektivität mit TLS/SSL in Azure Database for MySQL – Flexible Server
description: Hier finden Sie Anweisungen und Informationen zum Herstellen einer Verbindung unter Verwendung von TLS/SSL in Azure Database for MySQL – Flexible Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2f73d6c8969cd6915c1a571eb6196bcf8f901bd6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468136"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Herstellen einer Verbindung mit Azure Database for MySQL – Flexible Server mit verschlüsselten Verbindungen

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL Flexible Server unterstützt das Herstellen einer Verbindung zwischen Ihren Clientanwendungen und dem MySQL-Server über Secure Sockets Layer (SSL) mit TLS-Verschlüsselung (Transport Layer Security). TLS ist ein Standardprotokoll der Branche, das verschlüsselte Netzwerkverbindungen zwischen dem Datenbankserver und Clientanwendungen gewährleistet, sodass Sie Konformitätsanforderungen einhalten können.

Azure Database for MySQL Flexible Server unterstützt standardmäßig verschlüsselte Verbindungen mit Transport Layer Security (TLS 1.2), und alle eingehenden Verbindungen mit TLS 1.0 und TLS 1.1 werden standardmäßig verweigert. Die Erzwingung einer verschlüsselten Verbindung oder die TLS-Versionskonfiguration bei Ihrem flexiblen Server kann – wie in diesem Artikel erläutert – geändert werden.

Im Folgenden sind die verschiedenen Konfigurationen von SSL- und TLS-Einstellungen aufgeführt, die Sie bei Ihrem flexiblen Server verwenden können:

| Szenario   | Einstellungen für Serverparameter      | BESCHREIBUNG                                    |
|------------|--------------------------------|------------------------------------------------|
|Deaktivieren der SSL-Erzwingung | require_secure_transport = OFF |Wenn Ihre Legacy-Anwendung keine verschlüsselten Verbindungen mit dem MySQL-Server unterstützt, können Sie die Erzwingung von verschlüsselten Verbindungen mit Ihrem flexiblen Server deaktivieren, indem Sie „require_secure_transport=OFF“ festlegen.|
|Erzwingen von SSL bei der TLS-Version < 1.2 | require_secure_transport = ON und tls_version = TLSV1 oder TLSV1.1| Wenn Ihre Legacy-Anwendung verschlüsselte Verbindungen unterstützt, aber TLS-Version < 1.2 erfordert, können Sie verschlüsselte Verbindungen aktivieren, müssen aber Ihren flexiblen Server so konfigurieren, dass Verbindungen mit der von Ihrer Anwendung unterstützten TLS-Version (v1.0 oder v1.1) zulässig sind.|
|SSL mit TLS-Version = 1.2 erzwingen (Standardkonfiguration)|require_secure_transport = ON und tls_version = TLSV1.2| Dies ist die empfohlene Standardkonfiguration für einen flexiblen Server.|
|SSL mit TLS-Version = 1.3 erzwingen (unterstützt bei MySQL v8.0 und höher)| require_secure_transport = ON und tls_version = TLSV1.3| Dies ist nützlich und wird für die Entwicklung neuer Anwendungen empfohlen.|

> [!Note]
> Änderungen am SSL-Verschlüsselungsverfahren bei einem flexiblen Server werden nicht unterstützt. FIPS-Verschlüsselungssammlungen werden standardmäßig erzwungen, wenn „tls_version“ auf „TLS-Version 1.2“ festgelegt wird. Bei anderen TLS-Versionen als Version 1.2 wird das SSL-Verschlüsselungsverfahren auf die Standardeinstellungen festgelegt, die in der MySQL-Communityinstallation enthalten sind.

In diesem Artikel wird Folgendes behandelt:

* Konfigurieren Ihres flexiblen Servers
  * Bei deaktiviertem SSL
  * Bei SSL, das bei der TLS-Version > 1.2 erzwungen wurde
* Herstellen einer Verbindung mit Ihrem flexiblen Server mithilfe der mysql-Befehlszeile
  * Bei deaktivierten verschlüsselten Verbindungen
  * Bei aktivierten verschlüsselten Verbindungen
* Überprüfen des Verschlüsselungsstatus bei Ihrer Verbindung
* Herstellen einer Verbindung mit Ihrem flexiblen Server mit verschlüsselten Verbindungen mithilfe von verschiedenen Anwendungsframeworks

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>Deaktivieren der SSL-Erzwingung bei Ihrem flexiblen Server

Wenn Ihre Clientanwendung keine verschlüsselten Verbindungen unterstützt, müssen Sie die Erzwingung von verschlüsselten Verbindungen bei Ihrem flexiblen Server deaktivieren. Zum Deaktivieren der Erzwingung von verschlüsselten Verbindungen müssen Sie den Serverparameter „require_secure_transport“ auf „OFF“ festlegen, wie im Screenshot zu sehen ist, und die Konfiguration dafür speichern, damit er wirksam wird. „require_secure_transport“ ist ein **dynamischer Serverparameter,** der sofort wirksam wird und dafür keinen Serverneustart erfordert.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Der Screenshot zeigt, wie SSL bei einem flexiblen Azure Database for MySQL-Server deaktiviert wird.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Herstellen einer Verbindung mithilfe des mysql-Befehlszeilenclients bei deaktiviertem SSL

Das folgende Beispiel zeigt, wie Sie einen Server mithilfe der mysql-Befehlszeilenschnittstelle verbinden können. Verwenden Sie die Einstellung `--ssl-mode=DISABLED` der Verbindungszeichenfolge, um die TLS/SSL-Verbindung über den mysql-Client zu deaktivieren. Ersetzen Sie die Werte durch den tatsächlichen Servernamen und das Kennwort.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED
```

Wichtig: Wenn „require_secure_transport“ auf „OFF“ festgelegt wird, bedeutet dies nicht, dass verschlüsselte Verbindungen serverseitig nicht unterstützt werden. Wenn Sie „require_secure_transport“ bei einem flexiblen Server auf „OFF“ festlegen, der Client aber eine Verbindung mit verschlüsselter Verbindung herstellt, wird dies weiterhin akzeptiert. Die folgende Verbindung mithilfe des mysql-Clients bei einem flexiblen Server, der mit „require_secure_transport=OFF“ konfiguriert wurde, funktioniert ebenfalls, wie unten gezeigt wird.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```

```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

Kurz gesagt: „require_secure_transport=OFF“ lockert die Erzwingung verschlüsselter Verbindungen bei einem flexiblen Server und lässt – zusätzlich zu den verschlüsselten Verbindungen – unverschlüsselte Verbindungen mit dem Server über den Client zu.

## <a name="enforce-ssl-with-tls-version--12"></a>Erzwingen von SSL bei der TLS-Version < 1.2

Wenn Ihre Anwendung Verbindungen mit MySQL-Server mit SSL (aber TLS Version < 1.2) unterstützt, müssen Sie anfordern, dass der TLS-Serverparameter „versions“ bei Ihrem flexiblen Server festgelegt wird. Wenn Sie TLS-Versionen festlegen möchten, die Ihr flexibler Server unterstützen soll, müssen Sie den Serverparameter „tls_version“ auf „TLSV1“, „TLSV1.1“ oder „TLSV1“ und „TLSV1.1“ festlegen (wie im Screenshot gezeigt wird), und die Konfiguration dafür speichern, damit er wirksam wird. „tls_version“ ist ein **statischer Serverparameter**, der einen Serverneustart erfordert, damit er wirksam wird.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Der Screenshot zeigt, wie „tls version“ bei einem flexiblen Azure Database for MySQL-Server festgelegt wird.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Herstellen einer Verbindung mithilfe des mysql-Befehlszeilenclients mit TLS/SSL

### <a name="download-the-public-ssl-certificate"></a>Herunterladen des öffentlichen SSL-Zertifikats

Wenn Sie bei Ihren Clientanwendungen verschlüsselte Verbindungen verwenden möchten, müssen Sie das [öffentliche SSL-Zertifikat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) herunterladen, das auch im Azure-Portal auf dem Blatt „Netzwerk“ zur Verfügung steht, wie im folgenden Screenshot gezeigt wird.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Der Screenshot zeigt, wie ein öffentliches SSL-Zertifikat über das Azure-Portal heruntergeladen wird.":::

Speichern Sie die Zertifikatdatei am gewünschten Ort. In diesem Tutorial wird `c:\ssl` oder `\var\www\html\bin` für Ihre lokale Umgebung oder die Clientumgebung verwendet, wo die Anwendung gehostet wird. Auf diese Weise können Anwendungen über SSL eine sichere Verbindung mit der Datenbank herstellen.

Wenn Sie Ihre Flexible Server-Instanz mit *Privater Zugriff (VNET-Integration)* erstellt haben, müssen Sie eine Verbindung mit Ihrem Server über eine Ressource innerhalb desselben virtuellen Netzwerks herstellen. Sie können einen virtuellen Computer erstellen und zum virtuellen Netzwerk hinzufügen, das mit Ihrer Flexible Server-Instanz erstellt wurde.

Wenn Sie Ihre Flexible Server-Instanz mit *Öffentlicher Zugriff (zulässige IP-Adressen)* erstellt haben, können Sie die lokale IP-Adresse der Liste der Firewallregeln auf Ihrem Server hinzufügen.

Sie können [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) oder [MySQL Workbench](./connect-workbench.md) auswählen, um aus Ihrer lokalen Umgebung eine Verbindung mit dem Server herzustellen.

Das folgende Beispiel zeigt, wie Sie einen Server mithilfe der mysql-Befehlszeilenschnittstelle verbinden können. Verwenden Sie die Einstellung `--ssl-mode=REQUIRED` für die Verbindungszeichenfolge, um die Überprüfung des TLS-/SSL-Zertifikats zu erzwingen. Übergeben Sie den Pfad der lokalen Zertifikatdatei an den Parameter `--ssl-ca`. Ersetzen Sie die Werte durch den tatsächlichen Servernamen und das Kennwort.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

> [!Note]
> Vergewissern Sie sich, dass der Wert, den Sie an `--ssl-ca` übergeben haben, mit dem Dateipfad für das von Ihnen gespeicherte Zertifikat übereinstimmt.

Wenn Sie versuchen, mit unverschlüsselten Verbindungen eine Verbindung mit Ihrem Server herzustellen, werden Sie in einer Fehlermeldung informiert, dass Verbindungen mithilfe von unsicherem Transport nicht zulässig sind, ähnlich wie im folgenden Beispiel:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>Überprüfen der TLS/SSL-Verbindung

Führen Sie die MySQL-Befehl **status** aus, um sicherzustellen, dass die Verbindung mit dem MySQL-Server über TLS/SSL hergestellt wurde:

```dos
mysql> status
```

Vergewissern Sie sich, dass die Verbindung verschlüsselt ist, indem Sie die Ausgabe überprüfen, die Folgendes anzeigen sollte: **SSL: Verwendete Verschlüsselung ist**. Diese Verschlüsselungssammlung zeigt ein Beispiel und basierend auf dem Client können Sie eine andere Verschlüsselungssammlung sehen.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Herstellen einer Verbindung mit Ihrem flexiblen Server mit verschlüsselten Verbindungen mithilfe von verschiedenen Anwendungsframeworks

Entsprechend enthalten Verbindungszeichenfolgen, die im Azure-Portal unter dem Server auf der Seite „Verbindungszeichenfolgen“ vorab definiert sind, die erforderlichen Parameter für gängige Sprachen für die Verbindung mit dem Datenbankserver mithilfe von TLS/SSL. Der TLS/SSL-Parameter variiert je nach Connector. Beispiel: "useSSL=true", "sslmode=required" oder "ssl_verify_cert=true" und andere Variationen.

Sie können die folgenden Codebeispiele nutzen, um in Ihrer Anwendung über TLS/SSL eine verschlüsselte Verbindung mit Ihrer Flexible Server-Instanz einzurichten:

### <a name="wordpress"></a>WordPress

Laden Sie das [öffentliche SSL-Zertifikat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) herunter, und fügen Sie die folgenden Zeilen nach der Zeile ```// **MySQL settings - You can get this info from your web host** //``` in „wp-config.php“ ein.

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (mit PDO)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL-Connector für Java)

```java
# generate truststore and keystore in code

String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore

System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB-Connector für Java)

```java
# generate truststore and keystore in code

String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore

System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

### <a name="nodejs"></a>Node.js

```node
var fs = require('fs');
var mysql = require('mysql');
const serverCa = [fs.readFileSync("/var/www/html/DigiCertGlobalRootCA.crt.pem", "utf8")];
var conn=mysql.createConnection({
    host:"mydemoserver.mysql.database.azure.com",
    user:"myadmin",
    password:"yourpassword",
    database:"quickstartdb",
    port:3306,
    ssl: {
        rejectUnauthorized: true,
        ca: serverCa
    }
});
conn.connect(function(err) {
  if (err) throw err;
});
```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von MySQL Workbench zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL Flexible Server](./connect-workbench.md)
* [Verwenden von PHP zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL Flexible Server](./connect-php.md)
* [Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL Flexible Server mit der Azure CLI](./how-to-manage-virtual-network-cli.md)
* Erfahren Sie mehr über [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
* Erfahren Sie mehr über [Firewallregeln für Azure Database for MySQL Flexible Server](./concepts-networking-public.md#public-access-allowed-ip-addresses).
