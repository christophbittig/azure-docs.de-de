---
title: Visuelles Erstellen
titleSuffix: Azure Data Factory & Azure Synapse
description: Informationen zum Verwenden der visuellen Erstellung in Azure Data Factory und Synapse Analytics
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 09/09/2021
ms.openlocfilehash: a9ee177015896e314d140166c2477e92fa9edf8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788062"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visuelles Erstellen in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mithilfe der Azure Data Factory- und Synapse Analytics-Benutzeroberfläche können Sie Ressourcen für Ihre Data Factory- oder Synapse-Pipelines visuell erstellen und bereitstellen, ohne Code schreiben zu müssen. Sie können Aktivitäten auf eine Pipelinecanvas ziehen, Testläufe ausführen, iterativ debuggen sowie Ihre Pipelineausführungen bereitstellen und überwachen.

Die Benutzeroberfläche wird zurzeit nur unter Microsoft Edge und Google Chrome unterstützt.

## <a name="authoring-canvas"></a>Erstellungsbereich

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Um den **Erstellungsbereich** zu öffnen, klicken Sie auf das Stiftsymbol. 

:::image type="content" source="media/author-visually/authoring-canvas.png" alt-text="Erstellungsbereich":::

Hier können Sie die Pipelines, Aktivitäten, Datasets und Datenflüsse erstellen, die Ihre Factory bilden. Auf ähnliche Weise können verknüpfte Dienste, Trigger und Integration Runtimes auf der [Registerkarte „Verwalten“](#management-hub) bearbeitet werden. Informationen zu den ersten Schritte beim Erstellen einer Pipeline unter Verwendung des Erstellungsbereichs finden Sie unter [Kopieren von Daten mithilfe der Copy-Aktivität](tutorial-copy-data-portal.md). 

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
Um den **Erstellungsbereich** zu öffnen, klicken Sie auf das Pipeline-/Integrationssymbol. 

:::image type="content" source="media/author-visually/authoring-canvas-synapse.png" alt-text="Erstellungsbereich":::

Hier erstellen Sie die Pipelines und Aktivitäten in Synapse. Auf ähnliche Weise können die von den Pipelines verwendeten Datenflüsse auf der Registerkarte „Entwickeln“ sowie verknüpfte Dienste, Trigger und Integration Runtimes, die ihnen zugeordnet sind, auf der [Registerkarte „Verwalten“](#management-hub) bearbeitet werden. Informationen zu den ersten Schritte beim Erstellen einer Pipeline unter Verwendung des Erstellungsbereichs finden Sie unter [Kopieren von Daten mithilfe der Copy-Aktivität](tutorial-copy-data-portal.md). 

---

Die standardmäßige visuelle Erstellungsumgebung arbeitet direkt mit dem Dienst zusammen. Die Integration in Azure Repos Git oder GitHub wird ebenfalls unterstützt, um die Versionskontrolle und Zusammenarbeit in Ihren Pipelines zu ermöglichen. Weitere Informationen zu den Unterschieden dieser Erstellungsumgebungen finden Sie unter [Quellcodeverwaltung](source-control.md).

### <a name="properties-pane"></a>Eigenschaftenbereich

Bei Ressourcen der obersten Ebene, z. B Pipelines, Datasets und Datenflüssen, können allgemeine Eigenschaften im Eigenschaftenbereich auf der rechten Seite der Canvas bearbeitet werden. Der Eigenschaftenbereich enthält Eigenschaften wie Name, Beschreibung, Anmerkungen und andere allgemeine Eigenschaften. Unterressourcen, z. B. Pipelineaktivitäten und Datenflusstransformationen, werden in dem Bereich unten in der Canvas bearbeitet. 

:::image type="content" source="media/author-visually/properties-pane.png" alt-text="Eigenschaftenbereich":::

Der Eigenschaftenbereich wird standardmäßig nur bei der Ressourcenerstellung geöffnet. Wenn Sie ihn bearbeiten möchten, klicken Sie oben rechts im Erstellungsbereich auf das Symbol für den Eigenschaftenbereich.

### <a name="related-resources"></a>Verwandte Ressourcen

Im Bereich „Eigenschaften“ können Sie sehen, welche Ressourcen von der ausgewählten Ressource abhängig sind, indem Sie die Registerkarte **Verknüpft** auswählen. Hier werden alle Ressourcen aufgeführt, die auf die aktuelle Ressource verweisen.

:::image type="content" source="media/author-visually/related-resources.png" alt-text="Verwandte Ressourcen":::

Beispielsweise verwenden in der obigen Abbildung eine Pipeline und zwei Datenflüsse das aktuell ausgewählte Dataset.

## <a name="management-hub"></a>Verwaltungshub

Der Verwaltungshub, den Sie in der Benutzeroberfläche über die Registerkarte *Verwalten* aufrufen können, ist ein Portal mit globalen Verwaltungsaktionen für den Dienst. Hier können Sie die Verbindungen mit Datenspeichern und externen Compute-Umgebungen, die Konfiguration der Quellcodeverwaltung und die Triggereinstellungen verwalten. Weitere Informationen zu den Funktionen finden Sie unter [Verwaltungshub](author-management-hub.md).

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="Verwalten von verknüpften Diensten":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="Verwalten von verknüpften Diensten":::

---

## <a name="expressions-and-functions"></a>Ausdrücke und Funktionen

Anstelle statischer Werte können Sie Ausdrücke und Funktionen verwenden, um viele Eigenschaften im Dienst anzugeben.

Zum Angeben eines Ausdrucks für einen Eigenschaftswert wählen Sie **Dynamischen Inhalt hinzufügen** aus oder klicken auf **ALT+P**, wenn sich der Eingabefokus auf dem Feld befindet.

:::image type="content" source="media/author-visually/dynamic-content-1.png" alt-text="Dynamischen Inhalt hinzufügen":::

Dadurch wird der **Ausdrucks-Generator** geöffnet, in dem Sie Ausdrücke anhand von unterstützten Systemvariablen, Aktivitätsausgaben, Funktionen und benutzerdefinierten Variablen oder Parametern erstellen können. 

:::image type="content" source="media/author-visually/dynamic-content-2.png" alt-text="Ausdrucks-Generator":::

Informationen zur Ausdruckssprache finden Sie unter [Ausdrücke und Funktionen](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Feedback geben

Wählen Sie **Feedback** aus, um Kommentare zu Funktionen abzugeben oder um Microsoft Probleme mit dem Tool zu melden:

:::image type="content" source="media/author-visually/provide-feedback.png" alt-text="Feedback":::

## <a name="next-steps"></a>Nächste Schritte

Unter [Programmgesteuertes Überwachen und Verwalten von Pipelines](monitor-programmatically.md) erfahren Sie mehr über das Überwachen und Verwalten von Pipelines.
