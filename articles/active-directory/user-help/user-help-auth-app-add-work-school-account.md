---
title: Hinzufügen eines Geschäfts-, Schul- oder Unikontos zur Microsoft Authenticator-App – Azure AD
description: Fügen Sie der Microsoft Authenticator-App Ihr Geschäfts-, Schul- oder Unikonto hinzu, um während der zweistufigen Überprüfung Ihre Identität zu bestätigen.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/09/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 276590a295b59465184a9afa2f7ce5efea7bb8eb
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350864"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Hinzufügen Ihres Geschäfts-, Schul- oder Unikontos in der Microsoft Authenticator-App

Wenn Ihre Organisation eine zweistufige Überprüfung verwendet, können Sie Ihr Geschäfts-, Schul- oder Unikonto für die Verwendung der Microsoft Authenticator-App als eine der Überprüfungsmethoden einrichten.

>[!Important]
>Bevor Sie Ihr Konto hinzufügen können, müssen Sie die Microsoft Authenticator-App herunterladen und installieren. Wenn Sie dies noch nicht getan haben, führen Sie die im Artikel [Herunterladen und Installieren der App](user-help-auth-app-download-install.md) aufgeführten Schritte aus.

## <a name="add-your-work-or-school-account"></a>Hinzufügen Ihres Geschäfts-, Schul- oder Unikontos

Sie können Ihr Geschäfts-, Schul- oder Unikonto zur Microsoft Authenticator-App hinzufügen, indem Sie einen der folgenden Schritte ausführen:

- Anmelden mit den Anmeldeinformationen Ihres Geschäfts-, Schul- oder Unikontos
- Scannen eines QR-Codes

### <a name="sign-in-with-your-credentials"></a>Mit Ihren Anmeldeinformationen anmelden

>[!Note]
>Sie können sich jetzt bei der Microsoft Authenticator-App anmelden, um Ihr Arbeits- oder Schulkonto hinzuzufügen.

So fügen Sie ein Konto hinzu, indem Sie sich mit Ihren Anmeldeinformationen bei Ihrem Geschäfts-, Schul- oder Unikonto anmelden:

1. Öffnen Sie die Microsoft Authenticator-App, wählen Sie die Schaltfläche **+** aus, und tippen Sie auf **Geschäfts-, Schul- oder Unikonto hinzufügen**. Wählen Sie **Anmelden**.

1. Geben Sie die Anmeldeinformationen Ihres Geschäfts-, Schul- oder Unikontos ein. Wenn Sie über einen befristeten Zugriffspass (Temporary Access Pass, TAP) verfügen, können Sie diesen für die Anmeldung verwenden. Zu diesem Zeitpunkt können Sie den Vorgang aufgrund einer der folgenden Bedingungen möglicherweise nicht fortsetzen:

   - Wenn Sie nicht über genügend Authentifizierungsmethoden für Ihr Konto verfügen, um ein sicheres Authentifizierungstoken zu erhalten, können Sie den Vorgang zum Hinzufügen eines Kontos nicht fortsetzen.

   - Wenn Sie die Meldung `You might be signing in from a location that is restricted by your admin` (Unter Umständen melden Sie sich von einem Standort an, der von Ihrem Administrator als unzulässig eingestuft wurde) erhalten, hat Ihr Administrator das Feature für Sie nicht aktiviert und wahrscheinlich eine Richtlinie für bedingten Zugriff vom Typ „Registrierung von Sicherheitsinformationen“ eingerichtet. Wenden Sie sich an den Administrator Ihres Geschäfts-, Schul- oder Unikontos, wenn Sie diese Authentifizierungsmethode nutzen möchten.

1. Wenn Ihnen die Anmeldung per Telefon über die Authenticator-App vom Administrator gestattet wurde, können Sie Ihr Gerät registrieren, um die kennwortlose Anmeldung per Telefon sowie Azure AD Multi-Factor Authentication einzurichten. Sie können die mehrstufige Authentifizierung aber weiterhin unabhängig davon einrichten, ob die Anmeldung per Telefon für Sie aktiviert ist.

1. An diesem Punkt werden Sie unter Umständen aufgefordert, einen von Ihrer Organisation bereitgestellten QR-Code zu scannen, um ein lokales Multi-Factor Authentication-Konto in der App einzurichten. Dies ist nur erforderlich, wenn Ihre Organisation einen lokalen MFA-Server verwendet.

1. Tippen Sie auf Ihrem Gerät auf das Konto, und überprüfen Sie in der Vollbildansicht, ob Ihre Kontoinformationen korrekt sind. Um die Sicherheit zu erhöhen, wird der Prüfcode alle 30 Sekunden geändert, um zu verhindern, dass jemand einen Code mehrmals verwendet.

## <a name="sign-in-with-a-qr-code"></a>Anmelden mit einem QR-Code

Gehen Sie folgendermaßen vor, um ein Konto durch Scannen eines QR-Codes hinzuzufügen:

