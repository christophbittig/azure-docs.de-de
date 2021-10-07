---
title: Überprüfen und Veröffentlichen eines Dynamics 365-Angebots in Microsoft AppSource (Azure Marketplace)
description: Erfahren Sie, wie Sie ein Dynamics 365-Angebot in Microsoft AppSource (Azure Marketplace) überprüfen und veröffentlichen.
ms.reviewer: emuench
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: b5dc12c821471f822d88de89db974c081a4a1e33
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079566"
---
# <a name="review-and-publish-a-dynamics-365-offer"></a>Überprüfen und Veröffentlichen eines Dynamics 365-Angebots

In diesem Artikel erfahren Sie, wie Sie mit Partner Center eine Vorschau Ihres Dynamics 365-Angebots erstellen und es anschließend im kommerziellen Marketplace veröffentlichen. Außerdem wird erläutert, wie Sie den Veröffentlichungsstatus beim Durchlaufen der Veröffentlichungsschritte überprüfen können.

## <a name="offer-status"></a>Angebotsstatus

Sie können den Status Ihres Angebots im [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) im Dashboard des kommerziellen Marketplace auf der Registerkarte **Übersicht** überprüfen. Der **Status** jedes Angebots ist einer der folgenden:

| Status | BESCHREIBUNG |
| ------------ | ------------- |
| Entwurf | Angebot wurde erstellt, wird aber nicht veröffentlicht. |
| Veröffentlichung wird durchgeführt | Das Angebot durchläuft den Veröffentlichungsvorgang. |
| Eingreifen erforderlich | Wir haben während der Zertifizierung oder einer anderen Veröffentlichungsphase ein kritisches Problem festgestellt. |
| Vorschau | Wir haben das Angebot zertifiziert. Nun muss es abschließend durch den Herausgeber verifiziert werden. Wählen Sie **Live schalten** aus, um das Angebot live zu veröffentlichen. |
| Live | Das Angebot ist im Marketplace live geschaltet und kann von Kunden angezeigt und erworben werden. |
| Ausstehender Verkaufsstopp | Der Herausgeber hat „Verkaufsstopp“ für ein Angebot oder einen Plan ausgewählt, aber die Aktion ist noch nicht abgeschlossen. |
| Im Marketplace nicht verfügbar | Ein zuvor im Marketplace veröffentlichtes Angebot wurde entfernt. |
|||

## <a name="validation-and-publishing-steps"></a>Schritte für die Validierung und Veröffentlichung

Der Veröffentlichungsstatus Ihres Angebots ändert sich, während es den Veröffentlichungsprozess durchläuft. Ausführliche Informationen zu diesem Prozess finden Sie unter [Schritte für die Validierung und Veröffentlichung](review-publish-offer.md#validation-and-publishing-steps).

Wenn Sie bereit sind, ein Angebot für die Veröffentlichung zu übermitteln, wählen Sie oben rechts im Portal **Überprüfen und veröffentlichen** aus. Sie werden den Status jeder Seite für Ihr Angebot als einen der folgenden aufgeführt sehen:

- **Nicht gestartet**: Die Seite ist unvollständig.
- **Unvollständig**: Auf der Seite fehlen erforderliche Informationen, oder sie enthält Fehler, die korrigiert werden müssen. Sie müssen zur Seite zurückkehren und diese aktualisieren.
- **Abgeschlossen**: Die Seite ist vollständig. Alle erforderlichen Daten wurden angegeben, und es liegen keine Fehler vor.

Wenn eine der Seiten einen anderen Status als **Abgeschlossen** aufweist, müssen Sie das Problem auf dieser Seite beheben und dann zur Seite **Überprüfen und veröffentlichen** zurückkehren, um sich zu vergewissern, dass der Status nun **Abgeschlossen** lautet. Einige Angebotstypen müssen getestet werden. In diesem Fall wird ein Feld **Hinweise zur Zertifizierung** angezeigt, in dem Sie Testanweisungen für das Zertifizierungsteam und ergänzende Hinweise angeben müssen, die das Verständnis Ihrer App erleichtern.

Sobald alle Seiten vollständig sind und Sie zutreffende Prüfhinweise eingegeben haben, wählen Sie **Veröffentlichen**, um Ihr Angebot einzureichen. Wir werden Ihnen eine E-Mail senden, sobald eine Vorschauversion Ihres Angebots zur Freigabe verfügbar ist. Führen Sie zu diesem Zeitpunkt die folgenden Schritte aus:

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Kehren Sie zu [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002) zurück.
1. Wählen Sie auf der Startseite die Kachel **Marketplace-Angebote** aus.

    [ ![Screenshot der Kachel „Marketplace-Angebote“ auf der Startseite von Partner Center](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Wählen Sie auf der Seite „Marketplace-Angebote“ das Angebot aus.
1. Wählen Sie **Überprüfen und veröffentlichen** aus.
1. Wählen Sie **Liveschalten**, um das Angebot öffentlich zugänglich zu machen.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Kehren Sie zu [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002) zurück.
1. Wählen Sie die Registerkarte **Übersicht** in der linken Navigationsmenüleiste.
1. Wählen Sie das Angebot.
1. Wählen Sie **Überprüfen und veröffentlichen** aus.
1. Wählen Sie **Liveschalten**, um das Angebot öffentlich zugänglich zu machen.

---

Nachdem Sie **Überprüfen und veröffentlichen** ausgewählt haben, führen wir Zertifizierungs- und andere Überprüfungsprozesse durch, bevor Ihr Angebot in AppSource veröffentlicht wird. Wir benachrichtigen Sie, wenn Ihr Angebot in der Vorschau verfügbar ist, damit Sie es live schalten können. Wenn ein Problem vorliegt, stellen wir Ihnen die Details und Anleitungen zur Behebung bereit.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie die _Verwaltung von Drittanbieter-App-Lizenzen über Microsoft_ für Ihr Angebot aktiviert haben, müssen Sie nach dem Verkauf Ihres Angebots den Deal in Partner Center registrieren. Weitere Informationen finden Sie unter [Verwalten der Lizenzierung in Marketplace-Angeboten](/partner-center/csp-commercial-marketplace-licensing).
- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](update-existing-offer.md)
