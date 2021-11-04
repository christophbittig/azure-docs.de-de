---
title: Erstellen einer leeren Lake-Datenbank
description: Erfahren Sie, wie Sie eine leere Lake-Datenbank in Azure Synapse Analytics erstellen, die problemlos hinzugefügt werden kann.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 9612fabc94e3425c42bb30e3075a56fb3f789626
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510443"
---
# <a name="how-to-create-an-empty-lake-database"></a>How-to: Erstellen einer leeren Lake-Datenbank

In diesem Artikel erfahren Sie, wie Sie mithilfe des Datenbank-Designers eine leere [Lake-Datenbank](./concepts-lake-database.md) in Azure Synapse Analytics erstellen. Der Database Designer erlaubt es Ihnen, auf einfache Art und Weise ohne Veschlüsselung eine Datenbank zu erstellen und bereitzustellen. 

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen zumindest eine Synapse Nutzerrollenzugangsberechtigung, um auf eine Lake-Datenbank-Vorlage aus der Gallerie zugreifen zu können.
- Synapse Administrator,Synapse Contributor oder Synapse Artifact Publisher berechtigungen sind für den Synapse-Arbeitsbereich erforderlich, um eine Lake-Datenbank zu erstellen.
- Bei Data Lake benötigt man eine Zugangsberechtigung für den Blob-Storage-Daten-Verteiler.

## <a name="create-lake-database-from-database-template"></a>Erstellen einer Lake-Datenbank aus Datenbankvorlagen
1. Auf Ihrem Azure-Synapse-Analytics-Arbeitsbereich **Home** hub wählen Sie links den Tab **Datei** aus. Die Registerkarte **Daten** wird geöffnet, und die Liste der Datenbanken wird angezeigt, die bereits in Ihrem Arbeitsbereich vorhanden sind.
2. Zeigen Sie auf die **+** Schaltfläche, und wählen wählen Sie dann **Lake-Datenbank (Vorschau)** aus.
![Screenshot: Erstellen einer leeren Lake-Datenbank](./media/create-empty-lake-database/create-empty-lakedb.png)
3. Die Registerkarte des Datenbank-Designers wird mit einer leeren Datenbank geöffnet.
4. Der Datenbank-Designer verfügt auf der rechten Seite über **Eigenschaften**, die konfiguriert werden müssen.
    - **Name:** Geben Sie Ihrer Datenbank einen Namen. Namen können nach der Veröffentlichung der Datenbank nicht mehr bearbeitet werden. Stellen Sie daher sicher, dass der name, den Sie auswählen, richtig ist.
    - **Beschreibung:** Die Angabe einer Beschreibung für Ihre Datenbank ist optional, ermöglicht Benutzern jedoch, den Zweck der Datenbank zu verstehen.
    - **Speicher für die Datenbank:** Dieser Abschnitt enthält die Standardspeicherinformationen für Tabellen in der Datenbank. Dieser Standardwert wird auf jede Tabelle in der Datenbank angewendet, es sei denn, sie wird für die Tabelle selbst überschrieben.
    - **Verknüpfter Dienst:** Dies ist der standardmäßige verknüpfte Dienst, der zum Speichern Ihrer Daten in Azure Data Lake Storage.  Der voreingestellte verlinkte Service vom Synapse-Arbeitsbereich wird angezeigt, aber Sie können den **verlinkten Service** zu jedem anderen ADLS-Speicherkonto abändern, das Sie wollen. 
    - **Eingabeordner:** Dient zum Festlegen des Standardkontainers und Ordnerpfads innerhalb dieses verknüpften Diensts mithilfe des Dateibrowsers.
    - **Datenformat**: Lake-Datenbanken in Azure Synapse Parquet- und Text mit Trennzeichen als Speicherformate für Daten.

> [!NOTE]
> Sie können die Standardspeichereinstellungen auf Tabellenbasis jederzeit überschreiben, und die Standardeinstellung bleibt anpassbar. Wenn Sie nicht sicher sind, was Sie auswählen möchten, können Sie dies später noch einmal überprüfen.
 
5. Um ein Verzeichnis der Datenbank hinzuzufügen, wählen Sie die Schaltfläche **+ Verzeichnis**. 
    - **Benutzerdefiniert** fügt ein neues Verzeichnis der Arbeitsfläche hinzu.
    - Wenn Sie ein neues Verzeichnis hinzufügen, öffnet **Aus Vorlage** eine Galerie, aus der Sie eine zu verwendende Datenbankvorlage auswählen können. Für weitere Informationen, siehe [Lake-Datenbank aus Datenbankvorlage erstellen](./create-lake-database-from-lake-database-templates.md).
    - Mit **Aus Data Lake** können Sie ein Verzeichnisschema importieren, das schon Daten in Ihrem Lake verwendet.