1. Navigieren Sie auf Ihrem Computer zur Seite **Zusätzliche Sicherheitsüberprüfung**.

   >[!Note]
   >Wenn die Seite **Zusätzliche Sicherheitsüberprüfung** nicht angezeigt wird, hat Ihr Administrator möglicherweise die Sicherheitsinformation (Vorschau) aktiviert. Befolgen Sie in diesem Fall die Anweisungen im Abschnitt [Einrichten der Sicherheitsinformation zur Verwendung einer Authentifikator-App](security-info-setup-auth-app.md). Ist dies nicht der Fall, bitten Sie den Helpdesk Ihrer Organisation um Unterstützung. Weitere Informationen zu Sicherheitsinformationen finden Sie unter [Einrichten Ihrer Sicherheitsinformation über eine Anmeldeaufforderung](security-info-setup-signin.md).

1. Aktivieren Sie das Kontrollkästchen neben der Authenticator-App, und wählen Sie dann **Konfigurieren** aus. Die Seite **Mobile App konfigurieren** wird angezeigt.

   ![Bildschirm, der einen QR-Code enthält](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Öffnen Sie die Microsoft Authenticator-App, und wählen Sie das Pluszeichen ![Auswählen des Pluszeichens auf einem iOS- oder Android-Gerät](media/user-help-auth-app-add-work-school-account/plus-icon.png) und anschließend **Konto hinzufügen** aus. Wählen Sie dann **Geschäfts-, Schul- oder Unikonto** und anschließend **QR-Code scannen** aus.
   Wenn Sie kein Konto in der Authenticator-App eingerichtet haben, wird eine große blaue Schaltfläche mit der Aufschrift **Konto hinzufügen** angezeigt.

Wenn Sie nicht dazu aufgefordert werden, Ihre Kamera zum Scannen eines QR-Codes zu verwenden, stellen Sie in den Einstellungen Ihres Telefons sicher, dass die Authenticator-App Zugriff auf die Telefonkamera hat. Nachdem Sie Ihr Konto per QR-Code hinzugefügt haben, können Sie die Anmeldung per Telefon einrichten. Wenn Sie eine Meldung der Art „Unter Umständen melden Sie sich von einem Standort an, der von Ihrem Administrator als unzulässig eingestuft wurde“ erhalten, hat Ihr Administrator das Feature für Sie nicht aktiviert und wahrscheinlich eine Richtlinie für bedingten Zugriff vom Typ „Registrierung von Sicherheitsinformationen“ eingerichtet. Wenden Sie sich an den Administrator Ihres Geschäfts-, Schul- oder Unikontos, wenn Sie diese Authentifizierungsmethode nutzen möchten. Wenn Ihnen die Anmeldung per Telefon über die Authenticator-App vom Administrator *gestattet wurde*, können Sie Ihr Gerät registrieren, um die kennwortlose Anmeldung per Telefon sowie Azure AD Multi-Factor Authentication einzurichten.

>[!Note]
> Für US-Behörden besteht die einzige Möglichkeit zum Hinzufügen einer Anmeldung per Telefon darin, die Option zum [Anmelden mit Ihren Anmeldeinformationen](#sign-in-with-your-credentials) zu verwenden, anstatt das Upgrade von einem Konto auf Basis eines QR-Codes durchzuführen.

## <a name="sign-in-on-a-remote-computer"></a>Auf einem Remotecomputer anmelden

Viele Apps ermöglichen Ihnen die Authentifizierung durch Eingabe eines Codes auf einem anderen Gerät, z. B. einem PC. Gehen Sie folgendermaßen vor, um sich zur Installation der Microsoft Authenticator-App auf einem Remotecomputer anzumelden:

1. Öffnen Sie die Microsoft Authenticator-App und wählen Sie die **+** -Schaltfläche &gt;**Arbeits- oder Schulkonto hinzufügen** &gt; **Anmelden** aus.
1. Wählen Sie **Anmeldung über ein anderes Gerät** aus.
1. Öffnen Sie auf dem Remotebildschirm die [Seite **Bei Ihrem Konto anmelden**](https://microsoft.com/devicelogin) und geben Sie den Code ein, der in Ihrer Authenticator-App angezeigt wird.
1. Melden Sie sich auf dem Remotebildschirm mit den Anmeldeinformationen Ihres Arbeits- oder Schulkontos an. Wenn Sie über einen befristeten Zugriffspass (Temporary Access Pass, TAP) verfügen, können Sie diesen für die Anmeldung verwenden.
1. Nachdem Sie die Authentifizierung auf dem Remotebildschirm abgeschlossen haben, kehren Sie zur Authenticator-App zurück, um die Registrierung abzuschließen.

 ## <a name="next-steps"></a>Nächste Schritte

- Nachdem Ihre Konten der App hinzugefügt wurden, können Sie sich unter Verwendung der Authenticator-App auf Ihrem Gerät anmelden. Weitere Informationen finden Sie unter [Anmelden mithilfe der App](user-help-auth-app-sign-in.md).

- Für iOS-Geräte können Sie Ihre Kontoanmeldeinformationen und zugehörige App-Einstellungen, etwa die Reihenfolge Ihrer Konten, in der Cloud sichern. Weitere Informationen finden Sie unter [Sichern und Wiederherstellen von Kontoanmeldeinformationen mithilfe der Microsoft Authenticator-App](user-help-auth-app-backup-recovery.md).
