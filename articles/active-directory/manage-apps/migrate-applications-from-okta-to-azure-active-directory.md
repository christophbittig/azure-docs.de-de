---
title: Tutorial zum Migrieren Ihrer Anwendungen von Okta zu Azure Active Directory
titleSuffix: Active Directory
description: Hier erfahren Sie, wie Sie Ihre Anwendungen von Okta zu Azure Active Directory migrieren.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: c46410a6998998ace9bc1a9e9809262970a131f2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791724"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>Tutorial: Migrieren Ihrer Anwendungen von Okta zu Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ihre Anwendungen von Okta zu Azure Active Directory (Azure AD) migrieren.

## <a name="create-an-inventory-of-current-okta-applications"></a>Erstellen eines Bestands aktueller Okta-Anwendungen

Beim Konvertieren von Okta-Anwendungen in Azure AD empfiehlt es sich, vor der Migration zuerst die aktuelle Umgebung und die Anwendungseinstellungen zu dokumentieren.

Okta bietet eine API, mit der diese Informationen von einem zentralen Ort aus gesammelt werden können. Für die Verwendung der API ist ein API-Explorer-Tool wie [Postman](https://www.postman.com/) erforderlich.

Führen Sie zum Erstellen eines Anwendungsbestands die folgenden Schritte aus:

1. Installieren der Postman-App. Generieren Sie nach der Installation über die Okta-Verwaltungskonsole ein API-Token.

2. Navigieren Sie zum API-Dashboard im Abschnitt „Security“ (Sicherheit), und klicken Sie auf **Tokens** > **Create Token** („Token“ > „Token erstellen“).

   ![Abbildung zur Tokenerstellung](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

3. Fügen Sie einen Tokennamen ein, und klicken Sie dann auf **Create Token** (Token erstellen).

   ![Abbildung zum erstellten Token](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

4. Nachdem Sie auf **Create Token** (Token erstellen) geklickt haben, notieren Sie sich den Wert, und speichern Sie ihn, da er nach dem Klicken auf **OK, got it** (OK, verstanden) nicht mehr zugänglich ist.

   ![Abbildung zum Notieren des Tokenwerts](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

5. Nachdem Sie sich das API-Token notiert haben, navigieren Sie zurück zur Postman-App, und klicken Sie im Arbeitsbereich auf **Import** (Importieren).

   ![Abbildung zum API-Import](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

6. Klicken Sie auf der Seite „Import“ (Importieren) auf **Link**, und verwenden Sie den folgenden Link zum Importieren der API: <https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment>.

   ![Abbildung zum Link, mit der die API importiert werden soll](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

>[!NOTE]
>Ändern Sie den Link nicht unter Verwendung Ihrer Mandantenwerte.

7. Fahren Sie mit dem nächsten Menü fort, indem Sie auf **Import** (Importieren) klicken.

   ![Abbildung zum nächsten Menü für den Import](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

8. Ändern Sie nach dem Importieren die Umgebungsauswahl in **{yourOktaDomain}** .

   ![Abbildung zum Ändern der Umgebung](media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png)

9. Nachdem Sie die Umgebungsauswahl geändert haben, bearbeiten Sie Ihre Okta-Umgebung, indem Sie auf das Auge und dann auf **Edit** (Bearbeiten) klicken.

   ![Abbildung zum Bearbeiten der Umgebung](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

10. Aktualisieren Sie die Werte für die URL und den API-Schlüssel in den Feldern **Initial Value** (Anfangswert) und **Current Value** (Aktueller Wert), ändern Sie den Namen entsprechend Ihrer Umgebung, und speichern Sie dann die Werte.

    ![Abbildung zum Aktualisieren von Werten für die API](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

11. Nachdem Sie den API-Schlüssel gespeichert haben, [laden Sie die API für die Apps in Postman hoch](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17).

12. Nachdem die API in Postman geladen wurde, klicken Sie auf das Dropdownmenü **Apps**, dann auf **GET List Apps** und anschließend auf **Send** (Senden).

Jetzt können Sie alle Anwendungen in Ihrem Okta-Mandanten ins JSON-Format drucken.

![Abbildung zur Liste der Anwendungen](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

Es wird empfohlen, diese JSON-Liste mithilfe eines öffentlichen Konverters wie <https://konklone.io/json/> oder PowerShell mit [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) und [ConvertTo-CSV](/powershell/module/microsoft.powershell.utility/convertto-csv) zu kopieren und ins CSV-Format zu konvertieren.

Nach dem Herunterladen der CSV-Datei wurden die Anwendungen in Ihrem Okta-Mandanten erfolgreich für die zukünftige Referenz erfasst.

## <a name="migrate-a-saml-application-to-azure-ad"></a>Migrieren einer SAML-Anwendung zu Azure AD

Um eine SAML 2.0-Anwendung zu Azure AD zu migrieren, konfigurieren Sie zuerst die Anwendung in Ihrem Azure AD-Mandanten für den Anwendungszugriff. In diesem Beispiel wird eine Salesforce-Instanz konvertiert. Befolgen Sie die Schritte in [diesem Tutorial](../saas-apps/salesforce-tutorial.md), um das Onboarding für die Anwendungen durchzuführen.

Wiederholen Sie zum Abschließen des Migrationsprozesses die Konfigurationsschritte für alle Anwendungen, die im Okta-Mandanten gefunden wurden.

1. Navigieren Sie zum [Azure AD-Portal](https://aad.portal.azure.com), und klicken Sie auf **Azure Active Directory** > **Unternehmensanwendungen** > **Neue Anwendung**.

   ![Abbildung zur Liste der neuen Anwendungen](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

2. Salesforce ist im Azure AD-Katalog verfügbar. Suchen Sie nach Salesforce, wählen Sie die Anwendung aus, und klicken Sie dann auf **Erstellen**.

   ![Abbildung zur Salesforce-Anwendung](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

3. Navigieren Sie nach dem Erstellen der Anwendung zur Registerkarte **Einmaliges Anmelden** (SSO), und wählen Sie **SAML** aus.

   ![Abbildung zur SAML-Anwendung](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

4. Nachdem Sie SAML ausgewählt haben, laden Sie die **Verbundmetadaten-XML-Datei und das Zertifikat (Rohdaten)** für den Import in Salesforce herunter.

   ![Abbildung zum Herunterladen von Verbundmetadaten](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

5. Nachdem die XML-Datei heruntergeladen wurde, navigieren Sie zur Salesforce-Verwaltungskonsole, und klicken Sie dann auf **Identity** > **Single sign-on Settings** > **New from Metadata File** („Identität“ > „Einzelanmeldungseinstellungen“ > „Neu aus Metadatendatei“).

   ![Abbildung zur Salesforce-Verwaltungskonsole](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

6. Laden Sie die aus dem Azure AD-Portal heruntergeladene XML-Datei hoch, und klicken Sie dann auf **Erstellen**.

   ![Abbildung zum Hochladen der XML-Datei](media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png)

7. Laden Sie das aus Azure heruntergeladene Zertifikat hoch, und klicken Sie dann im nächsten Menü auf **Speichern**, um den SAML-Anbieter in Salesforce zu erstellen.

   ![Abbildung zum Erstellen eines SAML-Anbieters](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

8. Notieren Sie sich für die Verwendung in Azure die Werte **Entitäts-ID**, **URL für Anmeldung** und **Abmelde-URL**, und klicken Sie dann auf die Option **Metadaten herunterladen**.

   ![Abbildung zu den erfassten Werten in Azure](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

9. Navigieren Sie zurück zum Azure AD-Menü für Unternehmensanwendungen, und laden Sie die Metadatendatei über die Option **Metadatendatei hochladen** in das Azure AD-Portal in die Einzelanmeldungseinstellungen hoch. Vergewissern Sie sich vor dem Speichern, dass die importierten Werte mit den notierten Werten übereinstimmen.

   ![Abbildung zum Hochladen der Metadatendatei in Azure AD](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

10. Nachdem die SSO-Konfiguration gespeichert wurde, navigieren Sie zurück zur Salesforce-Verwaltungskonsole, und klicken Sie auf **Company Settings** > **My Domain** („Unternehmenseinstellungen“ > „Meine Domäne“). Navigieren Sie zu **Authentication Configuration** (Authentifizierungskonfiguration), und klicken Sie auf **Edit** (Bearbeiten).

    ![Abbildung zum Bearbeiten von Unternehmenseinstellungen](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

11. Wählen Sie den neuen SAML-Anbieter aus, der in den vorherigen Schritten als verfügbare Anmeldeoption konfiguriert wurde, und klicken Sie auf **Speichern**.

    ![Abbildung zur Option zum Speichern des SAML-Anbieters](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

12. Navigieren Sie zurück zur Unternehmensanwendung in Azure AD, klicken Sie auf **Benutzer und Gruppen**, und fügen Sie **Testbenutzer** hinzu.

    ![Abbildung zum Hinzufügen von Testbenutzer*innen](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

13. Melden Sie sich zum Testen mit einem Konto der Testbenutzer*innen an, navigieren Sie zu <https://aka.ms/myapps>, und wählen Sie die Kachel **Salesforce** aus.

    ![Abbildung zum Anmelden als Testbenutzer*innen](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

14. Klicken Sie nach dem Auswählen der Kachel „Salesforce“ für die Anmeldung auf den neu konfigurierten Identitätsanbieter (IdP).

    ![Abbildung zum Auswählen eines neuen Identitätsanbieters](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

15. Wenn alles ordnungsgemäß konfiguriert wurde, wird Benutzer*innen die Salesforce-Startseite angezeigt. Falls Probleme auftreten, befolgen Sie den [Debugleitfaden](../manage-apps/debug-saml-sso-issues.md).

16. Kehren Sie nach dem Testen der SSO-Verbindung in Azure zur Unternehmensanwendung zurück, und weisen Sie der Salesforce-Anwendung die verbleibenden Benutzer*innen mit den richtigen Rollen zu.

>[!NOTE]
>Nachdem Sie die verbleibenden Benutzer*innen zur Azure AD-Anwendung hinzugefügt haben, wird empfohlen, dass Benutzer*innen die Verbindung testen und sicherstellen, dass keine Probleme im Zusammenhang mit dem Zugriff bestehen, bevor sie mit dem nächsten Schritt fortfahren.

17. Nachdem die Benutzer*innen bestätigt haben, dass keine Probleme bei der Anmeldung vorliegen, navigieren Sie zurück zur Salesforce-Verwaltungskonsole, und klicken Sie auf **Company Settings** > **My Domain** („Unternehmenseinstellungen“ > „Meine Domäne“).

18. Navigieren Sie zur **Authentication Configuration** (Authentifizierungskonfiguration), klicken Sie auf **Edit** (Bearbeiten), und deaktivieren Sie Okta als Authentifizierungsdienst.

    ![Abbildung zum Deaktivieren von Okta als Authentifizierungsdienst](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

Salesforce wurde nun erfolgreich für das einmalige Anmelden bei Azure AD konfiguriert. Schritte zum Bereinigen des Okta-Portals werden später in diesem Dokument beschrieben.

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>Migrieren einer OIDC-/OAuth 2.0-Anwendung zu Azure AD

Konfigurieren Sie zunächst die Anwendung in Ihrem Azure AD-Mandanten für den Anwendungszugriff. In diesem Beispiel konvertieren Sie eine benutzerdefinierte OIDC-App.

Wiederholen Sie zum Abschließen des Migrationsprozesses die Konfigurationsschritte für alle Anwendungen, die im Okta-Mandanten gefunden wurden.

1. Navigieren Sie zum [Azure AD-Portal](https://aad.portal.azure.com), und klicken Sie auf **Azure Active Directory** > **Unternehmensanwendungen**. Klicken Sie im Menü **Alle Anwendungen** auf **Neue Anwendung**.

2. Wählen Sie **Eigene Anwendung erstellen** aus. Geben Sie der OIDC-App im angezeigten Seitenmenü einen Namen. Klicken Sie zunächst auf das Optionsfeld für **Von Ihnen bearbeitete Anwendung für die Integration mit Azure AD registrieren** und dann auf **Erstellen**.

   ![Abbildung zur neuen OIDC-Anwendung](media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png)

3. Auf der nächsten Seite wird Ihnen eine Auswahl für den Mandanten Ihrer Anwendungsregistrierung angezeigt. Nähere Einzelheiten finden Sie in [diesem Artikel](../develop/single-and-multi-tenant-apps.md).

Wählen Sie in diesem Beispiel **Konten in einem beliebigen Organisationsverzeichnis** **(Beliebiges Azure AD-Verzeichnis – mehrere Mandanten)** aus, und klicken Sie dann auf **Registrieren**.

![Abbildung zum Azure AD-Verzeichnis mit mehreren Mandanten](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

4. Navigieren Sie nach dem Registrieren der Anwendung unter **Azure Active Directory** zur Seite **App-Registrierungen**, und öffnen Sie die neu erstellte Registrierung.

   Je nach [Anwendungsszenario](../develop/authentication-flows-app-scenarios.md) sind möglicherweise verschiedene Konfigurationsaktionen erforderlich. Da die meisten Szenarios ein App-Clientgeheimnis erfordern, werden diese Beispiele beschrieben.

5. Notieren Sie sich die Anwendungs-ID (Client) auf der Seite **Übersicht** zur späteren Verwendung in Ihrer Anwendung.

   ![Abbildung zur Anwendungs-ID (Client)](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

6. Nachdem Sie sich die Anwendungs-ID notiert haben, klicken Sie im Menü links auf **Zertifikate und Geheimnisse**. Klicken Sie auf **Neuer geheimer Clientschlüssel**, benennen Sie diesen, und legen Sie danach das Ablaufdatum fest.

   ![Abbildung zum neuen geheimen Clientschlüssel](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

7. Notieren Sie sich den Wert und die ID des Geheimnisses, bevor Sie diese Seite schließen.

>[!NOTE]
>Sie können sich diese Informationen später nicht mehr notieren. Stattdessen müssen Sie das Geheimnis neu erstellen, wenn die Daten verloren gehen.

8. Nachdem Sie sich die Informationen aus den vorherigen Schritten notiert haben, klicken Sie links auf **API-Berechtigungen**, und gewähren Sie der Anwendung Zugriff auf den OIDC-Stapel.

9. Klicken Sie zunächst auf **Berechtigung hinzufügen**, wählen Sie **Microsoft Graph** aus, und klicken Sie dann auf **Delegierte Berechtigungen**.

10. Fügen Sie im Abschnitt **OpenId-Berechtigungen** „email“, „OpenID“ und „profile“ hinzu. Klicken Sie dann auf **Berechtigungen hinzufügen**.

    ![Abbildung zum Hinzufügen von OpenId-Berechtigungen](media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png)

11. Nachdem Sie die Berechtigungen hinzugefügt haben, klicken Sie auf die Option **Grant admin consent for Tenant Domain Name** (Administratorzustimmung für „Mandantendomänenname“ erteilen), und warten Sie, bis der Status **Gewährt** angezeigt wird. Auf diese Weise können die Benutzerfreundlichkeit verbessert und Aufforderungen zur Benutzereinwilligung unterdrückt werden.

    ![Abbildung zum Erteilen der Administratorzustimmung](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

12. Wenn Ihre Anwendung über einen Umleitungs-URI oder eine Antwort-URL verfügt, navigieren Sie zur Registerkarte **Authentifizierung**, dann zu **Plattform hinzufügen** und anschließend zu **Web**. Geben Sie die entsprechende URL ein, wählen Sie unten „Zugriffstoken“ und „ID tokens“ (ID-Token) aus, und klicken Sie dann auf **Konfigurieren**.

    ![Abbildung zum Konfigurieren von Token](media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png)

    Ändern Sie im Menü „Authentifizierung“ unter **Erweiterte Einstellungen** bei Bedarf die Einstellung für die Option **Öffentliche Clientflows zulassen** in „Ja“.

    ![Abbildung zur Option „Öffentliche Clientflows zulassen“](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

13. Navigieren Sie zurück zu Ihrer konfigurierten OIDC-Anwendung, und importieren Sie vor dem Testen die Anwendungs-ID und den geheimen Clientschlüssel in Ihre Anwendung. Konfigurieren Sie Ihre Anwendung für die Verwendung der zuvor genannten Konfiguration (z. B. „Client-ID“, „Geheimnis“ und „Bereiche“).

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>Migrieren eines benutzerdefinierten Autorisierungsservers zu Azure AD

Okta-Autorisierungsserver ordnen Anwendungsregistrierungen, [die eine API verfügbar machen](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope), 1:1 zu.

Der Okta-Standardautorisierungsserver sollte Microsoft Graph-Bereichen bzw. -Berechtigungen zugeordnet werden.

![Abbildung zur Okta-Standardautorisierung](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>Nächste Schritte 

- [Tutorial: Migrieren des Okta-Verbunds zur verwalteten Azure Active Directory-Authentifizierung](migrate-okta-federation-to-azure-active-directory.md)

- [Migrieren der Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Migrieren von Okta-Anmelderichtlinien zu bedingtem Azure AD-Zugriff](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)