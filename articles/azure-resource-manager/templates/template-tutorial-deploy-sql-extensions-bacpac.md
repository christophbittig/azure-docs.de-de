---
title: Importieren von SQL-BACPAC-Dateien mit Vorlagen
description: Hier erfahren Sie, wie Sie die Azure SQL-Datenbank-Erweiterung verwenden, um SQL-BACPAC-Dateien mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) zu importieren.
author: mumian
ms.date: 09/30/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ae7f7fe3e626079c7e3bcb04dc76ffe5cacfe4a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069784"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Tutorial: Importieren von SQL-BACPAC-Dateien mit ARM-Vorlagen

Erfahren Sie, wie Sie die Azure SQL-Datenbank-Erweiterung verwenden, um eine [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)-Datei mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) zu importieren. Bereitstellungsartefakte sind sämtliche Dateien, die zusätzlich zu Hauptvorlagendateien für eine Bereitstellung benötigt werden. Die BACPAC-Datei ist ein Artefakt.

In diesem Tutorial erstellen Sie eine Vorlage zum Bereitstellen eines [logischen SQL-Servers](../../azure-sql/database/logical-servers.md) und einer einzelnen Datenbank, und Sie importieren eine BACPAC-Datei. Informationen zum Bereitstellen von Azure-VM-Erweiterungen unter Verwendung von ARM-Vorlagen finden Sie unter [Tutorial: Bereitstellen von VM-Erweiterungen mit ARM-Vorlagen](./template-tutorial-deploy-vm-extensions.md).

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
>
> - Vorbereiten einer BACPAC-Datei
> - Öffnen einer Schnellstartvorlage
> - Bearbeiten der Vorlage
> - Stellen Sie die Vorlage bereit.
> - Überprüfen der Bereitstellung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Visual Studio Code mit der Erweiterung „Azure Resource Manager-Tools“. Weitere Informationen finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).
- Verwenden Sie aus Sicherheitsgründen ein generiertes Kennwort für das Serveradministratorkonto. Sie können die [Azure Cloud Shell](../../cloud-shell/overview.md) verwenden, um den folgenden Befehl in PowerShell oder Bash auszuführen:

    ```shell
    openssl rand -base64 32
    ```

    Um mehr zu erfahren, führen Sie `man openssl rand` aus, um die Handbuchseite zu öffnen.

    Azure Key Vault dient zum Schützen von kryptografischen Schlüsseln und anderen Geheimnissen. Weitere Informationen finden Sie im [Tutorial: Integrieren von Azure Key Vault in Ihre Bereitstellung einer ARM-Vorlage](./template-tutorial-use-key-vault.md). Wir empfehlen Ihnen auch, Ihr Kennwort alle drei Monate zu aktualisieren.

## <a name="prepare-a-bacpac-file"></a>Vorbereiten einer BACPAC-Datei

Auf [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) ist eine BACPAC-Datei verfügbar. Informationen zum Erstellen einer eigenen Datei finden Sie unter [Exportieren einer Datenbank aus Azure SQL-Datenbank in eine BACPAC-Datei](../../azure-sql/database/database-export.md). Wenn Sie die Datei an Ihrem eigenen Standort veröffentlichen möchten, müssen Sie die Vorlage später in diesem Tutorial aktualisieren.

Die BACPAC-Datei muss in einem Azure Storage-Konto gespeichert werden, damit sie mithilfe einer ARM-Vorlage importiert werden kann. Das folgende PowerShell-Skript bereitet die BACPAC-Datei mit den folgenden Schritten vor:

- Herunterladen der BACPAC-Datei
- Erstellen eines Azure-Speicherkontos.
- Erstellen eines Speicherkonto-Blobcontainers
- Hochladen der BACPAC-Datei in den Container
- Zeigt den Speicherkontoschlüssel, die Blob-URL, den Ressourcengruppennamen und den Speicherort an.

1. Wählen Sie **Ausprobieren** aus, um Cloud Shell zu öffnen. Kopieren Sie dann das folgende PowerShell-Skript, und fügen Sie es im Shellfenster ein.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "The project name and location are $projectName and $location"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Speichern Sie den Speicherkontoschlüssel, die BACPAC-Datei-URL, den Projektnamen und den Speicherort. Sie verwenden diese Werte, wenn Sie die Vorlage später in diesem Tutorial bereitstellen.

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

Die in diesem Tutorial verwendete Vorlage befindet sich auf [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Wählen Sie in Visual Studio Code **Datei** > **Datei öffnen** aus.
1. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.

    In der Vorlage sind zwei Ressourcen definiert:

   - `Microsoft.Sql/servers`. Informationen finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.sql/servers).
   - `Microsoft.SQL.servers/databases`. Informationen finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.sql/servers/databases).

     Bevor Sie die Vorlage anpassen, sollten Sie sich zunächst grundlegend damit vertraut machen.

1. Wählen Sie **Datei** > **Speichern unter** aus, um eine Kopie der Datei als _azuredeploy.json_ auf dem lokalen Computer zu speichern.

## <a name="edit-the-template"></a>Bearbeiten der Vorlage

1. Fügen Sie am Ende des Abschnitts `parameters` zwei Parameter hinzu, um den Speicherkontoschlüssel und die BACPAC-URL festzulegen.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Fügen Sie nach der schließenden geschweiften Klammer (`}`) der `adminPassword`-Eigenschaft ein Komma hinzu. Drücken Sie zum Formatieren der JSON-Datei in Visual Studio Code UMSCHALT+ALT+F.

