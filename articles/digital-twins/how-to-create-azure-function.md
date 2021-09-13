---
title: Einrichten einer Funktion in Azure für die Verarbeitung von Daten
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie eine Funktion in Azure erstellen, die auf digitale Zwillinge zugreifen und von diesen ausgelöst werden kann.
author: baanders
ms.author: baanders
ms.date: 7/14/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: eaa26b4682dc7e984c0c84575d9e87b5255f6c99
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114386971"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Verbinden von Funktions-Apps in Azure für die Verarbeitung von Daten

Digitale Zwillinge können basierend auf Daten mithilfe von [Ereignisrouten](concepts-route-events.md) über Computeressourcen aktualisiert werden. Beispielsweise kann eine Funktion, die mit [Azure Functions](../azure-functions/functions-overview.md) erstellt wurde, einen digitalen Zwilling als Reaktion auf Folgendes aktualisieren:
* Gerätetelemetriedaten von Azure IoT Hub
* Änderung von Eigenschaften oder anderen Daten, die von einem anderen digitalen Zwilling innerhalb des Zwillingsgraphen stammen

Dieser Artikel führt Sie durch die Erstellung einer Funktion in Azure zur Verwendung mit Azure Digital Twins. Führen Sie die folgenden grundlegenden Schritte aus, um eine Funktion zu erstellen:

1. Erstellen eines Azure Functions-Projekts in Visual Studio
2. Schreiben einer Funktion mit einem [Azure Event Grid](../event-grid/overview.md)-Trigger
3. Hinzufügen von Authentifizierungscode zur Funktion für den Zugriff auf Azure Digital Twins
4. Veröffentlichen der Funktions-App in Azure
5. Einrichten der [Sicherheit](concepts-security.md) für die Funktions-App

## <a name="prerequisite-set-up-azure-digital-twins"></a>Voraussetzung: Einrichten von Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Erstellen einer Funktions-App in Visual Studio

Anweisungen zum Erstellen einer Funktions-App mithilfe von Visual Studio finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Schreiben einer Funktion mit einem Event Grid-Trigger

Sie können eine Funktion schreiben, indem Sie Ihrer Funktions-App ein SDK hinzufügen. Die Funktions-App interagiert mit Azure Digital Twins unter Verwendung des [Azure Digital Twins SDK für .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Wenn Sie das SDK verwenden möchten, müssen Sie die folgenden Pakete in Ihr Projekt einbinden. Installieren Sie die Pakete mithilfe des NuGet-Paket-Managers von Visual Studio. Sie können die Pakete auch mithilfe von `dotnet` in einem Befehlszeilentool hinzufügen.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Öffnen Sie als Nächstes im Visual Studio-Projektmappen-Explorer die _CS_-Datei mit Ihrem Beispielcode. Fügen Sie die folgenden `using`-Anweisungen für die Pakete hinzu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Hinzufügen von Authentifizierungscode zur Funktion

Nun deklarieren Sie Variablen auf Klassenebene und fügen Authentifizierungscode hinzu, um der Funktion den Zugriff auf Azure Digital Twins zu ermöglichen. Fügen Sie Ihrer Funktion die Variablen und Code hinzu.

* **Code zum Lesen der Dienst-URL für Azure Digital Twins als Umgebungsvariable:** Es hat sich bewährt, die Dienst-URL aus einer Umgebungsvariable zu lesen, anstatt dafür die Hartcodierung in der Funktion zu verwenden. Sie legen den Wert dieser Umgebungsvariablen [weiter unten](#set-up-security-access-for-the-function-app) fest. Weitere Informationen zu Umgebungsvariablen finden Sie unter [Verwalten Ihrer Funktions-App](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Eine statische Variable zum Aufnehmen einer HttpClient-Instanz** Die Erstellung von HttpClient ist relativ aufwendig, sodass Sie es vermeiden sollten, dies für jeden Funktionsaufruf durchzuführen.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Anmeldeinformationen für die verwaltete Identität:**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Eine lokale Variable _DigitalTwinsClient_:** Fügen Sie die Variable innerhalb Ihrer Funktion hinzu, um Ihre Azure Digital Twins-Clientinstanz aufzunehmen. Verwenden Sie hierfür *keine* statische Variable innerhalb Ihrer Klasse.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Eine NULL-Überprüfung für _adtInstanceUrl_:** Fügen Sie eine NULL-Überprüfung hinzu, und umschließen Sie Ihre Funktionslogik mit einem Try-Catch-Block, um alle Ausnahmen abzufangen.

Nach diesen Änderungen sollte Ihr Funktionscode dem folgenden Beispiel ähneln:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Nachdem Sie Ihre Anwendung geschrieben haben, können Sie sie in Azure veröffentlichen.

## <a name="publish-the-function-app-to-azure"></a>Veröffentlichen der Funktions-App in Azure

Anweisungen zum Veröffentlichen einer Funktions-App finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

### <a name="verify-the-publication-of-your-function"></a>Überprüfen der Veröffentlichung Ihrer Funktion

1. Melden Sie sich mit Ihren Anmeldeinformationen im [Azure-Portal](https://portal.azure.com/) an.
2. Suchen Sie im Suchfeld am oberen Rand des Fensters nach dem Namen Ihrer Funktions-App, und wählen Sie ihn aus.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Screenshot des Azure-Portals. Geben Sie im Suchfeld den Namen der Funktions-App ein." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Wählen Sie auf der daraufhin geöffneten Seite der **Funktions-App** im Menü auf der linken Seite **Funktionen** aus. Wenn Ihre Funktion veröffentlicht wurde, ist der Funktionsname in der Liste enthalten.

    > [!Note] 
    > Sie müssen möglicherweise einige Minuten warten oder die Seite mehrmals aktualisieren, bevor die Funktion in der Liste der veröffentlichten Funktionen aufgeführt wird.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Screenshot: Veröffentlichte Funktionen im Azure-Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Damit Ihre Funktions-App auf Azure Digital Twins zugreifen kann, muss sie über eine systemseitig verwaltete Identität mit Zugriffsberechtigungen für Ihre Azure Digital Twins-Instanz verfügen. Dies wird im nächsten Schritt eingerichtet.

## <a name="set-up-security-access-for-the-function-app"></a>Einrichten des Sicherheitszugriffs für die Funktions-App

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Funktions-App in Azure für die Verwendung mit Azure Digital Twins eingerichtet. Als Nächstes erfahren Sie, wie Sie auf Ihrer grundlegenden Funktion aufbauen können, um [IoT Hub-Daten in Azure Digital Twins zu erfassen](how-to-ingest-iot-hub-data.md).