6. Wählen Sie **Benutzerdefiniert** aus. Ein neues Verzeichnis erscheint auf der Arbeitsfläche mit dem Namen Verzeichnis_1.
7. Sie können dann Verzeichnis_1 anpassen, indem Sie den Verzeichnisnamen, die Beschreibung, die Speichereinstellungen, Spalten und Bezüge einfügen. Weitere Informationen finden Sie unter [Ändern einer Lake-Datenbank.](./modify-lake-database.md)
8. Fügen Sie ein neues Verzeichnis von Data Lake hinzu, indem Sie **+ Verzeichnis** und dann **Von Data Lake** auswählen.
9. Es erscheint das Fenster **Ein externes Verzeichnis aus Data Lake erstellen**. Füllen Sie das Fenster mit den unteren Angaben aus und wählen Sie **Fortfahren** aus.
    - **Externer Verzeichnisname** ist der Name, den Sie dem Verzeichnis, das Sie gerade erstellen, geben wollen.
    - **Verlinkter Service** ist der verlinkte Service, der den Speicherort der Azure Data Lake beinhaltet, wo Ihre Datei zu finden ist.
    - **Eingabedatei oder -verzeichnis** benutzen Sie den Dateibrowser, um zu einer Datei zu navigieren und eine Datei in Ihrem Lake auszuwählen, die Sie als Verzeichnis erstellen möchten.
![Der Screenshot zeigt Ihnen die Optionen in dem Fenster Erstellen Sie ein externes Verzeichnis aus Data Lake](./media/create-empty-lake-database/create-from-lake.png)
    - Auf der nächsten Bildschirmanzeige gibt Ihnen Azure Synapse eine Vorschau von der Datei und erkennt das Schema.
    - Auf der Seite **Neue externe Tabelle** können Sie alle Einstellungen im Zusammenhang mit dem Datenformat und **Vorschaudaten** aktualisieren, um zu überprüfen, ob Synapse die Datei richtig identifiziert hat.
    - Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Erstellen** aus.
    - Ein neues Verzeichnis mit dem von Ihnen ausgewählten Namen wird nun der Arbeitsfläche hinzugefügt und der Abschnitt **Speichereinstellungen für das Verzeichnis** zeigt die Datei an, die Sie angegeben haben.
    
10. Jetzt ist es Zeit die angepasste Database zu veröffentlichen. Wenn Sie Git-Integration auf Ihrem Synapse-Arbeitsbereich verwenden, müssen Sie Ihre Änderungen durchführen und sie in den Kollaborationsbranch einspeisen. [Erfahren Sie mehr über die Quellcodeverwaltung in Azure Synapse](././cicd/../../cicd/source-control.md). Wenn Sie den Azure Live-Modus verwenden, könnten Sie „Veröffentlichen“ auswählen.
    - Ihre Datenbank wird auf Fehler überprüft, bevor Sie veröffentlicht wird. Alle gefundenen Fehler werden in dem Tab Benachrichtigungen angezeigt, und zwar mit Anweisungen, wie man den Fehler beheben kann.
    
       ![Der Screenshot des Prüfungsfensters zeigt die Validierungsfehler in der Datenbank an.](./media/create-empty-lake-database/validation-error.png)
    - Beim Veröffentlichen wird Ihre Datenbankschema im Azure Synapse Metastore erstellt. Nach der Veröffentlichung werden die Datenbank und die Verzeichnisse für andere Azure-Dienste sichtbar und die Metadata von Ihrer Datenbank kann in Apps wie Power BI oder Purview fließen.

11. Sie haben nun eine leere Lake-Datenbank in Azure Synapse erstellt und ihr mithilfe der Optionen **Benutzerdefiniert** und **Aus Data Lake Tabellen** hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Möglichkeiten des Database Designers, indem Sie den Link unten verwenden. 
- [Ändern einer Lake-Datenbank](./modify-lake-database.md)
- [Erfahren Sie mehr über die Lake-Datenbanken](./concepts-lake-database.md)
- [Erstellen Sie eine Lake-Datenbank anhand einer Lake-Datenbank-Vorlage](./create-lake-database-from-lake-database-templates.md)
