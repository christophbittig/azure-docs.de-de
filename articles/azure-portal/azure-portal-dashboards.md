---
title: Erstellen eines Dashboards im Azure-Portal
description: In diesem Artikel wird beschrieben, wie Dashboards im Azure-Portal erstellt und angepasst werden.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 57de040263fdc6ae7a3aaa366b7cabc4f98235b3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219847"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Erstellen eines Dashboards im Azure-Portal

Dashboards bieten eine fokussierte und organisierte Ansicht Ihrer Cloudressourcen im Azure-Portal. Verwenden Sie Dashboards als Arbeitsbereich, in dem Sie Ressourcen überwachen und schnell Aufgaben für alltägliche Vorgänge starten können. Erstellen Sie benutzerdefinierte Dashboards, die beispielsweise auf Projekten, Aufgaben oder Benutzerrollen basieren.

Das Azure-Portal stellt ein Standarddashboard als Ausgangspunkt bereit. Sie können das Standarddashboard bearbeiten und zusätzliche Dashboards erstellen und anpassen.

> [!NOTE]
> Jeder Benutzer kann bis zu 100 private Dashboards erstellen. Wenn Sie das [Dashboard veröffentlichen und freigeben](azure-portal-dashboard-share-access.md), wird es als Azure-Ressource in Ihrem Abonnement implementiert und nicht auf diesen Grenzwert angerechnet.

In diesem Artikel wird beschrieben, wie Sie ein neues Dashboard erstellen und anpassen. Weitere Informationen zum Freigeben von Dashboards finden Sie unter [Freigeben von Azure-Dashboards mithilfe der rollenbasierten Zugriffssteuerung](azure-portal-dashboard-share-access.md).

## <a name="create-a-new-dashboard"></a>Erstellen eines neuen Dashboards

In diesem Beispiel wird gezeigt, wie Sie ein neues privates Dashboard mit einem zugewiesenen Namen erstellen. Alle Dashboards sind bei ihrer Erstellung privat. Sie können Ihr Dashboard aber bei Bedarf veröffentlichen und für andere Benutzer in Ihrer Organisation freigeben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Menü des Azure-Portals die Option **Dashboard** aus. Die Standardansicht ist möglicherweise bereits auf das Dashboard festgelegt.

    :::image type="content" source="media/azure-portal-dashboards/portal-menu-dashboard.png" alt-text="Screenshot: Azure-Portal mit ausgewähltem Dashboard":::

1. Wählen Sie **Neues Dashboard** und dann **Leeres Dashboard** aus.

    :::image type="content" source="media/azure-portal-dashboards/create-new-dashboard.png" alt-text="Screenshot: Optionen für das neue Dashboard":::

    Mit dieser Aktion werden der **Kachelkatalog**, aus dem Sie Kacheln auswählen können, sowie ein leeres Raster geöffnet, in dem Sie die Kacheln anordnen.

1. Wählen Sie in der Dashboardbezeichnung den Text **Mein Dashboard** aus, und geben Sie einen Namen ein, mit dem Sie das benutzerdefinierte Dashboard leicht identifizieren können.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Screenshot: Leeres Rasters mit dem Kachelkatalog":::

1. Um das Dashboard ohne Änderungen zu speichern, wählen Sie in der Seitenkopfzeile **Anpassung abgeschlossen** aus. Fahren Sie alternativ mit Schritt 2 des nächsten Abschnitts fort, um Kacheln hinzuzufügen und Ihr Dashboard zu speichern.

In der Dashboardansicht wird nun das neue Dashboard angezeigt. Wählen Sie den Pfeil neben dem Namen des Dashboards aus, um die für Sie verfügbaren Dashboards anzuzeigen. Die Liste kann Dashboards enthalten, die von anderen Benutzern erstellt und freigegeben wurden.

## <a name="edit-a-dashboard"></a>Bearbeiten eines Dashboards

Nun bearbeiten wir das Dashboard zum Hinzufügen, Ändern der Größe und Anordnen von Kacheln, die Ihre Azure-Ressourcen darstellen.

### <a name="add-tiles-from-the-tile-gallery"></a>Hinzufügen von Kacheln aus dem Kachelkatalog

Führen Sie die folgenden Schritte aus, um einem Dashboard Kacheln hinzuzufügen:

1. Wählen Sie in der Seitenkopfzeile ![Bearbeitungssymbol](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Bearbeiten** aus.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-edit.png" alt-text="Screenshot: Dashboard mit hervorgehobener Bearbeitungsoption":::

1. Durchsuchen Sie den **Kachelkatalog**, oder verwenden Sie das Suchfeld, um nach einer bestimmten Kachel zu suchen. Wählen Sie die Kachel aus, die Sie Ihrem Dashboard hinzufügen möchten.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="Screenshot: Kachelkatalog":::

1. Wählen Sie **Hinzufügen** aus, um die Kachel dem Dashboard mit einer Standardgröße und einer Standardposition hinzuzufügen. Sie können die Kachel auf das Raster ziehen und dann an der gewünschten Position platzieren. Fügen Sie alle gewünschten Kacheln hinzu. Hier einige Empfehlungen:

    - Fügen Sie **Alle Ressourcen** hinzu, um alle Ressourcen anzuzeigen, die Sie bereits erstellt haben.

    - Wenn Sie mit mehreren Organisationen arbeiten, fügen Sie dem Dashboard die Kachel **Organisationsidentität** hinzu, um die Organisation eindeutig anzuzeigen, zu der die Ressourcen gehören.

1. Falls gewünscht, [ändern Sie die Größe oder die Anordnung](#resize-or-rearrange-tiles) ihrer Kacheln.

1. Wählen Sie zum Speichern der Änderungen in der Seitenkopfzeile **Speichern** aus. Sie können auch eine Vorschau der Änderungen anzeigen, ohne sie zu speichern, indem Sie in der Seitenkopfzeile **Vorschau** auswählen. In diesem Vorschaumodus können Sie auch sehen, wie sich [Filter](#set-and-override-dashboard-filters) auf Ihre Kacheln auswirken. Auf dem Vorschaubildschirm können Sie **Speichern** auswählen, um die Änderungen beizubehalten, **Verwerfen**, um sie zu entfernen, oder **Bearbeiten**, um zu den Bearbeitungsoptionen zurückzukehren und weitere Änderungen vorzunehmen.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Screenshot: Optionen „Vorschau“, „Speichern“ und „Verwerfen“":::

> [!NOTE]
> Mithilfe einer Markdownkachel können Sie benutzerdefinierte, statische Inhalte in Ihrem Dashboard anzeigen. Dabei kann es sich um grundlegende Anweisungen, ein Bild, eine Reihe von Links oder sogar um Kontaktinformationen handeln. Weitere Informationen zum Verwenden einer Markdownkachel finden Sie unter [Verwenden einer Markdownkachel in Azure-Dashboards zum Anzeigen benutzerdefinierter Inhalte](azure-portal-markdown-tile.md).

### <a name="pin-content-from-a-resource-page"></a>Anheften von Inhalten von einer Ressourcenseite

Sie können Kacheln auch direkt über eine Ressourcenseite zu Ihrem Dashboard hinzufügen.

Viele Ressourcenseiten enthalten ein Reißzweckensymbol oben auf der Seite, was bedeutet, dass Sie eine Kachel anheften können, die die Quellseite darstellt. Manchmal wird auch ein Reißzweckensymbol für einen bestimmten Inhalt auf einer Seite angezeigt. Das bedeutet, dass Sie eine Kachel für diesen bestimmten Inhalt anstatt für die gesamte Seite anheften können.

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-blade.png" alt-text="Screenshot der Befehlsleiste der Seite mit Reißzweckensymbol.":::

Wenn Sie dieses Symbol auswählen, können Sie die Kachel an ein vorhandenes privates oder freigegebenes Dashboard anheften. Sie können auch ein neues Dashboard erstellen, das diesen Reißzwecken enthält, indem Sie **Neu erstellen** auswählen.

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-pane.png" alt-text="Screenshot: an Dashboard-Optionen anheften.":::

### <a name="copy-a-tile-to-a-new-dashboard"></a>Kopieren einer Kachel in ein neues Dashboard

Wenn Sie eine Kachel in einem anderen Dashboard wiederverwenden möchten, können Sie sie von einem Dashboard in ein anderes kopieren. Wählen Sie dazu das Kontextmenü in der oberen rechten Ecke und dann **Kopieren** aus.

:::image type="content" source="media/azure-portal-dashboards/copy-dashboard.png" alt-text="Screenshot des Kopierens einer Kachel im Azure-Portal.":::

Anschließend können Sie dann auswählen, ob Sie die Kachel in ein vorhandenes privates oder freigegebenes Dashboard kopieren oder eine Kopie der Kachel innerhalb des Dashboards erstellen möchten, in dem Sie bereits arbeiten. Sie können auch ein neues Dashboard erstellen, das eine Kopie dieser Kachel enthält, indem Sie **Neu erstellen** auswählen.

### <a name="resize-or-rearrange-tiles"></a>Ändern der Größe oder Neuanordnung von Kacheln

Führen Sie die folgenden Schritte aus, um die Größe einer Kachel zu ändern oder die Kacheln auf einem Dashboard neu anzuordnen:

1. Wählen Sie ![Bearbeitungssymbol](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Bearbeiten** in der Kopfzeile der Seite aus.

1. Wählen Sie in der oberen rechten Ecke einer Kachel das Kontextmenü aus. Wählen Sie dann eine Kachelgröße aus. Kacheln, die eine beliebige Größe unterstützen, enthalten auch einen „Ziehpunkt“ in der unteren rechten Ecke, mit dem Sie die Kachel auf die gewünschte Größe ziehen können.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Screenshot: Dashboard mit geöffnetem Menü „Kachelgröße“":::

1. Wählen Sie eine Kachel aus, und ziehen Sie sie an eine neue Position im Raster, um das Dashboard anzuordnen.

### <a name="set-and-override-dashboard-filters"></a>Festlegen und Überschreiben von Dashboard-Filtern

Im oberen Bereich des Dashboards werden Optionen zum Festlegen der Einstellungen für **automatische Aktualisierung** und **Uhrzeit** für im Dashboard angezeigte Daten sowie eine Option zum Hinzufügen zusätzlicher Filter angezeigt.

:::image type="content" source="media/azure-portal-dashboards/dashboard-global-filters.png" alt-text="Screenshot: Globale Filter eines Dashboards.":::

Standardmäßig werden die Daten stündlich aktualisiert. Um dies zu ändern, wählen Sie **Automatische Aktualisierung** und dann ein neues Aktualisierungsintervall aus. Wenn Sie Ihre Auswahl getroffen haben, wählen Sie **Anwenden** aus.

Die Standardzeiteinstellungen sind **UTC-Zeit** und zeigen Daten für die **letzten 24 Stunden** an. Um dies zu ändern, wählen Sie die Schaltfläche und dann einen neuen Zeitbereich, eine neue Zeitgranularität und/oder Zeitzone aus und wählen Sie schließlich **Anwenden** aus.

Um zusätzliche Filter anzuwenden, wählen Sie **Filter hinzufügen** aus. Die angezeigten Optionen variieren abhängig von den Kacheln in Ihrem Dashboard. Beispielsweise können Sie möglicherweise nur Daten für ein bestimmtes Abonnement oder einen bestimmten Standort anzeigen. Wählen Sie den Filter aus, den Sie verwenden möchten, und treffen Sie Ihre Auswahl. Der Filter wird dann auf Ihre Daten angewendet. Um einen Filter zu entfernen, wählen Sie das **X** in der zugehörigen Schaltfläche aus.

Kacheln, die das Filtern unterstützen, verfügen über ein ![Filtersymbol](./media/azure-portal-dashboards/dashboard-filter.png) in der oberen linken Ecke der Kachel. Einige Kacheln ermöglichen es Ihnen, die globalen Filter mit spezifischen Filtern für diese Kachel zu überschreiben. Klicken Sie hierzu im Kontextmenü auf **Kacheldaten konfigurieren** oder wählen Sie das Filtersymbol aus und wenden Sie dann die gewünschten Filter an.

Wenn Sie Filter für eine bestimmte Kachel festlegen, zeigt die linke Ecke dieser Kachel ein doppeltes Filtersymbol an, das angibt, dass die Daten in dieser Kachel ihre eigenen Filter widerspiegeln.

:::image type="content" source="media/azure-portal-dashboards/dashboard-filter-override.png" alt-text="Screenshot: Symbol für eine Kachel mit einer Filterüberschreibung.":::

## <a name="modify-tile-settings"></a>Ändern von Kacheleinstellungen

Einige Kacheln erfordern möglicherweise weitere Konfiguration, um die gewünschten Informationen anzuzeigen. Beispielsweise muss die Kachel **Metrikdiagramm** so eingerichtet werden, dass eine Metrik aus Azure Monitor angezeigt wird. Sie können Kacheldaten auch anpassen, um die Standardzeiteinstellungen und Filter des Dashboards außer Kraft zu setzen.

### <a name="complete-tile-configuration"></a>Abschließen der Kachelkonfiguration

Für alle Kacheln, die eingerichtet werden müssen, wird ein Banner angezeigt, bis Sie die Kachel anpassen. Im **Diagramm mit den Metriken** lautet das Banner beispielsweise **In Metriken bearbeiten**. Andere Banner können einen anderen Text verwenden, z. B. **Kachel konfigurieren**.

So passen Sie die Kachel an

1. Wählen Sie im Seitenkopf **Speichern** aus, um den Bearbeitungsmodus zu beenden.

1. Wählen Sie das Banner aus, und nehmen Sie dann die gewünschte Einrichtung vor.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-configure-tile.png" alt-text="Screenshot einer Kachel, die konfiguriert werden muss.":::

### <a name="customize-time-span-for-a-tile"></a>Anpassen der Zeitspanne für eine Kachel

Daten auf dem Dashboard zeigen Aktivitäten und Aktualisierungen basierend auf den globalen Filtern an. Bei einigen Kacheln können Sie eine andere Zeitspanne für nur eine Kachel auswählen. Gehen Sie dazu folgendermaßen vor:

1. Wählen Sie im Kontextmenü oder über das ![Filtersymbol](./media/azure-portal-dashboards/dashboard-filter.png) in der linken oberen Ecke der Kachel die Option **Kacheldaten anpassen** aus.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-customize-tile-data.png" alt-text="Screenshot: Kontextmenü einer Kachel":::

1. Aktivieren Sie das Kontrollkästchen zum **Außerkraftsetzen der Zeiteinstellungen für das Dashboard auf Kachelebene**.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-override-time-settings.png" alt-text="Screenshot: Dialogfeld zum Konfigurieren der Zeiteinstellungen der Kachel":::

1. Wählen Sie die Zeitspanne aus, die für diese Kachel angezeigt werden soll. Sie können zwischen den letzten 30 Minuten bis zu den letzten 30 Tagen wählen oder einen benutzerdefinierten Bereich definieren.

1. Wählen Sie die anzuzeigende Zeitgranularität aus.  Sie können ein beliebiges Inkrement zwischen einer Minute und einem Monat anzeigen.

1. Wählen Sie **Übernehmen**.

### <a name="change-the-title-and-subtitle-of-a-tile"></a>Ändern des Titels und Untertitels einer Kachel

Für einige Kacheln kann der Titel und Untertitel bearbeitet werden. Wählen Sie hierzu im Kontextmenü **Kacheleinstellungen konfigurieren** aus.

:::image type="content" source="media/azure-portal-dashboards/dashboard-tile-rename.png" alt-text="Screenshot: Option „Kacheleinstellungen konfigurieren“":::

Nehmen Sie Änderungen am Titel und/oder Untertitel der Kachel vor, und klicken Sie dann auf **Anwenden**.

:::image type="content" source="media/azure-portal-dashboards/dashboard-title-subtitle.png" alt-text="Screenshot: Ändern des Titels und Untertitels für eine Kachel":::
 
## <a name="delete-a-tile"></a>Löschen einer Kachel

Führen Sie zum Entfernen einer Kachel von einem Dashboard eine der folgenden Aktionen aus:

- Wählen Sie das Kontextmenü in der oberen rechten Ecke der Kachel aus, und wählen Sie dann **Aus Dashboard entfernen** aus.

- Wählen Sie ![Bearbeitungssymbol](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Bearbeiten** aus, um den Anpassungsmodus einzugeben. Platzieren Sie den Mauszeiger in der oberen rechten Ecke der Kachel, und wählen Sie dann das ![Löschsymbol](./media/azure-portal-dashboards/dashboard-delete-icon.png) Löschsymbol aus, um die Kachel aus dem Dashboard zu entfernen.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-tile.png" alt-text="Screenshot: Entfernen einer Kachel vom Dashboard":::

## <a name="clone-a-dashboard"></a>Klonen eines Dashboards

Führen Sie die folgenden Schritte aus, um ein vorhandenes Dashboard als Vorlage für ein neues Dashboard zu verwenden:

1. Stellen Sie sicher, dass in der Dashboardansicht das Dashboard angezeigt wird, das Sie kopieren möchten.

1. Wählen Sie in der Kopfzeile der Seite ![Klonsymbol](./media/azure-portal-dashboards/dashboard-clone.png) **Klonen** aus.

1. Eine Kopie des Dashboards mit dem Namen **Klon von** *Ihr Dashboardname* wird im Bearbeitungsmodus geöffnet. Verwenden Sie die weiter oben beschriebenen Schritte in diesem Artikel, um das Dashboard umzubenennen und anzupassen.

## <a name="publish-and-share-a-dashboard"></a>Veröffentlichen und Freigeben eines Dashboards

Wenn Sie ein Dashboard erstellen, ist es standardmäßig ein privates Dashboard. Dies bedeutet, dass Sie die einzige Person sind, die es anzeigen kann. Um Dashboards für andere Benutzer verfügbar zu machen, können Sie sie veröffentlichen und freigeben. Weitere Informationen finden Sie unter [Freigeben von Azure-Dashboards mithilfe der rollenbasierten Zugriffssteuerung](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Öffnen eines freigegebenen Dashboards

Gehen Sie folgendermaßen vor, um ein freigegebenes Dashboard zu suchen und zu öffnen:

1. Wählen Sie den Pfeil neben dem Dashboardnamen aus.

1. Wählen Sie ein Dashboard in der angezeigten Liste der Dashboards aus. Gehen Sie wie folgt vor, wenn das Dashboard, das Sie öffnen möchten, nicht aufgeführt wird:

    1. Wählen Sie **Alle Dashboards durchsuchen** aus.

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse.png" alt-text="Screenshot: Menü zur Dashboardauswahl":::

    1. Wählen Sie im Feld **Typ** die Option **Freigegebene Dashboards** aus.

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse-all.png" alt-text="Screenshot: Auswahlmenü „Alle Dashboards“":::

    1. Wählen Sie mindestens ein Abonnement aus. Sie können auch Text eingeben, um Dashboards nach Namen zu filtern.

    1. Wählen Sie ein Dashboard aus der Liste der freigegebenen Dashboards aus.

## <a name="delete-a-dashboard"></a>Löschen eines Dashboards

Führen Sie die folgenden Schritte aus, um ein privates oder freigegebenes Dashboard dauerhaft zu löschen:

1. Wählen Sie das zu löschende Dashboard aus der Liste neben dem Namen des Dashboards aus.

1. Wählen Sie ![Löschsymbol](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Löschen** in der Kopfzeile der Seite aus.

1. Wählen Sie für ein privates Dashboard im Bestätigungsdialogfeld **OK** aus, um das Dashboard zu entfernen. Aktivieren Sie für ein freigegebenes Dashboard im Bestätigungsdialogfeld das Kontrollkästchen, um zu bestätigen, dass das veröffentlichte Dashboard von anderen Benutzern nicht mehr angezeigt werden kann. Wählen Sie anschließend **OK** aus.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-dash.png" alt-text="Screenshot: Löschbestätigung":::

## <a name="recover-a-deleted-dashboard"></a>Wiederherstellen eines gelöschten Dashboards

Wenn Sie in der globalen Azure-Cloud ein _veröffentlichtes_ Dashboard im Azure-Portal löschen, können Sie das Dashboard innerhalb von 14 Tagen nach dem Löschvorgang wiederherstellen. Weitere Informationen finden Sie unter [Wiederherstellen eines gelöschten Dashboards im Azure-Portal](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Nächste Schritte

- [Freigeben von Azure-Dashboards mithilfe der rollenbasierten Zugriffssteuerung](azure-portal-dashboard-share-access.md)
- [Programmgesteuertes Erstellen von Azure-Dashboards](azure-portal-dashboards-create-programmatically.md)
