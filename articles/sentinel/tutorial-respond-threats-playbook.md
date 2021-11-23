---
title: Verwenden von Playbooks mit Automatisierungsregeln in Microsoft Sentinel
description: Dieses Tutorial hilft Ihnen bei der Verwendung von Playbooks mit Automatisierungsregeln in Microsoft Sentinel, um Ihre Reaktion auf Vorfälle zu automatisieren und Sicherheitsbedrohungen zu behandeln.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 8a1c290cebe566ec7d3a812de9746df8e0297120
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517746"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-microsoft-sentinel"></a>Tutorial: Verwenden von Playbooks mit Automatisierungsregeln in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Tutorial erfahren Sie, wie Sie Playbooks mit Automatisierungsregeln verwenden, um Ihre Reaktion auf Vorfälle zu automatisieren und von Microsoft Sentinel erkannte Sicherheitsbedrohungen zu behandeln. Das Tutorial umfasst Folgendes:

> [!div class="checklist"]
>
> * Erstellen einer Automatisierungsregel
> * Erstellen eines Playbooks
> * Hinzufügen von Aktionen zu einem Playbook
> * Anfügen eines Playbooks an eine Automatisierungs- oder Analyseregel, um die Reaktion auf Bedrohungen zu automatisieren

> [!NOTE]
> Dieses Tutorial enthält grundlegende Anleitungen für eine der wichtigsten Kundenaufgaben: Das Einrichten von Automatisierung für die Selektierung von Vorfällen. Weitere Informationen finden Sie in unserem Abschnitt **Vorgehensweise**, z. B. [Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel](automate-responses-with-playbooks.md) und [Verwenden von Triggern und Aktionen in Microsoft Sentinel-Playbooks](playbook-triggers-actions.md).
>

## <a name="what-are-automation-rules-and-playbooks"></a>Was sind Automatisierungsregeln und Playbooks?

Automatisierungsregeln helfen bei der Selektierung von Vorfällen in Microsoft Sentinel. Hiermit können Sie Vorfälle automatisch den richtigen Mitarbeitern zuweisen, überflüssige Vorfälle oder bekannte [False Positives](false-positives.md) schließen, den Schweregrad ändern und Tags hinzufügen. Außerdem sind sie der Mechanismus, mit dem Playbooks als Reaktion auf Vorfälle ausgeführt werden können.

Bei Playbooks handelt es sich um eine Sammlung von Prozeduren, die über Microsoft Sentinel als Reaktion auf eine Warnung oder einen Vorfall ausgeführt werden können. Ein Playbook kann Ihnen dabei helfen, Ihre Reaktion zu automatisieren und zu orchestrieren, und es kann so festgelegt werden, dass es automatisch ausgeführt wird, wenn bestimmte Warnungen oder Vorfälle generiert werden. Hierzu wird es an eine Analyseregel oder an eine Automatisierungsregel angefügt. Bei Bedarf kann es aber auch manuell ausgeführt werden.

Da Playbooks in Microsoft Sentinel auf in [Azure Logic Apps](../logic-apps/logic-apps-overview.md) erstellten Workflows basieren, stehen Ihnen die Leistung, Anpassbarkeit und integrierten Vorlagen zur Verfügung, die Sie von Logic Apps gewohnt sind. Jedes Playbook wird zwar speziell für das Abonnement erstellt, zu dem es gehört, unter **Playbooks** werden jedoch alle Playbooks angezeigt, die für alle ausgewählten Abonnements verfügbar sind.

