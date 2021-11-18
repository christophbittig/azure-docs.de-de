---
title: Monatliche Releases von Azure Healthcare-APIs
description: Dieser Artikel enthält Details zu den monatlichen Features und Verbesserungen der Azure Healthcare-APIs.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/01/2021
ms.author: cavoeg
ms.openlocfilehash: 40afc404e3121ba4b35da895d804c475f9b28d95
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719354"
---
# <a name="release-notes-azure-healthcare-apis"></a>Versionshinweise: Azure Healthcare-APIs

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind. 

Azure Healthcare-APIs sind eine Reihe verwalteter API-Dienste, die auf offenen Standards und Frameworks für die Gesundheitsbranche basieren. Sie ermöglichen es Ihnen, skalierbare und sichere Lösungen für das Gesundheitswesen zu erstellen, indem Sie datasets für geschützte Gesundheitsinformationen (Protected Health Information, PHI) zusammenführen und sie end-to-end mit Tools für Machine Learning, Analysen und KI verbinden. Dieses Dokument enthält Details zu den Features und Verbesserungen von Azure Healthcare-APIs, einschließlich der verschiedenen Diensttypen (FHIR-Dienst, DICOM-Dienst und IoT-Connector), die nahtlos miteinander funktionieren.

## <a name="september-2021"></a>September 2021

### <a name="fhir-service"></a>FHIR-Dienst

#### <a name="feature-enhancements"></a>**Featureverbesserungen**

|Erweiterungen | BESCHREIBUNG |
|:------------------- | -----------:|

|Unterstützung für bedingten Patch hinzugefügt | [Bedingter Patch](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -----------:|
|Bedingter Patch | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Hinzufügen eines Bedingten Patchüberwachungsereignisses | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|JSON-Patch in Paketen zulassen | [JSON-Patch in Paketen](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -----------:|
|Ermöglicht das Durchsuchen von Verlaufspaketen mit Patchanforderungen. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Aktivieren des JSON-Patches in Paketen mit binären Ressourcen |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|Neue [OperationName-Untertypen](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details) des Überwachungsereignisses hinzugefügt| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| Ausführen eines Neuindizierungsauftrags | [Verbesserungen bei der Neuindizierung](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -----------:|
|[Grenzen für Neuindizierungsparameter](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations) hinzugefügt|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Fehlermeldung für parametergrenzen neu indizieren|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Fügt die abschließende Neuindizierungsanzahl-Überprüfung hinzu. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**Fehlerbehebungen**

|Behobene Patchfehler | BESCHREIBUNG |
|:------------------- | -----------:|

| Breiterer Catch für Ausnahmen während der Anwendung des Patches | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------:|
|Korrigieren des Verlaufs mit PATCH in STU3 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|Fehler bei der benutzerdefinierten Suche |BESCHREIBUNG |
|:------------------- | -----------:|
|Behebt den Löschfehler mit benutzerdefinierten Suchparametern. |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|Wiederholungslogik beim Löschen des Search-Parameters hinzugefügt | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Festlegen der maximalen Elementanzahl in Suchoptionen in SearchParameterDefinitionManager |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|Bessere Ausnahme bei einem ungültigen Ausdruck in einem Suchparameter |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|Behoben SQL Batch-Neuindizierung, wenn eine Ressource ausfällt |BESCHREIBUNG |
|:------------------- | -----------:|
|Aktualisiert die Wiederholungslogik SQL Batch-Neuindizierung. |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHub Behobene Probleme |BESCHREIBUNG |
|:------------------- | -----------:|
|Unklare Fehlermeldung für bedingte Erstellung ohne ID |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**DICOM-Dienst**

|Behebung von Programmfehlern | BESCHREIBUNG |
|:------------------- | -----------:|

|Korrektur zur Behebung von Problemen mit der QIDO-Paginierungsreihenfolge wurde implementiert. |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -----------:|

### <a name="iot-connector"></a>**IoT-Connector**

|Behebung von Programmfehlern | BESCHREIBUNG |
|:------------------- | -----------:|
| Der IoT-Connector normalisierte Verbesserungen mit Berechnungen, um die Standardisierung von Integritätsdaten zu unterstützen und zu verbessern. | Siehe: [Verwenden von Gerätezuordnungen](./../healthcare-apis/iot/how-to-use-device-mappings.md) und [berechneten Funktionen](./../healthcare-apis/iot/how-to-use-calculated-functions-mappings.md)  |

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den Features und Fehlerbehebungen in Azure API for FHIR finden Sie unter

>[!div class="nextstepaction"]
>[Versionshinweise: Azure API for FHIR](./azure-api-for-fhir/release-notes.md)
