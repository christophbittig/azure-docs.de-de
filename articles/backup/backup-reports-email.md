---
title: Azure Backup-Berichte per E-Mail
description: Erstellen automatisierter Aufgaben zum Empfangen regelmäßiger Berichte per E-Mail
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: dd5e31663262463449df4377329edc6604ffb691
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235979"
---
# <a name="email-azure-backup-reports"></a>Azure Backup-Berichte per E-Mail

Mit dem in Sicherungsberichten verfügbaren Feature **Bericht per E-Mail senden** können Sie automatisierte Aufgaben zum Empfangen regelmäßiger Berichte per E-Mail erstellen. Dieses Feature stellt eine Logik-App in Ihrer Azure-Umgebung bereit, die Daten aus Ihren ausgewählten Log Analytics-Arbeitsbereichen basierend auf den von Ihnen angegebenen Eingaben abfragt. [Erfahren Sie mehr über Logik-Apps und ihre Preise](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Erste Schritte

Um E-Mail-Aufgaben über Sicherungsberichte zu konfigurieren, führen Sie die folgenden Schritte aus:

1.  Navigieren Sie zu **Backup Center** > **Sicherungsberichte**, und klicken Sie auf die Registerkarte **Bericht per E-Mail senden**.
2.  Erstellen Sie eine Aufgabe, indem Sie die folgenden Informationen angeben:
    * **Aufgabendetails**: Der Name der zu erstellenden Logik-App und das Abonnement, die Ressourcengruppe und der Speicherort, in dem die Aufgabe erstellt werden soll. Beachten Sie, dass die Logik-App Daten über mehrere Abonnements, Ressourcengruppen und Speicherorte hinweg abfragen kann (wie im Abschnitt „Berichtsfilter“ ausgewählt), aber im Kontext eines einzelnen Abonnements, einer einzelnen Ressourcengruppe und eines einzelnen Speicherorts erstellt wird.
    * **Zu exportierende Daten**: Die Registerkarte, die Sie exportieren möchten. Sie können entweder eine einzelne Aufgaben-App pro Registerkarte erstellen oder alle Registerkarten mithilfe einer einzelnen Aufgabe per E-Mail senden, indem Sie die Option **Alle Registerkarten** auswählen.
    * **E-Mail-Optionen**: E-Mail-Häufigkeit, Empfänger-E-Mail-IDs und E-Mail-Betreff.

    ![Registerkarte „E-Mail“](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Wenn Sie auf **Senden** und **Bestätigen** klicken, wird die Logik-App erstellt. Die Logik-App und die zugehörigen API-Verbindungen werden mit dem Tag **UsedByBackupReports: true** erstellt, damit sie leichter erkennbar sind. Damit die Logik-App erfolgreich ausgeführt wird, müssen Sie einen einmaligen Autorisierungsschritt ausführen, wie im folgenden Abschnitt beschrieben.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Autorisieren von Verbindungen mit Azure Monitor-Protokollen und Office 365

Die Logik-App verwendet den [azuremonitorlogs](/connectors/azuremonitorlogs/)-Connector zum Abfragen der LA-Arbeitsbereiche und den [Office 365 Outlook](/connectors/office365connector/)-Connector zum Senden von E-Mails. Sie müssen eine einmalige Autorisierung für diese beiden Connectors durchführen. 
 
Führen Sie die folgenden Schritte aus, um die Autorisierung durchzuführen:

1.  Navigieren Sie zu **Logik-Apps** im Azure-Portal.
2.  Suchen Sie nach dem Namen der Logik-App, die Sie erstellt haben, und navigieren Sie zu der Ressource.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Klicken Sie auf das Menüelement **API-Verbindungen**.

    ![API-Verbindungen](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Es werden zwei Verbindungen mit dem Format `<location>-azuremonitorlogs` und `<location>-office365` angezeigt, d. h. _eastus-azuremonitorlogs_ und _eastus-office365_.
5.  Navigieren Sie zu jeder dieser Verbindungen, und wählen Sie das Menüelement **API-Verbindung bearbeiten** aus. Wählen Sie auf dem angezeigten Bildschirm **Autorisieren** aus, und speichern Sie die Verbindung nach Abschluss der Autorisierung.

    ![Autorisieren der Verbindung](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Um zu testen, ob die Logik-App nach der Autorisierung funktioniert, können Sie zur Logik-App zurück navigieren, die **Übersicht** öffnen und im oberen Bereich **Trigger ausführen** auswählen, um zu testen, ob erfolgreich eine E-Mail generiert wurde.

## <a name="contents-of-the-email"></a>Inhalte der E-Mail

* Alle Diagramme und Graphen, die im Portal angezeigt werden, sind als Inlineinhalt in der E-Mail verfügbar. [Erfahren Sie mehr](configure-reports.md) über die in Sicherungsberichten enthaltenen Informationen.
* Die im Portal angezeigten Raster sind als CSV-Anlagen in der E-Mail verfügbar.
* Für alle in der E-Mail angezeigten Daten werden Filter auf Berichtsebene verwendet, die zum Zeitpunkt der Erstellung der E-Mail-Aufgabe vom Benutzer im Bericht ausgewählt wurden.
* Filter auf Registerkartenebene wie **Name der Sicherungsinstanz**, **Richtlinienname** usw. werden nicht angewendet. Die einzige Ausnahme hierbei ist das Raster **Aufbewahrungsoptimierungen** auf der Registerkarte **Optimieren**, wo die Filter für die **Tägliche**, **Wöchentliche**, **Monatliche** und **Jährliche** RP-Aufbewahrung angewendet werden.
* Der Zeitbereich und der Aggregationstyp (für Diagramme) basieren auf der Zeitbereichsauswahl des Benutzers in den Berichten. Wenn die Zeitbereichsauswahl z. B. die letzten 60 Tage sind (übersetzt in den wöchentlichen Aggregationstyp) und die E-Mail-Häufigkeit „Täglich“ ist, erhält der Empfänger jeden Tag eine E-Mail mit Diagrammen, die über den letzten 60-Tage-Zeitraum erfasste Daten umfassen, wobei Daten auf wöchentlicher Ebene aggregiert werden.

## <a name="troubleshooting-issues"></a>Fragen zur Problembehandlung

Wenn Sie auch nach erfolgreicher Bereitstellung der Logik-App nicht wie erwartet E-Mail-Nachrichten erhalten, können Sie die folgenden Schritte zur Problembehandlung bei der Konfiguration anwenden:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Szenario 1: Weder erfolgreicher Empfang einer E-Mail noch einer Fehlermeldungs-E-Mail

* Dieses Problem kann auftreten, weil der Outlook-API-Connector nicht autorisiert ist. Befolgen Sie die oben aufgeführten Autorisierungsschritte, um die Verbindung zu autorisieren.

* Dieses Problem könnte auch auftreten, wenn Sie beim Erstellen der Logik-App einen falschen E-Mail-Empfänger angegeben haben. Um zu überprüfen, ob der E-Mail-Empfänger ordnungsgemäß angegeben wurde, können Sie im Azure-Portal zur Logik-App navigieren, den Logik-App-Designer öffnen und den E-Mail-Schritt auswählen, um festzustellen, ob die richtigen E-Mail-IDs verwendet wurden.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Szenario 2: Empfang einer Fehlermeldungs-E-Mail, die besagt, dass die Logik-App nicht vollständig ausgeführt werden konnte

So beheben Sie dieses Problem
1.  Navigieren Sie im Azure-Portal zur Logik-App.
2.  Unten im Bildschirm **Übersicht** wird der Abschnitt **Ausführungsverlauf** angezeigt. Sie können die letzte Ausführung öffnen und anzeigen, bei welchen Schritten im Workflow Fehler aufgetreten sind. Mögliche Ursachen:
    * **Connector für Azure Monitor-Protokolle wurde nicht autorisiert**: Um dieses Problem zu beheben, befolgen Sie die oben aufgeführten Autorisierungsschritte.
    * **Fehler in der LA-Abfrage**: Falls Sie die Logik-App mit Ihren eigenen Abfragen angepasst haben, könnte ein Fehler in einer der LA-Abfragen zu einem Fehler der Logik-App führen. Sie können den entsprechenden Schritt auswählen und den Fehler anzeigen, der bewirkt, dass die Abfrage nicht ordnungsgemäß ausgeführt wird.

Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zu Azure Backup-Berichten](./configure-reports.md)