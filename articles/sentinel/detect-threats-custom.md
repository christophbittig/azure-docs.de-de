---
title: Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen mit Microsoft Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie benutzerdefinierte Analyseregeln erstellen, um mit Microsoft Sentinel Sicherheitsbedrohungen zu erkennen. Nutzen Sie die Ereignis- und Warnungsgruppierung und die Warnungsanreicherung, und informieren Sie sich über „AUTO DISABLED“.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 66ccebb9154285c7f387b7ef51424cbfb177fa04
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521565"
---
# <a name="create-custom-analytics-rules-to-detect-threats"></a>Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Nachdem Sie [Ihre Datenquellen mit Microsoft Sentinel verbunden haben](quickstart-onboard.md), können Sie benutzerdefinierte Analyseregeln erstellen, mit denen Sie Bedrohungen und anomales Verhalten in Ihrer Umgebung erkennen können.

Mit diesen Analyseregeln werden bestimmte Ereignisse oder Ereignisgruppen in Ihrer Umgebung gesucht, beim Erreichen bestimmter Ereignisschwellenwerte oder -bedingungen Warnungen ausgegeben, und Incidents generiert, die Ihr SOC selektieren und untersuchen kann. Außerdem wird mit automatisierten Nachverfolgungs- und Korrekturprozessen auf Bedrohungen reagiert.

> [!TIP]
> Um benutzerdefinierte Regeln zu erstellen, verwenden Sie vorhandene Regeln als Vorlagen oder Referenzen. Die Verwendung vorhandener Regeln als Grundlage hilft dabei, den Großteil der Logik zu erstellen, bevor Sie die erforderlichen Änderungen vornehmen.

> [!div class="checklist"]
> - Erstellen von Analyseregeln
> - Definieren, wie Ereignisse und Warnungen verarbeitet werden
> - Definieren, wie Warnungen und Vorfälle generiert werden
> - Auswählen automatisierter Reaktionen auf Bedrohungen für Ihre Regeln

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Erstellen einer benutzerdefinierten Analyseregel mit einer geplanten Abfrage

1. Klicken Sie im Microsoft Sentinel-Navigationsmenü auf **Analytics**.

1. Wählen Sie auf der Aktionsleiste die Option **+ Erstellen** und anschließend **Geplante Abfrageregel** aus. Dadurch wird der **Assistent für Analyseregeln** geöffnet.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Erstellen einer geplanten Abfrage" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Analyseregel-Assistent: Registerkarte „Allgemein“

- Geben Sie einen eindeutigen **Namen** und eine **Beschreibung** an.

