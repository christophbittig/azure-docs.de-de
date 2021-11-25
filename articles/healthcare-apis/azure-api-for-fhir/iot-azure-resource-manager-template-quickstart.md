---
title: 'Schnellstart: Bereitstellen von Azure IoT-Konnektor für FHIR (Vorschauversion) mithilfe einer ARM-Vorlage'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie Azure IoT Connector for FHIR (Vorschau) mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) bereitstellen.
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 09/10/2021
ms.custom: devx-track-azurepowershell, mode-other
ms.openlocfilehash: 332b7aeaa9368dbdd0c4c863dfbaf174b2037331
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061517"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Schnellstart: Bereitstellen von Azure IoT-Konnektor für FHIR (Vorschauversion) mithilfe einer ARM-Vorlage (Azure Resource Manager)

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine ARM-Vorlage (Azure Resource Manager) verwenden, um Azure IoT-Konnektor für FHIR&#174; (Fast Healthcare Interoperability Resources)* – ein Feature von Azure API for FHIR – bereitzustellen. Um eine funktionierende Instanz von Azure IoT-Konnektor für FHIR bereitzustellen, werden von dieser Vorlage auch ein übergeordneter Azure API for FHIR-Dienst und eine Azure IoT Central-Anwendung bereitgestellt, die Telemetriedaten aus einem Gerätesimulator in Azure IoT-Konnektor für FHIR exportiert. Die ARM-Vorlage zum Bereitstellen von Azure IoT-Konnektor für FHIR kann über das Azure-Portal, per PowerShell oder mithilfe der CLI ausgeführt werden.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet, sobald Sie sich anmelden.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen einer Instanz von Azure IoT-Konnektor für FHIR in Azure über das Azure-Portal mithilfe einer ARM-Vorlage":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten, verwenden Sie [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten:
    * Eine Bash-Shell (z.B. Git Bash, in [Git für Windows](https://gitforwindows.org) enthalten).
    * [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Überprüfen der Vorlage

Durch die [Vorlage](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) werden folgende Azure-Ressourcen definiert:

* **Microsoft.HealthcareApis/services**
* **Microsoft.HealthcareApis/services/iomtconnectors**
* **Microsoft.IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie den folgenden Link aus, um Azure IoT-Konnektor für FHIR mit einer ARM-Vorlage im Azure-Portal bereitzustellen:

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen eines Azure IoT-Konnektor für FHIR-Diensts in Azure über das Azure-Portal mithilfe der ARM-Vorlage":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Auf der Seite **Azure API for FHIR bereitstellen**:

1. Wenn Sie möchten, ändern Sie das **Abonnement** aus dem Standardabonnement in ein anderes Abonnement.

2. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, geben Sie einen Namen für die neue Ressourcengruppe ein, und wählen Sie **OK** aus.

3. Wenn Sie eine neue Ressourcengruppe erstellt haben, wählen Sie eine **Region** für die Ressourcengruppe aus.

4. Geben Sie unter **FHIR Service Name** (Name des FHIR-Diensts) einen Namen für Ihre neue Azure API for FHIR-Instanz ein.

5. Wählen Sie unter **Standort** den Standort für Ihre Azure API for FHIR-Instanz aus. Der Standort kann mit dem Standort der Ressourcengruppe identisch sein oder sich davon unterscheiden.

6. Geben Sie unter **Iot Connector Name** (IoT-Konnektor-Name) einen Namen für Ihre Instanz von Azure IoT-Konnektor für FHIR an.

7. Geben Sie unter **Verbindungsname** einen Namen für eine innerhalb von Azure IoT-Konnektor für FHIR erstellte Verbindung an. Diese Verbindung wird von der Azure IoT Central-Anwendung verwendet, um simulierte Telemetriedaten an Azure IoT-Konnektor für FHIR zu pushen.

8. Geben Sie unter **Iot Central Name** (IoT Central-Name) einen Namen für Ihre neue Azure IoT Central-Anwendung ein. Von dieser Anwendung wird die Vorlage *Kontinuierliche Patientenüberwachung* verwendet, um ein Gerät zu simulieren.

9. Wählen Sie in der Dropdownliste **IoT Central Location** (IoT Central-Standort) den Standort Ihrer IoT Central-Anwendung aus. 

10. Klicken Sie auf **Überprüfen + erstellen**.

11. Lesen Sie die Geschäftsbedingungen, und wählen Sie dann **Erstellen** aus.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Wenn Sie die PowerShell-Skripts lokal ausführen möchten, geben Sie zunächst `Connect-AzAccount` ein, um Ihre Azure-Anmeldeinformationen einzurichten.

Wenn der `Microsoft.HealthcareApis`-Ressourcenanbieter noch nicht für Ihr Abonnement registriert ist, können Sie ihn mit dem folgenden interaktiven Code registrieren. Wählen Sie zum Ausführen des Codes in Azure Cloud Shell in der oberen Ecke eines beliebigen Codeblocks **Ausprobieren** aus.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Wenn der `Microsoft.IoTCentral`-Ressourcenanbieter noch nicht für Ihr Abonnement registriert ist, können Sie ihn mit dem folgenden interaktiven Code registrieren. Wählen Sie zum Ausführen des Codes in Azure Cloud Shell in der oberen Ecke eines beliebigen Codeblocks **Ausprobieren** aus.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Verwenden Sie den folgenden Code, um den Azure IoT-Konnektor für FHIR-Dienst mithilfe der ARM-Vorlage bereitzustellen:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

Wenn der `Microsoft.HealthcareApis`-Ressourcenanbieter noch nicht für Ihr Abonnement registriert ist, können Sie ihn mit dem folgenden interaktiven Code registrieren. Wählen Sie zum Ausführen des Codes in Azure Cloud Shell in der oberen Ecke eines beliebigen Codeblocks **Ausprobieren** aus.

```azurecli-interactive
az extension add --name healthcareapis
```

Wenn der `Microsoft.IoTCentral`-Ressourcenanbieter noch nicht für Ihr Abonnement registriert ist, können Sie ihn mit dem folgenden interaktiven Code registrieren.

```azurecli-interactive
az extension add --name azure-iot
```

Verwenden Sie den folgenden Code, um den Azure IoT-Konnektor für FHIR-Dienst mithilfe der ARM-Vorlage bereitzustellen:

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Die Bereitstellung dauert einige Minuten. Notieren Sie sich die Namen für den Azure API for FHIR-Dienst, für die Azure IoT Central-Anwendung und für die Ressourcengruppe, die Sie später zur Überprüfung der bereitgestellten Ressourcen verwenden.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um eine Übersicht über Ihren neuen Azure API for FHIR-Dienst anzuzeigen:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Azure API for FHIR**, und wählen Sie diese Option dann aus.

2. Wählen Sie in der FHIR-Liste den neuen Dienst aus. Die Seite **Übersicht** für den neuen Azure API for FHIR-Dienst wird angezeigt.

3. Um zu überprüfen, ob das neue FHIR-API-Konto bereitgestellt wurde, wählen Sie den Link neben **FHIR-Metadatenendpunkt** aus, um die FHIR-API-Funktionsanweisung abzurufen. Der Link weist das Format `https://<service-name>.azurehealthcareapis.com/metadata` auf. Wenn das Konto bereitgestellt wird, wird eine große JSON-Datei angezeigt.

4. Um zu überprüfen, ob die neue Instanz von Azure IoT-Konnektor für FHIR bereitgestellt wurde, wählen Sie im linken Navigationsmenü die Option **IoT-Konnektor (Vorschauversion)** aus, um die Seite **IoT Connectors** (IoT-Konnektor-Instanzen) zu öffnen. Auf der Seite muss die bereitgestellte Instanz von Azure IoT-Konnektor für FHIR mit dem Wert *Online* unter *Status*, dem Wert *1* unter *Verbindungen* und dem Symbol *Erfolgreich* für *Gerätezuordnung* und *FHIR-Zuordnung* angezeigt werden.

5. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **IoT Central-Anwendungen**, und wählen Sie die entsprechende Option aus.

6. Wählen Sie in der Liste der IoT Central-Anwendungen den neuen Dienst aus. Die Seite **Übersicht** für die neue IoT Central-Anwendung wird angezeigt.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrem Azure API for FHIR-Dienst anzuzeigen. Sie müssen den Namen des neuen FHIR-Diensts und die Ressourcengruppe eingeben.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> Von Azure IoT-Konnektor für FHIR werden aktuell keine PowerShell-Befehle bereitgestellt. Verwenden Sie den auf der Registerkarte **Portal** bereitgestellten Überprüfungsprozess, um zu überprüfen, ob die Instanz von Azure IoT-Konnektor für FHIR ordnungsgemäß bereitgestellt wurde.

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrer Azure IoT Central-Anwendung anzuzeigen. Sie müssen den Namen der neuen IoT Central-Anwendung und die Ressourcengruppe eingeben.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrem Azure API for FHIR-Dienst anzuzeigen. Sie müssen den Namen des neuen FHIR-Diensts und die Ressourcengruppe eingeben.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Von Azure IoT-Konnektor für FHIR werden aktuell keine CLI-Befehle bereitgestellt. Verwenden Sie den auf der Registerkarte **Portal** bereitgestellten Überprüfungsprozess, um zu überprüfen, ob die Instanz von Azure IoT-Konnektor für FHIR ordnungsgemäß bereitgestellt wurde.

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrer Azure IoT Central-Anwendung anzuzeigen. Sie müssen den Namen der neuen IoT Central-Anwendung und die Ressourcengruppe eingeben.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Verbinden Ihrer IoT-Daten mit Azure IoT-Konnektor für FHIR (Vorschauversion)
> [!IMPORTANT]
> Die in dieser Schnellstartanleitung bereitgestellte Vorlage für die Gerätezuordnung ist für den Datenexport (Legacy) in IoT Central konzipiert.

Von der IoT Central-Anwendung werden aktuell keine ARM-Vorlage und keine PowerShell- oder CLI-Befehle zum Festlegen des Datenexports bereitgestellt. Verwenden Sie daher die im Anschluss beschriebenen Schritte für das Azure-Portal.  

Nach der Bereitstellung Ihrer IoT Central-Anwendung beginnen Ihre beiden vordefinierten simulierten Geräte mit dem Generieren von Telemetriedaten. In diesem Tutorial erfassen Sie die Telemetriedaten vom Simulator *Smart Vitals Patch* in FHIR über Azure IoT-Konnektor für FHIR. Um Ihre IoT-Daten in die Instanz von Azure IoT-Konnektor für FHIR zu exportieren, [richten wir einen Datenexport (Legacy) in IoT Central ein](../../iot-central/core/howto-export-data-legacy.md). Gehen Sie auf der Seite „Datenexport (Legacy)“ wie folgt vor:
- Wählen Sie als Exportziel *Azure Event Hubs* aus.
- Wählen Sie für das Feld **Event Hubs-Namespace** den Wert *Verbindungszeichenfolge verwenden* aus.
- Geben Sie die Verbindungszeichenfolge von Azure IoT-Konnektor für FHIR an, die Sie in einem vorherigen Schritt für das Feld **Verbindungszeichenfolge** abgerufen haben.
- Übernehmen Sie für die Option **Telemetrie** die Option *Ein* für das Feld **Zu exportierende Daten**.

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Anzeigen von Gerätedaten in Azure API for FHIR

Sie können die FHIR-basierten Beobachtungsressourcen anzeigen, die von Azure IoT Connector for FHIR in Ihrem FHIR-Dienst mithilfe von Postman erstellt wurden. Weitere Informationen finden Sie unter [Zugreifen auf den FHIR-Dienst mithilfe](./../use-postman.md) von Postman und Senden einer Anforderung an , um FHIR-Beobachtungsressourcen mit `GET` einem `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` Heartrate-Wert anzeigen zu lassen.

> [!TIP]
> Vergewissern Sie sich, dass Ihr Benutzer über die erforderlichen Zugriffsrechte für die Azure API for FHIR-Datenebene verfügt. Verwenden Sie die [rollenbasierte Zugriffssteuerung in Azure](configure-azure-rbac.md) (Azure Role-Based Access Control, Azure RBAC), um die erforderlichen Datenebenenrollen zuzuweisen.

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.

2. Wählen Sie in der Liste der Ressourcengruppen den Namen Ihrer Ressourcengruppe aus.

3. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.

4. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Ein Tutorial mit schrittweisen Anleitungen zum Erstellen einer ARM-Vorlage finden Sie im [Tutorial zum Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie das Feature „Azure IoT-Konnektor für FHIR“ in Ihrer Azure API for FHIR-Ressource bereitgestellt. Weitere Informationen zu Azure IoT-Konnektor für FHIR finden Sie in den folgenden Schritten:

Machen Sie sich mit verschiedenen Datenflussphasen in Azure IoT-Konnektor für FHIR vertraut:

>[!div class="nextstepaction"]
>[Datenfluss im Azure IoT-Konnektor für FHIR](iot-data-flow.md)

Erfahren Sie, wie Sie IoT Connector mithilfe von Geräte- und FHIR-Zuordnungsvorlagen konfigurieren:

>[!div class="nextstepaction"]
>[Zuordnungsvorlagen im Azure IoT-Konnektor für FHIR](iot-mapping-templates.md)

*Im Azure-Portal wird Azure IoT-Konnektor für FHIR als „IoT-Konnektor (Vorschau)“ bezeichnet. FHIR ist eine eingetragene Marke von HL7 und wird mit Genehmigung von HL7 verwendet.
