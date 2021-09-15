---
title: 'Schnellstart: JavaScript-Clientbibliothek für Gesichtserkennung'
description: Verwenden Sie die Gesichtserkennungs-Clientbibliothek für JavaScript, um Gesichter zu erkennen, ähnliche Gesichter zu finden (Gesichtssuche anhand von Bildern), Gesichter zu identifizieren (Gesichtserkennungssuche) und Ihre Gesichtserkennungsdaten zu migrieren.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: 56f116b8c7b90f74da86a792242866d5a88ebff4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442328"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Schnellstart: Clientbibliothek zur Gesichtserkennung für JavaScript

Hier erfahren Sie mehr zu den ersten Schritten mit Gesichtserkennung unter Verwendung der Gesichtserkennungs-Clientbibliothek für JavaScript. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Über den Gesichtserkennungsdienst haben Sie Zugriff auf erweiterte Algorithmen für die Erkennung von menschlichen Gesichtern in Bildern.

Verwenden Sie die Clientbibliothek zur Gesichtserkennung für JavaScript für Folgendes:

* [Erkennen und Analysieren von Gesichtern](#detect-and-analyze-faces)
* [Identifizieren eines Gesichts](#identify-a-face)
* [Suchen ähnlicher Gesichter](#find-similar-faces)

[Referenzdokumentation](/javascript/api/overview/azure/cognitive-services/face-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face) | [Paket (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face) | [Beispiele](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Die aktuelle Version von [Node.js](https://nodejs.org/en/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie im Azure-Portal [eine Gesichtserkennungsressource erstellen](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace), um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Gesichtserkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek 

Installieren Sie die npm-Pakete `ms-rest-azure` und `azure-cognitiveservices-face`:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

Erstellen Sie eine Datei namens `index.js`, und importieren Sie die folgenden Bibliotheken:

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub]() zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. 

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Gesichtserkennungsressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen verarbeiten einige der Hauptfunktionen der .NET-Clientbibliothek für die Gesichtserkennung:

|Name|BESCHREIBUNG|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Diese Klasse stellt Ihre Autorisierung für die Verwendung des Gesichtserkennungsdiensts dar. Sie benötigen sie für alle Gesichtserkennungsfunktionen. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Generieren von Instanzen anderer Klassen. |
|[Gesichtserkennung](/javascript/api/@azure/cognitiveservices-face/face)|Diese Klasse behandelt die grundlegenden Erkennungs- und Wiedererkennungsaufgaben, die Sie für menschliche Gesichter ausführen können. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|Diese Klasse stellt alle Daten dar, die in einem einzelnen Gesicht in einem Bild erkannt wurden. Sie können damit ausführliche Informationen zum Gesicht abrufen.|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|Diese Klasse verwaltet die in der Cloud gespeicherten **FaceList**-Konstrukte, die verschiedene Gesichter speichern. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Diese Klasse verwaltet die in der Cloud gespeicherten **Person**-Konstrukte, die eine Gruppe von Gesichtern speichern, die zu einer einzelnen Person gehören.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| Diese Klasse verwaltet die in der Cloud gespeicherten **PersonGroup**-Konstrukte, die verschiedene **Person**-Objekte speichern. |

## <a name="code-examples"></a>Codebeispiele

Die folgenden Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Clientbibliothek zur Gesichtserkennung für .NET ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Erkennen und Analysieren von Gesichtern](#detect-and-analyze-faces)
* [Identifizieren eines Gesichts](#identify-a-face)
* [Suchen ähnlicher Gesichter](#find-similar-faces)

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein Objekt vom Typ **[ApiKeyCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials)** mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein Objekt vom Typ **[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient)** zu erstellen.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Deklarieren von globalen Werten und Hilfsfunktionen

Die folgenden globalen Werte sind für einige Vorgänge der Gesichtserkennung erforderlich, die Sie später hinzufügen.

Die URL verweist auf einen Ordner mit Beispielbildern. Die UUID dient sowohl als Name als auch als ID für die zu erstellende Personengruppe (PersonGroup).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Sie verwenden die folgende Funktion, um auf den Abschluss des Trainings der Personengruppe zu warten:

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-and-analyze-faces"></a>Erkennen und Analysieren von Gesichtern

Die Gesichtserkennung ist als erster Schritt für die Gesichtsanalyse und Identitätsüberprüfung erforderlich. In diesem Abschnitt wird gezeigt, wie die zusätzlichen Gesichtsattributdaten zurückgegeben werden. Wenn Sie Gesichter nur für die Gesichtsidentifikation oder -überprüfung erkennen möchten, fahren Sie mit den Abschnitten weiter unten fort.


### <a name="get-detected-face-objects"></a>Abrufen erkannter Gesichtsobjekte

Erstellen Sie eine neue Methode für die Erkennung von Gesichtern. Mit der `DetectFaceExtract`-Methode werden drei der Bilder unter der angegebenen URL verarbeitet, und im Programmspeicher wird eine Liste mit **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** -Objekten erstellt. Die Liste der Werte vom Typ **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** gibt an, welche Features extrahiert werden. 

Die Methode `DetectFaceExtract` analysiert dann die Attributdaten für jedes erkannte Gesicht und gibt sie aus. Jedes Attribut muss separat im ursprünglichen Gesichtserkennungs-API-Rückruf (in der Liste **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** ) angegeben werden. Mit dem folgenden Code werden alle Attribute verarbeitet, aber Sie müssen wahrscheinlich nur eines oder wenige verwenden.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> Sie können auch Gesichter in einem lokalen Bild erkennen. Sehen Sie sich die [Face](/javascript/api/@azure/cognitiveservices-face/face)-Methoden an, etwa [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).



## <a name="identify-a-face"></a>Identifizieren eines Gesichts

Der [Identifizierungsvorgang](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) verwendet ein Bild einer Person (oder mehrerer Personen) und sucht im Bild nach dem gespeicherten Personenobjekt, das den einzelnen Gesichter zugeordnet ist (Gesichtserkennungssuche). Er vergleicht jedes erkannte Gesicht mit einem [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)-Objekt, einer Datenbank mit verschiedenen [Person](/javascript/api/@azure/cognitiveservices-face/person)-Objekten,deren Gesichtsmerkmale bekannt sind. Für den Identifizierungsvorgang müssen Sie zunächst ein [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)-Element erstellen und trainieren.

### <a name="add-faces-to-persongroup"></a>Hinzufügen von Gesichtern zu PersonGroup

Erstellen Sie die folgende Funktion, um [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup) Gesichter hinzuzufügen.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-persongroup"></a>Warten auf das Training von PersonGroup

Erstellen Sie die folgende Hilfsfunktion, um auf den Abschluss des Trainings der **PersonGroup** zu warten.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-persongroup"></a>Erstellen einer PersonGroup

Der folgende Code führt folgende Aktionen aus:
- Erstellen einer Personengruppe ([PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup))
- Hinzufügen von Gesichtern zur **PersonGroup** durch Aufruf der zuvor erstellten Funktion `AddFacesToPersonGroup`.
- Trainieren der **PersonGroup**.
- Identifizieren der Gesichter in der **PersonGroup**.

Diese **PersonGroup** und die ihr zugeordneten **Personen** objekte sind jetzt für die Verwendung in den Überprüfungs-, Identifizierungs- und Gruppierungsvorgängen bereit.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Sie können auch ein **PersonGroup**-Element aus lokalen Bildern erstellen. Sehen Sie sich die [IPersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)-Methoden an, etwa [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="find-similar-faces"></a>Suchen ähnlicher Gesichter

Der folgende Code verwendet ein einzelnes erkanntes Gesicht (Quelle) und durchsucht eine Reihe anderer Gesichter (Ziel) auf Übereinstimmungen (Gesichtssuche anhand von Bildern). Bei einer Übereinstimmung wird die ID des entsprechenden Gesichts in der Konsole ausgegeben.

### <a name="detect-faces-for-comparison"></a>Erkennen von Gesichtern zum Vergleich

Definieren Sie zunächst eine zweite Gesichtserkennungsmethode. Sie müssen Gesichter in Bildern erkennen, bevor Sie sie vergleichen können, und diese Erkennungsmethode ist für Vergleichsvorgänge optimiert. Sie extrahiert keine detaillierten Gesichtsattribute wie im obigen Abschnitt und verwendet ein anderes Erkennungsmodell.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Suchen von Übereinstimmungen

Die folgende Methode erkennt Gesichter in einer Reihe von Zielbildern und in einem einzelnen Quellbild. Anschließend vergleicht sie diese und sucht alle Zielbilder, die dem Quellbild ähneln. Schließlich werden die Übereinstimmungsdetails in der Konsole ausgegeben:

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="main"></a>Main

Erstellen Sie abschließend die Funktion `main`, und rufen Sie sie auf.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Gesichtserkennungs-Clientbibliothek für JavaScript für einfache Gesichtserkennungsaufgaben verwenden. Als Nächstes erfahren Sie mehr über die verschiedenen Gesichtserkennungsmodelle und darüber, wie Sie das richtige Modell für Ihren Anwendungsfall angeben.

> [!div class="nextstepaction"]
> [Angeben einer Gesichtserkennungsmodellversion](../../Face-API-How-to-Topics/specify-detection-model.md)

* [Was ist der Gesichtserkennungsdienst?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).
