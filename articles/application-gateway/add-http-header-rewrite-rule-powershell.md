---
title: Erneutes Generieren von HTTP-Headern in Azure Application Gateway
description: Dieser Artikel enthält Informationen zum erneuten Generieren von HTTP-Headern in Azure Application Gateway mithilfe von Azure PowerShell
services: application-gateway
author: KumudD
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1a3f64167978196afc5d86b983c44f021b554115
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836284"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Erneutes Generieren von HTTP-Anforderungs- und Antwortheadern mit Azure Application Gateway – Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie mit der Azure PowerShell eine [Application Gateway v2-SKU](./application-gateway-autoscaling-zone-redundant.md)-Instanz konfigurieren, um die HTTP-Header in Anforderungen und Antworten erneut zu generieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

- Sie müssen Azure PowerShell lokal ausführen, um die Schritte in diesem Artikel durchzuführen. Sie müssen außerdem Version 1.0.0 oder höher des Az-Moduls installiert haben. Führen Sie `Import-Module Az` und dann `Get-Module Az` aus, um die Version zu ermitteln, die Sie installiert haben. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Führen Sie nach dem Überprüfen der PowerShell-Version `Login-AzAccount` aus, um eine Verbindung mit Azure zu erstellen.
- Sie benötigen eine Application Gateway v2-SKU-Instanz. Das erneute Generieren von Headern wird in der v1-SKU nicht unterstützt. Wenn Sie nicht über die v2-SKU verfügen, erstellen Sie eine [Application Gateway v2-SKU](./tutorial-autoscale-ps.md)-Instanz, bevor Sie beginnen.

## <a name="create-required-objects"></a>Erstellen der erforderlichen Objekte

Um das erneute Generieren von HTTP-Headern zu konfigurieren, müssen Sie diese Schritte durchführen.

1. Erstellen Sie die Objekte, die zum erneuten Generieren von HTTP-Headern erforderlich sind:

   - **RequestHeaderConfiguration**: Diese dient dazu, die Anforderungsheaderfelder, die Sie erneut generieren möchten, und den neuen Wert für die Header anzugeben.

   - **ResponseHeaderConfiguration**: Diese dient dazu, die Antwortheaderfelder, die Sie erneut generieren möchten, und den neuen Wert für die Header anzugeben.

   - **ActionSet**: Dieses Objekt beinhaltet die Konfigurationen der Anforderungs- und Antwortheader, die zuvor angegeben wurden.

   - **Condition**: Dies ist eine optionale Konfiguration. Bedingungen für das erneute Generieren werten den Inhalt von HTTP(S)-Anforderungen und -Antworten aus. Die Aktion für das erneute Generieren wird ausgeführt, wenn die HTTP(S)-Anforderung oder -Antwort die Bedingung für das erneute Generieren erfüllt.

     Wenn Sie der Aktion mehr als eine Bedingung zuordnen, erfolgt die Aktion nur, wenn alle Bedingungen erfüllt sind. Das heißt also, dass der Vorgang ein logischer UND-Vorgang ist.

   - **RewriteRule**: Diese Regel enthält mehrere Kombinationen aus Aktion und Bedingung zum erneuten Generieren.

   - **RuleSequence**: Dieses Objekt ist eine optionale Konfiguration zum Festlegen der Reihenfolge, in der die Regeln zum erneuten Generieren ausgeführt werden. Diese Konfiguration ist hilfreich, wenn Sie mehrere Regeln zum erneuten Generieren in einem Satz zum erneuten Generieren haben. Eine Regel zum erneuten Generieren, die eine niedrigere Regelsequenz besitzt, wird zuerst ausgeführt. Wenn Sie denselben Regelsequenzwert zwei Regeln zum erneuten Generieren zuweisen, ist die Reihenfolge der Ausführung unbestimmt.

     Wenn Sie die „RuleSequence“ nicht explizit angeben, wird der Standardwert 100 festgelegt.

   - **RewriteRuleSet**: Dieses Objekt enthält mehrere Regeln zum erneuten Generieren, die einer Anforderungsroutingregel zugeordnet werden.

2. Fügen Sie den „RewriteRuleSet“ an eine Routingregel an. Die Konfiguration für das erneute Generieren wird dem Quelllistener über die Routingregel angefügt. Bei Verwendung einer einfachen Routingregel wird die Konfiguration der erneuten Generierung eines Headers einem Quelllistener zugeordnet und fungiert als erneute Generierung eines globalen Headers. Wenn eine pfadbasierte Routingregel verwendet wird, wird die Konfiguration der erneuten Generierung eines Headers in der URL-Pfadzuordnung definiert. In diesem Fall gilt sie nur für den bestimmten Pfadbereich einer Site.

Sie können mehrere solche Sätze für das erneute Generieren eines HTTP-Headers erstellen, und jeder dieser Sätze kann auf mehrere Listener angewendet werden. Allerdings können Sie auf einen bestimmten Listener nur einen Satz für das erneute Generieren anwenden.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Festlegen der Regelkonfiguration für das erneute Generieren eines HTTP-Headers

In diesem Beispiel werden wir eine Umleitungs-URL ändern, indem der Adressheader in der HTTP-Antwort immer dann erneut generiert wird, wenn der Adressheader einen Verweis auf „azurewebsites.net“ enthält. Hierzu fügen wir eine Bedingung hinzu, um zu prüfen, ob der Adressheader in der Antwort „azurewebsites.net“ enthält. Wir verwenden das Muster `(https?)://.*azurewebsites.net(.*)$`. Außerdem verwenden wir `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als Headerwert. Dieser Wert ersetzt im Adressheader *azurewebsites.net* durch *contoso.com*.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet -Condition $condition
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Abrufen der Konfiguration Ihres Application Gateway

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Abrufen der Konfiguration Ihrer Routingregel für Anforderungen

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualisieren des Application Gateway mit der Konfiguration zum erneuten Generieren von HTTP-Headern

In diesem Beispiel würde der Satz zum erneuten Generieren sofort einer grundlegenden Routingregel zugeordnet werden. Im Falle einer pfadbasierten Routingregel würde die Zuordnung nicht standardmäßig aktiviert. Der Satz zum erneuten Generieren kann entweder durch das Überprüfen der Pfade, in denen er über das Portal angewendet werden muss, aktiviert werden, oder durch Bereitstellen einer URL-Pfadzuordnungskonfiguration, die das RewriteRuleSet für die einzelnen Pfadoptionen angibt.  

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name $rewriteRuleSet.Name  -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name $reqRoutingRule.Name -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Löschen einer Rewriteregel

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Einrichten einiger gängiger Anwendungsfälle finden Sie unter [Allgemeine Szenarien zum erneuten Generieren von Headern](./rewrite-http-headers-url.md).
