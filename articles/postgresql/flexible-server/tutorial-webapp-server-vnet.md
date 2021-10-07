---
title: 'Tutorial: Erstellen einer Instanz von Azure Database for PostgreSQL Flexible Server (Vorschau) und Azure App Service-Web-App im gleichen virtuellen Netzwerk'
description: Schnellstartanleitung zum Erstellen einer Instanz von Azure Database for PostgreSQL Flexible Server mit Web-App in einem virtuellen Netzwerk
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/30/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 146a3a252a0f8105245c2a6826876e1400efdc13
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766984"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Tutorial: Erstellen einer Instanz von Azure Database for PostgreSQL Flexible Server mit App Services-Web-App im virtuellen Netzwerk

> [!IMPORTANT]
> Azure Database for PostgreSQL Flexible Server befindet sich in der Vorschau.

In diesem Tutorial wird gezeigt, wie Sie eine Azure App Service-Web-App mit Azure Database for PostgreSQL Flexible Server (Vorschau) in einem [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) erstellen.

In diesem Lernprogramm lernen Sie Folgendes:
>[!div class="checklist"]
> * Erstellen einer PostgreSQL Flexible Server-Instanz in einem virtuellen Netzwerk
> * Erstellen einer Web-App
> * Hinzufügen der Web-App zum virtuellen Netzwerk
> * Herstellen einer Verbindung mit Postgres aus der Web-App 

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli) (Version 2.0 oder höher) lokal. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. 
- Melden Sie sich mithilfe des Befehls [az login](/cli/azure/authenticate-azure-cli) bei Ihrem Konto an. Beachten Sie die Eigenschaft **id** aus der Befehlsausgabe für den entsprechenden Abonnementnamen.

  ```azurecli
  az login
  ```
- Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) die Abonnement-ID unter Ihrem Konto aus.

  ```azurecli
  az account set --subscription <subscription ID>
  ```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Erstellen einer PostgreSQL Flexible Server-Instanz in einem virtuellen Netzwerk

Erstellen Sie einen privaten flexiblen Server innerhalb eines virtuellen Netzwerks (VNET) mit dem folgenden Befehl:

```azurecli
az postgres flexible-server create --resource-group demoresourcegroup --name demoserverpostgres --vnet demoappvnet --location westus2
```
Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen Sie die Ressourcengruppe, wenn sie noch nicht vorhanden ist.
- Generiert einen Servernamen, wenn er nicht angegeben ist.
- Erstellen Sie ein neues virtuelles Netzwerk für Ihren neuen PostgreSQL-Server und ein Subnetz innerhalb dieses virtuellen Netzwerks für den Datenbankserver.
- Erstellt einen Administratorbenutzernamen und ein Kennwort für Ihren Server, sofern nicht angegeben.
- Erstellt eine leere Datenbank mit dem Namen **postgres**.

Hier ist die Beispielausgabe.

```json
Local context is turned on. Its information is saved in working directory /home/jane. You can run `az local-context off` to turn it off.
Command argument values from local context: --resource-group demoresourcegroup, --location: eastus
Command group 'postgres flexible-server' is in preview. It may be changed/removed in a future release.
Checking the existence of the resource group ''...
Creating Resource group 'demoresourcegroup ' ...
Creating new vnet "demoappvnet" in resource group "demoresourcegroup" ...
Creating new subnet "Subnet095447391" in resource group "demoresourcegroup " and delegating it to "Microsoft.DBforPostgreSQL/flexibleServers"...
Creating PostgreSQL Server 'demoserverpostgres' in group 'demoresourcegroup'...
Your server 'demoserverpostgres' is using sku 'Standard_D2s_v3' (Paid Tier). Please refer to https://aka.ms/postgres-pricing for pricing details
Make a note of your password. If you forget, you would have to resetyour password with 'az postgres flexible-server update -n demoserverpostgres --resource-group demoresourcegroup -p <new-password>'.
{
  "connectionString": "postgresql://generated-username:generated-password@demoserverpostgres.postgres.database.azure.com/postgres?sslmode=require",
  "host": "demoserverpostgres.postgres.database.azure.com",
  "id": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/demoserverpostgres",
  "location": "East US",
  "password": "generated-password",
  "resourceGroup": "demoresourcegroup",
  "skuname": "Standard_D2s_v3",
  "subnetId": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.Network/virtualNetworks/VNET095447391/subnets/Subnet095447391",
  "username": "generated-username",
  "version": "12"
}
```

