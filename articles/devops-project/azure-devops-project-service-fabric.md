---
title: 'Tutorial: Bereitstellen einer ASP.NET Core-App in Azure Service Fabric mithilfe von Azure DevOps Starter'
description: Azure DevOps Starter erleichtert die ersten Schritte mit Azure. Mit Azure DevOps Projects können Sie in wenigen Schritten Ihre ASP.NET Core-App in Azure Service Fabric bereitstellen.
ms.author: gwallace
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.openlocfilehash: fc2a86e9bf8105851f4fb9e5cfe444cd44f850d5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061549"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>Tutorial: Bereitstellen einer ASP.NET Core-App in Azure Service Fabric mithilfe von Azure DevOps Starter

Azure DevOps Starter bietet eine vereinfachte Umgebung, in der Sie Ihren vorhandenen Code und Ihr Git-Repository verwenden oder eine Beispielanwendung auswählen können, um eine Continuous Integration- und Continuous Delivery-Pipeline (CI/CD) für Azure zu erstellen. 

DevOps Starter ermöglicht außerdem:

* Automatisches Erstellen von Azure-Ressourcen wie Azure Service Fabric
* Erstellen und Konfigurieren einer Releasepipeline in Azure DevOps, mit der eine CI/CD-Pipeline eingerichtet wird
* Erstellen einer Azure Application Insights-Ressource für die Überwachung

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer ASP.NET Core-App und Bereitstellen dieser App in Service Fabric mithilfe von DevOps Starter
> * Konfigurieren von Azure DevOps und eines Azure-Abonnements 
> * Überprüfen der CI-Pipeline
> * Überprüfen der CD-Pipeline
> * Ausführen eines Commits für Änderungen an Git und automatisches Bereitstellen in Azure
> * Bereinigen von Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Über [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) erhalten Sie ein kostenloses Abonnement.

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Erstellen einer ASP.NET Core-App und Bereitstellen dieser App in Service Fabric mithilfe von DevOps Starter

