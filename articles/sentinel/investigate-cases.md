---
title: Untersuchen von Vorfällen mit Microsoft Sentinel| Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mit Microsoft Sentinel erweiterte Warnungsregeln erstellen, die Vorfälle generieren, die Sie zuweisen und untersuchen können.
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
ms.openlocfilehash: 77cf1b6dd4038613af9d079fc6226ab8c784577a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520140"
---
# <a name="investigate-incidents-with-microsoft-sentinel"></a>Untersuchen Sie Vorfälle mit Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Entsprechend gekennzeichnete Features sind derzeit als VORSCHAUVERSION verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

Dieser Artikel unterstützt Sie bei der Untersuchung von Vorfällen mit Microsoft Sentinel. Nachdem Sie Ihre Datenquellen mit Microsoft Sentinel verbunden haben, möchten Sie benachrichtigt werden, wenn etwas Verdächtiges geschieht. Um dies zu ermöglichen, können Sie mit Microsoft Sentinel erweiterte Warnregeln erstellen, die Vorfälle erzeugen, die Sie zuordnen und untersuchen können.

In diesem Artikel wird Folgendes behandelt:
> [!div class="checklist"]
> * Untersuchen von Vorfällen
> * Verwenden des Untersuchungsdiagramms
> * Reagieren auf Bedrohungen

Ein Vorfall kann mehrere Warnungen enthalten. Es ist eine Aggregation aller relevanten Beweise für eine bestimmte Untersuchung. Ein Vorfall wird auf der Grundlage von Analyseregeln erstellt, die Sie auf der Seite **Analytics** erstellt haben. Die Eigenschaften, die zu den Warnungen gehören, z. B. Schweregrad und Status, werden auf Vorfallsebene festgelegt. Nachdem Sie Microsoft Sentinel mitgeteilt haben, nach welchen Arten von Bedrohungen Sie suchen und wie Sie diese finden, können Sie erkannte Bedrohungen durch die Untersuchung von Vorfällen überwachen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie können den Vorfall nur untersuchen, wenn Sie beim Einrichten der Analyseregel die Entitätszuordnungsfelder verwendet haben. Das Untersuchungsdiagramm fordert, dass Ihr ursprünglicher Vorfall Entitäten enthält.