1. Fügen Sie der Vorlage zwei Ressourcen hinzu.

    - Damit die SQL-Datenbank-Erweiterung BACPAC-Dateien importieren kann, müssen Sie Datenverkehr von Azure-Diensten zulassen. Wenn der SQL-Server bereitgestellt wird, aktiviert die Firewallregel die Einstellung **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten**.

      Fügen Sie unter der Serverdefinition die folgende Firewallregel hinzu:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2021-02-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        Das folgende Beispiel zeigt die aktualisierte Vorlage:

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png" alt-text="Vorlage mit Firewalldefinition.":::

    - Fügen Sie der Datenbankdefinition mithilfe des folgenden JSON-Codes eine SQL-Datenbank-Erweiterungsressource hinzu:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        Das folgende Beispiel zeigt die aktualisierte Vorlage:

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png" alt-text="Vorlage mit SQL-Datenbank-Erweiterung.":::

        Informationen zur Ressourcendefinition finden Sie in der [Referenz zur SQL-Datenbank-Erweiterung](/azure/templates/microsoft.sql/servers/databases/extensions) der API-Version. Im Anschluss sind einige zentrale Elemente aufgeführt:

        - `dependsOn` Die Erweiterungsressource muss nach der Erstellung der Datenbank erstellt werden.
        - `storageKeyType` Geben Sie die Art des zu verwendenden Speicherschlüssels an. Der Wert kann entweder `StorageAccessKey` oder `SharedAccessKey` sein. Verwenden Sie in diesem Tutorial `StorageAccessKey`.
        - `storageKey` Geben Sie den Schlüssel für das Speicherkonto an, in dem die BACPAC-Datei gespeichert ist. Bei Verwendung des Speicherschlüsseltyps `SharedAccessKey` muss ein Fragezeichen (?) vorangestellt werden.
        - `storageUri` Geben Sie die URL der in einem Speicherkonto gespeicherten BACPAC-Datei an.
        - `administratorLogin`: Der Kontoname des SQL-Administrators.
        - `administratorLoginPassword`: Das Kennwort des SQL-Administrators. Informationen zur Verwendung eines generierten Kennworts finden Sie unter [Voraussetzungen](#prerequisites).

Das folgende Beispiel zeigt die vollständige Vorlage:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Verwenden Sie den Projektnamen und den Speicherort, die beim Vorbereiten der BACPAC-Datei verwendet wurden. Damit werden alle Ressourcen in derselben Ressourcengruppe gespeichert, was beim Löschen von Ressourcen hilfreich ist.

1. Melden Sie sich bei [Cloud Shell](https://shell.azure.com) an.
1. Wählen Sie oben links **PowerShell** aus.

    :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/cloud-shell-select.png" alt-text="Öffnen Sie Azure Cloud Shell in PowerShell, und laden Sie eine Datei hoch.":::

1. Wählen Sie **Dateien hochladen/herunterladen** aus, und laden Sie die Datei _azuredeploy.json_ hoch.
1. Um die Vorlage bereitzustellen, kopieren Sie das folgende Skript, und fügen Sie es im Shellfenster ein.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the SQL admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $storageAccountKey = Read-Host -Prompt "Enter the storage account key"
    $bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUser $adminUsername `
        -adminPassword $adminPassword `
        -TemplateFile "$HOME/azuredeploy.json" `
        -storageAccountKey $storageAccountKey `
        -bacpacUrl $bacpacUrl

    Write-Host "Press [ENTER] to continue ..."
    ```

## <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

Wenn Sie über Ihren Clientcomputer auf den Server zugreifen möchten, müssen Sie eine weitere Firewallregel hinzufügen. Die IP-Adresse Ihres Clients und die IP-Adresse, die zum Herstellen der Verbindung mit dem Server verwendet wird, können aufgrund der Netzwerkadressenübersetzung (Network Address Translation, NAT) unterschiedlich sein. Weitere Informationen finden Sie unter [IP-Firewallregeln für Azure SQL-Datenbank und Azure SQL Data Warehouse](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules).

Wenn Sie sich beispielsweise beim **Abfrage-Editor anmelden**, wird eine Meldung angezeigt, dass die IP-Adresse nicht zulässig ist. Die Adresse unterscheidet sich aufgrund der NAT von der IP-Adresse Ihres Clients. Wählen Sie den Link der Nachricht aus, um eine Firewallregel für die IP-Adresse hinzuzufügen. Wenn Sie fertig sind, können Sie die IP-Adresse aus den Einstellungen für **Firewalls und virtuelle Netzwerke** des Servers löschen.

Wählen Sie im Azure-Portal die Datenbank aus der Ressourcengruppe aus. Wählen Sie **Abfrage-Editor (Vorschau)** aus, und geben Sie die Administratoranmeldeinformationen ein. Wie Sie sehen, wurden zwei Tabellen in die Datenbank importiert.

![Abfrage-Editor (Vorschau)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die von Ihnen bereitgestellten Azure-Ressourcen nicht mehr benötigt werden, löschen Sie die Ressourcengruppe. Die Ressourcengruppe, das Speicherkonto, der SQL-Server und die SQL-Datenbanken werden gelöscht.

1. Geben Sie im Azure-Portal im Suchfeld **Ressourcengruppen** ein.
1. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
1. Klicken Sie auf den Namen der Ressourcengruppe.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Um den Löschvorgang zu bestätigen, geben Sie den Namen der Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Server und eine Datenbank bereitgestellt und eine BACPAC-Datei importiert. Informationen zur Behandlung von Problemen bei der Bereitstellung von Vorlagen finden Sie unter:

> [!div class="nextstepaction"]
> [Problembehandlung bei Bereitstellungen von ARM-Vorlagen](../troubleshooting/quickstart-troubleshoot-arm-deployment.md)
