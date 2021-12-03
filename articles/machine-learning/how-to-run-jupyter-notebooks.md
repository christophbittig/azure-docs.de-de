---
title: Ausführen von Jupyter Notebooks in Ihrem Arbeitsbereich
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie in Azure Machine Learning Studio ein Jupyter Notebook ausführen können, ohne Ihren Arbeitsbereich zu verlassen.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: f6ac69d8bd3049efc55844ddd562e18a2950ef2b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560870"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Ausführen von Jupyter Notebooks in Ihrem Arbeitsbereich

Erfahren Sie, wie Sie in Azure Machine Learning Studio Ihr Jupyter Notebook direkt in Ihrem Arbeitsbereich ausführen können. Sie können zwar [Jupyter](https://jupyter.org/) oder [JupyterLab](https://jupyterlab.readthedocs.io) starten, aber Sie können Ihre Notebooks auch bearbeiten und ausführen, ohne den Arbeitsbereich zu verlassen.

Informationen zum Erstellen und Verwalten von Dateien, einschließlich Notebooks, finden Sie unter [Erstellen und Verwalten von Dateien in Ihrem Arbeitsbereich](how-to-manage-files.md).

> [!IMPORTANT]
> Mit „(Vorschau)“ gekennzeichnete Features werden ohne Vereinbarung zum Servicelevel bereitgestellt und nicht für Produktionsworkloads empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Bearbeiten eines Notebooks

Öffnen Sie zum Bearbeiten eines Notebooks ein beliebiges Notebook, das sich im Abschnitt **Benutzerdateien** Ihres Arbeitsbereichs befindet. Klicken Sie auf die Zelle, die Sie bearbeiten möchten.  Wenn Sie in diesem Abschnitt über keine Notebooks verfügen, finden Sie weitere Informationen unter [Erstellen und Verwalten von Dateien in Ihrem Arbeitsbereich](how-to-manage-files.md).

Sie können das Notebook bearbeiten, ohne eine Verbindung zu einer Computeinstanz herzustellen.  Wenn Sie die Zellen im Notebook ausführen möchten, wählen oder erstellen Sie eine Computeinstanz.  Wenn Sie eine beendete Computeinstanz auswählen, wird sie automatisch gestartet, wenn Sie die erste Zelle ausführen.

Wenn eine Compute-Instanz ausgeführt wird, können Sie auch die Codevervollständigung mit [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) in jedem Python-Notebook verwenden.

Sie können Jupyter oder JupyterLab auch über die Notebook-Symbolleiste starten.  Azure Machine Learning stellt keine Updates und Fehlerbehebungen von Jupyter oder JupyterLab zur Verfügung, da es sich um Open Source-Produkte außerhalb der Zuständigkeit des Microsoft-Supports handelt.

## <a name="focus-mode"></a>Fokusmodus

Verwenden Sie den Fokusmodus, um Ihre aktuelle Ansicht zu erweitern, sodass Sie sich auf Ihre aktiven Registerkarten konzentrieren können. Der Fokusmodus blendet den Datei-Explorer für Notebooks aus.

1. Wählen Sie auf der Symbolleiste des Terminalfensters **Fokusmodus** aus, um den Fokusmodus zu aktivieren. Abhängig von Ihrer Fensterbreite befindet sich dieses Tool möglicherweise unter dem Menüpunkt **...** auf Ihrer Symbolleiste.
1. Kehren Sie im Fokusmodus zur Standardansicht zurück, indem Sie **Standardansicht** auswählen.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Wechsel zwischen Fokusmodus/Standardansicht":::

## <a name="code-completion-intellisense"></a>Codevervollständigung [IntelliSense]

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) ist eine hilfreiche Anwendung zur Codevervollständigung mit vielen Features wie: Auflisten von Elementen, Parameterinformationen, QuickInfo und Wortvervollständigung. Mit nur wenigen Tastatureingaben können Sie Folgendes ausführen:
* Mehr über den von Ihnen verwendeten Code erfahren
* Die Parameter nachverfolgen, die Sie eingeben
* Aufrufe von Eigenschaften und Methoden hinzufügen 

### <a name="insert-code-snippets-preview"></a>Codeausschnitte einfügen (Vorschau)

Lösen Sie mit **STRG+LEERTASTE** IntelliSense-Codeausschnitte aus.  Scrollen Sie durch die Vorschläge, oder starten Sie die Eingabe, um den Code zu finden, den Sie einfügen möchten.  Nachdem Sie Code eingefügt haben, durchlaufen Sie mit der TAB-Taste die Argumente, um den Code Ihrem Zweck anzupassen.

