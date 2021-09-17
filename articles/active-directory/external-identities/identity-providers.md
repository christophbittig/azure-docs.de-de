---
title: Identitätsanbieter für externe Identitäten – Azure AD
description: Erfahren Sie, wie Sie Azure AD als Standardidentitätsanbieter für die Freigabe für externe Benutzer verwenden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca0592741018f054640b1d695f643695e4782518
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222137"
---
# <a name="identity-providers-for-external-identities"></a>Identitätsanbieter für externe Identitäten

Ein *Identitätsanbieter* erstellt und verwaltet die Identitätsinformationen und stellt gleichzeitig Authentifizierungsdienste für Anwendungen bereit. Wenn Sie Ihre Apps und Ressourcen für externe Benutzer freigeben, ist Azure AD der Standardidentitätsanbieter für die Freigabe. Dies bedeutet Folgendes: Wenn Sie externe Benutzer einladen, die bereits über ein Azure AD- oder Microsoft-Konto verfügen, können sich diese Benutzer automatisch anmelden, ohne dass Sie weitere Konfigurationsschritte ausführen müssen.

External Identities bietet verschiedene Identitätsanbieter.

- **Azure Active Directory-Konten**: Gastbenutzer können ihre Azure AD-Arbeits- oder Schulkonten verwenden, um Ihre Einladungen für die B2B-Zusammenarbeit einzulösen oder Ihre Benutzerflows für die Registrierung abzuschließen. [Azure Active Directory](azure-ad-account.md) ist standardmäßig einer der zulässigen Identitätsanbieter. Es ist keine zusätzliche Konfiguration erforderlich, um diesen Identitätsanbieter für Benutzerflows verfügbar zu machen.

- **Microsoft-Konten**: Gastbenutzer können ihr eigenes persönliches Microsoft-Konto (MSA) verwenden, um Ihre Einladungen zur B2B-Zusammenarbeit anzunehmen. Wenn Sie einen Benutzerflow für die Self-Service-Registrierung einrichten, können Sie [Microsoft-Konto](microsoft-account.md) als einen der zulässigen Identitätsanbieter hinzufügen. Es ist keine zusätzliche Konfiguration erforderlich, um diesen Identitätsanbieter für Benutzerflows verfügbar zu machen.

- **Einmalkennung per E-Mail**: Wenn ein Gastbenutzer eine Einladung annehmen oder auf eine freigegebene Ressource zugreifen möchte, kann er einen temporären Code anfordern. Dieser wird dann an seine E-Mail-Adresse gesendet. Anschließend gibt er diesen Code ein, um den Anmeldevorgang fortzusetzen. Mit der Funktion „Einmalkennung per E-Mail“ werden B2B-Gastbenutzer authentifiziert, wenn sie über andere Wege nicht authentifiziert werden können. Wenn Sie einen Benutzerflow für die Self-Service-Registrierung einrichten, können Sie **Einmalkennung per E-Mail** als einen der zulässigen Identitätsanbieter hinzufügen. Es sind einige wenige Einrichtungsschritte erforderlich. Weitere Informationen finden Sie unter [Authentifizierung mit Einmalkennung per E-Mail](one-time-passcode.md).

