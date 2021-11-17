---
title: Continuous Deployment für containerisierte Anwendungen mit Azure DevOps
description: 'Tutorial: Continuous Deployment für containerisierte Anwendungen mit Azure DevOps'
services: ''
author: rahug1190
manager: bsiva
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: rahugup
ms.openlocfilehash: 7d23b7c84cc7a7053bfa80b3f6e1b24c3bd4470c
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000646"
---
# <a name="continuous-deployment-for-containerized-applications-with-azure-devops"></a>Continuous Deployment für containerisierte Anwendungen mit Azure DevOps

In dieser Schritt-für-Schritt-Anleitung erfahren Sie, wie Sie eine Pipeline erstellen, die Ihre containerisierten Apps kontinuierlich für Day-2-Vorgänge mit Azure DevOps erstellt und bereitstellt. Sobald Sie Ihren Code in einem Repository ändern, das ein Dockerfile enthält, werden die Images an Ihre Azure Container Registry-Instanz gepusht und die Manifeste entweder in Azure Kubernetes Service oder in Azure App Service bereitgestellt.

Azure DevOps ermöglicht das Hosten, Erstellen, Planen und Testen Ihres Codes mit ergänzenden Workflows. Wenn Sie Azure Pipelines als einen dieser Workflows verwenden, können Sie Ihre Anwendung mit CI/CD für jede beliebige Plattform und Cloud bereitstellen. Eine Pipeline wird als YAML-Datei im Stammverzeichnis Ihres Repositorys definiert.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

