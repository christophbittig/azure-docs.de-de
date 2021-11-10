---
title: 'Tutorial: Bereitstellen einer ASP.NET-App auf virtuellen Azure-Computern mithilfe von Azure DevOps Starter'
description: DevOps Starter erleichtert die ersten Schritte mit Azure sowie die Bereitstellung Ihrer ASP.NET-App auf virtuellen Azure-Computern.
ms.author: gwallace
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.openlocfilehash: 9bf6b19722ac78d15c3a862b43976294dc75747c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055736"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Tutorial: Bereitstellen einer ASP.NET-App auf virtuellen Azure-Computern mithilfe von Azure DevOps Starter

Azure DevOps Starter bietet eine vereinfachte Umgebung, in der Sie Ihren vorhandenen Code und Ihr Git-Repository verwenden oder eine Beispielanwendung auswählen können, um eine Continuous Integration- und Continuous Delivery-Pipeline (CI/CD) für Azure zu erstellen. 

DevOps Starter ermöglicht außerdem:
* Automatisches Erstellen von Azure-Ressourcen, etwa eines neuen virtuellen Computers
* Erstellen und Konfigurieren einer Releasepipeline in Azure DevOps, die eine Buildpipeline für CI enthält
* Einrichten einer Releasepipeline für CD 
* Erstellen einer Azure Application Insights-Ressource für die Überwachung

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen Ihrer ASP.NET-App mit DevOps Starter
> * Konfigurieren von Azure DevOps und eines Azure-Abonnements 
> * Überprüfen der CI-Pipeline
> * Überprüfen der CD-Pipeline
> * Committen von Änderungen in Azure Repos und automatisches Bereitstellen dieser Änderungen in Azure
> * Konfigurieren der Azure Application Insights-Überwachung
> * Bereinigen von Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Über [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) erhalten Sie ein kostenloses Abonnement.

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>Bereitstellen Ihrer ASP.NET-App mit DevOps Starter

