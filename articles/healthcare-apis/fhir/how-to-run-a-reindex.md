---
title: Ausführen eines Neuindizierungsauftrags im FHIR-Dienst – Azure Healthcare-APIs (Vorschau)
description: Ausführen eines Neuindizierungsauftrags zum Indizieren von Such- oder Sortierparametern, die noch nicht in Ihrer Datenbank indiziert wurden
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/23/2021
ms.author: cavoeg
ms.openlocfilehash: 5544e0caee421d128d7238cb140c4dd525ed7022
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778911"
---
# <a name="running-a-reindex-job"></a>Ausführen eines Neuindizierungsauftrags

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Es gibt Szenarien, in denen Sie möglicherweise Such- oder Sortierparameter im FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) haben, die noch nicht indiziert wurden. Dieses Szenario ist relevant, wenn Sie Ihre eigenen Suchparameter definieren. Bis der Suchparameter indiziert ist, kann er nicht in der Suche verwendet werden. Dieser Artikel enthält eine Übersicht über das Ausführen eines Neuindizierungsauftrags zum Indizieren von Such- oder Sortierparametern, die noch nicht in Ihrer Datenbank indiziert wurden.

> [!Warning]
> Es ist wichtig, dass Sie diesen gesamten Artikel lesen, bevor Sie beginnen. Ein Neuindizierungsauftrag kann sehr leistungsintensiv sein. Dieser Artikel enthält Optionen zum Drosseln und Steuern des Neuindizierungsauftrags.

## <a name="how-to-run-a-reindex-job"></a>Ausführen eines Auftrags zum Neuindizieren 

Verwenden Sie das folgende Codebeispiel, um einen Neuindizierungsauftrag zu starten:

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

Wenn die Anforderung erfolgreich ist, wird der Status **201 Erstellt** zurückgegeben. Das Ergebnis dieser Meldung sieht wie folgt aus:

```json
HTTP/1.1 201 Created 
Content-Location: https://{{FHIR URL}}/_operations/reindex/560c7c61-2c70-4c54-b86d-c53a9d29495e 

{
  "resourceType": "Parameters",
  "id": "560c7c61-2c70-4c54-b86d-c53a9d29495e",
  "meta": {
    "versionId": "\"4c0049cd-0000-0100-0000-607dc5a90000\""
  },
  "parameter": [
    {
      "name": "id",
      "valueString": "560c7c61-2c70-4c54-b86d-c53a9d29495e"
    },
    {
      "name": "queuedTime",
      "valueDateTime": "2021-04-19T18:02:17.0118558+00:00"
    },
    {
      "name": "totalResourcesToReindex",
      "valueDecimal": 0.0
    },
    {
      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 0.0
    },
    {
      "name": "progress",
      "valueDecimal": 0.0
    },
    {
      "name": "status",
      "valueString": "Queued"
    },
    {
      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {
      "name": "resources",
      "valueString": ""
    },
    {
      "name": "searchParams",
      "valueString": ""
    }
  ]
}
```

> [!NOTE]
> Um den Status eines Neuindizierungsauftrags zu überprüfen oder abzubrechen, benötigen Sie die Neuindizierungs-ID. Dies ist die ID der resultierenden Parameters-Ressource. Im obigen Beispiel wäre die ID für den Neuindizierungsauftrag `560c7c61-2c70-4c54-b86d-c53a9d29495e` .

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>Überprüfen des Status eines Neuindizierungsauftrags

Nachdem Sie einen Neuindizierungsauftrag gestartet haben, können Sie den Status des Auftrags mithilfe des folgenden Aufrufs überprüfen:

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

Der Status des Ergebnisses des Neuindizierungsauftrags ist unten dargestellt:

