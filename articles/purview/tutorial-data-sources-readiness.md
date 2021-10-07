---
title: Bereitschaftsüberprüfung für Datenquellen im großen Stil
description: In diesem Tutorial prüfen Sie die Bereitschaft Ihrer Azure-Datenquellen, bevor Sie diese in Azure Purview registrieren und überprüfen.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 1eaca951a5abe43bda2621043321537cee2daa13
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213411"
---
# <a name="tutorial-check-data-source-readiness-at-scale"></a>Tutorial: Bereitschaftsüberprüfung für Datenquellen im großen Stil

Azure Purview benötigt Zugriff auf die Datenquellen, um sie zu überprüfen. Für diesen Zugriff verwendet der Service die entsprechenden Anmeldeinformationen. Bei *Anmeldeinformationen* handelt es sich um Informationen für die Authentifizierung, die von Azure Purview für die Authentifizierung bei Ihren registrierten Datenquellen genutzt werden können. Es gibt mehrere Möglichkeiten, die Anmeldeinformationen für Azure Purview einzurichten, z. B.: 
- Die verwaltete Identität, die dem Azure Purview-Konto zugewiesen ist.
- Geheimnisse, die in Azure Key Vault gespeichert sind. 
- Dienstprinzipale.

In dieser zweiteiligen Tutorialreihe lernen Sie wie Sie, die erforderlichen Azure-Rollenzuweisungen und den Netzwerkzugriff für verschiedene Azure-Datenquellen in Ihren Azure-Abonnements in großem Stil prüfen und konfigurieren. Anschließend können Sie Ihre Azure-Datenquellen in Azure Purview registrieren und überprüfen. 

Führen Sie das [Prüflistenskript für die Bereitschaftsüberprüfung von Azure Purview-Datenquellen](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) aus, nachdem Sie Ihr Azure Purview-Konto bereitgestellt haben, und bevor Sie Ihre Azure-Datenquellen registrieren und überprüfen. 

In Teil 1 dieser Tutorialreihe führen Sie die folgenden Aktionen aus:

> [!div class="checklist"]
>
> * Suchen Sie Ihre Datenquellen und bereiten Sie eine Liste mit den Datenquellenabonnements vor.
> * Führen Sie das Prüflistenskript für die Bereitschaftsüberprüfung aus, um eine fehlende rollenbasierte Zugriffssteuerung (RBAC) oder fehlende Netzwerkkonfigurationen bei Ihren Azure-Datenquellen zu ermitteln.
> * Überprüfen Sie den Ausgabebericht auf fehlende Netzwerkkonfigurationen und Rollenzuweisungen, die für die verwaltete Azure Purview-Identität (MSI) erforderlich sind. 
> * Geben Sie den Bericht für Azure-Abonnementbesitzer frei, damit diese die vorgeschlagenen Aktionen ausführen können.

## <a name="prerequisites"></a>Voraussetzungen

* Die Azure-Abonnements, in denen sich Ihre Datenquellen befinden. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
* Ein [Azure Purview-Konto](create-catalog-portal.md)
* Eine Azure Key Vault-Ressource für jedes Abonnement, das Datenquellen wie Azure SQL-Datenbank, Azure Synapse Analytics oder Azure SQL Managed Instance enthält.
* Das [Prüflistenskript für die Bereitschaftsüberprüfung von Azure Purview-Datenquellen](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness).

> [!NOTE]
> Die Prüfliste für die Bereitschaftsüberprüfung von Azure Purview-Datenquellen ist nur für Windows verfügbar.
> Das Prüflistenskript für die Bereitschaftsüberprüfung wird derzeit für Azure Purview MSI unterstützt.

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>Vorbereiten einer Liste der Azure-Abonnements für die Datenquellen

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

1. [Laden Sie das Prüflistenskript für die Bereitschaftsüberprüfung von Azure Purview-Datenquellen](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) an den gewünschten Speicherort herunter.

