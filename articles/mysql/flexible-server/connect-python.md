---
title: 'Schnellstart: Herstellen einer Verbindung unter Verwendung von Python: Azure Database for MySQL Flexible Server'
description: Diese Schnellstartanleitung enthält mehrere Python-Codebeispiele, mit deren Hilfe Sie eine Verbindung mit Azure Database for MySQL Flexible Server herstellen und Daten abfragen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 1c8195ae8552e400ae9f95812bb7152c86d7a868
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131429544"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Schnellstart: Verwenden von Python zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL Flexible Server

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In dieser Schnellstartanleitung stellen Sie unter Verwendung von Python eine Verbindung mit einer Azure Database for MySQL Flexible Server-Instanz her. Anschließend verwenden Sie SQL-Anweisungen, um Daten in der Datenbank über Mac-, Ubuntu Linux- und Windows-Plattformen abzufragen, einzufügen, zu aktualisieren und zu löschen. 

In diesem Artikel wird davon ausgegangen, dass Sie mit der Python-Entwicklung vertraut sind, aber noch keine Erfahrung mit Azure Database for MySQL Flexible Server haben.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
* Eine Azure Database for MySQL Flexible Server-Instanz. Unter [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mithilfe des Azure-Portals](./quickstart-create-server-portal.md) und [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mithilfe der Azure CLI](./quickstart-create-server-cli.md) erhalten Sie Informationen zur Erstellung einer Flexible Server-Instanz.

## <a name="preparing-your-client-workstation"></a>Vorbereiten Ihrer Clientarbeitsstation
- Wenn Sie Ihre Flexible Server-Instanz mit *Privater Zugriff (VNET-Integration)* erstellt haben, müssen Sie eine Verbindung mit Ihrem Server über eine Ressource innerhalb desselben virtuellen Netzwerks herstellen. Sie können einen virtuellen Computer erstellen und zum virtuellen Netzwerk hinzufügen, das mit Ihrer Flexible Server-Instanz erstellt wurde. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines virtuellen Netzwerks mit Azure Database for MySQL Flexible Server mithilfe der Azure CLI](./how-to-manage-virtual-network-cli.md).
- Wenn Sie Ihre Flexible Server-Instanz mit *öffentlichem Zugriff (zulässige IP-Adressen)* erstellt haben, können Sie die lokale IP-Adresse der Liste der Firewallregeln auf Ihrem Server hinzufügen. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL Flexible Server mit der Azure CLI](./how-to-manage-firewall-cli.md).

## <a name="install-python-and-the-mysql-connector"></a>Installieren von Python und dem MySQL-Connector

Führen Sie die folgenden Schritte aus, um Python und den MySQL-Connector für Python auf Ihrem Computer zu installieren: 

