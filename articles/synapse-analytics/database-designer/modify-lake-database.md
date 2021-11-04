---
title: Ändern Sie eine Lake-Datenbank mit dem Database Designer ab.
description: Erfahren Sie, wie Sie mit dem Database Designer eine Lake-Datenbank ansehen, auf Ihre Bedürfnisse zuschneiden und veröffentlichen.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: fbd84d3999b5aec4ea04139b1d2933a366edb5e7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433078"
---
# <a name="how-to-modify-a-lake-database"></a>Wie ändere ich eine Lake-Datenbank ab?

In diesem Abschnitt erfahren Sie, wie Sie eine bestehende [Lake-Datenbank](./concepts-lake-database.md) in Azure Synapse mit dem Database Designer abändern. Der Database Designer erlaubt es Ihnen, auf einfache Art und Weise ohne Veschlüsselung eine Datenbank zu erstellen und bereitzustellen. 

## <a name="prerequisites"></a>Voraussetzungen

- Man benötigt eine Zugangsberechtigung zum Synapse-Administrator oder Synapse-Verteiler auf dem Synapse-Arbeitsbereich, um eine Lake-Datenbank zu erstellen.
- Bei Data Lake benötigt man eine Zugangsberechtigung für den Blob-Storage-Daten-Verteiler.

## <a name="modify-database-properties"></a>Abändern von Datenbanken
1. Auf Ihrem Azure-Synapse-Analytics-Arbeitsbereich **Home** hub wählen Sie links den Tab **Datei** aus. Der Tab **Datei** öffnet sich und Sie sehen eine Liste von Datenbanken, die auf Ihrem Arbeitsbereich bereits vorhanden sind.
2. Gleiten Sie über den Abschnitt **Datenbanken** und wählen Sie die Auslassungszeichen **...** gleich neben der Datenbank aus, die Sie abändern möchten, dann wählen sie **Öffnen (Vorschau)** .

    ![Ein Screenshot zeigt Ihnen wie Sie eine bereits bestehende Datenbank öffnen](./media/modify-lake-database/open-designer.png)

3. Der Tab Database Designer öffnet sich mit Ihrer ausgewählten Datenbank, die auf die Arbeitsfläche geladen wird.
4. Der Database Designer hat ein Fenster **Eigenschaften**, das geöffnet werden kann, indem man das Icon **Eigenschaften** oben rechts vom Tab auswählt. ![Der Screenshot zeigt die Position des Icons Eigenschaften](./media/modify-lake-database/properties-location.png).
    - **Name** Namen können nicht mehr nach Veröffentlichung der Datenbank bearbeitet werden, also vergewissern Sie sich, dass der von Ihnen gewählte Name richtig ist.
    - **Beschreibung** Es steht Ihnen frei, Ihrer Datenbank eine Beschreibung hinzuzufügen, aber es ermöglicht es den Nutzern, den Bestimmungszweck der Datenbank zu verstehen.
    - Der Abschnitt **Speichereinstellungen für Datenbanken** enthält die voreingestellten Speicherinformationen für Verzeichnisse in der Datenbank. Die Werkseinstellung wird für jedes Verzeichnis in der Datenbank angewandt, außer sie wird von dem Verzeichnis selbst verworfen.
    - Der **verlinkte Service** ist ein voreingestellter verlinkter Service, um Ihre Daten im Azure-Data-Lake-Speicher zu speichern. Der voreingestellte verlinkte Service vom Synapse-Arbeitsbereich wird angezeigt, aber Sie können den **verlinkten Service** zu jedem anderen ADLS-Speicherkonto abändern, das Sie wollen.  
    - Das **Eingabeverzeichnis**, das benutzt wird, um den Standardcontainer und den Ordnerpfad innerhalb dieses verlinkten Service zu erstellen, das der Dateibrowser verwendet.
    - **Datenformat** Lake-Datenbanken in Azure Synapse unterstützen Parquet-Dateien und Text mit Trennzeichen als Speicherformate für Dateien.
