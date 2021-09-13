---
title: Dynamisches Festlegen von Spaltennamen in Datenflüssen
description: Dieses Tutorial enthält Schritte zum dynamischen Festlegen von Spaltennamen in Datenflüssen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/17/2021
ms.openlocfilehash: 96143f39811658c2794b46f3504a1a604264ab13
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112542803"
---
# <a name="dynamically-set-column-names-in-data-flows"></a>Dynamisches Festlegen von Spaltennamen in Datenflüssen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bei der Verarbeitung von Daten für ETL-Aufträge müssen Sie die Spaltennamen oft ändern, bevor Sie die Ergebnisse schreiben. Manchmal ist dies erforderlich, um Spaltennamen an einem bekannten Zielschema auszurichten. In anderen Fällen müssen Sie Spaltennamen möglicherweise zur Runtime auf sich entwickelnden Schemas basierend festlegen. In diesem Tutorial erfahren Sie, wie Sie mit Datenflüssen Spaltennamen für Ihre Zieldateien und Datenbanktabellen dynamisch mithilfe externer Konfigurationsdateien und Parameter festlegen.

Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

## <a name="prerequisites"></a>Voraussetzungen
* **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure-Speicherkonto**. Sie verwenden den ADLS-Speicher als *Quelldatenspeicher* und *Senkendatenspeicher*. Wenn Sie kein Speicherkonto besitzen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

In diesem Schritt erstellen Sie eine Data Factory und öffnen die Data Factory-Benutzeroberfläche, um eine Pipeline in der Data Factory zu erstellen.

1. Öffnen Sie **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird derzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus.
1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.
1. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:
    * Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.
    * Wählen Sie **Neu erstellen** aus, und geben Sie den Namen einer Ressourcengruppe ein. Weitere Informationen zu Ressourcengruppen finden Sie unter [Was ist Azure Resource Manager?](../azure-resource-manager/management/overview.md)    
1. Wählen Sie unter **Version** die Option **V2**.
1. Wählen Sie unter **Standort** einen Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich in anderen Regionen befinden.
1. Klicken Sie auf **Erstellen**.
1. Nach Abschluss der Erstellung wird der Hinweis im Benachrichtigungscenter angezeigt. Wählen Sie **Zu Ressource wechseln** aus, um zur Data Factory-Seite zu navigieren.
1. Klicken Sie auf **Erstellen und überwachen**, um die Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu starten.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Erstellen einer Pipeline mit einer Datenflussaktivität

In diesem Schritt erstellen Sie eine Pipeline mit einer Datenflussaktivität.

1. Wählen Sie auf der ADF-Startseite die Option **Pipeline erstellen** aus.
1. Geben Sie auf der Registerkarte **Allgemein** der Pipeline als **Namen** der Pipeline **DeltaLake** ein.
1. Setzen Sie auf der oberen Leiste der Factory den Schieberegler **Datenfluss debuggen** auf „Ein“. Der Debugmodus ermöglicht das interaktive Testen von Transformationslogik mit einem aktiven Spark-Cluster. Die Aufwärmphase von Datenflussclustern dauert 5 bis 7 Minuten. Den Benutzern wird empfohlen, zuerst den Debugmodus zu aktivieren, wenn sie die Entwicklung eines Datenflusses planen. Weitere Informationen finden Sie unter [Debugmodus](concepts-data-flow-debug-mode.md).

    ![Datenflussaktivität](media/tutorial-data-flow/dataflow1.png)
1. Erweitern Sie im Bereich **Aktivitäten** das Accordion-Element **Verschieben und transformieren**. Ziehen Sie die **Datenfluss**-Aktivität per Drag & Drop aus dem Bereich auf die Pipelinecanvas.

    ![Screenshot der Pipelinecanvas, auf der die Datenflussaktivität abgelegt werden kann](media/tutorial-data-flow/activity1.png)
1. Wählen Sie im Popupfenster **Datenfluss hinzufügen** die Option **Neuen Datenfluss erstellen** aus, und geben Sie dem Datenfluss den Namen **DynaCols**. Klicken Sie anschließend auf „Fertig stellen“.    

