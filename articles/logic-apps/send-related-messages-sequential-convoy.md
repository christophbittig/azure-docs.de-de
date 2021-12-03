---
title: Senden von korrelierten Nachrichten in der richtigen Reihenfolge mithilfe eines sequenziellen Konvois
description: Verwenden Sie das Muster für einen sequenziellen Konvoi in Azure Logic Apps mit Azure Service Bus, um verwandte Nachrichten in der richtigen Reihenfolge zu senden
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: e0df7b263ef5d62b064a5afe537cffc926230b0a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238676"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Verwenden Sie einen sequenziellen Konvoi in Azure Logic Apps mit Azure Service Bus, um verwandte Nachrichten in der richtigen Reihenfolge zu senden

Wenn Sie korrelierte Nachrichten in einer bestimmten Reihenfolge senden müssen, können Sie bei Verwendung von [Azure Logic Apps](../logic-apps/logic-apps-overview.md) mit dem [Azure Service Bus-Connector](../connectors/connectors-create-api-servicebus.md) das [Muster für einen *sequenziellen Konvoi*](/azure/architecture/patterns/sequential-convoy) nutzen. Korrelierte Nachrichten verfügen über eine Eigenschaft zum Definieren der Beziehung zwischen diesen Nachrichten, z. B. die ID für die [Sitzung](../service-bus-messaging/message-sessions.md) in Service Bus.

Beispiel: Sie verfügen über zehn Nachrichten für die Sitzung „Sitzung 1“ und fünf Nachrichten für die Sitzung „Sitzung 2“, die alle an dieselbe [Service Bus-Warteschlange](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) gesendet werden. Sie können eine Logik-App zur Verarbeitung von Nachrichten aus der Warteschlange erstellen, bei der alle Nachrichten aus „Sitzung 1“ von einer einzigen Triggerausführung behandelt und alle Nachrichten von „Sitzung 2“ von der nächsten Triggerausführung behandelt werden.

![Allgemeines Muster für einen sequenziellen Konvoi](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

In diesem Artikel erfahren Sie, wie Sie eine Logik-App erstellen, die dieses Muster unter Verwendung der Vorlage **Zustellung korrelierter Nachrichten in der richtigen Reihenfolge mithilfe von Service Bus-Sitzungen** implementiert. Mit dieser Vorlage wird ein Logik-App-Workflow definiert, der mit dem Trigger **Wenn eine Nachricht in einer Warteschlange empfangen wird (Peek-Lock)** des Service Bus-Connectors startet, der Nachrichten aus einer [Service Bus-Warteschlange](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) empfängt. In der folgenden Übersicht sind die wichtigsten Schritte aufgeführt, die diese Logik-App ausführt:

* Initialisieren einer Sitzung basierend auf einer Nachricht, die der Trigger aus der Service Bus-Warteschlange liest.

* Lesen und Verarbeiten aller Nachrichten aus derselben Sitzung in der Warteschlange während der aktuellen Workflowausführung.

Einzelheiten zur JSON-Datei dieser Vorlage finden Sie unter [GitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Weitere Informationen finden Sie unter [Muster für einen sequenziellen Konvoi](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein Service Bus-Namespace und eine [Service Bus-Warteschlange](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) (die Messagingentität, die Sie in Ihrer Logik-App verwenden). Diese Elemente und Ihre Logik-App müssen dasselbe Azure-Abonnement verwenden. Stellen Sie sicher, dass Sie beim Erstellen Ihrer Warteschlange die Option **Sitzungen aktivieren** auswählen. Wenn Sie nicht über diese Elemente verfügen, informieren Sie sich, wie Sie [Ihren Service Bus-Namespace und eine Warteschlange erstellen](../service-bus-messaging/service-bus-create-namespace-portal.md).

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Grundlegende Kenntnisse zum Erstellen von Logik-Apps. Wenn Sie noch nicht mit Azure Logic Apps vertraut sind, finden Sie in der Schnellstartanleitung [Erstellen Ihres ersten Workflows mithilfe von Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) nähere Informationen.

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Überprüfen des Zugriffs auf den Service Bus-Namespace

Wenn Sie nicht sicher sind, ob Ihre Logik-App über die erforderlichen Berechtigungen für den Zugriff auf Ihren Service Bus-Namespace verfügt, überprüfen Sie diese Berechtigungen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Suchen Sie Ihren Service Bus-*Namespace*, und wählen Sie ihn aus.

1. Wählen Sie im Menü „Namespace“ unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus. Stellen Sie im Bereich **Ansprüche** sicher, dass Sie die **Verwaltungsberechtigungen** für diesen Namespace besitzen.

   ![Verwalten von Berechtigungen für einen Service Bus-Namespace](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Rufen Sie nun die Verbindungszeichenfolge für Ihren Service Bus-Namespace ab. Sie können diese Zeichenfolge später verwenden, wenn Sie aus Ihrer Logik-App eine Verbindung zum Namespace herstellen.

   1. Wählen Sie im Bereich **Richtlinien für gemeinsamen Zugriff** unter **Richtlinie** den Eintrag **RootManageSharedAccessKey** aus.
   
   1. Wählen Sie neben der primären Verbindungszeichenfolge die Schaltfläche „Kopieren“ aus. Speichern Sie die Verbindungszeichenfolge für die spätere Verwendung.

      ![Kopieren der Verbindungszeichenfolge für Service Bus-Namespaces](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Um zu überprüfen, ob Ihre Verbindungszeichenfolge mit Ihrem Service Bus-Namespace oder einer Messagingentität (z.B. einer Warteschlange) verknüpft ist, suchen Sie die Verbindungszeichenfolge für den Parameter `EntityPath`. Wenn Sie diesen Parameter gefunden haben, gilt die Verbindungszeichenfolge für eine bestimmte Entität und stellt nicht die richtige Zeichenfolge für die Verwendung mit Ihrer Logik-App dar.

## <a name="create-logic-app"></a>Erstellen einer Logik-App

In diesem Abschnitt erstellen Sie unter Verwendung der Vorlage **Zustellung korrelierter Nachrichten in der richtigen Reihenfolge mithilfe von Service Bus-Sitzungen** eine Logik-App, die den Trigger und die Aktionen für die Implementierung dieses Workflowmusters enthält. Darüber hinaus stellen Sie eine Verbindung mit Ihrem Service Bus-Namespace her und geben den Namen für die Service Bus-Warteschlange an, die verwendet werden soll.

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) eine leere Logik-App. Wählen Sie auf der Azure-Startseite **Ressource erstellen** > **Integration** > **Logik-App** aus.

1. Scrollen Sie im Vorlagenkatalog nach unten, und überspringen Sie dabei das Video und die Abschnitte zu gängigen Triggern. Wählen Sie im Abschnitt **Vorlagen** die Vorlage **Zustellung korrelierter Nachrichten in der richtigen Reihenfolge mithilfe von Service Bus-Sitzungen** aus.

   ![Auswahl der Vorlage „Zustellung korrelierter Nachrichten in der richtigen Reihenfolge mithilfe von Service Bus-Sitzungen“](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Wenn das Bestätigungsfeld angezeigt wird, wählen Sie **Diese Vorlage verwenden** aus.

1. Wählen Sie im Logik-App-Designer unter **Service Bus** die Option **Weiter** und dann das Pluszeichen ( **+** ) aus.

   ![Auswahl von „Weiter“, um eine Verbindung mit Azure Service Bus herzustellen](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Erstellen Sie nun eine Service Bus-Verbindung, indem Sie eine der folgenden Optionen auswählen:

   * Zur Verwendung der Verbindungszeichenfolge, die Sie zuvor aus Ihrem Service Bus-Namespace kopiert haben, führen Sie die folgenden Schritte aus:

     1. Wählen Sie **Verbindungsinformationen manuell eingeben** aus.

     1. Geben Sie in **Verbindungsname** einen Namen für Ihre Verbindung ein. Fügen Sie in **Verbindungszeichenfolge** die Verbindungszeichenfolge Ihres Namespace ein, und wählen Sie **Erstellen** aus. Beispiel:

        ![Eingabe des Verbindungsnamens und der Service Bus-Verbindungszeichenfolge](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Wenn Sie nicht über diese Verbindungszeichenfolge verfügen, finden Sie unter [Ermitteln und Kopieren der Verbindungszeichenfolge für Ihren Service Bus-Namespace](#permissions-connection-string) Informationen dazu, wo Sie sie finden.

   * Zur Auswahl eines Service Bus-Namespace aus Ihrem aktuellen Azure-Abonnement führen Sie folgende Schritte aus:

     1. Geben Sie in **Verbindungsname** einen Namen für Ihre Verbindung ein. Wählen Sie für **Service Bus-Namespace** Ihren Service Bus-Namespace aus. Beispiel:

        ![Eingabe des Verbindungsnamens und Auswahl des Service Bus-Namespace](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Wählen Sie im nächsten Bereich Ihre Service Bus-Richtlinie und dann **Erstellen** aus.

        ![Auswahl der Service Bus-Richtlinie sowie der Option „Erstellen“](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Wenn Sie fertig sind, wählen Sie **Weiter** aus.

   Im Logik-App-Designer wird jetzt die Vorlage **Zustellung korrelierter Nachrichten in der richtigen Reihenfolge mithilfe von Service Bus-Sitzungen** angezeigt, die einen vorab aufgefüllten Workflow mit einem Trigger und Aktionen enthält. U. a. sind zwei Bereiche zur Implementierung der Fehlerbehandlung enthalten, die dem `Try-Catch`-Muster folgen.

Sie können sich jetzt entweder weiter mit dem Trigger und den Aktionen in der Vorlage vertraut machen oder mit dem Abschnitt zum [Angeben der Werte für die Logik-App-Vorlage](#complete-template) fortfahren.

<a name="template-summary"></a>

## <a name="template-summary"></a>Vorlagenzusammenfassung

Nachfolgend finden Sie den allgemeinen Workflow in der Vorlage **Zustellung korrelierter Nachrichten in der richtigen Reihenfolge mithilfe von Service Bus-Sitzungen** mit reduzierten Details:

![Allgemeiner Workflow der Vorlage](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Name | BESCHREIBUNG |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | Dieser Service Bus-Trigger überprüft basierend auf der festgelegten Wiederholungseinstellung, ob in der angegebenen Service Bus-Warteschlange Nachrichten vorhanden sind. Wenn eine Nachricht in der Warteschlange vorhanden ist, wird der Trigger ausgelöst, sodass eine Workflowinstanz erstellt und ausgeführt wird. <p><p>*Peek-Lock* bedeutet, dass der Trigger eine Anforderung zum Abrufen einer Nachricht aus der Warteschlange sendet. Wenn eine Nachricht vorhanden ist, ruft der Trigger die Nachricht ab und sperrt sie, damit keine weitere Verarbeitung für diese Nachricht erfolgt, bis die Sperrfrist abgelaufen ist. Einzelheiten finden Sie unter [Initialisieren der Sitzung](#initialize-session). |
| **`Init isDone`** | Mit dieser [**Variable initialisieren**-Aktion](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) wird eine boolesche Variable erstellt, die auf `false` festgelegt wird und anzeigt, wenn die folgenden Bedingungen erfüllt sind: <p><p>– In der Sitzung sind keine weiteren Nachrichten verfügbar, die gelesen werden können. <br>– Die Sitzungssperre muss nicht mehr verlängert werden, sodass die aktuelle Workflowinstanz abgeschlossen werden kann. <p><p>Einzelheiten finden Sie unter [Initialisieren der Sitzung](#initialize-session). |
| **`Try`** | Diese [**Bereich**-Aktion](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) beinhaltet die Aktionen, die zum Verarbeiten einer Nachricht ausgeführt werden. Wenn im Bereich `Try` ein Problem auftritt, wird dieses Problem von der nachfolgenden **Bereich**-Aktion `Catch` behandelt. Weitere Informationen finden Sie unter [Der Bereich „Try“](#try-scope). |
| **`Catch`**| Diese [**Bereich**-Aktion](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) umfasst die Aktionen, die bei einem Problem im vorangehenden `Try`-Bereich ausgeführt werden. Weitere Informationen finden Sie unter [Der Bereich „Catch“](#catch-scope). |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>Der Bereich „Try“

Nachfolgend ist der allgemeine Flow in der `Try`-[Bereichsaktion](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) mit reduzierten Details beschrieben:

![Der Workflow der Bereichsaktion „Try“](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Name | BESCHREIBUNG |
|------|-------------|
| **`Send initial message to topic`** | Sie können diese Aktion durch eine beliebige Aktion ersetzen, die die erste Nachricht aus der Sitzung in der Warteschlange behandeln soll. Die Sitzungs-ID gibt die Sitzung an. <p><p>Bei dieser Vorlage sendet eine Service Bus-Aktion die erste Nachricht an ein Service Bus-Thema. Einzelheiten finden Sie unter [Behandeln der ersten Nachricht](#handle-initial-message). |
| (parallele Verzweigung) | Mit dieser [Aktion für eine parallele Verzweigung](../logic-apps/logic-apps-control-flow-branches.md) werden zwei Pfade erstellt: <p><p>– Branch 1: Verarbeitung der Nachricht fortsetzen. Weitere Informationen finden Sie unter [Branch 1: Erste Nachricht in der Warteschlange abschließen](#complete-initial-message). <p><p>– Branch 2: Nachricht verwerfen, wenn ein Problem auftritt, und zur Aufnahme durch eine andere Triggerausführung freigeben. Weitere Informationen finden Sie unter [Branch 2: Erste Nachricht aus Warteschlange verwerfen](#abandon-initial-message). <p><p>Die beiden Pfade werden später in der Aktion **Sitzung in einer Warteschlange schließen, erfolgreicher Vorgang** zusammengeführt, die in der nächsten Zeile beschrieben wird. |
| **`Close a session in a queue and succeed`** | Mit dieser Service Bus-Aktion werden die zuvor beschriebenen Verzweigungen zusammengeführt, und die Sitzung in der Warteschlange wird geschlossen, nachdem eins der folgenden Ereignisse eintritt: <p><p>– Der Workflow schließt die Verarbeitung verfügbarer Nachrichten in der Warteschlange ab. <br>– Der Workflow verwirft die erste Nachricht, weil ein Fehler aufgetreten ist. <p><p>Weitere Informationen finden Sie unter [Sitzung in einer Warteschlange schließen, erfolgreicher Vorgang](#close-session-succeed). |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>Branch 1: Erste Nachricht in der Warteschlange abschließen

| Name | BESCHREIBUNG |
|------|-------------|
| `Complete initial message in queue` | Diese Service Bus-Aktion markiert eine erfolgreich abgerufene Nachricht als abgeschlossen und entfernt die Nachricht aus der Warteschlange, um eine erneute Verarbeitung zu verhindern. Einzelheiten finden Sie unter [Behandeln der ersten Nachricht](#handle-initial-message). |
| `While there are more messages for the session in the queue` | Diese [**Until**-Schleife](../logic-apps/logic-apps-control-flow-loops.md#until-loop) ruft weiter Nachrichten ab, solange Nachrichten vorhanden sind oder bis eine Stunde vergangen ist. Weitere Informationen zu den Aktionen in dieser Schleife finden Sie unter [Wenn weitere Nachrichten für die Sitzung in der Warteschlange vorhanden sind](#while-more-messages-for-session). |
| **`Set isDone = true`** | Wenn keine weiteren Nachrichten vorhanden sind, setzt diese [**Variable festlegen**-Aktion](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) `isDone` auf `true`. |
| **`Renew session lock until cancelled`** | Mit dieser [**Until**-Schleife](../logic-apps/logic-apps-control-flow-loops.md#until-loop) wird sichergestellt, dass die Sitzungssperre von dieser Logik-App beibehalten wird, solange Nachrichten vorhanden sind oder bis eine Stunde vergangen ist. Weitere Informationen zu den Aktionen in dieser Schleife finden Sie unter [Sitzungssperre bis zum Abbruch verlängern](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>Branch 2: Erste Nachricht aus Warteschlange verwerfen

Wenn bei der Aktion, die die erste Nachricht behandelt, ein Fehler auftritt, gibt die Service Bus-Aktion **Erste Nachricht aus Warteschlange verwerfen** die Nachricht für die Aufnahme und Verarbeitung durch eine andere Workflowinstanzausführung frei. Einzelheiten finden Sie unter [Behandeln der ersten Nachricht](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>Der Bereich „Catch“

Wenn bei Aktionen im `Try`-Bereich Fehler auftreten, muss die Logik-App die Sitzung dennoch schließen. Die `Catch`-[Bereichsaktion](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) wird ausgeführt, wenn die `Try`-Bereichsaktion den Status `Failed`, `Skipped` oder `TimedOut` aufweist. Der Bereich gibt eine Fehlermeldung mit der Sitzungs-ID zurück, bei der das Problem aufgetreten ist, und beendet die Logik-App.

Nachfolgend ist der allgemeine Flow in der `Catch`-Bereichsaktion mit reduzierten Details beschrieben:

![Der Workflow der Bereichsaktion „Catch“](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Name | BESCHREIBUNG |
|------|-------------|
| **`Close a session in a queue and fail`** | Diese Service Bus-Aktion schließt die Sitzung in der Warteschlange, sodass die Sitzungssperre nicht erhalten bleibt. Weitere Informationen finden Sie unter [Sitzung in einer Warteschlange schließen, Vorgang mit Fehler](#close-session-fail). |
| **`Find failure msg from 'Try' block`** | Mit dieser [**Array filtern**-Aktion](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) wird ein Array aus den Ein- und Ausgaben aller Aktionen innerhalb des `Try`-Bereichs basierend auf den angegebenen Kriterien erstellt. In diesem Fall gibt die Aktion die Ausgaben der Aktionen zurück, die den Status `Failed` aufweisen. Einzelheiten finden Sie unter [Fehlermeldung im „Try“-Block finden](#find-failure-message). |
| **`Select error details`** | Mit dieser [**Auswahl**-Aktion](../logic-apps/logic-apps-perform-data-operations.md#select-action) wird ein Array mit JSON-Objekten basierend auf den angegebenen Kriterien erstellt. Diese JSON-Objekte werden anhand der Werte des Arrays erstellt, das von der vorherigen Aktion (`Find failure msg from 'Try' block`) erstellt wurde. In diesem Fall gibt diese Aktion ein Array mit einem JSON-Objekt zurück, das anhand der Fehlerdetails erstellt wurde, die von der vorherigen Aktion zurückgegeben wurden. Weitere Informationen finden Sie unter [Auswahl der Fehlerdetails](#select-error-details). |
| **`Terminate`** | Mit dieser [**Beenden**-Aktion](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) wird die Ausführung für den Workflow beendet, alle aktiven Aktionen werden abgebrochen, und alle verbleibenden Aktionen werden übersprungen. Außerdem werden der angegebene Status, die Sitzungs-ID und das Fehlerergebnis der `Select error details`-Aktion zurückgegeben. Einzelheiten finden Sie unter [Logik-App-Ausführung beenden](#terminate-logic-app). |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>Abschließen der Vorlage

Führen Sie die folgenden Schritte aus, um die Werte für den Trigger und die Aktionen in der Vorlage **Zustellung korrelierter Nachrichten in der richtigen Reihenfolge mithilfe von Service Bus-Sitzungen** anzugeben. Bevor Sie Ihre Logik-App speichern können, müssen Sie alle erforderlichen Werte angeben. Diese sind mit einem Sternchen ( **\*** ) gekennzeichnet.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>Initialisieren der Sitzung

* Geben Sie diese Informationen für den Trigger **Wenn eine Nachricht in einer Warteschlange empfangen wird (Peek-Lock)** an, damit die Vorlage eine Sitzung unter Verwendung der Eigenschaft **Sitzungs-ID** initialisieren kann. Beispiel:

  ![Service Bus-Triggerdetails für „Wenn eine Nachricht in einer Warteschlange empfangen wird (Peek-Lock)“](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Das Abrufintervall ist anfänglich auf drei Minuten festgelegt, damit die Logik-App nicht häufiger ausgeführt wird als erwartet und keine unerwarteten Kosten entstehen. Idealerweise legen Sie für Intervall und Häufigkeit 30 Sekunden fest, damit die Logik-App umgehend ausgelöst wird, wenn eine Nachricht eingeht.

  | Eigenschaft | Für dieses Szenario erforderlich | Wert | BESCHREIBUNG |
  |----------|----------------------------|-------|-------------|
  | **Warteschlangenname** | Ja | <*Warteschlangenname*> | Der Name Ihrer zuvor erstellten Service Bus-Warteschlange. In diesem Beispiel wird „Fabrikam-Service-Bus-Queue“ verwendet. |
  | **Warteschlangentyp** | Ja | **Hauptseite** | Ihre primäre Service Bus-Warteschlange |
  | **Sitzungs-ID** | Ja | **Nächste verfügbare** | Mit dieser Option wird eine Sitzung für jede Triggerausführung abgerufen, die auf der Sitzungs-ID aus der Nachricht in der Service Bus-Warteschlange basiert. Die Sitzung wird zudem gesperrt, damit keine andere Logik-App und kein anderer Client Nachrichten verarbeiten kann, die mit dieser Sitzung zusammenhängen. Die nachfolgenden Aktionen des Workflows verarbeiten alle Nachrichten, die mit dieser Sitzung zusammenhängen. Dies wird weiter unten in diesem Artikel näher erläutert. <p><p>Im Folgenden finden Sie weitere Informationen zu den übrigen Optionen von **Sitzungs-ID**: <p>- **Keine**: Die Standardoption, bei der keine Sitzungen verwendet werden. Diese Option kann nicht verwendet werden, wenn das Muster für einen sequenziellen Konvoi implementiert werden soll. <p>- **Benutzerdefinierten Wert eingeben**: Verwenden Sie diese Option, wenn Sie die Sitzungs-ID kennen, die Sie verwenden möchten, und Sie den Trigger immer für diese Sitzungs-ID ausführen möchten. <p>**Hinweis**: Der Service Bus-Connector kann eine beschränkte Anzahl von eindeutigen Sitzungen von Azure Service Bus im Connectorcache speichern. Wenn die Anzahl der Sitzungen dieses Limit überschreitet, werden alte Sitzungen aus dem Cache entfernt. Weitere Informationen finden Sie unter [Austauschen von Nachrichten in der Cloud mit Azure Logic Apps und Azure Service Bus](../connectors/connectors-create-api-servicebus.md#connector-reference). |
  | **Intervall** | Ja | <*Anzahl von Intervallen*> | Die Anzahl von Zeiteinheiten zwischen Wiederholungen, bevor überprüft wird, ob eine Nachricht vorhanden ist. |
  | **Frequency** | Ja | **Sekunde**, **Minute**, **Stunde**, **Tag**, **Woche** oder **Monat** | Die Zeiteinheit für die Wiederholung, wenn überprüft wird, ob eine Nachricht vorhanden ist. <p>**Tipp**: Zum Hinzufügen einer **Zeitzone** oder einer **Startzeit** wählen Sie diese Eigenschaften aus der Liste **Neuen Parameter hinzufügen** aus. |
  |||||

  Weitere Informationen zu Triggern finden Sie unter [Service Bus – Wenn eine Nachricht in einer Warteschlange empfangen wird (Peek-Lock)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). Der Trigger gibt eine [Service Bus-Nachricht](/connectors/servicebus/#servicebusmessage) aus.

Nach der Initialisierung der Sitzung verwendet der Workflow die **Variable initialisieren**-Aktion, um eine boolesche Variable zu erstellen, die anfänglich auf `false` festgelegt ist und anzeigt, wenn die folgenden Bedingungen erfüllt sind: 

* In der Sitzung sind keine weiteren Nachrichten verfügbar, die gelesen werden können.

* Die Sitzungssperre muss nicht mehr verlängert werden, sodass die aktuelle Workflowinstanz abgeschlossen werden kann.

![Details der „Variable initialisieren“-Aktion für „Init isDone“](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

Anschließend führt der Workflow im **Try**-Block Aktionen für die erste Nachricht aus, die gelesen wird.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Behandeln der ersten Nachricht

Die erste Aktion ist eine Service Bus-Platzhalteraktion (**Erste Nachricht an Thema senden**), die durch eine beliebige Aktion ersetzt werden kann, die Sie als erste Nachricht aus der Sitzung in der Warteschlange behandeln möchten. Die Sitzungs-ID gibt die Sitzung an, aus der die Nachricht stammt.

Die Service Bus-Platzhalteraktion sendet die erste Nachricht an ein Service Bus-Thema, das in der Eigenschaft **Sitzungs-ID** angegeben ist. Auf diese Weise werden alle Nachrichten, die einer bestimmten Sitzung zugeordnet sind, an dasselbe Thema weitergeleitet. Alle **Sitzungs-ID**-Eigenschaften für nachfolgende Aktionen in dieser Vorlage verwenden denselben Sitzungs-ID-Wert.

![Details der Service Bus-Aktion für „Erste Nachricht an Thema senden“](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. Geben Sie in der Service Bus-Aktion **Erste Nachricht in der Warteschlange abschließen** den Namen Ihrer Service Bus-Warteschlange an, und übernehmen Sie alle weiteren Standardeigenschaftswerte in der Aktion.

   ![Details der Service Bus-Aktion für „Erste Nachricht in der Warteschlange abschließen“](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. Geben Sie in der Service Bus-Aktion **Erste Nachricht aus Warteschlange verwerfen** den Namen Ihrer Service Bus-Warteschlange an, und übernehmen Sie alle weiteren Standardeigenschaftswerte in der Aktion.

   ![Details der Service Bus-Aktion für „Erste Nachricht aus Warteschlange verwerfen“](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Im nächsten Schritt geben Sie die erforderlichen Informationen für die Aktionen an, die nach der Aktion **Erste Nachricht in der Warteschlange abschließen** ausgeführt werden. Sie beginnen mit den Aktionen in der Schleife **Wenn weitere Nachrichten für die Sitzung in der Warteschlange vorhanden sind**.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Wenn weitere Nachrichten für die Sitzung in der Warteschlange vorhanden sind

Diese [**Until**-Schleife](../logic-apps/logic-apps-control-flow-loops.md#until-loop) führt diese Aktionen aus, solange Nachrichten in der Warteschlange vorhanden sind bzw. bis eine Stunde vergangen ist. Um das Zeitlimit der Schleife zu ändern, bearbeiten Sie den Eigenschaftswert **Timeout** der Schleife.

* Abrufen weiterer Nachrichten aus der Warteschlange, solange Nachrichten vorhanden sind.

* Überprüfen der Anzahl von verbleibenden Nachrichten. Wenn noch Nachrichten vorhanden sind, wird die Verarbeitung fortgesetzt. Wenn keine weiteren Nachrichten vorhanden sind, legt der Workflow die `isDone`-Variable auf `true`fest und beendet die Schleife.

![Until-Schleife – Verarbeiten von Nachrichten in der Warteschlange](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Geben Sie in der Service Bus-Aktion **Weitere Nachrichten aus Sitzung abrufen** den Namen Ihrer Service Bus-Warteschlange an. Übernehmen Sie alle anderen Standardeigenschaftswerte in der Aktion.

   > [!NOTE]
   > Die maximale Anzahl von Nachrichten ist standardmäßig auf `175` festgelegt. Dieses Limit wird jedoch durch die Nachrichtengröße und durch die Eigenschaft für die maximale Nachrichtengröße in Service Bus beeinflusst. Weitere Informationen finden Sie unter [Nachrichtengröße für eine Warteschlange](../service-bus-messaging/service-bus-quotas.md).

   ![Service Bus-Aktion „Weitere Nachrichten aus Sitzung abrufen“](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Im nächsten Schritt wird der Workflow in diese beiden parallelen Verzweigungen unterteilt:

   * Wenn beim Überprüfen auf weitere Nachrichten ein Fehler auftritt, legen Sie die `isDone`-Variable auf `true` fest.

   * Mit der Bedingung **Nachrichten verarbeiten, falls vorhanden** wird überprüft, ob die Anzahl von verbleibenden Nachrichten Null beträgt. Wenn der Wert „false“ lautet und weitere Nachrichten vorhanden sind, wird die Verarbeitung fortgesetzt. Wenn das Ergebnis „true“ lautet und keine weiteren Nachrichten vorhanden sind, legt der Workflow die `isDone`-Variable auf `true` fest.

   ![Bedingung „Nachrichten verarbeiten, falls vorhanden“](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   Im Abschnitt **Wenn FALSE** verarbeitet eine **For each**-Schleife die einzelnen Nachrichten in der FIFO-Reihenfolge (First-In, First-Out). In den **Einstellungen** der Schleife ist die Einstellung **Parallelitätssteuerung** auf `1` festgelegt, damit jeweils nur eine einzelne Nachricht verarbeitet wird.

   ![„For each“-Schleife – Aufeinanderfolgende Verarbeitung der einzelnen Nachrichten](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. Geben Sie für die Service Bus-Aktionen **Nachricht in einer Warteschlange abschließen** und **Nachricht in einer Warteschlange verwerfen** den Namen Ihrer Service Bus-Warteschlange an.

   ![Service Bus-Aktionen „Nachricht in einer Warteschlange abschließen“ und „Nachricht in einer Warteschlange verwerfen“](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Wenn die Schleife **Wenn weitere Nachrichten für die Sitzung in der Warteschlange vorhanden sind** abgeschlossen wurde, setzt der Workflow die `isDone`-Variable auf `true`.

Nun geben Sie die erforderlichen Informationen für die Aktionen in der Schleife **Sitzungssperre bis zum Abbruch verlängern** an.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Sitzungssperre bis zum Abbruch verlängern

Mit dieser [**Until**-Schleife](../logic-apps/logic-apps-control-flow-loops.md#until-loop) wird sichergestellt, dass die Sitzungssperre von dieser Logik-App beibehalten wird, solange Nachrichten in der Warteschlange vorhanden sind oder bis diese Aktionen für eine Stunde ausgeführt wurden. Um das Zeitlimit der Schleife zu ändern, bearbeiten Sie den Eigenschaftswert **Timeout** der Schleife.

* Verzögerung von 25 Sekunden oder eine Zeitdauer, die geringer ist als die Timeoutdauer der Sperre für die Warteschlange, die verarbeitet wird. Die kürzeste Sperrdauer beträgt 30 Sekunden, der Standardwert ist also ausreichend. Sie können die Anzahl von Schleifenausführungen jedoch optimieren, indem Sie diesen Wert entsprechend anpassen.

* Überprüfen Sie, ob die `isDone`-Variable auf `true` festgelegt ist.

  * Wenn `isDone` auf `true` festgelegt ist, verarbeitet der Workflow weiterhin Nachrichten und verlängert auf diese Weise die Sperre der Sitzung in der Warteschlange und überprüft die Schleifenbedingung erneut.

    Sie müssen den Namen Ihrer Service Bus-Warteschlange in der Service Bus-Aktion [**Sperre für die Sitzung in einer Warteschlange verlängern**](#renew-lock-on-session) angeben.

  * Wenn `isDone` auf `true` festgelegt ist, verlängert der Workflow die Sperre für die Sitzung in der Warteschlange nicht, und die Schleife wird beendet.

  ![Until-Schleife „Sitzungssperre bis zum Abbruch verlängern“](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Sperre für die Sitzung in einer Warteschlange verlängern

Mit dieser Service Bus-Aktion wird die Sperre für die Sitzung in der Warteschlange verlängert, solange der Workflow Nachrichten verarbeitet.

* Geben Sie in der Service Bus-Aktion **Sperre für die Sitzung in einer Warteschlange verlängern** den Namen Ihrer Service Bus-Warteschlange an.

  ![Service Bus-Aktion „Sperre für die Sitzung in einer Warteschlange verlängern“](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Nun geben Sie die erforderlichen Informationen für die Service Bus-Aktion **Sitzung in einer Warteschlange schließen, erfolgreicher Vorgang** an.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Sitzung in einer Warteschlange schließen, erfolgreicher Vorgang

Diese Service Bus-Aktion schließt die Sitzung in der Warteschlange, nachdem der Workflow die Verarbeitung aller verfügbaren Nachrichten in der Warteschlange abgeschlossen hat oder der Workflow die erste Nachricht verwirft.

* Geben Sie in der Service Bus-Aktion **Sitzung in einer Warteschlange schließen, erfolgreicher Vorgang** den Namen Ihrer Service Bus-Warteschlange an.

  ![Service Bus-Aktion „Sitzung in einer Warteschlange schließen, erfolgreicher Vorgang“](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

In den folgenden Abschnitten werden die Aktionen im Bereich `Catch` beschrieben, die Fehler und Ausnahmen im Workflow behandeln.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Sitzung in einer Warteschlange schließen, Vorgang mit Fehler

Diese Service Bus-Aktion wird immer als erste Aktion im `Catch`-Bereich ausgeführt und schließt die Sitzung in der Warteschlange.

* Geben Sie in der Service Bus-Aktion **Sitzung in einer Warteschlange schließen, Vorgang mit Fehler** den Namen Ihrer Service Bus-Warteschlange an.

  ![Service Bus-Aktion „Sitzung in einer Warteschlange schließen, Vorgang mit Fehler“](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Als Nächstes erstellt der Workflow ein Array, das die Eingaben und Ausgaben aller Aktionen im `Try`-Bereich umfasst. Anhand dieser Daten kann die Logik-App auf Informationen zum Fehler zugreifen.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Fehlermeldung im „Try“-Block finden

Mit dieser [**Array filtern**-Aktion](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) wird ein Array aus den Ein- und Ausgaben aller Aktionen innerhalb des `Try`-Bereichs basierend auf den angegebenen Kriterien erstellt. Dazu wird die [`result()`-Funktion](../logic-apps/workflow-definition-language-functions-reference.md#result) verwendet. In diesem Fall gibt diese Aktion die Ausgaben der Aktionen mit dem Status `Failed` zurück. Zu diesem Zweck werden die [`equals()`-Funktion](../logic-apps/workflow-definition-language-functions-reference.md#equals) und die [`item()`-Funktion](../logic-apps/workflow-definition-language-functions-reference.md#item) verwendet.

![„Array filtern“-Aktion „Fehlermeldung im ‚Try‘-Block finden“](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Nachfolgend ist die JSON-Definition für diese Aktion gezeigt:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

Im nächsten Schritt erstellt der Workflow ein Array mit einem JSON-Objekt, das die Fehlerinformationen im Array enthält, die von der `Find failure msg from 'Try' block`-Aktion zurückgegebenen wurden.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Auswahl der Fehlerdetails

Mit dieser [**Auswählen**-Aktion](../logic-apps/logic-apps-perform-data-operations.md#select-action) wird ein Array mit JSON-Objekten basierend auf dem Eingabearray erstellt, das von der vorherigen Aktion (`Find failure msg from 'Try' block`) ausgegeben wurde. Das von dieser Aktion zurückgegebene Array enthält lediglich die angegebenen Eigenschaften für die einzelnen Objekte im Array. In diesem Fall enthält das Array den Aktionsnamen und das Fehlerergebnis.

![Auswählen-Aktion „Fehlerdetails auswählen“](./media/send-related-messages-sequential-convoy/select-error-details.png)

Nachfolgend ist die JSON-Definition für diese Aktion gezeigt:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

Im nächsten Schritt hält der Workflow die Ausführung der Logik-App an und gibt ihren Status sowie weitere Informationen zum aufgetretenen Fehler zurück.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Logik-App-Ausführung beenden

Mit dieser [**Beenden**-Aktion](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) wird die Ausführung der Logik-App angehalten und `Failed` als Status der Ausführung zurückgegeben. Außerdem werden die Sitzungs-ID und das Fehlerergebnis der `Select error details`-Aktion zurückgegeben.

![Beenden-Aktion zum Anhalten der Logik-App-Ausführung](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Nachfolgend ist die JSON-Definition für diese Aktion gezeigt:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Speichern und Ausführen der Logik-App

Nachdem Sie die Vorlage fertig gestellt haben, können Sie Ihre Logik-App speichern. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

Senden Sie Nachrichten an Ihre Service Bus-Warteschlange, um Ihre Logik-App zu testen. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den [Triggern und Aktionen des Service Bus-Connectors](/connectors/servicebus/)
