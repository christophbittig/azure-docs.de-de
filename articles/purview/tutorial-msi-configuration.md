---
title: Konfigurieren des Zugriffs auf Datenquellen für Azure Purview MSI in großem Stil
description: In diesem Tutorial konfigurieren Sie die Azure MSI-Einstellungen für Ihre Azure-Datenquellenabonnements.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 101d18cdecdc4fc7d4fb33b824500350f53b7b3f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213392"
---
# <a name="tutorial-configure-access-to-data-sources-for-azure-purview-msi-at-scale"></a>Tutorial: Konfigurieren Sie den Zugriff auf Datenquellen für Azure Purview MSI in großem Stil

Azure Purview benötigt Zugriff auf die Datenquellen, um sie zu überprüfen. Dieses Tutorial richtet sich an Besitzer von Azure-Abonnements und Azure Purview-Datenquellenadministratoren. Damit können Sie den erforderlichen Zugriff identifizieren und die erforderlichen Authentifizierungs- und Netzwerkregeln für Azure Purview über Azure-Datenquellen hinweg einrichten.

In Teil 2 dieser Tutorialreihe führen Sie die folgenden Aktionen aus:

> [!div class="checklist"]
>
> * Suchen Sie Ihre Datenquellen und bereiten Sie eine Liste mit den Datenquellenabonnements vor.
> * Führen Sie ein Skript aus, um alle fehlenden rollenbasierten Zugriffssteuerungen (RBAC) oder erforderlichen Netzwerkkonfigurationen bei Ihren Azure-Datenquellen zu konfigurieren.
> * Überprüfen Sie den Ausgabebericht.

## <a name="prerequisites"></a>Voraussetzungen

* Die Azure-Abonnements, in denen sich Ihre Datenquellen befinden. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
* Ein [Azure Purview-Konto](create-catalog-portal.md)
* Eine Azure Key Vault-Ressource für jedes Abonnement, das die Datenquellen wie Azure SQL-Datenbank, Azure Synapse Analytics oder Azure SQL Managed Instance enthält.
* Das [Azure Purview MSI-Konfigurations](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration)skript.

> [!NOTE]
> Das Azure Purview MSI-Konfigurationsskript ist nur für Windows verfügbar.
> Dieses Skript wird derzeit für die verwaltete Identität (Managed Identity, MSI) von Azure Purview unterstützt.

> [!IMPORTANT]
> Es wird dringend empfohlen, alle Änderungen zu testen und zu überprüfen, die das Skript in Ihrer Azure-Umgebung ausführt, bevor Sie es in Ihrer Produktionsumgebung bereitstellen.

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>Bereiten Sie die Liste der Azure-Abonnements für die Datenquellen vor 

Erstellen Sie vor dem Ausführen des Skripts eine .csv-Datei (z. B. C:\temp\Subscriptions.csv) mit vier Spalten:
   
|Spaltenname|Beschreibung|Beispiel|
|----|----|----|
|`SubscriptionId`|Azure-Abonnement-IDs für Ihre Datenquellen.|12345678-aaaa-bbbb-cccc-1234567890ab|
|`KeyVaultName`|Der Name des vorhandenen Schlüsseltresors, der im Datenquellenabonnement bereitgestellt wird.|ContosoDevKeyVault|
|`SecretNameSQLUserName`|Der Name eines vorhandenen Azure Key Vault-Geheimnisses, das einen Azure Active Directory-Benutzernamen (Azure AD) enthält, mit dem man sich über die Azure AD-Authentifizierung bei Azure Synapse, Azure SQL-Datenbank oder Azure SQL Managed Instance anmelden kann.|ContosoDevSQLAdmin|
|`SecretNameSQLPassword`|Der Name eines vorhandenen Azure Key Vault-Geheimnisses, das ein Azure Active Directory-Benutzerkennwort (Azure AD) enthält, mit dem man sich über die Azure AD-Authentifizierung bei Azure Synapse, Azure SQL-Datenbank oder Azure SQL Managed Instance anmelden kann.|ContosoDevSQLPassword|
   


**Beispiel .csv-Datei**:
    
:::image type="content" source="./media/tutorial-data-sources-readiness/subscriptions-input.png" alt-text="Ein Screenshot, der eine beispielhafte Abonnementliste zeigt." lightbox="./media/tutorial-data-sources-readiness/subscriptions-input.png":::

