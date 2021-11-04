---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 2fb845291697a2aee1d317c700a9a912d57565a5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051140"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Erstellen einer selbstgehosteten Integration Runtime

In diesem Abschnitt erstellen Sie eine selbstgehostete Integration Runtime und ordnen sie einem lokalen Computer mit der SQL Server-Datenbank zu. Die selbstgehostete Integration Runtime ist die Komponente, die Daten aus SQL Server auf Ihrem Computer in Azure SQL-Datenbank kopiert. 

1. Erstellen Sie eine Variable für den Namen der Integration Runtime. Verwenden Sie einen eindeutigen Namen, und notieren Sie ihn. Sie benötigen ihn später in diesem Tutorial. 

   ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Erstellen Sie eine selbstgehostete Integration Runtime. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Hier ist die Beispielausgabe:

   ```console
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. Führen Sie den folgenden Befehl aus, um den Status der erstellten Integration Runtime abzurufen. Vergewissern Sie sich, dass der Wert der Eigenschaft **State** auf **NeedRegistration** festgelegt ist. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Hier ist die Beispielausgabe:

   ```console  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. Führen Sie den folgenden Befehl aus, um die Authentifizierungsschlüssel abzurufen, mit denen Sie die selbstgehostete Integration Runtime beim Azure Data Factory-Dienst in der Cloud registrieren: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Hier ist die Beispielausgabe:

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Kopieren Sie einen der Schlüssel (ohne Anführungszeichen) zum Registrieren der selbstgehosteten Integration Runtime, die Sie in den nächsten Schritten auf Ihrem Computer installieren.  

## <a name="install-the-integration-runtime-tool"></a>Installieren des Integration Runtime-Tools

1. Falls die Integration Runtime bereits auf Ihrem Computer installiert ist, deinstallieren Sie sie über **Programme hinzufügen oder entfernen**. 

2. Führen Sie den [Download](https://www.microsoft.com/download/details.aspx?id=39717) der selbstgehosteten Integration Runtime auf einen lokalen Windows-Computer durch. Führen Sie die Installation aus.

3. Klicken Sie auf der Seite **Welcome to Microsoft Integration Runtime Setup** (Willkommen beim Microsoft Integration Runtime-Setup) auf **Weiter**.

4. Stimmen Sie auf der Seite mit den **Microsoft-Software-Lizenzbedingungen** den Bedingungen bzw. der Lizenzvereinbarung zu, und klicken Sie auf **Weiter**.

5. Klicken Sie auf der Seite **Zielordner** auf **Weiter**.

6. Wählen Sie auf der Seite **Ready to install Microsoft Integration Runtime** (Bereit für Installation der Microsoft Integration Runtime) die Option **Installieren**.

7. Klicken Sie auf der Seite **Completed the Microsoft Integration Runtime Setup** (Setup für Microsoft Integration Runtime abgeschlossen) auf **Fertig stellen**.

8. Fügen Sie auf der Seite **Integrationslaufzeit (selbstgehostet) registrieren** den Schlüssel ein, den Sie im vorherigen Abschnitt gespeichert haben, und klicken Sie auf **Registrieren**. 

    :::image type="content" source="media/data-factory-create-install-integration-runtime/register-integration-runtime.png" alt-text="Registrieren der Integration Runtime":::

9. Klicken Sie auf der Seite **Neuer Knoten der Integrationslaufzeit (selbstgehostet)** auf **Fertig stellen**. 

10. Wenn die selbstgehostete Integration Runtime erfolgreich registriert wurde, wird folgende Meldung angezeigt:

    :::image type="content" source="media/data-factory-create-install-integration-runtime/registered-successfully.png" alt-text="Erfolgreich registriert":::

14. Klicken Sie auf der Seite **Integrationslaufzeit (selbstgehostet) registrieren** auf **Konfigurations-Manager starten**.

15. Wenn der Knoten mit dem Clouddienst verbunden ist, wird die folgende Seite angezeigt:

    :::image type="content" source="media/data-factory-create-install-integration-runtime/node-is-connected.png" alt-text="Seite „Knoten ist verbunden“":::

16. Testen Sie nun die Verbindung mit Ihrer SQL Server-Datenbank.

    :::image type="content" source="media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png" alt-text="Registerkarte „Diagnose“":::   

    a. Wechseln Sie auf der Seite **Konfigurations-Manager** zur Registerkarte **Diagnose**.

    b. Wählen Sie als Datenquellentyp die Option **SqlServer**.

    c. Geben Sie den Servernamen ein.

    d. Geben Sie den Datenbanknamen ein.

    e. Wählen Sie den Authentifizierungsmodus aus.

    f. Geben Sie den Benutzernamen ein.

    g. Geben Sie das Kennwort ein, das dem Benutzernamen zugeordnet ist.

    h. Wählen Sie die Option **Test**, um zu überprüfen, ob die Integration Runtime eine Verbindung mit SQL Server herstellen kann. Wenn die Verbindung erfolgreich hergestellt wurde, wird ein grünes Häkchen angezeigt. Wenn keine Verbindung hergestellt wurde, wird eine Fehlermeldung angezeigt. Beheben Sie alle Probleme, und stellen Sie sicher, dass die Integration Runtime eine Verbindung mit SQL Server herstellen kann.    

    > [!NOTE]
    > Notieren Sie die Werte für Authentifizierungstyp, Server, Datenbank, Benutzer und Kennwort. Sie benötigen sie später in diesem Tutorial.
