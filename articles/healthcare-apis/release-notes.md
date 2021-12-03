---
title: Monatliche Releases von Azure Healthcare-APIs
description: Dieser Artikel enthält Details zu den monatlichen Features und Verbesserungen der Azure Healthcare-APIs.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/22/2021
ms.author: cavoeg
ms.openlocfilehash: 304fdc3ceea4c26e861ba54e2dd90f98fd7bc225
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936522"
---
# <a name="release-notes-azure-healthcare-apis"></a>Versionshinweise: Azure Healthcare-APIs

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind. 

Azure Healthcare-APIs sind eine Reihe verwalteter API-Dienste, die auf offenen Standards und Frameworks für die Gesundheitsbranche basieren. Sie ermöglichen es Ihnen, skalierbare und sichere Lösungen für das Gesundheitswesen zu erstellen, indem Sie PHI-Datasets (Protected Health Information) zusammenbringen und end-to-end mit Tools für maschinelles Lernen, Analysen und KI verbinden. Dieses Dokument enthält Details zu den Features und Verbesserungen für Azure Healthcare-APIs, einschließlich der verschiedenen Diensttypen (FHIR-Dienst, DICOM-Dienst und IoT-Connector), die nahtlos miteinander funktionieren.

## <a name="october-2021"></a>Oktober 2021

### <a name="azure-healthcare-apis-feature-enhancements"></a>Featureverbesserungen für Azure Healthcare-APIs

| Verbesserungen &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Verwandte Informationen           |
|------------- | -----------------------------: |
|Tool zum Testen des Datengenerators |Wir haben das Repository für Die Gesundheits-APIs GitHub um ein [Testdaten-Generator-Tool](https://github.com/microsoft/healthcare-apis-samples/blob/main/docs/HowToRunPerformanceTest.md) mit Synthea-Daten aktualisiert. Dieses Tool ist eine Verbesserung [](https://github.com/ShadowPic/PublicTestProjects)der öffentlichen Open Source-Testprojekte, die auf Apache JMeter basieren und für Leistungstests in Azure AKS bereitgestellt werden können. |

### <a name="fhir-service"></a>FHIR-Dienst

#### <a name="feature-enhancements"></a>**Featureverbesserungen**

|Erweiterungen | Verwandte Informationen |
|------------------------ | -------------------------------: |
|Unterstützung für [_sort](././../healthcare-apis/fhir/overview-of-search.md#search-result-parameters) für Zeichenfolgen und dateTime hinzugefügt. |[#2169](https://github.com/microsoft/fhir-server/pull/2169)  |

#### <a name="bug-fixes"></a>**Fehlerbehebungen**

|Behebung von Programmfehlern | Verwandte Informationen |
|------------------------ | -------------------------------: |
|Ein Problem wurde behoben, [bei dem das](././../healthcare-apis/fhir/fhir-rest-api-capabilities.md#conditional-delete) bedingte Löschen zu einer Endlosschleife führen konnte. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |
|500-Fehler wurde behoben, der möglicherweise durch einen falsch formatierten Transaktionskörper in einem Post-Paket verursacht wurde. Wir haben eine Überprüfung hinzugefügt, ob die URL in den Transaktionspaketanforderungen [aufgefüllt](././..//healthcare-apis/fhir/fhir-features-supported.md#rest-api) wird. | [#2255](https://github.com/microsoft/fhir-server/pull/2255) |

### <a name="dicom-service"></a>**DICOM-Dienst**

|Hinzugefügte Unterstützung | Verwandte Informationen |
|------------------------ | -------------------------------: |
|Regions | "Brasilien, Süden" und "Kanada, Mitte". Weitere Informationen zu Azure-Regionen und Verfügbarkeitszonen finden Sie unter [Azure-Dienste, die Verfügbarkeitszonen unterstützen.](./../availability-zones/az-region.md) |
|Tags für erweiterte Abfragen |DateTime (DT) und Time (TM) Value Representation (VR)-Typen |

|Behebung von Programmfehlern | Verwandte Informationen |
|------------------------ | -------------------------------: |
|Korrektur für Arbeitsbereichsnamen implementiert. |Der DICOM-Dienst kann mit Arbeitsbereichen arbeiten, deren Namen mit einem Buchstaben beginnen. |

## <a name="september-2021"></a>September 2021

### <a name="fhir-service"></a>FHIR-Dienst

#### <a name="feature-enhancements"></a>**Featureverbesserungen**

|Erweiterungen | Verwandte Informationen |
|:------------------- | -------------------------------: |

|Unterstützung für bedingten Patch hinzugefügt | [Bedingter Patch](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -------------------------------:|
|Bedingter Patch | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Bedingtes Patchüberwachungsereignis wurde hinzugefügt. | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|JSON-Patch in Paketen zulassen | [JSON-Patch in Paketen](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -------------------------------:|
|Ermöglicht Suchverlaufsbündel mit Patchanforderungen. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|JSON-Patch in Paketen mit binären Ressourcen aktiviert. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|Neue Untertypen des [Überwachungsereignis "OperationName" hinzugefügt](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| Ausführen eines Neuindizierungsauftrags | [Verbesserungen bei der Neuindizierung](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -------------------------------:|
|Es wurden [Grenzen für Neuindizierungsparameter](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations) hinzugefügt. |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Die Fehlermeldung für die Neuindizierung von Parametergrenzen wurde aktualisiert. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Abschließende Neuindizierungsanzahlprüfung hinzugefügt. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**Fehlerbehebungen**

|Behobene Patchfehler | Verwandte Informationen |
|:------------------- | --------------------------------: |

| Größerer Catch für Ausnahmen beim Anwenden des Patches | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------: |
|Korrigieren des Verlaufs mit PATCH in HP3 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|Fehler bei der benutzerdefinierten Suche | Verwandte Informationen |
|:------------------- | -------------------------------: |
|Behebt den Löschfehler mit Parametern der benutzerdefinierten Suche |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|Wiederholungslogik beim Löschen des Search-Parameters hinzugefügt | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Festlegen der maximalen Elementanzahl in den Suchoptionen in SearchParameterDefinitionManager |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|Bessere Ausnahme bei einem fehlerhaften Ausdruck in einem Suchparameter |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|Aufgelöst SQL Batch-Neuindizierung, wenn eine Ressource ausfällt | Verwandte Informationen |
|:------------------- | -------------------------------: |
|Aktualisiert SQL Wiederholungslogik für die Batch-Neuindizierung |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHub geschlossenen Probleme | Verwandte Informationen |
|:------------------- | -------------------------------: |
|Unklare Fehlermeldung für die bedingte Erstellung ohne ID |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**DICOM-Dienst**

|Behebung von Programmfehlern | Verwandte Informationen |
|:------------------- | -------------------------------: |

|Korrektur zur Behebung von Problemen mit der QIDO-Pagingbestellung implementiert |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -------------------------------: |

### <a name="iot-connector"></a>**IoT-Connector**

|Behebung von Programmfehlern | Verwandte Informationen |
|:------------------- | -------------------------------: |
| Der IoT-Connector normalisierte Verbesserungen mit Berechnungen, um die Standardisierung von Integritätsdaten zu unterstützen und zu verbessern. | Siehe: [Verwenden von Gerätezuordnungen](./../healthcare-apis/iot/how-to-use-device-mappings.md) und [berechneten Funktionen](./../healthcare-apis/iot/how-to-use-calculated-functions-mappings.md)  |

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den Features und Fehlerbehebungen in Azure API for FHIR finden Sie unter

>[!div class="nextstepaction"]
>[Versionshinweise: Azure API for FHIR](./azure-api-for-fhir/release-notes.md)
