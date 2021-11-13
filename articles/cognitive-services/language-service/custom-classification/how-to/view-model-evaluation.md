---
title: 'Anzeigen einer benutzerdefinierten Klassifizierungsmodellauswertung: Azure Cognitive Services'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Auswertungsergebnisse für ein benutzerdefiniertes Klassifizierungsmodell anzeigen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: d0b708f3b06eaeb458df4e4c9da73e86b451e137
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469649"
---
# <a name="view-the-model-evaluation"></a>Anzeigen der Modellauswertung

Die Überprüfung der Modellauswertung ist ein wichtiger Schritt bei der Entwicklung eines benutzerdefinierten Klassifizierungsmodells. Sie hilft Ihnen zu erfahren, wie gut Ihr Modell funktioniert, und bietet Ihnen eine Vorstellung davon, wie es bei Einsatz in der Produktion funktioniert. 


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihr Modell trainieren, benötigen Sie Folgendes:
* [Ein benutzerdefiniertes Klassifizierungsprojekt](create-project.md) mit einem konfigurierten Azure Blob Storage-Konto 
* Textdaten, die in Ihr Speicherkonto [hochgeladen wurden](create-project.md#prepare-training-data).
* [Mit einem Tag versehene Daten](tag-data.md)
* Ein [erfolgreich trainiertes Modell](train-model.md)

Weitere Informationen finden Sie im [Lebenszyklus der Anwendungsentwicklung](../overview.md#application-development-lifecycle).

## <a name="model-evaluation"></a>Modellauswertung

Der Auswertungsprozess nutzt das trainierte Modell zum Vorhersagen benutzerdefinierter Klassen für Modelle im Testsatz und für deren Vergleich mit den bereitgestellten Datentags. Der Testsatz besteht aus Daten, die während des Trainingsprozesses nicht in das Modell eingeführt wurden. 

## <a name="view-the-model-details-using-language-studio"></a>Anzeigen der Modelldetails mithilfe von Language Studio

1. Wechseln Sie in Language Studio zu Ihrer [Projektseite.](https://aka.ms/languageStudio)
    1. Wechseln Sie in Language Studio zum Abschnitt **Text klassifizieren**.
    2. Wählen Sie **Benutzerdefinierte Textklassifizierung** aus. 

2. Wählen Sie im Menü auf der linken Seite **Modelldetails anzeigen** aus.

3. Sehen Sie den Trainingsstatus Ihres Modells in der Spalte **Status** und den F1-Score des Modells in der Spalte **F1-Score** ein.

    :::image type="content" source="../media/model-details-1.png" alt-text="Schaltfläche „Modelldetails anzeigen“" lightbox="../media/model-details-1.png":::

1. Klicken Sie auf den Modellnamen, um weitere Details anzuzeigen.

2. Die Metriken zur Auswertung auf **Modellebene** finden Sie unter dem Abschnitt **Übersicht** und die Metriken zur Auswertung auf **Klassenebene** unter dem Abschnitt **Klassenleistungsmetriken**. Weitere Informationen finden Sie unter [Auswertungsmetriken](../concepts/evaluation.md#model-level-and-class-level-evaluation-metrics).

    :::image type="content" source="../media/model-details-2.png" alt-text="Modellleistungsmetriken" lightbox="../media/model-details-2.png":::

> [!NOTE]
> Wenn hier nicht alle Klassen angezeigt werden, liegt das daran, dass es im Testsatz keine mit markierten Dateien dieser Klasse gab.

Unter der **Konfusionsmatrix für Testsatz** finden Sie die Konfusionsmatrix für das Modell.

> [!NOTE]
> Die Konfusionsmatrix wird derzeit nicht für Klassifizierungsprojekte mit mehreren Bezeichnungen unterstützt.

**Klassifizierung mit einzelnen Bezeichnungen**

:::image type="content" source="../media/conf-matrix-single.png" alt-text="Konfusionsmatrix für die Klassifizierung einzelner Klassen" lightbox="../media/conf-matrix-single.png":::

<!-- **Multiple Label Classification**

:::image type="content" source="../media/conf-matrix-multi.png" alt-text="Confusion matrix for multiple class classification" lightbox="../media/conf-matrix-multi.png"::: -->

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die Leistung Ihres Modells überprüfen, sollten Sie sich über die zugrunde liegenden [Auswertungsmetriken](../concepts/evaluation.md) informieren. Sobald Sie wissen, ob die Leistung Ihres Modells verbessert werden muss, können Sie mit der [Verbesserung des Modells](improve-model.md) beginnen.
