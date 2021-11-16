---
title: Erstellen von Projekten für die benutzerdefinierte Textklassifizierung
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über die Vorgehensweise bei der Verwendung von Azure-Ressourcen mit benutzerdefinierter Klassifizierung.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, references_regions, ignite-fall-2021
ms.openlocfilehash: 7a9f0692f45f1d97824d4428e6a777e38b372d8a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025929"
---
# <a name="how-to-create-custom-text-classification-projects"></a>Erstellen von Projekten für die benutzerdefinierte Textklassifizierung

In diesem Artikel erfahren Sie, wie Sie diese Anforderungen einrichten und ein Projekt erstellen. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der Verwendung der benutzerdefinierten Textklassifizierung beginnen, benötigen Sie mehrere Komponenten:

* Azure-Abonnement – [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services).
* Eine Azure-Sprachressource 
* Ein Azure-Speicherkonto zum Speichern von Daten für Ihr Projekt
* Sie sollten eine Vorstellung von dem [Projektschema](design-schema.md) haben, das Sie für Ihre Daten verwenden werden.

## <a name="azure-resources"></a>Azure-Ressourcen

Bevor Sie mit der Verwendung der benutzerdefinierten Klassifizierung beginnen, benötigen Sie eine Azure-Sprachressource. Es wird empfohlen, die folgenden Schritte zum Erstellen Ihrer Ressource im Azure-Portal auszuführen. Wenn Sie eine Ressource im Azure-Portal erstellen, können Sie gleichzeitig ein Azure-Speicherkonto erstellen, wobei alle erforderlichen Berechtigungen vorkonfiguriert sind. Sie können in diesem Artikel auch weiterlesen, um zu erfahren, wie Sie eine bereits vorhandene Ressource verwenden und für die Verwendung mit der benutzerdefinierten Textklassifizierung konfigurieren können.

Darüber hinaus benötigen Sie ein Azure-Speicherkonto, in das Sie Ihre `.txt`-Dateien hochladen, die zum Trainieren eines Modells zum Klassifizieren von Text verwendet werden sollen

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

[!INCLUDE [create a new resource from the Azure portal](../includes/resource-creation-azure-portal.md)]

<!-- :::image type="content" source="../../media/azure-portal-resource-credentials.png" alt-text="A screenshot showing the resource creation screen in Language Studio." lightbox="../../media/azure-portal-resource-credentials.png"::: -->

# <a name="language-studio"></a>[Language Studio](#tab/studio)

### <a name="create-a-new-resource-from-language-studio"></a>Erstellen einer neuen Ressource aus Language Studio

