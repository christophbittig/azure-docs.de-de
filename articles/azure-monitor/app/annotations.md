---
title: Versionsanmerkungen für Application Insights | Microsoft Docs
description: Hier erfahren Sie, wie Sie Anmerkungen zum Nachverfolgen der Bereitstellung oder anderer wichtiger Ereignisse mit Application Insights erstellen.
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: a92e659353f6500a6e40e9704af73cae08e95fbf
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830153"
---
# <a name="release-annotations-for-application-insights"></a>Versionsanmerkungen für Application Insights

Mit Anmerkungen wird angegeben, wo Sie einen neuen Build bereitgestellt haben, oder es wird auf andere wichtige Ereignisse hingewiesen. Dank der Anmerkungen sehen Sie auf einen Blick, ob Ihre Änderungen Auswirkungen auf die Leistung Ihrer Anwendung hatten. Sie können automatisch durch das Buildsystem von [Azure Pipelines](/azure/devops/pipelines/tasks/) erstellt werden. Es können auch Anmerkungen erstellt werden, die alle gewünschten Ereignisse markieren, indem diese aus PowerShell erstellt werden.

## <a name="release-annotations-with-azure-pipelines-build"></a>Releaseanmerkungen mit Azure Pipelines-Build

Releaseanmerkungen sind ein Feature des cloudbasierten Azure Pipelines-Diensts von Azure DevOps.

Wenn alle folgenden Kriterien erfüllt sind, erstellt die Bereitstellungsaufgabe automatisch die Releaseanmerkung:

- Die Ressource, für die die Bereitstellung erfolgt, ist mit Application Insights (über die App-Einstellung `APPINSIGHTS_INSTRUMENTATIONKEY`) verknüpft.
- Die Application Insights-Ressource befindet sich im gleichen Abonnement wie die Ressource, in der die Bereitstellung erfolgt.
- Sie verwenden eine der folgenden Azure DevOps-Pipelineaufgaben:

    | Aufgabencode                 | Aufgabenname                     | Versionen     |
    |---------------------------|-------------------------------|--------------|
    | AzureAppServiceSettings   | Azure App Service-Einstellungen    | Beliebig          |
    | AzureRmWebAppDeployment   | Azure App Service-Bereitstellung      | V3 und höher |
    | AzureFunctionApp          | Azure-Funktionen               | Beliebig          |
    | AzureFunctionAppContainer | Azure Functions für Container | Beliebig          |
    | AzureWebAppContainer      | Azure-Web-App für Container  | Beliebig          |
    | AzureWebApp               | Azure-Web-App                 | Beliebig          |

> [!NOTE]
> Wenn Sie weiterhin die Bereitstellungsaufgabe für Application Insights-Anmerkungen verwenden, sollten Sie sie löschen.

### <a name="configure-release-annotations"></a>Konfigurieren von Versionsanmerkungen

Wenn Sie eine der Bereitstellungsaufgaben im vorherigen Abschnitt nicht verwenden können, müssen Sie in Ihrer Bereitstellungspipeline eine Inlineskriptaufgabe hinzufügen.

1. Navigieren Sie zu einer neuen oder vorhandenen Pipeline, und wählen Sie eine Aufgabe aus.
    :::image type="content" source="./media/annotations/task.png" alt-text="Screenshot: Ausgewählte Aufgabe unter „Phasen“" lightbox="./media/annotations/task.png":::
1. Fügen Sie eine neue Aufgabe hinzu, und wählen Sie **Azure CLI** aus.
    :::image type="content" source="./media/annotations/add-azure-cli.png" alt-text="Screenshot: Hinzufügen einer neuen Aufgabe und Auswählen der Azure CLI" lightbox="./media/annotations/add-azure-cli.png":::
