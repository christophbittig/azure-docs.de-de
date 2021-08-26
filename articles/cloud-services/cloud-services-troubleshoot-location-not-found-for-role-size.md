---
title: Problembehandlung von LocationNotFoundForRoleSize beim Bereitstellen eines Clouddiensts (klassisch) in Azure | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie eine LocationNotFoundForRoleSize-Ausnahme bei der Bereitstellung eines Clouddiensts (klassisch) in Azure auflösen.
services: cloud-services
author: hirenshah1
ms.author: hirshah
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f45e28e2c7a816f923d1af579b818e546c992717
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821491"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Problembehandlung von LocationNotFoundForRoleSize beim Bereitstellen eines Clouddiensts (klassisch) in Azure

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

In diesem Artikel behandeln Sie Zuordnungsfehler, bei denen die Größe eines virtuellen Computers (VM) nicht verfügbar ist, wenn Sie einen Azure-Clouddienst (klassisch) bereitstellen.

Wenn Sie Instanzen in einem Clouddienst (klassisch) bereitstellen oder neue Web- oder Workerrolleninstanzen hinzufügen, weist Microsoft Azure Computeressourcen zu.

Unter Umständen erhalten Sie bei diesen Vorgängen auch dann gelegentlich Fehlermeldungen, wenn Sie die Grenzwerte des Azure-Abonnements noch nicht erreicht haben.

> [!TIP]
> Diese Informationen können auch hilfreich sein, wenn Sie die Bereitstellung Ihrer Dienste planen.

## <a name="symptom"></a>Symptom

Navigieren Sie im Azure-Portal zu Ihrem Clouddienst (klassisch), und klicken Sie in der Randleiste auf *Vorgangsprotokolle (klassisch)* , um die Protokolle anzuzeigen.

![Das Bild zeigt das Blatt „Vorgangsprotokolle (klassisch)“.](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Wenn Sie die Protokolle Ihres Clouddiensts (klassisch) überprüfen, wird die folgende Ausnahme angezeigt:

|Ausnahmetyp  |Fehlermeldung  |
|---------|---------|
|LocationNotFoundForRoleSize     |Fehler beim Vorgang „`{Operation ID}`“: „Der angeforderte VM-Tarif ist in „`{Region ID}`“ für dieses Abonnement zurzeit nicht verfügbar. Verwenden Sie einen anderen Tarif, oder führen Sie die Bereitstellung an einem anderen Standort durch.“|

## <a name="cause"></a>Ursache

Bei der Region oder dem Cluster, wo Sie die Bereitstellung durchführen, liegt ein Kapazitätsproblem vor. Die Ausnahme *LocationNotFoundForRoleSize* tritt auf, wenn die von Ihnen ausgewählte Ressourcen-SKU (VM-Größe) für die angegebene Region nicht verfügbar ist.

## <a name="solution"></a>Lösung

In diesem Szenario sollten Sie eine andere Region oder SKU für die Bereitstellung des Clouddiensts (klassisch) auswählen. Vor dem Bereitstellen oder Aktualisieren Ihres Clouddiensts (klassisch) können Sie ermitteln, welche SKUs in einer Region oder Verfügbarkeitszone verfügbar sind. Befolgen Sie die folgenden [Azure CLI](#list-skus-in-region-using-azure-cli)-, [PowerShell](#list-skus-in-region-using-powershell)- oder [REST-API](#list-skus-in-region-using-rest-api)-Prozesse.

### <a name="list-skus-in-region-using-azure-cli"></a>Auflisten von SKUs in der Region mithilfe von Azure CLI

Sie können den Befehl [az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) verwenden.

- Filtern Sie die Ausgabe mit dem Parameter `--location` auf den verwendeten Standort.
- Verwenden Sie den Parameter `--size`, um nach dem Teil eines Namens für die Größe zu suchen.
- Weitere Informationen finden Sie unter [Lösung 2: Azure CLI](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli).

    **Beispiel:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Beispielergebnisse:** ![Azure CLI-Ausgabe der Ausführung des Befehls „az vm list-skus --location southcentralus --size Standard_F --output table“, der die verfügbaren SKUs anzeigt.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Auflisten von SKUs in der Region mithilfe von PowerShell

Sie können den Befehl [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) verwenden.

- Filtern Sie die Ergebnisse nach Standort.
- Für diesen Befehl benötigen Sie die aktuelle Version von PowerShell.
- Weitere Informationen finden Sie unter [Lösung 1: PowerShell](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell).

**Beispiel:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Einige weitere nützliche Befehle:**

Filtern Sie die Standorte, die die Größe (Standard_DS14_v2) enthalten:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtern Sie alle Standorte, die die Größe (V3) enthalten:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Auflisten von SKUs in der Region mithilfe von REST-API

Verwenden Sie den Vorgang [Auflisten von Ressourcen-SKUs](/rest/api/compute/resourceskus/list). Verfügbare SKUs und Regionen werden im folgenden Format zurückgegeben:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Zuordnungsfehlerlösungen und Informationen zum besseren Verständnis ihrer Generierung finden Sie hier:

> [!div class="nextstepaction"]
> [Zuteilungsfehler – Clouddienste (klassisch)](cloud-services-allocation-failures.md)

Suchen Sie in den Azure-Foren bei [MSDN und Stack Overflow](https://azure.microsoft.com/support/forums/), falls Sie Ihr Azure-Problem mit diesem Artikel nicht beheben konnten. Sie können Ihr Problem in diesen Foren veröffentlichen oder [auf Twitter an @AzureSupport senden](https://twitter.com/AzureSupport). Sie können auch eine Azure-Supportanfrage senden. Wenn Sie eine Supportanfrage senden möchten, wählen Sie auf der [Azure-Support-Seite](https://azure.microsoft.com/support/options/) die Option *Support erhalten* aus.
