---
title: Schützen von Azure AD B2C-Daten mit Azure Sentinel
titleSuffix: Azure AD B2C
description: 'Tutorial: Sicherheitsanalysen für Azure Active Directory B2C-Daten mit Azure Sentinel ausführen'
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: be45eaa692b0b8235541c798cf82ca26b14b9f3f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691536"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-azure-sentinel"></a>Tutorial: Sicherheitsanalysen für Azure Active Directory B2C-Daten mit Azure Sentinel konfigurieren

Sie können Ihre Azure Active Directory B2C-Umgebung weiter schützen, indem Sie die Routing-Protokolle und Überwachungsinformationen an Azure Sentinel weiterleiten. Azure Sentinel ist eine cloudnative Lösung für das Security Information Event Management (SIEM) und die Sicherheitsorchestrierung mit automatisierter Reaktion (Security Orchestration Automated Response, SOAR). Azure Sentinel bietet Lösungen für die Warnungserkennung, Einblicke in Bedrohungen, proaktives Jagen und die Reaktion auf Bedrohungen für Azure AD B2C-Daten.

Mit Azure Sentinel mit Azure AD B2C haben Sie folgende Möglichkeiten:

- Ermitteln Sie bisher unentdeckte Bedrohungen, und minimieren Sie falsch positive Ergebnisse mithilfe der Analysefunktionen und unvergleichlichen Informationen zu Bedrohungen von Microsoft.
- Untersuchen von Bedrohungen mithilfe von künstlicher Intelligenz Suchen Sie nach verdächtigen Aktivitäten im großen Stil, und greifen Sie auf Jahre cybersicherheitsbezogener Arbeit bei Microsoft zu.
- Reagieren Sie dank der integrierten Orchestrierung und Automatisierung häufiger Aufgaben schnell auf Incidents.
- Erfüllen Sie die Sicherheits- und Compliance-Anforderungen für Ihr Unternehmen.

In diesem Tutorial erhalten Sie Informationen zu den folgenden Vorgängen:

1. [Übertragen der Azure AD B2C-Protokolle in den Arbeitsbereich „Azure Monitor Protokolle“](#configure-azure-ad-b2c-with-azure-monitor-logs-analytics)
2. [Aktivieren von Azure Sentinel in einem Log Analytics-Arbeitsbereich](#deploy-an-azure-sentinel-instance)
3. [Erstellen einer Beispielregel in Azure Sentinel, die einen Vorfall auslöst](#create-an-azure-sentinel-rule)
4. [Konfigurieren der automatisierten Antwort](#automated-response)

## <a name="configure-azure-ad-b2c-with-azure-monitor-logs-analytics"></a>Konfigurieren von Azure AD B2C mit Azure Monitor Logs Analytics

Aktivieren Sie **Diagnoseeinstellungen** in Azure AD in Ihrem Azure AD B2C Mandanten, um zu definieren, wohin Protokolle und Metriken für eine Ressource gesendet werden sollen.

Konfigurieren Sie anschließend [Azure AD B2C so, dass Protokolle an Azure Monitor gesendet werden](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor).

## <a name="deploy-an-azure-sentinel-instance"></a>Bereitstellen einer Azure Sentinel-Instanz

>[!IMPORTANT]
>Für das Abonnement, in dem sich der Azure Sentinel-Arbeitsbereich befindet, benötigen Sie die **Berechtigungen für Mitwirkende**, um Azure Sentinel zu aktivieren. Für die Ressourcengruppe, zu der der Arbeitsbereich gehört, benötigen Sie entweder Berechtigungen für Mitwirkende oder für Leser, um Azure Sentinel zu verwenden.

Nachdem Sie Ihre Azure AD B2C Instanz zum Senden von Protokollen an Azure Monitor konfiguriert haben, müssen Sie eine Azure Sentinel-Instanz aktivieren.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie das Abonnement, in dem der Log Analytics-Arbeitsbereich erstellt wird.

2. Suchen Sie nach **Azure Sentinel**, und wählen Sie diese Lösung aus.

3. Wählen Sie **Hinzufügen**.

![Bild zeigt die Suche im Azure-Portal nach Azure Sentinel](./media/azure-sentinel/azure-sentinel-add.png)

4. Wählen Sie den neuen Arbeitsbereich aus.

![Bild zeigt das Auswählen des Sentinel-Arbeitsbereichs](./media/azure-sentinel/create-new-workspace.png)

5. Wählen Sie **Azure Sentinel hinzufügen** aus.

>[!NOTE]
>Sie können [Azure Sentinel in mehr als einem Arbeitsbereich ausführen](../sentinel/quickstart-onboard.md). Die Daten sind aber für einen einzelnen Arbeitsbereich isoliert.

## <a name="create-an-azure-sentinel-rule"></a>Erstellen einer Azure Sentinel-Regel

>[!NOTE]
>Azure Sentinel bietet standardmäßig integrierte Vorlagen, mit denen Sie Regeln zur Bedrohungserkennung erstellen können, die vom Microsoft-Team von Sicherheitsexperten und Analysten entwickelt wurden. Mit den Regeln, die anhand dieser Vorlagen erstellt wurden, werden Ihre Daten automatisch nach allen verdächtigen Aktivitäten durchsucht. Derzeit sind keine nativen Azure AD B2C-Connectors verfügbar. Für das Beispiel in diesem Tutorial erstellen wir eine eigene Regel.

Nachdem Sie Azure Sentinel aktiviert haben, werden Sie benachrichtigt, wenn in Ihrem Azure AD B2C-Mandanten etwas Verdächtiges auftritt.

Für das Entdecken von Bedrohungen und anormalen Verhaltensweisen in Ihrer Umgebung können Sie [benutzerdefinierte Analyseregeln](../sentinel/tutorial-detect-threats-custom.md) erstellen. Diese Regeln suchen nach bestimmten Ereignissen oder Ereignisgruppen und warnen Sie, wenn bestimmte Schwellenwerte oder Bedingungen erreicht werden. Sie generieren dann Vorfälle für weitere Untersuchungen.

Im folgenden Beispiel wird das Szenario erläutert, in dem Sie eine Benachrichtigung erhalten, wenn jemand versucht, den Zugriff auf Ihre Umgebung zu erzwingen, dies aber nicht erfolgreich ist. Dies kann einen Brute-Force-Angriff bedeuten. Sie möchten über zwei oder mehr nicht erfolgreiche Anmeldungen innerhalb von 60 Sekunden benachrichtigt werden.

1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Analysen** aus.

2. Wählen Sie auf der Aktionsleiste die Option **+ Erstellen** und anschließend **Geplante Abfrageregel** aus. Der **Assistent für Analyseregeln** wird geöffnet.

![Bild zeigt „Geplante Abfrageregel erstellen“ ausgewählt](./media/azure-sentinel/create-scheduled-rule.png)

3. Navigieren Sie im Assistent für Analyseregeln zur Registerkarte **Allgemein**.

   | Feld | Wert |
   |:--|:--|
   |Name | Nicht erfolgreiche B2C-Anmeldungen |
   |BESCHREIBUNG | Benachrichtigung bei zwei oder mehr nicht erfolgreichen Anmeldungen innerhalb von 60 Sekunden |
   | Taktik | Wählen Sie aus den Angriffskategorien, nach denen die Regel klassifiziert werden kann. Diese Kategorien basieren auf den Taktiken des [MITRE ATT&CK](https://attack.mitre.org/)-Frameworks.<BR>In unserem Beispiel wählen wir `PreAttack` aus. <BR> MITRE ATTCK® ist eine global zugängliche Wissensdatenbank zu den Taktiken und Techniken von Angreifern, die auf den Beobachtungen aus der realen Welt basieren. Die ATT&CK-Wissensdatenbank wird als Grundlage für die Entwicklung spezifischer Bedrohungsmodelle und -methoden verwendet.
   | severity | Gegebenenfalls |
   | Status | Beim Erstellen der Regel wird der Status standardmäßig auf `Enabled` eingestellt. Dies bedeutet, dass die Regel nach der Erstellung sofort ausgeführt wird. Wenn Sie die Regel nicht sofort ausführen möchten, wählen Sie `Disabled` aus. Die Regel wird der Registerkarte Aktive Regeln hinzugefügt, von wo Sie diese bei Bedarf aktivieren können.|

![Bild: Bereitstellen grundlegender Regeleigenschaften](./media/azure-sentinel/create-new-rule.png)

4.  Auf der Registerkarte **Regellogik festlegen** schreiben wir eine Abfrage direkt in das Feld **Regelabfrage**, um die Regelabfragelogik zu definieren und Einstellungen zu konfigurieren. Diese Abfrage warnt Sie, wenn innerhalb von 60 Sekunden zwei oder mehr nicht erfolgreiche Anmeldungen bei Ihrem Azure AD B2C-Mandanten durchgeführt werden. Sie sind nach `UserPrincipalName` organisiert

![Bild zeigt: Geben Sie die Regelabfrage auf der Registerkarte „Logik“ ein.](./media/azure-sentinel/rule-query.png)

Legen Sie im Abschnitt Abfrageplanung die folgenden Parameter fest:

![Bild: Festlegen von Abfrageplanungsparametern](./media/azure-sentinel/query-scheduling.png)

5. Wählen Sie **Weiter: Incidenteinstellungen (Vorschau)** aus. Das Konfigurieren und Hinzufügen der automatisierten Antwort erfolgt später.

6. Navigieren Sie zur Registerkarte **Überprüfen und erstellen**, um alle Einstellungen für die neue Warnungsregel zu überprüfen. Nachdem die Meldung **Überprüfung erfolgreich** angezeigt wurde, wählen Sie **Erstellen** aus, um die Warnungsregel zu initialisieren.

![Bild: Überprüfen und Erstellen einer Regel](./media/azure-sentinel/review-create.png)

7. Zeigen Sie die Regel und die von ihr generierten Vorfälle an. Die neu erstellte benutzerdefinierte Regel vom Typ **Geplant** finden Sie in der Tabelle auf der Registerkarte **Aktive Regeln** auf dem Hauptbildschirm von **Analysen**. Über diese Liste können Sie die einzelnen Regeln **Bearbeiten**, **Aktivieren**, **Deaktivieren** oder **Löschen**.

![Bild: Der Analysebildschirm, der die Optionen zum Bearbeiten, Aktivieren, Deaktivieren oder Löschen von Regeln zeigt](./media/azure-sentinel/rule-crud.png)

8. Zeigen Sie die Ergebnisse Ihrer neuen Azure AD B2C-Regel für nicht erfolgreiche Anmeldungen an. Navigieren Sie zur Seite **Vorfälle**. Hier können Sie Bedrohungen eingrenzen, untersuchen und beseitigen. Ein Vorfall kann mehrere Warnungen enthalten. Es ist eine Aggregation aller relevanten Beweise für eine bestimmte Untersuchung. Sie können Eigenschaften wie Schweregrad und Status auf einer Vorfallebene festlegen.

>[!Note]
>Ein wichtiges Feature von Azure Sentinel ist die [Untersuchung von Vorfällen](../sentinel/tutorial-investigate-cases.md).

9. Wählen Sie einen bestimmten Vorfall aus, um mit der Untersuchung zu beginnen. Rechts sehen Sie ausführliche Informationen zum Vorfall, einschließlich des Schweregrads, der betroffenen Entitäten, der ursprünglichen Ereignisse, die diesen Vorfall ausgelöst haben sowie der eindeutigen ID des Vorfalls.

![Bild-Alt-Text=„Vorfallbildschirm“](./media/azure-sentinel/select-incident.png)

10. Wählen Sie auf der Vorfallsseite **Alle Informationen anzeigen** aus. Hier können Sie die relevanten Registerkarten überprüfen, in denen die Vorfallsinformationen zusammengefasst sind, und weitere Details einsehen.

![Bild: Regel 73](./media/azure-sentinel/full-details.png)

11. Wählen Sie **Beweise** > **Ereignisse** > **Mit Log Analytics verknüpfen** aus. Die Ergebnisse zeigen den`UserPrincipalName` der Identität, die versucht sich anzumelden, und die Anzahl der Versuche an.

![Bild: Details des ausgewählten Vorfalls](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>Automatisierte Antwort

Azure Sentinel bietet eine [stabile SOAR-Funktion](../sentinel/automation-in-azure-sentinel.md). Automatisierte Aktionen, die in Azure Sentinel als Playbook bezeichnet werden, können an Analytics-Regeln angefügt werden, um Ihre Anforderungen zu erfüllen.

In diesem Beispiel fügen wir eine E-Mail-Benachrichtigung bei einem Vorfall hinzu, der von der Regel erstellt wurde. Wir verwenden ein [vorhandenes Playbook aus dem Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification), um diese Aufgabe zu erfüllen. Sobald das Playbook konfiguriert ist, bearbeiten Sie die vorhandene Regel und wählen Sie das Playbook auf der Registerkarte Automatisierung aus.

![Bild: Der Konfigurationsbildschirm für die automatisierte Antwort, die einer Regel zugeordnet ist](./media/azure-sentinel/automation-tab.png)

## <a name="next-steps"></a>Nächste Schritte

- [Behandeln falsch positiver Ergebnisse in Azure Sentinel](../sentinel/false-positives.md)

- [Beispielarbeitsmappen](https://github.com/azure-ad-b2c/siem#workbooks)

- [Dokumentation zu Azure Sentinel](../sentinel/index.yml)
