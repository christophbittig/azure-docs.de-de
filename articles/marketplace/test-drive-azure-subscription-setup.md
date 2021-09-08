---
title: Einrichten eines Azure Marketplace-Abonnements für gehostete Testversionen
description: In diesem Artikel erfahren Sie, wie Sie ein Azure Marketplace-Abonnement für Testversionen von Dynamics 365 Customer Engagement und Dynamics 365 for Operations einrichten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 03/16/2020
ms.openlocfilehash: 3fe1862f951b83c6514bda061650b912e9230e46
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350216"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Einrichten eines Azure Marketplace-Abonnements für gehostete Testversionen

In diesem Artikel erfahren Sie, wie Sie ein Azure Marketplace-Abonnement und eine Umgebung für Testversionen von **Dynamics 365 Customer Engagement und Power Apps** oder **Dynamics 365 for Operations** einrichten.

## <a name="set-up-for-dynamics-365-for-customer-engagement--power-apps"></a>Einrichtung für Dynamics 365 for Customer Engagement und Power Apps

1. Melden Sie sich mit einem Administratorkonto beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie sich in dem Mandanten befinden, der der Instanz Ihrer Dynamics 365-Testversion zugeordnet ist, indem Sie in der oberen rechten Ecke auf das Symbol für Ihr Konto zeigen. Wenn Sie sich nicht im richtigen Mandanten befinden, klicken Sie auf das Kontosymbol, um zum richtigen Mandanten zu wechseln.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Auswählen des richtigen Mandanten":::

