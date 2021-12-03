---
title: 'Tutorial: Konfigurieren von Azure Active Directory B2C mit Akamai Web Application Firewall'
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren der Akamai Web Application Firewall mit Azure AD B2C
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ea95f9194913b4df00f0b75e7e44ab301b000b95
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283219"
---
# <a name="tutorial-configure-akamai-with-azure-active-directory-b2c"></a>Tutorial: Konfigurieren von Akamai mit Azure Active Directory B2C

In diesem Beispieltutorial erfahren Sie, wie Sie die Lösung [Akamai Web Application Firewall (WAF)](https://www.akamai.com/us/en/resources/web-application-firewall.jsp) für einen Azure Active Directory (AD) B2C-Mandanten mit benutzerdefinierten Domänen aktivieren. Akamai WAF unterstützt Organisationen dabei, ihre Webanwendungen vor böswilligen Angriffen zu schützen, die darauf abzielen, Sicherheitsrisiken wie die Einschleusung von SQL-Befehlen und Cross-Site-Skripting auszunutzen.

>[!NOTE]
>Dieses Feature befindet sich in der Phase der öffentlichen Vorschau.

Vorteile der Verwendung der Akamai WAF-Lösung:

- Die Edgeplattform ermöglicht die Datenverkehrsverwaltung für Ihre Dienste.

- Sie kann vor Ihrem Azure AD B2C-Mandanten konfiguriert werden.

- Sie ermöglicht eine differenzierte Steuerung des Datenverkehrs, um Ihre Identitätsinfrastruktur zu schützen.

Dieses Beispieltutorial gilt für beide WAF-Lösungen von Akamai: [Web Application Protector (WAP)](https://www.akamai.com/us/en/products/security/web-application-protector-enterprise-waf-firewall-ddos-protection.jsp) und [Kona Site Defender (KSD)](https://www.akamai.com/us/en/products/security/kona-site-defender.jsp).

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ein [Akamai WAF](https://www.akamai.com/us/en/akamai-free-trials.jsp)-Konto

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Akamai WAF-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C-Mandant**: Dabei handelt es sich um den Autorisierungsserver, der für die Überprüfung der Anmeldeinformationen des Benutzers gemäß den im Mandanten festgelegten benutzerdefinierten Richtlinien zuständig ist.  Er wird auch als Identitätsanbieter bezeichnet.

- [**Azure Front Door**](../frontdoor/front-door-overview.md): Ist für die Aktivierung benutzerdefinierter Domänen für Azure AD B2C-Mandanten zuständig. Der gesamte Datenverkehr von Cloudflare WAF wird an Azure Front Door weitergeleitet, bevor er bei Azure AD B2C-Mandanten eingeht.

- [**Akamai WAF:**](https://www.akamai.com/us/en/resources/waf.jsp) Webanwendungsfirewall, die den gesamten Datenverkehr verwaltet, der an den Autorisierungsserver gesendet wird

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

1. Um benutzerdefinierte Domänen in Azure AD B2C verwenden zu können, müssen Sie die von Azure Front Door bereitgestellte Funktion für benutzerdefinierte Domänen verwenden. Lesen Sie den Artikel [Aktivieren von benutzerdefinierten Domänen für Azure Active Directory B2C](./custom-domain.md?pivots=b2c-user-flow).  

2. Nachdem die benutzerdefinierte Domäne für Azure AD B2C erfolgreich mit Azure Front Door konfiguriert wurde, [testen Sie die benutzerdefinierte Domäne](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain), bevor Sie fortfahren.  

## <a name="onboard-with-akamai"></a>Onboarding bei Akamai

[Registrieren Sie sich](https://www.akamai.com), und erstellen Sie ein Akamai-Konto.

### <a name="create-and-configure-property"></a>Erstellen und Konfigurieren der Eigenschaft

1. [Erstellen Sie eine neue Eigenschaft.](https://control.akamai.com/wh/CUSTOMER/AKAMAI/en-US/WEBHELP/property-manager/property-manager-help/GUID-14BB87F2-282F-4C4A-8043-B422344884E6.html)

2. Konfigurieren Sie die Einstellungen der Eigenschaft wie folgt:

    | Eigenschaft | Wert |
    |:---------------|:---------------|
    |Eigenschaftsversion | Wählen Sie „Standard“ oder „Enhanced TLS“ (bevorzugt) aus. |
    |Hostnamen der Eigenschaft | Fügen Sie einen Hostnamen für die Eigenschaft hinzu. Dies ist der Name Ihrer benutzerdefinierten Domäne, z. B. login.domain.com. <BR> Erstellen oder ändern Sie ein Zertifikat mit den entsprechenden Einstellungen für den benutzerdefinierten Domänennamen. Weitere Informationen finden Sie [hier](https://learn.akamai.com/en-us/webhelp/property-manager/https-delivery-with-property-manager/GUID-9EE0EB6A-E62B-4F5F-9340-60CBD093A429.html). |

3. Legen Sie die Konfigurationseinstellungen für die Ursprungsservereigenschaft wie folgt fest:

    |Eigenschaft| Wert |
    |:-----------|:-----------|
    | Ursprungstyp | Ihr Ursprung |
    | Hostname des Ursprungsservers | yourafddomain.azurefd.net |
    | Hostheader für Weiterleitung | Eingehender Hostheader |
    | Hostname des Cacheschlüssels| Eingehender Hostheader |

### <a name="configure-dns"></a>Konfigurieren des DNS

Erstellen Sie einen CNAME-Eintrag in Ihrem DNS, z. B. login.domain.com, der auf den Edgehostnamen im Feld „Hostnamen der Eigenschaft“ verweist.

### <a name="configure-akamai-waf"></a>Konfigurieren von Akamai WAF

1. [Konfigurieren Sie Akamai WAF.](https://learn.akamai.com/en-us/webhelp/kona-site-defender/kona-site-defender-quick-start/GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A.html#GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A)

2. Stellen Sie sicher, dass **Rule Actions** (Regelaktionen) für alle unter der **Attack Group** (Angriffsgruppe) aufgeführten Elemente auf **Deny** (Verweigern) festgelegt ist.

![Abbildung der auf „Deny“ (Verweigern) festgelegten Regelaktion](./media/partner-akamai/rule-action-deny.png)

Erfahren Sie mehr über [die Funktionsweise des Steuerung und die Konfigurationsoptionen](https://control.akamai.com/dl/security/GUID-81C0214B-602A-4663-839D-68BCBFF41292.html).

<!-- docutune:ignore "Security Center" -->

### <a name="test-the-settings"></a>Testen der Einstellungen

Überprüfen Sie Folgendes, um sicherzustellen, dass der gesamte Datenverkehr an Azure AD B2C jetzt die benutzerdefinierte Domäne durchläuft:

- Stellen Sie sicher, dass alle eingehenden Anforderungen an die benutzerdefinierte Azure AD B2C-Domäne über Akamai WAF weitergeleitet werden und dass eine gültige TLS-Verbindung verwendet wird.
- Stellen Sie sicher, dass alle Cookies von Azure AD B2C ordnungsgemäß für die benutzerdefinierte Domäne festgelegt werden.
- Das Akamai-WAF-Dashboard, das Sie in der Defender-für-Cloud-Konsole finden, zeigt Diagramme für den gesamten Datenverkehr an, der die WAF durchläuft, sowie jeglichen Angriffsverkehr.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren einer benutzerdefinierten Domäne in Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
