---
title: Best Practices für die Bereitstellung
description: Erfahren Sie mehr über die Schlüsselmechanismen bei der Bereitstellung in Azure App Service. Hier finden Sie sprachspezifische Empfehlungen sowie Informationen zu Einschränkungen.
keywords: Azure App Service, Web-App, bereitstellen, Bereitstellung, Pipelines, Build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 06728431db78d14be1a67f1dde753a0992b5dbba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471115"
---
# <a name="deployment-best-practices"></a>Bewährte Methoden der Bereitstellung

Jedes Entwicklungsteam verfügt über spezifische Anforderungen, die die Implementierung einer effizienten Bereitstellungspipeline in einem beliebigen Clouddienst erschweren können. In diesem Artikel werden die drei Hauptkomponenten der Bereitstellung in App Service vorgestellt: Bereitstellungsquellen, Buildpipelines und Bereitstellungsmechanismen. Dieser Artikel behandelt außerdem einige bewährte Methoden und Tipps für bestimmte Sprachenstapel.

## <a name="deployment-components"></a>Bereitstellungskomponenten

### <a name="deployment-source"></a>Bereitstellungsquelle

Eine Bereitstellungsquelle ist der Speicherort Ihres Anwendungscodes. Bei Produktions-Apps handelt es sich bei der Bereitstellungsquelle normalerweise um ein Repository, das von Software zur Versionskontrolle wie [GitHub, BitBucket oder Azure Repos](deploy-continuous-deployment.md) gehostet wird. Bei Entwicklungs- und Testszenarien kann die Bereitstellungsquelle [ein Projekt auf Ihrem lokalen Computer](deploy-local-git.md) sein. App Service unterstützt auch [OneDrive- und Dropbox-Ordner](deploy-content-sync.md) als Bereitstellungsquellen. Cloudordner können Ihnen die ersten Schritte mit App Service zwar erleichtern, es wird jedoch in der Regel nicht empfohlen, diese Quelle für Produktionsanwendungen auf Unternehmensebene zu verwenden. 

### <a name="build-pipeline"></a>Buildpipeline

Sobald Sie sich für eine Bereitstellungsquelle entschieden haben, besteht Ihr nächster Schritt in der Auswahl einer Buildpipeline. Eine Buildpipeline liest ihren Quellcode aus der Bereitstellungsquelle und führt eine Reihe von Schritten aus (z. B. das Kompilieren von Code, das Minimieren von HTML und JavaScript, das Ausführen von Tests und das Packen von Komponenten), um die Anwendung in einen ausführbaren Zustand zu bringen. Die von der Buildpipeline ausgeführten spezifischen Befehle hängen von Ihrem Sprachenstapel ab. Diese Vorgänge können auf einem Buildserver, z. B. Azure Pipelines, oder lokal ausgeführt werden.

### <a name="deployment-mechanism"></a>Bereitstellungsmechanismus

Der Bereitstellungsmechanismus ist die Aktion, mit der Ihre erstellte Anwendung im Verzeichnis */home/site/wwwroot* Ihrer Web-App abgelegt wird. Das Verzeichnis */wwwroot* ist ein eingebundener Speicherort, der von allen Instanzen Ihrer Web-App gemeinsam verwendet wird. Wenn der Bereitstellungsmechanismus Ihre Anwendung in diesem Verzeichnis ablegt, erhalten Ihre Instanzen eine Benachrichtigung, um sich mit den neuen Dateien zu synchronisieren. App Service unterstützt die folgenden Bereitstellungsmechanismen:

- Kudu-Endpunkte: [Kudu](https://github.com/projectkudu/kudu/wiki) ist das Open Source-Produktivitätstool für Entwickler, das in Windows App Service als gesonderter Prozess ausgeführt wird und in Linux App Service als zweiter Container. Kudu verarbeitet fortlaufende Bereitstellungen und bietet HTTP-Endpunkte für die Bereitstellung, z. B. zipdeploy.
- FTP und WebDeploy: Unter Verwendung Ihrer [Website- oder Benutzeranmeldeinformationen](deploy-configure-credentials.md) können Sie Dateien [über FTP](deploy-ftp.md) oder WebDeploy hochladen. Diese Mechanismen durchlaufen nicht Kudu.  

Bereitstellungstools wie Azure Pipelines, Jenkins und Editor-Plug-Ins verwenden einen dieser Bereitstellungsmechanismen.

## <a name="use-deployment-slots"></a>Verwenden von Bereitstellungsslots

Verwenden Sie nach Möglichkeit immer [Bereitstellungsslots](deploy-staging-slots.md), wenn Sie einen neuen Produktionsbuild bereitstellen. Wenn Sie den App Service-Plantarif „Standard“ oder besser verwenden, können Sie Ihre App in einer Stagingumgebung bereitstellen, Ihre Änderungen überprüfen und Buildüberprüfungstests durchführen. Wenn Sie bereit dazu sind, können Sie Ihre Staging- und Produktionsslots tauschen. Durch den Tauschvorgang werden die erforderlichen Workerinstanzen kontrolliert auf Ihr Produktionsniveau gebracht, wodurch Ausfallzeiten beseitigt werden.

### <a name="continuously-deploy-code"></a>Kontinuierliches Bereitstellen von Code

Wenn Ihr Projekt designierte Branches für Tests, QA und Staging hat, sollte jeder dieser Branches kontinuierlich in einem Stagingslot bereitgestellt werden. (Dies wird als [Gitflow-Design](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) bezeichnet.) Dies ermöglicht es den Projektbeteiligten, den bereitgestellten Branch einfach zu bewerten und zu testen. 

Continuous Deployment sollte nie für Ihren Produktionsslot aktiviert werden. Stattdessen sollte Ihr Produktionsbranch (häufig der Hauptbranch) in einem Nicht-Produktionsslot bereitgestellt werden. Wenn Sie bereit sind, den Basisbranch freizugeben, tauschen Sie ihn in den Produktionsslot. Der Austausch in die Produktion – anstatt in der Produktion bereitzustellen – verhindert Ausfallzeiten und ermöglicht es Ihnen, ein Rollback der Änderungen auszuführen, indem Sie einen erneuten Austausch vornehmen. 

![Diagramm: Flow zwischen Entwicklungs-, Staging- und Hauptbranches und Slots, in denen diese bereitgestellt sind.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Kontinuierliches Bereitstellen von Containern

Stellen Sie für benutzerdefinierte Container aus Docker oder anderen Containerregistrierungen das Image in einem Stagingslot bereit, und tauschen Sie in die Produktion, um Ausfallzeiten zu vermeiden. Die Automatisierung ist komplexer als die Codebereitstellung, da Sie das Image in eine Containerregistrierung pushen und das Imagetag in der „webapp“ aktualisieren müssen.

Richten Sie für jeden Branch, den Sie in einem Slot bereitstellen möchten, eine Automatisierung ein, um bei jedem Commit in den Branch Folgendes durchzuführen.

1. **Erstellen Sie das Image, und kennzeichnen Sie es**. Kennzeichnen Sie das Image als Teil der Buildpipeline mit der Git-Commit-ID, dem Zeitstempel oder anderen identifizierbaren Informationen. Es ist am besten, nicht das standardmäßige Tag „Latest“ zu verwenden. Andernfalls ist es schwierig nachzuverfolgen, welcher Code aktuell bereitgestellt ist, was das Debuggen wesentlich erschwert.
1. **Pushen Sie das gekennzeichnete Image**. Nachdem das Image erstellt und gekennzeichnet wurde, pusht die Pipeline das Image in unsere Containerregistrierung. Im nächsten Schritt pullt der Bereitstellungsslot das gekennzeichnete Image aus der Containerregistrierung.
1. **Aktualisieren Sie den Bereitstellungsslot mit dem neuen Imagetag**. Wenn diese Eigenschaft aktualisiert wird, wird die Site automatisch neu gestartet und pullt das neue Containerimage.

![Visualisierung der Slotverwendung](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Im Folgenden finden Sie Beispiele für gängige Automation-Frameworks.

### <a name="use-azure-devops"></a>Verwenden von Azure DevOps

App Service verfügt über [integrierte Continuous Delivery](deploy-continuous-deployment.md) für Container über das Bereitstellungscenter. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu ihrer App, und wählen Sie unter **Bereitstellung** die Option **Bereitstellungscenter** aus. Befolgen Sie die Anweisungen, um Ihr Repository und den Branch auszuwählen. Dadurch wird eine DevOps-Build- und -Releasepipeline so konfiguriert, dass Ihr Container automatisch erstellt, gekennzeichnet und bereitgestellt wird, wenn neue Commits in den ausgewählten Branch gepusht werden.

### <a name="use-github-actions"></a>Verwenden von GitHub Actions

Sie können Ihre Containerbereitstellung auch [mit GitHub Actions](./deploy-ci-cd-custom-container.md) automatisieren.  Die folgende Workflowdatei erstellt und kennzeichnet den Container mit der Commit-ID, pusht ihn in eine Containerregistrierung und aktualisiert den angegebenen Siteslot mit dem neuen Imagetag.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Verwenden anderer Automatisierungsanbieter

Die oben aufgeführten Schritte gelten für andere Automatisierungshilfsprogramme wie CircleCI oder Travis CI. Im letzten Schritt müssen Sie allerdings Azure CLI verwenden, um die Bereitstellungsslots mit neuen Imagetags zu aktualisieren. Generieren Sie mithilfe des folgenden Befehls einen Dienstprinzipal, um Azure CLI in Ihrem Automatisierungsskript zu verwenden.

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Melden Sie sich in Ihrem Skript mit `az login --service-principal`an, und geben Sie dabei die Informationen des Prinzipals an. Anschließend können Sie `az webapp config container set` verwenden, um den Containernamen, das Tag, die Registrierungs-URL und das Registrierungskennwort festzulegen. Im Folgenden finden Sie einige hilfreiche Links, mit denen Sie Ihren Container-CI-Prozess erstellen können.

- [Anmelden bei Azure CLI in Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Sprachspezifische Erwägungen

### <a name="java"></a>Java

Verwenden Sie die Kudu-API [zipdeploy/](deploy-zip.md) für die Bereitstellung von JAR-Anwendungen und [wardeploy/](deploy-zip.md#deploy-warjarear-packages) für WAR-Apps. Wenn Sie Jenkins verwenden, können Sie diese APIs direkt in der Bereitstellungsphase verwenden. [hier finden Sie weitere Informationen](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli)

### <a name="node"></a>Node

Standardmäßig führt Kudu die Buildschritte für Ihre Node-Anwendung (`npm install`) aus. Wenn Sie einen Builddienst wie Azure DevOps verwenden, ist der Kudu-Build unnötig. Um den Kudu-Build zu deaktivieren, erstellen Sie eine App-Einstellung `SCM_DO_BUILD_DURING_DEPLOYMENT` mit dem Wert `false`.

### <a name="net"></a>.NET 

Standardmäßig führt Kudu die Buildschritte für Ihre .NET-Anwendung (`dotnet build`) aus. Wenn Sie einen Builddienst wie Azure DevOps verwenden, ist der Kudu-Build unnötig. Um den Kudu-Build zu deaktivieren, erstellen Sie eine App-Einstellung `SCM_DO_BUILD_DURING_DEPLOYMENT` mit dem Wert `false`.

## <a name="other-deployment-considerations"></a>Sonstige Überlegungen zur Bereitstellung

### <a name="local-cache"></a>Lokaler Cache

Azure App Service-Inhalt wird in Azure Storage gespeichert und dauerhaft als Inhaltsfreigabe bereitgestellt. Manche Apps benötigen jedoch lediglich einen hochleistungsfähigen, schreibgeschützten Inhaltsspeicher, aus dem sie mit Hochverfügbarkeit ausgeführt werden können. Diese Apps können von der Verwendung eines [lokalen Caches](overview-local-cache.md) profitieren. Der lokale Cache wird für Content Management-Sites wie WordPress nicht empfohlen.

Verwenden Sie immer einen lokalen Cache zusammen mit [Bereitstellungsslots](deploy-staging-slots.md), um Ausfallzeiten zu verhindern. Informationen zur Verwendung dieser Funktionen in Kombination finden Sie in [diesem Abschnitt](overview-local-cache.md#best-practices-for-using-app-service-local-cache).

### <a name="high-cpu-or-memory"></a>Hohe CPU- oder Arbeitsspeicherauslastung

Wenn Ihr App Service-Plan mehr als 90 % der verfügbaren CPU oder des Arbeitsspeichers verwendet, hat der zugrunde liegende virtuelle Computer möglicherweise Probleme beim Verarbeiten Ihrer Bereitstellung. Wenn dies geschieht, skalieren Sie die Anzahl Ihrer Instanzen vorübergehend hoch, um die Bereitstellung auszuführen. Nachdem die Bereitstellung abgeschlossen ist, können Sie die Anzahl der Instanzen auf den vorherigen Wert zurücksetzen.

Unter [Übersicht über die Azure App Service-Diagnose](./overview-diagnostics.md) finden Sie spezifische bewährte Methoden für Ihre Ressource.

- Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Web-App.
- Klicken Sie im linken Navigationsbereich auf **Diagnose und Problembehandlung**, um die App Service-Diagnose zu öffnen.
- Wählen Sie auf der Startseite die Kachel **Bewährte Methoden** aus.
- Klicken Sie auf **Best Practices for Availability & Performance** (Bewährte Methoden für Verfügbarkeit und Leistung) oder **Best Practices for Optimal Configuration** (Bewährte Methoden für die optimale Konfiguration), um den aktuellen Zustand Ihrer App in Bezug auf diese bewährten Methoden anzuzeigen.

Sie können auch den folgenden Link nutzen, um die App Service-Diagnose für Ihre Ressource direkt zu öffnen: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.

## <a name="more-resources"></a>Weitere Ressourcen

[Referenz zu Umgebungsvariablen und App-Einstellungen](reference-app-settings.md)
