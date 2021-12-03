---
title: Workflowautomatisierung in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Workflows in Microsoft Defender für Cloud erstellen und automatisieren.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 8c0b1cc8a74f71b843150e482860336ed22db5a4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526142"
---
# <a name="automate-responses-to-microsoft-defender-for-cloud-triggers"></a>Automatisieren der Reaktionen auf Trigger von Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Jedes Sicherheitsprogramm umfasst mehrere Workflows für die Reaktion auf Vorfälle. Diese Prozesse können das Benachrichtigen relevanter Stakeholder, das Starten eines Change Management-Prozesses und das Anwenden spezifischer Korrekturschritte umfassen. Sicherheitsexperten empfehlen, möglichst viele Schritte dieser Verfahren zu automatisieren. Durch Automatisierung wird der Aufwand reduziert. Außerdem können Sie so die Sicherheit erhöhen, indem Sie sicherstellen, dass die Prozessschritte schnell, konsistent und gemäß Ihren vordefinierten Anforderungen ausgeführt werden.

In diesem Artikel wird das Feature für die Workflowautomatisierung von Microsoft Defender für Cloud beschrieben. Dieses Feature kann Logic Apps bei Sicherheitswarnungen, Empfehlungen und Änderungen der Einhaltung gesetzlicher Bestimmungen auslösen. Beispielsweise können Sie festlegen, dass von Defender für Cloud eine E-Mail an einen bestimmten Benutzer gesendet wird, wenn eine Warnung auftritt. Außerdem erfahren Sie, wie Sie Logik-Apps mithilfe von [Azure Logic Apps](../logic-apps/logic-apps-overview.md) erstellen.


## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Kostenlos|
|Erforderliche Rollen und Berechtigungen:|Rolle **Sicherheitsadministrator** oder **Besitzer** für die Ressourcengruppe<br>Außerdem sind Schreibberechtigungen für die Zielressource erforderlich<br><br>Damit Sie Azure Logic Apps-Workflows verwenden können, benötigen Sie zudem die folgenden Logic Apps-Rollen/-Berechtigungen:<br> Die Berechtigungen der Rolle - [Logik-App-Operator](../role-based-access-control/built-in-roles.md#logic-app-operator) oder der Lese-/Triggerzugriff für Logik-Apps sind erforderlich. (Diese Rolle kann keine Logik-Apps erstellen oder bearbeiten, sondern nur vorhandene *ausführen*.)<br> Die Berechtigungen der Rolle - [Logik-App-Mitwirkender](../role-based-access-control/built-in-roles.md#logic-app-contributor) sind für die Erstellung und Änderung von Logik-Apps erforderlich.<br>Wenn Sie Logik-App-Connectors verwenden möchten, benötigen Sie möglicherweise zusätzliche Anmeldeinformationen für die Anmeldung bei den jeweiligen Diensten (z. B. Ihren Instanzen von Outlook, Teams oder Slack).|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Erstellen einer Logik-App und Definieren des Zeitpunkts ihrer automatischen Ausführung 

1. Wählen Sie in der Randleiste von Defender für Cloud die Option **Workflowautomatisierung** aus.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Screenshot der Seite „Workflowautomatisierung“ mit der Liste der definierten Automatisierungen." lightbox="./media/workflow-automation/list-of-workflow-automations.png":::

    Auf dieser Seite können Sie neue Automatisierungsregeln erstellen sowie vorhandene aktivieren, deaktivieren oder löschen.

1. Um einen neuen Workflow zu definieren, klicken Sie auf **Workflowautomatisierung hinzufügen**. Der Bereich „Optionen“ für Ihre neue Automatisierung wird geöffnet.

    :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Bereich „Workflowautomatisierung hinzufügen“.":::

    Darin können Sie Folgendes eingeben:
    1. Einen Namen und eine Beschreibung für die Automatisierung
    1. Die Trigger zum Auslösen dieses automatischen Workflows. Sie könnten beispielsweise Ihre Logik-App ausführen, wenn eine Sicherheitswarnung generiert wird, die „SQL“ enthält.

        > [!NOTE]
        > Wenn Ihr Trigger eine Empfehlung mit „untergeordneten Empfehlungen“ ist, z. B. **Ergebnisse der Sicherheitsrisikobewertung in Ihren SQL Datenbanken müssen beseitigt werden**, wird die Logik-App nicht bei jedem neuen Sicherheitsergebnis ausgelöst, sondern nur dann, wenn sich der Status der übergeordneten Empfehlung ändert.

    1. Die Logik-App, die ausgeführt wird, wenn die Triggerbedingungen erfüllt sind 

1. Wählen Sie im Abschnitt „Aktionen“ die Option **Logic Apps-Seite besuchen** aus, um mit dem Prozess für die Logik-App-Erstellung zu beginnen.

    Sie werden zu Azure Logic Apps umgeleitet.

1. Wählen Sie **Hinzufügen**. 

    [![Erstellen einer neuen Logik-App.](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Geben Sie einen Namen, eine Ressourcengruppe und einen Standort ein, und wählen **Überprüfen und erstellen** > **Erstellen** aus.

    Die Meldung **Bereitstellung wird durchgeführt** wird angezeigt. Warten Sie, bis die Benachrichtigung „Bereitstellung abgeschlossen“ angezeigt wird, und wählen Sie darin **Zu Ressource wechseln** aus.

1. Sie können in Ihrer neuen Logik-App zwischen integrierten, vordefinierten Vorlagen der Kategorie „Sicherheit“ auswählen. Sie können aber auch einen benutzerdefinierten Ereignisflow definieren, der beim Auslösen dieses Prozesses gestartet wird.

    > [!TIP]
    > Manchmal sind in einer Logikanwendung Parameter im Connector als Teil einer Zeichenfolge und nicht in einem eigenen Feld enthalten. Ein Beispiel für das Extrahieren von Parametern finden Sie in Schritt 14 unter [Arbeiten mit Parametern der Logik-App beim Erstellen von Microsoft Defender für Cloud-Workflowautomatisierungen](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121).

    Der Logik-App-Designer unterstützt die folgenden Defender für Cloud-Trigger:

    - **Bei Erstellung oder Auslösung einer Microsoft Defender für Cloud-Empfehlung**: Wenn Ihre Logik-App auf einer Empfehlung basiert, die veraltet ist oder ersetzt wird, funktioniert die Automatisierung nicht mehr, und Sie müssen den Trigger aktualisieren. Informationen zum Nachverfolgen der Änderungen von Empfehlungen finden Sie in den [Versionshinweisen](release-notes.md).

    - **Bei Erstellung oder Auslösung einer Defender für Cloud-Warnung**: Sie können den Trigger so anpassen, dass er nur für Warnungen mit den für sie interessanten Schweregraden gilt.
    
    - **Bei Erstellung oder Auslösung einer Bewertung der Einhaltung gesetzlicher Vorschriften durch Defender für Cloud**: Sie können festlegen, dass Automatisierungen basierend auf den Aktualisierungen von Bewertungen der Einhaltung gesetzlicher Vorschriften ausgelöst werden.

    > [!NOTE]
    > Wenn Sie den älteren Trigger „Beim Auslösen einer Antwort auf eine Microsoft Defender für Cloud-Warnung“ verwenden, wird Ihre Logik-App nicht von der Funktion zur Workflowautomatisierung gestartet. Verwenden Sie stattdessen einen der oben genannten Trigger. 

    [![Beispiel-Logik-App.](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Kehren Sie nach dem Definieren Ihrer Logik-App zum Bereich zur Definition der Workflowautomatisierung („Workflowautomatisierung hinzufügen“) zurück. Klicken Sie auf **Aktualisieren**, um sicherzustellen, dass Ihre neue Logik-App als Auswahl verfügbar ist.

    ![Aktualisieren.](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Wählen Sie die Logik-App aus, und speichern Sie die Automatisierung. Beachten Sie, dass in der Dropdownliste nur Logik-Apps angezeigt werden, die über die oben beschriebenen unterstützenden Defender für Cloud-Connectors verfügen.


## <a name="manually-trigger-a-logic-app"></a>Manuelles Auslösen einer Logik-App

Sie können Logic Apps auch manuell ausführen, wenn Sie sich eine beliebige Sicherheitswarnung oder -empfehlung ansehen.

Um eine Logik-App manuell auszuführen, öffnen Sie eine Warnung oder Empfehlung, und klicken Sie auf **Logik-App auslösen**:

[![Manuelles Auslösen einer Logik-App.](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Konfigurieren der Workflowautomatisierung im großen Stil mithilfe der bereitgestellten Richtlinien

Die Automatisierung der Prozesse Ihrer Organisation zur Überwachung und Reaktion auf Vorfälle kann die Zeit, die zum Untersuchen von Sicherheitsvorfällen und zur Durchführung entsprechender Gegenmaßnahmen benötigt wird, erheblich verkürzen.

Verwenden Sie für die Bereitstellung Ihrer Automatisierungskonfigurationen in Ihrer Organisation die unten beschriebenen von Azure Policy bereitgestellten „DeployIfNotExist“-Richtlinien zum Erstellen und Konfigurieren von Prozeduren für die Workflowautomatisierung.

Beginnen Sie mit [Vorlagen zur Workflowautomatisierung](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

So implementieren Sie diese Richtlinien

1. Klicken Sie in der folgenden Tabelle auf die Richtlinie, die Sie anwenden möchten:

    |Zielsetzung  |Richtlinie  |Richtlinien-ID  |
    |---------|---------|---------|
    |Workflowautomatisierung für Sicherheitswarnungen              |[Bereitstellen der Workflowautomatisierung für Microsoft Defender für Cloud-Warnungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Workflowautomatisierung für Sicherheitsempfehlungen     |[Bereitstellen der Workflowautomatisierung für Microsoft Defender für Cloud-Empfehlungen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |Workflowautomatisierung für Änderungen bei der Einhaltung gesetzlicher Bestimmungen|[Bereitstellen der Workflowautomatisierung für die Einhaltung gesetzlicher Bestimmungen in Microsoft Defender für Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > Sie können auch in Azure Policy nach diesen Richtlinien suchen:
    > 1. Öffnen Sie Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Zugreifen auf Azure Policy.":::
    > 2. Klicken Sie im Azure Policy-Menü auf **Definitionen**, und suchen Sie nach dem Namen der gewünschten Richtlinie. 

1. Klicken Sie auf der entsprechenden Azure Policy-Seite auf **Zuweisen**.
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Zuweisen der Azure-Richtlinie.":::

1. Öffnen Sie alle Registerkarten, und legen Sie die Parameter wie gewünscht fest:
    1. Legen Sie auf der Registerkarte **Grundeinstellungen** den Bereich für die Richtlinie fest. Weisen Sie die Richtlinie für eine zentrale Verwaltung der Verwaltungsgruppe mit den Abonnements zu, die die Konfiguration für die Workflowautomatisierung verwenden sollen. 
    1. Legen Sie auf der Registerkarte **Parameter** die Ressourcengruppe und die Details für den Datentyp fest. 
        > [!TIP]
        > Jeder Parameter verfügt über eine QuickInfo, in der die verfügbaren Optionen erläutert werden.
        >
        > Die Registerkarte „Parameter“ in Azure Policy (1) ermöglicht den Zugriff auf ähnliche Konfigurationsoptionen wie auf der Seite „Workflowautomatisierung“ von Defender für Cloud (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Vergleich der Parameter auf der Seite „Workflowautomatisierung“ mit Azure Policy." lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Wenn Sie diese Zuweisung auf vorhandene Abonnements anwenden möchten, können Sie die Registerkarte **Wartung** öffnen und die Option zum Erstellen eines Wartungstasks auswählen.

1. Überprüfen Sie die Seite „Zusammenfassung“, und klicken Sie auf **Erstellen**.


## <a name="data-types-schemas"></a>Datentypenschemas

Um die unformatierten Ereignisschemas der Sicherheitswarnungen oder Empfehlungsereignisse anzuzeigen, die an die Logik-App-Instanz übermittelt werden, sehen Sie sich die [Schemas für Workflowautomatisierungs-Datentypen](https://aka.ms/ASCAutomationSchemas) an. Dies kann nützlich sein, wenn Sie nicht die oben genannten integrierten Logik-App-Connectors von Defender für Cloud, sondern den generischen HTTP-Connector der Logik-App verwenden. Sie können die Analyse hierfür bei Bedarf mit dem JSON-Ereignisschema manuell durchführen.


## <a name="faq---workflow-automation"></a>Häufig gestellte Fragen: Workflowautomatisierung

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Unterstützt die Workflowautomatisierung irgendwelche Szenarien der Geschäftskontinuität oder Notfallwiederherstellung (BCDR)?

Wenn Sie Ihre Umgebung für BCDR-Szenarien vorbereiten, in denen in der Zielressource ein Ausfall oder ein anderer Notfass eintritt, liegt es in der Verantwortung der Organisation, durch Einrichten von Sicherungen gemäß den Richtlinien von Azure Event Hubs, Log Analytics-Arbeitsbereichen und der Logik-App Datenverluste zu verhindern.

Wir empfehlen Ihnen, für jede aktive Automatisierung eine identische (deaktivierte) Automatisierung zu erstellen und an einem anderen Ort zu speichern. Bei einem Ausfall können Sie diese Sicherungsautomatisierungen aktivieren und den normalen Betrieb aufrechterhalten.

Weitere Informationen zu [Business Continuity & Disaster Recovery für Azure Logic Apps](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Logik-Apps erstellen, deren Ausführung in Defender für Cloud automatisieren und sie manuell ausführen.

Verwandte Informationen finden Sie hier: 

- [Das Microsoft Learn-Modul über die Verwendung der Workflowautomatisierung zur Automatisierung einer Sicherheitsreaktion](/learn/modules/resolve-threats-with-azure-security-center/)
- [Sicherheitsempfehlungen in Microsoft Defender für Cloud](review-security-recommendations.md)
- [Sicherheitswarnungen in Microsoft Defender für Cloud](alerts-overview.md)
- [Informationen zu Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Connectors für Azure Logic Apps](../connectors/apis-list.md)
- [Workflowautomatisierungs-Datentypenschemas](https://aka.ms/ASCAutomationSchemas)
