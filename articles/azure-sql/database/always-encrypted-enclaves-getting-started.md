---
title: 'Tutorial: Erste Schritte mit Always Encrypted mit Secure Enclaves'
description: In diesem Tutorial erfahren Sie, wie Sie eine grundlegende Umgebung für Always Encrypted mit Secure Enclaves in Azure SQL-Datenbank erstellen, Daten direkt verschlüsseln und umfassende vertrauliche Abfragen für verschlüsselte Spalten mithilfe von SQL Server Management Studio (SSMS) ausgeben.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: dd8fc18b8f24a6164830dda6044c1b03151eb180
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727344"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Tutorial: Erste Schritte mit Always Encrypted mit Secure Enclaves in Azure SQL-Datenbank

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial werden Ihnen die erste Schritte mit [Always Encrypted mit Secure Enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) in Azure SQL-Datenbank vermittelt. Es wird Folgendes gezeigt:

> [!div class="checklist"]
> - Erstellen einer Umgebung zum Testen und Auswerten von Always Encrypted mit Secure Enclaves
> - Direktes Verschlüsseln von Daten und Ausgeben umfangreicher vertraulicher Abfragen für verschlüsselte Spalten mithilfe von SQL Server Management Studio (SSMS)

## <a name="prerequisites"></a>Voraussetzungen

- Ein aktives Azure-Abonnement. Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/). Sie müssen Mitglied der Rolle „Mitwirkender“ oder „Besitzer“ sein, damit das Abonnement Ressourcen erstellen und eine Nachweisrichtlinie konfigurieren kann.

