---
title: Azure API for FHIR monatlichen Releases
description: Dieser Artikel enthält Details zu den Azure API for FHIR monatlichen Features und Verbesserungen.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: cavoeg
ms.openlocfilehash: 0acbf2efdb65ff5376da9be918eaead2169cd5d6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547910"
---
# <a name="release-notes-azure-api-for-fhir"></a>Versionshinweise: Azure API for FHIR

Azure API for FHIR bietet eine vollständig verwaltete Bereitstellung des Microsoft FHIR-Servers für Azure. Der Server ist eine Implementierung des [FHIR](https://hl7.org/fhir)-Standards. Dieses Dokument enthält Details zu den Features und Verbesserungen, die an Azure API for FHIR vorgenommen wurden.


## <a name="october-2021"></a>Oktober 2021

### <a name="bug-fixes"></a>**Fehlerbehebungen**

| Endlosschleifenfehler | Verwandte Informationen          |
| ----------------- | ----------------------------: |
|Ein Problem wurde behoben, bei dem [bedingtes Löschen](./././../azure-api-for-fhir/fhir-rest-api-capabilities.md#conditional-delete) zu einer Endlosschleife führen konnte. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |

## <a name="september-2021"></a>September 2021 

### <a name="features-and-enhancements"></a>**Features und Verbesserungen**

|Verbesserungen &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |Verwandte Informationen |
|------------------- | --------------- |

|Unterstützung für bedingten Patch hinzugefügt | [Bedingter Patch](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| ----------------------------------- | ------: |
|Bedingter Patch |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|Bedingtes Patchüberwachungsereignis hinzugefügt. |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|JSON-Patch in Paketen zulassen | [JSON-Patch in Paketen](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| ----------------------------------- | ------: |
|Ermöglicht das Durchsuchen von Verlaufsbündeln mit Patchanforderungen. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|Json-Patch in Paketen mit binären Ressourcen aktiviert. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|Neue Untertypen von Überwachungsereignissen |Verwandte Informationen |
| ----------------------------------- | ---------------: |
|Neue Audit [OperationName-Untertypen](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)wurden hinzugefügt.| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[Verbesserungen bei der Neuindizierung](how-to-run-a-reindex.md) |Verwandte Informationen |
| ----------------------------------- | ---------------: |
|Es wurden [Grenzen für Parameter zum Erneutindizieren](how-to-run-a-reindex.md) hinzugefügt. |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|Fehlermeldung für Parametergrenzen neu indizieren. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|Letzte Überprüfung der Neuindizierungsanzahl wurde hinzugefügt. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**Fehlerbehebungen**

|Behobene Patchfehler |Verwandte Informationen |
| :----------------------------------- | ---------------: |
|Breiterer Catch für Ausnahmen beim Anwenden von Patches. |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|Behebt den Verlauf mit PATCH in STU3.| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|Fehler bei der benutzerdefinierten Suche |Verwandte Informationen |
| ----------------------------------- | ---------------: |
|Behebt den Löschfehler mit benutzerdefinierten Suchparametern. | [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|Wiederholungslogik beim Löschen des Search-Parameters hinzugefügt. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|Legen Sie die maximale Elementanzahl in Suchoptionen in SearchParameterDefinitionManager fest. | [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|Stellt eine bessere Ausnahme bereit, wenn ein ungültiger Ausdruck im Suchparameter vorhanden ist. |[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|Behobener Wiederholungsfehler 503 |Verwandte Informationen |
| ----------------------------------- | ---------------: |
|Wiederholen Sie den Fehler 503 von Cosmos Datenbank. |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|Korrigiert die Verarbeitung von 429s aus StoreProcedures. |[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub Probleme geschlossen |Verwandte Informationen |
| ----------------------------------- | ---------------: |
|Der Benutzerdefinierte Suchparameter für das medizinische CarePlan-Gerät kann nicht erstellt werden. |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|Unklare Fehlermeldung für die bedingte Erstellung ohne ID. | [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>IoT-Connector für FHIR (Vorschau)

|Fehlerbehebungen &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|Verwandte Informationen |
| ----------------------------------- | ---------------: |
|Fehlerhafte Verknüpfung wurde behoben.| Link zur Azure-Dokumentation zum IoT-Connector im Azure API for FHIR-Portal aktualisiert. |

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den Features und Fehlerbehebungen in Azure Healthcare-APIs (FHIR-Dienst, DICOM-Dienst und IoT-Connector) finden Sie unter

>[!div class="nextstepaction"]
>[Versionshinweise: Azure Healthcare-APIs](../release-notes.md)
