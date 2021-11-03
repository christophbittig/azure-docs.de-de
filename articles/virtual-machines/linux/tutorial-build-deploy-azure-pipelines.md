---
title: CI/CD für Azure-VMs mithilfe von Azure Pipelines
description: Erfahren Sie, wie Sie mithilfe einer YAML-basierten Azure-Pipeline CI- und CD-Vorgänge (Continuous Integration bzw. Continuous Deployment) einer Node.js-App für Azure-VMs einrichten.
ms.service: virtual-machines
author: ushan
tags: azure-devops-pipelines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 1118fb9d41ace11adb55adedc4b3700c3c34e50a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003445"
---
# <a name="deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Bereitstellen der App auf virtuellen Linux-Computern in Azure mithilfe von Azure DevOps Services und Azure Pipelines

**Gilt für:** :heavy_check_mark: Linux-VMs 

Continuous Integration (CI) und Continuous Deployment (CD) bilden eine Pipeline, über die Sie Ihren Code nach jedem Codecommit erstellen, freigeben und bereitstellen können. Das vorliegende Dokument enthält die Schritte, die zum Einrichten einer CI/CD-Pipeline für die Bereitstellung auf mehreren Computern über Azure Pipelines ausgeführt werden müssen.

Azure Pipelines bietet einen umfassenden Satz an CI/CD-Automatisierungstools für Bereitstellungen auf virtuellen Computern – sowohl lokal als auch in einer beliebigen Cloud.

In diesem Tutorial richten Sie eine YAML-basierte CI/CD-Pipeline ein, um Ihre App in einer Azure Pipelines-[Umgebung](/azure/devops/pipelines/process/environments) bereitzustellen. Als Ressourcen verwenden Sie virtuelle Linux-Computer, die jeweils als Webserver zum Ausführen der App dienen.

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Abrufen einer Beispiel-App
> * Erstellen einer YAML-basierte Azure Pipelines-CI-Pipeline zum Erstellen der Beispiel-App
> * Erstellen einer Azure Pipelines-Umgebung für die virtuellen Azure-Computer
> * Erstellen einer Azure Pipelines-CD-Pipeline
> * Ausführen manueller und durch CI ausgelöster Bereitstellungen

## <a name="before-you-begin"></a>Voraussetzungen

* Melden Sie sich bei Ihrer Azure DevOps Services-Organisation an ( **https://dev.azure.com/** ). 
  Sie können eine [kostenlose Azure DevOps-Organisation](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308) erhalten.

  > [!NOTE]
  > Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure DevOps Services](/azure/devops/organizations/projects/connect-to-projects).

*  Sie benötigen einen virtuellen Linux-Computer für ein Bereitstellungsziel.  Weitere Informationen finden Sie unter [Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle](./tutorial-manage-vm.md).

*  Öffnen Sie den eingehenden Port 80 für den virtuellen Computer. Weitere Informationen finden Sie unter [Erstellen von Netzwerksicherheitsgruppen über das Azure-Portal](../../virtual-network/tutorial-filter-network-traffic.md).

## <a name="get-your-sample-app-code"></a>Abrufen des Codes für die Beispiel-App

Wenn Sie bereits über eine App in GitHub verfügen, die Sie bereitstellen möchten, können Sie versuchen, eine Pipeline für den Code dieser App zu erstellen.

