---
title: Verbinden mit und Verwalten eines Power BI-Mandanten
description: In diesem Leitfaden wird beschrieben, wie Sie in Azure Purview eine Verbindung mit einem Power BI-Mandanten herstellen und mit den Features von Purview Ihre Power BI-Mandantenquelle überprüfen und verwalten.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 39b6dd297ad0fbb739272db41900e68c799e790d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853783"
---
# <a name="connect-to-and-manage-a-power-bi-tenant-in-azure-purview"></a>Verbinden mit und Verwalten eines Power BI-Mandanten in Azure Purview

In diesem Artikel wird beschrieben, wie Sie in einen Power BI-Mandanten Azure Purview registrieren, authentifizieren und mit ihm interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register)| [Ja](#scan)| Nein | Nein | Nein | Nein| [Ja](how-to-lineage-powerbi.md)|

> [!Note]
> Wenn sich die Purview-Instanz und der Power BI-Mandant im selben Azure-Mandanten befinden, können Sie nur die Authentifizierung mit verwalteten Identitäten (MSI) verwenden, um eine Überprüfung eines Power BI-Mandanten einzurichten.

### <a name="known-limitations"></a>Bekannte Einschränkungen

-   Für mandantenübergreifende Szenarios ist derzeit keine Benutzerumgebung zum Registrieren und Überprüfen von mandantenübergreifenden Power BI-Mandanten verfügbar.
-   Durch Bearbeiten des übergreifenden Power BI-Mandanten, der mithilfe von Purview Studio bei PowerShell registriert wurde, wird die Datenquellenregistrierung durch inkonsistentes Scanverhalten verfälscht.
-   Informieren Sie sich über die [Einschränkungen bei der Überprüfung von Power BI-Metadaten](/power-bi/admin/service-admin-metadata-scanning).


## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie in Azure Purview einen Power BI-Mandanten sowohl im [selben Mandanten](#authentication-for-a-same-tenant-scenario) als auch [ mandantenübergreifend](#steps-to-register-cross-tenant) registrieren.

### <a name="authentication-for-a-same-tenant-scenario"></a>Authentifizierung im selben Mandanten

Für das Einrichten der Authentifizierung im selben Mandanten oder der mandantenübergreifenden Authentifizierung erstellen Sie eine Sicherheitsgruppe, der Sie die von Purview verwaltete Identität hinzufügen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Azure Active Directory**.
1. Erstellen Sie eine neue Sicherheitsgruppe in Ihrem Azure Active Directory-System, indem Sie gemäß [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) vorgehen.

    > [!Tip]
    > Sie können diesen Schritt überspringen, wenn Sie bereits über eine Sicherheitsgruppe verfügen, die Sie verwenden möchten.

1. Wählen Sie als **Gruppentyp** die Option **Sicherheit** aus.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Screenshot vom Sicherheitsgruppentyp":::

1. Fügen Sie Ihre von Purview verwaltete Identität dieser Sicherheitsgruppe hinzu. Wählen Sie **Mitglieder** und dann **+ Mitglieder hinzufügen** aus.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Screenshot: Hinzufügen der verwalteten Instanz des Katalogs zur Gruppe":::

1. Suchen Sie nach Ihrer von Purview verwalteten Identität, und wählen Sie sie aus.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Screenshot: Hinzufügen eines Katalogs durch Suchen nach seinem Namen":::

    Es sollte eine Erfolgsmeldung angezeigt werden, die Ihnen mitteilt, dass der Katalog hinzugefügt wurde.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Screenshot: erfolgreiches Hinzufügen der Katalog-MSI":::

#### <a name="associate-the-security-group-with-the-tenant"></a>Verknüpfen der Sicherheitsgruppe mit dem Mandanten

1. Melden Sie sich beim [Power BI-Verwaltungsportal](https://app.powerbi.com/admin-portal/tenantSettings) an.
1. Wählen Sie die Seite **Mandanteneinstellungen** aus.

    > [!Important]
    > Sie müssen Power BI-Administrator sein, um die Seite mit den Mandanteneinstellungen anzeigen zu können.

1. Wählen Sie **Administrator-API-Einstellungen** > **Dienstprinzipalen die Verwendung schreibgeschützter Power BI-Administrator-APIs gestatten (Vorschau)** aus.
1. Wählen Sie **Sicherheitsgruppen angeben** aus.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Abbildung: Dienstprinzipale erhalten schreibgeschützte Power BI-Administrator-API-Berechtigungen":::

1. Wählen Sie **Admin-API-Einstellungen** > **Admin-API-Antworten mit Metadatendetails erweitern** aus. Aktivieren Sie dann den Schalter, damit Purview Data Map automatisch die Metadatendetails von Power BI-Datensätzen als Teil seiner Scans erkennt.

    > [!IMPORTANT]
    > Warten Sie nach dem Aktualisieren der Admin-API-Einstellungen in Ihrem Power BI-Mandanten etwa 15 Minuten, bevor Sie eine Überprüfung registrieren und die Verbindung testen.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-sub-artifacts.png" alt-text="Abbildung der Konfiguration des Power BI-Verwaltungsportals zum Aktivieren der Überprüfung untergeordneter Artefakte.":::

    > [!Caution]
    > Wenn Sie der von Ihnen erstellten Sicherheitsgruppe (die Ihre verwaltete Purview-Identität als Mitglied hat) gestatten, schreibgeschützte Power BI-Administrator-APIs zu verwenden, gestatten Sie ihr auch, auf die Metadaten (z. B. Dashboard- und Berichtsnamen, Besitzer, Beschreibungen usw.) für sämtliche Ihrer Power BI-Artefakte in diesem Mandanten zugreifen zu können. Sobald die Metadaten in Azure Purview eingelesen wurden, wird anhand der Pruview-Berechtigungen, nicht anhand der Power BI-Berechtigungen, festgelegt, wer diese Metadaten anzeigen kann.
  
    > [!Note]
    > Sie können die Sicherheitsgruppe aus Ihren Entwicklereinstellungen entfernen, die zuvor extrahierten Metadaten werden jedoch nicht aus dem Purview-Konto entfernt. Bei Bedarf können Sie sie separat löschen.

### <a name="steps-to-register-in-the-same-tenant"></a>Schritte zum Registrieren im selben Mandanten

Nachdem Sie nun der von Purview verwalteten Identität die Berechtigung erteilt haben, eine Verbindung mit der Administrator-API Ihres Power BI-Mandanten herzustellen, können Sie in Azure Purview Studio die Überprüfung einrichten.

1. Wählen Sie im linken Navigationsbereich **Data Map** aus.

1. Klicken Sie anschließend auf **Registrieren**.

    Wählen Sie **Power BI** als Ihre Datenquelle aus.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Abbildung: Liste der in verfügbaren Datenquellen":::

1. Geben Sie Ihrer Power BI-Instanz einen Anzeigenamen.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Abbildung: Anzeigenamen der Power BI-Datenquelle":::

    Der Name muss zwischen 3 und 63 Zeichen lang sein und darf nur Buchstaben, Ziffern, Unterstriche und Bindestriche enthalten.  Leerzeichen sind nicht zulässig.

    Standardmäßig findet das System den Power BI-Mandanten, der in demselben Azure-Abonnement vorhanden ist.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Abbildung: registrierte Power BI-Datenquelle":::

    > [!Note]
    > Für Power BI ist die Registrierung und Überprüfung von Datenquellen nur für eine Instanz zulässig.

### <a name="steps-to-register-cross-tenant"></a>Schritte zur mandantenübergreifenden Registrierung

In einem mandantenübergreifenden Szenario können Sie PowerShell verwenden, um Ihre Power BI-Mandanten zu registrieren und zu überprüfen. Sie können Ressourcen des Remotemandanten mit Azure Purview Studio über die Benutzeroberfläche durchsuchen. 

Verwenden Sie diese Anleitung, wenn sich der Azure AD-Mandant, in dem sich der Power BI-Mandant befindet, von dem Azure AD-Mandanten unterscheidet, in dem Ihr Azure Purview-Konto bereitgestellt wird. Führen Sie die folgenden Schritte aus, um einen oder mehrere Power BI-Mandanten in Azure Purview in einem mandantenübergreifenden Szenario zu registrieren und zu überprüfen:

1. Laden Sie die [PowerShell-Module für die verwaltete Überprüfung](https://github.com/Azure/Purview-Samples/blob/master/Cross-Tenant-Scan-PowerBI/ManagedScanningPowerShell.zip) herunter, und extrahieren Sie den Inhalt an einem Speicherort Ihrer Wahl.

1. Geben Sie auf Ihrem Computer **PowerShell** in das Suchfeld auf der Windows-Taskleiste ein. Klicken Sie in der Suchliste auf **Windows PowerShell** und halten Sie die Maustaste gedrückt (oder klicken Sie mit der rechten Maustaste darauf), und wählen Sie **Als Administrator ausführen** aus.

1. Installieren und importieren Sie das Modul auf Ihrem Computer, wenn es noch nicht installiert wurde.

   ```powershell
    Install-Module -name az
    Import-Module -name az
    Login-AzAccount
    ```

1. Melden Sie sich mit den Azure AD-Administratoranmeldeinformationen bei Ihrer Azure-Umgebung an, in der sich Ihr Power BI-Mandant befindet.

   ```powershell
    Login-AzAccount
    ```

1. Geben Sie im PowerShell-Fenster den folgenden Befehl ein, und ersetzen Sie dabei `<path-to-managed-scanning-powershell-modules>` durch den Ordnerpfad der extrahierten Module wie `C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell`.

   ```powershell
   dir -Path <path-to-managed-scanning-powershell-modules> -Recurse | Unblock-File
   ```

1. Geben Sie den folgenden Befehl ein, um die PowerShell-Module zu installieren.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell\Microsoft.DataCatalog.Management.Commands.dll'
   ```

1. Verwenden Sie dieselbe PowerShell-Sitzung, um die folgenden Parameter festzulegen. Aktualisieren Sie `purview_tenant_id` mit der ID des Azure AD-Mandaten, in der Azure Purview bereitgestellt ist, und `powerbi_tenant_id` mit Ihrem Azure AD-Mandanten, in dem sich der Power BI-Mandant befindet. `purview_account_name` ist Ihr bestehendes Azure Purview-Konto.

   ```powershell
   $azuretenantId = '<purview_tenant_id>'
   $powerBITenantIdToScan = '<powerbi_tenant_id>'
   $purviewaccount = '<purview_account_name>'
   ```

1. Erstellen Sie einen mandantenübergreifenden Dienstprinzipal.

   1. Erstellen Sie eine App-Registrierung in Ihrem Azure Active Directory-Mandanten, in dem sich Power BI befindet. Stellen Sie sicher, dass Sie das Feld `password` mit einem sicheren Kennwort aktualisieren und `app_display_name` mit einem nicht vorhandenen Anwendungsnamen in Ihrem Azure AD-Mandanten aktualisieren, in dem der Power BI-Mandant gehostet wird.

      ```powershell   
      $SecureStringPassword = ConvertTo-SecureString -String <'password'> -AsPlainText -Force
      $AppName = '<app_display_name>'
      New-AzADApplication -DisplayName $AppName -Password $SecureStringPassword
      ```

   1. Wählen Sie aus dem Azure Active Directory-Dashboard die neu erstellte Anwendung und dann **App-Registrierung** aus. Weisen Sie der Anwendung die folgenden delegierten Berechtigungen zu, und erteilen Sie dem Mandanten die Administratoreinwilligung:

      - Power BI Service     Tenant.Read.All
      - Microsoft Graph      openid

      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-delegated-permissions.png" alt-text="Screenshot: delegierte Berechtigungen für Power BI Service und Microsoft Graph.":::

   1. Wählen Sie aus dem Azure Active Directory-Dashboard die neu erstellte Anwendung und dann **Authentifizierung** aus. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis (beliebigen Azure AD-Verzeichnis – mehrinstanzenfähig)** aus. 

      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-multitenant.png" alt-text="Screenshot: Unterstützung mehrerer Mandanten durch Kontotyp.":::

   1. Achten Sie darauf, dass Sie unter **Implizite Genehmigung und Hybridflows** die Option **ID-Token (werden für implizite und Hybridflows verwendet)** ausgewählt ist.
    
      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-id-token-hybrid-flows.png" alt-text="Screenshot: Hybridflows für ID-Token.":::

   1. Erstellen Sie eine mandantenspezifische Anmelde-URL für Ihren Dienstprinzipal, indem Sie die folgende URL in Ihrem Webbrowser ausführen:

      ```
      https://login.microsoftonline.com/<purview_tenant_id>/oauth2/v2.0/authorize?client_id=<client_id_to_delegate_the_pbi_admin>&scope=openid&response_type=id_token&response_mode=fragment&state=1234&nonce=67890
      ```
    
      Stellen Sie sicher, dass Sie die Parameter durch die richtigen Informationen ersetzen:
      
      - `<purview_tenant_id>` ist die Mandanten-ID (GUID) von Azure Active Directory, in der das Azure Purview-Konto bereitgestellt wird.
      - `<client_id_to_delegate_the_pbi_admin>` ist die Anwendungs-ID, die Ihrem Dienstprinzipal entspricht.
   
   1. Melden Sie sich mit einem beliebigen Nicht-Administratorkonto an. Dies ist erforderlich, um Ihren Dienstprinzipal im Fremdmandanten bereitstellen zu können.

   1. Wenn Sie dazu aufgefordert werden, akzeptieren Sie die erforderliche Berechtigung für _Ihr grundlegendes Profil anzeigen_ und _Zugriff auf Daten beibehalten, für die Sie der App Zugriff erteilt haben_.

1. Aktualisieren Sie `client_id_to_delegate_the_pbi_admin` mit der Anwendungs-ID (Client) der neu erstellten Anwendung, und führen Sie den folgenden Befehl in Ihrer PowerShell-Sitzung aus:

   ```powershell
   $ServicePrincipalId = '<client_id_to_delegate_the_pbi_admin>'
   ```

1. Erstellen Sie ein Benutzerkonto im Azure Active Directory-Mandanten, in dem sich der Power BI-Mandant befindet, und weisen Sie die Azure AD-Rolle **Power BI-Administrator** zu. Aktualisieren Sie `pbi_admin_username` und `pbi_admin_password` mit den entsprechenden Informationen und führen Sie die folgenden Zeilen im PowerShell-Terminal aus:

    ```powershell
    $UserName = '<pbi_admin_username>'
    $Password = '<pbi_admin_password>'
    ```

    > [!Note]
    > Wenn Sie im Portal ein Benutzerkonto in Azure Active Directory erstellen, ist die Standardoption für öffentliche Clientflows **Nein**. Sie müssen sie auf **Ja** umschalten:
    > <br>
    > :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-public-client-flows.png" alt-text="Screenshot: Öffentliche Clientflows.":::
    
1. Weisen Sie in Azure Purview Studio dem Dienstprinzipal und dem Power BI-Benutzer in der Stammsammlung die Rolle als _Datenquellenadministrator_ zu. 

1. Um den mandantenübergreifenden Power BI-Mandanten als neue Datenquelle im Azure Purview-Konto zu registrieren, aktualisieren Sie `service_principal_key` und führen die folgenden Cmdlets in der PowerShell-Sitzung aus:

    ```powershell
    Set-AzDataCatalogSessionSettings -DataCatalogSession -TenantId $azuretenantId -ServicePrincipalAuthentication -ServicePrincipalApplicationId $ServicePrincipalId -ServicePrincipalKey '<service_principal_key>' -Environment Production -DataCatalogAccountName $purviewaccount

    Set-AzDataCatalogDataSource -Name 'pbidatasource' -AccountType PowerBI -Tenant $powerBITenantIdToScan -Verbose
    ```

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um einen Power BI-Mandanten zu überprüfen, um Ressourcen automatisch zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

In diesem Leitfaden werden sowohl Überprüfungen im [selben Mandanten](#create-and-run-scan-for-same-tenant-power-bi) als auch [mandantenübergreifend](#create-and-run-scan-for-cross-tenant-power-bi) behandelt.

### <a name="create-and-run-scan-for-same-tenant-power-bi"></a>Erstellen und Ausführen einer Überprüfung im selben Power BI-Mandanten

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Navigieren Sie in Purview Studio im linken Menü zu **Data Map**.

1. Navigieren Sie zu **Quellen**.

1. Wählen Sie die registrierte Power BI-Quelle aus.

1. Wählen Sie **+ Neue Überprüfung** aus.

1. Geben Sie Ihrer Überprüfung einen Namen. Wählen Sie dann die Option zum Einschließen oder Ausschließen der persönlichen Arbeitsbereiche aus. Beachten Sie, dass die einzige unterstützte Authentifizierungsmethode **Verwaltete Identität** ist.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Abbildung: Einrichtung der Power BI-Überprüfung":::

    > [!Note]
    > Wenn Sie die Konfiguration einer Überprüfung so umstellen, dass ein persönlicher Arbeitsbereich ein- bzw. ausgeschlossen wird, wird eine vollständige Überprüfung der Power BI-Quelle ausgelöst.

1. Wählen Sie **Verbindung testen** aus, bevor Sie mit den nächsten Schritten fortfahren. Wenn **Verbindung testen** fehlschlägt, wählen Sie **Bericht anzeigen** aus, um den genauen Status zu sehen und das Problem zu beheben.
    1. Zugriff: Dieser Fehlerstatus bedeutet, dass die Benutzerauthentifizierung fehlgeschlagen ist. Überprüfungen mit verwalteter Identität werden immer erfolgreich ausgeführt, da keine Benutzerauthentifizierung erforderlich ist.
    1. Ressourcen (+ Herkunft): Der Fehlerstatus bedeutet, dass bei der Purview-Power BI-Autorisierung ein Fehler aufgetreten ist. Stellen Sie sicher, dass die verwaltete Purview-Identität der Sicherheitsgruppe hinzugefügt wird, die im Power BI-Verwaltungsportal zugeordnet ist.
    1. Detaillierte Metadaten (erweitert): Dieser Fehlerstatus bedeutet, dass das Power BI-Verwaltungsportal für die folgende Einstellung deaktiviert ist: **Enhance admin APIs responses with detailed metadata** (Admin-API-Antworten mit Metadatendetails)

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-test-connection-status-report.png" alt-text="Screenshot der Berichtsseite mit dem Testverbindungsstatus":::

1. Richten Sie einen Auslöser für die Überprüfung ein. Ihre Optionen sind **Einmal**, **Alle 7 Tage** und **Alle 30 Tage**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Screenshot des Purview-Scanplans":::

1. Wählen Sie für **Neue Überprüfung überprüfen** die Option **Speichern und ausführen** aus, um Ihre Überprüfung zu starten.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Screenshot: Speichern und Ausführen von Power BI-Quelle":::

### <a name="create-and-run-scan-for-cross-tenant-power-bi"></a>Erstellen und Ausführen einer Überprüfung in mehreren Power BI-Mandanten

Führen Sie zum Erstellen und Ausführen einer neuen Überprüfung in Azure Purview die folgenden Cmdlets in der PowerShell-Sitzung aus:

   ```powershell
   Set-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan' -AuthorizationType PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password -IncludePersonalWorkspaces $true -Verbose

   Start-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan'
   ```

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, befolgen Sie die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Datenerkenntnisse in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)