5. Um ein Verzeichnis der Datenbank hinzuzufügen, wählen Sie die Schaltfläche **+ Verzeichnis**. 
    - **Benutzerdefiniert** fügt ein neues Verzeichnis der Arbeitsfläche hinzu.
    - Wenn Sie ein neues Verzeichnis hinzufügen, öffnet **Aus Vorlage** eine Galerie, aus der Sie eine zu verwendende Datenbankvorlage auswählen können. Für weitere Informationen, siehe [Lake-Datenbank aus Datenbankvorlage erstellen](./create-lake-database-from-lake-database-templates.md).
    - Mit **Aus Data Lake** können Sie ein Verzeichnisschema importieren, das schon Daten in Ihrem Lake verwendet.
6. Wählen Sie **Benutzerdefiniert** aus. Ein neues Verzeichnis erscheint auf der Arbeitsfläche mit dem Namen Verzeichnis_1.
7. Sie können dann Verzeichnis_1 anpassen, indem Sie den Verzeichnisnamen, die Beschreibung, die Speichereinstellungen, Spalten und Bezüge einfügen. Unten sehen Sie die angepassten Verzeichnisse innerhalb des Datenbankabschnitts.
8. Fügen Sie ein neues Verzeichnis von Data Lake hinzu, indem Sie **+ Verzeichnis** und dann **Von Data Lake** auswählen.
9. Es erscheint das Fenster **Ein externes Verzeichnis aus Data Lake erstellen**. Füllen Sie das Fenster mit den unteren Angaben aus und wählen Sie **Fortfahren** aus.
    - **Externer Verzeichnisname** ist der Name, den Sie dem Verzeichnis, das Sie gerade erstellen, geben wollen.
    - **Verlinkter Service** ist der verlinkte Service, der den Speicherort der Azure Data Lake beinhaltet, wo Ihre Datei zu finden ist.
    - **Eingabedatei oder -verzeichnis** benutzen Sie den Dateibrowser, um zu einer Datei zu navigieren und eine Datei in Ihrem Lake auszuwählen, die Sie als Verzeichnis erstellen möchten.
![Der Screenshot zeigt Ihnen die Optionen in dem Fenster Erstellen Sie ein externes Verzeichnis aus Data Lake](./media/modify-lake-database/create-from-lake.png)
    - Auf der nächsten Bildschirmanzeige gibt Ihnen Azure Synapse eine Vorschau von der Datei und erkennt das Schema.
    - Sie landen auf den Seiten **Neues externes Verzeichnis**, wo Sie alle Einstellungen in Bezug auf das Datenformat auf den aktuellen Stand bringen können, und **Vorschau Datei**, um zu überprüfen, ob Azure Synapse die Datei richtig identifiziert hat.
    - Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Erstellen** aus.
    - Ein neues Verzeichnis mit dem von Ihnen ausgewählten Namen wird nun der Arbeitsfläche hinzugefügt und der Abschnitt **Speichereinstellungen für das Verzeichnis** zeigt die Datei an, die Sie angegeben haben.
10. Jetzt ist es Zeit die angepasste Database zu veröffentlichen. Wenn Sie Git-Integration auf Ihrem Synapse-Arbeitsbereich verwenden, müssen Sie Ihre Änderungen durchführen und sie in den Kollaborationsbranch einspeisen. [Erfahren Sie mehr über die Quellcodeverwaltung in Azure Synapse](././cicd/../../cicd/source-control.md). Wenn Sie den Azure Live-Modus verwenden, könnten Sie „Veröffentlichen“ auswählen.

     - Ihre Datenbank wird auf Fehler überprüft, bevor Sie veröffentlicht wird. Alle gefundenen Fehler werden in dem Tab Benachrichtigungen angezeigt, und zwar mit Anweisungen, wie man den Fehler beheben kann.

       ![Der Screenshot des Prüfungsfensters zeigt die Validierungsfehler in der Datenbank an.](./media/create-lake-database-from-lake-database-template/validation-error.png)
     - Beim Veröffentlichen wird Ihre Datenbankschema im Azure Synapse Metastore erstellt.  Nach der Veröffentlichung werden die Datenbank und die Verzeichnisse für andere Azure-Dienste sichtbar und die Metadata von Ihrer Datenbank kann in Apps wie Power BI oder Purview fließen.


