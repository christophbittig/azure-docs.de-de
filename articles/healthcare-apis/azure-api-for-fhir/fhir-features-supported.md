---
title: Unterstützte FHIR-Features in Azure – Azure API for FHIR
description: In diesem Artikel wird erläutert, welche Features der FHIR-Spezifikation in Azure API for FHIR implementiert sind.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: b6c170a7b2f46adeb4b287424601455bb078aba7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397298"
---
# <a name="features"></a>Features

Azure API for FHIR bietet eine vollständig verwaltete Bereitstellung des Microsoft FHIR-Servers für Azure. Der Server ist eine Implementierung des [FHIR](https://hl7.org/fhir)-Standards. In diesem Dokument sind die wichtigsten Features des FHIR-Servers aufgeführt.

## <a name="fhir-version"></a>FHIR-Version

Letzte unterstützte Version: `4.0.1`

Derzeit ebenfalls unterstützte vorherige Versionen: `3.0.2`

## <a name="rest-api"></a>REST-API

Im Folgenden finden Sie eine Zusammenfassung der unterstützten RESTful-Funktionen. Weitere Informationen zur Implementierung dieser Funktionen finden Sie unter [FHIR-REST-API-Funktionen.](fhir-rest-api-capabilities.md)

| API    | Azure-API für FHIR | FHIR-Dienst in ApIs für das Gesundheitswesen | Comment |
|--------|--------------------|---------------------------------|---------|
| Lesen   | Ja                | Ja                             |         |
| vread  | Ja                | Ja                             |         |
| aktualisieren | Ja                | Ja                             |         | 
| Update mit optimistischer Sperre | Ja       | Ja       |
| update (bedingt)           | Ja       | Ja       |
| patch                          | Ja       | Ja       | Unterstützung nur [für JSON Patch.](https://www.hl7.org/fhir/http.html#patch) Wir haben eine Problemumgehung für die Verwendung von JSON Patch in einem Paket in diesem [PR hinzugefügt.](https://github.com/microsoft/fhir-server/pull/2143)|
| patch (bedingt)            | Ja       | Ja       |
| history                        | Ja       | Ja       |
| create                         | Ja       | Ja       | Unterstützung für POST/PUT |
| create (bedingt)           | Ja       | Ja       | Issue [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | Weitere Informationen [finden Sie unter Übersicht über die FHIR-Suche.](overview-of-search.md) |
| Verkettete Suche                 | Ja       | Ja       | Siehe Hinweis unten. |
| Umgekehrte verkettete Suche         | Ja       | Ja       | Siehe Hinweis unten. |
| Batch                          | Ja       | Ja       |
| transaction                    | Nein        | Ja       |
| paging                         | Teilweise   | Teilweise   | `self` und `next` werden unterstützt                     |
| intermediaries                 | Nein        | Nein        |

> [!Note] 
> In der Azure API for FHIR und dem open-source-FHIR-Server, der von Cosmos unterstützt wird, ist die verkettete suche und die umgekehrte verkettete Suche eine MVP-Implementierung. Um eine verkettete Suche für Cosmos-Datenbank zu ermöglichen, durchsucht die Implementierung den Suchausdruck und gibt Unterabfragen aus, um die übereinstimmenden Ressourcen zu beheben. Dies erfolgt für jede Ebene des Ausdrucks. Wenn eine Abfrage mehr als 1.000 Ergebnisse zurückgibt, wird ein Fehler ausgelöst.

## <a name="extended-operations"></a>Erweiterte Vorgänge

Alle unterstützten Vorgänge, die die REST-API erweitern.

| Suchparametertyp | Azure-API für FHIR | FHIR-Dienst in ApIs für das Gesundheitswesen| Comment |
|------------------------|-----------|-----------|---------|
| [$export](../../healthcare-apis/data-transformation/export-data.md) (gesamtes System) | Ja       | Ja       | Unterstützt System, Gruppe und Patient.   |
| [$convert-data](convert-data.md)          | Ja       | Ja       |         |
| [$validate](validation-against-profiles.md)              | Ja       | Ja       |         |
| [$member-match](tutorial-member-match.md)          | Ja       | Ja       |         |
| [$patient alles](patient-everything.md)    | Ja       | Ja       |         |
| $purge-History         | Ja       | Ja       |         |

## <a name="persistence"></a>Persistenz

Der Microsoft FHIR-Server verfügt über ein austauschbares Persistenzmodul (siehe [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Derzeit umfasst der Open-Source-Code für FHIR-Server eine Implementierung für [Azure Cosmos DB](../../cosmos-db/index-overview.md) und [SQL-Datenbank](https://azure.microsoft.com/services/sql-database/).

Cosmos DB ist eine global verteilte Datenbank, die mehrere Modelle (SQL-API, MongoDB-API, usw.) unterstützt. Es unterstützt verschiedene [Konsistenzebenen](../../cosmos-db/consistency-levels.md). Die standardmäßige Bereitstellungsvorlage konfiguriert den FHIR-Server mit `Strong`-Konsistenz, aber die Konsistenzrichtlinie kann auf Anforderungsbasis unter Verwendung des Anforderungsheaders `x-ms-consistency-level` geändert (allgemein gelockert) werden.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Der FHIR-Server verwendet [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) für die Zugriffssteuerung. Insbesondere wird die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) erzwungen, wenn der Konfigurationsparameter `FhirServer:Security:Enabled` auf `true` festgelegt ist, und bei allen Anforderungen (außer `/metadata`) an den FHIR-Server muss der Anforderungsheader `Authorization` auf `Bearer <TOKEN>` festgelegt sein. Das Token muss eine oder mehrere Rollen gemäß der Definition im `roles`-Anspruch enthalten. Eine Anforderung ist zulässig, wenn der Token eine Rolle enthält, die die angegebene Aktion für die angegebene Ressource gestattet.

Derzeit werden die zulässigen Aktionen für eine bestimmte Rolle *global* auf die API angewendet.

## <a name="service-limits"></a>Diensteinschränkungen

* [**Anforderungseinheiten (Request Units, RUs):**](../../cosmos-db/concepts-limits.md) Sie können bis zu 10.000 RUs im Portal für Azure API for FHIR konfigurieren. Sie benötigen mindestens 400 RUs oder 40 RUs/GB, je nach Größe. Wenn Sie mehr als 10.000 RUs benötigen, können Sie ein Supportticket einlesen, um die RUs zu erhöhen. Maximal sind 1.000.000 RUs verfügbar.

* **Paketgröße:** Jedes Paket ist auf 500 Elemente beschränkt.

* **Datengröße:** Daten/Dokumente müssen jeweils etwas kleiner als 2 MB sein.

* **Abonnementlimit:** Standardmäßig ist jedes Abonnement auf maximal 10 FHIR-Serverinstanzen beschränkt. Wenn Sie mehr Instanzen pro Abonnement benötigen, öffnen Sie ein Supportticket, und geben Sie Details zu Ihren Anforderungen an.

* **Gleichzeitige Verbindungen und Instanzen:** Standardmäßig verfügen Sie über 15 gleichzeitige Verbindungen auf zwei Instanzen im Cluster (für insgesamt 30 gleichzeitige Anforderungen). Wenn Sie mehr gleichzeitige Anforderungen benötigen, öffnen Sie ein Supportticket, und geben Sie Details zu Ihren Anforderungen an.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zu den unterstützten FHIR-Features in Azure API for FHIR erhalten. Informationen zum Bereitstellen von Azure API for FHIR finden Sie unter
 
>[!div class="nextstepaction"]
>[Bereitstellen von Azure API for FHIR](fhir-paas-portal-quickstart.md)
