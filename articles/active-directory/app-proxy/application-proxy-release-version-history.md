---
title: 'Azure Active Directory-Anwendungsproxy: Verlauf der Versionsreleases'
description: In diesem Artikel werden alle Releases des Azure Active Directory-Anwendungsproxys aufgeführt und neue Features sowie behobene Probleme beschrieben.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/27/2021
ms.subservice: app-proxy
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 7b00c6fb425f04d84c6f5d5c46b42865c05d5e02
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988902"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD-Anwendungsproxy: Verlauf der Versionsveröffentlichungen
In diesem Artikel werden die veröffentlichten Versionen und Features des Anwendungsproxys von Azure Active Directory (Azure AD) aufgeführt. Das Azure AD-Team aktualisiert den Anwendungsproxy regelmäßig mit neuen Features und Funktionen. Anwendungsproxyconnectors werden [automatisch aktualisiert, wenn eine neue größere Version veröffentlicht wird](application-proxy-faq.yml#why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version-). 

Wir empfehlen die Aktivierung von automatischen Updates für Ihre Connectors, um sicherzustellen, dass Sie über die neuesten Features und Fehlerbehebungen verfügen. Microsoft-Support bitten Sie möglicherweise, die neueste Connectorversion zu installieren, um ein Problem zu beheben.

Hier finden Sie eine Liste der zugehörigen Ressourcen:

| Resource                                         | Details                                                      |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Aktivieren des Anwendungsproxys                  | In diesem [Tutorial](application-proxy-add-on-premises-application.md) werden die Voraussetzungen zum Aktivieren des Anwendungsproxys und zum Installieren und Registrieren eines Connectors beschrieben. |
| Grundlegendes zu Azure AD-Anwendungsproxyconnectors | Erfahren Sie mehr über die [Connectorverwaltung](application-proxy-connectors.md) und die [automatische Aktualisierung](application-proxy-connectors.md#automatic-updates) von Connectors. |
| Herunterladen des Azure AD-Anwendungsproxyconnectors    | [Laden Sie den aktuellen Connector herunter](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download). |

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>Releasestatus

22. Juli 2020: Für den Download veröffentlicht: Diese Version steht nur über die Downloadseite zur Installation zur Verfügung. 

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
-   Verbesserte Unterstützung für Azure Government-Cloudumgebungen. Ausführliche Informationen zur ordnungsgemäßen Installation des Connectors für die Azure Government-Cloudprüfung finden Sie unter [Voraussetzungen](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) und [Installieren des Agents für die Azure Government-Cloud](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).
- Unterstützung für die Verwendung des Webclients der Remotedesktopdienste mit dem Anwendungsproxy. Weitere Informationen finden Sie unter [Veröffentlichen des Remotedesktops per Azure AD-Anwendungsproxy](application-proxy-integrate-with-remote-desktop-services.md).
- Verbesserte Aushandlungen für WebSocket-Erweiterungen 
- Die Unterstützung für optimiertes Routing zwischen Connectorgruppen und Anwendungsproxy-Clouddiensten basierend auf der Region wurde hinzugefügt. Weitere Informationen finden Sie unter [Optimieren des Datenverkehrsflusses mit dem Azure Active Directory-Anwendungsproxy](application-proxy-network-topology.md). 

### <a name="fixed-issues"></a>Behobene Probleme
- Behoben: WebSocket-Problem, das Zeichenfolgen in Kleinbuchstaben erzwingt
- Behoben: Problem, das dazu führt, dass Connectors manchmal nicht reagieren

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>Releasestatus

17. Juli 2020 Für den Download veröffentlicht. Diese Version ist nur über die Downloadseite für die Installation verfügbar. 

### <a name="fixed-issues"></a>Behobene Probleme
- Behoben: Arbeitsspeicherverlust in vorheriger Version
- Allgemeine Verbesserungen der WebSocket-Unterstützung

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Releasestatus

07. April 2020: Für den Download veröffentlicht: Diese Version steht nur über die Downloadseite zur Installation zur Verfügung. 

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
-   Connectors verwenden für alle Verbindungen ausschließlich TLS 1.2. Weitere Informationen finden Sie unter [Voraussetzungen für Connectors](application-proxy-add-on-premises-application.md#prerequisites).
- Verbesserte Signalisierung zwischen Connector und Azure-Diensten. Dazu zählen die Unterstützung zuverlässiger Sitzungen für die WCF-Kommunikation zwischen Connector und Azure-Diensten sowie Verbesserungen des DNS-Caches für die WebSocket-Kommunikation.
- Unterstützung für die Konfiguration eines Proxys zwischen dem Connector und der Back-End-Anwendung. Weitere Informationen finden Sie unter [Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Behobene Probleme
- Das Fallback auf Port 8080 für die Kommunikation zwischen dem Connector und Azure-Diensten wurde entfernt.
- Debugablaufverfolgungen für die WebSocket-Kommunikation wurden hinzugefügt. 
- Das Beibehalten des SameSite-Attributs wurde behoben, wenn es in Back-End-Anwendungscookies festgelegt war.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Releasestatus

20. September 2018: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

- Es wurde WebSocket-Unterstützung für die Anwendung QlikSense hinzugefügt. Weitere Informationen zum Integrieren von QlikSense mit dem Anwendungsproxy finden Sie in dieser [exemplarischen Vorgehensweise](application-proxy-qlik.md). 
- Der Installations-Assistent wurde verbessert, um das Konfigurieren eines ausgehenden Proxys zu vereinfachen. 
- TLS 1.2 wurde als Standardprotokoll für Connectors festgelegt. 
- Es wurden neue Microsoft-Software-Lizenzbedingungen hinzugefügt.  

### <a name="fixed-issues"></a>Behobene Probleme

- Es wurde ein Fehler behoben, der zu Arbeitsspeicherverlusten im Connector führte.
- Die Azure Service Bus-Version wurde aktualisiert, wobei auch Connectortimeoutfehler behoben wurden.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Releasestatus

19. Januar 2018: Für den Download veröffentlicht

### <a name="fixed-issues"></a>Behobene Probleme

- Es wurde Unterstützung für benutzerdefinierte Domänen hinzugefügt, die eine Domänenübersetzung im Cookie erfordern.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Releasestatus 

25. Mai 2017: Für den Download veröffentlicht 

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 

Verbesserte Kontrolle über die Beschränkungen für ausgehende Verbindungen von Connectors. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Releasestatus

15. April 2017: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

- Das Onboarding und die Verwaltung wurden vereinfacht, wobei weniger Ports erforderlich sind. Für den Anwendungsproxy müssen jetzt nur zwei ausgehende Standardports geöffnet werden: 443 und 80. Der Anwendungsproxy verwendet weiterhin nur ausgehende Verbindungen, sodass Sie in einer DMZ keine Komponenten benötigen. Weitere Informationen finden Sie in unserer  [Konfigurationsdokumentation](application-proxy-add-on-premises-application.md).  
- Sie können Ihr Netzwerk jetzt über DNS statt über den IP-Adressbereich öffnen, wenn dies von Ihrem externen Proxy oder Ihrer Firewall unterstützt wird. Anwendungsproxydienste erfordern nur Verbindungen mit „*.msappproxy.net“ und „*.servicebus.windows.net“.


## <a name="earlier-versions"></a>Frühere Versionen

Wenn Sie eine Anwendungsproxyconnector-Version vor 1.5.36.0 verwenden, aktualisieren Sie auf die neueste Version, um sicherzustellen, dass Sie über die neuesten vollständig unterstützten Features verfügen.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über den [Remotezugriff auf lokale Anwendungen über den Azure Active Directory-Anwendungsproxy](application-proxy.md).
- Erste Schritte mit dem Anwendungsproxy finden Sie in [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy](application-proxy-add-on-premises-application.md).
