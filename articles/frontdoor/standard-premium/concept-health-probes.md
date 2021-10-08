---
title: Integritätstestüberwachung in Azure Front Door Standard/Premium (Vorschau)
description: In diesem Artikel wird erläutert, wie Azure Front Door die Integrität des Back-Ends überwacht.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a856ae7d38439d11ba0ddd65ebecdda7a3d2ce48
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750198"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Integritätstestüberwachung in Azure Front Door Standard/Premium (Vorschau)

> [!Note]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

Azure Front Door sendet in regelmäßigen Abständen eine HTTP- oder HTTPS-Anforderung an jedes Back-End. Mithilfe dieser Anforderungen kann Azure Front Door die Integrität der einzelnen Endpunkte im Back-End-Pool ermitteln. Anschließend ermittelt Front Door anhand der Antworten aus diesem Test die „besten“ Back-End-Ressourcen für die Weiterleitung Ihrer Clientanforderungen. 

> [!WARNING]
> Da Front Door weltweit viele Edge-Umgebungen hat, kann das Volumen der Integritätstests für Ihre Back-Ends recht hoch sein. Es variiert zwischen 25 Anforderungen pro Minute und bis zu 1200 Anforderungen pro Minute abhängig von der konfigurierten Häufigkeit von Integritätstests. Bei der standardmäßigen Testfrequenz von 30 Sekunden sollte das Testvolumen in Ihrem Back-End etwa 200 Anforderungen pro Minute betragen.

## <a name="supported-protocols"></a>Unterstützte Protokolle

Front Door unterstützt das Senden von Tests über die Protokolle HTTP und HTTPS. Diese Tests werden über dieselben TCP-Ports gesendet, die für das Routen von Clientanforderungen konfiguriert wurden. Sie können nicht außer Kraft gesetzt werden.

## <a name="supported-http-methods-for-health-probes"></a>Für Integritätstests unterstützte HTTP-Methoden

Front Door unterstützt die folgenden HTTP-Methoden zum Senden der Integritätstests:

* **GET:** Die GET-Methode bedeutet, dass alle Informationen (in Form einer Entität), die vom Anforderungs-URI identifiziert werden, abgerufen werden.
* **HEAD:** Die HEAD-Methode ist identisch mit GET, außer dass der Server in der Antwort KEINEN Nachrichtentext zurückgeben darf. Bei neuen Front Door-Profilen ist die Testmethode standardmäßig auf HEAD festgelegt.

> [!NOTE]
> Um die Belastung und Kosten Ihrer Back-Ends zu senken, empfiehlt Front Door die Verwendung von HEAD-Anforderungen für Integritätstests.

## <a name="health-probe-responses"></a>Integritätstestantworten

| Antworten  | BESCHREIBUNG | 
| ------------- | ------------- |
| Ermitteln der Integrität  |  Der Statuscode „200 OK“ gibt an, dass das Back-End fehlerfrei ist. Alle anderen Antworten werden als Fehler betrachtet. Wenn für einen Test aus irgendeinem Grund (einschließlich Netzwerkausfall) keine gültige HTTP-Antwort eingeht, gilt der Test als fehlerhaft.|
| Messen der Latenzzeit  | Latenz ist die Gesamtbetrachtungszeit, gemessen ab dem Zeitpunkt unmittelbar vor dem Senden der Testanforderung bis zu dem Moment, in dem wir das letzte Byte der Antwort erhalten. Wir verwenden für jede Anforderung eine neue TCP-Verbindung, damit diese Messung nicht zugunsten von Back-Ends mit vorhandenen betriebsbereiten Verbindungen ausfällt.  |

## <a name="how-front-door-determines-backend-health"></a>Vorgehensweise beim Ermitteln der Back-End-Integrität mit Front Door

Azure Front Door verwendet in allen Algorithmen denselben aus drei Schritten bestehenden Prozess, um die Integrität zu bestimmen.

1. Deaktivierte Back-Ends werden ausgeschlossen.

1. Back-Ends mit fehlerhaften Integritätstests werden ausgeschlossen:

    * Diese Auswahl erfolgt anhand der letzten _n_ Integritätstestantworten. Wenn mindestens _x_ fehlerfrei sind, wird das Back-End als fehlerfrei betrachtet.

    * _n_ wird durch Ändern der SampleSize-Eigenschaft in den Einstellungen für den Lastenausgleich konfiguriert.

    * _x_ wird durch Ändern der SuccessfulSamplesRequired-Eigenschaft in den Einstellungen für den Lastenausgleich konfiguriert.

1. Front Door misst und verwaltet zusätzlich die Latenz (Roundtripzeit) für jedes Back-End für die Menge der fehlerfreien Back-Ends im Back-End-Pool.

> [!NOTE]
> Wenn ein einzelner Endpunkt Mitglied mehrerer Back-End-Pools ist, optimiert Azure Front Door die Anzahl der an das Back-End gesendeten Integritätstests, um die Last auf dem Back-End zu reduzieren. Integritätstestanforderungen werden basierend auf dem niedrigsten konfigurierten Stichprobenintervall gesendet. Die Integrität des Endpunkts in allen Pools wird durch die Antworten von denselben Integritätstests bestimmt.

## <a name="complete-health-probe-failure"></a>Vollständiger Integritätstestfehler

Wenn Integritätstests für alle Back-Ends in einem Back-End-Pool fehlerhaft sind, betrachtet Front Door alle Back-Ends als fehlerfrei und routet den Datenverkehr in einer Roundrobinverteilung über alle Back-Ends.

Sobald ein Back-End zu einem fehlerfreien Integritätsstatus zurückkehrt, wird der normale Lastenausgleichsalgorithmus von Front Door fortgesetzt.

## <a name="disabling-health-probes"></a>Deaktivieren von Integritätstests

Wenn im Back-End-Pool ein einzelnes Back-End vorhanden ist oder in einem Back-End-Pool nur ein Back-End aktiv ist, können Sie die Integritätstests deaktivieren. Auf diese Weise verringern Sie die Last am Anwendungs-Back-End.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Erstellen einer Instanz von Front Door Standard/Premium](create-front-door-portal.md).
