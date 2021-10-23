---
title: Analysieren von Kundenverhalten in Application Insights | Microsoft-Dokumentation
description: 'Tutorial: Verwenden von Application Insights zur Analyse der Kundeninteraktion mit Ihrer Anwendung.'
ms.topic: tutorial
ms.date: 07/30/2021
ms.custom: mvc
ms.openlocfilehash: f06522ffe257bc7e20fe587b7c0a4479f6677240
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129622"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Verwenden von Azure Application Insights zur Analyse der Kundeninteraktion mit Ihrer Anwendung

 Application Insights erfasst Nutzungsinformationen, mit denen Sie nachvollziehen können, wie Benutzer mit Ihrer Anwendung interagieren.  In diesem Tutorial werden verschiedene Ressourcen vorgestellt, die zur Analyse dieser Informationen verwendet werden können.  Sie lernen Folgendes:

> [!div class="checklist"]
> * Analysieren von Daten über Benutzer, die auf Ihre Anwendung zugreifen
> * Verwenden von Sitzungsinformationen zur Analyse der Kundeninteraktion mit Ihrer Anwendung
> * Definieren von Trichtern, mit denen Sie die gewünschte Benutzeraktivität mit der tatsächlichen Aktivität vergleichen können 
> * Erstellen einer Arbeitsmappe, mit der Visualisierungen und Abfragen in einem einzigen Dokument zusammengefasst werden
> * Gruppieren von ähnlichen Benutzern für eine Gesamtanalyse
> * Ermitteln von Benutzern, die Ihre Anwendung wiederholt nutzen
> * Analysieren der Benutzernavigation in Ihrer Anwendung


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit den folgenden Workloads:
    - ASP.NET und Webentwicklung
    - Azure-Entwicklung
