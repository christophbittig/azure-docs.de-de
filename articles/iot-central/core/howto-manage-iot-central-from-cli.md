---
title: Verwalten von IoT Central über Azure CLI oder PowerShell | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Ihre IoT Central-Anwendung über die Azure-Befehlszeilenschnittstelle (Azure CLI) oder PowerShell erstellen und verwalten. Sie können die Anwendung mithilfe dieser Tools anzeigen, ändern und entfernen.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: b2ba640fec6f9bf2cd09611456967e15ed8f7ade
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339354"
---
# <a name="manage-iot-central-from-azure-cli-or-powershell"></a>Verwalten von IoT Central über Azure CLI oder PowerShell

Statt IoT Central-Anwendungen auf der Website [Azure IoT Central-Anwendungs-Manager](https://aka.ms/iotcentral) zu erstellen und zu verwalten, können Sie sie über [Azure CLI](/cli/azure/) oder [Azure PowerShell](/powershell/azure/) verwalten.

Wenn Sie lieber eine Sprache wie JavaScript, Python, C#, Ruby oder Go verwenden möchten, finden Sie im Repository mit den [Beispielen für das Azure IoT Central ARM SDK](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) Codebeispiele, die zeigen, wie Sie Azure IoT Central-Anwendungen erstellen, aktualisieren, auflisten und löschen.

## <a name="prerequisites"></a>Voraussetzungen

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [azure-powershell-requirements-no-header](../../../includes/azure-powershell-requirements-no-header.md)]

> [!TIP]
> Wenn Sie Ihre PowerShell-Befehle in einem anderen Azure-Abonnement ausführen müssen, finden Sie weitere Informationen unter [Ändern des aktiven Abonnements](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription).

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob das [IoT Central-Modul](/powershell/module/az.iotcentral/) in Ihre PowerShell-Umgebung installiert ist:

```powershell
Get-InstalledModule -name Az.I*
```

Wenn die Liste der installierten Module den Eintrag **Az.IotCentral** nicht enthält, führen Sie den folgenden Befehl aus:

```powershell
Install-Module Az.IotCentral
```

---

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="create-an-application"></a>Erstellen einer Anwendung

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) zum Erstellen einer IoT Central-Anwendung in Ihrem Azure-Abonnement. Beispiel:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Diese Befehle erstellen zuerst für die Anwendung eine Ressourcengruppe in der Region „East US“ (USA, Osten). In der folgenden Tabelle sind die Parameter beschrieben, die für den Befehl **az iot central app create** verwendet werden:

| Parameter         | BESCHREIBUNG |
| ----------------- | ----------- |
| resource-group    | Die Ressourcengruppe, die die Anwendung enthält. Diese Ressourcengruppe muss bereits in Ihrem Abonnement vorhanden sein. |
| location          | Standardmäßig wird in diesem Befehl der Standort aus der Ressourcengruppe verwendet. Sie können eine IoT Central-Anwendung derzeit in den geografischen Regionen **Australien**, **Asien, Osten**, **Europa**, **USA**, **Vereinigtes Königreich** und **Japan** erstellen. |
| name              | Der Name der Anwendung im Azure-Portal. Vermeiden Sie Sonderzeichen. Verwenden Sie stattdessen Kleinbuchstaben (a – z), Zahlen (0 – 9) und Bindestriche (-).|
| subdomain         | Die Unterdomäne in der URL der Anwendung. Im Beispiel lautet die Anwendungs-URL `https://mysubdomain.azureiotcentral.com`. |
| sku               | Derzeit können Sie **ST1** oder **ST2** verwenden. Weitere Informationen finden Sie unter [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/). |
| Vorlage          | Die zu verwendende Anwendungsvorlage. Ausführlichere Informationen finden Sie in der unten stehenden Tabelle. |
| display-name      | Der Name der Anwendung, wie er in der Benutzeroberfläche angezeigt wird. |

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp), um eine IoT Central-Anwendung in Ihrem Azure-Abonnement zu erstellen. Beispiel:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

Das Skript erstellt zuerst für die Anwendung eine Ressourcengruppe in der Region „East US“ (USA, Osten). In der folgenden Tabelle sind die Parameter beschrieben, die für den **New-AzIotCentralApp**-Befehl verwendet werden:

|Parameter         |BESCHREIBUNG |
|------------------|------------|
|ResourceGroupName |Die Ressourcengruppe, die die Anwendung enthält. Diese Ressourcengruppe muss bereits in Ihrem Abonnement vorhanden sein. |
|Location |Standardmäßig wird in diesem Cmdlet der Standort aus der Ressourcengruppe verwendet. Sie können eine IoT Central-Anwendung derzeit in den geografischen Regionen **Australien**, **Asien, Osten**, **Europa**, **USA**, **Vereinigtes Königreich** und **Japan** erstellen. |
|Name              |Der Name der Anwendung im Azure-Portal. Vermeiden Sie Sonderzeichen. Verwenden Sie stattdessen Kleinbuchstaben (a – z), Zahlen (0 – 9) und Bindestriche (-). |
|Unterdomäne         |Die Unterdomäne in der URL der Anwendung. Im Beispiel lautet die Anwendungs-URL `https://mysubdomain.azureiotcentral.com`. |
|Sku               |Derzeit können Sie **ST1** oder **ST2** verwenden. Weitere Informationen finden Sie unter [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/). |
|Vorlage          | Die zu verwendende Anwendungsvorlage. Ausführlichere Informationen finden Sie in der unten stehenden Tabelle. |
|DisplayName       |Der Name der Anwendung, wie er in der Benutzeroberfläche angezeigt wird. |

---

### <a name="application-templates"></a>Anwendungsvorlagen

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

Wenn Sie Ihre eigene Anwendungsvorlage erstellt haben, können Sie damit eine neue Anwendung erstellen. Wenn Sie nach einer Anwendungsvorlage gefragt werden, geben Sie die App-ID ein, die unter dem Abschnitt [Export der Anwendungsvorlage](howto-create-iot-central-application.md#create-and-use-a-custom-application-template) Ihrer App im freigabefähigen URL-Link der exportierten App angezeigt wird.

## <a name="view-applications"></a>Anwendungen anzeigen

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) zum Auflisten Ihrer IoT Central-Anwendungen und Anzeigen Ihrer Metadaten.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp), um Ihre IoT Central-Anwendungen aufzulisten und die Metadaten anzuzeigen.

---

## <a name="modify-an-application"></a>Ändern einer Anwendung

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) zum Aktualisieren der Metadaten einer IoT Central-Anwendung. Den Anzeigenamen Ihrer Anwendung können Sie beispielsweise wie folgt ändern:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp), um die Metadaten einer IoT Central-Anwendung zu aktualisieren. Den Anzeigenamen Ihrer Anwendung können Sie beispielsweise wie folgt ändern:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

---

## <a name="delete-an-application"></a>Löschen einer Anwendung

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) zum Löschen einer IoT Central-Anwendung. Beispiel:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp), um eine IoT Central-Anwendung zu löschen. Beispiel:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

---

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Azure IoT Central-Anwendungen über Azure CLI oder PowerShell verwalten, wird der folgende nächste Schritt empfohlen:

> [!div class="nextstepaction"]
> [Verwalten Ihrer Anwendung](howto-administer.md)
