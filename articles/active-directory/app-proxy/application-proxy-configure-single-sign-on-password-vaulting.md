---
title: Einmaliges Anmelden bei Apps mit einem Azure Active Directory-Anwendungsproxy
description: Aktivieren Sie einmaliges Anmelden für Ihre veröffentlichten lokalen Anwendungen mit dem Azure Active Directory-Anwendungsproxy im Azure-Portal.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 2589dcd1c077d50c80f7e6ec82fdf75f82cba27f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989853"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Kennworttresore (Password Vaulting) für einmaliges Anmelden mit Anwendungsproxy

Mit dem Azure Active Directory-Anwendungsproxy können Sie die Produktivität steigern, indem Sie lokale Anwendungen so veröffentlichen, dass auch Remotemitarbeiter sicher darauf zugreifen können. Im Azure-Portal können Sie auch einmaliges Anmelden (Single Sign-On, SSO) für diese Apps einrichten. Ihre Benutzer müssen sich nur bei Azure AD authentifizieren. Dann können sie auf die Unternehmensanwendung zugreifen, ohne sich erneut anzumelden.

Der Anwendungsproxy unterstützt mehrere [Modi für einmaliges Anmelden](../manage-apps/sso-options.md#choosing-a-single-sign-on-method). Die kennwortbasierte Anmeldung ist für Anwendungen bestimmt, bei denen für die Authentifizierung eine Kombination aus Benutzername und Kennwort verwendet wird. Wenn Sie die kennwortbasierte Anmeldung für Ihre Anwendung konfigurieren, müssen sich Ihre Benutzer nur einmal an der lokalen Anwendung anmelden. Danach speichert Azure Active Directory die Anmeldeinformationen und stellt sie automatisch für die Anwendung bereit, wenn Ihre Benutzer von einem Remotestandort aus darauf zugreifen.

Sie sollten Ihre App bereits mit dem Anwendungsproxy veröffentlicht und getestet haben. Führen Sie andernfalls die Schritte unter [Veröffentlichen von Anwendungen mit dem Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md) aus, und fahren Sie dann hier fort.

## <a name="set-up-password-vaulting-for-your-application"></a>Einrichten von Kennworttresoren für Ihre Anwendung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
1. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste die App aus, für die SSO einrichten möchten.  
1. Wählen Sie **Anwendungsproxy**. 
1. Ändern Sie den **Typ für die Vorauthentifizierung** in **Passthrough**, und wählen Sie **Speichern** aus. Später können Sie dann wieder zurück zum Typ **Azure Active Directory** wechseln. 
1. Wählen Sie **Einmaliges Anmelden**.

   ![Auswählen von „Einmaliges Anmelden“ auf der Übersichtsseite der App](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Wählen Sie als SSO-Modus **Kennwortbasierte Anmeldung** aus.
1. Geben Sie als Anmelde-URL die URL der Seite ein, auf der die Benutzer ihren Benutzernamen und ihr Kennwort für die Anmeldung bei Ihrer App von außerhalb des Unternehmensnetzwerks eingeben. Dies kann die externe URL sein, die Sie erstellt haben, als Sie die App über den Anwendungsproxy veröffentlicht haben.

   ![Kennwortbasierte Anmeldung auswählen und URL eingeben](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Wählen Sie **Speichern** aus.
1. Wählen Sie **Anwendungsproxy**. 
1. Ändern Sie den **Typ für die Vorauthentifizierung** in **Azure Active Directory**, und wählen Sie **Speichern** aus. 
1. Wählen Sie **Benutzer und Gruppen**.
1. Weisen Sie Benutzer der Anwendung zu, indem Sie **Benutzer hinzufügen** auswählen. 
1. Wenn Sie Anmeldeinformationen für einen Benutzer vorab definieren möchten, aktivieren Sie das Kontrollkästchen vor dem Benutzernamen und wählen dann die Option **Anmeldeinformationen aktualisieren** aus.
1. Wählen Sie **Azure Active Directory** > **App-Registrierungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste die App aus, die Sie mit Kennwort-SSO konfiguriert haben.
1. Wählen Sie **Branding** aus. 
1. Aktualisieren Sie die **URL der Startseite** mit der **Anmelde-URL** über die Seite „Kennwort-SSO“, und wählen Sie **Speichern** aus.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testen Ihrer App

Navigieren Sie zum Portal „Meine Apps“. Melden Sie sich mit Ihren Anmeldeinformationen an (oder mit den Anmeldeinformationen für ein Testkonto, für das Sie den Zugriff eingerichtet haben). Klicken Sie nach dem erfolgreichen Anmelden auf das Symbol der App. Auf diese Weise kann unter Umständen die Installation der Browsererweiterung für die sichere Anmeldung von „Meine Apps“ ausgelöst werden. Wenn Ihr Benutzer über vordefinierte Anmeldeinformationen verfügt, sollte die Authentifizierung für die App automatisch durchgeführt werden. Andernfalls müssen Sie den Benutzernamen oder das Kennwort zum ersten Mal angeben. 

## <a name="next-steps"></a>Nächste Schritte

- Lernen Sie andere Möglichkeiten zum Implementieren des [einmaligen Anmeldens](../manage-apps/what-is-single-sign-on.md) kennen.
- Informieren Sie sich über die [Sicherheitsaspekte beim Remotezugriff auf Apps mit dem Azure AD-Anwendungsproxy](application-proxy-security.md).
