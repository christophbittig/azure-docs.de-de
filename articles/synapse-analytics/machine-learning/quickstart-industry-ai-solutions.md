---
title: Branchenspezifische KI-Lösungen
description: Branchenspezifische KI-Lösungen in Azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: garye
ms.date: 11/02/2021
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0a4476b1324938acd2c753368f26bc7aafbed62b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100346"
---
# <a name="industry-ai-solutions"></a>Branchenspezifische KI-Lösungen

Jede Branche hat ihre eigenen spezifischen Probleme, die es zu lösen gilt. In diesem Artikel werden die Branchenlösungen behandelt, die in Azure Synapse Workspaces verfügbar sind, um schnell mit der Lösung gängiger Branchenprobleme zu beginnen. Derzeit ist die angebotene KI-Lösung eine Einzelhandelslösung für Produktempfehlungen. Nachstehend finden Sie weitere Einzelheiten darüber, wie Sie diese Lösung als Einzelhandelsunternehmen für den Einstieg nutzen können.

## <a name="target-user"></a>Zielgruppe

Die Empfehlungslösung richtet sich an bereits aktive und angehende Data Scientists, die mit Code und Konzepten für maschinelles Lernen vertraut sind. Diese Lösung soll die Produktivität dieser Benutzer beschleunigen, um ein bestimmtes Problem im Einzelhandelsbereich zu lösen.

## <a name="retail-product-recommendation-solution"></a>Lösungen für Produktempfehlungen im Einzelhandel

Die Lösung **Retail – Product recommendations** (Einzelhandel – Produktempfehlungen) bietet eine stabile und skalierbare Empfehlungs-Engine für eine standardmäßige Entwicklung in Synapse. Mit dieser Lösung erhalten Sie ein Notebook, das ein Machine Learning-Modell für Produktempfehlungen trainiert.

Möglicherweise müssen Sie das vorkonfigurierte Notebook anpassen, um sicherzustellen, dass es Ihre individuellen Geschäftsanforderungen erfüllt.

Diese Empfehlungslösung für den Einzelhandel kann in zwei Modi bereitgestellt werden. Sie können es mit Beispieldaten ausprobieren oder eine Datenbank verwenden, die in Synapse mit der Datenbankvorlage für Einzelhandel modelliert wurde.

Die Lösung **Retail – Product recommendations** bietet eine Empfehlungspipeline für inhaltsbasiertes Filtern von Empfehlungen. Die inhaltsbasierte Filterpipeline verwendet den LightGBM-Algorithmus, um ein Modell für die Vorhersage der Benutzervorlieben basierend auf Benutzer- und Artikelmerkmalen zu trainieren. Bei den Merkmalen kann es sich sowohl um statische Merkmale wie Benutzer- und Artikelprofile als auch um dynamische Merkmale wie die aggregierten Verhaltensmuster der Benutzer handeln. Das inhaltsbasierte filternde Empfehlungssystem wird häufig für Empfehlungen wie „personalisierte Empfehlungen“ oder „neue Produkte, die Ihnen gefallen könnten“ verwendet.

## <a name="get-started"></a>Erste Schritte

1. Öffnen Sie Ihren Synapse-Arbeitsbereich.
1. Wählen Sie auf dem Startbildschirm im Abschnitt **Knowledge Center** die Option **Mehr entdecken** aus.
1. Wählen Sie im Knowledge Center die Option **Katalog durchsuchen** aus.
1. Wählen Sie im Katalog die Registerkarte **Datenbankvorlagen** aus. Scrollen Sie nach unten zum Abschnitt **KI-Lösungen**, und wählen Sie die Lösung „Retail – Product recommendations“ aus. Klicken Sie auf **Weiter**.
1. Wählen Sie eine der beiden Optionen aus:
   * „Beispieldaten verwenden“. 
   * „Eigene Daten aus einer Datenbank im Arbeitsbereich verwenden“. Bei dieser Option kann es sich beispielsweise um eine Datenbank handeln, die mit der Vorlage „Retail Database“ (Datenbank für Einzelhandel) modelliert wurde.
   
    Wenn Sie auf **Bereitstellen** klicken, wird in Ihrem Synapse-Arbeitsbereich ein Notebook geöffnet.

1. Ein Notebook wird nun in Ihrem Arbeitsbereich geöffnet. Sie können dieses Notebook an einen Spark-Pool anfügen und mit seiner Erkundung beginnen. Beachten Sie, dass dieses Notebook an Ihre spezifischen Anforderungen angepasst werden sollte.

> [!NOTE]
> Wenn Sie Ihre eigene Datenbank wählen, müssen Sie das Notebook so anpassen, dass Ihre eigenen Tabellen- und Spaltennamen verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Azure Synapse Analytics](../get-started.md)
* [Erstellen eines Arbeitsbereichs](../get-started-create-workspace.md)
