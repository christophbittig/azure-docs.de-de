---
title: Aktivieren der Azure Active Directory-Authentifizierung für Azure-SSIS Integration Runtime
description: In diesem Artikel wird beschrieben, wie Sie die Azure Active Directory-Authentifizierung mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Azure Data Factory aktivieren, um die Azure-SSIS Integration Runtime zu erstellen.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 10/22/2021
ms.openlocfilehash: c800193e027821df00167930d06d9c13ad8cb67d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843597"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Aktivieren der Azure Active Directory-Authentifizierung für Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel erfahren Sie, wie Sie Azure Active Directory-Authentifizierung (Azure AD) mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz (Azure Data Factory) aktivieren und anstelle von herkömmlichen Authentifizierungsmethoden (wie SQL-Authentifizierung) verwenden, um Folgendes zu tun:

- Erstellen einer Azure-SSIS Integration Runtime (IR), die wiederum die SSIS-Katalogdatenbank (SSISDB) in Azure SQL-Datenbank-Server/in einer verwalteten Instanz in Ihrem Namen bereitstellt.

- Herstellen einer Verbindung mit verschiedenen Azure-Ressourcen beim Ausführen von SSIS-Paketen in der Azure-SSIS IR.

Weitere Informationen zur verwalteten Identität für Ihre ADF-Instanz finden Sie unter [Verwaltete Identität für Azure Data Factory](./data-factory-service-identity.md).

> [!NOTE]
> - In diesem Szenario wird die Azure AD-Authentifizierung mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz nur bei der Bereitstellung und den nachfolgenden Startvorgängen Ihrer Azure-SSIS IR verwendet, die wiederum zum Bereitstellen der SSISDB und/oder dem Herstellen einer Verbindung mit dieser verwendet wird. Für Ausführungen von SSIS-Paketen stellt Ihre Azure-SSIS IR trotzdem eine Verbindung mit der SSISDB mithilfe der SQL-Authentifizierung her, um Pakete abzurufen. Dabei werden vollständig verwaltete Konten (*AzureIntegrationServiceDbo* und *AzureIntegrationServiceWorker*) genutzt, die während der Bereitstellung der SSISDB erstellt wurden.
>
> - Wenn Sie bereits eine Azure-SSIS IR mithilfe der SQL-Authentifizierung erstellt haben, können Sie die IR gegenwärtig nicht mit PowerShell für die Verwendung der Azure AD-Authentifizierung neu konfigurieren. Dies ist jedoch im Azure-Portal oder in der ADF-App möglich. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-authentication-on-azure-sql-database"></a>Aktivieren der Azure AD-Authentifizierung in Azure SQL-Datenbank

Azure SQL-Datenbank unterstützt das Erstellen einer Datenbank mit einem Azure AD-Benutzer. Zunächst müssen Sie eine Azure AD-Gruppe mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz als Mitglied erstellen. Dann müssen Sie einen Azure AD-Benutzer als Active Directory-Administrator für Ihren Azure SQL-Datenbank-Server festlegen und anschließend in SQL Server Management Studio (SSMS) eine Verbindung mit dem Server über diesen Benutzer herstellen. Zum Schluss müssen Sie einen enthaltenen Benutzer erstellen, der die Azure AD-Gruppe darstellt, damit die systemseitig/benutzerseitig zugewiesene verwaltete Identität für Ihre ADF-Instanz von der Azure-SSIS IR zum Erstellen von SSISDB in Ihrem Namen verwendet werden kann.

### <a name="create-an-azure-ad-group-with-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-member"></a>Erstellen einer Azure AD-Gruppe mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz als Mitglied

Sie können eine vorhandene Azure AD-Gruppe verwenden oder mithilfe von Azure AD PowerShell eine neue erstellen.

1. Installieren Sie das [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)-Modul.

2. Melden Sie sich mit `Connect-AzureAD` an, führen Sie das folgende Cmdlet aus, um eine Gruppe zu erstellen, und speichern Sie sie in einer Variablen:

   ```powershell
   $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                             -MailEnabled $false `
                             -SecurityEnabled $true `
                             -MailNickName "NotSet"
   ```

   Das Ergebnis sieht etwa wie im folgenden Beispiel aus, das auch den Variablenwert zeigt:

   ```powershell
   $Group

   ObjectId DisplayName Description
   -------- ----------- -----------
   6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
   ```

