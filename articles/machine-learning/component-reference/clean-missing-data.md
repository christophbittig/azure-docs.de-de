---
title: 'Fehlende Daten bereinigen: Komponenten-Referenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente Clean Missing Data in Azure Machine Learning verwenden, um fehlende Werte zu entfernen, zu ersetzen oder abzuleiten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 7e75506d68f35e7b3a29e36af97d01e1174723c3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566580"
---
# <a name="clean-missing-data-component"></a>Komponente "Fehlende Daten bereinigen

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Verwenden Sie diese Komponente, um fehlende Werte zu entfernen, zu ersetzen oder abzuleiten. 

Datenanalysten überprüfen Daten oft auf fehlende Werte und führen dann verschiedene Vorgänge zum Korrigieren der Daten oder Einfügen neuer Werte aus. Durch solche Bereinigungsvorgänge sollen Probleme aufgrund von fehlenden Daten verhindert werden, die beim Trainieren eines Modells auftreten können. 

Diese Komponente unterstützt mehrere Arten von Operationen zur "Bereinigung" fehlender Werte, darunter:

+ Ersetzen von fehlenden Werten durch einen Platzhalter, einen Mittelwert oder einen anderen Wert
+ Vollständiges Entfernen von Zeilen und Spalten, in denen Werte fehlen
+ Ableiten von Werten basierend auf statistischen Methoden


Durch die Verwendung dieser Komponente wird Ihr Quelldatensatz nicht verändert. Stattdessen erstellt es ein neues Dataset in Ihrem Arbeitsbereich, das Sie im nachfolgenden Workflow verwenden können. Sie können das neue, bereinigte Dataset auch zur Wiederverwendung speichern.

Diese Komponente gibt auch eine Definition der Transformation aus, die zur Bereinigung der fehlenden Werte verwendet wird. Sie können diese Transformation auf andere Datensätze mit demselben Schema anwenden, indem Sie die Komponente [Transformation anwenden](./apply-transformation.md) verwenden.  

## <a name="how-to-use-clean-missing-data"></a>Verwenden des Moduls „Clean Missing Data“

Mit dieser Komponente können Sie einen Reinigungsvorgang definieren. Sie können den Bereinigungsvorgang auch speichern, damit Sie ihn zu einem späteren Zeitpunkt auf neue Daten anwenden können. In den folgenden Abschnitten erfahren Sie, wie Sie einen Bereinigungsprozess erstellen und speichern können: 
 
