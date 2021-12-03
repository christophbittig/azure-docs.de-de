---
title: Erstellen Sie mithilfe einer Datenbankvorlage eine Lake-Datenbank in Azure Synapse.
description: Erfahren Sie, wie Sie eine Lake-Datenbank anhand einer Datenbankvorlage untersuchen, anpassen und erstellen.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: b657ed3d840345e36c38ef1a97723e67bfc5a38b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456820"
---
# <a name="how-to-create-a-lake-database-from-database-templates"></a>Gewusst wie: Erstellen einer Lake-Datenbank aus einer Datenbankvorlage

In diesem Artikel erfahren Sie, wie Sie die Azure Synapse-Datenbankvorlagen verwenden, um die Erstellung einer [Lake-Datenbank](./concepts-lake-database.md) zu vereinfachen. Durch die Verwendung von Datenbankvorlagen wird Ihre Datenbank mit umfassende semantischen Informationen versehen, sodass die Endbenutzer der Daten leichter nachvollziehen können, welche Daten zur Verfügung stehen und wie sie zu verwenden sind.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen zumindest eine Synapse Nutzerrollenzugangsberechtigung, um auf eine Lake-Datenbank-Vorlage aus der Gallerie zugreifen zu können.
- Man benötigt eine Zugangsberechtigung zum Synapse-Administrator oder Synapse-Verteiler auf dem Synapse-Arbeitsbereich, um eine Lake-Datenbank zu erstellen.
- Bei Data Lake benötigt man eine Zugangsberechtigung für den Blob-Storage-Daten-Verteiler.

## <a name="create-lake-database-from-database-template"></a>Erstellen einer Lake-Datenbank aus Datenbankvorlagen

1. Wählen Sie in Ihrem Azure Synapse Analytics-Arbeitsbereich im **Start**-Hub zunächst **Knowledge Center** und dann **Katalog durchsuchen** aus. Die Registerkarte **Lake-Datenbankvorlagen** wird geöffnet.
2. In der Kategorie **Lake-Datenbankvorlagen** werden standardisierte Datenbankvorlagen aufgeführt, die für eine bestimmte Branche verfügbar sind.
   1. Sie können die Registerkarte **Lake-Datenbankvorlagen** auch über Hub **Daten**, **+** Neue Ressource hinzufügen, **Katalog durchsuchen** öffnen.
