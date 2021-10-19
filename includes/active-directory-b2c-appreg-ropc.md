---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/08/2021
ms.author: mimart
ms.openlocfilehash: b3bf18bcc0c20407f9c0d4722b95266dde31d731
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708610"
---
Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie die unsere neue einheitliche Benutzeroberfläche **App-Registrierungen** oder unsere alte Benutzeroberfläche **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](../articles/active-directory-b2c/app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält:
    1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
    1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Zum Beispiel *ROPC_Auth_app*.
1. Übernehmen Sie die anderen Werte unverändert, und wählen Sie dann **Registrieren** aus.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Wählen Sie **Neue Benutzeroberfläche ausprobieren** aus (sofern die Option angezeigt wird).
1. Klicken Sie unter **Erweiterte Einstellungen** im Abschnitt **Folgende Mobilgerät- und Desktopflows aktivieren** auf **Ja**, um die Anwendung als öffentlichen Client zu behandeln. Diese Einstellung ist für den ROPC-Flow erforderlich.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie im linken Menü **Manifest** aus, um den Manifest-Editor zu öffnen. 
1. Legen Sie das Attribut **oauth2AllowImplicitFlow** auf *true* fest:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Wählen Sie **Speichern** aus.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält:
    1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
    1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Anwendungen (Legacy)** und dann **Hinzufügen** aus.
1. Geben Sie einen Namen für die Anwendung ein. Zum Beispiel *ROPC_Auth_app*.
1. Wählen Sie unter **Nativer Client** die Option **Ja** aus.
1. Übernehmen Sie die anderen Werte unverändert, und wählen Sie dann **Erstellen** aus.
1. Notieren Sie sich die **ANWENDUNGS-ID** zur Verwendung in einem späteren Schritt.