- Wenn ein Gastbenutzer Vorfälle zuweisen muss, muss diesem im Azure AD-Mandanten die Rolle [Verzeichnisleseberechtigter](../active-directory/roles/permissions-reference.md#directory-readers) zugewiesen werden. Regulären Benutzern (keine Gastbenutzer) ist diese Rolle standardmäßig zugewiesen.

## <a name="how-to-investigate-incidents"></a>Untersuchen von Vorfällen

1. Wählen Sie **Vorfälle** aus. Auf der Seite **Vorfälle** werden Sie darüber informiert, wie viele Vorfälle vorhanden sind, wie viele offen sind, für wie viele Sie **In progress** (In Arbeit) festgelegt haben, und wie viele geschlossen wurden. Für jeden Vorfall wird angezeigt, wann er aufgetreten ist, sowie der Vorfallsstatus. Beurteilen Sie anhand des Schweregrads, welche Fälle zuerst zu bearbeiten sind.

    ![Anzeigen des Schweregrads von Vorfällen](media/tutorial-investigate-cases/incident-severity.png)

1. Sie können die Vorfälle nach Bedarf filtern, z. B. nach Status oder Schweregrad. Weitere Informationen finden Sie unter [Suchen nach Incidents](#search-for-incidents).

1. Wählen Sie einen bestimmten Vorfall aus, um mit der Untersuchung zu beginnen. Rechts sehen Sie ausführliche Informationen zum Vorfall, einschließlich des Schweregrads, einer Zusammenfassung zur Anzahl der betroffenen Entitäten, der ursprünglichen Ereignisse, die diesen Vorfall ausgelöst haben, sowie der eindeutigen ID des Vorfalls.

1. Wenn Sie sich mehr Details zu den Warnungen und Entitäten des Vorfalls ansehen möchten, wählen Sie auf der Vorfallsseite auf **Alle Informationen anzeigen** aus, und prüfen Sie die relevanten Registerkarten, in denen die Vorfallsinformationen zusammengefasst sind. 

    ![Anzeigen von Warnungsdetails](media/tutorial-investigate-cases/incident-timeline.png)

    Beispiel:

    - Überprüfen Sie die Zeitachse der Warnungen und Lesezeichen im Vorfall auf der Registerkarte **Zeitachse**. Dies hilft Ihnen, die Zeitachse der Angreiferaktivitäten zu rekonstruieren.
    - Überprüfen Sie auf der Registerkarte **Warnungen** die Warnung selbst. Sie können sich alle relevanten Informationen zur Warnung ansehen: die Abfrage, die die Warnung ausgelöst hat, die Anzahl der Ergebnisse, die pro Abfrage zurückgegeben wurde, und die Möglichkeit, Playbooks für die Warnungen auszuführen. Wenn Sie noch detaillierte Informationen zum Vorfall erhalten möchten, wählen Sie die Anzahl der **Ereignisse** aus. Dadurch werden die Abfrage, die die Ergebnisse generiert hat, sowie die Ereignisse geöffnet, die die Warnung in Log Analytics ausgelöst haben. 
    - In der Registerkarte **Entitäten** können Sie sich alle Entitäten ansehen, die Sie als Teil der Warnungsregeldefinition zugeordnet haben.

1. Wenn Sie einen Vorfall aktiv untersuchen, empfiehlt es sich, den Vorfallsstatus auf **In Bearbeitung** festzulegen, bis Sie ihn schließen.

1. Vorfälle können einem bestimmten Benutzer zugewiesen werden. Jedem Vorfall können Sie einen Besitzer zuweisen, indem Sie das Feld **Incident Owner** (Besitzer des Vorfalls) festlegen. Alle Vorfälle sind zu Beginn nicht zugewiesen. Sie können auch Kommentare hinzufügen, damit andere Analysten verstehen können, was Sie untersucht haben und welche Probleme mit dem Vorfall verbunden sind.

    ![Vorfall einem Benutzer zuweisen](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Wählen Sie **Untersuchen** aus, um die Untersuchungszuordnung anzuzeigen.



## <a name="use-the-investigation-graph-to-deep-dive"></a>Ausführliche Untersuchung mit dem Untersuchungsdiagramm

Mithilfe des Untersuchungsdiagramms können Analysten für jede Untersuchung die richtigen Fragen stellen. Anhand des Untersuchungsdiagramms können Sie den Umfang einer potenziellen Sicherheitsbedrohung verstehen und deren Ursache bestimmen, indem Sie relevante Daten mit einer betroffenen Entität in Beziehung setzen. Sie können tiefer eintauchen und die einzelnen im Diagramm dargestellten Entitäten untersuchen, indem Sie die jeweilige Entität auswählen und verschiedene Erweiterungsoptionen wählen.  
  
Das Untersuchungsdiagramm bietet Ihnen Folgendes:

- **Visueller Kontext aus Rohdaten**: Im visuellen Livediagramm werden Entitätsbeziehungen veranschaulicht, die automatisch aus den Rohdaten extrahiert werden. Dadurch können Sie auf einfache Weise Verbindungen zwischen verschiedenen Datenquellen erkennen.

- **Ermittlung des vollständigen Untersuchungsumfangs**: Erweitern Sie den Untersuchungsumfang mithilfe integrierter Auswertungsabfragen, um das vollständige Ausmaß einer Sicherheitsverletzung aufzudecken.

- **Integrierte Untersuchungsschritte**: Stellen Sie mit vordefinierten Untersuchungsoptionen sicher, dass Sie angesichts einer Bedrohung die richtigen Fragen stellen.

So verwenden Sie das Untersuchungsdiagramm:

1. Wählen Sie einen Vorfall und anschließend **Untersuchen** aus. Dadurch wird das Untersuchungsdiagramm aufgerufen. Im Diagramm erhalten Sie eine anschauliche Zuordnung der Entitäten, die direkt mit dem Vorfall verbunden sind, sowie aller darüber hinaus damit verbundenen Ressourcen.


    [ ![Anzeigen der Karte.](media/tutorial-investigate-cases/investigation-map.png) ](media/tutorial-investigate-cases/investigation-map.png#lightbox)

   > [!IMPORTANT] 
   > - Sie können den Vorfall nur untersuchen, wenn Sie beim Einrichten der Analyseregel die Entitätszuordnungsfelder verwendet haben. Das Untersuchungsdiagramm fordert, dass Ihr ursprünglicher Vorfall Entitäten enthält.
   >
   > - Microsoft Sentinel unterstützt derzeit die Untersuchung von **Vorfällen, die bis zu 30 Tage alt sind**.


1. Wählen Sie eine Entität aus, um den Bereich **Entitäten** zu öffnen, in dem Sie Informationen zur betreffenden Entität untersuchen können.

    ![Entitäten in der Zuordnung anzeigen](media/tutorial-investigate-cases/map-entities.png)
  
1. Erweitern Sie die Untersuchung, indem Sie den Mauszeiger über die einzelnen Entitäten bewegen. Dadurch wird eine Liste von Fragen eingeblendet, die von unseren Sicherheitsexperten und -analysten für den jeweiligen Entitätstyp entwickelt wurden, sodass Sie Ihre Untersuchung vertiefen können. Wir bezeichnen diese Optionen als **Erkundungsabfragen**.

    ![Weitere Details](media/tutorial-investigate-cases/exploration-cases.png)

   Sie können beispielsweise für einen Computer zugehörige Warnungen anfordern. Wenn Sie eine Erkundungsabfrage auswählen, werden die erhaltenen Entitäten dem Diagramm wieder hinzugefügt. In diesem Beispiel wurden durch Auswahl von **Related alerts** (Zugehörige Warnungen) die folgenden Warnungen in das Diagramm zurückgegeben:

    ![Zugehörige Warnungen anzeigen](media/tutorial-investigate-cases/related-alerts.png)

1. Für jede Erkundungsabfrage können Sie die Ergebnisse zu ursächlichen Ereignissen und die in Log Analytics verwendete Abfrage öffnen, indem Sie **Ereignisse\>** auswählen.

1. Das Diagramm enthält eine parallele Zeitachse, um ein Verständnis des Vorfalls zu vermitteln.

    ![Zeitachse in Diagramm anzeigen](media/tutorial-investigate-cases/map-timeline.png)

1. Bewegen Sie den Mauszeiger über die Zeitachse, um festzustellen, welche Ereignisse im Diagramm zu welchem Zeitpunkt aufgetreten sind.

    ![Warnungen anhand von Zeitachse in Diagramm untersuchen](media/tutorial-investigate-cases/use-timeline.png)


## <a name="closing-an-incident"></a>Schließen eines Incidents

Nachdem Sie einen bestimmten Incident gelöst haben (beispielsweise dann, wenn die Untersuchung abgeschlossen wurde), sollten Sie den Status des Incidents auf **Geschlossen** festlegen. Dabei werden Sie gefragt, ob Sie den Incident klassifizieren möchten, indem Sie den Grund für die Schließung angeben. Dieser Schritt ist obligatorisch. Klicken Sie auf **Klassifizierung auswählen**, und wählen Sie aus der Dropdownliste eine der folgenden Optionen aus:

- Richtig positiv – verdächtige Aktivität
- Unschädlich positiv – verdächtig, aber erwartet
- Falsch positiv – falsche Warnungslogik
- Falsch positiv – falsche Daten
- Unbestimmt

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Screenshot, der die in der Liste „Klassifizierung auswählen“ verfügbaren Klassifizierungen hervorhebt.":::

Weitere Informationen zu falsch positiven und unbedenklichen positiven Ergebnissen finden Sie unter [Behandeln falsch positiver Ergebnisse in Microsoft Sentinel](false-positives.md)

Nachdem Sie eine geeignete Klassifizierung ausgewählt haben, fügen Sie in das Feld **Kommentar** einen beschreibenden Text hinzu. Dies ist nützlich, falls Sie sich noch einmal auf diesen Incident beziehen müssen. Wenn Sie fertig sind, klicken Sie auf **Anwenden**. Der Incident wird geschlossen.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alt-text}":::

## <a name="search-for-incidents"></a>Suchen nach Incidents

Um einen bestimmten Incident schnell zu finden, geben Sie eine Suchzeichenfolge in das Suchfeld oberhalb des Rasters „Incidents“ ein, und drücken Sie die **EINGABETASTE**, um die Liste der angezeigten Incidents entsprechend zu ändern. Wenn Ihr Incident nicht in den Ergebnissen enthalten ist, können Sie Ihre Suche mithilfe der **erweiterten Suchoptionen** einschränken.

Zum Ändern der Suchparameter wählen Sie die Schaltfläche **Suchen** und dann die Parameter aus, für die Sie die Suche ausgeführen möchten.

Zum Beispiel:

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="Screenshot: Feld und Schaltfläche für die Incidentsuche, um grundlegende und/oder erweiterte Suchoptionen auszuwählen":::

Standardmäßig werden Incidentsuchvorgänge nur für die Werte **Incident-ID**, **Titel**, **Tags**, **Besitzer** und **Produktname** ausgeführt. Scrollen Sie im Suchbereich in der Liste nach unten, um einen oder mehrere weitere Parameter für die Suche auszuwählen, und wählen Sie **Anwenden** aus, um die Suchparameter zu aktualisieren. Wählen Sie **Auf Standard festlegen**, um die ausgewählten Parameter auf die Standardoption zurückzusetzen.


> [!NOTE]
> Bei der Suche im Feld **Besitzer** werden sowohl Namen als auch E-Mail-Adressen unterstützt.
>

Die Verwendung erweiterter Suchoptionen ändert das Suchverhalten wie folgt:

|Suchverhalten  |BESCHREIBUNG  |
|---------|---------|
|**Farbe der Suchschaltfläche**     |Die Farbe der Suchschaltfläche ändert sich abhängig von den Parametertypen, die jeweils in der Suche verwendet werden. <br><br>– Solange nur die Standardparameter ausgewählt sind, ist die Schaltfläche grau. <br>– Sobald verschiedene Parameter ausgewählt sind, z. B. erweiterte Suchparameter, wird die Schaltfläche blau.         |
|**Automatische Aktualisierung**     | Die Verwendung von erweiterten Suchparametern verhindert, dass Sie die automatische Aktualisierung Ihrer Ergebnisse auswählen können.        |
|**Entitätsparameter**     |Alle Entitätsparameter werden für erweiterte Suchvorgänge unterstützt. Bei der Suche in einem Entitätsparameter wird die Suche in allen Entitätsparametern ausgeführt.         |
|**Suchzeichenfolgen**     |    Die Suche nach einer Zeichenfolge enthält alle Wörter in der Suchabfrage. Bei Suchzeichenfolgen muss die Groß-/Kleinschreibung beachtet werden.     |
|**Arbeitsbereichsübergreifende Unterstützung**     |    Erweiterte Suchvorgänge werden für arbeitsbereichsübergreifende Ansichten nicht unterstützt.     |
| **Anzahl der angezeigten Suchergebnisse** | Wenn Sie erweiterte Suchparameter verwenden, werden jeweils nur 50 Ergebnisse angezeigt. |
|     |         |

> [!NOTE]
> Die erweiterte Suche befindet sich derzeit in der öffentlichen Vorschauphase.
>

> [!TIP]
>  Wenn Sie den gesuchten Incident nicht finden können, entfernen Sie Suchparameter, um Ihre Suche zu erweitern. Wenn Ihre Suchergebnisse zu viele Elemente enthalten, fügen Sie weitere Filter hinzu, um Ihre Ergebnisse einzugrenzen.
>


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie mit der Untersuchung von Vorfällen mit Microsoft Sentinel beginnen. Weitere Informationen finden Sie unter

- [Tutorial: Verwenden von Playbooks mit Automatisierungsregeln in Microsoft Sentinel](tutorial-respond-threats-playbook.md)
- [Untersuchen von Incidents mit UEBA-Daten](investigate-with-ueba.md)