Mit DevOps Starter wird eine CI/CD-Pipeline in Azure Pipelines erstellt. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation verwenden. Ferner werden mit DevOps Starter Azure-Ressourcen wie ein Service Fabric-Cluster im Azure-Abonnement Ihrer Wahl erstellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie in das Suchfeld **DevOps Starter** ein, und wählen sie die Option dann aus. Klicken Sie auf **Hinzufügen**, um einen neuen zu erstellen.

    ![Das DevOps Starter-Dashboard](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. Wählen Sie **.NET** und anschließend **Weiter** aus.

1. Wählen Sie unter **Anwendungsframework auswählen** die Option **ASP.NET Core** und anschließend **Weiter** aus.

1. Wählen Sie **Service Fabric-Cluster** und anschließend **Weiter** aus. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurieren von Azure DevOps und eines Azure-Abonnements

1. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation auswählen. 

1. Geben Sie einen Namen für Ihr Azure DevOps-Projekt ein. 

1. Wählen Sie Ihr Azure-Abonnement.

1. Wählen Sie **Änderung** aus, um zusätzliche Azure-Konfigurationseinstellungen anzuzeigen und die VM-Größe und Betriebssystem des Knotens für den Service Fabric-Cluster zu bestimmen. In diesem Bereich werden verschiedene Optionen zum Konfigurieren von Typ und Speicherort der Azure-Dienste angezeigt.
 
1. Beenden Sie den Konfigurationsbereich von Azure, und wählen Sie **Fertig** aus.  
    Nach einigen Minuten ist der Vorgang abgeschlossen. Eine ASP.NET Core-Beispiel-App wird in einem Git-Repository in Ihrer Azure DevOps-Organisation eingerichtet, ein Service Fabric-Cluster wird erstellt, eine CI/CD-Pipeline wird ausgeführt, und Ihre App wird in Azure bereitgestellt. 

    Nachdem alle diese Schritte abgeschlossen wurden, wird das DevOps Starter-Dashboard im Azure-Portal angezeigt. Sie können das DevOps Starter-Dashboard auch direkt im Azure-Portal über **Alle Ressourcen** aufrufen. 

    Dieses Dashboard bietet Einblick in Ihr Azure DevOps-Coderepository, in Ihre CI/CD-Pipeline und in Ihren Service Fabric-Cluster. Sie können zusätzliche Optionen für Ihre CI/CD-Pipeline in Azure Repos konfigurieren. Wählen Sie auf der rechten Seite **Durchsuchen** aus, um Ihre ausgeführte App anzuzeigen.

## <a name="examine-the-ci-pipeline"></a>Überprüfen der CI-Pipeline

Mit DevOps Starter wird automatisch eine CI/CD-Pipeline in Azure Pipelines konfiguriert. Sie können die Pipeline untersuchen und anpassen. Gehen Sie wie folgt vor, um sich damit vertraut zu machen:

1. Navigieren Sie zum DevOps Starter-Dashboard.

1. Wählen Sie oben im DevOps Starter-Dashboard die Option **Buildpipelines** aus. Auf einer Browserregisterkarte wird die Buildpipeline für Ihr neues Projekt angezeigt.

1. Zeigen Sie auf das Feld **Status**, und wählen Sie dann die Auslassungspunkte (...) aus. In einem Menü werden verschiedene Optionen angezeigt, etwa zum Einreihen eines neuen Builds in die Warteschlange, zum Anhalten eines Builds und zum Bearbeiten der Buildpipeline.

1. Wählen Sie **Bearbeiten** aus.

1. In diesem Bereich können Sie sich die verschiedenen Aufgaben ansehen, die Sie für Ihre Buildpipeline ausführen können. Vom Build werden verschiedene Aufgaben durchgeführt. Beispielsweise werden Quellen aus dem Git-Repository abgerufen, Abhängigkeiten wiederhergestellt und für Bereitstellungen verwendete Ausgaben veröffentlicht.

1. Wählen Sie oben in der Buildpipeline den Buildpipelinenamen aus. 

1. Wählen Sie unter dem Buildpipelinenamen **Verlauf** aus. In diesem Bereich wird ein Überwachungsprotokoll mit den letzten Änderungen für den Build angezeigt. An der Buildpipeline vorgenommene Änderungen werden von Azure DevOps nachverfolgt, sodass Sie verschiedene Versionen vergleichen können.

1. Wählen Sie **Trigger** aus. Mit DevOps Starter wird automatisch ein CI-Trigger erstellt, und mit jedem für das Repository ausgeführten Commit wird ein neuer Build gestartet. Optional können Sie Branches in den CI-Prozess einbeziehen oder davon ausschließen.

1. Wählen Sie **Aufbewahrung** aus. Abhängig vom Szenario können Sie Richtlinien zum Aufbewahren oder Entfernen einer bestimmten Anzahl von Builds festlegen.

## <a name="examine-the-cd-pipeline"></a>Überprüfen der CD-Pipeline

Mit DevOps Starter werden die erforderlichen Schritte zum Bereitstellen über Ihre Azure DevOps-Organisation in Ihrem Azure-Abonnement automatisch erstellt und konfiguriert. Diese Schritte umfassen die Konfiguration einer Azure-Dienstverbindung zur Authentifizierung von Azure DevOps für Ihr Azure-Abonnement. Die Automatisierung erstellt darüber hinaus eine Releasepipeline, mit der CD in Azure bereitgestellt wird. Gehen Sie wie folgt vor, um weitere Informationen zur Releasepipeline zu erhalten:

1. Wählen Sie **Build und Release** und anschließend **Releases** aus. DevOps Starter erstellt eine Releasepipeline zum Verwalten von Bereitstellungen in Azure.

1. Wählen Sie neben Ihrer Releasepipeline die Auslassungspunkte (...) und anschließend **Bearbeiten** aus. Die Releasepipeline enthält eine *Pipeline*, die den Releaseprozess definiert.

1. Wählen Sie unter **Artefakte** die Option **Ablegen** aus. Die zuvor untersuchte Buildpipeline erzeugt die für das Artefakt verwendete Ausgabe. 

1. Wählen Sie rechts neben dem Symbol **Ablegen** die Option **Continuous Deployment-Trigger** aus. Diese Releasepipeline enthält einen aktivierten CD-Trigger. Jedes Mal, wenn ein neues Buildartefakt verfügbar ist, wird von diesem CD-Trigger eine Bereitstellung ausgeführt. Optional können Sie den Trigger deaktivieren, sodass Ihre Bereitstellungen manuell ausgeführt werden müssen. 

1. Wählen Sie auf der rechten Seite **Releases anzeigen** aus, um einen Releaseverlauf anzuzeigen.

1. Wählen Sie neben einem Release die Auslassungspunkte (...) und anschließend **Öffnen** aus. Sie können sich verschiedene Menüs ansehen, etwa eine Releasezusammenfassung, zugeordnete Arbeitselemente und Tests.

1. Wählen Sie **Commits** aus. In dieser Ansicht werden die dieser Bereitstellung zugeordneten Codecommits angezeigt. Vergleichen Sie Releases, um die Commitunterschiede zwischen den einzelnen Bereitstellungen anzuzeigen.

1. Wählen Sie **Protokolle** aus. Die Protokolle enthalten nützliche Informationen zum Bereitstellungsprozess. Sie können während und nach Bereitstellungen angezeigt werden.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Committen von Änderungen in Git und automatisches Bereitstellen dieser Änderungen in Azure 

 > [!NOTE]
 > Mit dem folgenden Verfahren wird die CI/CD-Pipeline durch eine einfache Textänderung getestet.

Nun können Sie mithilfe eines CI/CD-Prozesses, mit dem Ihre aktuelle Arbeit auf Ihrer Website automatisch bereitgestellt wird, mit einem Team an Ihrer App zusammenarbeiten. Durch jede Änderung an einem Git-Repository wird ein Build gestartet, und ein Release stellt Ihre Änderungen in Azure bereit. Führen Sie die Schritte in diesem Abschnitt aus, oder nutzen Sie eine andere Methode zum Committen Ihrer Änderungen in Ihrem Repository. Sie können beispielsweise das Git-Repository in Ihrem bevorzugten Tool oder Ihrer bevorzugten IDE klonen und anschließend Änderungen per Push in dieses Repository übertragen.

1. Wählen Sie im Azure DevOps-Menü **Code** > **Dateien** aus, und navigieren Sie zu Ihrem Repository.

1. Navigieren Sie zum Verzeichnis *Views\Home*. Wählen Sie anschließend die Auslassungspunkte neben der Datei *Index.cshtml* und dann **Bearbeiten** aus.

1. Nehmen Sie eine Änderung an der Datei vor. Fügen Sie beispielsweise in einem der div-Tags Text hinzu. 

1. Wählen Sie oben rechts **Committen** und dann erneut **Committen** aus, um Ihre Änderung per Push zu übertragen.  
    Nach wenigen Augenblicken wird ein Build gestartet. Anschließend wird zur Bereitstellung der Änderungen ein Release ausgeführt. Sie können den Buildstatus auf dem DevOps Starter-Dashboard oder im Browser mit der Azure DevOps-Echtzeitprotokollierung überwachen.

1. Aktualisieren Sie nach Abschluss des Release Ihre App, um Ihre Änderungen zu überprüfen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beim Durchführen von Tests können Sie Gebühren vermeiden, indem Sie die Ressourcen bereinigen. Sie können den in diesem Tutorial erstellten Azure Service Fabric-Cluster und zugehörige Ressourcen löschen, wenn Sie sie nicht mehr benötigen. Verwenden Sie dazu die Funktion **Löschen** im DevOps Starter-Dashboard.

> [!IMPORTANT]
> Mit dem folgenden Verfahren werden Ressourcen endgültig gelöscht. Mit der Funktion *Löschen* werden die Daten in Azure und Azure DevOps gelöscht, die vom Projekt in DevOps Starter erstellt wurden. Diese Daten können anschließend nicht wiederhergestellt werden. Verwenden Sie dieses Verfahren nur, nachdem Sie die Anweisungen sorgfältig gelesen haben.

1. Navigieren Sie im Azure-Portal zum DevOps Starter-Dashboard.
1. Wählen Sie oben rechts **Löschen** aus. 
1. Wählen Sie an der Eingabeaufforderung **Ja** aus, um die Ressourcen *endgültig zu löschen*.

## <a name="next-steps"></a>Nächste Schritte

Diese CI/CD-Pipeline können Sie optional an die Anforderungen Ihres Teams anpassen. Sie können dieses CI/CD-Muster auch als Vorlage für Ihre anderen Pipelines verwenden. In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer ASP.NET Core-App und Bereitstellen dieser App in Service Fabric mithilfe von DevOps Starter
> * Konfigurieren von Azure DevOps und eines Azure-Abonnements 
> * Überprüfen der CI-Pipeline
> * Überprüfen der CD-Pipeline
> * Committen von Änderungen in Git und automatisches Bereitstellen dieser Änderungen in Azure
> * Bereinigen von Ressourcen

Weiter Informationen zu Service Fabric und Microservices finden Sie hier:

> [!div class="nextstepaction"]
> [Use a microservices approach for building applications (Verwenden eines Microservices-Ansatzes zum Erstellen von Anwendungen)](/azure/devops/pipelines/release/define-multistage-release-process)