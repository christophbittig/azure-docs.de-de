---
title: 'Transformation anwenden: Komponenten-Referenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente "Transformation anwenden" in Azure Machine Learning verwenden, um einen Eingabedatensatz basierend auf einer zuvor berechneten Transformation zu ändern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: 3091c4d85594f5bcb4236f04f8d58e21dfe125de
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566658"
---
# <a name="apply-transformation-component"></a>Komponente Transformation anwenden

Dieser Artikel beschreibt eine Komponente im Azure Machine Learning Designer.

Verwenden Sie diese Komponente, um einen Eingabedatensatz auf der Grundlage einer zuvor berechneten Transformation zu ändern. Diese Komponente wird benötigt, wenn Sie Transformationen in Rückschließen pipelines aktualisieren müssen.

Wenn Sie beispielsweise z-Scores verwendet haben, um Ihre Trainingsdaten mit Hilfe der Komponente **Daten normalisieren** zu normalisieren, sollten Sie den z-Score-Wert, der für das Training berechnet wurde, auch in der Auswertungsphase verwenden. In Azure Machine Learning können Sie die Normalisierungsmethode als Transformation speichern und dann **Apply Transformation** (Anwenden einer Transformation) verwenden, um das Z-Ergebnis vor der Bewertung auf die Eingabedaten anzuwenden.

## <a name="how-to-save-transformations"></a>Speichern von Transformationen

Mit dem Designer können Sie Datentransformationen als **Datasets** speichern, damit Sie sie in anderen Pipelines verwenden können.

1. Wählen Sie eine Datenumwandlungskomponente aus, die erfolgreich ausgeführt wurde.

1. Wählen Sie die Registerkarte **Ausgaben + Protokolle** aus.

1. Suchen Sie die Transformationsausgabe, und wählen Sie **Datensatz registrieren**, um ihn als Komponente unter der Kategorie **Datensätze** in der Komponentenpalette zu speichern.

## <a name="how-to-use-apply-transformation"></a>Verwenden des Moduls „Apply Transformation“  
  
1. Fügen Sie die Komponente **Umwandlung anwenden** zu Ihrer Pipeline hinzu. Sie finden diese Komponente im Abschnitt **Modellbewertung und -auswertung** der Komponentenpalette. 
  
1. Suchen Sie die gespeicherte Transformation, die Sie verwenden möchten, unter **Datensätze** in der Komponentenpalette.

1. Verbinden Sie den Ausgang der gespeicherten Transformation mit dem linken Eingangsanschluss der Komponente **Transformation anwenden**.

    Der Datensatz muss genau das gleiche Schema (Anzahl der Spalten, Spaltennamen, Datentypen) wie der Datensatz haben, für den die Transformation zuvor vorgesehen war.  
  
1. Verbinden Sie den Datensatzausgang der gewünschten Komponente mit dem rechten Eingangsport der Komponente **Transformation anwenden**.
  
1. Um eine Transformation auf das neue Dataset anzuwenden, übermitteln Sie die Pipeline.  

> [!IMPORTANT]
> Um sicherzustellen, dass die aktualisierte Transformation in Trainingspipelines auch für Rückschlusspipelines möglich ist, befolgen Sie die nachstehenden Schritte jedes Mal, wenn in der Trainingspipeline eine Transformation aktualisiert wird:
> 1. Registrieren Sie in der Trainingspipeline die Ausgabe von [Select Columns Transform](select-columns-transform.md) als Dataset.
> ![Datensatz der Komponentenausgabe registrieren](media/module/select-columns-transform-register-dataset.png)
> 1. Entfernen Sie in der Rückschließen pipeline die **TD-** -Komponente und ersetzen Sie sie durch den registrierten Datensatz aus dem vorherigen Schritt.
> ![TD-Komponente ersetzen](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 