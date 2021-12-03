---
title: 'Tutorial: Bereitstellen der von Azure Cosmos DB unterstützten Node.js-Apps mit Azure DevOps Starter'
description: Azure DevOps Starter erleichtert die ersten Schritte mit Azure. Mit DevOps Starter können Sie Ihre auf Azure Cosmos DB basierende Node.js-App in wenigen Schritten in einer Windows-Web-App bereitstellen.
ms.author: gwallace
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.custom: devx-track-js
ms.openlocfilehash: ff811be8f4651bde7059fe1d575c0ac703570830
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060451"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Bereitstellen von auf Azure Cosmos DB basierenden Node.js-Apps mit DevOps Starter

Azure DevOps Starter bietet eine optimierte Umgebung, in der Sie eine Continuous Integration- und Continuous Deployment-Pipeline (CI/CD) für Azure erstellen können. Hierzu verwenden Sie den vorhandenen Code und das Git-Repository oder wählen eine Beispielanwendung aus.

DevOps Starter ermöglicht außerdem:

* Automatisches Erstellen von Azure-Ressourcen wie Azure Cosmos DB, Azure Application Insights, Azure App Service und App Service-Plänen

* Erstellen und Konfigurieren einer CI/CD-Releasepipeline in Azure DevOps

In diesem Tutorial werden Sie Folgendes durchführen:

> [!div class="checklist"]
> * Bereitstellen einer auf Azure Cosmos DB basierenden Node.js-App mithilfe von DevOps Starter
> * Konfigurieren von Azure DevOps und eines Azure-Abonnements
> * Untersuchen der Azure Cosmos DB-Instanz
> * Überprüfen der CI-Pipeline
> * Überprüfen der CD-Pipeline
> * Committen der Änderungen in Git und automatisches Bereitstellen dieser Änderungen in Azure
> * Bereinigen der Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement, das Sie kostenlos über [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) erhalten.

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>Bereitstellen einer Node.js-App mit DevOps Starter

