---
title: Erstellen eines Power BI-Berichts aus Microsoft Sentinel-Daten
description: Es wird beschrieben, wie Sie einen Power BI-Bericht erstellen, indem Sie eine exportierte Abfrage aus Microsoft Sentinel Log Analytics verwenden. Geben Sie Ihren Bericht für andere Personen im Power BI-Dienst und in einem Teams-Kanal frei.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cd6c6de1fc1a83cecdf0ea96e912f989bdf6b904
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521318"
---
# <a name="tutorial-create-a-power-bi-report-from-microsoft-sentinel-data"></a>Tutorial: Erstellen eines Power BI-Berichts aus Microsoft Sentinel-Daten

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Power BI](https://powerbi.microsoft.com/) ist eine Plattform für die Berichterstellung und Analyse, mit der aus Daten kohärente, immersive, interaktive Visualisierungen erstellt werden können. Mit Power BI können Sie problemlos Verbindungen mit Datenquellen herstellen, Beziehungen visualisieren und ermitteln und Erkenntnisse mit beliebigen anderen Personen teilen.

Sie können als Grundlage für Power BI-Berichte Daten aus Microsoft Sentinel Log Analytics-Arbeitsbereichen verwenden und diese Berichte für Personen freigeben, die keinen Zugriff auf Microsoft Sentinel haben. Es kann beispielsweise sein, dass Sie Informationen zu nicht erfolgreichen Anmeldeversuchen für App-Besitzer freigeben möchten, ohne ihnen Zugriff auf Microsoft Sentinel zu gewähren. Mit Power BI-Visualisierungen können Sie eine Übersicht für die Daten erstellen, die alle wichtigen Informationen enthält.

In diesem Tutorial:

> [!div class="checklist"]
> * Exportieren einer Log Analytics-Kusto-Abfrage in eine Power BI-Abfrage in der Sprache M
> * Verwenden der M-Abfrage in Power BI Desktop, um Visualisierungen und einen Bericht zu erstellen
> * Veröffentlichen des Berichts unter dem Power BI-Dienst und Teilen mit anderen Personen
> * Hinzufügen des Berichts zu einem Teams-Kanal

Personen, denen Sie im Power BI-Dienst Zugriff gewährt haben, und Mitglieder des Teams-Kanals können den Bericht anzeigen, ohne dass sie Microsoft Sentinel-Berechtigungen benötigen.

> [!NOTE]
> Dieses Tutorial bietet eine szenariobasierte Vorgehensweise für einen der häufigsten Kundenwünsche: die Anzeige von Analyseberichten in Power BI für Ihre Microsoft Sentinel-Daten. Weitere Informationen finden Sie unter [Verbinden von Datenquellen](connect-data-sources.md) und [Visualisieren gesammelter Daten](get-visibility.md).
>
## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Mindestens Lesezugriff auf einen Microsoft Sentinel Log Analytics-Arbeitsbereich, für den Anmeldeversuche überwacht werden.
- Ein Power BI-Konto, das über Lesezugriff auf den Log Analytics-Arbeitsbereich verfügt.
- [Installation von Power BI Desktop über den Microsoft Store](https://aka.ms/pbidesktopstore).

## <a name="export-a-query-from-log-analytics"></a>Exportieren einer Abfrage aus Log Analytics

Führen Sie die Erstellung, die Ausführung und den Export einer Kusto-Abfrage in Ihrem Microsoft Sentinel Log Analytics-Arbeitsbereich durch. 

1. Wählen Sie in Ihrem Microsoft Sentinel Log Analytics-Arbeitsbereich die Option **Protokolle** aus, um eine einfache Abfrage zu erstellen. Geben Sie im Abfrage-Editor unter **Neue Abfrage 1** die folgende Kusto-Abfrage ein:
   
   ```kusto
   SigninLogs
   |  where TimeGenerated >ago(7d)
   | summarize Attempts = count(), Failed=countif(ResultType !=0), Succeeded = countif(ResultType ==0) by AppDisplayName
   |  top 10 by Failed
   | sort by Failed
   ```
   
   Verwenden Sie alternativ Ihre bevorzugte Kusto-Abfrage aus Microsoft Sentinel Log Analytics.
   
1. Wählen Sie **Ausführen** aus, um die Abfrage auszuführen und Ergebnisse zu generieren.
   
   :::image type="content" source="media/powerbi/query.png" alt-text="Screenshot: Log Analytics-Kusto-Abfrage und Ergebnisse":::
   
1. Wählen Sie **Exportieren** und dann **In Power BI exportieren (M-Abfrage)** aus, um die Abfrage im Format „Power BI (M-Abfrage)“ zu exportieren. Log Analytics exportiert die Abfrage in eine Textdatei mit dem Namen *PowerBIQuery.txt*.
   
   :::image type="content" source="media/powerbi/export.png" alt-text="Screenshot: Export der Abfrage im Power BI-M-Format":::
   
1. Kopieren Sie den Inhalt der exportierten Datei.

## <a name="get-the-data-in-power-bi-desktop"></a>Abrufen der Daten in Power BI Desktop

Führen Sie die exportierte M-Abfrage in Power BI Desktop aus, um Daten abzurufen.

1. Öffnen Sie Power BI Desktop, und melden Sie sich bei Ihrem Power BI-Konto an, das über Lesezugriff auf den Log Analytics-Arbeitsbereich verfügt.
   
   :::image type="content" source="media/powerbi/sign-in.png" alt-text="Screenshot: Anmeldung bei Power BI Desktop":::
   
1. Wählen Sie im Power BI-Menüband die Option **Daten abrufen** und dann **Leere Abfrage** aus. Der **Power Query-Editor** wird geöffnet.
   
   :::image type="content" source="media/powerbi/blank-query.png" alt-text="Screenshot: Auswahl von „Leere Abfrage“ unter „Daten abrufen“ in Power BI Desktop":::
   
1. Wählen Sie im **Power Query-Editor** die Option **Erweiterter Editor** aus.
   
1. Fügen Sie den kopierten Inhalt der exportierten Datei *PowerBIQuery.txt* in das Fenster **Erweiterter Editor** ein, und wählen Sie anschließend **Fertig** aus.
   
   :::image type="content" source="media/powerbi/advanced-editor.png" alt-text="Screenshot: Eingefügte M-Abfrage im erweiterten Editor von Power BI":::
   
1. Benennen Sie im **Power Query-Editor** die Abfrage in *App_signin_stats* um, und wählen Sie dann die Option **Schließen und übernehmen** aus.
   
   :::image type="content" source="media/powerbi/close-apply.png" alt-text="Screenshot: Umbenannte Abfrage und Befehl „Schließen und übernehmen“ im Power Query-Editor":::

## <a name="create-visualizations-from-the-data"></a>Erstellen von Visualisierungen aus den Daten

Da sich Ihre Daten nun in Power BI befinden, können Sie Visualisierungen erstellen, um wertvolle Erkenntnisse zu den Daten zu gewinnen.

### <a name="create-a-table-visual"></a>Erstellen eines visuellen Elements aus einer Tabelle

Erstellen Sie zunächst eine Tabelle, in der alle Ergebnisse der Abfrage angezeigt werden.

1. Wählen Sie das Symbol **Tabelle** unter **Visualisierungen** aus, um der Power BI Desktop-Canvas eine Tabellenvisualisierung hinzuzufügen.
   
   :::image type="content" source="media/powerbi/table.png" alt-text="Screenshot: Tabellensymbol unter „Visualisierungen“ in Power BI Desktop":::
   
1. Wählen Sie unter **Felder** alle Felder Ihrer Abfrage aus, damit sie alle in der Tabelle angezeigt werden. Falls in der Tabelle nicht alle Daten angezeigt werden, können Sie sie vergrößern, indem Sie die Auswahlziehpunkte verschieben.
   
   :::image type="content" source="media/powerbi/select-fields.png" alt-text="Screenshot: Auswahl aller Felder für die Tabellenvisualisierung":::
   
### <a name="create-a-pie-chart"></a>Erstellen eines Kreisdiagramms

Erstellen Sie als Nächstes ein Kreisdiagramm, mit dem angezeigt wird, welche Anwendungen die meisten fehlgeschlagenen Anmeldeversuche aufweisen.

1. Deaktivieren Sie das visuelle Tabellenelement, indem Sie auf eine Stelle daneben klicken oder tippen, und wählen Sie dann unter **Visualisierungen** das Symbol **Kreisdiagramm** aus.
   
   :::image type="content" source="media/powerbi/pie-chart.png" alt-text="Screenshot: Kreisdiagrammsymbol unter „Visualisierungen“ in Power BI Desktop":::
   
1. Wählen Sie unter **Legende** die Option **AppDisplayName** aus, oder ziehen Sie dieses Element aus dem Bereich **Felder**. Wählen Sie unter **Werte** die Option **Fehler** aus, oder ziehen Sie das Element aus dem Bereich **Felder**. Im Kreisdiagramm wird jetzt die Anzahl von fehlgeschlagenen Anmeldeversuchen pro Anwendung angezeigt.
   
   :::image type="content" source="media/powerbi/failed.png" alt-text="Screenshot: Anzahl fehlgeschlagener Anmeldeversuche pro Anwendung im Kreisdiagramm":::
   
### <a name="create-a-new-quick-measure"></a>Erstellen eines neuen Quickmeasures

Als Nächstes möchten Sie den Prozentsatz der nicht erfolgreichen Anmeldeversuche für jede Anwendung anzeigen. Da Ihre Abfrage keine Prozentspalte enthält, können Sie ein neues Measure erstellen, um diese Informationen anzuzeigen.

1. Wählen Sie unter **Visualisierungen** das Symbol für **Gestapeltes Säulendiagramm** aus, um ein gestapeltes Säulendiagramm zu erstellen.
   
   :::image type="content" source="media/powerbi/column-chart.png" alt-text="Screenshot: Symbol für „Gestapeltes Säulendiagramm“ unter „Visualisierungen“ in Power BI Desktop":::
   
1. Wählen Sie im Menüband die Option **Quickmeasure** aus, während die neue Visualisierung ausgewählt ist.
   
1. Wählen Sie im Fenster **Quickmeasures** unter **Berechnung** die Option **Division** aus. Ziehen Sie **Fehler** aus **Felder** in das Feld **Zähler** und dann **Versuche** aus **Felder** in das Feld **Nenner**.
   
   :::image type="content" source="media/powerbi/quick-measures.png" alt-text="Screenshot: Einstellungen im Fenster „Quickmeasures“":::
   
1. Klicken Sie auf **OK**. Das neue Measure wird im Bereich **Felder** angezeigt.
   
1. Wählen Sie das neue Measure im Bereich **Felder** und dann im Menüband unter **Formatierung** die Option **Prozentsatz** aus.
   
   :::image type="content" source="media/powerbi/percentage.png" alt-text="Screenshot: Auswahl des neuen Measures im Bereich „Felder“ und von „Prozentsatz“ unter „Formatierung“ im Menüband":::
   
1. Wählen Sie bei ausgewählter Visualisierung „Säulendiagramm“ in der Canvas das Feld **AppDisplayName** aus, bzw. ziehen Sie es in den Bereich **Achse** und das neue Measure **Fehler dividiert durch Versuche** in den Bereich **Werte**. Im Diagramm wird jetzt der Prozentsatz der nicht erfolgreichen Anmeldeversuche für jede Anwendung angezeigt.
   
   :::image type="content" source="media/powerbi/failed-percentage.png" alt-text="Screenshot: Säulendiagramm mit dem Prozentsatz der nicht erfolgreichen Versuche für jede Anwendung":::
   
### <a name="refresh-the-data-and-save-the-report"></a>Aktualisieren der Daten und Speichern des Berichts

1. Wählen Sie **Aktualisieren** aus, um die neuesten Daten aus Microsoft Sentinel abzurufen.
   
   :::image type="content" source="media/powerbi/refresh.png" alt-text="Screenshot: Schaltfläche „Aktualisieren“ im Menüband":::
   
1. Wählen Sie **Datei** > **Speichern** aus, und speichern Sie Ihren Power BI-Bericht.

## <a name="create-a-power-bi-online-workspace"></a>Erstellen eines Power BI Online-Arbeitsbereichs

Erstellen Sie wie folgt einen Power BI-Arbeitsbereich für die Freigabe des Berichts:

1. Melden Sie sich bei [powerbi.com](https://powerbi.com) mit demselben Konto an, das Sie für den Lesezugriff für Power BI Desktop und Microsoft Sentinel verwendet haben.
   
1. Wählen Sie unter **Arbeitsbereiche** die Option **Arbeitsbereich erstellen** aus. Geben Sie dem Arbeitsbereich den Namen *Management Reports* (Verwaltungsberichte), und wählen Sie **Speichern** aus.
   
   :::image type="content" source="media/powerbi/create-workspace.png" alt-text="Screenshot: „Arbeitsbereich erstellen“ im Power BI-Dienst":::
   
1. Wählen Sie neben dem Namen des neuen Arbeitsbereichs die Punkte für **Weitere Optionen** und dann die Option **Arbeitsbereichszugriff** aus, um Benutzern und Gruppen Zugriff auf den Arbeitsbereich zu gewähren.
   
   :::image type="content" source="media/powerbi/workspace-access.png" alt-text="Screenshot: „Arbeitsbereichszugriff“ im Menü „Weitere Optionen“ des Arbeitsbereichs":::
   
1. Im Seitenbereich **Arbeitsbereichszugriff** können Sie die E-Mail-Adressen von Benutzern hinzufügen und diesen jeweils eine Rolle zuweisen. Die verfügbaren Rollen sind „Administrator“, „Mitglied“, „Mitwirkender“ und „Anzeigender Benutzer“.

## <a name="publish-the-power-bi-report"></a>Veröffentlichen eines Power BI-Berichts

Jetzt können Sie Power BI Desktop verwenden, um Ihren Power BI-Bericht zu veröffentlichen, damit er von anderen Personen angezeigt werden kann.

1. Wählen Sie in Ihrem neuen Bericht in Power BI Desktop die Option **Veröffentlichen** aus.
   
   :::image type="content" source="media/powerbi/publish.png" alt-text="Screenshot: Option „Veröffentlichen“ im Menüband von Power BI Desktop":::
   
1. Wählen Sie den Arbeitsbereich **Management Reports** (Verwaltungsberichte) für die Veröffentlichung und dann die Option **Auswählen** aus.
   
   :::image type="content" source="media/powerbi/select-workspace.png" alt-text="Screenshot: Auswählen des Power BI-Arbeitsbereichs „Management Reports“ für die Veröffentlichung":::
   
## <a name="import-the-report-to-a-microsoft-teams-channel"></a>Importieren des Berichts in einen Microsoft Teams-Kanal

Sie möchten erreichen, dass auch Mitglieder des Management Teams-Kanals den Bericht anzeigen können. Fügen Sie den Bericht wie folgt einem Teams-Kanal hinzu:

1. Wählen Sie im Management Teams-Kanal das Pluszeichen ( **+** ) aus, um eine Registerkarte hinzuzufügen. Suchen Sie im Fenster **Registerkarte hinzufügen** nach **Power BI**, und wählen Sie diese Option aus.

   :::image type="content" source="media/powerbi/add-tab.png" alt-text="Screenshot: Auswählen von Power BI im Fenster „Registerkarte hinzufügen“ in Teams":::
   
1. Wählen Sie in der Liste mit den Power BI-Berichten Ihren neuen Bericht und dann die Option **Speichern** aus. Der Bericht wird auf einer neuen Registerkarte im Teams-Kanal angezeigt.
   
   :::image type="content" source="media/powerbi/teams.png" alt-text="Screenshot: Power BI-Bericht auf einer Registerkarte im Teams-Kanal":::

## <a name="schedule-report-refresh"></a>Planen der Berichtsaktualisierung

Aktualisieren Sie Ihren Power BI-Bericht basierend auf einem Zeitplan, damit im Bericht immer aktualisierte Daten angezeigt werden.

1. Wählen Sie im Power BI-Dienst den Arbeitsbereich aus, in dem Sie Ihren Bericht veröffentlicht haben.
   
1. Wählen Sie neben dem Dataset des Berichts **Weitere Optionen** > **Einstellungen** aus.
   
   :::image type="content" source="media/powerbi/settings.png" alt-text="Screenshot: Einstellungen unter „Weitere Optionen“ im Dataset des Power BI-Berichts":::
   
1. Wählen Sie **Anmeldeinformationen bearbeiten** aus, um die Anmeldeinformationen für ein Konto anzugeben, das über Lesezugriff auf den Log Analytics-Arbeitsbereich verfügt.
   
1. Legen Sie unter **Geplante Aktualisierung** den Schieberegler auf **Ein** fest, und richten Sie einen Aktualisierungszeitplan für den Bericht ein.
   
   :::image type="content" source="media/powerbi/schedule.png" alt-text="Screenshot: Einstellungen für die geplante Aktualisierung für das Dataset des Power BI-Berichts":::

## <a name="next-steps"></a>Nächste Schritte

- [Importieren von Azure Monitor-Protokolldaten in Power BI](../azure-monitor/logs/log-powerbi.md)
- [Power Query M – Formelsprache](/powerquery-m/)
