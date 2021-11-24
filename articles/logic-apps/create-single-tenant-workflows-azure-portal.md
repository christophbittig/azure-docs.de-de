---
title: Erstellen von Workflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal
description: Erstellen Sie automatisierte Workflows zur Integration von Apps, Daten, Diensten und Systemen mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/05/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 72e869b1439e5fd9bcb77af57bd4e0d3d8f6a677
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324800"
---
# <a name="create-an-integration-workflow-with-single-tenant-azure-logic-apps-standard-in-the-azure-portal"></a>Erstellen eines Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal

In diesem Artikel wird anhand eines Beispiels gezeigt, wie Sie im Azure-Portal mit dem Ressourcentyp **Logik-App (Standard)** einen automatisierten Integrationsworkflow erstellen, der in der Azure Logic Apps-Umgebung *mit einzelnem Mandanten* ausgeführt wird. Dieser Ressourcentyp kann mehrere [zustandsbehaftete und zustandslose Workflows](single-tenant-overview-compare.md#stateful-stateless) hosten. Zudem werden Workflows in derselben Logik-App und demselben Mandanten im gleichen Prozess wie die neu gestaltete Azure Logic Apps-Runtime ausgeführt, sodass sie dieselben Ressourcen gemeinsam nutzen und eine bessere Leistung bieten. Weitere Informationen zum Einzelmandantenangebot von Azure Logic Apps finden Sie unter [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](single-tenant-overview-compare.md).

Obwohl dieser Beispielworkflow cloudbasiert ist und nur zwei Schritte umfasst, können Sie Workflows aus Hunderten von Vorgängen erstellen, die eine Vielzahl von Apps, Daten, Diensten und Systemen über Cloud-, lokale und Hybridumgebungen hinweg verbinden können. Der Beispielworkflow beginnt mit dem integrierten Anforderungstrigger und folgt mit einer Office 365 Outlook-Aktion. Der Trigger erstellt einen aufrufbaren Endpunkt für den Workflow und wartet auf eine eingehende HTTPS-Anforderung einer beliebigen aufrufenden Funktion. Wenn der Trigger eine Anforderung erhält und auslöst, wird die nächste Aktion ausgeführt, indem eine E-Mail an die angegebene E-Mail-Adresse zusammen mit den ausgewählten Ausgaben des Triggers gesendet wird.

> [!TIP]
> Wenn Sie nicht über ein Office 365-Konto verfügen, können Sie jede andere verfügbare Aktion verwenden, die Nachrichten von Ihrem E-Mail-Konto senden kann, z. B. Outlook.com.
>
> Wenn Sie diesen Beispielworkflow stattdessen in Visual Studio Code erstellen möchten, führen Sie die Schritte unter [Erstellen eines Integrationsworkflows mit Azure Logic Apps mit einzelnem Mandanten und Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) aus. 
> Beide Optionen bieten die Möglichkeit, Logik-App-Workflows in denselben Arten von Umgebungen zu entwickeln, auszuführen und bereitzustellen. 
> Mit Visual Studio Code können Sie Workflows jedoch *lokal* in Ihrer Entwicklungsumgebung entwickeln, testen und ausführen.

![Screenshot, der das Azure-Portal mit dem Workflow-Designer für die „Logik-App (Standard)“ zeigt.](./media/create-single-tenant-workflows-azure-portal/azure-portal-logic-apps-overview.png)

Im Weiteren führen Sie diese allgemeinen Aufgaben aus:

* Erstellen Sie die Logik-App-Ressource, und fügen Sie einen leeren [*zustandsbehafteten*](single-tenant-overview-compare.md#stateful-stateless) Workflow hinzu.
* Hinzufügen eines Triggers und einer Aktion.
* Auslösen einer Workflowausführung.
* Anzeigen des Ausführungs- und Triggerverlaufs des Workflows
* Aktivieren oder Öffnen von Application Insights nach der Bereitstellung.
* Aktivieren des Ausführungsverlaufs für zustandslose Workflows.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ein [Azure Storage-Konto](../storage/common/storage-account-overview.md) Wenn Sie keines besitzen, können Sie entweder im Voraus oder während der Erstellung der Logik-App ein Speicherkonto erstellen.

  > [!NOTE]
  > Der Ressourcentyp **Logik-App (Standard)** wird von Azure Functions unterstützt und hat [ähnliche Speicheranforderungen wie Funktions-Apps](../azure-functions/storage-considerations.md). 
  > [Zustandsbehaftete Workflows](single-tenant-overview-compare.md#stateful-stateless) führen Speichertransaktionen wie die Verwendung von Warteschlangen zum Planen und Speichern von Workflowstatus in Tabellen und Blobs aus. Diese Transaktionen verursachen [Speichergebühren](https://azure.microsoft.com/pricing/details/storage/). Weitere Informationen darüber, wie zustandsbehaftete Workflows Daten im externen Speicher speichern, finden Sie unter [Zustandsbehaftete und zustandslose Workflows](single-tenant-overview-compare.md#stateful-stateless).

* Um denselben Beispielworkflow in diesem Artikel zu erstellen, benötigen Sie ein Office 365 Outlook-E-Mail-Konto, das ein Geschäfts-, Schul- oder Unikonto von Microsoft für die Anmeldung verwendet.

  Wenn Sie einen [anderen E-Mail-Connector](/connectors/connector-reference/connector-reference-logicapps-connectors) wählen, z. B. Outlook.com, können Sie dem Beispiel trotzdem folgen, und die allgemeinen Schritte sind dieselben. Ihre Optionen können sich jedoch in einigen Punkten unterscheiden. Wenn Sie beispielsweise den Outlook.com-Connector verwenden, verwenden Sie stattdessen Ihr persönliches Microsoft-Konto, um sich anzumelden.

* Um den Beispielworkflow in diesem Artikel zu testen, benötigen Sie ein Tool, das Aufrufe an den vom Anforderungstrigger erstellten Endpunkt senden kann. Falls Sie nicht über ein solches Tool verfügen, können Sie [Postman](https://www.postman.com/downloads/) herunterladen, installieren und verwenden.

* Wenn Sie Ihre Logik-App-Ressourcen mit Einstellungen erstellen, die die Verwendung von [Application Insights](../azure-monitor/app/app-insights-overview.md) unterstützen, können Sie optional die Diagnoseprotokollierung und Ablaufverfolgung für Ihre Logik-App aktivieren. Dies ist entweder beim Erstellen der Logik-App oder nach der Bereitstellung möglich. Sie benötigen eine Application Insights-Instanz, können aber diese Ressource entweder [im Voraus](../azure-monitor/app/create-workspace-resource.md), beim Erstellen Ihrer Logik-App oder nach der Bereitstellung erstellen.

* Um Ihre **Logik-App (Standard)-Ressource**  in einer [App Service-Umgebung v3 (ASEv3)](../app-service/environment/overview.md) bereitzustellen, müssen Sie diese Umgebungsressource zuerst erstellen. Dann können Sie diese Umgebung als Bereitstellungsort auswählen, wenn Sie Ihre Logik-App-Ressource erstellen. Weitere Informationen finden Sie unter [Ressourcentypen und Umgebungen](single-tenant-overview-compare.md#resource-environment-differences) und [Erstellen einer App Service-Umgebung](../app-service/environment/creation.md).

<a name="create-logic-app-resource"></a>

## <a name="create-the-logic-app-resource"></a>Erstellen der Logik-App-Ressource

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit den Anmeldeinformationen Ihres Azure-Kontos an.

1. Geben Sie in das Suchfeld des Azure-Portals `logic apps` ein, und wählen Sie **Logik-Apps** aus.

   ![Screenshot, der das Suchfeld des Azure-Portals mit dem Suchtext „Logik-Apps“ und der Gruppe „Logik-Apps“ zeigt.](./media/create-single-tenant-workflows-azure-portal/find-logic-app-resource-template.png)

1. Wählen Sie auf der Seite **Logic Apps** die Option **Hinzufügen** aus.

1. Geben Sie auf der Seite **Logik-App erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen zu Ihrer Logik-App-Ressource ein.

   | Eigenschaft | Erforderlich | Wert | Beschreibung |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Das für Ihre Logik-App zu verwendende Azure-Abonnement. |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Die Azure-Ressourcengruppe, in der Sie Ihre Logik-App und zugehörige Ressourcen erstellen. Dieser Ressourcenname muss regionsübergreifend eindeutig sein und darf nur Buchstaben, Ziffern, Bindestriche ( **-** ), Unterstriche ( **_** ), Klammern ( **()** ) und Punkte ( **.** ) enthalten. <p><p>In diesem Beispiel wird eine Ressourcengruppe namens `Fabrikam-Workflows-RG` erstellt. |
   | **Typ** | Ja | **Standard** | Dieser Typ von Logik-App-Ressource wird in der Azure Logic Apps-Umgebung mit einem einzelnen Mandanten ausgeführt und verwendet das [Nutzungs-, Abrechnungs- und Preismodell „Standard“](logic-apps-pricing.md#standard-pricing). |
   | **Name der Logik-App** | Ja | <*logic-app-name*> | Der für Ihre Logik-App zu verwendende Name. Dieser Ressourcenname muss regionsübergreifend eindeutig sein und darf nur Buchstaben, Ziffern, Bindestriche ( **-** ), Unterstriche ( **_** ), Klammern ( **()** ) und Punkte ( **.** ) enthalten. <p><p>In diesem Beispiel wird eine Logik-App namens `Fabrikam-Workflows` erstellt. <p><p>**Hinweis:** Der Name Ihrer Logik-App erhält automatisch das Suffix `.azurewebsites.net`, da die **Logik-App -Ressource (Standard)** von der Azure Logic Apps-Runtime mit einem Mandanten unterstützt wird, die das Azure Functions-Erweiterbarkeitsmodell verwendet und als Erweiterung auf der Azure Functions-Runtime gehostet wird. Azure Functions verwendet die gleiche Benennungskonvention für Apps. |
   | **Veröffentlichen** | Ja | <*deployment-environment*> | Das Bereitstellungsziel für Ihre Logik-App. Standardmäßig ist **Workflow** für die Bereitstellung in einzelinstanzenfähigen Azure Logic Apps ausgewählt. Azure erstellt eine leere Logik-App-Ressource, in der Sie Ihren ersten Workflow hinzufügen müssen. <p><p>**Hinweis**: Derzeit erfordert die Option **Docker-Container** einen [*benutzerdefinierten Standort*](../azure-arc/kubernetes/conceptual-custom-locations.md) in einem Kubernetes-Cluster mit Azure Arc-Unterstützung, den Sie mit [Azure Arc-fähigen Logik-Apps (Preview)](azure-arc-enabled-logic-apps-overview.md) verwenden können. Die Ressourcenstandorte für Ihre Logik-App, der benutzerdefinierte Standort und der Cluster müssen alle identisch sein. |
   | **Region** | Ja | <*Azure-Region*> | Der Standort, der für die Erstellung Ihrer Ressourcengruppe und Ressourcen verwendet werden soll. In diesem Beispiel wird die Beispiel-Logik-App in Azure bereitgestellt, und es wird **USA, Westen** verwendet. <p>– Wenn Sie **Docker-Container** ausgewählt haben, wählen Sie Ihren benutzerdefinierten Standort aus. <p>– Wählen Sie zum Bereitstellen in einer [ASEv3](../app-service/environment/overview.md)-Ressource, die zuerst vorhanden sein muss, diese Umgebungsressource aus der Liste **Region** aus. |
   |||||

   Das folgende Beispiel zeigt die Seite **Logik-App erstellen (Standard)** :

   ![Screenshot, der das Azure-Portal und die Seite „Logik-App erstellen“ zeigt.](./media/create-single-tenant-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Geben Sie die folgenden Informationen über die Speicherlösung und den Hostingplan, die für Ihre Logik-App verwendet werden sollen, auf der Registerkarte **Hosting** an.

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Speichertyp** | Ja | - **SQL und Azure Storage** <br>- **Azure Storage** | Dies ist der Speichertyp, den Sie für workflowbezogene Artefakte und Daten verwenden möchten. <p><p>– Um nur in Azure bereitzustellen, wählen Sie **Azure Storage.** <p><p>– Um SQL als primären Speicher und Azure Storage als sekundären Speicher zu verwenden, wählen Sie **SQL und Azure Storage** aus, und lesen Sie [Einrichten von SQL-Datenbankspeicher für Standard-Logik-Apps in einer Azure Logic Apps-Instanz mit nur einem Mandanten](set-up-sql-db-storage-single-tenant-standard-workflows.md). <p><p>**Hinweis:** Wenn Sie die Bereitstellung in einer Azure-Region durchführen, benötigen Sie weiterhin ein Azure Storage-Konto, das zum einmaligen Hosten der Konfiguration der Logik-App auf der Azure Logic Apps-Plattform verwendet wird. Der fortlaufende Workflowstatus, der Ausführungsverlauf und andere Runtime-Artefakte werden in Ihrer SQL-Datenbank gespeichert. <p><p>Für Bereitstellungen an einem benutzerdefinierten Speicherort, der in einem Azure Arc-Cluster gehostet wird, benötigen Sie nur SQL als Speicheranbieter. |
   | **Speicherkonto** | Ja | <*Azure-storage-account-name*> | Das [Azure Storage-Konto](../storage/common/storage-account-overview.md), das für Speichertransaktionen verwendet werden soll. <p><p>Dieser Ressourcenname muss regionsübergreifend eindeutig sein und 3-24 Zeichen enthalten (nur Ziffern und Kleinbuchstaben). Wählen Sie entweder ein vorhandenes Konto aus, oder erstellen Sie ein neues Konto. <p><p>In diesem Beispiel wird ein Speicherkonto namens `fabrikamstorageacct` erstellt. |
   | **Plantyp** | Ja | <*hosting-plan*> | Der Hostingplan, der für die Bereitstellung Ihrer Logik-App verwendet werden soll. <p><p>Weitere Informationen finden Sie unter [Hostingpläne und Tarife](logic-apps-pricing.md#standard-pricing). |
   | **Windows-Plan** | Ja | <*plan-name*> | Der zu verwendende Planname. Wählen Sie entweder einen vorhandenen Plannamen aus, oder geben Sie einen Namen für einen neuen Plan an. <p><p>In diesem Beispiel wird der Name `Fabrikam-Service-Plan`verwendet. |
   | **SKU und Größe** | Ja | <*pricing-tier*> | Der [Tarif](../app-service/overview-hosting-plans.md), der für Ihre Logik-App verwendet werden soll. Ihre Auswahl wirkt sich auf die Preise, den Compute, den Arbeitsspeicher und den Speicherplatz aus, die Ihre Logik-App und Ihre Workflows verwenden. <p><p>Wählen Sie **Größe ändern**, um den Standardtarif zu ändern. Sie können dann basierend auf der benötigten Workload andere Tarife auswählen. <p><p>Weitere Informationen finden Sie unter [Hostingpläne und Tarife](logic-apps-pricing.md#standard-pricing). |
   |||||

1. Wenn die Erstellungs- und Bereitstellungseinstellungen Ihrer Logik-App die Verwendung von [Application Insights](../azure-monitor/app/app-insights-overview.md) unterstützen, können Sie nun optional die Diagnoseprotokollierung und Ablaufverfolgung für Ihre Logik-App aktivieren.

   1. Legen Sie auf der Registerkarte **Überwachung** unter **Application Insights** für **Application Insights aktivieren** **Ja** fest, wenn diese Option nicht bereits ausgewählt ist.

   1. Wählen Sie für die Einstellung **Application Insights** entweder eine vorhandene Application Insights-Instanz aus, oder **Neue erstellen**, wenn Sie eine neue Instanz erstellen möchten, und geben Sie den gewünschten Namen an.

1. Nachdem Azure die Einstellungen Ihrer Logik-App überprüft hat, wählen Sie auf der Registerkarte **Überprüfen + Erstellen** die Option **Erstellen** aus. Beispiel:

   ![Screenshot, der das Azure-Portal und die Einstellungen der neuen Logik-App-Ressource zeigt.](./media/create-single-tenant-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Wenn nach diesem Schritt ein Validierungsfehler auftritt, öffnen und überprüfen Sie die Fehlerdetails. Wenn für Ihre ausgewählte Region beispielsweise ein Kontingent für Ressourcen erreicht wird, die Sie erstellen möchten, müssen Sie möglicherweise eine andere Region verwenden.

   Nachdem Azure die Bereitstellung abgeschlossen hat, ist Ihre Logik-App automatisch live und wird ausgeführt, erledigt aber noch keine Aufgaben, da die Ressource leer ist und Sie noch keine Workflows hinzugefügt haben.

1. Wählen Sie auf der Seite für den Abschluss der Bereitstellung die Option **Zu Ressource wechseln** aus, damit Sie einen leeren Workflow hinzufügen können.

   ![Screenshot, der das Azure-Portal und die fertige Bereitstellung zeigt.](./media/create-single-tenant-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Hinzufügen eines leeren Workflows

Nachdem Sie Ihre leere Logik-App-Ressource erstellt haben, müssen Sie Ihren ersten Workflow hinzufügen.

1. Nachdem Azure die Ressource geöffnet hat, wählen Sie im Menü Ihrer Logik-App **Workflows** aus. Wählen Sie auf der Symbolleiste **Workflows** die Option **Hinzufügen** aus.

   ![Screenshot, der das Menü der Logik-App-Ressource mit aktivierter Option „Workflows“ und Auswahl „Hinzufügen“ auf der Symbolleiste zeigt.](./media/create-single-tenant-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Geben Sie nach dem Öffnen des Bereichs **Neuer Workflow** einen Namen für den Workflow an, und wählen Sie den Zustandstyp aus, entweder [**Zustandsbehaftet** oder **Zustandslos**](single-tenant-overview-compare.md#stateful-stateless). Wählen Sie **Erstellen**, wenn Sie fertig sind.

   In diesem Beispiel wird ein leerer zustandsbehafteter Workflow namens `Fabrikam-Stateful-Workflow` hinzugefügt. Standardmäßig ist der Workflow aktiviert, führt aber erst Aufgaben aus, nachdem Sie einen Trigger und Aktionen hinzugefügt haben.

   ![Screenshot, der den neu hinzugefügten, leeren zustandsbehafteten Workflow „Fabrikam-Stateful-Workflow“ zeigt.](./media/create-single-tenant-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Öffnen Sie als Nächstes den leeren Workflow im Designer, damit Sie einen Trigger und eine Aktion hinzufügen können.

   1. Wählen Sie in der Workflowliste den leeren Workflow aus.

   1. Wählen Sie im Workflowmenü unter **Entwickler** die Option **Designer** aus.

      Auf der Designeroberfläche wird bereits die Eingabeaufforderung **Vorgang auswählen** angezeigt und ist standardmäßig aktiviert, sodass der Bereich **Trigger hinzufügen** ebenfalls geöffnet angezeigt wird.

      ![Screenshot mit dem geöffneten Workflow-Designer, wobei auf der Designeroberfläche „Vorgang auswählen“ ausgewählt ist.](./media/create-single-tenant-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Hinzufügen eines Triggers und einer Aktion

In diesem Beispiel wird ein Workflow mit den folgenden Schritten erstellt:

* Integrierter [Anforderungstrigger](../connectors/connectors-native-reqres.md), beim **Empfang einer HTTP-Anforderung**, der eingehende Aufrufe oder Anforderungen empfängt und einen Endpunkt erstellt, der von anderen Diensten oder Logik-Apps aufgerufen werden kann.

* Die [Office 365 Outlook-Aktion](../connectors/connectors-create-api-office365-outlook.md): **E-Mail senden**.

### <a name="add-the-request-trigger"></a>Hinzufügen des Anforderungstriggers

Bevor Sie einem leeren Workflow einen Trigger hinzufügen können, stellen Sie sicher, dass der Workflow-Designer geöffnet und die Eingabeaufforderung **Vorgang auswählen** auf der Designeroberfläche ausgewählt ist.

1. Überprüfen Sie, ob neben der Designeroberfläche im Bereich **Trigger hinzufügen** unter dem Suchfeld **Vorgang auswählen** die Registerkarte **Integriert** ausgewählt ist. Auf dieser Registerkarte werden Trigger angezeigt, die nativ in Azure Logic Apps ausgeführt werden.

1. Geben Sie in das Suchfeld **Vorgang auswählen** die Zeichenfolge `when a http request` ein, und wählen Sie den integrierten Anforderungstrigger namens **Empfang einer HTTP-Anforderung** aus.

   ![Der Screenshot zeigt den Bereich Designer und **Trigger hinzufügen** mit der Auswahl des Triggers "Empfang einer HTTP-Anforderung".](./media/create-single-tenant-workflows-azure-portal/find-request-trigger.png)

   Wenn der Trigger im Designer angezeigt wird, wird der Detailbereich des Triggers geöffnet, um die Eigenschaften, Einstellungen und anderen Aktionen des Triggers anzuzeigen.

   ![Screenshot, der den Designer mit dem ausgewählten Trigger „Beim Empfang einer HTTP-Anforderung“ und geöffnetem Triggerdetailbereich zeigt.](./media/create-single-tenant-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Wenn der Detailbereich nicht angezeigt wird, stellen Sie sicher, dass der Trigger im Designer ausgewählt ist.

1. Wenn Sie ein Element aus dem Designer löschen müssen, [führen Sie diese Schritte aus](#delete-from-designer).

1. Um Ihre Arbeit zu speichern, wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

   Wenn Sie einen Workflow zum ersten Mal speichern und dieser Workflow mit einem Anforderungstrigger ausgelöst wird, generiert der Logic Apps-Dienst automatisch eine URL für einen Endpunkt, der durch den Anforderungstrigger erstellt wird. Wenn Sie den Workflow später testen, senden Sie eine Anforderung an diese URL, die den Trigger auslöst und die Workflowausführung startet.

### <a name="add-the-office-365-outlook-action"></a>Hinzufügen der Office 365 Outlook-Aktion

1. Wählen Sie im Designer unter dem hinzugefügten Trigger das Pluszeichen ( **+** ) > **Aktion hinzufügen** aus.

   Die Eingabeaufforderung **Vorgang auswählen** wird im Designer angezeigt, und der Bereich **Aktion hinzufügen** wird erneut geöffnet, sodass Sie die nächste Aktion auswählen können.

   > [!NOTE]
   > Wenn im Bereich **Aktion hinzufügen** die Fehlermeldung „Die Eigenschaft ‚Filter‘ kann nicht von ‚undefiniert‘ gelesen werden“ angezeigt wird, speichern Sie den Workflow, laden Sie die Seite erneut, öffnen Sie den Workflow erneut, und versuchen Sie es noch einmal.

1. Wählen Sie im Bereich **Aktion hinzufügen** unter dem Suchfeld **Vorgang auswählen** die Option **Azure** aus. Auf dieser Registerkarte werden die verwalteten Connectors angezeigt, die in Azure verfügbar sind und gehostet werden.

   > [!NOTE]
   > Wenn im Bereich **Aktion hinzufügen** die Fehlermeldung `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` angezeigt wird, speichern Sie den Workflow, laden Sie die Seite erneut, öffnen Sie den Workflow erneut, und versuchen Sie noch einmal, die Aktion hinzuzufügen.

   In diesem Beispiel wird die Office 365 Outlook-Aktion **E-Mail senden (V2)** verwendet.

   ![Screenshot, der den Designer und den Bereich **Aktion hinzufügen** mit ausgewählter Office 365 Outlook-Aktion „E-Mail senden“ zeigt.](./media/create-single-tenant-workflows-azure-portal/find-send-email-action.png)

1. Wählen Sie im Detailbereich der Aktion auf der Registerkarte **Verbindung erstellen** die Option **Anmelden** aus, damit Sie eine Verbindung mit Ihrem E-Mail-Konto herstellen können.

   ![Screenshot, der den Designer und den Detailbereich „E-Mail senden (V2)“ mit ausgewähltem „Anmelden“ zeigt.](./media/create-single-tenant-workflows-azure-portal/send-email-action-sign-in.png)

1. Wenn Sie aufgefordert werden, auf Ihr E-Mail-Konto zuzugreifen, melden Sie sich mit den Anmeldeinformationen für Ihr Konto an.

   > [!NOTE]
   > Wenn Sie die Fehlermeldung `Failed with error: 'The browser is closed.'. Please sign in again` erhalten, überprüfen Sie, ob der Browser Cookies von Drittanbietern blockiert. Wenn diese Cookies blockiert werden, versuchen Sie, `https://portal.azure.com` der Liste der Websites hinzuzufügen, die Cookies verwenden können. Wenn Sie den Inkognitomodus verwenden, stellen Sie sicher, dass Cookies von Drittanbietern nicht blockiert werden, wenn Sie in diesem Modus arbeiten.
   > 
   > Laden Sie ggf. die Seite neu, öffnen Sie den Workflow, fügen Sie die E-Mail-Aktion erneut hinzu, und versuchen Sie, die Verbindung herzustellen.

   Nachdem Azure die Verbindung erstellt hat, wird die Aktion **E-Mail senden** auf der Designeroberfläche angezeigt und ist standardmäßig ausgewählt. Wenn die Aktion nicht ausgewählt ist, wählen Sie die Aktion aus, sodass der Detailbereich ebenfalls geöffnet ist.

1. Geben Sie im Detailbereich der Aktion auf der Registerkarte **Parameter** die erforderlichen Informationen für die Aktion an, z. B.:

   ![Screenshot, der den Designer und den Detailbereich „E-Mail senden“ mit ausgewählter Registerkarte „Parameter“ zeigt.](./media/create-single-tenant-workflows-azure-portal/send-email-action-details.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **An** | Ja | <*Ihre E-Mail-Adresse*> | Der E-Mail-Empfänger, bei dem es sich um Ihre E-Mail-Adresse zu Testzwecken handeln kann. In diesem Beispiel wird die fiktive E-Mail-Adresse `sophiaowen@fabrikam.com` verwendet. |
   | **Subject** | Ja | `An email from your example workflow` | der E-Mail-Betreff |
   | **Text** | Ja | `Hello from your example workflow!` | Der Textinhalt der E-Mail |
   ||||

   > [!NOTE]
   > Wenn Sie Änderungen im Detailbereich auf den Registerkarten **Einstellungen**, **Statisches Ergebnis** oder **Ausführen nach** vornehmen, stellen Sie sicher, dass Sie **Fertig** ausgewählt haben, um die Änderungen zu übernehmen, bevor Sie zwischen Registerkarten wechseln oder den Fokus innerhalb des Designers verschieben. Andernfalls behält der Designer die Änderungen nicht bei.

1. Speichern Sie Ihre Arbeit. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Wenn in Ihrer Umgebung strenge Netzwerkanforderungen gelten oder Firewalls vorhanden sind, die den Datenverkehr einschränken, müssen Sie die Berechtigungen für alle in Ihrem Workflow vorhandenen Trigger- oder Aktionsverbindungen einrichten. Informationen zum Suchen der vollqualifizierten Domänennamen finden Sie unter [Suchen von Domänennamen für den Firewallzugriff](#firewall-setup).

   Andernfalls, um Ihren Workflow zu testen, [lösen Sie eine Ausführung manuell aus](#trigger-workflow).

<a name="firewall-setup"></a>

## <a name="find-domain-names-for-firewall-access"></a>Suchen von Domänennamen für den Firewallzugriff

Bevor Sie Ihre Logik-App bereitstellen und Ihren Workflow im Azure-Portal ausführen, müssen Sie, wenn in Ihrer Umgebung strenge Netzwerkanforderungen gelten oder Firewalls vorhanden sind, die den Datenverkehr einschränken, Netzwerk- oder Firewallberechtigungen für alle Trigger- oder Aktionsverbindungen in den Workflows einrichten, die in Ihrer Logik-App vorhanden sind.

Führen Sie die folgenden Schritte aus, um die ein- und ausgehenden IP-Adressen zu ermitteln, die von Ihrer Logik-App und Ihren Workflows verwendet werden:

1. Wählen Sie in Ihrem Logik-App-Menü unter **Einstellungen** die Option **Netzwerk (Vorschau)** aus.

1. Suchen und überprüfen Sie auf der Netzwerkseite die Abschnitte **Eingehender Datenverkehr** und **Ausgehender Datenverkehr**.

Führen Sie die folgenden Schritte aus, um die vollqualifizierten Domänennamen (FQDNs) für Verbindungen zu ermitteln:

1. Wählen Sie im Menü Ihrer Logik-App unter **Workflows** die Option **Verbindungen** aus. Wählen Sie auf der Registerkarte **API-Verbindungen** den Ressourcennamen der Verbindung aus, z. B.:

   ![Screenshot, der das Azure-Portal und das Logik-App-Menü zeigt, in dem „Verbindungen“ und der Verbindungsressourcennamen „office365“ ausgewählt sind.](./media/create-single-tenant-workflows-azure-portal/logic-app-connections.png)

1. Erweitern Sie Ihren Browser so weit, dass in der oberen rechten Ecke des Browsers **JSON-Ansicht** angezeigt wird, und wählen Sie dann **JSON-Ansicht** aus.

   ![Screenshot, der das Azure-Portal und den Bereich „API-Verbindung“ mit ausgewähltem „JSON-Ansicht“ anzeigt.](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-view-json.png)

1. Kopieren Sie den `connectionRuntimeUrl`-Eigenschaftswert, und speichern Sie ihn an einem sicheren Ort, damit Sie Ihre Firewall mit diesen Informationen einrichten können.

   ![Screenshot, der den ausgewählten „connectionRuntimeUrl“-Eigenschaftswert zeigt.](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. Wiederholen Sie für jede Verbindung die relevanten Schritte.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>Auslösen des Workflows

In diesem Beispiel wird der Workflow ausgeführt, wenn der Anforderungstrigger eine eingehende Anforderung empfängt, die an die URL für den Endpunkt gesendet wird, der vom Trigger erstellt wird. Als Sie den Workflow zum ersten Mal gespeichert haben, hat der Logic Apps-Dienst diese URL automatisch generiert. Bevor Sie diese Anforderung zum Auslösen des Workflows senden können, müssen Sie diese URL suchen.

1. Wählen Sie im Workflow-Designer den Anforderungstrigger **Empfang einer HTTP-Anforderung** aus.

1. Nach Öffnen des Detailbereichs suchen Sie auf der Registerkarte **Parameter** die **HTTP-POST-URL**-Eigenschaft. Um die generierte URL zu kopieren, wählen Sie **URL kopieren** aus („Datei kopieren“-Symbol), und speichern Sie die URL vorerst an einem anderen Ort. Die URL weist das folgende Format auf:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Screenshot, der den Designer mit Anforderungstrigger und Endpunkt-URL in der Eigenschaft „HTTP-POST-URL“ zeigt.](./media/create-single-tenant-workflows-azure-portal/find-request-trigger-url.png)

   Für dieses Beispiel sieht die URL wie folgt aus:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > Sie finden die Endpunkt-URL auch im Bereich **Übersicht** Ihrer Logik-App in der **Workflow-URL**-Eigenschaft.
   >
   > 1. Wählen Sie im Ressourcenmenü **Übersicht** aus.
   > 1. Suchen Sie im Bereich **Übersicht** nach der Eigenschaft **Workflow-URL**.
   > 1. Um die Endpunkt-URL zu kopieren, bewegen Sie den Mauszeiger über das Ende des Endpunkt-URL-Texts, und wählen Sie **In Zwischenablage kopieren** aus („Datei kopieren“-Symbol).

1. Um die URL durch Senden einer Anforderung zu testen, öffnen Sie [Postman](https://www.postman.com/downloads/) oder Ihr bevorzugtes Tool zum Erstellen und Senden von Anforderungen.

   Dieses Beispiel wird mit Postman fortgesetzt. Weitere Informationen finden Sie unter [Erste Schritte mit Postman](https://learning.postman.com/docs/getting-started/introduction/).

   1. Wählen Sie auf der Postman-Symbolleiste **Neu** aus.

      ![Screenshot, der Postman mit ausgewählter Schaltfläche „Neu“ zeigt.](./media/create-single-tenant-workflows-azure-portal/postman-create-request.png)

   1. Wählen Sie im Bereich **Neu erstellen** unter **Bausteine** die Option **Anforderung** aus.

   1. Geben Sie im Fenster **Anforderung speichern** unter **Anforderungsname** einen Namen für die Anforderung an, z. B. `Test workflow trigger`.

   1. Wählen Sie unter **Sammlung oder Ordner als Speicherziel auswählen** den Eintrag **Sammlung erstellen** aus.

   1. Geben Sie unter **Alle Sammlungen** einen Namen für die zu erstellende Sammlung an, um Ihre Anforderungen zu organisieren, und wählen Sie **Speichern unter <*Sammlungs-Name*>** aus. In diesem Beispiel wird `Logic Apps requests` als Name der Sammlung verwendet.

      In der Postman-App wird der Anforderungsbereich geöffnet, sodass Sie eine Anforderung an die Endpunkt-URL für den Anforderungstrigger senden können.

      ![Screenshot, der Postman mit geöffnetem Anforderungsbereich zeigt.](./media/create-single-tenant-workflows-azure-portal/postman-request-pane.png)

   1. Fügen Sie im Anforderungsbereich in das Adressfeld neben der Liste der Methoden, wo derzeit **GET** als Standardanforderungsmethode angezeigt wird, die zuvor kopierte URL ein, und wählen Sie **Senden** aus.

      ![Screenshot, der Postman und die Endpunkt-URL im Adressfeld mit ausgewählter Schaltfläche „Senden“ zeigt.](./media/create-single-tenant-workflows-azure-portal/postman-test-endpoint-url.png)

      Wenn der Trigger ausgelöst wird, wird der Beispielworkflow ausgeführt und sendet eine E-Mail, die in etwa dem folgenden Beispiel entspricht:

      ![Screenshot, der die wie im Beispiel beschriebene Outlook-E-Mail zeigt.](./media/create-single-tenant-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Überprüfen des Ausführungsverlaufs

Bei einem zustandsbehafteten Workflow können Sie nach jeder Workflowausführung den Ausführungsverlauf anzeigen, einschließlich des Status für die gesamte Ausführung, den Trigger und jede Aktion sowie die zugehörigen Ein- und Ausgaben. Im Azure-Portal werden der Ausführungsverlauf und Triggerverlauf auf Workflowebene angezeigt, nicht auf Ebene der Logik-App. Wenn Sie den Triggerverlauf außerhalb des Kontexts des Ausführungsverlaufs überprüfen möchten, finden Sie unter [Überprüfen des Triggerverlaufs](#view-trigger-histories) weitere Informationen.

1. Wählen Sie im Azure-Portal im Menü „Workflow“ die Option **Übersicht** aus.

1. Wählen Sie im Bereich **Übersicht** die Option **Ausführungsverlauf** aus, um den Ausführungsverlauf für diesen Workflow anzuzeigen.

   ![Screenshot: Bereich „Übersicht“ des Workflows mit ausgewählter Option „Ausführungsverlauf“](./media/create-single-tenant-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Wenn der letzte Ausführungsstatus nicht angezeigt wird, wählen Sie auf der Bereichssymbolleiste **Übersicht** die Option **Aktualisieren** aus. Für Trigger, die aufgrund von nicht erfüllten Kriterien oder nicht gefundenen Daten ausgelassen werden, erfolgt keine Ausführung.

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

1. Um den Status der einzelnen Schritte in einer Ausführung zu überprüfen, wählen Sie die Ausführung aus, die Sie überprüfen möchten.

   Die Ausführungsdetailsansicht wird geöffnet und zeigt den Status der einzelnen Schritte in der Ausführung an.

   ![Screenshot, der die Ausführungsdetailsansicht mit dem Status für jeden Schritt im Workflow zeigt.](./media/create-single-tenant-workflows-azure-portal/review-run-details.png)

   In der folgenden Tabelle sind die möglichen Statuswerte aufgeführt, die in den einzelnen Workflowschritte aufweisen können:

   | Status einer Aktion | Beschreibung |
   |---------------|-------------|
   | **Aborted** | Die Aktion wurde aufgrund externer Probleme beendet oder nicht fertig gestellt, z. B. wegen eines Systemausfalls oder abgelaufenen Azure-Abonnements. |
   | **Abgebrochen** | Die Aktion wurde ausgeführt, hat aber eine Abbruchanforderung erhalten. |
   | **Fehlgeschlagen** | Die Aktion ist fehlgeschlagen. |
   | **Wird ausgeführt** | Die Aktion wird zurzeit ausgeführt. |
   | **Übersprungen** | Die Aktion wurde übersprungen, weil ihre `runAfter` Bedingungen nicht erfüllt wurden, z. B. weil vorherige Aktion fehlgeschlagen ist. Jede Aktion verfügt über ein `runAfter`-Objekt, in dem Sie Bedingungen einrichten können, die erfüllt sein müssen, bevor die aktuelle Aktion ausgeführt werden kann. |
   | **Erfolgreich** | Die Aktion war erfolgreich. |
   | **Erfolgreich mit Wiederholungen** | Die Aktion war erfolgreich, jedoch erst einem oder mehreren Wiederholungsversuchen. Wählen Sie diese Aktion zum Überprüfen des Wiederholungsverlaufs in der Detailansicht des Ausführungsverlaufs aus, damit Sie die Ein- und Ausgaben anzeigen können. |
   | **Timeout** | Die Aktion wurde aufgrund des Timeoutlimits beendet, das durch die Einstellungen dieser Aktion gesteuert wird. |
   | **Wartet** | Gilt für eine Webhookaktion, die auf eine eingehende Anforderung eines Aufrufers wartet. |
   |||

   [aborted-icon]: ./media/create-single-tenant-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-single-tenant-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-single-tenant-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-single-tenant-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-single-tenant-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-single-tenant-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-single-tenant-workflows-azure-portal/waiting.png

1. Um die Ein- und Ausgaben für einen bestimmten Schritt zu überprüfen, wählen Sie diesen Schritt aus.

   ![Screenshot, der die Ein- und Ausgaben in der ausgewählten Aktion „E-Mail senden“ zeigt.](./media/create-single-tenant-workflows-azure-portal/review-step-inputs-outputs.png)

1. Um die unformatierten Ein- und Ausgaben für diesen Schritt weiter zu überprüfen, wählen Sie **Unformatierte Eingaben anzeigen** oder **Unformatierte Ausgaben anzeigen** aus.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Überprüfen des Triggerverlaufs

Bei einem zustandsbehafteten Workflow können Sie den Triggerverlauf pro Ausführung und unabhängig vom [Ausführungsverlaufskontext](#view-run-history) überprüfen, einschließlich des Triggerstatus sowie Eingaben und Ausgaben. Im Azure-Portal werden der Triggerverlauf und der Ausführungsverlauf auf Workflowebene angezeigt, nicht auf Ebene der Logik-App. Wenn Sie sich Verlaufsdaten ansehen möchten, führen Sie die folgenden Schritte aus:

1. Wählen Sie im Azure-Portal im Menü „Workflow“ die Option **Übersicht** aus.

1. Wählen Sie auf der Seite **Übersicht** die Option **Triggerverlauf** aus.

   Im Bereich **Triggerverlauf** wird der Triggerverlauf für Ihre Workflowausführungen angezeigt.

1. Wenn Sie sich einen bestimmten Triggerverlauf ansehen möchten, wählen Sie die ID für die entsprechende Ausführung aus.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Aktivieren oder Öffnen von Application Insights nach der Bereitstellung

Während der Workflowausführung gibt Ihre Logik-App Telemetriedaten zusammen mit anderen Ereignissen aus. Sie können diese Telemetrie verwenden, um auf verschiedene Weise einen besseren Einblick in die Qualität der Workflowausführung und die Arbeitsweise der Logic Apps-Runtime zu bekommen. Sie können den Workflow überwachen, indem Sie [Application Insights](../azure-monitor/app/app-insights-overview.md) verwenden, wodurch nahezu in Echtzeit Telemetriedaten (Livemetriken) bereitgestellt werden. Mithilfe dieser Funktion können Sie Fehler und Leistungsprobleme leichter untersuchen, wenn Sie diese Daten zum Diagnostizieren von Problemen, Einrichten von Warnungen und Erstellen von Diagrammen verwenden.

Wenn die Erstellungs- und Bereitstellungseinstellungen Ihrer Logik-App die Verwendung von [Application Insights](../azure-monitor/app/app-insights-overview.md) unterstützen, können Sie optional die Diagnoseprotokollierung und Ablaufverfolgung für Ihre Logik-App aktivieren. Dies ist entweder beim Erstellen der Logik-App oder nach der Bereitstellung möglich. Sie benötigen eine Application Insights-Instanz, können aber diese Ressource entweder [im Voraus](../azure-monitor/app/create-workspace-resource.md), beim Erstellen Ihrer Logik-App oder nach der Bereitstellung erstellen.

Gehen Sie folgendermaßen vor, um Application Insights auf einer bereitgestellten Logik-App zu aktivieren oder das Application Insights-Dashboard zu öffnen, wenn Application Insights bereits aktiviert ist:

1. Suchen Sie im Azure-Portal nach Ihrer bereitgestellten Logik-App.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Application Insights** aus.

1. Wenn Application Insights nicht aktiviert ist, wählen Sie im Bereich **Application Insights** die Option **Application Insights aktivieren** aus. Nachdem der Bereich aktualisiert wurde, wählen Sie **Anwenden** > **Ja** aus.

   Wenn Application Insights aktiviert ist, wählen Sie im Bereich **Application Insights** die Option **Application Insights-Daten anzeigen** aus.

Nachdem Application Insights geöffnet wurde, können Sie verschiedene Metriken für Ihre Logik-App überprüfen. Weitere Informationen finden Sie in diesen Themen:

* [Azure Logic Apps ohne Grenzen ausführen: Überwachung mit Application Insights (Teil 1)](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps ohne Grenzen ausführen: Überwachung mit Application Insights (Teil 2)](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Aktivieren des Ausführungsverlaufs für zustandslose Workflows

Damit Sie einen zustandslosen Workflow debuggen können, können Sie den Ausführungsverlauf für diesen Workflow aktivieren und wieder deaktivieren, wenn Sie fertig sind. Führen Sie diese Schritte für das Azure-Portal aus. Wenn Sie in Visual Studio Code arbeiten, lesen Sie [Erstellen zustandsbehafteter und zustandsloser Workflows in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless).

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre **Logik-App (Standard)** -Ressource.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Konfiguration** aus.

1. Wählen Sie auf der Registerkarte **Anwendungseinstellungen** die Option **Neue Anwendungseinstellung** aus.

1. Geben Sie im Bereich **Anwendungseinstellung hinzufügen/bearbeiten** in das Feld **Name** den Namen dieser Vorgangsoption ein: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Geben Sie in das Feld **Wert** den folgenden Wert ein: `WithStatelessRunHistory`

   Beispiel:

   ![Screenshot, der das Azure-Portal und die Logik-App-Ressource (Standard) mit dem geöffneten Bereich „Konfiguration“ > „Neue Anwendungseinstellung“ < „Anwendungseinstellung hinzufügen/bearbeiten“ und der auf „WithStatelessRunHistory“ festgelegten Option „Workflow.{NameIhresWorkflows}.OperationOptions“ zeigt.](./media/create-single-tenant-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Wählen Sie **OK** aus, um diese Aufgabe abzuschließen. Wählen Sie auf der Symbolleiste des Bereichs **Konfiguration** die Option **Speichern** aus.

1. Um den Ausführungsverlauf zu deaktivieren, wenn Sie fertig sind, legen Sie entweder die Eigenschaft `Workflows.{yourWorkflowName}.OperationOptions` auf `None` fest oder löschen die Eigenschaft und ihren Wert.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Löschen von Elementen aus dem Designer

Führen Sie einen der folgenden Schritte aus, um ein Element in Ihrem Workflow aus dem Designer zu löschen:

* Wählen Sie das Element aus, öffnen Sie das Kontextmenü des Elements (UMSCHALT+F10), und wählen Sie **Löschen** aus. Klicken Sie auf **OK**, um dies zu bestätigen.

* Wählen Sie das Element aus, und drücken Sie dann die ENTF-Taste. Klicken Sie auf **OK**, um dies zu bestätigen.

* Wählen Sie das Element aus, sodass der Detailbereich für dieses Element geöffnet wird. Öffnen Sie in der oberen rechten Ecke des Bereichs das Menü mit den Auslassungspunkten ( **...** ), und wählen Sie **Löschen** aus. Klicken Sie auf **OK**, um dies zu bestätigen.

  ![Screenshot, der ein ausgewähltes Element im Designer mit geöffnetem Detailbereich sowie die ausgewählte Schaltfläche mit Auslassungspunkten und den Befehl „Löschen“ zeigt.](./media/create-single-tenant-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Wenn das Menü mit den Auslassungspunkten nicht sichtbar ist, erweitern Sie das Fenster Ihres Browsers, sodass im Detailbereich die Schaltfläche mit den Auslassungspunkten ( **...** ) in der oberen rechten Ecke angezeigt wird.

<a name="restart-stop-start"></a>

## <a name="restart-stop-or-start-logic-apps"></a>Neustarten, Beenden oder Starten von Logik-Apps

Sie können eine [einzelne Logik-App](#restart-stop-start-single-logic-app) oder [mehrere Logik-Apps gleichzeitig](#stop-start-multiple-logic-apps) stoppen oder starten. Sie können auch eine einzelne Logik-App neu starten, ohne sie zuerst zu beenden. Ihre auf nur einem Mandanten basierende Logik-App kann mehrere Workflows enthalten, sodass Sie entweder die gesamte Logik-App beenden oder [nur Workflows deaktivieren](#disable-enable-workflows) können.

> [!NOTE]
> Die Logik-App zum Beenden und Deaktivieren von Workflow-Vorgängen hat unterschiedliche Auswirkungen. Weitere Informationen hierzu sind unter [Überlegungen zum Beenden von Logik-Apps](#considerations-stop-logic-apps) und [Überlegungen zum Deaktivieren von Workflows](#disable-enable-workflows) zu lesen.

<a name="considerations-stop-logic-apps"></a>

### <a name="considerations-for-stopping-logic-apps"></a>Überlegungen zum Beenden von Logik-Apps

Das Beenden einer Logik-App wirkt sich wie folgt auf Workflow-Instanzen aus:

* Azure Logic Apps bricht alle laufenden und ausstehenden Ausführungen sofort ab.

* Azure Logic Apps erstellt keine neuen Workflowinstanzen und führt keine neuen Workflowinstanzen aus.

* Trigger werden nicht ausgelöst, wenn die definierten Bedingungen beim nächsten Mal erfüllt werden. Triggerzustände merken sich jedoch die Punkte, an denen die Logik-App beendet wurde. Wenn Sie die Logik-App erneut starten, wird der Trigger für alle nicht verarbeiteten Elemente seit der letzten Ausführung ausgelöst.

  Um zu verhindern, dass jeder Workflow seit der letzten Ausführung für nicht verarbeitete Elemente ausgelöst wird, löschen Sie den Triggerstatus, bevor Sie die Logik-App neu starten, indem Sie die folgenden Schritte ausführen:

  1. Öffnen Sie Ihre Logik-App im Azure-Portal.
  1. Wählen Sie im Menü der Logik-App unter **Workflows** die Option **Workflows** aus.
  1. Öffnen Sie einen Workflow, und bearbeiten Sie einen beliebigen Teil des Triggers dieses Workflows.
  1. Speichern Sie die Änderungen. Durch diesen Schritt wird der aktuelle Status des Triggers zurückgesetzt.
  1. Wiederholen Sie dies für jeden Workflow.
  1. Wenn Sie fertig sind, [speichern Sie Ihre Logik-App](#restart-stop-start-single-logic-app) neu.

<a name="restart-stop-start-single-logic-app"></a>

### <a name="restart-stop-or-start-a-single-logic-app"></a>Neustarten, Beenden oder Starten einer einzelnen Logik-App

1. Öffnen Sie Ihre Logik-App im Azure-Portal.

1. Wählen Sie in der Logik-App-Menü **Übersicht** aus.

   * Um eine Logik-App ohne Beenden neu zu starten, wählen Sie auf der Symbolleiste des Übersichtsbereichs **Neu starten** aus.
   * Um eine ausgeführte Logik-App zu beenden, wählen Sie auf der Symbolleiste des Übersichtsbereichs **Beenden** aus. Bestätigen Sie Ihre Auswahl.
   * Klicken Sie zum Starten einer beendeten Logik-App auf der Symbolleiste des Übersichtsbereichs **auf Starten**.

   > [!NOTE]
   > Wenn Ihre Logik-App bereits aktiviert ist, ist nur die Option **Starten** verfügbar. Wenn Ihre Logik-App bereits läuft, ist nur die Option **Stoppen** verfügbar.
   > Sie können Ihre Logik-App jederzeit neu starten.

1. Um zu überprüfen, ob Ihr Vorgang erfolgreich war oder fehlgeschlagen ist, öffnen Sie auf der Azure-Hauptsymbolleiste die Liste **Benachrichtigungen** (Glockensymbol).

<a name="stop-start-multiple-logic-apps"></a>

### <a name="stop-or-start-multiple-logic-apps"></a>Beenden oder Starten mehrerer Logik-Apps

Sie können mehrere Logik-Apps gleichzeitig beenden oder starten, aber Sie können nicht mehrere Logik-Apps neu starten, ohne diese zuerst zu beenden.

1. Geben Sie `logic apps` in das Suchfeld des Azure-Portals ein, und klicken Sie auf **Logic Apps**.

1. Überprüfen Sie auf der Seite **Logic Apps** die Spalte **Status** der Logik-Apps.

1. Wählen Sie in der Spalte mit den Kontrollkästchen die Logik-Apps aus, die Sie starten oder stoppen möchten.

   * Um die ausgewählten ausgeführten Logik-Apps zu beenden, wählen Sie auf der Symbolleiste des Übersichtsbereichs **Deaktivieren/Beenden** aus. Bestätigen Sie Ihre Auswahl.
   * Um die ausgewählten beendeten Logik-Apps zu starten, wählen Sie auf der Symbolleiste des Übersichtsbereichs die Option **Aktivieren/Starten** aus.

1. Um zu überprüfen, ob Ihr Vorgang erfolgreich war oder fehlgeschlagen ist, öffnen Sie auf der Azure-Hauptsymbolleiste die Liste **Benachrichtigungen** (Glockensymbol).

<a name="disable-enable-workflows"></a>

## <a name="disable-or-enable-workflows"></a>Deaktivieren oder Aktivieren von Workflows

Um zu verhindern, dass der Trigger das nächste Mal ausgelöst wird, wenn die Triggerbedingung erfüllt ist, deaktivieren Sie Ihren Workflow. Sie können einen einzelnen Workflow deaktivieren oder aktivieren, aber Sie können nicht mehrere Workflows gleichzeitig deaktivieren oder aktivieren. Das Deaktivieren von Workflow-Instanzen wirkt sich wie folgt auf Workflow-Instanzen aus:

* Azure Logic Apps setzt alle ausgeführten und ausstehenden Ausführungen fort, bis sie abgeschlossen sind. Basierend auf dem Volume oder Backlog kann es einige Zeit dauern, bis dieser Prozess abgeschlossen ist.

* Azure Logic Apps erstellt keine neuen Workflowinstanzen und führt keine neuen Workflowinstanzen aus.

* Der Trigger wird nicht ausgelöst, wenn die definierten Bedingungen beim nächsten Mal erfüllt werden. Der Triggerzustand speichert jedoch den Punkt, an dem der Workflow deaktiviert wurde. Wenn Sie den Workflow erneut aktivieren, wird der Trigger für alle nicht verarbeiteten Elemente seit der letzten Ausführung ausgelöst.

  Um das Auslösen eines Triggers für nicht verarbeitete Elemente seit der letzten Ausführung zu verhindern, löschen Sie den Workflow aktivieren:

  1. Bearbeiten Sie im Workflow einen beliebigen Teil des Triggers des Workflows.
  1. Speichern Sie die Änderungen. Durch diesen Schritt wird der aktuelle Status Ihres Triggers zurückgesetzt.
  1. [Reaktivieren Sie Ihren Workflow](#disable-enable-workflows).

> [!NOTE]
> Das Deaktvieren von Workflow-Vorgängen und das Beenden von Logik-App-Vorgängen hat unterschiedliche Auswirkungen. Weitere Informationen hierzu finden Sie unter [Überlegungen zum Beenden von Logik-Apps](#considerations-stop-logic-apps).

<a name="disable-workflow"></a>

### <a name="disable-workflow"></a>Workflow deaktivieren

1. Wählen Sie im Menü der Logik-App unter **Workflows** die Option **Workflows** aus. Wählen Sie in der Spalte mit den Kontrollkästchen den Workflow aus, den Sie deaktivieren möchten.

1. Wählen Sie auf der Symbolleiste des Bereichs **Workflows** die Option **Deaktivieren** aus.

1. Um zu überprüfen, ob Ihr Vorgang erfolgreich war oder fehlgeschlagen ist, öffnen Sie auf der Azure-Hauptsymbolleiste die Liste **Benachrichtigungen** (Glockensymbol).

<a name="enable-workflow"></a>

### <a name="enable-workflow"></a>Workflow aktivieren

1. Wählen Sie im Menü der Logik-App unter **Workflows** die Option **Workflows** aus. Wählen Sie in der Spalte mit den Kontrollkästchen den Workflow zum Aktivieren aus.

1. Wählen Sie auf der Symbolleiste des Bereichs **Workflows** die Option **Aktivieren** aus.

1. Um zu überprüfen, ob Ihr Vorgang erfolgreich war oder fehlgeschlagen ist, öffnen Sie auf der Azure-Hauptsymbolleiste die Liste **Benachrichtigungen** (Glockensymbol).

<a name="delete"></a>

## <a name="delete-logic-apps-or-workflows"></a>Löschen von Logik-Apps oder Workflows

Sie können [eine einzelne oder mehrere Logik-Apps gleichzeitig löschen](#delete-logic-apps). Ihre auf einem einzelnen Mandanten basierende Logik-App kann mehrere Workflows enthalten, sodass Sie entweder die gesamte Logik-App löschen oder [nur Workflows löschen](#delete-workflows) können.

<a name="delete-logic-apps"></a>

### <a name="delete-logic-apps"></a>Löschen von Logik-Apps

Durch das Löschen einer Logik-App werden in Bearbeitung und ausstehende Ausführungen sofort abgebrochen, es werden jedoch keine Bereinigungsaufgaben für den von der App verwendeten Speicher ausgeführt.

1. Geben Sie `logic apps` in das Suchfeld des Azure-Portals ein, und klicken Sie auf **Logic Apps**.

1. Wählen Sie in der Liste **Logik-Apps** in der Kontrollkästchenspalte eine einzelne oder mehrere zu löschende Logik-Apps aus. Wählen Sie in der Symbolleiste die Option **Löschen** aus.

1. Wenn das Bestätigungsfeld angezeigt wird, geben Sie `yes` ein, und wählen Sie **Löschen** aus.

1. Um zu überprüfen, ob Ihr Vorgang erfolgreich war oder fehlgeschlagen ist, öffnen Sie auf der Azure-Hauptsymbolleiste die Liste **Benachrichtigungen** (Glockensymbol).

<a name="delete-workflows"></a>

### <a name="delete-workflows"></a>Workflows löschen

Das Löschen von Workflow-Instanzen wirkt sich wie folgt auf Workflow-Instanzen aus:

* Azure Logic Apps bricht aktive und ausstehende Ausführungen sofort ab, führt jedoch Bereinigungsaufgaben für den vom Workflow verwendeten Speicher aus.

* Azure Logic Apps erstellt keine neuen Workflowinstanzen und führt keine neuen Workflowinstanzen aus.

* Wenn Sie einen Workflow löschen und dann denselben Workflow neu erstellen, hat der neu erstellte Workflow nicht die gleichen Metadaten wie der gelöschte Workflow. Zum Aktualisieren der Metadaten müssen Sie jeden Workflow, der den gelöschten Workflow aufgerufen hat, neu speichern. Auf diese Weise ruft der Aufrufer die richtigen Informationen für den neu erstellten Workflow ab. Andernfalls schlagen Aufrufe des neu erstellten Workflows mit einem `Unauthorized`-Fehler fehl. Dieses Verhalten gilt auch für Workflows, die Artefakte in Integrationskonten und Workflows verwenden, welche Azure-Funktionen aufrufen.

1. Öffnen Sie Ihre Logik-App im Azure-Portal.

1. Wählen Sie im Menü der Logik-App unter **Workflows** die Option **Workflows** aus. Wählen Sie in der Spalte mit den Kontrollkästchen einen einzelnen oder mehrere zu löschende Workflows aus.

1. Wählen Sie in der Symbolleiste die Option **Löschen** aus.

1. Um zu überprüfen, ob Ihr Vorgang erfolgreich war oder fehlgeschlagen ist, öffnen Sie auf der Azure-Hauptsymbolleiste die Liste **Benachrichtigungen** (Glockensymbol).

<a name="recover-deleted"></a>

## <a name="recover-deleted-logic-apps"></a>Wiederherstellen gelöschter Logik-Apps

Wenn Sie die Quellcodeverwaltung verwenden, können Sie eine gelöschte **Logik-App-Ressource (Standard)** nahtlos erneut für Azure Logic Apps mit einzelnem Mandanten bereitstellen. Wenn Sie jedoch keine Quellcodeverwaltung verwenden, führen Sie die folgenden Schritte aus, um Ihre gelöschte Logik-App wiederherzustellen.

> [!NOTE]
> Bevor Sie versuchen, Ihre gelöschte Logik-App wiederherzustellen, betrachten Sie die folgenden Überlegungen:
>
> * Sie können nur gelöschte **Logik-App-Ressourcen (Standard)** wiederherstellen, die den Hostingplan **Workflow-Standard** verwenden. 
> Gelöschte Ressourcen vom Typ **Logik-App (Verbrauch)** können nicht wiederhergestellt werden.
>
> * Wenn Ihr Workflow mit dem Anforderungstrigger beginnt, unterscheidet sich die Rückruf-URL für die wiederhergestellte Logik-App von der URL für die gelöschte Logik-App.
>
> * Der Ausführungsverlauf der gelöschten Logik-App ist in der wiederhergestellten Logik-App nicht verfügbar.

1. Vergewissern Sie sich, dass das Speicherkonto Ihrer Logik-App noch vorhanden ist. Wenn das Speicherkonto gelöscht wurde, müssen Sie [zuerst das gelöschte Speicherkonto wiederherstellen](../storage/common/storage-account-recover.md).

1. Wählen Sie im Speicherkontomenü unter **Sicherheit + Netzwerkbetrieb** die Option **Zugriffsschlüssel** aus.

1. Kopieren Sie auf der Seite **Zugriffsschlüssel** die primäre Verbindungszeichenfolge des Kontos, und speichern Sie sie zur späteren Verwendung. Beispiel:

   `DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accesskey>;EndpointSuffix=core.windows.net`

1. Wählen Sie im Speicherkontomenü unter **Datenspeicher** die Option **Dateifreigaben** aus, kopieren Sie den Namen für die Dateifreigabe, die Ihrer Logik-App zugeordnet ist, und speichern Sie ihn zur späteren Verwendung.

1. Erstellen Sie eine neue Ressource vom Typ **Logik-App (Standard)** mit demselben Hostingplan und Tarif. Sie können entweder einen neuen Namen verwenden oder den Namen der gelöschten Logik-App wiederverwenden.

1. Bevor Sie fortfahren, beenden Sie die Logik-App. Wählen Sie im Logik-App-Menü die Option **Übersicht** aus. Wählen Sie in der Symbolleiste des Bereichs **Übersicht** die Option **Beenden** aus.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Konfiguration** aus.

1. Aktualisieren Sie auf der Seite **Konfiguration** die folgenden Werte für die Anwendungseinstellung, und speichern Sie ihre Änderungen, wenn Sie fertig sind.

   | App-Einstellung | Replacement value |
   |-------------|-------------------|
   | `AzureWebJobsStorage` | Ersetzen Sie den vorhandenen Wert durch die zuvor kopierte Verbindungszeichenfolge aus Ihrem Speicherkonto. |
   | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | Ersetzen Sie den vorhandenen Wert durch die zuvor kopierte Zeichenfolge aus Ihrem Speicherkonto. |
   | `WEBSITE_CONTENTSHARE` | Ersetzen Sie den vorhandenen Wert durch den zuvor kopierten Dateifreigabenamen. |
   |||

1. Wählen Sie im Menü Ihrer Logik-App unter **Workflows** die Option **Verbindungen** aus.

1. Öffnen Sie jede Verbindung, und wählen Sie unter **Einstellungen** die Option **Zugriffsrichtlinien** aus.

1. Löschen Sie die Zugriffsrichtlinie für die gelöschte Logik-App, und fügen Sie dann eine neue Zugriffsrichtlinie für die Ersatz-Logik-App hinzu.

1. Kehren Sie zur Seite **Konfiguration** der Logik-App zurück, und fügen Sie alle benutzerdefinierten Einstellungen hinzu, die in der gelöschten Logik-App vorhanden waren.

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App neu.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Beheben von Problemen und Fehlern

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>In der Designerauswahl für zuvor erstellte Workflows fehlen neue Trigger und Aktionen

Azure Logic Apps mit einzelnem Mandanten unterstützt integrierte Aktionen für Azure-Funktionsvorgänge, Liquid-Vorgänge und XML-Vorgänge wie **XML-Validierung** und **Transformieren von XML**. Allerdings werden diese Aktionen für zuvor erstellte Logik-Apps möglicherweise nicht für Sie zur Auswahl im Designer angezeigt, wenn Ihre Logik-App eine veraltete Version des Erweiterungspakets `Microsoft.Azure.Functions.ExtensionBundle.Workflows` verwendet.

Um dieses Problem zu beheben, führen Sie die folgenden Schritte aus, um die veraltete Version zu löschen, damit das Erweiterungspaket automatisch auf die neueste Version aktualisiert werden kann.

> [!NOTE]
> Diese spezifische Lösung gilt nur für **Logik-App (Standard)** -Ressourcen, die Sie im Azure-Portal erstellen, nicht die Logik-Apps, die Sie mit Visual Studio Code und der Erweiterung „Azure Logic Apps (Standard)“ erstellen und bereitstellen. Siehe [Neue Trigger und Aktionen fehlen in der Designerauswahl für früher erstellte Workflows](create-single-tenant-workflows-visual-studio-code.md#missing-triggers-actions).

1. Beenden Sie im Azure-Portal Ihre Logik-App.

   1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.

   1. Wählen Sie in der Symbolleiste des Bereichs **Übersicht** die Option **Beenden** aus.

1. Wählen Sie im Menü Ihrer Logik-App unter **Entwicklungstools** die Option **Erweiterte Tools** aus.

1. Wählen Sie im Bereich **Erweiterte Tools** die Option **Los** aus, wodurch die Kudu-Umgebung für Ihre Logik-App geöffnet wird.

1. Öffnen Sie auf der Kudu-Symbolleiste das Menü **Debugkonsole**, und wählen Sie **CMD** aus.

   Ein Konsolenfenster wird geöffnet, in dem Sie mithilfe der Eingabeaufforderung zum Paketordner navigieren können. Alternativ können Sie die Verzeichnisstruktur durchsuchen, die oben im Konsolenfenster angezeigt wird.

1. Navigieren Sie zum folgenden Ordner, der Ordner mit Versionsangaben für das vorhandene Paket enthält:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Löschen Sie den Versionsordner für das vorhandene Paket. Im Konsolenfenster können Sie diesen Befehl ausführen, wo Sie `{bundle-version}` durch die vorhandene Version ersetzen:

   `rm -rf {bundle-version}`

   Beispiel: `rm -rf 1.1.3`

   > [!TIP]
   > Wenn Sie eine Fehlermeldung wie „Berechtigung verweigert“ oder „Datei wird verwendet“ erhalten, aktualisieren Sie die Seite in Ihrem Browser, und wiederholen Sie die vorherigen Schritte, bis der Ordner gelöscht wird.

1. Kehren Sie im Azure-Portal zur Seite **Übersicht** der Logik-App zurück, und wählen Sie **Neu starten** aus.

   Im Portal wird automatisch das neueste Paket abgerufen und verwendet.

## <a name="next-steps"></a>Nächste Schritte

Bitte teilen Sie uns Ihre Erfahrungen mit diesem Szenario mit!

* Bei Fehlern oder Problemen [erstellen Sie Ihre Probleme in GitHub](https://github.com/Azure/logicapps/issues).
* Bei Fragen, Anforderungen, Kommentaren und anderem Feedback [verwenden Sie dieses Feedbackformular](https://aka.ms/lafeedback).
