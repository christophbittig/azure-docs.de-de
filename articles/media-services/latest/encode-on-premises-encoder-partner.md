---
title: Partner für lokale Encoder werden
description: In diesem Artikel wird erläutert, wie Sie Ihre lokalen Livestreamingencoder überprüfen.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: how-to
ms.service: media-services
ms.openlocfilehash: 553fb2b55a5b183b596016c2942106ead99e79c4
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387162"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Überprüfen der lokalen Livestreamingencoder

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wenn Sie Azure Media Services-Partner für lokale Encoder werden, unterstützt Media Services Ihr Produkt, indem es Ihren Encoder Unternehmenskunden empfiehlt. Um Partner für lokale Encoder zu werden, müssen Sie die Kompatibilität Ihres lokalen Encoders mit Media Services bestätigen. Führen Sie hierzu die folgenden Überprüfungsschritte aus.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Überprüfung von Pass-Through-Liveereignissen

1. Vergewissern Sie sich in Ihrem Media Services-Konto, dass der **Streamingendpunkt** ausgeführt wird. 
2. Erstellen und starten Sie das **Pass-Through**-Liveereignis („Basic“ oder „Standard“). <br/> Weitere Informationen finden Sie im Abschnitt [LiveEvent-Zustandswerte und Abrechnung](live-event-states-billing-concept.md).
3. Rufen Sie die Erfassungs-URLs ab, und konfigurieren Sie Ihren lokalen Encoder für die Verwendung der URL, um einen Livedatenstrom mit Mehrfachbitrate an Media Services zu senden.
4. Rufen Sie die Vorschau-URL ab und verwenden Sie sie, um sich zu vergewissern, dass die Eingabe des Encoders auch tatsächlich empfangen wird.
5. Erstellen Sie ein neues **Medienobjekt**.
6. Erstellen Sie eine **Liveausgabe**, und verwenden Sie den Namen des erstellten Medienobjekts.
7. Erstellen Sie einen **Streaminglocator** mit den integrierten Arten von **Streamingrichtlinien**.
8. Listen Sie die Pfade auf dem **Streaminglocator** auf, um die zu verwendenden URLs zurückzugeben.
9. Rufen Sie den Hostnamen für den **Streamingendpunkt** ab, von dem aus Sie streamen möchten.
10. Kombinieren Sie die URL aus Schritt 8 mit dem Hostnamen aus Schritt 9, um die vollständige URL zu erhalten.
11. Lassen Sie Ihren Liveencoder ungefähr 10 Minuten lang laufen.
12. Beenden Sie das Liveereignis. 
13. Verwenden Sie einen Player wie [Azure Media Player](https://aka.ms/azuremediaplayer), um die archivierten Medienobjekte zu beobachten, um sicherzustellen, dass die Wiedergabe auf allen Qualitätsebenen keine sichtbaren Störungen aufweist. Zum Beobachten und Überprüfen können Sie während der Livesitzung auch die Vorschau-URL verwenden.
14. Vermerken Sie die Medienobjekt-ID, die veröffentlichte Streaming-URL für das Livearchiv und die Einstellungen sowie die verwendete Version Ihres Liveencoders.
15. Setzen Sie den Status des Liveereignisses nach dem Erstellen der einzelnen Stichproben zurück.
16. Wiederholen Sie die Schritte 5 bis 15 für alle Konfigurationen, die von Ihrem Encoder unterstützt werden (mit und ohne Werbesignalisierung oder Untertiteln sowie in verschiedenen Codiergeschwindigkeiten).

## <a name="live-encoding-live-event-verification"></a>Überprüfung von Liveereignissen mit Livecodierung

1. Vergewissern Sie sich in Ihrem Media Services-Konto, dass der **Streamingendpunkt** ausgeführt wird. 
2. Erstellen und starten Sie ein **Livecodierungs**-Liveereignis. <br/> Weitere Informationen finden Sie im Abschnitt [LiveEvent-Zustandswerte und Abrechnung](live-event-states-billing-concept.md).
3. Rufen Sie die Erfassungs-URLs ab, und konfigurieren Sie Ihren Encoder so, dass er einen Livedatenstrom mit Einzelbitrate per Push an Media Services überträgt.
4. Rufen Sie die Vorschau-URL ab und verwenden Sie sie, um sich zu vergewissern, dass die Eingabe des Encoders auch tatsächlich empfangen wird.
5. Erstellen Sie ein neues **Medienobjekt**.
6. Erstellen Sie eine **Liveausgabe**, und verwenden Sie den Namen des erstellten Medienobjekts.
7. Erstellen Sie einen **Streaminglocator** mit den integrierten Arten von **Streamingrichtlinien**.
8. Listen Sie die Pfade auf dem **Streaminglocator** auf, um die zu verwendenden URLs zurückzugeben.
9. Rufen Sie den Hostnamen für den **Streamingendpunkt** ab, von dem aus Sie streamen möchten.
10. Kombinieren Sie die URL aus Schritt 8 mit dem Hostnamen aus Schritt 9, um die vollständige URL zu erhalten.
11. Lassen Sie Ihren Liveencoder ungefähr 10 Minuten lang laufen.
12. Beenden Sie das Liveereignis.
13. Verwenden Sie einen Player wie [Azure Media Player](https://aka.ms/azuremediaplayer), um die archivierten Medienobjekte zu beobachten, um sicherzustellen, dass die Wiedergabe auf allen Qualitätsebenen keine sichtbaren Störungen aufweist. Zum Beobachten und Überprüfen können Sie während der Livesitzung auch die Vorschau-URL verwenden.
14. Vermerken Sie die Medienobjekt-ID, die veröffentlichte Streaming-URL für das Livearchiv und die Einstellungen sowie die verwendete Version Ihres Liveencoders.
15. Setzen Sie den Status des Liveereignisses nach dem Erstellen der einzelnen Stichproben zurück.
16. Wiederholen Sie die Schritte 5 bis 15 für alle Konfigurationen, die von Ihrem Encoder unterstützt werden (mit und ohne Werbesignalisierung oder Untertiteln sowie in verschiedenen Codiergeschwindigkeiten).

## <a name="longevity-verification"></a>Überprüfung der Lebensdauer

Fügen Sie die gleichen Schritte wie bei der [Überprüfung von Pass-Through-Liveereignissen](#pass-through-live-event-verification) mit Ausnahme von Schritt 11 aus. <br/>Lassen Sie Ihren Liveencoder statt für 10 Minuten mindestens eine Woche lang laufen. Verwenden Sie einen Player wie [Azure Media Player](https://aka.ms/azuremediaplayer), um das Livestreaming (oder ein archiviertes Medienobjekt) von Zeit zu Zeit zu beobachten, um sicherzustellen, dass die Wiedergabe keine sichtbaren Störungen aufweist.

## <a name="email-your-recorded-settings"></a>Senden Ihrer aufgezeichneten Einstellungen per E-Mail

Senden Sie schließlich eine E-Mail mit Ihren aufgezeichneten Einstellungen und Livearchivparametern als Benachrichtigung darüber, dass alle automatischen Überprüfungen bestanden wurden, unter amshelp@microsoft.com an Azure Media Services. Schließen Sie auch Ihre Kontaktinformationen für nachfolgende Kontaktaufnahmen ein. Setzen Sie sich bei Fragen zu dieser Vorgehensweise mit dem Azure Media Services-Team in Verbindung.

## <a name="see-also"></a>Weitere Informationen

[Getestete lokale Encoder](encode-recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Nächste Schritte

[Livestreaming mit Media Services v3](stream-live-streaming-concept.md)
