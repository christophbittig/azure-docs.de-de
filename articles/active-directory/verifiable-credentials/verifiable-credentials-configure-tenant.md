---
title: 'Tutorial: Konfigurieren Ihres Mandanten für Azure AD-Nachweise (Vorschau)'
description: In diesem Tutorial erfahren Sie, wie Sie Ihren Mandanten für die Unterstützung konfigurieren.
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
manager: karenh444
ms.author: barclayn
ms.topic: tutorial
ms.date: 10/08/2021
ms.openlocfilehash: d480696acf10586f41aa9c5c70a8dcf6e2c7651f
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070012"
---
# <a name="configure-your-tenant-for-azure-ad-verifiable-credentials-preview"></a>Konfigurieren Ihres Mandanten für Azure AD-Nachweise (Vorschau)

Der Azure AD-Nachweisdienst (Azure Active Directory) ermöglicht Ihnen das Ausstellen und Überprüfen von Nachweisen. Befolgen Sie die Anweisungen in diesem Tutorial, um Nachweise auszustellen oder zu überprüfen (oder beides).

In diesem Tutorial werden die Schritte beschrieben, die zum Konfigurieren Ihres Azure AD-Mandanten erforderlich sind, damit er Azure AD-Nachweise verwenden kann.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> - Einrichten eines Dienstprinzipals
> - Erstellen einer Azure Key Vault-Instanz
> - Registrieren einer Anwendung in Azure AD
> - Einrichten des Nachweisdiensts (Vorschauversion)

Im folgenden Diagramm sind die Architektur für Azure AD-Nachweise und die Komponente dargestellt, die Sie konfigurieren:

![Diagramm: Veranschaulichung der Architektur für Azure AD-Nachweise](media/verifiable-credentials-configure-tenant/verifiable-credentials-architecture.png)

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Registrieren Sie sich für ein Abonnement für [Azure Active Directory Premium-Editionen](../../active-directory/fundamentals/active-directory-get-started-premium.md) in Ihrem Mandanten.
- Die Berechtigung [Globaler Administrator](../../active-directory/roles/permissions-reference.md#global-administrator) für das zu konfigurierende Verzeichnis
- [PowerShell](/powershell/scripting/install/installing-powershell) 7.0.6 LTS-x64, PowerShell 7.1.3-x64 oder eine höhere Version muss installiert sein.

## <a name="step-1-set-up-a-service-principal"></a>Schritt 1: Einrichten eines Dienstprinzipals

Erstellen Sie einen Dienstprinzipal für die Anforderungsdienst-API. Die Dienst-API ist der Microsoft-Dienst, den Sie zum Ausstellen oder Überprüfen von Azure AD-Nachweisen verwenden.

So erstellen Sie den Dienstprinzipal

1. Führen Sie die folgenden PowerShell-Befehle aus: Mit diesen Befehlen wird das [AzureAD-Modul](/powershell/azure/install-az-ps#installation) installiert (sofern es noch nicht installiert ist) und importiert. Wenn bei der Installation des Moduls Probleme auftreten, lesen Sie den Artikel [Installieren des Azure Az PowerShell-Moduls](/powershell/azure/install-az-ps#installation).

    ```powershell
    if ((Get-Module -ListAvailable -Name "AzureAD") -eq $null) {  Install-Module "AzureAD" -Scope CurrentUser }  Import-Module AzureAD
    ```

1. Führen Sie den folgenden PowerShell-Befehl aus, um eine Verbindung mit Ihrem Azure AD-Mandanten herzustellen. Ersetzen Sie \<*your-tenant-ID*> durch die [ID Ihres Azure AD-Mandanten](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

    ```powershell
    Connect-AzureAD -TenantId <your-tenant-ID>
    ```

1. Führen Sie in der gleichen PowerShell-Sitzung den folgenden Befehl aus. Der AppId-Wert „bbb94529-53a3-4be5-a069-7eaf2712b826“ bezieht sich auf den Microsoft-Nachweisdienst.

    ```powershell
    New-AzureADServicePrincipal -AppId "bbb94529-53a3-4be5-a069-7eaf2712b826" -DisplayName "Verifiable Credential Request Service" 
    ```

## <a name="step-2-create-a-key-vault"></a>Schritt 2: Erstellen eines Schlüsseltresors

[Azure Key Vault](/azure/key-vault/general/basic-concepts) ist ein Clouddienst, der das sichere Speichern von Geheimnissen und Schlüsseln und den sicheren Zugriff darauf ermöglicht. Ihr Nachweisdienst speichert öffentliche und private Schlüssel in Azure Key Vault. Diese Schlüssel werden zum Signieren und Überprüfen von Nachweisen verwendet.

Wenn Sie nicht über eine Instanz von Azure Key Vault verfügen, führen Sie die folgenden Schritte aus, um mithilfe des Azure-Portal einen Schlüsseltresor zu erstellen.

>[!NOTE]
>Standardmäßig ist das Konto, mit dem der Schlüsseltresor erstellt wird, das einzige mit Zugriff. Der Dienst für überprüfbare Anmeldeinformationen benötigt Zugriff auf den Schlüsseltresor. Der Schlüsseltresor muss mit einer Zugriffsrichtlinie konfiguriert werden, die es dem während der Konfiguration verwendeten Konto ermöglicht, **Schlüssel zu erstellen** und **Schlüssel zu löschen**, wenn Sie sich abmelden. Darüber hinaus muss die Berechtigung zum **Signieren** erteilt werden, um die Domänenbindung für den Nachweisdienst zu erstellen. Wenn Sie beim Testen dasselbe Konto verwenden, ändern Sie die Standardrichtlinie, um dem Konto die Berechtigung **Signieren** zusätzlich zu den Standardberechtigungen zu erteilen, die Tresorerstellern erteilt wurden.

### <a name="step-21-set-access-policies-for-the-key-vault"></a>Schritt 2.1 Festlegen von Zugriffsrichtlinien für Key Vault

Nach dem Onboarding des Azure-Nachweisdiensts generiert der Dienst mehrere Schlüssel, mit denen Nachrichtensicherheit gewährleistet wird. Diese Schlüssel werden im Schlüsseltresor gespeichert. Unabhängig davon, ob Sie einen Nachweis ausstellen oder überprüfen: Es wird weiterhin ein Schlüsselsatz zum Signieren, Aktualisieren und Wiederherstellen von Nachweisen verwendet.

Eine Key Vault-[Zugriffsrichtlinie](/azure/key-vault/general/assign-access-policy) legt fest, ob ein bestimmter Sicherheitsprinzipal Vorgänge für Key Vault-Geheimnisse und -Schlüssel ausführen kann. Legen Sie Zugriffsrichtlinien in Ihrer Azure Key Vault-Instanz sowohl für das Administratorkonto des Azure AD-Nachweisdiensts als auch für den von Ihnen erstellten Prinzipal der Anforderungsdienst-API fest.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu der für dieses Tutorial verwendeten **Key Vault**-Instanz.

1. Wählen Sie unter **Einstellungen** die Option **Zugriffsrichtlinien** aus.

1. Wählen Sie auf der Seite **Zugriffsrichtlinie hinzufügen** unter **BENUTZER** das Konto aus, das Sie für dieses Tutorial verwenden möchten.

1. Überprüfen Sie unter **Schlüsselberechtigungen**, ob die folgenden Berechtigungen ausgewählt wurden: **Erstellen, Löschen** und **Signieren**. Standardmäßig sind Erstellen und Löschen bereits aktiviert. Signieren sollte die einzige Schlüsselberechtigung sein, die Sie aktualisieren müssen.
    
    Der folgende Screenshot zeigt, wie die Richtlinie für den Administratorzugriff konfiguriert wird:

    ![Screenshot: Konfigurieren der Richtlinie für den Administratorzugriff](media/verifiable-credentials-configure-tenant/set-key-vault-admin-access-policy.png)

1. Wählen Sie zum Speichern der Änderungen **Speichern** aus.

1. Wählen Sie **+ Zugriffsrichtlinie hinzufügen** aus, um dem Dienstprinzipal des Nachweisanforderungsdiensts eine Berechtigung hinzuzufügen.

1. Gehen Sie auf der Seite **Zugriffsrichtlinie hinzufügen** wie folgt vor:

    1. Wählen Sie unter **Schlüsselberechtigungen** die Optionen **Abrufen** und **Signieren** aus.

    1. Wählen Sie unter **Geheimnisberechtigungen** die Option **Abrufen** aus.

    1. Wählen Sie unter **Prinzipal auswählen** die Option **Verifiable Credential Request Service** (Nachweisanforderungsdienst) aus.

    1. Wählen Sie **Hinzufügen**.  
        
       Der folgende Screenshot zeigt, wie Sie eine Zugriffsrichtlinie für den Nachweisanforderungsdienst hinzufügen. 
    
       ![Screenshot: Hinzufügen einer Zugriffsrichtlinie für den Nachweisanforderungsdienst](media/verifiable-credentials-configure-tenant/set-key-vault-service-principal-access-policy.png)

1. Wählen Sie **Speichern** aus, um die von Ihnen erstellte neue Richtlinie zu speichern.

## <a name="step-3-register-an-application-in-azure-ad"></a>Schritt 3: Registrieren einer Anwendung in Azure AD

Der Azure AD-Nachweisanforderungsdienst muss Zugriffstoken für die Ausstellung und Überprüfung abrufen können. Registrieren Sie zum Abrufen von Zugriffstoken eine Webanwendung, und erteilen Sie API-Berechtigungen für den API-Nachweisanforderungsdienst, den Sie im vorherigen Schritt eingerichtet haben.

1. Melden Sie sich mit Ihrem Administratorkonto beim [Azure-Portal](https://portal.azure.com/) an.

1. Wenn Sie Zugriff auf mehrere Mandanten besitzen, wählen Sie das Symbol **Verzeichnis + Abonnement** :::image type="icon" source="media/verifiable-credentials-configure-tenant/portal-directory-subscription-filter.png" border="false"::: aus. Suchen Sie dann nach Ihrer Instanz von **Azure Active Directory**, und wählen Sie sie aus.

1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann **Neue Registrierung** aus.  

   ![Screenshot: Auswählen einer neuen Anwendungsregistrierung](media/verifiable-credentials-configure-tenant/register-azure-ad-app.png)

1. Geben Sie einen **Anzeigenamen** für Ihre Anwendung ein. Beispiel: **verifiable-credentials-app**

1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis (nur Standardverzeichnis – einzelner Mandant)** aus.

1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
  
   Der folgende Screenshot zeigt, wie die Anwendung registriert wird:

   ![Screenshot: Registrieren der Nachweis-App](media/verifiable-credentials-configure-tenant/register-azure-ad-app-properties.png)

### <a name="31-grant-permissions-to-get-access-tokens"></a>3.1 Erteilen von Berechtigungen zum Abrufen von Zugriffstoken

In diesem Schritt gewähren Sie dem in [Schritt 1](#step-1-set-up-a-service-principal) erstellten Prinzipal des Nachweisanforderungsdiensts Berechtigungen.

Führen Sie die folgenden Schritte aus, um die erforderlichen Berechtigungen hinzuzufügen:

1. Bleiben Sie auf der Seite mit den Details für die Anwendung **verifiable-credentials-app**. Klicken Sie auf **API-Berechtigungen** und anschließend auf **Berechtigung hinzufügen**.
    
    ![Screenshot: Hinzufügen von Berechtigungen zur Nachweis-App](media/verifiable-credentials-configure-tenant/add-app-api-permissions.png)

1. Wählen Sie **Von meiner Organisation verwendete APIs** aus.

1. Suchen Sie nach dem Dienstprinzipal, den Sie zuvor für den **Nachweisanforderungsdienst** erstellt haben, und wählen Sie ihn aus.
    
    ![Screenshot: Auswählen des Dienstprinzipals](media/verifiable-credentials-configure-tenant/add-app-api-permissions-select-service-principal.png)

1. Wählen Sie **Anwendungsberechtigung** aus, und erweitern Sie **VerifiableCredential.Create.All**.

    ![Screenshot: Auswählen der erforderlichen Berechtigungen](media/verifiable-credentials-configure-tenant/add-app-api-permissions-verifiable-credentials.png)

1. Wählen Sie **Berechtigungen hinzufügen** aus.

1. Wählen Sie **Administratoreinwilligung erteilen für \<your tenant name\>** aus.

## <a name="step-4-set-up-verifiable-credentials-preview-service"></a>Schritt 4. Einrichten des Nachweisdiensts (Vorschauversion)

Der Azure AD-Nachweisdienst schützt Ihre Organisation mit einer nahtlosen Lösung für dezentralisierte Identitäten. Für Aussteller stellt Azure AD einen Dienst zur Verfügung, den sie anpassen und verwenden können, um ihre eigenen Nachweise auszustellen. Für Prüfer bieten die Nachweisdienste einen kostenlosen REST-API-Dienst, der das Anfordern und Akzeptieren von Nachweisen in Ihren Apps und Diensten vereinfacht.

Führen Sie zum Einrichten Ihrer Azure AD-Nachweise die folgenden Schritte aus:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach **Nachweise**. Wählen Sie dann **Nachweise (Vorschau)** aus.

1. Wählen Sie im Menü auf der linken Seite **Erste Schritte** aus.

1. Richten Sie Ihre Organisation ein, indem Sie die folgenden Informationen bereitstellen:

    1. **Organisationsname**: Geben Sie einen Namen ein, um in den überprüfbaren Anmeldeinformationen auf Ihr Unternehmen zu verweisen. Dieser Wert ist nicht kundenbasiert.

    1. **Domäne:** Geben Sie eine Domäne ein, die einem Dienstendpunkt in Ihrem DID-Dokument hinzugefügt wird. Die Domäne bindet Ihre DID an etwas Konkretes, das der Benutzer möglicherweise über Ihr Unternehmen kennt. Microsoft Authenticator und andere digitale Geldbörsen überprüfen anhand dieser Informationen, ob Ihre DID mit Ihrer Domäne verknüpft ist. Wenn das Wallet die DID überprüfen kann, wird ein verifiziertes Symbol angezeigt. Wenn das Wallet die DID nicht überprüfen kann, wird der Benutzer darüber informiert, dass die Anmeldeinformationen von einer Organisation ausgestellt wurden, die sie nicht überprüfen konnte.   
            
        >[!IMPORTANT]
        > Die Domäne kann keine Umleitung sein. Andernfalls können DID und Domäne nicht verknüpft werden. Achten Sie darauf, HTTPS für die Domäne zu verwenden, etwa <https://contoso.com>.

    1. **Schlüsseltresor:** Geben Sie den Namen des Schlüsseltresors ein, den Sie zuvor erstellt haben.

1. Wählen Sie **Anmeldeinformation speichern und erstellen** aus.  
    
    Der folgende Screenshot zeigt, wie Sie die Nachweise einrichten:  
    
    ![Screenshot: Einrichten der Nachweise](media/verifiable-credentials-configure-tenant/verifiable-credentials-getting-started.png)

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie, wie Sie Azure AD-Nachweise über eine Webanwendung ausstellen.](verifiable-credentials-configure-issuer.md)
- [Informieren Sie sich über das Überprüfen von Azure AD-Nachweisen.](verifiable-credentials-configure-verifier.md)