Mit DevOps Starter wird eine CI/CD-Pipeline in Azure Pipelines erstellt. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation verwenden. Ferner werden mit DevOps Projects Azure-Ressourcen wie etwa virtuelle Computer im Azure-Abonnement Ihrer Wahl erstellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie in das Suchfeld **DevOps Starter** ein, und wählen sie die Option dann aus. Klicken Sie auf **Hinzufügen**, um einen neuen zu erstellen.

    ![Das DevOps Starter-Dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Wählen Sie **.NET** und anschließend **Weiter** aus.

1. Wählen Sie für **Anwendungsframework auswählen** die Option **ASP.NET** und anschließend **Weiter** aus. Das Anwendungsframework, das Sie in einem vorherigen Schritt ausgewählt haben, bestimmt den Typ des hier verfügbaren Bereitstellungsziels für den Azure-Dienst. 

1. Wählen Sie den virtuellen Computer und anschließend **Weiter** aus.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurieren von Azure DevOps und eines Azure-Abonnements

1. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation auswählen. 

1. Geben Sie einen Namen für Ihr Azure DevOps-Projekt ein. 

1. Wählen Sie Ihre Azure-Abonnementsdienste aus. Optional können Sie **Ändern** auswählen und anschließend weitere Konfigurationsdetails wie etwa den Speicherort der Azure-Ressourcen eingeben.
 
1. Geben Sie den Namen des virtuellen Computers, den Benutzernamen und das Kennwort für Ihre neue Azure-VM-Ressource ein, und wählen Sie anschließend **Fertig** aus. Der virtuelle Azure-Computer ist nach wenigen Minuten bereit. Eine ASP.NET-Beispielanwendung wird in einem Repository in Ihrer Azure DevOps-Organisation eingerichtet, ein Build und ein Release werden ausgeführt, und Ihre Anwendung wird auf dem neu erstellten virtuellen Azure-Computer bereitgestellt. 

   Anschließend wird das DevOps Starter-Dashboard im Azure-Portal angezeigt. Sie können auch direkt über **Alle Ressourcen** im Azure-Portal zum Dashboard navigieren. 

   Das Dashboard bietet Einblick in Ihr Azure DevOps-Coderepository, in Ihre CI/CD-Pipeline und in Ihre aktive Anwendung in Azure.   

   ![Dashboardansicht](_img/azure-devops-project-vms/vm-starter-dashboard.png)

Mit DevOps Starter werden automatisch ein CI-Build und ein Releasetrigger konfiguriert, durch die Codeänderungen im Repository bereitgestellt werden. Sie können zusätzliche Optionen in Azure DevOps konfigurieren. Wählen Sie **Durchsuchen** aus, um Ihre ausgeführte Anwendung anzuzeigen.
    
## <a name="examine-the-ci-pipeline"></a>Überprüfen der CI-Pipeline
 
Mit DevOps Starter wird automatisch eine CI/CD-Pipeline in Azure Pipelines konfiguriert. Sie können die Pipeline untersuchen und anpassen. Gehen Sie wie folgt vor, um sich mit der Buildpipeline vertraut zu machen:

1. Wählen Sie oben im DevOps Starter-Dashboard die Option **Buildpipelines** aus. Auf einer Browserregisterkarte wird die Buildpipeline für Ihr neues Projekt angezeigt.

1. Zeigen Sie auf das Feld **Status**, und wählen Sie dann die Auslassungspunkte (...) aus. In einem Menü werden verschiedene Optionen angezeigt, etwa zum Einreihen eines neuen Builds in die Warteschlange, zum Anhalten eines Builds und zum Bearbeiten der Buildpipeline.

1. Wählen Sie **Bearbeiten** aus.

1. In diesem Bereich können Sie sich die verschiedenen Aufgaben ansehen, die Sie für Ihre Buildpipeline ausführen können. Vom Build werden verschiedene Aufgaben durchgeführt. Beispielsweise werden Quellen aus dem Git-Repository abgerufen, Abhängigkeiten wiederhergestellt und für Bereitstellungen verwendete Ausgaben veröffentlicht.

1. Wählen Sie oben in der Buildpipeline den Buildpipelinenamen aus.

1. Ersetzen Sie den Namen Ihrer Buildpipeline durch einen aussagekräftigeren Namen, und wählen Sie **Speichern und in Warteschlange einreihen** und dann **Speichern** aus.

1. Wählen Sie unter dem Buildpipelinenamen **Verlauf** aus. In diesem Bereich wird ein Überwachungsprotokoll mit den letzten Änderungen für den Build angezeigt. An der Buildpipeline vorgenommene Änderungen werden von Azure DevOps nachverfolgt, sodass Sie verschiedene Versionen vergleichen können.

1. Wählen Sie **Trigger** aus. Mit DevOps Starter wird automatisch ein CI-Trigger erstellt, und mit jedem für das Repository ausgeführten Commit wird ein neuer Build gestartet. Optional können Sie Branches in den CI-Prozess einbeziehen oder davon ausschließen.

1. Wählen Sie **Aufbewahrung** aus. Abhängig vom Szenario können Sie Richtlinien zum Aufbewahren oder Entfernen einer bestimmten Anzahl von Builds festlegen.

## <a name="examine-the-cd-pipeline"></a>Überprüfen der CD-Pipeline

Mit DevOps Starter werden die erforderlichen Schritte zum Bereitstellen über Ihre Azure DevOps-Organisation in Ihrem Azure-Abonnement automatisch erstellt und konfiguriert. Diese Schritte umfassen die Konfiguration einer Azure-Dienstverbindung zur Authentifizierung von Azure DevOps für Ihr Azure-Abonnement. Bei der Automatisierung wird außerdem eine CD-Pipeline erstellt, über die CD für den virtuellen Azure-Computer bereitgestellt wird. Gehen Sie wie folgt vor, um weitere Informationen zur Azure DevOps-CD-Pipeline zu erhalten:

1. Wählen Sie **Build und Release** und anschließend **Releases** aus.  DevOps Starter erstellt eine Releasepipeline zum Verwalten von Bereitstellungen in Azure.

1. Wählen Sie neben Ihrer Releasepipeline die Auslassungspunkte (...) und anschließend **Bearbeiten** aus. Die Releasepipeline enthält eine *Pipeline*, die den Releaseprozess definiert.

1. Wählen Sie unter **Artefakte** die Option **Ablegen** aus. Die in den vorherigen Schritten untersuchte Buildpipeline erzeugt die für das Artefakt verwendete Ausgabe. 

1. Wählen Sie neben dem Symbol **Ablegen** die Option **Continuous Deployment-Trigger** aus. Diese Releasepipeline enthält einen aktivierten CD-Trigger. Jedes Mal, wenn ein neues Buildartefakt verfügbar ist, wird von diesem CD-Trigger eine Bereitstellung ausgeführt. Optional können Sie den Trigger deaktivieren, sodass Ihre Bereitstellungen manuell ausgeführt werden müssen. 

1. Wählen Sie auf der linken Seite **Aufgaben** und dann Ihre Umgebung aus. Bei Aufgaben handelt es sich um die Aktivitäten, die beim Bereitstellungsprozess ausgeführt werden. Sie sind in Phasen gruppiert. Dieses Releasepipeline besteht aus zwei Phasen:

    - Die erste Phase enthält die Aufgabe „Bereitstellung einer Azure-Ressourcengruppe“, die zwei Aktionen ausführt:
     
        - Konfigurieren des virtuellen Computers für die Bereitstellung
        -   Hinzufügen des neuen virtuellen Computers zu einer Azure DevOps-Bereitstellungsgruppe Mit der VM-Bereitstellungsgruppe in Azure DevOps werden logische Gruppen von Bereitstellungszielcomputern verwaltet.
     
    - In der zweiten Phase erstellt die Aufgabe „IIS-Web-App verwalten“ eine IIS-Website auf dem virtuellen Computer. Zum Bereitstellen der Website wird eine zweite Aufgabe vom Typ „IIS-Web-App-Bereitstellung“ erstellt.

1. Wählen Sie auf der rechten Seite **Releases anzeigen** aus, um einen Releaseverlauf anzuzeigen.

1. Wählen Sie neben einem Release die Auslassungspunkte (...) und anschließend **Öffnen** aus. Sie können sich verschiedene Menüs ansehen, etwa eine Releasezusammenfassung, zugeordnete Arbeitselemente und Tests.

1. Wählen Sie **Commits** aus. In dieser Ansicht werden die dieser Bereitstellung zugeordneten Codecommits angezeigt. Vergleichen Sie Releases, um die Commitunterschiede zwischen den einzelnen Bereitstellungen anzuzeigen.

1. Wählen Sie **Protokolle** aus. Die Protokolle enthalten nützliche Informationen zum Bereitstellungsprozess. Sie können während und nach Bereitstellungen angezeigt werden.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Committen von Änderungen in Azure Repos und automatisches Bereitstellen dieser Änderungen in Azure 

Nun können Sie mithilfe eines CI/CD-Prozesses, mit dem Ihre aktuelle Arbeit auf Ihrer Website automatisch bereitgestellt wird, mit einem Team an Ihrer App zusammenarbeiten. Mit jeder Änderung am GitHub-Repository wird in Azure DevOps ein Build gestartet, und durch eine CD-Pipeline wird eine Bereitstellung in Azure ausgeführt. Führen Sie die Schritte in diesem Abschnitt aus, oder nutzen Sie eine andere Methode zum Committen Ihrer Änderungen in Ihrem Repository. Mit den Codeänderungen wird der CI/CD-Prozess initiiert. Außerdem werden Ihre Änderungen an der IIS-Website automatisch auf dem virtuellen Azure-Computer bereitgestellt.

1. Wählen Sie im linken Bereich **Code** aus, und navigieren Sie anschließend zu Ihrem Repository.

1. Navigieren Sie zum Verzeichnis *Views\Home*. Wählen Sie anschließend die Auslassungspunkte neben der Datei *Index.cshtml* und dann **Bearbeiten** aus.

1. Nehmen Sie eine Änderung an der Datei vor. Fügen Sie beispielsweise in einem der div-Tags Text hinzu. 

1. Wählen Sie oben rechts **Committen** und dann erneut **Committen** aus, um Ihre Änderung per Push zu übertragen. Kurz danach wird in Azure DevOps ein Build gestartet und zur Bereitstellung der Änderungen ein Release ausgeführt. Überwachen Sie den Buildstatus auf dem DevOps Starter-Dashboard oder im Browser mit Ihrer Azure DevOps-Organisation.

1. Aktualisieren Sie nach Abschluss des Release Ihre Anwendung, um Ihre Änderungen zu überprüfen.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurieren der Azure Application Insights-Überwachung

Mithilfe von Azure Application Insights können Sie die Leistung und Nutzung Ihrer Anwendung ganz einfach überwachen. Mit DevOps Starter wird für Ihre Anwendung automatisch eine Application Insights-Ressource konfiguriert. Sie können Warnungen und Überwachungsfunktionen je nach Bedarf weiter konfigurieren.

1. Navigieren Sie im Azure-Portal zum DevOps Starter-Dashboard. 

1. Wählen Sie unten rechts den Link **Application Insights** für Ihre App aus. Der Bereich **Application Insights** wird geöffnet. Diese Ansicht enthält Informationen zur Nutzungs-, Leistungs- und Verfügbarkeitsüberwachung für Ihre App.

   ![Der Bereich „Application Insights“](_img/azure-devops-project-github/appinsights.png) 

1. Wählen Sie **Zeitbereich** und dann **Letzte Stunde** aus. Wählen Sie zum Filtern der Ergebnisse **Aktualisieren** aus. Sie können nun alle Aktivitäten der letzten 60 Minuten anzeigen. 
    
1. Wählen Sie zum Schließen des Zeitbereichs **x** aus.

1. Wählen Sie **Warnungen** und anschließend **Metrikwarnung hinzufügen** aus. 

1. Geben Sie einen Namen für die Warnung ein.

1. Sehen Sie sich in der Dropdownliste **Metrik** die verschiedenen Warnungsmetriken an. Für die Standardwarnung gilt eine **Serverantwortzeit, die größer als 1 Sekunde** ist. Sie können problemlos eine Vielzahl von Warnungen konfigurieren und dadurch die Überwachungsfunktionen Ihrer App verbessern.

1. Aktivieren Sie das Kontrollkästchen für **Notify via Email owners, contributors, and readers** (Besitzer, Mitwirkende und Leser über E-Mail informieren). Optional können Sie durch die Ausführung einer Logik-App zusätzliche Aktionen ausführen, wenn eine Warnung angezeigt wird.

1. Wählen Sie **OK** , um die Warnung zu erstellen. Nach wenigen Augenblicken wird die Warnung auf dem Dashboard als aktiv angezeigt. 

1. Verlassen Sie den Bereich **Warnung**, und navigieren Sie zurück zum Blatt **Application Insights**.

1. Wählen Sie **Verfügbarkeit** und anschließend **Test hinzufügen** aus. 

1. Geben Sie einen Testnamen ein, und wählen Sie dann **Erstellen** aus. Ein einfacher Ping-Test wird erstellt, um die Verfügbarkeit Ihrer Anwendung zu überprüfen. Nach wenigen Minuten sind die Testergebnisse verfügbar, und das Application Insights-Dashboard zeigt einen Verfügbarkeitsstatus an.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beim Durchführen von Tests können Sie Gebühren vermeiden, indem Sie die Ressourcen bereinigen. Sie können den in diesem Tutorial erstellten virtuellen Computer und zugehörige Ressourcen löschen, wenn Sie sie nicht mehr benötigen. Verwenden Sie dazu die Funktion **Löschen** auf dem DevOps Starter-Dashboard. 

> [!IMPORTANT]
> Mit dem folgenden Verfahren werden Ressourcen endgültig gelöscht. Mit der Funktion *Löschen* werden die Daten in Azure und Azure DevOps gelöscht, die vom Projekt in DevOps Starter erstellt wurden. Diese Daten können anschließend nicht wiederhergestellt werden. Verwenden Sie dieses Verfahren nur, nachdem Sie die Anweisungen sorgfältig gelesen haben.

1. Navigieren Sie im Azure-Portal zum DevOps Starter-Dashboard.
1. Wählen Sie oben rechts **Löschen** aus. 
1. Wählen Sie an der Eingabeaufforderung **Ja** aus, um die Ressourcen *endgültig zu löschen*.

Diese Build- und Releasepipelines können Sie optional an die Anforderungen Ihres Teams anpassen. Sie können dieses CI/CD-Muster auch als Vorlage für Ihre anderen Pipelines verwenden. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen Ihrer ASP.NET-App mit DevOps Starter
> * Konfigurieren von Azure DevOps und eines Azure-Abonnements 
> * Überprüfen der CI-Pipeline
> * Überprüfen der CD-Pipeline
> * Committen von Änderungen in Azure Repos und automatisches Bereitstellen dieser Änderungen in Azure
> * Konfigurieren der Azure Application Insights-Überwachung
> * Bereinigen von Ressourcen

Weitere Informationen zur CI/CD-Pipeline finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
> [Define your multi-stage continuous deployment (CD) pipeline](/azure/devops/pipelines/release/define-multistage-release-process) (Festlegen Ihrer mehrstufigen CD-Pipeline (Continuous Deployment))