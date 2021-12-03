---
title: Azure Digital Twins-Explorer
titleSuffix: Azure Digital Twins
description: Erfahren Sie mehr über die Funktionen und den Zweck von Azure Digital Twins-Explorer und darüber, wann es ein nützliches Werkzeug zum Visualisieren digitaler Modelle, von Twins und Diagrammen sein kann.
author: baanders
ms.author: baanders
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bbcc693123f84d5cf789f53c8961f648ce2e90bf
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504244"
---
# <a name="azure-digital-twins-explorer-preview"></a>Azure Digital Twins-Explorer (Vorschau)

Dieser Abschnitt enthält weitere Informationen zum **Azure Digital Twins-Explorer**, einschließlich der Anwendungsfälle und einer Übersicht seiner Features. Ausführliche Schritte zur Verwendung der einzelnen Funktionen finden Sie unter [Verwenden des Azure Digital Twins-Explorers](how-to-use-azure-digital-twins-explorer.md).

Azure Digital Twins-Explorer ist ein Entwicklertool zum Visualisieren und Interagieren mit den Daten in Ihrer Azure Digital Twins-Instanz, einschließlich Ihrer [Modelle](concepts-models.md) und des [Zwillingsgraphen](concepts-twins-graph.md). 

>[!NOTE]
>Dieses Tool ist aktuell als **öffentliche Vorschauversion** verfügbar.

Hier sehen Sie eine Ansicht des Explorer-Fensters mit Modellen und Zwillingen, die für einen Beispielgraphen aufgefüllt wurden:

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit Beispielmodellen und -zwillingen." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

Die visuelle Schnittstelle ist ein großartiges Tool, um die Form Ihres Graphen und Modellsatzes zu untersuchen und zu verstehen. Sie können damit auch punktuelle Änderungen an einzelnen Zwillingen und Beziehungen vornehmen.

## <a name="when-to-use"></a>Verwendung

Azure Digital Twins-Explorer ist ein visuelles Tool für Benutzer, die ihren Zwillingsgraphen untersuchen sowie Zwillinge und Beziehungen im Kontext ihres Graphen ändern möchten.

Entwickler finden dieses Tool in den folgenden Szenarien möglicherweise besonders nützlich:
* **Erkunden**: Verwenden Sie den Explorer, um mehr über Azure Digital Twins und die Art und Weise zu erfahren, wie es Ihre reale Umgebung darstellt. Importieren Sie Beispielmodelle und Graphen, die Sie anzeigen und bearbeiten können, um sich mit dem Dienst vertraut zu machen. Anleitungen für die ersten Schritte mit Azure Digital Twins-Explorer finden Sie unter [Erste Schritte mit dem Azure Digital Twins-Explorer](quickstart-azure-digital-twins-explorer.md).
* **Entwicklung**: Verwenden Sie den Explorer, um Ihren Zwillingsgraphen anzuzeigen und zu überprüfen. Sie können ihn auch verwenden, um bestimmte Eigenschaften von Modellen, Zwillingen und Beziehungen zu untersuchen. Nehmen Sie sofortige Änderungen an Ihrem Graphen und seinen Daten vor. Ausführliche Anweisungen zur Verwendung der einzelnen Funktionen finden Sie unter [Verwenden des Azure Digital Twins-Explorers](how-to-use-azure-digital-twins-explorer.md). 

Der Hauptzweck des Explorers besteht darin, Sie beim Visualisieren und Verstehen Ihres Graphen zu unterstützen und Ihren Graphen nach Bedarf zu aktualisieren. Bei umfangreichen Lösungen und bei Arbeiten, die wiederholt oder automatisiert werden sollten, sollten Sie stattdessen die Verwendung von [APIs und SDKs](./concepts-apis-sdks.md) erwägen, um mit Ihrer Instanz über Code zu interagieren.

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

## <a name="features-and-capabilities"></a>Features und Funktionen

Azure Digital Twins-Explorer sind in Panels organisiert, die jeweils einen anderen Satz von Funktionen zum Untersuchen und Verwalten Ihrer Modelle, Zwillinge und Beziehungen bieten.

Die Abschnitte des Explorers lauten wie folgt:
* **Abfrage-Explorer**: Ausführen von Abfragen an das Zwillingsdiagramm und Anzeigen der visuellen Ergebnisse im Panel **Zwillingsgraph**.
* **Modelle**: Zeigen Sie eine Liste Ihrer Modelle an und führen Sie Modellaktionen durch, wie z. B. Hinzufügen, Entfernen und Anzeigen von Modelldetails.
* Im Bereich **Twins** wird eine Liste Ihrer Zwillinge und der dazugehörigen Beziehungen angezeigt.
* **Zwillingsgraph**: Visualisieren Ihrer Zwillinge und Beziehungen als anpassbare Zwillingsgraphen. Erstellen und Löschen von Zwillingen und Beziehungen sowie Anzeigen oder Bearbeiten ihrer Eigenschaften.
* **Modellgraph**: Visualisieren Ihrer Modelle und der Art und Weise, wie sie miteinander verbunden sind, in Form eines anpassbaren Modellgraphen.

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png" alt-text="Screenshot von Azure Digital Twins-Explorer mit einer Hervorhebung um jedes der oben beschriebenen Panels." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png":::

Ausführliche Anweisungen zur Verwendung der einzelnen Funktionen finden Sie unter [Verwenden des Azure Digital Twins-Explorers](how-to-use-azure-digital-twins-explorer.md). 

## <a name="how-to-contribute"></a>Beitragen

Azure Digital Twins-Explorer ist ein **Open-Source**-Tool, das Mitwirkung am Code und der Dokumentation begrüßt. Die gehostete Anwendung wird regelmäßig aus einem Quellcoderepository in GitHub bereitgestellt.

Um den Quellcode für das Tool anzuzeigen und ausführliche Anweisungen zur Mitwirkung am Code zu lesen, besuchen Sie das GitHub-Repository [digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer).

Anleitungen zur Mitwirkung an dieser Dokumentation finden Sie im [Microsoft-Leitfaden für Dokumentationsmitwirkende](/contribute/).

## <a name="other-considerations"></a>Weitere Überlegungen

### <a name="region-support"></a>Unterstützung für Regionen

Azure Digital Twins-Explorer ist für die Verwendung mit allen Instanzen von Azure Digital Twins in allen [unterstützten Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins) verfügbar.

Während der öffentlichen Vorschau können Daten jedoch für die Verarbeitung über andere Regionen als die Region gesendet werden, in der die Instanz gehostet wird. Um das Weiterleiten von Daten auf diese Weise in Situationen zu vermeiden, in denen die Datenhoheit ein Problem ist, können Sie den [Open-Source-Code](#how-to-contribute) herunterladen, um eine lokal gehostete Version des Explorers auf Ihrem eigenen Computer zu erstellen.

### <a name="billing"></a>Abrechnung

Azure Digital Twins-Explorer ist ein kostenloses Tool für die Interaktion mit dem Azure Digital Twins-Dienst. Während das Tool selbst kostenlos ist, können während der Verwendung Kosten anfallen, wenn Anforderungen an den Azure Digital Twins-Dienst gesendet werden Dabei gelten die folgenden Preisrichtlinien: [Azure Digital Twins-Preise](https://azure.microsoft.com/pricing/details/digital-twins/).

## <a name="next-steps"></a>Nächste Schritte 

Detaillierte Informationen zur Verwendung von Azure Digital Twins-Explorer-Funktionen: [Verwenden des Azure Digital Twins-Explorers](how-to-use-azure-digital-twins-explorer.md).