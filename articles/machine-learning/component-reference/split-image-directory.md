---
title: Teilen des Bildverzeichnisses
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente „Split Image Directory“ (Bildverzeichnis teilen) im Azure Machine Learning-Designer verwenden, um die Bilder eines Bildverzeichnisses in zwei unterschiedliche Gruppen aufzuteilen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 5adf44b76632af7e1d37189e9b81cc323b2e1216
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566670"
---
# <a name="split-image-directory"></a>Teilen des Bildverzeichnisses

In diesem Artikel wird beschrieben, wie Sie die Komponente „Split Image Directory“ (Bildverzeichnis teilen) im Azure Machine Learning-Designer verwenden, um die Bilder eines Bildverzeichnisses in zwei unterschiedliche Gruppen aufzuteilen.

Diese Komponente ist besonders hilfreich, wenn Sie Bilddaten in Trainings- und Testsätze aufteilen müssen. 

## <a name="how-to-configure-split-image-directory"></a>Konfigurieren des Moduls „Split Image Directory“ (Teilen des Bildverzeichnisses)

1. Fügen Sie der Pipeline die Komponente **Split Image Directory** (Bildverzeichnis teilen) hinzu. Sie finden diese Komponente in der Kategorie „ Maschinelles Sehen/Transformation von Bilddaten“.

2. Stellen Sie eine Verbindung mit der Komponente her, dessen Ausgabe das Bildverzeichnis ist.

3. Geben Sie **Fraction of images in the first output** (Anteil der Bilder in der ersten Ausgabe) ein, um den Prozentsatz der Daten anzugeben, die in den linken Teil eingefügt werden sollen (standardmäßig 0,9). Wenn das Bruchergebnis nicht ganzzahlig ist, verwendet die Komponente die kleinere nächste Ganzzahl.


## <a name="technical-notes"></a>Technische Hinweise

### <a name="expected-inputs"></a>Erwartete Eingaben

| Name                  | type           | BESCHREIBUNG              |
| --------------------- | -------------- | ------------------------ |
| Input image directory (Eingabebildverzeichnis) | ImageDirectory | Dies ist das Bildverzeichnis, das geteilt werden soll. |

### <a name="component-parameters"></a>Komponentenparameter

| Name                                   | type  | Range | Optional | BESCHREIBUNG                            | Standard |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fraction of images in the first output (Anteil der Bilder in der ersten Ausgabe) | Float | 0-1   | Erforderlich | Dies ist der Anteil der Bilder in der ersten Ausgabe. | 0.9     |

### <a name="outputs"></a>Ausgaben

| Name                    | type           | BESCHREIBUNG                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Output image directory1 (Ausgabe Bildverzeichnis1) | ImageDirectory | Dies ist das Verzeichnis, das die ausgewählten Eingabebilder enthält. |
| Output image directory2 (Ausgabe Bildverzeichnis2) | ImageDirectory | Dies ist das Bildverzeichnis, das alle anderen Bilder enthält. |

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 
