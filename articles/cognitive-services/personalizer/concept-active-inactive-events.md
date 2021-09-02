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
ms.openlocfilehash: ba95c22a773a382a3c03aab18f8f885e6a2791d8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831008"
---
# <a name="active-and-inactive-events"></a>Aktive und inaktive Ereignisse

Ein **aktives** Ereignis ist jeder Aufruf für die Rangfolge, bei dem Sie wissen, dass Sie dem Kunden das Ergebnis zeigen und den Relevanzwert bestimmen werden. Dies ist das Standardverhalten.

Ein **inaktives** Ereignis ist ein Aufruf für die Rangfolge, bei dem Sie nicht sicher sind, ob der Benutzer die empfohlene Aktion aufgrund der Geschäftslogik jemals angezeigt bekommt. Dadurch können Sie das Ereignis verwerfen, sodass die Personalisierung nicht mit der Standardrelevanz trainiert wird. Inaktive Ereignisse sollten die Relevanz-API nicht aufrufen.

Es ist wichtig, dass die Lernschleife den eigentlichen Ereignistyp kennt. Ein inaktives Ereignis weist keinen Relevanzaufruf auf. Ein aktives Ereignis sollte einen Relevanzaufruf aufweisen, aber wenn der API-Aufruf nie erfolgt, wird die standardmäßige Relevanzbewertung angewendet. Ändern Sie den Status eines Ereignisses von „inaktiv“ zu „aktiv“, sobald Sie wissen, dass es die Benutzererfahrung beeinflusst.

## <a name="typical-active-events-scenario"></a>Typisches Szenario für aktive Ereignisse

Wenn Ihre Anwendung die Rang-API aufruft, erhalten Sie die Angabe, welche Aktion die Anwendung im Feld **rewardActionId** anzeigen soll.  Von diesem Moment an erwartet die Personalisierung einen Relevanzaufruf mit einer Relevanzbewertung, die dieselbe Ereignis-ID aufweist. Die Relevanzbewertung wird zum Trainieren des Modells für zukünftige Rangaufrufe verwendet. Wenn für die Ereignis-ID kein Reward-Aufruf empfangen wird, wird eine Standardrelevanz angewandt. [Standardrelevanzen](how-to-settings.md#configure-rewards-for-the-feedback-loop) werden für Ihre Personalisierungsressource im Azure-Portal festgelegt.

## <a name="other-event-type-scenarios"></a>Sonstige Ereignistypenszenarien

In einigen Szenarien muss die Anwendung möglicherweise Rank aufrufen, bevor sie weiß, ob das Ergebnis verwendet oder dem Benutzer angezeigt wird. Dies kann beispielsweise geschehen, wenn das Seitenrendering von beworbenen Inhalten durch eine Marketingkampagne überschrieben wird. Wenn das Ergebnis des Rank-Aufrufs niemals verwendet und dem Benutzer niemals angezeigt wurde, übermitteln Sie keinen zugehörigen Reward-Aufruf.

In der Regel treten diese Szenarien in folgenden Fällen ein:

* Sie rendern vorab die Benutzeroberfläche, die dem Benutzer möglicherweise gar nicht angezeigt wird.
* Ihre Anwendung führt eine vorausschauende Personalisierung durch, bei der Rank-Aufrufe mit wenig Echtzeitkontext ausgeführt werden, und die Ausgabe wird von der Anwendung unter Umständen gar nicht genutzt.

In diesen Fällen verwenden Sie die Personalisierung im Aufruf von Rank mit der Anforderung, dass das Ereignis _inaktiv_ sein muss. Die Personalisierung erwartet für dieses Ereignis keine Relevanz und wendet auch keine Standardrelevanz an.

Wenn die Anwendung später in Ihrer Geschäftslogik die Informationen des Rank-Aufrufs verwendet, müssen Sie das Ereignis lediglich _aktivieren_. Sobald das Ereignis aktiv ist, erwartet die Personalisierung eine Ereignisrelevanz. Wenn kein expliziter Aufruf der Reward-API erfolgt, wendet die Personalisierung eine Standardrelevanz an.

## <a name="inactive-events"></a>Inaktive Ereignisse

Um das Training für ein Ereignis zu deaktivieren, rufen Sie Rank mit `learningEnabled = False` auf.

Für ein inaktives Ereignis wird das Lernen implizit aktiviert, wenn Sie für die Ereignis-ID eine Relevanz senden oder die `activate`-API aufrufen.

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zum Ermitteln der Relevanzbewertung und zu den zu berücksichtigenden Daten](concept-rewards.md).
