---
title: Erstellen einer serverlosen Beispiel-App mit Visual Studio
description: Erstellen, Bereitstellen und Verwalten einer serverlosen Beispiel-App anhand einer Azure-Schnellstartvorlage mit Azure Logic Apps und Azure Functions in Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: df395fcffc1a7e675921f2ff397d488c301c703a
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363857"
---
# <a name="create-an-example-serverless-app-with-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Erstellen einer serverlosen Beispiel-App mit Azure Logic Apps und Azure Functions in Visual Studio

Mithilfe der Dienste und Funktionen in Azure (beispielsweise Azure Logic Apps und Azure Functions) können serverlose Cloud-Apps schnell erstellt, kompiliert und bereitgestellt werden. Wenn Sie Azure Logic Apps verwenden, können Sie schnell und einfach Workflows anhand von Low-Code- oder No-Code-Ansätzen erstellen, um die Orchestrierung kombinierter Aufgaben zu vereinfachen. Sie können verschiedene Dienste (cloudbasiert, lokal oder hybrid) integrieren, ohne diese Interaktionen zu programmieren, Glue Code zu verwalten oder neue APIs oder Spezifikationen zu erlernen. Wenn Sie Azure Functions verwenden, können Sie die Entwicklung mithilfe eines ereignisgesteuerten Modells beschleunigen. Sie können Trigger verwenden, die auf Ereignisse reagieren, indem Sie automatisch Ihren eigenen Code ausführen. Mithilfe von Bindungen lassen sich andere Dienste nahtlos integrieren.

In diesem Artikel erfahren Sie, wie Sie mithilfe einer Azure-Schnellstartvorlage eine serverlose Beispiel-App erstellen, die in mehrinstanzenfähigem Azure ausgeführt wird. Die Vorlage erstellt ein Azure-Ressourcengruppenprojekt, das eine Azure Resource Manager-Bereitstellungsvorlage enthält. Diese Vorlage definiert eine grundlegende Logik-App-Ressource, bei der ein vordefinierter Workflow einen Aufruf einer von Ihnen definierten Azure-Funktion enthält. Die Workflowdefinition umfasst die folgenden Komponenten:

