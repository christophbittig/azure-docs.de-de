---
title: Schützen von Azure AD B2C mit Microsoft Sentinel
titleSuffix: Azure AD B2C
description: In diesem Tutorial wird Microsoft Sentinel verwendet, um Sicherheitsanalysen für Azure Active Directory B2C-Daten durchzuführen.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5afd5535dc9bd9a6f0dce507e6279b664c90fe8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281417"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-microsoft-sentinel"></a>Tutorial: Sicherheitsanalysen für Azure Active Directory B2C-Daten mit Microsoft Sentinel konfigurieren

Sie können Ihre Azure AD B2C-Umgebung (Azure Active Directory B2C) noch besser schützen, indem Sie Protokolle und Überwachungsinformationen an Microsoft Sentinel weiterleiten. Microsoft Sentinel ist eine cloudnative Lösung für SIEM (Security Information & Event Management) und SOAR (Security Orchestration, Automation, and Response). Microsoft Sentinel bietet Lösungen für die Warnungserkennung, Einblicke in Bedrohungen, Hunting und die Reaktion auf Bedrohungen für Azure AD B2C.

Die Verwendung von Microsoft Sentinel mit Azure AD B2C ermöglicht Folgendes:

- Ermitteln Sie bisher unentdeckte Bedrohungen, und minimieren Sie falsch positive Ergebnisse mithilfe der Analysen und Threat Intelligence von Microsoft.
- Untersuchen Sie Bedrohungen mithilfe von KI. Suchen Sie im großen Stil nach verdächtigen Aktivitäten, und profitieren Sie von der jahrelangen Erfahrung von Microsoft im Bereich Cybersicherheit.
- Reagieren Sie dank der integrierten Orchestrierung und Automatisierung häufiger Aufgaben schnell auf Incidents.
- Erfüllen Sie die Sicherheits- und Compliance-Anforderungen für Ihr Unternehmen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Übertragen von Azure AD B2C-Protokollen in einen Log Analytics-Arbeitsbereich
> * Aktivieren von Microsoft Sentinel in einem Log Analytics-Arbeitsbereich
> * Erstellen einer Beispielregel in Microsoft Sentinel, durch die ein Vorfall ausgelöst wird
> * Konfigurieren einer automatisierten Reaktion

## <a name="configure-azure-ad-b2c-with-azure-monitor-log-analytics"></a>Konfigurieren von Azure AD B2C mit Azure Monitor Logs Analytics

Aktivieren Sie innerhalb Ihres Azure AD B2C-Mandanten **Diagnoseeinstellungen** in Azure AD, um zu definieren, wohin Protokolle und Metriken für eine Ressource gesendet werden sollen. Konfigurieren Sie anschließend Azure AD B2C so, dass Protokolle an Azure Monitor gesendet werden. Eine entsprechende Anleitung finden Sie [hier](./azure-monitor.md).

## <a name="deploy-a-microsoft-sentinel-instance"></a>Bereitstellen einer Microsoft Sentinel-Instanz

Nachdem Sie Ihre Azure AD B2C-Instanz zum Senden von Protokollen an Azure Monitor konfiguriert haben, müssen Sie eine Microsoft Sentinel-Instanz aktivieren.

>[!IMPORTANT]
>Für das Abonnement, in dem sich der Microsoft Sentinel-Arbeitsbereich befindet, benötigen Sie die Berechtigungen für Mitwirkende, um Microsoft Sentinel zu aktivieren. Für die Ressourcengruppe, zu der der Arbeitsbereich gehört, benötigen Sie entweder Berechtigungen für Mitwirkende oder für Leser, um Microsoft Sentinel zu verwenden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie das Abonnement aus, in dem der Log Analytics-Arbeitsbereich erstellt wird.

2. Suchen Sie nach **Microsoft Sentinel**, und wählen Sie diese Lösung aus.

   ![Screenshot: Suchen nach Microsoft Sentinel im Azure-Portal](./media/azure-sentinel/azure-sentinel-add.png)

3. Wählen Sie **Hinzufügen**.

4. Wählen Sie den neuen Arbeitsbereich aus.

   ![Screenshot: Auswählen eines Microsoft Sentinel-Arbeitsbereichs](./media/azure-sentinel/create-new-workspace.png)

5. Wählen Sie **Add Microsoft Sentinel** (Microsoft Sentinel hinzufügen) aus.

>[!NOTE]
>Sie können [Microsoft Sentinel in mehr als einem Arbeitsbereich ausführen](../sentinel/quickstart-onboard.md). Die Daten sind aber für einen einzelnen Arbeitsbereich isoliert.

## <a name="create-a-microsoft-sentinel-rule"></a>Erstellen einer Microsoft Sentinel-Regel