3. Fügen Sie die angegebene systemseitig/benutzerseitig zugewiesene verwaltete Identität für Ihre ADF-Instanz zur Gruppe hinzu. Sie können die Schritte im Artikel [Verwaltete Identität für Azure Data Factory](./data-factory-service-identity.md) ausführen, um die Objekt-ID der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz abzurufen (z. B. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, aber verwenden Sie für diesen Zweck nicht die Anwendungs-ID).

   ```powershell
   Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
   ```

   Sie können die Gruppenmitgliedschaft auch anschließend überprüfen.

   ```powershell
   Get-AzureAdGroupMember -ObjectId $Group.ObjectId
   ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database"></a>Konfigurieren der Azure AD-Authentifizierung für Azure SQL-Datenbank

Sie können die [Azure AD-Authentifizierung für Azure SQL-Datenbank](../azure-sql/database/authentication-aad-configure.md) mithilfe der folgenden Schritte konfigurieren und verwalten:

1. Wählen Sie im Azure-Portal im Navigationsbereich links **Alle Dienste** -> **SQL-Server** aus.

2. Wählen Sie den Azure SQL-Datenbank-Server aus, der mit Azure AD-Authentifizierung konfiguriert werden soll.

3. Klicken Sie auf dem Blatt im Abschnitt **Einstellungen** auf **Active Directory-Administrator**.

4. Wählen Sie in der Befehlsleiste **Administrator festlegen** aus.

5. Wählen Sie ein Azure AD-Benutzerkonto als Administrator des Servers und dann **Auswählen** aus.

6. Wählen Sie in der Befehlsleiste **Speichern** aus.

### <a name="create-a-contained-user-in-azure-sql-database-representing-the-azure-ad-group"></a>Erstellen eines enthaltenen Benutzers in Azure SQL-Datenbank, der die Azure AD-Gruppe darstellt

Für den nächsten Schritt benötigen Sie [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

1. Starten Sie SSMS.

2. Geben Sie im Dialogfeld **Mit Server verbinden** im Feld **Servername** den Namen Ihres Servers ein.

3. Wählen Sie im Feld **Authentifizierung** die Option **Active Directory: universell mit MFA-Unterstützung** aus. (Sie können auch die beiden anderen Active Directory-Authentifizierungstypen verwenden. Informationen dazu finden Sie unter [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit Azure SQL](../azure-sql/database/authentication-aad-configure.md).)

4. Geben Sie im Feld **Benutzername** den Namen des Azure AD-Kontos ein, das Sie als Serveradministrator festlegen möchten, z. B. testuser@xxxonline.com.

5. Wählen Sie **Verbinden** aus, und schließen Sie den Anmeldevorgang ab.

6. Erweitern Sie im **Objekt-Explorer** unter **Datenbanken** ->  den Ordner **Systemdatenbanken**.

7. Klicken Sie mit der rechten Maustaste auf die **master**-Datenbank, und wählen Sie **Neue Abfrage** aus.

8. Geben Sie im Abfragefenster den folgenden T-SQL-Befehl ein, und wählen Sie auf der Symbolleiste **Ausführen** aus.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Der Befehl sollte erfolgreich abgeschlossen und ein enthaltener Benutzer zur Darstellung der Gruppe erstellt werden.

9. Löschen Sie den Inhalt des Abfragefensters, geben Sie den folgenden T-SQL-Befehl ein, und klicken Sie auf der Symbolleiste auf **Ausführen**.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Der Befehl sollte erfolgreich abgeschlossen werden und dem enthaltenen Benutzer die Möglichkeit gewähren, eine Datenbank (SSISDB) zu erstellen.

10. Wenn Ihre SSISDB mithilfe von SQL-Authentifizierung erstellt wurde und Sie zur Verwendung der Azure AD-Authentifizierung für Ihre Azure-SSIS IR wechseln möchten, um darauf zugreifen zu können, müssen Sie zunächst sicherstellen, dass die obigen Schritte zum Erteilen der Berechtigung für die **Master**-Datenbank erfolgreich abgeschlossen wurden. Klicken Sie dann mit der rechten Maustaste auf die **SSISDB**-Datenbank, und wählen Sie **Neue Abfrage** aus.

    1. Geben Sie im Abfragefenster den folgenden T-SQL-Befehl ein, und wählen Sie auf der Symbolleiste **Ausführen** aus.

       ```sql
       CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
       ```

       Der Befehl sollte erfolgreich abgeschlossen und ein enthaltener Benutzer zur Darstellung der Gruppe erstellt werden.

    2. Löschen Sie den Inhalt des Abfragefensters, geben Sie den folgenden T-SQL-Befehl ein, und klicken Sie auf der Symbolleiste auf **Ausführen**.

       ```sql
       ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
       ```

       Der Befehl sollte erfolgreich abgeschlossen werden und dem in der Datenbank enthaltenen Benutzer die Möglichkeit gewähren, auf die SSISDB zuzugreifen.

## <a name="enable-azure-ad-authentication-on-azure-sql-managed-instance"></a>Aktivieren der Azure AD-Authentifizierung für Azure SQL Managed Instance

Azure SQL Managed Instance unterstützt das direkte Erstellen einer Datenbank mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz. Sie müssen die ystemseitig/benutzerseitig zugewiesene verwaltete Identität für Ihre ADF-Instanz weder mit einer Azure AD-Gruppe verknüpfen noch einen enthaltenen Benutzer zur Darstellung dieser Gruppe in Azure SQL Managed Instance erstellen.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Konfigurieren der Azure AD-Authentifizierung für Azure SQL Managed Instance

Führen Sie die unter [Bereitstellen eines Azure AD-Administrators (SQL Managed Instance)](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) beschriebenen Schritte aus.

### <a name="add-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-user-in-azure-sql-managed-instance"></a>Hinzufügen der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz als Benutzer in Azure SQL Managed Instance

Für den nächsten Schritt benötigen Sie [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

1. Starten Sie SSMS.

2. Stellen Sie über ein SQL Server-Konto, das Berechtigungen als **Systemadministrator** hat, eine Verbindung mit Azure SQL Managed Instance her. Dabei handelt es sich um eine temporäre Einschränkung, die aufgehoben wird, sobald die Unterstützung für Azure AD-Serverprinzipale (Anmeldungen) in Azure SQL Managed Instance allgemein verfügbar wird. Wenn Sie versuchen, die Anmeldung mit einem Azure AD-Administratorkonto zu erstellen, wird der folgende Fehler angezeigt: *Meldung 15247, Ebene 16, Status 1, Zeile 1: Der Benutzer besitzt keine Berechtigung zum Ausführen dieser Aktion.*

3. Erweitern Sie im **Objekt-Explorer** unter **Datenbanken** ->  den Ordner **Systemdatenbanken**.

4. Klicken Sie mit der rechten Maustaste auf die **master**-Datenbank, und wählen Sie **Neue Abfrage** aus.

5. Führen Sie im Abfragefenster das folgende T-SQL-Skript aus, um die angegebene systemseitig/benutzerseitig zugewiesene verwaltete Identität für Ihre ADF-Instanz als Benutzer hinzuzufügen.

   ```sql
   CREATE LOGIN [{your managed identity name}] FROM EXTERNAL PROVIDER
   ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your managed identity name}]
   ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your managed identity name}]
   ```

   Wenn Sie die vom System verwaltete Identität für Ihre ADF verwenden, sollte der *Name Ihrer verwalteten Identität* Ihr ADF-Name sein. Wenn Sie eine benutzerseitig zugewiesene verwaltete Identität für Ihre ADF verwenden, sollte der *Name Ihrer verwalteten Identität* der Name der angegebenen benutzerseitig zugewiesenen verwalteten Identität sein.
    
   Der Befehl sollte erfolgreich abgeschlossen werden und der systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz die Möglichkeit gewähren, eine Datenbank (SSISDB) zu erstellen.

6. Wenn Ihre SSISDB mithilfe von SQL-Authentifizierung erstellt wurde und Sie zur Verwendung der Azure AD-Authentifizierung für Ihre Azure-SSIS IR wechseln möchten, um darauf zugreifen zu können, müssen Sie zunächst sicherstellen, dass die obigen Schritte zum Erteilen der Berechtigung für die **Master**-Datenbank erfolgreich abgeschlossen wurden. Klicken Sie dann mit der rechten Maustaste auf die **SSISDB**-Datenbank, und wählen Sie **Neue Abfrage** aus.

   1. Geben Sie im Abfragefenster den folgenden T-SQL-Befehl ein, und wählen Sie auf der Symbolleiste **Ausführen** aus.

      ```sql
      CREATE USER [{your managed identity name}] FOR LOGIN [{your managed identity name}] WITH DEFAULT_SCHEMA = dbo
      ALTER ROLE db_owner ADD MEMBER [{your managed identity name}]
      ```

      Der Befehl sollte erfolgreich abgeschlossen werden und der systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz die Möglichkeit gewähren, auf SSISDB zuzugreifen.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Bereitstellen von Azure-SSIS IR im Azure-Portal oder der ADF-App

Aktivieren Sie bei der Bereitstellung Ihrer Azure-SSIS IR im Azure-Portal/in der ADF-App auf der Seite **Bereitstellungseinstellungen** das Kontrollkästchen **Vom Azure SQL-Datenbank-Server/von der verwalteten Instanz gehosteten SSIS-Katalog (SSISDB) zum Speichern Ihrer Projekte/Pakete/Umgebungen/Ausführungsprotokolle erstellen**. Aktivieren Sie dann entweder das Kontrollkästchen **Use AAD authentication with the system managed identity for Data Factory** (AAD-Authentifizierung mit der systemseitig zugewiesenen verwalteten Identität verwenden) oder das Kontrollkästchen **Use AAD authentication with a user-assigned managed identity for Data Factory** (AAD-Authentifizierung mit einer benutzerseitig zugewiesenen verwalteten Identität verwenden), um die Azure AD-Authentifizierungsmethode für die Azure-SSIS IR zu wählen, um auf Ihren Datenbankserver zuzugreifen, der SSISDB hostet.

Weitere Informationen finden Sie unter [Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft-Dokumentation](./create-azure-ssis-integration-runtime.md).

## <a name="provision-azure-ssis-ir-with-powershell"></a>Bereitstellen von Azure-SSIS IR mit PowerShell

Um Ihre Azure SSIS IR mit PowerShell bereitzustellen, führen Sie folgende Schritte aus:

1. Installieren Sie das [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)-Modul.

2. Legen Sie im Skript den `CatalogAdminCredential`-Parameter nicht fest. Beispiel:

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                         -DataFactoryName $DataFactoryName `
                                         -Name $AzureSSISName `
                                         -Description $AzureSSISDescription `
                                         -Type Managed `
                                         -Location $AzureSSISLocation `
                                         -NodeSize $AzureSSISNodeSize `
                                         -NodeCount $AzureSSISNodeNumber `
                                         -Edition $AzureSSISEdition `
                                         -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                         -CatalogServerEndpoint $SSISDBServerEndpoint `
                                         -CatalogPricingTier $SSISDBPricingTier

   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName
   ```

## <a name="run-ssis-packages-using-azure-ad-authentication-with-the-specified-systemuser-assigned-managed-identity-for-your-adf"></a>Ausführen von SSIS-Paketen unter Verwendung der Azure AD-Authentifizierung mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz

Bei der Ausführung von SSIS-Paketen in Azure-SSIS IR können Sie die Azure AD-Authentifizierung mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz verwenden, um eine Verbindung mit verschiedenen Azure-Ressourcen herzustellen. Derzeit wird die Azure AD-Authentifizierung mit der angegebenen systemseitig/benutzerseitig zugewiesenen verwalteten Identität für Ihre ADF-Instanz in den folgenden Verbindungs-Managern unterstützt:

- [OLEDB-Verbindungs-Manager](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET-Verbindungs-Manager](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage-Verbindungs-Manager](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
