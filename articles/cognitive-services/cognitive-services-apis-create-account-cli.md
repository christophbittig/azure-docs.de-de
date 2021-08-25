---
title: Erstellen einer Cognitive Services-Ressource mithilfe der Azure-Befehlszeilenschnittstelle
titleSuffix: Azure Cognitive Services
description: Beginnen Sie mit Azure Cognitive Services, indem Sie mithilfe der Azure-Befehlszeilenschnittstelle eine Ressource erstellen und diese abonnieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: Cognitive Services, kognitive Intelligenz, kognitive Lösungen, KI-Dienste
ms.topic: quickstart
ms.date: 06/04/2021
ms.author: aahi
ms.openlocfilehash: 1c838a9b805eed20fec9f9feabf4aa24c58b1cd5
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429935"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Schnellstart: Erstellen einer Cognitive Services-Ressource mithilfe der Azure-Befehlszeilenschnittstelle (CLI)

Verwenden Sie diese Schnellstartanleitung für die Verwendung von Azure Cognitive Services mithilfe der [Azure-Befehlszeilenschnittstelle (Azure Command Line Interface, CLI)](/cli/azure/install-azure-cli).

Azure Cognitive Services sind cloudbasierte Dienste mit REST-APIs und Clientbibliothek-SDKs, die Entwickler bei der Erstellung intelligenter Anwendungen unterstützen, ohne dass direkte KI- oder Data Science-Fähigkeiten oder -Kenntnisse erforderlich sind. Mit Cognitive Services können Entwickler ganz einfach kognitive Features in ihre Anwendungen integrieren – mit kognitiven Lösungen, die sehen, hören, sprechen, verstehen und sogar schlussfolgern können.

Cognitive Services werden von [Azure-Ressourcen](../azure-resource-manager/management/manage-resources-portal.md) dargestellt, die Sie in Ihrem Azure-Abonnement erstellen. Verwenden Sie nach dem Erstellen der Ressource die Schlüssel und einen für Sie zum Authentifizieren Ihrer Anwendungen generierten Endpunkt.