3. Wählen Sie die für Sie relevante Branche aus (z. B. **Einzelhandel**), und klicken Sie auf **Weiter**, um zur Erkundung des Datenmodells zu navigieren.
4. Sie gelangen zum Zeichenbereich für Datenbanken und können die in der Vorlage verfügbaren Tabellen untersuchen. Standardmäßig wird eine Auswahl der am häufigsten verwendeten Tabellen in dieser Vorlage angezeigt. Der Zeichenbereich umfasst verschiedene Tools, mit denen Sie im Entitätsbeziehungsdiagramm navigieren können.
    - **An Fenstergröße anpassen**: Passt alle Tabellen im Zeichenbereich an den Anzeigebereich an.
    - **Vergrößern**: Vergrößert die Anzeige im Zeichenbereich.
    - **Verkleinern**: Verkleinert die Anzeige im Zeichenbereich.
    - **Zoomregler**: Steuert den Zoomfaktor.
    - **Zoomvorschau**: Zeigt eine Vorschau des Zeichenbereichs an.
    - **Alle erweitern**/**Alle reduzieren**: Zeigt mehr oder weniger Spalten innerhalb einer Tabelle im Zeichenbereich an.
    - **Zeichenbereich löschen**: Löscht alle Tabellen im Zeichenbereich. ![Seite zur Erkundung des Zeichenbereichs, mit Anzeige von Beispieltabellen und Steuerelementen](./media/create-lake-database-from-lake-database-template/canvas-overview.png)

5. Auf der linken Seite wird eine Liste der Ordner angezeigt, die Elemente der Vorlage enthalten, die Sie dem Zeichenbereich hinzufügen können. Es gibt verschiedene Steuerelemente, die Sie unterstützen können.
    - **Suchfelder** zum Suchen nach Tabellen basierend auf einem Begriff. Der Begriff wird in den Vorlagentabellen, Spalten und Beschreibungen gesucht.
    - **Filtertabellen**, um ganze Abschnitte des Modells auszuwählen, das untersucht werden soll.
      - **Unternehmensvorlagen** zur Auswahl von Tabellen aus jedem **Geschäftsbereich**, um ein „Best of“ anzuzeigen.
      - Mit **Geschäftsbereichsvorlagen** werden in erster Linie Tabellen aus einem einzigen **Geschäftsbereich** ausgewählt, sie enthalten aber auch verwandte Tabellen aus anderen **Geschäftsbereichen**.
    - **Geschäftsbereiche** sind Ordner mit Tabellen, die sich auf dieses Geschäftskonstrukt beziehen. Beispielsweise enthält „Budget & Prognose“ Tabellen im Zusammenhang mit der Verwaltung von Budgets.
    - Sie können Geschäftsbereichsordner erweitern, um die Tabellen anzuzeigen, und das Kontrollkästchen aktivieren, um sie dem Zeichenbereich hinzuzufügen. 
    - Ausgewählte Tabellen können über das Kontrollkästchen entfernt werden.

6. Sie können eine Tabelle im Zeichenbereich auswählen. Der Tabelleneigenschaftenbereich wird mit den Registerkarten „Allgemein“, „Spalten“ und „Beziehungen“ geöffnet.
    - Die Registerkarte „Allgemein“ enthält Informationen zur Tabelle, z. B. den Namen und die Beschreibung.
    - Die Registerkarte „Spalten“ enthält Details zu allen Spalten, aus denen die Tabelle stammt, z. B. Spaltennamen und Datentypen.
    - Auf der Registerkarte „Beziehungen“ werden die eingehenden und ausgehenden Beziehungen der Tabelle mit anderen Tabellen im Zeichenbereich aufgelistet.
    
7. Um schnell Tabellen hinzuzufügen, die mit den Tabellen im Zeichenbereich zusammenhängen, klicken Sie auf die Auslassungspunkte rechts neben dem Tabellennamen und wählen dann **Verwandte Tabellen hinzufügen** aus. Alle Tabellen mit vorhandenen Beziehungen werden dem Zeichenbereich hinzugefügt.

8. Sobald der Zeichenbereich alle Tabellen enthält, die Ihre Anforderungen erfüllen, wählen Sie **Datenbank erstellen** aus, um mit der Erstellung der Lake-Datenbank fortzufahren. Die neue Datenbank wird im Datenbank-Designer angezeigt, und Sie können sie an Ihre geschäftlichen Anforderungen anpassen. 

9. Der Datenbank-Designer enthält auf der rechten Seite weitere **Eigenschaften**, die konfiguriert werden müssen.
    - **Name**: Geben Sie Ihrer Datenbank einen Namen. Namen können nach der Veröffentlichung der Datenbank nicht mehr bearbeitet werden. Stellen Sie daher sicher, dass der name, den Sie auswählen, richtig ist.
    - **Beschreibung** Es steht Ihnen frei, Ihrer Datenbank eine Beschreibung hinzuzufügen, aber es ermöglicht es den Nutzern, den Bestimmungszweck der Datenbank zu verstehen.
    - Der Abschnitt **Speichereinstellungen für Datenbanken** enthält die voreingestellten Speicherinformationen für Verzeichnisse in der Datenbank. Dieser Standardwert wird auf jede Tabelle in der Datenbank angewendet, es sei denn, sie wird für die Tabelle selbst überschrieben.
    - Der **verlinkte Service** ist ein voreingestellter verlinkter Service, um Ihre Daten im Azure-Data-Lake-Speicher zu speichern. Der voreingestellte verlinkte Service vom Synapse-Arbeitsbereich wird angezeigt, aber Sie können den **verlinkten Service** zu jedem anderen ADLS-Speicherkonto abändern, das Sie wollen. 
    - Das **Eingabeverzeichnis**, das benutzt wird, um den Standardcontainer und den Ordnerpfad innerhalb dieses verlinkten Service zu erstellen, das der Dateibrowser verwendet.
    - **Datenformat** Lake-Datenbanken in Azure Synapse unterstützen Parquet-Dateien und Text mit Trennzeichen als Speicherformate für Dateien.
> [!NOTE]
> Sie können die Standardspeichereinstellungen auf Tabellenbasis jederzeit überschreiben, und die Standardeinstellung bleibt anpassbar. Wenn Sie nicht sicher sind, was Sie auswählen möchten, können Sie dies später noch einmal überprüfen.
 
![Screenshot: Datenbank-Designer mit geöffnetem Eigenschaftenbereich](./media/create-lake-database-from-lake-database-template/designer-overview.png)


10. Sie können mit dem Anpassen von Tabellen, Spalten und Beziehungen beginnen, die von der Datenbankvorlage geerbt wurden. Sie können der Datenbank bei Bedarf auch benutzerdefinierte Tabellen, Spalten und Beziehungen hinzufügen. Weitere Informationen zum Ändern einer Lake-Datenbank finden Sie unter [Ändern einer Lake-Datenbank](./modify-lake-database.md).

11. Jetzt ist es Zeit die angepasste Database zu veröffentlichen. Wenn Sie Git-Integration auf Ihrem Synapse-Arbeitsbereich verwenden, müssen Sie Ihre Änderungen durchführen und sie in den Kollaborationsbranch einspeisen. [Erfahren Sie mehr über die Quellcodeverwaltung in Azure Synapse](././cicd/../../cicd/source-control.md). Wenn Sie Synapse im Livemodus verwenden, können Sie **Veröffentlichen** auswählen.
     - Ihre Datenbank wird auf Fehler überprüft, bevor Sie veröffentlicht wird. Alle gefundenen Fehler werden in dem Tab Benachrichtigungen angezeigt, und zwar mit Anweisungen, wie man den Fehler beheben kann.
      
       ![Der Screenshot des Prüfungsfensters zeigt die Validierungsfehler in der Datenbank an.](./media/create-lake-database-from-lake-database-template/validation-error.png)
     - Beim Veröffentlichen wird Ihre Datenbankschema im Azure Synapse Metastore erstellt. Nach der Veröffentlichung werden die Datenbank und die Verzeichnisse für andere Azure-Dienste sichtbar und die Metadata von Ihrer Datenbank kann in Apps wie Power BI oder Purview fließen.

12.  Sie haben nun eine Lake-Datenbank mithilfe einer Lake-Datenbankvorlage in Azure Synapse erstellt. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Möglichkeiten des Database Designers, indem Sie den Link unten verwenden. 
- [Ändern einer Lake-Datenbank](./modify-lake-database.md)
- [Erfahren Sie mehr über die Lake-Datenbanken](./concepts-lake-database.md)
