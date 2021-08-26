---
title: Übersicht über Anrufautomatisierungs-APIs von Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie eine Übersicht über das Feature und die APIs für die Anrufautomatisierung.
author: GrantMeStrength
manager: anvalent
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e45e18271ef26d1968f3d1ba28d4cf2d59fa8448
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253676"
---
# <a name="call-automation-overview"></a>Übersicht über die Anrufautomatisierung

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

Mit den Anrufautomatisierungs-APIs können Sie über **Dienste** auf Sprach- und Videoanruffunktionen zugreifen. Mithilfe dieser APIs können Sie Dienstanwendungen zum Steuern automatisierter ausgehender Erinnerungsanrufe für Termine erstellen oder proaktive Benachrichtigungen für Ereignisse wie Stromausfälle oder Waldbrände bereitstellen. Dienstanwendungen, die an einem Anruf teilnehmen, können Aktualisierungen überwachen, wenn Teilnehmer dem Anruf beitreten oder den Anruf verlassen, und ermöglichen Ihnen dadurch die Implementierung umfangreicher Berichts- und Protokollierungsfunktionen.

![Anrufinterne und -externe Apps](../media/call-automation-apps.png)

Aufrufautomatisierungs-APIs werden sowohl für anrufinterne Aktionen (durch Anwendungsteilnehmer oder App-Teilnehmer) als auch anrufexterne Aktionen bereitgestellt. Zwischen diesen APIs gibt es zwei wichtige Unterschiede:
- Anrufinterne APIs erfordern, dass Ihre Anwendung dem Anruf als Teilnehmer beitritt. App-Teilnehmer werden zu den [standardmäßigen PSTN- und VoIP-Tarifen](https://azure.microsoft.com/pricing/details/communication-services/) abgerechnet.
- Anrufinterne APIs verwenden die `callConnectionId`, die app-participant zugeordnet ist, während anrufexterne APIs die der Anrufinstanz zugeordnete `serverCallId` verwenden. 

## <a name="use-cases"></a>Anwendungsfälle
| Anwendungsfall                                                       | Anrufintern (App-Teilnehmer) | Anrufextern   |
| ---------------------------------------------------------------| ------------------------- | ------------- |
| Tätigen oder Empfangen von 1:1-Anrufen zwischen Bot und menschlichen Teilnehmern  | X                         |               |
| Wiedergeben von Audioaufforderungen und Lauschen auf Antworten                    | X                         |               |
| Überwachen von anrufinternen Ereignissen                                         | X                         |               |
| Erstellen von Anrufen mit mehreren Teilnehmern                        | X                         |               |
| Abrufen von Anrufteilnehmern und Teilnehmerdetails                  | X                         |               |
| Hinzufügen oder Entfernen von Anrufteilnehmern                                | X                         | X             |
| Serverseitige Aktionen in Peer-to-Peer-Anrufen (z. B. Aufzeichnung)     |                           | X             |
| Wiedergeben von Audioankündigungen für alle Teilnehmer                   |                           | X             |
| Starten und Verwalten der Anrufaufzeichnung                                |                           | X             |

## <a name="in-call-app-participant-apis"></a>Anrufinterne (App-Teilnehmer-)APIs

Anrufinterne APIs ermöglichen es einer Anwendung, Aktionen in einem Aufruf als App-Teilnehmer auszuführen. Wenn eine Anwendung auf einen Aufruf antwortet oder diesem beitritt, wird eine `callConnectionId` zugewiesen, die für anrufinterne Aktionen verwendet wird, z. B.:
- Hinzufügen oder Entfernen von Anrufteilnehmern
- Wiedergeben von Audioaufforderungen und Lauschen auf DTMF-Antworten
- Lauschen auf Aufruflistenaktualisierungen und -ereignisse
- Beenden eines Anrufs

![Anrufinterne Anwendung](../media/call-automation-in-call.png)

### <a name="in-call-events"></a>Anrufinterne Ereignisse
Ereignisbenachrichtigungen werden als JSON-Nutzdaten über den `callbackUri`-Satz an die anrufende Anwendung gesendet, wenn dem Anruf beigetreten wird. Ereignisse, die an anrufinterne App-Teilnehmer gesendet werden:
- CallState-Ereignisse (Aufbauen, Aufgebaut, Beenden, Beendet)
- DTMF erhalten
- Audioergebnis wiedergeben
- Abbrechen der Medienverarbeitung
- Ergebnis der Einladung des Teilnehmers
- Teilnehmer aktualisiert

## <a name="out-of-call-apis"></a>Anrufexterne APIs
Anrufexterne APIs ermöglichen es Ihnen, Aktionen für einen Anruf oder eine Besprechung auszuführen, ohne dass ein App-Teilnehmer vorhanden ist. Anrufexterne APIs verwenden die `serverCallId`, die entweder vom Anrufclient-SDK bereitgestellt oder generiert wird, wenn ein Aufruf über die Aufrufautomatisierungs-APIs erstellt wird. Da anrufexterne APIs keinen App-Teilnehmer erfordern, sind sie für die Implementierung serverseitiger Geschäftslogik in Peer-zu-Peer-Aufrufe nützlich. Stellen Sie sich beispielsweise das Szenario eines Supportanrufs vor, der als Peer-to-Peer-Aufruf gestartet wurde, und bei dem der Agent (Teilnehmer B) einen Fachexperten zur Unterstützung hinzuziehen möchte. Teilnehmer B löst ein Ereignis in der Clientschnittstelle für eine Serveranwendung aus, um einen verfügbaren Experten zu ermitteln und zum Anruf einzuladen. Der unten gezeigte Endzustand des Datenflusses ist ein Gruppenaufruf mit drei menschlichen Teilnehmern.

![Anrufexterne Anwendung](../media/call-automation-out-of-call.png)

Anrufexterne APIs sind u. a. für folgende Aktionen verfügbar:
- Hinzufügen oder Entfernen von Anrufteilnehmern
- Starten/Stoppen/Anhalten/Fortsetzen der Anrufaufzeichnung
                                                       
### <a name="out-of-call-events"></a>Anrufexterne Ereignisse
Ereignisbenachrichtigungen werden über den `callbackUri` gesendet, der während des ursprünglichen API-Aufrufs angegeben wird, als JSON-Nutzdaten an die anrufende Anwendung gesendet. Aktionen mit entsprechenden anrufexternen Ereignissen:
- Anrufaufzeichnung (Starten, Stoppen, Anhalten, Fortsetzen)
- Ergebnis der Einladung des Teilnehmers

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie im [Schnellstart zur Anrufautomatisierung](../../quickstarts/voice-video-calling/call-automation-api-sample.md).
