---
title: API-Versionierung für den DICOM-Dienst – Azure Healthcare-APIs
description: Dieser Leitfaden bietet eine Übersicht über die API-Versionsrichtlinien für den DICOM-Dienst.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: c07a963a9dfeaa8e05d79c43cff18ab934f44f1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339664"
---
# <a name="api-versioning-for-dicom-service"></a>API-Versionierung für den DICOM-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Dieser Referenzleitfaden bietet Ihnen eine Übersicht über die API-Versionsrichtlinien für den DICOM-Dienst. 

Alle Versionen der DICOM-APIs entsprechen immer den DICOMweb™ Standard-Spezifikationen, aber Versionen machen möglicherweise unterschiedliche APIs verfügbar, die auf der [DICOM Conformance Statement](dicom-services-conformance-statement.md)basieren.

## <a name="specifying-version-of-rest-api-in-requests"></a>Angeben der Rest-API-Version in Anforderungen

Die Version der REST-API sollte wie im folgenden Beispiel explizit in der Anforderungs-URL angegeben werden:

`<service_url>/v<version>/studies`

Derzeit werden Routen ohne Version weiterhin unterstützt. Beispielsweise `<service_url>/studies` hat das gleiche Verhalten wie das Angeben der Version als v1.0-Vorabversion. Es wird jedoch dringend empfohlen, die Version in allen Anforderungen über die URL anzugeben.

## <a name="supported-versions"></a>Unterstützte Versionen

Derzeit werden folgende Versionen unterstützt:

* v1.0-prerelease

Die OpenApi-Dokumentation für die unterstützten Versionen finden Sie unter der folgenden URL:
 
`<service_url>/{version}/api.yaml`

## <a name="prerelease-versions"></a>Vorabversionen

Eine API-Version mit der Bezeichnung "Vorabversion" gibt an, dass die Version nicht für die Produktion bereit ist und nur in Testumgebungen verwendet werden sollte. Bei diesen Endpunkten treten möglicherweise ohne vorherige Ankündigung Breaking Changes auf.

## <a name="how-versions-are-incremented"></a>Inkrementieren von Versionen

Die Hauptversion wird derzeit nur erhöht, wenn eine Breaking Change vorliegt, die als nicht abwärtskompatibel betrachtet wird. Alle Nebenversionen werden als 0 (0) impliziert. Alle Versionen haben das Format `Major.0` .

Im Folgenden finden Sie einige Beispiele für breaking changes (Hauptversion wird inkrementiert):

1. Umbenennen oder Entfernen von Endpunkten.
2. Entfernen von Parametern oder Hinzufügen von obligatorischen Parametern.
3. Ändern des Statuscodes.
4. Löschen einer Eigenschaft in einer Antwort oder Ändern eines Antworttyps, aber es ist in Ordnung, der Antwort Eigenschaften hinzuzufügen.
5. Ändern des Typs einer Eigenschaft.
6. Verhalten, wenn sich eine API ändert, wie z. B. Änderungen in der Geschäftslogik, die für foo verwendet werden, aber jetzt bar.

Nicht breaking changes (Version wird nicht erhöht):

1. Hinzufügen von Eigenschaften, die NULL-Werte zu enthalten oder einen Standardwert aufweisen.
2. Hinzufügen von Eigenschaften zu einem Antwortmodell.
3. Ändern der Reihenfolge der Eigenschaften.

## <a name="header-in-response"></a>Header als Antwort

ReportApiVersions ist aktiviert. Das bedeutet, dass die Header api-supported-versions und api-deprecated-versions zurückgegeben werden, wenn dies erforderlich ist.

* api-supported-versions listet auf, welche Versionen für die angeforderte API unterstützt werden. Er wird nur zurückgegeben, wenn ein Endpunkt aufgerufen wird, der mit versehen `ApiVersion("<someVersion>")` ist.

* api-deprecated-versions listet auf, welche Versionen für die angeforderte API veraltet sind. Er wird nur zurückgegeben, wenn ein Endpunkt aufgerufen wird, der mit versehen `ApiVersion("<someVersion>", Deprecated = true)` ist.

Beispiel:

ApiVersion("1.0")

ApiVersion("1.0-prerelease", Deprecated = true)

[![Von der API unterstützte und veraltete Versionen. ](media/api-supported-deprecated-versions.png) ](media/api-supported-deprecated-versions.png#lightbox)

