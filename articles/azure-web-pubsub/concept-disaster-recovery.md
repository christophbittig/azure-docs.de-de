---
title: Resilienz und Notfallwiederherstellung im Azure Web PubSub-Dienst
description: Übersicht über die Einrichtung mehrerer Azure Web PubSub-Dienstinstanzen für Resilienz und Notfallwiederherstellung.
author: vicancy
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: lianwei
ms.openlocfilehash: 14a6661196f7bfa16d3611137d1517c41f21b00d
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156526"
---
# <a name="resiliency-and-disaster-recovery-in-azure-web-pubsub-service"></a>Resilienz und Notfallwiederherstellung im Azure Web PubSub-Dienst

Resilienz und Notfallwiederherstellung sind eine übliche Anforderung für Onlinesysteme. Der Azure Web PubSub-Dienst garantiert bereits eine Verfügbarkeit von 99,9 %, ist aber noch immer ein regionaler Dienst.

Ihre Dienstinstanz ist ein regionaler Dienst, und die Instanz wird in einer einzigen Region ausgeführt. Bei einem regionsweiten Ausfall ist es wichtig, dass der Dienst weiterhin Echtzeitnachrichten in einer anderen Region verarbeiten kann. In diesem Artikel werden einige der Strategien erläutert, mit denen Sie den Dienst so bereitstellen können, dass eine Notfallwiederherstellung möglich ist.

## <a name="high-available-architecture-for-web-pubsub-service"></a>Hoch verfügbare Architektur für den Web PubSub-Dienst

Um regionsübergreifende Resilienz für den Web PubSub-Dienst zu erreichen, müssen Sie mehrere Dienstinstanzen in verschiedenen Regionen einrichten. So können beim Ausfall einer Region die anderen Regionen als Sicherung fungieren.

Ein typisches Setup für ein regionsübergreifendes Szenario ist die Verwendung von zwei (oder mehr) Paaren von Web PubSub-Dienstinstanzen und App-Servern.

In jedem Paar befinden sich App-Server und Web PubSub-Dienst in derselben Region, und der Web PubSub-Dienst legt den Ereignishandler upstream auf den App-Server in derselben Region fest.

Zur besseren Veranschaulichung der Architektur wird der Web PubSub-Dienst als **primärer** Dienst für den App-Server im gleichen Paar bezeichnet. Web PubSub-Dienste in anderen Paaren werden als **sekundäre** Dienste für den App-Server bezeichnet.

Der Anwendungsserver kann die [API für die Überprüfung der Dienstintegrität](/rest/api/webpubsub/dataplane/health-api/get-service-status) verwenden, um zu ermitteln, ob die **primären** und **sekundären** Dienste fehlerfrei sind oder nicht. Für einen Web PubSub-Dienst mit dem Namen `demo` gibt der Endpunkt `https://demo.webpubsub.azure.com/api/health` beispielsweise den Wert 200 zurück, wenn der Dienst fehlerfrei ist. Der App-Server kann die Endpunkte in regelmäßigen Abständen oder bei Bedarf aufrufen, um zu überprüfen, ob die Endpunkte fehlerfrei sind. WebSocket-Clients **verknüpfen** sich in der Regel zuerst mit ihrem Anwendungsserver, um die URL abzurufen, die eine Verbindung mit dem Web PubSub-Dienst herstellt. Die Anwendung verwendet diesen Schritt des **Verknüpfens**, um ein Failover der Clients auf andere fehlerfreie **sekundäre** Dienste auszuführen. Hier finden Sie die ausführlichen Schritte:

1. Beim **Aushandeln** zwischen einem Client und dem App-Server SOLLTE der App-Server nur primäre Web PubSub-Dienstendpunkte zurückgeben, sodass Clients im Normalfall nur eine Verbindung mit primären Endpunkten herstellen.
1. Wenn die primäre Instanz nicht erreichbar ist, SOLLTE beim **Aushandeln** ein fehlerfreier sekundärer Endpunkt zurückgegeben werden, damit der Client weiterhin Verbindungen herstellen kann, und der Client stellt eine Verbindung mit dem sekundären Endpunkt her.
1. Wenn die primäre Instanz eingerichtet ist, SOLLTE das **Verknüpfen** den fehlerfreien primären Endpunkt zurückgeben, damit Clients jetzt eine Verbindung mit dem primären Endpunkt herstellen können.
1. Wenn der App-Server Nachrichten an mehrere Clients **überträgt**, SOLLTEN Nachrichten an alle **fehlerfreien** Endpunkte **übertragen** werden, sowohl an die **primären** als auch an die **sekundären**.
1. Der App-Server kann Verbindungen schließen, die mit **sekundären** Endpunkten verbunden sind, um die Clients zu zwingen, stattdessen eine Verbindung mit dem fehlerfreien primären Endpunkt herzustellen.

In dieser Topologie können Nachrichten von einem Server weiterhin an alle Clients übermittelt werden, da alle App-Server und Web PubSub-Dienstinstanzen miteinander verbunden sind.

Wir haben die Strategie noch nicht in das SDK integriert, daher muss die Anwendung diese Strategie vorerst selbst implementieren. 

Zusammenfassend gilt: Die Anwendungsseite muss Folgendes implementieren:
1. Integritätsprüfung Die Anwendung kann entweder überprüfen, ob der Dienst fehlerfrei ist, indem sie die [API für die Überprüfung der Dienstintegrität](/rest/api/webpubsub/dataplane/health-api/get-service-status) in regelmäßigen Abständen im Hintergrund oder bei Bedarf für jeden **Verknüpfungsaufruf** verwendet.
1. Verknüpfungslogik Die Anwendung gibt standardmäßig einen fehlerfreien **primären** Endpunkt zurück. Wenn der **primäre** Endpunkt nicht verfügbar ist, gibt die Anwendung einen fehlerfreien **sekundären** Endpunkt zurück.
1. Übertragungslogik Wenn Nachrichten an mehrere Clients gesendet werden, muss die Anwendung sicherstellen, dass Nachrichten an alle **fehlerfreien** Endpunkte übertragen werden.

Das folgende Diagramm veranschaulicht diese Topologie:

![Das Diagramm zeigt zwei Regionen, die jeweils einen App-Server und einen Web PubSub-Dienst aufweisen und in denen jeder Server dem Web PubSub-Dienst in seiner eigenen Region als primärer Server und in der anderen Region als sekundärer Server zugeordnet ist.](media/concept-disaster-recovery/topology.png)

## <a name="failover-sequence-and-best-practice"></a>Failoversequenz und bewährte Methode

Sie verfügen jetzt über die richtige Konfiguration der Systemtopologie. Sollte eine Web PubSub-Dienstinstanz ausfallen, wird Onlinedatenverkehr zu anderen Instanzen geroutet.
Folgendes geschieht, wenn eine primäre Instanz ausfällt (und später wiederhergestellt wird):

1. Die primäre Dienstinstanz ist ausgefallen, alle damit verbundenen Clients werden getrennt.
2. Neue Clients oder Clients, die die Verbindung wiederherstellen möchten, führen eine **Aushandlung** mit dem App-Server durch.
2. Der App-Server erkennt, dass die primäre Dienstinstanz ausgefallen ist, und bei Aushandlungen wird nicht mehr dieser Endpunkt, sondern ein fehlerfreier sekundärer Endpunkt zurückgegeben.
3. Clients stellen eine Verbindung mit der sekundären Instanz her.
4. Die sekundäre Instanz übernimmt nun den gesamten Onlinedatenverkehr. Alle Nachrichten vom Server an Clients können weiterhin übermittelt werden, da die sekundäre Instanz mit allen App-Servern verbunden ist. Ereignismeldungen von Clients an den Server werden jedoch nur an den Upstream-App-Server in derselben Region gesendet.
5. Nachdem die primäre Instanz wiederhergestellt und wieder online geschaltet wurde, erkennt der App-Server, dass die primäre Instanz wieder fehlerfrei ist. Bei der Aushandlung wird jetzt wieder der primäre Endpunkt zurückgegeben, sodass neue Clients mit dem primären Endpunkt verbunden werden. Vorhandene Clients werden jedoch nicht getrennt und sind weiterhin mit dem sekundären Endpunkt verbunden, bis sie die Verbindung selbst trennen.

