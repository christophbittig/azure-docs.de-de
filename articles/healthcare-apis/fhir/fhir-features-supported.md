---
title: Unterstützte FHIR-Features im FHIR-Dienst
description: In diesem Artikel wird erläutert, welche Features der FHIR-Spezifikation in Gesundheits-APIs implementiert sind.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: 4c1b9efce8ad29eeb9ebca6285849760f20fbc17
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399899"
---
# <a name="supported-fhir-features"></a>Unterstützte FHIR-Features

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der &reg; FHIR-Dienst in Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) bietet eine vollständig verwaltete Bereitstellung des [Open-Source-FHIR-Servers](https://github.com/microsoft/fhir-server) und ist eine Implementierung des [FHIR-Standards.](https://hl7.org/fhir) In diesem Dokument werden die Hauptfunktionen des FHIR-Diensts aufgeführt.

## <a name="fhir-version"></a>FHIR-Version

Letzte unterstützte Version: `4.0.1`

Derzeit ebenfalls unterstützte vorherige Versionen: `3.0.2`

## <a name="rest-api"></a>REST-API

Im Folgenden finden Sie eine Zusammenfassung der unterstützten RESTful-Funktionen. Weitere Informationen zur Implementierung dieser Funktionen finden Sie unter [FHIR-REST-API-Funktionen.](fhir-rest-api-capabilities.md) 

| API    | Azure-API für FHIR | FHIR-Dienst in Healthcare-APIs | Comment |
|--------|--------------------|---------------------------------|---------|
| Lesen   | Ja                | Ja                             |         |
| vread  | Ja                | Ja                             |         |
| aktualisieren | Ja                | Ja                             |         | 
| Update mit optimistischer Sperre | Ja       | Ja       |
| update (bedingt)           | Ja       | Ja       |
| patch                          | Ja       | Ja       | Unterstützung nur für [JSON Patch.](../../healthcare-apis/fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch) |
| Patch (bedingt)            | Ja       | Ja       |
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

## <a name="extended-operations"></a>Erweiterte Vorgänge

Alle Vorgänge, die unterstützt werden, um die REST-API zu erweitern.

| Suchparametertyp | Azure-API für FHIR | FHIR-Dienst in Healthcare-APIs| Comment |
|------------------------|-----------|-----------|---------|
| [$export](../../healthcare-apis/data-transformation/export-data.md) (gesamtes System) | Ja       | Ja       | Unterstützt System, Gruppe und Patient. |
| [$convert-data](../../healthcare-apis/data-transformation/convert-data.md)          | Ja       | Ja       |         |
| [$validate](validation-against-profiles.md)              | Ja       | Ja       |         |
| [$member-Match](tutorial-member-match.md)          | Ja       | Ja       |         |
| [$patient alles](patient-everything.md)    | Ja       | Ja       |         |
| $purge-Verlauf         | Ja       | Ja       |         |

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Der FHIR-Dienst verwendet [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) für die Zugriffssteuerung. 

## <a name="service-limits"></a>Diensteinschränkungen

* **Paketgröße:** Jedes Paket ist auf 500 Elemente beschränkt.

* **Abonnementlimit:** Standardmäßig ist jedes Abonnement auf maximal 10 FHIR-Dienste beschränkt. Der Grenzwert kann in einem oder vielen Arbeitsbereichen verwendet werden. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über die unterstützten FHIR-Features im FHIR-Dienst erfahren. Informationen zum Bereitstellen des FHIR-Diensts finden Sie unter
 
>[!div class="nextstepaction"]
>[Bereitstellen des FHIR-Diensts](fhir-portal-quickstart.md)
