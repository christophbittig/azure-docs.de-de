---
title: 'Schnellstart: Verwenden von cURL & REST zum Verwalten einer Wissensdatenbank – Benutzerdefinierte Fragen und Antworten'
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie Ihre Wissensdatenbank mit den REST-APIs erstellen, veröffentlichen und abfragen.
ms.date: 11/02/2021
ms.topic: include
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2768081da14be2c481093d54c833f1cde45e36a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029793"
---
## <a name="prerequisites"></a>Voraussetzungen

* Aktuelle Version von [cURL](https://curl.haxx.se/). In den Schnellstartanleitungen werden mehrere Befehlszeilenschalter verwendet, die in der [cURL-Dokumentation](https://curl.haxx.se/docs/manpage.html) angegeben sind.
* Für benutzerdefinierte Fragen und Antworten ist eine [Sprachressource](../../../qnamaker/how-to/set-up-qnamaker-service-azure.md?tabs=v2#create-a-new-qna-maker-service) mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“ erforderlich, um einen API-Schlüssel und Endpunkt zu generieren. Der **Name**, den Sie beim Erstellen Ihrer Ressource gewählt haben, wird als Unterdomäne für Ihren Endpunkt verwendet. Wählen Sie für Ihre Ressource im Azure-Portal die Option **Schnellstart** aus, um den Schlüssel und Ressourcennamen abzurufen. Der Ressourcenname ist die erste Unterdomäne der Endpunkt-URL: <!--TODO: Change link-->

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2`

> [!CAUTION]
> In den folgenden Bash-Beispielen wird das Zeilenfortsetzungszeichen `\` verwendet. Verwenden Sie dieses Zeichen, falls in Ihrer Konsole oder Ihrem Terminal ein anderes Zeilenfortsetzungszeichen genutzt wird.

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

Sie benötigen die folgenden Informationen, um mit den REST-APIs und mit cURL eine Wissensdatenbank zu erstellen:

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|Name einer Sprachressource (mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“)|URL|Wird zum Erstellen der URL verwendet.|
|Sprachressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim Sprachdienst|
|JSON-Code zur Beschreibung der Wissensdatenbank|Parameter `-d`|[Beispiele](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) für JSON|
|Größe der JSON-Datei in Byte|Parameter `-h` für den Header `Content-Size`||

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie für diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die JSON-Werte und die JSON-Größe ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Die cURL-Antwort von benutzerdefinierten Fragen und Antworten enthält die `operationId`, die benötigt wird, um den [Status des Vorgangs abzurufen](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Abrufen des Status eines Vorgangs

Da der Vorgang beim Erstellen einer Wissensdatenbank asynchron ist, enthält die Antwort Informationen zum Ermitteln des Status.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|Name einer Sprachressource (mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“)|URL|Wird zum Erstellen der URL verwendet.|
|Vorgangs-ID|URL-Route|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Sprachressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim Sprachdienst|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Vorgangs-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Die cURL-Antwort enthält den Status. Wenn der Status des Vorgangs „Erfolgreich“ lautet, enthält `resourceLocation` die Wissensdatenbank-ID.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

Bevor Sie die Wissensdatenbank abfragen, müssen Sie sie veröffentlichen.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|Name einer Sprachressource (mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“)|URL|Wird zum Erstellen der URL verwendet.|
|Sprachressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim Sprachdienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Der Antwortstatus lautet 204 ohne Ergebnisse. Verwenden Sie den Befehlszeilenparameter `-v`, um die ausführliche Ausgabe für den cURL-Befehl anzuzeigen. Darin ist auch der HTTP-Status enthalten.

## <a name="query-for-answer-from-published-knowledge-base"></a>Abfragen einer Antwort von der veröffentlichten Wissensdatenbank

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|Name einer Sprachressource (mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“)|URL|Wird zum Erstellen der URL verwendet.|
|Sprachressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim Sprachdienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON-Code zur Beschreibung der Abfrage|Parameter `-d`|[Anforderungstextparameter](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) und [JSON-Beispiele](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|Größe der JSON-Datei in Byte|Parameter `-h` für den Header `Content-Size`||

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Eine erfolgreiche Antwort enthält die beste Antwort und die anderen Informationen, die von einer Clientanwendung, z. B. einem Chatbot, zum Anzeigen einer Antwort für den Benutzer benötigt werden.

## <a name="delete-knowledge-base"></a>Löschen einer Wissensdatenbank

Löschen Sie die Wissensdatenbank, wenn Sie die Nutzung beendet haben.

|Information|cURL-Konfiguration|Zweck|
|--|--|--|
|Name einer Sprachressource (mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“)|URL|Wird zum Erstellen der URL verwendet.|
|Sprachressourcenschlüssel|Parameter `-h` für den Header `Ocp-Apim-Subscription-Key`|Authentifizieren beim Sprachdienst|
|Wissensdatenbank-ID|URL-Route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die Wissensdatenbank-ID ein.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Der Antwortstatus lautet 204 ohne Ergebnisse. Verwenden Sie den Befehlszeilenparameter `-v`, um die ausführliche Ausgabe für den cURL-Befehl anzuzeigen. Darin ist auch der HTTP-Status enthalten.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Referenzdokumentation zur [Erstellung](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)
* Referenzdokumentation zur [Laufzeit](/rest/api/cognitiveservices/qnamaker4.0/runtime)
* [Bash-Beispielskripts mit cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)
