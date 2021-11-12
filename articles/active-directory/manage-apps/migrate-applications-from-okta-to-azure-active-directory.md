---
title: Tutorial zum Migrieren Ihrer Anwendungen von Okta zu Azure Active Directory
titleSuffix: Active Directory
description: Erfahren Sie, wie Sie Ihre Anwendungen von Okta zu Azure Active Directory migrieren.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 25724ab674879d460c11e52a8360cb2c76a23a26
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451614"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>Tutorial: Migrieren Ihrer Anwendungen von Okta zu Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ihre Anwendungen von Okta zu Azure Active Directory (AD) migrieren.

## <a name="create-an-inventory-of-current-okta-applications"></a>Erstellen eines Bestands aktueller Okta-Anwendungen

Bevor Sie mit der Migration beginnen, sollten Sie die aktuellen Umgebungs- und Anwendungseinstellungen dokumentieren. Sie können die Okta-API verwenden, um diese Daten von einem zentralen Ort aus zu sammeln. Für die Verwendung der API benötigen Sie ein API-Explorer-Tool wie [Postman](https://www.postman.com/).

Führen Sie zum Erstellen eines Anwendungsbestands die folgenden Schritte aus:

1. Installieren der Postman-App. Generieren Sie anschließend über die Okta-Verwaltungskonsole ein API-Token.

1. Wählen Sie auf dem API-Dashboard unter **Security** (Sicherheit) **Tokens** > **Create Token** („Token“ > „Token erstellen“).

   ![Screenshot mit der Schaltfläche zum Erstellen eines Token.](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

1. Fügen Sie einen Tokennamen ein, und klicken Sie dann auf **Create Token** (Token erstellen).

   ![Screenshot, der zeigt, wo Sie den Namen für das Token eingeben.](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

1. Notieren und speichern Sie den Tokenwert. Der Zugriff ist erst möglich, nachdem Sie **OK, got it** (OK, verstanden) ausgewählt haben.

   ![Screenshot mit dem Feld für den Tokenwert.](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

1. Wählen Sie im Arbeitsbereich in der Postman-App **Import** (Importieren) aus.

   ![Screenshot mit der Import-API.](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

1. Wählen Sie auf der Seite **Import** (Importieren) **Link** aus. Fügen Sie dann den folgenden Link ein, um die API zu importieren: `https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment`

   ![Screenshot mit dem Link für den Import.](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

   >[!NOTE]
   >Ändern Sie den Link nicht in Ihre Mandantenwerte um.

1. Wählen Sie zum Fortfahren **Import** (Importieren) aus.

   ![Screenshot der nächsten Import-Seite.](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

1. Ändern Sie nach dem API-Import die Auswahl **Environment** (Umgebung) in **{yourOktaDomain}** .

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png" alt-text="Screenshot, der das Ändern der Umgebung zeigt." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png":::

1. Klicken Sie auf das Augensymbol, um Ihre Okta-Umgebung zu bearbeiten. Klicken Sie dann auf **Bearbeiten**.

   ![Screenshot, der das Bearbeiten der Okta-Umgebung zeigt.](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

1. Aktualisieren Sie die Werte für die URL und den API-Schlüssel in den Feldern **Initial Value** (Anfangswert) und **Current Value** (Aktueller Wert). Ändern Sie den Namen, um Ihre Umgebung widerzuspiegeln. Speichern Sie dann die Werte.

    ![Screenshot, der zeigt, wie die API-Werte aktualisiert werden.](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

1. [Laden Sie die API in Postman.](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17)

1. Wählen Sie **Apps** > **Get List Apps** > **Send** („Anwendungen“ > „Anwendungsliste abrufen“ > „Senden“).

   Jetzt können Sie alle Anwendungen in Ihrem Okta-Mandanten drucken. Die Liste liegt im JSON-Format vor.

    ![Screenshot der Liste der Anwendungen im Okta-Mandanten.](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

Es wird empfohlen, diese JSON-Liste zu kopieren und ins CSV-Format umzuwandeln. Dafür können Sie einen öffentlichen Konverter wie [Konklone](https://konklone.io/json/) verwenden. In PowerShell können Sie auch [Konvertieren von-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) (CovertFrom-Json) und [Konvertieren nach-CSV](/powershell/module/microsoft.powershell.utility/convertto-csv) (ConvertTo-CSV) verwenden.

Laden Sie die CSV-Datei herunter, um einen Datensatz der Anwendungen in Ihrem Okta-Mandanten für eine zukünftige Verwendung zu speichern.

## <a name="migrate-a-saml-application-to-azure-ad"></a>Migrieren einer SAML-Anwendung zu Azure AD

Um eine SAML 2.0-Anwendung zu Azure AD zu migrieren, konfigurieren Sie zuerst die Anwendung in Ihrem Azure AD-Mandanten für den Anwendungszugriff. In diesem Beispiel wird eine Salesforce-Instanz konvertiert. Befolgen Sie die Schritte in [diesem Tutorial](../saas-apps/salesforce-tutorial.md), um die Anwendungen zu konfigurieren.

Wiederholen Sie zum Abschließen des Migrationsvorgangs die Konfigurationsschritte für alle Anwendungen, die im Okta-Mandanten entdeckt wurden.

1. Klicken Sie im [Azure AD-Portal](https://aad.portal.azure.com) auf **Azure Active Directory** > **Unternehmensanwendungen** > **Neue Anwendung**.

   ![Screenshot von einer Liste der neuen Anwendungen.](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

1. Suchen Sie im **Azure AD-Katalog** nach **Salesforce**, wählen Sie die Anwendung aus, und klicken Sie dann auf **Erstellen**.

   ![Screenshot mit der Salesforce-Anwendung im Azure AD-Katalog.](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

1. Wählen Sie nach dem Erstellen der Anwendung auf der Registerkarte **Einmaliges Anmelden** (SSO) **SAML** aus.

   ![Screenshot der SAML-Anwendung.](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

1. Laden Sie das **Zertifikat (Rohdaten)** und die **Verbundmetadaten XML** für den Import in Salesforce herunter.

   ![Screenshot, der zeigt, wo die Verbundmetadaten heruntergeladen werden.](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

1. Klicken Sie auf der Salesforce-Verwaltungskonsole auf **Identity** > **Single sign-on Settings** > **New from Metadata File** („Identität“ > „Einzelanmeldungseinstellungen“ > „Neu aus Metadatendatei“).

   ![Screenshot der Salesforce-Verwaltungskonsole.](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

1. Laden Sie die aus dem Azure AD-Portal heruntergeladene XML-Datei hoch. Klicken Sie anschließend auf **Erstellen**.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png" alt-text="Screenshot, der zeigt, wo die XML-Datei hochgeladen werden soll." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png":::

1. Laden Sie das Zertifikat hoch, das Sie von Azure heruntergeladen haben. Klicken Sie dann auf **Save** (Speichern), um den SAML-Anbieter in Salesforce zu erstellen.

   ![Screenshot, der das Erstellen des SAML-Anbieters in Salesforce zeigt.](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

1. Notieren Sie die Werte in den folgenden Feldern. Diese Werte benötigen Sie in Azure.
   * **Entitäts-ID**
   * **Anmelde-URL**
   * **Abmelde-URL**

   Wählen Sie dann **Metadaten herunterladen** (Download Metadata) aus.

   ![Screenshot, der die Werte zeigt, die Sie für die Verwendung in Azure notieren müssen.](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

1. Wählen Sie auf der Azure AD-Seite für **Unternehmensanwendungen** in den SAML-SSO-Einstellungen **Metadatendatei hochladen** aus, um die Datei in Azure AD-Portal hochzuladen. Stellen Sie vor dem Speichern sicher, dass die importierten Werte mit den notierten Werten übereinstimmen.

   ![Screenshot der das Hochladen der Metadatendatei in Azure AD zeigt.](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

1. Klicken Sie in der Salesforce-Verwaltungskonsole auf **Company Settings** > **My Domain** („Unternehmenseinstellungen“ > „Meine Domäne“). Navigieren Sie zu **Authentication Configuration** (Authentifizierungskonfiguration), und klicken Sie auf **Edit** (Bearbeiten).

    ![Screenshot, der das Bearbeiten der Unternehmenseinstellungen zeigt.](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

1. Wählen Sie für eine Anmeldeoption den neuen SAML-Anbieter aus, den Sie zuvor konfiguriert haben. Klicken Sie dann auf **Speichern**.

    ![Screenshot, der zeigt, wo die SAML-Anbieter-Option gespeichert wird.](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

1. Klicken Sie in Azure AD auf der Seite **Unternehmensanwendungen** auf **Benutzer und Gruppen**. Fügen Sie dann Testbenutzer hinzu.

    ![Screenshot mit hinzugefügten Testbenutzern.](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

1. Melden Sie sich zum Testen der Konfiguration als einer der Testbenutzer an. Navigieren Sie zum Microsoft-[App-Katalog](https://aka.ms/myapps), und wählen Sie dann **Salesforce** aus.

    ![Screenshot, der das Öffnen von Salesforce über den App-Katalog zeigt.](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

1. Wählen Sie den neu konfigurierten Identitätsanbieter (IdP) für die Anmeldung aus.

    ![Screenshot, der zeigt, wo Sie sich anmelden können.](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

    Wenn alles ordnungsgemäß konfiguriert wurde, wird dem Testbenutzer die Salesforce-Startseite angezeigt. Informationen zur Problembehandlung finden Sie im [Debugging-Leitfaden](../manage-apps/debug-saml-sso-issues.md).

1. Weisen Sie auf der Seite **Unternehmensanwendungen** der Salesforce-Anwendung die verbleibenden Benutzer mit den richtigen Rollen zu.

    >[!NOTE]
    >Nachdem Sie die verbleibenden Benutzer der Azure AD Anwendung hinzugefügt haben, sollten die Benutzer die Verbindung testen und überprüfen, ob sie Zugriff haben. Testen Sie die Verbindung, bevor Sie mit dem nächsten Schritt fortfahren.

1. Klicken Sie in der Salesforce-Verwaltungskonsole auf **Company Settings** > **My Domain** („Unternehmenseinstellungen“ > „Meine Domäne“).

1. Klicken Sie unter **Authentication Configuration** (Authentifizierungskonfiguration) auf **Edit** (Bearbeiten). Heben Sie die Auswahl von **Okta** als Authentifizierungsdienst auf.

    ![Screenshot, der zeigt, wo die Auswahl von Okta als Authentifizierungsdienst aufgehoben wird.](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

Salesforce wurde nun erfolgreich mit Azure AD für SSO konfiguriert.

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>Migrieren einer OIDC-/OAuth 2.0-Anwendung zu Azure AD

Um eine OpenID Connect (OIDC)-Anwendung oder eine OAuth 2.0-Anwendung zu Azure AD zu migrieren, konfigurieren Sie zunächst in Ihrem Azure AD-Mandanten die Anwendung für den Zugriff. In diesem Beispiel konvertieren wir eine benutzerdefinierte OIDC-App.

Wiederholen Sie zum Abschließen des Migrationsvorgangs die folgenden Konfigurationsschritte für alle Anwendungen, die im Okta-Mandanten entdeckt wurden.

1. Wählen Sie im [Azure-Portal](https://aad.portal.azure.com) die Optionen **Azure Active Directory** > **Unternehmensanwendungen** aus. Wählen Sie unter **Alle Anwendungen** die Option **Neue Anwendung** aus.

1. Wählen Sie **Eigene Anwendung erstellen** aus. Geben Sie im daraufhin geöffneten Menü einen Namen für die OIDC-App ein, und wählen Sie dann **Von Ihnen bearbeitete Anwendung für die Integration in Azure AD registrieren** aus. Klicken Sie anschließend auf **Erstellen**.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png" alt-text="Screenshot, der das Erstellen einer OIDC-Anwendung zeigt." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png":::

1. Richten Sie auf der nächsten Seite den Mandant ihrer Anwendungsregistrierung ein. Weitere Informationen hierzu finden Sie unter [Mandanten in Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

   In diesem Beispiel wählen wir **Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig)**  > **Registrieren** aus.

   ![Screenshot, der die Auswahl des mehrinstanzenfähigen Azure AD-Verzeichnisses zeigt.](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

1. Öffnen Sie auf der Seite **App-Registrierungen** unter **Azure Active Directory** die neu erstellte Registrierung.

   Je nach [Anwendungsszenario](../develop/authentication-flows-app-scenarios.md) sind möglicherweise verschiedene Konfigurationsaktionen erforderlich. Die meisten Szenarios erfordern einen App-Clientschlüssel. Daher werden diese Beispiele beschrieben.

1. Notieren Sie auf der Seite **Übersicht** die **Anwendungs-ID (Client)** . Diese ID verwenden Sie in Ihrer Anwendung.

   ![Screenshot mit der Client-ID der Anwendung.](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

1. Wählen Sie links **Zertifikate und Geheimnisse** aus. Wählen Sie dann **Neuer geheimer Clientschlüssel** aus. Legen Sie einen Namen und ein Ablaufdatum für den Clientschlüssel fest.

   ![Screenshot des neuen Clientschlüssels.](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

1. Notieren Sie sich den Wert und die ID des Clientschlüssels.

   >[!NOTE]
   >Wenn Sie den geheimen Clientschlüssel verlieren, können Sie ihn nicht erneut abrufen. Stattdessen müssen Sie einen neuen Schlüssel generieren.

1. Wählen Sie links **API-Berechtigungen** aus. Gewähren Sie dann der Anwendung Zugriff auf den OIDC-Stapel.

1. Wählen Sie **Berechtigung hinzufügen** > **Microsoft Graph** > **Delegierte Berechtigungen** aus.

1. Wählen Sie im Bereich **OpenID-Berechtigungen** die Optionen **E-Mail**, **OpenID** und **Profil** aus. Klicken Sie dann auf **Berechtigungen hinzufügen**.

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png" alt-text="Screenshot, der zeigt, wo OpenID-Berechtigungen hinzugefügt werden." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png":::

1. Um die Benutzerfreundlichkeit zu verbessern und Eingabeaufforderungen für die Benutzereinwilligung zu unterdrücken, wählen Sie **Administratorzustimmung für „Mandantendomänenname“ erteilen** aus. Warten Sie dann, bis der Status **Gewährt** angezeigt wird.

    ![Screenshot, der zeigt, wo die Administratoreinwilligung erteilt wird.](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

1. Wenn Ihre Anwendung über einen Umleitungs-URI verfügt, geben Sie den entsprechenden URI ein. Wenn die Antwort-URL auf die Registerkarte **Authentifizierung** verweist, gefolgt von **Plattform hinzufügen** und **Web**, geben Sie die entsprechende URL ein. Wählen Sie **Zugriffstoken** und **ID-Token** aus. Klicken Sie anschließend auf **Konfigurieren**.

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png" alt-text="Screenshot, der das Konfigurieren von Token zeigt." lightbox="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png":::
    
    Wählen Sie bei Bedarf im Menü **Authentifizierung** unter **Erweiterte Einstellungen** und **Öffentliche Clientflows zulassen** **Ja** aus.

    ![Screenshot, der das Zulassen öffentlicher Clientflows zeigt.](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

1. Importieren Sie vor dem Testen in Ihrer OIDC-konfigurierten Anwendung die Anwendungs-ID und den Clientschlüssel. Führen Sie die vorherigen Schritte aus, um Ihre Anwendung mit Einstellungen wie Client-ID, Geheimnis und Geltungsbereichen zu konfigurieren.

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>Migrieren eines benutzerdefinierten Autorisierungsservers zu Azure AD

Okta-Autorisierungsserver ordnen Anwendungsregistrierungen, [die eine API verfügbar machen](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope), 1:1 zu.

Der standardmäßige Okta-Autorisierungsserver sollte Microsoft Graph-Bereichen bzw. -Berechtigungen zugeordnet werden.

![Screenshot der standardmäßigen Okta-Autorisierung.](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Migrieren des Okta-Verbunds zur verwalteten Azure Active Directory-Authentifizierung](migrate-okta-federation-to-azure-active-directory.md)
- [Migrieren der Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung](migrate-okta-sync-provisioning-to-azure-active-directory.md)
- [Migrieren von Okta-Anmelderichtlinien zu bedingtem Azure AD-Zugriff](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