## <a name="customize-tables-within-a-database"></a>Passen Sie Verzeichnisse innerhalb Ihrer Datenbank an.
Der Database Designer erlaubt es Ihnen, jegliche Verzeichnisse in Ihrer Datenbank voll und ganz anzupassen. Wenn Sie ein Verzeichnis auswählen, stehen Ihnen drei Tabs zur Verfügung, wobei ein jedes davon Einstellungen in Bezug auf das Verzeichnisschema oder die Metadata enthält.

### <a name="general"></a>Allgemein
Der **Allgemein**-Tab enthält Informationen, die sich auf das Verzeichnis selbst beziehen.
   - **Name** der Name des Verzeichnisses Der Verzeichnisname kann jedem einzigartigen Wert innerhalb der Datenbank angepasst werden. Mehrere Verzeichnisse mit demselben Namen sind nicht zulässig.
   - **Übernommen von** (optional) dies wird angezeigt, wenn ein Verzeichnis aus einer anderen Datenbankvorlage erstellt wurde. Es kann nicht bearbeitet werden und zeigt dem Nutzer an, aus welchem Vorlagenverzeichnis es stammt.
   - **Beschreibung** ist die Beschreibung des Verzeichnisses. Wenn das Verzeichnis aus einer Datenbankvorlage erstellt wurde, enthält es eine Beschreibung des Konzepts, das diesem Verzeichnis zugrunde liegt. Das Feld kann bearbeitet und so abgeändert werden, dass es mit der Beschreibung übereinstimmt, die auf Ihre Geschäftsanforderungen passen.
   - **Ordner anzeigen** zeigt den Namen des Ordners des Geschäftsbereiches an, unter welchem dieses Verzeichnis als Teil der Datenbankvorlage eingeordnet wurde. Benutzerdefinierte Verzeichnisse findet man bei „Andere“.

     ![Screenshot des Tabs Allgemein](./media/modify-lake-database/general-tab.png)
   - Zusätzlich gibt es einen zusammenklappbaren Abschnitt namens **Speichereinstellungen für das Verzeichnis**, der Einstellungen für die zugrunde liegenden Speicherinformationen, die vom Verzeichnis verwendet werden, bereitstellt.
   - **Von voreingestellter Datenbank übernehmen**  ist ein Optionsfeld, das festlegt, ob die unteren Speichereinstellungen von der Einstellung im Datenbank-Tab **Eigenschaften** übernommen werden oder individuell gemacht werden. Wenn Sie die Speicherwerte anpassen wollen, entfernen Sie das Häkchen aus dem Kontrollkästchen.
      - Der **verlinkte Service** ist ein voreingestellter verlinkter Service, um Ihre Daten im Azure-Data-Lake-Speicher zu speichern. Ändern Sie dies, um ein anderes ADLS-Konto zu wählen.     
      - **Eingabeordner** der Ordner in ADLS, wo die Datei, die in dieses Verzeichnis geladen wurde, zu finden ist. Dies kann über den Dateibrowser überarbeitet werden.
      - **Datenformat** das Datenformat der Datei im  **Eingabeordner** Lake-Datenbanken in Azure Synapse unterstützen Parquetdateien und Texte mit Trennzeichen als Speicherformate für Daten. Wenn das Datenformat nicht mit den Daten im Ordner übereinstimmt, werden Abfragen im Verzeichnis nicht gelingen.
   - Was das **Datenformat** von Texten mit Trennzeichen angeht, gibt es weitere Einstellungen:
        - **Kopfzeilen** Aktivieren Sie dieses Kontrollkästchen, wenn die Datei Kopfzeilen hat.
        - **Zeilenumbrüche** Aktivieren Sie dieses Kontrollkästchen, wenn die Datei irgendwelche Zeilenumbrüche enthält. Das vermeidet Probleme beim Formatieren.
        - **Datenkomprimierung** die Art von Komprimierung, die bei der Datei verwendet wurde.
        - **Trennzeichen** die Trennzeichen, die in der Datei verwendet wurden Unterstützte Werte sind: Komma (,), Tab (\t) und senkrechter Strich (|). 
   - Was Parquet-Datei angeht, gibt es die folgende Einstellung:
        - **Datenkomprimierung** die Art von Komprimierung, die bei der Datei verwendet wurde.