## <a name="build-dynamic-column-mapping-in-data-flows"></a>Erstellen einer dynamischen Spaltenzuordnung in Datenflüssen

In diesem Tutorial verwenden wir eine Beispieldatei zur Filmbewertung und benennen einige Felder in der Quelle in einen neuen Satz von Zielspalten um, die sich im Laufe der Zeit ändern können. Die Datasets, die Sie unten erstellen, sollten auf diese Filme-CSV-Datei in Ihrem Blob Storage- oder ADLS Gen2-Speicherkonto verweisen. [Laden Sie die Filmdatei hier herunter](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB.csv), und speichern Sie die Datei in Ihrem Azure-Speicherkonto.

![Abschließender Flow](media/data-flow/dynacols-1.png "Abschließender Flow")

### <a name="tutorial-objectives"></a>Ziele des Tutorials

Sie erfahren, wie Sie Spaltennamen mithilfe eines Datenflusses dynamisch festlegen.

1. Erstellen Sie ein Quelldataset für die Filme-CSV-Datei.
1. Erstellen Sie ein Suchdataset für eine JSON-Konfigurationsdatei für die Feldzuordnung.
1. Konvertieren Sie die Spalten aus der Quelle in die Zielspaltennamen.

### <a name="start-from-a-blank-data-flow-canvas"></a>Ausgangspunkt: leere Datenflusscanvas

Zunächst richten Sie die Datenflussumgebung für jeden der unten beschriebenen Mechanismen zum Bereitstellen der Daten in ADLS Gen2 ein.

1. Klicken Sie auf die Quelltransformation, und nennen Sie sie ```movies1```.
1. Klicken Sie im unteren Bereich neben dem Dataset auf die neue Schaltfläche.
1. Wählen Sie entweder Blob oder ADLS Gen2, je nachdem, wo Sie die Datei „moviesDB.csv“ von oben gespeichert haben.
1. Fügen Sie eine zweite Quelle hinzu, die wir als Quelle für die JSON-Konfigurationsdatei zum Suchen von Feldzuordnungen verwenden.
1. Nennen Sie dies ```columnmappings```.
1. Zeigen Sie für das Dataset auf eine neue JSON-Datei, in der eine Konfiguration für die Spaltenzuordnung gespeichert wird. Für dieses Tutorialbeispiel ist eine Einfügung in die JSON-Datei möglich:
    ```
    [
    {"prevcolumn":"title","newcolumn":"movietitle"},
    {"prevcolumn":"year","newcolumn":"releaseyear"}
    ]
    ```

1. Legen Sie für diese Quelleinstellungen ```array of documents``` fest.
1. Fügen Sie eine dritte Quelle hinzu, und nennen Sie sie ```movies2```. Konfigurieren Sie sie genauso wie ```movies1```.
   
### <a name="parameterized-column-mapping"></a>Parametrisierte Spaltenzuordnung

In diesem ersten Szenario legen Sie Ausgabespaltennamen in Ihrem Datenfluss fest, indem Sie die Spaltenzuordnung auf Basis des Abgleichs von Eingangsfeldern mit einem Parameter festlegen, der ein Zeichenfolgenarray von Spalten ist, und jeden Arrayindex mit der Ordnungsposition der Eingangsspalte abgleichen. Wenn Sie diesen Datenfluss von einer Pipeline aus ausführen, können Sie bei jeder Pipelineausführung unterschiedliche Spaltennamen festlegen, indem Sie diesen Zeichenfolgenarrayparameter an die Datenflussaktivität senden.

![Parameter](media/data-flow/dynacols-3.png "Parameter")

