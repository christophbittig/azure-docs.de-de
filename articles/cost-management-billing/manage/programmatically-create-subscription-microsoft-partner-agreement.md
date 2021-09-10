---
title: Programmgesteuertes Erstellen von Azure-Abonnements für eine Microsoft Partner-Vereinbarung mit den neuesten APIs
description: Hier erfahren Sie, wie Sie mithilfe der neuesten Versionen der REST-API, der Azure CLI, von Azure PowerShell und der Azure Resource Manager-Vorlagen programmgesteuert Azure-Abonnements für eine Microsoft Partner-Vereinbarung erstellen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 06/22/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 64c2601199db5ca64ab22d49dff4435ecf2fcb44
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539959"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>Programmgesteuertes Erstellen von Azure-Abonnements für eine Microsoft Partner-Vereinbarung mit den neuesten APIs

In diesem Artikel wird erläutert, wie Sie mithilfe der aktuellen API-Versionen programmgesteuert Azure-Abonnements für eine Microsoft Partner-Vereinbarung erstellen. Wenn Sie noch die ältere Vorschauversion verwenden, finden Sie weitere Informationen unter [Programmgesteuertes Erstellen von Azure-Abonnements mit Legacy-APIs](programmatically-create-subscription-preview.md). 

Dieser Artikel enthält Informationen zum programmgesteuerten Erstellen von Abonnements mithilfe von Azure Resource Manager.

