---
title: 'Schnellstart: Entitätsverknüpfungs-Clientbibliothek für Java | Microsoft-Dokumentation'
description: Erste Schritte mit der Entitätsverknüpfung mithilfe der Clientbibliothek für Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.custom: devx-track-java, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: ed109dc32e461381628cb04e3f32ba91a5fa5f99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029988"
---
[Referenzdokumentation](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-stable) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html), Version 8 oder höher
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie im Azure-Portal <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Sprachressource"  target="_blank">eine Sprachressource erstellen</a>, um Ihren Schlüssel und Endpunkt abzurufen.  Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Sie benötigen eine Sprachressource des Standard-Tarifs (S), um die Analysefunktion verwenden zu können.

## <a name="setting-up"></a>Einrichten

### <a name="add-the-client-library"></a>Hinzufügen der Clientbibliothek

Erstellen Sie ein Maven-Projekt in Ihrer bevorzugten IDE oder Entwicklungsumgebung. Fügen Sie anschließend der Datei *pom.xml* Ihres Projekts die folgende Abhängigkeit hinzu. Die Implementierungssyntax [für andere Buildtools](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) ist online verfügbar.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
```

## <a name="code-example"></a>Codebeispiel

Erstellen Sie eine Java-Datei mit dem Namen `Example.java`. Öffnen Sie die Datei, und kopieren Sie den folgenden Code. Denken Sie daran, die Variable `key` durch den Schlüssel für Ihre Ressource und die Variable `endpoint` durch den Endpunkt für Ihre Ressource zu ersetzen. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;

public class Example {

    private static String KEY = "replace-with-your-key-here";
    private static String ENDPOINT = "replace-with-your-endpoint-here";

    public static void main(String[] args) {
        TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
        recognizeLinkedEntitiesExample(client);
    }
    // Method to authenticate the client object with your key and endpoint
    static TextAnalyticsClient authenticateClient(String key, String endpoint) {
        return new TextAnalyticsClientBuilder()
                .credential(new AzureKeyCredential(key))
                .endpoint(endpoint)
                .buildClient();
    }
    // Example method for recognizing entities and providing a link to an online data source
    static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
    {
        // The text that need be analyzed.
        String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
                "to develop and sell BASIC interpreters for the Altair 8800. " +
                "During his career at Microsoft, Gates held the positions of chairman, " +
                "chief executive officer, president and chief software architect, " +
                "while also being the largest individual shareholder until May 2014.";

        System.out.printf("Linked Entities:%n");
        for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
            System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
                    linkedEntity.getName(),
                    linkedEntity.getDataSourceEntityId(),
                    linkedEntity.getUrl(),
                    linkedEntity.getDataSource());
            System.out.printf("Matches:%n");
            for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
                System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
                        linkedEntityMatch.getText(),
                        linkedEntityMatch.getConfidenceScore(),
                        linkedEntityMatch.getOffset(),
                        linkedEntityMatch.getLength());
            }
        }
    }
}

```

### <a name="output"></a>Output

```console
Linked Entities:

Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 0, Length: 9
Text: Microsoft, Score: 0.55, Offset: 150, Length: 9
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 25, Length: 10
Text: Gates, Score: 0.63, Offset: 161, Length: 5
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 40, Length: 10
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 54, Length: 7
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 89, Length: 5
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 116, Length: 11
```
