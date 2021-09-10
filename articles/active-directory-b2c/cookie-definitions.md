---
title: Cookiedefinitionen
titleSuffix: Azure AD B2C
description: Enthält Definitionen für die Cookies, die in Azure Active Directory B2C verwendet werden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0b2cae7f3c338ea5966ea8e37821e7ae0ebd746d
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343462"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Cookiedefinitionen für Azure AD B2C

Die nachstehenden Abschnitte enthalten Informationen zu den in Azure Active Directory B2C (Azure AD B2C) verwendeten Cookies.

## <a name="samesite"></a>SameSite

Der Dienst Microsoft Azure AD B2C ist kompatibel mit SameSite-Browserkonfigurationen, einschließlich des Supports für `SameSite=None` mit dem Attribut `Secure`.

Zum Schutz des Zugriffs auf Websites wird bei Webbrowsern ein neues standardmäßig sicheres Modell mit der Annahme eingeführt, dass alle Cookies vor externem Zugriff geschützt werden sollten, sofern nichts anderes angegeben wurde. Im Chrome-Browser wird diese Änderung als Erstes implementiert, und zwar ab [Chrome 80 im Februar 2020](https://www.chromium.org/updates/same-site). Weitere Informationen zur Vorbereitung auf die Änderung in Chrome finden Sie im Chromium-Blog unter [Developers: Get Ready for New SameSite=None; Secure Cookie Settings](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) (Entwickler: Vorbereiten für neue Einstellung „SameSite = None“. Sichere Cookieeinstellungen).

Entwickler müssen die neue Cookieeinstellung, `SameSite=None`, verwenden, um Cookies für websiteübergreifenden Zugriff festzulegen. Wenn das Attribut `SameSite=None` vorhanden ist, muss ein zusätzliches `Secure`-Attribut verwendet werden, damit auf websiteübergreifende Cookies nur über HTTPS-Verbindungen zugegriffen werden kann. Überprüfen und testen Sie alle Ihre Anwendungen, einschließlich der Anwendungen, die Azure AD B2C verwenden.

Weitere Informationen finden Sie unter:

* [Verarbeiten von SameSite-Cookieänderungen im Chrome-Browser](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Auswirkung auf Kundenwebsites sowie Microsoft-Dienste und -Produkte in Chrome, Version 80 oder höher](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

In der nachstehenden Tabelle werden die in Azure AD B2C verwendeten Cookies aufgeführt.

| Name | Domain | Ablauf | Zweck |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Ende der [Browsersitzung](session-behavior.md) | Enthält mandantenübergreifende Daten zur Benutzermitgliedschaft: Mandanten, zu denen ein Benutzer gehört, und die Ebene der Mitgliedschaft („Admin“ oder „Benutzer“). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md) | Zum Weiterleiten von Anforderungen an die entsprechende Produktionsinstanz. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md) | Zum Nachverfolgen von Transaktionen (Anzahl der Authentifizierungsanforderungen an Azure AD B2C) und der aktuellen Transaktion. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md) | Zum Verwalten der Sitzung mit einmaligem Anmelden (SSO). Dieses Cookie wird auf `persistent` festgelegt, wenn [Angemeldet bleiben](session-behavior.md#enable-keep-me-signed-in-kmsi) aktiviert ist.|
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md), erfolgreiche Authentifizierung | Zum Verwalten des Anforderungsstatus. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md) | Token der websiteübergreifenden Anforderungsfälschung zum Schutz vor CSRF-Angriffen. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md) | Für Azure AD B2C-Netzwerkrouting. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md) | Kontext |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md) | Zum Speichern der Mitgliedschaftsdaten für den Ressourceourcenanbieter-Mandanten. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, unternehmensbezogener Domänenname | Ende der [Browsersitzung](session-behavior.md) | Zum Speichern des Relay-Cookies. |
