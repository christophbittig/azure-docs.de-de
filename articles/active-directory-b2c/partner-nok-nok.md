---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Nok Nok
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren von Nok Nok mit Azure Active Directory B2C zum Aktivieren der kennwortlosen FIDO2-Authentifizierung
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/04/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 586c8373e32f210d6f2f53c773fbe58ef5a2181b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346654"
---
# <a name="tutorial-configure-nok-nok-with-azure-active-directory-b2c-to-enable-passwordless-fido2-authentication"></a>Tutorial: Konfigurieren von Nok Nok mit Azure Active Directory B2C zum Aktivieren der kennwortlosen FIDO2-Authentifizierung

In diesem Beispieltutorial erfahren Sie, wie Sie die Nok Nok S3-Authentifizierungssammlung in Ihren Azure Active Directory B2C-Mandanten (AD) integrieren. [Nok Nok](https://noknok.com/) ermöglicht mobilen und Webanwendungen eine FIDO-zertifizierte mehrstufige Authentifizierung wie FIDO UAF, FIDO U2F, WebAuthn und FIDO2. Mit Nok Nok können Kunden ihren Sicherheitsstatus und die Benutzerfreundlichkeit verbessern.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Holen Sie sich einen kostenlosen Nok Nok-[Testmandanten](https://noknok.com/products/strong-authentication-service/).

## <a name="scenario-description"></a>Beschreibung des Szenarios

Um Ihren Benutzern die kennwortlose FIDO-Authentifizierung zu ermöglichen, aktivieren Sie Nok Nok für Ihren Azure AD B2C-Mandanten als Identitätsanbieter. Die Nok Nok-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C**: der Autorisierungsserver, der für die Überprüfung der Anmeldeinformationen des Benutzers zuständig ist.

- **Web- und mobile Anwendungen**: Ihre mobilen oder Webanwendungen, die Sie mit Nok Nok und Azure AD B2C schützen möchten.

- **Das SDK für Nok Nok-App oder die App „Nok Nok Passport“** : Anwendungen, die zur Authentifizierung von Azure AD B2C-fähigen Anwendungen verwendet werden. Diese Anwendungen sind im [Apple App Store](https://apps.apple.com/us/app/nok-nok-passport/id1050437340) und [Google Play Store](https://play.google.com/store/apps/details?id=com.noknok.android.passport2&hl=en&gl=US) verfügbar.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt. Nok Nok fungiert als Identitätsanbieter für Azure AD B2C mit Open ID Connect (OIDC), um die kennwortlose Authentifizierung zu ermöglichen.

![Abbildung: Architekturdiagramm von Nok Nok und Azure AD B2C](./media/partner-nok-nok/nok-nok-architecture-diagram.png)

| Schritt | BESCHREIBUNG |
|:------|:-----------|
| 1. | Der Benutzer gelangt auf eine Anmeldeseite. Der Benutzer wählt die Anmeldung oder Registrierung aus und gibt den Benutzernamen ein. |
| 2. | Azure AD B2C leitet den Benutzer an den OIDC-Authentifizierungsanbieter für Nok Nok um. |
| 3a. | Bei mobilen Authentifizierungen zeigt Nok Nok entweder einen QR-Code an oder sendet eine Pushbenachrichtigungsanforderung an das mobile Gerät des Endbenutzers. |
| 3b. | Bei der Desktop-/PC-basierten Anmeldung leitet Nok Nok den Endbenutzer zur Anmeldeseite der Webanwendung um, um eine kennwortlose Authentifizierungsaufforderung auszulösen. |
|4a. | Der Benutzer scannt den angezeigten QR-Code auf dem Smartphone mit dem SDK der Nok Nok- oder Nok Nok Passport-App.|
| 4b. | Der Benutzer gibt auf der Anmeldeseite der Webanwendung den Benutzernamen als Eingabe an und wählt „Weiter“ aus. |
| 5a. | Der Benutzer wird auf dem Smartphone zur Authentifizierung aufgefordert. <BR> Der Benutzer führt die kennwortlose Authentifizierung mithilfe der bevorzugten Methode durch, z. B. biometrisch, Geräte-PIN oder roamingbasiert.|
| 5b. | Der Benutzer wird in der Webanwendung zur Authentifizierung aufgefordert. <BR> Der Benutzer führt die kennwortlose Authentifizierung mithilfe der bevorzugten Methode durch, z. B. biometrisch, Geräte-PIN oder roamingbasiert. |
| 6. | Der Nok Nok-Server überprüft die FIDO-Assertion und sendet nach Überprüfung eine OIDC-Authentifizierungsantwort an Azure AD B2C.|
| 7. | Basierend auf der Antwort wird dem Benutzer der Zugriff gewährt oder verweigert. |

## <a name="onboard-with-nok-nok"></a>Onboarding bei Nok Nok

Füllen Sie das [Nok Nok-Cloudformular](https://noknok.com/contact/) aus, um einen eigenen Nok Nok-Mandanten zu erstellen. Nachdem Sie das Formular übermittelt haben, erhalten Sie eine E-Mail, in der erläutert wird, wie Sie auf Ihren Mandanten zugreifen. Die E-Mail ermöglicht auch den Zugriff auf Nok Nok-Leitfäden. Befolgen Sie die Anweisungen in der Nok Nok-Integrationsanleitung, um die OIDC-Konfiguration Ihres Nok Nok-Cloudmandanten abzuschließen.

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Hinzufügen eines neuen Identitätsanbieters

Führen Sie zum Hinzufügen eines neuen Identitätsanbieters die folgenden Schritte aus:

1. Melden Sie sich beim **[Azure-Portal](https://portal.azure.com/#home)** als globaler Administrator Ihres Azure AD B2C-Mandanten an.

2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü den **Verzeichnis- und Abonnementfilter** und das entsprechende Verzeichnis auswählen.

3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.

4. Navigieren Sie zu **Dashboard** > **Azure Active Directory B2C** >  **Identitätsanbieter**.

5. Wählen Sie **Identitätsanbieter** aus.

6. Wählen Sie **Hinzufügen**.

### <a name="configure-an-identity-provider"></a>Konfigurieren eines Identitätsanbieters 

Führen Sie zum Konfigurieren eines Identitätsanbieters die folgenden Schritte aus:

1. Wählen Sie **Identitätsanbietertyp** > **OpenID Connect (Vorschau)** aus.
2. Füllen Sie das Formular aus, um den Identitätsanbieter einzurichten:

   |Eigenschaft | Wert |
   |:-----| :-----------|
   | Name   | Nok Nok-Authentifizierungsanbieter |
   | Metadaten-URL | Fügen Sie den URI der gehosteten Nok Nok-Authentifizierungs-App gefolgt vom spezifischen Pfad ein, z. B. https://demo.noknok.com/mytenant/oidc/.well-known/openid-configuration. |
   | Geheimer Clientschlüssel | Verwenden Sie den geheimen Clientschlüssel, der von der Nok Nok-Plattform bereitgestellt wird.|
   | Client-ID | Verwenden Sie die Client-ID, die von der Nok Nok-Plattform bereitgestellt wird.|
   | `Scope` | E-Mail-Adresse aus OpenID-Profil |
   | Antworttyp | code |
   | Antwortmodus | form_post|

3. Klicken Sie auf **OK**.

4. Wählen Sie **Ansprüche dieses Identitätsanbieters zuordnen** aus.

5. Füllen Sie das Formular aus, um den Identitätsanbieter zuzuordnen:

   |Eigenschaft | Wert |
   |:-----| :-----------|
   | UserID    | Aus Abonnement |
   | `Display name` | Aus Abonnement |
   | Antwortmodus | Aus Abonnement |

6. Wählen Sie **Speichern** aus, um die Einrichtung Ihres neuen OIDC-Identitätsanbieters abzuschließen.

### <a name="create-a-user-flow-policy"></a>Erstellen einer Benutzerflowrichtlinie

Nok Nok sollte jetzt unter Ihren B2C-Identitätsanbietern als neuer OIDC-Identitätsanbieter aufgeführt sein.

1. Wählen Sie auf Ihrem Azure AD B2C-Mandanten unter **Richtlinien** die Option **Benutzerflows** aus.

2. Wählen Sie **Neuer Benutzerflow** aus.

3. Wählen Sie **Registrierung und Anmeldung**, anschließend eine **Version** und dann **Erstellen** aus.

4. Geben Sie einen **Namen** für die Richtlinie ein.

5. Wählen Sie im Abschnitt „Identitätsanbieter“ den neu erstellten Nok Nok-Identitätsanbieter aus.

6. Richten Sie die Parameter für den Benutzerflow ein. Fügen Sie einen Namen ein, und wählen Sie den von Ihnen erstellten Identitätsanbieter aus. Sie können auch eine E-Mail-Adresse hinzufügen. In diesem Fall leitet Azure die Anmeldeprozedur nicht direkt an Nok Nok um, sondern zeigt stattdessen einen Bildschirm an, auf dem der Benutzer die gewünschte Option auswählen kann.

7. Belassen Sie das Feld **Multi-Factor Authentication** unverändert.

8. Auswählen von **Richtlinien für bedingten Zugriff erzwingen**

9. Wählen Sie unter **Benutzerattribute und Tokenansprüche** für „Attribut sammeln“ die Option **E-Mail-Adresse** aus. Sie können alle Attribute hinzufügen, die Azure Active Directory über den Benutzer sammeln kann, sowie alle Ansprüche, die Azure AD B2C an die Clientanwendung zurückgeben kann.

10. Wählen Sie **Erstellen** aus.

11. Wählen Sie nach der erfolgreichen Erstellung den neuen **Benutzerflow** aus.

12. Wählen Sie im linken Bereich **Anwendungsansprüche** aus. Aktivieren Sie unter den Optionen das Kontrollkästchen **E-Mail**, und wählen Sie **Speichern** aus.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter „Richtlinien“ die Option Identity Experience Framework aus.

2. Wählen Sie den zuvor erstellten Flow SignUpSignIn aus.

3. Wählen Sie Benutzerflow ausführen und dann die Einstellungen aus:

   a. Anwendung: Wählen Sie die registrierte App aus (Beispiel: JWT).

   b. Antwort-URL: Wählen Sie die Umleitungs-URL aus.

   c. Wählen Sie Benutzerflow ausführen aus.

4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.

5. Nok Nok wird während des Flows aufgerufen, nachdem das Benutzerattribut erstellt wurde. Wenn der Flow unvollständig ist, stellen Sie sicher, dass der Benutzer nicht im Verzeichnis gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
