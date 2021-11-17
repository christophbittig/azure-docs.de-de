---
title: 'Schnellstart: Aktivieren des einmaligen Anmeldens für eine Unternehmensanwendung'
titleSuffix: Azure AD
description: Aktivieren Sie einmaliges Anmelden für eine Unternehmensanwendungen in Azure Active Directory.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/21/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: 337561853bb34c8ecc67483dc8b94448b0b853a1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548518"
---
# <a name="quickstart-enable-single-sign-on-for-an-enterprise-application"></a>Schnellstart: Aktivieren des einmaligen Anmeldens für eine Unternehmensanwendung

In dieser Schnellstartanleitung verwenden Sie Azure Active Directory Admin Center, um einmaliges Anmelden (Single Sign-On, SSO) für eine Unternehmensanwendung zu aktivieren, die Sie Ihrem Azure AD-Mandanten (Azure Active Directory) hinzugefügt haben. Nachdem Sie SSO konfiguriert haben, können sich Ihre Benutzer mit ihren Azure AD-Anmeldeinformationen anmelden. 

Azure AD enthält einen Katalog mit Tausenden von vorab integrierten Anwendungen, die einmaliges Anmelden nutzen. In dieser Schnellstartanleitung wird die Unternehmensanwendung **Azure AD SAML Toolkit** als Beispiel verwendet, aber die Konzepte gelten für die meisten vorkonfigurierten [Unternehmensanwendungen im Katalog](../saas-apps/tutorial-list.md).

Es wird empfohlen, zum Testen der Schritte in dieser Schnellstartanleitung keine Produktionsumgebung zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren des einmaligen Anmeldens benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Ausführen der Schritte unter [Schnellstart: Zuweisen von Benutzern zu einer Anwendung](add-application-portal-assign-users.md)

## <a name="enable-single-sign-on"></a>Einmaliges Anmelden aktivieren

So aktivieren Sie einmaliges Anmelden für eine Anwendung:

1. Navigieren Sie zu [Azure Active Directory Admin Center](https://aad.portal.azure.com), und melden Sie sich mit einer der in den Voraussetzungen aufgeführten Rollen an.
1. Wählen Sie im Menü auf der linken Seite die Option **Unternehmensanwendungen** aus. Der Bereich **Alle Anwendungen** wird geöffnet und zeigt eine Liste der Anwendungen in Ihrem Azure AD-Mandanten an. Suchen Sie nach der gewünschten Anwendung, und wählen Sie diese aus. Beispiel: **Azure AD SAML Toolkit 1**
1. Wählen Sie im Menü auf der linken Seite im Abschnitt **Verwalten** die Option **Einmaliges Anmelden** aus, um den Bereich **Einmaliges Anmelden** zur Bearbeitung zu öffnen.
1. Wählen Sie **SAML** aus, um die Seite für die SSO-Konfiguration zu öffnen. Nach dem Konfigurieren der Anwendung können sich Benutzer mit ihren Anmeldeinformationen des Azure AD-Mandanten bei ihr anmelden.
1. Der Prozess zum Konfigurieren einer Anwendung für die Verwendung von Azure AD für SAML-basiertes SSO variiert je nach Anwendung. Verwenden Sie für die Unternehmensanwendungen im Katalog den Link, um Informationen zu den Schritten zu erhalten, die zum Konfigurieren der Anwendung erforderlich sind. Die Schritte für **Azure AD SAML Toolkit** sind in dieser Schnellstartanleitung aufgeführt.

    :::image type="content" source="media/add-application-portal-setup-sso/saml-configuration.png" alt-text="Konfigurieren des einmaligen Anmeldens für eine Unternehmensanwendung":::

1. Notieren Sie sich im Abschnitt **Set up Azure AD SAML Toolkit 1** (Azure AD SAML Toolkit 1 einrichten) die Werte der Eigenschaften **Anmelde-URL**, **Azure AD-Bezeichner** und **Abmelde-URL** zur späteren Verwendung.

## <a name="configure-single-sign-on-in-the-tenant"></a>Konfigurieren des einmaligen Anmeldens im Mandanten

Sie fügen Werte für Anmelde- und Antwort-URL hinzu und laden ein Zertifikat herunter, um mit der Konfiguration des einmaligen Anmeldens in Azure AD zu beginnen.

So konfigurieren Sie einmaliges Anmelden in Azure AD:

1. Wählen Sie im Azure-Portal im Bereich **Einmaliges Anmelden einrichten** im Abschnitt **Grundlegende SAML-Konfiguration** die Option **Bearbeiten** aus. 
1. Geben Sie unter **Antwort-URL (Assertionsverbraucherdienst-URL)** die URL `https://samltoolkit.azurewebsites.net/SAML/Consume` ein.
1. Geben Sie unter **Anmelde-URL** die URL `https://samltoolkit.azurewebsites.net/` ein.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** unter **Zertifikat (Rohdaten)** die Option **Herunterladen** aus, um das SAML-Signaturzertifikat herunterzuladen und zur späteren Verwendung zu speichern.

## <a name="configure-single-sign-on-in-the-application"></a>Konfigurieren des einmaligen Anmeldens in der Anwendung

Bei Verwendung des einmaligen Anmeldens in der Anwendung müssen Sie das Benutzerkonto bei der Anwendung registrieren und die zuvor notierten SAML-Konfigurationswerte hinzufügen.

### <a name="register-the-user-account"></a>Registrieren des Benutzerkontos

So registrieren Sie ein Benutzerkonto bei der Anwendung:

1. Öffnen Sie ein neues Browserfenster, und navigieren Sie zur Anmelde-URL für die Anwendung. Für die Anwendung **Azure AD SAML Toolkit** lautet die Adresse `https://samltoolkit.azurewebsites.net`.
1. Wählen Sie oben rechts auf der Seite **Registrieren** aus.

    :::image type="content" source="media/add-application-portal-setup-sso/toolkit-register.png" alt-text="Registrieren eines Benutzerkontos in der Anwendung „Azure AD SAML Toolkit“":::

1. Geben Sie unter **E-Mail-Adresse** die E-Mail-Adresse des Benutzers ein, der auf die Anwendung zugreifen wird. In einer vorherigen Schnellstartanleitung wurde beispielsweise das Benutzerkonto mit der Adresse `contosouser1@contoso.com` erstellt. Achten Sie darauf, `contoso.com` in die Domäne Ihres Mandanten zu ändern.
1. Geben Sie ein **Kennwort** ein, und bestätigen Sie es.
1. Wählen Sie **Registrieren**.

### <a name="configure-saml-settings"></a>Konfigurieren von SAML-Einstellungen

So konfigurieren Sie die SAML-Einstellung für die Anwendung:

1. Wenn Sie sich mit den Anmeldeinformationen des von Ihnen erstellten Benutzerkontos angemeldet haben, wählen Sie links oben auf der Seite die Option **SAML Configuration** (SAML-Konfiguration) aus.
1. Wählen Sie in der Mitte der Seite **Erstellen** aus.
1. Geben Sie unter **Anmelde-URL**, **Azure AD-Bezeichner** und **Abmelde-URL** die zuvor notierten Werte ein.
1. Wählen Sie **Datei auswählen** aus, um das zuvor heruntergeladene Zertifikat hochzuladen.
1. Wählen Sie **Erstellen** aus.
1. Kopieren Sie die Werte für **SP Initiated Login URL** (URL für SP-initiierte Anmeldung) und **Assertion Consumer Service (ACS) URL** (Assertionsverbraucherdienst-URL) zur späteren Verwendung.

## <a name="update-single-sign-on-values"></a>Aktualisieren der Werte für einmaliges Anmelden

Aktualisieren Sie die Werte für einmaliges Anmelden in Ihrem Mandanten mit den Werten für **SP Initiated Login URL** (URL für SP-initiierte Anmeldung) und **Assertion Consumer Service (ACS) URL** (Assertionsverbraucherdienst-URL).

So aktualisieren Sie die Werte für einmaliges Anmelden:

1. Wählen Sie im Azure-Portal im Bereich **Einmaliges Anmelden einrichten** im Abschnitt **Grundlegende SAML-Konfiguration** die Option **Bearbeiten** aus. 
1. Geben Sie im Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** den zuvor notierten Wert von **Assertion Consumer Service (ACS) URL** (Assertionsverbraucherdienst-URL) ein.
1. Geben Sie im Feld **Anmelde-URL** den zuvor notierten Wert von **SP Initiated Login URL** (URL für SP-initiierte Anmeldung) ein.
1. Wählen Sie **Speichern** aus.

## <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Sie können die Konfiguration des einmaligen Anmeldens im Bereich **Einmaliges Anmelden einrichten** testen.

So testen Sie einmaliges Anmelden:

1. Wählen Sie im Abschnitt **Einmaliges Anmelden mit Azure AD SAML Toolkit 1 testen** im Bereich **Einmaliges Anmelden einrichten** die Option **Testen** aus.
1. Melden Sie sich bei der Anwendung mit den Azure AD-Anmeldeinformationen des Benutzerkontos an, das Sie der Anwendung zugewiesen haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den nächsten Schnellstart durcharbeiten möchten, behalten Sie die von Ihnen erstellte Unternehmensanwendung. Andernfalls können Sie sie löschen, um Ihren Mandanten zu bereinigen. Weitere Informationen finden Sie unter [Löschen einer Anwendung](delete-application-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Konfigurieren der Eigenschaften einer Unternehmensanwendung:
> [!div class="nextstepaction"]
> [Konfigurieren einer Anwendung](add-application-portal-configure.md)
