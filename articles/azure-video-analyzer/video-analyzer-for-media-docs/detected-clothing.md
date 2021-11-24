---
title: In einem Video erkannte Bekleidung von Personen
description: Dieses Thema bietet eine Übersicht über das Feature, bei dem die Bekleidung einer Person in einem Video erkannt wurde.
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: juliako
ms.openlocfilehash: 4bc0c0fd21a68d80bf8c8803f1a64e6e0ea097c7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495376"
---
# <a name="peoples-detected-clothing-preview"></a>Erkannte Bekleidung von Personen (Vorschau)

Video Analyzer for Media erkennt Kleidungsstücke in Verbindung mit der Person, die sie im Video trägt, und liefert Informationen wie die Art der erkannten Bekleidung und den Zeitstempel des Auftritts (Beginn, Ende). Die API gibt den Konfidenzgrad der Erkennung zurück.
 
Zwei Beispiele, in denen das Feature nützlich sein kann:
 
* Verbessern Sie die Effizienz beim Erstellen von Rohdaten für Inhaltsersteller, wie Videowerbung, Nachrichten oder Mannschaftsspiele (z. B. das Auffinden von Personen, die ein rotes Hemd in einem Videoarchiv tragen).
* Post-Event-Analyse: Erkennen und nachverfolgen von Bewegungen einer Person, um einen Unfall oder ein Verbrechen nach dem Ereignis besser analysieren zu können (z. B. Explosion, Banküberfall, Vorfall).
 
Das neu hinzugefügte Feature für die Erkennung von Kleidung ist verfügbar, wenn Sie Ihre Datei mit der Voreinstellung **Erweiterte Option** -> **Erweitertes Video** oder **Erweitertes Video + Audio** (unter „Video + Audio Indizierung“) indizieren. Die Standardindizierung schließt dieses neue erweiterte Modell nicht ein.
 
:::image type="content" source="./media/detected-clothing/index-video.png" alt-text="Screenshot: Videoindizierungsoption":::  

Wenn Sie auf der Website von [Video Analyzer for Media](https://www.videoindexer.ai/) (früher Video Indexer) **Erkenntnisse** zu Ihrem Video erhalten möchten, können Sie die erkannten Bekleidungsstücke der Personen in den Erkenntnissen zur Ablaufverfolgung der **beobachteten Personen** anzeigen lassen. Bei der Auswahl einer Miniaturansicht einer Person wurde die erkannte Bekleidung verfügbar.

:::image type="content" source="./media/detected-clothing/observed-people.png" alt-text="Screenshot: Beobachtete Personen":::  
 
Wenn Sie daran interessiert sind, die von Personen erkannte Bekleidung in der Zeitleiste Ihres Videos auf der Website von Video Analyzer for Media anzuzeigen, wechseln Sie zu **Ansicht** -> **Erkenntnisse anzeigen**, und wählen Sie die Option **Alle** oder **Ansicht** -> **Benutzerdefinierte Ansicht** aus. Anschließend wählen Sie **Beobachtete Personen** aus. 

:::image type="content" source="./media/detected-clothing/observed-person.png" alt-text="Screenshot: Beobachtete Personen":::  
 
Die Suche nach einem bestimmten Kleidungsstück, um alle beobachteten Personen, die dieses Kleidungsstück tragen, anzuzeigen, kann mithilfe der Suchleiste entweder in den **Erkenntnissen** oder auf der **Zeitleiste** Ihres Videos auf der Website von Video Analyzer for Media erfolgen.

Die folgende JSON-Antwort veranschaulicht, was Video Analyzer for Media zurückgibt, wenn beobachtete Personen nachverfolgt werden, denen erkannte Bekleidung zugeordnet ist:

```json
"observedPeople": [
    {
        "id": 1,
        "thumbnailId": "68bab0f2-f084-4c2b-859b-a951ed03c209",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:05.5055",
                "adjustedEnd": "0:00:09.9766333",
                "start": "0:00:05.5055",
                "end": "0:00:09.9766333"
            }
        ]
    },
    {
        "id": 2,
        "thumbnailId": "449bf52d-06bf-43ab-9f6b-e438cde4f217",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:07.2072",
                "adjustedEnd": "0:00:10.5105",
                "start": "0:00:07.2072",
                "end": "0:00:10.5105"
            }
        ]
    },
]
```

## <a name="limitations-and-assumptions"></a>Einschränkungen und Voraussetzungen

Es ist wichtig, die Einschränkungen bei erkannter Bekleidung zu beachten, damit die Auswirkungen von falsch-negativen Ergebnissen (verpasste Erkennungen) vermieden oder minimiert werden können.
 
* Verwenden Sie Videomaterial von statischen Kameras (obwohl eine bewegte Kamera oder gemischte Szenen ebenfalls Ergebnisse liefern), um die Erkennungsergebnisse zu optimieren.
* Personen werden nicht erkannt, wenn sie klein erscheinen (die Mindesthöhe der Person beträgt 200 Pixel).
* Die maximale Framegröße ist HD
* Personen werden nicht erkannt, wenn sie nicht stehen oder gehen.
* Videos mit niedriger Qualität (z. B. dunkle Beleuchtungsbedingungen) können sich auf die Erkennungsergebnisse auswirken.
* Die empfohlene Bildfrequenz beträgt mindestens 30 FPS.
* Die empfohlene Videoeingabe sollte bis zu 10 Personen in einem Einzel-Frame enthalten. Die Funktion kann mit mehr Personen in einem Einzel-Frame funktionieren, aber das Erkennungsergebnis ruft bis zu 10 Personen in einem Frame mit der höchsten Erkennungssicherheit ab.
* Personen mit ähnlicher Kleidung (z. B. Personen, die Uniformen tragen, Spieler in Mannschaftsspielen) können als dieselbe Person mit der gleichen ID-Nummer erkannt werden.
* Okklusionen: Es können Fehler auftreten, bei denen Okklusionen (Szene/Selbst oder Verdecken durch andere Personen) auftreten.
* Pose: Die Nachverfolgungen können aufgrund verschiedener Posen (hinten/vorne) aufgeteilt werden

## <a name="next-steps"></a>Nächste Schritte 

[Nachverfolgen von beobachteten Personen in einem Video](observed-people-tracing.md)