- **Google**: Mit dem Google-Verbund können externe Benutzer Ihre Einladungen einlösen, indem sie sich mit ihren eigenen Gmail-Konten bei Ihren Apps anmelden. Der Google-Verbund kann auch in Ihren Benutzerflows mit Self-Service-Registrierung verwendet werden. Weitere Informationen finden Sie unter [Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer](google-federation.md).
   > [!IMPORTANT]
   >
   > - Wenn Azure AD B2B-Kunden ab dem **12. Juli 2021** neue Google-Integrationen für die Self-Service-Registrierung für ihre benutzerdefinierten oder branchenspezifischen Anwendungen einrichten, funktioniert die Authentifizierung mit Google-Identitäten erst, wenn Authentifizierungen in die Systemwebansichten verschoben werden. [Weitere Informationen](google-federation.md#deprecation-of-web-view-sign-in-support)
   > - Ab dem **30. September 2021** wird Google die [Unterstützung für die Anmeldung in der eingebetteten Webansicht einstellen](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Wenn Ihre Apps Benutzer mit einer eingebetteter Webansicht authentifizieren und Sie den Google-Verbund mit [ Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) oder Azure AD B2B für [externe Benutzereinladungen](google-federation.md) oder die Self-Service-Registrierung verwenden, können sich Google Gmail-Benutzer nicht authentifizieren. [Weitere Informationen](google-federation.md#deprecation-of-web-view-sign-in-support)


- **Facebook**: Beim Entwickeln einer App können Sie die Self-Service-Registrierung konfigurieren und den Facebook-Verbund aktivieren, damit sich Benutzer mit ihren eigenen Facebook-Konten für Ihre App registrieren können. Facebook kann nur für Benutzerflows mit Self-Service-Registrierung verwendet werden und steht nicht als Anmeldeoption zur Verfügung, wenn Benutzer Ihre Einladungen einlösen. Weitere Informationen finden Sie unter [Hinzufügen von Facebook als Identitätsanbieter für externe Identitäten](facebook-federation.md).

- **SAML/WS-Fed Identitätsanbieterverbund**: Sie können auch einen Verbund mit einem beliebigen externen IdP einrichten, der das SAML- oder WS-Fed-Protokoll unterstützt. Mit dem SAML/WS-Fed-IdP-Verbund können externe Benutzer Ihre Einladungen einlösen, indem sie sich mit ihren eigenen Social Media- oder Unternehmenskonten bei Ihren Apps anmelden. Wie das [Einrichten eines SAML-/WS-Fed-IdP-Verbunds](direct-federation.md) funktioniert.
   > [!NOTE]
   > SAML-/WS-Fed-IdP-Verbünde können nicht in Ihren Benutzerabläufen für die Self-Service-Anmeldung verwendet werden.

## <a name="adding-social-identity-providers"></a>Hinzufügen von sozialen Netzwerken als Identitätsanbieter

Azure AD ist standardmäßig für die Self-Service-Registrierung aktiviert. Daher haben Benutzer immer die Möglichkeit, sich mit einem Azure AD-Konto zu registrieren. Sie können jedoch auch andere Identitätsanbieter aktivieren. Hierzu zählen unter anderem auch soziale Netzwerke wie Google und Facebook. Um soziale Netzwerke als Identitätsanbieter in Ihrem Azure AD-Mandanten einzurichten, erstellen Sie bei jedem Identitätsanbieter eine Anwendung und konfigurieren die Anmeldeinformationen. Sie erhalten eine Client- oder App-ID sowie einen geheimen Client- oder App-Schlüssel, den Sie dann zu Ihrem Azure AD-Mandanten hinzufügen können.

Nachdem Sie Ihrem Azure AD-Mandanten einen Identitätsanbieter hinzugefügt haben, ist Folgendes möglich:

- Wenn Sie einen externen Benutzer zu Apps oder Ressourcen in Ihrer Organisation einladen, kann sich der externe Benutzer mit seinem eigenen Konto dieses Identitätsanbieters anmelden.
- Wenn Sie die [Self-Service-Registrierung](self-service-sign-up-overview.md) für Ihre Apps aktivieren, können sich externe Benutzer mit ihren eigenen Konten der von Ihnen hinzugefügten Identitätsanbieter bei Ihren Apps registrieren. Sie können aus den Optionen für soziale Medien als Identitätsanbieter wählen, die Sie auf der Registrierungsseite verfügbar gemacht haben:

   ![Screenshot des Anmeldebildschirms mit den Optionen für Google und Facebook](media/identity-providers/sign-in-with-social-identity.png)

Um für ein optimales Benutzererlebnis bei der Anmeldung zu sorgen, sollten Sie nach Möglichkeit einen Verbund mit Identitätsanbietern einrichten, damit Sie Ihren eingeladenen Gästen eine nahtlose Anmeldung ermöglichen, wenn sie auf Ihre Apps zugreifen.  

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um Informationen zum Hinzufügen von Identitätsanbietern für die Anmeldung bei Ihren Anwendungen zu erhalten:

- [Hinzufügen der Authentifizierung mit Einmalkennung per E-Mail](one-time-passcode.md)
- [Hinzufügen von Google](google-federation.md) als zulässiger Identitätsanbieter
- [Hinzufügen von Facebook](facebook-federation.md) als zulässiger Identitätsanbieter
- [Einrichten eines SAML-/WS-Fed-IdP-Verbunds](direct-federation.md) mit jeder Organisation, deren Identitätsanbieter das SAML 2.0- oder WS-Fed-Protokoll unterstützt. Für Benutzerflows für die Self-Service-Registrierung ist der SAML/WS-Fed-IdP-Verbund keine Option.
