---
title: Verwenden von Barrierefreiheitsfunktionen im Designer
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die im Designer verfügbaren Tastenkombinationen und Barrierefreiheitsfunktionen für die Sprachausgabe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: lagayhar
author: lgayhardt
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: dfa3b5630b09e2a3e3411afc7d77b76f1b651e72
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554999"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Verwenden einer Tastatur zur Verwendung von Azure Machine Learning-Designer

Erfahren Sie, wie Sie den Azure Machine Learning-Designer mithilfe einer Tastatur und der Sprachausgabe verwenden können. Eine Liste der Tastenkombinationen, die überall im Azure-Portal funktionieren, finden Sie unter [Tastenkombinationen im Azure-Portal](../azure-portal/azure-portal-keyboard-shortcuts.md)

Dieser Workflow wurde mit [Sprachausgabe](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) und [JAWS](https://www.freedomscientific.com/products/software/jaws/) getestet, sollte aber auch mit anderen standardmäßigen Sprachausgaben funktionieren.

## <a name="navigate-the-pipeline-graph"></a>Navigieren im Pipelinegraph

Der Pipelinegraph ist als geschachtelte Liste organisiert. Die äußere Liste ist eine Komponentenliste, die alle Komponenten im Pipelinegraphen beschreibt. Die innere Liste ist eine Verbindungsliste, die alle Verbindungen einer bestimmten Komponente beschreibt.  

1. Verwenden Sie in der Komponentenliste die Pfeiltaste, um die Komponenten zu wechseln.
1. Verwenden Sie die Registerkarte, um die Verbindungsliste für die Zielkomponente zu öffnen.
1. Verwenden Sie die Pfeiltaste, um zwischen den Verbindungsports für die Komponente zu wechseln.
1. Verwenden Sie „G“, um zur Zielkomponente zu wechseln.

## <a name="edit-the-pipeline-graph"></a>Bearbeiten des Pipelinegraphs

### <a name="add-a-component-to-the-graph"></a>Hinzufügen einer Komponente zum Graphen

1. Verwenden Sie STRG+F6, um den Fokus von der Canvas zur Komponentenstruktur zu verschieben.
1. Suchen Sie die gewünschte Komponente in der Komponentenstruktur mithilfe des standardmäßigen TreeView-Steuerelements.

### <a name="edit-a-component"></a>Bearbeiten einer Komponente

So verbinden Sie eine Komponente mit einer anderen Komponente:

1. Verwenden Sie STRG+UMSCHALT+H, wenn Sie auf eine Komponente in der Komponentenliste abzielen, um das Verbindungshilfsprogramm zu öffnen.
1. Bearbeiten Sie die Verbindungsports für die Komponente.

So passen Sie Komponenteneigenschaften an:

1. Verwenden Sie STRG+UMSCHALT+E, wenn Sie eine Komponente als Ziel verwenden, um die Komponenteneigenschaften zu öffnen.
1. Bearbeiten Sie die Komponenteneigenschaften.

## <a name="navigation-shortcuts"></a>Tastenkombinationen zur Navigation

| Tastatureingabe | Beschreibung |
|-|-|
| STRG + F6 | Fokus zwischen Canvas und Komponentenstruktur umschalten |
| STRG + F1   | Informationskarte beim Fokussieren auf einen Knoten in der Komponentenstruktur öffnen |
| STRG + UMSCHALT + H | Verbindungshilfsprogramm öffnen, wenn der Fokus auf einem Knoten liegt |
| STRG + UMSCHALT + E | Komponenteneigenschaften öffnen, wenn der Fokus auf einem Knoten liegt |
| STRG + G | Fokus auf ersten fehlerhaften Knoten verschieben, wenn bei der Pipelineausführung ein Fehler aufgetreten ist |

## <a name="action-shortcuts"></a>Tastenkombinationen für Aktionen

Verwenden Sie die folgenden Tastenkombinationen mit dem Zugriffsschlüssel. Weitere Informationen zu Zugriffsschlüsseln finden Sie unter https://en.wikipedia.org/wiki/Access_key.

| Tastatureingabe | Aktion |
|-|-|
| Zugriffsschlüssel + R | Ausführen |
| Zugriffsschlüssel + P | Veröffentlichen |
| Zugriffsschlüssel + C | Klon |
| Zugriffsschlüssel + D | Bereitstellen |
| Zugriffsschlüssel + I | Rückschlusspipeline erstellen/aktualisieren |
| Zugriffsschlüssel + B | Batchrückschlusspipeline erstellen/aktualisieren |
| Zugriffsschlüssel + K | Dropdownliste „Rückschlusspipeline erstellen“ öffnen |
| Zugriffsschlüssel + U | Dropdownliste „Rückschlusspipeline aktualisieren“ öffnen |
| Zugriffsschlüssel + M | Dropdownliste „Mehr(...)“ öffnen |

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren des hohen Kontrasts oder Ändern des Designs](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Tools für die Barrierefreiheit bei Microsoft](https://www.microsoft.com/accessibility)