* Einen Anforderungstrigger, der HTTP-Anforderungen empfängt. Um diesen Trigger zu starten, senden Sie eine Anforderung an die URL des Triggers.
* Eine Azure Functions-Aktion zum Aufruf einer Azure-Funktion, die Sie später definieren können.
* Eine Antwortaktion, die eine HTTP-Antwort mit dem Ergebnis der Funktion zurückgibt.

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Serverloses Computing: Einführung in serverlose Technologien](https://azure.microsoft.com/overview/serverless-computing/)
* [Informationen zu Azure Logic Apps](logic-apps-overview.md)
* [Informationen zu Azure Functions](../azure-functions/functions-overview.md)
* [Azure serverlos: Übersicht über das Entwickeln von cloudbasierten Apps und Lösungen mit Azure Logic Apps und Azure Functions](logic-apps-serverless-overview.md)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Laden Sie ggf. die folgenden Tools herunter, und installieren Sie sie:

  * [Visual Studio 2019, 2017 oder 2015 (Community Edition oder höher)](https://aka.ms/download-visual-studio). In dieser Schnellstartanleitung wird die kostenlose Version Visual Studio Community 2019 verwendet.

    > [!IMPORTANT]
    > Stellen Sie beim Installieren von Visual Studio 2019 oder 2017 sicher, dass Sie die Workload **Azure-Entwicklung** auswählen.

  * [Microsoft Azure SDK für .NET (ab Version 2.9.1).](https://azure.microsoft.com/downloads/) Weitere Informationen zu [Azure SDK für .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps-Tools für die verwendete Visual Studio-Version. Sie können sich über das [Installieren dieser Erweiterung aus Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions) informieren oder die jeweiligen Versionen der Azure Logic Apps-Tools aus dem Visual Studio Marketplace herunterladen:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)
    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)
    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    > [!IMPORTANT]
    > Achten Sie darauf, dass Sie Visual Studio nach Abschluss der Installation neu starten.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools), damit Sie Ihre Azure-Funktion lokal debuggen können. Weitere Informationen finden Sie unter [Arbeiten mit Azure Functions Core Tools](../azure-functions/functions-run-local.md).

* Internetzugriff während der Verwendung des eingebetteten Workflow-Designers.

  Für den Designer ist eine Internetverbindung zum Erstellen von Ressourcen in Azure und zum Lesen der Eigenschaften und Daten von [verwalteten Connectors](../connectors/managed.md) in Ihrem Workflow erforderlich. Wenn Sie beispielsweise den SQL-Connector verwenden, prüft der Designer Ihre Serverinstanz auf verfügbare standardmäßige und benutzerdefinierte Eigenschaften.

## <a name="create-a-resource-group-project"></a>Erstellen eines Ressourcengruppenprojekts

Erstellen Sie zunächst ein Azure-Ressourcengruppenprojekt als Container für Ihre serverlose App. In Azure ist eine *Ressourcengruppe* eine logische Sammlung, mit der Sie die Ressourcen für eine ganze App organisieren. Anschließend können Sie diese Ressourcen wie eine einzelne Ressource verwalten und bereitstellen. Für eine serverlose App in Azure umfasst eine Ressourcengruppe Azure Logic Apps- *und* Azure Functions-Ressourcen. Weitere Informationen finden Sie unter [Resource Manager-Terminologie](../azure-resource-manager/management/overview.md#terminology).

1. Öffnen Sie Visual Studio, und melden Sie sich nach Aufforderung mit Ihrem Azure-Konto an.

1. Wählen Sie im geöffneten Startfenster die Option **Neues Projekt erstellen** aus.

   ![Screenshot: Visual Studio-Startfenster mit ausgewählter Option „Neues Projekt erstellen“](./media/create-serverless-apps-visual-studio/start-window.png)

1. Wenn das Startfenster nicht geöffnet wird, wählen Sie im Menü **Datei** die Optionen **Neu** > **Projekt** aus.

   ![Screenshot: Geöffnetes Menü „Datei“ mit Menü „Neu“ und ausgewählter Option „Projekt“](./media/create-serverless-apps-visual-studio/create-new-project-visual-studio.png)

1. Wenn das Fenster **Neues Projekt erstellen** geöffnet ist, wählen Sie im Suchfeld die Projektvorlage **Azure-Ressourcengruppe** für Visual C# oder für Visual Basic aus. Klicken Sie abschließend auf **Weiter**. In diesem Beispiel wird C# verwendet.

   ![Screenshot: Fenster „Neues Projekt erstellen“ und Suchfeld mit „Ressourcengruppe“ sowie ausgewählter Projektvorlage „Azure-Ressourcengruppe“](./media/create-serverless-apps-visual-studio/start-window-find-project-template.png)

1. Nachdem das Fenster **Neues Projekt konfigurieren** geöffnet wurde, geben Sie Informationen zu Ihrem Projekt an, z. B. den Namen. Wählen Sie **Erstellen**, wenn Sie fertig sind.

   ![Screenshot: Fenster „Neues Projekt konfigurieren“ und Projektdetails](./media/create-serverless-apps-visual-studio/start-window-create-new-project-details.png)

1. Wählen Sie im Fenster **Azure-Vorlage auswählen** in der Liste **Vorlagen aus diesem Speicherort anzeigen** die Option **Azure-Schnellstart (github.com/Azure/azure-quickstart-templates)** als Vorlagenspeicherort aus.

1. Geben Sie im Suchfeld `logic-app-and-function-app`ein. Wählen Sie in den Ergebnissen die Vorlage namens **quickstarts\microsoft.logic\logic-app-and-function-app** aus. Wenn Sie fertig sind, wählen Sie **OK**.

   ![Screenshot: Fenster „Azure-Vorlage auswählen“ mit ausgewählter Option „Azure-Schnellstart“ als Vorlagenspeicherort und ausgewählter Option „quickstarts\microsoft.logic\logic-app-and-function-app“](./media/create-serverless-apps-visual-studio/select-template.png)

   Visual Studio erstellt Ihr Ressourcengruppenprojekt, einschließlich des Projektmappencontainers für Ihr Projekt.

   ![Screenshot: Erstelltes Projekt und Projektmappe](./media/create-serverless-apps-visual-studio/create-serverless-solution.png)

1. Stellen Sie als Nächstes Ihre Projektmappe in Azure bereit.

   > [!IMPORTANT]
   > Stellen Sie sicher, dass der Bereitstellungsschritt vollständig durchgeführt wird. Andernfalls können Sie den Workflow Ihrer Logik-App nicht mithilfe des Designers in Visual Studio öffnen, überprüfen und bearbeiten.

## <a name="deploy-your-solution"></a>Bereitstellen der Lösung

Bevor Sie Ihre Logik-App mit dem Designer in Visual Studio öffnen können, müssen Sie Ihre App in Azure bereitstellen. Der Designer kann anschließend Verbindungen mit den Diensten und Ressourcen herstellen, die im Workflow Ihrer Logik-App verwendet werden.

1. Wählen Sie im Projektmappen-Explorer das Kontextmenü Ihres Ressourcenprojekts **Bereitstellen** > **Neu** aus.

   ![Screenshot: Projektmappen-Explorer mit geöffnetem Projektkontextmenü, geöffnetem Menü „Bereitstellen“ und ausgewählter Option „Neu“](./media/create-serverless-apps-visual-studio/deploy.png)

1. Nachdem das Fenster **In Ressourcengruppe bereitstellen** geöffnet wurde, führen Sie die folgenden Schritte aus, um die Bereitstellungsinformationen anzugeben:

   1. Nachdem das Fenster Ihr aktuelles Abonnement erkannt hat, bestätigen Sie Ihr Azure-Abonnement, oder wählen Sie bei Bedarf ein anderes Abonnement aus.

   1. Erstellen Sie eine neue Ressourcengruppe in Azure. Wählen Sie in der Liste **Ressourcengruppe** die Option **Neu erstellen** aus.

      ![Screenshot: Fenster „In Ressourcengruppe bereitstellen“ mit ausgewählter Option „Neu erstellen“](./media/create-serverless-apps-visual-studio/create-resource-group-start.png)

   1. Geben Sie im Fenster **Ressourcengruppe erstellen** die folgenden Informationen an:

      | Eigenschaft | BESCHREIBUNG |
      |----------|-------------|
      | Ressourcengruppenname | Der Name für Ihre Ressourcengruppe |
      | Ressourcengruppenstandort | Die Region des Azure-Rechenzentrums, in dem Ihre Logik-App gehostet werden soll |
      |||

      In diesem Beispiel wird eine Ressourcengruppe in der Region „USA, Westen“ erstellt.

      ![Screenshot: Fenster „Ressourcengruppe erstellen“ mit Informationen zur neuen Ressourcengruppe](./media/create-serverless-apps-visual-studio/create-resource-group.png)

   1. Schließen Sie die Erstellung und Bereitstellung Ihrer Projektmappe ab. Beispiel:

      ![Bereitstellungseinstellungen](./media/create-serverless-apps-visual-studio/deploy-to-resource-group.png)

1. Geben Sie im Fenster **Parameter bearbeiten** die Ressourcennamen ein, die für die bereitgestellte Logik-App-Ressource und die Funktions-App-Ressource verwendet werden sollen, und speichern Sie Ihre Änderungen.

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie global eindeutige Namen für Ihre Logik-App und Funktions-App verwenden.

   ![Screenshot: Fenster „Parameter bearbeiten“ mit Namen für Ihre Logik-App-Ressource und Ihre Funktions-App-Ressource](./media/create-serverless-apps-visual-studio/logic-function-app-name-parameters.png)

   Wenn Visual Studio mit der Bereitstellung in Ihrer Ressourcengruppe beginnt, wird der Bereitstellungsstatus Ihrer Projektmappe im Visual Studio-Fenster **Ausgabe** angezeigt. Nach Abschluss der Bereitstellung ist Ihre Logik-App im Azure-Portal live.

## <a name="open-and-edit-your-deployed-logic-app"></a>Öffnen und Bearbeiten Ihrer bereitgestellten Logik-App

1. Wählen Sie im Projektmappen-Explorer im Kontextmenü der Datei **azuredeploy.json** die Option **Mit Logik-App-Designer öffnen** aus.

   ![Screenshot: Kontextmenü für „azuredeploy.json“ mit ausgewählter Option „Mit Logik-App-Designer öffnen“](./media/create-serverless-apps-visual-studio/open-designer.png)

   > [!TIP]
   > Sollte dieser Befehl in Visual Studio 2019 nicht zur Verfügung stehen, überprüfen Sie, ob Visual Studio über die neuesten Updates verfügt.

1. Nachdem der Workflow-Designer geöffnet wurde, können Sie den Workflow bearbeiten oder Schritte hinzufügen. Speichern Sie zum Abschluss Ihre Änderungen in der Datei **azuredeploy.json**.

   ![Screenshot: Logik-App-Workflow im Designer](./media/create-serverless-apps-visual-studio/opened-logic-app-workflow.png)

## <a name="create-an-azure-functions-project"></a>Erstellen eines Azure Functions-Projekts

Um eine C#-basierte Azure-Funktion innerhalb Ihrer Projektmappe zu erstellen, legen Sie ein C#-Klassenbibliotheksprojekt an, indem Sie die Schritte unter [Schnellstart: Erstellen Ihrer ersten C#-Funktion in Azure mithilfe von Visual Studio](../azure-functions/functions-create-your-first-function-visual-studio.md) befolgen. Um andernfalls ein Azure Functions-Projekt und eine Funktion mithilfe anderer Sprachen zu erstellen, gehen Sie gemäß den folgenden Schnellstartanleitungen vor:

* [Schnellstart: Erstellen einer Java-Funktion in Azure mit Visual Studio Code](../azure-functions/create-first-function-vs-code-java.md)
* [Schnellstart: Erstellen einer JavaScript-Funktion in Azure mit Visual Studio Code](../azure-functions/create-first-function-vs-code-node.md)
* [Schnellstart: Erstellen einer PowerShell-Funktion in Azure mit Visual Studio Code](../azure-functions/create-first-function-vs-code-powershell.md)
* [Schnellstart: Erstellen einer Python-Funktion in Azure mit Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md)
* [Schnellstart: Erstellen einer TypeScript-Funktion in Azure mit Visual Studio Code](../azure-functions/create-first-function-vs-code-typescript.md)

## <a name="deploy-functions-from-visual-studio"></a>Bereitstellen von Funktionen aus Visual Studio

Die Bereitstellungsvorlage in Ihrer Projektmappe kann alle in Ihrer Projektmappe enthaltenen Azure-Funktionen über das Git-Repository bereitstellen, das durch Variablen in der Datei **azuredeploy.json** angegeben wird. Wenn Sie Ihr Functions-Projekt in Ihrer Projektmappe erstellen, können Sie es in die Git-Quellcodeverwaltung (beispielsweise in GitHub oder Azure DevOps) einchecken und dann die Variable `repo` in der Datei **azuredeploy.json** so ändern, dass Ihre Azure-Funktion in der Vorlage bereitgestellt wird.

## <a name="manage-logic-apps-and-view-run-history"></a>Verwalten von Logik-Apps und Anzeigen des Ausführungsverlaufs

Wenn Sie bereits Logik-App-Ressourcen in Azure bereitstellen, können Sie den Ausführungsverlauf bearbeiten, verwalten und anzeigen sowie diese Apps aus Visual Studio heraus deaktivieren. Weitere Informationen finden Sie unter [Verwalten von Logik-Apps mit Visual Studio](manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Nächste Schritte

* Ein weiteres Beispiel mit Azure Logic Apps und Azure Functions finden Sie unter [Automatisieren von Aufgaben zur Verarbeitung von E-Mails mithilfe von Azure Logic Apps, Azure Functions und Azure Storage](tutorial-process-email-attachments-workflow.md).