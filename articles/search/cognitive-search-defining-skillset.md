---
title: Erstellen eines Skillsets
titleSuffix: Azure Cognitive Search
description: Definieren Sie Schritte zur Datenextraktion, Verarbeitung natürlicher Sprache oder Bildanalyse, um strukturierte Informationen aus Ihren Daten für die Verwendung in der kognitiven Azure-Suche anzureichern und zu extrahieren.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/15/2021
ms.openlocfilehash: 4425a4b7c29bc0f4bc237c021610087c933631d8
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224038"
---
# <a name="create-a-skillset-in-azure-cognitive-search"></a>Erstellen eines Skillsets in Azure Cognitive Search

![Indexerstufen](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "Indexerphasen")

Ein Skillset definiert die Vorgänge, die Daten extrahieren und anreichern, um sie durchsuchbar zu machen. Ein Skillset wird nach einer Dokumententschlüsselung ausgeführt, nachdem die Text- und Bildinhalte aus der Quelldokumenten extrahiert wurden und nachdem alle Felder aus dem Quelldokument den Zielfeldern in einem Index oder einem Wissensspeicher (optional) zugeordnet wurden.

In diesem Artikel lernen Sie die Schritte zum Erstellen eines Skillsets. Als Referenz wird in diesem Artikel die Funktion[Skillset erstellen (REST-API)](/rest/api/searchservice/create-skillset) verwendet. 

Einige Verwendungsregeln für Skillsets umfassen Folgendes:

+ Ein Skillset ist eine Ressource der obersten Ebene. Das bedeutet, dass sie einmal erstellt wird und dann von vielen Indexern referenziert werden kann.
+ Ein Skillset muss mindestens eine Fertigkeit enthalten.
+ Ein Skillset kann Fertigkeiten desselben Typs wiederholen (z. B. mehrere Former-Fertigkeiten).

Denken Sie daran, dass Indexer die Ausführung von Skillsets vorantreiben. Das bedeutet, dass Sie auch einen [Indexer](search-howto-create-indexers.md), eine [Datenquelle](search-data-sources-gallery.md) und einen [Suchindex](search-what-is-an-index.md) erstellen müssen, bevor Sie Ihr Skillset testen können.

> [!TIP]
> Aktivieren Sie das [Zwischenspeichern von Anreicherungen](cognitive-search-incremental-indexing-conceptual.md), um bereits verarbeitete Inhalte wiederzuverwenden und die Entwicklungskosten zu senken.

## <a name="skillset-definition"></a>Definition von Qualifikationsgruppen

Beginnen Sie mit der grundlegenden Struktur. In der [REST-API](/rest/api/searchservice/create-skillset) wird ein Skillset in JSON verfasst. Es besteht aus den folgenden Abschnitten:

```json
{
   "name":"skillset-template",
   "description":"A description makes the skillset self-documenting (comments aren't allowed in JSON itself)",
   "skills":[
      
   ],
   "cognitiveServices":{
      "@odata.type":"#Microsoft.Azure.Search.CognitiveServicesByKey",
      "description":"A Cognitive Services resource in the same region as Azure Cognitive Search",
      "key":"<Your-Cognitive-Services-Multiservice-Key>"
   },
   "knowledgeStore":{
      "storageConnectionString":"<Your-Azure-Storage-Connection-String>",
      "projections":[
         {
            "tables":[ ],
            "objects":[ ],
            "files":[ ]
         }
      ]
    },
    "encryptionKey":{ }
}
```

Nach dem Namen und der Beschreibung verfügt ein Skillset über vier Haupteigenschaften:

+ Ein `skills`-Array, eine ungeordnete [Sammlung von Fertigkeiten](cognitive-search-predefined-skills.md), für die der Service die Ausführungssequenz basierend auf den für jede Fertigkeiten erforderlichen Eingaben bestimmt. Wenn die Fertigkeiten unabhängig sind, werden sie parallel ausgeführt. Fertigkeiten können nützlich (z. B. für das Aufteilen von Text), transformative (basierend auf KI aus dem Cognitive Services) oder von Ihnen zur Verfügung stellende benutzerdefinierte Fertigkeiten sein. Ein Beispiel für ein Fertigkeiten-Array finden Sie im folgenden Abschnitt.

+ `cognitiveServices` wird für [abrechenbare Fertigkeiten](cognitive-search-predefined-skills.md) verwendet, die Cognitive Services-APIs aufrufen. Entfernen Sie diesen Abschnitt, wenn Sie weder abrechenbaren Fertigkeiten noch einen benutzerdefinierte Entitäten Lookup verwenden. [Fügen Sie eine Ressource hinzu](cognitive-search-attach-cognitive-services.md), wenn Sie es sind.

