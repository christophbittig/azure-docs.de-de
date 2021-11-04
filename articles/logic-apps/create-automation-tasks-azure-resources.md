---
title: Erstellen von Automatisierungsaufgaben zum Verwalten und Überwachen von Azure-Ressourcen
description: Einrichten automatisierter Aufgaben, mit denen Sie Azure-Ressourcen verwalten und Kosten überwachen können, indem Sie Workflows erstellen, die auf Azure Logic Apps ausgeführt werden.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 436a9422adcc9f0e564d34c68d3ed0d5b9185151
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079660"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Verwalten von Azure-Ressourcen und Überwachen von Kosten durch Erstellen von Automatisierungsaufgaben (Vorschau)

> [!IMPORTANT]
> Diese Funktion befindet sich in der Vorschauphase und unterliegt den [Zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um Ihnen die Verwaltung von [Azure-Ressourcen](../azure-resource-manager/management/overview.md#terminology) zu erleichtern, können Sie automatisierte Verwaltungsaufgaben für eine bestimmte Ressource oder Ressourcengruppe mithilfe von Vorlagen für Automatisierungsaufgaben erstellen, deren Verfügbarkeit je nach Ressourcentyp variiert. Beispielsweise können Sie für ein [Azure-Speicherkonto](../storage/common/storage-account-overview.md) eine Automatisierungsaufgabe einrichten, die Ihnen die monatlichen Kosten für dieses Speicherkonto sendet. Für einen [virtuellen Azure-Computer](https://azure.microsoft.com/services/virtual-machines/) können Sie eine Automatisierungsaufgabe erstellen, die diesen virtuellen Computer nach einem vordefinierten Zeitplan ein- und ausschaltet.

Im Folgenden sehen Sie die aktuell verfügbaren Aufgabenvorlagen in dieser Vorschau:

| Ressourcentyp | Vorlagen für Automatisierungsaufgaben |
|---------------|---------------------------|
| Azure-Ressourcengruppen | **Wenn die Ressource gelöscht wird** |
| Alle Azure-Ressourcen | **Monatliche Kosten für Ressource senden** |
| Virtuelle Azure-Computer | Darüber hinaus gilt: <p>- **Virtuellen Computer ausschalten** <br>- **Virtuellen Computer starten** |
| Azure Storage-Konten | Darüber hinaus gilt: <p>- **Alte Blobs löschen** |
| Azure Cosmos DB | Darüber hinaus gilt: <p>- **Abfrageergebnis per E-Mail senden** |
|||

In diesem Artikel wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* [Erstellen einer Automatisierungsaufgabe](#create-automation-task) für eine bestimmte Azure-Ressource.

* [Überprüfen des Verlaufs einer Aufgabe](#review-task-history), was den Ausführungsstatus, Eingaben, Ausgaben und andere Verlaufsinformationen umfasst.

* [Bearbeiten der Aufgabe](#edit-task), sodass Sie die Aufgabe aktualisieren oder den der Aufgabe zugrunde liegenden Workflow im Workflow-Designer anpassen können.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Wie unterscheiden sich Automatisierungsaufgaben von Azure Automation?

Automatisierungsaufgaben sind grundlegender und schlanker als [Azure Automation](../automation/automation-intro.md). Derzeit können Sie eine Automatisierungsaufgabe nur auf der Azure-Ressourcenebene erstellen. Eine Automatisierungsaufgabe ist eigentlich eine Logik-App-Ressource, die einen Workflow ausführt und vom [*mehrinstanzenfähigen* Azure Logic Apps-Dienst](logic-apps-overview.md) unterstützt wird. Nachdem Sie die Automatisierungsaufgabe erstellt haben, können Sie den zugrunde liegenden Workflow anzeigen und bearbeiten, indem Sie die Aufgabe im Workflow-Designer öffnen. Nachdem eine Aufgabe mindestens eine Ausführung abgeschlossen hat, können Sie den Status der Aufgabe, die Workflowausführung, den Verlauf, die Eingaben und die Ausgaben für jede Ausführung überprüfen.

Im Vergleich dazu ist Azure Automation ein cloudbasierter Automatisierungs- und Konfigurationsdienst, der eine einheitliche Verwaltung Ihrer Azure- und Nicht-Azure-Umgebungen unterstützt. Der Dienst umfasst [Prozessautomatisierung für Orchestrierungszwecke](../automation/automation-intro.md#process-automation) durch Verwendung von [Runbooks](../automation/automation-runbook-execution.md), Konfigurationsverwaltung mit [Änderungsnachverfolgung und Bestand](../automation/change-tracking/overview.md), Updateverwaltung, gemeinsam genutzte Funktionen und Features für heterogene Umgebungen. Mit Azure Automation haben Sie die volle Kontrolle über Bereitstellung, Ausführung und Außerbetriebnahme von Workloads und Ressourcen.

<a name="pricing"></a>

## <a name="pricing"></a>Preise

Wenn Sie eine Automatisierungsaufgabe erstellen, fallen nicht automatisch Gebühren an. Darunter basiert ein Automatisierungstask auf einem Workflow in einer Logik-App-Ressource, die in mehrinstanzenfähigen Azure Logic Apps-Instanzen gehostet wird. Das [Verbrauchspreismodell](logic-apps-pricing.md) gilt also für Automatisierungsaufgaben. Die Messung und die Abrechnung basieren auf den Trigger- und Aktionsausführungen des zugrunde liegenden Logik-App-Workflows.

Die Ausführungen werden gemessen und abgerechnet, unabhängig davon, ob der Workflow erfolgreich ausgeführt wird oder ob er überhaupt instanziiert wird. Gehen wir beispielsweise davon aus, dass Ihre Automatisierungsaufgabe einen Abruftrigger verwendet, der regelmäßig ausgehende Aufrufe an einen Endpunkt sendet. Diese ausgehenden Anforderungen werden gemessen und als Ausführung abgerechnet, unabhängig davon, ob der Trigger ausgelöst oder übersprungen wurde. Dies wirkt sich darauf aus, ob eine Workflowinstanz erstellt wird.

Trigger und Aktionen folgen [Verbrauchsplan-Tarifen](https://azure.microsoft.com/pricing/details/logic-apps/). Diese unterscheiden sich auf Grundlage dessen, ob es sich um [„integrierte“](../connectors/built-in.md) oder [„verwaltete“ (Standard oder Enterprise)](../connectors/managed.md) Vorgänge handelt. Trigger und Aktionen führen ebenfalls Speichertransaktionen durch, die auf die [Verbrauchsplan-Datenrate](https://azure.microsoft.com/pricing/details/logic-apps/) zurückgreifen.

> [!TIP]
> Als monatlichen Bonus enthält der Verbrauchsplan *mehrere Tausend* integrierte Ausführungen, die kostenlos sind. Spezifische Informationen hierzu können Sie den [Verbrauchsplan-Tarifen](https://azure.microsoft.com/pricing/details/logic-apps/) entnehmen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Die Azure-Ressource, die Sie verwalten möchten. In diesem Artikel wird ein Azure-Speicherkonto als Beispiel verwendet.

* Ein Office 365-Konto, wenn Sie das Beispiel parallel durcharbeiten möchten, das Ihnen E-Mails per Office 365 Outlook sendet.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Erstellen einer Automatisierungsaufgabe

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) die Ressource, die Sie verwalten möchten.

1. Scrollen Sie im Navigationsmenü zum Abschnitt **Automatisierung**, und wählen Sie **Aufgaben (Vorschau)** aus.

   ![Screenshot: Ressourcenmenü für Azure-Portal und Speicherkonto mit ausgewählter Option „Aufgaben (Vorschau)“](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. Wählen Sie im Bereich **Aufgaben** den Befehl **Aufgabe hinzufügen** aus, damit Sie eine Aufgabenvorlage auswählen können.

   ![Screenshot: Bereich „Aufgaben (Vorschau)“ mit ausgewählter Option „Aufgabe hinzufügen“](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. Wählen Sie im Bereich **Aufgabe hinzufügen** unter **Vorlage auswählen** die Vorlage für den Replikationstask aus, die Sie erstellen möchten, und wählen Sie **Auswählen** aus. Wenn die nächste Seite nicht angezeigt wird, wählen Sie **Weiter: Authentifizierung** aus.

   Dieses Beispiel wird fortgesetzt, indem Sie die Aufgabenvorlage **Monatliche Kosten für Ressource senden** auswählen.

   ![Screenshot: Bereich „Aufgabe hinzufügen“ mit ausgewählter Vorlage „Monatliche Kosten für Ressource senden“](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. Wählen Sie unter **Authentifizierung** im Abschnitt **Verbindungen** für jede Verbindung **Erstellen** aus, die in der Aufgabe angezeigt wird, damit Sie Anmeldeinformationen für die Authentifizierung für die jeweilige Verbindung angeben können. Die Verbindungstypen in den einzelnen Aufgaben variieren je nach Aufgabe.

   Dieses Beispiel zeigt nur eine der Verbindungen, die diese Aufgabe benötigt.

   ![Screenshot, der die ausgewählte Option „Erstellen“ für die Azure Resource Manager-Verbindung zeigt](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos an, wenn Sie dazu aufgefordert werden.

   ![Screenshot, der die Auswahl „Anmelden“ zeigt](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Jede erfolgreich authentifizierte Verbindung sieht in etwa wie im folgenden Beispiel aus:

   ![Screenshot, der eine erfolgreich hergestellte Verbindung zeigt](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Nachdem Sie alle Verbindungen authentifiziert haben, wählen Sie **Weiter: Konfigurieren** aus, wenn die nächste Seite nicht angezeigt wird.

1. Geben Sie unter **Konfigurieren** einen Namen für die Aufgabe und alle anderen für die Aufgabe erforderlichen Informationen an. Wählen Sie abschließend **Überprüfen + erstellen** aus.

   > [!NOTE]
   > Sie können den Namen der Aufgabe nach deren Erstellung nicht mehr ändern. Erwägen Sie daher einen Namen, der auch dann noch zutrifft, wenn Sie den [zugrunde liegenden Workflow bearbeiten](#edit-task-workflow). Änderungen, die Sie am zugrunde liegenden Workflow vornehmen, gelten nur für die Aufgabe, die Sie erstellt haben, nicht für die Aufgabenvorlage.
   >
   > Wenn Sie z. B. Ihre Aufgabe `SendMonthlyCost` nennen, den zugrunde liegenden Workflow aber zu einem späteren Zeitpunkt so bearbeiten, dass er wöchentlich ausgeführt wird, können Sie den Namen der Aufgabe nicht in `SendWeeklyCost` ändern.

   Aufgaben, die E-Mail-Benachrichtigungen senden, benötigen eine E-Mail-Adresse.

   ![Screenshot, der die erforderlichen Informationen für die ausgewählte Aufgabe zeigt](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   Die Aufgabe, die Sie erstellt haben, die automatisch aktiv ist und ausgeführt wird, wird jetzt in der Liste **Aufgaben** angezeigt.

   ![Screenshot, der die Liste mit den Automatisierungsaufgaben zeigt.](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Wenn die Aufgabe nicht sofort angezeigt wird, versuchen Sie, die Aufgabenliste zu aktualisieren, oder warten Sie ein wenig vor dem Aktualisieren. Wählen Sie auf der Symbolleiste **Aktualisieren** aus.

   Sobald die ausgewählte Aufgabe ausgeführt wird, erhalten Sie eine E-Mail, die wie folgt aussieht:

   ![Screenshot, der die von der Aufgabe gesendete E-Mail-Benachrichtigung zeigt](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Überprüfen des Aufgabenverlaufs

Um den Ausführungsverlauf einer Aufgabe zusammen mit deren Statuswerten, Eingaben, Ausgaben und anderen Informationen anzuzeigen, führen Sie die folgenden Schritte aus:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) die Ressource, zu der der Aufgabenverlauf gehört, den Sie überprüfen möchten.

1. Scrollen Sie im Ressourcenmenü unter **Einstellungen** zum Abschnitt **Automatisierung**, und wählen Sie **Aufgaben (Vorschau)** aus.

1. Suchen Sie in der Aufgabenliste die Aufgabe, die Sie überprüfen möchten. Wählen Sie in der Spalte **Ausführungen** dieser Aufgabe **Ansicht** aus.

   ![Screenshot, der eine Aufgabe und die ausgewählte Option „Ansicht“ zeigt](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   Im Bereich **Ausführungsverlauf** werden alle Ausführungen für die Aufgabe sowie deren Statuswerte, Startzeiten, Bezeichner und Ausführungsdauern angezeigt.

   ![Screenshot, der die Ausführungen einer Aufgabe, deren Statuswerte und andere Informationen zeigt](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Eine Ausführung kann folgende Statuswerte haben:

   | Status | BESCHREIBUNG |
   |--------|-------------|
   | **Abgebrochen** | Die Aufgabe wurde während der Ausführung abgebrochen. |
   | **Fehler** | Die Aufgaben hat mindestens eine fehlgeschlagene Aktion, aber es gab keine nachfolgenden Aktionen, um den Fehler zu beheben. |
   | **Wird ausgeführt** | Die Aufgabe wird gerade ausgeführt. |
   | **Erfolgreich** | Alle Aktionen waren erfolgreich. Eine Aufgabe kann auch noch erfolgreich abgeschlossen werden, wenn eine Aktion fehlgeschlagen ist, aber eine nachfolgende Aktion vorhanden war, um den Fehler zu beheben. |
   | **Wartet** | Die Ausführung wurde noch nicht gestartet und ist angehalten, weil noch eine frühere Instanz der Aufgabe ausgeführt wird. |
   |||

   Weitere Informationen finden Sie unter [Überprüfen des Ausführungsverlaufs in der Überwachungsansicht](monitor-logic-apps.md#review-runs-history).

1. Um die Statuswerte und anderen Informationen für jeden Schritt in einer Ausführung zu überprüfen, wählen Sie diese Ausführung aus.

   Der Bereich **Logik-App-Ausführung** wird geöffnet und zeigt den zugrunde liegenden Workflow an, der ausgeführt wurde.

   * Ein Workflow beginnt immer mit einem [*Trigger*](../connectors/apis-list.md#triggers). Für diese Aufgabe beginnt der Workflow mit dem [**Wiederholungs** trigger](../connectors/connectors-native-recurrence.md).

   * Jeder Schritt zeigt seinen Status und die Ausführungsdauer an. Die Ausführung von Schritten mit einer Ausführungsdauer von 0 Sekunden war kürzer als 1 Sekunde.

   ![Screenshot, der jeden Schritt in der Ausführung, den Status und die Ausführungsdauer zeigt](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Um die Eingaben und Ausgaben für jeden einzelnen Schritt zu überprüfen, wählen Sie den Schritt aus, der erweitert wird.

   In diesem Beispiel werden die Eingaben für den Wiederholungstrigger gezeigt, der keine Ausgaben hat, da der Trigger nur angibt, wann der Workflow ausgeführt wird, und keine Ausgaben für die nachfolgenden Aktionen zur Verarbeitung bereitstellt.

   ![Screenshot, der den erweiterten Trigger und die Eingaben zeigt](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Im Gegensatz dazu weist die Aktion **E-Mail senden** Eingaben von früheren Aktionen im Workflow und Ausgaben auf.

   ![Screenshot, der eine erweiterte Aktion mit Ein- und Ausgaben zeigt](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Informationen, wie Sie Ihre eigenen automatisierten Workflows erstellen können, damit Sie Apps, Daten, Dienste und Systeme unabhängig vom Kontext von Automatisierungsaufgaben für Azure-Ressourcen integrieren können, finden Sie unter [Schnellstart: Erstellen Ihres ersten Integrationsworkflows mit Azure Logic Apps – Azure-Portal](quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Bearbeiten der Aufgabe

Zum Ändern einer Aufgabe stehen Ihnen folgende Optionen zur Verfügung:

* [Bearbeiten der Aufgabe „inline“](#edit-task-inline), damit Sie die Eigenschaften der Aufgabe ändern können, z. B. Verbindungs- oder Konfigurationsinformationen, beispielsweise Ihre E-Mail-Adresse.

* [Bearbeiten des der Aufgabe zugrunde liegenden Workflows](#edit-task-workflow) im Workflow-Designer

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Bearbeiten der Aufgabe inline

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) die Ressource, die die Aufgabe besitzt, die Sie aktualisieren möchten.

1. Scrollen Sie im Navigationsmenü zum Abschnitt **Automatisierung**, und wählen Sie **Aufgaben (Vorschau)** aus.

1. Suchen Sie in der Aufgabenliste die Aufgabe, die Sie aktualisieren möchten. Öffnen Sie das Menü der Aktion mit den Auslassungspunkten ( **...** ), und wählen Sie **Inline bearbeiten** aus.

   ![Screenshot, der das geöffnete Menü mit Auslassungspunkten und die ausgewählte Option „Inline bearbeiten“ zeigt](./media/create-automation-tasks-azure-resources/edit-task-in-line.png)

   Standardmäßig wird die Registerkarte **Authentifizierung** angezeigt, auf der die vorhandenen Verbindungen angezeigt werden.

1. Um neue Anmeldeinformationen für die Authentifizierung hinzuzufügen oder andere vorhandene Anmeldeinformationen für die Authentifizierung einer Verbindung auswählen möchten, öffnen Sie das Menü mit den Auslassungspunkten ( **...** ) der Verbindung, und wählen Sie entweder **Neue Verbindung hinzufügen** oder, falls verfügbar, andere Anmeldeinformationen für die Authentifizierung aus.

   ![Screenshot, der die Registerkarte „Authentifizierung“, vorhandene Verbindungen und das ausgewählte Menü mit Auslassungspunkten zeigt](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Um andere Aufgabeneigenschaften zu aktualisieren, wählen Sie **Weiter: Konfigurieren** aus.

   Für die Aufgabe in diesem Beispiel ist die einzige Eigenschaft, die für die Bearbeitung verfügbar ist, die E-Mail-Adresse.

   ![Screenshot, der die Registerkarte „Konfigurieren“ zeigt](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Bearbeiten des der Aufgabe zugrunde liegenden Workflows

Wenn Sie den zugrunde liegenden Workflow für eine Automatisierungsaufgabe ändern, wirken sich Ihre Änderungen nur auf die von Ihnen erstellte Aufgabeninstanz aus, nicht auf die Vorlage, die die Aufgabe erstellt. Nachdem Sie Ihre Änderungen vorgenommen und gespeichert haben, beschreibt der Name, den Sie der ursprünglichen Aufgabe gegeben haben, die Aufgabe möglicherweise nicht mehr ausreichend, weshalb Sie die Aufgabe eventuell mit einem anderen Namen neu erstellen müssen.

> [!TIP]
> Als bewährte Methode empfiehlt es sich, den zugrunde liegenden Workflow zu klonen, sodass Sie stattdessen die kopierte Version bearbeiten können. Auf diese Weise können Sie Ihre Änderungen an der Kopie vornehmen und testen, während die ursprüngliche Automatisierungsaufgabe weiterhin funktioniert und ausgeführt wird, ohne dass Sie eine Störung oder Unterbrechung vorhandener Funktionen riskieren. Nachdem Sie Ihre Änderungen abgeschlossen haben und zufrieden sind, dass die neue Version erfolgreich ausgeführt wird, können Sie die ursprüngliche Automatisierungsaufgabe deaktivieren oder löschen und die geklonte Version als Automatisierungsaufgabe verwenden. Die folgenden Schritte enthalten Informationen zur Vorgehensweise beim Klonen Ihres Workflows.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) die Ressource, die die Aufgabe besitzt, die Sie aktualisieren möchten.

1. Scrollen Sie im Navigationsmenü zum Abschnitt **Automatisierung**, und wählen Sie **Aufgaben** aus.

1. Suchen Sie in der Aufgabenliste die Aufgabe, die Sie aktualisieren möchten. Öffnen Sie das Menü der Aufgabe mit den Auslassungspunkten ( **...** ), und wählen Sie **In Logic Apps öffnen** aus.

   ![Screenshot, der das geöffnete Menü mit Auslassungspunkten und die ausgewählte Option „In Logic Apps öffnen“ zeigt](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Der der Aufgabe zugrunde liegende Workflow wird im Azure Logic Apps-Dienst geöffnet und zeigt den Bereich **Übersicht** an, in dem Sie denselben Ausführungsverlauf anzeigen können, der für die Aufgabe verfügbar ist.

   ![Screenshot, der die Aufgabe in der Azure Logic Apps-Ansicht mit ausgewähltem Bereich „Übersicht“ zeigt](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Um den zugrunde liegenden Workflow im Designer zu öffnen, wählen Sie im Navigationsmenü der Logik-App **Logik-App-Designer** aus.

   ![Screenshot, der die ausgewählte Menüoption „Logik-App-Designer“ sowie die Designeroberfläche mit dem zugrunde liegenden Workflow zeigt](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Sie können nun die Eigenschaften für den Trigger und die Aktionen des Workflows bearbeiten sowie den Trigger und die Aktionen bearbeiten, die den Workflow selbst definieren. Als bewährte Methode empfiehlt es sich jedoch, die Schritte zum Klonen Ihres Workflows durchzuführen, damit Sie Ihre Änderungen an einer Kopie vornehmen können, während der ursprüngliche Workflow weiterhin funktioniert und ausgeführt wird.

1. Führen Sie die folgenden Schritte aus, um Ihren Workflow zu klonen und stattdessen die kopierte Version zu bearbeiten:

   1. Wählen Sie im Menü „Workflow“ der Logik-App **Übersicht** aus.

   1. Wählen Sie in der Symbolleiste des Bereichs „Übersicht“ die Option **Klonen** aus.

   1. Geben Sie im Erstellungsbereich der Logik-App unter **Name** einen neuen Namen für Ihren kopierten Logik-App-Workflow ein.

      Mit Ausnahme von **Logik-App-Status** sind die anderen Eigenschaften nicht zur Bearbeitung verfügbar.

   1. Wählen Sie unter **Logik-App-Status** die Option **Deaktiviert** aus, damit der geklonte Workflow nicht ausgeführt wird, während Sie Ihre Änderungen vornehmen. Sie können den Workflow aktivieren, wenn Sie bereit sind, um Ihre Änderungen zu testen.

   1. Nachdem Azure die Bereitstellung Ihres geklonten Workflows abgeschlossen hat, suchen und öffnen Sie den Workflow im Designer.

1. Um die Eigenschaften für den Trigger oder eine Aktion anzuzeigen, erweitern Sie diesen Trigger oder die Aktion.

   Beispielsweise können Sie den Wiederholungstrigger so ändern, dass er statt monatlich wöchentlich ausgeführt wird.

   ![Screenshot, der den erweiterten Wiederholungstrigger mit geöffneter Liste „Häufigkeit“ zum Anzeigen der verfügbaren Häufigkeitsoptionen zeigt](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Weitere Informationen zum Wiederholungstrigger finden Sie unter [Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Wiederholungstrigger](../connectors/connectors-native-recurrence.md). Weitere Informationen zu anderen Triggern und Aktionen, die Sie verwenden können, finden Sie unter [Connectors für Azure Logic Apps](../connectors/apis-list.md).

1. Um Ihre Änderungen zu speichern, wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

   ![Screenshot, der die Symbolleiste des Designers und den ausgewählten Befehl „Speichern“ zeigt](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Um den aktualisierten Workflow zu testen und auszuführen, wählen Sie auf der Symbolleiste des Designers **Ausführen** aus.

   Nach Abschluss der Ausführung werden im Designer Ausführungsdetails des Workflows angezeigt.

   ![Screenshot, der die Ausführungsdetails im Designer zeigt](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Um den Workflow zu deaktivieren, damit die Aufgabe nicht weiter ausgeführt wird, finden Sie unter [Verwalten von Logik-Apps im Azure-Portal](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Senden von Feedback

Wir freuen uns darauf, von Ihnen zu hören. Um Fehler zu melden, Feedback zu geben oder Fragen zu dieser Vorschaufunktion zu stellen, [wenden Sie sich an das Azure Logic Apps-Team](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Logik-Apps im Azure-Portal](manage-logic-apps-with-azure-portal.md)
