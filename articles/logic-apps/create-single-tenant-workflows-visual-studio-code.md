---
title: Erstellen von Workflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) in Visual Studio Code
description: Erstellen Sie automatisierte Workflows zur Integration von Apps, Daten, Diensten und Systemen mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) in Visual Studio Code.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b71edfc5f57779bc96b165f8bdef2436ec9d16b3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552563"
---
# <a name="create-an-integration-workflow-with-single-tenant-azure-logic-apps-standard-in-visual-studio-code"></a>Erstellen von Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) in Visual Studio Code

In diesem Artikel wird anhand eines Beispiels gezeigt, wie Sie in Visual Studio Code mit der Erweiterung **Azure Logik-App (Standard)** einen automatisierten Integrationsworkflow erstellen, der in der Azure Logic Apps-Umgebung mit *Einzelmandanteninstanz* ausgeführt wird. Die Logik-App, die Sie mit dieser Erweiterung erstellen, basiert auf dem **Logik-App-Ressourcentyp (Standard)** , der die folgenden Funktionen bietet:

* Sie können Ihre Logik-App-Workflows in der Visual Studio Code-Entwicklungsumgebung lokal ausführen und testen.

* Ihre Logik-App kann über mehrere [zustandsbehaftete und zustandslose](single-tenant-overview-compare.md#stateful-stateless) Workflows verfügen.

* Workflows in derselben Logik-App und demselben Mandanten werden im gleichen Prozess wie die Azure Logic Apps-Runtime ausgeführt, sodass sie dieselben Ressourcen gemeinsam nutzen und eine bessere Leistung bieten.

* Sie können die Bereitstellung des Ressourcentyps **Logik-App (Standard)** in der Azure Logic Apps-Umgebung mit Einzelmandanteninstanz oder an einer beliebigen Stelle (darunter Container) ausführen, die Azure Functions aufgrund der containerisierten Runtime von Azure Logic Apps ausführen kann.

Weitere Informationen zum Einzelmandantenangebot von Azure Logic Apps finden Sie unter [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](single-tenant-overview-compare.md).

Obwohl der Beispielworkflow cloudbasiert ist und nur zwei Schritte umfasst, können Sie Workflows aus Hunderten von Vorgängen erstellen, die eine Vielzahl von Apps, Daten, Diensten und Systemen über Cloud-, lokale und Hybridumgebungen hinweg verbinden können. Der Beispielworkflow beginnt mit dem integrierten Anforderungstrigger und folgt mit einer Office 365 Outlook-Aktion. Der Trigger erstellt einen aufrufbaren Endpunkt für den Workflow und wartet auf eine eingehende HTTPS-Anforderung einer beliebigen aufrufenden Funktion. Wenn der Trigger eine Anforderung erhält und auslöst, wird die nächste Aktion ausgeführt, indem eine E-Mail an die angegebene E-Mail-Adresse zusammen mit den ausgewählten Ausgaben des Triggers gesendet wird.

> [!TIP]
> Wenn Sie nicht über ein Office 365-Konto verfügen, können Sie jede andere verfügbare Aktion verwenden, die Nachrichten von Ihrem E-Mail-Konto senden kann, z. B. Outlook.com.
>
> Führen Sie die Schritte unter [Erstellen von Integrationsworkflows mithilfe von Azure Logic Apps mit einzelnem Mandanten und dem Azure-Portal](create-single-tenant-workflows-azure-portal.md) aus, um diesen Beispielworkflow stattdessen mithilfe des Azure-Portals zu erstellen. 
> Beide Optionen bieten die Möglichkeit, Logik-App-Workflows in denselben Arten von Umgebungen zu entwickeln, auszuführen und bereitzustellen. 
> Mit Visual Studio Code können Sie Workflows jedoch *lokal* in Ihrer Entwicklungsumgebung entwickeln, testen und ausführen.

![Screenshot, der Visual Studio Code, Logik-App-Projekt und Workflow zeigt.](./media/create-single-tenant-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Im Weiteren führen Sie diese allgemeinen Aufgaben aus:

* Erstellen Sie ein Projekt für Ihre Logik-App und einen leeren [*zustandsbehafteten* Workflow](single-tenant-overview-compare.md#stateful-stateless).
* Hinzufügen eines Triggers und einer Aktion.
* Lokales Ausführen, Testen, Debuggen und Überprüfen des Ausführungsverlaufs.
* Suchen von Domänennamensdetails für den Firewallzugriff.
* Bereitstellen in Azure, was die optionale Aktivierung von Application Insights beinhaltet.
* Verwalten Ihrer bereitgestellten Logik-App in Visual Studio Code und im Azure-Portal.
* Aktivieren des Ausführungsverlaufs für zustandslose Workflows.
* Aktivieren oder Öffnen von Application Insights nach der Bereitstellung.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="access-and-connectivity"></a>Zugriff und Konnektivität

* Zugriff auf das Internet, damit Sie die Anforderungen herunterladen, von Visual Studio Code aus eine Verbindung mit Ihrem Azure-Konto herstellen und aus Visual Studio Code in Azure veröffentlichen können.

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Um denselben Beispielworkflow in diesem Artikel zu erstellen, benötigen Sie ein Office 365 Outlook-E-Mail-Konto, das ein Geschäfts-, Schul- oder Unikonto von Microsoft für die Anmeldung verwendet.

  Wenn Sie einen [anderen E-Mail-Connector](/connectors/connector-reference/connector-reference-logicapps-connectors) wählen, z. B. Outlook.com, können Sie dem Beispiel trotzdem folgen, und die allgemeinen Schritte sind dieselben. Ihre Optionen können sich jedoch in einigen Punkten unterscheiden. Wenn Sie beispielsweise den Outlook.com-Connector verwenden, verwenden Sie stattdessen Ihr persönliches Microsoft-Konto, um sich anzumelden.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Speicheranforderungen

Für die lokale Entwicklung in Visual Studio Code müssen Sie einen lokalen Datenspeicher für Ihr Logik-App-Projekt und Ihre Workflows einrichten, die für die Ausführung in Ihrer lokalen Entwicklungsumgebung verwendet werden. Sie können den Azurite-Speicheremulator als lokalen Datenspeicher verwenden und ausführen.

1. Laden Sie [Azurite 3.12.0 oder höher](https://www.npmjs.com/package/azurite) herunter, und installieren Sie es.
1. Bevor Sie Ihre Logik-App ausführen, müssen Sie den Emulator starten.

Weitere Informationen finden Sie in der [Azurite-Dokumentation](https://github.com/Azure/Azurite#azurite-v3).

### <a name="tools"></a>Tools

* [Visual Studio Code](https://code.visualstudio.com/) (kostenlos). Laden Sie darüber hinaus diese Tools für Visual Studio Code herunter, und installieren Sie sie, falls sie noch nicht vorhanden sind:

  * [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), die eine einzelne allgemeine Azure-Anmeldungs- und -Abonnementfilterungserfahrung für alle anderen Azure-Erweiterungen in Visual Studio Code bereitstellt.

  * [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), mit der die F5-Funktionalität in die Lage versetzt wird, Ihre Logik-App auszuführen.

  * [Azure Functions Core Tools – Version 3.x](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3904) über die MSI-Version (Microsoft Installer), die `func-cli-X.X.XXXX-x*.msi` lautet. Installieren Sie nicht die Version 4.x, die nicht unterstützt wird und nicht funktioniert.

    Diese Tools enthalten eine Version derselben Runtime, auf der die Azure Functions-Runtime basiert, die die Erweiterung „Azure Logic Apps (Standard)“ in Visual Studio Code verwendet.

    > [!IMPORTANT]
    > Wenn Sie über eine Installation verfügen, die älter als diese Versionen ist, deinstallieren Sie diese Version zuerst, oder stellen Sie sicher, dass die PATH-Umgebungsvariable auf die Version verweist, die Sie herunterladen und installieren.

  * [Azure Logic Apps (Standard)-Erweiterung für Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167).

    > [!IMPORTANT]
    > Projekte, die mit früheren Vorschauerweiterungen erstellt wurden, funktionieren nicht mehr. Deinstallieren Sie zum Fortfahren alle früheren Versionen, und erstellen Sie Ihre Logik-App-Projekte neu.

    Führen Sie zum Installieren der Erweiterung **Azure Logic Apps (Standard)** die folgenden Schritte aus:

    1. Wählen Sie in Visual Studio Code in der linken Symbolleiste **Erweiterungen** aus.

    1. Geben Sie in das Suchfeld für Erweiterungen `azure logic apps standard` ein. Wählen Sie in der Ergebnisliste **Azure Logic Apps (Standard)** **>** **Installieren** aus.

       Nach Abschluss der Installation wird die Erweiterung in der Liste **Erweiterungen: Installiert** angezeigt.

       ![Screenshot: Visual Studio Code mit installierter Erweiterung „Azure Logic Apps (Standard)“](./media/create-single-tenant-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Versuchen Sie, Visual Studio Code neu zu starten, falls die Erweiterung nicht in der Installationsliste angezeigt wird.

    Derzeit können Sie sowohl die Erweiterungen „Verbrauch“ (mehrere Mandanten) als auch „Standard“ (einzelner Mandant) gleichzeitig installieren. Die Entwicklungserfahrungen unterscheiden sich in mancher Hinsicht voneinander, aber Ihr Azure-Abonnement kann die Logik-App-Typen „Standard“ und „Verbrauch“ enthalten. Visual Studio Code zeigt alle bereitgestellten Logik-Apps in Ihrem Azure-Abonnement an, organisiert Ihre Apps jedoch unter jeder Erweiterung, **Azure Logic Apps (Verbrauch)** und **Azure Logic Apps (Standard)** .

* Installieren Sie die [Node.js-Versionen 10.x.x, 11.x.x oder 12.x.x](https://nodejs.org/en/download/releases/), um die [„Inlinecodevorgänge“-Aktion](../logic-apps/logic-apps-add-run-inline-code.md) mit Ausführung von JavaScript zu verwenden.

  > [!TIP]
  > Laden Sie für Windows die MSI-Version herunter. Falls Sie stattdessen die ZIP-Version nutzen, müssen Sie Node.js manuell verfügbar machen, indem Sie eine PATH-Umgebungsvariable für Ihr Betriebssystem verwenden.

* Zum lokalen Ausführen von webhookbasierten Triggern und Aktionen wie des [integrierten HTTP-Webhook-Triggers](../connectors/connectors-native-webhook.md) in Visual Studio Code müssen Sie die [Weiterleitung für die Rückruf-URL einrichten](#webhook-setup).

* Um den Beispielworkflow in diesem Artikel zu testen, benötigen Sie ein Tool, das Aufrufe an den vom Anforderungstrigger erstellten Endpunkt senden kann. Falls Sie nicht über ein solches Tool verfügen, können Sie die [Postman](https://www.postman.com/downloads/)-App herunterladen, installieren und verwenden.

* Wenn Sie Ihre Logik-App-Ressourcen mit Einstellungen erstellen, die die Verwendung von [Application Insights](../azure-monitor/app/app-insights-overview.md) unterstützen, können Sie optional die Diagnoseprotokollierung und Ablaufverfolgung für Ihre Logik-App aktivieren. Dies ist entweder beim Erstellen der Logik-App oder nach der Bereitstellung möglich. Sie benötigen eine Application Insights-Instanz, können aber diese Ressource entweder [im Voraus](../azure-monitor/app/create-workspace-resource.md), beim Erstellen Ihrer Logik-App oder nach der Bereitstellung erstellen.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Einrichten von Visual Studio Code

1. Um sicherzustellen, dass alle Erweiterungen ordnungsgemäß installiert sind, laden Sie Visual Studio Code neu, oder starten Sie es erneut.

1. Vergewissern Sie sich, dass Visual Studio Code Erweiterungsupdates automatisch sucht und installiert, damit Ihre Erweiterungen die neuesten Updates erhalten. Andernfalls müssen Sie die veraltete Version manuell deinstallieren und die neueste Version installieren.

   1. Navigieren Sie im Menü **Datei** zu **Voreinstellungen** **>** **Einstellungen**.

   1. Wechseln Sie auf der Registerkarte **Benutzer** zu **Features** **>** **Erweiterungen**.

   1. Vergewissern Sie sich, dass **Updates automatisch suchen** und **Automatische Aktualisierung** ausgewählt sind.

Standardmäßig sind die folgenden Einstellungen für die Erweiterung „Azure Logic Apps (Standard)“ aktiviert und festgelegt:

* **Azure Logic Apps Standard: Projektlaufzeit**, festgelegt auf Version **~3**

  > [!NOTE]
  > Diese Version ist erforderlich, um die [Aktionen in Inlinecodevorgängen](../logic-apps/logic-apps-add-run-inline-code.md) verwenden zu können.

* **Azure Logic Apps Standard: Experimental View Manager**: Hiermit wird der aktuelle Designer in Visual Studio Code aktiviert. Deaktivieren Sie diese Einstellung, falls für den Designer Probleme auftreten, z. B. beim Ziehen und Ablegen von Elementen.

Führen Sie die folgenden Schritte aus, um nach diesen Einstellungen zu suchen und sie zu bestätigen:

1. Navigieren Sie im Menü **Datei** zu **Voreinstellungen** **>** **Einstellungen**.

1. Wechseln Sie auf der Registerkarte **Benutzer** zu **>** **Erweiterungen** **>** **Azure Logic Apps (Standard)** .

   Beispielsweise finden Sie hier die Einstellung **Azure Logic Apps Standard: Projektlaufzeit** und können über das Suchfeld nach anderen Einstellungen suchen:

   ![Screenshot, der die Visual Studio Code-Einstellungen für die Erweiterung „Azure Logic Apps (Standard)“ zeigt](./media/create-single-tenant-workflows-visual-studio-code/azure-logic-apps-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Herstellen einer Verbindung mit Ihrem Azure-Konto

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus.

   ![Screenshot, der die Visual Studio Code-Aktivitätsleiste mit ausgewähltem Azure-Symbol zeigt.](./media/create-single-tenant-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Wählen Sie im Azure-Bereich unter **Azure: Logic Apps (Standard)** die Option **Bei Azure anmelden** aus. Wenn die Authentifizierungsseite für Visual Studio Code angezeigt wird, melden Sie sich mit Ihrem Azure-Konto an.

   ![Screenshot, der den Azure-Bereich und den ausgewählten Link für die Azure-Anmeldung zeigt.](./media/create-single-tenant-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Nachdem Sie sich angemeldet haben, werden im Azure-Bereich die Abonnements in Ihrem Azure-Konto angezeigt. Wenn Sie auch über die öffentlich herausgegebene Erweiterung verfügen, finden Sie alle Logik-Apps, die Sie mit dieser Erweiterung erstellt haben, im Abschnitt **Logic Apps** und nicht im Abschnitt **Logic Apps (Standard)** .

   Wenn die erwarteten Abonnements nicht angezeigt werden oder der Bereich nur bestimmte Abonnements anzeigen soll, führen Sie die folgenden Schritte aus:

   1. Bewegen Sie in der Liste „Abonnements“ den Zeiger neben das erste Abonnement, bis die Schaltfläche (Filtersymbol) **Abonnements auswählen** angezeigt wird. Wählen Sie das Filtersymbol aus.

      ![Screenshot, der den Azure-Bereich und das ausgewählte Filtersymbol zeigt.](./media/create-single-tenant-workflows-visual-studio-code/filter-subscription-list.png)

      Oder wählen Sie in der Statusleiste von Visual Studio Code Ihr Azure-Konto aus. 

   1. Wenn eine weitere Abonnement liste angezeigt wird, wählen Sie die gewünschten Abonnements aus, und stellen Sie dann sicher, dass Sie **OK** auswählen.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Erstellen eines lokalen Projekts

Bevor Sie Ihre Logik-App erstellen können, erstellen Sie ein lokales Projekt, damit Sie Ihre Logik-App über Visual Studio Code verwalten, ausführen und bereitstellen können. Das zugrunde liegende Projekt ähnelt stark einem Azure Functions-Projekt, das auch als Funktions-App-Projekt bezeichnet wird. Diese Projekttypen sind jedoch voneinander getrennt, sodass Logik-Apps und Funktions-App nicht im selben Projekt vorhanden sein können.

1. Erstellen Sie auf Ihrem Computer einen *leeren* lokalen Ordner, der für das Projekt verwendet werden soll, das Sie später in Visual Studio Code erstellen werden.

1. Schließen Sie in Visual Studio Code alle offenen Ordner.

1. Wählen Sie im Azure-Bereich neben **Azure: Logic Apps (Standard)** die Option **Neues Projekt erstellen** (Symbol mit Ordner und Blitz) aus.

   ![Screenshot, der die Symbolleiste im Azure-Bereich mit ausgewähltem „Neues Projekt erstellen“ anzeigt.](./media/create-single-tenant-workflows-visual-studio-code/create-new-project-folder.png)

1. Wenn Sie von der Windows Defender Firewall aufgefordert werden, für `Code.exe`, wobei es sich um Visual Studio Code handelt, und für `func.exe`, wobei es sich um Azure Functions Core Tools handelt, Netzwerkzugriff zu gewähren, wählen Sie **Private Netzwerke, z. B. Heim- oder Firmennetzwerk** **>** **Zugriff zulassen** aus.

1. Navigieren Sie zu dem Speicherort, an dem Sie Ihren Projektordner erstellt haben, wählen Sie diesen Ordner aus, und fahren Sie fort.

   ![Screenshot, der das Dialogfeld „Ordner auswählen“ mit einem neu erstellten Projektordner und der ausgewählten Schaltfläche „Auswählen“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/select-project-folder.png)

1. Wählen Sie in der angezeigten Vorlagenliste entweder **Zustandsbehafteter Workflow** oder **Zustandsloser Workflow** aus. In diesem Beispiel wird **Zustandsbehafteter Workflow** ausgewählt.

   ![Screenshot, der die Liste der Workflowvorlagen mit ausgewähltem „Zustandsbehafteter Workflow“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Geben Sie einen Namen für Ihren Workflow ein, und drücken Sie die EINGABETASTE. In diesem Beispiel wird `Fabrikam-Stateful-Workflow` als Name verwendet.

   ![Screenshot: Feld „Neuen zustandsbehafteten Workflow erstellen (3/4)“ und „Fabrikam-Stateful-Workflow“ als Workflowname](./media/create-single-tenant-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code schließt die Erstellung des Projekts ab und öffnet die Datei **workflow.json** für Ihren Workflow im Code-Editor.

   > [!NOTE]
   > Wählen Sie in der Eingabeaufforderung zum Öffnen des Projekts die Option **In aktuellem Fenster öffnen** aus, wenn Sie Ihr Projekt im aktuellen Visual Studio Code-Fenster öffnen möchten. Wählen Sie **In neuem Fenster öffnen** aus, um eine neue Instanz für Visual Studio Code zu öffnen.

1. Öffnen Sie in der Visual Studio-Symbolleiste den Explorer-Bereich, falls er nicht bereits geöffnet ist.

   Im Explorer-Bereich wird Ihr Projekt angezeigt, das nun automatisch generierte Projektdateien enthält. Beispielsweise verfügt das Projekt über einen Ordner, der den Namen Ihres Workflows trägt. In diesem Ordner enthält die **workflow.json**-Datei die zugrunde liegende JSON-Definition Ihres Workflows.

   ![Screenshot, der den Explorer-Bereich mit Projektordner, Workflowordner und der Datei „workflow.json“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/local-project-created.png)

   [!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Aktivieren der Erstellung integrierter Connectors

Sie können Ihre eigenen integrierten Connectors für jeden benötigten Dienst erstellen, indem Sie das [Erweiterbarkeitsframework für Azure Logic Apps-Instanzen mit einem Mandanten](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) verwenden. Ähnlich wie integrierte Connectors wie Azure Service Bus und SQL Server bieten diese Connectors einen höheren Durchsatz, niedrige Wartezeiten, lokale Konnektivität und werden nativ im selben Prozess wie die Azure Logic Apps-Runtime mit einzelnem Mandanten ausgeführt.

Die Erstellungsfunktion ist derzeit nur in Visual Studio Code verfügbar, aber nicht standardmäßig aktiviert. Um diese Connectors zu erstellen, müssen Sie zuerst Ihr Projekt von erweiterungspaketbasiert (Node.js) in NuGet-Paketbasiert (.NET) konvertieren.

> [!IMPORTANT]
> Diese Aktion ist ein unidirektionaler Vorgang, der nicht rückgängig gemacht werden kann.

1. Bewegen Sie im Bereich „Explorer“ im Stammverzeichnis Ihres Projekts den Mauszeiger über einen beliebigen leeren Bereich unterhalb aller anderen Dateien und Ordner, öffnen Sie das Kontextmenü, und wählen Sie **In Nuget-basiertes Logik-App-Projekt konvertieren** aus.

   ![Screenshot, der den Bereich „Explorer“ mit dem aus einem leeren Bereich heraus geöffneten Kontextmenü des Projekts im Projektfenster zeigt.](./media/create-single-tenant-workflows-visual-studio-code/convert-logic-app-project.png)

1. Wenn die Eingabeaufforderung angezeigt wird, bestätigen Sie die Projektkonvertierung.

1. Um den Vorgang fortzusetzen, überprüfen und befolgen Sie die Schritte im Artikel [Azure Logic Apps läuft überall: Erweiterbarkeit integrierter Connectors](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Öffnen der Workflowdefinitionsdatei im Designer

1. Überprüfen Sie die auf Ihrem Computer installierten Versionen mit folgendem Befehl:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Wenn Sie über .NET Core SDK 5.x verfügen, kann diese Version Sie möglicherweise daran hindern, die zugrunde liegende Workflowdefinition der Logik-App im Designer zu öffnen. Anstatt diese Version zu deinstallieren, erstellen Sie im Stammordner Ihres Projekts die Datei **global.json**, mit der auf Ihre .NET Core Runtime 3.x-Version verwiesen wird, die höher als 3.1.201 ist. Beispiel:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie die Datei **global.json** in Visual Studio Code im Stammordner Ihres Projekts explizit hinzufügen. Andernfalls wird der Designer nicht geöffnet.

1. Erweitern Sie den Projektordner für Ihren Workflow. Öffnen Sie das Kontextmenü der Datei **workflow.json**, und wählen Sie **Im Designer öffnen** aus.

   ![Screenshot, der den Explorer-Bereich und das Kontextmenüfenster für die Datei „workflow.json“ mit ausgewähltem „In Designer öffnen“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Wählen Sie in der Liste **Enable connectors in Azure** (Connectors in Azure aktivieren) die Option **Use connectors from Azure** (Connectors aus Azure verwenden) aus, die für alle verwalteten Connectors gilt, die im Azure-Portal verfügbar sind, nicht nur für Connectors für Azure-Dienste.

   ![Screenshot, der den Explorer-Bereich mit der geöffneten Liste „Connectors in Azure aktivieren“ und ausgewähltem „Connectors von Azure verwenden“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Zustandslose Workflows unterstützen derzeit nur *Aktionen* für in Azure bereitgestellte [verwaltete Connectors](../connectors/managed.md), keine Aktionen für Trigger. Obwohl Sie die Möglichkeit haben, Connectors in Azure für zustandslose Workflows zu aktivieren, zeigt der Designer keine verwalteten Connectortrigger an, die Sie auswählen können.

1. Wählen Sie in der Liste **Abonnement auswählen** das Azure-Abonnement aus, das für Ihr Logik-App-Projekt verwendet werden soll.

   ![Screenshot, der den Explorer-Bereich mit dem Feld „Abonnement auswählen“ und dem ausgewählten Abonnement zeigt.](./media/create-single-tenant-workflows-visual-studio-code/select-azure-subscription.png)

1. Wählen Sie in der Liste der Ressourcengruppen **Neue Ressourcengruppe erstellen** aus.

   ![Screenshot, der den Explorer-Bereich mit der Liste der Ressourcengruppen und ausgewähltem „Neue Ressourcengruppe erstellen“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/create-select-resource-group.png)

1. Geben Sie einen Namen für die Ressourcengruppe an, und drücken Sie die EINGABETASTE. In diesem Beispiel wird `Fabrikam-Workflows-RG` verwendet.

   ![Screenshot, der den Explorer-Bereich und das Feld für den Namen der Ressourcengruppe zeigt.](./media/create-single-tenant-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Wählen Sie in der Liste der Speicherorte die Azure-Region aus, die für die Erstellung Ihrer Ressourcengruppe und Ressourcen verwendet werden soll. In diesem Beispiel wird **USA, Westen-Mitte** verwendet.

   ![Screenshot, der den Explorer-Bereich mit der Liste der Standorte und ausgewähltem „USA, Westen-Mitte“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/select-azure-region.png)

   Nachdem Sie diesen Schritt ausgeführt haben, öffnet Visual Studio Code den Workflow-Designer.

   > [!NOTE]
   > Wenn Visual Studio Code die Workflow-Entwurfszeit-API startet, wird ggf. eine Meldung angezeigt, dass der Startvorgang einige Sekunden dauern kann. Sie können diese Meldung ignorieren oder **OK** auswählen.
   >
   > Falls der Designer nicht geöffnet wird, helfen Ihnen die Informationen im Abschnitt [Designer wird nicht geöffnet](#designer-fails-to-open) bei der Problembehandlung weiter.

   Sobald der Designer angezeigt wird, wird die Eingabeaufforderung **Vorgang auswählen** im Designer standardmäßig ausgewählt angezeigt, wodurch der Bereich **Aktion hinzufügen** angezeigt wird.

   ![Screenshot, der den Workflow-Designer zeigt.](./media/create-single-tenant-workflows-visual-studio-code/workflow-designer.png)

1. Als Nächstes [fügen Sie Ihrem Workflow einen Trigger und Aktionen hinzu](#add-trigger-actions).

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Hinzufügen eines Triggers und von Aktionen

Nachdem Sie den Designer geöffnet haben, wird die Eingabeaufforderung **Vorgang auswählen** im Designer standardmäßig ausgewählt angezeigt. Sie können nun mit dem Erstellen Ihres Workflows beginnen, indem Sie einen Trigger und Aktionen hinzufügen.

Der Workflow in diesem Beispiel verwendet diesen Triggern und die folgenden Aktionen:

* Den integrierten [Anforderungstrigger](../connectors/connectors-native-reqres.md), **Beim Empfang einer HTTP-Anforderung**, der eingehende Aufrufe oder Anforderungen empfängt und einen Endpunkt erstellt, der von anderen Diensten oder Logik-Apps aufgerufen werden kann.

* Die [Office 365 Outlook-Aktion](../connectors/connectors-create-api-office365-outlook.md): **E-Mail senden**.

* Die integrierte [Antwortaktion](../connectors/connectors-native-reqres.md), mit der Sie eine Antwort gesendet und Daten an den Aufrufer zurückgegeben haben.

### <a name="add-the-request-trigger"></a>Hinzufügen des Anforderungstriggers

1. Vergewissern Sie sich neben dem Designer im Bereich **Trigger hinzufügen** unter dem Suchfeld **Vorgang auswählen**, dass **Integriert** ausgewählt ist, damit Sie einen Trigger auswählen können, der nativ ausgeführt wird.

1. Geben Sie in das Suchfeld **Vorgang auswählen** die Zeichenfolge `when a http request` ein, und wählen Sie den integrierten Anforderungstrigger namens **Beim Empfang einer HTTP-Anforderung** aus.

   ![Screenshot, der den Workflow-Designer mit dem Bereich **Trigger hinzufügen** mit ausgewähltem Trigger „Beim Empfang einer HTTP-Anforderung“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/add-request-trigger.png)

   Wenn der Trigger im Designer angezeigt wird, wird der Detailbereich des Triggers geöffnet, um die Eigenschaften, Einstellungen und anderen Aktionen des Triggers anzuzeigen.

   ![Screenshot, der den Workflow-Designer mit dem ausgewählten Trigger „Beim Empfang einer HTTP-Anforderung“ und geöffnetem Triggerdetailbereich zeigt.](./media/create-single-tenant-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Wenn der Detailbereich nicht angezeigt wird, stellen Sie sicher, dass der Trigger im Designer ausgewählt ist.

1. Wenn Sie ein Element aus dem Designer löschen müssen, [führen Sie diese Schritte aus](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Hinzufügen der Office 365 Outlook-Aktion

1. Wählen Sie im Designer unter dem hinzugefügten Trigger das Pluszeichen ( **+** ) > **Aktion hinzufügen** aus.

   Die Eingabeaufforderung **Vorgang auswählen** wird im Designer angezeigt, und der Bereich **Aktion hinzufügen** wird erneut geöffnet, sodass Sie die nächste Aktion auswählen können.

1. Wählen Sie im Bereich **Aktion hinzufügen** unter dem Suchfeld **Vorgang auswählen** die Zeichenfolge **Azure** aus, damit Sie eine Aktion für einen verwalteten Connector auswählen können, der in Azure bereitgestellt ist.

   In diesem Beispiel wird die Office 365 Outlook-Aktion **E-Mail senden (V2)** ausgewählt und verwendet.

   ![Screenshot, der den Workflow-Designer und den Bereich **Aktion hinzufügen** mit ausgewählter Office 365 Outlook-Aktion „E-Mail senden“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/add-send-email-action.png)

1. Wählen Sie im Detailbereich der Aktion **Anmelden** aus, damit Sie eine Verbindung mit Ihrem E-Mail-Konto herstellen können.

   ![Screenshot, der den Workflow-Designer und den Bereich **E-Mail senden (V2)** mit ausgewähltem „Anmelden“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Wenn Visual Studio Code Sie zur Zustimmung für den Zugriff auf Ihr E-Mail-Konto auffordert, wählen Sie **Öffnen** aus.

   ![Screenshot, der die Aufforderung von Visual Studio Code zur Zulassung des Zugriffs zeigt.](./media/create-single-tenant-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Um zukünftige Aufforderungen zu vermeiden, wählen Sie **Vertrauenswürdige Domänen konfigurieren** aus, damit Sie die Authentifizierungsseite als vertrauenswürdige Domäne hinzufügen können.

1. Befolgen Sie die nachfolgenden Aufforderungen, um sich anzumelden, den Zugriff zuzulassen und die Rückkehr zu Visual Studio Code zuzulassen.

   > [!NOTE]
   > Wenn zu viel Zeit verstrichen ist, bevor Sie die Eingabeaufforderungen erfüllt haben, kommt es beim Authentifizierungsprozess zum Timeout, und er schlägt fehl. Kehren Sie in diesem Fall zum Designer zurück, und wiederholen Sie die Anmeldung, um die Verbindung zu erstellen.

1. Wenn die Erweiterung „Azure Logic Apps (Standard)“ Sie zur Zustimmung für den Zugriff auf Ihr E-Mail-Konto auffordert, wählen Sie **Öffnen** aus. Befolgen Sie die nachfolgende Aufforderung, um den Zugriff zuzulassen.

   ![Screenshot, der die Aufforderung der Erweiterung zur Zulassung des Zugriffs zeigt](./media/create-single-tenant-workflows-visual-studio-code/allow-extension-open-uri.png)

   > [!TIP]
   > Um zukünftige Eingabeaufforderungen zu vermeiden, wählen Sie **Für diese Erweiterung nicht mehr nachfragen** aus.

   Nachdem Visual Studio Code die Verbindung erstellt hat, zeigen einige Connectors die Meldung `The connection will be valid for {n} days only` an. Dieses Zeitlimit gilt nur für die Dauer der Erstellung Ihrer Logik-App in Visual Studio Code. Nach der Bereitstellung gilt dieses Limit nicht mehr, da sich Ihre Logik-App zur Laufzeit mittels der automatisch aktivierten [vom System zugewiesenen verwalteten Identität](../logic-apps/create-managed-service-identity.md) authentifizieren kann. Diese verwaltete Identität unterscheidet sich von den Anmeldeinformationen für die Authentifizierung oder der Verbindungszeichenfolge, die Sie verwenden, wenn Sie eine Verbindung herstellen. Wenn Sie diese vom System zugewiesene verwaltete Identität deaktivieren, funktionieren Verbindungen zur Laufzeit nicht.

1. Wenn die Aktion **E-Mail senden** im Designer nicht ausgewählt angezeigt wird, wählen Sie diese Aktion aus.

1. Geben Sie im Detailbereich der Aktion auf der Registerkarte **Parameter** die erforderlichen Informationen für die Aktion an, z. B.:

   ![Screenshot, der den Workflow-Designer mit Details für die Office 365 Outlook-Aktion „E-Mail senden“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/send-email-action-details.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **An** | Ja | <*Ihre E-Mail-Adresse*> | Der E-Mail-Empfänger, bei dem es sich um Ihre E-Mail-Adresse zu Testzwecken handeln kann. In diesem Beispiel wird die fiktive E-Mail-Adresse `sophiaowen@fabrikam.com` verwendet. |
   | **Subject** | Ja | `An email from your example workflow` | der E-Mail-Betreff |
   | **Text** | Ja | `Hello from your example workflow!` | Der Textinhalt der E-Mail |
   ||||

   > [!NOTE]
   > Wenn Sie Änderungen im Detailbereich auf der Registerkarte **Einstellungen**, **Statisches Ergebnis** oder **Ausführen nach** vornehmen möchten, stellen Sie sicher, dass Sie **Fertig** ausgewählt haben, um die Änderungen zu übernehmen, bevor Sie zwischen Registerkarten wechseln oder den Fokus innerhalb des Designers verschieben. Andernfalls behält Visual Studio Code Ihre Änderungen nicht.

1. Wählen Sie im Designer **Speichern** aus.

> [!IMPORTANT]
> Zum lokalen Ausführen eines Workflows, der einen webhookbasierten Trigger oder Aktionen verwendet, wie z. B. den [integrierten Trigger oder eine Aktion des HTTP-Webhooks](../connectors/connectors-native-webhook.md), müssen Sie diese Funktion aktivieren, indem Sie die [Weiterleitung für die Rückruf-URL des Webhooks einrichten](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Aktivieren von lokal ausgeführten Webhooks

Wenn Sie einen webhookbasierten Trigger oder eine Aktion wie z. B. **HTTP-Webhook** verwenden und eine Logik-App in Azure ausgeführt wird, abonniert die Logic Apps-Runtime den Dienstendpunkt, indem eine Rückruf-URL mit diesem Endpunkt erzeugt und registriert wird. Der Trigger oder die Aktion wartet dann darauf, dass der Dienstendpunkt die URL aufruft. Wenn Sie jedoch in Visual Studio Code arbeiten, beginnt die generierte Rückruf-URL mit `http://localhost:7071/...`. Diese URL gilt für den Localhost-Server, der privat ist, sodass der Dienstendpunkt diese URL nicht aufrufen kann.

Zum lokalen Ausführen von webhookbasierten Triggern und Aktionen in Visual Studio Code müssen Sie eine öffentliche URL einrichten, die den Localhost-Server verfügbar macht und Aufrufe vom Dienstendpunkt sicher an die Webhookrückruf-URL weiterleitet. Sie können einen Weiterleitungsdienst und ein Tool wie [**ngrok**](https://ngrok.com/) verwenden, mit dem ein HTTP-Tunnel für den Localhost-Port geöffnet wird, oder Ihr eigenes äquivalentes Tool verwenden.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Einrichten der Aufrufweiterleitung mit **ngrok**

1. [Registrieren Sie sich für ein **ngrok**-Konto](https://dashboard.ngrok.com/signup), wenn Sie keines besitzen. Andernfalls [melden Sie sich bei Ihrem Konto an](https://dashboard.ngrok.com/login).

1. Rufen Sie Ihr persönliches Authentifizierungstoken ab, das Ihr **ngrok**-Client benötigt, um eine Verbindung mit Ihrem Konto herzustellen und den Zugriff zu authentifizieren.

   1. Um Ihre [Authentifizierungstokenseite](https://dashboard.ngrok.com/auth/your-authtoken) zu finden, erweitern Sie in Ihrem Kontodashboardmenü die Option **Authentifizierung**, und wählen Sie **Ihr Authentifizierungstoken** aus.

   1. Kopieren Sie das Token aus dem Feld **Ihr Authentifizierungstoken** an einen sicheren Speicherort.

1. Laden Sie von der [**ngrok**-Downloadseite](https://ngrok.com/download) oder [Ihrem Kontodashboard](https://dashboard.ngrok.com/get-started/setup) die gewünschte **ngrok**-Version herunter, und extrahieren Sie die ZIP-Datei. Weitere Informationen finden Sie unter [Schritt 1: Unzip to install](https://ngrok.com/download) (Zum Installieren entzippen).

1. Öffnen Sie auf Ihrem Computer das Eingabeaufforderungstool. Wechseln Sie zu dem Speicherort, wo sich die Datei **ngrok.exe** befindet.

1. Stellen Sie eine Verbindung des **ngrok**-Clients mit Ihrem **ngrok**-Konto her, indem Sie den folgenden Befehl ausführen. Weitere Informationen finden Sie unter [Schritt 2: Connect your account](https://ngrok.com/download) (Stellen Sie eine Verbindung mit Ihrem Konto her).

   `ngrok authtoken <your_auth_token>`

1. Öffnen Sie den HTTP-Tunnel zum Localhost-Port 7071, indem Sie den folgenden Befehl ausführen. Weitere Informationen finden Sie unter [Schritt 3: Fire it up](https://ngrok.com/download) (Starten Sie es).

   `ngrok http 7071`

1. In der Ausgabe finden Sie folgende Zeile:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Kopieren und speichern Sie die URL mit dem folgenden Format: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Einrichten der Weiterleitungs-URL in ihren App-Einstellungen

1. Fügen Sie in Visual Studio Code im Designer als Trigger bzw. Aktion **HTTP + Webhook** hinzu.

1. Geben Sie die zuvor erstellte Weiterleitungs-URL (Umleitung) ein, wenn die Eingabeaufforderung für den Hostendpunkt-Speicherort angezeigt wird.

   > [!NOTE]
   > Wenn Sie die Eingabeaufforderung ignorieren, wird eine Warnung mit dem Hinweis angezeigt, dass Sie die Weiterleitungs-URL angeben müssen. Wählen Sie daher die Option **Konfigurieren** aus, und geben Sie die URL ein. Nachdem Sie diesen Schritt ausgeführt haben, wird die Eingabeaufforderung für nachfolgende Webhook-Trigger oder -Aktionen, die Sie ggf. hinzufügen, nicht mehr angezeigt.
   >
   > Gehen Sie wie folgt vor, wenn die Eingabeaufforderung auf der Stammebene Ihres Projekts wieder angezeigt werden soll: Öffnen Sie das Kontextmenü der Datei **local.settings.json**, und wählen Sie die Option **Configure Webhook Redirect Endpoint** (Endpunkt für Webhookumleitung konfigurieren) aus. Die Eingabeaufforderung wird angezeigt, damit Sie die Weiterleitungs-URL angeben können.

   In Visual Studio Code wird die Weiterleitungs-URL im Stammordner Ihres Projekts der Datei **local.settings.json** hinzugefügt. Im Objekt `Values` wird jetzt die Eigenschaft mit dem Namen `Workflows.WebhookRedirectHostUri` angezeigt und auf die Weiterleitungs-URL festgelegt. Beispiel:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

Wenn Sie zum ersten Mal eine lokale Debugsitzung starten oder den Workflow ohne Debuggen ausführen, registriert die Logic Apps-Runtime den Workflow beim Dienstendpunkt und abonniert diesen Endpunkt, um die Benachrichtigung über Webhookvorgänge zu ermöglichen. Wenn Sie den Workflow das nächste Mal ausführen, wird die Runtime nicht registriert oder erneut abonniert, weil die Abonnementregistrierung bereits im lokalen Speicher vorhanden ist.

Wenn Sie die Debugsitzung für eine Workflowausführung beenden, die lokal ausgeführte webhookbasierte Trigger oder Aktionen verwendet, werden die vorhandenen Abonnementregistrierungen nicht gelöscht. Wenn Sie die Registrierung aufheben möchten, müssen Sie die Abonnementregistrierungen manuell entfernen oder löschen.

> [!NOTE]
> Nachdem der Workflow gestartet wurde, werden im Terminalfenster möglicherweise Fehler wie in diesem Beispiel angezeigt:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Öffnen Sie in diesem Fall die Datei **local.settings.json** im Stammordner des Projekts, und stellen Sie sicher, dass die Eigenschaft auf `true` festgelegt ist:
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Verwalten von Haltepunkten zum Debuggen

Bevor Sie Ihren Logik-App-Workflow ausführen und testen, indem Sie eine Debugsitzung starten, können Sie in der Datei **workflow.json** für jeden Workflow [Haltepunkte](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) festlegen. Es ist kein anderes Setup erforderlich. 

Haltepunkte werden derzeit jedoch nur für Aktionen unterstützt, nicht für Trigger. Jede Aktionsdefinition enthält diese Haltepunktpositionen:

* Legen Sie den Starthaltepunkt in der Zeile fest, die den Namen der Aktion enthält. Wenn dieser Haltepunkt während der Debugsitzung erreicht wird, können Sie die Eingaben der Aktion überprüfen, bevor sie ausgewertet werden.

* Legen Sie den Endhaltepunkt in der Zeile fest, die die schließende geschweifte Klammer ( **}** ) der Aktion enthält. Wenn dieser Haltepunkt während der Debugsitzung erreicht wird, können Sie die Ergebnisse der Aktion überprüfen, bevor die Ausführung der Aktion endet.

Um einen Haltepunkt hinzuzufügen, führen Sie diese Schritte aus:

1. Öffnen Sie die Datei **workflow.json** für den Workflow, den Sie debuggen möchten.

1. Wählen Sie in der Zeile, in der Sie den Haltepunkt festlegen möchten, das Innere der linken Spalte aus. Um den Haltepunkt zu entfernen, wählen Sie den Haltepunkt aus.

   Wenn Sie die Debugsitzung starten, wird die Ausführungsansicht auf der linken Seite des Codefensters angezeigt, während die Debugsymbolleiste im oberen Bereich angezeigt wird.

   > [!NOTE]
   > Wenn die Ausführungsansicht nicht automatisch angezeigt wird, drücken Sie STRG+UMSCHALT+D.

1. Um die verfügbaren Informationen zu überprüfen, wenn ein Haltepunkt erreicht wird, untersuchen Sie in der Ausführungsansicht den Bereich **Variablen**.

1. Um die Workflowausführung fortzusetzen, wählen Sie auf der Symbolleiste „Debuggen“ **Fortsetzen** (Wiedergabeschaltfläche) aus.

Während der Workflowausführung können Sie jederzeit Haltepunkte hinzufügen und entfernen. Wenn Sie jedoch die **workflow.json**-Datei nach dem Start der Ausführung aktualisieren, werden Haltepunkte nicht automatisch aktualisiert. Um die Haltepunkte zu aktualisieren, starten Sie die Logik-App neu.

Allgemeine Informationen finden Sie unter [Breakpoints – Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) (Haltepunkte – Visual Studio Code).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Lokales Ausführen, Testen und Debuggen

Um Ihre Logik-App zu testen, führen Sie die folgenden Schritte aus, um eine Debugsitzung zu starten und die URL für den Endpunkt zu finden, der durch den Anforderungstrigger erstellt wird. Sie benötigen diese URL, damit Sie später eine Anforderung an diesen Endpunkt senden können.

1. Damit Sie einen zustandslosen Workflow leichter debuggen können, können Sie [den Ausführungsverlauf für diesen Workflow aktivieren](#enable-run-history-stateless).

1. Öffnen Sie auf der Aktivitätsleiste von Visual Studio Code das Menü **Ausführen**, und wählen Sie **Debuggen starten** (F5) aus.

   Das **Terminal** fenster wird geöffnet, sodass Sie die Debugsitzung überprüfen können.

   > [!NOTE]
   > Wenn Sie einen Fehler wie **Fehler nach der Ausführung von preLaunchTask „generateDebugSymbols“** erhalten, helfen Ihnen die Informationen im Abschnitt [Debugsitzung wird nicht gestartet](#debugging-fails-to-start) bei der Problembehandlung weiter.

1. Suchen Sie nun die Rückruf-URL für den Endpunkt im Anforderungstrigger.

   1. Öffnen Sie den Explorer-Bereich erneut, damit Sie Ihr Projekt anzeigen können.

   1. Wählen Sie im Kontextmenü der Datei **workflow.json** die Option **Übersicht** aus.

      ![Screenshot, der den Explorer-Bereich und das Kontextmenüfenster für die Datei „workflow.json“ mit ausgewähltem „Übersicht“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/open-workflow-overview.png)

   1. Suchen Sie den Wert **Rückruf-URL**, der dieser URL für den Anforderungsbeispieltrigger ähnelt:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Screenshot, der die Übersichtsseite Ihres Workflows mit Rückruf-URL zeigt.](./media/create-single-tenant-workflows-visual-studio-code/find-callback-url.png)

1. Um die Rückruf-URL zu testen, indem Sie den Logik-App-Workflow auslösen, öffnen Sie [Postman](https://www.postman.com/downloads/) oder Ihr bevorzugtes Tool zum Erstellen und Senden von Anforderungen.

   Dieses Beispiel wird mit Postman fortgesetzt. Weitere Informationen finden Sie unter [Erste Schritte mit Postman](https://learning.postman.com/docs/getting-started/introduction/).

   1. Wählen Sie auf der Postman-Symbolleiste **Neu** aus.

      ![Screenshot, der Postman mit ausgewählter Schaltfläche „Neu“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/postman-create-request.png)

   1. Wählen Sie im Bereich **Neu erstellen** unter **Bausteine** die Option **Anforderung** aus.

   1. Geben Sie im Fenster **Anforderung speichern** unter **Anforderungsname** einen Namen für die Anforderung an, z. B. `Test workflow trigger`.

   1. Wählen Sie unter **Sammlung oder Ordner als Speicherziel auswählen** den Eintrag **Sammlung erstellen** aus.

   1. Geben Sie unter **Alle Sammlungen** einen Namen für die zu erstellende Sammlung an, um Ihre Anforderungen zu organisieren, und wählen Sie **Speichern unter <*Sammlungs-Name*>** aus. In diesem Beispiel wird `Logic Apps requests` als Name der Sammlung verwendet.

      In Postman wird der Anforderungsbereich geöffnet, sodass Sie eine Anforderung an die Rückruf-URL für den Anforderungstrigger senden können.

      ![Screenshot, der Postman mit geöffnetem Anforderungsbereich zeigt.](./media/create-single-tenant-workflows-visual-studio-code/postman-request-pane.png)

   1. Kehren Sie zu Visual Studio Code zurück. Kopieren Sie von der Übersichtsseite des Workflows den Eigenschaftswert **Rückruf-URL**.

   1. Kehren Sie zu Postman zurück. Fügen Sie im Anforderungsbereich neben der Liste der Methoden, in der derzeit **GET** als Standardanforderungsmethode angezeigt wird, die zuvor kopierte Rückruf-URL in das Feld „Adresse“ ein, und wählen Sie **Senden** aus.

      ![Screenshot, der Postman und die Rückruf-URL im Adressfeld mit ausgewählter Schaltfläche „Senden“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/postman-test-call-back-url.png)

      Der Logik-App-Beispielworkflow sendet eine E-Mail, die in etwa wie im folgenden Beispiel aussieht:

      ![Screenshot, der die wie im Beispiel beschriebene Outlook-E-Mail zeigt.](./media/create-single-tenant-workflows-visual-studio-code/workflow-app-result-email.png)

1. Kehren Sie in Visual Studio Code zur Übersichtsseite Ihres Workflows zurück.

   Wenn Sie einen zustandsbehafteten Workflow erstellt haben, werden auf der Übersichtsseite der Ausführungsstatus und -verlauf des Workflows angezeigt, nachdem die von Ihnen gesendete Anforderung den Workflow ausgelöst hat.

   > [!TIP]
   > Wenn der Ausführungsstatus nicht angezeigt wird, aktualisieren Sie die Übersichtsseite, indem Sie **Aktualisieren** auswählen. Für Trigger, die aufgrund von nicht erfüllten Kriterien oder nicht gefundenen Daten ausgelassen werden, erfolgt keine Ausführung.

   ![Screenshot: Übersichtsseite Ihres Workflows mit Ausführungsstatus und -verlauf](./media/create-single-tenant-workflows-visual-studio-code/post-trigger-call.png)

   | Ausführungsstatus | Beschreibung |
   |------------|-------------|
   | **Aborted** | Die Ausführung wurde aufgrund externer Probleme beendet oder nicht abgeschlossen (beispielsweise wegen eines Systemausfalls oder abgelaufenen Azure-Abonnements). |
   | **Abgebrochen** | Die Ausführung wurde ausgelöst und gestartet, es wurde jedoch eine Abbruchanforderung empfangen. |
   | **Fehler** | Mindestens eine Aktion in der Ausführung war nicht erfolgreich. Es wurden keine nachfolgenden Aktionen im Workflow eingerichtet, um den Fehler zu behandeln. |
   | **Wird ausgeführt** | Die Ausführung wurde ausgelöst und wird gerade ausgeführt. Dieser Status kann jedoch auch für eine Ausführung angezeigt werden, die aufgrund von [Aktionslimits](logic-apps-limits-and-config.md) oder aufgrund des [aktuellen Tarifs](https://azure.microsoft.com/pricing/details/logic-apps/) gedrosselt wird. <p><p>**Tipp**: Wenn Sie die [Diagnoseprotokollierung](monitor-logic-apps-log-analytics.md) einrichten, erhalten Sie Informationen zu ggf. aufgetretenen Drosselungsereignissen. |
   | **Erfolgreich** | Die Ausführung war erfolgreich. Sollte bei einer Aktion ein Fehler aufgetreten sein, wurde dieser Fehler von einer nachfolgenden Aktion im Workflow behandelt. |
   | **Timeout** | Bei der Ausführung ist ein Timeout aufgetreten, da die aktuelle Dauer die maximal zulässige Ausführungsdauer überschritten hat, die durch die [Einstellung **Aufbewahrung des Ausführungsverlaufs in Tagen**](logic-apps-limits-and-config.md#run-duration-retention-limits) gesteuert wird. Die Dauer einer Ausführung wird anhand der Startzeit der Ausführung und der maximalen Ausführungsdauer zu dieser Startzeit berechnet. <p><p>**Hinweis**: Wenn die Ausführungsdauer auch das aktuelle *Aufbewahrungslimit im Ausführungsverlauf* übersteigt, das ebenfalls durch die [Einstellung **Aufbewahrung des Ausführungsverlaufs in Tagen**](logic-apps-limits-and-config.md#run-duration-retention-limits) gesteuert wird, wird die Ausführung durch einen täglichen Bereinigungsauftrag aus dem Ausführungsverlauf gelöscht. Der Aufbewahrungszeitraum wird immer auf der Grundlage der Startzeit der Ausführung und des *aktuellen* Aufbewahrungslimits berechnet – unabhängig davon, ob bei der Ausführung ein Timeout auftritt oder ob die Ausführung abgeschlossen wird. Wenn Sie also die maximale Dauer einer aktiven Ausführung verringern, tritt ein Timeout für die Ausführung auf. Ob die Ausführung im Ausführungsverlauf verbleibt oder daraus entfernt wird, hängt davon ab, ob die Ausführungsdauer das Aufbewahrungslimit übersteigt. |
   | **Wartet** | Die Ausführung wurde nicht gestartet oder wurde angehalten, z. B. aufgrund einer früheren Workflowinstanz, die noch ausgeführt wird. |
   |||

1. Wählen Sie zum Überprüfen der Statuswerte für jeden Schritt in einer bestimmten Ausführung sowie der Eingaben und Ausgaben des Schritts die Schaltfläche mit den Auslassungspunkten ( **...** ) für diese Ausführung aus, und wählen Sie **Ausführung anzeigen** aus.

   ![Screenshot, der die Zeile mit dem Ausführungsverlauf Ihres Workflows mit der Schaltfläche mit Auslassungspunkten und ausgewähltem „Ausführung anzeigen“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code öffnet die Überwachungsansicht und zeigt den Status der einzelnen Ausführungsschritte an.

   ![Screenshot: einzelne Ausführungsschritte in der Workflowausführung und ihr Status](./media/create-single-tenant-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Wenn bei einer Ausführung ein Schritt in der Überwachungsansicht den Fehler `400 Bad Request` anzeigt, wird das Problem möglicherweise durch einen längeren Trigger- oder Aktionsnamen verursacht, der dazu führt, dass der zugrunde liegende URI (Uniform Resource Identifier) das Standardzeichenlimit überschreitet. Weitere Informationen finden Sie unter [„400 – Ungültige Anforderung“](#400-bad-request).

   In der folgenden Tabelle sind die möglichen Statuswerte aufgeführt, die in den einzelnen Workflowschritte aufweisen können:

   | Status einer Aktion | Beschreibung |
   |---------------|-------------|
   | **Aborted** | Die Aktion wurde aufgrund externer Probleme beendet oder nicht fertig gestellt, z. B. wegen eines Systemausfalls oder abgelaufenen Azure-Abonnements. |
   | **Abgebrochen** | Die Aktion wurde ausgeführt, aber es wurde eine Abbruchanforderung empfangen. |
   | **Fehlgeschlagen** | Die Aktion ist fehlgeschlagen. |
   | **Wird ausgeführt** | Die Aktion wird zurzeit ausgeführt. |
   | **Übersprungen** | Die Aktion wurde übersprungen, weil die unmittelbar vorhergehende Aktion fehlgeschlagen ist. Eine Aktion weist eine `runAfter`-Bedingung auf, die erfordert, dass die vorherige Aktion erfolgreich abgeschlossen wurde, bevor die aktuelle Aktion ausgeführt werden kann. |
   | **Erfolgreich** | Die Aktion war erfolgreich. |
   | **Erfolgreich mit Wiederholungen** | Die Aktion war erfolgreich, jedoch erst nach mindestens einem Wiederholungsversuch. Wählen Sie diese Aktion zum Überprüfen des Wiederholungsverlaufs in der Detailansicht des Ausführungsverlaufs aus, damit Sie die Ein- und Ausgaben anzeigen können. |
   | **Timeout** | Die Aktion wurde aufgrund des Timeoutlimits beendet, das durch die Einstellungen dieser Aktion gesteuert wird. |
   | **Wartet** | Gilt für eine Webhookaktion, die auf eine eingehende Anforderung eines Aufrufers wartet. |
   |||

   [aborted-icon]: ./media/create-single-tenant-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-single-tenant-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-single-tenant-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-single-tenant-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-single-tenant-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-single-tenant-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-single-tenant-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-single-tenant-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-single-tenant-workflows-visual-studio-code/waiting.png

1. Wählen Sie zum Überprüfen der Ein- und Ausgaben für die einzelnen Schritte den Schritt aus, den Sie untersuchen möchten.

   ![Screenshot, der den Status für jeden Schritt im Workflow sowie die Ein- und Ausgaben in der erweiterten Aktion „E-Mail senden“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/run-history-details.png)

1. Um die unformatierten Ein- und Ausgaben für diesen Schritt weiter zu überprüfen, wählen Sie **Unformatierte Eingaben anzeigen** oder **Unformatierte Ausgaben anzeigen** aus.

1. Um die Debugsitzung zu beenden, wählen Sie im menü **Ausführen** den Eintrag **Debuggen beenden** (UMSCHALT+F5) aus.

<a name="return-response"></a>

## <a name="return-a-response"></a>Zurückgeben einer Antwort

Um eine Antwort an den Aufrufer zurückzugeben, der eine Anforderung an Ihre Logik-App gesendet hat, können Sie die integrierte [Antwortaktion](../connectors/connectors-native-reqres.md) für einen Workflow verwenden, der mit dem Anforderungstrigger gestartet wird.

1. Wählen Sie im Workflow-Designer unter der Aktion **E-Mail senden** das Pluszeichen ( **+** ) > **Aktion hinzufügen** aus.

   Die Eingabeaufforderung **Vorgang auswählen** wird im Designer angezeigt, und der Bereich **Aktion hinzufügen** wird erneut geöffnet, sodass Sie die nächste Aktion auswählen können.

1. Vergewissern Sie sich, dass im Bereich **Aktion hinzufügen** unter dem Suchfeld **Aktion auswählen** die Option **Integriert** ausgewählt ist. Geben Sie `response` in das Suchfeld ein, und wählen Sie die Aktion **Antwort** aus.

   ![Screenshot, der den Workflow-Designer mit ausgewählter Antwortaktion zeigt.](./media/create-single-tenant-workflows-visual-studio-code/add-response-action.png)

   Wenn die Aktion **Antwort** im Designer angezeigt wird, wird automatisch der Detailbereich der Aktion geöffnet.

   ![Screenshot, der den Workflow-Designer mit geöffnetem Detailbereich der Aktion „Antwort“ und die auf den Wert der Eigenschaft „Text“ der Aktion „E-Mail senden“ festgelegte Eigenschaft „Text“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/response-action-details.png)

1. Geben Sie auf der Registerkarte **Parameter** die erforderlichen Informationen für die Funktion an, die Sie aufrufen möchten.

   In diesem Beispiel wird der Wert der Eigenschaft **Text** zurückgegeben, der von der Aktion **E-Mail senden** ausgegeben wird.

   1. Klicken Sie in das Eigenschaftsfeld **Text**, damit die Liste dynamischer Inhalte und die verfügbaren Ausgabewerte des vorherigen Auslösers und der Aktionen im Workflow angezeigt werden.

      ![Screenshot, der den Detailbereich der Aktion „Antwort“ mit dem Mauszeiger in der Eigenschaft „Text“ zeigt, sodass die Liste dynamischer Inhalte angezeigt wird.](./media/create-single-tenant-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Wählen Sie in der Liste dynamischer Inhalte unter **E-Mail senden** die Option **Text** aus.

      ![Screenshot, der die offene Liste dynamischer Inhalte zeigt. In der Liste ist unter der Kopfzeile „E-Mail senden“ der Ausgabewert „Text“ ausgewählt.](./media/create-single-tenant-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Wenn Sie fertig sind, wird die Eigenschaft **Text** der Antwortaktion auf den Ausgabewert von **Text** für die Aktion **E-Mail senden** festgelegt.

      ![Screenshot, der den Status für jeden Schritt im Workflow sowie die Ein- und Ausgaben in der erweiterten Aktion „Antwort“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/response-action-details-body-property.png)

1. Wählen Sie im Designer **Speichern** aus.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Erneutes Testen Ihrer Logik-App

Nachdem Sie Aktualisierungen an Ihrer Logik-App vorgenommen haben, können Sie einen weiteren Test ausführen, indem Sie den Debugger in Visual Studio erneut ausführen und eine weitere Anforderung senden, um Ihre aktualisierte Logik-App auszulösen, ähnlich wie die Schritte unter [Lokales Ausführen, Testen und Debuggen](#run-test-debug-locally).

1. Öffnen Sie auf der Aktivitätsleiste von Visual Studio Code das Menü **Ausführen**, und wählen Sie **Debuggen starten** (F5) aus.

1. Senden Sie in Postman oder Ihrem Tool zum Erstellen und Senden von Anforderungen eine weitere Anforderung, um Ihren Workflow auszulösen.

1. Wenn Sie einen zustandsbehafteten Workflow erstellt haben, überprüfen Sie auf der Übersichtsseite des Workflows den Status der letzten Ausführung. Wählen Sie zum Anzeigen des Status, der Ein- und Ausgaben für jeden Schritt in dieser Ausführung die Schaltfläche mit den Auslassungspunkten ( **...** ) für diese Ausführung aus, und wählen Sie **Ausführung anzeigen** aus.

   Hier ist beispielsweise der schrittweise Status einer Ausführung, nachdem der Beispielworkflow mit der Antwortaktion aktualisiert wurde.

   ![Screenshot, der den Status für jeden Schritt im aktualisierten Workflow sowie die Ein- und Ausgaben in der erweiterten Aktion „Antwort“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/run-history-details-rerun.png)

1. Um die Debugsitzung zu beenden, wählen Sie im menü **Ausführen** den Eintrag **Debuggen beenden** (UMSCHALT+F5) aus.

<a name="firewall-setup"></a>

## <a name="find-domain-names-for-firewall-access"></a>Suchen von Domänennamen für den Firewallzugriff

Bevor Sie Ihren Logik-App-Workflow im Azure-Portal bereitstellen und ausführen, müssen Sie, wenn in Ihrer Umgebung strenge Netzwerkanforderungen gelten oder Firewalls vorhanden sind, die den Datenverkehr einschränken, Berechtigungen für alle Trigger- oder Aktionsverbindungen, die in Ihrem Workflow vorhanden sind, einrichten.

Führen Sie die folgenden Schritte aus, um die vollqualifizierten Domänennamen (FQDNs) für diese Verbindungen zu ermitteln:

1. Öffnen Sie in Ihrem Logik-App-Projekt die Datei **connections.json**, die erstellt wird, nachdem Sie Ihrem Workflow den ersten verbindungsbasierten Trigger oder eine Aktion hinzugefügt haben, und suchen Sie das `managedApiConnections`-Objekt.

1. Für jede von Ihnen erstellte Verbindung kopieren und speichern Sie den `connectionRuntimeUrl`-Eigenschaftswert an einem sicheren Ort, damit Sie Ihre Firewall mit diesen Informationen einrichten können.

   Diese Beispieldatei **connections.json** enthält zwei Verbindungen, eine AS2-Verbindung und eine Office 365-Verbindung mit diesen `connectionRuntimeUrl`-Werten:

   * AS2: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Bereitstellen in Azure

In Visual Studio Code können Sie Ihr Projekt direkt in Azure veröffentlichen, wo Ihre Logik-App mit dem Ressourcentyp **Logik-App (Standard)** bereitgestellt wird. Sie können Ihre Logik-App als neue Ressource veröffentlichen. Hierbei werden automatisch alle erforderlichen Ressourcen erstellt, z. B. ein [Azure-Speicherkonto (ähnlich wie bei Funktions-App-Anforderungen)](../azure-functions/storage-considerations.md). Alternativ können Sie Ihre Logik-App in einer zuvor bereitgestellten **Logik-App**-Ressource (Standard) veröffentlichen, die diese Logik-App überschreibt.

Für die Bereitstellung für den Ressourcentyp **Logik-App (Standard)** sind ein Hostingplan und ein Tarif erforderlich, den Sie während der Bereitstellung auswählen. Weitere Informationen finden Sie unter [Hostingpläne und Tarife](logic-apps-pricing.md#standard-pricing).

<a name="publish-new-logic-app"></a>

### <a name="publish-to-a-new-logic-app-standard-resource"></a>Veröffentlichen als neue Ressource „Logik-App (Standard)“

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus.

1. Wählen Sie auf der Symbolleiste des Bereichs **Azure: Logic Apps (Standard)** die Option **In Logik-App bereitstellen** aus.

   ![Screenshot, der den Bereich „Azure: Logic Apps (Standard)“ und die Symbolleiste des Bereichs mit ausgewähltem „In Logik-App bereitstellen“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Wählen Sie bei entsprechender Aufforderung das Azure-Abonnement aus, das für Ihre Logik-App-Bereitstellung verwendet werden soll.

1. Wählen Sie in der Liste, die Visual Studio Code öffnet, aus den folgenden Optionen aus:

   * **Neue Logik-App (Standard) in Azure erstellen** (schnell)
   * **Neue Logik-App (Standard) in Azure Advanced erstellen**
   * Eine zuvor bereitgestellte **Logik-App (Standard)** -Ressource, falls vorhanden

   Dieses Beispiel wird mit **Neue Logik-App (Standard) in Azure Advanced erstellen** fortgesetzt.

   ![Screenshot, der den Bereich „Azure: Logic Apps (Standard)“ und eine Liste mit ausgewähltem „Neue Logik-App (Standard) in Azure erstellen“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Um Ihre neue **Logik-App (Standard)** -Ressource zu erstellen, gehen Sie wie folgt vor:

   1. Geben Sie einen global eindeutigen Namen für Ihre neue Logik-App an, wobei es sich um den Namen handelt, der für die **Logik-App (Standard)** -Ressource verwendet werden soll. In diesem Beispiel wird `Fabrikam-Workflows-App` verwendet.

      ![Screenshot, der den Bereich „Azure: Logic Apps (Standard)“ und eine Aufforderung zur Eingabe eines Namens für die neue, zu erstellende Logik-App zeigt.](./media/create-single-tenant-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Wählen Sie einen Hostingplan für Ihre neue Logik-App aus. Erstellen Sie entweder einen Namen für Ihren Plan, oder wählen Sie einen vorhandenen Plan aus. In diesem Beispiel wird **Neuen App Service-Plan erstellen** ausgewählt.

      ![Screenshot, der den Bereich „Azure: Logic Apps (Standard)“und eine Aufforderung zum „Neuen App Service-Plan erstellen“ oder zum Auswählen eines vorhandenen App Service-Plans zeigt.](./media/create-single-tenant-workflows-visual-studio-code/create-app-service-plan.png)

   1. Geben Sie einen Namen für Ihren Hostingplan an, und wählen Sie dann einen Tarif für Ihren ausgewählten Plan aus.

      Weitere Informationen finden Sie unter [Hostingpläne und Tarife](logic-apps-pricing.md#standard-pricing).

   1. Wählen Sie dieselbe Ressourcengruppe wie die für die Bereitstellung des Projekts verwendete aus, um eine optimale Leistung zu erzielen.

      > [!NOTE]
      > Obwohl Sie eine andere Ressourcengruppe erstellen oder verwenden können, könnte sich dies negativ auf die Leistung auswirken. Wenn Sie eine andere Ressourcengruppe erstellen oder auswählen, nach der Anzeige der Bestätigungsaufforderung aber abbrechen, wird Ihre Bereitstellung ebenfalls abgebrochen.

   1. Wählen Sie für zustandsbehaftete Workflows **Neues Speicherkonto erstellen** oder ein vorhandenes Speicherkonto aus.

      ![Screenshot, der den Bereich „Azure: Logic Apps (Standard)“und eine Aufforderung zum Erstellen oder Auswählen eines Speicherkontos zeigt.](./media/create-single-tenant-workflows-visual-studio-code/create-storage-account.png)

   1. Wenn die Erstellungs- und Bereitstellungseinstellungen Ihrer Logik-App die Verwendung von [Application Insights](../azure-monitor/app/app-insights-overview.md) unterstützen, können Sie optional die Diagnoseprotokollierung und Ablaufverfolgung für Ihre Logik-App aktivieren. Dies können Sie entweder tun, wenn Sie Ihre Logik-App über Visual Studio Code bereitstellen, oder nach der Bereitstellung. Sie benötigen eine Application Insights-Instanz, können aber diese Ressource entweder [im Voraus](../azure-monitor/app/create-workspace-resource.md), beim Bereitstellen Ihrer Logik-App oder nach der Bereitstellung erstellen.

      Gehen Sie folgendermaßen vor, um die Protokollierung und Ablaufverfolgung jetzt zu aktivieren:

      1. Wählen Sie entweder eine vorhandene Application Insights-Ressource, oder **erstellen Sie eine neue Application Insights-Ressource**.

      1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Application Insights-Ressource.

      1. Wählen Sie im Ressourcenmenü **Übersicht** aus. Suchen Sie den **Instrumentierungsschlüssel**, und kopieren Sie ihn.

      1. Öffnen Sie in Visual Studio Code im Stammordner Ihres Projekts die Datei **local.settings.json**.

      1. Fügen Sie im `Values`-Objekt die `APPINSIGHTS_INSTRUMENTATIONKEY`-Eigenschaft hinzu, und legen Sie den Wert auf den Instrumentierungsschlüssel fest, z. B.:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Sie können überprüfen, ob die Trigger- und Aktionsnamen in der Application Insights-Instanz ordnungsgemäß angezeigt werden.
         >
         > 1. Navigieren Sie im Azure-Portal zu Ihrer Application Insights-Ressource.
         >
         > 2. Wählen Sie im Ressourcenmenü unter **Untersuchen** die Option **Anwendungszuordnung** aus.
         >
         > 3. Überprüfen Sie die Vorgangsnamen, die in der Zuordnung angezeigt werden.
         >
         > Einige eingehende Anforderungen von integrierten Triggern werden in der Anwendungszuordnung möglicherweise als Duplikate angezeigt. 
         > Anstatt das `WorkflowName.ActionName`-Format zu verwenden, verwenden diese Duplikate den Workflownamen als Vorgangsnamen und haben ihren Ursprung auf dem Azure Functions-Host.

      1. Anschließend können Sie den Schweregrad der Ablaufverfolgungsdaten, die ihre Logik-App sammelt und an Ihre Application Insights-Instanz sendet, optional anpassen.

         Bei jedem workflowbezogenen Ereignis, wenn z. B. ein Workflow ausgelöst oder eine Aktion ausgeführt wird, gibt die Runtime verschiedene Ablaufverfolgungen aus. Diese Ablaufverfolgungen decken die Lebensdauer des Workflows ab und umfassen die folgenden Ereignistypen, sind aber nicht darauf beschränkt:

         * Dienstaktivität, z. B. Starten, Beenden und Fehler.
         * Aufträge und Verteileraktivität.
         * Workflowaktivität, z. B. Trigger, Aktion und Ausführen.
         * Speicheranforderungsaktivität, z. B. Erfolg oder Fehler.
         * HTTP-Anforderungsaktivität, z. B. eingehend, ausgehend, Erfolg und Fehler.
         * Alle Entwicklungsablaufverfolgungen, z. B. Debugmeldungen.

         Jeder Ereignistyp wird einem Schweregrad zugewiesen. Beispielsweise erfasst die `Trace`-Ebene die detailliertesten Nachrichten, während die `Information`-Ebene allgemeine Aktivitäten in Ihrem Workflow erfasst, z. B. wann Logik-App, Workflow, Trigger und Aktionen gestartet und beendet werden. In dieser Tabelle werden die Schweregrade und deren Ablaufverfolgungstypen beschrieben:

         | Schweregrad | Ablaufverfolgungstyp |
         |----------------|------------|
         | Kritisch | Protokolle, in denen ein nicht behebbarer Fehler in Ihrer Logik-App beschrieben wird. |
         | Debuggen | Protokolle, die Sie für die Untersuchung während der Entwicklung verwenden können, z. B. eingehende und ausgehende HTTP-Aufrufe. |
         | Fehler | Protokolle, die auf einen Fehler bei der Workflowausführung hindeuten, aber nicht auf einen allgemeinen Fehler in Ihrer Logik-App. |
         | Information | Protokolle, die die allgemeine Aktivität in ihrer Logik-App oder im Workflow nachverfolgen, z. B.: <p><p>– Wann ein Trigger, eine Aktion oder eine Ausführung gestartet und beendet wird. <br>– Wann Ihre Logik-App gestartet oder beendet wird. |
         | Trace | Protokolle, die die detailliertesten Meldungen enthalten, z. B. Speicheranforderungen oder Verteileraktivität, sowie alle Meldungen, die sich auf die Workflowausführungsaktivität beziehen. |
         | Warnung | Protokolle, die einen ungewöhnlichen Status in Ihrer Logik-App hervorheben, jedoch nicht verhindern, dass sie ausgeführt wird. |
         |||

         Um den Schweregrad festzulegen, öffnen Sie auf der Stammebene des Projekts die Datei **host.json**, und suchen Sie nach dem `logging`-Objekt. Dieses Objekt steuert die Protokollfilterung für alle Workflows in Ihrer Logik-App und folgt dem [ASP.NET Core-Layout für die Protokolltypfilterung](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Wenn das `logging`-Objekt kein `logLevel`-Objekt enthält, das die `Host.Triggers.Workflow`-Eigenschaft enthält, fügen Sie diese Elemente hinzu. Legen Sie die Eigenschaft auf den Schweregrad für den gewünschten Ablaufverfolgungstyp fest, z. B.:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Wenn Sie die Bereitstellungsschritte abgeschlossen haben, beginnt Visual Studio Code mit der Erstellung und Bereitstellung der Ressourcen, die für das Veröffentlichen Ihrer Logik-App erforderlich sind.

1. Um den Bereitstellungsprozess zu überwachen und zu überprüfen, wählen Sie im Menü **Ansicht** die Option **Ausgabe** aus. Wählen Sie in der Liste der Symbolleiste des Ausgabefensters **Azure Logic Apps** aus.

   ![Screenshot, der das Ausgabefenster mit in der Symbolleistenliste ausgewähltem „Azure Logic Apps“ zusammen mit Bereitstellungsfortschritt und -statuswerten zeigt.](./media/create-single-tenant-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Wenn Visual Studio Code die Bereitstellung Ihrer Logik-App in Azure abschließt, wird diese Meldung angezeigt:

   ![Screenshot, der eine Meldung zeigt, dass die Bereitstellung in Azure erfolgreich abgeschlossen wurde.](./media/create-single-tenant-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Herzlichen Glückwunsch, ihre Logik-App ist jetzt live in Azure und standardmäßig aktiviert.

Als Nächstes erfahren Sie, wie Sie diese Aufgaben ausführen:

* [Hinzufügen eines leeren Workflows zum Projekt](#add-workflow-existing-project).

* [Verwalten bereitgestellter Logik-Apps in Visual Studio Code](#manage-deployed-apps-vs-code) oder mithilfe des [Azure-Portals](#manage-deployed-apps-portal).

* [Aktivieren des Ausführungsverlaufs für zustandslose Workflows](#enable-run-history-stateless).

* [Aktivieren der Überwachungsansicht im Azure-Portal für eine bereitgestellte Logik-App](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Hinzufügen eines leeren Workflows zum Projekt

Ihr Logik-App-Projekt kann mehrere Workflows enthalten. Um dem Projekt einen leeren Workflow hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Wählen Sie auf der Aktivitätsleiste von Visual Studio Code das Azure-Symbol aus.

1. Wählen Sie im Azure-Bereich neben **Azure: Logic Apps (Standard)** die Option **Workflow erstellen** (Symbol für Azure Logic Apps) aus.

1. Wählen Sie den Workflowtyp aus, den Sie hinzufügen möchten: **Zustandsbehaftet** oder **Zustandslos**.

1. Geben Sie einen Namen für den Workflow ein.

Wenn Sie fertig sind, wird in Ihrem Projekt ein neuer Workflowordner zusammen mit einer **workflow.json**-Datei für die Workflowdefinition angezeigt.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Verwalten bereitgestellter Logik-Apps in Visual Studio Code

In Visual Studio Code können Sie alle bereitgestellten Logik-Apps in Ihrem Azure-Abonnement anzeigen, unabhängig davon, ob es sich um den ursprünglichen **Logic Apps**- oder den **Logik-App (Standard)** -Ressourcentyp handelt, und Aufgaben auswählen, die Ihnen bei der Verwaltung dieser Logik-Apps helfen. Für den Zugriff auf beide Ressourcentypen benötigen Sie aber beide Erweiterungen für Visual Studio Code: **Azure Logic Apps** und **Azure Logic Apps (Standard)** .

1. Wählen Sie auf der linken Symbolleiste das Azure-Symbol aus. Wählen Sie im Bereich **Azure: Logic Apps (Standard)** Ihr Abonnement, in dem alle bereitgestellten Logik-Apps für dieses Abonnement angezeigt werden.

1. Öffnen Sie die Logik-App, die Sie verwalten möchten. Wählen Sie im Kontextmenü der Logik-App die Aufgabe aus, die Sie durchführen möchten.

   Beispielsweise können Sie Aufgaben wie das Beenden, Starten, Neustarten oder Löschen Ihrer bereitgestellten Logik-App auswählen. Sie können [einen Workflow mithilfe des Azure-Portals deaktivieren oder aktivieren.](create-single-tenant-workflows-azure-portal.md#disable-enable-workflows)

   > [!NOTE]
   > Die Vorgänge „Logik-App beenden“ und „Logik-App löschen“ wirken sich auf unterschiedliche Weise auf Workflow-Instanzen aus. Weitere Informationen hierzu sind unter [Überlegungen zum Beenden von Logik-Apps](#considerations-stop-logic-apps) und [Überlegungen zum Löschen von Logik-Apps](#considerations-delete-logic-apps) zu lesen.

   ![Screenshot, der Visual Studio Code dem geöffneten Bereich der Erweiterung „Azure Logic Apps (Standard)“ und dem bereitgestellten Workflow zeigt.](./media/create-single-tenant-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Um alle Workflows in der Logik-App anzuzeigen, erweitern Sie Ihre Logik-App und dann den Knoten **Workflows**.

1. Um einen bestimmten Workflow anzuzeigen, öffnen Sie das Kontextmenü des Workflows, und wählen Sie **Im Designer öffnen** aus. Dadurch wird der Workflow im schreibgeschützten Modus geöffnet.

   Um den Workflow zu bearbeiten, haben Sie folgende Optionen:

   * Öffnen Sie in Visual Studio Code die **workflow.json**-Datei des Projekts im Workflow-Designer, nehmen Sie Ihre Änderungen vor, und stellen Sie Ihre Logik-App erneut in Azure bereit.

   * [Öffnen Sie Ihre Logik-App](#manage-deployed-apps-portal) im Azure-Portal. Anschließend können Sie Ihren Workflow öffnen, bearbeiten und speichern.

1. Um die bereitgestellte Logik-App im Azure-Portal zu öffnen, öffnen Sie das Kontextmenü der Logik-App, und wählen Sie **Im Portal öffnen** aus.

   Das Azure-Portal wird in Ihrem Browser geöffnet, Sie werden automatisch beim Portal angemeldet, wenn Sie bei Visual Studio Code angemeldet sind, und Ihre Logik-App wird angezeigt.

   ![Screenshot, der die Seite des Azure-Portals für Ihre Logik-App in Visual Studio Code zeigt.](./media/create-single-tenant-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Sie können sich auch gesondert beim Azure-Portal anmelden, Ihre Logik-App über das Suchfeld des Portals suchen und dann Ihre Logik-App aus der Ergebnisliste auswählen.

   ![Screenshot, der das Azure-Portal und die Suchleiste mit Suchergebnissen für die bereitgestellte Logik-App zeigt, die ausgewählt angezeigt wird.](./media/create-single-tenant-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="considerations-stop-logic-apps"></a>

### <a name="considerations-for-stopping-logic-apps"></a>Überlegungen zum Beenden von Logik-Apps

Das Beenden einer Logik-App wirkt sich wie folgt auf Workflow-Instanzen aus:

* Der Logic Apps-Dienst bricht alle laufenden und ausstehenden Ausführungen sofort ab.

* Der Logic Apps-Dienst erstellt keine neuen Workflowinstanzen und führt keine neuen Workflowinstanzen aus.

* Trigger werden nicht ausgelöst, wenn die definierten Bedingungen beim nächsten Mal erfüllt werden. Triggerzustände merken sich jedoch die Punkte, an denen die Logik-App beendet wurde. Wenn Sie die Logik-App erneut starten, wird der Trigger für alle nicht verarbeiteten Elemente seit der letzten Ausführung ausgelöst.

  Um das Auslösen eines Triggers für nicht verarbeitete Elemente seit der letzten Ausführung zu verhindern, löschen Sie den Triggerstatus, bevor Sie die Logik-App erneut starten:

  1. Wählen Sie in Visual Studio Code in der linken Symbolleiste das Symbol Azure aus. 
  1. Wählen Sie im Bereich **Azure: Logic Apps (Standard)** Ihr Abonnement, in dem alle bereitgestellten Logik-Apps für dieses Abonnement angezeigt werden.
  1. Erweitern Sie Ihre Logik-App, und erweitern Sie dann den Knoten mit der Bezeichnung **Workflows**.
  1. Öffnen Sie einen Workflow, und bearbeiten Sie einen beliebigen Teil des Triggers dieses Workflows.
  1. Speichern Sie die Änderungen. Durch diesen Schritt wird der aktuelle Status des Triggers zurückgesetzt.
  1. Wiederholen Sie dies für jeden Workflow.
  1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App neu.

<a name="considerations-delete-logic-apps"></a>

### <a name="considerations-for-deleting-logic-apps"></a>Überlegungen zum Beenden von Logik-Apps

Das Löschen einer Logik-App wirkt sich wie folgt auf Workflow-Instanzen aus:

* Der Logic Apps-Dienst wird in Bearbeitung und ausstehende Ausführungen sofort abgebrochen, führt jedoch keine Bereinigungsaufgaben für den von der App verwendeten Speicher aus.

* Der Logic Apps-Dienst erstellt keine neuen Workflowinstanzen und führt keine neuen Workflowinstanzen aus.

* Wenn Sie einen Workflow löschen und dann denselben Workflow neu erstellen, hat der neu erstellte Workflow nicht die gleichen Metadaten wie der gelöschte Workflow. Zum Aktualisieren der Metadaten müssen Sie jeden Workflow, der den gelöschten Workflow aufgerufen hat, neu speichern. Auf diese Weise ruft der Aufrufer die richtigen Informationen für den neu erstellten Workflow ab. Andernfalls schlagen Aufrufe des neu erstellten Workflows mit einem `Unauthorized`-Fehler fehl. Dieses Verhalten gilt auch für Workflows, die Artefakte in Integrationskonten und Workflows verwenden, welche Azure-Funktionen aufrufen.

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Verwalten bereitgestellter Logik-Apps im Portal

Wenn Sie eine Logik-App im Azure-Portal von Visual Studio Code einsetzen, können Sie alle bereitgestellten Logik-Apps anzeigen, die in Ihrem Azure-Abonnement enthalten sind, unabhängig davon, ob es sich um den ursprünglichen **Logik-App**-Ressourcentyp oder den **Logik-App (Standard)** -Ressourcentyp handelt. Zurzeit wird jeder Ressourcentyp in Azure als separate Kategorien organisiert und verwaltet. Um Logik-Apps zu finden, die vom Ressourcentyp **Logik-App (Standard)** sind, gehen Sie wie folgt vor:

1. Geben Sie in das Suchfeld des Azure-Portals `logic apps` ein. Wenn die Ergebnisliste angezeigt wird, wählen Sie unter **Dienste** die Option **Logik-Apps** aus.

   ![Screenshot, der das Suchfeld des Azure-Portals mit dem Suchtext „Logik-Apps“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/portal-find-logic-app-resource.png)

1. Wählen Sie im Bereich **Logik-App (Standard)** die Logik-App aus, die Sie aus Visual Studio Code bereitgestellt haben.

   ![Screenshot, der das Azure-Portal und die in Azure bereitgestellten Logic App (Standard)-Ressourcen zeigt.](./media/create-single-tenant-workflows-visual-studio-code/logic-app-resources-pane.png)

   Das Azure-Portal öffnet die Seite der einzelnen Ressource für die ausgewählte Logik-App.

   ![Screenshot, der die Ressourcenseite Ihres Logik-App-Workflows im Azure-Portal zeigt.](./media/create-single-tenant-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Um die Workflows in dieser Logik-App auszuwählen, wählen im Menü der Logik-App **Workflows** aus.

   Im Bereich **Workflows** werden alle in der aktuellen Logik-App vorhandenen Workflows angezeigt. Dieses Beispiel zeigt den Workflow, den Sie in Visual Studio Code erstellt haben.

   ![Screenshot, der eine „Logik-App (Standard)“-Ressourcenseite mit geöffnetem Bereich „Workflows“ und dem bereitgestellten Workflow zeigt.](./media/create-single-tenant-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Um einen Workflow anzuzeigen, wählen Sie im Bereich **Workflows** diesen Workflow aus.

   Der Workflowbereich wird geöffnet und zeigt weitere Informationen und Aufgaben an, die Sie für diesen Workflow ausführen können.

   Um beispielsweise die Schritte in dem Workflow anzuzeigen, wählen Sie **Designer** aus.

   ![Screenshot, der den Bereich „Übersicht“ des ausgewählten Workflows zeigt, während im Menü „Workflow“ der ausgewählte Befehl „Designer“ angezeigt wird.](./media/create-single-tenant-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Der Workflow-Designer wird geöffnet und zeigt den Workflow an, den Sie in Visual Studio Code erstellt haben. Sie können jetzt Änderungen an diesem Workflow im Azure-Portal vornehmen.

   ![Screenshot, der den Workflow-Designer und den aus Visual Studio Code bereitgestellten Workflow zeigt.](./media/create-single-tenant-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Hinzufügen eines weiteren Workflows im Portal

Über das Azure-Portal können Sie einer **Logik-App**-Ressource (Standard), die Sie aus Visual Studio Code bereitgestellt haben, leere Workflows hinzufügen und diese Workflows im Azure-Portal erstellen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihre bereitgestellte **Logik-App (Standard)** -Ressource aus.

1. Wählen Sie im Menü der Logik-App **Workflows** aus. Wählen Sie im Bereich **Workflows** den Befehl **Hinzufügen** aus.

   ![Screenshot, der den Bereich „Workflows“ der ausgewählten Logik-App und die Symbolleiste mit ausgewähltem Befehl „Hinzufügen“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/add-new-workflow.png)

1. Geben Sie im Bereich **Neuer Workflow** einen Namen für den Workflow an. Wählen Sie entweder **Zustandsbehaftet** oder **Zustandslos** **>** **Erstellen** aus.

   Nachdem Azure Ihren neuen Workflow bereitgestellt hat, der im Bereich **Workflows** angezeigt wird, wählen Sie diesen Workflow aus, um die Verwaltung und andere Aufgaben auszuführen, z. B. das Öffnen des Designers oder der Codeansicht.

   ![Screenshot, der den ausgewählten Workflow mit Verwaltungs- und Überprüfungsoptionen zeigt.](./media/create-single-tenant-workflows-visual-studio-code/view-new-workflow.png)

   Wenn Sie z. B. den Designer für einen neuen Workflow öffnen, wird ein leerer Zeichenbereich angezeigt. Sie können diesen Workflow jetzt im Azure-Portal erstellen.

   ![Screenshot, der den Workflow-Designer und einen leeren Workflow zeigt.](./media/create-single-tenant-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Aktivieren des Ausführungsverlaufs für zustandslose Workflows

Damit Sie einen zustandslosen Workflow debuggen können, können Sie den Ausführungsverlauf für diesen Workflow aktivieren und wieder deaktivieren, wenn Sie fertig sind. Führen Sie diese Schritte für Visual Studio Code aus. Wenn Sie im Azure-Portal arbeiten, lesen Sie [Erstellen von Workflows für Instanzen mit einem Mandanten im Azure-Portal](create-single-tenant-workflows-azure-portal.md#enable-run-history-stateless).

1. Erweitern Sie in Ihrem Visual Studio Code-Projekt den Ordner **workflow-designtime**, und öffnen Sie die Datei **local.settings.json**.

1. Fügen Sie die Eigenschaft `Workflows.{yourWorkflowName}.operationOptions` hinzu, und legen Sie deren Wert auf `WithStatelessRunHistory` fest, z. B.:

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS oder Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Um den Ausführungsverlauf zu deaktivieren, wenn Sie fertig sind, legen Sie entweder die Eigenschaft `Workflows.{yourWorkflowName}.OperationOptions` auf `None` fest oder löschen die Eigenschaft und ihren Wert.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Aktivieren der Überwachungsansicht im Azure-Portal

Nachdem Sie eine Ressource von **Logic Apps (Standard)** aus Visual Studio Code in Azure bereitgestellt haben, können Sie jeden verfügbaren Ausführungsverlauf und die Details für einen Workflow in dieser Ressource überprüfen, indem Sie das Azure-Portal und die **Monitor**-Umgebung für diesen Workflow verwenden. Sie müssen jedoch zunächst die **Monitor**-Ansichtsfunktion für diese Logik-App-Ressource aktivieren.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die bereitgestellte **Logik-App (Standard)** -Ressource aus.

1. Wählen Sie im Menü dieser Ressource unter **API** die Option **CORS** aus.

1. Fügen Sie im Bereich **CORS** unter **Zulässige Ursprünge** das Platzhalterzeichen (*) hinzu.

1. Wenn Sie fertig sind, wählen Sie auf der **CORS**-Symbolleiste **Speichern** aus.

   ![Screenshot, der das Azure-Portal mit einer bereitgestellten Ressource von „Logic Apps (Standard)“ zeigt. Im Ressourcenmenü ist „CORS“ ausgewählt, mit einem neuen Eintrag für „Zulässige Ursprünge“, der auf das Platzhalterzeichen „*“ festgelegt ist.](./media/create-single-tenant-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Aktivieren oder Öffnen von Application Insights nach der Bereitstellung

Während der Workflowausführung gibt Ihre Logik-App Telemetriedaten zusammen mit anderen Ereignissen aus. Sie können diese Telemetrie verwenden, um auf verschiedene Weise einen besseren Einblick in die Qualität der Workflowausführung und die Arbeitsweise der Logic Apps-Runtime zu bekommen. Sie können den Workflow überwachen, indem Sie [Application Insights](../azure-monitor/app/app-insights-overview.md) verwenden, wodurch nahezu in Echtzeit Telemetriedaten (Livemetriken) bereitgestellt werden. Mithilfe dieser Funktion können Sie Fehler und Leistungsprobleme leichter untersuchen, wenn Sie diese Daten zum Diagnostizieren von Problemen, Einrichten von Warnungen und Erstellen von Diagrammen verwenden.

Wenn die Erstellungs- und Bereitstellungseinstellungen Ihrer Logik-App die Verwendung von [Application Insights](../azure-monitor/app/app-insights-overview.md) unterstützen, können Sie optional die Diagnoseprotokollierung und Ablaufverfolgung für Ihre Logik-App aktivieren. Dies können Sie entweder tun, wenn Sie Ihre Logik-App über Visual Studio Code bereitstellen, oder nach der Bereitstellung. Sie benötigen eine Application Insights-Instanz, können aber diese Ressource entweder [im Voraus](../azure-monitor/app/create-workspace-resource.md), beim Bereitstellen Ihrer Logik-App oder nach der Bereitstellung erstellen.

Gehen Sie folgendermaßen vor, um Application Insights auf einer bereitgestellten Logik-App zu aktivieren oder Application Insights-Daten zu überprüfen, wenn sie bereits aktiviert sind:

1. Suchen Sie im Azure-Portal nach Ihrer bereitgestellten Logik-App.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Application Insights** aus.

1. Wenn Application Insights nicht aktiviert ist, wählen Sie im Bereich **Application Insights** die Option **Application Insights aktivieren** aus. Nachdem der Bereich aktualisiert wurde, wählen Sie unten **Anwenden** aus.

   Wenn Application Insights aktiviert ist, wählen Sie im Bereich **Application Insights** die Option **Application Insights-Daten anzeigen** aus.

Nachdem Application Insights geöffnet wurde, können Sie verschiedene Metriken für Ihre Logik-App überprüfen. Weitere Informationen finden Sie in diesen Themen:

* [Azure Logic Apps ohne Grenzen ausführen: Überwachung mit Application Insights (Teil 1)](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps ohne Grenzen ausführen: Überwachung mit Application Insights (Teil 2)](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Löschen von Elementen aus dem Designer

Führen Sie einen der folgenden Schritte aus, um ein Element in Ihrem Workflow aus dem Designer zu löschen:

* Wählen Sie das Element aus, öffnen Sie das Kontextmenü des Elements (UMSCHALT+F10), und wählen Sie **Löschen** aus. Klicken Sie auf **OK**, um dies zu bestätigen.

* Wählen Sie das Element aus, und drücken Sie dann die ENTF-Taste. Klicken Sie auf **OK**, um dies zu bestätigen.

* Wählen Sie das Element aus, sodass der Detailbereich für dieses Element geöffnet wird. Öffnen Sie in der oberen rechten Ecke des Bereichs das Menü mit den Auslassungspunkten ( **...** ), und wählen Sie **Löschen** aus. Klicken Sie auf **OK**, um dies zu bestätigen.

  ![Screenshot, der ein ausgewähltes Element im Designer mit geöffnetem Detailbereich sowie die ausgewählte Schaltfläche mit Auslassungspunkten und den Befehl „Löschen“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Wenn das Menü mit den Auslassungspunkten nicht sichtbar ist, erweitern Sie das Visual Studio Code-Fenster, sodass im Detailbereich die Schaltfläche mit den Auslassungspunkten ( **...** ) in der oberen rechten Ecke angezeigt wird.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Beheben von Fehlern und Problemen

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Designer wird nicht geöffnet

Beim Versuch, den Designer zu öffnen, erhalten Sie einen Fehler wie **Workflowentwurfszeit konnte nicht gestartet werden**. Falls Sie schon einmal versucht haben, den Designer zu öffnen, und Ihr Projekt anschließend nicht weiter verfolgt oder gelöscht haben, wird das Erweiterungspaket ggf. nicht richtig heruntergeladen. Führen Sie die folgenden Schritte aus, um zu überprüfen, ob dies die Ursache des Problems ist:

  1. Öffnen Sie in Visual Studio Code das Fenster „Ausgabe“. Wählen Sie im Menü **Ansicht** die Option **Ausgabe** aus.

  1. Wählen Sie in der Liste auf der Titelleiste des Fensters „Ausgabe“ **Azure Logic Apps (Standard)** aus, damit Sie die Ausgabe für die Erweiterung überprüfen können, z. B.:

     ![Screenshot, der das Fenster „Ausgabe“ mit ausgewählten „Azure Logic Apps“ zeigt.](./media/create-single-tenant-workflows-visual-studio-code/check-output-window-azure-logic-apps.png)

  1. Überprüfen Sie die Ausgabe, und prüfen Sie, ob diese Fehlermeldung angezeigt wird:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Um diesen Fehler zu beheben, löschen Sie den Ordner **ExtensionBundles** am Speicherort **...\Benutzer\{Ihr-Benutzername}\AppData\Local\Temp\Functions\ExtensionBundles**. Versuchen Sie anschließend erneut, die Datei **workflow.json** im Designer zu öffnen.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>In der Designerauswahl für zuvor erstellte Workflows fehlen neue Trigger und Aktionen.

Azure Logic Apps mit einzelnem Mandanten unterstützt integrierte Aktionen für Azure-Funktionsvorgänge, Liquid-Vorgänge und XML-Vorgänge wie **XML-Validierung** und **Transformieren von XML**. Allerdings werden diese Aktionen für zuvor erstellte Logik-Apps möglicherweise nicht für Sie zur Auswahl in der Designerauswahl angezeigt, wenn Visual Studio Code eine veraltete Version des Erweiterungspakets `Microsoft.Azure.Functions.ExtensionBundle.Workflows` verwendet.

Außerdem werden der Connector für **Azure-Funktionsvorgänge** und Aktionen nicht in der Designerauswahl angezeigt, es sei denn, Sie haben **Connectors von Azure verwenden** beim Erstellen Ihrer Logik-App aktiviert oder ausgewählt. Wenn Sie die von Azure bereitgestellten Connectors zum Zeitpunkt der Erstellung der App nicht aktiviert haben, können Sie sie in Visual Studio Code von Ihrem Projekt aus aktivieren. Öffnen Sie das **workflow.json**-Kontextmenü, und wählen Sie **Connectors von Azure verwenden** aus.

Führen Sie zur Korrektur des Problems mit dem veralteten Paket die folgenden Schritte zum Löschen des veralteten Pakets aus. Dann aktualisiert Visual Studio Code das Erweiterungspaket automatisch auf die neueste Version.

> [!NOTE]
> Diese Lösung gilt nur für Logik-Apps, die Sie mit Visual Studio Code mit der Erweiterung „Azure Logic Apps (Standard)“ erstellen und bereitstellen, nicht die Logik-Apps, die Sie mit dem Azure-Portal erstellt haben. Siehe [Neue Trigger und Aktionen fehlen in der Designerauswahl für früher erstellte Workflows](create-single-tenant-workflows-azure-portal.md#missing-triggers-actions).

1. Speichern Sie sämtliche Arbeit, die Sie nicht verlieren möchten, und schließen Sie Visual Studio.

1. Navigieren Sie auf Ihrem Computer zum folgenden Ordner, der Ordner mit Versionsangaben für das vorhandene Paket enthält:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Löschen Sie den Versionsordner für das frühere Paket. Wenn Sie z. B. einen Ordner für Version 1.1.3 haben, löschen Sie diesen Ordner.

1. Navigieren Sie nun zum folgenden Ordner, der Ordner mit Versionsangaben für das erforderliche NuGet-Paket enthält:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Löschen Sie den Versionsordner für das vorherige Paket.

1. Öffnen Sie Visual Studio Code, Ihr Projekt und die **workflow.json**-Datei im Designer erneut.

Die fehlenden Trigger und Aktionen werden nun im Designer angezeigt.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>„400 – Ungültige Anforderung“ wird für einen Trigger oder eine Aktion angezeigt

Wenn Sie nach einem Fehler in einer Ausführung diese Ausführung in der Überwachungsansicht untersuchen, kann dieser Fehler bei einem Trigger oder einer Aktion mit einem längeren Namen angezeigt werden. Ein solcher Name kann dazu führen, dass der zugrunde liegende URI (Uniform Resource Identifier) das Standardzeichenlimit überschreitet.

Um dieses Problem zu beheben und längere URIs zuzulassen, bearbeiten Sie die Registrierungsschlüssel `UrlSegmentMaxCount` und `UrlSegmentMaxLength` auf Ihrem Computer. Führen Sie dazu die folgenden Schritte aus. Die Standardwerte dieser Schlüssel werden in diesem Thema beschrieben: [Http.sys-Registrierungseinstellungen für Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Speichern Sie Ihre bisherige Arbeit, bevor Sie beginnen. Diese Lösung erfordert nach Abschluss der Änderungen einen Neustart Ihres Computers, damit die Änderungen wirksam werden.

1. Öffnen Sie auf Ihrem Computer das Fenster **Ausführen**, und führen Sie den Befehl `regedit` aus. Dieser öffnet den Registrierungs-Editor.

1. Klicken Sie im Feld **Benutzerkontensteuerung** auf **Ja**, um Änderungen am Computer zuzulassen.

1. Erweitern Sie im linken Bereich unter **Computer** die Knoten im Pfad **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters**, und wählen Sie dann **Parameters** aus.

1. Suchen Sie im rechten Bereich nach den Registrierungsschlüsseln `UrlSegmentMaxCount` und `UrlSegmentMaxLength`.

1. Erhöhen Sie die Werte dieser Schlüssel so, dass die URIs die von Ihnen gewünschten Namen aufnehmen können. Wenn diese Schlüssel nicht vorhanden sind, fügen Sie sie mithilfe der folgenden Schritte zum Ordner **Parameters** hinzu:

   1. Wählen Sie im Kontextmenü für **Parameters** die Option **Neu** > **DWORD-Wert (32-Bit)** aus.

   1. Geben Sie im daraufhin angezeigten Bearbeitungsfeld `UrlSegmentMaxCount` als neuen Schlüsselnamen ein.

   1. Öffnen Sie das Kontextmenü des neuen Schlüssels, und wählen Sie **Ändern** aus.

   1. Geben Sie im angezeigten Feld **DWORD-Wert (32-Bit) bearbeiten** unter **Wertdaten** den gewünschten Schlüsselwert im Hexadezimal- oder Dezimalformat ein. Der Wert `400` im Hexadezimalformat entspricht beispielsweise dem Wert `1024` im Dezimalformat.

   1. Zum Hinzufügen des Schlüsselwerts `UrlSegmentMaxLength` wiederholen Sie diese Schritte.

   Nachdem Sie die Schlüsselwerte erhöht oder hinzugefügt haben, sieht der Registrierungs-Editor wie folgt aus:

   ![Screenshot des Registrierungs-Editors](media/create-single-tenant-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Wenn Sie fertig sind, starten Sie Ihren Computer neu, damit die Änderungen wirksam werden.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Debugsitzung wird nicht gestartet

Beim Versuch, eine Debugsitzung zu starten, erhalten Sie einen Fehler wie **Fehler nach der Ausführung von preLaunchTask „generateDebugSymbols“** . Bearbeiten Sie die Datei **tasks.json** in Ihrem Projekt so, dass die Symbolgenerierung übersprungen wird, um dieses Problem zu beheben.

1. Erweitern Sie in Ihrem Projekt den Ordner **.vscode**, und öffnen Sie die Datei **tasks.json**.

1. Löschen Sie in der folgenden Aufgabe die Zeile `"dependsOn: "generateDebugSymbols"` (einschließlich des Kommas, mit dem die vorherige Zeile abgeschlossen wird). Beispiel:

   Vorher:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Nachher:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Nächste Schritte

Teilen Sie uns bitte Ihre Erfahrungen mit der Erweiterung „Azure Logic Apps (Standard)“ mit!

* Bei Fehlern oder Problemen [erstellen Sie Ihre Probleme in GitHub](https://github.com/Azure/logicapps/issues).
* Bei Fragen, Anforderungen, Kommentaren und anderem Feedback [verwenden Sie dieses Feedbackformular](https://aka.ms/lafeedback).
