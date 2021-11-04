---
title: 'Präzise Antworten mithilfe der Erkennung von Antwortspannen: Fragen und Antworten'
description: Hier erfahren Sie mehr über das Feature für präzise Antworten, das in Fragen und Antworten verfügbar ist.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 432e65d3d2f3dc3d4bc71157f165311e7fad87e9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476659"
---
# <a name="precise-answering"></a>Präzise Antworten

Das eingeführte Feature „Präzise Antwort“ ermöglicht es Ihnen, für jede Benutzerabfrage eine präzise Kurzantwort von der geeigneten Antwortpassage zu erhalten, die in der Wissensdatenbank vorhanden ist. Dieses Feature verwendet ein Deep Learning-Modell zur Laufzeit, das die Absicht der Benutzerabfrage versteht und die präzise Kurzantwort aus der Antwortpassage erkennt, wenn eine Kurzantwort als Fakt in der Antwortpassage vorhanden ist.

Dieses Feature ist sowohl für Contententwickler als auch für Endbenutzer von Vorteil. Jetzt müssen Contententwickler nicht mehr manuell bestimmte Frage-Antwort-Paare für jeden in der Wissensdatenbank vorhandenen Fakt kuratieren, und der Endbenutzer muss nicht mehr die gesamte vom Dienst zurückgegebene Antwortpassage überprüfen, um den tatsächlichen Fakt zu finden, der die Abfrage des Benutzers beantwortet.

## <a name="precise-answering-via-the-portal"></a>Präzise Antwort über das Portal

Wenn Sie im [Language Studio-Portal](https://aka.ms/languageStudio) den Testbereich öffnen, wird oben unter „Erweiterte Optionen anzeigen“ die Option **Kurze Antwort einbeziehen** angezeigt.

Wenn Sie eine Frage in den Testbereich eingeben, sehen Sie eine Kurzantwort zusammen mit der Antwortpassage, wenn in der Antwortpassage eine Kurzantwort vorhanden ist.

>[!div class="mx-imgBorder"]
>[![Screenshot: Testbereich mit aktivierter Option für kurze Antwort und einer Frage mit einer kurzen Antwort](../media/precise-answering/short-answer.png)](../media/precise-answering/short-answer.png#lightbox)

Sie können die Option zum **Einbeziehen der Kurzantwort** deaktivieren, wenn Sie nur die Passage mit der **langen Antwort** im Testbereich anzeigen möchten.

Der Dienst gibt auch die Zuverlässigkeitsbewertung der präzisen Antwort als **Zuverlässigkeitsbewertung der Antwortspanne** zurück, die Sie überprüfen können, indem Sie die Option **Überprüfen** und dann **Weitere Informationen** auswählen.

>[!div class="mx-imgBorder"]
>[![Screenshot: Überprüfungsbereich mit angezeigter Zuverlässigkeitsbewertung für die Antwortspanne](../media/precise-answering/answer-confidence-score.png)](../media/precise-answering/answer-confidence-score.png#lightbox)

## <a name="deploying-a-bot"></a>Bereitstellen eines Bots

Wenn Sie einen Bot veröffentlichen, erhalten Sie in Ihrer Anwendung standardmäßig die Umgebung mit aktivierten präzisen Antworten, wobei eine Kurzantwort zusammen mit der Antwortpassage angezeigt wird. Informationen zur Verwendung der präzisen Antwort (AnswerSpan) in der Antwort finden Sie in der API-Referenz für die REST-API. Benutzer haben die Flexibilität, andere Umgebungen auszuwählen, indem sie die Vorlage über den Bot-App-Dienst aktualisieren.
