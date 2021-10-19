---
title: Patient-everything
description: In diesem Artikel wird erläutert, wie Der Patient-Everything-Vorgang verwendet wird.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: 3aaed02f7de7e8a4495415b15d893fd7b357fc63
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595007"
---
# <a name="using-patient-everything-in-fhir-service"></a>Verwenden $patient alles im FHIR-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der [$patient-alles-Vorgang](https://www.hl7.org/fhir/patient-operation-everything.html) wird verwendet, um einem Patienten Zugriff auf den gesamten Datensatz zu ermöglichen oder einem Anbieter oder anderen Benutzer einen Massendownload von Daten zu ermöglichen. Dieser Vorgang wird verwendet, um alle Informationen im Zusammenhang mit einem oder mehr Patienten zurück, die in der Ressource oder im Kontext beschrieben werden, in der bzw. dem dieser Vorgang aufgerufen wird.  

## <a name="use-patient-everything"></a>Verwenden von patient-everything
Verwenden Sie zum Aufrufen von patient-everything den folgenden Befehl:

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Der FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) überprüft, ob der Patient mit der angegebenen Patienten-ID übereinstimmen kann. Wenn ein Ergebnis gefunden wird, ist die Antwort ein Paket vom Typ "searchset" mit den folgenden Informationen: 
* [Patient-Ressource](https://www.hl7.org/fhir/patient.html) 
* Ressourcen, auf die direkt von der Patient-Ressource verwiesen wird (außer Link) 
* Ressourcen im [Patientendepartment](https://www.hl7.org/fhir/compartmentdefinition-patient.html)
* [Geräteressourcen,](https://www.hl7.org/fhir/device.html) die auf die Patient-Ressource verweisen. Dies ist auf 100 Geräte beschränkt. Wenn der Patient über mehr als 100 Geräte verfügt, die mit ihnen verknüpft sind, werden nur 100 zurückgegeben. 


## <a name="patient-everything-parameters"></a>Patient-everything-Parameter
Der FHIR-Dienst unterstützt die folgenden Abfrageparameter. die alle optional sind:

|Query parameter (Abfrageparameter)        |  BESCHREIBUNG|
|-----------------------|------------|
| \_type | Hier können Sie angeben, welche Ressourcentypen in die Antwort eingeschlossen werden. \_Type=Encounter würde beispielsweise nur Ressourcen zurückgeben, die dem Patienten zugeordnet `Encounter` sind. |
| \_since | Gibt nur Ressourcen zurück, die seit der angegebenen Zeit geändert wurden. |
| start | Wenn Sie das Startdatum angeben, werden Ressourcen eingezogen, deren datumsgemäß nach dem angegebenen Startdatum liegt. Wenn kein Startdatum angegeben wird, befinden sich alle Datensätze vor dem Enddatum im Gültigkeitsbereich. |
| end | Wenn Sie das Enddatum angeben, werden Ressourcen mit dem Zeitpunkt, an dem ihr Datum vor dem angegebenen Enddatum liegt, in die Ressourcen eingezogen. Wenn kein Enddatum angegeben wird, befinden sich alle Datensätze nach dem Startdatum im Gültigkeitsbereich. |

> [!Note]
> Sie müssen eine ID für einen bestimmten Patienten angeben. Wenn Sie alle Daten für alle Patienten benötigen, lesen Sie [$export](../data-transformation/export-data.md). 


## <a name="examples-of-patient-everything"></a>Beispiele für $patient alles 

Im Folgenden finden Sie einige Beispiele für die Verwendung $patient -alles-Vorgangs. 

Verwenden Sie $patient folgenden Aufruf, um "alles" eines Patienten zwischen 2010 und 2020 mithilfe von "alles" abfragen zu können: 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

Verwenden Sie $patient Aufruf, um die Beobachtung und die Beobachtung eines Patienten mithilfe von "alles" abfragen zu können: 
```json
GET {FHIRURL}/Patient/{ID}/$everything?_type=Observation,Encounter 
```

Um $patient-everything zum Abfragen des "Alles" eines Patienten seit 2021-05-27T05:00:00Z zu verwenden, verwenden Sie den folgenden Aufruf: 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

Wenn für jeden dieser Aufrufe ein Patient gefunden wird, erhalten Sie eine 200-Antwort mit einer `Bundle` der entsprechenden Ressourcen zurück.

## <a name="next-step"></a>Nächster Schritt
Nachdem Sie nun wissen, wie Der Patient-Everything-Vorgang verwendet wird, können Sie sich über weitere Suchoptionen in der Übersicht über den Suchleitfaden informieren.

>[!div class="nextstepaction"]
>[Übersicht über die FHIR-Suche](overview-of-search.md)
