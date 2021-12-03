---
title: Automatisieren von Anwendungsbereitstellungen für Azure Spring Cloud
description: Beschreibt die Verwendung des Azure Spring Cloud-Tasks für Azure Pipelines.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 4f1075a690c806be76b0102c2954a4f5127dabee
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258333"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Automatisieren von Anwendungsbereitstellungen für Azure Spring Cloud

In diesem Artikel erfahren Sie, wie Sie den [Azure Spring Cloud-Task für Azure Pipelines](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud) zum Bereitstellen von Anwendungen verwenden.

Continuous Integration- und Continuous Delivery-Tools (CI/CD) ermöglichen Ihnen die schnelle Bereitstellung von Updates für vorhandene Anwendungen mit minimalem Aufwand und Risiko. Mit Azure DevOps können Sie diese wichtigen Aufträge organisieren und steuern. 

Im folgenden Video wird die End-to-End-Automatisierung mit Tools Ihrer Wahl beschrieben, einschließlich Azure Pipelines.

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

::: zone pivot="programming-language-csharp"

## <a name="create-an-azure-resource-manager-service-connection"></a>Erstellen einer Azure Resource Manager-Dienstverbindung

Erstellen Sie zunächst eine Azure Resource Manager-Dienstverbindung mit Ihrem Azure DevOps-Projekt. Anweisungen finden Sie unter [Herstellen einer Verbindung mit Microsoft Azure](/azure/devops/pipelines/library/connect-to-azure). Achten Sie darauf, dasselbe Abonnement auszuwählen, das Sie für Ihre Azure Spring Cloud-Dienstinstanz verwenden.

## <a name="build-and-deploy-apps"></a>Erstellen und Bereitstellen von Apps

Sie können jetzt Ihre Projekte mit einer Reihe von Tasks erstellen und bereitstellen. Die folgende Azure Pipelines-Vorlage definiert Variablen, einen .NET Core-Task zum Erstellen der Anwendung und einen Azure Spring Cloud-Task zur Bereitstellung der Anwendung.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Deploy the planet app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(planetAppName)/publish-deploy-planet.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(planetMainEntry)

