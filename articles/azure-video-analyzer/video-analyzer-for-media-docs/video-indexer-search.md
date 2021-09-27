---
title: Suchen nach bestimmten Momenten in Videos mit Azure Video Analyzer for Media (früher Video Indexer)
description: Erfahren Sie, wie Sie mithilfe von Azure Video Analyzer for Media (früher Video Indexer) nach bestimmten Momenten in Videos suchen.
ms.topic: how-to
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: 454c5a8033e30a10183d03a1689b6e606bcb77ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607518"
---
# <a name="search-for-exact-moments-in-videos-with-video-analyzer-for-media"></a>Suchen nach bestimmten Momenten in Videos mit Video Analyzer for Media

In diesem Thema erfahren Sie, wie Sie die Website von Azure Video Analyzer for Media (früher Video Indexer) verwenden, um in Videos nach bestimmten Momenten zu suchen.

1. Navigieren Sie zur [Video Analyzer for Media](https://www.videoindexer.ai/)-Website und melden Sie sich an.
1. Geben Sie Suchwörter an – die Suche wird in allen Videos in der Bibliothek Ihres Kontos durchgeführt. 

    Sie können **Filter** auswählen, um Ihre Suche zu filtern. Im folgenden Beispiel suchen wir nach dem Begriff „Microsoft“, der nur als Bildschirmtext (optische Zeichenerkennung) angezeigt wird.

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filter, nur Text":::
1. Klicken Sie auf **Suchen**, um das Ergebnis anzuzeigen.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Ergebnis der Videosuche":::

    Wenn Sie ein Ergebnis auswählen, gelangen Sie im Player im Video zum entsprechenden Moment.
1. Klicken Sie im Video auf **Wiedergeben**, oder wählen Sie eins der ursprünglichen Suchergebnisse aus, um die zusammengefassten Erkenntnisse aus dem Video anzuzeigen und zu durchsuchen. 

    Sie können die **Erkenntnisse** anzeigen, durchsuchen und bearbeiten. Wenn Sie eine Erkenntnis auswählen, springt der Player im Video zu genau diesem Moment.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Anzeigen, Durchsuchen und Bearbeiten der Erkenntnisse aus dem Video":::

    Wenn Sie das Video mithilfe von Video Analyzer for Media-Widgets einbetten, können Sie die Ansicht für Player/Erkenntnisse in Ihrer App anzeigen und Synchronisierungen durchführen. Weitere Informationen finden Sie im Artikel zum [Einbetten von Video Analyzer for Media-Widgets in Ihre App](video-indexer-embed-widgets.md).
1. Klicken Sie auf die Registerkarte **Zeitachse**, um Transkripte anzuzeigen, zu durchsuchen und zu bearbeiten. 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Anzeigen, Durchsuchen und Bearbeiten der Transkripte des Videos":::

    Zum Bearbeiten des Texts wählen Sie in der oberen rechten Ecke **Bearbeiten** aus und ändern den Text nach Bedarf. 

    Sie können die Transkripte auch übersetzen und herunterladen, indem Sie oben rechts die entsprechende Option auswählen. 

## <a name="embed-download-create-projects"></a>Einbetten, Herunterladen und Erstellen von Projekten

Sie können Ihr Video einbetten, indem Sie unter dem Video die Option **</>Einbetten** auswählen. Weitere Informationen finden Sie unter [Einbetten von visuellen Widgets in Ihre Anwendung](video-indexer-embed-widgets.md).

Sie können das Quellvideo, Erkenntnisse aus dem Video sowie Transkripte herunterladen, indem Sie unter dem Video auf **Download** klicken.

Sie können basierend auf bestimmten Textzeilen und Momenten einen Clip aus Ihrem Video erstellen, indem Sie auf **In Editor öffnen** klicken. Bearbeiten das Video, und speichern Sie das Projekt. Weitere Informationen finden Sie unter [Verwenden des Video Indexer-Editors zum Erstellen von Projekten](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Einbetten, Herunterladen und Erstellen von Projekten aus dem Video":::

## <a name="next-steps"></a>Nächste Schritte

[Verarbeiten von Inhalten mit der Video Analyzer for Media-REST-API](video-indexer-use-apis.md)
