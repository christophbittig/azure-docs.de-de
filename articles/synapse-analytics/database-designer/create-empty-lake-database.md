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
ms.openlocfilehash: 2cff956fa8507ec1dcbf8e3ab3576d19ecebf526
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060810"
---
# <a name="how-to-create-an-empty-lake-database"></a>How-to: Erstellen einer leeren Lake-Datenbank

In diesem Artikel erfahren Sie, wie Sie mithilfe des Datenbank-Designers eine leere [Lake-Datenbank](./concepts-lake-database.md) in Azure Synapse Analytics erstellen. Mit dem Datenbank-Designer können Sie ganz einfach eine Datenbank erstellen und bereitstellen, ohne Code schreiben zu müssen. 

## <a name="prerequisites"></a>Voraussetzungen

- Zum Untersuchen einer Lake-Datenbankvorlage aus dem Katalog sind mindestens Berechtigungen für die Synapse-Benutzerrolle erforderlich.
- Synapse Administrator,Synapse Contributor oder Synapse Artifact Publisher berechtigungen sind für den Synapse-Arbeitsbereich erforderlich, um eine Lake-Datenbank zu erstellen.
- Storage Berechtigungen für Mitwirkende an Blobdaten sind für Data Lake erforderlich.

## <a name="create-lake-database-from-database-template"></a>Erstellen einer Lake-Datenbank aus Datenbankvorlagen
1. In Ihrem Azure Synapse Analytics Arbeitsbereich **Startseite**-Hub wählen Sie auf der linken Seite die Registerkarte **Daten** aus. Die Registerkarte **Daten** wird geöffnet, und die Liste der Datenbanken wird angezeigt, die bereits in Ihrem Arbeitsbereich vorhanden sind.
2. Zeigen Sie auf die **+** Schaltfläche, und wählen wählen Sie dann **Lake-Datenbank (Vorschau)** aus.
![Screenshot: Erstellen einer leeren Lake-Datenbank](./media/create-empty-lake-database/create-empty-lakedb.png)
3. Die Registerkarte des Datenbank-Designers wird mit einer leeren Datenbank geöffnet.
4. Der Datenbank-Designer verfügt auf der rechten Seite über **Eigenschaften**, die konfiguriert werden müssen.
    - **Name:** Geben Sie Ihrer Datenbank einen Namen. Namen können nach der Veröffentlichung der Datenbank nicht mehr bearbeitet werden. Stellen Sie daher sicher, dass der name, den Sie auswählen, richtig ist.
    - **Beschreibung:** Die Angabe einer Beschreibung für Ihre Datenbank ist optional, ermöglicht Benutzern jedoch, den Zweck der Datenbank zu verstehen.
    - **Speicher für die Datenbank:** Dieser Abschnitt enthält die Standardspeicherinformationen für Tabellen in der Datenbank. Dieser Standardwert wird auf jede Tabelle in der Datenbank angewendet, es sei denn, sie wird für die Tabelle selbst überschrieben.
    - **Verknüpfter Dienst:** Dies ist der standardmäßige verknüpfte Dienst, der zum Speichern Ihrer Daten in Azure Data Lake Storage.  Der standardmäßige verknüpfte Dienst, der dem Synapse-Arbeitsbereich zugeordnet ist, wird angezeigt. Sie können den **verknüpften Dienst** jedoch in ein beliebiges ADLS-Speicherkonto ändern. 
    - **Eingabeordner:** Dient zum Festlegen des Standardkontainers und Ordnerpfads innerhalb dieses verknüpften Diensts mithilfe des Dateibrowsers.
    - **Datenformat**: Lake-Datenbanken in Azure Synapse Parquet- und Text mit Trennzeichen als Speicherformate für Daten.

> [!NOTE]
> Sie können die Standardspeichereinstellungen auf Tabellenbasis jederzeit überschreiben, und die Standardeinstellung bleibt anpassbar. Wenn Sie nicht sicher sind, was Sie auswählen möchten, können Sie dies später noch einmal überprüfen.
 
5. Um der Datenbank eine Tabelle hinzuzufügen, wählen Sie die Schaltfläche **+ Tabelle** aus. 
    - **Benutzerdefiniert** fügt dem Canvas-Panel eine neue Tabelle hinzu.
    - **Über die Vorlage** wird der Katalog geöffnet, und Sie können eine Datenbankvorlage auswählen, die beim Hinzufügen einer neuen Tabelle verwendet werden soll. Weitere Informationen finden Sie unter [Erstellen einer Lake-Datenbank aus einer Datenbankvorlage](./create-lake-database-from-lake-database-templates.md).
    - **Mithilfe von Data Lake** können Sie ein Tabellenschema mit daten importieren, die sich bereits in Ihrem Lake befindet.
