---
title: Anreichern eines Cognitive Search-Index mit benutzerdefinierten Entitäten
titleSuffix: Azure Cognitive Services
description: Verbessern Sie Ihre Indizes für die kognitive Suche mithilfe von benutzerdefinierter Erkennung benannter Entitäten (NER)
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: tutorial
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 555269beeb5e8b36597998a8941e2b64120691b4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484154"
---
# <a name="tutorial-enrich-a-cognitive-search-index-with-custom-entities-from-your-data"></a>Tutorial: Anreichern eines Cognitive Search-Index mit benutzerdefinierten Entitäten aus Ihren Daten

In Unternehmen kann das Vorliegen einer Fülle elektronischer Dokumente bedeuten, dass das Durchsuchen dieser Dokumente eine zeitaufwändige und teure Aufgabe ist. [Azure Cognitive Search](/azure/search/search-create-service-portal) kann Sie mithilfe ihrer Indizes beim Durchsuchen Ihrer Dateien unterstützen. Benutzerdefinierte NER kann Abhilfe schaffen, indem relevante Entitäten aus Ihren Dateien extrahiert werden und der Prozess der Indizierung dieser Dateien angereichert wird.

In diesem Tutorial lernen Sie Folgendes:

* Erstellen eines Projekts zur benutzerdefinierten Erkennung benannter Entitäten.
* Veröffentlichen Sie die Azure-Funktion.
* Hinzufügen eines Index zu Azure Cognitive Search.

## <a name="prerequisites"></a>Voraussetzungen

* [Eine Azure-Sprachressource, die mit einem Azure Blob Storage-Konto verbunden ist](../how-to/create-project.md).
    * Zur einfacheren Einrichtung empfiehlt es sich, die Anweisungen zum Erstellen einer Ressource mithilfe des Azure-Portals zu befolgen. 
* [Ein Azure Cognitive Search-Dienst](/azure/search/search-create-service-portal) in Ihrem aktuellen Abonnement
    * Sie können für diesen Dienst jede Ebene und jede Region verwenden.
