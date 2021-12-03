---
title: 'Azure Front Door: Überwachung der Back-End-Integrität | Microsoft-Dokumentation'
description: In diesem Artikel wird erläutert, wie Azure Front Door die Integrität Ihrer Back-Ends überwacht.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2021
ms.author: duau
ms.openlocfilehash: 101d769fce7baaac629c756b3f7fad173f736b6c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053705"
---
# <a name="health-probes"></a>Integritätstests

Um die Integrität und Entfernung der einzelnen Back-Ends für eine bestimmte Front Door-Umgebung zu ermitteln, sendet jede Front Door-Umgebung in regelmäßigen Abständen eine synthetische HTTP-/HTTPS-Anforderung an jedes konfigurierte Back-End. Anschließend ermittelt Front Door anhand der Antworten aus diesem Test die „besten“ Back-End-Ressourcen für die Weiterleitung Ihrer Clientanforderungen. 

> [!WARNING]
> Da Front Door weltweit viele Edge-Umgebungen hat, kann das Volumen der Integritätstests für Ihre Back-Ends recht hoch sein. Es variiert zwischen 25 Anforderungen pro Minute und bis zu 1200 Anforderungen pro Minute abhängig von der konfigurierten Häufigkeit von Integritätstests. Bei der standardmäßigen Testfrequenz von 30 Sekunden sollte das Testvolumen in Ihrem Back-End etwa 200 Anforderungen pro Minute betragen.

> [!NOTE]
> Die HTTP-/HTTPS-Tests von Front Door werden mit dem auf den folgenden Wert festgelegten `User-Agent`-Header gesendet: `Edge Health Probe`. 

## <a name="supported-protocols"></a>Unterstützte Protokolle

Front Door unterstützt das Senden von Tests über die Protokolle HTTP und HTTPS. Diese Tests werden über dieselben TCP-Ports gesendet, die für das Routen von Clientanforderungen konfiguriert wurden. Sie können nicht außer Kraft gesetzt werden.

## <a name="supported-http-methods-for-health-probes"></a>Für Integritätstests unterstützte HTTP-Methoden

Front Door unterstützt die folgenden HTTP-Methoden zum Senden der Integritätstests:

1. **GET:** Die GET-Methode bedeutet, dass alle Informationen (in Form einer Entität), die vom Anforderungs-URI identifiziert werden, abgerufen werden.
2. **HEAD:** Die HEAD-Methode ist identisch mit GET, außer dass der Server in der Antwort KEINEN Nachrichtentext zurückgeben darf. Bei neuen Front Door-Profilen ist die Testmethode standardmäßig auf HEAD festgelegt.

> [!NOTE]
> Um die Belastung und Kosten Ihrer Back-Ends zu senken, empfiehlt Front Door die Verwendung von HEAD-Anforderungen für Integritätstests.

## <a name="health-probe-responses"></a>Integritätstestantworten

| Antworten  | BESCHREIBUNG | 
| ------------- | ------------- |
| Ermitteln der Integrität  | Der Statuscode „200 OK“ gibt an, dass das Back-End fehlerfrei ist. Alle anderen Antworten werden als Fehler betrachtet. Wenn für einen Test aus irgendeinem Grund (einschließlich Netzwerkausfall) keine gültige HTTP-Antwort eingeht, gilt der Test als fehlerhaft.|
| Messen der Latenzzeit  | Latenz ist die Gesamtbetrachtungszeit, gemessen ab dem Zeitpunkt unmittelbar vor dem Senden der Testanforderung bis zu dem Moment, in dem wir das letzte Byte der Antwort erhalten. Wir verwenden für jede Anforderung eine neue TCP-Verbindung, damit diese Messung nicht zugunsten von Back-Ends mit vorhandenen betriebsbereiten Verbindungen ausfällt.  |

## <a name="how-front-door-determines-backend-health"></a>Vorgehensweise beim Ermitteln der Back-End-Integrität mit Front Door

Azure Front Door verwendet in allen Algorithmen denselben aus drei Schritten bestehenden Prozess, um die Integrität zu bestimmen.

1. Deaktivierte Back-Ends werden ausgeschlossen.

2. Back-Ends mit fehlerhaften Integritätstests werden ausgeschlossen:
    * Diese Auswahl erfolgt anhand der letzten _n_ Integritätstestantworten. Wenn mindestens _x_ fehlerfrei sind, wird das Back-End als fehlerfrei betrachtet.

    * _n_ wird durch Ändern der SampleSize-Eigenschaft in den Einstellungen für den Lastenausgleich konfiguriert.

    * _x_ wird durch Ändern der SuccessfulSamplesRequired-Eigenschaft in den Einstellungen für den Lastenausgleich konfiguriert.

3. Front Door misst und verwaltet zusätzlich die Latenz (Roundtripzeit) für jedes Back-End für die Menge der fehlerfreien Back-Ends im Back-End-Pool.

> [!NOTE]
> Wenn ein einzelner Endpunkt Mitglied mehrerer Back-End-Pools ist, optimiert Azure Front Door die Anzahl der an das Back-End gesendeten Integritätstests, um die Last auf dem Back-End zu reduzieren. Integritätstestanforderungen werden basierend auf dem niedrigsten konfigurierten Stichprobenintervall gesendet. Die Integrität des Endpunkts in allen Pools wird durch die Antworten von denselben Integritätstests bestimmt.

## <a name="complete-health-probe-failure"></a>Vollständiger Integritätstestfehler

Wenn Integritätstests für alle Back-Ends in einem Back-End-Pool fehlerhaft sind, betrachtet Front Door alle Back-Ends als fehlerfrei und routet den Datenverkehr in einer Roundrobinverteilung über alle Back-Ends.

Sobald ein Back-End zu einem fehlerfreien Integritätsstatus zurückkehrt, wird der normale Lastenausgleichsalgorithmus von Front Door fortgesetzt.

## <a name="disabling-health-probes"></a>Deaktivieren von Integritätstests

Wenn Sie ein einzelnes Back-End in Ihrem Back-End-Pool haben, können Sie die Integritätstests deaktivieren, um die Last Ihres Anwendungs-Back-Ends zu verringern. Selbst wenn Sie über mehrere Back-Ends im Back-End-Pool verfügen, aber nur eines davon im aktivierten Zustand ist, können Sie Integritätstests deaktivieren.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über das [Erstellen einer Instanz von Front Door Standard/Premium](standard-premium/create-front-door-portal.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