6. Wählen Sie **Benutzerdefiniert** aus. Im Zeichenbereich wird eine neue Tabelle mit dem Namen Table_1.
7. Anschließend können Sie die Table_1, einschließlich Tabellenname, Beschreibung, Speichereinstellungen, Spalten und Beziehungen, anpassen. Weitere Informationen finden Sie unter [Ändern einer Lake-Datenbank.](./modify-lake-database.md)
8. Fügen Sie eine neue Tabelle aus dem Data Lake hinzu, indem Sie **+ Tabelle** und dann **Aus Data Lake** auswählen.
9. Der Bereich **Externe Tabelle aus Data Lake erstellen** wird angezeigt. Füllen Sie den Bereich mit den folgenden Details aus, und wählen Sie **Weiter** aus.
    - **Geben Sie der externen Tabelle den Namen**, den Sie der Tabelle geben möchten, die Sie erstellen.
    - **Verknüpfter Dienst**: Der verknüpfte Dienst, der den Azure Data Lake-Storage, an dem sich Ihre Datendatei befindet.
    - **Eingabedatei oder -ordner** verwenden den Dateibrowser, um zu einer Datei in Ihrem Lake zu navigieren und diese auszuwählen, mit der Sie eine Tabelle erstellen möchten.
![Screenshot: Optionen im Bereich "Externe Tabelle aus Data Lake erstellen"](./media/create-empty-lake-database/create-from-lake.png)
    - Auf dem nächsten Bildschirm wird Azure Synapse Datei in der Vorschau angezeigt und das Schema erkannt.
    - Auf der Seite **Neue externe Tabelle** können Sie alle Einstellungen im Zusammenhang mit dem Datenformat und **Vorschaudaten** aktualisieren, um zu überprüfen, ob Synapse die Datei richtig identifiziert hat.
    - Wenn Sie mit den Einstellungen zufrieden sind, wählen Sie **Erstellen** aus.
    - Eine neue Tabelle mit dem von Ihnen ausgewählten Namen wird der Canvas hinzugefügt, und im Abschnitt **Speichereinstellungen für Tabelle** wird die Datei angezeigt, die Sie angegeben haben.
    
10. Nachdem die Datenbank angepasst wurde, ist es an der Zeit, sie zu veröffentlichen. Wenn Sie die Git-Integration mit Ihrem Synapse-Arbeitsbereich verwenden, müssen Sie Ihre Änderungen commiten und im Kollaborationszweig zusammenführen. [Erfahren Sie mehr über die Quellcodeverwaltung in Azure Synapse](././cicd/../../cicd/source-control.md). Wenn Sie den Synapse Live-Modus verwenden, können Sie "Veröffentlichen" auswählen.
    - Ihre Datenbank wird vor der Veröffentlichung auf Fehler überprüft. Alle gefundenen Fehler werden auf der Registerkarte "Benachrichtigungen" mit Anweisungen zum Beheben des Fehlers angezeigt.
    
       ![Screenshot des Überprüfungsbereichs mit Validierungsfehlern in der Datenbank](./media/create-empty-lake-database/validation-error.png)
    - Die Veröffentlichung erstellt Ihr Datenbankschema im Azure Synapse Metastore. Nach der Veröffentlichung sind die Datenbank- und Tabellenobjekte für andere Azure-Dienste sichtbar und ermöglichen den Fluss der Metadaten aus Ihrer Datenbank in Apps wie Power BI Purview.

11. Sie haben nun eine leere Lake-Datenbank in Azure Synapse erstellt und ihr mithilfe der Optionen **Benutzerdefiniert** und **Aus Data Lake Tabellen** hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie fort, die Möglichkeiten des Datenbankdesigners zu erkunden, indem Sie die nachstehenden Links verwenden. 
- [Ändern einer Lake-Datenbank](./modify-lake-database.md)
- [Weitere Informationen zu Lake-Datenbanken](./concepts-lake-database.md)
- [Erstellen einer Lake-Datenbank aus einer Lake-Datenbankvorlage](./create-lake-database-from-lake-database-templates.md)