Nachdem Sie Microsoft Sentinel aktiviert haben, werden Sie benachrichtigt, wenn in Ihrem Azure AD B2C-Mandanten etwas Verdächtiges passiert.

Für die Erkennung von Bedrohungen und anormalen Verhaltensweisen in Ihrer Umgebung können Sie [benutzerdefinierte Analyseregeln](../sentinel/detect-threats-custom.md) erstellen. Diese Regeln ermöglichen die Suche nach bestimmten Ereignissen oder Ereignisgruppen und warnen Sie, wenn bestimmte Schwellenwerte oder Bedingungen erreicht werden. Anschließend werden Vorfälle zur weiteren Untersuchung generiert.

>[!NOTE]
>Microsoft Sentinel bietet integrierte Vorlagen, mit denen Sie Regeln zur Bedrohungserkennung erstellen können, die von Sicherheitsexperten und Analysten von Microsoft entwickelt wurden. Mit den Regeln, die anhand dieser Vorlagen erstellt wurden, werden Ihre Daten automatisch nach allen verdächtigen Aktivitäten durchsucht. Derzeit sind keine nativen Azure AD B2C-Connectors verfügbar. Für das Beispiel in diesem Tutorial erstellen wir eine eigene Regel.

Im folgenden Beispiel erhalten Sie eine Benachrichtigung, wenn jemand versucht, sich Zugang zu Ihrer Umgebung zu verschaffen, dabei aber nicht erfolgreich ist. Hierbei kann es sich um einen Brute-Force-Angriff handeln. Sie möchten benachrichtigt werden, wenn innerhalb von 60 Sekunden zwei oder mehr Anmeldungen nicht erfolgreich waren.

1. Wählen Sie in Microsoft Sentinel im Menü auf der linken Seite die Option **Analysen** aus.

2. Wählen Sie im oberen Bereich auf der Aktionsleiste **+ Erstellen** > **Geplante Abfrageregel** aus. 

    ![Screenshot: Optionen zum Erstellen einer geplanten Abfrageregel](./media/azure-sentinel/create-scheduled-rule.png)