In diesem Schnellstart erfahren Sie, wie Sie sich mit der [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/install-azure-cli) für Azure Cognitive Services registrieren und ein Konto mit einem Abonnement mit einem einzelnen Dienst oder mehreren Diensten erstellen. Diese Dienste werden durch Azure-[Ressourcen](../azure-resource-manager/management/manage-resources-portal.md) repräsentiert, die Ihnen ermöglichen, Verbindungen mit einer oder mehreren der Azure Cognitive Services-APIs herzustellen.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein gültiges Azure-Abonnement. Dieses können Sie [kostenlos erstellen](https://azure.microsoft.com/free/cognitive-services).
* Die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/install-azure-cli)
* [!INCLUDE [contributor-requirement](./includes/quickstarts/contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./includes/quickstarts/terms-azure-portal.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Installieren der Azure CLI und Anmelden

Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli). Wenn Sie sich bei Ihrer lokalen Installation der Befehlszeilenschnittstelle anmelden möchten, führen Sie den Befehl [az login](/cli/azure/reference-index#az_login) aus:

```azurecli-interactive
az login
```

Sie können auch die grüne **Ausprobieren**-Schaltfläche verwenden, um diese Befehle in Ihrem Browser auszuführen.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Erstellen einer neuen Azure Cognitive Services-Ressourcengruppe

Vor dem Erstellen einer Cognitive Services-Ressource müssen Sie über eine Azure-Ressourcengruppe verfügen, die die Ressource enthalten soll. Beim Erstellen einer neuen Ressource haben Sie die Möglichkeit, entweder eine neue Ressourcengruppe zu erstellen oder eine bereits vorhandene zu verwenden. In diesem Artikel wird gezeigt, wie Sie eine neue Ressourcengruppe erstellen.

### <a name="choose-your-resource-group-location"></a>Auswählen des Speicherorts Ihrer Ressourcengruppe

Um eine Ressource zu erstellen, benötigen Sie einen der für Ihr Abonnement verfügbaren Azure-Speicherorte. Sie können eine Liste der verfügbaren Speicherorte mit dem Befehl [az account list-locations](/cli/azure/account#az_account_list_locations) abrufen. Auf die meisten Cognitive Services kann von mehreren Speicherorten aus zugegriffen werden. Wählen Sie den Ihnen am nächsten gelegenen aus, oder stellen Sie fest, welche Speicherorte für den Dienst verfügbar sind.

> [!IMPORTANT]
> * Merken Sie sich Ihren Azure-Speicherort, da Sie ihn beim Aufrufen der Azure Cognitive Services benötigen.
> * Die Verfügbarkeit einiger Cognitive Services kann je nach Region variieren. Weitere Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Sobald Ihr Azure-Speicherort feststeht, erstellen Sie eine neue Ressourcengruppe in der Azure-Befehlszeilenschnittstelle mit dem Befehl [az group create](/cli/azure/group#az_group_create).

Ersetzen Sie im folgenden Beispiel den Azure-Speicherort `westus2` durch einen der für Ihr Abonnement verfügbaren Azure-Speicherorte.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Erstellen einer Cognitive Services-Ressource

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Auswählen eines Cognitive Service und eines Tarifs

Wenn Sie eine neue Ressource erstellen, müssen Sie die „Art“ des Diensts kennen, den Sie verwenden möchten, sowie den [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/), den Sie wünschen (oder die SKU). Diese und andere Informationen werden Sie beim Erstellen der Ressource als Parameter verwenden.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]


[!INCLUDE [SKUs and pricing](./includes/quickstarts/sku-pricing.md)]

Sie können eine Liste der verfügbaren „Arten“ von Cognitive Services mit dem Befehl [az cognitiveservices account list-kinds](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds) finden:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Hinzufügen einer neuen Ressource zu Ihrer Ressourcengruppe

Um eine neue Cognitive Services-Ressource zu erstellen und zu abonnieren, verwenden Sie den Befehl [az cognitiveservices account create](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create). Dieser Befehl fügt der zuvor erstellten Ressourcengruppe eine neue abrechenbare Ressource hinzu. Wenn Sie Ihre neue Ressource erstellen, müssen Sie die „Art“ des Diensts kennen, den Sie verwenden möchten, sowie dessen Tarif (oder die SKU) und einen Azure-Speicherort.

Sie können eine (kostenlose) F0-Ressource zur Anomalieerkennung erstellen, die mit dem folgenden Befehl den Namen `anomaly-detector-resource` erhält.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Abrufen der Schlüssel für die Ressource

Wenn Sie sich bei Ihrer lokalen Installation der Befehlszeilenschnittstelle (CLI) anmelden möchten, führen Sie den Befehl [az login](/cli/azure/reference-index#az_login) aus.

```azurecli-interactive
az login
```

Rufen Sie mit dem Befehl [az cognitiveservices account keys list](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) die Schlüssel für Ihre Cognitive Service-Ressource ab.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Tarife und Abrechnung

Die Tarife (und der Betrag, der Ihnen in Rechnung gestellt wird) basieren auf der Anzahl der unter ihren Authentifizierungsinformationen gesendeten Transaktionen. Für jeden Tarif wird Folgendes angegeben:
* Maximale Anzahl zulässiger Transaktionen pro Sekunde (TPS).
* Dienstfeatures, die innerhalb des Tarifs aktiviert sind.
* Die Kosten für eine vordefinierte Anzahl von Transaktionen. Wird diese Anzahl überschritten, fällt eine zusätzliche Gebühr gemäß den [Preisdetails](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) für Ihren Dienst an.

## <a name="get-current-quota-usage-for-your-resource"></a>Abrufen des aktuellen Kontingentverbrauchs für Ihre Ressource

Rufen Sie mit dem Befehl [az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) den Verbrauch für Ihre Cognitive Service-Ressource ab.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Cognitive Services-Ressource bereinigen und entfernen möchten, können Sie entweder diese selbst oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen in der Gruppe enthaltenen Ressourcen gelöscht.

Verwenden Sie den Befehl „az group delete“, um die Ressourcengruppe und die zugeordneten Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

Wenn Sie eine gelöschte Ressource wiederherstellen müssen, finden Sie weitere Informationen unter [Wiederherstellen gelöschter Cognitive Services-Ressourcen](manage-resources.md).

## <a name="see-also"></a>Siehe auch

* Informationen zum sicheren Arbeiten mit Cognitive Services finden Sie unter **[Authentifizieren von Anforderungen an Azure Cognitive Services](authentication.md)** .
* Eine Liste der verschiedenen Kategorien in Cognitive Services finden Sie unter **[Was ist Azure Cognitive Services?](./what-are-cognitive-services.md)** .
* Eine Liste der natürlichen Sprachen, die von Cognitive Services unterstützt werden, finden Sie unter **[Unterstützung für natürliche Sprachen in Azure Cognitive Services](language-support.md)** .
* Informationen zur lokalen Verwendung von Cognitive Services finden Sie unter **[Azure Cognitive Services-Container](cognitive-services-container-support.md)** .
* Informationen zum Schätzen der Kosten für die Verwendung von Cognitive Services finden Sie unter **[Planen und Verwalten von Kosten für Azure Cognitive Services](plan-manage-costs.md)** .
