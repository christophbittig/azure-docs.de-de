---
title: Erweitern von Ontologien
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie mehr über die Gründe und Strategien hinter der Erweiterung einer Ontologie.
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 4051f8fe4b338f223b36c3a87ebcc3e3d3e51979
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472971"
---
# <a name="extending-ontologies"></a>Erweitern von Ontologien 

Eine [Ontologie](concepts-ontologies.md), die dem Branchenstandard entspricht, z. B. die [DTDL-basierte RealEstateCore-Ontologie für Smart-Gebäude](https://github.com/Azure/opendigitaltwins-building), ist eine hervorragende Möglichkeit, um mit der Erstellung Ihrer IoT-Lösung zu beginnen. Branchenontologien bieten umfassende Basisschnittstellen, die für Ihre Domäne konzipiert sind und so entwickelt wurden, dass sie von Beginn an mit Azure IoT-Diensten wie Azure Digital Twins funktionieren. 

Allerdings ist es möglich, dass Ihre Lösung spezifische Anforderungen aufweist, die nicht von der Branchenontologie abgedeckt werden. Beispielsweise können Sie Ihre digitalen Zwillinge mit 3D-Modellen verknüpfen, die in einem separaten System gespeichert werden. In diesem Fall können Sie eine dieser Ontologien erweitern, um Ihre eigenen Funktionen hinzuzufügen und dabei alle Vorteile der ursprünglichen Ontologie beizubehalten.

In diesem Artikel wird die DTDL-basierte [RealEstateCore](https://www.realestatecore.io/)-Ontologie als Grundlage für Beispiele zum Erweitern von Ontologien mit neuen DTDL-Eigenschaften verwendet. Hier werden allgemeine Verfahren beschrieben, die jedoch auch auf jeden Teil einer auf DTDL-basierenden Ontologie mit einer beliebigen DTDL-Funktion (Telemetrie, Eigenschaft, Beziehung, Komponente oder Befehl) angewendet werden kann. 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore-Bereichshierarchie 

In der DTDL-basierten RealEstateCore-Ontologie wird die Bereichshierarchie zum Definieren verschiedener Arten von Bereichen verwendet: Räume, Gebäude, Zonen usw. Die Hierarchie erstreckt sich von jedem dieser Modelle, um verschiedene Arten von Räumen, Gebäuden und Zonen zu definieren. 

Ein Teil dieser Hierarchie entspricht der Darstellung im folgenden Diagramm. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="Diagramm, das einen Teil der RealEstateCore-Bereichshierarchie illustriert. Es werden Elemente für Space, Room, ConferenceRoom und Office gezeigt."::: 

Weitere Informationen zur RealEstateCore-Ontologie finden Sie unter [Übernehmen von Branchenstandardontologien](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>Erweitern der RealEstateCore-Bereichshierarchie 

In manchen Fällen weist Ihre Lösung spezifische Anforderungen auf, die nicht von der Branchenontologie abgedeckt werden. In diesem Fall ermöglicht Ihnen das Erweitern der Hierarchie es Ihnen, weiter die Branchenontologie zu verwenden, während Sie diese entsprechend Ihrer Anforderungen anpassen. 

In diesem Artikel werden zwei verschiedene Fälle erläutert, in denen das Erweitern der Hierarchie der Ontologie hilfreich ist: 

* Hinzufügen neuer Schnittstellen für nicht in der Branchenontologie enthaltene Konzepte 
* Hinzufügen zusätzlicher Eigenschaften (oder Beziehungen, Komponenten, Telemetriedaten oder Befehle) zu vorhandenen Schnittstellen

### <a name="add-new-interfaces-for-new-concepts"></a>Hinzufügen neuer Schnittstellen für neue Konzepte 

In diesem Fall müssen Sie Schnittstellen für Konzepte hinzufügen, die für Ihre Lösung erforderlich, aber nicht in der Branchenontologie enthalten sind. Wenn Ihre Lösung beispielsweise andere Arten von Räumen umfasst, die nicht in der DTDL-basierten RealEstateCore-Ontologie enthalten sind, können Sie diese hinzufügen, indem Sie die RealEstateCore-Schnittstellen direkt erweitern. 

Im folgenden Beispiel wird eine Lösung veranschaulicht, die „Fokusräume“ darstellen muss, die nicht in der RealEstateCore-Ontologie vorhanden sind. Ein Fokusraum ist ein kleiner Bereich, der dazu konzipiert ist, dass sich Mitarbeiter in diesem einige Stunden auf eine bestimmte Aufgabe konzentrieren können. 

Erstellen Sie eine neue Schnittstelle, die die Schnittstellen in der Branchenontologie [erweitert](concepts-models.md#model-inheritance), um die Branchenontologie um dieses neue Konzept zu erweitern. 

Nachdem Sie die Schnittstelle für Fokusräume hinzugefügt haben, zeigt die erweiterte Hierarchie den neuen Raumtyp an. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="Diagramm, das einen Teil der RealEstateCore-Bereichshierarchie illustriert, einschließlich der neuen Hinzufügung."::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Hinzufügen zusätzlicher Funktionen zu vorhandenen Schnittstellen 

In diesem Fall möchten Sie weitere Eigenschaften (oder Beziehungen, Komponenten, Telemetrie oder Befehle) zu Schnittstellen in der Branchenontologie hinzufügen.

In diesem Abschnitt werden zwei Beispiele vorgestellt: 
* Wenn Sie eine Lösung erstellen, die 3D-Zeichnungen von Bereichen anzeigt, die bereits in einem vorhandenen System vorliegen, sollten Sie jeden digitalen Zwilling der jeweiligen 3D-Zeichnung zuordnen (nach ID), damit auch die 3D-Zeichnung aus dem System abgerufen werden kann, wenn die Lösung Informationen zum Bereich anzeigt. 
* Wenn Ihre Lösung den Online-/Offlinestatus von Konferenzräumen nachverfolgen muss, sollten Sie den Konferenzraumstatus für die Verwendung in Anzeigen oder Abfragen nachverfolgen. 

Beide Beispiele können mithilfe neuer Eigenschaften implementiert werden: eine `drawingId`-Eigenschaft, die die 3D-Zeichnung dem digitalen Zwilling und einer „Online“-Eigenschaft, die angibt, ob der Konferenzraum online ist oder nicht. 

In der Regel wird davon abgeraten, die Branchenontologie direkt zu ändern, da Sie in der Zukunft dazu in der Lage sein müssen, Updates für diese zu Ihrer Lösung hinzuzufügen (welche Ihre Ergänzungen überschreiben würden). Stattdessen können solche Additionen in Ihrer eigenen Schnittstellenhierarchie erfolgen, die die DTDL-basierte RealEstateCore-Ontologie erweitert. Jede Schnittstelle, die Sie erstellen, verwendet mehrere Schnittstellenvererbungen, um die übergeordnete RealEstateCore-Schnittstelle und die übergeordnete Schnittstelle Ihrer erweiterten Schnittstellenhierarchie zu erweitern. Dieser Ansatz ermöglicht es Ihnen, die Branchenontologie zusammen mit Ihren Ergänzungen zu verwenden. 

Zum Erweitern der Branchenontologie erstellen Sie Ihre eigenen Schnittstellen, die die Schnittstellen in der Branchenontologie erweitern und neue Funktionen zu Ihren erweiterten Schnittstellen hinzufügen. Für jede Schnittstelle, die Sie erweitern möchten, müssen Sie eine neue Schnittstelle erstellen. Die erweiterten Schnittstellen werden in DTDL geschrieben (Informationen hierzu finden Sie im Abschnitt „DTDL für erweiterte Schnittstellen“ später in diesem Artikel). 

Nach der Erweiterung des oben gezeigten Teils der Hierarchie sieht die erweiterte Hierarchie wie in der folgenden Abbildung aus. Die erweiterte Schnittstelle „Space“ fügt die Eigenschaft `drawingId` hinzu, die eine ID enthalten soll, die den digitalen Zwilling der 3D-Zeichnung zuordnet. Darüber hinaus fügt die ConferenceRoom-Schnittstelle eine Online-Eigenschaft hinzu, die den Onlinestatus des Konferenzraums enthält. Durch die Vererbung enthält die ConferenceRoom-Schnittstelle alle Funktionen der RealEstateCore-Schnittstelle „ConferenceRoom“ sowie alle Funktionen der erweiterten Schnittstelle „Space“. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="Diagramm, das die erweiterte RealEstateCore-Bereichshierarchie illustriert, mit weiteren neuen Hinzufügungen, wie beschrieben."::: 

## <a name="using-the-extended-space-hierarchy"></a>Verwenden der erweiterten Bereichshierarchie 

Wenn Sie digitale Zwillinge mithilfe der erweiterten Bereichshierarchie erstellen, stammen die Modelle jedes digitalen Zwillings aus der erweiterten Bereichshierarchie (nicht aus der ursprünglichen Branchenontologie) und enthalten alle Funktionen der Branchenontologie und die erweiterten Schnittstellen über die Schnittstellenvererbung.

Jedes Modell der digitalen Zwillinge ist dann eine Schnittstelle der erweiterten Hierarchie (siehe folgendes Diagramm). 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="Diagramm, das die erweiterte RealEstateCore-Bereichshierarchie illustriert, einschließlich der verbundenen Modelle Space, Room, ConferenceRoom, Office und FocusRoom."::: 

Beim Abfragen digitaler Zwillinge mithilfe der Modell-ID (der `IS_OF_MODEL`-Operator) sollten die Modell-IDs aus der erweiterten Hierarchie verwendet werden. Beispiel: `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Mitwirken bei der ursprünglichen Ontologie 

Manchmal erweitern Sie die Branchenontologie auf eine Weise, die für die meisten Benutzer der Ontologie nützlich ist. In diesem Fall sollten Sie es in Betracht ziehen, Ihre Erweiterungen zur ursprünglichen Ontologie beizutragen. Jede Ontologie bietet einen anderen Prozess zum Mitwirken. Überprüfen Sie also das GitHub-Repository der Ontologie, um Informationen zum Mitwirken zu erhalten. 

## <a name="dtdl-for-new-interfaces"></a>DTDL für neue Schnittstellen 

Die DTDL für neue Schnittstellen, die direkt aus der Branchenontologie erweitert werden, sieht wie folgt aus. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL für erweiterte Schnittstellen 

Die DTDL für die erweiterten Schnittstellen (beschränkt auf den oben behandelten Abschnitt) sieht wie folgt aus. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem Pfad für das Entwickeln von Modellen basierend auf Ontologien fort: [Verwenden von Ontologiestrategien in einem Modellentwicklungspfad](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).