2. Geben Sie auf Ihrem Computer **PowerShell** in das Suchfeld auf der Windows-Taskleiste ein. Klicken Sie in der Suchliste auf **Windows PowerShell**, und halten Sie die Maustaste gedrückt (oder klicken Sie mit der rechten Maustaste darauf), und wählen Sie dann **Als Administrator ausführen** aus.

3. Geben Sie den folgenden Befehl im PowerShell-Fenster ein. (Ersetzen Sie `<path-to-script>` durch den Ordnerpfad der extrahierten Skript-Datei.)

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. Geben Sie den folgenden Befehl ein, um die Azure-Cmdlets zu installieren:

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
6. Wird die Meldung *Der NuGet-Anbieter ist erforderlich, um den Vorgang fortzusetzen* angezeigt, geben Sie **J** ein, und drücken dann die **Eingabetaste**.

7. Wird die Meldung *Nicht vertrauenswürdiges Repository* angezeigt, geben Sie **A** ein und drücken dann die **Eingabetaste**.

5. Wiederholen Sie die vorherigen Schritte, um die Module `Az.Synapse` und `AzureAD` zu installieren.

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

Rolle oder Berechtigung | Bereich |
|-------|--------|
| **Globaler Leser** | Azure AD-Mandant |
| **Leser** | Azure-Abonnements, in denen sich Ihre Azure-Datenquellen befinden. |
| **Leser** | Abonnement, in dem Ihr Azure Purview-Konto erstellt wurde. |
| **SQL Admin** (Azure AD-Authentifizierung) | dedizierte Azure Synapse-Pools, Azure SQL-Datenbankinstanzen, verwaltete Azure SQL-Instanzen |
| Zugriff auf Ihren Azure-Schlüsseltresor | Zugriff auf den Schlüsseltresor, um Geheimnisse oder Azure Key Vault-Geheimnisbenutzer abzurufen/aufzulisten |  


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
   .\purview-data-sources-readiness-checklist.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   Wenn Sie den Befehl ausführen, erscheint möglicherweise zweimal ein Popupfenster, in dem Sie aufgefordert werden, sich bei Azure und bei Azure AD mit Ihren Azure Active Directory-Anmeldeinformationen anzumelden.


Die Erstellung des Berichts kann je nach Anzahl der Azure-Abonnements und -Ressourcen in der Umgebung einige Minuten dauern. 

Überprüfen Sie nach Abschluss des Prozesses den Ausgabebericht, in dem die fehlenden Konfigurationen in Ihren Azure-Abonnements oder -Ressourcen aufgelistet werden. Die Ergebnisse werden entweder als _Passed_ (bestanden), _Not Passed_ (nicht bestanden) oder _Awareness_ (Kenntnisnahme) angezeigt. Sie können die Ergebnisse mit den entsprechenden Abonnementadministratoren in Ihrer Organisation teilen, damit diese die erforderlichen Einstellungen vornehmen können.


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

### <a name="what-checks-are-included-in-the-results"></a>Welche Überprüfungen sind in den Ergebnissen enthalten?

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob Storage (BlobStorage)

- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Überprüfung, ob Azure Purview MSI in jedem Abonnement unter dem ausgewählten Bereich die Rolle **Leser von Speicherblobdaten** zugewiesen ist.
- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Überprüfung, ob Azure Purview MSI die Rolle **Leser** für den ausgewählten Bereich zugewiesen ist.
- Der Dienstendpunkt. Überprüfung, ob der Dienstendpunkt aktiviert ist, und Überprüfung, ob die Option **vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktiviert ist.
- Netzwerk: Überprüfung, ob ein privater Endpunkt zum Speichern erstellt wurde und für Blob Storage aktiviert ist.

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Überprüfung, ob Azure Purview MSI in jedem Abonnement unter dem ausgewählten Bereich die Rolle **Leser von Speicherblobdaten** zugewiesen ist.
- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Überprüfung, ob Azure Purview MSI die Rolle **Leser** für den ausgewählten Bereich zugewiesen ist.
- Der Dienstendpunkt. Überprüfung, ob der Dienstendpunkt aktiviert ist, und Überprüfung, ob die Option **vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktiviert ist.
- Netzwerk: Überprüfung, ob ein privater Endpunkt zum Speichern erstellt wurde und für Blob Storage aktiviert ist.

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- Netzwerk Überprüfung, ob der Dienstendpunkt aktiviert ist, und Überprüfung, ob die Option **Allen Azure-Diensten den Zugriff auf dieses Data Lake Storage Gen1-Konto erlauben** aktiviert ist.
- Berechtigungen. Überprüfung, ob Azure Purview MSI über Lese-/Ausführungsberechtigungen verfügt.

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL-Datenbank (AzureSQLDB)

