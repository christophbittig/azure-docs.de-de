---
title: Sicherer Hybridzugriff auf Azure AD | Microsoft-Dokumentation
description: In diesem Artikel werden Partnerlösungen für die Integration Ihrer lokalen Legacy-, Public Cloud- oder Private Cloud-Anwendungen in Azure AD beschrieben.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/17/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a1447b059409a278850fccde31f43b4dad2dca8
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418645"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Sicherer Hybridzugriff: Schützen von Legacy-Anwendungen mit Azure Active Directory

Sie können jetzt Ihre lokalen und cloudbasierten Legacy-Authentifizierungsanwendungen schützen, indem Sie sie mithilfe folgender Methoden mit Azure Active Directory (Azure AD) verbinden:

- [Azure AD-Anwendungsproxy](#secure-hybrid-access-through-azure-ad-application-proxy)

- [Partner für sicheren Hybridzugriff](#secure-hybrid-access-through-azure-ad-partner-integrations)

Mit Azure AD-Funktionen wie [Bedingter Zugriff](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) und [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) können Sie die Lücken schließen und den Sicherheitsstatus anwendungsübergreifend erhöhen. Mit Azure AD als Identitätsanbieter (Identity Provider, IDP) können Sie moderne Authentifizierungs- und Autorisierungsmethoden wie [Einmaliges Anmelden (Single Sign-On, SSO)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) und [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) von Azure AD verwenden, um Ihre lokalen Legacyanwendungen zu schützen.

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Sicherer Hybridzugriff über Azure AD-Anwendungsproxy
  
Mit dem [Anwendungsproxy](https://docs.microsoft.com/azure/active-directory/app-proxy/what-is-application-proxy) können Sie einen [sicheren Remotezugriff](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-add-on-premises-application) auf Ihre lokalen Webanwendungen bereitstellen. Ihre Benutzer müssen kein VPN verwenden. Benutzer profitieren nach dem [SSO](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-config-sso-how-to#how-to-configure-single-sign-on) von einer einfachen Verbindung mit ihren Anwendungen von jedem Gerät aus. Der Anwendungsproxy stellt den Remotezugriff als Dienst bereit und ermöglicht die [einfache Veröffentlichung Ihrer lokalen Anwendungen](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-add-on-premises-application) für Benutzer außerhalb des Unternehmensnetzwerks. Unterstützung bietet er auch bei der Skalierung Ihrer Cloudzugriffsverwaltung, ohne dass Sie Ihre lokalen Anwendungen ändern müssen. [Planen Sie die Bereitstellung eines Azure AD-Anwendungsproxys](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-deployment-plan) als nächsten Schritt.

## <a name="secure-hybrid-access-through-azure-ad-partner-integrations"></a>Sicherer Hybridzugriff über Azure AD-Partnerintegrationen  

Zusätzlich zum [Azure AD-Anwendungsproxy](https://aka.ms/whyappproxy) unterhält Microsoft Partnerschaften mit Drittanbietern, um den sicheren Zugriff auf Ihre lokalen Anwendungen und Anwendungen, die die Legacyauthentifizierung verwenden, zu ermöglichen.

![Die Abbildung zeigt den sicheren Hybridzugriff mit App-Proxy und Partnern.](./media/secure-hybrid-access/secure-hybrid-access.png)

Die folgenden Partner bieten vorgefertigte Lösungen zur Unterstützung von Richtlinien für bedingten Zugriff pro Anwendung und stellen detaillierte Anleitungen für die Integration in Azure AD bereit. 

- [Akamai Enterprise Application Access](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix Application Delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)  

- [Datawiza Access Broker](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [F5 Big-IP APM ADC](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [F5 Big-IP APM VPN](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial)

- [Silverfort Authentication Platform](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

Die folgenden Partner bieten vorgefertigte Lösungen und eine ausführliche Anleitung für die Integration in Azure AD. 

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect Secure (PCS)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