+ [So ersetzen Sie fehlende Werte](#replace-missing-values)
  
+ [So wenden Sie eine Bereinigungstransformation auf neue Daten an](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> Die Bereinigungsmethode, die Sie zur Behandlung fehlender Werte verwenden, kann sich auf Ihre Ergebnisse erheblich auswirken. Wir empfehlen, dass Sie mit verschiedenen Methoden experimentieren. Berücksichtigen Sie sowohl die Begründung zur Verwendung einer bestimmten Methode als auch die Qualität der Ergebnisse.

### <a name="replace-missing-values"></a>Ersetzen fehlender Werte  

Jedes Mal, wenn Sie die Komponente [Fehlende Daten bereinigen](./clean-missing-data.md) auf einen Datensatz anwenden, wird derselbe Bereinigungsvorgang auf alle von Ihnen ausgewählten Spalten angewendet. Wenn Sie also verschiedene Spalten mit verschiedenen Methoden bereinigen müssen, verwenden Sie separate Instanzen der Komponente.

1.  Fügen Sie die Komponente [Bereinigen Sie fehlende Daten](./clean-missing-data.md) zu Ihrer Pipeline hinzu, und verbinden Sie den Datensatz mit fehlenden Werten.  
  
2.  Wählen Sie bei **Columns to be cleaned** (Zu bereinigende Spalten) die Spalten mit den fehlenden Werten aus, die Sie ändern möchten. Sie können mehrere Spalten auswählen, müssen aber dieselbe Ersetzungsmethode in allen ausgewählten Spalten verwenden. Daher müssen Sie Zeichenfolgenspalten und numerische Spalten in der Regel getrennt bereinigen.

    Um beispielsweise auf fehlende Werte in allen numerischen Spalten zu überprüfen, führen Sie die folgenden Schritte aus:

    1. Wählen Sie die Komponente **Fehlende Daten bereinigen**, und klicken Sie auf **Spalte bearbeiten** im rechten Feld der Komponente.

    3. Wählen Sie für **Include** (Einschließen) den **Column Type** (Spaltentyp) aus der Dropdownliste und anschließend **Numeric** (Numerisch) aus. 
  
    Jede von Ihnen ausgewählte Bereinigungs- oder Ersetzungsmethode muss auf **alle** Spalten in der Auswahl anwendbar sein. Wenn die Daten in einer Spalte mit dem angegebenen Vorgang nicht kompatibel sind, gibt die Komponente einen Fehler zurück und hält die Pipeline an.
  
3.  Geben Sie für **Minimum missing value ratio** (Mindestverhältnis für fehlende Werte) die Mindestanzahl von fehlenden Werten an, die für den auszuführenden Vorgang erforderlich sind.  
  
    Sie verwenden diese Option in Kombination mit **Maximum missing value ratio** (Höchstverhältnis für fehlende Werte) zum Definieren der Bedingungen, unter denen ein Bereinigungsvorgang am Dataset ausgeführt wird. Wenn es zu viele oder zu wenige Zeilen gibt, in denen Werte fehlen, kann der Vorgang nicht ausgeführt werden. 
  
    Die eingegebene Anzahl stellt das **Verhältnis** von fehlenden Werten zu allen Werten in der Spalte dar. Für die Eigenschaft **Minimum missing value ratio** ist standardmäßig „0“ festgelegt. Dies bedeutet: Fehlende Werte werden selbst dann bereinigt, wenn nur ein einziger Wert fehlt. 

    > [!WARNING]
    > Diese Bedingung muss von jeder einzelnen Spalte erfüllt werden, damit der angegebene Vorgang angewendet werden kann. Angenommen beispielsweise, Sie haben drei Spalten ausgewählt und dann als Mindestverhältnis für fehlende Werte „0,2“ (20%) festgelegt, doch nur in einer einzigen Spalte fehlen tatsächlich 20% der Werte. In diesem Fall würde der Bereinigungsvorgang nur auf die Spalte angewendet, in der mehr als 20% der Werte fehlen. Die anderen Spalten würden unverändert beibehalten.
    > 
    > Wenn Sie unsicher sind, ob fehlende Werte geändert wurden, wählen Sie die Option **Generate missing value indicator column** aus. Dann wird eine Spalte an das Dataset angefügt, um anzugeben, ob jede Spalte die festgelegten Kriterien für den minimalen und maximalen Bereich erfüllt hat oder nicht.  
  
4. Geben Sie für **Maximum missing value ratio** die maximale Anzahl von Werten an, die fehlen können, damit der Vorgang ausgeführt wird.   
  
    So möchten Sie eine Ersetzung fehlender Werte beispielsweise nur dann durchführen, wenn in 30% oder weniger der Zeilen Werte fehlen, möchten die Werte aber unverändert beibehalten, wenn in mehr als 30% der Zeilen Werte fehlen.  
  
    Sie definieren die Anzahl als Verhältnis der fehlenden Werte zu allen Werten in der Spalte. Standardmäßig ist für **Maximum missing value ratio** „1“ festgelegt. Dies bedeutet: Fehlende Werte werden selbst dann bereinigt, wenn 100% der Werte in der Spalte fehlen.  
  
   
  
5. Wählen Sie für **Cleaning Mode** (Bereinigungsmodus) eine der folgenden Optionen zum Ersetzen oder Entfernen fehlender Werte aus:  
  
  
    + **Custom substitution value** (Benutzerdefinierter Ersatzwert): Verwenden Sie diese Option zur Angabe eines Platzhalterwerts (z. B. „0“ oder „N/V“), der für alle fehlenden Werte gilt. Der Wert, den Sie als Ersatz angeben, muss mit dem Datentyp der Spalte kompatibel sein.
  
    + **Replace with mean** (Durch Mittelwert ersetzen): Berechnet den Mittelwert der Spalte und verwendet ihn als Ersatzwert für jeden fehlenden Wert in der Spalte.  
  
        Gilt nur für Spalten mit den Datentypen „Integer“, „Double“ oder „Boolean“.  
  
    + **Replace with median** (Durch Median ersetzen): Berechnet den Median der Spalte und verwendet ihn als Ersatz für jeden fehlenden Wert in der Spalte.  
  
        Gilt nur für Spalten mit den Datentypen „Integer“ oder „Double“. 
  
    + **Replace with mode** (Durch Modus ersetzen) Berechnet den Modus für die Spalte und verwendet ihn als Ersatzwert für jeden fehlenden Wert in der Spalte.  
  
        Gilt für Spalten mit den Datentypen „Integer“, „Double“, „Boolean“ oder „Categorical“. 
  
    + **Remove entire row** (Gesamte Zeile entfernen): Entfernt jede Zeile im Dataset vollständig, in der mindestens ein Wert fehlt. Dies ist hilfreich, wenn der fehlende Wert als zufällig fehlend betrachtet werden kann.  
  
    + **Remove entire column** (Gesamte Spalte entfernen): Entfernt jede Spalte im Dataset vollständig, in der mindestens ein Wert fehlt.  
  
    
  
6. Die Option **Replacement value** (Ersatzwert) steht zur Verfügung, wenn Sie die Option **Custom substitution value** (Benutzerdefinierter Ersatzwert) ausgewählt haben. Geben Sie den neuen Wert ein, der als Ersatzwert für alle fehlenden Werte in der Spalte verwendet werden soll.  
  
    Beachten Sie, dass Sie diese Option nur in Spalten mit den Datentypen „Integer“, „Double“, „Boolean“ oder „String“ verwenden können.
  
7. **Generate missing value indicator column** (Indikatorspalte für fehlende Werte generieren): Wählen Sie diese Option aus, wenn Sie einen Hinweis darauf ausgeben möchten, ob die Werte in der Spalte die Kriterien für eine Bereinigung von fehlenden Werten erfüllt haben. Diese Option ist besonders hilfreich, wenn Sie beim Einrichten eines neuen Bereinigungsvorgangs sicherstellen möchten, dass er wie vorgesehen funktioniert.
  
8. Übermitteln Sie die Pipeline.

### <a name="results"></a>Ergebnisse

Die Komponente liefert zwei Ausgaben:  

-   **Cleaned dataset** (Bereinigtes Dataset): Ein Dataset, das aus den ausgewählten Spalten besteht, in denen fehlende Werte wie angegeben behandelt werden, und einer Indikatorspalte, wenn Sie diese Option ausgewählt haben.  

    Spalten, die nicht zur Bereinigung ausgewählt wurden, werden auch „per Pass-Through übergeben“.  
  
-  **Cleaning transformation** (Bereinigungstransformation): Eine für die Bereinigung verwendete Datentransformation, die in Ihrem Arbeitsbereich gespeichert und zu einem späteren Zeitpunkt auf neue Daten angewendet werden kann.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Anwenden eines gespeicherten Bereinigungsvorgang auf neue Daten  

Wenn Sie Bereinigungsvorgänge oft wiederholen müssen, empfehlen wir, dass Sie Ihr „Rezept“ für die Datenbereinigung als eine *Transformation* speichern, um es bei demselben Dataset wiederverwenden zu können. Das Speichern einer Bereinigungstransformation ist besonders hilfreich, wenn Sie Daten mit demselben Schema häufig erneut importieren und dann bereinigen müssen.  
      
1.  Fügen Sie die Komponente [Umwandlung anwenden](./apply-transformation.md) zu Ihrer Pipeline hinzu.  
  
2.  Fügen Sie dann das zu bereinigende Dataset hinzu, und verbinden Sie es mit dem rechten Eingangsport.  
  
3.  Erweitern Sie die Gruppe **Transforms** im linken Bereich des Designers. Suchen Sie nach der gespeicherten Transformation, und ziehen Sie sie in die Pipeline.  

4.  Verbinden Sie die gespeicherte Transformation mit dem linken Eingangsport von [Apply Transformation](./apply-transformation.md). 

    Wenn Sie eine gespeicherte Transformation anwenden, können Sie die Spalten, auf die die Transformation angewendet wird, nicht auswählen. Der Grund: Die Transformation wurde bereits definiert und gilt automatisch für die Spalten, die im ursprünglichen Vorgang angegeben wurden.

    Allerdings: Nehmen Sie einmal an, Sie hätten eine Transformation für eine Teilmenge numerischer Spalten erstellt. Diese Transformation können Sie auf ein Dataset von gemischten Spaltentypen anwenden, ohne dass ein Fehler ausgelöst wird, weil die fehlenden Werte nur in den übereinstimmenden numerischen Spalten geändert werden.

6.  Übermitteln Sie die Pipeline.  

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 