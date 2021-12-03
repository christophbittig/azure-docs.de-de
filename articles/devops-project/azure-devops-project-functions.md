---
title: 'Tutorial: Bereitstellen von ASP.NET-Apps in Azure Functions mit Azure DevOps Starter'
description: Azure DevOps Starter erleichtert die ersten Schritte mit Azure. Mit DevOps Starter können Sie in wenigen Schritten Ihre ASP.NET-App in Azure Functions bereitstellen.
ms.author: gwallace
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.custom: devx-track-csharp
ms.openlocfilehash: ef4a33fee3b06155ef02f043d61fcac5646d6a20
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053441"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>Bereitstellung in Azure Functions mit DevOps Starter

Azure DevOps Starter bietet eine vereinfachte Umgebung, in der Sie Ihren vorhandenen Code und Ihr Git-Repository verwenden oder eine Beispielanwendung auswählen können, um eine Continuous Integration- und Continuous Delivery-Pipeline (CI/CD) für Azure zu erstellen.

DevOps Starter ermöglicht außerdem:

* Automatisches Erstellen von Azure-Ressourcen, etwa Azure Functions

* Erstellen und Konfigurieren einer Releasepipeline in Azure DevOps für CI/CD

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>* Bereitstellen einer ASP.NET-App in Azure Functions mithilfe von DevOps Starter
>* Konfigurieren von Azure DevOps und eines Azure-Abonnements
>* Untersuchen der Azure-Funktion
>* Überprüfen der CI-Pipeline
>* Überprüfen der CD-Pipeline
>* Committen von Änderungen in Git und automatisches Bereitstellen dieser Änderungen in Azure
>* Bereinigen von Ressourcen

Derzeit werden für Funktionen die Runtimes **.NET** und **Node.js** unterstützt. In diesem Tutorial wird für die Bereitstellung in Azure Functions die .NET-Runtime verwendet. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Über [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) erhalten Sie ein kostenloses Abonnement.

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>Bereitstellen einer ASP.NET-App in Azure Functions mithilfe von DevOps Starter

Mit DevOps Starter wird eine CI/CD-Pipeline in Azure Pipelines erstellt. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation verwenden. Ferner werden mit DevOps Projects Azure-Ressourcen wie etwa ein IoT-Hub im Azure-Abonnement Ihrer Wahl erstellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

