---
title: 'Schnellstart: Erstellen eines Profils für Hochverfügbarkeit von Anwendungen: Azure PowerShell – Azure Traffic Manager'
description: In diesem Schnellstartartikel wird beschrieben, wie Sie ein Traffic Manager-Profil erstellen, um eine hoch verfügbare Webanwendung zu entwickeln.
services: traffic-manager
author: duongau
ms.author: duau
manager: kumud
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: a970013ebffc9ed4249baf34de39d146357742c4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074648"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Schnellstart: Erstellen eines Traffic Manager-Profils für eine hoch verfügbare Webanwendung mit Azure PowerShell

In dieser Schnellstartanleitung wird beschrieben, wie Sie ein Traffic Manager-Profil erstellen, mit dem die Hochverfügbarkeit für Ihre Webanwendung sichergestellt wird.

In dieser Schnellstartanleitung erstellen Sie zwei Instanzen einer Webanwendung. Jede Instanz wird in einer anderen Azure-Region ausgeführt. Sie erstellen ein Traffic Manager-Profil basierend auf der [Endpunktpriorität](traffic-manager-routing-methods.md#priority-traffic-routing-method). Das Profil leitet den Benutzerdatenverkehr an den primären Standort, an dem die Webanwendung ausgeführt wird. Die Webanwendung wird von Traffic Manager ständig überwacht. Wenn der primäre Standort nicht verfügbar ist, erfolgt automatisch ein Failover zum Sicherungsstandort.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Diagramm: Traffic Manager-Bereitstellungsumgebung mit Azure PowerShell" border="false":::

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie jetzt ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.

```azurepowershell-interactive

# Variables
$Location1="EastUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

Erstellen Sie mit [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) ein Traffic Manager-Profil, das Benutzerdatenverkehr basierend auf der Endpunktpriorität weiterleitet.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Erstellen von Web-Apps

Für diese Schnellstartanleitung benötigen Sie zwei Instanzen einer Webanwendung, die in zwei unterschiedlichen Azure-Regionen bereitgestellt werden (*USA, Westen* und *USA, Osten*). Jede dient jeweils als primärer bzw. Failoverendpunkt für Traffic Manager.

### <a name="create-web-app-service-plans"></a>Erstellen von Web-App-Dienstplänen
Erstellen Sie Web-App-Dienstpläne, indem Sie [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) für die beiden Instanzen der Webanwendung verwenden, die Sie in zwei unterschiedlichen Azure-Regionen bereitstellen.

```azurepowershell-interactive

# Variables
$Location1="EastUS"
$Location2="WestEurope"

# Create an App service plan
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Erstellen einer Web-App im App Service-Plan
Erstellen Sie zwei Instanzen der Webanwendung, indem Sie [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) in den App Service-Plänen in den Azure-Regionen *USA, Osten* und *Europa, Westen* verwenden.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name myWebAppEastUS -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "myAppServicePlanEastUS").Id
$App2ResourceId=(New-AzWebApp -Name myWebAppWestEurope -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "myAppServicePlanWestEurope").Id

```

## <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten
Fügen Sie die beiden Web-Apps dem Traffic Manager-Profil wie folgt als Traffic Manager-Endpunkte hinzu, indem Sie [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) verwenden:
- Fügen Sie die Web-App aus der Azure-Region *USA, Osten* als primären Endpunkt für das Routing des gesamten Benutzerdatenverkehrs hinzu. 
- Fügen Sie die Web-App aus der Azure-Region *Europa, Westen* als Failoverendpunkt hinzu. Wenn der primäre Endpunkt nicht verfügbar ist, wird der Datenverkehr automatisch an den Failoverendpunkt weitergeleitet.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "myFailoverEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Testen des Traffic Manager-Profils

In diesem Abschnitt überprüfen Sie den Domänennamen Ihres Traffic Manager-Profils. Außerdem konfigurieren Sie den primären Endpunkt so, dass er nicht verfügbar ist. Abschließend können Sie sehen, dass die Web-App weiterhin verfügbar ist. Dies liegt daran, dass Traffic Manager den Datenverkehr an den Failoverendpunkt sendet.

### <a name="determine-the-dns-name"></a>Ermitteln des DNS-Namens

Ermitteln Sie den DNS-Namen des Traffic Manager-Profils mit [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopieren Sie den Wert von **RelativeDnsName**. Der DNS-Name Ihres Traffic Manager-Profils lautet „*http://<* relativednsname *>.trafficmanager.net*“. 

### <a name="view-traffic-manager-in-action"></a>Anzeigen von Traffic Manager in Aktion
1. Geben Sie in einem Webbrowser den DNS-Namen Ihres Traffic Manager-Profils (*http://<* relativednsname *>.trafficmanager.net*) ein, um die Standardwebsite Ihrer Web-App anzuzeigen.

    > [!NOTE]
    > In diesem Schnellstartszenario werden alle Anforderungen an den primären Endpunkt weitergeleitet. Es ist **Priorität 1** festgelegt.
2. Deaktivieren Sie Ihren primären Standort mit [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint), um das Traffic Manager-Failover in Aktion zu sehen.

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Kopieren Sie den DNS-Namen Ihres Traffic Manager-Profils (*http://<* relativednsname *>.trafficmanager.net*), um die Website in einer neuen Browsersitzung anzuzeigen.
4. Vergewissern Sie sich, dass die Web-App weiterhin verfügbar ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppen, Webanwendungen und alle dazugehörigen Ressourcen mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), wenn Sie fertig sind.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Traffic Manager-Profil erstellt, um für Ihre Webanwendung für Hochverfügbarkeit zu sorgen. Weitere Informationen zum Weiterleiten des Datenverkehrs finden Sie in den Tutorials zu Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager-Tutorials](tutorial-traffic-manager-improve-website-response.md)
