---
title: Personalisierung mit mehreren Slots
description: Erfahren Sie mit den Personalizer Rang- und Belohnungs-APIs, wo und wann die Personalisierung mit einem Slot und mehreren Slots verwendet werden soll.
services: cognitive-services
author: jeffmend
ms.author: jeffme
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
ms.openlocfilehash: 38e94b4e7527fa32b24773e5120d60d6d039f169
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829427"
---
# <a name="multi-slot-personalization-preview"></a>Personalisierung mit mehreren Slots (Vorschau)

Mit der Personalisierung mit mehreren Slots (Vorschau) können Sie Inhalte in Weblayouts, Rotationen und Listen als Ziel verwenden, in denen Ihren Benutzern mehr als eine Aktion (z. B. ein Produkt oder ein Inhaltsteil) angezeigt wird. Mit Personalizer-APIs mit mehreren Slots können Sie die KI-Modelle in Personalizer lernen lassen, welche Benutzerkontexte und Produkte bestimmte Verhaltensweisen beeinflussen, und dabei die Platzierung in Ihrer Benutzeroberfläche berücksichtigen und daraus lernen. Zum Beispiel kann Personalizer feststellen, dass bestimmte Produkte oder Inhalte als Seitenleiste oder Fußzeile mehr Klicks bringen als als Haupt-Highlight auf einer Seite.

In diesem Artikel erfahren Sie, warum Personalisierung mit mehreren Slots die Ergebnisse verbessert, wie sie aktiviert wird und wann sie verwendet werden sollte. In diesem Artikel wird davon ausgegangen, dass Sie mit den Personalizer-APIs wie `Rank` und `Reward` vertraut sind und über ein konzeptionelles Verständnis der Verwendung in Ihrer Anwendung verfügen. Wenn Sie mit Personalizer und dessen Funktionsweise nicht vertraut sind, überprüfen Sie Folgendes, bevor Sie fortfahren:

* [Was ist Personalisierung?](what-is-personalizer.md) 
* [Funktionsweise der Personalisierung](how-personalizer-works.md) 

> [!IMPORTANT] 
>  Die Personalisierung mit mehreren Slots befindet sich in Public Preview. Features, Ansätze und Prozesse ändern sich basierend auf dem Benutzerfeedback. Wenn Sie die Vorschau für mehrere Slots aktivieren, werden andere Personalisierungsfunktionen in Ihrer Schleife dauerhaft deaktiviert. Die Personalisierung mit mehreren Slots kann nicht deaktiviert werden, wenn sie bereits für eine Personalizer-Schleife aktiviert wurde. Lesen Sie dieses Dokument und berücksichtigen Sie die Auswirkungen, bevor Sie eine Personalizer-Schleife für die Personalisierung mit mehreren Slots konfigurieren.

<!-- 
We encourage you to provide feedback on multi-slot personalization APIs via ...
-->

## <a name="when-to-use-multi-slot-personalization"></a>Wann die Personalisierung mit mehreren Slots angewendet wird

Wenn Sie Ihren Benutzern Produkte und/oder Inhalte anzeigen, möchten Sie Ihren Kunden mehrere Elemente anzeigen. Beispiel:

* **Websitelayouts für Startseiten**: Viele Kacheln und Seitenbereiche sind speziell für die Hervorhebung von Inhalten in Feldern, Bannern und Randleisten unterschiedlicher Formen und Größen verfügbar. Die Personalisierung mit mehreren Slots stellt fest, wie sich die Merkmale dieses Layouts auf die Auswahl und Aktionen der Kunden auswirken.
* **Rotation**: Für Rotationen mit dynamisch veränderlichem Inhalt sind mehrere Elemente erforderlich. Die Personalisierung mit mehreren Slots kann lernen, wie sich die Sequenz und sogar die Anzeigedauer auf Klicks und Kundenbindung auswirken.
* **Verwandte Produkte/Inhalte und eingebettete Verweise**: Es ist üblich, Benutzer einzubinden, indem Verweise auf zusätzliche Inhalte und Produkte in oder zwischen Bannern, Randleisten, Balken und Fußzeilenfeldern eingebettet werden. Die Personalisierung mit mehreren Slots kann Ihnen helfen, Ihre Verweise dort einzufügen, wo sie am wahrscheinlichsten eine größere Nutzung fördern.
* **Suchergebnisse oder Listen**: Wenn Ihre Anwendungssuchfunktion Ergebnisse als Listen oder Kacheln bereitstellt, können Sie die Personalisierung mit mehreren Slots verwenden, um auszuwählen, welche Elemente oben hervorgehoben werden sollen und dabei mehr Metadaten als herkömmliche Bewerter berücksichtigen.
* **Dynamische Kanäle und Wiedergabelisten**: Die Personalisierung mit mehreren Slots kann dabei helfen, eine kurze Sequenz für eine Liste von Videos oder Titeln zu bestimmen, die als Nächstes in einem dynamischen Kanal wiedergegeben werden sollen.