Wenn Sie sich zum ersten Mal anmelden, wird in [Language Studio](https://aka.ms/languageStudio) ein Fenster angezeigt, in dem Sie eine Sprachressource auswählen oder eine neue erstellen können. Sie können eine Ressource auch erstellen, indem Sie auf das Einstellungssymbol in der oberen rechten Ecke klicken, **Ressourcen** auswählen und dann auf **Neue Ressource erstellen** klicken.

> [!IMPORTANT]
> * Für die Verwendung der benutzerdefinierten Textklassifizierung benötigen Sie eine Sprachressource in **USA, Westen 2** oder **Europa, Westen** mit dem Tarif „Standard“ (**S**).
> * Achten Sie darauf, dass Sie die **Verwaltete Identität** auswählen, wenn Sie eine Ressource erstellen. 

:::image type="content" source="../../media/create-new-resource-small.png" alt-text="Screenshot: Bildschirm zum Erstellen von Ressourcen in Language Studio." lightbox="../../media/create-new-resource.png":::

Falls Sie noch nicht über ein Konto verfügen, müssen Sie [ein Azure-Speicherkonto erstellen](/azure/storage/common/storage-account-create), um die benutzerdefinierte Klassifizierung zu verwenden. 

Als Nächstes müssen Sie die [richtigen Rollen](#roles-for-your-storage-account) für das Speicherkonto zuweisen, um es mit Ihrer Sprachressource zu verbinden. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

### <a name="create-a-new-resource-with-the-azure-powershell"></a>Erstellen einer neuen Ressource mit Azure PowerShell

Sie können eine neue Ressource und ein Speicherkonto mithilfe der folgenden CLI-[Vorlage](https://github.com/Azure-Samples/cognitive-services-sample-data-files) und [Parameterdateien](https://github.com/Azure-Samples/cognitive-services-sample-data-files) erstellen, die auf GitHub gehostet werden.

Bearbeiten Sie die folgenden Werte in der Parameterdatei:

| Parametername | Wertbeschreibung |
|--|--|
|`name`| Name Ihrer Sprachressource|
|`location`| Region, in der Ihre Ressource gehostet wird. Die benutzerdefinierte Textklassifizierung steht nur in den Regionen **USA, Westen 2** und **Europa, Westen** zur Verfügung.|
|`sku`| Tarif Ihrer Ressource. Benutzerdefinierte Textklassifizierung funktioniert nur mit der Ebene **S**.|
|`storageResourceName`| Name Ihres Speicherkontos|
|`storageLocation`| Region, in der Ihr Speicherkonto gehostet wird.|
|`storageSkuType`| SKU Ihres [Speicherkontos](/rest/api/storagerp/srp_sku_types).|
|`storageResourceGroupName`| Ressourcengruppe Ihres Speicherkontos|
<!-- |builtInRoleType| Legen Sie diese Rolle auf **„Mitwirkender“** fest.| -->

Verwenden Sie den folgenden PowerShell-Befehl, um die ARM-Vorlage (Azure Resource Manager) mit den von Ihnen bearbeiteten Dateien bereitzustellen.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-arm-template> `
  -TemplateParameterFile <path-to-parameters-file>
```

Informationen zum [Bereitstellen von Vorlagen](/azure/azure-resource-manager/templates/deploy-powershell#parameter-files) und zu [Parameterdateien](/azure/azure-resource-manager/templates/parameter-files?tabs=json)finden Sie in der ARM-Vorlagendokumentation.

--- 

## <a name="using-a-pre-existing-azure-resource"></a>Verwenden einer bereits vorhandenen Azure-Ressource

Sie können eine vorhandene Sprachressource verwenden, um mit der benutzerdefinierten Textklassifizierung zu beginnen, solange diese Ressource die folgenden Anforderungen erfüllt:

|Anforderung  |BESCHREIBUNG  |
|---------|---------|
|Regions     | Stellen Sie sicher, dass Ihre vorhandene Ressource in einer der beiden unterstützten Regionen bereitgestellt wird: **USA, Westen 2** oder **Europa, Westen**. Falls nicht, müssen Sie eine neue Ressource in diesen Regionen erstellen.        |
|Tarif     | Stellen Sie sicher, dass Sich Ihre vorhandene Ressource im Standardtarif (**S**) befindet. Nur dieser Tarif wird unterstützt. Wenn Ihre Ressource diesen Tarif nicht verwendet, müssen Sie eine neue Ressource erstellen.        |
|Verwaltete Identität     | Stellen Sie sicher, dass die Einstellung für die ressourcenverwaltete Identität aktiviert ist. Lesen Sie andernfalls den nächsten Abschnitt. |

Falls Sie noch nicht über ein Konto verfügen, müssen Sie [ein Azure-Speicherkonto erstellen](/azure/storage/common/storage-account-create), um die benutzerdefinierte Klassifizierung zu verwenden. 

Als Nächstes müssen Sie die [richtigen Rollen](#roles-for-your-storage-account) für das Speicherkonto zuweisen, um es mit Ihrer Sprachressource zu verbinden. 

## <a name="roles-for-your-azure-language-resource"></a>Rollen für Ihre Azure-Sprachressource

Ihnen sollte die Rolle **Besitzer** oder **Mitwirkender** für Ihre Azure-Sprachressource zugewiesen sein.

## <a name="enable-identity-management-for-your-resource"></a>Aktivieren der Identitätsverwaltung für Ihre Ressource

Ihre Language-Ressource muss über eine Identitätsverwaltung verfügen, die entweder über Azure-Portal oder über Language Studio aktiviert werden kann. So führen Sie die Aktivierung mit [Language Studio](https://aka.ms/languageStudio) aus:
1. Klicken Sie in der oberen rechten Ecke der Anzeige auf das Einstellungssymbol
2. Wählen Sie **Ressourcen** aus.
3. Wählen Sie die **Verwaltete Identität** für die Azure-Ressource aus.

## <a name="roles-for-your-storage-account"></a>Rollen für Ihr Speicherkonto

Ihr Azure Blob-Speicherkonto muss über die folgenden Rollen verfügen:

* Ihre Ressource verfügt über die Rolle **„Besitzer“** oder **„Mitwirkender“** für das Speicherkonto.
* Ihre Ressource verfügt über die Rolle **Storage-Blobdatenbesitzer** oder **Storage Blobdatenmitwirkender** für das Speicherkonto.
* Ihre Ressource verfügt über die Rolle **„Leser“** für das Speicherkonto.

So legen Sie die richtigen Rollen für Ihr Speicherkonto fest:

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrem Speicherkonto.
2. Wählen Sie im linken Navigationsmenü **Access Control (IAM)** aus.
3. Wählen Sie **Hinzufügen** aus, um **Rollenzuweisungen hinzuzufügen** und wählen Sie die Rolle **Besitzer** oder **Mitwirkender** aus. Sie können im Feld **Auswählen** nach Benutzernamen suchen.

[!INCLUDE [Storage connection note](../includes/storage-account-note.md)]

## <a name="prepare-training-data"></a>Vorbereiten von Trainingsdaten

* Als Voraussetzung für die Erstellung eines Projekts zur benutzerdefinierten Textklassifizierung müssen Ihre Trainingsdaten in einen Blobcontainer in Ihrem Speicherkonto hochgeladen werden. Sie können Trainingsdateien direkt aus Azure oder mithilfe des Tools Azure Storage-Explorer erstellen und hochladen. Mit dem Azure Storage-Explorer-Tool können Sie schneller mehr Daten hochladen.

  * [Erstellen und Hochladen von Dateien aus Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
  * [Erstellen und Hochladen von Dateien mit Azure Storage-Explorer](/azure/vs-azure-tools-storage-explorer-blobs)

* Sie können nur `.txt`- Dateien für benutzerdefinierte Textklassifizierung verwenden. Wenn Ihre Daten in einem anderen Format vorliegen, können Sie das [Cognitive Services-Sprachnutzungstool](https://aka.ms/CognitiveServicesLanguageUtilities) verwenden, um Ihre Datei im Format `.txt` zu analysieren.

* Sie können entweder getaggte Daten hochladen oder Ihre Daten in Language Studio taggen. Getaggte Daten müssen dem [Tagdateiformat](../concepts/data-formats.md) entsprechen. 

>[!TIP]
> Informationen zur Datenauswahl und -vorbereitung finden Sie unter [Entwerfen eines Schemas](design-schema.md).

## <a name="create-a-project"></a>Erstellen eines Projekts

[!INCLUDE [Language Studio project creation](../includes/create-project.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem das Projekt erstellt wurde, können Sie mit dem [Taggen Ihrer Daten](tag-data.md) beginnen, wodurch Ihr Textklassifizierungsmodell darüber informiert wird, wie Text interpretiert wird, und für das Training und die Auswertung verwendet wird.
