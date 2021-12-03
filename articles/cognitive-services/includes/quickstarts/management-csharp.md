---
title: 'Schnellstart: Clientbibliothek für die Azure-Verwaltung für .NET'
description: In diesem Schnellstart werden die ersten Schritte mit der Clientbibliothek für die Azure-Verwaltung für .NET erläutert.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 9424766aea22b0dc8b8b43e2eb14be5f3621f688
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520273"
---
[Referenzdokumentation](/dotnet/api/overview/azure/cognitiveservices/management) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/8.0.0-preview/) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>C#-Voraussetzungen

* Ein gültiges Azure-Abonnement: [Erstellen Sie ein kostenloses Abonnement](https://azure.microsoft.com/free/).
* Aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* [!INCLUDE [contributor-requirement](./contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Erstellen Sie eine neue .NET Core-Anwendung. Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `azure-management-quickstart`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Clientbibliothek für die Azure-Verwaltung für .NET:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Bei Verwendung der Visual Studio-IDE ist die Clientbibliothek als herunterladbares NuGet-Paket verfügbar.

### <a name="import-libraries"></a>Importbibliotheken

Öffnen Sie *program.cs*, und fügen Sie am Anfang der Datei die folgenden `using`-Anweisungen hinzu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Fügen Sie dem Stamm von *program.cs* die folgenden Felder hinzu, und geben Sie die zugehörigen Werte unter Verwendung des zuvor erstellten Dienstprinzipals und Ihrer Azure-Kontoinformationen ein.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Verwenden Sie diese Werte dann in der **Main**-Methode, um ein **CognitiveServicesManagementClient**-Objekt zu erstellen. Dieses Objekt wird für alle Azure-Verwaltungsvorgänge benötigt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Aufrufen von Verwaltungsmethoden

Fügen Sie Ihrer **Main**-Methode den folgenden Code hinzu, um verfügbare Ressourcen aufzulisten, eine Beispielressource zu erstellen, Ihre eigenen Ressourcen aufzulisten und dann die Beispielressource zu löschen. Diese Methoden definieren Sie in den nächsten Schritten.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Erstellen einer Cognitive Services-Ressource (C#)

Um eine neue Cognitive Services-Ressource zu erstellen und zu abonnieren, verwenden Sie die **Create**-Methode. Diese Methode fügt der Ressourcengruppe, die Sie übergeben, eine neue abrechenbare Ressource hinzu. Wenn Sie die neue Ressource erstellen, müssen Sie die „Art“ des Diensts, den Sie verwenden möchten, sowie dessen Tarif (oder die SKU) und einen Azure-Standort kennen. Die folgende Methode akzeptiert all diese Argumente und erstellt eine Ressource.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Auswählen eines Diensts und eines Tarifs

Wenn Sie eine neue Ressource erstellen, müssen Sie die „Art“ des Diensts, den Sie verwenden möchten, sowie den gewünschten [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/) (oder die SKU) kennen. Diese und andere Informationen verwenden Sie beim Erstellen der Ressource als Parameter. Sie können eine Liste der verfügbaren Arten von Cognitive Services-Diensten anzeigen, indem Sie die folgende Methode im Skript aufrufen:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Anzeigen Ihrer Ressourcen

Verwenden Sie die folgende Methode, um alle Ressourcen in Ihrem Azure-Konto (in allen Ressourcengruppen) anzuzeigen:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Löschen einer Ressource

Die folgende Methode löscht die angegebene Ressource aus der jeweiligen Ressourcengruppe.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

Wenn Sie eine gelöschte Ressource wiederherstellen müssen, finden Sie weitere Informationen unter [Wiederherstellen gelöschter Cognitive Services-Ressourcen](../../manage-resources.md).

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnet
dotnet run
```

## <a name="see-also"></a>Siehe auch

* Informationen zum sicheren Arbeiten mit Cognitive Services finden Sie unter **[Authentifizieren von Anforderungen an Azure Cognitive Services](../../authentication.md)** .
* Eine Liste der verschiedenen Kategorien in Cognitive Services finden Sie unter **[Was ist Azure Cognitive Services?](../../what-are-cognitive-services.md)** .
* Eine Liste der natürlichen Sprachen, die von Cognitive Services unterstützt werden, finden Sie unter **[Unterstützung für natürliche Sprachen in Azure Cognitive Services](../../language-support.md)** .
* Informationen zur lokalen Verwendung von Cognitive Services finden Sie unter **[Azure Cognitive Services-Container](../../cognitive-services-container-support.md)** .
* Informationen zum Schätzen der Kosten für die Verwendung von Cognitive Services finden Sie unter **[Planen und Verwalten von Kosten für Azure Cognitive Services](../../plan-manage-costs.md)** .
* Weitere Informationen zum Management SDK finden Sie in der **[Kognitive Services SDK-Referenz Dokumentation](/dotnet/api/overview/azure/cognitiveservices/management)** .