- Laden Sie den [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger) herunter, und installieren Sie diesen.
- Stellen Sie eine .NET-Anwendung in Azure bereit, und [aktivieren Sie das Application Insights SDK](../app/asp-net.md). 
- [Senden Sie Telemetriedaten aus Ihrer Anwendung](../app/usage-overview.md#send-telemetry-from-your-app) zum Hinzufügen von benutzerdefinierten Ereignis-/Seitenansichten.
- Senden Sie [Benutzerkontext-IDs](./usage-overview.md), um Benutzeraktivitäten über einen bestimmten Zeitraum nachzuverfolgen und alle Nutzungsfunktionen zu verwenden.

## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="get-information-about-your-users"></a>Auswerten von Benutzerdaten
Im Bereich **Benutzer** können Sie wichtige Benutzerdaten auf unterschiedliche Weise analysieren. Beispielsweise können Sie hier benutzerbezogene Informationen wie den Ort der Verbindungsherstellung, Clientdaten und bestimmte Anwendungsbereiche nachvollziehen, auf die zugegriffen wurde. 

1. Wählen Sie in Ihrer Application Insights-Ressource unter *Nutzung* die Option **Benutzer** im Menü aus.
2. In der Standardansicht wird die Anzahl der individuellen Benutzer angezeigt, die sich in den letzten 24 Stunden mit Ihrer Anwendung verbunden haben.  Sie können das Zeitfenster ändern und verschiedene andere Kriterien festlegen, um diese Informationen zu filtern.

3. Klicken Sie auf die Dropdownliste **Während**, und legen Sie als Zeitfenster sieben Tage fest.  Hierdurch werden in den unterschiedlichen Diagrammen im Bereich mehr Daten angezeigt.

4. Klicken Sie auf die Dropdownliste **Split by** (Trennen nach), um sich im Diagramm eine Aufschlüsselung nach Benutzereigenschaft anzeigen zu lassen.  Wählen Sie aus der Dropdownliste **Land oder Region** einen entsprechenden Eintrag aus.  Im Diagramm werden nun zwar dieselben Daten angezeigt, jedoch können Sie sich eine Aufschlüsselung nach der Anzahl der Benutzer jedes Landes/jeder Region anzeigen lassen.

      :::image type="content" source="./media/tutorial-users/user-1.png" alt-text="Screenshot des Abfrage-Generators der Registerkarte „Benutzer“." lightbox="./media/tutorial-users/user-1.png":::

5. Positionieren Sie den Cursor über verschiedenen Balken im Diagramm. Beachten Sie hierbei, dass sich die angezeigte Anzahl für jedes Land/jede Region nur auf das Zeitfenster bezieht, das von diesem Balken dargestellt wird.
6. Wählen Sie **Weitere Erkenntnisse anzeigen** aus, um weitere Informationen zu erfahren. 

      :::image type="content" source="./media/tutorial-users/user-2.png" alt-text="Screenshot der Registerkarte „Benutzer“ mit weiteren Erkenntnissen." lightbox="./media/tutorial-users/user-2.png":::


## <a name="analyze-user-sessions"></a>Analysieren von Benutzersitzungen
Der Bereich **Sessions** (Sitzungen) ist vergleichbar mit dem Bereich **Benutzer**.  Während Sie im Bereich **Benutzer** Daten über Benutzer analysieren können, die auf Ihre Anwendung zugreifen, hilft Ihnen der Bereich **Sessions** (Sitzungen) dabei, nachzuvollziehen, wie diese Benutzer die Anwendung verwenden.  

1. Wählen Sie unter *Nutzung* die Option **Sitzungen** aus.
2. Beachten Sie, dass für das Diagramm dieselben Optionen zum Filtern und Unterteilen der Daten wie im Bereich **Benutzer** verfügbar sind.

     :::image type="content" source="./media/tutorial-users/sessions.png" alt-text="Screenshot der Registerkarte „Sitzungen“ mit einem Balkendiagramm." lightbox="./media/tutorial-users/sessions.png":::

4. Um die Sitzungschronik anzuzeigen, wählen Sie **Weitere Erkenntnisse anzeigen** aus, und wählen Sie dann unter den aktiven Sitzungen die Option **Sitzungschronik anzeigen** für eine der Chroniken aus. Die Sitzungschronik zeigt jede Aktion in den Sitzungen an. Auf diese Weise lassen sich Informationen wie z.B. Sitzungen mit einer großen Anzahl von Ausnahmen identifizieren.

     :::image type="content" source="./media/tutorial-users/timeline.png" alt-text="Screenshot der Registerkarte „Sitzungen“ mit einer ausgewählten Zeitachse." lightbox="./media/tutorial-users/timeline.png":::

## <a name="group-together-similar-users"></a>Gruppieren von ähnlichen Benutzern
Eine **Kohorte** umfasst Benutzer, die nach ähnlichen Eigenschaften gruppiert werden.  Sie können Kohorten zum Filtern von Daten in anderen Bereichen verwenden, um bestimmte Benutzergruppen zu analysieren.  Beispielsweise lassen sich so nur Benutzer analysieren, die einen Kaufvorgang abgeschlossen haben.

1.  Wählen Sie oben auf einer der Nutzungsregisterkarten („Benutzer“, „Sitzungen“, „Ereignisse“ usw.) die Option **Kohorte erstellen** aus.

1.  Wählen Sie eine Vorlage aus dem Katalog aus.

    :::image type="content" source="./media/tutorial-users/cohort.png" alt-text="Screenshot des Vorlagenkatalogs für Kohorten." lightbox="./media/tutorial-users/cohort.png":::
1.  Bearbeiten Sie Ihre Kohorte, und wählen Sie dann **Speichern** aus.
1.  Um Ihre Kohorte anzuzeigen, wählen Sie sie im Dropdownmenü **Anzeigen** aus. 

    :::image type="content" source="./media/tutorial-users/cohort-2.png" alt-text="Screenshot des Dropdowns „Anzeigen“ mit angezeigter Kohorte." lightbox="./media/tutorial-users/cohort-2.png":::


## <a name="compare-desired-activity-to-reality"></a>Vergleichen von gewünschter und tatsächlicher Aktivität
Während der Fokus in den vorherigen Bereichen auf dem tatsächlichen Benutzerverhalten lag, verschiebt sich dieser über **Trichter** nun auf das gewünschte Benutzerverhalten.  Ein Trichter setzt sich aus mehreren Schritten Ihrer Anwendung und dem Prozentsatz der Benutzer zusammen, der zwischen den einzelnen Schritten navigiert.  Sie können z.B. einen Trichter erstellen, mit dem der Prozentsatz der Benutzer erfasst wird, der eine Verbindung zu Ihrer Anwendung herstellt, um nach einem Produkt zu suchen.  Anschließend wird Ihnen der prozentuale Anteil der Benutzer angezeigt, der das Produkt zum Einkaufswagen hinzugefügt und ggf. den Kaufvorgang abgeschlossen hat.

1. Klicken Sie im Menü zuerst auf **Trichter** und anschließend auf **Bearbeiten**. 

3. Erstellen Sie einen Trichter mit mindestens zwei Schritten, indem Sie für jeden Schritt eine Aktion auswählen.  Die Liste der Aktionen basiert auf den von Application Insights gesammelten Nutzungsdaten.

    :::image type="content" source="./media/tutorial-users/funnel.png" alt-text="Screenshot der Registerkarte „Trichter“ und Auswählen von Schritten auf der Registerkarte „Bearbeiten“" lightbox="./media/tutorial-users/funnel.png":::.

4. Wählen Sie die Registerkarte **Ansicht** aus, um die Ergebnisse anzuzeigen. Im Fenster rechts werden die am häufigsten aufgetretenen Ereignisse vor der ersten und nach der letzten Aktivität angezeigt. So können Sie Benutzertendenzen für eine bestimmte Aktionssequenz nachvollziehen.

     :::image type="content" source="./media/tutorial-users/funnel-2.png" alt-text="Screenshot der angezeigten Registerkarte „Trichter“." lightbox="./media/tutorial-users/funnel-2.png":::

4. Wählen Sie **Speichern** aus, um den Trichter zu speichern. 

## <a name="learn-which-customers-return"></a>Ermitteln von Kunden, die wiederholt auf Ihre Anwendung zugreifen

Mit der Option **Retention** (Vermerkdauer) können Sie nachvollziehen, welche Benutzer Ihre Anwendung mehrfach nutzen.  

1. Wählen Sie **Aufbewahrung** im Menü und dann „Arbeitsmappe für Aufbewahrungsanalyse“ aus.
2. Standardmäßig umfassen die analysierten Informationen Benutzer, die eine Aktion ausgeführt, die Anwendung verlassen und anschließend die Anwendung erneut verwendet haben, um eine weitere Aktion auszuführen.  Sie können diesen Filter z.B. so konfigurieren, dass nur Benutzer erfasst werden, die nach Abschluss eines Kaufvorgangs erneut auf Ihre Anwendung zugegriffen haben.

      :::image type="content" source="./media/tutorial-users/retention.png" alt-text="Screenshot: Diagramm der Benutzer, die den festgelegten Kriterien für die Vermerkdauer entsprechen" lightbox="./media/tutorial-users/retention.png":::

3. Die Anzahl der wiederkehrenden Benutzer, die den Kriterien entsprechen, wird in einem Diagramm und in einer Tabelle für verschiedene Zeiträume angezeigt. Üblicherweise ergibt sich mit größer werdenden Zeiträumen als Muster ein sukzessiver Abfall von wiederkehrenden Benutzern.  Ein plötzlicher Abfall zwischen Zeiträumen kann unter Umständen Anlass für Bedenken sein. 

      :::image type="content" source="./media/tutorial-users/retention-2.png" alt-text="Screenshot der „Bindung“-Arbeitsmappe, die ein Diagramm der Rückkehr von Benutzern nach Anzahl von Wochen zeigt." lightbox="./media/tutorial-users/retention-2.png":::

## <a name="analyze-user-navigation"></a>Analysieren der Benutzernavigation
Mit einem **User flow** (Benutzerflow) wird die Navigation von Benutzern zwischen den Seiten und Funktionen Ihrer Anwendung visualisiert.  Dadurch können Sie ermitteln, zu welchem Ziel Benutzer in der Regel von einer bestimmten Seite aus navigieren, wie sie die Anwendung üblicherweise beenden und welche Aktionen wiederholt ausgeführt werden.

1.  Klicken Sie im Menü auf **User flows** (Benutzerflows).
2.  Klicken Sie zum Erstellen eines neuen Benutzerflows zuerst auf **Neu** und anschließend auf **Bearbeiten**, um die zugehörigen Details zu bearbeiten.
3.  Erhöhen Sie den **Zeitbereich** auf sieben Tage, und wählen Sie anschließend ein Ausgangsereignis aus.  Der Flow erfasst nun Benutzersitzungen, die mit diesem Ereignis beginnen.

     :::image type="content" source="./media/tutorial-users/flowsedit.png" alt-text="Screenshot: Erstellen eines neuen Benutzerflows" lightbox="./media/tutorial-users/flowsedit.png":::
    
4.  Der Benutzerflow wird angezeigt. In diesem sehen Sie unterschiedliche Benutzerpfade und die Anzahl der Benutzersitzungen.  Blaue Linien stehen für eine Aktion, die der Benutzer nach der aktuellen Aktion ausgeführt hat.  Rote Linien stellen das Ende der Benutzersitzung dar.

   :::image type="content" source="./media/tutorial-users/flows.png" alt-text="Screenshot: Anzeige der Benutzerpfade und der Anzahl der Benutzersitzungen für einen Benutzerflow" lightbox="./media/tutorial-users/flows.png":::

5.  Klicken Sie zuerst auf das **x** in der Ecke des Aktionsfelds und anschließend auf **Diagramm erstellen**, um ein Ereignis aus dem Flow zu entfernen.  Daraufhin wird jede Instanz des Ereignisses entfernt, und das Diagramm wird neu gezeichnet. Wählen Sie **Bearbeiten** aus, damit das Ereignis der Liste **Ausgeschlossene Ereignisse** hinzugefügt wird.

    :::image type="content" source="./media/tutorial-users/flowsexclude.png" alt-text="Screenshot: Liste der ausgeschlossenen Ereignisse für einen Benutzerflow" lightbox="./media/tutorial-users/flowsexclude.png":::

## <a name="consolidate-usage-data"></a>Zusammenfassung von Nutzungsdaten
In **Workbooks** (Arbeitsmappen) werden Datenvisualisierungen, Analytics-Abfragen und Text in interaktiven Dokumenten zusammengefasst.  Sie können Arbeitsmappen verwenden, um gemeinsame Nutzungsdaten zu gruppieren, Informationen zu einem bestimmten Vorfall zusammenzutragen oder ein Team über die beobachtete Nutzung Ihrer Anwendung zu informieren.

1.  Klicken Sie im Menü auf **Workbooks** (Arbeitsmappen).
2.  Klicken Sie auf **Neu**, um eine neue Arbeitsmappe zu erstellen.
3.  Eine Abfrage wird bereitgestellt, in der alle Nutzungsdaten des letzten Tags als Balkendiagramm angezeigt werden.  Sie können diese Abfrage verwenden oder manuell bearbeiten. Alternativ können Sie auf **Beispielabfragen** klicken, um weitere relevante Abfragen auszuwählen.

    :::image type="content" source="./media/tutorial-users/sample-queries.png" alt-text="Screenshot: Schaltfläche für Beispiele und Liste der Beispielabfragen, die verwendet werden können." lightbox="./media/tutorial-users/sample-queries.png":::

4.  Wählen Sie **Bearbeitung abgeschlossen** aus.
5.  Wählen Sie im oberen Bereich **Bearbeiten** aus, um den Text oben in der Arbeitsmappe zu bearbeiten.  Dieser ist in Markdown formatiert.

6.  Wählen Sie **Benutzer hinzufügen** aus, um ein Diagramm mit Benutzerinformationen hinzuzufügen.  Bearbeiten Sie ggf. die Diagrammdaten, und wählen Sie anschließend **Bearbeitung abgeschlossen** aus, um das Diagramm zu speichern.

Weitere Informationen zu Arbeitsmappen finden Sie in der [Übersicht über Arbeitsmappen](../visualize/workbooks-overview.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie erfahren, wie Sie Benutzerdaten analysieren. Im nächsten Tutorial lernen Sie, benutzerdefinierte Dashboards zu erstellen, die die analysierten Informationen mit anderen nützlichen Daten über Ihre Anwendung verknüpfen.

> [!div class="nextstepaction"]
> [Erstellen benutzerdefinierter Dashboards](./tutorial-app-dashboards.md)