Mit DevOps Starter wird eine CI/CD-Pipeline in Azure Pipelines erstellt. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation verwenden. DevOps Starter erstellt außerdem Azure-Ressourcen (etwa Azure Cosmos DB, Application Insights, App Service und App Service-Pläne) im Azure-Abonnement Ihrer Wahl.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie in das Suchfeld **DevOps Starter** ein, und wählen sie die Option dann aus. Klicken Sie auf **Hinzufügen**, um einen neuen zu erstellen.

    ![Das DevOps Starter-Dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Wählen Sie als Laufzeit **Node.js** und anschließend **Weiter** aus. Wählen Sie unter **Anwendungsframework auswählen** die Option **Express.js** aus.

1. Aktivieren Sie für **Cosmos DB** den Abschnitt **Datenbank hinzufügen**, und wählen Sie dann **Weiter** aus.

    ![Hinzufügen einer Datenbank](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Starter unterstützt verschiedene Anwendungsframeworks wie **Express.js**, **Node.js-Beispiel-Apps** und **Sail.js**. In diesem Tutorial wird **Express.js** verwendet.

1. Wählen Sie einen Azure-Dienst zum Bereitstellen der Anwendung und anschließend **Weiter** aus. Zu den Optionen gehören Windows-Web-App, Azure Kubernetes Service und Azure-Web-App für Container. In diesem Tutorial wird **Windows-Web-App** verwendet.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurieren von Azure DevOps und eines Azure-Abonnements

1. Geben Sie einen Namen für Ihr Azure DevOps-Projekt ein.

1. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation auswählen.

1. Wählen Sie Ihr Azure-Abonnement.

1. Wenn Sie weitere Azure-Konfigurationseinstellungen anzeigen und den Tarif und den Standort ermitteln möchten, wählen Sie **Weitere Einstellungen** aus. In diesem Bereich werden verschiedene Optionen zum Konfigurieren des Tarifs und Standorts der Azure-Dienste angezeigt.

1. Beenden Sie den Konfigurationsbereich von Azure, und wählen Sie **Fertig** aus.

1. Der Vorgang wird nach wenigen Minuten abgeschlossen. Eine Node.js-Beispiel-App wird in einem Git-Repository in Ihrer Azure DevOps-Organisation eingerichtet. Anschließend werden Azure Cosmos DB-, App Service- und Application Insights-Ressourcen, ein App Service-Plan sowie eine CI/CD-Pipeline erstellt. Ihre App wird anschließend in Azure bereitgestellt.

   Wenn alle diese Schritte abgeschlossen sind, wird das Azure DevOps Starter-Dashboard im Azure-Portal angezeigt. Sie können das DevOps Starter-Dashboard auch direkt im Azure-Portal über **Alle Ressourcen** aufrufen.

   Dieses Dashboard bietet Einblick in Ihr Azure DevOps-Coderepository, in Ihre CI/CD-Pipeline und in Ihre Azure Cosmos DB-Datenbank. Sie können zusätzliche CI/CD-Optionen in Ihrer Azure DevOps-Pipeline konfigurieren. Wählen Sie auf der rechten Seite des Dashboards **Azure Cosmos DB** aus, um diese Optionen anzuzeigen.

## <a name="examine-azure-cosmos-db"></a>Untersuchen der Azure Cosmos DB-Instanz

DevOps Starter konfiguriert automatisch die Azure Cosmos DB-Instanz, die Sie untersuchen und anpassen können. Gehen Sie wie folgt vor, um sich mit Azure Cosmos DB vertraut zu machen:

1. Navigieren Sie zum DevOps Starter-Dashboard.

    ![DevOps Projects-Dashboard](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. Wählen Sie auf der rechten Seite Azure Cosmos DB aus. Für die Azure Cosmos DB-Instanz wird ein Bereich geöffnet. In dieser Ansicht können Sie verschiedene Aktionen ausführen und beispielsweise Vorgänge überwachen oder Protokolle durchsuchen.

    ![Bereich „Azure Cosmos DB“](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Überprüfen der CI-Pipeline

Mit DevOps Starter wird automatisch eine CI/CD-Pipeline in Ihrer Azure DevOps-Organisation konfiguriert. Sie können die Pipeline untersuchen und anpassen. Gehen Sie wie folgt vor, um sich damit vertraut zu machen:

1. Navigieren Sie zum DevOps Starter-Dashboard.

1. Wählen Sie unter **Build** den Link aus. Auf einer Browserregisterkarte wird die Buildpipeline für Ihr neues Projekt angezeigt.

    ![Bereich „Build“](_img/azure-devops-project-cosmos-db/build.png)

1. Wählen Sie **Bearbeiten** aus. In diesem Bereich können Sie sich die verschiedenen Aufgaben ansehen, die Sie für Ihre Buildpipeline ausführen können. Vom Build werden verschiedene Aufgaben durchgeführt. Beispielsweise wird Quellcode aus dem Git-Repository abgerufen, die Anwendung wird erstellt, Komponententests werden ausgeführt, und für Bereitstellungen verwendete Ausgaben werden veröffentlicht.

1. Wählen Sie **Trigger** aus. Mit DevOps Starter wird automatisch ein CI-Trigger erstellt, und mit jedem für das Repository ausgeführten Commit wird ein neuer Build gestartet. Sie können Branches in den CI-Prozess einbeziehen oder davon ausschließen.

1. Wählen Sie **Aufbewahrung** aus. Abhängig vom Szenario können Sie Richtlinien zum Aufbewahren oder Entfernen einer bestimmten Anzahl von Builds festlegen.

1. Wählen Sie oben in der Buildpipeline den Buildpipelinenamen aus.

1. Ersetzen Sie den Namen Ihrer Buildpipeline durch einen aussagekräftigeren Namen, und wählen Sie im Dropdownmenü **Speichern und in Warteschlange einreihen** die Option **Speichern** aus.

1. Wählen Sie unter dem Buildpipelinenamen **Verlauf** aus. In diesem Bereich wird ein Überwachungsprotokoll mit den letzten Änderungen für den Build angezeigt. An der Buildpipeline vorgenommene Änderungen werden von Azure DevOps nachverfolgt, sodass Sie verschiedene Versionen vergleichen können.

## <a name="examine-the-cd-release-pipeline"></a>Überprüfen der CD-Releasepipeline

Mit DevOps Starter werden die erforderlichen Schritte zum Bereitstellen über Ihre Azure DevOps-Organisation in Ihrem Azure-Abonnement automatisch erstellt und konfiguriert. Diese Schritte umfassen die Konfiguration einer Azure-Dienstverbindung zur Authentifizierung von Azure DevOps für Ihr Azure-Abonnement. Die Automatisierung erstellt darüber hinaus eine Releasepipeline, mit der CD in Azure bereitgestellt wird. Gehen Sie wie folgt vor, um weitere Informationen zur Releasepipeline zu erhalten:

1. Wählen Sie **Pipelines** und **Releases** aus.

1. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie unter **Artefakte** die Option **Ablegen** aus. Die in den vorherigen Schritten untersuchte Buildpipeline erzeugt die für das Artefakt verwendete Ausgabe.

1. Wählen Sie rechts neben dem Symbol **Ablegen** die Option **Continuous Deployment-Trigger** aus. Diese Releasepipeline enthält einen aktivierten Continuous Deployment-Trigger. Jedes Mal, wenn ein neues Buildartefakt verfügbar ist, wird von diesem CD-Trigger eine Bereitstellung ausgeführt. Sie können den Trigger deaktivieren, sodass Ihre Bereitstellungen manuell ausgeführt werden.

1. Wählen Sie auf der rechten Seite den Abschnitt **Releases anzeigen** aus, um einen Releaseverlauf anzuzeigen.

1. Wählen Sie das Release aus. Dadurch wird die Pipeline angezeigt. Wählen Sie eine beliebige Umgebung aus, um die Zusammenfassung, Commits oder zugehörige Arbeitselemente für das Release zu überprüfen.

1. Wählen Sie **Commits** aus. In dieser Ansicht werden die dieser Bereitstellung zugeordneten Codecommits angezeigt. Vergleichen Sie Releases, um die Commitunterschiede zwischen den einzelnen Bereitstellungen anzuzeigen.

1. Wählen Sie **Protokolle anzeigen** aus. Die Protokolle enthalten nützliche Informationen zum Bereitstellungsprozess. Sie können während und nach Bereitstellungen angezeigt werden.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Committen von Codeänderungen und Ausführen der CI/CD-Pipeline

> [!NOTE]
> Mit dem folgenden Verfahren wird die CI/CD-Pipeline durch eine einfache Textänderung getestet.

Nun können Sie mithilfe eines CI/CD-Prozesses, mit dem Ihre aktuelle Arbeit in App Service bereitgestellt wird, mit einem Team an Ihrer App zusammenarbeiten. Mit jeder Änderung am GitHub-Repository wird in Azure DevOps ein Build gestartet, und durch eine CD-Pipeline wird eine Bereitstellung in Azure ausgeführt. Führen Sie die Schritte in diesem Abschnitt aus, oder nutzen Sie eine andere Methode zum Committen Ihrer Änderungen in Ihrem Repository. Sie können beispielsweise das Git-Repository in Ihrem bevorzugten Tool oder Ihrer bevorzugten IDE klonen und anschließend Änderungen per Push in dieses Repository übertragen.

1. Wählen Sie im Azure DevOps-Menü **Repos** und dann **Dateien** aus. Navigieren Sie dann zu Ihrem Repository.

1. Das Repository enthält bereits Code, der auf der Anwendungssprache basiert, die Sie bei der Erstellung ausgewählt haben. Öffnen Sie die Datei **Application/views/index.pug**.

1. Wählen Sie **Bearbeiten** aus, und ändern Sie **Zeile 15**. Ändern Sie sie beispielsweise in „My First deployment to Azure App Service powered by Azure Cosmos DB“.

1. Wählen Sie oben rechts **Committen** und dann erneut **Committen** aus, um Ihre Änderung per Push zu übertragen.

     Nach wenigen Sekunden wird in Azure DevOps ein Build gestartet und zur Bereitstellung der Änderungen ein Release ausgeführt. Überwachen Sie den Buildstatus auf dem DevOps Starter-Dashboard oder im Browser mit Ihrer Azure DevOps-Organisation.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die erstellten zugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Verwenden Sie die Funktion **Löschen** auf dem DevOps Starter-Dashboard.

## <a name="next-steps"></a>Nächste Schritte

Diese Build- und Releasepipelines können Sie den Anforderungen Ihres Teams anpassen. Sie können dieses CI/CD-Muster auch als Vorlage für Ihre anderen Pipelines verwenden. In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen einer auf Azure Cosmos DB basierenden Node.js-App mithilfe von DevOps Starter
> * Konfigurieren von Azure DevOps und eines Azure-Abonnements 
> * Untersuchen der Azure Cosmos DB-Instanz
> * Überprüfen der CI-Pipeline
> * Überprüfen der CD-Pipeline
> * Committen von Änderungen in Git und automatisches Bereitstellen dieser Änderungen in Azure
> * Bereinigen von Ressourcen

Weitere Informationen und nächste Schritte finden Sie unter [Define your multi-stage continuous deployment (CD) pipeline](/azure/devops/pipelines/release/define-multistage-release-process) (Definieren einer mehrstufigen CD-Pipeline (Continuous Deployment)).