### <a name="columns"></a>Spalten
Der **Spalten**-Tab ist da zu finden, wo die Spalten für das Verzeichnis aufgelistet sind und abgeändert werden können. Auf diesem Tab gibt es zwei Listen von Spalten: **Standardspalten** und **Trennspalten**. **Standardspalten** sind jegliche Spalten, die Daten speichern, ist ein Primärschlüssel und wird sonst nicht zum Trennen von Daten benutzt. **Trennspalten** speichern auch Daten, aber sie teilen die zugrunde liegenden Daten auf Ordner auf, basierend auf den Werten, die die Spalte enthält. Jede Spalte hat die folgenden Eigenschaften.
![Screenshot des Spalten-Tabs](./media/modify-lake-database/columns-tab.png)
   - **Name** der Name der Spalte. Muss innerhalb des Verzeichnisses einzigartig sein.
   - **PK** oder Primärschlüssel. Zeigt an, ob die Spalte ein Primärschlüssel für das Verzeichnis ist. Nicht anwendbar bei Trennspalten.
   - **Beschreibung** eine Beschreibung der Spalte. Wenn die Spalte von einer Datenbankvorlage erstellt wurde, wird die Beschreibung des Konzepts, das dieser Spalte zugrunde liegt, sichtbar sein. Das Feld kann bearbeitet und so abgeändert werden, dass es mit der Beschreibung übereinstimmt, die auf Ihre Geschäftsanforderungen passen.
   - **Nullbarkeit** zeigt an, ob Nullwerte in dieser Spalte zulässig sind. Nicht anwendbar bei Trennspalten.
   - **Datentyp** legt den Datentyp für die Spalte fest, basierend auf der verfügbaren Liste von Spark-Daten-Typen. 
   - Mit **Format / Länge** kann man das Format oder die maximale Länge einer Spalte anpassen, je nach Datentyp. Date- und  Timestamp-Datentypen weisen Dropdown-Formate auf und andere Datentypen wie String haben ein Feld für eine maximale Länge. Nicht alle Datentypen haben einen Wert, da manche Datentypen eine festgelegte Länge haben.
Ganz oben auf dem **Spalten**-Tab ist eine Befehlsleiste, mit der man mit den Spalten interagieren kann.
   - **Nach Stichwort filtern** filtert die Liste der Spalten nach Elementen, die mit dem angegebenen Stichwort übereinstimmen.
   - Mit **+ Spalte** können Sie eine neue Spalte hinzufügen. Es gibt drei mögliche Optionen.
      - **Neue Spalte** erstellt eine neue benutzerdefinierte Standardspalte.
      - **Aus Vorlage** öffnet das Suchfenster und hilft Ihnen, Spalten aus einer Datenbankvorlage zu identifizieren, um sie in Ihr Verzeichnis einzuspeisen. Wenn Ihre Datenbank nicht mit Hilfe einer Datenbankvorlage erstellt wurde, steht diese Option nicht zur Verfügung.
      - **Trennspalte** fügt eine neue benutzerdefinierte Trennspalte hinzu.
   - **Kopieren** dupliziert die ausgewählte Spalte. Kopierte Spalten sind immer vom selben Typ wie die ausgewählte Spalte.
   - **Spalte konvertieren** wird verwendet, um die ausgewählte **Sandardspalte** in eine **Trennspalte** und umgekehrt abzuändern. Diese Option wird inaktiviert, wenn Sie mehrere Spalten verschiedener Arten ausgewählt haben oder die ausgewählte Spalte kann nicht konvertiert werden, weil ein **PK** oder eine **Nullbarkeit** bei der Spalte verwendet wurde.
   - **Löschen** löscht die ausgewählten Spalten aus dem Verzeichnis. Diese Aktion kann nicht rückgängig gemacht werden. 

#### <a name="partition-columns"></a>Trennspalten

Trennspalten werden verwendet, um die physischen Daten in Ihrer Datenbank aufzuteilen, basierend auf den in jenen Spalten verwendeten Werten. Trennspalten bieten eine einfache Methode, um Daten auf  Datenträgern auf effizientere Einheiten aufzuteilen. Trennspalten in Azure Synapse sind immer am Ende des Verzeichnisschemas. Außerdem werden Sie vom Anfang bis zum Ende verwendet, wenn Trennordner erstellt werden. Zum Beispiel, wenn Ihre Trennspalten Jahr und Monat sind, werden Sie auf eine Struktur in ADLS wie diese stoßen:

