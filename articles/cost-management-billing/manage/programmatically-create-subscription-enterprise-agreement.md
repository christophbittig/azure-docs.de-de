---
title: Programmgesteuertes Erstellen von Azure Enterprise Agreement-Abonnements mit den neuesten APIs
description: Hier erfahren Sie, wie Sie mithilfe der neuesten Versionen der REST-API, der Azure CLI, von Azure PowerShell und der Azure Resource Manager-Vorlagen programmgesteuert Azure Enterprise Agreement-Abonnements erstellen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/01/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 45bc6066152fbb83f5124b5ee157e6b16efa9bf9
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475168"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Programmgesteuertes Erstellen von Azure Enterprise Agreement-Abonnements mit den neuesten APIs

In diesem Artikel wird erläutert, wie Sie mithilfe der aktuellen API-Versionen programmgesteuert Azure Enterprise Agreement-Abonnements für ein EA-Abrechnungskonto erstellen. Wenn Sie noch die ältere Vorschauversion verwenden, finden Sie weitere Informationen unter [Programmgesteuertes Erstellen von Azure-Abonnements mit Legacy-APIs](programmatically-create-subscription-preview.md). 

Dieser Artikel enthält Informationen zum programmgesteuerten Erstellen von Abonnements mithilfe von Azure Resource Manager.

