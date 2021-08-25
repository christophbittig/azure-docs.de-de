---
title: 'Schnellstart: Clientbibliothek für die Azure-Verwaltung für Node.js'
description: In dieser Schnellstartanleitung werden die ersten Schritte mit der Clientbibliothek für die Azure-Verwaltung für Node.js erläutert.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 37081904104befe18ed4f072b8fe474aabaa688b
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442447"
---
[Referenzdokumentation](/javascript/api/@azure/arm-cognitiveservices/) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Paket (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>JavaScript-Voraussetzungen

* Ein gültiges Azure-Abonnement: [Erstellen Sie ein kostenloses Abonnement](https://azure.microsoft.com/free/).
* Die aktuelle Version von [Node.js](https://nodejs.org/)
* [!INCLUDE [contributor-requirement](./contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen. 

```console
npm init
```

Erstellen Sie eine Datei namens _index.js_, bevor Sie fortfahren.

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie die folgenden NPM-Pakete:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

### <a name="import-libraries"></a>Importbibliotheken

Öffnen Sie das Skript _index.js_, und importieren Sie die folgenden Bibliotheken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Fügen Sie dem Stamm des Skripts die folgenden Felder hinzu, und geben Sie die zugehörigen Werte unter Verwendung des zuvor erstellten Dienstprinzipals und Ihrer Azure-Kontoinformationen ein.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Fügen Sie als Nächstes die folgende `quickstart`-Funktion für die eigentliche Arbeit des Programms hinzu. Der erste Codeblock erstellt ein **CognitiveServicesManagementClient**-Objekt mithilfe der Anmeldeinformationsvariablen, die Sie oben eingegeben haben. Dieses Objekt wird für alle Azure-Verwaltungsvorgänge benötigt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Aufrufen von Verwaltungsfunktionen

Fügen Sie am Ende Ihrer `quickstart`-Methode den folgenden Code hinzu, um verfügbare Ressourcen aufzulisten, eine Beispielressource zu erstellen, Ihre eigenen Ressourcen aufzulisten und dann die Beispielressource zu löschen. Diese Funktionen definieren Sie in den nächsten Schritten.

## <a name="create-a-cognitive-services-resource-nodejs"></a>Erstellen einer Cognitive Services-Ressource (Node.js)

Um eine neue Cognitive Services-Ressource zu erstellen und zu abonnieren, verwenden Sie die **Create**-Funktion. Diese Funktion fügt der Ressourcengruppe, die Sie übergeben, eine neue abrechenbare Ressource hinzu. Wenn Sie die neue Ressource erstellen, müssen Sie die „Art“ des Diensts, den Sie verwenden möchten, sowie dessen Tarif (oder die SKU) und einen Azure-Standort kennen. Die folgende Funktion akzeptiert all diese Argumente und erstellt eine Ressource.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Auswählen eines Diensts und eines Tarifs

Wenn Sie eine neue Ressource erstellen, müssen Sie die „Art“ des Diensts, den Sie verwenden möchten, sowie den gewünschten [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/) (oder die SKU) kennen. Diese und andere Informationen verwenden Sie beim Erstellen der Ressource als Parameter. Die folgende Funktion listet die verfügbaren Arten von Cognitive Services-Diensten auf.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Anzeigen Ihrer Ressourcen

Verwenden Sie die folgende Funktion, um alle Ressourcen in Ihrem Azure-Konto (in allen Ressourcengruppen) anzuzeigen:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Löschen einer Ressource

Die folgende Funktion löscht die angegebene Ressource aus der jeweiligen Ressourcengruppe.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

Wenn Sie eine gelöschte Ressource wiederherstellen müssen, finden Sie weitere Informationen unter [Wiederherstellen gelöschter Cognitive Services-Ressourcen](../../manage-resources.md).

## <a name="run-the-application"></a>Ausführen der Anwendung

Fügen Sie am Ende Ihres Skripts den folgenden Code hinzu, um die `quickstart`-Hauptfunktion mit Fehlerbehandlung aufzurufen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Führen Sie dann in Ihrem Konsolenfenster die Anwendung mit dem Befehl `node` aus.

```console
node index.js
```

## <a name="see-also"></a>Siehe auch

* Informationen zum sicheren Arbeiten mit Cognitive Services finden Sie unter **[Authentifizieren von Anforderungen an Azure Cognitive Services](../../authentication.md)** .
* Eine Liste der verschiedenen Kategorien in Cognitive Services finden Sie unter **[Was ist Azure Cognitive Services?](../../what-are-cognitive-services.md)** .
* Eine Liste der natürlichen Sprachen, die von Cognitive Services unterstützt werden, finden Sie unter **[Unterstützung für natürliche Sprachen in Azure Cognitive Services](../../language-support.md)** .
* Informationen zur lokalen Verwendung von Cognitive Services finden Sie unter **[Azure Cognitive Services-Container](../../cognitive-services-container-support.md)** .
* Informationen zum Schätzen der Kosten für die Verwendung von Cognitive Services finden Sie unter **[Planen und Verwalten von Kosten für Azure Cognitive Services](../../plan-manage-costs.md)** .
* Weitere Informationen zum Management SDK finden Sie in der **[Azure-Verwaltungs-SDK-Referenz Dokumentation](/javascript/api/@azure/arm-cognitiveservices/)** .
