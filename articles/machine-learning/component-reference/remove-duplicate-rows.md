---
title: 'Doppelte Zeilen entfernen: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente „Remove Duplicate Rows“ (Doppelte Zeilen entfernen) in Azure Machine Learning verwenden, um potenzielle Duplikate aus einem Dataset zu entfernen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 2fa7d5f3cf7d80531c03ae4ab7ef019c90cf8c71
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566613"
---
# <a name="remove-duplicate-rows-component"></a>Komponente „Remove Duplicate Rows“ (Doppelte Zeilen entfernen)

In diesem Artikel wird eine Komponente im Azure Machine Learning-Designer beschrieben.

Mit dieser Komponente können Sie potenzielle Duplikate aus einem Dataset entfernen.

Angenommen beispielsweise, Ihre Daten sehen wie die folgenden aus und stellen mehrere Datensätze für Patienten dar. 

| Patienten-ID | Initialen| Geschlecht|Age|Aufgenommen|
|----|----|----|----|----|
|1|W.M.| M| 53| Jan|
|2| W.J.M.| M| 53| Jan|
|3| W.J.M.| M| 24| Jan|
|3| W.M.| M| 24| Feb|
|4| W.M.| M| 23| Feb|
| | W.M.| M| 23| |
|5| W.J.M.| M| 53| |
|6| W.J.M.| M| NaN| |
|7| W.J.M.| M| NaN| |

Wie zu sehen ist, enthält dieses Beispiel mehrere Spalten mit möglicherweise doppelten Daten. Ob es sich dabei tatsächlich um Duplikate handelt, hängt von Ihrer Kenntnis der Daten ab. 

+ So wissen Sie beispielsweise, dass viele Patienten denselben Namen haben. Deshalb würden Sie Duplikate nicht anhand einer der Namensspalten entfernen, sondern dafür ausschließlich die Spalte **ID** verwenden. Auf diese Weise werden nur die Zeilen mit doppelten ID-Werten ausgefiltert – unabhängig davon, ob die Patienten denselben Namen haben oder nicht.

+ Alternativ dazu könnten Sie auch Duplikate im Feld „ID“ zulassen und eine andere Kombination von Dateien für die Suche nach eindeutigen Datensätzen verwenden, z. B. „Vorname“, „Nachname“, „Alter“ und „Geschlecht“.  

Wenn Sie die Kriterien dafür festlegen möchten, ob eine Zeile doppelt ist oder nicht, geben Sie eine einzelne Spalte oder eine Gruppe von Spalten an, die als **Schlüssel** verwendet werden soll. Zwei Zeilen werden nur dann als Duplikate betrachtet, wenn die Werte in **allen** Schlüsselspalten gleich sind. Wenn in einer Zeile ein Wert für **Schlüssel** fehlt, handelt es sich nicht um doppelte Zeilen. Wenn in der vorstehenden Tabelle beispielsweise „Geschlecht“ und „Alter“ als „Schlüssel“ festgelegt wurden, sind die Zeilen 6 und 7 keine doppelten Zeilen, weil in der Spalte „Alter“ ein Wert fehlt.

Wenn Sie die Komponente ausführen, erstellt sie ein Dataset mit Kandidaten und gibt eine Gruppe von Zeilen zurück, die in der gesamten von Ihnen angegebenen Gruppe von Spalten keine Duplikate enthalten.

> [!IMPORTANT]
> Das Quelldataset wird nicht geändert. Diese Komponente erstellt ein neues Dataset, das gefiltert wird, um Duplikate auszuschließen. Grundlage dafür sind die von Ihnen angegebenen Kriterien.

## <a name="how-to-use-remove-duplicate-rows"></a>Verwenden von „Remove Duplicate Rows“

1. Fügen Sie der Pipeline die Komponente hinzu. Sie finden die Komponente **Remove Duplicate Rows** (Doppelte Zeilen entfernen) unter **Data Transformation** (Datentransformation), **Manipulation** (Bearbeitung).  

2. Verbinden Sie das Dataset, das Sie auf doppelte Zeilen überprüfen möchten.

3. Klicken Sie im Bereich **Eigenschaften** unter **Key column selection filter expression** (Filterausdruck aus Schlüsselspaltenauswahl) auf **Launch column selector** (Spaltenauswahl starten), um die Spalten auszuwählen, die bei der Identifizierung von Duplikaten verwendet werden sollen.

    In diesem Kontext steht **Schlüssel** nicht für einen eindeutigen Bezeichner. Alle Spalten, die Sie mit der Spaltenauswahl auswählen, werden als **Schlüsselspalten** bezeichnet. Alle nicht ausgewählten Spalten werden als Nichtschlüsselspalten betrachtet. Die Kombination aus Spalten, die Sie als Schlüssel auswählen, legt die Eindeutigkeit der Datensätze fest. (Stellen Sie sich diese Kombination als SQL-Anweisung vor, die mehrere Gleichheitsverknüpfungen verwendet.)

    Beispiele:

    + „Ich möchte sicherstellen, dass IDs eindeutig sind“: Wählen Sie nur die ID-Spalte aus.
    + „Ich möchte sicherstellen, dass die Kombination des Vornamens, des Nachnamens und der ID eindeutig ist“: Wählen Sie alle drei Spalten aus.

4. Geben Sie mithilfe des Kontrollkästchens **Retain first duplicate row** (Erste doppelte Zeile beibehalten) an, welche Zeile zurückgegeben werden soll, wenn Duplikate gefunden werden:

    + Wenn das Kontrollkästchen aktiviert ist, wird die erste Zeile zurückgegeben, und die anderen Zeilen werden verworfen. 
    + Wenn Sie das Kontrollkästchen deaktivieren, wird die letzte doppelte Zeile in den Ergebnissen beibehalten, und die anderen Zeilen werden verworfen. 

5. Übermitteln Sie die Pipeline.

6. Um die Ergebnisse anzuzeigen, klicken Sie mit der rechten Maustaste auf die Komponente, und wählen Sie **Visualisieren** aus. 

> [!TIP]
> Wenn die Ergebnisse schwer zu verstehen sind oder wenn Sie einige Spalten nicht berücksichtigen möchten, können Sie Spalten mithilfe der Komponente [Select Columns in Dataset](./select-columns-in-dataset.md) (Spalten im Dataset auswählen) entfernen.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an. 