---
title: 'Schnellstart: Einrichten von Hochverfügbarkeit mit Azure Front Door: Azure PowerShell'
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mit Azure Front Door eine hochverfügbare und leistungsstarke globale Webanwendung erstellen, indem Sie Azure PowerShell verwenden.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 29c0718f048358867f78ef58f2d0a688240d08a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086263"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Schnellstart: Erstellen einer hochverfügbaren globalen Front Door-Webanwendung mit Azure PowerShell

Führen Sie erste Schritte mit Azure Front Door aus, indem Sie Azure PowerShell verwenden, um eine hochverfügbare und leistungsstarke globale Webanwendung zu erstellen.

Front Door leitet Webdatenverkehr an bestimmte Ressourcen in einem Back-End-Pool weiter. Sie haben die Front-End-Domäne definiert, einem Back-End-Pool Ressourcen hinzugefügt und eine Routingregel erstellt. In diesem Artikel wird eine einfache Konfiguration eines Back-End-Pools mit zwei Web-App-Ressourcen und einer einzelnen Routingregel mit einem Standardpfad verwendet, der „/*“ entspricht.

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Diagramm: Front Door-Umgebung mit PowerShell" border="false":::

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. Sie können entweder eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Erstellen von zwei Instanzen einer Web-App

Für diese Schnellstartanleitung sind zwei Instanzen einer Webanwendung erforderlich, die in verschiedenen Azure-Regionen ausgeführt werden. Beide Webanwendungsinstanzen werden im Aktiv/Aktiv-Modus ausgeführt, sodass jede von ihnen Datenverkehr annehmen kann. Diese Konfiguration unterscheidet sich von einer Aktiv/Standby-Konfiguration, bei der eine Instanz als Failover fungiert.

Wenn Sie noch nicht über eine Web-App verfügen, verwenden Sie das folgende Skript, um zwei Beispiel-Web-Apps einzurichten.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-1" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-2" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanEastUS
```

## <a name="create-a-front-door"></a>Erstellen einer „Front Door“

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten von Front Door erstellen und konfigurieren:
    
* Ein Front-End-Objekt enthält die Front Door-Standarddomäne.
* Ein Back-End-Pool ist ein Satz redundanter Back-Ends, für die Front Door einen Lastenausgleich für Ihre Clientanforderung durchführt.
* Eine Routingregel ordnet Ihren Front-End-Host und das übereinstimmende URL-Pfadmuster einem bestimmten Back-End-Pool zu.

### <a name="create-a-frontend-object"></a>Erstellen eines Front-End-Objekts

Mit dem Front-End-Objekt wird der Hostname für die Front Door-Instanz konfiguriert. Standardmäßig verfügt der Hostname über das Suffix *.azurefd.net*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Erstellen des Back-End-Pools

Der Back-End-Pool besteht aus den zwei Web-Apps, die zu Beginn dieser Schnellstartanleitung erstellt wurden. In den in diesem Schritt definierten Einstellungen für den Integritätstest und Lastenausgleich werden Standardwerte verwendet.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Erstellen einer Routingregel

Mit der Routingregel wird der Back-End-Pool der Front-End-Domäne zugeordnet und der Übereinstimmungswert des Standardpfads auf „/*“ festgelegt.

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Erstellen der Front Door-Instanz

Nachdem Sie nun die erforderlichen Objekte erstellt haben, erstellen Sie als Nächstes die Front Door-Instanz:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Nach dem erfolgreichen Abschluss der Bereitstellung können Sie einen Test durchführen, indem Sie die Schritte im nächsten Abschnitt ausführen.

## <a name="test-the-front-door"></a>Testen der Front Door-Instanz

Führen Sie die folgenden Befehle aus, um den Hostnamen für die Front Door-Instanz zu erhalten.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Öffnen Sie einen Webbrowser, und geben Sie den Hostnamen ein, den Sie mit den Befehlen abgerufen haben. Front Door leitet Ihre Anforderung an eine der Back-End-Ressourcen weiter. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door-Testseite":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, wenn Sie die mit Front Door erstellten Ressourcen nicht mehr benötigen. Wenn Sie die Ressourcengruppe löschen, werden auch die Front Door-Instanz und alle zugehörigen Ressourcen gelöscht. 

Rufen Sie zum Löschen der Ressourcengruppe das Cmdlet `Remove-AzResourceGroup` auf:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Folgendes erstellt:
* Front Door
* Zwei Web-Apps

Fahren Sie mit den Front Door-Tutorials fort, um zu erfahren, wie Sie Ihrer Front Door-Instanz eine benutzerdefinierte Domäne hinzufügen.

> [!div class="nextstepaction"]
> [Hinzufügen einer benutzerdefinierten Domäne](front-door-custom-domain.md)