- Im Feld **Taktiken** stehen verschiedene Angriffskategorien zur Auswahl, nach denen die Regel klassifiziert werden kann. Diese basieren auf den Taktiken des [MITRE ATT&CK](https://attack.mitre.org/)-Frameworks.

- Legen Sie den **Schweregrad** der Warnung nach Bedarf fest.

- Beim Erstellen der Regel wird der **Status** standardmäßig auf **Aktiviert** eingestellt. Dies bedeutet, dass die Regel nach der Erstellung sofort ausgeführt wird. Wenn Sie die Regel nicht sofort ausführen möchten, wählen Sie **Deaktiviert** aus. Die Regel wird der Registerkarte **Aktive Regeln** hinzugefügt, von wo Sie diese bei Bedarf aktivieren können.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Beginnen mit dem Erstellen einer benutzerdefinierten Analyseregel":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Definieren der Regelabfragelogik und Konfigurieren von Einstellungen

Auf der Registerkarte **Regellogik festlegen** können Sie im Feld **Regelabfrage** eine Abfrage direkt eingeben. Alternativ können Sie eine Abfrage in Log Analytics erstellen, diese kopieren und in das Feld einfügen.

- Abfragen werden in der Kusto-Abfragesprache (KQL) geschrieben. Weitere Informationen zu KQL-[Konzepten](/azure/data-explorer/kusto/concepts/) und -[Abfragen](/azure/data-explorer/kusto/query/) finden Sie in diesem praktischen [Kurzreferenzhandbuch](/azure/data-explorer/kql-quick-reference).

- Im Beispiel in diesem Screenshot wird die Tabelle *SecurityEvent* abgefragt, um einen Typ von [fehlerhaften Windows-Anmeldeereignissen](/windows/security/threat-protection/auditing/event-4625) anzuzeigen.

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Konfigurieren von Abfrageregellogik und Einstellungen" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Mit der folgenden Beispielabfrage werden Sie gewarnt, wenn im [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) eine ungewöhnliche Anzahl von Ressourcen erstellt wird.

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > **Bewährte Methoden für Abfragen**:
    >
    > - Die Abfrage sollte zwischen 1 und 10.000 Zeichen lang sein und darf weder `search *` noch `union *` enthalten. Sie können [benutzerdefinierte Funktionen](/azure/data-explorer/kusto/query/functions/user-defined-functions) verwenden, um die Einschränkung der Abfragelänge außer Kraft zu setzen.
    >
    > - Das Erstellen von Azure Data Explorer-Abfragen mit ADX-Funktionen innerhalb des Log Analytics-Abfragefensters **wird nicht unterstützt**.
    >
    > - Wenn Sie bei Verwendung der **`bag_unpack`** -Funktion die Spalten mithilfe von `project field1` als Felder projizieren und eine Spalte nicht vorhanden ist, treten bei der Abfrage Fehler auf. Um dies zu verhindern, müssen Sie die Spalte wie folgt projizieren:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Warnungsanreicherung

> [!IMPORTANT]
> Die Funktionen zur Warnungsanreicherung befinden sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

- Im Konfigurationsbereich **Entitätszuordnung** können Sie Parameter aus den Abfrageergebnissen den von Microsoft Sentinel erkannten Entitäten zuordnen. Durch Entitäten wird die Ausgabe der Regeln (Warnungen und Vorfälle) um wesentliche Informationen erweitert, die als Bausteine für alle nachfolgenden Untersuchungsprozesse und Korrekturmaßnahmen dienen. Außerdem können Sie auf der Registerkarte **Incidenteinstellungen** Warnungen nach bestimmten Kriterien in Vorfällen gruppieren.

    Erfahren Sie mehr über [Entitäten in Microsoft Sentinel](entities-in-azure-sentinel.md).

    Unter [Zuordnen von Datenfeldern zu Entitäten in Microsoft Sentinel](map-data-fields-to-entities.md) finden Sie umfassende Anweisungen zur Entitätszuordnung sowie wichtige Informationen zur [Abwärtskompatibilität](map-data-fields-to-entities.md#notes-on-the-new-version).

- Im Konfigurationsbereich **Benutzerdefinierten Details** können Sie Ereignisdatenelemente aus der Abfrage extrahieren und in den durch die jeweilige Regel generierten Warnungen anzeigen. Dadurch erhalten Sie direkten Einblick in die Ereignisinhalte der Warnungen und Vorfälle.

    Weitere Informationen zum Anzeigen benutzerdefinierter Details in Warnungen finden Sie in den [vollständigen Anweisungen](surface-custom-details-in-alerts.md).

- Verwenden Sie den Konfigurationsabschnitt **Warnungsdetails**, um die Details zur Darstellung der Warnung an den tatsächlichen Inhalt anzupassen. Mit Warnungsdetails können Sie z. B. die IP-Adresse oder den Kontonamen eines Angreifers im Titel der Warnung selbst anzeigen, sodass diese Informationen in der Warteschlange Ihrer Incidents angezeigt werden und Sie ein viel umfassenderes und klareres Bild Ihrer Bedrohungslandschaft erhalten.

    Lesen Sie die vollständigen Anweisungen zum [Anpassen Ihrer Warnungsdetails](customize-alert-details.md).

### <a name="query-scheduling-and-alert-threshold"></a>Abfrageplanung und Warnungsschwellenwert

- Legen Sie im Abschnitt **Abfrageplanung** die folgenden Parameter fest:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Festlegen von Abfragezeitplan und Ereignisgruppierung" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

  - Legen Sie unter **Abfrage ausführen alle** fest, wie häufig die Abfrage ausgeführt werden soll: häufig (alle fünf Minuten) oder selten (einmal alle 14 Tage).

  - Legen Sie unter **Datensuche für letzte** den Zeitraum der von der Abfrage abgedeckten Daten fest: z. B. Daten der letzten 10 Minuten oder der letzten 6 Stunden. Der Höchstwert ist 14 Tage.

    > [!NOTE]
    > **Abfrageintervalle und Rückblickperiode**
    >
    >  Diese beiden Einstellungen sind bis zu einem Punkt voneinander unabhängig. Sie können in einem kurzen Intervall eine Abfrage für einen Zeitraum ausführen, der länger ist als das Intervall (was zu sich überschneidenden Abfragen führt). Sie können jedoch für eine Abfrage kein Intervall festlegen, das den Abdeckungszeitraum überschreitet, da dies zu Lücken in der Gesamtabdeckung der Abfrage führen würde.
        >
        > **Erfassungsverzögerung**
        >
        > Microsoft Sentinel führt geplante Analyseregeln mit einer **fünfminütigen Verzögerung** nach der geplanten Zeit aus, um die **Wartezeit** zu berücksichtigen, die zwischen der Erstellung eines Ereignisses in der Quelle und dessen Erfassung in Microsoft Sentinel auftreten kann, und um eine vollständige Abdeckung ohne Datenduplizierung zu gewährleisten.
        >
        > Weitere Informationen finden Sie unter [Behandeln von Erfassungsverzögerungen in Regeln für geplante Analysen](ingestion-delay.md).

- Im Bereich **Warnungsschwellenwert** können Sie die Vertraulichkeitsstufe der Regel definieren. Legen Sie beispielsweise **Warnung generieren, wenn für die Anzahl der Abfrageergebnisse Folgendes gilt:** auf **Ist größer als** fest, und geben Sie die Zahl 1000 ein, wenn die Regel nur dann eine Warnung generieren soll, wenn die Abfrage bei jeder Ausführung mehr als 1000 Ergebnisse zurückgibt. Da dies ein Pflichtfeld ist, müssen Sie, wenn Sie keinen Schwellenwert festlegen möchten (d. h., wenn bei jedem Ereignis eine Warnung registriert werden soll), im Zahlenfeld den Wert „0“ eingeben.

### <a name="results-simulation"></a>Ergebnissimulation

Wenn Sie im Bereich **Ergebnissimulation** rechts im Assistenten die Option **Mit aktuellen Daten testen** auswählen, wird in Microsoft Sentinel ein Diagramm der Ergebnisse (Protokollereignisse) angezeigt, die die Abfrage in den letzten 50 Ausführungen entsprechend dem aktuell definierten Zeitplan generiert hätte. Wenn Sie die Abfrage ändern, wählen Sie **Mit aktuellen Daten testen** erneut aus, um das Diagramm zu aktualisieren. Das Diagramm zeigt die Anzahl der Ergebnisse im definierten Zeitraum, der durch die Einstellungen im Abschnitt **Abfrageplanung** bestimmt wird.

Die Ergebnissimulation für die Abfrage im obigen Screenshot könnte wie folgt aussehen. Die linke Seite ist die Standardansicht, und die rechte Seite zeigt, was Sie sehen, wenn Sie im Diagramm auf einen bestimmten Zeitpunkt zeigen.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Screenshots der Ergebnissimulation":::

Wenn die Abfrage zu viele oder zu häufige Warnungen generiert hätte, können Sie die Einstellungen in den [Bereichen **Abfrageplanung** und **Warnungsschwellenwert**](#query-scheduling-and-alert-threshold) anpassen und dann erneut **Mit aktuellen Daten testen** auswählen.

### <a name="event-grouping-and-rule-suppression"></a>Ereignisgruppierung und Unterdrückung von Regeln

> [!IMPORTANT]
> Die Ereignisgruppierung befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

- Wählen Sie unter **Ereignisgruppierung** eine von zwei Methoden aus, um **Ereignisse** in **Warnungen** zu gruppieren:

  - **Group all events into a single alert** (Alle Ereignisse in einer einzelnen Warnung gruppieren) (Standardeinstellung). Die Regel generiert bei jeder Ausführung eine einzelne Warnung, solange die Anzahl der von der Abfrage zurückgegebenen Ergebnisse den oben angegebenen **Warnungsschwellenwert** überschreitet. Die Warnung enthält eine Zusammenfassung aller in den Ergebnissen zurückgegebenen Ereignisse.

  - **Warnung für jedes Ereignis auslösen**. Die Regel generiert eine eindeutige Warnung für jedes Ereignis, das von der Abfrage zurückgegeben wird. Dies ist hilfreich, wenn die Ereignisse einzeln angezeigt oder nach bestimmten Parametern – etwa Benutzer oder Hostname – gruppiert werden sollen. Sie können diese Parameter in der Abfrage definieren.

    Derzeit ist die Anzahl von Warnungen, die eine Regel generieren kann, auf 20 begrenzt. Wenn in einer bestimmten Regel **Ereignisgruppierung** auf **Warnung für jedes Ereignis auslösen** festgelegt ist und die Abfrage der Regel mehr als 20 Ereignisse zurückgibt, generiert jedes der ersten 19 Ereignisse eine eindeutige Warnung, und die 20. Warnung fasst den gesamten Satz zurückgegebener Ereignisse zusammen. Mit anderen Worten: Die 20. Warnung ist die Warnung, die auch mit der Option **Alle Ereignisse in einer einzigen Warnung gruppieren** generiert wird.

    Wenn Sie diese Option auswählen, fügt Microsoft Sentinel das neue Feld **OriginalQuery** zu den Ergebnissen der Abfrage hinzu. Hier sehen Sie eine Gegenüberstellung des vorhandenen und des neuen Felds **Abfrage**:

    | Feldname | Enthält | Abfrage wird in diesem Feld ausgeführt<br>Ergebnis |
    | - | :-: | :-: |
    | **Abfrage** | Der komprimierte Datensatz des Ereignisses, das diese Instanz der Warnung generiert hat | Das Ereignis, das diese Instanz der Warnung generiert hat |
    | **OriginalQuery** | Die ursprüngliche Abfrage, wie in der&nbsp;Analyseregel | Das letzte Ereignis in dem Zeitraum, in dem die Abfrage ausgeführt wird, das den von der Abfrage definierten Parametern entspricht |

    Anders ausgedrückt: Das Feld **OriginalQuery** verhält sich so, wie sich das Feld **Abfrage** normalerweise verhält. Ergebnis dieses zusätzlichen Felds: Das durch das erste Element im Abschnitt [Problembehandlung](#troubleshooting) unten beschriebene Problem wurde gelöst.

  > [!NOTE]
  > Was ist der Unterschied zwischen **Ereignissen** und **Warnungen**?
  >
  > - Ein **Ereignis** ist eine Beschreibung eines einzelnen Vorkommens einer Aktion. Beispielsweise kann ein einzelner Eintrag in einer Protokolldatei als Ereignis gezählt werden. In diesem Kontext bezeichnet ein Ereignis ein einzelnes Ergebnis, das von einer Abfrage in einer Analyseregel zurückgegeben wird.
  >
  > - Eine **Warnung** ist eine Sammlung von Ereignissen, die gemeinsam im Hinblick auf die Sicherheit von Bedeutung sind. Eine Warnung kann ein einzelnes Ereignis beinhalten, wenn das Ereignis bedeutende Auswirkungen auf die Sicherheit hat, z. B. eine Administratoranmeldung aus dem Ausland außerhalb der Geschäftszeiten.
  >
  > - Und was sind **Incidents**? Die interne Logik von Microsoft Sentinel erstellt **Incidents** aus **Warnungen** oder Gruppen von Warnungen. Die Arbeit der SOC-Analysten – Selektierung, Untersuchung und Abhilfe – konzentriert sich auf die Vorfallwarteschlange.
  >
  > Microsoft Sentinel erfasst unformatierte Ereignisse aus Datenquellen und bereits verarbeitete Warnungen von anderen. Es muss jederzeit unterschieden werden, ob es sich um Ereignisse oder Warnungen handelt.

- Im Abschnitt **Unterdrückung** können Sie die Einstellung **Ausführung der Abfrage beenden, wenn eine Warnung generiert wurde** auf **Ein** festlegen, wenn Sie nach dem Eingang einer Warnung das Ausführen dieser Regel für einen Zeitraum unterbrechen möchten, der das Abfrageintervall überschreitet. Wenn Sie diese Option aktivieren, müssen Sie unter **Abfrageausführung beenden für:** den Zeitraum festlegen, in dem die Abfrage nicht ausgeführt werden soll (bis zu 24 Stunden).

## <a name="configure-the-incident-creation-settings"></a>Konfigurieren der Einstellungen für die Incidenterstellung

Auf der Registerkarte **Incidenteinstellungen** können Sie auswählen, ob und wie Microsoft Sentinel Warnungen in verwertbare Incidents umwandelt. Wenn Sie diese Registerkarte unverändert lassen, erstellt Microsoft Sentinel einen eigenen separaten Incident von jeder einzelnen Warnung. Durch Ändern der Einstellungen auf dieser Registerkarte können Sie festlegen, dass keine Incidents erstellt oder mehrere Warnungen zu einem einzelnen Incident gruppiert werden.

> [!IMPORTANT]
> Die Registerkarte „Incidenteinstellungen“ befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Beispiel:

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definieren der Einstellungen für Incidenterstellung und Warnungsgruppierung":::

### <a name="incident-settings"></a>Incidenteinstellungen

Im Bereich **Incidenteinstellungen** ist die Option **Incidents aus Warnungen erstellen, die von dieser Analyseregel ausgelöst werden** standardmäßig auf **Aktiviert** festgelegt. Dies bedeutet, dass in Microsoft Sentinel ein separater Vorfall von jeder einzelnen Warnung erstellt wird, die von der Regel ausgelöst wird.

- Wenn durch diese Regel nicht alle Incidents erstellt werden sollen (z. B. wenn Sie mit dieser Regel nur Informationen für die nachfolgende Analyse sammeln möchten), legen Sie diese Einstellung auf **Deaktiviert** fest.

- Informationen dazu, wie statt einem Vorfall für jede einzelne Warnung ein einzelner Vorfall aus einer Gruppe von Warnungen erstellt wird, finden Sie im nächsten Abschnitt.

### <a name="alert-grouping"></a>Warnungsgruppierung

Wenn aus einer Gruppe von bis zu 150 ähnlichen oder wiederkehrenden Warnungen (siehe Hinweis) ein einzelner Incident erstellt werden soll, legen Sie im Abschnitt **Warnungsgruppierung** die Option **Alle verwandten Warnungen, die durch diese Analyseregel ausgelöst werden, in Incidents gruppieren** auf **Aktiviert** fest, und legen Sie die folgenden Parameter fest.

- **Gruppe auf Warnungen beschränken, die innerhalb des ausgewählten Zeitraums erstellt werden**: Bestimmen Sie den Zeitraum, in dem ähnliche oder wiederkehrende Warnungen gruppiert werden sollen. Alle entsprechenden Warnungen innerhalb dieses Zeitraums generieren zusammen einen Incident oder eine Gruppe von Incidents (abhängig von den unten aufgeführten Gruppierungseinstellungen). Bei Warnungen außerhalb dieses Zeitraums wird ein separater Incident oder eine Reihe von Incidents generiert.

- **Von dieser Analyseregel ausgelöste Warnungen in einem einzigen Incident zusammenfassen und gruppieren nach**: Wählen Sie die Grundlage für die Gruppierung der Warnungen aus:

    | Option | BESCHREIBUNG |
    | ------- | ---------- |
    | **Warnungen in einem einzigen Incident gruppieren, wenn alle Entitäten übereinstimmen** | Warnungen werden gruppiert, wenn sie identische Werte für jede der zugeordneten Entitäten aufweisen (weiter oben definiert auf der Registerkarte [Regellogik festlegen](#define-the-rule-query-logic-and-configure-settings)). Dies ist die empfohlene Einstellung. |
    | **Alle von dieser Regel ausgelösten Warnungen in einem einzigen Incident gruppieren** | Alle von dieser Regel generierten Warnungen werden zusammengefasst, auch wenn sie keine identischen Werte haben. |
    | **Gruppieren von Warnungen in einem einzigen Incident, wenn die ausgewählten Entitäten und Details übereinstimmen** | Warnungen werden gruppiert, wenn sie identische Werte für alle zugeordneten Entitäten, Warnungsdetails und benutzerdefinierten Details aufweisen, die in den entsprechenden Dropdownlisten ausgewählt wurden.<br><br>Sie können diese Einstellung verwenden, wenn Sie z. B. separate Incidents basierend auf der Quell- oder Ziel-IP-Adresse erstellen oder Warnungen mit einer bestimmten Entität und einem bestimmten Schweregrad gruppieren möchten.<br><br>**Hinweis**: Wenn Sie diese Option auswählen, muss mindestens ein Entitätstyp oder ein Feld für die Regel ausgewählt sein. Andernfalls ist die Regelüberprüfung nicht erfolgreich, und die Regel wird nicht erstellt. |
    |

- **Geschlossene übereinstimmende Incidents erneut öffnen**: Wenn ein Incident gelöst oder geschlossen wurde und später eine weitere Warnung generiert wird, die zu diesem Incident gehören würde, haben Sie folgende Möglichkeiten: Legen Sie diese Einstellung auf **Aktiviert** fest, wenn der geschlossene Incident erneut geöffnet werden soll, oder übernehmen Sie die Einstellung **Deaktiviert**, wenn durch die Warnung ein neuer Incident erzeugt werden soll.

    > [!NOTE]
    > **Bis zu 150 Warnungen** können in einem einzelnen Vorfall gruppiert werden. Wenn mehr als 150 Warnungen von einer Regel generiert werden, die sie zu einem einzelnen Incident gruppiert, wird ein neuer Incident mit denselben Incidentinformationen wie der ursprüngliche Incident generiert, und die überzähligen Warnungen werden in dem neuen Incident gruppiert.

## <a name="set-automated-responses-and-create-the-rule"></a>Festlegen automatisierter Antworten und Erstellen der Regel

1. Auf der Registerkarte **Automatisierte Reaktionen** können Sie die Automatisierung auf Grundlage der von dieser Analyseregel generierten Warnungen oder auf Grundlage des durch die Warnungen erzeugten Incidents festlegen.
    - Wählen Sie in der Dropdownliste unter **Automatisierung von Warnungen** die Playbooks aus, die Sie automatisch ausführen möchten, wenn eine Warnung generiert wird.
    - Für die incidentbasierte Automatisierung wählen oder erstellen Sie unter **Incidentautomatisierung (Vorschau)** eine Automatisierungsregel. Sie können Playbooks (die auf dem **Incidenttrigger** basieren) über diese Automatisierungsregeln aufrufen sowie Selektierung, Zuweisung und Abschluss automatisieren.
    - Weitere Informationen und Anweisungen zum Erstellen von Playbooks und Automatisierungsregeln finden Sie unter [Automatisieren von Reaktionen auf Bedrohungen](tutorial-respond-threats-playbook.md#automate-threat-responses).
    - Weitere Informationen darüber, wann Sie den **Warnungstrigger** oder **Incidenttrigger** verwenden sollten, finden Sie unter [Verwenden von Triggern und Aktionen in Microsoft Sentinel-Playbooks](playbook-triggers-actions.md#microsoft-sentinel-triggers-summary).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Definieren der Einstellungen für automatisierte Antworten":::

1. Wählen Sie **Überprüfen und erstellen** aus, um alle Einstellungen für die neue Warnungsregel zu überprüfen. Nachdem die Meldung „Überprüfung erfolgreich“ angezeigt wurde, wählen Sie **Erstellen** aus, um die Warnungsregel zu initialisieren.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Überprüfen aller Einstellungen und Erstellen der Regel":::

## <a name="view-the-rule-and-its-output"></a>Anzeigen der Regel und ihrer Ausgabe
  
- Die neu erstellte benutzerdefinierte Regel (vom Typ „Geplant“) finden Sie in der Tabelle auf der Registerkarte **Aktive Regeln** auf dem Hauptbildschirm von **Analysen**. Über diese Liste können Sie die einzelnen Regeln aktivieren, deaktivieren oder löschen.

- Die Ergebnisse der von Ihnen erstellten Warnungsregeln können Sie auf der Seite **Vorfälle** anzeigen. Hier können Sie Bedrohungen eingrenzen, [Vorfälle untersuchen](investigate-cases.md) und die Bedrohungen beseitigen.

- Sie können die Regelabfrage aktualisieren, um falsch positive Ergebnisse auszuschließen. Weitere Informationen finden Sie unter [Behandeln von falsch positiven Ergebnissen in Microsoft Sentinel](false-positives.md)

> [!NOTE]
> In Microsoft Sentinel generierte Warnungen stehen über [Microsoft Graph Security](/graph/security-concept-overview) zur Verfügung. Weitere Informationen finden Sie unter [Verwenden der Sicherheits-API von Microsoft Graph](/graph/api/resources/security-api-overview).

## <a name="export-the-rule-to-an-arm-template"></a>Exportieren der Regel in eine ARM-Vorlage

Wenn Sie Ihre Regel packen möchten, damit sie verwaltet und als Code bereitgestellt wird, können Sie die [Regel problemlos in eine ARM-Vorlage (Azure Resource Manager) exportieren](import-export-analytics-rules.md). Sie können auch Regeln aus Vorlagendateien importieren und dann auf der Benutzeroberfläche anzeigen und bearbeiten.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="issue-no-events-appear-in-query-results"></a>Problem: Keine Ereignisse in den Abfrageergebnissen

Wenn **Ereignisgruppierung** auf **Warnung für jedes Ereignis auslösen** festgelegt ist, kann es in bestimmten Szenarien vorkommen, dass bei einem späteren Anzeigen der Abfrageergebnisse (z. B. bei der Pivotierung von Warnungen eines Incidents) keine Abfrageergebnisse angezeigt werden. Dies liegt daran, dass die Verknüpfung zwischen Ereignis und Warnung durch Hashing der Informationen des jeweiligen Ereignisses und das Einfügen des Hash in die Abfrage erreicht wird. Wenn die Abfrageergebnisse seit der Generierung der Warnung geändert wurden, ist der Hash nicht mehr gültig, und es werden keine Ergebnisse angezeigt.

Um die Ereignisse anzuzeigen, entfernen Sie die Zeile mit dem Hash aus der Abfrage der Regel und führen die Abfrage aus.

> [!NOTE]
> Dieses Problem wurde behoben, indem ein neues Feld (**OriginalQuery**) zu den Ergebnissen hinzugefügt wird, wenn diese Ereignisgruppierungsoption ausgewählt ist. Weitere Informationen finden Sie in der obigen [Beschreibung](#event-grouping-and-rule-suppression).

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problem: Fehler bei der Ausführung einer geplanten Regel oder beim Hinzufügen von „AUTO DISABLED“ zum Namen

Ein Fehler bei der Ausführung einer geplanten Abfrage ist zwar selten, aber nicht ausgeschlossen. Microsoft Sentinel klassifiziert Fehler auf der Grundlage des spezifischen Fehlertyps und der Umstände, die dazu geführt haben, als vorübergehende oder dauerhafte Fehler.

#### <a name="transient-failure"></a>Vorübergehender Fehler

Ein vorübergehender Fehler tritt aufgrund einer temporären Situation auf, die sich bald wieder normalisiert. Zu diesem Zeitpunkt wird die Regelausführung erfolgreich angewendet. Dies sind einige Beispiele für Fehler, die von Microsoft Sentinel als vorübergehend klassifiziert werden:

- Die Ausführung einer Regelabfrage dauert zu lang und führt zu einem Timeout.
- Es bestehen Konnektivitätsprobleme zwischen Datenquellen und Log Analytics bzw. zwischen Log Analytics und Microsoft Sentinel.
- Alle anderen neuen und unbekannten Fehler werden als vorübergehende Fehler eingestuft.

Wenn ein vorübergehender Fehler auftritt, versucht Microsoft Sentinel weiterhin, die Regel gemäß den vordefinierten und immer kürzeren Intervallen bis zu einem bestimmten Punkt noch mal auszuführen. Danach wird die Regel nur zum nächsten geplanten Zeitpunkt wieder ausgeführt. Eine Regel wird aufgrund eines vorübergehenden Fehlers nie automatisch deaktiviert.

#### <a name="permanent-failure---rule-auto-disabled"></a>Dauerhafter Fehler: Regel automatisch deaktiviert

Ein dauerhafter Fehler tritt aufgrund einer Änderung der Bedingungen auf, die normalerweise die Ausführung der Regel ermöglichen und ohne menschliches Eingreifen nicht wiederhergestellt werden können. Im Folgenden finden Sie einige Beispiele für Fehler, die als permanente Fehler klassifiziert werden:

- Der Zielarbeitsbereich (für den die Regelabfrage ausgeführt wurde) wurde gelöscht.
- Die Zieltabelle (für die die Regelabfrage ausgeführt wurde) wurde gelöscht.
- Microsoft Sentinel wurde aus dem Zielarbeitsbereich entfernt.
- Eine von der Regelabfrage verwendete Funktion ist nicht mehr gültig. Sie wurde entweder geändert oder entfernt.
- Die Berechtigungen für eine der Datenquellen der Regelabfrage wurden geändert.
- Eine der Datenquellen der Regelabfrage wurde gelöscht oder getrennt.

**Im Fall einer vordefinierten Anzahl aufeinanderfolgender dauerhafter Fehler mit demselben Typ und derselben Regel** versucht Microsoft Sentinel nicht mehr, die Regel auszuführen und führt stattdessen die folgenden Schritte aus:

- Die Regel wird deaktiviert.
- Die Wörter **AUTO DISABLED** werden am Anfang des Regelnamens hinzugefügt.
- Der Grund für den Fehler (und die Deaktivierung) wird der Regelbeschreibung hinzugefügt.

Sie können mühelos erkennen, ob automatisch deaktivierte Regeln vorhanden sind, indem Sie die Regelliste nach Namen sortieren. Die automatisch deaktivierten Regeln werden am Anfang der Liste angezeigt.

SOC-Manager sollten sicherstellen, dass die Regelliste regelmäßig auf automatisch deaktivierte Regeln überprüft wird.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Analyseregeln zum Erkennen von Bedrohungen durch Microsoft Sentinel verwenden, stellen Sie sicher, dass Sie alle Regeln aktivieren, die Ihren verbundenen Datenquellen zugeordnet sind, um eine vollständige Sicherheitsabdeckung für Ihre Umgebung zu gewährleisten. Die effizienteste Möglichkeit zum Aktivieren von Analyseregeln besteht direkt auf der Seite des Datenconnectors, auf der alle zugehörigen Regeln aufgelistet sind. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zu Datenquellen](connect-data-sources.md).

Sie können auch Regeln per [API](/rest/api/securityinsights/) und [PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0) an Microsoft Sentinel pushen, obwohl dies zusätzlichen Aufwand mit sich bringt. Wenn Sie die API oder PowerShell verwenden, müssen Sie die Regeln zunächst in JSON exportieren, bevor Sie die Regeln aktivieren. Eine API oder PowerShell kann hilfreich sein, wenn Sie Regeln in mehreren Instanzen von Microsoft Sentinel mit identischen Einstellungen in jeder Instanz aktivieren.

Weitere Informationen finden Sie unter:

Weitere Informationen finden Sie unter:

- [Tutorial: Untersuchen von Incidents mit Microsoft Sentinel](investigate-cases.md)
- [Klassifizieren und Analysieren von Daten mithilfe von Entitäten in Microsoft Sentinel](entities-in-azure-sentinel.md)
- [Tutorial: Verwenden von Playbooks mit Automatisierungsregeln in Microsoft Sentinel](tutorial-respond-threats-playbook.md)

Erfahren Sie außerdem anhand eines Beispiels, wie benutzerdefinierte Analyseregeln bei der [Überwachung von Zoom](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) mit einem [benutzerdefinierten Connector](create-custom-connector.md) eingesetzt werden.
