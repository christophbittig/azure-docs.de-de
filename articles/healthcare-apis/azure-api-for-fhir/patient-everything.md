---
title: Verwenden von Patient-Everything in Azure API for FHIR
description: In diesem Artikel wird erläutert, wie Sie den Patient-Everything-Vorgang in der Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: 83c38f2add15481bab844f1c21a142644b546c0c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429206"
---
# <a name="patient-everything-in-fhir"></a>„patient-everything“ in FHIR

Der [Vorgang $patient alles](https://www.hl7.org/fhir/patient-operation-everything.html) wird verwendet, um einem Patienten Zugriff auf den gesamten Datensatz zu gewähren oder einem Anbieter oder anderen Benutzer den Massendownload von Daten zu ermöglichen. Dieser Vorgang wird verwendet, um alle Informationen im Zusammenhang mit einem oder mehreren Patienten zurückzugeben, die in der Ressource oder dem Kontext beschrieben sind, für die dieser Vorgang aufgerufen wird.  

## <a name="use-patient-everything"></a>Verwenden von patient-everything
Um patient-everything aufzurufen, verwenden Sie den folgenden Befehl:

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Der Azure API for FHIR überprüft, ob er den Patienten finden kann, der mit der angegebenen Patienten-ID übereinstimmt. Wenn ein Ergebnis gefunden wird, ist die Antwort ein Bündel vom Typ "searchset" mit den folgenden Informationen: 
* [Patientenressource](https://www.hl7.org/fhir/patient.html) 
*  Ressourcen, auf die direkt von der Patient-Ressource verwiesen wird (mit Ausnahme des Links) 
*  Ressourcen im [Patientende depot](https://www.hl7.org/fhir/compartmentdefinition-patient.html)
*  [Geräteressourcen,](https://www.hl7.org/fhir/device.html) die auf die Patient-Ressource verweisen. Dies ist auf 100 Geräte beschränkt. Wenn der Patient mehr als 100 Geräte mit ihnen verknüpft hat, werden nur 100 zurückgegeben. 


## <a name="patient-everything-parameters"></a>Parameter für "Patient-everything"
Die Azure API for FHIR unterstützt die folgenden Abfrageparameter, die alle optional sind:

|Query parameter (Abfrageparameter)        |  BESCHREIBUNG|
|-----------------------|------------|
| \_type | Ermöglicht es Ihnen, anzugeben, welche Ressourcentypen in die Antwort einbezogen werden sollen. Beispielsweise \_ würde type=Encounter nur Ressourcen zurückgeben, `Encounter` die dem Patienten zugeordnet sind. |
| \_since | Gibt nur Ressourcen zurück, die seit dem angegebenen Zeitpunkt geändert wurden. |
| start | Wenn Sie das Startdatum angeben, werden Ressourcen abgerufen, deren klinisches Datum nach dem angegebenen Startdatum liegt. Wenn kein Startdatum angegeben wird, befinden sich alle Datensätze vor dem Enddatum im Gültigkeitsbereich. |
| end | Wenn Sie das Enddatum angeben, werden Ressourcen abgerufen, deren klinisches Datum vor dem angegebenen Enddatum liegt. Wenn kein Enddatum angegeben wird, befinden sich alle Datensätze nach dem Startdatum im Gültigkeitsbereich. |

> [!Note]
> Sie müssen eine ID für einen bestimmten Patienten angeben. Wenn Sie alle Daten für alle Patienten benötigen, lesen Sie [$export](../data-transformation/export-data.md). 


## <a name="examples-of-patient-everything"></a>Beispiele für $patient-alles 

Im Folgenden finden Sie einige Beispiele für die Verwendung des vorgangs $patient-everything. 

Verwenden Sie den folgenden Aufruf, um $patient-everything zum Abfragen des "alles" eines Patienten zwischen 2010 und 2020 zu verwenden: 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

Verwenden Sie den folgenden Aufruf, um $patient-everything zum Abfragen von Beobachtung und Begegnung eines Patienten zu verwenden: 
```json
GET {FHIRURL}/Patient/{ID}/$everything?_type=Observation,Encounter 
```

Verwenden Sie den folgenden Aufruf, um $patient-everything zum Abfragen des "alles" eines Patienten seit 2021-05-27T05:00:00Z zu verwenden: 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

Wenn für jeden dieser Aufrufe ein Patient gefunden wird, erhalten Sie eine 200-Antwort mit einem Bündel der entsprechenden Ressourcen.

## <a name="next-step"></a>Nächster Schritt
Nachdem Sie nun wissen, wie Sie den Vorgang patient-everything verwenden, können Sie sich in der Übersicht über den Suchleitfaden über weitere Suchoptionen informieren.

>[!div class="nextstepaction"]
>[Übersicht über die FHIR-Suche](overview-of-search.md)