# Deploy the solar app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(solarAppName)/publish-deploy-solar.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(solarMainEntry)
```

::: zone-end
::: zone pivot="programming-language-java"

## <a name="set-up-an-azure-spring-cloud-instance-and-an-azure-devops-project"></a>Einrichten einer Azure Spring Cloud-Instanz und eines Azure DevOps-Projekts

Verwenden Sie zunächst die folgenden Schritte, um eine vorhandene Azure Spring Cloud-Instanz für die Verwendung mit Azure DevOps einzurichten.

1. Wechseln Sie zu Ihrer Azure Spring Cloud-Instanz, und erstellen Sie dann eine neue App. 
1. Wechseln Sie zum Azure DevOps-Portal, und erstellen Sie dann ein neues Projekt unter Ihrer ausgewählten Organisation. Falls Sie keine Azure DevOps-Organisation besitzen, können Sie kostenlos eine erstellen.
1. Wählen Sie **Repos** (Repositorys) aus, und importieren Sie dann den [Spring Boot-Democode](https://github.com/spring-guides/gs-spring-boot) in das Repository.

## <a name="create-an-azure-resource-manager-service-connection"></a>Erstellen einer Azure Resource Manager-Dienstverbindung

Erstellen Sie als nächstes eine Azure Resource Manager-Dienstverbindung mit Ihrem Azure DevOps-Projekt. Anweisungen finden Sie unter [Herstellen einer Verbindung mit Microsoft Azure](/azure/devops/pipelines/library/connect-to-azure). Achten Sie darauf, dasselbe Abonnement auszuwählen, das Sie für Ihre Azure Spring Cloud-Dienstinstanz verwenden.

## <a name="build-and-deploy-apps"></a>Erstellen und Bereitstellen von Apps

Sie können jetzt Ihre Projekte mit einer Reihe von Tasks erstellen und bereitstellen. In den folgenden Abschnitten werden verschiedene Optionen für die Bereitstellung Ihrer App mit Azure DevOps erläutert.

### <a name="deploy-using-a-pipeline"></a>Bereitstellen mithilfe einer Pipeline

Gehen Sie wie folgt vor, um die Bereitstellung mithilfe einer Pipeline vorzunehmen:

1. Wählen Sie **Pipelines** aus, und erstellen Sie dann eine neue Pipeline mit einer Maven-Vorlage.
1. Bearbeiten Sie die Datei *azure-pipelines.yml*, um das Feld `mavenPomFile` auf *'complete/pom.xml'* festzulegen.
1. Wählen Sie auf der rechten Seite **Assistent anzeigen** und dann die **Azure Spring Cloud**-Vorlage aus.
1. Wählen Sie die Dienstverbindung, die Sie für Ihr Azure-Abonnement erstellt haben, und dann Ihre Spring Cloud-Instanz sowie App-Instanz aus. 
1. Deaktivieren Sie **Use Staging Deployment** (Stagingbereitstellung verwenden).
1. Legen Sie **Paket oder Ordner** auf *complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar* fest.
1. Wählen Sie **Hinzufügen** aus, um diesen Task Ihrer Pipeline hinzuzufügen.
  
   Ihre Pipelineeinstellungen sollten der folgenden Abbildung entsprechen.

   :::image type="content" source="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg" alt-text="Screenshot: Pipelineeinstellungen" lightbox="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg":::

   Sie können Ihre Projekte auch mithilfe der folgenden Pipelinevorlage erstellen und bereitstellen. In diesem Beispiel wird zunächst ein Maven-Task zum Erstellen der Anwendung definiert, gefolgt von einem zweiten Task, der die JAR-Datei mithilfe des Azure Spring Cloud-Tasks für Azure Pipelines bereitstellt.

   ```yaml
   steps:
   - task: Maven@3
     inputs:
       mavenPomFile: 'complete/pom.xml'
   - task: AzureSpringCloud@0
     inputs:
       azureSubscription: '<your service connection name>'
       Action: 'Deploy'
       AzureSpringCloud: <your Azure Spring Cloud service>
       AppName: <app-name>
       UseStagingDeployment: false
       DeploymentName: 'default'
       Package: ./target/your-result-jar.jar
   ```

3. Wählen Sie **Speichern und ausführen** aus, und warten Sie, bis der Auftrag abgeschlossen ist.

### <a name="blue-green-deployments"></a>Blaugrün-Bereitstellungen

Die im vorherigen Abschnitt gezeigte Bereitstellung empfängt bei der Bereitstellung sofort Anwendungdatenverkehr. Dadurch können Sie die Anwendung in der Produktionsumgebung testen, bevor sie Kundendatenverkehr empfängt.

#### <a name="edit-the-pipeline-file"></a>Bearbeiten der Pipelinedatei

Verwenden Sie die folgende Vorlage, um die Anwendung auf dieselbe Weise wie zuvor zu erstellen und in einer Stagingbereitstellung bereitzustellen. In diesem Beispiel muss die Stagingbereitstellung bereits vorhanden sein. Einen alternativen Ansatz finden Sie unter [Strategien für Blau-Grün-Bereitstellungen](concepts-blue-green-deployment-strategies.md).

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
    Package: ./target/your-result-jar.jar
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Set Production'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
```

#### <a name="use-the-releases-section"></a>Verwenden des Abschnitts „Releases“

Die folgenden Schritte zeigen Ihnen, wie Sie eine Blau-Grün-Bereitstellung im Abschnitt **Releases** aktivieren.

