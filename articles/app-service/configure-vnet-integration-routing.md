---
title: Konfigurieren der Integration für virtuelle Netzwerke mit Anwendungsrouting
description: In diesem Anleitungsartikel erfahren Sie, wie das App-Routing in einer Integration regionaler virtueller Netzwerke konfiguriert wird.
author: madsd
ms.author: madsd
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: b62a8c4bcef5e5556820f6f4e816f01566d44f72
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892038"
---
# <a name="manage-azure-app-service-virtual-network-integration-routing"></a>Verwalten des Routings der Integration für virtuelle Netzwerke in Azure App Service

Wenn Sie das Anwendungsrouting konfigurieren, können Sie entweder den gesamten oder nur privaten Datenverkehr (auch [RFC1918](https://datatracker.ietf.org/doc/html/rfc1918#section-3)-Datenverkehr genannt) an Ihr virtuelles Azure-Netzwerk (VNet) weiterleiten. In diesem Artikel wird beschrieben, wie Sie das Anwendungsrouting konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Ihre App ist bereits mithilfe des regionalen VNet-Integrationsfeatures integriert.

## <a name="configure-in-the-azure-portal"></a>Konfigurieren im Azure-Portal

Führen Sie die folgenden Schritte aus, um **Route All** (Gesamten Datenverkehr weiterleiten) in Ihrer App über das Portal zu deaktivieren:

:::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-enabled.png" alt-text="Screenshot: Aktivieren von „Route all“ (Gesamten Datenverkehr weiterleiten)":::

1. Navigieren Sie in Ihrem App-Portal zu **Netzwerk** > **VNet-Integration**.
1. Legen Sie **Route All** (Gesamten Datenverkehr weiterleiten) auf **Deaktiviert** fest.
    
    :::image type="content" source="./media/configure-vnet-integration-routing/vnetint-route-all-disabling.png" alt-text="Screenshot: Deaktivieren von „Route all“ (Gesamten Datenverkehr weiterleiten)":::

1. Klicken Sie auf **Ja**, um zu bestätigen.

## <a name="configure-with-the-azure-cli"></a>Konfigurieren mit der Azure CLI

Sie können **Route All** (Gesamten Datenverkehr weiterleiten) auch mithilfe der Azure CLI konfigurieren. Es ist mindestens Az-Version 2.27.0 erforderlich.

```azurecli-interactive
az webapp config set --resource-group <group-name> --name <app-name> --vnet-route-all-enabled [true|false]
```

## <a name="configure-with-azure-powershell"></a>Konfigurieren mit Azure PowerShell

```azurepowershell
# Parameters
$siteName = '<app-name>'
$resourceGroupName = '<group-name>'

# Configure VNet Integration
$webApp = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName $resourceGroupName -ResourceName $siteName
Set-AzResource -ResourceId ($webApp.Id + "/config/web") -Properties @{ vnetRouteAllEnabled = $true } -Force
```

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren der Integration virtueller Netzwerke](./configure-vnet-integration-enable.md)
- [Allgemeine Übersicht über Netzwerke](./networking-features.md)