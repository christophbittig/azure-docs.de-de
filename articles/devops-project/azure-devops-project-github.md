---
title: 'Tutorial: Erstellen einer CI/CD-Pipeline für vorhandenen Code mit Azure DevOps Starter'
description: Azure DevOps Starter erleichtert die ersten Schritte mit Azure. Mit wenigen schnellen Schritten können Sie mit DevOps Projects Ihren eigenen Code und das GitHub-Repository verwenden, um eine App in einem Azure-Dienst zu starten.
services: vsts
documentationcenter: vs-devops-build
ms.author: gwallace
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.custom: mvc
ms.openlocfilehash: 9600dcc2c9cbd150783ed1a14afb2b785ba5b054
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056338"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Erstellen einer CI/CD-Pipeline für GitHub-Repositorys mit Azure DevOps Starter

Azure DevOps Starter verfügt über einen vereinfachten Prozess zum Erstellen einer CI- und CD-Pipeline (Continuous Integration, Continuous Delivery) für Azure. Sie können Ihren vorhandenen Code und Ihr Git-Repository nutzen oder eine Beispielanwendung auswählen.

Dieses Modul umfasst Folgendes:

> [!div class="checklist"]
> * Erstellen einer CI/CD-Pipeline mithilfe von DevOps Starter
> * Konfigurieren des Zugriffs auf Ihr GitHub-Repository und Auswählen eines Frameworks
> * Konfigurieren von Azure DevOps und eines Azure-Abonnements 
> * Committen von Änderungen in GitHub und automatisches Bereitstellen dieser Änderungen in Azure
> * Überprüfen der CI/CD-Pipeline in Azure Pipelines
> * Bereinigen von Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Über [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) erhalten Sie ein kostenloses Abonnement.
* Zugriff auf ein GitHub- oder externes Git-Repository, das .NET, Java, PHP, Node.js, Python oder statischen Webcode enthält.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Mit Azure DevOps Starter wird eine CI/CD-Pipeline in Azure Pipelines erstellt. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation verwenden. Ferner werden mit Azure DevOps Starter Azure-Ressourcen im Azure-Abonnement Ihrer Wahl erstellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie in das Suchfeld **DevOps Starter** ein, und wählen sie die Option dann aus. Klicken Sie auf **Hinzufügen**, um einen neuen zu erstellen.

    ![Das DevOps Starter-Dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. Wählen Sie **Eigenen Code verwenden** und anschließend **Weiter** aus.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Konfigurieren des Zugriffs auf Ihr GitHub-Repository und Auswählen eines Frameworks

1. Wählen Sie entweder **GitHub** oder ein externes **Git**-Coderepository aus. Wählen Sie für dieses Tutorial die Option **GitHub** aus. Wenn Sie Azure erstmals Zugriff auf Ihr GitHub-Repository gewähren, ist unter Umständen eine GitHub-Authentifizierung erforderlich.

1. Wählen Sie ein **Repository** und einen **Branch** und dann **Weiter** aus.

1. Ändern Sie bei Verwendung von Docker-Containern die Einstellung von **Ist die App dockerisiert?** in **JA**. Behalten Sie für dieses Tutorial die Einstellung **NEIN** bei, und wählen Sie **Weiter** aus. Weitere Informationen zur Verwendung von Docker-Containern erhalten Sie, indem Sie mit dem Mauszeiger auf das Symbol **i** zeigen.

   ![Auswahl des Anwendungsframeworks im Dropdownmenü](_img/azure-devops-project-github/appframework.png)

1. Wählen Sie in den Dropdownmenüs eine **Anwendungsruntime** und ein **Anwendungsframework** und anschließend **Weiter** aus. Das Anwendungsframework bestimmt die Art des verfügbaren Bereitstellungsziels für den Azure-Dienst.

1. Wählen Sie einen **Azure-Dienst** zum Bereitstellen der Anwendung und anschließend **Weiter** aus.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurieren von Azure DevOps und eines Azure-Abonnements

1. Geben Sie unter **Projektname** einen Namen ein.

1. Erstellen Sie unter **Azure DevOps-Organisation** eine neue kostenlose Organisation, oder wählen Sie im Dropdownmenü eine vorhandene aus.

1. Wählen Sie Ihr Abonnement unter **Azure-Abonnement** aus, und geben Sie entweder einen Namen unter **Web-App** ein, oder verwenden Sie die Standardeinstellung. Wählen Sie einen **Standort** und anschließend **Fertig** aus. Nach wenigen Minuten wird die Bereitstellungsübersicht für DevOps Starter im Azure-Portal angezeigt.

1. Klicken Sie auf **Zu Ressource wechseln**, um das DevOps Starter-Dashboard aufzurufen. Heften Sie das **Projekt** in der oberen rechten Ecke in Ihrem Dashboard an, um den Schnellzugriff zu ermöglichen. Mit Azure DevOps Starter wird automatisch ein CI-Trigger für Build und Release konfiguriert. Ihr Code verbleibt in Ihrem GitHub-Repository oder einem anderen externen Repository, und in einem Repository unter **Azure DevOps-Organisation** wird eine Beispiel-App eingerichtet. Azure DevOps Starter führt den Buildvorgang durch und stellt die App in Azure bereit.

   ![Azure DevOps Projects-Dashboardansicht](_img/azure-devops-project-github/projectsdashboard.png)

1. Im Dashboard werden Ihr Coderepository, Ihre CI/CD-Pipeline und Ihre App in Azure angezeigt. Wählen Sie auf der rechten Seite unter „Azure-Ressourcen“ die Option **Durchsuchen** aus, um Ihre ausgeführte App anzuzeigen.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Committen von Änderungen in GitHub und automatisches Bereitstellen dieser Änderungen in Azure

Nun können Sie mit einem Team zusammen an Ihrer App arbeiten. Vom CI/CD-Prozess werden Ihre aktuellen Änderungen automatisch auf Ihrer Website bereitgestellt. Mit jeder Änderung am GitHub-Repository wird in Azure DevOps ein Build gestartet, und über eine CD-Pipeline wird eine Bereitstellung in Azure ausgeführt.

1. Klicken Sie auf dem DevOps Starter-Dashboard auf **Repositorys**. Ihr GitHub-Repository wird auf einer neuen Browserregisterkarte geöffnet. Ändern Sie Ihre Anwendung, und klicken Sie auf **Commit Changes** (Änderungen übernehmen).

1. Nach wenigen Augenblicken wird in Azure Pipelines ein Build gestartet. Sie können den Buildstatus auf dem DevOps Starter-Dashboard überwachen. Die Überwachung ist auch in Ihrer Azure DevOps-Organisation möglich, indem Sie auf dem DevOps Starter-Dashboard die Registerkarte **Buildpipelines** auswählen.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Überprüfen der CI/CD-Pipeline in Azure Pipelines

Mit Azure DevOps Starter wird automatisch eine CI/CD-Pipeline in Azure Pipelines konfiguriert. Untersuchen Sie die Pipeline, und passen Sie sie bei Bedarf an. Gehen Sie wie folgt vor, um sich mit den Build- und Releasepipelines vertraut zu machen:

1. Klicken Sie auf dem DevOps Starter-Dashboard auf **Buildpipelines**.

1. Auf der Seite **Azure Pipelines** werden ein Verlauf der neuesten Builds sowie der Status der einzelnen Builds angezeigt.

   ![Seite „Builds“ in Azure Pipelines](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. Oben rechts auf der Seite **Builds** können Sie die Option **Bearbeiten** auswählen, um den aktuellen Build zu ändern. Mit **Warteschlange** können Sie einen neuen Build hinzufügen, und mit der Schaltfläche mit den vertikalen Auslassungszeichen (**&#8942;**) können Sie ein Menü mit weiteren Optionen öffnen. Wählen Sie **Bearbeiten** aus.

1. Der Build führt verschiedene Aufgaben durch. Beispielsweise werden Quellen aus dem Repository abgerufen, Abhängigkeiten wiederhergestellt und Ausgaben für Bereitstellungen veröffentlicht. Ändern Sie auf der rechten Seite unter **Name** den Namen der Buildpipeline in einen aussagekräftigeren Namen. Wählen Sie **Speichern und in Warteschlange einreihen** und dann **Speichern** aus. Geben Sie einen Kommentar ein, und wählen Sie dann erneut **Speichern** aus.

   ![Azure DevOps-Seite „Builds“](_img/azure-devops-project-github/buildpage.png)

1. Wählen Sie die Registerkarte **Verlauf** aus, um ein Überwachungsprotokoll Ihrer kürzlich vorgenommenen Änderungen für den Build anzuzeigen.  An der Buildpipeline vorgenommene Änderungen werden von Azure DevOps nachverfolgt, sodass Sie verschiedene Versionen vergleichen können.

1. Wählen Sie die Registerkarte **Trigger** aus. Azure DevOps Projects erstellt automatisch einen CI-Trigger mit einigen Standardeinstellungen. Sie können Trigger festlegen, z. B. **Continuous Integration aktivieren**, um bei jedem Committen einer Codeänderung einen Buildvorgang durchzuführen. Sie können auch Trigger festlegen, um die Ausführung von Builds zu bestimmten Zeiten zu planen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellte Azure App Service-Instanz und die zugehörigen Ressourcen nicht mehr benötigen, können Sie sie löschen. Verwenden Sie dazu die Funktion **Löschen** auf dem DevOps Projects-Dashboard.

## <a name="next-steps"></a>Nächste Schritte

Als Sie den CI/CD-Prozess in diesem Tutorial konfiguriert haben, haben Sie in Azure DevOps Projects automatisch eine Build- und eine Releasepipeline erstellt. Diese Build- und Releasepipelines können Sie den Anforderungen Ihres Teams anpassen.

Weitere Informationen zur CI/CD-Pipeline finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
> [Define your multi-stage continuous deployment (CD) pipeline](/azure/devops/pipelines/release/define-multistage-release-process) (Festlegen Ihrer mehrstufigen CD-Pipeline (Continuous Deployment))

Weitere Informationen zur Anwendungsüberwachung finden Sie hier:
  
 > [!div class="nextstepaction"]
 > [Azure Monitor – Übersicht](../azure-monitor/overview.md)