Durch die Personalisierung mit mehreren Slots können Sie die „Slots“ auf der Benutzeroberfläche bestimmen, für die Aktionen ausgewählt werden müssen. Außerdem können Sie weitere Informationen zu den Slots bereitstellen, die die Personalisierung zur Verbesserung der Produktplatzierung verwenden kann, z. B.: Ist dies eine große oder kleine Box? Wird eine Untertitel oder nur ein Feature angezeigt? Befindet sie sich in einer Fußzeile oder Randleiste?...


## <a name="how-to-use-multi-slot-personalization"></a>Wie die Personalisierung mit mehreren Slots angewendet wird 

1. [Aktivierung der Personalisierung mit mehreren Slots](#enable-multi-slot-personalization)
1. [Erstellen eines JSON-Objekts für die Ranganforderung](#create-json-object-for-a-rank-request)
1. [Aufrufen der Rang-API zum Definieren von Slots und Baseline-Aktionen](#use-the-response-of-the-rank-api)
1. [Aufrufen der Belohnungs-APIs](#call-the-reward-api)


### <a name="enable-multi-slot-personalization"></a>Aktivierung der Personalisierung mit mehreren Slots 

Weitere Informationen zum persönlichen Nutzen der Personalisierung mit mehreren Slots finden Sie weiter unten unter [Unterschiede zwischen Personalisierung mit einem oder mehreren Slots](#differences-between-single-slot-and-multi-slot-personalization). *Die Personalisierung mit mehreren Slots ist ein Vorschau-Feature*: Wir empfehlen Ihnen, eine neue Personalizer-Schleife zu erstellen, wenn Sie APIs für die Personalisierung mit mehreren Slots testen möchten, da die Aktivierung nicht umkehrbar ist und Auswirkungen auf eine Personalizer-Schleife hat, die in der Produktion ausgeführt wird.

Sobald Sie sich dazu entschieden haben, eine Schleife in eine Personalisierung mit mehreren Slots zu konvertieren, müssen Sie diese Schritte für diese Personalizer-Schleife einmal ausführen:

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](./includes/upgrade-personalizer-multi-slot.md)]

### <a name="create-json-object-for-a-rank-request"></a>Erstellen eines JSON-Objekts für die Ranganforderung
Die Verwendung der Personalisierung mit mehreren Slots erfordert eine API, die sich geringfügig von der Einzelslot-Personalisierungs-API unterscheidet.

Sie deklarieren die verfügbaren Slots zum Zuweisen von Aktionen in jeder Rangaufrufanforderung im Slots-Objekt:

* **Array von Slots**: Sie müssen ein Array von Slots deklarieren. Slots werden *geordnet*: Die Position der einzelnen Slots im Array ist wichtig. Es wird dringend empfohlen, Ihre Slot-Definitionen basierend darauf zu ordnen, wie viele Belohnungen/Klicks/Konvertierungen die einzelnen Slots tendenziell erhalten, beginnend mit dem Slot, der am besten erhält. Beispielsweise fügen Sie dann ein großes „Hero“-Feld auf der Homepage für eine Website als Slot 1 anstelle einer kleinen Fußzeile hinzu.  Die Personalisierung weist Aktionen mit einer besseren Wahrscheinlichkeit zu, dass sie früher in der Sequenz Belohnungen erhalten kann, ohne dass Sie sonst etwas verändern müssen.
* **Slot-ID**: Sie müssen jedem Slot eine „slotId“ geben. Das ist eine Zeichenfolge, die für alle anderen Slots in diesem Rangaufruf eindeutig ist.
* **Slot-Features**: Sie sollten zusätzliche Metadaten bereitstellen, die die Slots beschreiben und weiter von anderen Slots unterscheiden. Diese werden als *Features* bezeichnet. Beim Bestimmen von Slot-Features müssen Sie die gleichen Richtlinien befolgen, die für die Features von Kontext und Aktionen empfohlen werden (siehe: [Features für Kontext und Aktionen]()).  Typische Slot-Features helfen beim Identifizieren von Größe, Position oder visuellen Merkmalen eines Benutzeroberflächenelements. Zum Beispiel: `position: "top-left"`, `size: "big"`, `animated: "no"`, `sidebar: "true"` oder `sequence: "1"`.
* **Baseline-Aktionen**: Sie müssen die Baseline-Aktions-ID für jeden Slot angeben. Das ist die ID der Aktion, die in diesem Slot angezeigt wird, wenn die Personalisierung nicht vorhanden wäre. Dies ist erforderlich, um die Personalisierung im [Ausbildungsmodus]() zu trainieren und bei [Offlineauswertungen]() eine aussagekräftige Zahl zu haben.
* **Über ausreichend Aktionen verfügen**: Stellen Sie sicher, dass Sie Rangfolge mit mehr Aktionen als Slots aufrufen, damit die Personalisierung jedem Slot mindestens eine Aktion zuweisen kann. Die Personalisierung wiederholt Aktionsempfehlungen nicht Slot-übergreifend: Die Rangantwort weist jede Aktion höchstens einem Slot zu. 

Es ist kein Problem, wenn Sie Slots im Laufe der Zeit hinzufügen oder entfernen, ihre Features hinzufügen und ändern oder das Array neu anordnen: Die Personalisierung passt das Training forlaufend basierend auf den neuen Informationen an.

Hier sehen Sie ein Beispiel-`slots`-Objekt mit einigen Beispielfeatures. Während der Großteil des `slots`-Objekts stabil ist (da sich die Benutzeroberflächen tendenziell langsam ändern), ändert sich der Größte davon nicht häufig: Sie müssen jedoch sicherstellen, dass Sie jedem Rangfolgeaufruf die entsprechenden „baselineAction-IDs“ zuweisen. 


```json
"slots": [ 
    { 
      "id": "BigHighlight", 
      "features": [ 
            { 
              "size": "Large", 
              "position": "Left-Middle" 
            }
        ],
        "baselineAction": "BlackBoot_4656" 
    }, 

    { 
      "id": "Sidebar1", 
      "features": [ 
            { 
              "size": "Small", 
              "position": "Right-Top" 
            } 
        ],
        "baselineAction": "TrekkingShoe_1122"  
    }  
  ]

```


### <a name="use-the-response-of-the-rank-api"></a>Verwenden der Antwort der Rang-API

Eine Rangantwort mit mehreren Slots aus der obigen Anforderung kann wie folgt aussehen: 

```json
{ 
  "slots": [ 
        { 
          "id": "BigHighlight", 
          "rewardActionId": "WhiteSneaker_8181" 
        }, 
        { 
          "id": "SideBar1", 
          "rewardActionId": "BlackBoot_4656" 
        } 
    ], 
  "eventId": "123456D0-BFEE-4598-8196-C57383D38E10" 
} 
```

Verwenden Sie die „rewardAction-ID“ für jeden Slot und nutzen Sie sie, um Ihre Benutzeroberfläche entsprechend zu rendern.

### <a name="call-the-reward-api"></a>Aufrufen der Belohnungs-API

Personalizer lernt, wie Sie Aktionen auswählen, die die erhaltene Belohnung maximieren. Ihre Anwendung beobachtet das Benutzerverhalten und berechnet basierend auf der beobachteten Reaktion eine „Belohnungsbewertung“ für Personalizer. Wenn der Benutzer beispielsweise auf die Aktion im `"slotId": "SideBar1",` geklickt hat, senden Sie eine „1“ an die Personalisierung, um eine positive Verstärkung für die Aktionsoptionen bereitzustellen.

Die Belohnungs-API gibt die „eventId“ für die Belohnung in der URL an:

```http
https://{endpoint}/personalizer/v1.0/events/{eventId}/reward
``` 

Beispielsweise wird die Belohnung für das obige Ereignis mit der ID 123456D0-BFEE-4598-8196-C57383D38E10/reward an `https://{endpoint}/personalizer/v1.0/events/123456D0-BFEE-4598-8196-C57383D38E10/reward/reward` gesendet:

```json
{ 
  "reward": [ 
    { 
      "slotId": "BigHighlight", 
      "value": 0.2 
    }, 
    { 
      "slotId": "SideBar1", 
     "value": 1.0 
    }, 
  ] 
} 
```

Sie müssen nicht alle Belohnungsbewertungen in nur einem Aufruf der Reward-API bereitstellen. Sie können die Belohnungs-API mehrmals aufrufen, jeweils mit den entsprechenden „eventId“ und „slotIds“. Wenn für einen Slot in einem Ereignis keine Belohnungsbewertung empfangen wird, weist die Personalisierung implizit die für die Schleife konfigurierte standardmäßige Belohnung zu (in der Regel 0).


## <a name="differences-between-single-slot-and-multi-slot-personalization"></a>Unterschiede zwischen der Personalisierung mit einem einzelnen und mehreren Slots

Es gibt Unterschiede in der Verwendung der Rangfolge- und Belohnungs-APIs bei der Personalisierung mit einem einzelnen und mehreren Slots:

|  BESCHREIBUNG  | Personalisierung mit einem einzelnen Slot   | Personalisierung mit mehreren Slots    |  
|---------------|-------------------------------|-------------------------------|
| Rangfolge von API-Aufrufanforderungselementen | Sie senden ein Kontextobjekt und eine Liste von Aktionen  | Sie senden Kontext, eine Liste von Aktionen und eine geordnete Liste von Slots     | 
| Ranganforderung mit Angabe der Baseline | Personalizer verwendet die erste Aktion in der Aktionsliste als Baselineaktion (das Element, das Ihre Anwendung ausgewählt hätte, wenn Personalizer nicht vorhanden wäre).|Sie müssen die ActionID der Baseline angeben, die in jedem Slot verwendet worden wäre.|
| Rangfolge-API-Aufrufantwort | Ihre Anwendung hebt die im Feld „rewardActionId“ angegebene Aktion hervor     | Die Antwort enthält eine andere „rewardActionId“ für jeden Slot, der in der Anforderung angegeben wurde. Ihre Anwendung zeigt diese „rewardActionId“-Aktionen in jedem Slot an. | 
| Aufruf der Belohnungs-API | Sie rufen die Reward-API mit einer Belohnungsbewertung auf, die Sie berechnen, wie die Benutzer mit „rewardActionId“ für diese bestimmte „eventId“ interagiert haben. Wenn der Benutzer z. B. darauf geklickt hat, senden Sie eine Belohnung von 1.     | Sie geben die Belohnung für jeden Slot an, wenn Sie wissen, wie gut die Aktion mit „rewardActionId“ das gewünschte Benutzerverhalten geliefert hat. Dies kann in einem oder mehreren Belohnungs-API-Aufrufen mit der gleichen „eventId“ gesendet werden.   | 


### <a name="impact-of-enabling-multi-slot-for-a-personalizer-loop"></a>Auswirkungen der Aktivierung von mehreren Slots für eine Personalizer-Schleife

Berücksichtigen Sie außerdem Folgendes, wenn Sie mehrere Slots aktivieren:

|  BESCHREIBUNG  | Personalisierung mit einem einzelnen Slot   | Personalisierung mit mehreren Slots    | 
|---------------|-------------------------------|-------------------------------|
| Inaktive Ereignisse und Aktivierung    | Beim Aufrufen der Aktivierungs-API aktiviert Personalizer das Ereignis, erwartet eine Belohnungsbewertung oder weist die konfigurierte Standardbewertung zu, wenn die Belohnungswartezeit überschritten wird. | Personalizer aktiviert und erwartet Belohnungen für alle Slots, die in der „eventId“ angegeben wurden |     
| Ausbildungsmodus | Die Personalizer-Rangfolge-API gibt immer die Baseline-Aktion zurück und trainiert interne Modelle, indem die Baseline-Aktion imitiert wird.| Die Rangfolge-API von Personalizer gibt die Baseline-Aktion für jeden Slot zurück, der im Feld „baselineAction“ angegeben ist. Personalizer trainiert interne Modelle beim Imitieren des ersten Modells |
| Lerngeschwindigkeit | Lernt nur aus der hervorgehobenen Aktion | Kann aus Interaktionen mit einem beliebigen Slot lernen. Dies bedeutet in der Regel mehr Benutzerverhalten, das Belohnungen liefern kann, was zu einem schnelleren Lernen für Personalizer führen würde. |  
| Offlineauswertungen |Vergleicht die Leistung der Personalisierung mit Baseline-basierten und optimierten Lerneinstellungen, basierend darauf, welche Aktion ausgewählt worden wären.| (Vorschaueinschränkung) Wertet nur die Leistung des ersten Slots im Array aus. Für genauere Auswertungen wird empfohlen, sicherzustellen, dass der Slot mit den meisten Belohnungen der erste Slot in Ihrem Array ist. |
| Automatische Optimierung (Vorschau) | Ihre Personalizer-Schleife kann regelmäßig Offlineauswertungen im Hintergrund durchführen und Lerneinstellungen ohne Eingriff des Administrators optimieren | (Vorschaueinschränkung) Die automatische Optimierung ist für Personalizer-Schleifen deaktiviert, für die APIs mit mehreren Slots aktiviert sind. |

## <a name="next-steps"></a>Nächste Schritte
* [Wie die Personalisierung mit mehreren Slots angewendet wird](how-to-multi-slot.md)
* [Beispiel: Jupyter Notebook, das eine Personalizer-Schleife mit mehreren Slots ausführt](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer/azure-notebook)