---
title: Konvertieren von Ontologien
titleSuffix: Azure Digital Twins
description: Hier erhalten Sie Informationen zum Konvertierungsprozess branchenüblicher Modelle in DTDL für Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 45df0eb8bfc6ab5b181b8bc103c4a4a47f3196f6
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428447"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Konvertieren von Branchenstandardontologien in DTDL für Azure Digital Twins

Die meisten [Ontologien](concepts-ontologies.md) basieren auf semantischen Webstandards wie [OWL](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF) und [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Damit ein Modell mit Azure Digital Twins verwendet werden kann, muss es im DTDL-Format vorliegen. Dieser Artikel bietet einen allgemeinen Leitfaden für den Entwurf in Form eines **Konvertierungsmusters** zur Konvertierung von RDF-basierten Modellen in DTDL, damit diese mit Azure Digital Twins verwendet werden können. 

Er enthält außerdem einen [Beispielkonvertercode](#converter-samples) für RDF- und OWL-Konverter, die für andere Schemas in der Baubranche erweitert werden können.

## <a name="conversion-pattern"></a>Konvertierungsmuster

Zur Konvertierung von RDF-basierten Modellen in DTDL können mehrere Bibliotheken von Drittanbietern verwendet werden. Mit einigen dieser Bibliotheken können Sie die Modelldatei in ein Diagramm laden. Sie können das Diagramm durchlaufen, um nach spezifischen RDFS- und OWL-Konstrukten zu suchen und diese in DTDL zu konvertieren.   

In der folgenden Tabelle sind Beispiele dafür aufgeführt, wie RDFS- und OWL-Konstrukte in DTDL zugeordnet werden können. 

| RDFS/OWL-Konzept | RDFS/OWL-Konstrukt | DTDL-Konzept | DTDL-Konstrukt |
| --- | --- | --- | --- |
| Klassen | `owl:Class`<br>IRI-Suffix<br>``rdfs:label``<br>``rdfs:comment`` | Schnittstelle | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Unterklassen von  werden erstellt. | `owl:Class`<br>IRI-Suffix<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Schnittstelle | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Datentypeigenschaften | `owl:DatatypeProperty`<br>`rdfs:label` oder `INode`<br>`rdfs:label`<br>`rdfs:range` | Schnittstelleneigenschaften | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objekteigenschaften | `owl:ObjectProperty`<br>`rdfs:label` oder `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (oder weggelassen, wenn ohne `rdfs:range`)<br>`comment`<br>`displayName`<br>

Im folgenden C#-Codeausschnitt wird eine RDF-Modelldatei unter Verwendung der [dotNetRDF](https://www.dotnetrdf.org/)-Bibliothek in ein Diagramm geladen und in DTDL konvertiert. 

:::code language="csharp" source="~/digital-twins-docs-samples/other/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Konverterbeispiele

### <a name="rdf-converter-application"></a>RDF-Konverteranwendung 

Es steht eine Beispielanwendung zur Verfügung, mit der eine RDF-basierte Modelldatei für die Verwendung im Azure Digital Twins-Dienst in [DTDL (Version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) konvertiert werden kann. Diese wurde für das [Brick](https://brickschema.org/ontology/)-Schema überprüft und kann für andere Schemas in der Baubranche erweitert werden (z. B. [Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/), [Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/) oder [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Das Beispiel ist eine [.NET Core-Befehlszeilenanwendung namens RdfToDtdlConverter](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/).

Um den Code auf Ihren Computer herunterzuladen, wählen Sie auf der Beispielseite unterhalb des Titels die Schaltfläche **Code durchsuchen** aus. Sie gelangen zum GitHub-Repository für das Beispiel. Klicken Sie auf die Schaltfläche **Code** und dann auf **ZIP herunterladen**, um das Beispiel als ZIP-Datei namens *RdfToDtdlConverter-main.zip* herunterzuladen. Anschließend können Sie die Datei entzippen und den Code untersuchen.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Screenshot: GitHub-Repository „RdfToDtdlConverter“. Die Schaltfläche „Code“ wurde ausgewählt, und ein Dialogfeld wird angezeigt, in dem die Schaltfläche „ZIP herunterladen“ hervorgehoben ist." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

Sie können sich mit dieser Beispielanwendung die Konvertierungsmuster im Kontext ansehen und als Baustein für Ihre eigenen Anwendungen verwenden. Außerdem können Sie entsprechend Ihren spezifischen Anforderungen Modellkonvertierungen durchführen.

### <a name="owl2dtdl-converter"></a>OWL2DTDL-Konverter 

Beim [OWL2DTDL-Konverter](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) handelt es sich um ein Beispiel, das eine OWL-Ontologie in mehrere DTDL-Schnittstellendeklarationen übersetzt. Diese können mit Azure Digital Twins verwendet werden. Der Konverter kann auch für Ontologienetzwerke verwendet werden, die aus einer Stammontologie bestehen, für die andere Ontologien über `owl:imports`-Deklarationen wiederverwendet werden.

Dieser Konverter wurde verwendet, um die [RealEstateCore-Ontologie](https://doc.realestatecore.io/3.1/full.html) in DTDL zu übersetzen. Er kann für jede OWL-basierte Ontologie wiederverwendet werden.

## <a name="next-steps"></a>Nächste Schritte 

* Informieren Sie sich über das Erweitern von Branchenstandardontologien zur Erfüllung Ihrer Spezifikationen: [Erweitern von Branchenontologien](concepts-ontologies-extend.md).

* Oder fahren Sie mit dem Pfad für das Entwickeln von Modellen basierend auf Ontologien fort: [Verwenden von Ontologiestrategien in einem Modellentwicklungspfad](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).