3. Navigieren Sie im Analyseregel-Assistenten zur Registerkarte **Allgemein**, und geben Sie die folgenden Informationen ein:

    | Feld | Wert |
    |:--|:--|
    |**Name** | Geben Sie einen geeigneten Namen für nicht erfolgreiche Azure AD B2C-Anmeldungen ein. |
    |**Beschreibung** | Geben Sie eine Beschreibung ein, die besagt, dass durch die Regel eine Benachrichtigung ausgelöst wird, wenn innerhalb von 60 Sekunden zwei oder mehr Anmeldungen nicht erfolgreich sind. |
    | **Taktik** | Wählen Sie aus den Angriffskategorien, nach denen die Regel klassifiziert werden kann. Diese Kategorien basieren auf den Taktiken des [MITRE ATT&CK](https://attack.mitre.org/)-Frameworks.<BR>In diesem Beispiel wird **PreAttack** ausgewählt. <BR> MITRE ATT&CK ist eine global zugängliche Wissensdatenbank mit Taktiken und Techniken von Angreifern, die auf Beobachtungen aus der realen Welt basiert. Die Wissensdatenbank wird als Grundlage für die Entwicklung spezifischer Bedrohungsmodelle und -methoden verwendet.
    | **Schweregrad** | Wählen Sie einen geeigneten Schweregrad. |
    | **Status** | Wenn Sie die Regel erstellen, hat sie standardmäßig den Status **Aktiviert**. Dieser Status bedeutet, dass die Regel sofort nach ihrer Erstellung ausgeführt wird. Soll sie nicht sofort ausgeführt werden, wählen Sie **Deaktiviert** aus. Die Regel wird dann der Registerkarte **Aktive Regeln** hinzugefügt und kann dort bei Bedarf aktiviert werden.|

    ![Screenshot: Grundlegende Regeleigenschaften](./media/azure-sentinel/create-new-rule.png)

4. Schreiben Sie auf der Registerkarte **Regellogik festlegen** eine Abfrage direkt in das Feld **Regelabfrage**, um die Regelabfragelogik zu definieren und Einstellungen zu konfigurieren. 

    ![Screenshot: Eingeben der Regelabfrage auf der Registerkarte, um die Regellogik festzulegen](./media/azure-sentinel/rule-query.png)

    Diese Abfrage sorgt dafür, dass Sie gewarnt werden, wenn innerhalb von 60 Sekunden zwei oder mehr nicht erfolgreiche Anmeldungen bei Ihrem Azure AD B2C-Mandanten durchgeführt werden. Die Anmeldungen werden nach `UserPrincipalName` strukturiert.

5. Legen Sie im Abschnitt **Abfrageplanung** die folgenden Parameter fest:

    ![Screenshot: Festlegen von Abfrageplanungsparametern](./media/azure-sentinel/query-scheduling.png)

6. Wählen Sie **Weiter: Incidenteinstellungen (Vorschau)** aus. Die automatisierte Reaktion wird später konfiguriert und hinzugefügt.

7. Navigieren Sie zur Registerkarte **Überprüfen und erstellen**, um alle Einstellungen für die neue Warnungsregel zu überprüfen. Nachdem die Meldung **Überprüfung erfolgreich** angezeigt wurde, wählen Sie **Erstellen** aus, um die Warnungsregel zu initialisieren.

    ![Screenshot: Registerkarte zum Überprüfen und Erstellen einer Regel](./media/azure-sentinel/review-create.png)

8. Sehen Sie sich die Regel und die von ihr generierten Vorfälle an. Die neu erstellte benutzerdefinierte Regel vom Typ **Geplant** finden Sie in der Tabelle auf der Registerkarte **Aktive Regeln** auf dem Hauptbildschirm von **Analysen**. In dieser Liste können Sie Regeln mithilfe der entsprechenden Schaltflächen bearbeiten, aktivieren, deaktivieren oder löschen.

    ![Screenshot: Aktive Regeln mit Optionen zum Bearbeiten, Aktivieren, Deaktivieren und Löschen](./media/azure-sentinel/rule-crud.png)

9. Sehen Sie sich die Ergebnisse Ihrer neuen Regel für nicht erfolgreiche Azure AD B2C-Anmeldungen an. Navigieren Sie zur Seite **Vorfälle**. Hier können Sie Bedrohungen eingrenzen, untersuchen und beseitigen. 

    Ein Vorfall kann mehrere Warnungen enthalten. Es ist eine Aggregation aller relevanten Beweise für eine bestimmte Untersuchung. Sie können Eigenschaften wie Schweregrad und Status auf einer Vorfallebene festlegen.

    > [!NOTE]
    > Ein wichtiges Feature von Microsoft Sentinel ist die [Untersuchung von Vorfällen](../sentinel/investigate-cases.md).
    
10. Wählen Sie einen bestimmten Vorfall aus, um mit der Untersuchung zu beginnen. 

    Auf der rechten Seite werden ausführliche Informationen für den Vorfall angezeigt. Hierzu zählen Schweregrad und betroffene Entitäten sowie die ursprünglichen Ereignisse, durch die dieser Vorfall ausgelöst wurde, und die eindeutige ID des Vorfalls.

    ![Screenshot: Vorfallsinformationen](./media/azure-sentinel/select-incident.png)

11. Wählen Sie im Vorfallsbereich die Option **Alle Details anzeigen** aus. Überprüfen Sie die Registerkarten, auf denen die Vorfallsinformationen zusammengefasst sind, und geben Sie weitere Details an.

    ![Screenshot: Registerkarten für Vorfallsinformationen](./media/azure-sentinel/full-details.png)

12. Wählen Sie **Beweise** > **Ereignisse** > **Mit Log Analytics verknüpfen** aus. Das Ergebnis enthält den Benutzerprinzipalnamen (`UserPrincipalName`) der Identität, die versucht, sich anzumelden, sowie die Anzahl von Versuchen.

    ![Screenshot: Alle Details eines ausgewählten Vorfalls](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>Automatisierte Antwort

Microsoft Sentinel bietet eine [stabile SOAR-Funktion](../sentinel/automation-in-azure-sentinel.md). Automatisierte Aktionen (in Microsoft Sentinel als *Playbook* bezeichnet) können an Analyseregeln angefügt werden, um Ihre individuellen Anforderungen zu erfüllen.

In diesem Beispiel wird eine E-Mail-Benachrichtigung für einen durch die Regel generierten Vorfall hinzugefügt. Verwenden Sie hierzu ein [bereits vorhandenes Playbook aus dem GitHub-Repository für Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification). Bearbeiten Sie nach dem Konfigurieren des Playbooks die vorhandene Regel, und wählen Sie das Playbook auf der Registerkarte **Automatisierte Antwort** aus.

![Screenshot: Konfigurationsbildschirm für die automatisierte Antwort einer Regel](./media/azure-sentinel/automation-tab.png)

## <a name="related-information"></a>Verwandte Informationen

Weitere Informationen zu Microsoft Sentinel und Azure AD B2C finden Sie hier:

- [Beispielarbeitsmappen](https://github.com/azure-ad-b2c/siem#workbooks)

- [Dokumentation zu Microsoft Sentinel](../sentinel/index.yml)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Behandeln falsch positiver Ergebnisse in Microsoft Sentinel](../sentinel/false-positives.md)