```json
{

  "resourceType": "Parameters",
  "id": "b65fd841-1c62-47c6-898f-c9016ced8f77",
  "meta": {

    "versionId": "\"1800f05f-0000-0100-0000-607a1a7c0000\""
  },
  "parameter": [

    {

      "name": "id",
      "valueString": "b65fd841-1c62-47c6-898f-c9016ced8f77"
    },
    {

      "name": "startTime",
      "valueDateTime": "2021-04-16T23:11:35.4223217+00:00"
    },
    {

      "name": "queuedTime",
      "valueDateTime": "2021-04-16T23:11:29.0288163+00:00"
    },
    {

      "name": "totalResourcesToReindex",
      "valueDecimal": 262544.0
    },
    {

      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 5754.0
    },
    {

      "name": "progress",
      "valueDecimal": 2.0
    },
    {

      "name": "status",
      "valueString": "Running"
    },
    {

      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {

      "name": "resources",
      "valueString": 
      "{LIST OF IMPACTED RESOURCES}"
    },
    {
```

Die folgenden Informationen werden im Ergebnis des Neuindizierungsauftrags angezeigt:

* **totalResourcesToReindex:** Schließt die Gesamtzahl der Ressourcen ein, die im Rahmen des Auftrags neu indiziert werden.

* **resourcesSuccessfullyReindexed:** Die Summe, die bereits erfolgreich neu indiziert wurde.

* **progress:** Auftragsprozentwert neu indizieren abgeschlossen. Entspricht resourcesSuccessfullyReindexed/totalResourcesToReindex x 100.

* **status:** Gibt an, ob der Neuindizierungsauftrag in die Warteschlange eingereiht, ausgeführt, abgeschlossen, fehlgeschlagen oder abgebrochen wurde.

* **resources:** Listet alle Ressourcentypen auf, die vom Neuindizierungsauftrag betroffen sind.

## <a name="delete-a-reindex-job"></a>Löschen eines Neuindizierungsauftrags

Wenn Sie einen Neuindizierungsauftrag abbrechen müssen, verwenden Sie einen Löschaufruf, und geben Sie die ID des Neuindizierungsauftrags an:

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Ein Neuindizierungsauftrag kann sehr leistungsintensiv sein. Wir haben einige Drosselungssteuerelemente implementiert, mit denen Sie verwalten können, wie ein Neuindizierungsauftrag in Ihrer Datenbank ausgeführt wird.

> [!NOTE]
> Bei großen Datasets ist es nicht ungewöhnlich, dass ein Neuindizierungsauftrag tagelang ausgeführt wird.

Im Folgenden finden Sie eine Tabelle mit den verfügbaren Parametern, Standardwerten und empfohlenen Bereichen. Sie können diese Parameter verwenden, um entweder den Prozess zu beschleunigen (mehr Compute zu verwenden) oder den Prozess zu verlangsamen (weniger Compute). 

| **Parameter**                     | **Beschreibung**              | **Standard**        | **Verfügbarer Bereich**            |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | Die Verzögerung zwischen den einzelnen Batches von Ressourcen, die während des Neuindizierungsauftrags gestartet werden. Eine kleinere Zahl beschleunigt den Auftrag, während eine höhere Zahl ihn verlangsamt. | 500 MS (,5 Sekunden) | 50 bis 500000 |
| MaximumResourcesPerQuery  | Die maximale Anzahl von Ressourcen, die im Ressourcenbatch enthalten sind, der neu indiziert werden soll.  | 100 | 1-5000 |
| MaximumConcurrency  | Die Anzahl der gleichzeitig ausgeführten Batches.  | 1 | 1 - 10 |

Wenn Sie einen der oben genannten Parameter verwenden möchten, können Sie diese beim Starten des Auftrags für die Neuindizierung an die Ressource Parameter übergeben.

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "maximumConcurrency",
      "valueInteger": "3"
    },
    {
      "name": "queryDelayIntervalInMilliseconds",
      "valueInteger": "1000"
    },
    {
      "name": "maximumNumberOfResourcesPerQuery",
      "valueInteger": "1"
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie einen Neuindizierungsauftrag starten. Informationen zum Definieren neuer Suchparameter, die den Auftrag für die Neuindizierung erfordern, finden Sie unter 

>[!div class="nextstepaction"]
>[Definieren von benutzerdefinierten Suchparametern](how-to-do-custom-search.md)