## <a name="create-a-web-app"></a>Erstellen einer Web-App
In diesem Abschnitt erstellen Sie einen App-Host in der App Service-App, verbinden die App mit der Postgres-Datenbank und stellen anschließend Ihren Code auf dem Host bereit. Vergewissern Sie sich im Terminal, dass Sie sich im Repositorystamm Ihres Anwendungscodes befinden. Im Basic-Tarif wird die VNet-Integration nicht unterstützt. Verwenden Sie den Standard- oder Premium-Tarif. 

Erstellen Sie mithilfe des Befehls „az webapp up“ eine App Service-App (den Hostprozess):

```azurecli
az webapp up --resource-group demoresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Verwenden Sie für das Argument _--location_ den gleichen Standort wie für die Datenbank im vorherigen Abschnitt.
> - Ersetzen Sie \<app-name\> durch einen für Azure eindeutigen Namen. Zulässige Zeichen für \<app-name\> sind A-Z, 0-9 und -. Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.

Durch diesen Befehl werden folgende Aktionen ausgeführt, was einige Minuten dauern kann:

- Erstellen Sie die Ressourcengruppe, wenn sie noch nicht vorhanden ist. Verwenden Sie in diesem Befehl die gleiche Ressourcengruppe, in der Sie zuvor schon die Datenbank erstellt haben.
- Erstellen der App Service-App (sofern noch nicht vorhanden)
- Aktivieren der Standardprotokollierung für die App (sofern noch nicht aktiviert)
- Hochladen des Repositorys per ZIP-Bereitstellung mit aktivierter Buildautomatisierung

### <a name="create-subnet-for-web-app"></a>Erstellen eines Subnetzes für Web-Apps
Bevor Sie die VNet-Integration aktivieren, benötigen Sie ein Subnetz, das für App Service-Web-Apps delegiert ist. Bevor Sie das Subnetz erstellen, sehen Sie sich die Datenbanksubnetzadresse an, um zu vermeiden, dasselbe Adresspräfix für das Web-App-Subnetz zu verwenden. 

```azurecli
az network vnet show --resource-group demoresourcegroup -n demoappvnet
```

Führen Sie den folgenden Befehl aus, um ein neues Subnetz im gleichen virtuellen Netzwerk zu erstellen, in dem auch der Datenbankserver erstellt wurde. **Aktualisieren Sie das Adresspräfix, um Konflikte mit dem Datenbanksubnetz zu vermeiden.**

```azurecli
az network vnet subnet create --resource-group demoresourcegroup --vnet-name demoappvnet --name webappsubnet  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms
```

## <a name="add-the-web-app-to-the-virtual-network"></a>Hinzufügen der Web-App zum virtuellen Netzwerk
Verwenden Sie den [az webapp vnet-integration](/cli/azure/webapp/vnet-integration)-Befehl, um eine regionale Integration eines virtuellen Netzwerks einer Web-App hinzuzufügen. 

```azurecli
az webapp vnet-integration add --resource-group demoresourcegroup -n  mywebapp --vnet demoappvnet --subnet webappsubnet
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurieren der Umgebungsvariablen für die Datenbankverbindung
Nachdem der Code nun in App Service bereitgestellt ist, muss als Nächstes eine Verbindung zwischen der App und dem flexiblen Server in Azure hergestellt werden. Vom App-Code werden Datenbankinformationen in einer Reihe von Umgebungsvariablen erwartet. Verwenden Sie den Befehl [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set), um die Umgebungsvariablen in App Service festzulegen.

  
```azurecli
  
az webapp config appsettings set  --name mywebapp --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>" 
```
- Ersetzen Sie **postgres-server-name**,**username**,**password** im Befehl für den neu erstellten flexiblen Server.
- Ersetzen Sie **\<username\>** und **\<password\>** durch die Anmeldeinformationen, die durch den Befehl generiert wurden.
- Die Ressourcengruppe und der Name der App werden aus den zwischengespeicherten Werten in der Datei .azure/config abgerufen.
- Der Befehl erstellt Einstellungen namens **DBHOST**, **DBNAME**, **DBUSER** _ und _*DBPASS**. Wenn Ihr Anwendungscode einen anderen Namen für die Datenbankinformationen verwendet, verwenden Sie diese Namen für die App-Einstellungen, wie im Code erwähnt.

Konfigurieren Sie die Web-App so, dass alle ausgehenden Verbindungen, die vom virtuellen Netzwerk ausgehen, zugelassen werden.
```azurecli
az webapp config set --name mywebapp --resource-group demoresourcegroup --generic-configurations '{"vnetRouteAllEnabled": true}'
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie alle im Tutorial erstellten Ressourcen mit dem folgenden Befehl. Mit diesem Befehl werden alle Ressourcen in dieser Ressourcengruppe gelöscht.

```azurecli
az group delete -n demoresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