-  Containerisieren und Bereitstellen Ihrer ASP.NET- oder Java-Web-App mittels „Azure Migrate: App-Containerisierung“
-  Ein GitHub-Konto, in dem Sie ein Repository erstellen können. Wenn Sie noch keines besitzen, können Sie es [kostenlos erstellen](https://github.com/).
-  Eine Azure DevOps-Organisation. Wenn Sie noch keines besitzen, können Sie es [kostenlos erstellen](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). (Eine Azure DevOps-Organisation unterscheidet sich von Ihrer GitHub-Organisation. Sie können Ihrer DevOps-Organisation und Ihrer GitHub-Organisation den gleichen Namen geben, wenn Sie sie aufeinander abstimmen möchten.) <br/> Wenn Ihr Team bereits über eine solche Organisation verfügt, stellen Sie sicher, dass Sie Administrator des Azure DevOps-Projekts sind, das Sie verwenden möchten.
-  Möglichkeit zum Ausführen von Pipelines auf von Microsoft gehosteten Agents. Sie können entweder einen [Parallelauftrag](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops) erwerben oder einen Free-Tarif anfordern. Eine Anleitung zum Anfordern eines Free-Tarifs finden Sie in [diesem Artikel](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops). Beachten Sie, dass es zwei bis drei Werktage dauern kann, bis der Free-Tarif gewährt wird.


## <a name="locate-the-artifacts"></a>Suchen nach den Artefakten

Das Tool „Azure Migrate: App-Containerisierung“ generiert automatisch Artefakte, die verwendet werden können, um mithilfe von Azure Pipelines einen CI/CD-Workflow für Ihre Anwendung zu konfigurieren. Die Artefakte werden generiert, sobald die Anwendungsbereitstellung über das Tool abgeschlossen ist. Sie finden die Artefakte wie folgt: 

1. Wechseln Sie zu dem Computer, auf dem das Tool „Azure Migrate: App-Containerisierung“ ausgeführt wird. 
2. Navigieren Sie zum Verzeichnis **C:\ProgramData\Microsoft Azure Migrate App Containerization**. Sollten Sie nicht zu „C:\ProgramData“ navigieren können, aktivieren Sie im Datei-Explorer unter *Ansicht* die Option *Ausgeblendete Elemente*. 
3. Wählen Sie das Verzeichnis aus, das der **IP-Adresse bzw. dem FQDN des Quellcomputers** entspricht. Der Quellcomputer ist der im App-Containerisierungstool angegebene Computer, auf dem die containerisierte Anwendung ausgeführt wird.
4. Gehen Sie bei Java-Anwendungen wie folgt vor: 
    - Navigieren Sie zu **JavaTomcatWebApp\Artifacts**.    
    - Navigieren Sie zum Verzeichnis **Catalina\localhost**. Sollten Sie dieses Verzeichnis nicht finden, navigieren Sie zu dem Verzeichnis, das dem Namen der Tomcat-Engine und dem Hostnamen entspricht.
    - Suchen Sie in diesem Verzeichnis nach dem Anwendungsordner. 
5. Gehen Sie bei ASP.NET-Anwendungen wie folgt vor:
    - Navigieren Sie zu **IISAspNetWebApp**.
    - Suchen Sie in diesem Verzeichnis nach dem Anwendungsordner.


## <a name="upload-artifacts-to-github"></a>Hochladen von Artefakten in GitHub

Sie müssen die Artefakte in ein Quellrepository hochladen, das später mit Azure DevOps verwendet wird. 

1. Melden Sie sich bei Ihrem GitHub-Konto an. 
2. Erstellen Sie ein neues Repository, wie in [diesem Artikel](https://docs.github.com/get-started/quickstart/create-a-repo) beschrieben. 
3. Der nächste Schritt besteht darin, die folgenden Artefakte in dieses Repository hochzuladen:
   -  Wählen Sie für Java-Apps die folgenden Ordner und Dateien aus (im Anwendungsordner auf dem Computer, auf dem das App-Containerisierungstool ausgeführt wird):
        - Ordner „MandatoryArtifacts“
        - Ordner „manifests“
        - Ordner „OptionalArtifacts“
        - Dockerfile 
        - Datei „Entryscript.sh“
        - Datei „azure-pipelines.yml“
    - Wählen Sie für ASP.NET-Apps die folgenden Ordner und Dateien aus (im Anwendungsordner auf dem Computer, auf dem das App-Containerisierungstool ausgeführt wird):
        - Ordner „manifests“
        - Ordner „Build“
        - Datei „azure-pipelines.yml“

## <a name="sign-in-to-azure-pipelines"></a>Anmelden bei Azure Pipelines

Melden Sie sich bei [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines) an. Nach der Anmeldung wechselt Ihr Browser zu https://dev.azure.com/my-organization-name und zeigt Ihr Azure DevOps-Dashboard an.

Erstellen Sie in Ihrer ausgewählten Organisation ein *Projekt*. Sollten in Ihrer Organisation noch keine Projekte vorhanden sein, wird der Bildschirm **Erstellen Sie als ersten Schritt ein Projekt.** angezeigt. Wählen Sie andernfalls in der rechten oberen Ecke des Dashboards die Schaltfläche **Projekt erstellen** aus.

## <a name="add-service-connections"></a>Hinzufügen von Dienstverbindungen

Vor der Pipelineerstellung müssen Sie zunächst Ihre Dienstverbindungen erstellen, da Sie in der Vorlage zum Auswählen und Überprüfen Ihrer Verbindungen aufgefordert werden. Eine Dienstverbindung ermöglicht es Ihnen, eine Verbindung mit Ihrer Azure Container Registry-Instanz herzustellen, wenn Sie die Aufgabenvorlagen verwenden, sowie mit dem Azure-Abonnement, in dem Sie die Anwendung bereitstellen möchten. 

1. Wählen Sie in der linken unteren Ecke **Projekteinstellungen > Dienstverbindungen** aus.
2. Wählen Sie **Neue Dienstverbindung** und dann die Option **Docker-Registrierung > Azure Container Registry** für die Art von Dienstverbindung aus, die Sie benötigen. Wählen Sie anschließend „Weiter“ aus.
3. Wählen Sie eine Authentifizierungsmethode und anschließend „Weiter“ aus.
4. Geben Sie die Parameter für die Dienstverbindung ein. Die Liste der Parameter unterscheidet sich je nach Dienstverbindungstyp. Weitere Informationen finden Sie in der [Liste der Dienstverbindungstypen und zugeordneten Parameter](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#common-service-connection-types).
5. Wählen Sie **Speichern** aus, um das die Verbindung zu erstellen.
6. **Überprüfen** Sie die Verbindung, nachdem sie erstellt wurde und die Parameter eingegeben wurden. Der Überprüfungslink sendet einen REST-Aufruf mit den eingegebenen Informationen an den externen Dienst und gibt an, ob der Aufruf erfolgreich war.
7. Erstellen Sie mithilfe der gleichen Schritte eine Dienstverbindung mit Ihrem Azure-Abonnement, indem Sie **Neue Dienstverbindung > Azure Resource Manager** auswählen. 
8. Notieren Sie sich die Ressourcen-ID für beide Dienstverbindungen. 

## <a name="create-the-pipeline"></a>Erstellen der Pipeline

Nachdem Sie nun beide Dienstverbindungen erstellt haben, können Sie Ihre Pipeline konfigurieren. Die YAML-Datei für die Pipeline wurde automatisch vom App-Containerisierungstool erstellt und kann wie folgt konfiguriert werden:  

1. Navigieren Sie zu „Pipelines“, und wählen Sie **Neue Pipeline** aus.
2. Führen Sie die Schritte des Assistenten aus. Wählen Sie zuerst **GitHub** als Speicherort Ihres Quellcodes aus.
3. Möglicherweise werden Sie zu GitHub weitergeleitet, um sich anzumelden. Geben Sie in diesem Fall Ihre Anmeldeinformationen für GitHub ein.
4. Wenn die Liste der Repositorys angezeigt wird, wählen Sie Ihr Repository aus.
5. Sie werden möglicherweise zu GitHub weitergeleitet, um die Azure Pipelines-App zu installieren. Wählen Sie in diesem Fall „Approve & Install“ (Genehmigen und installieren) aus.
6. Wenn Ihre neue Pipeline angezeigt wird, überprüfen Sie anhand der YAML-Datei, was sie bewirkt.
7. Geben Sie in der YAML-Datei die Ressourcen-ID für die Azure Container Registry-Dienstverbindung als Wert für die Variable **dockerRegistryServiceConnection** an.
8. Geben Sie die Ressourcen-ID für die Azure Resource Manager-Dienstverbindung als Wert für die Variable **dockerRegistryServiceConnection** an.
9. Wenn Sie so weit sind, wählen Sie **Speichern** aus, um die neue Pipeline in Ihrem Repository zu committen. 

Ihre Pipeline ist nun für die Erstellung und Bereitstellung Ihrer containerisierten Apps für Day-2-Vorgänge eingerichtet. Sie können Ihre [Pipeline anpassen](/azure/devops/pipelines/customize-pipeline?view=azure-devops#prerequisite), um die Anforderungen Ihrer Organisation zu erfüllen. 