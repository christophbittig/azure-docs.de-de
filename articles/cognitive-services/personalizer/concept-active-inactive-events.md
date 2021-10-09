---
title: 'Aktive und inaktive Ereignisse: Personalisierung'
description: In diesem Artikel wird beschrieben, wie Sie aktive und inaktive Ereignisse im Personalisierungsdienst verwenden.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 948d375c0f580a71dbd27fa10660c0c7e0046a10
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219152"
---
# <a name="defer-event-activation"></a>Ereignisaktivierung aufschieben

Die aufgeschobene Aktivierung von Ereignissen ermöglicht es Ihnen, personalisierte Websites oder Mailing-Kampagnen zu erstellen, wobei Sie berücksichtigen, dass der Benutzer die Seite möglicherweise nie sieht oder die E-Mail nicht öffnet. In diesen Szenarien muss die Anwendung möglicherweise Rank aufrufen, bevor sie überhaupt weiß, ob das Ergebnis verwendet oder dem Benutzer angezeigt wird. Wenn der Inhalt dem Nutzer nie gezeigt wird, sollte keine Standardbelohnung (in der Regel Null) angenommen werden, aus der er lernen kann.
Die aufgeschobene Aktivierung ermöglicht es Ihnen, die Ergebnisse eines Rank-Aufrufs zu einem bestimmten Zeitpunkt zu verwenden und zu entscheiden, ob das Ereignis zu einem späteren Zeitpunkt oder an anderer Stelle in Ihrem Code gelernt werden soll.

## <a name="typical-scenarios-for-deferred-activation"></a>Typische Szenarien für eine aufgeschobene Aktivierung

Das Aufschieben der Aktivierung von Ereignissen ist in den folgenden Beispielszenarien nützlich:

* Sie bereiten eine personalisierte Webseite für einen Benutzer vor, aber der Benutzer bekommt sie möglicherweise nie zu sehen, weil eine Geschäftslogik die Aktionsauswahl des Personalizers außer Kraft setzt.
* Sie personalisieren Inhalte „below the fold“ auf einer Webseite, und es ist sehr wahrscheinlich, dass der Inhalt vom Benutzer nie gesehen wird.
* Sie personalisieren Marketing-E-Mails und müssen vermeiden, dass E-Mails, die von den Nutzern nie geöffnet wurden, zu einer Schulung führen.
* Sie haben einen dynamischen Medienkanal personalisiert und Ihre Benutzer hören möglicherweise auf, den Kanal abzuspielen, bevor er zu den von Personalizer ausgewählten Liedern oder Videos gelangt. 

Im Allgemeinen treten diese Szenarien auf, wenn:

* Sie rendern die Benutzeroberfläche vor, die der Benutzer aufgrund von UI- oder Zeitbeschränkungen möglicherweise nicht zu sehen bekommt.
* Ihre Anwendung führt eine prädiktive Personalisierung durch, bei der Sie Rangaufrufe tätigen, bevor Sie wissen, ob Sie die Ausgabe verwenden werden.

## <a name="how-to-defer-activation-and-later-activate-events"></a>Aufschieben der Aktivierung und spätere Aktivierung von Ereignissen

Um die Aktivierung für ein Ereignis aufzuschieben, rufen Sie im Textkörper des Vermächtnisses den Befehl [Rang](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) mit `deferActivation = True` auf.

Sobald Sie wissen, dass Ihren Nutzern der personalisierte Inhalt oder die Medien gezeigt wurden und die Erwartung einer Belohnung angemessen ist, müssen Sie dieses Ereignis aktivieren. Rufen Sie dazu die [Activate API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) mit der eventId auf.


Der Aufruf [API aktivieren](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) für diesen EventID-Aufruf muss empfangen werden, bevor das Zeitfenster für die Belohnungswartezeit abläuft.

### <a name="behavior-with-deferred-activation"></a>Verhalten bei aufgeschobener Aktivierung 

Der Personalizer lernt aus Ereignissen und Belohnungen wie folgt:
* Wenn Sie den Rang mit `deferActivation = True` aufrufen, und *nicht* die `Activate`API für diese eventId aufrufen, und Reward aufrufen, lernt Personalizer nicht von dem Ereignis.
* Wenn Sie den Rang mit `deferActivation = True` aufrufen, und die `Activate`API für diese eventId *aufrufen*, und Reward aufrufen, wird Personalizer von dem Ereignis mit der angegebenen Reward-Punktzahl lernen.
* Wenn Sie den Rang mit `deferActivation = True` aufrufen und die `Activate`API für diese eventId *aufrufen*, aber den Aufruf von Reward unterlassen, lernt Personalizer aus dem Ereignis mit der in der Konfiguration eingestellten Standard-Belohnungspunktzahl.

## <a name="next-steps"></a>Nächste Schritte
* So konfigurieren Sie die [Standardbelohnungen](how-to-settings.md#configure-rewards-for-the-feedback-loop).
* [Informationen zum Ermitteln der Relevanzbewertung und zu den zu berücksichtigenden Daten](concept-rewards.md).