Wenn Sie ein Azure-Abonnement programmgesteuert erstellen, unterliegt es der Vereinbarung, auf deren Grundlage Sie Azure-Dienste von Microsoft oder einem autorisierten Handelspartner erhalten haben. Weitere Informationen finden Sie unter [Rechtliche Hinweise zu Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen im Cloudlösungsanbieter-Konto Ihrer Organisation über die Rolle „Globaler Administrator“ oder „Administrator-Agent“ verfügen, um ein Abonnement für Ihr Abrechnungskonto erstellen zu können. Weitere Informationen finden Sie unter [Zuweisen von Rollen und Berechtigungen zu Benutzern](/partner-center/permissions-overview).

Wenn Sie nicht wissen, ob Sie Zugriff auf ein Konto für eine Microsoft Partner-Vereinbarung haben, finden Sie weitere Informationen unter [Überprüfen des Zugriffs auf eine Microsoft-Partnervereinbarung](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement).

In den folgenden Beispielen werden REST-APIs verwendet. PowerShell und Azure CLI werden derzeit nicht unterstützt.

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>Suchen nach Abrechnungskonten, auf die Sie Zugriff haben

Verwenden Sie die folgende Anforderung, um alle Abrechnungskonten aufzulisten, auf die Sie Zugriff haben:

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

In der API-Antwort sind die Abrechnungskonten aufgeführt.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Verwenden Sie die `displayName`-Eigenschaft, um das Abrechnungskonto zu identifizieren, für das Sie Abonnements erstellen möchten. Vergewissern Sie sich, dass als „agreementType“ des Kontos die Option *MicrosoftPartnerAgreement* festgelegt ist. Kopieren Sie den Namen (`name`) für das Konto. Wenn Sie beispielsweise ein Abonnement für das Abrechnungskonto `Contoso` erstellen möchten, kopieren Sie `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Halten Sie den Wert für den nächsten Schritt bereit.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rufen Sie diesen Wert mithilfe der Azure CLI oder der REST-API ab.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az billing account list
```
Sie erhalten eine Liste aller Abrechnungskonten, auf die Sie Zugriff haben.

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Partner",
    "agreementType": "MicrosoftPartnerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

Verwenden Sie die displayName-Eigenschaft, um das Abrechnungskonto zu identifizieren, für das Sie Abonnements erstellen möchten. Vergewissern Sie sich, dass als „agreementType“ des Kontos die Option MicrosoftPartnerAgreement festgelegt ist. Kopieren Sie den Namen für das Konto. Wenn Sie z. B. ein Abonnement für das Abrechnungskonto „Contoso“ erstellen möchten, kopieren Sie „99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx“. Halten Sie den Wert für den nächsten Schritt bereit.

---

## <a name="find-customers-that-have-azure-plans"></a>Suchen nach Kunden mit Azure-Plänen

Senden Sie die folgende Anforderung mit dem im ersten Schritt (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) kopierten `name`-Element, um alle Kunden unter dem Abrechnungskonto aufzulisten, für die Sie Azure-Abonnements erstellen können.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

In der API-Antwort sind die Kunden des Abrechnungskontos aufgelistet, die über Azure-Pläne verfügen. Sie können Abonnements für diese Kunden erstellen.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

Verwenden Sie die `displayName`-Eigenschaft, um den Kunden zu identifizieren, für den Sie Abonnements erstellen möchten. Kopieren Sie die `id` für den Kunden. Wenn Sie beispielsweise ein Abonnement für `Fabrikam toys` erstellen möchten, kopieren Sie `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Halten Sie diesen Wert für spätere Schritte bereit.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rufen Sie diesen Wert mithilfe der Azure CLI oder der REST-API ab.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az billing customer list --account-name 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

In der API-Antwort sind die Kunden des Abrechnungskontos aufgelistet, die über Azure-Pläne verfügen. Sie können Abonnements für diese Kunden erstellen.

```json
[
  {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Fabrikam toys",
    "id": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  },
  {
    "billingProfileDisplayName": "Contoso toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Contoso toys",
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "d49c364c-f866-4cc2-a284-d89f369b7951",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  }
]

```

Verwenden Sie die `displayName`-Eigenschaft, um den Kunden zu identifizieren, für den Sie Abonnements erstellen möchten. Kopieren Sie die `id` für den Kunden. Wenn Sie beispielsweise ein Abonnement für `Fabrikam toys` erstellen möchten, kopieren Sie `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Halten Sie diesen Wert für spätere Schritte bereit.

---

## <a name="get-the-resellers-for-a-customer"></a>Abrufen der Handelspartner für einen Kunden

Dieser Abschnitt ist nur für indirekte Anbieter optional.

Falls Sie im Rahmen des zweistufigen Cloudlösungsanbieter-Modells als indirekter Anbieter fungieren, können Sie einen Handelspartner angeben, während Sie Abonnements für Kunden erstellen.

### <a name="rest"></a>[REST](#tab/rest)

Senden Sie die folgende Anforderung mit dem im zweiten Schritt (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kopierten `id`-Element, um alle Handelspartner aufzulisten, die für einen Kunden verfügbar sind.

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
In der API-Antwort sind die Handelspartner für den Kunden aufgeführt:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

Verwenden Sie die `description`-Eigenschaft, um den Handelspartner zu identifizieren, der dem Abonnement zugeordnet ist. Kopieren Sie die `resellerId` für den Handelspartner. Kopieren Sie `3xxxxx`, um beispielsweise `Wingtip` zuzuordnen. Halten Sie den Wert für den nächsten Schritt bereit.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rufen Sie diesen Wert mithilfe der Azure CLI oder der REST-API ab.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie die folgende Anforderung mit dem aus dem ersten Schritt kopierten `name` (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) und dem aus dem vorherigen Schritt kopierten `name` des Kunden (```acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) aus.

```azurecli
 az billing customer show --expand "enabledAzurePlans,resellers" --account-name "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --name "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

In der API-Antwort sind die Handelspartner für den Kunden aufgeführt:

```json
{
  "billingProfileDisplayName": "Fabrikam toys Billing Profile",
  "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
  "displayName": "Fabrikam toys",
  "enabledAzurePlans": [
    {
      "skuDescription": "Microsoft Azure Plan",
      "skuId": "0001"
    }
  ],
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resellers": [
    {
      "description": "Wingtip",
      "resellerId": "3xxxxx"
    }
  ],
  "type": "Microsoft.Billing/billingAccounts/customers"
}

```

Verwenden Sie die `description`-Eigenschaft, um den Handelspartner zu identifizieren, der dem Abonnement zugeordnet ist. Kopieren Sie die `resellerId` für den Handelspartner. Kopieren Sie `3xxxxx`, um beispielsweise `Wingtip` zuzuordnen. Halten Sie den Wert für den nächsten Schritt bereit.

---

## <a name="create-a-subscription-for-a-customer"></a>Erstellen eines Abonnements für einen Kunden

Im folgenden Beispiel wird ein Abonnement mit dem Namen *Dev Team subscription* für *Fabrikam toys* erstellt, und anschließend wird dem Abonnement der Handelspartner *Wingtip* zugeordnet. Sie verwenden den kopierten Abrechnungsbereich aus dem vorherigen Schritt: `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Anforderungstext

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>Antwort

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

Sie können eine GET-Anforderung für die gleiche URL ausführen, um den Status der Anforderung zu erhalten.

### <a name="request"></a>Anforderung

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Antwort

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Der Status „In Bearbeitung“ wird unter `provisioningState` als Status `Accepted` zurückgegeben. 

Übergeben Sie das optionale Element *resellerId*, das Sie im zweiten Schritt in den Anforderungstext der API kopiert haben.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Führen Sie `Install-Module Az.Subscription` aus, um die neueste Version des Moduls zu installieren, das das Cmdlet `New-AzSubscriptionAlias` enthält. Eine aktuelle Version von PowerShellGet finden Sie unter [Abrufen des PowerShellGet-Moduls](/powershell/scripting/gallery/installing-psget).

Führen Sie den folgenden Befehl [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) aus, und verwenden Sie dabei den Abrechnungsbereich `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`. 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

Sie erhalten die Abonnement-ID (subscriptionId) als Teil der Befehlsantwort.

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

Übergeben Sie das optionale Element *resellerId*, das Sie im zweiten Schritt in den Aufruf von `New-AzSubscriptionAlias` kopiert haben.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Installieren Sie zunächst die Erweiterung, indem Sie `az extension add --name account` und `az extension add --name alias` ausführen.

Führen Sie den folgenden Befehl [az account alias create](/cli/azure/account/alias#az_account_alias_create) aus. 

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
```

Sie erhalten die Abonnement-ID (subscriptionId) als Teil der Befehlsantwort.

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

Übergeben Sie das optionale Element *resellerId*, das Sie im zweiten Schritt in den Aufruf von `az account alias create` kopiert haben.

---

## <a name="use-arm-template"></a>Verwenden einer ARM-Vorlage

Im vorherigen Abschnitt wurde gezeigt, wie Sie ein Abonnement mit PowerShell, der CLI oder der REST-API erstellen. Wenn Sie das Erstellen von Abonnements automatisieren müssen, verwenden Sie ggf. eine Azure Resource Manager-Vorlage (ARM-Vorlage).

Mit der folgenden Vorlage wird ein Abonnement erstellt. Geben Sie für `billingScope` die Kunden-ID an. Das Abonnement wird in der Stammverwaltungsgruppe erstellt. Nachdem Sie das Abonnement erstellt haben, können Sie es in eine andere Verwaltungsgruppe verschieben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]"
            }
        }
    ],
    "outputs": {}
}
```

Stellen Sie die Vorlage auf [Verwaltungsgruppenebene](../../azure-resource-manager/templates/deploy-to-management-group.md) bereit.

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

Mit dem Anforderungstext:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```

---

Verwenden Sie die folgende Vorlage, um ein Abonnement in eine neue Verwaltungsgruppe zu verschieben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/",
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

## <a name="next-steps"></a>Nächste Schritte

* Da Sie nun ein Abonnement erstellt haben, können Sie diese Möglichkeit auch für andere Benutzer und Dienstprinzipale eröffnen. Weitere Informationen finden Sie unter [Gewähren des Zugriffs zum Erstellen von Azure Enterprise-Abonnements (Vorschau) ](grant-access-to-create-subscription.md).
* Weitere Informationen zum Verwalten einer großen Anzahl von Abonnements mithilfe von Verwaltungsgruppen finden Sie unter [Was sind Azure-Verwaltungsgruppen?](../../governance/management-groups/overview.md).
