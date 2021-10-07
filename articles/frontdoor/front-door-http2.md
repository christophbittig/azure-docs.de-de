---
title: Azure Front Door – Unterstützung für HTTP2 | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur HTTP/2-Unterstützung in Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: dc5679036eb241abc82a57779e41e2d667238216
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601424"
---
# <a name="http2-support-in-azure-front-door"></a>HTTP/2-Unterstützung in Azure Front Door

Derzeit ist die HTTP/2-Unterstützung für alle Azure Front Door-Konfigurationen aktiviert. Es ist keine weitere Aktion vom Kunden erforderlich.

HTTP/2 ist eine wichtige Überarbeitung von HTTP/1.1, die eine schnellere Webleistung bietet, indem die Reaktionszeit reduziert wird. HTTP/2 behält die bekannten HTTP-Methoden, Statuscodes und die Semantik von HTTP/1.1 bei, um die Benutzerfreundlichkeit zu verbessern. Obwohl HTTP/2 darauf ausgelegt ist, unter HTTP und HTTPS zu funktionieren, unterstützen viele Clientwebbrowser HTTP/2 nur über TLS (Transport Layer Security).

> [!NOTE]
> Die Unterstützung des HTTP/2-Protokolls ist nur für Anforderungen von Clients an Front Door verfügbar. Die Kommunikation von Front Door zu Back-Ends im Back-End-Pool erfolgt über HTTP/1.1. 

### <a name="http2-benefits"></a>Vorteile von HTTP/2

HTTP/2 bietet unter anderem folgende Vorteile:

*   **Multiplexing und Parallelität**

    Mit HTTP 1.1 sind für mehrere Anforderungen von Ressourcen mehrere TCP-Verbindungen erforderlich, und jede Verbindung verursacht ihre eigenen Leistungskosten. HTTP/2 ermöglicht die Anforderung mehrerer Ressourcen über eine einzelne TCP-Verbindung.

*   **Header-Komprimierung**

    Durch die Komprimierung der HTTP-Header für bereitgestellte Ressourcen werden deutlich weniger Daten über die Leitung gesendet.

*   **Datenstrom-Abhängigkeiten**

    Datenstromabhängigkeiten ermöglichen dem Client, dem Server anzugeben, welche Ressourcen Vorrang haben.


## <a name="http2-browser-support"></a>HTTP/2-Browserunterstützung

Alle wichtigen Browser haben HTTP/2-Unterstützung in ihren aktuellen Versionen implementiert. Nicht unterstützte Browser führen ein automatisches Fallback auf HTTP/1.1 durch.

|Browser|Mindestversion|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu HTTP/2 finden Sie in den folgenden Ressourcen:

- [HTTP/2-Homepage](https://http2.github.io/) (in englischer Sprache)
- [Häufig gestellte Fragen zu HTTP/2 (offiziell)](https://http2.github.io/faq/) (in englischer Sprache)
- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