Wenn Sie jedoch noch keine Erfahrung mit diesen Vorgängen haben, empfiehlt es sich, unseren Beispielcode zu verwenden. Forken Sie in diesem Fall das folgende Repository in GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic ist eine [Java Spring Boot](https://spring.io/guides/gs/spring-boot)-Anwendung, die mit [Maven](https://spring.io/guides/gs/maven/) erstellt wurde.

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Die Node.js-App wurde über [Yeoman](https://yeoman.io/learning/index.html) erstellt. Sie verwendet Express, Bower und Grunt. Darüber hinaus verfügt sie über einige npm-Pakete als Abhängigkeiten.
> Die Beispiel-App enthält zudem ein Skript, über das Nginx eingerichtet und die App bereitgestellt wird. Es wird auf den virtuellen Computern ausgeführt. Das Skript führt folgende Aktionen aus:
> 1. Es installiert Node, Nginx und PM2.
> 2. Es konfiguriert Nginx und PM2.
> 3. Es startet die Node-App.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Voraussetzungen für die Linux-VM

Die oben genannten Beispiel-Apps wurden unter Ubuntu 16.04 getestet, und es empfiehlt sich, für diese Schnellstartanleitung die gleiche Linux-VM-Version zu verwenden.
Führen Sie die nachfolgend beschriebenen Schritte aus, je nachdem, welcher Runtimestapel für die App verwendet wird.

#### <a name="java"></a>[Java](#tab/java)

- Um Java Spring Boot- und Spring Cloud-basierte Apps bereitzustellen, erstellen Sie eine Linux-VM in Azure. Verwenden Sie dafür [diese](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) Vorlage, die eine vollständig unterstützte, auf OpenJDK basierende Runtime enthält.
- Um Java-Servlets auf einem Tomcat-Server bereitzustellen, erstellen Sie eine Linux-VM mit Java 8. Verwenden Sie dafür [diese](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) Azure-Vorlage, und [konfigurieren Sie Tomcat 9.x als Dienst](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Um eine Java EE-basierte App bereitzustellen, verwenden Sie eine Azure-Vorlage, um eine [Linux-VM mit Java und WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) oder eine [Linux-VM mit Java und WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) oder eine [Linux-VM mit Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) und WildFly/JBoss 14 zu erstellen. 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Zum Installieren einer JavaScript-App oder einer Node.js-App benötigen Sie eine Linux-VM mit einem Nginx-Webserver, um die App bereitzustellen.
Wenn Sie noch nicht über eine Linux-VM mit Nginx verfügen, erstellen Sie jetzt eine, indem Sie die Schritte in [diesem Beispiel](./quick-create-cli.md) ausführen.

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Erstellen einer Azure Pipelines-Umgebung mit virtuellen Azure-Computern

Virtuelle Computer können in [Umgebungen](/azure/devops/pipelines/process/environments) als Ressourcen hinzugefügt und für Bereitstellungen mit mehreren Computern als Ziel verwendet werden. Ansichten des Bereitstellungsverlaufs in der Umgebung ermöglichen die Nachverfolgung von der VM über die Pipeline bis hin zum Commit.

Sie können eine Umgebung im Hub **Umgebungen** im Abschnitt **Pipelines** erstellen.
1.  Melden Sie sich bei Ihrer Azure DevOps-Organisation an, und navigieren Sie zu Ihrem Projekt.
2.  Navigieren Sie in Ihrem Projekt zur Seite **Pipelines**. Wählen Sie **Umgebungen** aus, und klicken Sie auf **Umgebung erstellen**. Legen Sie einen **Namen** für die Umgebung fest (erforderlich), und geben Sie eine **Beschreibung** ein.
3.  Wählen Sie **Virtual Machines** als **Ressource** aus, die zur Umgebung hinzugefügt werden soll, und klicken Sie auf **Weiter**.
4.  Wählen Sie das Betriebssystem aus (Windows/Linux), und **kopieren Sie das PS-Registrierungsskript**. 
5.  Führen Sie das kopierte Skript auf jeder Ziel-VM, die in dieser Umgebung registriert werden soll, an einer PowerShell-Eingabeaufforderung mit Administratorrechten aus.
    > [!NOTE]
    > - Das persönliche Zugriffstoken des angemeldeten Benutzers wird vorab in das Skript eingefügt. Das Token läuft am gleichen Tag ab, sodass das kopierte Skript danach unbrauchbar ist.
    > - Wenn auf der VM bereits ein Agent ausgeführt wird, geben Sie unter „Agent“ einen eindeutigen Namen an, der in der Umgebung registriert wird.
6.  Sobald die VM registriert ist, wird sie auf der Registerkarte „Ressourcen“ der Umgebung als Umgebungsressource angezeigt.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Um weitere VMs hinzuzufügen, können Sie das Skript anzeigen und erneut kopieren, indem Sie auf „Ressource hinzufügen“ klicken und „Virtual Machines“ als Ressource auswählen. Dieses Skript ist für alle VMs gleich, die zu dieser Umgebung hinzugefügt werden sollen. 
8.  Jeder Computer interagiert mit Azure Pipelines, um die Bereitstellung Ihrer App zu koordinieren.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Sie können über das interaktive PowerShell-Registrierungsskript Tags zu Ihrer VM hinzufügen. Sie können Tags auch über die Ressourcenansicht hinzufügen oder entfernen, indem Sie in der Ressourcenansicht auf die drei Punkte neben der entsprechenden VM-Ressource klicken.

   Mit den zugewiesenen Tags können Sie die Bereitstellung auf bestimmte virtuelle Computer beschränken, wenn die Umgebung in einem Bereitstellungsauftrag verwendet wird. Jedes Tag kann maximal 256 Zeichen enthalten, in Bezug auf die Anzahl von Tags, die Sie verwenden können, gibt es keine Obergrenze.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definieren der CI-Buildpipeline

Sie benötigen eine CI-Buildpipeline, die Ihre Webanwendung veröffentlicht, sowie ein Bereitstellungsskript, das lokal auf dem Ubuntu-Server ausgeführt werden kann. Richten Sie eine CI-Buildpipeline basierend auf der Runtime ein, die Sie verwenden möchten. 

1. Melden Sie sich bei Ihrer Azure DevOps-Organisation an, und navigieren Sie zu Ihrem Projekt.

1. Navigieren Sie in Ihrem Projekt zur Seite **Pipelines**. Wählen Sie dann die Aktion zum Erstellen einer neuen Pipeline aus.

1. Führen Sie die Schritte des Assistenten aus. Wählen Sie zuerst **GitHub** als Speicherort Ihres Quellcodes aus.

1. Möglicherweise werden Sie zu GitHub weitergeleitet, um sich anzumelden. Geben Sie in diesem Fall Ihre Anmeldeinformationen für GitHub ein.

1. Wenn die Liste der Repositorys angezeigt wird, wählen Sie das gewünschte Repository mit Beispiel-Apps aus.

1. Azure Pipelines analysiert das Repository und empfiehlt eine geeignete Pipelinevorlage.

#### <a name="java"></a>[Java](#tab/java)

Wählen Sie die Vorlage **starter** aus, und kopieren Sie den folgenden YAML-Codeausschnitt, der Ihr Java-Projekt kompiliert und Tests mit Apache Maven ausführt:

```yaml
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Weitere Anleitungen erhalten Sie in den Schritten im Artikel [Erstellen Ihrer Java-App mit Maven](/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Wählen Sie die Vorlage **starter** aus, und kopieren Sie den folgenden YAML-Codeausschnitt, der ein allgemeines Node.js-Projekt mit npm kompiliert.

```yaml
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Weitere Anleitungen erhalten Sie in den Schritten im Artikel [Erstellen Ihrer Node.js-App mit gulp](/azure/devops/pipelines/ecosystems/javascript).

- Sehen Sie sich die Pipeline an, um herauszufinden, welche Vorgänge sie ausführt. Stellen Sie sicher, dass alle Standardangaben für Ihren Code geeignet sind.

- Klicken Sie auf **Speichern und ausführen**, wählen Sie **Direkten Commit an Mainbranch ausführen** aus, und klicken Sie dann erneut auf **Speichern und ausführen**.

- Eine neue Ausführung wird gestartet. Warten Sie, bis die Ausführung beendet ist.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definieren von CD-Schritten zur Bereitstellung auf der Linux-VM

1. Ändern Sie die YAML-Datei für die oben verwendete Pipeline, und fügen Sie einen [Bereitstellungsauftrag](/azure/devops/pipelines/process/deployment-jobs) ein, indem Sie mit der folgenden YAML-Syntax auf die Umgebung und die VM-Ressourcen verweisen, die Sie zuvor eingerichtet haben:

   ```yaml
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. Sie können bestimmte Gruppen von virtuellen Computern aus der Umgebung auswählen, die die Bereitstellung erhalten sollen. Geben Sie dazu die **Tags** an, die Sie für jeden virtuellen Computer in der Umgebung definiert haben.
[Hier](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) finden Sie das vollständige YAML-Schema für den Bereitstellungsauftrag.

3. Sie können entweder `runOnce` oder `rolling` als Bereitstellungsstrategie angeben. 

   `runOnce` ist die einfachste Bereitstellungsstrategie, bei der alle Lebenszyklushooks, also `preDeploy` `deploy`, `routeTraffic` und `postRouteTraffic`, einmal ausgeführt werden. Danach wird entweder `on:` `success` oder `on:` `failure` ausgeführt.

   Im Folgenden finden Sie den YAML-Beispielcodeausschnitt für `runOnce`:
   ```yaml
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Im Folgenden finden Sie ein Beispiel des YAML-Codeausschnitts, den Sie verwenden können, um eine Strategie für die parallele Bereitstellung von VM-Updates für bis zu fünf Ziele bei jeder Iteration zu definieren. `maxParallel` bestimmt die Anzahl von Zielen, auf denen eine parallele Bereitstellung erfolgen kann. Die Auswahl berücksichtigt die absolute Anzahl oder einen Prozentsatz der Ziele, die zu jedem Zeitpunkt verfügbar bleiben müssen, ausgenommen die Ziele, auf denen die Bereitstellung erfolgt. Hiermit werden auch die Erfolgs- und Fehlerbedingungen während der Bereitstellung bestimmt.

   ```yaml
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Bei jeder Ausführung dieses Auftrags wird ein Bereitstellungsverlauf für die Umgebung „`<environment name>`“ erfasst, in der Sie die VMs erstellt und registriert haben.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Ausführen der Pipeline und Abrufen von Ansichten zur Nachverfolgbarkeit in der Umgebung
Eine Bereitstellungsansicht der Umgebung ermöglicht die vollständige Nachverfolgbarkeit von Commits und Arbeitselementen und bietet einen pipelineübergreifenden Bereitstellungsverlauf für jede Umgebung bzw. Ressource.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Nächste Schritte
- Jetzt können Sie die [Pipeline anpassen](/azure/devops/pipelines/customize-pipeline), die Sie gerade erstellt haben.
- Informationen zu weiteren Möglichkeiten in YAML-Pipelines finden Sie in der [YAML-Schemareferenz](/azure/devops/pipelines/yaml-schema).
- Im nächsten Tutorial erfahren Sie, wie Sie einen LAMP-Stapel (Linux, Apache, MySQL und PHP) bereitstellen.

> [!div class="nextstepaction"]
> [Bereitstellen des LAMP-Stapels](tutorial-lamp-stack.md)