3. Stellen Sie sicher, dass die Lizenz **Dynamics 365 for Customer Engagement-Plan** verfügbar ist.

    [![Überprüfen der Planlizenz](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Erstellen Sie eine Azure AD-App (Azure Active Directory) in Azure. AppSource verwendet diese App zum Bereitstellen und Aufheben der Bereitstellung des Benutzers der Testversion in Ihrem Mandanten.
    1. Wählen Sie im Filterbereich **Azure Active Directory** aus.
    2. Wählen Sie **App-Registrierungen** aus.

        [![Auswählen von App-Registrierungen](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Wählen Sie **Neue Registrierung** aus.
    4. Geben Sie einen geeigneten Anwendungsnamen an.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrieren einer Anwendung":::

    5. Wählen Sie unter „Unterstützte Kontotypen“ die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.
    6. Klicken Sie auf **Erstellen**, und warten Sie, bis die App erstellt wird.
    7. Nachdem die App erstellt wurde, notieren Sie sich die **Anwendungs-ID**, die in der Übersicht angezeigt wird. Diesen Wert werden Sie später beim Konfigurieren Ihrer Testversion benötigen.
    8. Klicken Sie unter **Anwendung verwalten** auf **API-Berechtigungen**.
    9. Klicken Sie auf **Berechtigung hinzufügen**, und wählen Sie dann  **Microsoft Graph-API** aus.
    10. Wählen Sie die Berechtigungskategorie **Anwendung** und anschließend die Berechtigungen **User.ReadWrite.All**, **Directory.Read.All** und **Directory.ReadWrite.All** aus.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Festlegen der Anwendungsberechtigungen":::

    11. Klicken Sie auf **Administratorzustimmung für Microsoft erteilen**, sobald die Berechtigung hinzugefügt wurde.
    12. Klicken Sie in der Warnungsmeldung auf **Ja**.

        [![Erfolgreich erteilte Anwendungsberechtigungen](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. So generieren Sie ein Geheimnis für die Azure AD-App:
        1. Klicken Sie unter **Anwendung verwalten** auf **Zertifikate und Geheimnisse**.
        2. Wählen Sie unter „Geheime Clientschlüssel“ die Option **Neuer geheimer Clientschlüssel** aus.
        3. Geben Sie eine Beschreibung ein (z. B. *Testversion*), und wählen Sie dann eine angemessene Dauer aus. Die Testversion funktioniert nicht mehr, sobald dieser Schlüssel abläuft. Dann müssen Sie einen neuen Schlüssel generieren und in AppSource angeben.
        4. Klicken Sie auf **Hinzufügen**, um den geheimen Schlüssel für die Azure-App zu generieren. Kopieren Sie diesen Wert, da er ausgeblendet wird, sobald Sie dieses Blatt verlassen. Diesen Wert werden Sie später beim Konfigurieren Ihrer Testversion benötigen.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Hinzufügen eines geheimen Clientschlüssels":::

5. Fügen Sie die Dienstprinzipalrolle zur Anwendung hinzu, um der Azure AD-App das Entfernen von Benutzern aus Ihrem Azure-Mandanten zu erlauben.
    1. Öffnen Sie eine PowerShell-Eingabeaufforderung auf Verwaltungsebene.
    2. Führen Sie den Befehl „Install-Module MSOnline“ aus, wenn MSOnline noch nicht installiert ist.
    3. Führen Sie „Connect-MsolService“ aus (dadurch wird ein Popupfenster angezeigt, über das Sie sich mit dem neu erstellten Organisationsmandanten anmelden).
    4. Führen Sie „$applicationId = **<IHRE_ANWENDUNGS-ID>** “ aus.
    5. Führen Sie „$SP = Get-msolserviceprincipal-appprincipalid $ApplicationId“ aus.
    6. Führen Sie „Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal.“ aus.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Anmelden mit Ihrem Konto":::

6. Erstellen Sie eine neue Sicherheitsgruppe, und fügen Sie sie der Canvas-App (Power Apps) hinzu. Dieser Schritt gilt nur für Canvas-App-Angebote (Power Apps).
    1. Erstellen Sie eine neue Sicherheitsgruppe.
        1. Wechseln Sie zu **Azure Active Directory**.
        1. Wählen Sie unter **Verwalten** die Option **Gruppen** aus.
        1. Wählen Sie **+ Neue Gruppe** aus.
        1. Wählen Sie als Gruppentyp die Option **Sicherheit** aus. 
        1. Geben Sie als **Gruppenname** die Zeichenfolge *TestDriveSecurityGroup* ein.
        1. Fügen Sie eine Beschreibung hinzu, z. B. **Sicherheitsgruppe für Testversion**.
        1. Behalten Sie bei den anderen Feldern die Standardeinstellungen bei, und wählen Sie **Erstellen** aus.

            :::image type="content" source="./media/test-drive/create-new-group.png" alt-text="Erstellen einer neuen Sicherheitsgruppe":::

    1. Fügen Sie die soeben erstellte Sicherheitsgruppe der Canvas-App (Power Apps) hinzu.
        1. Öffnen Sie die Portalseite **PowerApps**, und melden Sie sich an.
        1. Wählen Sie **Apps** und dann die Auslassungspunkte neben der App aus.
        1. Wählen Sie **Freigeben** aus.
        1. Suchen Sie nach der Sicherheitsgruppe **TestDriveSecurityGroup**, die Sie im vorherigen Schritt erstellt haben.
        1. Fügen Sie der Sicherheitsgruppe **Datenberechtigungen** hinzu.
        1. Deaktivieren Sie das Kontrollkästchen **E-Mail-Einladung senden**.
        1. Wählen Sie **Freigeben** aus.
    
            > [!NOTE]
            > Bei Verwendung einer anderen Back-End-Datenquelle als CE/Dataverse für die Canvas-App (Power Apps):
            > - Erlauben Sie der oben erstellten Sicherheitsgruppe den Zugriff auf Ihre Datenquelle, z. B. eine SharePoint-Datenquelle.
            > - Öffnen Sie SharePoint, und geben Sie die Datentabelle für die Sicherheitsgruppe frei.

7. Fügen Sie die soeben erstellte Azure-App als Anwendungsbenutzer zur Testversion Ihrer CRM-Instanz hinzu. Dieser Schritt gilt nur für Dynamics 365 Customer Engagement-Angebote.
    1. Fügen Sie einen neuen Benutzer in **Azure Active Directory** hinzu. Nur die Werte **Name** und **Benutzername** (die zum selben Mandanten gehören) sind erforderlich, um diesen Benutzer zu erstellen. Behalten Sie für die anderen Felder die Standardwerte bei. Kopieren Sie den Wert für den Benutzernamen.
    2. Melden Sie sich bei der **CRM-Instanz** an, und klicken Sie dann auf **Einstellungen** > **Sicherheit** > **Benutzer**.
    3. Wechseln Sie zur Ansicht **Anwendungsbenutzer**.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Festlegen von Kontoinformationen für einen Benutzer":::

    4. Fügen Sie einen neuen Benutzer hinzu (stellen Sie sicher, dass das Formular für APPLICATION USER (Anwendungsbenutzer) ist).
    5. Geben Sie den gleichen Benutzernamen in die Felder **Primäre E-Mail-Adresse** und **Benutzername** ein. Fügen Sie die **Azure-Anwendungs-ID** unter **Anwendungs-ID** ein.
    6. Geben Sie einen beliebigen **Vollständigen Namen** ein.
    7. Wählen Sie **Speichern** aus.
    8. Klicken Sie auf **Rollen verwalten**.
    9. Weisen Sie eine benutzerdefinierte oder vorgefertigte Sicherheitsrolle zu, die über Berechtigungen zum Lesen, Schreiben und Zuweisen von Rollen verfügt, z. B. *Systemadministrator*.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Auswählen der Rollenberechtigungen":::

    10. Aktivieren Sie außerdem die Berechtigung **Vorgänge im Namen anderer Benutzer ausführen**.
    11. Weisen Sie dem Anwendungsbenutzer die benutzerdefinierte Sicherheitsrolle zu, die Sie für Ihre Testversion erstellt haben.

## <a name="set-up-for-dynamics-365-for-operations"></a>Einrichten von Dynamics 365 for Operations

1. Melden Sie sich mit einem Administratorkonto beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie sich in dem Mandanten befinden, der der Instanz Ihrer Dynamics 365-Testversion zugeordnet ist, indem Sie in der oberen rechten Ecke auf das Symbol für Ihr Konto zeigen. Wenn Sie sich nicht im richtigen Mandanten befinden, klicken Sie auf das Kontosymbol, um zum richtigen Mandanten zu wechseln.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Auswählen des richtigen Mandanten":::

3. Erstellen Sie eine Azure AD-App in Azure. AppSource verwendet diese App zum Bereitstellen und Aufheben der Bereitstellung des Benutzers der Testversion in Ihrem Mandanten.
    1. Wählen Sie im Filterbereich **Azure Active Directory** aus.
    2. Wählen Sie **App-Registrierungen** aus.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Auswählen einer App-Registrierung":::

    3. Wählen Sie **Neue Registrierung** aus.
    4. Geben Sie einen geeigneten Anwendungsnamen an.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrieren einer Anwendung":::

    5. Wählen Sie unter „Unterstützte Kontotypen“ die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.
    6. Klicken Sie auf **Erstellen**, und warten Sie, bis die App erstellt wird.
    7. Nachdem die App erstellt wurde, notieren Sie sich die **Anwendungs-ID**, die in der Übersicht angezeigt wird. Diesen Wert werden Sie später beim Konfigurieren Ihrer Testversion benötigen.
    8. Klicken Sie unter **Anwendung verwalten** auf **API-Berechtigungen**.
    9. Klicken Sie auf **Berechtigung hinzufügen**, und wählen Sie dann  **Microsoft Graph-API** aus.
    10. Wählen Sie die Berechtigungskategorie **Anwendung** aus, und wählen Sie anschließend die Berechtigungen **Directory.Read.All** und **Directory.ReadWrite.All** aus.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Festlegen der Anwendungsberechtigungen":::

    11. Klicken Sie auf **Berechtigung hinzufügen**.
    12. Klicken Sie auf **Administratorzustimmung für Microsoft erteilen**, sobald die Berechtigung hinzugefügt wurde.
    13. Klicken Sie in der Warnungsmeldung auf **Ja**.

        [![Erfolgreich erteilte Anwendungsberechtigungen](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. So generieren Sie ein Geheimnis für die Azure AD-App:
        1. Klicken Sie unter **Anwendung verwalten** auf **Zertifikate und Geheimnisse**.
        2. Wählen Sie unter „Geheime Clientschlüssel“ die Option **Neuer geheimer Clientschlüssel** aus.
        3. Geben Sie eine Beschreibung ein (z. B. *Testversion*), und wählen Sie dann eine angemessene Dauer aus. Die Testversion funktioniert nicht mehr, sobald dieser Schlüssel abläuft. Dann müssen Sie einen neuen Schlüssel generieren und in AppSource angeben.
        4. Klicken Sie auf **Hinzufügen**, um den geheimen Schlüssel für die Azure-App zu generieren. Kopieren Sie diesen Wert, da er ausgeblendet wird, sobald Sie dieses Blatt verlassen. Diesen Wert werden Sie später beim Konfigurieren Ihrer Testversion benötigen.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Hinzufügen eines geheimen Clientschlüssels":::

4. Fügen Sie die Dienstprinzipalrolle zur Anwendung hinzu, um der Azure AD-App das Entfernen von Benutzern aus Ihrem Azure-Mandanten zu erlauben.
    1. Öffnen Sie eine PowerShell-Eingabeaufforderung auf Verwaltungsebene.
    2. Führen Sie den Befehl „Install-Module MSOnline“ aus, wenn MSOnline noch nicht installiert ist.
    3. Führen Sie „Connect-MsolService“ aus (dadurch wird ein Popupfenster angezeigt, über das Sie sich mit dem neu erstellten Organisationsmandanten anmelden).
    4. Führen Sie „$applicationId = **<IHRE_ANWENDUNGS-ID>** “ aus.
    5. Führen Sie „$SP = Get-msolserviceprincipal-appprincipalid $ApplicationId“ aus.
    6. Führen Sie „Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal.“ aus.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Anmelden mit Ihrem Konto":::

5. Fügen Sie nun die obige App zu **Dynamics 365 for Operations** hinzu, um der App das Verwalten von Benutzern zu ermöglichen.
    1. Suchen Sie nach Ihrer **Dynamics 365 for Operations**-Instanz.
    2. Klicken Sie in der oberen linken Ecke auf das Menü mit drei Strichen.
    3. Klicken Sie auf **Systemverwaltung**.
    4. Klicken Sie auf **Azure Active Directory-Anwendungen**.
    5. Wählen Sie **+ Neu** aus.
    6. Geben Sie die **Client-ID der Azure AD-App** ein, die die OBO-Aktionen (On Behalf Of) durchführen soll.

    > [!NOTE]
    > Die Benutzer-ID entspricht dem Benutzer, unter dessen Identität die Aktionen durchgeführt werden (dabei handelt es sich in der Regel um den Systemadministrator der Instanz oder einen Benutzer, der über die Berechtigung zum Hinzufügen anderer Benutzer verfügt).

    [![Die Benutzer-ID entspricht dem Benutzer, unter dessen Identität die Aktionen durchgeführt werden (dabei handelt es sich in der Regel um den Systemadministrator der Instanz oder einen Benutzer, der über die Berechtigung zum Hinzufügen anderer Benutzer verfügt).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
