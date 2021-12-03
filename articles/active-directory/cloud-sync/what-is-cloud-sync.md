---
title: Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Azure AD Connect-Cloudsynchronisierung.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/07/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff693e9985f0c741a329d5e4bc17398461bb224d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424076"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung?
Die Azure AD Connect-Cloudsynchronisierung ist ein neues Angebot von Microsoft, das entwickelt wurde, um Ihre Ziele in Bezug auf die Hybrididentität für die Synchronisierung von Benutzern, Gruppen und Kontakten mit Azure AD zu erreichen und die entsprechenden Anforderungen zu erfüllen.  Hierfür wird nicht die Azure AD Connect-Anwendung verwendet, sondern der Agent für die Azure AD-Cloudbereitstellung.  Die Azure AD Connect-Cloudbereitstellung kann aber zusammen mit der Azure AD Connect-Synchronisierung verwendet werden. Dies hat die folgenden Vorteile:
    
- Unterstützung für die Synchronisierung mit einem Azure AD-Mandanten aus einer getrennten Active Directory-Gesamtstrukturumgebung mit mehreren Gesamtstrukturen: Zu den gängigen Szenarien gehören Fusionen und Übernahmen, bei denen die AD-Gesamtstrukturen des übernommenen Unternehmens von den AD-Gesamtstrukturen und Unternehmen der Muttergesellschaft isoliert sind, die in der Vergangenheit über mehrere AD-Gesamtstrukturen verfügten.
- Vereinfachte Installation mit einfachen Bereitstellungs-Agents: Die Agents fungieren als Brücke zwischen AD und Azure AD, wobei die gesamte Synchronisierungskonfiguration in der Cloud verwaltet wird. 
- Es können mehrere Bereitstellungs-Agents verwendet werden, um Bereitstellungen mit hoher Verfügbarkeit zu vereinfachen, was insbesondere für Organisationen wichtig ist, die die Kennworthashsynchronisierung zwischen AD und Azure AD verwenden müssen.
- Unterstützung für große Gruppen mit bis zu 50.000 Mitgliedern. Wir empfehlen Ihnen, beim Synchronisieren von großen Gruppen nur den Organisationseinheiten-Bereichsfilter zu verwenden.

![Was ist Azure AD Connect?](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Wie unterscheidet sich die Azure AD Connect-Cloudsynchronisierung von der Azure AD Connect-Synchronisierung?
Bei der Azure AD Connect-Cloudsynchronisierung wird die Bereitstellung von AD zu Azure AD in Microsoft Online Services orchestriert. Eine Organisation muss nur in ihrer lokalen oder IaaS-gehosteten Umgebung einen einfachen Agent bereitstellen, der als Brücke zwischen Azure AD und AD fungiert. Die Bereitstellungskonfiguration wird in Azure AD gespeichert und als Teil des Diensts verwaltet.

## <a name="azure-ad-connect-cloud-sync-video"></a>Video zur Azure AD Connect-Cloudsynchronisierung
Im folgenden kurzen Video erhalten Sie einen guten Überblick über die Azure AD Connect-Cloudsynchronisierung:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWJ8l5]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Vergleich zwischen Azure AD Connect und der Cloudsynchronisierung

Die folgende Tabelle enthält einen Vergleich zwischen der Azure AD Connect-Synchronisierung und der Azure AD Connect-Cloudsynchronisierung:

| Feature | Azure Active Directory Connect-Synchronisierung| Azure Active Directory Connect-Cloudsynchronisierung |
|:--- |:---:|:---:|
|Herstellen einer Verbindung mit einer einzelnen lokalen AD-Gesamtstruktur|● |● |
| Herstellen einer Verbindung mit mehreren lokalen AD-Gesamtstrukturen |● |● |
| Herstellen einer Verbindung mit mehreren getrennten lokalen AD-Gesamtstrukturen | |● |
| Installationsmodell mit einfachen Agents | |● |
| Mehrere aktive Agents für Hochverfügbarkeit | |● |
| Herstellen einer Verbindung mit LDAP-Verzeichnissen|●| | 
| Unterstützung für Benutzerobjekte |● |● |
| Unterstützung für Gruppenobjekte |● |● |
| Unterstützung für Kontaktobjekte |● |● |
| Unterstützung für Geräteobjekte |● | |
| Zulassen grundlegender Anpassungen von Attributflüssen |● |● |
| Synchronisieren von Exchange Online-Attributen |● |● |
| Synchronisieren der Erweiterungsattribute 1–15 |● |● |
| Synchronisieren von benutzerdefinierten AD-Attributen (Verzeichniserweiterungen) |● | |
| Unterstützung der Kennworthashsynchronisierung |●|●|
| Unterstützung der Passthrough-Authentifizierung |●||
| Verbundunterstützung |●|●|
| Nahtloses einmaliges Anmelden|● |●|
| Unterstützt die Installation auf einem Domänencontroller |● |● |
| Unterstützung für Windows Server 2016|● |● |
| Filtern nach Domänen/Organisationseinheiten/Gruppen |● |● |
| Filterung nach den Attributwerten eines Objekts |● | |
| Zulassen eines minimalen Attributsatzes für die Synchronisierung (MinSync) |● |● |
| Zulassen der Entfernung von Attributen aus dem Attributfluss von AD nach Azure AD |● |● |
| Zulassen einer erweiterten Anpassung des Attributflusses |● | |
| Unterstützung für das Kennwortrückschreiben |● |● |
| Unterstützung für das Geräterückschreiben|● | |
| Unterstützung für das Gruppenrückschreiben|● | |
| Azure AD Domain Services-Unterstützung|● | |
| [Exchange-Hybridrückschreiben](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Unbegrenzte Anzahl von Objekten pro AD-Domäne |● | |
| Unterstützung für bis zu 150.000 Objekte pro AD-Domäne |● |● |
| Gruppen mit bis zu 50.000 Mitgliedern |● |● |
| Große Gruppen mit bis zu 250.000 Mitgliedern |● |  |
| Domänenübergreifende Verweise|● | |
| Bedarfsorientierte Bereitstellung|● |● |
| Unterstützung für US Government|● |● |

## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Installieren der Cloudsynchronisierung](how-to-install.md)