+ `knowledgeStore`, (optional) gibt ein Azure Storage-Konto und Einstellungen für die Projektierung von Skillset-Ausgaben in Tabellen, Blobs und Dateien in Azure Storage an. Entfernen Sie diesen Abschnitt, wenn Sie ihn nicht benötigen, andernfalls [geben Sie einen Wissensspeicher](knowledge-store-create-rest.md) an.

+ `encryptionKey`, (optional) gibt einen Azure Key Vault und einen vom [Kunden verwalteten Schlüssel](search-security-manage-encryption-keys.md) an, die verwendet werden, um sensible Inhalte in einer Skillset-Definition zu verschlüsseln. Entfernen Sie diese Eigenschaft, wenn Sie keine vom Kunden verwaltete Verschlüsselung verwenden.

## <a name="add-a-skills-array"></a>Hinzufügen eines Fertigkeiten-Arrays

Innerhalb einer Skillset-Definition gibt das Fertigkeiten-Array an, welche Fertigkeiten ausgeführt werden sollen. Das folgende Beispiel führt Sie in seine Zusammensetzung ein, indem es Ihnen zwei nicht verbundene [integrierte Fertigkeiten](cognitive-search-predefined-skills.md) zeigt. Beachten Sie, dass jede Fertigkeit über einen Typ, einen Kontext, Eingaben und Ausgaben verfügt. 

```json
"skills":[
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "organizations",
        "targetName": "orgs"
      }
    ]
  },
  {
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "score",
        "targetName": "mySentiment"
      }
    ]
  }
]
```