1. Geben Sie in das Suchfeld **DevOps Starter** ein, und wählen sie die Option dann aus. Klicken Sie auf **Hinzufügen**, um einen neuen zu erstellen.

    ![Das DevOps Starter-Dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Wählen Sie **.NET** und anschließend **Weiter** aus. Wählen Sie unter **Anwendungsframework auswählen** die Option **ASP.NET** aus, und klicken Sie auf **Weiter**.

1. Wählen Sie **Funktions-App** und anschließend **Weiter** aus.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurieren von Azure DevOps und eines Azure-Abonnements

1. Geben Sie einen Namen für Ihr Azure DevOps-Projekt ein.

1. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation auswählen.

1. Wählen Sie Ihr Azure-Abonnement.

1. Wenn Sie weitere Azure-Konfigurationseinstellungen anzeigen und den Tarif und den Standort ermitteln möchten, klicken Sie auf **Weitere Einstellungen**. In diesem Bereich werden verschiedene Optionen zum Konfigurieren des Tarifs und Standorts der Azure-Dienste angezeigt.

1. Beenden Sie den Konfigurationsbereich von Azure, und wählen Sie **Fertig** aus.

1. Nach einigen Minuten ist der Vorgang abgeschlossen. Eine ASP.NET-Beispiel-App wird in einem Git-Repository in Ihrer Azure DevOps-Organisation eingerichtet, eine Funktions-App und eine Application Insights-Instanz werden erstellt, eine CI/CD-Pipeline wird ausgeführt, und Ihre App wird in Azure bereitgestellt.

   Nachdem alle diese Schritte abgeschlossen wurden, wird das Azure DevOps Starter-Dashboard im Azure-Portal angezeigt. Sie können das DevOps Starter-Dashboard auch direkt im Azure-Portal über **Alle Ressourcen** aufrufen.

   Dieses Dashboard bietet Einblick in Ihr Azure DevOps-Coderepository, in Ihre CI/CD-Pipeline und in Ihre Azure-Funktion. Sie können zusätzliche CI/CD-Optionen in Ihrer Azure DevOps-Pipeline konfigurieren. Wählen Sie auf der rechten Seite die anzuzeigende **Funktions-App** aus.

## <a name="examine-the-function-app"></a>Untersuchen der Funktions-App

DevOps Starter konfiguriert automatisch die Funktions-App, die Sie untersuchen und anpassen können. Gehen Sie wie folgt vor, um sich mit der Funktions-App vertraut zu machen:

1. Navigieren Sie zum DevOps Starter-Dashboard.

    ![DevOps Projects-Dashboard](_img/azure-devops-project-functions/fapp-dashboard.png)

1. Wählen Sie auf der rechten Seite die Funktions-App aus. Für die Funktions-App wird ein Bereich geöffnet. In dieser Ansicht können Sie verschiedene Aktionen ausführen und beispielsweise den Betrieb überwachen oder Protokolle durchsuchen.

    ![Funktionen-App](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Überprüfen der CI-Pipeline

Mit DevOps Starter wird automatisch eine CI/CD-Pipeline in Ihrer Azure DevOps-Organisation konfiguriert. Sie können die Pipeline untersuchen und anpassen. Gehen Sie wie folgt vor, um sich damit vertraut zu machen:

1. Navigieren Sie zum DevOps Starter-Dashboard.

1. Klicken Sie unter **Build** auf den Link. Auf einer Browserregisterkarte wird die Buildpipeline für Ihr neues Projekt angezeigt.

    ![Screenshot: DevOps Starter-Dashboard im Azure-Portal mit einem Pfeil, der auf einen Link unter „Build“ in der CI/CD-Pipeline zeigt](_img/azure-devops-project-functions/build.png)

1. Wählen Sie **Bearbeiten** aus. In diesem Bereich können Sie sich die verschiedenen Aufgaben ansehen, die Sie für Ihre Buildpipeline ausführen können. Vom Build werden verschiedene Aufgaben durchgeführt. Beispielsweise wird Quellcode aus dem Git-Repository abgerufen, die Anwendung wird erstellt, Komponententests werden ausgeführt, und für Bereitstellungen verwendete Ausgaben werden veröffentlicht.

1. Wählen Sie **Trigger** aus. Mit DevOps Starter wird automatisch ein CI-Trigger erstellt, und mit jedem für das Repository ausgeführten Commit wird ein neuer Build gestartet. Optional können Sie Branches in den CI-Prozess einbeziehen oder davon ausschließen.

1. Wählen Sie **Aufbewahrung** aus. Abhängig vom Szenario können Sie Richtlinien zum Aufbewahren oder Entfernen einer bestimmten Anzahl von Builds festlegen.

1. Wählen Sie oben in der Buildpipeline den Buildpipelinenamen aus.

1. Ersetzen Sie den Namen Ihrer Buildpipeline durch einen aussagekräftigeren Namen, und wählen Sie im Dropdownmenü **Speichern und in Warteschlange einreihen** die Option **Speichern** aus.

1. Wählen Sie unter dem Buildpipelinenamen **Verlauf** aus. In diesem Bereich wird ein Überwachungsprotokoll mit den letzten Änderungen für den Build angezeigt. An der Buildpipeline vorgenommene Änderungen werden von Azure DevOps nachverfolgt, sodass Sie verschiedene Versionen vergleichen können.

## <a name="examine-the-cd-release-pipeline"></a>Überprüfen der CD-Releasepipeline

Mit DevOps Starter werden die erforderlichen Schritte zum Bereitstellen über Ihre Azure DevOps-Organisation in Ihrem Azure-Abonnement automatisch erstellt und konfiguriert. Diese Schritte umfassen die Konfiguration einer Azure-Dienstverbindung zur Authentifizierung von Azure DevOps für Ihr Azure-Abonnement. Die Automatisierung erstellt darüber hinaus eine Releasepipeline, mit der CD in Azure bereitgestellt wird. Gehen Sie wie folgt vor, um weitere Informationen zur Releasepipeline zu erhalten:

1. Navigieren Sie zu **Pipelines | Releases**.

1. Klicken Sie auf **Bearbeiten**.

1. Wählen Sie unter **Artefakte** die Option **Ablegen** aus. Die in den vorherigen Schritten untersuchte Buildpipeline erzeugt die für das Artefakt verwendete Ausgabe.

1. Wählen Sie rechts neben dem Symbol **Ablegen** die Option **Continuous Deployment-Trigger** aus. Diese Releasepipeline enthält einen aktivierten CD-Trigger. Jedes Mal, wenn ein neues Buildartefakt verfügbar ist, wird von diesem CD-Trigger eine Bereitstellung ausgeführt. Optional können Sie den Trigger deaktivieren, sodass Ihre Bereitstellungen manuell ausgeführt werden müssen.

1. Wählen Sie auf der rechten Seite **Releases anzeigen** aus, um einen Releaseverlauf anzuzeigen.

1. Klicken Sie auf das Release. Dadurch wird die Pipeline angezeigt. Klicken Sie auf eine beliebige Umgebung, um die **Zusammenfassung, Commits** und die zugehörigen **Arbeitselemente** für das Release zu überprüfen.

1. Wählen Sie **Commits** aus. In dieser Ansicht werden die dieser Bereitstellung zugeordneten Codecommits angezeigt. Vergleichen Sie Releases, um die Commitunterschiede zwischen den einzelnen Bereitstellungen anzuzeigen.

1. Wählen Sie **Protokolle anzeigen** aus. Die Protokolle enthalten nützliche Informationen zum Bereitstellungsprozess. Sie können während und nach Bereitstellungen angezeigt werden.

## <a name="commit-code-changes-and-execute-cicd"></a>Ausführen eines Commits für Codeänderungen und Ausführen von CI/CD

> [!NOTE]
> Mit dem folgenden Verfahren wird die CI/CD-Pipeline durch eine einfache Textänderung getestet.

Nun können Sie mithilfe eines CI/CD-Prozesses, mit dem Ihre aktuelle Arbeit automatisch in Ihrer Azure-Funktion bereitgestellt wird, mit einem Team an Ihrer App zusammenarbeiten. Mit jeder Änderung am GitHub-Repository wird in Azure DevOps ein Build gestartet, und durch eine CD-Pipeline wird eine Bereitstellung in Azure ausgeführt. Führen Sie die Schritte in diesem Abschnitt aus, oder nutzen Sie eine andere Methode zum Committen Ihrer Änderungen in Ihrem Repository. Sie können beispielsweise das Git-Repository in Ihrem bevorzugten Tool oder Ihrer bevorzugten IDE klonen und anschließend Änderungen per Push in dieses Repository übertragen.

1. Wählen Sie im Azure DevOps-Menü **Repos | Dateien** aus, und navigieren Sie zu Ihrem Repository.

1. Das Repository enthält bereits Code namens **SampleFunctionApp**, basierend auf der Anwendungssprache, die Sie bei der Erstellung ausgewählt haben. Öffnen Sie die Datei **Application/SampleFunctionApp/Function1.cs**.

1. Wählen Sie **Bearbeiten** aus, und ändern Sie **Zeile 31**. Sie können sie beispielsweise in **Hello there! Welcome to Azure Functions using DevOps Starter** ändern.

1. Wählen Sie oben rechts **Committen** und dann erneut **Committen** aus, um Ihre Änderung per Push zu übertragen.

1. Öffnen Sie die Datei **Application/SampleFunctionApp.Test/Function1TestRunner.cs**. 

1. Wählen Sie **Bearbeiten** aus, und ändern Sie **Zeile 21**. Sie können sie beispielsweise in **Hello there! Welcome to Azure Functions using Azure DevOps Starter** ändern.

     Kurz danach wird in Azure DevOps ein Build gestartet und zur Bereitstellung der Änderungen ein Release ausgeführt. Überwachen Sie den Buildstatus auf dem DevOps Starter-Dashboard oder im Browser mit Ihrer Azure DevOps-Organisation.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die erstellten zugehörigen Ressourcen löschen, wenn Sie sie nicht mehr benötigen. Verwenden Sie die Funktion **Löschen** auf dem DevOps Starter-Dashboard.

## <a name="next-steps"></a>Nächste Schritte

Diese Build- und Releasepipelines können Sie optional an die Anforderungen Ihres Teams anpassen. Sie können dieses CI/CD-Muster auch als Vorlage für Ihre anderen Pipelines verwenden. In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen einer ASP.NET Core-App in Azure Functions mithilfe von DevOps Starter
> * Konfigurieren von Azure DevOps und eines Azure-Abonnements 
> * Untersuchen der Azure-Funktion
> * Überprüfen der CI-Pipeline
> * Überprüfen der CD-Pipeline
> * Committen von Änderungen in Git und automatisches Bereitstellen dieser Änderungen in Azure
> * Bereinigen von Ressourcen

