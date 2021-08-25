---
title: 'Tutorial: Konfigurieren von Routenfiltern für Microsoft-Peering: Azure PowerShell'
description: In diesem Tutorial wird erläutert, wie Sie mithilfe von PowerShell Routenfilter für das Microsoft-Peering konfigurieren.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 08/02/2021
ms.author: duau
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: dca4a74ea7ba345a6ca56b72eb933563abc831bf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749655"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-powershell"></a>Tutorial: Konfigurieren von Routenfiltern für Microsoft-Peering mithilfe von PowerShell

> [!div class="op_single_selector"]
> * [Azure-Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](how-to-routefilter-cli.md)
> 

Routenfilter stellen eine Möglichkeit dar, um eine Teilmenge von unterstützten Diensten durch das Microsoft-Peering zu nutzen. Die in diesem Artikel erläuterten Schritte unterstützen Sie bei der Konfiguration und Verwaltung von Routenfiltern für ExpressRoute-Verbindungen.

Das Microsoft-Peering ermöglicht den Zugriff auf Microsoft 365-Dienste wie Exchange Online, SharePoint Online und Skype for Business sowie öffentliche Azure-Dienste wie Storage und SQL-Datenbank. Öffentliche Azure-Dienste sind auf Regionsbasis auswählbar und können nicht per öffentlichem Dienst definiert werden.

