---
title: '„Convert to Dataset“: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie die „Convert to Dataset“-Komponente (In ein Dataset konvertieren) im Azure Machine Learning-Designer verwenden, um Dateneingaben in das interne Datasetformat zu konvertieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: e86c7c6f6a5eb3c4b3a388dab4680d2856a80b43
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566472"
---
# <a name="convert-to-dataset"></a>Konvertieren in ein Dataset

In diesem Artikel wird beschrieben, wie Sie die „Convert to Dataset“-Komponente im Azure Machine Learning-Designer verwenden, um Daten für eine Pipeline in das interne Format des Designers zu konvertieren.
  
In den meisten Fällen ist keine Konvertierung erforderlich. Azure Machine Learning konvertiert Daten implizit in das native Datasetformat, wenn ein Vorgang für die Daten ausgeführt wird. 

Es empfiehlt sich, Daten im Datasetformat zu speichern, wenn Sie Daten normalisiert oder bereinigt haben und sicherstellen möchten, dass die Änderungen in anderen Pipelines verwendet werden.  
  
> [!NOTE]
> „Convert to Dataset“ (In Dataset konvertieren) ändert nur das Format der Daten. Es wird keine neue Kopie der Daten im Arbeitsbereich gespeichert. Um das Dataset zu speichern, doppelklicken Sie auf den Ausgabeport, wählen Sie **Als Dataset speichern** aus, und geben Sie einen neuen Namen ein.  
  
## <a name="how-to-use-convert-to-dataset"></a>Verwenden von Convert to Dataset  

Es wird empfohlen, dass Sie die [Metadaten bearbeiten](edit-metadata.md)-Komponente verwenden, um das Dataset vorzubereiten, bevor Sie „Convert to Dataset“ anwenden. Sie können Spaltennamen hinzufügen oder ändern, Datentypen anpassen und andere ggf. erforderliche Änderungen vornehmen.

1.  Fügen Sie Ihrer Pipeline die „Convert to Dataset“-Komponente hinzu. Sie können diese Komponente im Designer in der Kategorie **Datentransformation** finden. 

2. Verbinden Sie sie mit einer beliebigen Komponente, die ein Dataset ausgibt.   

    Solange die Daten [tabellarisch](/python/api/azureml-core/azureml.data.tabulardataset) sind, können Sie sie in ein Dataset konvertieren. Dies schließt Daten ein, die mit [Import Data](import-data.md) (Daten importieren) geladen oder mithilfe von [Enter Data Manually](enter-data-manually.md) (Daten manuell eingeben) erstellt wurden, sowie Datasets, die mithilfe von [Apply Transformation](apply-transformation.md) (Transformation anwenden) transformiert wurden.

3.  Geben Sie in der Dropdownliste **Aktion** an, ob vor dem Speichern des Datasets eine Datenbereinigung durchgeführt werden soll:  
  
    - **Keine:**  Daten unverändert verwenden.  
  
    - **SetMissingValue:** Legen Sie einen bestimmten Wert auf einen fehlenden Wert im Dataset fest. Der Standardplatzhalter ist das Fragezeichen (?), Sie können jedoch die Option **Custom missing value** (Benutzerdefinierter fehlender Wert) verwenden, um einen anderen Wert einzugeben. Wenn Sie für **Custom missing value** (Benutzerdefinierter fehlender Wert) z. B. den Wert **Taxi** eingeben, werden alle Vorkommen von **Taxi** im Dataset in den fehlenden Wert geändert.
  
    - **ReplaceValues:** Verwenden Sie diese Option, um einen einzelnen exakten Wert anzugeben, der durch einen anderen exakten Wert ersetzt werden soll. Sie können fehlende oder benutzerdefinierte Werte ersetzen, indem Sie die Methode **Ersetzen** festlegen:

      - **Missing**: Wählen Sie diese Option aus, um fehlende Werte im Eingabedataset zu ersetzen. Geben Sie für **Neuer Wert** den Wert ein, durch den die fehlenden Werte ersetzt werden sollen.
      - **Benutzerdefiniert**: Wählen Sie diese Option aus, um benutzerdefinierte Werte im Eingabedataset zu ersetzen. Geben Sie für **Benutzerdefinierter Wert** den Wert ein, den Sie suchen möchten. Wenn die Daten z. B. die Zeichenfolge `obs` als Platzhalter für fehlende Werte enthalten, geben Sie `obs` ein. Geben Sie für **Neuer Wert** den neuen Wert ein, durch den die ursprüngliche Zeichenfolge ersetzt werden soll.
  
    Beachten Sie, dass der Vorgang **ReplaceValues** nur für exakte Übereinstimmungen gilt. Diese Zeichenfolgen sind z. B. nicht betroffen: `obs.`, `obsolete`.  
 
  
5.  Übermitteln Sie die Pipeline.  

## <a name="results"></a>Ergebnisse

+  Um das resultierende Dataset unter einem neuen Namen abzuspeichern, wählen Sie das Symbol **Register dataset** (Dataset registrieren) auf der Registerkarte **Ausgaben** im rechten Bereich der Komponente aus.  
  
## <a name="technical-notes"></a>Technische Hinweise  

-   Jede Komponente, die ein Dataset als Eingabe verwendet, akzeptiert auch Daten im CSV- oder TSV-Format. Bevor Komponentencode ausgeführt wird, werden die Eingaben vorverarbeitet. Die Vorverarbeitung entspricht dem Ausführen der „Convert to Dataset“-Komponente für die Eingabe.  
  
-   Das SVMLight-Format kann nicht in ein Dataset konvertiert werden.  
  
-   Wenn Sie einen benutzerdefinierten Ersetzungsvorgang angeben, wird der Such- und Ersetzungsvorgang auf vollständige Werte angewendet. Teilübereinstimmungen sind nicht zulässig. Sie können z. B. eine 3 durch „-1“ oder durch „33“ ersetzen, es ist jedoch nicht möglich, eine 3 in einer zweistelligen Zahl wie 35 zu ersetzen.  
  
-   Bei benutzerdefinierten Ersetzungsvorgängen tritt bei der Ersetzung automatisch ein Fehler auf, wenn Sie ein Ersetzungszeichen verwenden, das nicht dem aktuellen Datentyp der Spalte entspricht.  

  
## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md).