---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: kengaderdus
ms.openlocfilehash: e840d71b813432829a4eba4a33d50ff2b664222e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066313"
---
## <a name="configure-the-relying-party-policy"></a>Konfigurieren einer Richtlinie für die vertrauende Seite

Die Richtlinie für die vertrauende Seite (z. B. [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)) gibt die User Journey an, die Azure AD B2C ausführt. Suchen Sie das **DefaultUserJourney**-Element in [Vertrauende Seite](../articles/active-directory-b2c/relyingparty.md). Aktualisieren Sie die **ReferenceId** entsprechend der ID der User Journey, in der Sie den Identitätsanbieter hinzugefügt haben. 

Im folgenden Beispiel wird die **ReferenceId** für die User Journey `CustomSignUpOrSignIn` auf `CustomSignUpOrSignIn` festgelegt:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Hochladen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die beiden geänderten Richtliniendateien in der folgenden Reihenfolge hoch: zuerst die Erweiterungsrichtlinie (z. B. `TrustFrameworkExtensions.xml`) und dann die Richtlinie für die vertrauende Seite (z. B. `SignUpSignIn.xml`).