> [!NOTE]
> Sie können komplexe Skillsets mit Schleifen und Branches erstellen, indem Sie den [Skill „Bedingt“](cognitive-search-skill-conditional.md) zur Erstellung der Ausdrücke verwenden. Die Syntax basiert auf der [JSON-Zeiger](https://tools.ietf.org/html/rfc6901)-Pfadnotation, mit einigen Änderungen zum Identifizieren von Knoten in der Anreicherungsstruktur. Ein `"/"` durchläuft eine niedrigere Ebene in der Struktur und `"*"` fungiert als „for-each“-Operator im Kontext. Zahlreiche Beispiele in diesem Artikel veranschaulichen die Syntax. 

### <a name="how-built-in-skills-are-structured"></a>Nachfolgend erfahren Sie, wie integrierte Fertigkeiten strukturiert sind

Jede Fertigkeit ist hinsichtlich ihrer Eingabewerte und der benötigten Parameter eindeutig. Die [Dokumentation für jede Fertigkeit](cognitive-search-predefined-skills.md) beschreibt alle Parameter und Eigenschaften einer bestimmten Fertigkeit. Obwohl es Unterschiede gibt, teilen sich die meisten Fertigkeiten einen gemeinsamen Satz und sind ähnlich gemustert. Zur Veranschaulichung einiger Punkte dient die [Fertigkeit zur Erkennung von Entitäten](cognitive-search-skill-entity-recognition-v3.md) als Beispiel:

```json
{
  "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
  "context": "/document",
  "categories": [ "Organization" ],
  "defaultLanguageCode": "en",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "organizations",
      "targetName": "orgs"
    }
  ]
}
```

Allgemeine Parameter schließen „odata.type“, „Eingaben“ und „Ausgaben“ ein. Die anderen Parameter, d. h. „Kategorien“ und „defaultLanguageCode“, sind Beispiele für Parameter, die spezifisch für die Erkennung von Entitäten sind. 

+ **„odata.type“** identifiziert eindeutig jede Fertigkeit. Sie finden den Typ in der [Referenzdokumentation zu den Fertigkeiten](cognitive-search-predefined-skills.md).

+ **„Kontext“** ist ein Knoten in einer Anreicherungsstruktur und stellt die Ebene dar, auf der die Vorgänge ausgeführt werden. Alle Fertigkeiten verfügen über diese Eigenschaft. Wenn das Feld „Kontext“ nicht explizit festgelegt ist, wird das Dokument als Standardkontext `"/document"` genommen. In der oben gezeigten Fertigkeit ist der Kontext das gesamte Dokument. Das bedeutet, dass die Fertigkeiten zur Entitätserkennung einmal pro Dokument aufgerufen wird.

  Der Kontext bestimmt auch, wo Ausgaben auch in der Anreicherungsstruktur erzeugt werden. In diesem Beispiel gibt die Fertigkeit eine Eigenschaft namens `"organizations"`, die als `orgs` erfasst ist und die als untergeordneter Knoten von `"/document"` hinzugefügt wird, zurück. Bei nachgelagerten Fertigkeiten lautet der Pfad zu diesem neu erstellten Anreicherungsknoten `"/document/orgs"`. Für ein bestimmtes Dokument ist der Wert von `"/document/orgs"` ein Array von Unternehmen, die aus dem Text extrahiert werden (z. B.: `["Microsoft", "LinkedIn"]`). Weitere Informationen zur Pfadsyntax finden Sie unter [Verweisen auf Anmerkungen in einem Skillset](cognitive-search-concept-annotations-syntax.md).

+ Die **„Eingaben“** geben den Ursprung der eingehenden Daten und deren Verwendung an. Im Fall der Entitätserkennung ist eine der Eingaben `"text"`. Das ist der Inhalt, der für die Entitäten analysiert werden soll. Der Inhalt stammt aus dem Knoten `"/document/content"` in einer Anreicherungsstruktur. In einer Anreicherungsstruktur, ist `"/document"` der Stammknoten. Bei Dokumenten, die mit einem Azure Blob Indexer abgerufen werden, ist das `content` Feld jedes Dokuments ein Standardfeld, das vom Indexer erstellt wird. 

+ Die **„Ausgaben“** stellen die Ausgabe einer Fertigkeit dar. Jede Fertigkeit ist so konzipiert, dass bestimmte Arten von Ausgaben ausgegeben werden, auf die über den Namen im Skillset verwiesen wird. Im Fall der Entitätserkennung, ist `"organizations"` eine der unterstützten Ausgaben. In der Dokumentation für jede Fertigkeit werden die Ausgaben beschrieben, die sie erzeugen kann.

Ausgaben gibt es nur während der Verarbeitung. Um diese Ausgabe mit der Eingabe eines nachfolgenden Skills zu verketten, verweisen Sie auf die Ausgabe mit `"/document/orgs"`. Um die Ausgabe an ein Feld in einem Suchindex zu senden, müssen Sie [eine Ausgabefeldzuordnung in einem Indexer erstellen](cognitive-search-output-field-mapping.md). Um die Ausgabe an einen Wissensspeicher zu senden, müssen Sie [eine Projektion erstellen](knowledge-store-projection-overview.md).

Ausgaben dieses einen Skills können zu Konflikten mit Ausgaben eines anderen Skills führen. Wenn Sie über mehrere Fertigkeiten verfügen, die dieselbe Ausgabe zurückgeben, müssen Sie `"targetName"` zur Namensunterscheidung innerhalb der Anreicherungsknotenpfade verwenden.

In manchen Situationen ist es notwendig, auf jedes Element eines Arrays einzeln zu verweisen. Angenommen, Sie möchten *jedes Element* von `"/document/orgs"` separat an eine anderen Fertigkeit übergeben. Fügen Sie dazu dem Pfad ein Sternchen hinzu: `"/document/orgs/*"` 

Die zweite Fertigkeit zum analysieren von Stimmungen folgt dem gleichen Muster wie die erste Anreicherungsfunktion. Er verwendet `"/document/content"` als Eingabe und gibt einen Wert für die Stimmung für jede Inhaltsinstanz zurück. Da Sie das „Kontext“-Feld nicht explizit festgelegt haben, ist die Ausgabe (mySentiment) nun ein untergeordnetes Element von `"/document"`.

```json
{
  "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "score",
      "targetName": "mySentiment"
    }
  ]
}
```

## <a name="adding-a-custom-skill"></a>Hinzufügen einer benutzerdefinierten Fertigkeit

Nachstehend sehen Sie ein Beispiel für eine [benutzerdefinierte Fertigkeit](cognitive-search-custom-skill-web-api.md). Der URI verweist auf eine Azure-Funktion, die wiederum das von Ihnen bereitgestellte Modell oder die von Ihnen bereitgestellte Transformation aufruft. Weitere Informationen finden Sie unter [Definieren einer benutzerdefinierte Schnittstelle](cognitive-search-custom-skill-interface.md).

Obwohl die benutzerdefinierte Fertigkeit einen Code außerhalb der Pipeline ausführt, handelt es sich bei einem Fertigkeiten-Array nur um eine weitere Fertigkeit. Wie die integrierten Fertigkeiten, verfügt sie auch über einen Typ, einen Kontext, Eingaben und Ausgaben. Sie liest und schreibt auch in eine Anreicherungsstruktur, genau wie die integrierten Fertigkeiten. Beachten Sie, dass das Feld „context“ mit einem Sternchen auf `"/document/orgs/*"` gesetzt ist, d.h. der Anreicherungsschritt wird *für jede* Organisation unter `"/document/orgs"` aufgerufen.

Die Ausgabe, in diesem Fall eine Firmenbeschreibung, wird für jede identifizierte Organisation generiert. Bei Bezugnahme auf die Beschreibung in einem nachfolgenden Schritt (z.B. bei der Schlüsselbegriffserkennung) würden Sie dazu den Pfad `"/document/orgs/*/companyDescription"` verwenden. 

```json
{
  "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
  "description": "This skill calls an Azure function, which in turn calls custom code",
  "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeCode?code=foo",
  "httpHeaders": {
      "Ocp-Apim-Subscription-Key": "foobar"
  },
  "context": "/document/orgs/*",
  "inputs": [
    {
      "name": "query",
      "source": "/document/orgs/*"
    }
  ],
  "outputs": [
    {
      "name": "description",
      "targetName": "companyDescription"
    }
  ]
}
```

## <a name="send-output-to-an-index"></a>Versenden einer Ausgabe an einen Index

Während jeder Fertigkeit ausgeführt wird, wird ihre Ausgabe als Knoten in der Anreicherungsstruktur eines Dokuments hinzugefügt. Angereicherte Dokumente sind in der Pipeline als temporäre Datenstrukturen vorhanden. Um eine permanente Datenstruktur zu erstellen und vollständigen Einblick in das zu erhalten, was eine Fertigkeit tatsächlich erzeugt, müssen Sie die Ausgabe an einen Suchindex oder einen [Wissensspeicher](knowledge-store-concept-intro.md) senden.

In den frühen Phasen der Skillset-Auswertung sollten Sie vorläufige Ergebnisse mit minimalem Aufwand überprüfen. Wir empfehlen dafür den Suchindex, da er einfacher eingerichtet werden kann. Definieren Sie für jede Skill-Ausgabe eine [Ausgabefeldzuordnung](cognitive-search-output-field-mapping.md) im Indexer und ein Feld im Index.

Ein :::image type="content" source="media/cognitive-search-defining-skillset/skillset-indexer-index-combo.png" alt-text="Objektdiagramm, das zeigt, wie eine Personenentität in der Fertigkeitsausgabe, Indexerfeldzuordnung und Indexfeld definiert wird":::.

Nach dem Ausführen des Indexers können Sie mit dem [Such-Explorer](search-explorer.md) Dokumente aus dem Index zurückgeben und den Inhalt der einzelnen Felde überprüfen, um zu bestimmen, was das Skillset erkannt oder erstellt hat.

Das folgende Beispiel zeigt die Ergebnisse einer Entitätserkennungs-Fertigkeit, die Personen, Orte, Unternehmen und andere Entitäten in einem Textblock erkannt hat. Wenn Sie die Ergebnisse im Such-Explorer anzeigen, können Sie ermitteln, ob eine Fertigkeit ihrer Projektmappe einen Mehrwert für Ihre Lösung bietet.

:::image type="content" source="media/cognitive-search-defining-skillset/doc-in-search-explorer.png" alt-text="Ein Screenshot, der ein Dokuments im Such-Explorer zeigt.":::

## <a name="tips-for-a-first-skillset"></a>Tipps für ein erstes Skillset

+ Stellen Sie eine repräsentative Stichprobe Ihrer Inhalte in Blob Storage oder einer anderen unterstützten Indexerdatenquelle zusammen, und führen Sie den **Datenimport**-Assistenten aus, um das Skillset, den Index, den Indexer und das Datenquellenobjekt zu erstellen. 

  Der Assistent automatisiert mehrere Schritte, die beim ersten Mal schwierig sein können, wie z. B. die Definition der Felder in einem Index, die Definition von Ausgabefeldzuordnungen in einem Indexer und Projektionen in einem Wissensspeicher, wenn Sie einen solchen verwenden. Für einige Skills, wie OCR oder Bildanalyse, fügt der Assistent Hilfsskills hinzu, die Bild- und Textinhalte zusammenführen, die bei der Dokumententschlüsselung getrennt wurden.

+ Alternativ können Sie Postman-Skillsammlungen importieren, die vollständige Beispiele für alle Objektdefinitionen bereitstellen, die zum Auswerten eines Skills erforderlich sind, vom Skillset bis hin zu einem Index, den Sie abfragen können, um die Ergebnisse einer Transformation anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Die Kontext- und Eingabequellenfelder sind Pfade zu Knoten in einer Anreicherungsstruktur. Im nächsten Schritt erfahren Sie mehr über die Syntax zum Einrichten von Pfaden zu Knoten in einer Anreicherungsstruktur.

> [!div class="nextstepaction"]
> [Verweisen auf Anmerkungen in einem Skillset](cognitive-search-concept-annotations-syntax.md)
