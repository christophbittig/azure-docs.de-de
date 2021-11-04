---
title: 'Schnellstart: Erstellen eines Servers – Azure CLI – Azure Database for MySQL Flexible Server'
description: In diesem Schnellstart wird die Verwendung der Azure CLI zum Erstellen einer Azure Database for MySQL Flexible Server-Instanz in einer Azure-Ressourcengruppe beschrieben.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b737e4095fedec7260a8c666649c77443c7d4523
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433534"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Schnellstart: Erstellen einer Azure Database for MySQL Flexible Server-Instanz mit der Azure CLI

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


In diesem Schnellstart erfahren Sie, wie Sie mit den Befehlen der [Azure CLI](/cli/azure/get-started-with-azure-cli) in [Azure Cloud Shell](https://shell.azure.com) in fünf Minuten eine Azure Database for MySQL Flexible Server-Instanz erstellen können. 

[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]


## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel durchführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe**, um sie auszuführen.

Wenn Sie es vorziehen, die CLI lokal zu installieren und zu verwenden, müssen Sie für diesen Schnellstart mindestens Version 2.0 der Azure CLI verwenden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen sich mithilfe des Befehls [az login](/cli/azure/reference-index#az_login) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id**, die auf die **Abonnement-ID** für Ihr Azure-Konto verweist.

```azurecli-interactive
az login
```

Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account#az_account_set) das Abonnement unter Ihrem Konto aus. Notieren Sie sich aus der Ausgabe von **az login** den Wert für **id**. Sie verwenden ihn im Befehl als Wert für das Argument **subscription**. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Verwenden Sie [az account list](/cli/azure/account#az_account_list), um alle Abonnements abzurufen.

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Erstellen eines flexiblen Servers

Erstellen Sie mithilfe des Befehls `az group create` eine [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md) und anschließend in dieser Ressourcengruppe Ihren flexiblen MySQL-Server. Geben Sie daher einen eindeutigen Namen an. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myresourcegroup` am Standort `eastus2` erstellt.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Erstellen Sie mit dem Befehl `az mysql flexible-server create` einen flexiblen Server. Ein Server kann mehrere Datenbanken enthalten. Mit dem folgenden Befehl wird ein Server mit Dienststandards und -werten aus dem [lokalen Kontext](/cli/azure/local-context) Ihrer Azure CLI erstellt:

```azurecli-interactive
az mysql flexible-server create
```

Der erstellte Server weist die folgenden Attribute auf:
- Er hat einen automatisch generierten Servernamen, Administratorbenutzernamen, ein Administratorkennwort, einen Ressourcengruppennamen (sofern nicht bereits im lokalen Kontext angegeben), und er befindet sich am selben Speicherort wie die Ressourcengruppe.
- Die Dienststandardwerte für verbleibende Serverkonfigurationen lauten wie folgt: Computetarif (Burstable) (Burstfähig), Computegröße/SKU (B1MS), Aufbewahrungszeitraum für Sicherungen (7 Tage) und MySQL-Version (5.7).
- Die Standardkonnektivitätsmethode ist der private Zugriff (VNET-Integration) mit einem automatisch generierten virtuellen Netzwerk und Subnetz.

> [!NOTE]
> Die Konnektivitätsmethode kann nicht geändert werden, nachdem der Server erstellt wurde. Wenn Sie z. B. während der Erstellung *Private access (VNet Integration)* (Privater Zugriff (VNET-Integration)) ausgewählt haben, können Sie nach der Erstellung nicht zu *Public access (allowed IP addresses)* (Öffentlicher Zugriff (zulässige IP-Adressen)) wechseln. Es wird dringend empfohlen, einen Server mit privatem Zugriff zu erstellen, um mithilfe der VNET-Integration sicher auf den Server zugreifen zu können. Weitere Informationen zum privaten Zugriff finden Sie im Artikel zu [Konzepten](./concepts-networking.md).

Wenn Sie irgendwelche Standardwerte ändern möchten, finden Sie in der [Referenzdokumentation](/cli/azure/mysql/flexible-server) zur Azure CLI die komplette Liste der konfigurierbaren CLI-Parameter.

Nachstehend ist eine Beispielausgabe aufgeführt:

```json
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Wenn Sie irgendwelche Standardwerte ändern möchten, finden Sie in der [Referenzdokumentation](/cli/azure/mysql/flexible-server) zur Azure CLI die komplette Liste der konfigurierbaren CLI-Parameter.

## <a name="create-a-database"></a>Erstellen einer Datenbank
Führen Sie den folgenden Befehl aus, um eine Datenbank (**newdatabase**) zu erstellen, wenn Sie noch keine erstellt haben.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> Die Kommunikation für Verbindungen mit Azure-Datenbank für MySQL erfolgt über Port 3306. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 3306 unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 3306 öffnet.

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Das Ergebnis liegt im JSON-Format vor. Notieren Sie sich die Werte für **fullyQualifiedDomainName** und **administratorLogin**. Im Folgenden finden Sie ein Beispiel für die JSON-Ausgabe:

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Stellen Sie die Verbindung her und testen Sie sie mit Azure CLI

Azure Database for MySQL Flexible Server ermöglicht es Ihnen, mit dem Azure CLI-Befehl ```az mysql flexible-server connect``` eine Verbindung mit dem MySQL-Server herzustellen. Mit diesem Befehl können Sie die Konnektivität mit dem Datenbankserver testen, eine Schnellstartdatenbank erstellen und Abfragen direkt auf Ihrem Server ausführen, ohne mysql.exe oder MySQL Workbench installieren zu müssen.  Sie können den Befehl auch in einem interaktiven Modus für die Ausführung mehrerer Abfragen verwenden.

Führen Sie das folgende Skript aus, um die Verbindung mit der Datenbank aus Ihrer Entwicklungsumgebung zu testen und zu überprüfen:

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Beispiel:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Für eine erfolgreiche Verbindung sollte die folgende Ausgabe angezeigt werden:

```output
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Konnte die Verbindung nicht hergestellt werden, versuchen Sie Folgendes:
- Überprüfen Sie, ob Port 3306 auf dem Clientcomputer geöffnet ist.
- Überprüfen Sie, ob Benutzername und Kennwort des Serveradministrators korrekt sind.
- Überprüfen Sie, ob die Firewallregel für Ihren Clientcomputer konfiguriert wurde.
- Wenn Sie Ihren Server mit privatem Zugriff in einem virtuellen Netzwerk konfiguriert haben, vergewissern Sie sich, dass sich Ihr Clientcomputer im gleichen virtuellen Netzwerk befindet.

Führen Sie den folgenden Befehl aus, um eine einzelne Abfrage mit dem ```--querytext```-Argument ```-q``` auszuführen.

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Beispiel:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Weitere Informationen zum Verwenden des Befehls ```az mysql flexible-server connect``` finden Sie in der Dokument zu [Herstellen der Verbindung und abfragen](connect-azure-cli.md).

## <a name="connect-using-mysql-command-line-client"></a>Herstellen einer Verbindung mithilfe des mysql-Befehlszeilenclients

Wenn Sie Ihre Flexible Server-Instanz mit privatem Zugriff (VNET-Integration) erstellt haben, müssen Sie eine Verbindung mit der Serverinstanz über eine Ressource innerhalb desselben virtuellen Netzwerks herstellen. Sie können einen virtuellen Computer erstellen und zum virtuellen Netzwerk hinzufügen, das mit Ihrer Flexible Server-Instanz erstellt wurde. Weitere Informationen finden Sie in der [Dokumentation zum Konfigurieren von privatem Zugriff](how-to-manage-virtual-network-portal.md).

Wenn Sie Ihre Flexible Server-Instanz mit öffentlichem Zugriff (zulässige IP-Adressen) erstellt haben, können Sie die lokale IP-Adresse der Liste der Firewallregeln für die Serverinstanz hinzufügen. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL – Flexible Server mit Azure-Portal](how-to-manage-firewall-portal.md).

Sie können [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) oder [MySQL Workbench](./connect-workbench.md) auswählen, um aus Ihrer lokalen Umgebung eine Verbindung mit dem Server herzustellen. Azure Database for MySQL Flexible Server unterstützt das Herstellen einer Verbindung zwischen Ihren Clientanwendungen und dem MySQL-Dienst über TLS (Transport Layer Security), ehemals als SSL (Secure Sockets Layer) bezeichnet. TLS ist ein Standardprotokoll der Branche, das verschlüsselte Netzwerkverbindungen zwischen dem Datenbankserver und Clientanwendungen gewährleistet, sodass Sie Konformitätsanforderungen einhalten können. Um eine Verbindung mit dem flexiblen MySQL Server herzustellen, müssen Sie das [öffentliche SSL-Zertifikat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) herunterladen, um es von der Zertifizierungsstelle überprüfen zu lassen. Weitere Informationen zum Herstellen einer verschlüsselten Verbindung oder zum Deaktivieren von SSL finden Sie unter [Herstellen einer Verbindung mit Azure Database for MySQL - Flexible Server mit verschlüsselten Verbindungen](how-to-connect-tls-ssl.md).

Das folgende Beispiel zeigt, wie Sie einen flexiblen Server mithilfe der mysql-Befehlszeilenschnittstelle verbinden können. Wenn dies noch nicht geschehen ist, müssen Sie zunächst die MySQL-Befehlszeile installieren. Laden Sie das für SSL-Verbindungen erforderliche DigiCertGlobalRootCA-Zertifikat herunter. Verwenden Sie die Verbindungszeichenfolge --ssl-mode=REQUIRED, um die Überprüfung des TLS-/SSL-Zertifikats zu erzwingen. Übergeben Sie den Pfad der lokalen Zertifikatdatei an den Parameter --ssl-ca. Ersetzen Sie die Werte durch den tatsächlichen Servernamen und das Kennwort.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Wenn Sie Ihren flexiblen Server mit **öffentlichem Zugriff** bereitgestellt haben, können Sie auch [Azure Cloud Shell](https://shell.azure.com/bash) verwenden, um über den vorinstallierten MySQL-Client eine Verbindung mit Ihrem flexiblen Server herzustellen:

Wenn Sie Azure Cloud Shell verwenden möchten, um eine Verbindung mit Ihrem flexiblen Server herzustellen, müssen Sie für Azure Cloud Shell Netzwerkzugriff auf Ihren flexiblen Server zulassen. Navigieren Sie hierzu im Azure-Portal zum Blatt **Netzwerk** für Ihren flexiblen MySQL-Server, und aktivieren Sie im Abschnitt **Firewall** das Kontrollkästchen „Öffentlichen Zugriff auf diesen Server über beliebigen Azure-Dienst in Azure gestatten“. Klicken Sie anschließend, wie im folgenden Screenshot gezeigt, auf „Speichern“, um die Einstellung zu speichern.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Screenshot: Vorgehensweise zum Erlauben des Azure Cloud Shell-Zugriffs auf einen flexiblen MySQL-Server zum Konfigurieren des Netzwerks für öffentlichen Zugriff.":::


> [!NOTE]
> Das Kontrollkästchen **Öffentlichen Zugriff auf diesen Server über beliebigen Azure-Dienst in Azure gestatten** sollte nur zu Entwicklungs- und Testzwecken aktiviert werden. Durch diese Option wird die Firewall so konfiguriert, dass Verbindungen von IP-Adressen zugelassen werden, die einem beliebigen Azure-Dienst oder einer beliebigen Azure-Ressource zugeordnet sind. Dies schließt auch Verbindungen aus den Abonnements anderer Kunden mit ein.

Klicken Sie auf **Jetzt testen**, um Azure Cloud Shell zu starten, und verwenden Sie die folgenden Befehle, um eine Verbindung mit Ihrem flexiblen Server herzustellen. Verwenden Sie im Befehl Ihren Servernamen, Benutzernamen und Ihr Kennwort.

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
>Beim Herstellen einer Verbindung mit Ihrem flexiblen Server mithilfe von Azure Cloud Shell müssen Sie den Parameter „--ssl=true“ und nicht „--ssl-mode=REQUIRED“ verwenden.
> Der Hauptgrund ist, dass Azure Cloud Shell mit einem vorinstalliertem mysql.exe-Client aus der MariaDB-Distribution kommt, die den Parameter „--ssl-“ erfordert, während der MySQL-Client aus der Distribution von Oracle den Parameter „--ssl-mode“ erfordert.

Sollte bei dem Versuch, mithilfe des obigen Befehls eine Verbindung mit Ihrem flexiblen Server herzustellen, die folgende Fehlermeldung angezeigt werden, haben Sie vergessen, die Firewallregel mithilfe des Kontrollkästchens „Öffentlichen Zugriff auf diesen Server über beliebigen Azure-Dienst in Azure gestatten“ festzulegen, oder die Option wurde nicht gespeichert. Konfigurieren Sie die Firewall, und wiederholen Sie anschließend den Vorgang.

FEHLER 2002 (HY000): Verbindungsherstellung mit MySQL-Server nicht möglich auf \<servername\> (115)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Ressourcen nicht für einen anderen Schnellstart bzw. ein anderes Tutorial benötigen, können Sie sie mit dem folgenden Befehl löschen:

```azurecli-interactive
az group delete --name myresourcegroup
```

Wenn Sie nur den einen neu erstellten Server löschen möchten, können Sie den Befehl `az mysql server delete` ausführen.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
> [Verbinden und Abfragen mithilfe von Azure CLI](connect-azure-cli.md)
> [Herstellen einer Verbindung mit Azure Database for MySQL: Flexible Server mit verschlüsselten Verbindungen](how-to-connect-tls-ssl.md)
> [Erstellen einer PHP-Web-App (Laravel) mit MySQL](tutorial-php-database-app.md)
