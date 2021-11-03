---
title: Kontextfilter mithilfe von Metadaten
titleSuffix: Azure Cognitive Services
description: QnA Maker filtert Frage-Antwort-Paare nach Metadaten.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: e38baa47d22ebb1fbdd5127cbd415afc3027d8a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016849"
---
# <a name="filter-responses-with-metadata"></a>Filtern von Antworten mit Metadaten

QnA Maker ermöglicht es Ihnen, Ihren Frage-Antwort-Paaren Metadaten in Form von Schlüssel-Wert-Paaren hinzuzufügen. Mit diesen Informationen können Sie Ergebnisse nach Benutzerabfragen filtern und zusätzliche Informationen speichern, die in Folgekonversationen verwendet werden können.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Speichern von Fragen und Antworten mit einer QnA-Entität

Zunächst müssen Sie wissen, wie QnA Maker die Frage-Antwort-Daten speichert. Die folgende Abbildung zeigt eine QnA-Entität:

![Illustration einer QnA-Entität](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Jede QnA-Entität hat eine eindeutige und dauerhafte ID. Sie können die ID verwenden, um bestimmte QnA-Entitäten zu aktualisieren.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Verwenden von Metadaten zum Filtern von Antworten nach benutzerdefinierten Metadatentags

Durch das Hinzufügen von Metadaten können Sie die Antworten nach diesen Metadatentags filtern. Fügen Sie die Metadatenspalte aus dem Menü **Ansichtsoptionen** hinzu. Fügen Sie Ihrer Wissensdatenbank Metadaten hinzu, indem Sie auf das Metadatensymbol **+** klicken, um ein Metadatenpaar hinzuzufügen. Dieses Paar besteht aus einem Schlüssel und einen Wert.

![Screenshot: Hinzufügen von Metadaten](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtern von Ergebnissen mit „strictFilters“ nach Metadatentags

Betrachten Sie die Benutzerfrage „When does this hotel close?“ (Wann schließt dieses Hotel?); die Absicht zielt auf das Restaurant „Paradise“ ab.

Da nur Ergebnisse für das Restaurant „Paradise“ erforderlich sind, können Sie im GenerateAnswer-Aufruf einen Filter für die Metadaten zum Restaurantnamen festlegen. Dies wird im folgenden Beispiel veranschaulicht:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

## <a name="filter-by-source"></a>Nach Quelle filtern

Wenn Ihre Wissensdatenbank mehrere Inhaltsquellen enthält und Sie die Ergebnisse auf eine bestimmte Gruppe von Quellen beschränken möchten, verwenden Sie dazu das reservierte Schlüsselwort `source_name_metadata`, wie unten gezeigt.

```json
"strictFilters": [
    {
        "name": "category",
        "value": "api"
    },
   {
        "name": "source_name_metadata",
        "value": "boby_brown_docx"
    },
   {
        "name": "source_name_metadata",
        "value": "chitchat.tsv"
   }
]
```

---

### <a name="logical-and-by-default"></a>Standardmäßig logisches UND

Fügen Sie zum Kombinieren mehrerer Metadatenfilter in der Abfrage die zusätzlichen Metadatenfilter dem Array der `strictFilters`-Eigenschaft hinzu. Standardmäßig werden die Werte logisch kombiniert (UND). Für eine logische Kombination müssen alle Filter mit den F&A-Paaren übereinstimmen, damit das Paar in der Antwort zurückgegeben wird.

Dies entspricht der Verwendung der `strictFiltersCompoundOperationType`-Eigenschaft mit dem Wert `AND`.

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logisches ODER mit der strictFiltersCompoundOperationType-Eigenschaft

Wenn Sie mehrere Metadatenfilter kombinieren, aber nur einer oder einige der Filter übereinstimmen müssen, verwenden Sie die `strictFiltersCompoundOperationType`-Eigenschaft mit dem Wert `OR`.

Dadurch kann Ihre Wissensdatenbank Antworten zurückgeben, wenn einer der Filter übereinstimmt, Es werden aber keine Antworten zurückgegeben, die keine Metadaten enthalten.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Metadatenbeispiele in Schnellstarts

Weitere Informationen zu Metadaten finden Sie im Schnellstart zum QnA Maker Portal über Metadaten:
* [Dokumenterstellung: Hinzufügen von Metadaten zu Frage-Antwort-Paaren](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Abfragevorhersage: Filtern von Antworten nach Metadaten](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Verwenden von Frage und Antwortergebnis zum Beibehalten des Unterhaltungskontexts

Die Antwort auf den GenerateAnswer-Aufruf enthält die entsprechenden Metadateninformationen des übereinstimmenden Frage-Antwort-Paars. Sie können diese Informationen in Ihrer Clientanwendung verwenden, um den Kontext der vorherigen Unterhaltung für die Verwendung in späteren Unterhaltungen zu speichern.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Analysieren von Wissensdatenbanken](../How-to/get-analytics-knowledge-base.md)
