---
title: Verhindern von Datenverlusten
description: Mit den Funktionen zur Verhinderung von Datenverlust von Cognitive Services können Kunden die Liste der ausgehenden URLs konfigurieren, auf die ihre Cognitive Services-Ressourcen zugreifen dürfen. Diese Konfiguration schafft eine weitere Kontrollebene für Kunden, um Datenverluste zu verhindern.
author: gclarkmt
ms.author: gregc
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: template-concept
ms.openlocfilehash: c9a5ac391607249393d362f0e0b5b007989ea138
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2021
ms.locfileid: "113601380"
---
# <a name="configure-data-loss-prevention-for-azure-cognitive-services"></a>Konfigurieren der Verhinderung von Datenverlust für Azure Cognitive Services

Mit den Funktionen zur Verhinderung von Datenverlust von Cognitive Services können Kunden die Liste der ausgehenden URLs konfigurieren, auf die ihre Cognitive Services-Ressourcen zugreifen dürfen. So wird eine weitere Kontrollebene für Kunden geschaffen, um Datenverluste zu verhindern. In diesem Artikel werden die erforderlichen Schritte beschrieben, um die Funktion zum Schutz vor Datenverlust für Cognitive Services-Ressourcen zu aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie eine Anforderung übermitteln können, benötigen Sie ein Azure-Konto und ein Azure Cognitive Services-Abonnement. Wenn Sie bereits über ein Konto verfügen, können Sie mit dem nächsten Abschnitt fortfahren. Wenn Sie noch kein Konto haben, sind Sie mit der folgenden Anleitung in wenigen Minuten startbereit: [Erstellen eines Cognitive Services-Kontos für Azure](cognitive-services-apis-create-account.md).

Sie können Ihren Abonnementschlüssel über das [Azure-Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) abrufen, nachdem Sie [Ihr Konto erstellt haben](https://azure.microsoft.com/free/cognitive-services/).

## <a name="enabling-data-loss-prevention"></a>Aktivieren der Verhinderung von Datenverlusten

Die Aktivierung der Verhinderung von Datenverlusten umfasst zwei Teile. Zuerst muss die Eigenschaft „restrictOutboundNetworkAccess“ auf „true“ festgelegt werden. Anschließend müssen Sie auch die Liste der genehmigten URLs angeben. Die Liste der URLs wird der Eigenschaft „allowedFqdnList“ hinzugefügt. Die Eigenschaft „allowedFqdnList“ enthält ein Array von durch Kommas getrennten URLs.

>[!NOTE]
>
> * Der `allowedFqdnList`-Eigenschaftswert unterstützt maximal 1.000 URLs.
> * Die Eigenschaft unterstützt sowohl IP-Adressen als auch vollqualifizierte Domänennamen, d. h. `www.microsoft.com`, Werte.
> * Es kann bis zu 15 Minuten dauern, bis die aktualisierte Liste wirksam wird. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Installieren Sie die [Azure-CLI](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli), oder wählen Sie **Testen** aus.

1. Zeigen Sie die Details der Cognitive Services-Ressource an.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
    ```

1. Zeigen Sie die aktuellen Eigenschaften der Cognitive Services-Ressource an.

    ```azurecli-interactive
    az rest -m get \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
    ```

1. Konfigurieren Sie die Eigenschaft „restrictOutboundNetworkAccess“, und aktualisieren Sie das zulässige FqdnList-Element mit den genehmigten URLs.

    ```azurecli-interactive
    az rest -m patch \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
        -b '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
    ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps), oder wählen Sie **Testen** aus.

1. Zeigen Sie die aktuellen Eigenschaften für die Cognitive Services-Ressource an.

    ```azurepowershell-interactive
    $getParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Method = 'GET'
    }
    Invoke-AzRestMethod @getParams
    ```

1. Konfigurieren Sie die Eigenschaft „restrictOutboundNetworkAccess“, und aktualisieren Sie das zulässige FqdnList-Element mit den genehmigten URLs.

    ```azurepowershell-interactive
    $patchParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Payload = '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
        Method = 'PATCH'
    }
    Invoke-AzRestMethod @patchParams
    ```

---

## <a name="supported-services"></a>Unterstützte Dienste

Die folgenden Dienste unterstützen die Konfiguration zur Verhinderung von Datenverlust:

- Maschinelles Sehen
- Content Moderator
- Custom Vision
- Gesicht
- Formularerkennung
- Spracherkennungsdienst
- QnA Maker

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren virtueller Netzwerke](cognitive-services-virtual-networks.md)
