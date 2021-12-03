---
title: Nachverfolgen von beobachteten Personen in einem Video
description: Dieses Thema bietet eine Übersicht über die Verfolgung beobachteter Personen in einem Videokonzept.
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 6726c67cf5274ba252b86ace9165330dc296afb4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491941"
---
# <a name="trace-observed-people-in-a-video-preview"></a>Beobachtete Personen in einem Video zurückverfolgen (Vorschau)

Video Indexer erkennt beobachtete Personen in Videos und liefert Informationen wie die Position der Person im Video-Frame und den genauen Zeitstempel (Start, Ende), wann eine Person erscheint. Die API gibt die Begrenzungsfeldkoordinaten (in Pixel) für jede erkannte Personeninstanz zurück, einschließlich Erkennungssicherheit.  
 
Einige Szenarien, in denen die Funktion nützlich sein kann:

* Post-Event-Analyse: Erkennen und nachverfolgen von Bewegungen einer Person, um einen Unfall oder ein Verbrechen nach dem Ereignis besser analysieren zu können (z. B. Explosion, Banküberfall, Vorfall).  
* Verbessern Sie die Effizienz beim Erstellen von Rohdaten für Inhaltsersteller, wie Videowerbung, Nachrichten oder Mannschaftsspiele (z. B. das Auffinden von Personen, die ein rotes Hemd in einem Videoarchiv tragen).
* Erstellen eine Zusammenfassung anhand eines langen Videos, z. B. einen Gerichtsbeweis für das Auftreten einer bestimmten Person in einem Video, unter Verwendung der gleichen erkannten Personen-ID.
* Lernen und analysieren Sie Trends im Laufe der Zeit, z. B. wie sich Kunden in einem Einkaufszentrum durch die Gänge bewegen oder wie viel Zeit sie in der Kassenschlange verbringen.

Wenn ein Video beispielsweise eine Person enthält, listet der Erkennungsvorgang das Erscheinen dieser Person zusammen mit ihren Koordinaten in den Videoframes auf. Sie können diese Funktion verwenden, um den Weg der Person in einem Video zu bestimmen. Sie können außerdem ermitteln, ob mehrere Instanzen derselben Person einem Video enthalten sind.

Die neu hinzugefügte Funktion **Verfolgung von beobachteten Personen** ist verfügbar, wenn Sie Ihre Datei (unter **Video + Audio Indizierung**) mit der Voreinstellung **Erweiterte Option** -> **Erweitertes Video** oder **Erweitertes Video + Audio** indizieren. Die Standardindizierung schließt dieses neue erweiterte Modell nicht ein. 

:::image type="content" source="./media/observed-people-tracing/youtube-trailer.png" alt-text="Screenshot der Verfolgung für beobachteten Personen":::  
 
Wenn Sie sich dafür entscheiden, **Einblicke** in Ihr Video auf der [Video Indexer](https://www.videoindexer.ai/account/login) Website anzuzeigen, wird die Verfolgung von beobachteten Personen auf der Seite mit allen erkannten Personenminiaturansichten angezeigt. Sie können eine Miniaturansicht einer Person auswählen und sehen, wo die Person im Videoplayer erscheint. 

Die folgende JSON-Antwort veranschaulicht, was Video Indexer zurückgibt, wenn beobachtete Personen nachverfolgt werden: 

```json
    {
    ...
    "videos": [
        {
            ...
            "insights": {
                ...
                "observedPeople": [{
                    "id": 1,
                    "thumbnailId": "560f2cfb-90d0-4d6d-93cb-72bd1388e19d",
                    "instances": [
                        {
                            "adjustedStart": "0:00:01.5682333",
                            "adjustedEnd": "0:00:02.7027",
                            "start": "0:00:01.5682333",
                            "end": "0:00:02.7027"
                        }
                    ]
                },
                {
                    "id": 2,
                    "thumbnailId": "9c97ae13-558c-446b-9989-21ac27439da0",
                    "instances": [
                        {
                            "adjustedStart": "0:00:16.7167",
                            "adjustedEnd": "0:00:18.018",
                            "start": "0:00:16.7167",
                            "end": "0:00:18.018"
                        }
                    ]
                },]
            }
            ...
            }
    ]
}
```

## <a name="limitations-and-assumptions"></a>Einschränkungen und Annahmen 

Es ist wichtig, die Einschränkungen bei der Nachverfolgung beobachteter Personen zu beachten, damit die Auswirkungen von falsch-negativen Ergebnissen (verpasste Erkennungen) und begrenzten Details vermieden oder minimiert werden können.

* Verwenden Sie Videomaterial von statischen Kameras (obwohl eine bewegte Kamera oder gemischte Szenen ebenfalls Ergebnisse liefern), um die Erkennungsergebnisse zu optimieren. 
* Personen werden in der Regel nicht erkannt, wenn sie klein erscheinen (die Mindesthöhe der Person beträgt 200 Pixel).
* Die maximale Framegröße ist HD
* Personen werden in der Regel nicht erkannt, wenn sie nicht stehen oder gehen. 
* Videos mit niedriger Qualität (z. B. dunkle Beleuchtungsbedingungen) können sich auf die Erkennungsergebnisse auswirken. 
* Die empfohlene Frame-Frequenz – mindestens 30 FPS. 
* Die empfohlene Videoeingabe sollte bis zu 10 Personen in einem Einzel-Frame enthalten. Die Funktion kann mit mehr Personen in einem Einzel-Frame funktionieren, aber das Erkennungsergebnis ruft bis zu 10 Personen in einem Frame mit der höchsten Erkennungssicherheit ab. 
* Personen mit ähnlicher Kleidung (z. B. Personen, die Uniformen tragen, Spieler in Mannschaftsspielen) können als dieselbe Person mit der gleichen ID-Nummer erkannt werden. 
* Okklusionen: Es können Fehler auftreten, bei denen Okklusionen (Szene/Selbst oder Verdecken durch andere Personen) auftreten.
* Pose: Die Nachverfolgungen können aufgrund verschiedener Posen (hinten/vorne) aufgeteilt werden       

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](video-indexer-overview.md)
