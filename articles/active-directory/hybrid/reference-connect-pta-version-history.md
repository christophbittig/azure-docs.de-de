---
title: 'Azure AD-Passthrough-Authentifizierung: Versionsverlauf | Microsoft-Dokumentation'
description: In diesem Artikel werden alle Releases des Agents für die Azure AD-Passthrough-Authentifizierung aufgeführt.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 852a842c9b58a76742d0b482e62c49cd91d34f1b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099644"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Agent für die Azure AD-Passthrough-Authentifizierung: Verlauf der Versionsveröffentlichungen 
 
Die lokal installierten Agents, die die Passthrough-Authentifizierung aktivieren, werden regelmäßig aktualisiert, damit neue Funktionen bereitgestellt werden können. In diesem Artikel werden die Versionen und Features, die mit der Einführung neuer Funktionen hinzugefügt werden, aufgeführt. Die Agents für die Passthrough-Authentifizierung werden automatisch aktualisiert, sobald eine neue Version verfügbar ist. 

Verwandte Themen: 

- [Benutzeranmeldung mit der Azure AD-Passthrough-Authentifizierung](how-to-connect-pta.md) 
- [Installation des Agents für die Azure AD-Passthrough-Authentifizierung](how-to-connect-pta-quick-start.md) 

## <a name="1524820"></a>1.5.2482.0
### <a name="release-status"></a>Releasestatus: 
07.07.2021: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

- Die Pakete/Bibliotheken wurden auf neuere Versionen aktualisiert, die mit SHA-256RSA signiert wurden.

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Releasestatus: 
09.04.2020: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

- Unterstützung für Cloudumgebungen als Ziele bei der Installation hinzugefügt. Das Paket kann an eine bestimmte Cloudumgebung angeheftet werden.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Releasestatus 
22.01.2019: Für den Download veröffentlicht  
### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 
- Support für Service Bus-Kanäle hinzugefügt, um eine weitere Ebene für die Verbindungsstabilität ausgehender Verbindungen hinzuzufügen 
- Erzwingen von TLS 1.2 während der Agent-Registrierung 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Releasestatus 
10.04.2018: Für den Download veröffentlicht  
### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 
- Support für die Websocketverbindung 
- Festlegen von TLS 1.2 als Standardprotokoll für den Agent 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Releasestatus 
31.01.2018: Für den Download veröffentlicht  
### <a name="fixed-issues"></a>Behobene Probleme 
- Es wurde ein Fehler behoben, der zu Arbeitsspeicherverlusten im Agent führte. 
- Die Azure Service Bus-Version wurde aktualisiert, wobei auch Connectortimeoutfehler behoben wurden. 
### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 
- Support für Websocket-basierte Verbindungen zwischen dem Agent und Azure AD-Diensten hinzugefügt, um die Verbindungsstabilität zu verbessern

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Releasestatus 
25.11.2017: Für den Download veröffentlicht  
### <a name="fixed-issues"></a>Behobene Probleme 
- Es wurden Fehler im Zusammenhang mit dem DNS-Cache für Standardproxyszenarios behoben. 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Releasestatus 
17.10.2017: Für den Download veröffentlicht  
### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 
- DNS-Cachefunktion für ausgehende Verbindungen hinzugefügt, um die Resilienz vor DNS-Fehlern hinzuzufügen 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Releasestatus 
31.08.2017: Für den Download veröffentlicht  
### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen 
- Version zur allgemeinen Verfügbarkeit des Agents für die Azure AD-Passthrough-Authentifizierung 

## <a name="next-steps"></a>Nächste Schritte

- [Benutzeranmeldung mit der Azure Active Directory-Passthrough-Authentifizierung](how-to-connect-pta.md)