1. Geben Sie das entsprechende Azure-Abonnement an.  Ändern Sie **Skripttyp** in *PowerShell* und **Skriptspeicherort** in *Inline*.
1. Fügen Sie das [PowerShell-Skript aus Schritt 2 im nächsten Abschnitt](#create-release-annotations-with-azure-cli) zu **Inlineskript** hinzu.
1. Fügen Sie unter **Skriptargumente** die folgenden Argumente hinzu, und ersetzen Sie die Platzhalter in eckigen Klammern durch Ihre eigenen Werte. Angaben für „-releaseProperties“ sind optional.

    ```powershell
        -aiResourceId "<aiResourceId>" `
        -releaseName "<releaseName>" `
        -releaseProperties @{"ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
    ```

    :::image type="content" source="./media/annotations/inline-script.png" alt-text="Screenshot: Azure CLI-Aufgabeneinstellungen mit hervorgehobenen Optionen für Skripttyp, Skriptspeicherort, Inlineskript und Skriptargumente" lightbox="./media/annotations/inline-script.png":::

    Im Folgenden finden Sie ein Beispiel für Metadaten, die Sie im optionalen releaseProperties-Argument mithilfe von [Build-](/azure/devops/pipelines/build/variables#build-variables-devops-services) und [Release-](/azure/devops/pipelines/release/variables#default-variables---release)Variablen festlegen können.
    

    ```powershell
    -releaseProperties @{
     "BuildNumber"="$(Build.BuildNumber)";
     "BuildRepositoryName"="$(Build.Repository.Name)";
     "BuildRepositoryProvider"="$(Build.Repository.Provider)";
     "ReleaseDefinitionName"="$(Build.DefinitionName)";
     "ReleaseDescription"="Triggered by $(Build.DefinitionName) $(Build.BuildNumber)";
     "ReleaseEnvironmentName"="$(Release.EnvironmentName)";
     "ReleaseId"="$(Release.ReleaseId)";
     "ReleaseName"="$(Release.ReleaseName)";
     "ReleaseRequestedFor"="$(Release.RequestedFor)";
     "ReleaseWebUrl"="$(Release.ReleaseWebUrl)";
     "SourceBranch"="$(Build.SourceBranch)";
     "TeamFoundationCollectionUri"="$(System.TeamFoundationCollectionUri)" }
    ```            

1. Speichern Sie.

## <a name="create-release-annotations-with-azure-cli"></a>Erstellen von Releaseanmerkungen mit der Azure CLI

Mit dem PowerShell-Skript „CreateReleaseAnnotation“ können Sie Anmerkungen auf der Grundlage eines beliebigen Prozesses erstellen, ohne Azure DevOps zu verwenden.

1. Melden Sie sich bei der [Azure CLI](/cli/azure/authenticate-azure-cli) an.

2. Erstellen Sie eine lokale Kopie des folgenden Skripts, und nennen Sie es „CreateReleaseAnnotation.ps1“.

    ```powershell
    param(
        [parameter(Mandatory = $true)][string]$aiResourceId,
        [parameter(Mandatory = $true)][string]$releaseName,
        [parameter(Mandatory = $false)]$releaseProperties = @()
    )
    
    $annotation = @{
        Id = [GUID]::NewGuid();
        AnnotationName = $releaseName;
        EventTime = (Get-Date).ToUniversalTime().GetDateTimeFormats("s")[0];
        Category = "Deployment";
        Properties = ConvertTo-Json $releaseProperties -Compress
    }
    
    $body = (ConvertTo-Json $annotation -Compress) -replace '(\\+)"', '$1$1"' -replace "`"", "`"`""
    az rest --method put --uri "$($aiResourceId)/Annotations?api-version=2015-05-01" --body "$($body) "

    # Use the following command for Linux Azure DevOps Hosts or other PowerShell scenarios
    # Invoke-AzRestMethod -Path "$aiResourceId/Annotations?api-version=2015-05-01" -Method PUT -Payload $body
    ```

3. Rufen Sie das PowerShell-Skript mithilfe des folgenden Codes auf, und ersetzen Sie dabei die in spitzen Klammern angegebenen Platzhalter durch Ihre eigenen Werte. Angaben für „-releaseProperties“ sind optional.

    ```powershell
         .\CreateReleaseAnnotation.ps1 `
          -aiResourceId "<aiResourceId>" `
          -releaseName "<releaseName>" `
          -releaseProperties @{"ReleaseDescription"="<a description>";
              "TriggerBy"="<Your name>" }
    ```

|Argument | Definition | Hinweis|
|--------------|-----------------------|--------------------|
|aiResourceId | Die Ressourcen-ID der Application Insights-Zielressource | Beispiel:<br> /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRGName/providers/microsoft.insights/components/MyResourceName|
|releaseName | Der Name, den die erstellte Releaseanmerkung erhalten soll | | 
|releaseProperties | Wird zum Anfügen benutzerdefinierter Metadaten an die Anmerkung verwendet. | Optional|


## <a name="view-annotations"></a>Anmerkungen anzeigen

> [!NOTE]
> Versionsanmerkungen sind im Bereich „Metriken“ von Application Insights derzeit nicht verfügbar.

Wenn Sie nun diese Versionsvorlage zum Bereitstellen einer neuen Version verwenden, wird jedes Mal eine Anmerkung an Application Insights gesendet. Die Anmerkungen können an den folgenden Orten angezeigt werden:

- Leistung

    :::image type="content" source="./media/annotations/performance.png" alt-text="Screenshot: Registerkarte „Leistung“ mit ausgewählter Releaseanmerkung (blauer Pfeil), um die Registerkarte „Releaseanmerkung“ anzuzeigen" lightbox="./media/annotations/performance.png":::

- Fehler

    :::image type="content" source="./media/annotations/failures.png" alt-text="Screenshot: Registerkarte „Fehler“ mit ausgewählter Releaseanmerkung (blauer Pfeil), um die Registerkarte „Releaseanmerkung“ anzuzeigen" lightbox="./media/annotations/failures.png":::
- Verwendung

    :::image type="content" source="./media/annotations/usage-pane.png" alt-text="Screenshot: Registerkartenleiste „Benutzer“ mit ausgewählten Releaseanmerkungen. Releaseanmerkungen werden als blaue Pfeile über dem Diagramm angezeigt und geben den Zeitpunkt an, zu dem ein Release erfolgt ist." lightbox="./media/annotations/usage-pane.png":::

- Arbeitsmappen

    Alle protokollbasierten Arbeitsmappenabfragen, bei denen bei der Visualisierung die Uhrzeit auf der X-Achse angezeigt wird.
    
    :::image type="content" source="./media/annotations/workbooks-annotations.png" alt-text="Screenshot: Arbeitsmappenbereich mit protokollbasierter Zeitreihenabfrage mit Anzeige von Anmerkungen" lightbox="./media/annotations/workbooks-annotations.png":::
    
    Navigieren Sie zum Aktivieren der Anmerkungen in Ihrer Arbeitsmappe zu **Erweiterte Einstellungen**, und wählen Sie **Anmerkungen anzeigen** aus.
    
    :::image type="content" source="./media/annotations/workbook-show-annotations.png" alt-text="Screenshot: Menü „Erweiterte Einstellungen“ mit hervorgehobenem Kontrollkästchen „Anmerkungen anzeigen“":::

Wählen Sie einen Anmerkungsmarker aus, um Details zur Version anzuzeigen, z. B. Anforderer, Quellcodeverwaltungsbranch, Releasepipeline und Umgebung.

## <a name="release-annotations-using-api-keys"></a>Releaseanmerkungen mit API-Schlüsseln

Releaseanmerkungen sind ein Feature des cloudbasierten Azure Pipelines-Diensts von Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installieren der Erweiterung für Anmerkungen (einmalig)

Um Releaseanmerkungen erstellen zu können, müssen Sie eine der zahlreichen Azure DevOps-Erweiterungen installieren, die im Visual Studio Marketplace zur Verfügung stehen.

1. Melden Sie sich bei Ihrem [Azure DevOps](https://azure.microsoft.com/services/devops/)-Projekt an.
   
1. Wählen Sie im Visual Studio Marketplace auf der Seite [Release Annotations for Azure Application Insights](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) (Releaseanmerkungen für Azure Application Insights) Ihre Azure DevOps-Organisation und anschließend **Install** (Installieren) aus, um die Erweiterung Ihrer Azure DevOps-Organisation hinzuzufügen.
   
   ![Wählen Sie eine Azure DevOps-Organisation und anschließend „Install“ (Installieren) aus.](./media/annotations/1-install.png)
   
Die Erweiterung muss nur einmal für Ihre Azure DevOps-Organisation installiert werden. Nun können Sie Releaseanmerkungen für ein beliebiges Projekt in Ihrer Organisation konfigurieren.

### <a name="configure-release-annotations-using-api-keys"></a>Konfigurieren von Releaseanmerkungen mit API-Schlüsseln

Erstellen Sie für Ihre Azure Pipelines-Versionsvorlagen jeweils einen separaten API-Schlüssel.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie die Application Insights-Ressource, die Ihre Anwendung überwacht. Sollten Sie über keine solche Ressource verfügen, [erstellen Sie eine neue Application Insights-Ressource](create-workspace-resource.md).
   
1. Öffnen Sie die Registerkarte **API-Zugriff**, und kopieren Sie die **Application Insights-ID**.
   
   ![Kopieren Sie die Anwendungs-ID unter „API-Zugriff“.](./media/annotations/2-app-id.png)

1. Öffnen oder erstellen Sie in einem separaten Browserfenster die Versionsvorlage, mit der Ihre Azure Pipelines-Bereitstellungen verwaltet werden.
   
1. Wählen Sie **Aufgabe hinzufügen** und anschließend im Menü die Aufgabe **Application Insights Release Annotation** (Application Insights-Releaseanmerkung) aus.
   
   ![Wählen Sie „Aufgabe hinzufügen“ und anschließend „Application Insights Release Annotation“ (Application Insights-Releaseanmerkung) aus.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Die Aufgabe „Releaseanmerkung“ unterstützt zurzeit nur Windows-basierte Agents. Sie kann nicht unter Linux, macOS oder anderen Agent-Typen ausgeführt werden.
   
1. Fügen Sie unter **Anwendungs-ID** die Application Insights-ID ein, die Sie auf der Registerkarte **API-Zugriff** kopiert haben.
   
   ![Application Insights-ID einfügen](./media/annotations/4-paste-app-id.png)
   
1. Wählen Sie im Application Insights-Fenster **API-Zugriff** die Option **API-Schlüssel erstellen** aus. 
   
   ![Wählen Sie auf der Registerkarte „API-Zugriff“ die Option „API-Schlüssel erstellen“ aus.](./media/annotations/5-create-api-key.png)
   
1. Geben Sie im Fenster **API-Schlüssel erstellen** eine Beschreibung ein, wählen Sie **Anmerkungen schreiben** aus, und wählen Sie anschließend **Schlüssel generieren** aus. Kopieren Sie den neuen Schlüssel.
   
   ![Geben Sie im Fenster „API-Schlüssel erstellen“ eine Beschreibung ein, wählen Sie „Anmerkungen schreiben“ aus, und wählen Sie anschließend „Schlüssel generieren“ aus.](./media/annotations/6-create-api-key.png)
   
1. Wählen Sie im Fenster für die Versionsvorlage auf der Registerkarte **Variablen** die Option **Hinzufügen** aus, um eine Variablendefinition für den neuen API-Schlüssel zu erstellen.

1. Geben Sie unter **Name** den Namen `ApiKey` ein, und fügen Sie unter **Wert** den API-Schlüssel ein, den Sie auf der Registerkarte **API-Zugriff** kopiert haben.
   
   ![Wählen Sie auf der Azure DevOps-Registerkarte „Variablen“ die Option „Hinzufügen“ aus, nennen Sie die Variable „ApiKey“, und fügen Sie unter „Wert“ den API-Schlüssel ein.](./media/annotations/7-paste-api-key.png)
   
1. Wählen Sie im Hauptfenster der Versionsvorlage die Option **Speichern** aus, um die Vorlage zu speichern.


   > [!NOTE]
   > Einschränkungen für API-Schlüssel werden in der [Dokumentation zur REST-API-Ratenbegrenzungen](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits) beschrieben.

### <a name="transition-to-the-new-release-annotation"></a>Übergang zur neuen Releaseanmerkung

So verwenden Sie die neuen Releaseanmerkungen: 
1. [Entfernen Sie die Erweiterung für Releaseanmerkungen](/azure/devops/marketplace/uninstall-disable-extensions).
1. Entfernen Sie den Application Insights-Task für Releaseanmerkungen in Ihrer Azure Pipelines-Bereitstellung. 
1. Erstellen Sie neue Releaseanmerkungen mit [Azure Pipelines](#release-annotations-with-azure-pipelines-build) oder [Azure CLI](#create-release-annotations-with-azure-cli).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Arbeitselements](./diagnostic-search.md#create-work-item)
* [Automation mit PowerShell](./powershell.md)
