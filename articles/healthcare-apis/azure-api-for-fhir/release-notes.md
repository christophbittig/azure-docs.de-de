---
title: Azure API for FHIR monatlichen Releases
description: Dieser Artikel enthält Details zu den Azure API for FHIR Features und Erweiterungen.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/01/2021
ms.author: cavoeg
ms.openlocfilehash: 201de8f7bbae01c708757ee2290f6005979410c4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477523"
---
# <a name="release-notes-azure-api-for-fhir"></a>Versionshinweise: Azure API for FHIR

Azure API for FHIR bietet eine vollständig verwaltete Bereitstellung des Microsoft FHIR-Servers für Azure. Der Server ist eine Implementierung des [FHIR](https://hl7.org/fhir)-Standards. Dieses Dokument enthält Details zu den Features und Verbesserungen, die für Azure API for FHIR.

## <a name="september-2021"></a>September 2021 

### <a name="features-and-enhancements"></a>**Features und Verbesserungen**

|Erweiterungen | BESCHREIBUNG |
|:------------------- | -----------:|

|Unterstützung für bedingten Patch hinzugefügt | [Bedingter Patch](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| :----------------------------------- | ------: |
|Bedingter Patch |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Überwachungsereignis für bedingten Patch hinzugefügt |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|JSON-Patch in Paketen zulassen | [JSON-Patch in Paketen](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| :----------------------------------- | ------: |
|Zulassen von Suchverlaufspaketen mit Patchanforderungen |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Aktivieren von JSON-Patch in Paketen mit binären Ressourcen |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|Neue Überwachungsereignisuntertypen| BESCHREIBUNG|
| :----------------------------------- | ------: |
|Neue Untertypen von [Audit OperationName hinzugefügt](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[Verbesserungen bei der Neuindizierung:](how-to-run-a-reindex.md) | BESCHREIBUNG|
| :----------------------------------- | ------: |
|Hinzugefügte [Grenzen für Neuindizierungsparameter](how-to-run-a-reindex.md)|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Aktualisieren der Fehlermeldung für die Neuindizierung von Parametergrenzen|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Fügt eine abschließende Überprüfung der Anzahl von Neuindizierungen hinzu. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**Fehlerbehebungen**

|Behobene Patchfehler| BESCHREIBUNG|
| :----------------------------------- | ------: |
|Größerer Catch für Ausnahmen beim Anwenden des Patches |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|Korrekturen des Verlaufs mit PATCH in HOTFIX3| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|Fehler bei der benutzerdefinierten Suche| BESCHREIBUNG|
| :----------------------------------- | ------: |
|Behebt den Löschfehler mit Parametern der benutzerdefinierten Suche.| [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|Wiederholungslogik beim Löschen des Suchparameters hinzugefügt| [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Festlegen der maximalen Elementanzahl in den Suchoptionen in SearchParameterDefinitionManager| [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|Bessere Ausnahme bei einem fehlerhaften Ausdruck im Suchparameter|[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|Wiederholungsfehler 503 behoben| BESCHREIBUNG|
| :----------------------------------- | ------: |
|Wiederholungsfehler 503 von Cosmos DB |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|Behebt die Verarbeitung von 429-429-Daten aus StoreProcedures|[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub geschlossenen Probleme| BESCHREIBUNG|
| :----------------------------------- | ------: |
|Benutzerdefinierter Suchparameter für das Medizinische CarePlan-Gerät kann nicht erstellt werden |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|Unklare Fehlermeldung für die bedingte Erstellung ohne ID| [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>IoT-Connector für FHIR (Vorschau)

|Behebung von Programmfehlern| BESCHREIBUNG|
| :-----------------------------------| ------: |
|Link zur Azure-Dokumentation für den IoT-Connector korrigiert|Fehlerhafter Link im Azure API for FHIR Portal |

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den Features und Fehlerbehebungen in Azure Healthcare-APIs (FHIR-Dienst, DICOM-Dienst und IoT-Connector) finden Sie unter

>[!div class="nextstepaction"]
>[Versionshinweise: Azure Healthcare-APIs](../release-notes.md)
