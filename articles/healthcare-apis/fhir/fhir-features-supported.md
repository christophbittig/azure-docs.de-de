---
title: Unterstützte FHIR-Features im FHIR-Dienst
description: In diesem Artikel wird erläutert, welche Features der FHIR-Spezifikation in Gesundheits-APIs implementiert sind.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: c1f40d6129f08b8369885631232b394d66f1d260
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045841"
---
# <a name="supported-fhir-features"></a>Unterstützte FHIR-Features

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der &reg; FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) bietet eine vollständig verwaltete Bereitstellung des [Open-Source-FHIR-Servers](https://github.com/microsoft/fhir-server) und ist eine Implementierung des [FHIR-Standards.](https://hl7.org/fhir) In diesem Dokument werden die Hauptfunktionen des FHIR-Diensts aufgeführt.

## <a name="fhir-version"></a>FHIR-Version

Letzte unterstützte Version: `4.0.1`

Derzeit ebenfalls unterstützte vorherige Versionen: `3.0.2`

## <a name="rest-api"></a>REST-API

| API    | Azure-API für FHIR | FHIR-Dienst in Healthcare-APIs | Comment |
|--------|--------------------|---------------------------------|---------|
| Lesen   | Ja                | Ja                             |         |
| vread  | Ja                | Ja                             |         |
| aktualisieren | Ja                | Ja                             |         | 
| Update mit optimistischer Sperre | Ja       | Ja       |
| update (bedingt)           | Ja       | Ja       |
| patch                          | Ja       | Ja       | Unterstützung nur für [JSON Patch.](https://www.hl7.org/fhir/http.html#patch) Wir haben eine Problemumgehung für die Verwendung des JSON-Patches in ein Paket in [diesem PR](https://github.com/microsoft/fhir-server/pull/2143)eingefügt.|
| Patch (bedingt)            | Ja       | Ja       |
| delete                         | Ja       | Ja       | Weitere Informationen finden Sie weiter unten im Abschnitt "Löschen". |
| delete (bedingt)           | Ja       | Ja       | Weitere Informationen finden Sie weiter unten im Abschnitt "Löschen". |
| history                        | Ja       | Ja       |
| create                         | Ja       | Ja       | Unterstützung für POST/PUT |
| create (bedingt)           | Ja       | Ja       | Issue [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | Weitere Informationen finden Sie [unter Übersicht über die FHIR-Suche.](overview-of-search.md) |
| Verkettete Suche                 | Ja       | Ja       | |
| Umgekehrte verkettete Suche         | Ja       | Ja       | |
| Batch                          | Ja       | Ja       |
| transaction                    | Nein        | Ja       |
| paging                         | Teilweise   | Teilweise   | `self` und `next` werden unterstützt                     |
| intermediaries                 | Nein        | Nein        |

### <a name="delete-and-conditional-delete"></a>Löschen und bedingtes Löschen

Der von der FHIR-Spezifikation definierte Löschvorgang erfordert, dass nach dem Löschen nachfolgende nicht versionsspezifische Lesevorgänge einer Ressource den HTTP-Statuscode 410 zurückgeben und die Ressource beim Suchen nicht mehr gefunden wird. Mit dem Azure API for FHIR und dem FHIR-Dienst können Sie die Ressource auch vollständig löschen (einschließlich des gesamten Verlaufs). Zum vollständigen Löschen der Ressource können Sie eine Einstellung des Parameters `hardDelete` auf TRUE (`DELETE {server}/{resource}/{id}?hardDelete=true`) übergeben. Wenn Sie diesen Parameter nicht übergeben oder für `hardDelete` FALSE festlegen, sind die früheren Versionen der Ressource weiterhin verfügbar.

Zusätzlich zum Löschen unterstützen die Azure API for FHIR und der FHIR-Dienst das bedingte Löschen, sodass Sie ein Suchkriterium zum Löschen einer Ressource übergeben können. Standardmäßig können Sie mit dem bedingten Löschen jeweils ein Element löschen. Sie können auch den `_count` Parameter angeben, um bis zu 100 Elemente gleichzeitig zu löschen. Im Folgenden finden Sie einige Beispiele für die Verwendung des bedingten Löschens.

Um ein einzelnes Element mit bedingtem Löschen zu löschen, müssen Sie Suchkriterien angeben, die ein einzelnes Element zurückgibt.
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704
```

Sie können dieselbe Suche durchführen, aber hardDelete=true einschließen, um auch den gesamten Verlauf zu löschen.
```JSON 
DELETE https://{{hostname}}/Patient?identifier=1032704&hardDelete=true
```

Wenn Sie mehrere Ressourcen löschen möchten, können Sie einschließen, `_count=100` wodurch bis zu 100 Ressourcen gelöscht werden, die den Suchkriterien entsprechen. 
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704&_count=100
```

## <a name="extended-operations"></a>Erweiterte Vorgänge

Alle Vorgänge, die unterstützt werden, um die REST-API zu erweitern.

| Suchparametertyp | Azure-API für FHIR | FHIR-Dienst in Healthcare-APIs| Comment |
|------------------------|-----------|-----------|---------|
| $export (gesamtes System) | Ja       | Ja       |         |
| Patient/$export        | Ja       | Ja       |         |
| Gruppe/$export          | Ja       | Ja       |         |
| $convert-data          | Ja       | Ja       |         |
| $validate              | Ja       | Ja       |         |
| $member-Match          | Ja       | Ja       |         |
| $patient alles    | Ja       | Ja       |         |
| $purge-Verlauf         | Ja       | Ja       |         |

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Der FHIR-Dienst verwendet [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) für die Zugriffssteuerung. 

## <a name="service-limits"></a>Diensteinschränkungen

* **Paketgröße:** Jedes Paket ist auf 500 Elemente beschränkt.

* **Abonnementlimit:** Standardmäßig ist jedes Abonnement auf maximal 10 FHIR-Dienste beschränkt. Der Grenzwert kann in einem oder mehreren Arbeitsbereichen verwendet werden. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über die unterstützten FHIR-Features im FHIR-Dienst erfahren. Stellen Sie als Nächstes den FHIR-Dienst bereit.
 
>[!div class="nextstepaction"]
>[Bereitstellen des FHIR-Diensts](fhir-portal-quickstart.md)
