---
title: Sicherer Hybridzugriff auf Azure AD | Microsoft-Dokumentation
description: In diesem Artikel werden Partnerlösungen für die Integration Ihrer lokalen Legacy-, Public Cloud- oder Private Cloud-Anwendungen in Azure AD beschrieben.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/17/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8294a0d3668077a1ead9ccb8b2f020360a75dc37
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273702"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Sicherer Hybridzugriff: Schützen von Legacy-Anwendungen mit Azure Active Directory

Sie können jetzt Ihre lokalen und cloudbasierten Legacy-Authentifizierungsanwendungen schützen, indem Sie sie mithilfe folgender Methoden mit Azure Active Directory (Azure AD) verbinden:

- [Azure AD-Anwendungsproxy](#secure-hybrid-access-through-azure-ad-application-proxy)

- [Partner für sicheren Hybridzugriff](#secure-hybrid-access-through-azure-ad-partner-integrations)

Mit Azure AD-Funktionen wie [Bedingter Zugriff](../conditional-access/overview.md) und [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) können Sie die Lücken schließen und den Sicherheitsstatus anwendungsübergreifend erhöhen. Mit Azure AD als Identitätsanbieter (Identity Provider, IDP) können Sie moderne Authentifizierungs- und Autorisierungsmethoden wie [Einmaliges Anmelden (Single Sign-On, SSO)](what-is-single-sign-on.md) und [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) von Azure AD verwenden, um Ihre lokalen Legacyanwendungen zu schützen.

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Sicherer Hybridzugriff über Azure AD-Anwendungsproxy
  
Mit dem [Anwendungsproxy](../app-proxy/what-is-application-proxy.md) können Sie einen [sicheren Remotezugriff](../app-proxy/application-proxy-add-on-premises-application.md) auf Ihre lokalen Webanwendungen bereitstellen. Ihre Benutzer müssen kein VPN verwenden. Benutzer profitieren nach dem [SSO](../app-proxy/application-proxy-config-sso-how-to.md#how-to-configure-single-sign-on) von einer einfachen Verbindung mit ihren Anwendungen von jedem Gerät aus. Der Anwendungsproxy stellt den Remotezugriff als Dienst bereit und ermöglicht die [einfache Veröffentlichung Ihrer lokalen Anwendungen](../app-proxy/application-proxy-add-on-premises-application.md) für Benutzer außerhalb des Unternehmensnetzwerks. Unterstützung bietet er auch bei der Skalierung Ihrer Cloudzugriffsverwaltung, ohne dass Sie Ihre lokalen Anwendungen ändern müssen. [Planen Sie die Bereitstellung eines Azure AD-Anwendungsproxys](../app-proxy/application-proxy-deployment-plan.md) als nächsten Schritt.

## <a name="secure-hybrid-access-through-azure-ad-partner-integrations"></a>Sicherer Hybridzugriff über Azure AD-Partnerintegrationen  

Zusätzlich zum [Azure AD-Anwendungsproxy](../app-proxy/what-is-application-proxy.md) unterhält Microsoft Partnerschaften mit Drittanbietern, um den sicheren Zugriff auf Ihre lokalen Anwendungen und Anwendungen, die die Legacyauthentifizierung verwenden, zu ermöglichen.

![Die Abbildung zeigt den sicheren Hybridzugriff mit App-Proxy und Partnern.](./media/secure-hybrid-access/secure-hybrid-access.png)

Die folgenden Partner bieten vorgefertigte Lösungen zur Unterstützung von **Richtlinien für bedingten Zugriff pro Anwendung** und stellen detaillierte Leitfäden für die Integration mit Azure AD bereit. 

- [Akamai Enterprise Application Access](../saas-apps/akamai-tutorial.md)

- [Citrix Application Delivery Controller (ADC)](../saas-apps/citrix-netscaler-tutorial.md)  

- [Datawiza Access Broker](../manage-apps/datawiza-with-azure-ad.md)

- [F5 Big-IP APM ADC](../manage-apps/f5-aad-integration.md)

- [F5 Big-IP APM VPN](../manage-apps/f5-aad-password-less-vpn.md)

- [Kemp](../saas-apps/kemp-tutorial.md)

- [Perimeter 81](../saas-apps/perimeter-81-tutorial.md)

- [Silverfort Authentication Platform](../manage-apps/silverfort-azure-ad-integration.md)

- [Strata](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md)

Die folgenden Partner bieten vorgefertigte Lösungen und eine ausführliche Anleitung für die Integration in Azure AD.

- [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

- [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

- [Palo Alto Networks Global Protect](../saas-apps/paloaltoadmin-tutorial.md)

- [Pulse Secure Pulse Connect Secure (PCS)](../saas-apps/pulse-secure-pcs-tutorial.md)

- [Pulse Secure Virtual Traffic Manager (VTM)](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)
