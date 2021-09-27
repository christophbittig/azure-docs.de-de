---
title: Sitzungssteuerelemente in Richtlinien für bedingten Zugriff – Azure Active Directory
description: Was sind Sitzungssteuerelemente in einer Richtlinie für bedingten Zugriff in Azure AD?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f97d2c7ac7026032344f7f08f80cb28bcb0d3d71
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618956"
---
# <a name="conditional-access-session"></a>Bedingter Zugriff: Sitzung

In einer Richtlinie für bedingten Zugriff kann ein Administrator Sitzungssteuerelemente verwenden, um die Interaktionsmöglichkeiten innerhalb bestimmter Cloudanwendungen einzuschränken.

![Richtlinie für bedingten Zugriff mit einem Gewährungssteuerelement, durch das eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erforderlich ist](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Durch die Anwendung erzwungene Einschränkungen

Organisationen können dieses Steuerelement verwenden, um zu erzwingen, dass Azure AD die Geräteinformationen an die ausgewählten Cloud-Apps übergibt. Mithilfe der Geräteinformationen können Cloud-Apps herausfinden, ob eine Verbindung von einem konformen oder einem in die Domäne eingebundenen Gerät initiiert wird. Diese Steuermöglichkeit unterstützt nur SharePoint Online und Exchange Online als ausgewählte Cloud-Apps. Bei Auswahl werden die Geräteinformationen von der Cloud-App dazu verwendet, Benutzern je nach Gerätezustand eine eingeschränkte oder vollständige Benutzeroberfläche zur Verfügung zu stellen.

Weitere Informationen zum Verwenden und Konfigurieren von durch die App erzwungenen Einschränkungen finden Sie in den folgenden Artikeln:

- [Aktivieren des eingeschränkten Zugriffs mit SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Aktivieren des eingeschränkten Zugriffs mit Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Anwendungssteuerung für bedingten Zugriff

App-Steuerung für bedingten Zugriff verwendet eine Reverseproxyarchitektur und ist in bedingten Azure AD-Zugriff eindeutig integriert. Bedingter Azure AD-Zugriff ermöglicht Ihnen das Erzwingen von Zugriffssteuerungen für die Apps Ihrer Organisation auf der Grundlage bestimmter Bedingungen. Die Bedingungen definieren, auf wen (Benutzer oder Benutzergruppe), auf was (welche Cloud-Apps) und wo (welche Orte und Netzwerke) eine Richtlinie für bedingten Zugriff angewendet wird. Nachdem Sie die Bedingungen festgelegt haben, können Sie die Benutzer an [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) weiterleiten, wo Sie Daten mit der App-Steuerung für bedingten Zugriff schützen können, indem Sie Zugriffs- und Sitzungssteuerelemente anwenden.

Mit der App-Steuerung für bedingten Zugriff können Benutzerzugriffe auf Apps und Sitzungen auf der Grundlage von Zugriffs- und Sitzungsrichtlinien in Echtzeit überwacht und gesteuert werden. Zugriffs- und Sitzungsrichtlinien werden im Cloud App Security-Portal verwendet, um Filter weiter zu verfeinern und Aktionen festzulegen, die für einen Benutzer ausgeführt werden sollen. Mithilfe von Zugriffs- und Sitzungsrichtlinien können Sie die folgenden Aktionen ausführen:

- Datenexfiltration verhindern: Sie können das Herunterladen, Ausschneiden, Kopieren und Drucken von vertraulichen Dokumenten beispielsweise auf nicht verwalteten Geräten blockieren.
- Beim Herunterladen schützen: Statt das Herunterladen von vertraulichen Dokumenten zu blockieren, können Sie festlegen, dass Dokumente mit Azure Information Protection bezeichnet und geschützt werden müssen. Durch diese Aktion wird sichergestellt, dass das Dokument geschützt ist und der Benutzerzugriff in einer potenziell riskanten Sitzung eingeschränkt ist.
- Das Hochladen von Dateien ohne Bezeichnung verhindern: Bevor eine vertrauliche Datei hochgeladen, verteilt und von anderen Benutzern verwendet wird, müssen Sie unbedingt sicherstellen, dass die Datei über die richtige Bezeichnung und den richtigen Schutz verfügt. Sie können sicherstellen, dass unbezeichnete Dateien mit vertraulichen Inhalten erst hochgeladen werden, wenn der Benutzer den Inhalt klassifiziert hat.
- Benutzersitzungen auf Compliance überwachen (Vorschau): Riskante Benutzer werden überwacht, wenn sie sich bei Apps anmelden, und ihre Aktionen innerhalb der Sitzung werden protokolliert. Sie können das Benutzerverhalten untersuchen und analysieren, um zu verstehen, wo und unter welchen Bedingungen Sitzungsrichtlinien in Zukunft angewendet werden sollen.
- Zugriff blockieren (Vorschau): Sie können den Zugriff für bestimmte Apps und Benutzer abhängig von mehreren Risikofaktoren differenziert blockieren. Sie können sie z. B. blockieren, wenn sie Clientzertifikate als eine Form der Geräteverwaltung verwenden.
- Benutzerdefinierte Aktivitäten blockieren: Einige Apps werden in eindeutigen Szenarien verwendet, die Risiken bergen, wie z. B. das Senden von Nachrichten mit vertraulichen Inhalten in Apps wie Microsoft Teams oder Slack. In diesen Szenarien können Sie Nachrichten auf vertrauliche Inhalte überprüfen und in Echtzeit blockieren.

Weitere Informationen finden Sie im Artikel [Bereitstellen der App-Steuerung für bedingten Zugriff für ausgewählte Apps](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency"></a>Anmeldehäufigkeit

Die Anmeldehäufigkeit bezeichnet den Zeitraum, bevor ein Benutzer beim Zugriff auf eine Ressource aufgefordert wird, sich erneut anzumelden.

Die Einstellung für die Anmeldehäufigkeit funktioniert bei Apps mit standardkonformer Implementierung des OAUTH2- oder OIDC-Protokolls. Die meisten nativen Microsoft-Apps für Windows, Mac und mobile Umgebungen, einschließlich der folgenden Webanwendungen, sind mit der Einstellung kompatibel.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Microsoft 365-Verwaltungsportal
- Exchange Online
- SharePoint und OneDrive
- Teams-Webclient
- Dynamics CRM Online
- Azure-Portal

Weitere Informationen finden Sie im Artikel [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session"></a>Persistente Browsersitzung

Bei einer persistenten Browsersitzung können Benutzer angemeldet bleiben, nachdem sie ihr Browserfenster geschlossen und erneut geöffnet haben.

Weitere Informationen finden Sie im Artikel [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Nächste Schritte

- [Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)

- [Modus „Nur Bericht“](concept-conditional-access-report-only.md)
