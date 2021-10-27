---
title: Hinzufügen eines Identitätsanbieters – Azure Active Directory B2C
description: Erfahren Sie, wie Sie Ihrem Azure Active Directory B2C-Mandanten einen Identitätsanbieter hinzufügen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.author: kengaderdus
ms.date: 04/29/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 92cbc796d4372c6127922ac6cac11d803a29b293
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039978"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Hinzufügen eines Identitätsanbieters zu Ihrem Azure Active Directory B2C-Mandanten

Sie können Azure AD B2C so konfigurieren, dass Benutzer sich bei Ihrer Anwendung mit Anmeldeinformationen von externen Identitätsanbietern (Identity Provider, IdP) für soziale Netzwerke oder Unternehmen anmelden können. Azure AD B2C unterstützt externe Identitätsanbieter wie Facebook, Microsoft-Konto, Google, Twitter und alle Identitätsanbieter, die OAuth 1.0, OAuth 2.0, OpenID Connect und SAML-Protokolle unterstützen.

Mit dem Verbund für externe Identitätsanbieter können Sie Ihren Kunden die Möglichkeit bieten, sich mit ihren vorhandenen Konten für soziale Netzwerke oder Unternehmen anzumelden, ohne dass sie ein neues Konto nur für Ihre Anwendung erstellen müssen.

Azure AD B2C bietet auf der Registrierungs- oder Anmeldeseite eine Liste externer Identitätsanbieter an, die der Benutzer für die Anmeldung auswählen kann. Nachdem sie einen der externen Identitätsanbieter ausgewählt haben, werden sie auf die Website des ausgewählten Anbieters weitergeleitet, um den Anmeldevorgang abzuschließen. Nachdem sich der Benutzer erfolgreich angemeldet hat, kehrt er zurück zu Azure AD B2C, um das Konto in Ihrer Anwendung zu authentifizieren.

![Beispiel für die mobile Anmeldung mit einem Konto für soziale Netzwerke (Facebook)](media/add-identity-provider/external-idp.png)

Sie können die von Azure Active Directory B2C (Azure AD B2C) unterstützten Identitätsanbieter über das Azure-Portal Ihren [Benutzerflows](user-flow-overview.md) hinzufügen. Sie können Identitätsanbieter auch zu [benutzerdefinierten Richtlinien](user-flow-overview.md) hinzufügen.

## <a name="select-an-identity-provider"></a>Auswählen eines Identitätsanbieters

In der Regel verwenden Sie nur einen Identitätsanbieter in Ihren Anwendungen, Sie haben aber die Möglichkeit, weitere hinzuzufügen. In den folgenden Artikeln erfahren Sie, wie Sie die Identitätsanbieteranwendung erstellen und den Identitätsanbieter Ihrem Mandanten sowie dem Benutzerflow oder der benutzerdefinierten Richtlinie hinzufügen.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Apple](identity-provider-apple-id.md)
* [Azure AD (ein Mandant)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (mehrere Mandanten)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [eBay](identity-provider-ebay.md)
* [Facebook](identity-provider-facebook.md)
* [Generischer Identitätsanbieter](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Microsoft-Konto](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (SAML-Protokoll)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
