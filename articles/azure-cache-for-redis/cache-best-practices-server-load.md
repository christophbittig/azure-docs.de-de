---
title: Bewährte Methoden für die Verwendung und Überwachung der Serverauslastung für Azure Cache for Redis
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie die Serverauslastung für ihre Azure Cache for Redis verwenden und überwachen.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: ceeff68f65ace76f4fe9060edeb775085508c969
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114771"
---
# <a name="manage-server-load-for-azure-cache-for-redis"></a>Verwalten der Serverauslastung für Azure Cache for Redis

## <a name="value-sizes"></a>Wertgrößen

Der Entwurf Ihrer Clientanwendung bestimmt, ob Sie viele kleine Werte oder eine kleinere Anzahl größerer Werte speichern sollten. In Bezug auf den Redis-Server führen kleinere Werte zu einer besseren Leistung. Es wird empfohlen, die Wertgröße kleiner als 100 kB zu halten.

Wenn Ihr Entwurf erfordert, dass Sie größere Werte in der Azure Cache for Redis speichern, ist die Serverauslastung höher. In diesem Fall müssen Sie möglicherweise eine höhere Cacheebene verwenden, um sicherzustellen, dass die CPU-Auslastung den Durchsatz nicht einschränkt.

Auch wenn der Cache über eine ausreichende CPU-Kapazität verfügt, erhöhen größere Werte die Latenz. Befolgen Sie daher die Anleitung unter [Konfigurieren geeigneter Timeouts](cache-best-practices-connection.md#configure-appropriate-timeouts).

Größere Werte erhöhen auch die Wahrscheinlichkeit einer Speicherfragmentierung. Befolgen Sie daher unbedingt die Anleitung unter [Konfigurieren der Einstellung „maxmemory-reserved“](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting).

## <a name="avoid-client-connection-spikes"></a>Vermeiden von Spitzen bei Clientverbindungen

Das Erstellen und Schließen von Verbindungen ist ein aufwendiger Vorgang für Redis-Server. Wenn Ihre Clientanwendung innerhalb einer kurzen Zeit zu viele Verbindungen erstellt oder schließt, kann dies den Redis-Server belasten.

Wenn Sie viele Clientinstanzen instanziieren, um gleichzeitig eine Verbindung mit Redis herzustellen, sollten Sie erwägen, die neuen Verbindungserstellungen zu staffeln, um eine starke Spitze bei der Anzahl verbundener Clients zu vermeiden.

## <a name="memory-pressure"></a>Arbeitsspeicherauslastung

Eine hohe Arbeitsspeicherauslastung auf dem Server erhöht die Wahrscheinlichkeit, dass das System Daten auf den Datenträger weitergeben muss, was zu Seitenfehlern führt, die das System erheblich verlangsamen können.

## <a name="avoid-long-running-commands"></a>Vermeiden von zeitintensiven Befehlen

Der Redis-Server ist ein Single-Thread-Prozess. Befehle mit einer zeitintensiven Ausführungszeit können zu Wartezeiten oder Timeouts auf der clientseitigen Seite führen. Der Grund dafür ist, dass der Server nicht auf andere Anforderungen reagieren kann, während er mit einem Befehl mit einer langer Ausführungszeit beschäftigt ist. Weitere Informationen finden Sie unter [Behandeln von serverseitigen Problemen bei Azure Cache for Redis](cache-troubleshoot-server.md).  

## <a name="monitor-server-load"></a>Überwachungsserverauslastung

Fügen Sie die Überwachung der Serverauslastung hinzu, um sicherzustellen, dass Sie Benachrichtigungen erhalten, wenn eine hohe Serverauslastung auftritt. Die Überwachung kann Ihnen helfen, Ihre Anwendungseinschränkungen zu verstehen. Anschließend können Sie proaktiv an der Entschärfung von Problemen arbeiten. Es wird empfohlen, die Serverauslastung unter 80 % zu halten, um negative Leistungseffekte zu vermeiden.

## <a name="plan-for-server-maintenance"></a>Planen der Serverwartung

Stellen Sie sicher, dass Sie über genügend Serverkapazität verfügen, um Ihre Spitzenlast zu bewältigen, während ihre Cacheserver in Wartung sind. Testen Sie Ihr System, indem Sie die Knoten bei einer Spitzenlast neu starten. Weitere Informationen zum Simulieren der Bereitstellung eines Patches finden Sie unter [Neustart](cache-administration.md#reboot).

## <a name="next-steps"></a>Nächste Schritte

- [Behandeln von serverseitigen Problemen bei Azure Cache for Redis](cache-troubleshoot-server.md)
- [Verbindungsresilienz](cache-best-practices-connection.md)
  - [Konfigurieren von entsprechenden Timeouts](cache-best-practices-connection.md#configure-appropriate-timeouts).
- [Speicherverwaltung](cache-best-practices-memory-management.md)
  - [Konfigurieren der Einstellung „maxmemory-reserved“](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