![Screenshot, der die Anordnung von Ordnern beim Trennen zeigt: 2020 -> Jan, Feb -> Dateien](./media/modify-lake-database/partition-example.png)

Wo Datei1 und Datei2 alle Reihen beinhalten, wo die Werte von Jahr und Monat jeweils 2020 und Jan waren. Je mehr Trennspalten einem Verzeichnis hinzugefügt werden, desto mehr Dateien werden dieser Anordnung hinzugefügt, was die Gesamtdateigröße einer Trennspalte kleiner macht.

Azure Synapse führt diese Anordnung nicht durch oder erschafft sie, indem es Trennspalten einem Verzeichnis hinzufügt. Daten müssen entweder mit Synapse Pipelines oder mit einem Spark-Notebook in das Verzeichnis geladen werden, damit die Trennstruktur gebildet werden kann. 

### <a name="relationships"></a>Beziehungen

Das Tab Beziehungen ermöglicht es Ihnen, Beziehungen zwischen Verzeichnissen in der Datenbank anzugeben. Beziehungen im Database Designer sind informativer Natur und schränken die zugrunde liegenden Daten in keiner Weise ein. Sie werden von anderen Microsoft-Anwendungen gelesen, können verwendet werden, um Transformationen zu beschleunigen oder um Geschäftsnutzern Einblicke darin zu gewähren, wie die Verzeichnisse verknüpft sind. Das Fenster Beziehungen hält folgende Informationen bereit.
![Screenshot vom Beziehungen-Tab](./media/modify-lake-database/relationships-tab.png)
   - **Beziehungen aus (Verzeichnis)** sind dann der Fall, wenn ein oder mehr Verzeichnisse fremde Schlüssel haben, die mit diesem Verzeichnis im Zusammenhang stehen. Dies wird manchmal auch als Elternbeziehung bezeichnet.
   - **Beziehungen zu (Verzeichnis)** sind dann der Fall, wenn ein Verzeichnis, das einen fremden Schlüssel hat, mit einem anderen Verzeichnis im Zusammenhang steht. Dies wird manchmal auch als Kindbeziehung bezeichnet.
   - Beide Arten von Beziehung haben die folgenden Eigenschaften.
      - **Aus Verzeichnis** das Elternverzeichnis in der Beziehung oder die „eine“ Seite.
      - **Aus Spalte** die Spalte im Elternverzeichnis, auf der die Beziehung basiert.
      - **Nach Verzeichnis** das Kindverzeichnis in der Beziehung, oder die „vielseitige“ Seite.
      - **Nach Spalte** die Spalte im Kindverzeichnis, auf der die Beziehung basiert.
Ganz oben auf dem **Beziehungen**-Tab, gibt es eine Befehlsleiste, die verwendet werden kann, um zwischen den Beziehungen zu interagieren
   - **Nach Stichwort filtern** filtert die Liste der Spalten nach Elementen, die mit dem angegebenen Stichwort übereinstimmen.
   - Mit **+ Beziehung** können Sie eine neue Beziehung hinzufügen. Es stehen zwei Optionen zur Verfügung.
      - **Aus Verzeichnis** erschafft eine neue Beziehung zu einem anderen Verzeichnis, und zwar aus dem Verzeichnis, mit dem Sie gerade arbeiten.
      - **Nach Verzeichnis** erschafft eine neue Beziehung von einem anderen Verzeichnung zu dem Verzeichnis, mit dem Sie gerade arbeiten.
      - **Aus Vorlage** öffnet ein Suchfenster und ermöglicht es Ihnen, aus den Beziehungen in der Datenbankvorlage zu wählen, um sie in Ihre Datenbank einzuspeisen. Wenn Ihre Datenbank nicht mit Hilfe einer Datenbankvorlage erstellt wurde, steht diese Option nicht zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die Möglichkeiten des Database Designers, indem Sie den Link unten verwenden. 
- [Erstellen Sie eine leere Lake-Datenbank](./create-empty-lake-database.md)
- [Erfahren Sie mehr über die Lake-Datenbanken](./concepts-lake-database.md)
- [Erstellen Sie eine Lake-Datenbank anhand einer Lake-Datenbank-Vorlage](./create-lake-database-from-lake-database-templates.md)
