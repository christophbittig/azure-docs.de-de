---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Cloudflare Web Application Firewall
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Cloudflare Web Application Firewall (WAF) zum Schutz Ihrer Anwendungen vor böswilligen Angriffen
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/24/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f2b4f2e54c046ca7abc2172b8df33f14def902fd
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767896"
---
# <a name="tutorial-configure-cloudflare-with-azure-active-directory-b2c"></a>Tutorial: Konfigurieren von Cloudflare mit Azure Active Directory B2C

In diesem Beispieltutorial erfahren Sie, wie Sie die Lösung [Cloudflare Web Application Firewall (WAF)](https://www.cloudflare.com/waf/) für einen Azure Active Directory (AD) B2C-Mandanten mit einer benutzerdefinierten Domäne aktivieren. Cloudflare WAF unterstützt Organisationen dabei, sich vor Angriffen zu schützen, die auf die Ausnutzung von Sicherheitsrisiken wie SQLi und XSS abzielen.

>[!NOTE]
>Dieses Feature befindet sich in der Phase der öffentlichen Vorschau.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ein [Cloudflare](https://dash.cloudflare.com/sign-up)-Konto.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Cloudflare WAF-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C-Mandant**: Dabei handelt es sich um den Autorisierungsserver, der für die Überprüfung der Anmeldeinformationen des Benutzers gemäß den im Mandanten festgelegten benutzerdefinierten Richtlinien zuständig ist.  Er wird auch als Identitätsanbieter bezeichnet.

- [**Azure Front Door**](../frontdoor/front-door-overview.md): Ist für die Aktivierung benutzerdefinierter Domänen für Azure AD B2C-Mandanten zuständig. Der gesamte Datenverkehr von Cloudflare WAF wird an Azure Front Door weitergeleitet, bevor er bei Azure AD B2C-Mandanten eingeht.

- **Cloudflare**: Die Webanwendungsfirewall (Web Application Firewall, WAF), die den gesamten Datenverkehr verwaltet, der an den Autorisierungsserver gesendet wird.

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

Um benutzerdefinierte Domänen in Azure AD B2C verwenden zu können, müssen Sie die von Azure Front Door bereitgestellte Funktion für benutzerdefinierte Domänen verwenden. Lesen Sie den Artikel [Aktivieren von benutzerdefinierten Domänen für Azure Active Directory B2C](./custom-domain.md?pivots=b2c-user-flow).  

Nachdem die benutzerdefinierte Domäne für Azure AD B2C erfolgreich mit Azure Front Door konfiguriert wurde, [testen Sie die benutzerdefinierte Domäne](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain), bevor Sie fortfahren.  

## <a name="onboard-with-cloudflare"></a>Durchführen des Onboardings mit Cloudflare

[Registrieren Sie sich](https://dash.cloudflare.com/sign-up), und erstellen Sie ein Cloudflare-Konto. Für die Aktivierung von WAF ist mindestens der [Pro-Tarif](https://www.cloudflare.com/plans/) erforderlich.

### <a name="configure-dns"></a>Konfigurieren des DNS

1. Um WAF für eine Domäne aktivieren zu können, müssen Sie über die DNS-Konsole für den CNAME-Eintrag **die Proxyeinstellung aktivieren**, wie im Beispiel für die Domäne „id.contosobank.co.uk“ gezeigt.

   ![Abbildung: Auswählen der Proxyeinstellungen](./media/partner-cloudflare/select-proxy-settings.png)

1. Schalten Sie die im DNS-Bereich verfügbare Option **Proxystatus** um.

1. Nachdem Sie die Option auf **Mit Proxy** umgestellt haben, wird ein orangefarbenes Symbol angezeigt. Die endgültige Einstellung sollte wie folgt aussehen:

   ![Abbildung: Auswählen der Option „Mit Proxy“](./media/partner-cloudflare/select-proxied.png)

### <a name="configure-waf"></a>Konfigurieren der WAF

[Konfigurieren Sie WAF](https://www.cloudflare.com/waf/) in Ihren Cloudflare-Einstellungen.

### <a name="configure-firewall-rule"></a>Konfigurieren der Firewallregel

Mithilfe der im oberen Konsolenbereich verfügbaren Firewalloption können Sie Firewallregeln hinzufügen, aktualisieren oder entfernen. Mit der folgenden Firewalleinstellung wird z. B. ein CAPTCHA für alle eingehenden Anforderungen für die Domäne *contosobank.co.uk* aktiviert, bevor die Anforderung an Azure Front Door gesendet wird. Weitere Informationen zu Firewallregeln finden Sie unter [Festlegen von Firewallregeln](https://support.cloudflare.com/hc/articles/360016473712-Cloudflare-Firewall-Rules).

![Abbildung: Erzwingen eines Captchas](./media/partner-cloudflare/configure-firewall-rule.png)

### <a name="test-the-settings"></a>Testen der Einstellungen

1. Nachdem Sie die Regel gespeichert haben, muss jedes Mal, wenn Zugriff auf die benutzerdefinierte Domäne angefordert wird, ein CAPTCHA eingegeben werden.

   ![Abbildung: Cloudflare WAF erzwingt ein CAPTCHA](./media/partner-cloudflare/enforce-captcha.png)

   > [!NOTE]
   > Cloudflare bietet auch verschiedene [**andere Optionen**](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-) zum Erstellen benutzerdefinierter Blockierungsseiten.  

2. Nach der erfolgreichen CAPTCHA-Eingabe wird der Benutzer zur Azure AD B2C-Richtlinien weitergeleitet.

   ![Abbildung der Azure AD B2C-Richtlinie für die Anmeldung](./media/partner-cloudflare/azure-ad-b2c-policy.png)


## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Beheben von Problemen mit benutzerdefinierten Cloudflare-Seiten](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-#5QWV2KVjLnaAQ8L4tjiguw)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)

## <a name="next-steps"></a>Nächste Schritte 
 
- [Konfigurieren einer benutzerdefinierten Domäne in Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow)
