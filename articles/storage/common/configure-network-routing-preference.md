---
title: Konfigurieren der Netzwerkroutingpräferenz
titleSuffix: Azure Storage
description: Konfigurieren Sie die Netzwerkroutingpräferenz für Ihr Azure-Speicherkonto, um festzulegen, wie Netzwerkdatenverkehr von Clients über das Internet zu Ihrem Konto geleitet wird.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c871a1ec4feec89cc3250f1fbfefefa69ed927bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589319"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Konfigurieren der Netzwerkroutingpräferenz für Azure Storage

In diesem Artikel wird beschrieben, wie Sie die Netzwerkroutingpräferenz und routenspezifische Endpunkte für Ihr Speicherkonto konfigurieren können.

Die Netzwerkroutingpräferenz legt fest, wie Netzwerkdatenverkehr von Clients über das Internet zu Ihrem Konto geleitet wird. Routenspezifische Endpunkte sind neue Endpunkte, die Azure Storage für Ihr Speicherkonto erstellt. Diese Endpunkte leiten den Datenverkehr über einen gewünschten Pfad weiter, ohne die Standardroutingpräferenz zu ändern. Weitere Informationen finden Sie unter [Konfigurieren der Netzwerkroutingpräferenz für Azure Storage](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Konfigurieren der Routingpräferenz für den öffentlichen Standardendpunkt

Standardmäßig ist die Routingpräferenz für den öffentlichen Endpunkt des Speicherkontos auf das globale Microsoft-Netzwerk festgelegt. Sie können zwischen dem globalen Microsoft-Netzwerk und dem Internetrouting als standardmäßige Routingpräferenz für den öffentlichen Endpunkt Ihres Speicherkontos wählen. Weitere Informationen zum Unterschied zwischen diesen beiden Routingtypen finden Sie unter [Konfigurieren der Netzwerkroutingpräferenz für Azure Storage (Vorschau)](network-routing-preference.md).

### <a name="portal"></a>[Portal](#tab/azure-portal)

So ändern Sie die Routingpräferenz in das Internetrouting:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie im Portal zu Ihrem Speicherkonto.

3. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus.

    > [!div class="mx-imgBorder"]
    > ![Menüoption „Netzwerk“](./media/configure-network-routing-preference/networking-option.png)

4. Ändern Sie auf der Registerkarte **Firewalls und virtuelle Netzwerke** unter **Netzwerkrouting** die Einstellung für die **Routingpräferenz** in **Internetrouting**.

5. Klicken Sie auf **Speichern**.

    > [!div class="mx-imgBorder"]
    > ![Internetrouting-Option](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Authentifizierungsanweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, auf dem Ihre statische Website gehostet wird.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

3. Verwenden Sie den Befehl [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) und legen Sie den `--routing-choice`-Parameter auf `InternetRouting` fest, um die Routingeinstellungen für das Internetrouting zu ändern.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Ersetzen Sie den Wert des Platzhalters `<resource-group-name>` durch den Namen der Ressourcengruppe, die das Speicherkonto enthält.

   Ersetzen Sie den Werte des Platzhalters `<storage-account-name>` durch den Namen des Speicherkontos.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Melden Sie sich bei Ihrem Azure-Abonnement an.

   - Melden Sie sich zum Starten von Azure Cloud Shell beim [Azure-Portal](https://portal.azure.com) an.

   - Wenn Sie sich bei Ihrer lokalen Installation der Befehlszeilenschnittstelle anmelden möchten, führen Sie den Befehl [az login](/cli/azure/reference-index#az_login) aus:

     ```azurecli
     az login
     ```

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement auf das Abonnement des Speicherkontos fest, auf dem Ihre statische Website gehostet wird.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

3. Verwenden Sie den Befehl [Set-AzStorageAccount](/cli/azure/storage/account#az_storage_account_update) und legen Sie den `--routing-choice`-Parameter auf `InternetRouting` fest, um die Routingeinstellungen für das Internetrouting zu ändern.

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

---

## <a name="configure-a-route-specific-endpoint"></a>Konfigurieren eines routenspezifischen Endpunkts

Sie können auch einen routenspezifischen Endpunkt konfigurieren. Beispielsweise können Sie die Routingpräferenz für den Standardendpunkt auf *Internetrouting* festlegen und dann einen routenspezifischen Endpunkt veröffentlichen, der ermöglicht, dass der Datenverkehr zwischen Clients im Internet und Ihrem Speicherkonto über das globale Microsoft-Netzwerk weitergeleitet wird.

Diese Einstellung wirkt sich nur auf den routenspezifischen Endpunkt aus. Diese Einstellung wirkt sich nicht auf Ihre Standardroutingpräferenz aus.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie im Portal zu Ihrem Speicherkonto.

2. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus.

3. Wählen Sie auf der Registerkarte **Firewalls und virtuelle Netzwerke** unter **Routenspezifische Endpunkte veröffentlichen** die Routingpräferenz Ihres routenspezifischen Endpunkts aus, und klicken Sie dann auf **Speichern**.

    In der folgenden Abbildung ist die Option **Microsoft-Netzwerkrouting** ausgewählt.

    > [!div class="mx-imgBorder"]
    > ![Option „Microsoft-Netzwerkrouting“](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Um einen routenspezifischen Endpunkt zu konfigurieren, verwenden Sie den Befehl [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount).

   - Legen Sie zum Erstellen eines routenspezifischen Endpunkts, der die Routingeinstellungen für das Microsoft-Netzwerk verwendet, den `-PublishMicrosoftEndpoint`-Parameter auf `true` fest.

   - Legen Sie zum Erstellen eines routenspezifischen Endpunkts, der die Internetroutingeinstellungen verwendet, den `-PublishInternetEndpointTo`- Parameter auf `true` fest.

   Im folgenden Beispiel wird ein routenspezifischer Endpunkt erstellt, der die Routingeinstellungen für das Microsoft-Netzwerk verwendet.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Ersetzen Sie den Wert des Platzhalters `<resource-group-name>` durch den Namen der Ressourcengruppe, die das Speicherkonto enthält.

   Ersetzen Sie den Werte des Platzhalters `<storage-account-name>` durch den Namen des Speicherkontos.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Verwenden Sie den Befehl [az storage account update](/cli/azure/storage/account#az_storage_account_update), um einen routenspezifischen Endpunkt zu konfigurieren.

   - Legen Sie zum Erstellen eines routenspezifischen Endpunkts, der die Routingeinstellungen für das Microsoft-Netzwerk verwendet, den `--publish-microsoft-endpoints`-Parameter auf `true` fest.

   - Legen Sie zum Erstellen eines routenspezifischen Endpunkts, der die Internetroutingeinstellungen verwendet, den `--publish-internet-endpoints`- Parameter auf `true` fest.

   Im folgenden Beispiel wird ein routenspezifischer Endpunkt erstellt, der die Routingeinstellungen für das Microsoft-Netzwerk verwendet.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Ersetzen Sie den Werte des Platzhalters `<storage-account-name>` durch den Namen des Speicherkontos.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Finden des Endpunktnamens für einen routenspezifischen Endpunkt

Wenn Sie einen routenspezifischen Endpunkt konfiguriert haben, finden Sie den Endpunkt in den Eigenschaften Ihres Speicherkontos.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.

    > [!div class="mx-imgBorder"]
    > ![Menüoption „Eigenschaften“](./media/configure-network-routing-preference/properties.png)

2. Der **Microsoft-Netzwerkrouting**-Endpunkt wird für jeden Dienst angezeigt, der Routingpräferenzen unterstützt. Diese Abbildung zeigt den Endpunkt für die Blob- und Dateidienste.

    > [!div class="mx-imgBorder"]
    > ![Option „Microsoft-Netzwerkrouting“ für routenspezifische Endpunkte](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Um die Endpunkte zur Konsole zu drucken, verwenden Sie die `PrimaryEndpoints`-Eigenschaft des Speicherkontoobjekts.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Ersetzen Sie den Wert des Platzhalters `<resource-group-name>` durch den Namen der Ressourcengruppe, die das Speicherkonto enthält.

   Ersetzen Sie den Werte des Platzhalters `<storage-account-name>` durch den Namen des Speicherkontos.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Um die Endpunkte zur Konsole zu drucken, verwenden Sie die Eigenschaft [az storage account show](/cli/azure/storage/account#az_storage_account_show) des Speicherkontoobjekts.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Ersetzen Sie den Wert des Platzhalters `<resource-group-name>` durch den Namen der Ressourcengruppe, die das Speicherkonto enthält.

   Ersetzen Sie den Werte des Platzhalters `<storage-account-name>` durch den Namen des Speicherkontos.

---

## <a name="see-also"></a>Weitere Informationen

- [Netzwerkroutingpräferenz](network-routing-preference.md)
- [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](storage-network-security.md)
- [Sicherheitsempfehlungen für Blob Storage](../blobs/security-recommendations.md)
