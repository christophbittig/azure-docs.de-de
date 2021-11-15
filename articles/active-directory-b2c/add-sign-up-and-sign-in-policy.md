---
title: Einrichten eines Registrierungs- und Anmeldeflows
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie einen Registrierungs- und Anmeldeflow in Azure Active Directory B2C einrichten.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8b1607609bafabc210572717fd84bfcb25da8022
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231987"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Einrichten eines Registrierungs- und Anmeldeflows in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Registrierungs- und Anmeldeflow

Eine Registrierungs- und Anmelderichtlinie ermöglicht Benutzern Folgendes: 

* Registrieren mit einem lokalen Konto
* Anmelden mit einem lokalen Konto
* Registrieren oder Anmelden mit einem Social Media-Konto
* Zurücksetzen von Kennwörtern

![Profilbearbeitungsflow](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

Sehen Sie sich dieses Video an, um zu erfahren, wie die Benutzeranmelde- und Anmelderichtlinie funktioniert. 

>[!Video https://www.youtube.com/embed/c8rN1ZaR7wk]

## <a name="prerequisites"></a>Voraussetzungen

Wenn dies noch nicht erfolgt ist, [registrieren Sie eine Webanwendung in Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Erstellen eines Benutzerflows für Registrierung und Anmeldung

Der Benutzerflow für Registrierung und Anmeldung verarbeitet die Benutzeroberflächen für die Registrierung und Anmeldung in ein und derselben Konfiguration. Die Benutzer Ihrer Anwendung werden je nach Kontext auf den entsprechenden Pfad geleitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse + Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.

    ![Seite „Benutzerflows“ im Portal mit hervorgehobener Schaltfläche „Neuer Benutzerflow“](./media/add-sign-up-and-sign-in-policy/sign-up-sign-in-user-flow.png)

1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Registrierung und Anmeldung** aus.

    ![Seite „Benutzerflow auswählen“ mit hervorgehobenem „Anmeldung“ und „Anmeldeflow“](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus. (Weitere Informationen zu [Benutzerflowversionen](user-flow-versions.md))

    ![Seite „Benutzerflow erstellen“ im Azure-Portal mit hervorgehobenen Eigenschaften](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *signupsignin1*.
1. Wählen Sie unter **Identitätsanbieter** mindestens einen Identitätsanbieter aus:

   * Wählen Sie unter **Lokale Konten** eins der folgenden Konten aus: **E-Mail-Registrierung**, **Benutzer-ID-Registrierung**, **Registrierung per Telefon**, **Registrierung per Telefon/E-Mail** oder **Keins**. [Weitere Informationen](sign-in-options.md)
   * Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** einen der externen Anbieter für soziale oder Unternehmensidentitäten aus, die Sie eingerichtet haben. [Weitere Informationen](add-identity-provider.md)
1. Wenn Sie unter **Mehrstufige Authentifizierung** festlegen möchten, dass Benutzer ihre Identität anhand einer zweiten Authentifizierungsmethode verifizieren müssen, wählen Sie den Methodentyp und den Zeitpunkt aus, an dem die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erzwungen werden soll. [Weitere Informationen](multi-factor-authentication.md)
1. Wenn Sie unter **Bedingter Zugriff** Richtlinien für bedingten Zugriff für Ihren Azure AD B2C-Mandanten konfiguriert haben und sie für diesen Benutzerflow aktivieren möchten, aktivieren Sie das Kontrollkästchen **Richtlinien für bedingten Zugriff erzwingen**. Sie müssen keinen Richtliniennamen angeben. [Weitere Informationen](conditional-access-user-flow.md?pivots=b2c-user-flow)
1. Wählen Sie unter **Benutzerattribute und Tokenansprüche** die Attribute aus, die Sie vom Benutzer während der Registrierung erfassen möchten. Wählen Sie außerdem die Ansprüche aus, die im Token zurückgegeben werden sollen. Um eine vollständige Liste der Werte zu erhalten, wählen Sie **Mehr anzeigen** aus, wählen Sie die Werte aus, und wählen Sie dann **OK**.

   > [!NOTE]
   > Sie können auch [benutzerdefinierte Attribute erstellen](user-flow-custom-attributes.md?pivots=b2c-user-flow), die in Ihrem Azure AD B2C-Mandanten verwendet werden sollen.

    ![Auswahlseite für Attribute und Ansprüche mit drei ausgewählten Ansprüchen](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Wählen Sie **Erstellen** aus, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.
1. Führen Sie die Schritte für die [Verarbeitung des Ablaufs für "Kennwort vergessen?"](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) in der Registrierungs- oder Anmelderichtlinie aus.


### <a name="re-order-the-sign-up-form"></a>Neuanordnen des Registrierungsformulars
Erfahren Sie, [wie Sie Eingabefelder im Benutzerflow für lokale Konten neu anordnen](customize-ui.md#re-order-input-fields-in-the-sign-up-form)

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**, und wählen Sie dann **Jetzt registrieren** aus.

    ![Seite „Benutzerflow ausführen“ im Portal mit hervorgehobener Schaltfläche „Benutzerflow ausführen“](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. Geben Sie eine gültige E-Mail-Adresse ein, klicken Sie auf **Überprüfungscode senden**, geben Sie den Überprüfungscode ein, und wählen Sie dann **Code überprüfen** aus.
1. Geben Sie ein neues Kennwort ein, und bestätigen Sie es.
1. Wählen Sie das Land und die Region aus, geben Sie den anzuzeigenden Namen und eine Postleitzahl ein, und klicken Sie dann auf **Erstellen**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.
1. Sie können den Benutzerflow jetzt erneut ausführen. Sie sollten in der Lage sein, sich mit dem Konto, das Sie erstellt haben, anzumelden. Das zurückgegebene Token enthält die Ansprüche, die Sie für Land/Region, Name und Postleitzahl ausgewählt haben.

> [!NOTE]
> Die Benutzeroberfläche für „Benutzerflow ausführen“ ist derzeit nicht mit dem SPA-URL-Antworttyp kompatibel, für den der Autorisierungscodeflow verwendet wird. Wenn Sie die Benutzeroberfläche für „Benutzerflow ausführen“ mit dieser Art von App nutzen möchten, müssen Sie eine Antwort-URL vom Typ „Web“ registrieren und den impliziten Flow wie [hier](tutorial-register-spa.md) beschrieben aktivieren.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Erstellen einer Registrierungs- und Anmelderichtlinie

Benutzerdefinierte Richtlinien bestehen aus mehreren XML-Dateien, die Sie in den Azure AD B2C-Mandanten hochladen, um User Journeys zu definieren. Es sind Starter Packs mit mehreren integrierten Richtlinien verfügbar, z. B. für die Registrierung und Anmeldung, für die Kennwortzurücksetzung und für die Profilbearbeitung. Weitere Informationen finden Sie unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen eines Identitätsanbieters zu Ihrem Azure Active Directory B2C-Mandanten](add-identity-provider.md)
* [Einrichten eines Kennwortzurücksetzungsflows in Azure Active Directory B2C](add-password-reset-policy.md)
