---
title: Registrieren Sie sich für den Azure Video Analyzer for Media (früher Video Indexer) und laden Sie Ihr erstes Video hoch – Azure
description: Erfahren Sie, wie Sie sich anmelden und Ihr erstes Video mit dem Azure Video Analyzer for Media-Portal (früher Video Indexer) hochladen.
ms.topic: quickstart
ms.subservice: azure-video-analyzer-media
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: bf8b8f640fbcb6155092a50fdbd6e1af66060e07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636152"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Schnellstart: Registrieren und Hochladen Ihres ersten Videos

Dieser Schnellstart für die ersten Schritte zeigt, wie Sie sich auf dem Website des Azure Video Analyzer for Media (früher Video Indexer) anmelden und wie Sie Ihr erstes Video hochladen.

Beim Anlegen eines Video Analyzer for Media-Kontos können Sie ein kostenloses Testkonto (für das Sie eine bestimmte Anzahl von kostenlosen Indizierungsminuten erhalten) oder eine kostenpflichtige Option (bei der Sie nicht aufgrund des Kontingents begrenzt sind) wählen. Bei der kostenlosen Testversion stellt der Video Analyzer bis zu 600 Minuten an kostenloser Indizierungszeit für Websitebenutzer und bis zu 2400 Minuten an kostenloser Indizierungszeit für API-Benutzer bereit. Bei der kostenpflichtigen Option erstellen Sie ein Azure Video Analyzer for Media-Konto, [das mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden ist](connect-to-azure.md). Sie bezahlen pro Minute Indizierungszeit. Weitere Informationen finden Sie unter [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/). 

## <a name="sign-up-for-video-analyzer-for-media"></a>Registrieren Sie sich für Azure Video Analyzer for Media

Um mit der Entwicklung mit Azure Video Analyzer for Media zu beginnen, navigieren Sie zur [Azure Video Analyzer for Media-Website](https://www.videoindexer.ai/) und registrieren Sie sich.

Sobald Sie Azure Video Analyzer for Media verwenden, werden alle Ihre gespeicherten Daten und hochgeladenen Inhalte im Ruhezustand mit einem von Microsoft verwalteten Schlüssel verschlüsselt.

> [!NOTE]
> Überprüfen Sie die [geplanten Änderungen des Azure Video Analyzers für die Authentifizierung von Medien-Websites](./release-notes.md#planned-video-analyzer-for-media-website-authenticatication-changes).

## <a name="upload-a-video-using-the-video-analyzer-for-media-website"></a>Laden Sie ein Video über die Azure Video Analyzer for Media Website hoch

### <a name="supported-file-formats-for-video-analyzer-for-media"></a>Unterstützte Dateiformate für Azure Video Analyzer for Media

Im Artikel [Eingabecontainer/Dateiformate](../../media-services/latest/encode-media-encoder-standard-formats-reference.md) finden Sie eine Liste der Dateiformate, die Sie mit dem Azure Video Analyzer for Media verwenden können.

### <a name="upload-a-video"></a>Hochladen eines Videos

1. Melden Sie sich auf der [Azure Video Analyzer for Media](https://www.videoindexer.ai/) Website an.
1. Klicken Sie auf die Schaltfläche bzw. den Link **Hochladen**, um ein Video hochzuladen.

    > [!NOTE]
    > Der Name des Videos darf nicht mehr als 80 Zeichen umfassen.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Upload":::
1. Nachdem Ihr Video hochgeladen wurde, beginnt der Azure Video Analyzer for Media mit dem Indizieren und Analysieren des Videos. Der Status wird angezeigt. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Uploadstatus":::
1. Sobald der Azure Video Analyzer for Media die Analyse abgeschlossen hat, erhalten Sie eine E-Mail mit einem Link zu Ihrem Video und einer kurzen Beschreibung dazu, was in Ihrem Video gefunden wurde. Beispiel: Personen, gesprochene und geschriebene Wörter, Themen und benannte Entitäten.
1. Ihr Video ist später dann in der Bibliotheksliste aufgeführt, und Sie können unterschiedliche Vorgänge durchführen. Beispiel: Suchen, Erneutes Indizieren, Bearbeiten.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Abgeschlossener Uploadvorgang":::

## <a name="supported-browsers"></a>Unterstützte Browser

Weitere Informationen finden Sie unter [Unterstützte Browser](video-indexer-overview.md#supported-browsers).

## <a name="see-also"></a>Weitere Informationen

Weitere Informationen finden Sie unter [Hochladen und Indizieren Ihrer Videos](upload-index-videos.md).

Nachdem Sie ein Video hochgeladen und indiziert haben, können Sie die [Azure Video Analyzer for Media Website](video-indexer-view-edit.md) oder das [Azure Video Analyzer for Media Entwickler-Portal](video-indexer-use-apis.md) verwenden, um Einblicke in das Video zu erhalten. 

[Einstieg in APIs](video-indexer-use-apis.md)

## <a name="next-steps"></a>Nächste Schritte

Eine ausführliche Einführung erhalten Sie in unserem entsprechenden [Lab](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Am Ende des Workshops werden Sie einen guten Überblick über die Art von Informationen haben, die aus Video- und Audioinhalten extrahiert werden können. Sie werden besser darauf vorbereitet sein, Möglichkeiten im Zusammenhang mit Content Intelligence zu identifizieren, Video-KI auf Azure zu präsentieren und verschiedene Szenarien auf Video Analyzer for Media zu demonstrieren.

