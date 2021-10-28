---
title: Lebenszyklus einer Wissensdatenbank – QnA Maker
description: QnA Maker lernt am besten in einem iterativen Zyklus aus Modelländerungen, Ausdrucksbeispielen, Veröffentlichungen und dem Sammeln von Daten aus Endpunktabfragen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 6a41a2a522d082fa04c60cd58d095ab9b20401f9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229916"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Lebenszyklus einer Wissensdatenbank in QnA Maker
QnA Maker lernt am besten in einem iterativen Zyklus aus Modelländerungen, Ausdrucksbeispielen, Veröffentlichungen und dem Sammeln von Daten aus Endpunktabfragen.

![Erstellungszyklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Erstellen einer QnA Maker-Knowledge Base
Der Endpunkt der QnA Maker-Knowledge Base (KB) stellt die am besten übereinstimmende Antwort auf eine Benutzerfrage basierend auf dem Inhalt der Knowledge Base bereit. Das Erstellen einer Wissensdatenbank ist eine einmalige Aktion zum Einrichten eines Inhaltsrepositorys mit Fragen, Antworten und zugeordneten Metadaten. Eine Wissensdatenbank kann durch das Crawlen bereits vorhandener Inhalte, wie z. B. der folgenden Quellen, erstellt werden:

- FAQ-Seiten
- Produkthandbücher
- Frage-Antwort-Paare

Erfahren Sie mehr über das [Erstellen einer Knowledge Base](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testen und Aktualisieren der Knowledge Base

Die Knowledge Base ist bereit für das Testen, nachdem sie mit manuell bearbeiteten oder automatisch extrahierten Inhalten aufgefüllt wurde. Interaktive Tests können im QnA Maker-Portal über den Bereich **Test** ausgeführt werden. Sie geben allgemeine Benutzerfragen ein. Dann überprüfen Sie, ob die Antworten sowohl mit der richtigen Aussage als auch mit einem ausreichenden Zuverlässigkeitsgrad zurückgegeben werden.


* **Optimieren niedriger Zuverlässigkeitsbewertungen**: Fügen Sie alternative Fragen hinzu.
* **Wenn eine Abfrage fälschlicherweise die [Standardantwort zurückgibt](../How-to/change-default-answer.md)**: Fügen Sie neue Antworten auf die richtige Frage hinzu.

Diese nahe Abfolge von Tests und Updates wird so lange fortgesetzt, bis Sie mit den Ergebnissen zufrieden sind. Erfahren Sie mehr über das [Testen Ihrer Knowledge Base](../How-To/test-knowledge-base.md).

Verwenden Sie für große Wissensdatenbanken automatisierte Tests mit der [generateAnswer-API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) und der `isTest`-Texteigenschaft, die die `test`-Wissensdatenbank anstelle der veröffentlichten Wissensdatenbank abfragt.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Veröffentlichen der Wissensdatenbank
Nachdem Sie die Tests der Knowledge Base abgeschlossen haben, können Sie diese veröffentlichen. Beim Veröffentlichen wird die neueste Version der getesteten Knowledge Base in einen dedizierten Azure Cognitive Search-Index gepusht, der die **veröffentlichte** Knowledge Base darstellt. Außerdem wird ein Endpunkt erstellt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.

Nach der Veröffentlichung bleibt die veröffentlichte Version von weiteren Änderungen an der Testversion der Wissensdatenbank unberührt. Die veröffentlichte Version kann in einer Produktionsanwendung ausgeführt werden.

Jede dieser Knowledge Bases kann zu Testzwecken separat angesprochen werden. Mithilfe der APIs und der `isTest`-Texteigenschaft im generateAnswer-Aufruf können Sie die Testversion der Wissensdatenbank als Ziel verwenden.

Erfahren Sie mehr über das [Veröffentlichen Ihrer Knowledge Base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Überwachen der Nutzung
Um die Chatprotokolle Ihres Diensts erfassen zu können, müssen Sie beim [Erstellen Ihres QnA Maker-Diensts](../How-To/set-up-qnamaker-service-azure.md) Application Insights aktivieren.

Sie können verschiedene Analysen Ihrer Dienstnutzung abrufen. Erfahren Sie mehr über die Verwendung von Application Insights zum Abrufen von [Analysen zu Ihrem QnA Maker-Dienst](../How-To/get-analytics-knowledge-base.md).

Je nach den Ergebnissen Ihrer Analysen können Sie entsprechende [Änderungen an Ihrer Knowledge Base](../How-To/edit-knowledge-base.md) vornehmen.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versionskontrolle für Daten in Ihrer Wissensdatenbank

Die Versionskontrolle für Daten wird über die Import/Export-Funktion auf der Seite **Einstellungen** im QnA Maker-Portal bereitgestellt.

Sie können eine Wissensdatenbank sichern, indem Sie die Wissensdatenbank exportieren, entweder im `.tsv`- oder im `.xls`-Format. Schließen Sie diese Datei nach dem Exportieren in die reguläre Überprüfung Ihrer Quellcodeverwaltung ein.

Wenn Sie zu einer bestimmten Version zurückkehren müssen, müssen Sie diese Datei aus Ihrem lokalen System importieren. Eine exportierte Wissensdatenbank **darf nur** über den Import auf der Seite **Einstellungen** verwendet werden. Sie kann nicht als Datei- oder URL-Dokumentdatenquelle verwendet werden. Dadurch werden Fragen und Antworten, die sich derzeit in der Wissensdatenbank befinden, durch den Inhalt der importierten Datei ersetzt.

## <a name="test-and-production-knowledge-base"></a>Test- und Produktionsversion der Wissensdatenbank
Eine Wissensdatenbank ist das Repository der mithilfe von QnA Maker erstellten, verwalteten und verwendeten Frage-Antwort-Sätze. Jede QnA Maker-Ressource kann mehrere Wissensdatenbanken enthalten.

Eine Wissensdatenbank verfügt über zwei Statuswerte: *Testversion* und *Veröffentlicht*.

### <a name="test-knowledge-base"></a>Testversion der Wissensdatenbank

Die *Testwissensdatenbank* ist die aktuell bearbeitete und gespeicherte Version. Die Testversion wurde auf Genauigkeit und Vollständigkeit der Antworten geprüft. Änderungen an der Testversion der Wissensdatenbank wirken sich nicht auf die Benutzer Ihrer Anwendung oder Ihres Chatbots aus. Die Testversion der Wissensdatenbank wird in der HTTP-Anforderung als `test` bezeichnet. Die `test`-Wissensdatenbank steht im interaktiven **Test**-Bereich im QnA Maker-Portal zur Verfügung.

### <a name="production-knowledge-base"></a>Produktionsversion der Wissensdatenbank

Die *veröffentlichte Wissensdatenbank* ist die Version, die in Ihrem Chatbot oder Ihrer Anwendung verwendet wird. Durch die Veröffentlichung einer Wissensdatenbank wird der Inhalt der Testversion in die veröffentlichte Version übernommen. Die veröffentlichte Wissensdatenbank ist die Version, die die Anwendung über den Endpunkt verwendet. Stellen Sie sicher, dass der Inhalt korrekt und sorgfältig geprüft ist. Die veröffentlichte Wissensdatenbank wird in der HTTP-Anforderung als `prod` bezeichnet.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vorschläge für aktives Lernen](../index.yml)