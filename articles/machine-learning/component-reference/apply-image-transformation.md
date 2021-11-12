---
title: Anwenden einer Bildtransformation
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente Bildtransformation anwenden verwenden, um eine Bildtransformation auf ein Bildverzeichnis anzuwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 45929ecdcc4327a12e2b2867a0cb0c95ca78a3fd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566686"
---
# <a name="apply-image-transformation"></a>Anwenden einer Bildtransformation 

Dieser Artikel beschreibt die Verwendung der Komponente Bildtransformation anwenden im Azure Machine Learning Designer, um ein Eingabebildverzeichnis basierend auf einer zuvor festgelegten Bildtransformation zu ändern.  

Sie müssen eine Komponente [Bildtransformation einrichten](init-image-transformation.md) verbinden, um die Transformation festzulegen, und dann können Sie diese Transformation auf das Eingabebildverzeichnis der Komponente Bildtransformation anwenden anwenden.

## <a name="how-to-use-apply-image-transformation"></a>Verwenden von „Anwenden einer Bildtransformation“  

1. Fügen Sie die Komponente **Bildtransformation anwenden** zu Ihrer Pipeline hinzu. Sie finden diese Komponente in der Kategorie *maschinelles Sehen/Bilddatenumwandlung*. 

2. Verbinden Sie die Ausgabe von **Initialisieren einer Bildtransformation** mit der Eingabe von **Anwenden einer Bildtransformation** auf der linken Seite.

     > [!NOTE]
     > Nur die von der Komponente [ Init Image Transformation ](init-image-transformation.md) erzeugte Bildtransformation wird für diese Komponente akzeptiert. Stellen Sie für andere Transformationsarten eine Verbindung mit [Anwenden der Transformation](apply-transformation.md) her, andernfalls wird der Fehler ‚InvalidTransformationDirectoryError‘ ausgelöst.


3. Stellen Sie eine Verbindung mit dem Bildverzeichnis her, das Sie transformieren möchten.

4. Geben Sie für **Modus** an, für welchen Zweck Sie die Eingabetransformation verwenden möchten: ‚Zum Training‘ oder ‚Zum Rückschließen‘. 

   Wenn Sie **Zum Training** angeben, werden alle Transformationen angewendet, die Sie in „Initialisieren einer Bildtransformation“ angeben.

   Wenn Sie **Zum Rückschließen** angeben, werden Transformationen wie das zufällige Erstellen neuer Stichproben vor dem Anwenden ausgeschlossen. Das hat den Grund, dass Transformationsvorgänge zum zufälligen Erstellen neuer Stichproben wie ‚Random horizontal flip‘ zur Vermehrung der Daten beim Training verwendet werden, was beim Rückschließen nicht zur Anwendung kommen sollte, da Rückschluss-Stichproben zum Erreichen einer genauen Vorhersage und Auswertung fixiert sein müssen.

   > [!NOTE]
   > Diese Transformationen werden im Modus **Zum Rückschließen** ausgeschlossen: Random resized crop, Random crop, Random horizontal flip, Random vertical flip, Random rotation, Random affine, Random grayscale, Random perspective, Random erasing.

5. Um eine Bildtransformation auf ein neues Bildverzeichnis anzuwenden, reichen Sie die Pipeline ein.  

### <a name="component-parameters"></a>Komponentenparameter

| Name | Range | type | Standard                   | BESCHREIBUNG                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | Any   | Mode | (Muss vom Benutzer angegeben werden) | Der Zweck, für den Sie die Eingabetransformation verwenden. Sie sollten zufällige (Random) Transformationsvorgänge beim Rückschließen ausschließen, sie aber fürs Training beibehalten |

### <a name="expected-inputs"></a>Erwartete Eingaben  

| Name                       | type                    | BESCHREIBUNG                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Eingabe-Bildtransformation | TransformationDirectory | Eingabe-Bildtransformation        |
| Eingabe-Bildverzeichnis      | ImageDirectory          | Zu transformierendes Bildverzeichnis |

### <a name="outputs"></a>Ausgaben  

| Name                   | type           | BESCHREIBUNG            |
| ---------------------- | -------------- | ---------------------- |
| Ausgabe-Bildverzeichnis | ImageDirectory | Ausgabe-Bildverzeichnis |

## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 