* Eine [Azure-Funktions-App](/azure/azure-functions/functions-create-function-app-portal)
* Laden Sie diese [Beispieldaten](https://go.microsoft.com/fwlink/?linkid=2175226) herunter.

## <a name="create-a-custom-ner-project-through-language-studio"></a>Erstellen eines benutzerdefinierten NER-Projekts mithilfe von Language Studio

1. Melden Sie sich über das [Language Studio-Portal](https://aka.ms/LanguageStudio) an, und wählen Sie **Custom entity extraction** (Benutzerdefinierte Entitätsextraktion) aus.

2. Wählen Sie Ihre Sprachressource aus. Vergewissern Sie sich, dass Sie die [Identitätsverwaltung aktiviert haben](../how-to/create-project.md#enable-identity-management-for-your-resource), ebenso die Rollen für Ihre Ressource und Ihr Speicherkonto.

3. Wählen Sie oben auf dem Projektbildschirm **Neues Projekt erstellen** aus. Wählen Sie auf Anfrage im angezeigten Menü Ihr Speicherkonto aus.

    :::image type="content" source="../media/create-project.png" alt-text="Screenshot der Seite zur Projekterstellung." lightbox="../media/create-project.png":::

4. Geben Sie die Informationen für Ihr Projekt ein:

    | Schlüssel | Beschreibung |
    |--|--|
    | Name | Der Name des Projekts. Sie können Ihr Projekt nach der Erstellung nicht mehr umbenennen. |
    | BESCHREIBUNG | Eine Beschreibung Ihres Projekts |
    | Sprache | Die Sprache der Dateien in Ihrem Projekt.|

    > [!NOTE]
    > Wenn Sie Dokumente in mehreren Sprachen verarbeiten möchten, wählen Sie bei der Projekterstellung die Option **Mehrere Sprachen** aus, und legen Sie die Option **Sprache** auf die Sprache der Mehrheit Ihrer Dokumente fest.

5. Verwenden Sie für dieses Tutorial eine **vorhandene Tagdatei**, und wählen Sie die Tagdatei in den heruntergeladenen Beispieldaten aus.

## <a name="train-your-model"></a>Trainieren Ihres Modells

1. Wählen Sie im Menü auf der linken Seite **Trainieren** aus.

2. Wählen Sie zum Trainieren eines neuen Modells **Neues Modell trainieren** aus, und geben Sie den Namen des Modells im Textfeld darunter ein.

    :::image type="content" source="../media/train-model.png" alt-text="Erstellen eines neuen Modells" lightbox="../media/train-model.png":::

3. Wählen Sie unten auf der Seite die Schaltfläche **Trainieren** aus.

4. Nach Abschluss des Trainings können Sie [die Auswertungsdetails des Modells anzeigen](../how-to/view-model-evaluation.md) und [das Modell verbessern](../how-to/improve-model.md).

## <a name="deploy-your-model"></a>Bereitstellen Ihres Modells

1. Wählen Sie im Menü auf der linken Seite **Modell bereitstellen** aus.

2. Wählen Sie das Modell aus, das Sie bereitstellen möchten, und klicken Sie im oberen Menü auf **Modell bereitstellen**. Es werden nur Modelle angezeigt, deren Training erfolgreich abgeschlossen wurde.

## <a name="prepare-your-secrets-for-the-azure-function"></a>Vorbereiten Ihrer Geheimnisse für die Azure-Funktion

Als Nächstes müssen Sie Ihre Geheimnisse für Ihre Azure-Funktion vorbereiten. Dies sind Ihre Projektgeheimnisse: 
* Endpunkt
* Ressourcenschlüssel
* Modell-ID

### <a name="get-your-custom-ner-project-secrets"></a>Abrufen der Geheimnisse Ihres benutzerdefinierte NER-Projekts

* Sie benötigen Ihren **Projektnamen**. Bei Projektnamen wird die Groß-/Kleinschreibung beachtet.

* Außerdem benötigen Sie den Bereitstellungsslot. 
   * Wenn Sie Ihr Modell über Language Studio bereitgestellt haben, ist Ihr Bereitstellungsslot standardmäßig `prod`. 
   * Wenn Sie Ihr Modell programmgesteuert mithilfe der API bereitgestellt haben, ist dies der Bereitstellungsname, den Sie in Ihrer Anforderung zugewiesen haben.

### <a name="get-your-resource-keys-endpoint"></a>Abrufen Ihrer Ressourcenschlüssel und des Endpunkts

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/#home) zu Ihrer Ressource.

2. Wählen Sie im Menü auf der linken Seite **Schlüssel und Endpunkt** aus. Sie benötigen den Endpunkt und einen der Schlüssel für die API-Anforderungen.

    :::image type="content" source="../../media/azure-portal-resource-credentials.png" alt-text="Screenshot des Schlüssel- und Endpunkt-Bildschirms im Azure-Portal" lightbox="../../media/azure-portal-resource-credentials.png":::
   
## <a name="edit-and-deploy-your-azure-function"></a>Bearbeiten und Bereitstellen Ihrer Azure-Funktion

1. Laden Sie die [bereitgestellte Beispielfunktion](https://aka.ms/ct-cognitive-search-integration-tool) herunter, und verwenden Sie sie.

2. Nachdem Sie die Beispielfunktion heruntergeladen haben, öffnen Sie die Datei *program.cs*, und geben Sie Ihre App-Geheimnisse ein.

3. [Veröffentlichen Sie die Funktion in Azure](/azure/azure-functions/functions-develop-vs?tabs=in-process#publish-to-azure).

## <a name="use-the-integration-tool"></a>Verwenden des Integrationstools

In den folgenden Abschnitten verwenden Sie das [Cognitive Search-Integrationstool](https://aka.ms/ct-cognitive-search-integration-tool), um Ihr Projekt in Azure Cognitive Search zu integrieren. Laden Sie dieses Repository jetzt herunter. 

### <a name="prepare-configuration-file"></a>Vorbereitung der Konfigurationsdatei

1. Suchen Sie in dem Ordner, den Sie gerade heruntergeladen haben, die [Beispielkonfigurationsdatei](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Samples/configs.json). Öffnen Sie die Datei in einem Text-Editor. 

2. Rufen Sie die Verbindungszeichenfolge Ihres Speicherkontos wie folgt ab:
    1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/#home) zur Übersichtsseite Ihres Speicherkontos.
    2. Kopieren Sie im oberen Abschnitt des Bildschirms den Containernamen in das Feld `containerName` in der Konfigurationsdatei, unter `blobStorage`.  
    3. Kopieren Sie im Abschnitt **Zugriffsschlüssel** des Menüs auf der linken Bildschirmseite Ihre **Verbindungszeichenfolge** in das Feld `connectionString` in der Konfigurationsdatei, unter `blobStorage`.

1. Rufen Sie Ihren Endpunkt und die Schlüssel für die kognitive Suche wie folgt ab:
    1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/#home) zur Übersichtsseite Ihrer Ressource.
    2. Kopieren Sie die **Url** im oberen rechten Abschnitt der Seite in das Feld `endpointUrl` in `cognitiveSearch`.
    3. Wechseln Sie im Menü auf der linken Bildschirmseite zum Abschnitt **Schlüssel**. Kopieren Sie Ihren **primären Administratorschlüssel** in das Feld `apiKey` in `cognitiveSearch`.

3. Abrufen des Azure Function-Endpunkts und der Schlüssel
    
    1. Zum Abrufen Ihres Azure Function-Endpunkts und der Schlüssel navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/#home) zur Übersichtsseite Ihrer Funktion.
    2. Wechseln Sie zum Menü **Funktionen** auf der linken Bildschirmseite, und klicken Sie auf die von Ihnen erstellte Funktion.
    3. Klicken Sie im oberen Menü auf **Funktions-Url abrufen**. Die URL weist das folgende Format auf: `YOUR-ENDPOINT-URL?code=YOUR-API-KEY`. 
    4. Kopieren Sie `YOUR-ENDPOINT-URL` in das Feld `endpointUrl` in der Konfigurationsdatei unter `azureFunction`. 
    5. Kopieren Sie `YOUR-API-KEY` in das Feld `apiKey` in der Konfigurationsdatei unter `azureFunction`. 

### <a name="prepare-schema-file"></a>Vorbereiten der Schemadatei

Suchen Sie in dem Ordner, den Sie zuvor heruntergeladen haben, die [Beispielschemadatei](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/dev/CustomTextAnalytics.CognitiveSearch/Samples/app-schema.json). Öffnen Sie die Datei in einem Text-Editor. 

Die Einträge im Array `entityNames` sind die Entitätsnamen, die Sie beim Erstellen Ihres Projekts zugewiesen haben. Sie können sie kopieren und aus Ihrem Projekt in [Language Studio](https://aka.ms/custom-extraction) einfügen. Alternativ: 

### <a name="run-the-index-command"></a>Führen Sie den Befehl `Index` aus.

Nachdem Sie ihre Konfigurations- und Schemadatei fertig gestellt haben, können Sie das Projekt indizieren. Platzieren Sie Ihre Konfigurationsdatei im gleichen Pfad wie das CLI-Tool, und führen Sie den folgenden Befehl aus:

```cli
    indexer index --schema <path/to/your/schema> --index-name <name-your-index-here>
```

Ersetzen Sie `name-your-index-here` durch den Indexnamen, der in Ihrer Cognitive Search-Instanz angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

* [Durchsuchen Ihrer App mit dem Cognitive Search SDK](/azure/search/search-howto-dotnet-sdk#run-queries)
