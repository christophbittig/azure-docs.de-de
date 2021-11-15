---
title: 'Tutorial: Bereitstellen Spring Cloud-Anwendung, die mit dem Dienstconnector mit Azure Database for MySQL verbunden ist'
description: Erstellen Sie eine Spring Boot-Anwendung, die mit dem Dienstconnector mit Azure Database for MySQL verbunden ist.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f25ab8e1eb144675567316194c9b4ca3a547f111
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475234"
---
# <a name="tutorial-deploy-spring-cloud-application-connected-to-azure-database-for-mysql-with-service-connector"></a>Tutorial: Bereitstellen Spring Cloud-Anwendung, die mit dem Dienstconnector mit Azure Database for MySQL verbunden ist

In diesem Tutorial führen Sie die folgenden Aufgaben im Azure-Portal oder über die Azure CLI aus. Beide Methoden werden in den folgenden Verfahren näher beschrieben.

> [!div class="checklist"]
> * Bereitstellen einer Instanz von Azure Spring Cloud
> * Erstellen und Bereitstellen von Apps in Azure Spring Cloud
> * Integrieren von Azure Spring Cloud in Azure Database for MySQL mit dem Dienstconnector

## <a name="1-prerequisites"></a>1. Voraussetzungen

* [Installieren Sie JDK 8 oder JDK 11](/azure/developer/java/fundamentals/java-jdk-install).
* [Registrierung für ein Azure-Abonnement](https://azure.microsoft.com/free/)
* [Installation der Azure CLI, Version 2.0.67 oder höher](/cli/azure/install-azure-cli) und Installation der Azure Spring Cloud-Erweiterung mit dem Befehl `az extension add --name spring-cloud`

## <a name="2-provision-an-instance-of-azure-spring-cloud"></a>2. Bereitstellen einer Instanz von Azure Spring Cloud

Im folgenden Verfahren wird die Azure CLI-Erweiterung zum Bereitstellen einer Azure Spring Cloud-Instanz verwendet.

1. Aktualisieren Sie die Azure CLI mit der Azure Spring Cloud-Erweiterung.

    ```azurecli
    az extension update --name spring-cloud
    ```

1. Melden Sie sich bei der Azure CLI an, und wählen Sie Ihr aktives Abonnement aus.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Bereiten Sie einen Namen für Ihren Azure Spring Cloud-Dienst vor.  Der Name muss zwischen 4 und 32 Zeichen lang sein und darf nur Kleinbuchstaben, Ziffern und Bindestriche enthalten.  Das erste Zeichen des Dienstnamens muss ein Buchstabe und das letzte Zeichen entweder ein Buchstabe oder eine Ziffer sein.

1. Erstellen Sie eine Ressourcengruppe, die Ihren Azure Spring Cloud-Dienst enthält.  Erstellen Sie eine Instanz des Azure Spring Cloud-Diensts.

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg
    az spring-cloud create -n <service instance name> -g ServiceConnector-tutorial-rg
    ```

## <a name="3-create-an-azure-database-for-mysql"></a>3. Erstellen einer Azure Database for MySQL-Instanz

Im folgenden Verfahren wird die Azure CLI-Erweiterung zum Bereitstellen einer Azure Database for MySQL-Instanz verwendet.

1. Installieren Sie die [db-up](/cli/azure/ext/db-up/mysql)-Erweiterung.

```azurecli
az extension add --name db-up
```

Erstellen Sie mit dem folgenden Befehl einen Azure Database for MySQL-Server:

```azurecli
az mysql up --resource-group ServiceConnector-tutorial-rg --admin-user <admin-username> --admin-password <admin-password>
```

- Geben Sie für *\<admin-username>* und *\<admin-password>* Anmeldeinformationen ein, um einen Administratorbenutzer für diesen MySQL-Server zu erstellen. Der Administratorbenutzername darf nicht *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* oder *public* lauten. Er kann nicht mit *pg_* beginnen. Das Kennwort muss **8 bis 128 Zeichen** aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (z. B. !, #, %). Das Kennwort darf nicht den Benutzernamen enthalten.

Der Server wird mit den folgenden Standardwerten erstellt (sofern Sie diese nicht manuell überschreiben):

**Einstellung** | **Standardwert** | **Beschreibung**
---|---|---
Servername | Systemgeneriert | Ein eindeutiger Name, der Ihren Server für Azure-Datenbank für MySQL identifiziert.
sku-name | GP_Gen5_2 | Der Name der SKU. Folgt der Konvention „{Tarif}\_{Computegeneration}\_{virtuelle Kerne}“ in Kurzform. Der Standardwert ist ein Gen5-Server vom Typ „Universell“ mit zwei virtuellen Kernen. Informationen zu den Tarifen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/mysql/).
backup-retention | 7 | Gibt die Aufbewahrungsdauer für eine Sicherung an. Die Einheit ist Tage.
geo-redundant-backup | Disabled | Gibt an, ob georedundante Sicherungen für diesen Server aktiviert werden sollen.
location | westus2 | Der Azure-Standort für den Server.
ssl-enforcement | Aktiviert | Gibt an, ob SSL für diesen Server aktiviert werden soll.
storage-size | 5120 | Die Speicherkapazität des Servers (Einheit: MB).
version | 5.7 | Die MySQL-Hauptversion.


> [!NOTE]
> Weitere Informationen zum Befehl `az mysql up` und den zugehörigen zusätzlichen Parametern finden Sie in der [Dokumentation der Azure-Befehlszeilenschnittstelle](/cli/azure/mysql#az_mysql_up).

Nach der Erstellung weist Ihr Server die folgenden Einstellungen auf:

- Es wird eine Firewallregel namens „devbox“ erstellt. Die Azure-Befehlszeilenschnittstelle versucht, die IP-Adresse des Computers zu ermitteln, auf dem der Befehl `az mysql up` ausgeführt wird, und lässt diese IP-Adresse zu.
- „Zugriff auf Azure-Dienste zulassen“ ist aktiviert. Mit dieser Einstellung wird die Firewall des Servers so konfiguriert, dass sie Verbindungen von allen Azure-Ressourcen akzeptiert – auch von Ressourcen, die nicht in Ihrem Abonnement enthalten sind.
- Der Parameter `wait_timeout` ist auf 8 Stunden festgelegt.
- Es wird eine leere Datenbank namens `sampledb` erstellt.
- Es wird ein neuer Benutzer mit dem Namen „root“ und Berechtigungen für `sampledb` erstellt.

## <a name="4-build-and-deploy-the-app"></a>4. Erstellen und Bereitstellen der App

1. Erstellen Sie die App mit einem zugewiesenen öffentlichen Endpunkt. Fügen Sie die Option `--runtime-version=Java_11` ein, wenn Sie beim Generieren des Spring Cloud-Projekts die Java-Version 11 ausgewählt haben.

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg --assign-endpoint true
    ```

1. Erstellen Sie Dienstverbindungen zwischen Spring Cloud und der MySQL-Datenbank.

    ```azurecli
    az spring-cloud connection create mysql
    ```

    > [!NOTE]
    > Wird die Fehlermeldung „Das Abonnement ist nicht für die Verwendung des Ressourcenanbieters "{0}" registriert.“ angezeigt, führen Sie `az provider register -n Microsoft.ServiceLinker` aus, um den Dienstconnector-Ressourcenanbieter zu registrieren. Führen Sie anschließend erneut den Verbindungsbefehl aus. 

1. Klonen des Beispielcodes

    ```bash
    git clone https://github.com/Azure-Samples/serviceconnector-springcloud-mysql-springboot.git
    ```

1. Erstellen Sie das Projekt mithilfe von Maven.

    ```bash
    cd serviceconnector-springcloud-mysql-springboot
    mvn clean package -DskipTests 
    ```

1. Stellen Sie die JAR-Datei für die App bereit (`target/demo-0.0.1-SNAPSHOT.jar`).

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg  --artifact-path target/demo-0.0.1-SNAPSHOT.jar
    ```

1. Fragen Sie den App-Status nach der Bereitstellung mit dem folgenden Befehl ab:

    ```azurecli
    az spring-cloud app list -o table
    ```

    Die Ausgabe sollte in etwa wie folgt aussehen.

    ```
    Name               Location    ResourceGroup    Production Deployment    Public Url                                           Provisioning Status    CPU    Memory    Running Instance    Registered Instance    Persistent Storage
    -----------------  ----------  ---------------  -----------------------  ---------------------------------------------------  ---------------------  -----  --------  ------------------  ---------------------  --------------------
    hellospring         eastus    <resource group>   default                                                                       Succeeded              1      2         1/1                 0/1                    -
    ```

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
