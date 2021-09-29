---
title: 'Schnellstart: Erstellen einer Azure API Management-Instanz mit PowerShell | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie eine neue Azure API Management-Instanz mithilfe von Azure PowerShell erstellen.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 09/14/2020
ms.author: danlep
ms.openlocfilehash: dcc6c96eb634316554a939688766feb153479778
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590758"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>Schnellstart: Erstellen einer neuen Azure API Management-Dienstinstanz mithilfe von PowerShell

Azure API Management (APIM) unterstützt Organisationen beim Veröffentlichen von APIs für externe Entwickler, Partnerentwickler und interne Entwickler, um das volle Potenzial von Daten und Diensten ausschöpfen zu können. API Management bietet die Kernkompetenzen zur Sicherstellung eines erfolgreichen API-Programms in Form von Entwicklerengagement, geschäftlichen Erkenntnissen, Analysen, Sicherheit und Schutz. Mit APIM können Sie moderne API-Gateways für vorhandene Back-End-Dienste, die an einem beliebigen Ort gehostet werden, erstellen und verwalten. Weitere Informationen finden Sie in der [Übersicht](api-management-key-concepts.md).

In diesem Schnellstart werden die Schritte zum Erstellen einer neuen API Management-Instanz mit Azure PowerShell-Cmdlets beschrieben.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 1.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Mit dem folgenden Befehl wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort „USA, Westen“ erstellt:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Erstellen eines API Management-Diensts

Da Sie nun über eine Ressourcengruppe verfügen, können Sie eine API Management-Dienstinstanz erstellen. Erstellen Sie eine solche Instanz mithilfe des [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement)-Befehls, und geben Sie einen Dienstname sowie Details zum Herausgeber an. Der Dienstname muss in Azure eindeutig sein.

Im folgenden Beispiel wird *myapim* als Dienstname verwendet. Ersetzen Sie diesen Namen durch einen eindeutigen Wert. Aktualisieren Sie außerdem den Name der Organisation des Herausgebers der API sowie die E-Mail-Adresse des Administrators, um Benachrichtigungen zu erhalten.

Standardmäßig erstellt der Befehl die Instanz im Developer-Tarif, wobei es sich um eine günstige Option zum Bewerten von Azure API Management handelt. Dieser Tarif ist nicht für die Produktion bestimmt. Weitere Informationen zum Skalieren der API Management-Tarife finden Sie unter [Upgrade and scale an API Management instance](upgrade-and-scale.md) (Upgraden und Skalieren einer API Management-Instanz).

> [!NOTE]
> Dieser Vorgang dauert lange. Es dauert in der Regel zwischen 30 und 40 Minuten, einen API Management-Dienst mit diesem Tarif zu erstellen und zu aktivieren.

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

Wenn der Befehl etwas zurückgibt, führen Sie [Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement) aus, damit die Eigenschaften des Azure API Management-Diensts angezeigt werden. Nach der Aktivierung lautet der Bereitstellungsstatus „Erfolgreich“, und die Dienstinstanz verfügt über mehrere dazugehörige URLs. Zum Beispiel:

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

Beispielausgabe:

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

Sobald Ihre API Management-Dienstinstanz bereitgestellt wurde, können Sie sie verwenden. Beginnen Sie mit dem Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md)