Wenn Sie ein Azure-Abonnement programmgesteuert erstellen, unterliegt es der Vereinbarung, auf deren Grundlage Sie Azure-Dienste von Microsoft oder einem autorisierten Handelspartner erhalten haben. Weitere Informationen finden Sie unter [Rechtliche Hinweise zu Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Benutzer muss über die Rolle „Besitzer“ für ein Registrierungskonto verfügen, um ein Abonnement zu erstellen. Es gibt zwei Möglichkeiten, wie Sie die Rolle erhalten:

* Der Enterprise-Administrator Ihrer Registrierung kann [Sie zum Kontobesitzer machen](https://ea.azure.com/helpdocs/addNewAccount) (Anmeldung erforderlich), damit Sie zum Besitzer des Registrierungskontos werden.
* Ein vorhandener Besitzer des Registrierungskontos kann [Ihnen Zugriff gewähren](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). 

Wenn ein Dienstprinzipal (SPN) zum Erstellen eines EA-Abonnements verwendet werden soll, muss ein Besitzer des Registrierungskontos [diesem Dienstprinzipal die Berechtigung zum Erstellen von Abonnements erteilen](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Wenn Abonnements mithilfe eines SPN erstellt werden, verwenden Sie die Objekt-ID (ObjectId) der Azure AD-Anwendungsregistrierung als Dienstprinzipalobjekt-ID mit [Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0&preserve-view=true ) oder der [Azure CLI](/cli/azure/ad/sp?view=azure-cli-latest&preserve-view=true#az_ad_sp_list). Weitere Informationen zur API-Anforderung für die EA-Rollenzuweisung finden Sie unter [Zuweisen von Rollen zu Azure Enterprise Agreement-Dienstprinzipalnamen](assign-roles-azure-service-principals.md). Der Artikel enthält eine Liste der Rollen (und Rollendefinitions-IDs), die einem SPN zugewiesen werden können.

  > [!NOTE]
  > - Stellen Sie sicher, dass Sie die richtige API-Version verwenden, um dem Registrierungskonto Besitzerberechtigungen zu erteilen. Verwenden Sie für diesen Artikel und die darin dokumentierten APIs die API [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). 
  > - Wenn Sie zur Verwendung der neueren APIs eine Migration durchführen, wird Ihre vorherige Konfiguration, die mit der [Version 2015-07-01](grant-access-to-create-subscription.md) erstellt wurde, nicht automatisch für die Verwendung mit den neueren APIs konvertiert.

## <a name="find-accounts-you-have-access-to"></a>Ermitteln der Konten, auf die Sie Zugriff besitzen

Nachdem Sie einem Registrierungskonto hinzugefügt wurden, das einem Kontobesitzer zugeordnet ist, verwendet Azure die Beziehung zwischen dem Konto und der Registrierung für die Ermittlung, wo die Abonnementgebühren angerechnet werden sollen. Alle Abonnements, die unter dem Konto erstellt werden, werden über die EA-Registrierung abgerechnet, in der sich das Konto befindet. Um Abonnements zu erstellen, müssen Sie Werte zum Registrierungskonto und den Benutzerprinzipalen als Besitzer des Abonnements übergeben.

Um die folgenden Befehle ausführen zu können, müssen Sie im *Basisverzeichnis* des Kontobesitzers angemeldet sein. Dies ist das Verzeichnis, in dem Abonnements standardmäßig erstellt werden.

### <a name="rest"></a>[REST](#tab/rest)

Fordern Sie eine Liste aller Registrierungskonten an, auf die Sie zugreifen können:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

In der API-Antwort sind alle Registrierungskonten aufgelistet, auf die Sie Zugriff haben:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

Die Werte für einen Abrechnungsbereich und `id` sind identisch. Die ID (`id`) für Ihr Registrierungskonto ist der Abrechnungsbereich, unter dem die Abonnementanforderung initiiert wird. Es ist wichtig, die ID zu kennen, da sie ein erforderlicher Parameter ist, den Sie später im Artikel zum Erstellen eines Abonnements verwenden.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rufen Sie diesen Wert mithilfe der Azure CLI oder der REST-API ab.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Fordern Sie eine Liste aller Registrierungskonten an, auf die Sie zugreifen können:

```azurecli-interactive
> az billing account list
```

In der Antwort sind alle Registrierungskonten aufgelistet, auf die Sie Zugriff haben.

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

Die Werte für einen Abrechnungsbereich und `id` sind identisch. Die ID (`id`) für Ihr Registrierungskonto ist der Abrechnungsbereich, unter dem die Abonnementanforderung initiiert wird. Es ist wichtig, die ID zu kennen, da sie ein erforderlicher Parameter ist, den Sie später im Artikel zum Erstellen eines Abonnements verwenden.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Erstellen von Abonnements unter einem bestimmten Registrierungskonto

Im folgenden Beispiel wird ein Abonnement namens *Dev Team Subscription* in Registrierungskonto erstellt, das im vorherigen Schritt ausgewählt wurde. 

Mit einer der folgenden Methoden erstellen Sie einen Abonnementaliasnamen. Es wird empfohlen, beim Erstellen des Aliasnamens Folgendes zu beachten:

- Verwenden Sie alphanumerische Zeichen und Bindestriche.
- Der Name sollte mit einem Buchstaben beginnen und mit einem alphanumerischen Zeichen enden.
- Verwenden Sie keine Punkte.


### <a name="rest"></a>[REST](#tab/rest)

Rufen Sie die PUT-API auf, um eine Anforderung bzw. einen Alias für die Abonnementerstellung zu generieren.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

Geben Sie im Anforderungstext die ID (`id`) eines Ihrer Registrierungskonten (`enrollmentAccounts`) als `billingScope` an.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Zulässige Werte für `Workload` sind `Production` und `DevTest`.

#### <a name="response"></a>Antwort

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Führen Sie `Install-Module Az.Subscription` aus, um die neueste Version des Moduls zu installieren, das das Cmdlet `New-AzSubscriptionAlias` enthält. Eine aktuelle Version von PowerShellGet finden Sie unter [Abrufen des PowerShellGet-Moduls](/powershell/scripting/gallery/installing-psget).

Führen Sie den folgenden Befehl [New-AzSubscriptionAlias](/powershell/module/az.subscription/get-azsubscriptionalias) aus, und verwenden Sie dabei den Abrechnungsbereich `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

Sie erhalten die Abonnement-ID (subscriptionId) als Teil der Befehlsantwort.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Installieren Sie zunächst die Erweiterung, indem Sie `az extension add --name account` und `az extension add --name alias` ausführen.

Führen Sie den folgenden Befehl [az account alias create](/cli/azure/account/alias#az_account_alias_create) aus, und geben Sie `billing-scope` und `id` von einem Ihrer Registrierungskonten (`enrollmentAccounts`) an. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

Sie erhalten die Abonnement-ID (subscriptionId) als Teil der Befehlsantwort.

```azurecli
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

---

## <a name="use-arm-template-or-bicep"></a>Verwenden von ARM-Vorlagen oder Bicep

Im vorherigen Abschnitt wurde gezeigt, wie Sie ein Abonnement mit PowerShell, der CLI oder der REST-API erstellen. Wenn Sie das Erstellen von Abonnements automatisieren müssen, verwenden Sie ggf. eine Azure Resource Manager-Vorlage (ARM-Vorlage) oder eine [Bicep-Datei](../../azure-resource-manager/bicep/overview.md).

Mit der folgenden ARM-Vorlage wird ein Abonnement erstellt. Geben Sie für `billingScope` die ID des Registrierungskontos an. Das Abonnement wird in der Stammverwaltungsgruppe erstellt. Nachdem Sie das Abonnement erstellt haben, können Sie es in eine andere Verwaltungsgruppe verschieben.

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

Alternativ können Sie eine Bicep-Datei verwenden, um das Abonnement zu erstellen.

```bicep
targetScope = 'managementGroup'

@description('Provide a name for the alias. This name will also be the display name of the subscription.')
param subscriptionAliasName string

@description('Provide the full resource ID of billing scope to use for subscription creation.')
param billingScope string

resource subscriptionAlias 'Microsoft.Subscription/aliases@2020-09-01' = {
  scope: tenant()
  name: subscriptionAliasName
  properties: {
    workload: 'Production'
    displayName: subscriptionAliasName
    billingScope: billingScope
  }
}
```

Stellen Sie die Vorlage auf [Verwaltungsgruppenebene](../../azure-resource-manager/templates/deploy-to-management-group.md) bereit. Die folgenden Beispiele zeigen die Bereitstellung der JSON-ARM-Vorlage, aber Sie können stattdessen auch eine Bicep-Datei bereitstellen.

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
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321'
```

---

Verwenden Sie die folgende ARM-Vorlage, um ein Abonnement in eine neue Verwaltungsgruppe zu verschieben.

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

Verwenden Sie alternativ die folgende Bicep-Datei.

```bicep
targetScope = 'managementGroup'

@description('Provide the ID of the management group that you want to move the subscription to.')
param targetMgId string

@description('Provide the ID of the existing subscription to move.')
param subscriptionId string

resource subToMG 'Microsoft.Management/managementGroups/subscriptions@2020-05-01' = {
  scope: tenant()
  name: '${targetMgId}/${subscriptionId}'
}
```

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Einschränkungen der Azure Enterprise-Abonnementerstellungs-API

- Nur Azure Enterprise-Abonnements werden mithilfe der API erstellt.
- Pro Registrierungskonto sind maximal 2.000 Abonnements zulässig. Im Anschluss können weitere Abonnements für das Konto nur über das Azure-Portal erstellt werden. Wenn Sie über die API weitere Abonnements erstellen möchten, erstellen Sie ein weiteres Registrierungskonto. Abgebrochene, gelöschte und übertragene Abonnements werden auf den Grenzwert von 2.000 angerechnet.
- Benutzer, die keine Kontobesitzer sind, aber per Azure RBAC einem Registrierungskonto hinzugefügt wurden, können über das Azure-Portal keine Abonnements erstellen.
- Sie können den Mandanten nicht auswählen, in dem das Abonnement erstellt werden soll. Das Abonnement wird immer im Basismandanten des Kontobesitzers erstellt. Wenn Sie das Abonnement in einen anderen Mandanten verschieben möchten, finden Sie weitere Informationen unter [Ändern des Abonnementmandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Nächste Schritte

* Da Sie nun ein Abonnement erstellt haben, können Sie diese Möglichkeit auch für andere Benutzer und Dienstprinzipale eröffnen. Weitere Informationen finden Sie unter [Gewähren des Zugriffs zum Erstellen von Azure Enterprise-Abonnements (Vorschau) ](grant-access-to-create-subscription.md).
* Weitere Informationen zum Verwalten einer großen Anzahl von Abonnements mithilfe von Verwaltungsgruppen finden Sie unter [Was sind Azure-Verwaltungsgruppen?](../../governance/management-groups/overview.md).
* Informationen zum Ändern der Verwaltungsgruppe für ein Abonnement finden Sie unter [Verschieben von Abonnements](../../governance/management-groups/manage.md#move-subscriptions).
