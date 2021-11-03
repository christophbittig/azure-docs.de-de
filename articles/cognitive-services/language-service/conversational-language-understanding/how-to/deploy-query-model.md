---
title: Versenden eines Conversational Language Understanding-Auftrags
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über das Senden einer Anforderung für Conversational Language Understanding.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: e1f53678ef99fb5477700628e14127e5572da816
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095033"
---
# <a name="deploy-and-test-model"></a>Bereitstellen und Testen des Modells

Nachdem Sie ein Modell in Ihrem Dataset [trainiert haben](./train-model.md), können Sie es nun bereitstellen. Nachdem Sie Ihr Modell bereitgestellt haben, können Sie es nach Vorhersagen abfragen. 

## <a name="deploy-model"></a>Bereitstellen des Modells

Bei der Bereitstellung eines Modells wird es gehostet und für Vorhersagen über einen Endpunkt zur Verfügung gestellt. Sie können nur ein bereitgestelltes Modell pro Projekt verwenden. Durch die Bereitstellung eines anderen Modells wird das zuvor bereitgestellte Modell überschrieben.

Wenn ein Modell bereitgestellt wird, können Sie das Modell direkt im Portal oder durch Aufrufen der zugeordneten API testen.

Wählen Sie einfach ein Modell aus, und klicken Sie auf der Seite „Modell bereitstellen“ auf „Modell bereitstellen“. 

:::image type="content" source="../media/deploy-model.png" alt-text="Screenshot: Seite „Modellbereitstellung“ in Language Studio" lightbox="../media/deploy-model.png":::

**Bereitstellungen von Orchestrierungsworkflowprojekten**

Wenn Sie ein Orchestrierungsworkflowprojekt bereitstellen, wird ein kleines Fenster angezeigt, in dem Sie Ihre Bereitstellung bestätigen und Parameter für verbundene Dienste konfigurieren können.

Wenn Sie eine Verbindung mit einer oder mehreren LUIS-Anwendungen herstellen, geben Sie den Bereitstellungsnamen an und ob Sie eine Bereitstellung des Typs *Slot* oder *Version* verwenden.       
* Der Bereitstellungstyp *Slot* erfordert, dass Sie zwischen einem Produktions- und Stagingslot auswählen.
* Der Bereitstellungstyp *Version* erfordert, dass Sie die Version angeben, die Sie veröffentlicht haben.

Für benutzerdefinierte Fragen und Antworten sowie CLU-Verbindungen oder nicht verknüpfte Absichten sind keine Konfigurationen erforderlich.

LUIS-Projekte **müssen in dem Slot veröffentlicht werden**, der während der Orchestrierungsbereitstellung konfiguriert wurde, und benutzerdefinierte Fragen zur Beantwortung von KBs müssen auch in ihren Produktionsslots veröffentlicht werden.

:::image type="content" source="../media/deploy-connected-services.png" alt-text="Screenshot: Bereitstellungsbildschirm für Orchestrierungsworkflowprojekte" lightbox="../media/deploy-connected-services.png":::

## <a name="send-a-conversational-language-understanding-request"></a>Versenden einer Conversational Language Understanding-Anforderung

Nachdem Ihr Modell bereitgestellt wurde, können Sie mit der Verwendung des bereitgestellten Modells für Vorhersagen beginnen. Außerhalb der Seite „Testmodell“ können Sie mit dem Aufrufen Ihres bereitgestellten Modells über API-Anforderungen an Ihren bereitgestellten benutzerdefinierten Endpunkt beginnen. Diese Endpunktanforderung ruft die Absichts- und Entitätsvorhersagen ab, die innerhalb des Modells definiert sind.

Sie können die vollständige URL für Ihren Endpunkt abrufen, indem Sie zur Seite **Modell bereitstellen** wechseln, Ihr bereitgestelltes Modell auswählen und auf „Vorhersage-URL abrufen“ klicken.

:::image type="content" source="../media/prediction-url.png" alt-text="Screenshot: Vorhersageanforderung und URL" lightbox="../media/prediction-url.png":::

## <a name="api-response-for-a-conversations-project"></a>API-Antwort für ein Konversationsprojekt

In einem Konversationsprojekt erhalten Sie Vorhersagen für Ihre Absichten und Entitäten, die in Ihrem Projekt vorhanden sind. 
- Die Absichten und Entitäten enthalten eine Zuverlässigkeitsbewertung zwischen 0,0 und 1,0, die damit verbunden ist, wie sicher das Modell ist, ein bestimmtes Element in Ihrem Projekt vorhersagen zu können. 
- Die Absicht mit der höchsten Bewertung ist in ihrem eigenen Parameter enthalten.
- In Ihrer Antwort werden nur vorhergesagte Entitäten angezeigt.
- Entitäten geben Folgendes an:
    - Den Text der Entität, die extrahiert wurde
    - Die Startposition, die durch einen Offsetwert gekennzeichnet ist
    - Die Länge des Entitätstexts, der durch einen Längenwert bezeichnet wird

## <a name="api-response-for-an-orchestration-workflow-project"></a>API-Antwort für eine Orchestrierungsworkflowprojekt

Ein Orchestrierungsworkflowprojekt wird mit der Antwort der Absicht mit der höchsten Bewertung und der Antwort des Diensts zurückgegeben, mit dem es verbunden ist.
- Innerhalb der Absicht können Sie mit dem Parameter *targetKind* den Typ der Antwort bestimmen, die von der obersten Absicht des Orchestrators zurückgegeben wurde (Konversation, LUIS oder QnA Maker).
- Sie erhalten die Antwort des verknüpften Diensts im Parameter *results*. 

Innerhalb der Anforderung können Sie zusätzliche Parameter für jeden verbundenen Dienst angeben, falls der Orchestrator an diesen Dienst weiterleitet.
- Innerhalb der Projektparameter können Sie optional eine andere Abfrage für den verbundenen Dienst angeben. Wenn Sie keine andere Abfrage angeben, wird die ursprüngliche Abfrage verwendet.
- Mit dem Parameter *direct target* können Sie die Routingentscheidung des Orchestrators umgehen und direkt auf eine bestimmte verbundene Absicht abzielen, um eine Antwort dafür zu erzwingen.

## <a name="next-steps"></a>Nächste Schritte

* [Conversational Language Understanding – Übersicht](../overview.md)