- SQL Server-Instanzen:
  - Netzwerk Überprüfung, ob der öffentliche Endpunkt oder der private Endpunkt aktiviert ist.
  - Firewall. Überprüfung, ob die Option **Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** aktiviert ist.
  - Azure AD-Verwaltung. Überprüfung, ob Azure SQL Server über eine Azure AD-Authentifizierung verfügt.
  - Azure AD-Verwaltung. Ausfüllen des Azure SQL Server-Azure AD-Administratorbenutzers oder der entsprechenden Gruppe.

- SQL-Datenbanken:
  - SQL-Rolle. Überprüfung, ob Azure Purview MSI die Rolle **db_datareader** zugewiesen ist.

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance (AzureSQLMI)

- SQL Managed Instance-Server:
  - Netzwerk Überprüfung, ob der öffentliche Endpunkt oder der private Endpunkt aktiviert ist.
  - ProxyOverride. Überprüfung, ob Azure SQL Managed Instance als Proxy oder als Umleitung konfiguriert ist.
  - Netzwerk Überprüfung, ob die NSG über eine Eingangsregel verfügt, um AzureCloud über erforderliche Ports zuzulassen:  
    - Umleitung: 1433 und 11000-11999  
    oder
    - Proxy: 3342
  - Azure AD-Verwaltung. Überprüfung, ob Azure SQL Server über eine Azure AD-Authentifizierung verfügt.
  - Azure AD-Verwaltung. Ausfüllen des Azure SQL Server-Azure AD-Administratorbenutzers oder der entsprechenden Gruppe.

- SQL-Datenbanken:
  - SQL-Rolle. Überprüfung, ob Azure Purview MSI die Rolle **db_datareader** zugewiesen ist.

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Dedizierter Azure Synapse-Pool (Synapse)

- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Überprüfung, ob Azure Purview MSI in jedem Abonnement unter dem ausgewählten Bereich die Rolle **Leser von Speicherblobdaten** zugewiesen ist.
- Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC). Überprüfung, ob Azure Purview MSI die Rolle **Leser** für den ausgewählten Bereich zugewiesen ist.
- SQL Server-Instanzen (dedizierte Pools):
  - Netzwerk: Überprüfung, ob der öffentliche Endpunkt oder der private Endpunkt aktiviert ist.
  - Firewall: Überprüfung, ob die Option **Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** aktiviert ist.
  - Azure AD-Verwaltung: Überprüfung, ob Azure SQL Server über eine Azure AD-Authentifizierung verfügt.
  - Azure AD-Verwaltung: Ausfüllen des Azure SQL Server-Azure AD-Administratorbenutzers oder der entsprechenden Gruppe.

- SQL-Datenbanken:
  - SQL-Rolle. Überprüfung, ob Azure Purview MSI die Rolle **db_datareader** zugewiesen ist.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
>
> * Ausführen der Azure Purview-Prüfliste für die Bereitschaftsüberprüfung in großem Stil. Auf diese Weise wird ermittelt, ob bei Ihren Azure-Abonnements Konfigurationen fehlen, bevor Sie diese in Azure Purview registrieren und überprüfen.

Im nächsten Tutorial erfahren Sie, wie Sie den erforderlichen Zugriff ermitteln und die notwendigen Authentifizierungs- und Netzwerkregeln für Azure Purview über alle Azure-Datenquellen hinweg einrichten:

> [!div class="nextstepaction"]
> [Konfigurieren des Zugriffs auf Datenquellen für Azure Purview MSI in großem Stil](tutorial-msi-configuration.md)
