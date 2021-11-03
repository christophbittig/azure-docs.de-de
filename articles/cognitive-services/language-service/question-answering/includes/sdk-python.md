---
title: 'Schnellstart: Clientbibliothek für benutzerdefinierte Fragen und Antworten für Python'
description: In dieser Schnellstartanleitung werden die ersten Schritte mit der QnA Maker-Clientbibliothek für Python gezeigt.
ms.topic: include
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cdca2bce7a2731d4a819bd9e24d8aba5ab61b782
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029812"
---
Verwenden Sie die Clientbibliothek für benutzerdefinierte Fragen und Antworten für Python für folgende Aufgaben:

* Erstellen einer Wissensdatenbank
* Aktualisieren einer Wissensdatenbank
* Veröffentlichen einer Wissensdatenbank
* Warten auf Aufgaben mit langer Ausführungsdauer
* Herunterladen einer Wissensdatenbank
* Abrufen einer Antwort aus einer Wissensdatenbank
* Löschen einer Wissensdatenbank

[Referenzdokumentation](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Paket (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python-Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* * Benutzerdefinierte Fragen und Antworten erfordern eine [Sprachressource](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“, um einen API-Schlüssel und Endpunkt zu generieren.
    * Wählen Sie nach der Bereitstellung der Sprachressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der QnA Maker-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python, können Sie die Clientbibliothek mit Folgendem installieren:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie eine neue Python-Datei mit dem Namen `quickstart-file.py`, und importieren Sie die folgenden Bibliotheken:

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

<!-- TODO: Replace Link

- We use subscription key and authoring key interchangeably. For more details on authoring key, follow [Keys](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

-->

- Der Wert von „QNA_MAKER_ENDPOINT“ hat das Format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Navigieren Sie zum Azure-Portal, und suchen Sie die in den Voraussetzungen erstellte Sprachressource. Wählen Sie unter **Ressourcenverwaltung** die Seite **Schlüssel und Endpunkt** aus, um den Schlüssel für die Dokumenterstellung (Abonnement) und Endpunkt zu ermitteln.

    > [!div class="mx-imgBorder"]
    > ![Benutzerdefinierter Endpunkt für die QnA-Erstellung](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)
 
- In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise bietet der [Azure-Schlüsseltresor](../../../../key-vault/general/overview.md) sichere Aufbewahrung von Schlüsseln.

    [!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

## <a name="object-models"></a>Objektmodelle

Von [QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) wird das folgende Objektmodell verwendet:
* **[QnAMakerClient](#qnamakerclient-object-model)** ist das Objekt zum Erstellen, Verwalten, Veröffentlichen, Herunterladen und Abfragen der Wissensdatenbank.

[!INCLUDE [Get KBinformation](../../../qnamaker/includes/quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-Objektmodell

Der erstellende QnA Maker-Client ist ein [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient)-Objekt, das mithilfe der „Microsoft.Rest.ServiceClientCredentials“-Klasse, die Ihren Schlüssel enthält, bei Azure authentifiziert wird.

Nachdem der Client erstellt wurde, verwenden Sie die [Knowledge Base](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations)-Eigenschaft, um Ihre Knowledge Base zu erstellen, zu verwalten und zu veröffentlichen.

Verwalten Sie Ihre Knowledge Base durch Senden eines JSON-Objekts. Bei sofortigen Vorgängen gibt eine Methode in der Regel ein JSON-Objekt zurück, das den Status angibt. Bei zeitintensiven Vorgängen ist die Antwort die Vorgangs-ID. Rufen Sie die [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#get-details-kb-id--custom-headers-none--raw-false----operation-config-)-Methode mit der Vorgangs-ID auf, um den [Status der Anforderung](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype) zu bestimmen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-Objektmodell

Benutzerdefinierte Fragen und Antworten erfordern nicht die Verwendung des QnAMakerRuntimeClient-Objekts. Stattdessen kann [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) direkt für das Objekt [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) aufgerufen werden.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifizieren des Clients zum Erstellen der Wissensdatenbank

Instanziieren Sie einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein CognitiveServicesCredentials-Objekt mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient)-Objekt zu erstellen.

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank

Verwenden Sie das Clientobjekt, um ein Objekt vom Typ [KnowledgebaseOperations](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations) abzurufen.

Eine Wissensdatenbank speichert Frage- und Antwortpaare für das [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto)-Objekt aus drei Quellen:

* Für **redaktionellen Inhalt** verwenden Sie das [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto)-Objekt.
    * Verwenden Sie für Metadaten und Folgeaufforderungen den redaktionellen Kontext, da diese Daten auf der Ebene der einzelnen QnA-Paare hinzugefügt werden.
* Für **Dateien** verwenden Sie das [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto)-Objekt. Das FileDTO-Objekt enthält den Dateinamen sowie die öffentliche URL für den Zugriff auf die Datei.
* Verwenden Sie für **URLs** eine Liste von Zeichenfolgen zur Darstellung von öffentlich verfügbaren URLs.

Rufen Sie die [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#create-create-kb-payload--custom-headers-none--raw-false----operation-config-)-Methode auf, und übergeben Sie dann die zurückgegebene Vorgangs-ID an die [Operations.getDetails](#get-status-of-an-operation)-Methode, um den Status abzurufen.

In der letzten Zeile des folgenden Codes wird die Knowledge Base-ID aus der Antwort von „MonitorOperation“ zurückgegeben.

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

Zum erfolgreichen Erstellen einer Wissensdatenbank muss die Funktion [`_monitor_operation`](#get-status-of-an-operation) eingeschlossen werden, auf die im obigen Code verwiesen wird.

## <a name="update-a-knowledge-base"></a>Aktualisieren einer Wissensdatenbank

Sie können eine Wissensdatenbank aktualisieren, indem Sie die Wissensdatenbank-ID und ein [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto)-Element, das [add](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd)-, [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)- und [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete)-DTO-Objekte enthält, als Eingabe an die [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations)-Methode übergeben. Verwenden Sie die [Operation.getDetail](#get-status-of-an-operation) -Methode, um zu bestimmen, ob das Update erfolgreich war.

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

Nehmen Sie zum erfolgreichen Aktualisieren einer Wissensdatenbank unbedingt die Funktion [`_monitor_operation`](#get-status-of-an-operation) mit auf, auf die im Code oben verwiesen wird.

## <a name="download-a-knowledge-base"></a>Herunterladen einer Knowledge Base

Verwenden Sie die [download](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations)-Methode, um die Datenbank als Liste von [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto) herunterzuladen. Dies entspricht _nicht_ dem Export des QnA Maker-Portals von der Seite **Einstellungen**, da das Ergebnis dieser Methode keine TSV-Datei ist.

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

## <a name="publish-a-knowledge-base"></a>Veröffentlichen einer Wissensdatenbank

Veröffentlichen Sie die Wissensdatenbank mit der [publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#publish-kb-id--custom-headers-none--raw-false----operation-config-)-Methode. Diese übernimmt das aktuell gespeicherte und trainierte Modell, auf das von der Knowledge Base-ID verwiesen wird, und veröffentlicht dieses an einem Endpunkt.

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

## <a name="query-a-knowledge-base"></a>Abfragen einer Knowledge Base

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generieren einer Antwort auf der Grundlage der Wissensdatenbank

Generieren Sie mithilfe der Methode [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) eine Antwort auf der Grundlage einer veröffentlichten Wissensdatenbank. Diese Methode akzeptiert die Wissensdatenbank-ID und die [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto)-Klasse. Greifen Sie auf weitere QueryDTO-Eigenschaften zu (etwa „Top“ und „Context“), und verwenden Sie sie in Ihrem Chatbot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

<!-- TODO: Replace Link
This is a simple example of querying the knowledge base. To understand advanced querying scenarios, review [other query examples](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).
-->

## <a name="delete-a-knowledge-base"></a>Löschen einer Wissensdatenbank

Löschen Sie die Wissensdatenbank mit der [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#delete-kb-id--custom-headers-none--raw-false----operation-config-)-Methode mit einem Parameter der Wissensdatenbank-ID.

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

## <a name="get-status-of-an-operation"></a>Abrufen des Status eines Vorgangs

Einige Methoden, wie „create“ und „update“, können so viel Zeit in Anspruch nehmen, dass ein [Vorgang](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)) zurückgegeben wird, anstatt auf den Abschluss des Prozesses zu warten. Verwenden Sie die Vorgangs-ID des Vorgangs, um den Status der ursprünglichen Methode per Abruf (mit Wiederholungslogik) zu bestimmen.

Der _setTimeout_-Aufruf im folgenden Codeblock wird verwendet, um asynchronen Code zu simulieren. Ersetzen Sie dies durch Wiederholungslogik.

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Python-Befehl für die Schnellstartdatei aus.

```console
python quickstart-file.py
```

Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py).