> [!NOTE] 
> Bei Bedarf können Sie den Dateinamen und den Pfad im Code aktualisieren.


## <a name="run-the-script-and-install-the-required-powershell-modules"></a>Führen Sie das Skript aus und installieren Sie die erforderlichen PowerShell-Module 

Führen Sie die folgenden Schritte aus, um das Skript auf Ihrem Windows-Computer auszuführen:

1. [Herunterladen des Azure Purview MSI-Konfigurationsskripts](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration) an den Speicherort Ihrer Wahl.

2. Geben Sie auf Ihrem Computer **PowerShell** in das Suchfeld auf der Windows-Taskleiste ein. Klicken Sie in der Suchliste auf **Windows PowerShell** und halten Sie die Maustaste gedrückt (oder klicken Sie mit der rechten Maustaste darauf), und wählen Sie **Als Administrator ausführen** aus.

3. Geben Sie den folgenden Befehl im PowerShell-Fenster ein. (Ersetzen Sie `<path-to-script>` durch den Ordnerpfad der extrahierten Skript-Datei.)

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. Geben Sie den folgenden Befehl ein, um die Azure-Cmdlets zu installieren:

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
5. Wird die Meldung *Der NuGet-Anbieter ist erforderlich, um den Vorgang fortzusetzen* angezeigt, geben Sie **J** ein, und drücken dann die **Eingabetaste**.

6. Wird die Meldung *Nicht vertrauenswürdiges Repository* angezeigt, geben Sie **A** ein und drücken dann die **Eingabetaste**.

7. Wiederholen Sie die vorherigen Schritte, um die Module `Az.Synapse` und `AzureAD` zu installieren.

Es kann bis zu einer Minute dauern, bis PowerShell die erforderlichen Module installiert hat.


## <a name="collect-other-data-needed-to-run-the-script"></a>Sammeln von anderen Daten, die zum Ausführen des Skripts erforderlich sind

Bevor Sie das PowerShell-Skript ausführen, um die Bereitschaft Ihrer Datenquellenabonnements zu überprüfen, rufen Sie die Werte der folgenden Argumente ab, die in den Skripts verwendet werden:

- `AzureDataType`: Wählen Sie eine der folgenden Optionen als Datenquellentyp aus, um die Bereitschaft des Datentyps in allen Ihrer Abonnements zu überprüfen: 
    
    - `BlobStorage`

    - `AzureSQLMI`

    - `AzureSQLDB`
    
    - `ADLSGen2`
    
    - `ADLSGen1`
    
    - `Synapse`
    
    - `All`

- `PurviewAccount`: Der vorhandene Ressourcenname Ihres Azure Purview-Kontos.

- `PurviewSub`: Die ID des Abonnements, in dem das Azure Purview-Konto bereitgestellt wird.

## <a name="verify-your-permissions"></a>Überprüfen von Berechtigungen

Stellen Sie sicher, dass Ihr Benutzer über die folgenden Rollen und Berechtigungen verfügt:

Sie benötigen mindestens die folgenden Berechtigungen, um das Skript in Ihrer Azure-Umgebung auszuführen:

Rolle | Bereich | Warum wird sie benötigt? |
|-------|--------|--------|
| **Globaler Leser** | Azure AD-Mandant | So lesen Sie die Azure SQL Administratoren-Benutzergruppenmitgliedschaft und die Azure Purview MSI |
| **Globaler Administrator** | Azure AD-Mandant | So weisen den verwalteten Instanzen von Azure SQL die Rolle **Verzeichnisleser** zu |
| **Mitwirkender** | Das Abonnementen oder die Ressourcengruppe, in dem Ihr Azure Purview-Konto erstellt wird | So lesen Sie die Azure Purview-Kontoressource und erstellen eine Schlüsseltresor-Ressource und ein Geheimnis |
| **„Besitzer“ oder „Benutzerzugriffsadministrator“** | Die Verwaltungsgruppe oder das Abonnement, in der sich Ihre Azure-Datenquellen befinden | So weisen Sie RBAC zu |
| **Mitwirkender** | Die Verwaltungsgruppe oder das Abonnement, in der sich Ihre Azure-Datenquellen befinden | So richten Sie eine Netzwerkkonfiguration ein |
| **SQL Admin** (Azure AD-Authentifizierung) | Azure SQL Server-Instanzen oder Verwaltete Azure SQL-Instanzen | So weisen Sie dem Azure Purview-Dienst die Rolle **db_datareader** zu |
| Zugriff auf Ihren Azure-Schlüsseltresor | So können Sie Zugriff auf das Geheimnis des Schlüsseltresors für die Azure SQL-Datenbank, die Azure SQL-Managed Instance oder die Azure Synapse Authentifizierung abrufen/auflisten |  