> [!NOTE]
> In dieser Schnellstartanleitung wird eine unformatierte SQL-Abfrage verwendet, um eine Verbindung mit MySQL herzustellen. Verwenden Sie bei Nutzung eines Webframeworks den empfohlenen Connector für das Framework, z. B. [mysqlclient](https://pypi.org/project/mysqlclient/) für Django.

1. Laden Sie [Python 3.7 oder höher](https://www.python.org/downloads/) für Ihr Betriebssystem herunter, und führen Sie die Installation durch. Stellen Sie sicher, dass Sie Python unter `PATH` hinzufügen, da dies für den MySQL-Connector erforderlich ist.
   
2. Öffnen Sie eine Eingabeaufforderung oder eine `bash`-Shell, und überprüfen Sie Ihre Python-Version, indem Sie `python -V` ausführen (Switch „V“ als Großbuchstabe).
   
3. Das Installationsprogramm für das `pip`-Paket ist in den aktuellen Versionen von Python enthalten. Führen Sie für `pip` das Update auf die aktuelle Version durch, indem Sie `pip install -U pip` ausführen. 
   
   Wenn `pip` nicht installiert ist, können Sie `get-pip.py` zum Herunterladen und Installieren verwenden. Weitere Informationen finden Sie unter [Installation](https://pip.pypa.io/en/stable/installing/). 
   
4. Verwenden Sie `pip`, um den MySQL-Connector für Python und die Abhängigkeiten zu installieren:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Sie können den Python-Connector für MySQL auch über [mysql.com](https://dev.mysql.com/downloads/connector/python/) installieren. Weitere Informationen zum MySQL-Connector für Python finden Sie unter [Entwicklerhandbuch für MySQL-Connector/Python](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

Sie können die Verbindungsinformationen, die Sie zum Herstellen einer Verbindung mit Azure Database for MySQL Flexible Server benötigen, über das Azure-Portal ermitteln. Sie benötigen den Servernamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
   
2. Suchen Sie im Portal in der Suchleiste nach der von Ihnen erstellten Azure Database for MySQL Flexible Server-Instanz, z. B. **mydemoserver**, und klicken Sie darauf.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
3. Notieren Sie sich auf der Seite **Übersicht** des Servers den **Servernamen** und die **Anmelde-ID des Serveradministrators**. Falls Sie Ihr Kennwort vergessen haben, können Sie es auf dieser Seite auch zurücksetzen.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>Codebeispiele

### <a name="run-below-mentioned-python-code-samples"></a>Ausführen der untenstehenden Codebeispiele

Führen Sie für jedes Codebeispiel in diesem Artikel die folgenden Schritte aus:

1. Erstellen Sie eine neue Datei in einem Text-Editor.
2. Fügen Sie der Datei das Codebeispiel hinzu. Ersetzen Sie im Code die Platzhalter `<mydemoserver>`, `<myadmin>`, `<mypassword>` und `<mydatabase>` durch die Werte für Ihren MySQL-Server und die zugehörige Datenbank.
3. Speichern Sie die Datei in einem Projektordner mit der Erweiterung *.py*, z. B. *C:\pythonmysql\createtable.py* oder */home/username/pythonmysql/createtable.py*.
4. Öffnen Sie zum Ausführen des Codes eine Eingabeaufforderung oder `bash`-Shell, und wechseln Sie in Ihren Projektordner, indem Sie beispielsweise `cd pythonmysql` ausführen. Geben Sie den Befehl `python` gefolgt vom Dateinamen ein (z. B. `python createtable.py`), und drücken Sie die EINGABETASTE. 
   
   > [!NOTE]
   > Falls *python.exe* unter Windows nicht gefunden wird, müssen Sie Ihrer PATH-Umgebungsvariablen ggf. den Python-Pfad hinzufügen oder den vollständigen Pfad zu *python.exe* angeben, z. B. `C:\python27\python.exe createtable.py`.

### <a name="create-a-table-and-insert-data"></a>Erstellen einer Tabelle und Einfügen von Daten

Verwenden Sie den folgenden Code, um eine Verbindung mit dem Server und der Datenbank herzustellen, eine Tabelle zu erstellen und die Daten mit einer SQL-Anweisung vom Typ **INSERT** zu laden. 

Mit dem Code wird die Bibliothek „mysql.connector“ importiert und die Funktion [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) verwendet, um eine Verbindung zum flexiblen Server herzustellen. Hierfür werden die [Argumente](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in der config-Sammlung genutzt. Im Code wird ein Cursor für die Verbindung verwendet, und mit der [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html)-Methode wird die SQL-Abfrage für die MySQL-Datenbank ausgeführt.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string

try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed).")

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table.")

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
print("Inserted",cursor.rowcount,"row(s) of data.")
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
print("Inserted",cursor.rowcount,"row(s) of data.")
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted",cursor.rowcount,"row(s) of data.")

# Cleanup
conn.commit()
cursor.close()
conn.close()
print("Done.")
```

### <a name="read-data"></a>Lesen von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **SELECT** zu verbinden und zu lesen. 

Mit dem Code wird die Bibliothek „mysql.connector“ importiert und die Funktion [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) verwendet, um eine Verbindung zum flexiblen Server herzustellen. Hierfür werden die [Argumente](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in der config-Sammlung genutzt. Im Code wird ein Cursor für die Verbindung verwendet, und mit der [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html)-Methode wird die SQL-Abfrage für die MySQL-Datenbank ausgeführt. 

Der Code liest die Datenzeilen mit der [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html)-Methode, legt die Ergebnisse in einer Zeile der Auflistung ab und verwendet einen `for`-Iterator, um einen Schleifenvorgang für die Zeilen durchzuführen.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string

try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="update-data"></a>Aktualisieren von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **UPDATE** zu verbinden und zu aktualisieren. 

Mit dem Code wird die Bibliothek „mysql.connector“ importiert und die Funktion [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) verwendet, um eine Verbindung zum flexiblen Server herzustellen. Hierfür werden die [Argumente](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in der config-Sammlung genutzt. Im Code wird ein Cursor für die Verbindung verwendet, und mit der [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html)-Methode wird die SQL-Abfrage für die MySQL-Datenbank ausgeführt. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string

try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="delete-data"></a>Löschen von Daten

Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten mithilfe einer SQL-Anweisung des Typs **DELETE** zu entfernen. 

Mit dem Code wird die Bibliothek „mysql.connector“ importiert und die Funktion [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) verwendet, um eine Verbindung zum flexiblen Server herzustellen. Hierfür werden die [Argumente](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in der config-Sammlung genutzt. Im Code wird ein Cursor für die Verbindung verwendet, und mit der [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html)-Methode wird die SQL-Abfrage für die MySQL-Datenbank ausgeführt. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal

config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string

try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Nächste Schritte
- [Verschlüsselte Konnektivität mit der Transport Layer Security (TLS 1.2) in Azure Database for MySQL Flexible Server](./how-to-connect-tls-ssl.md)
- In diesem Artikel erfahren Sie mehr über [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
- [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-firewall-portal.md).
- [Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-virtual-network-portal.md).
