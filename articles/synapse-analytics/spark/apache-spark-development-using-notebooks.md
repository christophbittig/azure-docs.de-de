---
title: Verwendung von Synapse-Notebooks
description: In diesem Artikel erfahren Sie, wie Sie Synapse-Notebooks erstellen und entwickeln, um Datenvorbereitung und -visualisierung durchzuführen.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/08/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: de703ea3f85025657b4c71089f158b7d1db2189d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013599"
---
# <a name="create-develop-and-maintain-synapse-notebooks-in-azure-synapse-analytics"></a>Erstellen, Entwickeln und Verwalten von Synapse-Notebooks in Azure Synapse Analytics

Ein Synapse-Notebook ist eine Weboberfläche zum Erstellen von Dateien, die Live-Code, Visualisierungen und beschreibenden Text enthalten. Notebooks sind ein guter Ausgangspunkt, um Ideen zu überprüfen und schnelle Experimente zu verwenden, um Erkenntnisse aus Ihren Daten zu gewinnen. Notebooks werden auch häufig bei der Datenvorbereitung, Datenvisualisierung, Machine Learning und andere Big Data-Szenarien verwendet.

Mit einem Synapse-Notebook können Sie: 

* Ohne Einrichtungsaufwand sofort loslegen.
* Daten mit integrierten Sicherheitsfeatures auf Unternehmensniveau schützen.
* Daten in Rohformaten (CSV, TXT, JSON usw.), verarbeiteten Dateiformaten (Parquet, Delta Lake, ORC usw.) und tabellarischen SQL-Datendateien gegen Spark und SQL analysieren.
* Produktiv sein mit erweiterten Funktionen zur Dokumenterstellung und integrierter Datenvisualisierung.

In diesem Artikel wird beschrieben, wie Notebooks in Synapse Studio verwendet werden.


## <a name="create-a-notebook"></a>Erstellen eines Notebooks

Ein Notebook kann auf zwei Arten erstellt werden. Sie können ein neues Notebook erstellen oder ein vorhandenes Notebook aus dem **Objekt-Explorer** in einen Synapse-Arbeitsbereich importieren. Synapse-Notebooks erkennen Standard Jupyter Notebook IPYNB-Dateien.