## <a name="run-the-client-side-readiness-script"></a>Ausführen des clientseitigen Bereitschaftsskripts

Befolgen Sie die nachstehenden Schritte, um das Skript auszuführen:

1. Verwenden Sie den folgenden Befehl, um zum Ordner des Skripts zu wechseln. Ersetzen Sie `<path-to-script>` durch den Ordnerpfad der extrahierten Datei.

   ```powershell
   cd <path-to-script>
   ```

2. Führen Sie den folgenden Befehl aus, um die Ausführungsrichtlinie für den lokalen Computer festzulegen. Geben Sie **A** für *Ja, alle* ein, wenn Sie zur Änderung der Ausführungsrichtlinie aufgefordert werden.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

3. Führen Sie das Skript mit den folgenden Parametern aus. Ersetzen Sie die Platzhalter `DataType` , `PurviewName` und `SubscriptionID` .

   ```powershell
   .\purview-msi-configuration.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   Wenn Sie den Befehl ausführen, erscheint möglicherweise zweimal ein Popupfenster, in dem Sie aufgefordert werden, sich bei Azure und bei Azure AD mit Ihren Azure Active Directory-Anmeldeinformationen anzumelden.

Die Erstellung des Berichts kann je nach Anzahl der vorhandenen Azure-Abonnements und -Ressourcen in der Umgebung einige Minuten dauern. 

Sie werden möglicherweise aufgefordert, sich bei Ihren Azure SQL Server-Instanzen anzumelden, wenn die Anmeldeinformationen nicht mit denen im Schlüsseltresor übereinstimmen. Sie können die Anmeldeinformationen angeben oder die **Eingabetaste** auswählen, um den jeweiligen Server zu überspringen. 

Sehen Sie sich nach Abschluss des Prozesses den Ausgabebericht an, um die Änderungen zu überprüfen. 


## <a name="more-information"></a>Weitere Informationen

### <a name="what-data-sources-are-supported-by-the-script"></a>Welche Datenquellen werden vom Skript unterstützt?

Derzeit werden die folgenden Datenquellen vom Skript unterstützt:

- Azure Blob Storage (BlobStorage)
- Azure Data Lake Storage Gen2 (ADLSGen2)
- Azure Data Lake Storage Gen1 (ADLSGen1)
- Azure SQL-Datenbank (AzureSQLDB)
- Azure SQL Managed Instance (AzureSQLMI)
- Dedizierter Azure Synapse-Pool (Synapse)

Sie können alle oder eine dieser Datenquellen als Eingabeparameter auswählen, wenn Sie das Skript ausführen.

### <a name="what-configurations-are-included-in-the-script"></a>Welche Konfigurationen sind im Skript enthalten?

Mithilfe dieses Skripts können Sie die folgenden Aufgaben automatisch ausführen:

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob Storage (BlobStorage)

- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Weisen Sie die Azure RBAC für die **Leser** Rolle der Azure Purview MSI für den ausgewählten Bereich zu. Überprüfen Sie die Rollenzuweisung. 
- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Weisen Sie die Azure RBAC für die **Leser von Speicherblobdaten** Rolle der Azure Purview MSI in jedem der Abonnements unterhalb des ausgewählten Bereichs zu. Überprüfen Sie die Rollenzuweisungen.
- Netzwerk Melden Sie, ob ein privater Endpunkt zum Speichern erstellt wurde und für Blob Storage aktiviert ist.
- Der Dienstendpunkt. Wenn der private Endpunkt deaktiviert ist, überprüfen Sie, ob der Dienstendpunkt aktiviert ist und aktivieren Sie die Funktion **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**.

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Weisen Sie die Azure RBAC für die **Leser** Rolle der Azure Purview MSI für den ausgewählten Bereich zu. Überprüfen Sie die Rollenzuweisung. 
- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Weisen Sie die Azure RBAC für die **Leser von Speicherblobdaten** Rolle der Azure Purview MSI in jedem der Abonnements unterhalb des ausgewählten Bereichs zu. Überprüfen Sie die Rollenzuweisungen.
- Netzwerk Melden Sie, ob ein privater Endpunkt zum Speichern erstellt wurde und für Blob Storage aktiviert ist.
- Der Dienstendpunkt. Wenn der private Endpunkt deaktiviert ist, überprüfen Sie, ob der Dienstendpunkt aktiviert ist und aktivieren Sie die Funktion **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**.

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- Netzwerk Überprüfen Sie, ob der Dienstendpunkt aktiviert ist, aktivieren Sie die Funktion **Allen Azure-Diensten den Zugriff auf dieses Data Lake Storage Gen1-Konto erlauben** in Data Lake Storage.
- Berechtigungen. Weisen Sie der Azure Purview MSI den Lese-/Ausführungszugriff zu. Überprüfen Sie den Zugriff. 

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL-Datenbank (AzureSQLDB)

- SQL Server-Instanzen:
  - Netzwerk Melden Sie, ob der öffentliche Endpunkt oder der private Endpunkt aktiviert ist.
  - Firewall. Wenn der private Endpunkt deaktiviert ist, überprüfen Sie die Firewall-Regeln und aktivieren Sie die Funktion **Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten**.
  - Azure AD-Verwaltung. Aktivieren der Azure AD-Authentifizierung für die Azure SQL-Datenbank.

- SQL-Datenbanken:
  - SQL-Rolle. Weisen Sie der Azure Purview-MSI die Rolle **db_datareader** zu.

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance (AzureSQLMI)

- SQL Managed Instance-Server:
  - Netzwerk Vergewissern Sie sich, dass der öffentliche Endpunkt oder der private Endpunkt eingeschaltet ist. Melden Sie, ob der öffentliche Endpunkt deaktiviert ist.
  - ProxyOverride. Überprüfung Sie, ob die Azure SQL Managed Instance als Proxy oder als Umleitung konfiguriert ist.
  - Netzwerk Aktualisieren Sie NSG-Regeln, um eingehenden AzureCloud-Zugriff auf SQL Server Instanzen über erforderliche Ports zuzulassen:  
    - Umleitung: 1433 und 11000-11999
    
    oder 
    - Proxy: 3342
    
    Überprüfen Sie diesen Zugriff. 
  - Azure AD-Verwaltung. Aktivieren Sie die Azure AD-Authentifizierung für die Azure SQL Managed Instance.
  
- SQL-Datenbanken:
  - SQL-Rolle. Weisen Sie der Azure Purview-MSI die Rolle **db_datareader** zu.

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Dedizierter Azure Synapse-Pool (Synapse)

- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Weisen Sie die Azure RBAC für die **Leser** Rolle der Azure Purview MSI für den ausgewählten Bereich zu. Überprüfen Sie die Rollenzuweisung. 
- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Weisen Sie die Azure RBAC für die **Leser von Speicherblobdaten** Rolle der Azure Purview MSI in jedem der Abonnements unterhalb des ausgewählten Bereichs zu. Überprüfen Sie die Rollenzuweisungen.
- SQL Server-Instanzen (dedizierte Pools):
  - Netzwerk Melden Sie, ob der öffentliche Endpunkt oder der private Endpunkt aktiviert ist.
  - Firewall. Wenn der private Endpunkt deaktiviert ist, überprüfen Sie die Firewall-Regeln und aktivieren Sie die Funktion **Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten**.
  - Azure AD-Verwaltung. Aktivieren der Azure AD-Authentifizierung für die Azure SQL-Datenbank.

- SQL-Datenbanken:
  - SQL-Rolle. Weisen Sie der Azure Purview-MSI die Rolle **db_datareader** zu.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
>
> * Identifizieren Sie den erforderlichen Zugriff und richten Sie die erforderlichen Authentifizierungs- und Netzwerkregeln für Azure Purview über die Azure-Datenquellen hinweg ein.

Im nächsten Tutorial erfahren Sie, wie Sie [Mehrere Quellen in Azure Purview registrieren und überprüfen](register-scan-azure-multiple-sources.md) können.