Wenn das Microsoft-Peering in einer ExpressRoute-Leitung konfiguriert wird, werden alle Präfixe im Zusammenhang mit diesen Diensten über die eingerichteten BGP-Sitzungen angekündigt. Jedem Präfix wird zur Identifizierung des Diensts, der über das Präfix angeboten wird, ein BGP-Communitywert angefügt. Eine Liste der BGP-Communitywerte und der Dienste, denen sie zugeordnet sind, finden Sie unter [BGP-Communitys](expressroute-routing.md#bgp).

Die Konnektivität mit allen Azure- und Microsoft 365-Diensten bewirkt, dass eine große Anzahl von Präfixen über BGP angekündigt wird. Dadurch werden die Routingtabellen, die von Routern innerhalb Ihres Netzwerks verwaltet werden, erheblich größer. Wenn Sie nur eine Teilmenge der Dienste nutzen möchten, die über das Microsoft-Peering angeboten werden, können Sie die Routentabellen auf zwei Arten verringern. Ihre Möglichkeiten:

* Sie filtern unerwünschte Präfixe heraus, indem Sie Routenfilter auf BGP-Communitys anwenden. Routenfilterung ist eine standardmäßige Vorgehensweise bei Netzwerken, die bei zahlreichen Netzwerken zum Einsatz kommt.

* Sie definieren Routenfilter und wenden sie auf Ihre ExpressRoute-Verbindung an. Ein Routenfilter ist eine neue Ressource, mit der Sie die Liste der Dienste, die Sie über das Microsoft-Peering nutzen möchten, auswählen können. ExpressRoute-Router senden lediglich die Liste der Präfixe, die den im Routenfilter identifizierten Diensten zugehörig sind.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Abrufen von BGP-Communitywerten
> - Erstellen eines Routenfilters und einer Filterregel
> - Verknüpfen des Routenfilters mit einer ExpressRoute-Leitung

### <a name="about-route-filters"></a><a name="about"></a>Informationen zu Routenfiltern

Wenn das Microsoft-Peering in Ihrer ExpressRoute-Leitung konfiguriert wird, richten die Microsoft Edge-Router über Ihren Konnektivitätsanbieter ein BGP-Sitzungspaar mit den Edge-Routern ein. Ihrem Netzwerk werden keine Routen angekündigt. Um Routenankündigungen für Ihr Netzwerk zu aktivieren, müssen Sie einen Routenfilter zuordnen.

Durch einen Routenfilter können Sie die Dienste identifizieren, die Sie über das Microsoft-Peering Ihrer ExpressRoute-Verbindung nutzen möchten. Im Wesentlichen handelt es sich um eine Zulassungsliste für alle BGP-Communitywerte. Sobald eine Routenfilterressource definiert und an eine ExpressRoute-Leitung angefügt ist, werden Ihrem Netzwerk alle Präfixe angekündigt, die den BGP-Communitywerten zugeordnet sind.

Um Routenfilter mit Microsoft 365-Diensten anfügen zu können, müssen Sie zur Nutzung von Microsoft 365-Diensten über ExpressRoute autorisiert sein. Wenn Sie nicht zur Nutzung von Microsoft 365-Diensten über ExpressRoute autorisiert sind, tritt beim Vorgang zum Anfügen von Routenfiltern ein Fehler auf. Weitere Informationen zum Autorisierungsvorgang finden Sie unter [Azure ExpressRoute für Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Beim Microsoft-Peering von ExpressRoute-Verbindungen, die vor dem 1. August 2017 konfiguriert wurden, werden alle Dienstpräfixe über das Microsoft-Peering angekündigt, auch wenn keine Routenfilter definiert sind. Beim Microsoft-Peering von ExpressRoute-Verbindungen, die am oder nach dem 1. August 2017 konfiguriert wurden, werden Präfixe erst angekündigt, wenn der Verbindung ein Routenfilter hinzugefügt wurde.
> 
## <a name="prerequisites"></a>Voraussetzungen

- Lesen Sie vor Beginn der Konfiguration die Seiten zu den [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).

- Sie benötigen eine aktive ExpressRoute-Verbindung, für die das Microsoft-Peering bereitgestellt ist. Zur Durchführung dieser Aufgaben können Sie folgende Anweisungen befolgen:
  - [Erstellen Sie eine ExpressRoute-Leitung](expressroute-howto-circuit-arm.md), und lassen Sie die Leitung von Ihrem Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Die ExpressRoute-Verbindung muss den Zustand „Provisioned“ und „Enabled“ aufweisen.
  - [Erstellen Sie das Microsoft-Peering](expressroute-circuit-peerings.md), wenn Sie die BGP-Sitzung direkt verwalten. Überlassen Sie die Bereitstellung des Microsoft-Peerings für Ihre Verbindung wahlweise Ihrem Konnektivitätsanbieter.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie Ihr Abonnement aus.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a> Abrufen einer Liste von Präfixen und BGP-Communitywerten

1. Rufen Sie mit dem folgenden Cmdlet die Liste von BGP-Communitywerten und Präfixen ab, die Diensten mit Zugriff über Microsoft-Peering zugeordnet sind:

    ```azurepowershell-interactive
    Get-AzBgpServiceCommunity
    ```

1. Erstellen Sie eine Liste von BGP-Communitywerten, die Sie im Routenfilter verwenden möchten.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Erstellen eines Routenfilters und einer Filterregel

Ein Routenfilter kann nur eine Regel aufweisen, die zudem vom Typ „Zulassen“ sein muss. Diese Regel kann eine Liste von BGP-Communitywerten enthalten, die ihr zugeordnet sind. Mit dem Befehl `az network route-filter create` wird nur eine Routenfilterressource erstellt. Nachdem Sie die Ressource erstellt haben, müssen Sie eine Regel erstellen und dem Routenfilterobjekt anfügen.

1. Führen Sie den folgenden Befehl aus, um eine Routenfilterressource zu erstellen:

    ```azurepowershell-interactive
    New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
    ```

1. Führen Sie den folgenden Befehl aus, um eine Routenfilterregel zu erstellen:
 
    ```azurepowershell-interactive
    $rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
    ```

1. Führen Sie den folgenden Befehl aus, um die Filterregel zum Routenfilter hinzuzufügen:
 
    ```azurepowershell-interactive
    $routefilter = Get-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
    $routefilter.Rules.Add($rule)
    Set-AzRouteFilter -RouteFilter $routefilter
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Anfügen des Routenfilters zu einer ExpressRoute-Verbindung

Führen Sie den folgenden Befehl aus, um der ExpressRoute-Verbindung den Routenfilter anzufügen, sofern Sie nur über Microsoft-Peering verfügen:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "MyResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Häufige Aufgaben

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Abrufen der Eigenschaften eines Routenfilters

Um die Eigenschaften eines Routenfilters abzurufen, führen Sie die folgenden Schritte durch:

1. Führen Sie den folgenden Befehl aus, um die Routenfilterressource abzurufen:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
   ```
2. Rufen Sie die Routenfilterregeln für die Routenfilterressource ab, indem Sie folgenden Befehl ausführen:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aktualisieren der Eigenschaften eines Routenfilters

Wenn der Routenfilter bereits an eine Leitung angefügt ist, geben Updates der BGP-Communityliste Änderungen an Präfixankündigungen automatisch über die eingerichtete BGP-Sitzung weiter. Sie können die BGP-Communityliste Ihres Routenfilters mit dem folgenden Befehl aktualisieren:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Trennen eines Routenfilters von einer ExpressRoute-Verbindung

Nachdem ein Routenfilter von der ExpressRoute-Verbindung getrennt wurde, werden keine Präfixe über die BGP-Sitzung angekündigt. Sie können einen Routenfilter mit dem folgenden Befehl von einer ExpressRoute-Verbindung trennen:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können einen Routenfilter nur löschen, wenn er an keine Verbindung angefügt ist. Stellen Sie sicher, dass der Routenfilter an keine Verbindung angefügt ist, bevor Sie versuchen, ihn zu löschen. Sie können einen Routenfilter mit dem folgenden Befehl löschen:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

Beispiele für Routerkonfigurationen finden Sie hier:

> [!div class="nextstepaction"]
> [Beispiele für die Routerkonfiguration zum Einrichten und Verwalten des Routings](expressroute-config-samples-routing.md)
