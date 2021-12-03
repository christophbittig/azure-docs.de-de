---
title: Konfigurieren eines Flows zum Erzwingen der Kennwortzurücksetzung in Azure AD B2C
titleSuffix: Azure AD B2C
description: Es wird beschrieben, wie Sie in Azure Active Directory B2C einen Flow zum Erzwingen der Kennwortzurücksetzung einrichten.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4c5802d9cd1fc9a7a41d38bc70e9d384882a1ea1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424323"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Einrichten eines Flows zum Erzwingen der Kennwortzurücksetzung in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

## <a name="overview"></a>Übersicht

Als Administrator können Sie [das Kennwort für einen Benutzer zurücksetzen](manage-users-portal.md#reset-a-users-password), wenn der Benutzer sein Kennwort vergessen hat. Es kann auch sein, dass Sie die Kennwortzurücksetzung erzwingen möchten. In diesem Artikel wird beschrieben, wie Sie in diesen Szenarien eine Kennwortzurücksetzung erzwingen.

Wenn ein Administrator das Kennwort eines Benutzers über das Azure-Portal zurücksetzt, wird der Wert des Attributs [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) auf `true` festgelegt. Während des [Anmeldungs- und Registrierungsablaufs](add-sign-up-and-sign-in-policy.md) wird der Wert dieses Attributs überprüft. Nachdem der Benutzer die Anmeldung durchgeführt hat, muss er sein Kennwort zurücksetzen, wenn das Attribut auf `true` festgelegt ist. Anschließend wird der Wert des Attributs wieder auf `false` festgelegt.

![Flow zum Erzwingen der Kennwortzurücksetzung](./media/force-password-reset/force-password-reset-flow.png)

Der Flow für die Kennwortzurücksetzung wird für lokale Konten in Azure AD B2C verwendet, bei denen für die Anmeldung eine [E-Mail-Adresse](sign-in-options.md#email-sign-in) oder ein [Benutzername](sign-in-options.md#username-sign-in) und ein Kennwort verwendet werden.

::: zone-end

::: zone pivot="b2c-custom-policy"

Dieses Feature ist derzeit nur für Benutzerflows verfügbar. Wählen Sie für die Einrichtungsschritte weiter oben **Benutzerflow** aus. Verwenden Sie für benutzerdefinierte Richtlinien das [GitHub-Beispiel](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset-first-logon) zum Erzwingen der Kennwortrücksetzung für die erste Anmeldung mit den unten angegebenen Voraussetzungen.

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="configure-your-user-flow"></a>Konfigurieren des Benutzerflows

Gehen Sie wie folgt vor, um die Einstellung **Erzwungene Kennwortzurücksetzung** in einem Flow für die Registrierung oder Anmeldung zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie den (**empfohlenen**) Benutzerflow für die Registrierung/Anmeldung oder nur für die Anmeldung aus, den Sie anpassen möchten.
1. Wählen Sie im linken Menü unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie unter **Kennwortkonfiguration** die Option **Erzwungene Kennwortzurücksetzung** aus.
1. Wählen Sie **Speichern** aus.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Melden Sie sich als Benutzer- oder Kennwortadministrator beim [Azure-Portal](https://portal.azure.com) an. Weitere Informationen zu den verfügbaren Rollen finden Sie unter [Zuweisen von Administratorrollen in Azure AD](../active-directory/roles/permissions-reference.md#all-roles).
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **Benutzer** aus. Suchen Sie nach dem Benutzer, den Sie zum Testen der Kennwortzurücksetzung verwenden möchten, und wählen Sie ihn aus. Wählen Sie anschließend die Option **Kennwort zurücksetzen** aus.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie einen (**empfohlenen**) Benutzerflow für die Registrierung oder Anmeldung aus, den Sie testen möchten.
1. Wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie **Benutzerflow ausführen** aus.
1. Melden Sie sich mit dem Benutzerkonto an, für das Sie das Kennwort zurücksetzen.
1. Nun müssen Sie das Kennwort für den Benutzer ändern. Ändern Sie das Kennwort, und klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

## <a name="force-password-reset-on-next-login"></a>Erzwingen der Kennwortrücksetzung bei der nächsten Anmeldung

Um das Zurücksetzen des Kennworts bei der nächsten Anmeldung zu erzwingen, aktualisieren Sie das Kontokennwortprofil mithilfe des MS Graph-Vorgangs [Benutzer aktualisieren](/graph/api/user-update). Im folgenden Beispiel wird das Attribut [ForceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) des Kennwortprofils auf `true` aktualisiert, wodurch der Benutzer gezwungen wird, das Kennwort bei der nächsten Anmeldung zurückzusetzen.

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
"passwordProfile": {
  "forceChangePasswordNextSignIn": true
}
```

Nachdem das Kontokennwortprofil festgelegt wurde, müssen Sie auch den Flow zum Erzwingen der Kennwortzurücksetzung konfigurieren. Dies ist in diesem Artikel beschrieben.

## <a name="force-a-password-reset-after-90-days"></a>Erzwingen einer Kennwortzurücksetzung nach 90 Tagen

Als Administrator können Sie [MS Graph](microsoft-graph-operations.md) verwenden, um die Gültigkeitsdauer eines Benutzerkennworts auf 90 Tage festzulegen. Nach 90 Tagen wird der Wert des Attributs [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) automatisch auf `true` festgelegt. Entfernen Sie den Wert `DisablePasswordExpiration` aus dem Attribut [Kennwortrichtlinie](user-profile-attributes.md#password-policy-attribute) des Benutzerprofils, um eine Kennwortzurücksetzung nach 90 Tagen zu erzwingen.

Im folgenden Beispiel wird die Kennwortrichtlinie auf `None` aktualisiert, wodurch eine Kennwortzurücksetzung nach 90 Tagen erzwungen wird:

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "None"
}
```

Wenn Sie die hohe [Kennwortkomplexität](password-complexity.md) deaktiviert haben, aktualisieren Sie die Kennwortrichtlinie auf [DisableStrongPassword](user-profile-attributes.md#password-policy-attribute):

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "DisableStrongPassword"
}
```

Nachdem eine Kennwortablaufrichtlinie festgelegt wurde, müssen Sie auch den Flow zum Erzwingen der Kennwortzurücksetzung konfigurieren. Dies ist in diesem Artikel beschrieben.

### <a name="password-expiry-duration"></a>Zeitraum bis zum Ablauf des Kennworts

Der Standardwert für die Kennwortablaufdauer beträgt **90** Tage. Der Wert kann im Azure Active Directory-Modul für Windows PowerShell mit dem Cmdlet [Set-MsolPasswordPolicy](/powershell/module/msonline/set-msolpasswordpolicy) konfiguriert werden. Mit diesem Befehl wird der Mandant aktualisiert, sodass die Kennwörter aller Benutzer nach der von Ihnen konfigurierten Anzahl von Tagen ablaufen.

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Richten Sie eine [Self-Service-Kennwortzurücksetzung](add-password-reset-policy.md) ein.