Die folgenden Diagramme veranschaulichen, wie ein Failover durchgeführt wird:

Abb. 1: Vor dem Failover ![Vor dem Failover](media/concept-disaster-recovery/before-failover.png)

Abb. 2: Nach dem Failover ![Nach dem Failover](media/concept-disaster-recovery/after-failover.png)

Abb. 3: Kurz nach der Wiederherstellung der primären Instanz ![Kurz nach der Wiederherstellung der primären Instanz](media/concept-disaster-recovery/after-recover.png)

Wie Sie sehen, ist Onlinedatenverkehr im Normalfall nur für den primären App-Server und die primäre Web PubSub-Dienstinstanz vorhanden (blau dargestellt).

Nach dem Failover werden auch der sekundäre App-Server und die sekundäre Web PubSub-Instanz aktiv.
Nach dem der primäre Web PubSub-Dienst wieder online geschaltet wurde, stellen neue Clients eine Verbindung mit der primären Web PubSub-Instanz her. Vorhandene Clients stellen jedoch weiterhin eine Verbindung mit der sekundären Instanz her, sodass beide Instanzen Datenverkehr verarbeiten.

Nachdem alle vorhandenen Clients die Verbindung getrennt haben, kehrt das System zum normalen Betriebszustand zurück (Abb. 1).

Für die Implementierung einer regionsübergreifenden hochverfügbaren Architektur stehen zwei grundlegende Muster zur Verfügung:

1. Das erste ist die Verwendung eines Paars aus App-Server und Web PubSub-Dienstinstanz, das den gesamten Onlinedatenverkehr verarbeitet, und eines weiteren Paars als Sicherung (bezeichnet als Aktiv/Passiv-Konfiguration, siehe Abb. 1). 
2. Das zweite ist die Verwendung von zwei (oder mehr) Paaren aus App-Servern und Web PubSub-Dienstinstanzen, die alle Onlinedatenverkehr verarbeiten und als Sicherung für andere Paare fungieren (bezeichnet als Aktiv/Aktiv-Konfiguration, siehe Abb. 3).

Der Web PubSub-Dienst kann beide Muster unterstützen. Der wesentliche Unterschied besteht in der Implementierung der App-Server.
Wenn App-Server aktiv/passiv sind, sind auch die Web PubSub-Instanzen aktiv/passiv (weil der primäre App-Server nur seine primäre Web PubSub-Dienstinstanz zurückgibt).
Wenn App-Server aktiv/aktiv sind, sind auch die Web PubSub-Instanzen aktiv/aktiv (weil alle App-Server ihre eigenen primären Web PubSub-Instanzen zurückgeben und daher alle Datenverkehr empfangen können).

Beachten Sie, dass Sie unabhängig vom ausgewählten Muster jede Web PubSub-Dienstinstanz als **primäre** Rolle mit einem App-Server verbinden müssen.

Aufgrund der Natur von WebSocket-Verbindungen (lange Verbindungen) treten zudem im Fall eines Failovers nach einem Notfall Verbindungsabbrüche bei Clients auf.
Sie müssen solche Verbindungsabbrüche auf der Clientseite behandeln, damit sie für Ihre Endkunden transparent sind. Stellen Sie beispielsweise nach dem Schließen einer Verbindung erneut eine Verbindung her.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Ihre Anwendung konfigurieren, um Resilienz für den Web PubSub-Dienst zu erreichen. 

[!INCLUDE [next step](includes/include-next-step.md)]
