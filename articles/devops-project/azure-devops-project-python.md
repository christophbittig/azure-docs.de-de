---
title: 'Schnellstart: Erstellen einer CI/CD-Pipeline für Python mit Azure DevOps Starter'
description: DevOps Starter erleichtert die ersten Schritte mit Azure. Damit können Sie eine App in einem Azure-Dienst Ihrer Wahl in einigen wenigen Schritten starten.
services: vsts
documentationcenter: vs-devops-build
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.date: 03/24/2020
ms.prod: devops
ms.technology: devops-cicd
ms.topic: quickstart
ms.workload: web
ms.tgt_pltfrm: na
ms.custom: mvc, devx-track-python, mode-api
ms.openlocfilehash: 3f6fda9367ba27acbd47fcb316541d1b3d50aafc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061642"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-starter"></a>Erstellen einer CI/CD-Pipeline für Python mit Azure DevOps Starter

In dieser Schnellstartanleitung verwenden Sie die vereinfachte Benutzeroberfläche von Azure DevOps Starter zum Einrichten einer CI/CD-Pipeline (Continuous Integration und Continuous Delivery) für Ihre Python-App in Azure Pipelines. Sie können mit Azure DevOps Starter alles einrichten, was Sie zum Entwickeln, Bereitstellen und Überwachen Ihrer App benötigen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Ein [Azure DevOps](https://azure.microsoft.com/services/devops/)-Konto und eine Organisation

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Mit DevOps Starter wird eine CI/CD-Pipeline in Azure Pipelines erstellt. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation verwenden. Ferner werden mit DevOps Starter Azure-Ressourcen im Azure-Abonnement Ihrer Wahl erstellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Geben Sie in das Suchfeld **DevOps Starter** ein, und wählen sie die Option dann aus. Klicken Sie auf **Hinzufügen**, um einen neuen zu erstellen.

    ![Das DevOps Starter-Dashboard](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Auswählen einer Beispielanwendung und eines Azure-Diensts

1. Wählen Sie die Python-Beispielanwendung aus. Zu den Python-Beispielen zählen verschiedene Anwendungsframeworks.

1. Standardmäßig wird das Beispielframework Django verwendet. Übernehmen Sie die Standardeinstellung, und wählen Sie **Weiter** aus. „Web-App für Container“ ist das Standardziel für die Bereitstellung. Das Anwendungsframework, das Sie zuvor ausgewählt haben, bestimmt den Typ des hier verfügbaren Bereitstellungsziels für den Azure-Dienst. 

3. Übernehmen Sie den Standarddienst, und wählen Sie **Weiter** aus.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurieren von Azure DevOps und eines Azure-Abonnements 

1. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation auswählen. 

    1. Geben Sie einen Namen für Ihr Projekt in Azure DevOps ein.  

    1. Wählen Sie Ihr Azure-Abonnement und den Standort aus, geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie dann **Fertig** aus.  
    
     Nach wenigen Minuten wird das Starter-Dashboard im Azure-Portal angezeigt. Eine Beispielanwendung wird in einem Repository in Ihrer Azure DevOps-Organisation eingerichtet, ein Build wird ausgeführt, und Ihre Anwendung wird in Azure bereitgestellt. Dieses Dashboard bietet Einblick in Ihr Coderepository, in Ihre CI/CD-Pipeline und in Ihre Anwendung in Azure.  
    
2. Wählen Sie **Durchsuchen** aus, um Ihre ausgeführte Anwendung anzuzeigen.

    ![Dashboardansicht](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   Mit DevOps Projects wird automatisch ein CI-Trigger für Build und Release konfiguriert. Nun können Sie mithilfe eines CI/CD-Prozesses, mit dem Ihre aktuelle Arbeit auf Ihrer Website automatisch bereitgestellt wird, mit einem Team an einer Python-App zusammenarbeiten.

## <a name="commit-code-changes-and-execute-cicd"></a>Ausführen eines Commits für Codeänderungen und Ausführen von CI/CD

Von DevOps Starter wird ein Git-Repository in Azure Repos oder auf GitHub erstellt. Gehen Sie wie folgt vor, um das Repository anzuzeigen und Codeänderungen an Ihrer Anwendung vorzunehmen: 

1. Klicken Sie auf der linken Seite des DevOps Starter-Dashboards auf den Link für Ihren Mainbranch. Über diesen Link wird die Ansicht des neu erstellten Git-Repositorys geöffnet.

1. Wählen Sie oben rechts im Browser die Option **Klonen** aus, um die Repository-Klon-URL anzuzeigen. Sie können Ihr Git-Repository in Ihrer bevorzugten IDE klonen. Bei den nächsten Schritten können Sie den Webbrowser verwenden, um Codeänderungen direkt am Mainbranch vorzunehmen und für Codeänderungen einen Commit auszuführen.

1. Navigieren Sie auf der linken Seite zur Datei **app/templates/app/index.html**.

1. Wählen Sie **Bearbeiten** aus, und ändern Sie den Text an einigen Stellen. Ändern Sie beispielsweise einige Textstellen für eines der div-Tags.

1. Wählen Sie **Commit** aus, und speichern Sie anschließend die Änderungen.

1. Navigieren Sie in Ihrem Browser zum DevOps Starter-Dashboard. Nun wird angezeigt, dass ein Buildvorgang ausgeführt wird. Die Änderungen, die Sie eben vorgenommen haben, werden automatisch erstellt und über eine CI/CD-Pipeline bereitgestellt.

## <a name="examine-the-cicd-pipeline"></a>Überprüfen der CI/CD-Pipeline

Im vorherigen Schritt wurde von DevOps Starter automatisch eine vollständige CI/CD-Pipeline konfiguriert. Untersuchen Sie die Pipeline, und passen Sie sie bei Bedarf an. Gehen Sie wie folgt vor, um sich mit den Build- und Releasepipelines vertraut zu machen:

1. Wählen Sie oben im DevOps Starter-Dashboard die Option **Buildpipelines** aus. Auf einer Browserregisterkarte wird die Buildpipeline für Ihr neues Projekt angezeigt.

1. Zeigen Sie auf das Feld **Status**, und wählen Sie dann die **Auslassungspunkte** (...) aus. In einem Menü werden verschiedene Optionen angezeigt, etwa zum Einreihen eines neuen Builds in die Warteschlange, zum Anhalten eines Builds und zum Bearbeiten der Buildpipeline.

1. Wählen Sie **Bearbeiten** aus.

1. In diesem Bereich können Sie sich die verschiedenen Aufgaben ansehen, die Sie für Ihre Buildpipeline ausführen können. Vom Build werden verschiedene Aufgaben durchgeführt. Beispielsweise werden Quellen aus dem Git-Repository abgerufen, Abhängigkeiten wiederhergestellt und Ausgaben für Bereitstellungen veröffentlicht.

1. Wählen Sie oben in der Buildpipeline den Buildpipelinenamen aus.

1. Ersetzen Sie den Namen Ihrer Buildpipeline durch einen aussagekräftigeren Namen, und wählen Sie **Speichern und in Warteschlange einreihen** und dann **Speichern** aus.

1. Wählen Sie unter dem Buildpipelinenamen **Verlauf** aus. Es wird ein Überwachungspfad mit den letzten Änderungen für den Build angezeigt. An der Buildpipeline vorgenommene Änderungen werden von Azure DevOps nachverfolgt, sodass Sie verschiedene Versionen vergleichen können.

1. Wählen Sie **Trigger** aus. DevOps Starter erstellt automatisch einen CI-Trigger, und mit jedem für das Repository ausgeführten Commit wird ein neuer Build gestartet. Optional können Sie Branches aus dem CI-Prozess einbeziehen oder ausschließen.

1. Wählen Sie **Aufbewahrung** aus. Abhängig vom Szenario können Sie Richtlinien zum Aufbewahren oder Entfernen einer bestimmten Anzahl von Builds festlegen.

1. Wählen Sie **Build und Release** und anschließend **Releases** aus.   
 DevOps Projects erstellt eine Releasepipeline zum Verwalten von Bereitstellungen in Azure.

1. Wählen Sie neben Ihrer Releasepipeline die Auslassungspunkte (...) und anschließend **Bearbeiten** aus. Die Releasepipeline bestimmt den Releaseprozess.  
        
12. Wählen Sie unter **Artefakte** die Option **Ablegen** aus. Die in den vorherigen Schritten untersuchte Buildpipeline erzeugt die für das Artefakt verwendete Ausgabe. 

1. Wählen Sie neben dem Symbol **Ablegen** die Option **Continuous Deployment-Trigger** aus. Die Releasepipeline enthält einen aktivierten CD-Trigger. Jedes Mal, wenn ein neues Buildartefakt verfügbar ist, wird von diesem CD-Trigger eine Bereitstellung ausgeführt. Optional können Sie den Trigger deaktivieren, sodass Ihre Bereitstellungen manuell ausgeführt werden müssen. 

1. Wählen Sie auf der linken Seite **Aufgaben** aus. Bei den Aufgaben handelt es sich um die Aktivitäten, die beim Bereitstellungsprozess ausgeführt werden. In diesem Beispiel wurde für die Bereitstellung in Azure App Service eine Aufgabe erstellt.

1. Wählen Sie auf der rechten Seite **Releases anzeigen** aus, um einen Releaseverlauf anzuzeigen.  
        
1. Wählen Sie neben einem Ihrer Releases die Auslassungspunkte (...) und dann die Option **Öffnen** aus. In dieser Ansicht finden Sie verschiedene Menüs, etwa eine Releasezusammenfassung, zugeordnete Arbeitselemente und Tests.

1. Wählen Sie **Commits** aus. In dieser Ansicht werden die der jeweiligen Bereitstellung zugeordneten Codecommits angezeigt. 

1. Wählen Sie **Protokolle** aus. Die Protokolle enthalten nützliche Informationen zum Bereitstellungsprozess. Sie können während und nach Bereitstellungen angezeigt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Azure App Service-Instanz und zugehörige Ressourcen löschen, wenn Sie sie nicht mehr benötigen. Verwenden Sie die Funktion **Löschen** auf dem DevOps Starter-Dashboard.

## <a name="next-steps"></a>Nächste Schritte

Beim Konfigurieren des CI/CD-Prozesses wurden automatisch Build- und Releasepipelines erstellt. Diese Build- und Releasepipelines können Sie den Anforderungen Ihres Teams anpassen. Weitere Informationen zur CI/CD-Pipeline finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
> [Anpassen von CD-Prozessen](/azure/devops/pipelines/release/define-multistage-release-process)