> [!NOTE]
> Da Playbooks Azure Logic Apps nutzen, fallen möglicherweise zusätzliche Gebühren an. Ausführlichere Informationen finden Sie auf der [Preisseite von Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Wenn Sie beispielsweise verhindern möchten, dass sich potenziell kompromittierte Benutzer durch Ihr Netzwerk bewegen und Informationen stehlen, können Sie eine automatisierte, vielseitige Reaktion auf Vorfälle erstellen, die durch Regeln zur Erkennung kompromittierter Benutzer generiert werden. Hierzu können Sie ein Playbook mit folgenden Aktionen erstellen:

1. Wenn das Playbook durch eine Automatisierungsregel aufgerufen wird und von ihr einen Vorfall erhält, wird ein Ticket in [ServiceNow](/connectors/service-now/) oder in einem anderen IT-Ticketsystem erstellt.

1. Eine Nachricht wird an Ihren Sicherheitskanal in [Microsoft Teams](/connectors/teams/) oder [Slack](/connectors/slack/) gesendet, um Ihre Sicherheitsanalysten auf den Vorfall aufmerksam zu machen.

1. Außerdem werden sämtliche Informationen des Vorfalls per E-Mail an den leitenden Netzwerkadministrator sowie an den Sicherheitsadministrator gesendet. Die E-Mail enthält Optionsschaltflächen zum **Blockieren** und **Ignorieren**.

1. Das Playbook wartet auf eine Reaktion der Administratoren und fährt dann mit den nächsten Schritten fort.

1. Wenn die Administratoren die Option **Blockieren** auswählen, werden Befehle an Azure AD und an die Firewall gesendet, um den Benutzer zu deaktivieren bzw. um die IP-Adresse zu blockieren.

1. Wenn die Administratoren die Option **Ignorieren** auswählen, werden der Vorfall in Microsoft Sentinel und das Ticket in ServiceNow geschlossen.

Erstellen Sie anschließend als Auslöser für das Playbook eine Automatisierungsregel, die ausgeführt wird, wenn diese Vorfälle generiert werden. Diese Regel umfasst folgende Schritte:

1. Sie ändert den Status des Vorfalls in **Aktiv**.

1. Sie weist den Vorfall dem Analysten zu, der für diese Art von Vorfall zuständig ist.

1. Sie fügt das Tag „Kompromittierter Benutzer“ hinzu.

1. Sie ruft das soeben erstellte Playbook auf. [(Für diesen Schritt sind spezielle Berechtigungen erforderlich.)](automate-responses-with-playbooks.md#incident-creation-automated-response)

Playbooks können automatisch als Reaktion auf Vorfälle ausgeführt werden. Hierzu werden Automatisierungsregeln erstellt, die die Playbooks als Aktionen aufrufen, wie im obigen Beispiel gezeigt. Sie können aber auch automatisch als Reaktion auf Warnungen ausgeführt werden. Hierzu wird die Analyseregel so konfiguriert, dass automatisch mindestens ein Playbook aufgerufen wird, wenn die Warnung generiert wird. 

Sie können ein Playbook bei Bedarf auch manuell ausführen, um auf eine ausgewählte Warnung zu reagieren.

Eine ausführlichere Einführung in die Automatisierung von Reaktionen auf Bedrohungen mit Automatisierungsregeln und Playbooks in Microsoft Sentinel finden Sie unter [Automatisierung der Vorfallbehandlung in Microsoft Sentinel mit Automatisierungsregeln](automate-incident-handling-with-automation-rules.md) sowie unter [Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel](automate-responses-with-playbooks.md).

> [!IMPORTANT]
>
> - **Automatisierungsregeln** und die Verwendung des **Incidenttriggers** für Playbooks befinden sich aktuell in der **Vorschauphase**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="create-a-playbook"></a>Erstellen eines Playbooks

Gehen Sie wie folgt vor, um in Microsoft Sentinel ein neues Playbook zu erstellen:

### <a name="prepare-the-playbook-and-logic-app"></a>Vorbereiten des Playbooks und der Logik-App

1. Wählen Sie im Navigationsmenü **Microsoft Sentinel** die Option **Automatisierung** aus.

1. Wählen Sie im oberen Menü **Erstellen** > **Neues Playbook hinzufügen** aus.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Hinzufügen eines neuen Playbooks":::

    Eine neue Browserregisterkarte wird geöffnet, und der Assistent **Logik-App erstellen** wird angezeigt.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Erstellen einer Logik-App":::

1. Geben Sie Ihr **Abonnement** und Ihre **Ressourcengruppe** ein, und benennen Sie unter **Logik-App-Name** Ihr Playbook.

1. Wählen Sie unter **Region** die Azure-Region aus, in der die Informationen Ihrer Logik-App gespeichert werden sollen.

1. Wenn Sie die Aktivität dieses Playbooks zu Diagnosezwecken überwachen möchten, aktivieren Sie das Kontrollkästchen **Log Analytics aktivieren**, und geben Sie unter **Log Analytics-Arbeitsbereich** den Namen Ihres Log Analytics-Arbeitsbereichs ein.

1. Wenn Sie Tags auf Ihr Playbook anwenden möchten, klicken Sie auf **Weiter: Tags >** . (Hierbei handelt es sich nicht um Tags, die durch Automatisierungsregeln angewendet werden. Weitere Informationen zu Tags finden Sie [hier](../azure-resource-manager/management/tag-resources.md).) Klicken Sie andernfalls auf **Überprüfen und erstellen**. Überprüfen Sie die von Ihnen angegebenen Details, und klicken Sie auf **Erstellen**.

1. Während Ihr Playbook erstellt und bereitgestellt wird (was einige Minuten dauert), werden Sie zu einem Bildschirm namens **Microsoft.EmptyWorkflow** weitergeleitet. Wenn die Meldung „Ihre Bereitstellung wurde abgeschlossen.“ angezeigt wird, können Sie auf **Zu Ressource wechseln** klicken.

1. Dadurch gelangen Sie zum [Designer für Logik-Apps](../logic-apps/logic-apps-overview.md) Ihres neuen Playbooks, in dem Sie den Workflow entwerfen können. Ein Bildschirm mit einem kurzen Einführungsvideo und einigen häufig verwendeten Logik-App-Triggern und -Vorlagen wird angezeigt. Weitere Informationen zum Erstellen eines Playbooks mit Logic Apps finden Sie [hier](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

1. Wählen Sie die Vorlage **Leere Logik-App** aus.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Vorlagenkatalog des Designers für Logik-Apps":::

### <a name="choose-the-trigger"></a>Auswählen des Triggers

Jedes Playbook muss mit einem Trigger beginnen. Der Trigger definiert die Aktion, durch die das Playbook gestartet wird, sowie das vom Playbook erwartete Schema.

1. Suchen Sie in der Suchleiste nach Microsoft Sentinel. Wählen Sie **Microsoft Sentinel** aus, wenn der entsprechende Eintrag in den Ergebnissen angezeigt wird.

1. Auf der daraufhin angezeigten Registerkarte **Trigger** stehen die beiden von Microsoft Sentinel angebotenen Trigger zur Verfügung:
    - Wenn eine Reaktion auf eine Microsoft Sentinel-Warnung ausgelöst wird
    - Wenn die Microsoft Sentinel-Vorfallerstellungsregel ausgelöst wird

   Wählen Sie den passenden Trigger für die Art des Playbooks aus, das Sie erstellen.

    > [!NOTE]
    > Denken Sie daran, dass nur Playbooks, die auf dem **Incidenttrigger** basieren, von Automatisierungsregeln aufgerufen werden können. Playbooks, die auf dem **Warnungstrigger** basieren, müssen für die direkte Ausführung in [Analyseregeln](detect-threats-custom.md#set-automated-responses-and-create-the-rule) definiert werden und können auch manuell ausgeführt werden.
    > 
    > Weitere Informationen zum zu verwendenden Trigger finden Sie unter [**Verwenden von Triggern und Aktionen in Microsoft Sentinel-Playbooks**](playbook-triggers-actions.md).

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Auswählen eines Triggers für Ihr Playbook":::

> [!NOTE]
> Wenn Sie einen Trigger oder eine nachfolgende Aktion auswählen, werden Sie aufgefordert, sich bei dem Ressourcenanbieter zu authentifizieren, mit dem Sie interagieren. In diesem Fall ist der Anbieter Microsoft Sentinel. Sie können verschiedene Ansätze für die Authentifizierung verwenden. Weitere Informationen und Anweisungen finden Sie unter [**Authentifizieren von Playbooks für Microsoft Sentinel**](authenticate-playbooks-to-sentinel.md).

### <a name="add-actions"></a>Hinzufügen von Aktionen

Nun können Sie definieren, was passieren soll, wenn das Playbook aufgerufen wird. Durch Auswählen von **Neuer Schritt** können Sie Aktionen, logische Bedingungen, Schleifen oder Parameterbedingungen hinzufügen. Nach dem Auswählen dieser Option wird im Designer ein neuer Rahmen geöffnet, in dem Sie ein System oder eine Anwendung für die Interaktion oder eine festzulegende Bedingung auswählen können. Geben Sie am oberen Rand des Frames den Namen des Systems oder der Anwendung in die Suchleiste ein, und wählen Sie anschließend eines der verfügbaren Ergebnisse aus.

In jedem dieser Schritte wird nach dem Klicken auf ein beliebiges Feld ein Bereich mit zwei Menüs angezeigt: **Dynamischer Inhalt** und **Ausdruck**. Im Menü **Dynamischer Inhalt** können Sie Verweise auf die Attribute der Warnung oder des Vorfalls hinzufügen, die bzw. der an das Playbook übergeben wurde – einschließlich der Werte und Attribute aller beteiligten Entitäten. Im Menü **Ausdruck** steht eine umfangreiche Bibliothek mit Funktionen zur Verfügung, mit denen Sie Ihren Schritten zusätzliche Logik hinzufügen können.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Logik-App-Designer":::

Dieser Screenshot zeigt die Aktionen und Bedingungen, die Sie bei der Erstellung des Playbooks hinzufügen würden, das im Beispiel am Anfang dieses Dokuments beschrieben wurde. Der einzige Unterschied besteht darin, dass Sie im hier gezeigten Playbook anstelle des **Incidenttriggers** den **Warnungstrigger** verwenden. Das bedeutet, dass dieses Playbook nicht über eine Automatisierungsregel, sondern direkt über eine Analyseregel aufgerufen wird. Im weiteren Verlauf werden beide Methoden zum Aufrufen eines Playbooks beschrieben.

## <a name="automate-threat-responses"></a>Automatisieren der Reaktionen auf Bedrohungen

Sie haben Ihr Playbook erstellt und den Trigger definiert, die Bedingungen festgelegt und die auszuführenden Aktionen sowie die zu generierenden Ausgaben vorgegeben. Nun müssen Sie die Kriterien für die Ausführung bestimmen und den Automatisierungsmechanismus einrichten, durch den es ausgeführt wird, wenn diese Kriterien erfüllt sind.

### <a name="respond-to-incidents"></a>Reagieren auf Incidents

Sie verwenden ein Playbook, um auf einen **Vorfall** zu reagieren. Hierzu erstellen Sie eine [Automatisierungsregel](automate-incident-handling-with-automation-rules.md), die ausgeführt wird, wenn der Vorfall generiert wird, und das Playbook aufruft.

So erstellen Sie eine Automatisierungsregel:

1. Wählen Sie im Microsoft Sentinel-Navigationsmenü auf dem Blatt **Automatisierung** über das obere Menü **Erstellen** > **Neue Regel hinzufügen** aus.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Hinzufügen einer neuen Regel":::

1. Der Bereich **Neue Automatisierungsregel erstellen** wird geöffnet. Geben Sie einen Namen für Ihre Regel ein.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Erstellen einer Automatisierungsregel":::

1. Wenn die Automatisierungsregel nur für bestimmte Analyseregeln gelten soll, ändern Sie die Bedingung **If Analytics rule name** (Wenn Name der Analyseregel), um die gewünschten Regeln anzugeben.

1. Fügen Sie alle weiteren Bedingungen hinzu, die ggf. für die Aktivierung dieser Automatisierungsregel gelten sollen. Klicken Sie auf **Bedingung hinzufügen**, und wählen Sie Bedingungen aus der Dropdownliste aus. Die Liste mit den Bedingungen wird mit Feldern für Warnungsdetails und Entitätsbezeichner aufgefüllt.

1. Wählen Sie die Aktionen aus, die durch diese Automatisierungsregel ausgeführt werden sollen. Zu den verfügbaren Aktionen zählen **Assign owner** (Besitzer zuweisen), **Status ändern**, **Schweregrad ändern**, **Tags hinzufügen** und **Playbook ausführen**. Sie können beliebig viele Aktionen hinzufügen.

1. Wenn Sie eine Aktion vom Typ **Playbook ausführen** hinzufügen, werden Sie aufgefordert, ein Playbook aus einer Liste mit verfügbaren Playbooks auszuwählen. Nur Playbooks, die mit dem **Incidenttrigger** beginnen, können über Automatisierungsregeln ausgeführt werden. Daher werden in der Liste auch nur diese Playbooks angezeigt.<a name="permissions-to-run-playbooks"></a>

    > [!IMPORTANT]
    > Microsoft Sentinel müssen explizite Berechtigungen erteilt werden, um Playbooks über Automatisierungsregeln ausführen zu können. Ist ein Playbook in der Dropdownliste ausgegraut dargestellt, verfügt Sentinel über keine Berechtigung für die Ressourcengruppe des Playbooks. Klicken Sie auf den Link **Manage playbook permissions** (Playbookberechtigungen verwalten), um Berechtigungen zuzuweisen.
    > Aktivieren Sie im daraufhin angezeigten Bereich **Berechtigungen verwalten** die Kontrollkästchen der Ressourcengruppen, die die auszuführenden Playbooks enthalten, und klicken Sie auf **Anwenden**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Verwalten von Berechtigungen":::
    > - Sie selbst müssen für jede Ressourcengruppe, für die Sie Microsoft Sentinel Berechtigungen erteilen möchten, über die Berechtigung **Besitzer** und für jede Ressourcengruppe, die auszuführende Playbooks enthält, über die Rolle **Mitwirkender für Logik-Apps** verfügen.
    > - In einer Bereitstellung mit mehreren Mandanten gilt: Wenn sich das Playbook, das Sie ausführen möchten, in einem anderen Mandanten befindet, müssen Sie Microsoft Sentinel die Berechtigung zum Ausführen des Playbooks im zugehörigen Mandanten erteilen.
    >    1. Wählen Sie im Mandanten des Playbooks im Microsoft Sentinel-Navigationsmenü die Option **Einstellungen** aus.
    >    1. Wählen Sie auf dem Blatt **Einstellungen** die Registerkarte **Einstellungen** aus, und erweitern Sie anschließend den Bereich **Playbookberechtigungen**.
    >    1. Klicken Sie auf die Schaltfläche **Berechtigungen konfigurieren**, um den weiter oben erwähnten Bereich **Berechtigungen verwalten** zu öffnen, und fahren Sie wie dort beschrieben fort.
    > - Wenn Sie in einem **MSSP**-Szenario ein [Playbook in einem Kundenmandanten](automate-incident-handling-with-automation-rules.md#permissions-in-a-multi-tenant-architecture) anhand einer Automatisierungsregel ausführen möchten, die Sie erstellt haben, während Sie beim Dienstanbietermandanten angemeldet waren, müssen Sie Microsoft Sentinel die Berechtigung zum Ausführen des Playbooks in **_beiden Mandanten_ *erteilen. Befolgen Sie im _* Kundenmandanten** die Anweisungen für die mehrinstanzenfähige Bereitstellung unter dem vorherigen Aufzählungspunkt. Im **Dienstanbietermandanten** müssen Sie die Azure Security Insights-App in Ihrer Azure Lighthouse-Onboardingvorlage hinzufügen:
    >    1. Navigieren Sie im Azure-Portal zu **Azure Active Directory**.
    >    1. Klicken Sie auf **Unternehmensanwendungen**.
    >    1. Wählen Sie **Anwendungstyp** aus, und filtern Sie nach **Microsoft-Anwendungen**.
    >    1. Geben Sie im Suchfeld den Namen **Azure Security Insights** ein.
    >    1. Kopieren Sie das Feld **Objekt-ID**. Sie müssen diese zusätzliche Autorisierung zu Ihrer vorhandenen Azure Lighthouse-Delegierung hinzufügen.
    >
    >    Die Rolle **Mitwirkender für Microsoft Sentinel-Automatisierung** weist eine feste GUID auf. Hierbei handelt es sich um `f4c81013-99ee-4d62-a7ee-b3f1f648599a`. Ein Beispiel für eine Azure Lighthouse Autorisierung würde in Ihrer Parametervorlage wie folgt aussehen:
    >    
    >    ```json
    >    {
    >        "principalId": "<Enter the Azure Security Insights app Object ID>", 
    >        "roleDefinitionId": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
    >        "principalIdDisplayName": "Microsoft Sentinel Automation Contributors" 
    >    }
    >    ```

1. Falls gewünscht, können Sie ein Ablaufdatum für Ihre Automatisierungsregel festlegen.

1. Geben Sie unter **Reihenfolge** eine Zahl ein, um festzulegen, wann diese Regel in der Sequenz der Automatisierungsregeln ausgeführt werden soll.

1. Klicken Sie auf **Anwenden**. Sie haben es geschafft!

Weitere Möglichkeiten zum Erstellen von Automatisierungsregeln finden Sie [hier](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules).

### <a name="respond-to-alerts"></a>Reagieren auf Warnungen

Sie verwenden ein Playbook, um auf eine **Warnung** zu reagieren. Hierzu erstellen Sie eine **Analyseregel** oder bearbeiten eine bereits vorhandene Regel, die ausgeführt wird, wenn die Warnung generiert wird, und wählen Ihr Playbook als automatisierte Antwort im [Analyseregel-Assistent](detect-threats-custom.md) aus.

1. Wählen Sie im Microsoft Sentinel-Navigationsmenü auf dem Blatt **Analyse** die Analyseregel aus, für die Sie die Reaktion automatisieren möchten, und klicken Sie im Detailbereich auf **Bearbeiten**.

1. Wählen Sie auf der Seite **Analyseregel-Assistent – Vorhandene Regel bearbeiten** die Registerkarte **Automatisierte Antwort** aus.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Registerkarte „Automatisierte Antwort“":::

1. Wählen Sie in der Dropdownliste Ihr Playbook aus. Sie können mehrere Playbooks auswählen. Die Liste enthält jedoch nur Playbooks mit dem **Warnungstrigger**.

1. Wählen Sie auf der Registerkarte **Überprüfen und erstellen** die Option **Speichern** aus.

## <a name="run-a-playbook-on-demand"></a>Ausführen eines Playbooks bei Bedarf

Sie können ein Playbook auch bei Bedarf ausführen.

 > [!NOTE]
 > Nur Playbooks mit **Warnungstrigger** können bei Bedarf ausgeführt werden.

So führen Sie ein Playbook bei Bedarf aus:

1. Wählen Sie auf der Seite **Incidents** einen Vorfall aus, und klicken Sie anschließend auf **Alle Informationen anzeigen**.

2. Klicken Sie auf der Registerkarte **Warnungen** auf die Warnung, für die Sie das Playbook ausführen möchten, scrollen Sie ganz nach rechts, klicken Sie auf **Playbooks anzeigen**, und wählen Sie in der Liste mit den verfügbaren Playbooks für das Abonnement ein Playbook zum **Ausführen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Playbooks und Automatisierungsregeln in Microsoft Sentinel verwenden, um auf Bedrohungen zu reagieren. 
- Im [nächsten Artikel](hunting.md) erfahren Sie, wie Sie mithilfe von Microsoft Sentinel proaktiv nach Bedrohungen suchen.
