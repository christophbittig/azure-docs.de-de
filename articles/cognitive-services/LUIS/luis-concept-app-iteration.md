---
title: Iteratives App-Design – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS lernt am besten in einem iterativen Zyklus aus Modelländerungen, Äußerungsbeispielen, Veröffentlichungen und dem Sammeln von Daten aus Endpunktabfragen.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 92991ef4b9889b0be2e887884ed46086b87ff9f3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588882"
---
# <a name="iterative-app-design-for-luis"></a>Iteratives App-Design für LUIS

Am effizientesten lernen und arbeiten LUIS-Apps (Language Understanding) in Iterationen. Dies ist ein typischer Iterationszyklus:

* Erstellen einer neuen Version
* Bearbeiten des LUIS-App-Schemas Dies schließt Folgendes ein:
    * Absichten mit Beispieläußerungen
    * Entitäten
    * Features
* Training, Test und Veröffentlichung
    * Testen am Vorhersageendpunkt für aktives Lernen
* Sammeln von Daten aus Endpunktabfragen

![Erstellungszyklus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Erstellen eines LUIS-Schemas

Mithilfe eines App-Schemas wird definiert, was der Benutzer fragt (_Absicht_ oder _Wunsch_) und welche Teile der Absicht Details (_Entitäten_) enthalten, mit deren Hilfe die Antwort bestimmt werden kann.

Das App-Schema muss spezifisch für die App-Domäne sein, damit Wörter und Ausdrücke ermittelt werden können, die relevant sind, und um die typische Reihenfolge zu bestimmen.

Beispielausdrücke stellen Benutzereingaben (z. B. erkannte Sprache oder Text) dar, die von der App zur Laufzeit erwartet werden.

Das Schema erfordert Absichten und _sollte über Entitäten verfügen_.

### <a name="example-schema-of-intents"></a>Beispielschema von Absichten

Das gängigste Schema ist ein Absichtsschema, das nach Absichten strukturiert ist. Dieser Schematyp nutzt LUIS, um die Absicht eines Benutzers zu bestimmen.

Der Schematyp der Absicht kann über Entitäten verfügen, wenn dies LUIS beim Bestimmen der Benutzerabsicht nützt. Beispielsweise unterstützt eine Versandentität (als _Machine Learning-Feature_ für eine Absicht) LUIS bei der Erkennung einer Versandabsicht.

### <a name="example-schema-of-entities"></a>Beispielschema von Entitäten

Ein Entitätsschema konzentriert sich auf die Entitäten, die ihrerseits die Daten darstellen, die aus den Benutzeräußerungen extrahiert werden sollen. Angenommen, ein Benutzer sagt: „Ich möchte drei Pizzas bestellen“. In diesem Fall werden zwei Entitäten extrahiert: _drei_ und _Pizzas_. Mit diesen wird der Zweck der Absicht erfüllt, nämlich das Aufgeben einer Bestellung.

Bei einem Entitätsschema ist die Absicht der Äußerung für die Clientanwendung weniger oder gar nicht wichtig.

### <a name="example-of-a-mixed-schema"></a>Beispiel für ein gemischtes Schema

Das leistungsfähigste und ausgereifteste Schema ist ein Absichtsschema mit einer umfassenden Palette an Entitäten und Features. Dieses Schema kann entweder als Absichts- oder als Entitätsschema beginnen und dann im weiteren Wachstum Konzepte von beiden einschließen, in dem Maß, wie die Clientanwendung diese Informationseinheiten benötigt.

## <a name="add-example-utterances-to-intents"></a>Hinzufügen von Beispieläußerungen zu Absichten

LUIS benötigt einige Beispieläußerungen in jeder **Absicht**. Für die Beispieläußerungen sind ausreichend viele Variationen bei der Wortauswahl und Wortreihenfolge erforderlich, um die Absicht der Äußerung zu bestimmen.

> [!CAUTION]
> Fügen Sie nicht massenhaft Beispieläußerungen hinzu. Beginnen Sie mit 15 bis 30 spezifischen und unterschiedlichen Beispielen.

Für jede Beispieläußerung müssen alle **erforderlichen zu extrahierenden Daten** mit **Entitäten** entworfen und bezeichnet sein.

|Schlüsselelement|Zweck|
|--|--|
|Intent|**Klassifizieren** von Benutzeräußerungen in einer einzelnen Absicht oder Aktion. Beispiele sind `BookFlight` und `GetWeather`.|
|Entität|**Extrahieren** von Daten aus Äußerungen, was für die Vervollständigung der Absicht erforderlich ist. Beispiele hierfür sind Datum und Uhrzeit der Reise und der Standort.|

Eine LUIS-App kann so entworfen sein, dass sie Äußerungen ignoriert, die für Ihre App-Domäne nicht relevant sind, indem die Äußerung der Absicht **None** zugewiesen wird.

## <a name="test-and-train-your-app"></a>Testen und Trainieren Ihrer App

Wenn Sie zu jeder Absicht 15 bis 30 verschiedene Beispieläußerungen haben und die erforderlichen Entitäten mit Bezeichnungen versehen sind, müssen Sie Ihre LUIS-App Testen und [trainieren](luis-how-to-train.md).

## <a name="publish-to-a-prediction-endpoint"></a>Veröffentlichung an einem Vorhersageendpunkt

Die LUIS-App muss veröffentlicht werden, damit sie in der Liste der [Vorhersageendpunkt-Regionen](luis-reference-regions.md) verfügbar ist.

## <a name="test-your-published-app"></a>Testen der veröffentlichten App

Sie können Ihre veröffentlichte LUIS-App vom HTTPS-Vorhersageendpunkt aus testen. Durch Tests vom Vorhersageendpunkt kann LUIS alle Äußerungen mit niedrigen Zuverlässigkeitswerten zur [Überprüfung](luis-how-to-review-endpoint-utterances.md) auswählen.

## <a name="create-a-new-version-for-each-cycle"></a>Erstellen einer neuen Version für jeden Zyklus

Jede Version ist eine Momentaufnahme der LUIS-App. Bevor Sie Änderungen an der App vornehmen, erstellen Sie eine neue Version. Es ist einfacher, zu einer älteren Version zurückzukehren, als Absichten und Äußerungen zu entfernen, um einen früheren Zustand wiederherzustellen.

Die Versions-ID besteht aus Zeichen, Ziffern oder „.“ und darf nicht länger als 10 Zeichen sein.

Die ursprüngliche Version (0.1) ist standardmäßig die aktive Version.

### <a name="begin-by-cloning-an-existing-version"></a>Klonen Sie zunächst eine vorhandene Version.

Klonen Sie eine vorhandene Version, um sie als Ausgangspunkt für eine neue Version zu verwenden. Nachdem Sie eine Version geklont haben, wird die neue Version die **aktive** Version.

### <a name="publishing-slots"></a>Veröffentlichungsslots

Sie können im Staging- und/oder im Produktionsslot veröffentlichen. Jeder Slot kann eine andere Version oder die gleiche Version aufweisen. Das ist nützlich, um Änderungen vor der Veröffentlichung in der Produktion zu überprüfen, die für Bots oder andere Apps verfügbar ist, die LUIS aufrufen.

Trainierte Versionen stehen nicht automatisch am [Endpunkt](luis-glossary.md#endpoint) Ihrer LUIS-App zur Verfügung. Sie müssen eine Version [veröffentlichen](luis-how-to-publish-app.md) oder erneut veröffentlichen, damit sie am Endpunkt der LUIS-App verfügbar ist. Sie können für das **Staging** und die **Produktion** veröffentlichen. Damit stehen Ihnen am Endpunkt zwei Versionen zur Verfügung. Wenn Sie weitere Versionen der App an einem Endpunkt benötigen, sollten Sie die Version exportieren und in eine neue App importieren. Die neue App hat eine andere App-ID.

### <a name="import-a-version"></a>Importieren einer Version

Eine Version kann wie folgt **importiert** werden:
* Als neue App mit einer neuen App-ID
* Als neue Version einer vorhandenen App

Diese Version wird die aktive Version und verwendet die Versions-ID in der `versionId`-Eigenschaft der App-Datei.

### <a name="export-a-version"></a>Exportieren einer Version

Eine Version kann über das LUIS-Portal entweder auf App-Ebene oder auf Versionsebene **exportiert** werden:

* App-Ebene: Wählen Sie auf der Seite **Meine Apps** die App und dann **Exportieren** aus.
* Versionsebene: Wählen Sie auf der Seite **Meine Apps** den App-Link und anschließend **Verwalten** und **Versionen** aus.

Der einzige Unterschied besteht darin, dass die auf App-Ebene exportierte Version die derzeit aktive Version ist, während Sie beim Exportieren auf Versionsebene auf der Seite **[Einstellungen](luis-how-to-manage-versions.md)** die zu exportierende Version auswählen können.

Die exportierte Datei enthält folgende Informationen **nicht**:

* Maschinell erlernte Informationen, da die App nach dem Importieren neu trainiert wird
* Informationen zu Mitwirkenden

Um Ihr LUIS-App-Schema zu sichern, exportieren Sie eine Version aus dem [LUIS-Portal](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Verwalten von Änderungen durch Mitwirkende mit Versionen und Mitwirkenden

LUIS bietet das Konzept der Mitwirkenden an einer App und stellt dazu Berechtigungen auf Azure-Ressourcenebene zur Verfügung. Kombinieren Sie dieses Konzept mit Versionsverwaltung, um gezielte Zusammenarbeit zu ermöglichen.

Verwenden Sie die folgenden Techniken zur Verwaltung von Änderungen durch Mitwirkende an ihrer App.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Verwalten mehrerer Versionen in derselben App

Zunächst [klonen](luis-how-to-manage-versions.md#clone-a-version) Sie für jeden Ersteller eine Version aus einer Basisversion.

Jeder Ersteller ändert seine eigene Version der App. Wenn jeder Ersteller mit seinem Modell zufrieden ist, werden die neuen Versionen in JSON-Dateien exportiert.

Exportierte Apps sowie Dateien mit den Endungen `.json` oder `.lu` können auf Änderungen verglichen werden. Kombinieren Sie die Dateien, um eine einzelne Datei der neuen Version zu erstellen. Ändern Sie die `versionId`-Eigenschaft, um die neue zusammengeführte Version zu kennzeichnen. Importieren Sie diese Version in die ursprüngliche App.

Diese Methode erlaubt eine aktive Version, eine Stagingversion und eine veröffentlichte Version. Sie können die Ergebnisse der aktiven Version mit einer veröffentlichten Version (Staging oder Produktion) im [Bereich für interaktives Testen](luis-interactive-test.md) vergleichen.

### <a name="manage-multiple-versions-as-apps"></a>Verwalten mehrerer Versionen als Apps

[Exportieren](luis-how-to-manage-versions.md#export-version) Sie die Basisversion. Jeder Ersteller importiert die Version. Die Person, die die App importiert, ist auch der Besitzer dieser Version. Wenn sie das Bearbeiten der App abgeschlossen hat, exportiert sie ihre Version.

Die exportierten Apps sind JSON-formatierte Dateien, die mit dem Basisexport auf Änderungen verglichen werden können. Kombinieren Sie die Dateien, um eine einzelne JSON-Datei der neuen Version zu erstellen. Ändern Sie die **versionId**-Eigenschaft in der JSON-Datei, um die neue zusammengeführte Version zu kennzeichnen. Importieren Sie diese Version in die ursprüngliche App.

Erfahren Sie mehr über das Erstellen von Beiträgen durch [Projektmitarbeiter](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Überprüfen von Endpunktäußerungen zum Einleiten des neuen Iterationszyklus

Wenn Sie einen Iterationszyklus abgeschlossen haben, können Sie den Vorgang wiederholen. Beginnen Sie mit dem [Überprüfen der Vorhersageendpunktäußerungen](luis-how-to-review-endpoint-utterances.md), die LUIS mit niedrigen Zuverlässigkeitswerten gekennzeichnet hat. Überprüfen Sie diese Äußerungen sowohl auf richtig vorhergesagte Absicht als auch auf richtig und vollständig extrahierte Entitäten. Nach dem Überprüfen und Akzeptieren der Änderungen sollte die Prüfliste leer sein.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die Konzepte der [Zusammenarbeit](luis-how-to-azure-subscription.md).