- SQL Server Management Studio (SSMS) Version 18.9.1 oder höher. Informationen zum Herunterladen von SSMS finden Sie unter [Herunterladen von SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>PowerShell-Anforderungen

> [!NOTE]
> Die in diesem Abschnitt aufgeführten Voraussetzungen gelten nur, wenn Sie PowerShell für einige der Schritte in diesem Tutorial verwenden möchten. Wenn Sie stattdessen das Azure-Portal verwenden möchten, können Sie diesen Abschnitt überspringen.

Stellen Sie sicher, dass die folgenden PowerShell-Module auf Ihrem Computer installiert sind.

1. Az Version 5.6 oder höher Ausführliche Informationen zum Installieren des Az-Moduls von PowerShell finden Sie unter [Installieren des Azure Az PowerShell-Moduls](/powershell/azure/install-az-ps). Führen Sie den folgenden Befehl in einer PowerShell-Sitzung aus, um die Version des Az-Moduls zu ermitteln, das auf Ihrem Computer installiert ist.

    ```powershell
    Get-InstalledModule -Name Az
    ```

1. Az.Attestation Version 0.1.8 oder höher Ausführliche Informationen zum Installieren des Az.Attestation-Moduls von PowerShell finden Sie unter [Installieren des PowerShell-Moduls „Az.Attestation“](../../attestation/quickstart-powershell.md#install-azattestation-powershell-module). Führen Sie den folgenden Befehl in einer PowerShell-Sitzung aus, um die Version des Az.Attestation-Moduls zu ermitteln, das auf Ihrem Computer installiert ist.

    ```powershell
    Get-InstalledModule -Name Az.Attestation
    ```

Führen Sie den Befehl `Update-Module` aus, wenn die Versionen nicht die Mindestanforderungen erfüllen.

Im PowerShell-Katalog wurden die TLS-Versionen (Transport Layer Security) 1.0 und 1.1 als veraltet gekennzeichnet. Empfohlen wird TLS 1.2 oder eine höhere Version. Wenn Sie eine niedrigere TLS-Version als 1.2 verwenden, werden unter Umständen die folgenden Fehler angezeigt:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Führen Sie den folgenden Befehl vor den Befehlen vom Typ „Install-Module“ aus, um weiterhin mit dem PowerShell-Katalog zu interagieren.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>Schritt 1: Erstellen und Konfigurieren eines Servers und einer Datenbank der DC-Serie

In diesem Schritt erstellen Sie unter Verwendung der Hardwaregeneration der DC-Serie einen neuen logischen Azure SQL-Datenbank-Server und eine neue Datenbank. Beides wird für Always Encrypted mit Secure Enclaves benötigt. Weitere Informationen finden Sie unter [DC-Serie](service-tiers-sql-database-vcore.md#dc-series).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie zur Seite [SQL-Bereitstellungsoption auswählen](https://portal.azure.com/#create/Microsoft.AzureSQL).
1. Wenn Sie noch nicht beim Azure-Portal angemeldet sind, melden Sie sich nach einer entsprechenden Aufforderung an.
1. Behalten Sie unter **SQL-Datenbanken** für **Einzeldatenbank** den festgelegten Wert **Ressourcentyp** bei, und wählen Sie **Erstellen** aus.

   :::image type="content" source="./media/single-database-create-quickstart/select-deployment.png" alt-text="Hinzufügen zu Azure SQL":::

1. Wählen Sie auf der Registerkarte **Grundeinstellungen** des Formulars **SQL-Datenbank erstellen** unter **Projektdetails** das gewünschte **Abonnement** für Azure aus.
1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, geben Sie einen Namen für Ihre Ressourcengruppe ein, und wählen Sie **OK** aus.
1. Geben Sie als **Datenbankname** *ContosoHR* ein.
1. Wählen Sie unter **Server** die Option **Neu erstellen** aus, und füllen Sie das Formular **Neuer Server** mit den folgenden Werten aus:
   - **Servername**: Geben Sie *mysqlserver* ein, und fügen Sie einige weitere Zeichen hinzu, um einen eindeutigen Wert zu erhalten. Wir können keinen exakten Servernamen zur Verwendung angeben, weil Servernamen für alle Server in Azure global eindeutig sein müssen (und nicht nur innerhalb eines Abonnements eindeutig sind). Geben Sie also beispielsweise „mysqlserver135“ ein. Dann werden Sie vom Portal informiert, ob dieser Server verfügbar ist.
   - **Serveradministratoranmeldung:** Geben Sie einen Administratoranmeldenamen ein, z. B. *azureuser*.
   - **Kennwort**: Geben Sie ein geeignetes Kennwort ein, und wiederholen Sie die Eingabe im Feld **Kennwort bestätigen**.
   - **Standort**: Wählen Sie in der Dropdownliste einen Standort aus.
      > [!IMPORTANT]
      > Sie müssen einen Standort (eine Azure-Region) auswählen, der sowohl die Hardwaregeneration der DC-Serie als auch Microsoft Azure Attestation unterstützt. Die Liste der Regionen, die die DC-Serie unterstützen, finden Sie unter [Verfügbarkeit der DC-Serie](service-tiers-sql-database-vcore.md#dc-series). [Hier](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) finden Sie die regionale Verfügbarkeit von Microsoft Azure Attestation.

   Klicken Sie auf **OK**.
1. Behalten Sie für **Möchten Sie einen Pool für elastische SQL-Datenbanken verwenden?** den Wert **Nein** bei.
1. Wählen Sie unter **Compute und Speicher** die Option **Datenbank konfigurieren** aus, und klicken Sie auf **Konfiguration ändern**.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-database.png" alt-text="Konfigurieren der Datenbank" lightbox="./media/always-encrypted-enclaves/portal-configure-database.png":::

1. Wählen Sie die Hardwarekonfiguration der **DC-Serie** und dann **OK** aus.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-dc-series-database.png" alt-text="Konfigurieren der Datenbank der DC-Serie":::

1. Wählen Sie **Übernehmen**. 
1. Überprüfen Sie auf der Registerkarte **Grundlagen**, ob **Compute und Speicher** auf **Universell**, **DC, 2 virtuelle Kerne, 32 GB Speicher** festgelegt ist.
1. Klicken Sie auf **Weiter: Netzwerk** aus (im unteren Bereich der Seite).

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-dc-series-database-basics.png" alt-text="Konfigurieren der Datenbank der DC-Serie – Grundlagen":::

1. Wählen Sie auf der Registerkarte **Netzwerk** als **Konnektivitätsmethode** die Option **Öffentlicher Endpunkt** aus.
1. Legen Sie bei **Firewallregeln** die Option **Aktuelle Client-IP-Adresse hinzufügen** auf **Ja** fest. Behalten Sie für **Azure-Diensten und -Ressourcen den Zugriff auf diese Servergruppe gestatten** den Wert **Nein** bei.
1. Wählen Sie am unteren Rand der Seite die Option **Bewerten + erstellen** aus.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-database-networking.png" alt-text="Neue SQL-Datenbank – Netzwerk":::

1. Wählen Sie nach Überprüfung auf der Seite **Überprüfen + erstellen** die Option **Erstellen** aus.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Öffnen Sie eine PowerShell-Konsole, und importieren Sie die erforderliche Az-Version.

   ```PowerShell
   Import-Module "Az" -MinimumVersion "5.6.0"
   ```

1. Melden Sie sich bei Azure an. [Wechseln Sie ggf. zu dem Abonnement](/powershell/azure/manage-subscriptions-azureps), das Sie für dieses Tutorial verwenden.

   ```PowerShell
   Connect-AzAccount
   $subscriptionId = "<your subscription ID>"
   Set-AzContext -Subscription $subscriptionId
   ```

1. Erstellen einer neuen Ressourcengruppe

   > [!IMPORTANT]
   > Ihre Ressourcengruppe muss in einer Region (Standort) erstellt werden, die sowohl die Hardwaregeneration der DC-Serie als auch Microsoft Azure Attestation unterstützt. Die Liste der Regionen, die die DC-Serie unterstützen, finden Sie unter [Verfügbarkeit der DC-Serie](service-tiers-sql-database-vcore.md#dc-series). [Hier](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) finden Sie die regionale Verfügbarkeit von Microsoft Azure Attestation.

   ```powershell
   $resourceGroupName = "<your new resource group name>"
   $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
   New-AzResourceGroup -Name $resourceGroupName -Location $location
   ```

1. Erstellen Sie einen logischen Azure SQL-Server. Geben Sie bei entsprechender Aufforderung den Serveradministratornamen und ein Kennwort ein. Merken Sie sich den Administratornamen und das Kennwort. Sie benötigen diese Informationen später, um eine Verbindung mit dem Server herzustellen. 

   ```powershell
   $serverName = "<your server name>" 
   New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
   ```

1. Erstellen Sie eine Serverfirewallregel, die den Zugriff aus dem angegebenen IP-Adressbereich zulässt.
  
   ```powershell
   $startIp = "<start of IP range>"
   $endIp = "<end of IP range>"
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   ```

1. Erstellen Sie eine Datenbank der DC-Serie.

   ```powershell
   $databaseName = "ContosoHR"
   $edition = "GeneralPurpose"
   $vCore = 2
   $generation = "DC"
   New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
   ```

---

## <a name="step-2-configure-an-attestation-provider"></a>Schritt 2: Konfigurieren eines Nachweisanbieters

In diesem Schritt erstellen und konfigurieren Sie einen Nachweisanbieter in Microsoft Azure Attestation. Dies ist erforderlich, um einen Nachweis für die Secure Enclave-Instanz zu erbringen, die von Ihrer Datenbank verwendet wird.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie zur Seite [Nachweisanbieter erstellen](https://ms.portal.azure.com/#create/Microsoft.Attestation).
1. Geben Sie auf der Seite **Nachweisanbieter erstellen** Folgendes ein:

   - **Abonnement**: Wählen Sie dasselbe Abonnement aus, in dem Sie den logischen Azure SQL-Server erstellt haben.
   - **Ressourcengruppe**: Wählen Sie dieselbe Ressourcengruppe aus, in der Sie den logischen Azure SQL-Server erstellt haben.
   - **Name**: Geben Sie *myattestprovider* ein, und fügen Sie einige weitere Zeichen hinzu, um einen eindeutigen Wert zu erhalten. Wir können keinen genauen Nachweisanbieternamen angeben, der verwendet werden soll, da Namen global eindeutig sein müssen. Geben Sie also z. B. „myattestprovider12345“ ein. Dann werden Sie vom Portal informiert, ob dieser Server verfügbar ist.
   - **Speicherort**: Wählen Sie den Speicherort aus, an dem Sie den logischen Azure SQL-Server erstellt haben.
   - **Datei für Richtlinien-Signaturgeberzertifikate**: Lassen Sie dieses Feld leer, da Sie eine nicht signierte Richtlinie konfigurieren.

1. Wählen Sie nach dem Eingeben der erforderlichen Daten die Option **Überprüfen und erstellen** aus.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-create-attestation-provider-basics.png" alt-text="Nachweisanbieter erstellen":::

1. Klicken Sie auf **Erstellen**.
1. Nachdem der Nachweisanbieter erstellt wurde, klicken Sie auf **Zu Ressource wechseln**.
1. Kopieren Sie auf der Registerkarte **Übersicht** für den Nachweisanbieter den Wert der Eigenschaft **Nachweis-URI** in die Zwischenablage und speichern Sie ihn in einer Datei. Dies ist die Nachweis-URL, die Sie in späteren Schritten benötigen.  

   :::image type="content" source="./media/always-encrypted-enclaves/portal-attest-uri.png" alt-text="Nachweis-URL":::

1. Wählen Sie links im Fenster im Ressourcenmenü oder im unteren Bereich die Option **Richtlinie** aus.
1. Legen Sie **Nachweistyp** auf **SGX-IntelSDK** fest.
1. Wählen Sie im oberen Menü die Option **Konfigurieren** aus.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-attestation-policy.png" alt-text="Konfigurieren der Nachweisrichtlinie":::

1. Legen Sie das **Richtlinienformat** auf **Text** fest. Lassen Sie **Richtlinienoptionen** auf **Richtlinie eingeben** festgelegt.
1. Ersetzen Sie im Feld **Richtlinientext** die Standardrichtlinie durch die folgende Richtlinie. Informationen zur folgenden Richtlinie finden Sie unter [Erstellen und Konfigurieren eines Nachweisanbieters](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

    ```output
    version= 1.0;
    authorizationrules 
    {
           [ type=="x-ms-sgx-is-debuggable", value==false ]
            && [ type=="x-ms-sgx-product-id", value==4639 ]
            && [ type=="x-ms-sgx-svn", value>= 0 ]
            && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
        => permit();
    };
    ```

1. Klicken Sie auf **Speichern**.

   :::image type="content" source="./media/always-encrypted-enclaves/portal-edit-attestation-policy.png" alt-text="Nachweisrichtlinie bearbeiten":::

1. Klicken Sie im oberen Menü auf die Option **Aktualisieren**, um die konfigurierte Richtlinie anzuzeigen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Kopieren Sie die nachfolgende Nachweisrichtlinie, und speichern Sie die Richtlinie in einer Textdatei (.txt). Informationen zur folgenden Richtlinie finden Sie unter [Erstellen und Konfigurieren eines Nachweisanbieters](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

    ```output
    version= 1.0;
    authorizationrules 
    {
           [ type=="x-ms-sgx-is-debuggable", value==false ]
            && [ type=="x-ms-sgx-product-id", value==4639 ]
            && [ type=="x-ms-sgx-svn", value>= 0 ]
            && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
        => permit();
    };
    ```

1. Importieren Sie die erforderliche Version von `Az.Attestation`.  

   ```powershell
   Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
   ```
  
1. Erstellen Sie einen Nachweisanbieter.

   ```powershell
   $attestationProviderName = "<your attestation provider name>" 
   New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
   ```

1. Konfigurieren Sie die Nachweisrichtlinie.
  
   ```powershell
   $policyFile = "<the pathname of the file from step 1 in this section>"
   $teeType = "SgxEnclave"
   $policyFormat = "Text"
   $policy=Get-Content -path $policyFile -Raw
   Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
   ```

1. Rufen Sie die Nachweis-URL ab (den Nachweis-URI Ihres Nachweisanbieters).

   ```powershell
   $attestationUrl = (Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName).AttestUri
   Write-Host "Your attestation URL is: $attestationUrl"
   ```

   Die Nachweis-URL sollte wie folgt aussehen: `https://myattestprovider12345.eus.attest.azure.net`

---


## <a name="step-3-populate-your-database"></a>Schritt 3: Auffüllen Ihrer Datenbank

In diesem Schritt erstellen Sie eine Tabelle und füllen sie mit einigen Daten auf, die Sie später verschlüsseln und abfragen.

1. Öffnen Sie SSMS, und stellen Sie eine Verbindung mit der Datenbank **ContosoHR** auf dem von Ihnen erstellten logischen Azure SQL-Server her, **ohne** Always Encrypted in der Datenbankverbindung zu aktivieren.
    1. Geben Sie im Dialogfeld **Verbindung mit dem Server herstellen** den vollqualifizierten Namen Ihres Servers (z. B. *myserver135.database.windows.net*) sowie den Benutzernamen und das Kennwort des Administrators ein, den bzw. das Sie beim Erstellen des Servers festgelegt haben.
    2. Klicken Sie auf **Optionen >>** , und wählen Sie die Registerkarte **Verbindungseigenschaften** aus. Wählen Sie unbedingt die Datenbank **ContosoHR** (nicht die Standardmasterdatenbank) aus. 
    3. Wählen Sie die Registerkarte **Always Encrypted** aus.
    4. Stellen Sie sicher, dass das Kontrollkästchen **Always Encrypted aktivieren (Spaltenverschlüsselung)** **nicht** ausgewählt ist.

        :::image type="content" source="./media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png" alt-text="Herstellen einer Verbindung ohne Always Encrypted":::

    5. Klicken Sie auf **Verbinden**.

2. Erstellen Sie eine neue Tabelle mit dem Namen **Employees**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Fügen Sie der Tabelle **Employees** einige Mitarbeiterdatensätze hinzu.

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```

## <a name="step-4-provision-enclave-enabled-keys"></a>Schritt 4: Bereitstellen Enclave-fähiger Schlüssel

In diesem Schritt erstellen Sie einen Spaltenhauptschlüssel und einen Spaltenverschlüsselungsschlüssel, die Enclave-Berechnungen zulassen.

1. Erweitern Sie Ihre Datenbank im **Objekt-Explorer** mit der SSMS-Instanz aus dem vorherigen Schritt, und navigieren Sie zu **Sicherheit** > **Always Encrypted-Schlüssel**.
1. Stellen Sie einen neuen Enclave-fähigen Spaltenhauptschlüssel bereit:
    1. Klicken Sie mit der rechten Maustaste auf **Always Encrypted-Schlüssel**, und wählen Sie **Neuer Spaltenhauptschlüssel** aus.
    2. Wählen Sie den Namen des Spaltenhauptschlüssels aus: **CMK1**.
    3. Stellen Sie sicher, dass Sie entweder **Windows-Zertifikatspeicher (Aktueller Benutzer oder lokaler Computer)** oder **Azure Key Vault** auswählen.
    4. Wählen Sie **Enclave-Berechnungen zulassen**.
    5. Wenn Sie Azure Key Vault ausgewählt haben, melden Sie sich bei Azure an, und wählen Sie Ihren Schlüsseltresor aus. Weitere Informationen zum Erstellen eines Schlüsseltresors für Always Encrypted finden Sie unter [Verwalten Ihrer Schlüsseltresore im Azure-Portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Wählen Sie Ihr Zertifikat oder Ihren Azure-Schlüsselwertschlüssel aus, wenn bereits vorhanden, oder klicken Sie auf die Schaltfläche **Zertifikat generieren**, um ein neues zu erstellen.
    7. Klicken Sie auf **OK**.

        :::image type="content" source="./media/always-encrypted-enclaves/allow-enclave-computations.png" alt-text="Enclave-Berechnungen zulassen":::

1. Erstellen Sie einen neuen Enclave-fähigen Spaltenverschlüsselungsschlüssel:

    1. Klicken Sie mit der rechten Maustaste auf **Always Encrypted-Schlüssel**, und wählen Sie **Neuer Spaltenverschlüsselungsschlüssel**.
    2. Geben Sie einen Namen für den neuen Spaltenverschlüsselungsschlüssel ein: **CEK1**.
    3. Wählen Sie in der Dropdownliste **Spaltenhauptschlüssel** den in den vorherigen Schritten erstellten Spaltenhauptschlüssel.
    4. Klicken Sie auf **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Schritt 5: Direktes Verschlüsseln einiger Spalten

In diesem Schritt verschlüsseln Sie die in den Spalten **SSN** und **Salary** gespeicherten Daten in der serverseitigen Enclave und testen dann eine SELECT-Abfrage der Daten.

1. Öffnen Sie eine neue SSMS-Instanz, und stellen Sie eine Verbindung mit Ihrer Datenbank her. Always Encrypted muss dabei für die Datenbankverbindung **aktiviert** sein.
    1. Starten Sie eine neue SSMS-Instanz.
    2. Geben Sie im Dialogfeld **Verbindung mit dem Server herstellen** den vollqualifizierten Namen Ihres Servers (z. B. *myserver135.database.windows.net*) sowie den Benutzernamen und das Kennwort des Administrators ein, den bzw. das Sie beim Erstellen des Servers festgelegt haben.
    3. Klicken Sie auf **Optionen >>** , und wählen Sie die Registerkarte **Verbindungseigenschaften** aus. Wählen Sie unbedingt die Datenbank **ContosoHR** (nicht die Standardmasterdatenbank) aus. 
    4. Wählen Sie die Registerkarte **Always Encrypted** aus.
    5. Stellen Sie sicher, dass das Kontrollkästchen **Always Encrypted aktivieren (Spaltenverschlüsselung)** aktiviert **ist**.
    6. Geben Sie die Enclave-Nachweis-URL an, die Sie mithilfe der Schritte unter [Schritt 2: Konfigurieren eines Nachweisanbieters](#step-2-configure-an-attestation-provider) abgerufen haben. Sehen Sie sich hierzu folgenden Screenshot an:

        :::image type="content" source="./media/always-encrypted-enclaves/connect-to-server-configure-attestation.png" alt-text="Herstellen einer Verbindung mit dem Nachweis":::

    7. Wählen Sie **Verbinden**.
    8. Wenn Sie aufgefordert werden, die Parametrisierung für Always Encrypted zu aktivieren, wählen Sie **Aktivieren** aus.

1. Öffnen Sie mit derselben SSMS-Instanz (Always Encrypted ist aktiviert) ein neues Abfragefenster, und verschlüsseln Sie die Spalten **SSN** und **Salary**, indem Sie die folgenden Anweisungen ausführen:

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Achten Sie auf die ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE-Anweisung, die den Abfrageplancache für die Datenbank im obigen Skript löscht. Nachdem Sie die Tabelle bearbeitet haben, müssen Sie die Pläne für alle Batches und gespeicherten Prozeduren leeren, die Zugriff auf die Tabelle haben, um die Informationen für die Parameterverschlüsselung zu aktualisieren. 

1. Um zu überprüfen, ob die Spalten **SSN** und **Salary** nun verschlüsselt sind, öffnen Sie ein neues Abfragefenster in der SSMS-Instanz, **ohne** dass Always Encrypted für die Datenbankverbindung aktiviert ist, und führen Sie die folgende Anweisung aus. Das Abfragefenster sollte verschlüsselte Werte in den Spalten **SSN** und **Salary** zurückgeben. Wenn Sie die gleiche Abfrage mit der SSMS-Instanz ausführen, bei der Always Encrypted aktiviert ist, sollten die Daten entschlüsselt angezeigt werden.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Schritt 6: Ausführen umfangreicher Abfragen für verschlüsselte Spalten

Sie können umfangreiche Abfragen für verschlüsselte Spalten ausführen. Ein Teil der Abfrageverarbeitung wird in der serverseitigen Enclave ausgeführt. 

1. Stellen Sie in der SSMS-Instanz **mit** aktiviertem Always Encrypted sicher, dass auch die Parametrisierung für Always Encrypted aktiviert ist.
    1. Wählen Sie im Hauptmenü von SSMS **Tools** aus.
    2. Wählen Sie **Optionen...** aus.
    3. Navigieren Sie zu **Abfrageausführung** > **SQL Server** > **Erweitert**.
    4. Stellen Sie sicher, dass **Parametrisierung für Always Encrypted aktivieren** aktiviert ist.
    5. Klicken Sie auf **OK**.
2. Öffnen Sie ein neues Abfragefenster, fügen Sie die folgende Abfrage ein, und führen Sie sie anschließend aus. Die Abfrage sollte Klartextwerte und Zeilen zurückgeben, die den angegebenen Suchkriterien entsprechen.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Probieren Sie dieselbe Abfrage erneut in der SSMS-Instanz aus, in der Always Encrypted nicht aktiviert ist. Ein Fehler sollte auftreten.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie dieses Tutorial abgeschlossen haben, können Sie mit einem der folgenden Tutorials fortfahren:
- [Tutorial: Entwickeln einer .NET-Anwendung mithilfe von Always Encrypted mit Secure Enclaves](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Tutorial: Entwickeln einer .NET Framework-Anwendung mithilfe von Always Encrypted mit Secure Enclaves](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Tutorial: Erstellen und Verwenden von Indizes für Enclave-fähige Spalten mit zufälliger Verschlüsselung](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Siehe auch

- [Konfigurieren und Verwenden von Always Encrypted mit Secure Enclaves](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
