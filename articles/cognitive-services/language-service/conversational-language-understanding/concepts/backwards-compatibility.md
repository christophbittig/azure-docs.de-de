---
title: Abwärtskompatibilität von Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Informationen zur Abwärtskompatibilität zwischen LUIS und Conversational Language Understanding
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: f4c354780cd5d32c2c801b76bde2b8b6abd2d555
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101373"
---
# <a name="backwards-compatibility-with-luis-applications"></a>Abwärtskompatibilität mit LUIS-Anwendungen

Sie können einen Teil des Inhalts Ihrer vorhandenen LUIS-Anwendungen in Conversational Language Understanding wiederverwenden. Bei der Arbeit mit Conversational Language Understanding-Projekten haben Sie diese Möglichkeiten:
* Erstellen von CLU-Unterhaltungsprojekten aus JSON-Dateien der LUIS-Anwendung.
* Erstellen von LUIS-Anwendungen, die mit Orchestrierungsworkflow-Projekten verbunden werden können.  
  
> [!NOTE]
> In diesem Leitfaden wird davon ausgegangen, dass Sie eine Sprachressource erstellt haben. Wenn Sie gerade Ihre ersten Schritte mit dem Dienst tun, lesen Sie den [Schnellstartartikel](../quickstart.md). 

## <a name="import-a-luis-application-json-file-into-conversational-language-understanding"></a>Importieren der JSON-Datei einer LUIS-Anwendung in Conversational Language Understanding

Zum Importieren der JSON-Datei einer LUIS-Anwendung klicken Sie auf das Symbol neben **Neues Projekt erstellen**, und wählen Sie **Importieren** aus. Wählen Sie dann die LUIS-Datei aus. Wenn Sie ein neues Projekt in Conversational Language Understanding importieren, können Sie eine exportierte JSON-Datei der LUIS-Anwendung auswählen, dann erstellt der Dienst automatisch ein Projekt mit den aktuell verfügbaren Features.

:::image type="content" source="../media/import.png" alt-text="Screenshot der Schaltfläche „Importieren“ für Unterhaltungsprojekte." lightbox="../media/import.png":::

### <a name="supported-features"></a>Unterstützte Features
Beim Importieren der LUIS JSON-Anwendung in CLU wird ein Projekt **Unterhaltungen** erstellt, in dem die folgenden Features ausgewählt sind:

|**Feature**|**Hinweise**|
| :- | :- |
|Absichten|Alle Ihre Absichten werden als CLU-Absichten mit den gleichen Namen übertragen.|
|ML-Entitäten|Alle Ihre ML-Entitäten werden als CLU-Entitäten mit den gleichen Namen übertragen. Die ML Bezeichnungen werden beibehalten und zum Trainieren der gelernten Komponente der Entität verwendet. Strukturierte ML-Entitäten werden nur als Entität der obersten Ebene übertragen. Die einzelnen Unterentitäten werden ignoriert.|
|Äußerungen|Alle Ihre LUIS-Äußerungen werden als CLU-Äußerungen mit ihren Absichten und Entitätsbezeichnungen übertragen. Strukturierte ML-Entitätsbezeichnungen berücksichtigen nur die Entitätsbezeichnungen der obersten Ebene, und die einzelnen untergeordneten Entitätsbezeichnungen werden ignoriert.|
|culture|Die primäre Sprache des Unterhaltungsprojekts entspricht der Kultur der LUIS-App. Wenn die Kultur nicht unterstützt wird, tritt beim Import ein Fehler auf. |

### <a name="unsupported-features"></a>Nicht unterstützte Funktionen

Beim Importieren der LUIS JSON-Anwendung in CLU werden bestimmte Features ignoriert, aber sie blockieren nicht den Import der Anwendung. Die folgenden Features werden ignoriert:

|**Feature**|**Hinweise**|
| :- | :- |
|Anwendungseinstellungen|Die Einstellungen wie „Interpunktion normalisieren“, „Diakritische Zeichen normalisieren“ und „Alle Trainingsdaten verwenden“ dienten dazu, Vorhersagen für Absichten und Entitäten zu verbessern. Die neuen Modelle in CLU reagieren nicht auf kleine Änderungen wie Interpunktion, daher sind sie nicht als Einstellungen verfügbar.|
|Features|Features von Ausdruckslisten und Absichten werden sämtlich ignoriert. Features sollten in LUIS ein semantisches Verständnis einführen, das CLU mit seinen neuen Modellen aus dem Stand bieten kann.|
|Muster|Muster wurden verwendet, um fehlende Qualität bei der Absichtsklassifizierung abzudecken. Von den neuen Modellen in CLU wird auch ohne die Nutzung von Mustern eine bessere Leistung erwartet.|
|Pattern.Any-Entitäten|Pattern.Any-Entitäten wurden verwendet, um fehlende Qualität bei der ML-Entitätsextraktion auszugleichen. Von den neuen Modellen in CLU wird auch ohne die Nutzung von Pattern.Any-Entitäten eine bessere Leistung erwartet.|
|Regex-Entitäten| Derzeit nicht unterstützt |
|Strukturierte ML-Entitäten| Derzeit nicht unterstützt |
|Auflisten von Entitäten | Derzeit nicht unterstützt |
|Vordefinierte Entitäten | Derzeit nicht unterstützt |
|Erforderliche Entitätsfeatures in ML-Entitäten | Derzeit nicht unterstützt |
|Nicht erforderliche Entitätsfeatures in ML-Entitäten | Derzeit nicht unterstützt |
|Rollen | Derzeit nicht unterstützt |

## <a name="use-a-published-luis-application-in-conversational-language-understanding-orchestration-projects"></a>Verwenden einer veröffentlichten LUIS-Anwendung in Conversational Language Understanding-Orchestrierungsprojekten

Sie können nur Verbindungen mit veröffentlichten LUIS-Anwendungen herstellen, die sich im Besitz der gleichen Sprachressource befinden, die Sie für Conversational Language Understanding verwenden. Sie können die Erstellungsressource in eine **S**-Sprachressource in Anwendungen für **Europa, Westen** ändern. Schritte zum Zuweisen einer anderen Ressource zu Ihrer LUIS-Anwendung finden Sie in der [LUIS-Dokumentation](../../../luis/luis-how-to-azure-subscription.md#assign-luis-resources). Darüber hinaus können Sie die LUIS-Anwendungen exportieren und sie anschließend in Ihre Sprachressource importieren. Sie müssen LUIS-Anwendungen trainieren und veröffentlichen, damit sie in Conversational Language Understanding angezeigt werden, wenn Sie sie mit Orchestrierungsprojekten verbinden möchten.


## <a name="next-steps"></a>Nächste Schritte

[Conversational Language Understanding – Übersicht](../overview.md)