1. Wählen Sie **Pipelines** aus, und erstellen Sie eine neue Pipeline für Ihren Maven-Build, und veröffentlichen Sie das Artefakt.
   1. Wählen Sie **Azure Repos Git** als Codespeicherort aus.
   1. Wählen Sie ein Repository aus, in dem sich Ihr Code befindet.
   1. Wählen Sie die **Maven**-Vorlage und ändern Sie die Datei, um das Feld `mavenPomFile` auf *`complete/pom.xml`* festzulegen.
   1. Wählen Sie auf der rechten Seite **Assistent anzeigen**, und wählen Sie die Vorlage **Buildartefakte veröffentlichen** aus.
   1. Legen Sie **Path to publish** (Pfad zum Veröffentlichen) auf *complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar* fest.
   1. Klicken Sie auf **Speichern und ausführen**.

1. Wählen Sie **Releases**, dann **Release erstellen** aus.
1. Fügen Sie eine neue Pipeline hinzu, und wählen Sie **Leerer Auftrag** aus, um einen Auftrag zu erstellen.
1. Wählen Sie unter **Stages** (Phasen) die Zeile **1 Auftrag, 0 Task** aus.

   :::image type="content" source="media/spring-cloud-how-to-cicd/create-new-job.jpg" alt-text="Screenshot: Auswählen der Position zum Hinzufügen eines Tasks zu einem Auftrag":::

   1. Wählen Sie **+** aus, um dem Auftrag einen Task hinzuzufügen.
   1. Suchen Sie nach der **Azure Spring Cloud**-Vorlage, und wählen Sie dann **Hinzufügen** aus, um den Task zum Auftrag hinzuzufügen.
   1. Wählen Sie **Azure Spring Cloud Deploy:** (Bereitstellen) aus, um den Task zu bearbeiten.
   1. Füllen Sie diesen Task mit den Informationen zu Ihrer App, und deaktivieren Sie anschließend die Option **Use Staging Deployment** (Stagingbereitstellung verwenden).
   1. Aktivieren Sie **Create a new staging deployment if one does not exist** (Neue Stagingbereitstellung erstellen, wenn keine vorhanden ist), und geben Sie dann einen Namen in **Deployment** (Bereitstellung) ein.
   1. Wählen Sie **Speichern** aus, um diesen Task zu speichern.
   1. Klicken Sie auf **OK**.
1. Wählen Sie **Pipeline** und dann **Artefakt hinzufügen** aus.
   1. Wählen Sie unter **Quelle (Buildpipeline)** die zuvor erstellte Pipeline aus.
   1. Wählen Sie **Hinzufügen** und dann **Speichern** aus.
1. Wählen Sie **1 Auftrag, 1 Task** unter **Stages** (Phasen) aus.
1. Navigieren Sie zum Task **Azure Spring Cloud Deploy** (Azure Spring Cloud bereitstellen) in **Phase 1**, und wählen Sie dann die Auslassungszeichen neben **Paket oder Ordner** aus.
1. Wählen Sie *spring-boot-complete-0.0.1-SNAPSHOT.jar* im Dialogfeld und dann **OK** aus.

   :::image type="content" source="media/spring-cloud-how-to-cicd/change-artifact-path.jpg" alt-text="Screenshot: Dialogfeld zum Auswählen von Datei oder Ordner":::

1. Wählen Sie **+** aus, um dem Auftrag einen weiteren **Azure Spring Cloud**-Task hinzuzufügen.
2. Ändern Sie die Aktion in **Set Production Deployment** (Produktionsbereitstellung festlegen).
3. Wählen Sie **Speichern** und dann **Release erstellen** aus, um die Bereitstellung automatisch zu starten. 

Um den aktuellen Releasestatus Ihrer App zu überprüfen, wählen Sie **Release anzeigen** aus. Nachdem dieser Task abgeschlossen ist, besuchen Sie das Azure-Portal, um Ihren App-Status zu überprüfen.

### <a name="deploy-from-source"></a>Bereitstellen aus der Quelle

Verwenden Sie die folgende Pipelinevorlage, um die Bereitstellung ohne separaten Buildschritt direkt in Azure bereitzustellen.

```yaml
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(Build.SourcesDirectory)
```

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Bereitstellen Ihrer ersten Spring Boot-App in Azure Spring Cloud](./quickstart.md)
