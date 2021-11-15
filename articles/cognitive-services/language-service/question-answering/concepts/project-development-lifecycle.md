---
title: 'Projektlebenszyklus: Fragen und Antworten'
description: Fragen und Antworten lernt am besten in einem iterativen Zyklus aus Modelländerungen, Äußerungsbeispielen, Bereitstellungen und dem Sammeln von Daten aus Endpunktabfragen.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 193d01adac85681c39554ec1f6cab24dce9082f0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478952"
---
# <a name="question-answering-project-lifecycle"></a>Projektlebenszyklus bei Fragen und Antworten

Fragen und Antworten lernt am besten in einem iterativen Zyklus aus Modelländerungen, Äußerungsbeispielen, Bereitstellungen und dem Sammeln von Daten aus Endpunktabfragen.

## <a name="creating-a-project"></a>Erstellen eines Projekts

Projekte/Wissensdatenbanken von Fragen und Antworten stellen die basierend auf dem Inhalt des Projekts optimale Antwort auf eine Benutzerabfrage bereit. Das Erstellen eines Projekts ist eine einmalige Aktion zum Einrichten eines Inhaltsrepositorys mit Fragen, Antworten und zugeordneten Metadaten. Ein Projekt kann durch das Crawlen bereits vorhandener Inhalte, wie z. B. der folgenden Quellen, erstellt werden:

- FAQ-Seiten
- Produkthandbücher
- Frage-Antwort-Paare

Erfahren Sie mehr über das [Erstellen einer Knowledge Base](../how-to/create-test-deploy.md).

## <a name="testing-and-updating-your-project"></a>Testen und Aktualisieren Ihres Projekts

Das Projekt ist bereit für das Testen, nachdem es mit manuell bearbeiteten oder automatisch extrahierten Inhalten aufgefüllt wurde. Interaktive Tests können im Language Studio-Portal im Menü zu benutzerdefinierten Fragen und Antworten über den Bereich **Test** durchgeführt werden. Sie geben allgemeine Benutzerfragen ein. Dann überprüfen Sie, ob die Antworten sowohl mit der richtigen Aussage als auch mit einem ausreichenden Zuverlässigkeitsgrad zurückgegeben werden.

* **Optimieren niedriger Zuverlässigkeitsbewertungen**: Fügen Sie alternative Fragen hinzu.
* **Wenn eine Abfrage fälschlicherweise die [Standardantwort zurückgibt](../How-to/change-default-answer.md)**: Fügen Sie neue Antworten auf die richtige Frage hinzu.

Diese nahe Abfolge von Tests und Updates wird so lange fortgesetzt, bis Sie mit den Ergebnissen zufrieden sind.

## <a name="deploy-your-project"></a>Bereitstellen des Projekts

Nachdem Sie das Projekt getestet haben, können Sie es in der Produktion bereitstellen. Beim Bereitstellen wird die neueste Version des getesteten Projekts in einen dedizierten Azure Cognitive Search-Index gepusht, der das **veröffentlichte** Projekt bzw. die veröffentlichte Wissensdatenbank darstellt. Außerdem wird ein Endpunkt erstellt, der in Ihrer Anwendung oder Ihrem Chatbot aufgerufen werden kann.

Nach der Bereitstellung bleibt die veröffentlichte Version von weiteren Änderungen an der Testversion der Wissensdatenbank unberührt. Die veröffentlichte Version kann in einer Produktionsanwendung ausgeführt werden.

Sämtliche Projekte/Wissensdatenbanken können zu Testzwecken separat angesprochen werden.

## <a name="monitor-usage"></a>Überwachen der Nutzung

Um die Chatprotokolle Ihres Diensts protokollieren und zusätzliche Analysen abrufen zu können, müssen Sie [Azure Monitor Diagnoseprotokolle](../how-to/analytics.md) aktivieren, nachdem Sie Ihre Sprachressource erstellt haben.

Je nach den Ergebnissen Ihrer Analysen können Sie entsprechende Änderungen an Ihrem Projekt vornehmen.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versionskontrolle für Daten in Ihrer Wissensdatenbank

Die Versionskontrolle für Daten wird über die Import-/Exportfunktionen auf der Projektseite im Abschnitt „Fragen und Antworten“ im Language Studio-Portal bereitgestellt.

Sie können Projekte/Wissensdatenbanken sichern, indem Sie da Projekt exportieren, entweder im `.tsv`- oder im `.xls`-Format. Schließen Sie diese Datei nach dem Exportieren in die reguläre Überprüfung Ihrer Quellcodeverwaltung ein.

Wenn Sie zu einer bestimmten Version zurückkehren müssen, müssen Sie diese Datei aus Ihrem lokalen System importieren. Ein exportiertes Projekt **darf nur** über den Import auf der Projektseite verwendet werden. Sie kann nicht als Datei- oder URL-Dokumentdatenquelle verwendet werden. Dadurch werden Fragen und Antworten, die sich derzeit in der Wissensdatenbank befinden, durch den Inhalt der importierten Datei ersetzt.

## <a name="test-and-production-project"></a>Test- und Produktionsversion des Projekts

Ein Projekt/eine Wissensdatenbank ist das Repository der mithilfe von Fragen und Antworten erstellten, verwalteten und verwendeten Frage-Antwort-Sätze. Jede Sprachressource kann mehrere Projekte/Wissensdatenbanken enthalten.

Ein Projekt/eine Wissensdatenbank verfügt über zwei Statuswerte: *Testversion* und *Veröffentlicht*.

### <a name="test-projectknowledge-base"></a>Testversion des Projekts/der Wissensdatenbank

Die *Testwissensdatenbank* ist die aktuell bearbeitete und gespeicherte Version. Die Testversion wurde auf Genauigkeit und Vollständigkeit der Antworten geprüft. Änderungen an der Testversion der Wissensdatenbank wirken sich nicht auf die Benutzer Ihrer Anwendung oder Ihres Chatbots aus. Die Testversion der Wissensdatenbank wird in der HTTP-Anforderung als `test` bezeichnet. Die `test`-Wissensdatenbank steht im interaktiven Bereich **Test** von Language Studio zur Verfügung.

### <a name="production-projectknowledge-base"></a>Produktionsversion des Projekts/der Wissensdatenbank

Die *veröffentlichte Wissensdatenbank* ist die Version, die in Ihrem Chatbot oder Ihrer Anwendung verwendet wird. Durch die Veröffentlichung einer Wissensdatenbank wird der Inhalt der Testversion in die veröffentlichte Version übernommen. Die veröffentlichte Wissensdatenbank ist die Version, die die Anwendung über den Endpunkt verwendet. Stellen Sie sicher, dass der Inhalt korrekt und sorgfältig geprüft ist. Die veröffentlichte Wissensdatenbank wird in der HTTP-Anforderung als `prod` bezeichnet.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vorschläge für aktives Lernen](../tutorials/active-learning.md)