1. Wechseln Sie zurück zum Datenflussdesigner, und bearbeiten Sie den oben erstellten Datenfluss.
1. Klicken Sie auf die Registerkarte „Parameter“.
1. Erstellen Sie einen neuen Parameter, und wählen Sie den Zeichenfolgenarray-Datentyp aus.
1. Geben Sie als Standardwert ```['a','b','c']``` ein.
1. Verwenden Sie die oberste ```movies1```-Quelle, um die Spaltennamen so zu ändern, dass Sie sie diesen Arraywerten zuordnen können.
1. Fügen Sie eine Select-Transformation hinzu. Mit der Select-Transformation werden Eingangsspalten neuen Spaltennamen für die Ausgabe zugeordnet.
1. Wir werden die ersten drei Spaltennamen in die neuen, im Parameter definierten Namen ändern.
1. Fügen Sie hierzu im unteren Bereich drei regelbasierte Zuordnungseinträge hinzu.
1. Für die erste Spalte ist die Abgleichsregel ```position==1``` und der Name ```$parameter1[1]```.
1. Befolgen Sie das gleiche Muster für Spalte 2 und 3.
 
    ![Auswahltransformation](media/data-flow/dynacols-4.png "Auswahltransformation")

1. Klicken Sie auf die Registerkarten „Überprüfen“ und „Datenvorschau“ der Select-Transformation, um die neuen Spaltennamenwerte anzuzeigen. ```(a,b,c)``` ersetzen die ursprünglichen Spaltennamen „movie“, „title“ und „genres“.
   
### <a name="create-a-cached-lookup-of-external-column-mappings"></a>Erstellen einer zwischengespeicherten Suche nach externen Spaltenzuordnungen

Als Nächstes erstellen wir eine zwischengespeicherte Senke für eine spätere Suche. Der Cache liest eine externe JSON-Konfigurationsdatei, die verwendet werden kann, um bei jeder Pipelineausführung Ihres Datenflusses dynamisch Spalten umzubenennen.

1. Wechseln Sie zurück zum Datenflussdesigner, und bearbeiten Sie den oben erstellten Datenfluss. Fügen Sie der ```columnmappings```-Quelle eine Sink-Transformation hinzu.
1. Legen Sie den Senkentyp auf ```Cache``` fest.
1. Wählen Sie unter Einstellungen ```prevcolumn``` als Schlüsselspalte aus.

### <a name="lookup-columns-names-from-cached-sink"></a>Suchen von Spaltennamen von einer zwischengespeicherten Senke aus

Nachdem Sie nun den Inhalt der Konfigurationsdatei im Arbeitsspeicher gespeichert haben, können Sie Eingangsspaltennamen dynamisch neuen Ausgangsspaltennamen zuordnen.

1. Wechseln Sie zurück zum Datenfluss-Designer, und bearbeiten Sie den oben erstellten Datenfluss. Klicken Sie auf die ```movies2```-Quelltransformation.
1. Fügen Sie eine Select-Transformation hinzu. Dieses Mal verwenden wir die Select-Transformation, um Spaltennamen basierend auf dem Zielnamen in der JSON-Konfigurationsdatei umzubenennen, die in der zwischengespeicherten Senke gespeichert wird.
1. Fügen Sie eine regelbasierte Zuordnung hinzu. Verwenden Sie für die Abgleichsbedingung diese Formel: ```!isNull(cachedSink#lookup(name).prevcolumn)```.
1. Verwenden Sie für den Ausgabespaltennamen diese Formel: ```cachedSink#lookup($$).newcolumn```.
1. Wir haben alle Spaltennamen gefunden, die mit der ```prevcolumn```-Eigenschaft aus der externen JSON-Konfigurationsdatei übereinstimmen, und jede Übereinstimmung in den neuen ```newcolumn```-Namen umbenannt.
1. Klicken Sie in der Select-Transformation auf die Registerkarten „Datenvorschau“ und „Überprüfen“. Nun sollten die neuen Spaltennamen aus der externen Zuordnungsdatei angezeigt werden.

![Quelle 2](media/data-flow/dynacols-2.png "Quelle 2")

## <a name="next-steps"></a>Nächste Schritte

* Die vollständige Pipeline für dieses Tutorial können Sie [hier](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DynaColsPipe.zip) herunterladen.
* Weitere Informationen zu [Datenflusssenken](data-flow-sink.md)
