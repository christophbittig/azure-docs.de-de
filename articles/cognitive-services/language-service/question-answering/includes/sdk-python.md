---
title: 'Schnellstart: Clientbibliothek für Fragen und Antworten für Python'
description: In dieser Schnellstartanleitung werden die ersten Schritte mit der Clientbibliothek für Fragen und Antworten für Python gezeigt.
ms.topic: include
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/11/2021
ms.openlocfilehash: 0aa0f65e9a277cbcb28870b970ead3e357057b2f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354107"
---
Diese Schnellstartanleitung zur Clientbibliothek für Fragen und Antworten für Python ermöglicht Folgendes:

* Erhalten einer Antwort auf der Grundlage einer Wissensdatenbank
* Erhalten einer Antwort auf der Grundlage eines Texts, den Sie zusammen mit Ihrer Frage übermitteln
* Erhalten der Zuverlässigkeitsbewertung für die Antwort auf Ihre Frage

[API-Referenzdokumentation][questionanswering_refdocs] | [Quellcode][questionanswering_client_src] | [Paket (PyPI)][questionanswering_pypi_package] | [Python-Beispiele][questionanswering_samples] |

[questionanswering_client_class]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/
[questionanswering_pypi_package]: https://pypi.org/project/azure-ai-language-questionanswering/
[questionanswering_refdocs]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/samples/README.md

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Für Fragen und Antworten wird eine [Sprachressource](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“ benötigt, um einen API-Schlüssel und Endpunkt zu generieren.
    * Wählen Sie nach der Bereitstellung der Sprachressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung mit der API herstellen zu können. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
* Eine bereits vorhandene Wissensdatenbank für die Abfrage. Eine Anleitung zum Einrichten einer Wissensdatenbank finden Sie bei Bedarf unter [**Schnellstart: Fragen und Antworten**](../quickstart/sdk.md). Sie können aber auch eine Wissensdatenbank hinzufügen, die [diese URL für das Surface-Benutzerhandbuch](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf) als Datenquelle verwendet.

## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python, können Sie die Clientbibliothek mit Folgendem installieren:

```console
pip install azure-ai-language-questionanswering
```

## <a name="query-a-knowledge-base"></a>Abfragen einer Knowledge Base

### <a name="generate-an-answer-from-a-knowledge-base"></a>Generieren einer Antwort auf der Grundlage einer Wissensdatenbank

Im folgenden Beispiel können Sie eine Wissensdatenbank mithilfe von [get_answers](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers) abfragen, um eine Antwort auf Ihre Frage zu erhalten. Sie können den Code in eine dedizierte PY-Datei oder in eine Zelle in [Jupyter Notebook/Lab](https://jupyter.org/) kopieren.

Aktualisieren den im Anschluss bereitgestellten Code mit Ihren eigenen Werten für die folgenden Variablen:

|Variablenname | Wert |
|--------------------------|-------------|
| `endpoint`               | Diesen Wert finden Sie im Abschnitt **Schlüssel und Endpunkt**, wenn Sie die Ressource über das Azure-Portal untersuchen. Alternativ finden Sie den Wert unter **Language Studio** > **Fragen und Antworten** > **Wissensdatenbank bereitstellen** > **Vorhersage-URL abrufen**. Beispielendpunkt: `https://southcentralus.api.cognitive.microsoft.com/`|
| `credential` | Diesen Wert finden Sie im Abschnitt **Schlüssel und Endpunkt**, wenn Sie die Ressource über das Azure-Portal untersuchen. Sie können entweder „Schlüssel1“ oder „Schlüssel2“ verwenden. Es gibt immer zwei gültige Schlüssel für die sichere Schlüsselrotation ohne Downtime. Alternativ finden Sie den Wert unter **Language Studio** > **Fragen und Antworten** > **Wissensdatenbank bereitstellen** > **Vorhersage-URL abrufen**. Der Schlüsselwert ist Teil der Beispielanforderung.|
| `knowledge_base_project` | Der Name Ihres Projekts für Fragen und Antworten.|
| `deployment`             | Es gibt zwei mögliche Werte: `test` und `production`. `production` setzt voraus, dass Sie Ihre Wissensdatenbank über **Language Studio** > **Fragen und Antworten** > **Wissensdatenbank bereitstellen** bereitgestellt haben.|

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}")
knowledge_base_project = "{YOUR-PROJECT-NAME}"
deployment = "production"

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How much battery life do I have left?"
        output = client.get_answers(
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
    print("Q: {}".format(question))
    print("A: {}".format(output.answers[0].answer))

if __name__ == '__main__':
    main()
```

In diesem Beispiel werden die Variablen hartcodiert. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise bietet der [Azure-Schlüsseltresor](../../../../key-vault/general/overview.md) sichere Aufbewahrung von Schlüsseln.

Wenn Sie den obigen Code ausführen und die Datenquelle aus den Voraussetzungen verwenden, erhalten Sie eine Antwort wie die folgende:

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

Wenn Sie wissen möchten, wie zuverlässig die Antwort ist, können Sie im Anschluss an die bereits vorhandenen print-Anweisungen eine weitere print-Anweisung hinzufügen:

```python
print("Q: {}".format(question))
print("A: {}".format(output.answers[0].answer))
print("Confidence Score: {}".format(output.answers[0].confidence_score)) # add this line 
```

Nun erhalten Sie ein Ergebnis mit einer Zuverlässigkeitsbewertung:

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
Confidence Score: 0.9185
```

Die Zuverlässigkeitsbewertung gibt einen Wert zwischen 0 und 1 zurück. Multiplizieren Sie diesen Wert mit 100, um eine Prozentangabe zu erhalten. Eine Zuverlässigkeitsbewertung von 0,9185 bedeutet also beispielsweise, dass es sich hierbei mit einer Wahrscheinlichkeit von 91,85 Prozent um die richtige Antwort auf die Frage handelt (basierend auf der Wissensdatenbank).

Wenn Sie Antworten ausschließen möchten, deren Zuverlässigkeitsbewertung unter einem bestimmten Schwellenwert liegt, können Sie die Antwortoptionen ([AnswerOptions](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersOptions)) ändern, um den Parameter `confidence_threshold` hinzuzufügen.

```python
        output = client.get_answers(
            confidence_threshold = 0.95, #add this line
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
```

Da wir aufgrund der vorherigen Ausführung des Codes wissen, dass unsere Zuverlässigkeitsbewertung `.9185` lautet, wird die [Standardantwort](../how-to/change-default-answer.md) zurückgegeben, wenn der Schwellenwert auf `.95` festgelegt wird.

```
Q: How much battery life do I have left?
A: No good match found in KB
Confidence Score: 0.0
```

## <a name="query-text-without-a-knowledge-base"></a>Abfragen von Text ohne Wissensdatenbank

Fragen und Antworten kann mit [get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text) auch ohne Wissensdatenbank verwendet werden. In diesem Fall werden für Fragen und Antworten sowohl eine Frage als auch die zugehörigen Textdatensätze bereitgestellt, die nach einer Antwort durchsucht werden sollen, wenn die Anforderung übermittelt wird.

Für dieses Beispiel müssen nur die Variablen für `endpoint` und `credential` geändert werden.

```python
import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient
from azure.ai.language.questionanswering import models as qna

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY")

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How long does it takes to charge a surface?"
        input = qna.AnswersFromTextOptions(
            question=question,
            text_documents=[
                "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.",
                "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface.",
            ]
        )


        output = client.get_answers_from_text(input)

    best_answer = [a for a in output.answers if a.confidence > 0.9][0]
    print(u"Q: {}".format(input.question))
    print(u"A: {}".format(best_answer.answer))
    print("Confidence Score: {}".format(output.answers[0].confidence))

if __name__ == '__main__':
    main()
```

Sie können den Code in eine dedizierte PY-Datei oder in eine neue Zelle in [Jupyter Notebook/Lab](https://jupyter.org/) kopieren. In diesem Beispiel wird folgendes Ergebnis zurückgeben:

```
Q: How long does it takes to charge surface?
A: Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.
Confidence Score: 0.9254655838012695
```

Hier werden alle Antworten durchlaufen, und es wird nur die Antwort mit der höchsten Zuverlässigkeitsbewertung zurückgegeben, die größer als 0,9 ist. Weitere Informationen zu den verfügbaren Optionen für [get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text) finden Sie in den [AnswersFromTextOptions-Parametern](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersFromTextOptions).