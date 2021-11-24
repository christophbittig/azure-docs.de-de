---
title: Konfigurieren der Richtlinie zum automatischen Herunterfahren für Labs und virtuelle Computer
description: Erfahren Sie, wie Sie Zeitpläne und Richtlinien für das automatische Herunterfahren für Azure DevTest Labs bzw. für einzelne virtuelle Computer (VMs) festlegen, um die VMs zu einem bestimmten Zeitpunkt täglich herunterfahren zu können.
ms.topic: how-to
ms.date: 11/01/2021
ms.openlocfilehash: 622d9c2da013ad9eb8c3a0eef46a21999f54ee76
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286733"
---
# <a name="configure-auto-shutdown-for-labs-and-vms-in-devtest-labs"></a>Konfigurieren des automatischen Herunterfahrens für Labs und VMs in DevTest Labs

Als Lab-Besitzer von Azure DevTest Labs können Sie einen Zeitplan zum Herunterfahren aller virtuellen Computer (VMs) in Ihrem Lab für eine bestimmte Tages- oder Nachtzeit konfigurieren. Sie sparen die Kosten für die Ausführung von Computern, die nicht verwendet werden.

Sie können auch eine zentrale Richtlinie zum automatischen Herunterfahren festlegen, um zu steuern, ob Labbenutzer das automatische Herunterfahren für ihre eigenen einzelnen virtuellen Computer planen können. Die Richtlinien für das automatische Herunterfahren reichen von der Ermächtigung für VM-Besitzer, die Zeitpläne für das Herunterfahren ihrer VMs vollständig zu kontrollieren, bis hin zu der Regelung, dass sie keine Kontrolle über die Zeitpläne haben.

In diesem Artikel wird erläutert, wie Sie Zeitpläne für das automatische Herunterfahren für DevTest Labs-Labs und für einzelne Lab-VMs festlegen. In diesem Artikel wird auch beschrieben, wie Sie die Richtlinie zum automatischen Herunterfahren des Labs festlegen und Benachrichtigungen über das automatische Herunterfahren konfigurieren.

## <a name="configure-lab-auto-shutdown-schedule"></a>Konfigurieren des Zeitplans für das automatische Herunterfahren des Labs

Das automatische Herunterfahren trägt zur Minimierung von Lab-Verschwendung bei, indem alle virtuellen Computer eines Labs zu einer bestimmten Tages- oder Nachtzeit heruntergefahren werden. Führen Sie zum Anzeigen oder Ändern des Zeitplans für das automatische Herunterfahren eines Labs die folgenden Schritte aus:

1. Wählen Sie auf der Startseite Ihres Labs **Konfiguration und Richtlinien** aus.
1. Wählen Sie im linken Menü im Abschnitt **Zeitpläne** die Option **Automatisches Herunterfahren** aus.
1. Wählen Sie auf dem Bildschirm **Autoabschaltung** für **Aktiviert** die Option **Ein**, um die automatische Abschaltung zu aktivieren, oder **Aus**, um sie zu deaktivieren.
1. Für **Geplantes Herunterfahren** und **Zeitzone**, geben Sie, wenn Sie das automatische Herunterfahren aktiviert haben, die Zeit und die Zeitzone an, um alle Labor-VMs herunterzufahren.
1. Wählen Sie für **Benachrichtigung vor automatischer Abschaltung senden?** die Option **Ja** oder **Nein**, um eine Benachrichtigung 30 Minuten vor der angegebenen automatischen Abschaltzeit zu senden. Wenn Sie **Ja** wählen, geben Sie einen Webhook-URL-Endpunkt unter **Webhook-URL** oder durch Semikolon getrennte E-Mail-Adressen unter **E-Mail-Adresse** ein, an welche die Benachrichtigung gepostet oder gesendet werden soll. Weitere Informationen finden Sie im Abschnitt [Benachrichtigungen über automatisches Herunterfahren](#auto-shutdown-notifications).
1. Wählen Sie **Speichern** aus.

   ![Screenshot: Festlegen von Details zum automatischen Herunterfahren für ein Lab.](media/devtest-lab-auto-shutdown/auto-shutdown.png)

Standardmäßig gilt dieser Zeitplan für alle virtuellen Computer im Lab. Um diese Einstellung von einem bestimmten virtuellen Computer zu entfernen, öffnen Sie den Verwaltungsbereich des virtuellen Computers, und ändern Sie die Einstellung **Automatisch herunterfahren**.

> [!NOTE]
> Wenn Sie den Zeitplan für das automatische Herunterfahren für Ihr Lab oder einen virtuellen Computer innerhalb von 30 Minuten nach dem zuvor geplanten Herunterfahren aktualisieren, wird die neue Zeit zum Herunterfahren am nächsten Tag wirksam.

## <a name="configure-lab-auto-shutdown-policy"></a>Konfigurieren Sie die Richtlinien zum automatischen Herunterfahren des Labors

Als Lab-Besitzer können Sie Kosten steuern und Verschwendung in Ihren Labs minimieren, indem Sie Richtlinieneinstellungen für das automatische Herunterfahren für Ihr Lab anpassen. Informationen zum Festlegen aller Richtlinien finden Sie unter [Definieren von Labrichtlinien in Azure DevTest Labs](devtest-lab-set-lab-policy.md).

> [!IMPORTANT]
> Änderungen an der Richtlinie für das Herunterfahren gelten nur für neu im Lab erstellte VMs und nicht für bereits vorhandene VMs.

1. Wählen Sie auf der Startseite Ihres Labs **Konfiguration und Richtlinien** aus.

1. Wählen Sie im linken Menü im Abschnitt **Zeitpläne** die Option **Richtlinien zum automatischen Herunterfahren** aus.

1. Wählen Sie eine der Optionen aus.

   ![Screenshot: Richtlinienoptionen für automatisches Herunterfahren.](./media/devtest-lab-auto-shutdown/policy-options.png)

   - **Benutzer legt einen Zeitplan fest und kann sich abmelden**: Laborbenutzer können den Laborplan außer Kraft setzen oder abbestellen. Diese Option gewährt VM-Besitzern volle Kontrolle über das Festlegen der Zeitpläne für das automatische Herunterfahren ihrer VMs.

   - **Der Benutzer legt einen Zeitplan fest und kann nicht deaktivieren**: Lab-Benutzer können den Labzeitplan außer Kraft setzen, aber sie können die Richtlinie zum automatischen Herunterfahren nicht deaktivieren. Mit dieser Option wird sichergestellt, dass für jede Lab-VM ein Zeitplan für das automatische Herunterfahren in Kraft ist. VM-Besitzer können die Zeitplanung aktualisieren und Benachrichtigungen über das Herunterfahren einrichten.

   - **Der Benutzer hat keine Kontrolle über den vom Labadministrator festgelegten Zeitplan**: Labbenutzer können den Zeitplan für das automatische Herunterfahren des Labs nicht ändern oder deaktivieren. Mit dieser Option erhält der Labadministrator die vollständige Kontrolle über den Zeitplan für alle Lab-VMs. Labbenutzer können nur Benachrichtigungen zum automatischen Herunterfahren für ihre virtuellen Computer einrichten.

1. Wählen Sie **Speichern** aus.

## <a name="configure-vm-auto-shutdown-settings"></a>Konfigurieren Sie die Einstellungen für das automatische Herunterfahren des VM

Abhängig von der Richtlinie zum automatischen Herunterfahren können Sie auch einen Zeitplan für das automatische Herunterfahren für einzelne Lab-VMs festlegen.

1. Wählen Sie auf der Homepage der VM im Abschnitt **Betrieb** im linken Menü die Option **Automatisches Herunterfahren** aus.
1. Wählen Sie auf dem Bildschirm **Autoabschaltung** für **Aktiviert** die Option **Ein**, um die automatische Abschaltung zu aktivieren, oder **Aus**, um sie zu deaktivieren.
1. Für **Geplantes Herunterfahren** und **Zeitzone**, geben Sie, wenn Sie das automatische Herunterfahren aktiviert haben, die Zeit und die Zeitzone an, um alle Labor-VMs herunterzufahren.
1. Wählen Sie für **Benachrichtigung vor automatischer Abschaltung senden?** die Option **Ja** oder **Nein**, um eine Benachrichtigung 30 Minuten vor der angegebenen automatischen Abschaltzeit zu senden. Wenn Sie **Ja** wählen, geben Sie einen Webhook-URL-Endpunkt unter **Webhook-URL** oder eine E-Mail-Adresse unter **E-Mail-Adresse** ein, an welche die Benachrichtigung gepostet bzw. gesendet werden soll. Weitere Informationen finden Sie im Abschnitt [Benachrichtigungen über automatisches Herunterfahren](#auto-shutdown-notifications).
1. Wählen Sie **Speichern** aus.

   ![Screenshot: Festlegen von Details zum automatischen Herunterfahren für einen VM.](media/devtest-lab-auto-shutdown/compute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Anzeigen von Aktivitätsprotokollen für Aktualisierungen der Einstellung zum automatischen Herunterfahren

Wenn Sie die Einstellung zum automatischen Herunterfahren aktualisieren, wird die Aktivität im Aktivitätsprotokoll des VM aufgezeichnet.

1. Wählen Sie auf der Startseite des VM im linken Menü die Option **Aktivitätsprotokoll** aus.
1. Entfernen Sie **den Ressourcenfilter**, wenden Sie den entsprechenden **Ressourcengruppenfilter** an, und zeigen Sie die Einträge für **Zeitpläne Hinzufügen oder Ändern** an.

   ![Screenshot: Hinzufügen oder Ändern von Zeitplänen im Aktivitätsprotokoll.](media/devtest-lab-auto-shutdown/activity-log-entry.png)

1. Wählen Sie den Vorgang **Zeitpläne Hinzufügen oder Ändern** aus, um eine Zusammenfassungsseite zu öffnen, auf der weitere Details zum Vorgang angezeigt werden.

## <a name="auto-shutdown-notifications"></a>Benachrichtigungen zum automatischen Herunterfahren

Wenn Sie Benachrichtigungen in der Konfiguration für das automatische Herunterfahren aktivieren, erhalten Labbenutzer 30 Minuten vor dem automatischen Herunterfahren eine Benachrichtigung, wenn einer ihrer VMs betroffen ist. Mit dieser Option erhalten die Labbenutzer die Möglichkeit, ihre Arbeit vor dem Herunterfahren zu speichern. Wenn in den Einstellungen für das automatische Herunterfahren eine E-Mail-Adresse angegeben ist, wird die Benachrichtigung an diese E-Mail-Adresse gesendet. Wenn in den Einstellungen ein Webhook angegeben ist, wird die Benachrichtigung an den Webhook-URL gesendet.

Die Benachrichtigung kann zudem Links enthalten, welche die folgenden Aktionen für jeden VM ermöglichen, wenn jemand weiterarbeiten muss:

- Dieses Mal das automatische Herunterfahren Überspringen.
- Aussetzen des automatischen Herunterfahrens für eine Stunde.
- Aussetzen des automatischen Herunterfahrens für 2 Stunden.

Sie können Webhooks verwenden, um Ihre eigenen Benachrichtigungen zu implementieren. Sie richten Integrationen ein, die bestimmte Ereignisse abonnieren. Wenn eines dieser Ereignisse eintritt, wird eine HTTP POST-Nutzlast an die URL des Webhooks gesendet.

Apps wie [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und Slack bieten umfassende Unterstützung für Webhooks. Weitere Informationen zum Antworten auf Webhooks finden Sie unter [Azure Functions: Übersicht über HTTP-Trigger und Bindungen](../azure-functions/functions-bindings-http-webhook.md) oder [Hinzufügen eines HTTP-Triggers für Azure Logic Apps](../connectors/connectors-native-http.md#add-an-http-trigger).

Im folgenden Beispiel wird veranschaulicht, wie Sie Logic Apps verwenden, um eine Benachrichtigung zum automatischen Herunterfahren zu konfigurieren, die eine E-Mail an VM-Besitzer sendet.

### <a name="create-a-logic-app-that-sends-email-notifications"></a>Erstellen einer Logik-App, die E-Mail-Benachrichtigungen sendet

Azure Logic Apps bietet viele Connectors, die es einfach machen, einen Dienst für andere Clients wie Office 365 und Twitter zu integrieren. Auf hoher Ebene sind die Schritte zum Einrichten einer Logik-App für E-Mail-Benachrichtigungen:

1. Erstellen Sie eine Logik-App.
1. Konfigurieren der integrierten Vorlage
1. Integrieren in den E-Mail-Client.
1. Hier finden Sie den Webhook-URL, der in den Benachrichtigungseinstellungen für das automatische Herunterfahren verwendet werden soll.

Erstellen Sie zum Einstieg eine Logik-App in Azure mit den folgenden Schritten:

1. Geben Sie im Azure-Portal im oberen Suchfeld *Logik-Apps* ein, und wählen Sie dann **Logik-Apps** aus.

1. Wählen Sie auf der Seite **Logik-Apps** die Option **Hinzufügen** aus.

1. Auf der Seite **Logik-App erstellen**:

   - Wählen Sie Ihr Azure-**Abonnement** aus.
   - Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue.
   - Geben Sie einen **Logik-App-Namen** ein.
   - Wählen Sie die **Region** für die Logik-App aus.

   ![Screenshot, der die Seite Logik-App Erstellen zeigt.](media/devtest-lab-auto-shutdown/new-logic-app-page.png)

1. Klicken Sie auf **Überprüfen + erstellen** und nach der Überprüfung auf **Erstellen**.

1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressource wechseln**.

Konfigurieren der integrierten Vorlage.

1. Wählen Sie auf der Seite Logik-App die Option **Logik-App Designer** unter **Bereitstellungstools** im linken Navigationsbereich aus.

1. Wählen Sie im oberen Menü **Vorlagen** aus.

1. Wählen Sie unter **Vorlagen** die Option **HTTP Anforderung/Antwort** aus.

   ![Screenshot: Vorlage "HTTP-Anforderungsantwort".](media/devtest-lab-auto-shutdown/select-http-request-response-option.png)

1. Wählen Sie auf der Seite **HTTP-Anforderung/-Antwort** die Option **Diese Vorlage verwenden** aus.

   ![Screenshot, der die Auswahl von Diese Vorlage Verwenden zeigt.](./media/devtest-lab-auto-shutdown/select-use-this-template.png)

1. Kopieren Sie den folgenden JSON-Code in den Abschnitt **JSON-Schema für Anforderungstext**.

   ![Screenshot: JSON-Schema für Anforderungstext.](media/devtest-lab-auto-shutdown/request-json.png)

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

Integrieren in den E-Mail-Client.

1. Wählen Sie im Designer **Neuer Schritt** aus.

   ![Screenshot: Neuer Schritt im Designer.](media/devtest-lab-auto-shutdown/new-step.png)

1. Geben Sie auf der Seite **Vorgang wählen** in das Suchfeld *Office 365 Outlook - E-Mail senden* ein und wählen Sie dann **E-Mail senden (V2)** aus **Aktionen**.

   ![Screenshot: Option E-Mail Senden V2.](media/devtest-lab-auto-shutdown/select-send-email.png)

1. Füllen Sie im Formular **E-Mail senden (V2)** die Felder **An**, **Betreff** und **Text** aus.

   Wählen Sie **Dynamischen Inhalt hinzufügen** aus, um die Benachrichtigung automatisch mit Werten zu füllen, die von der App und den Connectors verwendet werden. Wählen Sie beispielsweise für **An** die Option **Besitzer aus.** Füllen Sie **Subject** mit **vmName und** **labName** auf. Fügen Sie dem Nachrichtentext Inhalte wie zum Beispiel die Werte **skipUrl** und **delayUrl** s hinzu.

   ![Screenshot mit einem Beispiel für eine Benachrichtigungs-E-Mail.](media/devtest-lab-auto-shutdown/email-options.png)

1. Wählen Sie auf der Symbolleiste **Speichern** aus.

Nun können Sie die Webhook-URL kopieren. Wählen Sie den Schritt **Wenn eine HTTP-Anforderung empfangen wird** und wählen Sie dann die Schaltfläche Kopieren, um die HTTP POST URL in die Zwischenablage zu kopieren. Fügen Sie diesen Webhook-URL in die Benachrichtigungseinstellungen für das automatische Herunterfahren ein.

![Screenshot, der das Kopieren des Webhook-URL zeigt.](media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Nächste Schritte

- [Automatisches Starten virtueller Labcomputer](devtest-lab-auto-startup-vm.md)
- [Definieren von Labrichtlinien in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
- [Empfangen und Beantworten eingehender HTTPS-Anforderungen in Azure Logic Apps](/azure/connectors/connectors-native-reqres)
