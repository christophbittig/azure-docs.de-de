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
ms.openlocfilehash: ba9bb7fcdef85ae93fdce593cdf416ca3e7387c9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477531"
---
# <a name="release-notes-azure-healthcare-apis"></a>Versionshinweise: Azure Healthcare-APIs

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind. 

Azure Healthcare-APIs sind eine Reihe verwalteter API-Dienste, die auf offenen Standards und Frameworks für das Gesundheitswesen basieren. Sie ermöglichen es Ihnen, skalierbare und sichere Lösungen für das Gesundheitswesen zu erstellen, indem Sie PHI-Datasets (Protected Health Information) zusammenbringen und end-to-end mit Tools für maschinelles Lernen, Analysen und KI verbinden. Dieses Dokument enthält Details zu den Features und Verbesserungen für Azure Healthcare-APIs, einschließlich der verschiedenen Diensttypen (FHIR-Dienst, DICOM-Dienst und IoT-Connector), die nahtlos miteinander funktionieren.

## <a name="september-2021"></a>September 2021

### <a name="fhir-service"></a>FHIR-Dienst

#### <a name="feature-enhancements"></a>**Featureverbesserungen**

|Erweiterungen | BESCHREIBUNG |
|:------------------- | -----------:|

|Unterstützung für bedingten Patch hinzugefügt | [Bedingter Patch](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -----------:|
|Bedingter Patch | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Hinzufügen eines Überwachungsereignis für bedingten Patch | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|JSON-Patch in Paketen zulassen | [JSON-Patch in Paketen](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -----------:|
|Ermöglicht Suchverlaufsbündel mit Patchanforderungen. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Aktivieren von JSON-Patch in Paketen mit binären Ressourcen |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|Neue Untertypen des [Überwachungsereignis "OperationName" hinzugefügt](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| Ausführen eines Auftrags zum erneuten Indizieren | [Verbesserungen bei der Neuindizierung](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -----------:|
|Hinzugefügte [Grenzen für Neuindizierungsparameter](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations)|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Aktualisieren der Fehlermeldung für die Neuindizierung von Parametergrenzen|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Fügt eine abschließende Überprüfung der Anzahl von Neuindizierungen hinzu. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**Fehlerbehebungen**

|Behobene Patchfehler | BESCHREIBUNG |
|:------------------- | -----------:|

| Größerer Catch für Ausnahmen beim Anwenden des Patches | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------:|
|Korrigieren des Verlaufs mit PATCH in FIX3 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|Fehler bei der benutzerdefinierten Suche |BESCHREIBUNG |
|:------------------- | -----------:|
|Behebt den Löschfehler mit Parametern der benutzerdefinierten Suche. |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|Wiederholungslogik beim Löschen des Search-Parameters hinzugefügt | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Festlegen der maximalen Elementanzahl in den Suchoptionen in SearchParameterDefinitionManager |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|Bessere Ausnahme bei einem fehlerhaften Ausdruck in einem Suchparameter |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|Aufgelöst SQL Batch-Neuindizierung, wenn eine Ressource ausfällt |BESCHREIBUNG |
|:------------------- | -----------:|
|Aktualisiert SQL Wiederholungslogik für die Neuindizierung von Batches. |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHub geschlossenen Probleme |BESCHREIBUNG |
|:------------------- | -----------:|
|Unklare Fehlermeldung für die bedingte Erstellung ohne ID |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**DICOM-Dienst**

|Behebung von Programmfehlern | BESCHREIBUNG |
|:------------------- | -----------:|

|Korrektur zur Behebung von Problemen mit der QIDO-Paging-Sortierung implementiert |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -----------:|

### <a name="iot-connector"></a>**IoT-Connector**

|Behebung von Programmfehlern | BESCHREIBUNG |
|:------------------- | -----------:|
| Der IoT-Connector normalisierte Verbesserungen mit Berechnungen, um die Standardisierung von Integritätsdaten zu unterstützen und zu verbessern. | Siehe: [Verwenden von Gerätezuordnungen und](./../healthcare-apis/iot/how-to-use-device-mapping-iot.md) [berechneten Funktionen](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md)  |

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den Features und Fehlerbehebungen in Azure API for FHIR Sie unter

>[!div class="nextstepaction"]
>[Versionshinweise: Azure API for FHIR](./azure-api-for-fhir/release-notes.md)