:::image type="content" source="media/how-to-run-jupyter-notebooks/insert-snippet.gif" alt-text="Einfügen eines Codeausschnitts":::

Dieselben Ausschnitte sind verfügbar, wenn Sie Ihr Notebook in VS Code öffnen. Eine umfassende Liste der verfügbaren Codeausschnitte finden Sie unter [Azure Machine Learning VS Code Snippets](https://github.com/Azure/azureml-snippets/blob/main/snippets/snippets.md) (VS Code-Ausschnitte für Azure Machine Learning).

Um die Liste der Ausschnitte zu durchsuchen, können Sie in der Notebook-Symbolleiste den Ausschnittbereich öffnen.

:::image type="content" source="media/how-to-run-jupyter-notebooks/open-snippet-panel.png" alt-text="Öffnen des Ausschnittbereichstools in der Notebook-Symbolleiste":::

Im Ausschnittbereich können Sie auch eine Anforderung zum Hinzufügen neuer Codeausschnitte senden.

:::image type="content" source="media/how-to-run-jupyter-notebooks/propose-new-snippet.png" alt-text="Im Ausschnittbereich können Sie einen neuen Ausschnitt vorschlagen.":::

## <a name="collaborate-with-notebook-comments-preview"></a>Zusammenarbeiten mit Notebookkommentaren (Vorschau)

Verwenden Sie einen Notebookkommentar, um mit anderen Personen zusammenzuarbeiten, die Zugriff auf Ihr Notebook haben.

Schalten Sie den Kommentarbereich mit dem Werkzeug **Notebookkommentare** am oberen Rand des Notebooks ein und aus.  Wenn Ihr Bildschirm nicht breit genug ist, finden Sie dieses Tool, indem Sie zuerst die **...** am Ende des Toolsatzes auswählen.

:::image type="content" source="media/how-to-run-jupyter-notebooks/notebook-comments-tool.png" alt-text="Screenshot: Tool für Notebookkommentare in der oberen Symbolleiste":::  

Unabhängig davon, ob der Kommentarbereich sichtbar ist oder nicht, können Sie einen Kommentar in eine beliebige Codezelle einfügen:

1. Wählen Sie etwas Text in der Codezelle aus.  Sie können nur Text in einer Codezelle kommentieren.
1. Verwenden Sie das Tool **Neuer Kommentarthread**, um Ihren Kommentar zu erstellen.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/comment-from-code.png" alt-text="Screenshot: Hinzufügen eines Kommentars zu einem Codezellentool":::
1. Wenn der Kommentarbereich zuvor ausgeblendet war, wird er nun geöffnet.  
1. Geben Sie Ihren Kommentar ein, und veröffentlichen Sie ihn mit dem Tool, oder drücken Sie **STRG+EINGABETASTE**.
1. Nachdem ein Kommentar veröffentlicht wurde, wählen Sie **...** in der oberen rechten Ecke aus, um Folgendes auszuführen:
    * Bearbeiten des Kommentars
    * Auflösen des Threads
    * Löschen des Threads

Kommentierter Text wird im Code lila hervorgehoben. Wenn Sie im Kommentarbereich einen Kommentar auswählen, scrollt Ihr Notebook zu der Zelle, die den markierten Text enthält.

> [!NOTE]
> Kommentare werden in den Metadaten der Codezelle gespeichert.

## <a name="clean-your-notebook-preview"></a>Bereinigen des Notebooks (Vorschau)

Im Laufe der Erstellung eines Notebooks verfügen Sie in der Regel über Zellen, die Sie zum Durchsuchen von Daten oder zum Debuggen verwendet haben. Mithilfe der Funktion *Sammeln* können Sie ein sauberes Notebook ohne diese irrelevanten Zellen erstellen.

1. Führen Sie alle Notebookzellen aus.
1. Wählen Sie die Zelle aus mit dem Code aus, den das neue Notebook ausführen soll, beispielsweise Code zum Übermitteln eines Experiments oder vielleicht Code zum Registrieren eines Modells.
1. Wählen Sie das Symbol **Sammeln** aus, das auf der Symbolleiste für Zellen angezeigt wird.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Screenshot: Auswählen des Symbols „Sammeln“":::
1. Geben Sie den Namen für das neue „gesammelte“ Notebook ein.  

Das neue Notebook enthält nur Codezellen. Dabei müssen alle Zellen die gleichen Ergebnisse wie die Zelle generieren, die Sie für die Sammlung ausgewählt haben.

## <a name="save-and-checkpoint-a-notebook"></a>Speichern eines Notebooks und Hinzufügen eines Prüfpunkts

Azure Machine Learning erstellt eine Prüfpunktdatei, wenn Sie eine *IPYNB*-Datei erstellen.

Wählen Sie in der Notebook-Symbolleiste das Menü und dann **Datei&gt;Save and Checkpoint** (Speichern und Prüfpunkt), um das Notebook manuell zu speichern. Dadurch wird eine dem Notebook zugeordnete Prüfpunktdatei hinzugefügt.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Screenshot des Speichertools auf der Notebook-Symbolleiste":::

Jedes Notebook wird alle 30 Sekunden automatisch gespeichert. Das automatische Speichern aktualisiert nur die ursprüngliche *IPYNB*-Datei, nicht die Prüfpunktdatei.
 
Wählen Sie **Checkpoints** (Prüfpunkte) im Notebook-Menü aus, um einen benannten Prüfpunkt zu erstellen und das Notebook auf einen gespeicherten Prüfpunkt zurückzusetzen.

## <a name="export-a-notebook"></a>Exportieren eines Notebooks

Wählen Sie in der Symbolleiste des Notebooks das Menü und dann **Exportieren als** aus, um das Notebook als einen der unterstützten Typen zu exportieren:

* Notebook
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Exportieren eines Notebooks auf Ihren Computer":::

Die exportierte Datei wird auf dem Computer gespeichert.

## <a name="run-a-notebook-or-python-script"></a>Ausführen eines Notebooks oder Python-Skripts

Um ein Notebook oder ein Python-Skript auszuführen, stellen Sie zunächst eine Verbindung mit einer aktiven [Compute-Instanz](concept-compute-instance.md) her.

* Wenn Sie keine Computeinstanz besitzen, verwenden Sie die folgenden Schritte, um eine zu erstellen:

    1. Wählen Sie rechts neben der Dropdownliste „Compute“ auf der Symbolleiste des Notebooks oder Skripts die Option **+ New Compute** (Neue Computeressource) aus. Je nach Bildschirmgröße befindet sich dies möglicherweise unter einem **...** -Menü.
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Erstellen einer neuen Computeressource":::
    1. Benennen Sie die Computeinstanz, und wählen Sie eine **VM-Größe** aus. 
    1. Klicken Sie auf **Erstellen**.
    1. Die Compute-Instanz wird automatisch mit der Datei verknüpft.  Sie können jetzt die Notebookzellen oder das Python-Skript mit dem Tool links neben der Compute-Instanz ausführen.

* Wenn Sie über eine angehaltene Compute-Instanz verfügen, wählen Sie rechts neben der Dropdownliste „Compute“ die Option **Start compute** (Compute starten) aus. Je nach Bildschirmgröße befindet sich dies möglicherweise unter einem **...** -Menü.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Starten einer Compute-Instanz":::
    
Sobald Sie mit einer Compute-Instanz verbunden sind, verwenden Sie die Symbolleiste, um alle Zellen im Notebook auszuführen, oder die Tastenkombination „Steuerung + Eingabe“, um eine einzelne ausgewählte Zelle auszuführen. 

Nur Sie können die von Ihnen erstellten Computeinstanzen sehen und verwenden.  Ihre **Benutzerdateien** werden getrennt vom virtuellen Computer gespeichert und von allen Computeinstanzen im Arbeitsbereich gemeinsam genutzt.

### <a name="view-logs-and-output"></a>Anzeigen von Protokollen und Ausgaben

Verwenden Sie [Notebook-Widgets](/python/api/azureml-widgets/azureml.widgets), um den Fortschritt der Ausführung und die Protokolle anzuzeigen. Ein Widget ist asynchron und bietet Updates, bis das Training abgeschlossen ist. Azure Machine Learning-Widgets werden auch in Jupyter und JupterLab unterstützt.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Screenshot: Jupyter Notebook-Widget ":::

## <a name="explore-variables-in-the-notebook"></a>Untersuchen von Variablen im Notebook

Verwenden Sie auf der Notebook-Symbolleiste das Tool **Variablen-Explorer**, um den Namen, den Typ, die Länge und Stichprobenwerte aller Variablen anzuzeigen, die in Ihrem Notebook erstellt wurden.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Screenshot: Variablen-Explorer-Tool":::

Wählen Sie das Tool aus, um das Fenster des Variablen-Explorers anzuzeigen.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Screenshot: Fenster des Variablen-Explorers":::

## <a name="navigate-with-a-toc"></a>Navigieren mit einem Inhaltsverzeichnis

Verwenden Sie auf der Notebook-Symbolleiste das Tool **Inhaltsverzeichnis**, um das Inhaltsverzeichnis anzuzeigen oder auszublenden.  Beginnen Sie mit einer Markdown-Zelle mit einer Überschrift, um sie dem Inhaltsverzeichnis hinzuzufügen. Klicken Sie auf einen Eintrag in der Tabelle, um zu dieser Zelle im Notebook zu scrollen.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Screenshot: Inhaltsverzeichnis im Notebook":::

## <a name="change-the-notebook-environment"></a>Ändern der Notebookumgebung

Die Notebook-Symbolleiste ermöglicht Ihnen, die Umgebung zu ändern, in der das Notebook ausgeführt wird.  

Diese Aktionen ändern weder den Notebook-Zustand noch die Werte von Variablen im Notebook:

|Aktion  |Ergebnis  |
|---------|---------| --------|
|Beenden des Kernels     |  Beendet jede ausgeführte Zelle. Durch das Ausführen einer Zelle wird der Kernel automatisch neu gestartet. |
|Navigieren zu einem anderen Arbeitsbereichsabschnitt     |     Das Ausführen von Zellen wird beendet. |

Mit diesen Aktionen werden der Notebook-Zustand und alle Variablen im Notebook zurückgesetzt.

|Aktion  |Ergebnis  |
|---------|---------| --------|
| Ändern des Kernels | Das Notebook verwendet neuen Kernel. |
| Wechseln der Computeinstanz    |     Das Notebook verwendet automatisch die neue Computeinstanz. |
| Zurücksetzen der Computeinstanz | Wird erneut gestartet, wenn Sie versuchen, eine Zelle auszuführen. |
| Beenden der Computeinstanz     |    Es werden keine Zellen ausgeführt.  |
| Öffnen eines Notebooks in Jupyter oder JupyterLab     |    Das Notebook wurde auf einer neuen Registerkarte geöffnet.  |

## <a name="add-new-kernels"></a>Hinzufügen neuer Kernel

[Verwenden Sie das Terminal ](how-to-access-terminal.md#add-new-kernels), um neue Kernel zu erstellen und sie Ihrer Computeinstanz hinzuzufügen. Das Notebook findet automatisch alle Jupyter-Kernel, die auf der verbundenen Compute-Instanz installiert sind.

Verwenden Sie die Kernel-Dropdownliste auf der rechten Seite, um zu einem der installierten Kernel zu wechseln.  


### <a name="status-indicators"></a>Statusanzeigen

Ein Indikator neben der **Compute**-Dropdownliste zeigt seinen Status an.  Der Status wird auch in der Dropdownliste selbst angezeigt.  

|Color |Computestatus |
|---------|---------| 
| Grün | Compute wird ausgeführt |
| Red |Fehler bei Compute | 
| Schwarz | Compute beendet |
|  Hellblau |Erstellen, Starten, Neustarten und Einrichten von Compute |
|  Grau |Compute wird gelöscht, beendet |

Ein Indikator neben der **Kernel**-Dropdownliste zeigt seinen Status an.

|Color |Kernelstatus |
|---------|---------|
|  Grün |Kernel verbunden, im Leerlauf, ausgelastet|
|  Grau |Kernel nicht verbunden |

## <a name="find-compute-details"></a>Suchen von Computedetails

Hier finden Sie Details zu ihren Computeinstanzen auf der **Compute**-Seite in [Studio](https://ml.azure.com).

## <a name="useful-keyboard-shortcuts"></a>Hilfreiche Tastenkombinationen
Ähnlich wie Jupyter Notebooks verfügen Azure Machine Learning-Notebooks über eine modale Benutzeroberfläche. Mit der Tastatur werden unterschiedliche Aktionen ausgeführt, je nachdem, in welchem Modus sich die Notebook-Zelle befindet. Azure Machine Learning-Notebooks unterstützen die folgenden zwei Modi für eine bestimmte Codezelle: Befehlsmodus und Bearbeitungsmodus.

### <a name="command-mode-shortcuts"></a>Tastenkombinationen im Befehlsmodus

Eine Zelle befindet sich im Befehlsmodus, wenn Sie kein Textcursor zur Eingabe auffordert. Wenn sich eine Zelle im Befehlsmodus befindet, können Sie das Notebook als Ganzes bearbeiten, aber keine Eingaben in einzelne Zellen vornehmen. Sie wechseln in den Befehlsmodus, indem Sie `ESC` drücken oder mit der Maus außerhalb des Editor-Bereichs einer Zelle klicken.  Der linke Rand der aktiven Zelle ist blau und durchgehend, und die zugehörige Schaltfläche **Ausführen** ist blau.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Notebookzelle im Befehlsmodus ":::

| Verknüpfung                      | Beschreibung                          |
| ----------------------------- | ------------------------------------|
| EINGABETASTE                         | Aktivieren des Bearbeitungsmodus             |        
| UMSCHALT + ENTER                 | Zelle ausführen, Zelle darunter auswählen         |     
| STRG/Befehlstaste + EINGABETASTE       | Run cell (Zelle ausführen)                            |
| ALT+EINGABETASTE                   | Zelle ausführen, Codezelle unten einfügen    |
| STRG/Befehlstaste + ALT + EINGABETASTE | Zelle ausführen, Markdownzelle unten einfügen|
| ALT + R                       | Run all (Alle ausführen)      |                       
| J                             | Zelle in Code konvertieren    |                         
| M                             | Zelle in Markdown konvertieren  |                       
| Nach oben/K                          | Zelle darüber auswählen    |               
| Nach unten/J                        | Zelle darunter auswählen    |               
| Ein                             | Codezelle oben einfügen  |            
| B                             | Codezelle unten einfügen   |           
| STRG/Befehlstaste + UMSCHALT + A   | Markdownzelle oben einfügen    |      
| STRG/Befehlstaste + UMSCHALT + B   | Markdownzelle unten einfügen   |       
| X                             | Ausgewählte Zelle ausschneiden    |               
| C                             | Ausgewählte Zelle kopieren   |               
| UMSCHALT + V                     | Ausgewählte Zelle oben einfügen           |
| V                             | Ausgewählte Zelle unten einfügen    |       
| D D                           | Ausgewählte Zelle löschen|                
| O                             | Toggle Output         |              
| UMSCHALT + O                     | Scrollen der Ausgabe umschalten   |          
| I I                           | Kernel unterbrechen |                   
| 0 0                           | Kernel neu starten |                     
| UMSCHALT + LEERTASTE                 | Bildlauf nach oben  |                         
| LeerZchn                         | Bildlauf nach unten|
| Registerkarte                           | Fokus auf das nächste Element ändern, das den Fokus erhalten kann (bei deaktivierter Fokusinitialisierung)|
| STRG/Befehlstaste + S           | Notebook speichern |                      
| 1                             | In h1 ändern|                       
| 2                             | In h2 ändern|                        
| 3                             | In h3 ändern|                        
| 4                             | In h4 ändern |                       
| 5                             | In h5 ändern |                       
| 6                             | In h6 ändern |                       

### <a name="edit-mode-shortcuts"></a>Tastenkombinationen im Bearbeitungsmodus

Der Bearbeitungsmodus wird durch einen Textcursor angezeigt, der Sie zur Eingabe im Editor-Bereich auffordert. Wenn sich eine Zelle im Bearbeitungsmodus befindet, können Sie etwas in die Zelle eingeben. Sie wechseln in den Bearbeitungsmodus, indem Sie `Enter` drücken oder mit der Maus auf den Editor-Bereich einer Zelle klicken. Der linke Rand der aktiven Zelle ist grün und gestrichelt, und die zugehörige Schaltfläche **Ausführen** ist grün. Außerdem wird im Bearbeitungsmodus der Cursor der Eingabeaufforderung in der Zelle angezeigt.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Notebookzelle im Bearbeitungsmodus":::

Mithilfe der folgenden Tastenkombinationen können Sie in Azure Machine Learning-Notebooks einfacher navigieren und Code ausführen, während sie sich im Bearbeitungsmodus befinden.

| Verknüpfung                      | Beschreibung|                                     
| ----------------------------- | ----------------------------------------------- |
| Escape                        | Befehlsmodus aktivieren|  
| STRG/Befehlstaste + LEERTASTE       | Aktivieren von IntelliSense |
| UMSCHALT + ENTER                 | Zelle ausführen, Zelle darunter auswählen |                         
| STRG/Befehlstaste + EINGABETASTE       | Run cell (Zelle ausführen)  |                                      
| ALT+EINGABETASTE                   | Zelle ausführen, Codezelle unten einfügen  |              
| STRG/Befehlstaste + ALT + EINGABETASTE | Zelle ausführen, Markdownzelle unten einfügen  |          
| ALT + R                       | Ausführen aller Zellen     |                              
| Nach oben                            | Cursor nach oben oder in die vorherige Zelle bewegen    |             
| Nach unten                          | Cursor nach unten oder in die nächste Zelle bewegen |                  
| STRG/Befehlstaste + S           | Notebook speichern   |                                
| STRG/Befehlstaste + Nach-oben          | Zum Anfang der Zelle wechseln   |                             
| STRG/Befehlstaste + Nach-unten        | Zum Ende der Zelle wechseln |                                 
| Registerkarte                           | Codevervollständigung oder -einzug (bei aktivierter Fokusinitialisierung) |
| STRG/Befehlstaste + M           | Fokusinitialisierung aktivieren/deaktivieren  |                       
| STRG/Befehlstaste + ]           | Einziehen |                                         
| STRG/Befehlstaste + [           | Einzug entfernen  |                                        
| STRG/Befehlstaste + A           | Alle auswählen|                                      
| STRG/Befehlstaste + Z           | Rückgängig |                                           
| STRG/Befehlstaste + UMSCHALT + Z   | Wiederholen |                                           
| STRG/Befehlstaste + Y           | Wiederholen |                                           
| STRG/Befehlstaste + Pos1        | Zum Anfang der Zelle wechseln|                                
| STRG/Befehlstaste + Ende         | Zum Ende der Zelle wechseln   |                               
| STRG/Befehlstaste + Nach-links        | Ein Wort nach links wechseln |                               
| STRG/Befehlstaste + Nach-rechts       | Ein Wort nach rechts wechseln |                              
| STRG/Befehlstaste + RÜCKTASTE   | Wort davor löschen |                             
| STRG/Befehlstaste + Entf      | Wort danach löschen |                              
| STRG/Befehlstaste + /           | Kommentar für Zelle ein-/ausschalten

## <a name="troubleshooting"></a>Problembehandlung

* Wenn Sie keine Verbindung mit einem Notebook herstellen können, stellen Sie sicher, dass die WebSocket-Kommunikation **nicht** deaktiviert ist. Damit die Jupyter-Funktionen für Compute-Instanzen eingesetzt werden können, muss die WebSocket-Kommunikation aktiviert sein. Stellen Sie sicher, dass Ihr [Netzwerk Websocket-Verbindungen](how-to-access-azureml-behind-firewall.md?tabs=ipaddress#microsoft-hosts) zu *.instances.azureml.net und *.instances.azureml.ms erlaubt. 

* Wenn eine Compute-Instanz in einem Arbeitsbereich mit einem privaten Endpunkt bereitgestellt wird, kann [nur im virtuellen Netzwerk darauf zugegriffen werden](./how-to-secure-training-vnet.md). Wenn Sie benutzerdefiniertes DNS oder HOSTS-Dateien verwenden, fügen Sie einen Eintrag für „<Instanzname>.<Region>.instances.azureml.ms“ mit der privaten IP-Adresse des privaten Endpunkts Ihres Arbeitsbereichs hinzu. Weitere Informationen finden Sie im Artikel [Benutzerdefiniertes DNS](./how-to-custom-dns.md?tabs=azure-cli).

* Wenn Ihr Kernel abgestürzt ist und neu gestartet wurde, können Sie den folgenden Befehl ausführen, um das Jupyter-Protokoll anzuzeigen und weitere Details zu erfahren: `sudo journalctl -u jupyter`. Wenn Kernelprobleme weiterhin bestehen, erwägen Sie, eine Compute-Instanz mit mehr Arbeitsspeicher zu verwenden.

* Wenn ein Problem mit einem abgelaufenen Token aufgetreten ist, melden Sie sich von Ihrem Azure ML Studio ab, melden Sie sich erneut an, und starten Sie den Notebook-Kernel neu.
    
## <a name="next-steps"></a>Nächste Schritte

* [Ausführen Ihres ersten Experiments](tutorial-1st-experiment-sdk-train.md)
* [Sichern des Dateispeichers mit Momentaufnahmen](../storage/files/storage-snapshots-files.md)
* [Arbeiten in sicheren Umgebungen](./how-to-secure-training-vnet.md#compute-cluster)