![Ein Screenshot, der das Erstellen eines neuen Notebooks oder Importieren eines Notebooks zeigt](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Entwickeln von Notebooks

Notebooks bestehen aus Zellen, bei denen es sich um einzelne Code- oder Textblöcke handelt, die unabhängig oder als Gruppe ausgeführt werden können.

Wir bieten umfangreiche Vorgänge zum Entwickeln von Notebooks:
+ [Hinzufügen einer Zelle](#add-a-cell)
+ [Festlegen einer primären Sprache](#set-a-primary-language)
+ [Verwenden mehrerer Sprachen](#use-multiple-languages)
+ [Verwenden von temporären Tabellen zum Verweisen auf Daten in verschiedenen Sprachen](#use-temp-tables-to-reference-data-across-languages)
+ [IDE-artiges IntelliSense](#ide-style-intellisense)
+ [Codeausschnitte](#code-snippets)
+ [Textzelle mit Symbolleisten-Schaltflächen formatieren](#format-text-cell-with-toolbar-buttons)
+ [Zellenvorgang rückgängig machen/wiederholen](#undo-redo-cell-operation)
+ [Kommentieren von Codezellen](#Code-cell-commenting)
+ [Verschieben einer Zelle](#move-a-cell)
+ [Löschen einer Zelle](#delete-a-cell)
+ [Reduzieren einer Zelleneingabe](#collapse-a-cell-input)
+ [Reduzieren einer Zellenausgabe](#collapse-a-cell-output)
+ [Notebook-Gliederung](#notebook-outline)

<h3 id="add-a-cell">Hinzufügen einer Zelle</h3>

Es gibt mehrere Möglichkeiten, um Ihrem Notebook eine neue Zelle hinzuzufügen.

1. Zeigen Sie mit dem Mauszeiger auf den Bereich zwischen zwei Zellen, und wählen Sie **Code** oder **Markdown** aus.
    ![Ein Screenshot, der „Eine Zelle mit der Schaltfläche Zelle hinzufügen“ in Azure Notebook zeigt](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)

2. Verwenden Sie [aznb-Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode). Drücken Sie **A**, um eine Zelle oberhalb der aktuellen Zelle einzufügen. Drücken Sie **B**, um eine Zelle unterhalb der aktuellen Zelle einzufügen.

---

<h3 id="set-a-primary-language">Festlegen einer primären Sprache</h3>

Synapse-Notebooks unterstützen vier Apache Spark-Sprachen:

* PySpark (Python)
* Spark (Scala)
* Spark SQL
* .NET Spark (C#)

Sie können die primäre Sprache für neu hinzugefügte Zellen in der Dropdownliste in der oberen Befehlsleiste festlegen.

   ![Ein Screenshot der die „Standard-Synapse-Sprache“ zeigt](./media/apache-spark-development-using-notebooks/synapse-default-language.png)


<h3 id="use-multiple-languages">Verwenden mehrerer Sprachen</h3>

Sie können in einem Notebook mehrere Sprachen verwenden, indem Sie den richtigen Magic-Befehl für die Sprache am Anfang einer Zelle angeben. In der folgenden Tabelle werden die Magic-Befehle zum Wechseln von Zellensprachen aufgelistet.

|Magic-Befehl |Sprache | BESCHREIBUNG |  
|---|------|-----|
|%%pyspark| Python | Eine **Python**-Abfrage im Spark-Kontext ausführen.  |
|%%spark| Scala | Eine **Scala**-Abfrage im Spark-Kontext ausführen.  |  
|%%sql| SparkSQL | Eine **SparkSQL**-Abfrage im Spark-Kontext ausführen.  |
|%%csharp | .NET für Spark (C#) | Führen Sie eine Abfrage vom Typ **.NET für Spark (C#)** für Spark-Kontext aus. |

Die folgende Abbildung zeigt ein Beispiel dafür, wie Sie eine PySpark-Abfrage mit dem Magic-Befehl **%%pyspark** schreiben können oder eine SparkSQL-Abfrage mit dem Magic-Befehl **%%sql** in einem **Spark (Scala)** -Notebook. Beachten Sie, dass die primäre Sprache für das Notebook auf PySpark festgelegt ist.

   ![Ein Screenshot der die „Magic-Befehle“ von Synapse Spark zeigt](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)


<h3 id="use-temp-tables-to-reference-data-across-languages&quot;>Verwenden von temporären Tabellen zum Verweisen auf Daten in verschiedenen Sprachen</h3>

Sie können in einem Synapse-Notebook nicht direkt auf Daten oder Variablen in verschiedenen Sprachen verweisen. In Spark kann auf eine temporäre Tabelle in verschiedenen Sprachen verwiesen werden. Im Folgenden finden Sie ein Beispiel, wie Sie einen `Scala`-Datenrahmen in `PySpark` und `SparkSQL` mittels einer temporären Spark-Tabelle als Problemumgehung lesen können.

1. Lesen Sie in Zelle 1 einen Datenrahmen von einem SQL-Poolconnector mithilfe von Scala, und erstellen Sie eine temporäre Tabelle.

   ```scala
   %%spark
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. In Zelle 2 fragen Sie die Daten mithilfe von Spark SQL ab.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. In Zelle 3 verwenden Sie die Daten in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

<h3 id="ide-style-intellisense">IDE-artiges IntelliSense</h3>

Synapse-Notebooks sind in den Monaco-Editor integriert, um den Zellen-Editor mit IDE-style IntelliSense auszustatten. Syntaxhervorhebung, Fehlermarkierungen und automatische Codevervollständigungen helfen Ihnen dabei, Code zu schreiben und Probleme schneller zu erkennen.

Die IntelliSense-Funktionen befinden sich in unterschiedlichen Stadien der Entwicklung für verschiedene Sprachen. In der folgenden Tabelle können Sie sehen, was unterstützt wird.

|Languages| Syntaxhervorhebung | Syntaxfehlermarkierungen  | Codevervollständigung für Syntax | Codevervollständigung für Variablen| Codevervollständigung für Systemfunktionen| Codevervollständigung für Benutzerfunktionen| Intelligenter Einzug | Codefaltung|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Spark (Scala)|Ja|Ja|Ja|Ja|Ja|Ja|-|Ja|
|SparkSQL|Ja|Ja|Ja|Ja|Ja|-|-|-|
|.NET für Spark (C#)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|

>[!Note]
> Eine aktive Spark-Sitzung ist erforderlich, um von der Codevervollständigung für Variablen, der Codevervollständigung für Systemfunktionen, der Codevervollständigung für Benutzerfunktionen für .NET für Spark (C#) zu profitieren.

<h3 id="code-snippets">Codeausschnitte</h3>

Synapse-Notebooks stellen Codeausschnitte bereit, die die Eingabe häufig verwendeter Codemuster erleichtern, wie z. B. das Konfigurieren Ihrer Spark-Sitzung, das Lesen von Daten als Spark DataFrame oder das Zeichnen von Diagrammen mit matplotlib usw.

Codeausschnitte werden in [Tastaturkurzbefehlen im IDE Stil in IntelliSense](#ide-style-intellisense) gemeinsam mit anderen Vorschlägen angezeigt. Der Inhalt der Codeausschnitte richtet sich nach der Codezellensprache. Sie können die verfügbaren Ausschnitte anzeigen, indem Sie **Snippet** oder beliebige Schlüsselwörter eingeben, die im Titel für den Ausschnitt im Codezellen-Editor angezeigt werden. Wenn Sie beispielsweise **read** eingeben, wird eine Liste der Codeausschnitte zum Lesen von Daten aus verschiedenen Datenquellen angezeigt.

![Animierte GIF-Datei mit Synapse-Codeausschnitten](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)


<h3 id="format-text-cell-with-toolbar-buttons">Textzelle mit Symbolleisten-Schaltflächen formatieren</h3>

Sie können die Formatschaltflächen auf der Textzellen-Symbolleiste verwenden, um allgemeine Markdown-Aktionen durchzuführen. Darunter fällt das Formatieren von Text als fett oder kursiv, Absätze/Kopfzeilen mittels einer Dropdownliste, das Einfügen von Code, von unsortierten Listen, von sortierten Listen, Hyperlinks und Bildern aus URLs.

  ![Ein Screenshot, der die „Synapse-Textzellensymbolleiste“ zeigt](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar-preview.png)

---

<h3 id="undo-redo-cell-operation">Zellenvorgang rückgängig machen/wiederholen</h3>

Wählen Sie die Schaltfläche **Rückgängig** / **Wiederholen** oder drücken Sie **Z** / **Umschalt+Y** um die letzten Zellenvorgänge aufzuheben. Sie können jetzt bis zu 10 der letzten zurückliegenden Zellenaktionen rückgängig machen/wiederherstellen.

   ![Ein Screenshot, der die „Synapse-Rückgängig-Zellen“ in Azure Notebooks zeigt](./media/apache-spark-development-using-notebooks/synapse-undo-cells-aznb.png)

Unterstützte Rückgängig-Zellenvorgänge:
+ Zelle einfügen/löschen: Sie können die Löschvorgänge widerrufen, indem Sie **Rückgängig** auswählen. Der Textinhalt wird zusammen mit der Zelle beibehalten.
+ Eine Zelle neu anordnen.
+ Umschalten von Parametern.
+ Konvertieren zwischen der Codezelle und der Markdown-Zelle.

> [!NOTE]
> Die Textvorgänge in der Zelle und die Codezellenkommentare sind nicht rückgängig zu machen.
> Sie können jetzt bis zu 10 der letzten zurückliegenden Zellenaktionen rückgängig machen/wiederherstellen.


---

<h3 id="Code-cell-commenting">Kommentieren von Codezellen</h3>

1. Wählen Sie auf der Notebook-Symbolleiste die Schaltfläche **Kommentare,** um den Bereich **Kommentare** zu öffnen.

   ![Ein Screenshot, der die Schaltfläche für „Synapse-Kommentare“ zeigt](./media/apache-spark-development-using-notebooks/synapse-comments-button.png)

2. Wählen Sie Code in der Codezelle aus, klicken Sie im Bereich **Kommentare** auf **Neu**, fügen Sie Kommentare hinzu, und klicken Sie dann auf die Schaltfläche **Kommentar posten**, um zu speichern.

   ![Ein Screenshot des neuen Synapse-Kommentars](./media/apache-spark-development-using-notebooks/synapse-new-comments.png)

3. Sie können einen **Kommentar bearbeiten**, einen **Thread auflösen** oder einen **Thread löschen** ausführen, indem Sie neben Ihrem Kommentar auf die Schaltfläche **Mehr** klicken. 

   ![Ein Screenshot, der „Kommentar bearbeiten“ in Synapse zeigt](./media/apache-spark-development-using-notebooks/synapse-edit-comments.png)

---

<h3 id="move-a-cell">Verschieben einer Zelle</h3>

Klicken Sie auf die linke Seite einer Zelle, und ziehen Sie sie an die gewünschte Position. 
    ![Animiertes GIF von einer Zellverschiebung in Synapse](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

<h3 id="delete-a-cell">Löschen einer Zelle</h3>

Wählen Sie zum Löschen einer Zelle die Schaltfläche „Löschen“ auf der rechten Seite der Zelle. 

Sie können auch [Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode) verwenden. Drücken Sie **UMSCHALT+D**, um die aktuelle Zelle zu löschen. 

   ![Ein Screenshot, der „Eine Zelle löschen“ in Azure-Notebook zeigt](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

<h3 id="collapse-a-cell-input">Reduzieren einer Zelleneingabe</h3>

Wählen Sie auf der Zellensymbolleiste die Auslassungspunkte für **Weitere Befehle** (...) und dann **Eingabe ausblenden** aus, um die Eingabe der aktuellen Zelle zu reduzieren. Zum Erweitern wählen Sie **Eingabe anzeigen** aus, während die Zelle reduziert ist.

   ![Ein Animiertes GIF von „Reduzieren einer Zelleneingabe“ in Azure Notebook](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

<h3 id="collapse-a-cell-output">Reduzieren einer Zellenausgabe</h3>

Wählen Sie auf der Zellensymbolleiste die Auslassungspunkte für **Weitere Befehle** (...) und dann **Ausgabe ausblenden** aus, um die Ausgabe der aktuellen Zelle zu reduzieren. Zum Erweitern wählen Sie **Ausgabe anzeigen** aus, während die Zelle reduziert ist.

   ![Ein Animiertes GIF von „Reduzieren einer Zellenausgabe“ in Azure Notebook](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

<h3 id="notebook-outline">Notebook-Gliederung</h3>

Das Feld „Gliederung (Inhaltsverzeichnis)“ stellt den ersten Markdownheader einer beliebigen Markdownzelle in einem Randleistenfenster für die schnelle Navigation dar. Die Gliederungsrandleiste ist in der Größe veränderbar und reduzierbar, um den Bildschirm optimal anzupassen. Sie können die Schaltfläche **Gliederung** auf der Notebook-Befehlsleiste auswählen, um die Randleiste zu öffnen oder auszublenden.

![Ein Screenshot, der „Ein Umriss von Azure-Notebook" zeigt](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-outline.png)

---


## <a name="run-notebooks"></a>Ausführen von Notebooks

Sie können die Codezellen in Ihrem Notebook einzeln oder alle gleichzeitig ausführen. Status und Fortschritt jeder Zelle werden im Notebook dargestellt.

### <a name="run-a-cell"></a>Ausführen einer Zelle

Es gibt mehrere Methoden, um den Code in einer Zelle auszuführen.

1. Zeigen Sie mit dem Mauszeiger auf die Zelle, die Sie ausführen möchten, und wählen Sie die Schaltfläche **Zelle ausführen** aus, oder drücken Sie **STRG+EINGABE**.

   ![Ein Screenshot, der „Ausführen einer Zelle 1“ zeigt](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Verwenden Sie [Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode). Drücken Sie **UMSCHALT+EINGABE**, um die aktuelle Zelle auszuführen und die Zelle darunter auszuwählen. Drücken Sie **ALT+EINGABE**, um die aktuelle Zelle auszuführen und darunter eine neue Zelle einzufügen.

---

### <a name="run-all-cells"></a>Ausführen aller Zellen
Wählen Sie die Schaltfläche **Alle ausführen** aus, um alle Zellen im aktuellen Notebook nacheinander auszuführen.

   ![Ein Screenshot, der „Ausführen aller Zellen“ zeigt](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Ausführen aller darüber- oder darunterliegenden Zellen

Erweitern Sie die Dropdownliste über die Schaltfläche **Alle ausführen**, und wählen Sie dann **Zellen oberhalb ausführen** aus, um alle Zellen oberhalb der aktuellen Zelle nacheinander auszuführen. Wählen Sie **Zellen unterhalb ausführen** aus, um alle Zellen unterhalb der aktuellen nacheinander auszuführen.

   ![Ein Screenshot, der „Ausführen von Zellen, die sich oberhalb oder unterhalb befinden“ in Azure Notebook zeigt](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Abbrechen aller ausgeführten Zellen

Wählen Sie die Schaltfläche **Alle abbrechen** aus, um die ausgeführten oder in der Warteschlange wartenden Zellen abzubrechen. 
   ![Ein Screenshot, der „Abbrechen aller Zellen“ in Azure Notebook zeigt](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---

### <a name="notebook-reference"></a>Notebookverweis

Sie können den Magic-Befehl ```%run <notebook path>``` verwenden, um im Kontext des aktuellen Notebooks auf ein anderes Notebook zu verweisen. Alle im Referenznotebook definierten Variablen sind im aktuellen Notebook verfügbar. Der Magic-Befehl ```%run``` unterstützt geschachtelte Aufrufe, aber keine rekursiven Aufrufe. Sie erhalten eine Ausnahme, wenn die Anweisungstiefe größer als **fünf** ist.  

Beispiel: ``` %run /<path>/Notebook1 { "parameterInt": 1, "parameterFloat": 2.5, "parameterBool": true,  "parameterString": "abc" } ```.

Notebookverweise funktionieren sowohl im interaktiven Modus als auch in der Synapse-Pipeline.

> [!NOTE]
> - Der ```%run```-Befehl unterstützt derzeit nur das Übergeben eines absoluten Pfads oder Notebooknamens nur als Parameter, ein relativer Pfad wird nicht unterstützt. 
> - Der ```%run```-Befehl unterstützt derzeit nur bis zu 4 Parameterwerttypen: `int`, `float`, `bool`, `string`. Der Variablenersetzungsvorgang wird nicht unterstützt.
> - Die Notebooks, auf die verwiesen wird, müssen veröffentlicht werden. Sie müssen die Notebooks veröffentlichen, um darauf verweisen zu können. Synapse Studio erkennt die nicht veröffentlichten Notebooks aus dem Git-Repository nicht. 
> - Referenzierte Notebooks unterstützen keine Anweisung, deren die Tiefe größer als **fünf** ist.
>

---

### <a name="variable-explorer"></a>Variablen-Explorer

Synapse-Notebooks verfügen über einen integrierten Variablen-Explorer, in dem Sie die Liste der Variablennamen, -typen, -längen und -werte in der aktuellen Spark-Sitzung für PySpark-Zellen (Python) anzeigen können. Weitere Variablen werden automatisch angezeigt, wenn sie in den Codezellen definiert sind. Durch Klicken auf die einzelnen Spaltenkopfzeilen, werden die Variablen in der Tabelle sortiert.

Sie können die Schaltfläche **Variablen** in der Notebook-Befehlsleiste auswählen, um den Variablen-Explorer zu öffnen oder auszublenden.

![Ein Screenshot, der den „Variablen-Explorer“ in Azure Notebook zeigt](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-variable-explorer.png)

> [!NOTE]
> Der Variablen-Explorer unterstützt nur Python.

---

### <a name="cell-status-indicator"></a>Zellenstatusindikator

Unterhalb der Zelle wird ein schrittweiser Zellenausführungsstatus angezeigt, damit Sie den aktuellen Fortschritt verfolgen können. Nachdem die Ausführung der Zelle abgeschlossen wurde, wird eine Ausführungszusammenfassung mit Gesamtdauer und Endzeit angezeigt, die zur späteren Bezugnahme dort auch aufbewahrt wird.

![Ein Screenshot, der den „Zellenstatus“ zeigt](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark-Statusanzeige

Ein Synapse-Notebook basiert ausschließlich auf Spark. Codezellen werden im serverlosen Apache Spark-Pool remote ausgeführt. Eine Spark-Auftragsstatusanzeige wird mit einem Statusbalken in Echtzeit angezeigt, um Ihnen den Status der Auftragsausführung zu verdeutlichen.
Anhand der Anzahl der Aufgaben pro Auftrag oder Phase können Sie die parallele Ebene Ihres Spark-Auftrags identifizieren. Sie können außerdem die Spark-Benutzeroberfläche eines spezifischen Auftrags (oder einer Phase) erweitern, indem Sie den Link im Auftragsnamen (oder Phasennamen) auswählen.


![Ein Screenshot, der eine „Spark-Statusanzeige“ zeigt](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-Sitzungskonfiguration

Sie können in **Sitzung konfigurieren** die Timeoutdauer sowie die Anzahl und Größe der Executors angeben, die für die aktuelle Spark-Sitzung gelten sollen. Sie müssen die Spark-Sitzung neu starten, damit die Konfigurationsänderungen wirksam werden. Alle zwischengespeicherten Notebook-Variablen werden gelöscht.

[![Ein Screenshot, der die „Sitzungsverwaltung“ zeigt](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Magic-Befehl config für Spark-Sitzung
Sie können auch Spark-Sitzungseinstellungen über einen **%%configure**-Magic-Befehl angeben. Die Spark-Sitzung muss neu gestartet werden, damit die Einstellungen wirksam werden. Wir empfehlen Ihnen, **%%configure** am Anfang Ihres Notebooks auszuführen. Hier ist ein Beispiel. Die vollständige Liste der gültigen Parameter finden Sie unter https://github.com/cloudera/livy#request-body. 

```json
%%configure
{
    //You can get a list of valid parameters to config the session from https://github.com/cloudera/livy#request-body.
    "driverMemory":"28g", // Recommended values: ["28g", "56g", "112g", "224g", "400g", "472g"]
    "driverCores":4, // Recommended values: [4, 8, 16, 32, 64, 80]
    "executorMemory":"28g",
    "executorCores":4, 
    "jars":["abfs[s]://<file_system>@<account_name>.dfs.core.windows.net/<path>/myjar.jar","wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>/myjar1.jar"],
    "conf":{
    //Example of standard spark property, to find more available properties please visit:https://spark.apache.org/docs/latest/configuration.html#application-properties.
        "spark.driver.maxResultSize":"10g",
    //Example of customized property, you can specify count of lines that Spark SQL returns by configuring "livy.rsc.sql.num-rows".
        "livy.rsc.sql.num-rows&quot;:&quot;3000" 
    }
}
```
> [!NOTE]
> - Es wird empfohlen, „DriverMemory“ und „ExecutorMemory“ in %%configure auf denselben Wert zu setzen. Dies gilt auch für „driverCores“ und „executorCores“.
> - Sie können den Magic-Befehl für die Konfiguration der Spark-Sitzung in Synapse-Pipelines verwenden. Er wird nur wirksam, wenn er auf der obersten Ebene aufgerufen wird. Die im Notebook, auf das verwiesen wird, verwendete „%%configure“-Eigenschaft wird ignoriert.
> - Die Spark-Konfigurationseigenschaften müssen im „conf“-Textkörper verwendet werden. Wir unterstützen keine Verweise auf oberster Ebene für die Spark-Konfigurationseigenschaften.
>

## <a name="bring-data-to-a-notebook"></a>Einfügen von Daten in ein Notebook

Sie können Daten aus Azure Blob Storage, Azure Data Lake Store Gen 2 und dem SQL-Pool laden, wie in den folgenden Codebeispielen gezeigt.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Lesen einer CSV-Datei aus Azure Data Lake Store Gen2 als Spark-Datenrahmen

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Lesen einer CSV-Datei aus Azure Blob Storage als Spark-Datenrahmen

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Lesen von Daten aus einem primären Speicherkonto

Sie können auf Daten im primären Speicherkonto direkt zugreifen. Es besteht keine Notwendigkeit, die geheimen Schlüssel bereitzustellen. Klicken Sie im Daten-Explorer mit der rechten Maustaste auf eine Datei, und wählen Sie **Neues Notebook** aus, um ein neues Notebook mit einem automatisch generierten Datenextraktor anzuzeigen.

![Ein Screenshot, der von „Daten aus Zelle“ zeigt](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)


## <a name="ipython-widgets"></a>IPython-Widgets

Widgets sind ereignisbehaftete Python-Objekte, die eine Darstellung im Browser besitzen – häufig als Steuerelement wie ein Schieberegler, ein Textfeld usw. IPython-Widgets funktionieren nur in der Python-Umgebung und werden in anderen Sprachen (z. B. Scala, SQL, C#) noch nicht unterstützt. 

### <a name="to-use-ipython-widget"></a>So verwenden Sie das IPython-Widget
1. Sie müssen zuerst das Modul `ipywidgets` importieren, um das Jupyter Widget-Framework zu verwenden.
   ```python
   import ipywidgets as widgets
   ```
2. Sie können die Funktion `display` der obersten Ebene verwenden, um ein Widget zu rendern, oder einen Ausdruck vom Typ **widget** in der letzten Zeile der Codezelle belassen.
   ```python
   slider = widgets.IntSlider()
   display(slider)
   ```

   ```python
   slider = widgets.IntSlider()
   slider
   ```
   
3. Führen Sie die Zelle aus, und das Widget wird im Ausgabebereich angezeigt.

   ![Ein Screenshot, der den „Schiebereglers für IPython-Widgets“ zeigt](./media/apache-spark-development-using-notebooks/ipython-widgets-slider.png)

4. Sie können mehrere `display()`-Aufrufe verwenden, um dieselbe Widgetinstanz mehrmals zu rendern, wobei diese aber miteinander synchronisiert bleiben.

   ```python
   slider = widgets.IntSlider()
   display(slider)
   display(slider)
   ```

   ![Ein Screenshot, der die „Schieberegler für IPython-Widgets“ zeigt](./media/apache-spark-development-using-notebooks/ipython-widgets-multiple-sliders.png)

5. Um zwei Widgets unabhängig voneinander zu rendern, erstellen Sie zwei Widgetinstanzen:

   ```python
   slider1 = widgets.IntSlider()
   slider2 = widgets.IntSlider()
   display(slider1)
   display(slider2)
   ```


### <a name="supported-widgets"></a>Unterstützte Widgets

|Widgettyp|Widgets|
|--|--|
|Numerische Widgets|IntSlider, FloatSlider, FloatLogSlider, IntRangeSlider, FloatRangeSlider, IntProgress, FloatProgress, BoundedIntText, BoundedFloatText, IntText, FloatText|
|Boolesche Widgets|ToggleButton, Checkbox, Valid|
|Auswahlwidgets|Dropdown, RadioButtons, Select, SelectionSlider, SelectionRangeSlider, ToggleButtons, SelectMultiple|
|Zeichenfolgenwidgets|Text, Text area, Combobox, Password, Label, HTML, HTML Math, Image, Button|
|Wiedergabewidgets (Animation)|Date picker, Color picker, Controller (Datumsauswahl, Farbauswahl, Controller)|
|Container-/Layoutwidgets|Box, HBox, VBox, GridBox, Accordion, Tabs, Stacked|


### <a name="known-limitations"></a>Bekannte Einschränkungen

1. Die folgenden Widgets werden noch nicht unterstützt. Sie können aber wie folgt eine Problemumgehung befolgen:

   |Funktionalität|Problemumgehung|
   |--|--|
   |`Output`-Widget|Sie können stattdessen die `print()`-Funktion verwenden, um Text in stdout zu schreiben.|
   |`widgets.jslink()`|Sie können die `widgets.link()`-Funktion verwenden, um zwei ähnliche Widgets zu verknüpfen.|
   |`FileUpload`-Widget| Noch nicht unterstützt.|

2. Die von Synapse bereitgestellte globale `display`-Funktion unterstützt nicht die Anzeige mehrerer Widgets in einem Aufruf (d. h. `display(a, b)`), was sich von der IPython-Funktion `display` unterscheidet.
3. Wenn Sie ein Notebook schließen, das IPython-Widget enthält, können Sie es erst anzeigen oder damit interagieren, wenn Sie die entsprechende Zelle erneut ausführen.


---
## <a name="save-notebooks"></a>Speichern von Notebooks

Sie können ein einzelnes Notebook oder alle Notebooks in Ihrem Arbeitsbereich speichern.

1. Um an einem einzelnen Notebook vorgenommene Änderungen zu speichern, wählen Sie auf der Notebook-Befehlsleiste die Schaltfläche **Veröffentlichen** aus.

   ![Ein Screenshot, der die „Veröffentlichungseinstellungen“ zeigt](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Um alle Notebooks in Ihrem Arbeitsbereich zu speichern, wählen Sie die Schaltfläche **Alle veröffentlichen** auf der Befehlsleiste des Arbeitsbereichs aus. 

   ![Ein Screenshot, der die Einstellung „Alle veröffentlichen“ zeigt](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

In den Notebook-Eigenschaften können Sie konfigurieren, ob die Zellenausgabe beim Speichern eingeschlossen werden soll.

   ![Ein Screenshot, der die“ Notebookeigenschaften“ zeigt](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

<!--
## Export a notebook
You can Export your notebook to other standard formats. Synapse notebook supports to be exported into: 

+ Standard Notebook file(.ipynb) that is usually used for Jupyter notebooks. 
+ HTML file(.html) that can be opened from browser directly.  
+ Python file(.py).  
+ Latex file(.tex).  

   ![Screenshot of notebook-export](./media/apache-spark-development-using-notebooks/synapse-notebook-export.png)

-->

## <a name="magic-commands"></a>Magic-Befehle
Sie können bekannte Jupyter-Magic-Befehle in Synapse-Notebooks verwenden. In der folgenden Liste finden Sie die derzeit verfügbaren Magic-Befehle. [Teilen Sie uns Ihre Anwendungsfälle auf GitHub mit](https://github.com/MicrosoftDocs/azure-docs/issues/new), damit wir weitere Magic-Befehle erstellen können, um Ihre Anforderungen zu erfüllen.

> [!NOTE]
> Nur die folgenden Magic-Befehle werden in einer Synapse-Pipeline unterstützt: %%pyspark, %%spark, %%csharp, %%sql. 
>


Verfügbare Zeilen-Magics: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [%run](#notebook-reference), [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Verfügbare Zeilen-Magics: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages), [%%html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrieren eines Notebooks

### <a name="add-a-notebook-to-a-pipeline"></a>Hinzufügen eines Notebooks zu einer Pipeline

Wählen Sie in der oberen rechten Ecke **Zur Pipeline hinzufügen** aus, um ein Notebook zu einer vorhandenen Pipeline hinzuzufügen oder eine neue Pipeline zu erstellen.

![Ein Screenshot der das „ Hinzufügen eines Notebooks zur Pipeline“ zeigt](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Festlegen einer Parameterzelle

Um Ihr Notebook zu parametrisieren, wählen Sie die Auslassungspunkte (...) aus, um auf der Zellensymbolleiste auf **Weitere Befehle** zuzugreifen. Klicken Sie dann auf **Parameterzelle umschalten**, um die Zelle als Parameterzelle festzulegen.

![Ein Screenshot, der das „Umschalten von Parametern“ in Azure Notebook zeigt](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory sucht nach der Parameterzelle und behandelt diese Zelle als Standard für die Parameter, die zur Ausführungszeit übermittelt werden. Die Ausführungs-Engine fügt eine neue Zelle mit Eingabeparametern unter der Parameterzelle hinzu, um die Standardwerte zu überschreiben. 


### <a name="assign-parameters-values-from-a-pipeline"></a>Zuweisen von Parameterwerten über eine Pipeline

Sobald Sie ein Notebook mit Parametern erstellt haben, können Sie dieses über eine Pipeline mit der Synapse Analytics-Aktivität ausführen. Nachdem Sie die Aktivität zu Ihrem Pipelinecanvas hinzugefügt haben, können Sie die Parameterwerte auf der Registerkarte **Einstellungen** im Abschnitt **Basisparameter** festlegen. 

![Ein Screenshot, der das „Zuweisen eines Parameters“ zeigt](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Beim Zuweisen von Parameterwerten können Sie die [Pipelineausdruckssprache](../../data-factory/control-flow-expression-language-functions.md) oder [Systemvariablen](../../data-factory/control-flow-system-variables.md) verwenden.



## <a name="shortcut-keys"></a>Tastenkombinationen

Ähnlich wie Jupyter-Notebooks verfügen Synapse-Notebooks über eine modale Benutzeroberfläche. Mit der Tastatur werden unterschiedliche Aktionen ausgeführt, je nachdem, in welchem Modus sich die Notebook-Zelle befindet. Synapse-Notebooks unterstützen die folgenden zwei Modi für eine bestimmte Codezelle: Befehlsmodus und Bearbeitungsmodus.

1. Eine Zelle befindet sich im Befehlsmodus, wenn Sie kein Textcursor zur Eingabe auffordert. Wenn sich eine Zelle im Befehlsmodus befindet, können Sie das Notebook als Ganzes bearbeiten, aber keine Eingaben in einzelne Zellen vornehmen. Sie wechseln in den Befehlsmodus, indem Sie `ESC` drücken oder mit der Maus außerhalb des Editor-Bereichs einer Zelle klicken.

   ![Ein Screenshot, der den „Befehlsmodus“ zeigt](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. Der Bearbeitungsmodus wird durch einen Textcursor angezeigt, der Sie zur Eingabe im Editor-Bereich auffordert. Wenn sich eine Zelle im Bearbeitungsmodus befindet, können Sie etwas in die Zelle eingeben. Sie wechseln in den Bearbeitungsmodus, indem Sie `Enter` drücken oder mit der Maus auf den Editor-Bereich einer Zelle klicken.
   
   ![Ein Screenshot der den „Bearbeitungsmodus“ zeigt](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Tastenkombinationen im Befehlsmodus

| Aktion |Tastenkombinationen für Synapse-Notebooks  |
|--|--|
|Aktuelle Zelle ausführen und die darunter auswählen | UMSCHALT+EINGABE |
|Aktuelle Zelle ausführen und darunter einfügen | ALT+EINGABE |
|Aktuelle Zelle ausführen| STRG+EINGABE |
|Zelle darüber auswählen| Nach oben |
|Zelle darunter auswählen| Nach unten |
|Vorherige Zelle auswählen| K |
|Nächste Zelle auswählen| J |
|Zelle oberhalb einfügen| Ein |
|Zelle unterhalb einfügen| B |
|Ausgewählte Zellen löschen| UMSCHALT+D |
|In den Bearbeitungsmodus wechseln| EINGABETASTE |

---

### <a name="shortcut-keys-under-edit-mode"></a>Tastenkombinationen im Bearbeitungsmodus


Mithilfe der folgenden Tastenkombinationen können Sie in Synapse-Notebooks leichter navigieren und Code ausführen, während sie sich im Bearbeitungsmodus befinden.

| Action |Tastenkombinationen für Synapse-Notebooks  |
|--|--|
|Cursor nach oben verschieben | Nach oben |
|Cursor nach unten verschieben|Nach unten|
|Rückgängig|STRG+Z|
|Wiederholen|STRG+Y|
|Auskommentieren/Auskommentierung aufheben|STRG+/|
|Wort davor löschen|STRG+RÜCKTASTE|
|Wort danach löschen|STRG+DELETE|
|Zum Anfang der Zelle wechseln|STRG+POS1|
|Zum Ende der Zelle wechseln |STRG+ENDE|
|Ein Wort nach links wechseln|STRG+NACH-LINKS|
|Ein Wort nach rechts wechseln|STRG+NACH-RECHTS|
|Alle auswählen|STRG+A|
|Einziehen| STRG+]|
|Einzug entfernen|STRG+[|
|In den Befehlsmodus wechseln| Esc |

---

## <a name="next-steps"></a>Nächste Schritte
- [Beispiele für Synapse-Notebooks](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Schnellstart: Erstellen eines Apache Spark-Pools in Azure Synapse Analytics mithilfe von Webtools](../quickstart-apache-spark-notebook.md)
- [Was ist Apache Spark in Azure Synapse Analytics?](apache-spark-overview.md)
- [Verwenden von .NET für Apache Spark mit Azure Synapse Analytics](spark-dotnet.md)
- [Dokumentation zu .NET für Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
