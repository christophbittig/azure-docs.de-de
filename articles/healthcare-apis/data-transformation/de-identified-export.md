---
title: Exportieren von nicht identifizierten Daten (Vorschau) für den FHIR-Dienst
description: In diesem Artikel wird beschrieben, wie Sie den anonymisierten Export einrichten und verwenden.
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: ranku
ms.openlocfilehash: a7757830dfb75f7ad111913ee4a8ea41926db600
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2021
ms.locfileid: "132867372"
---
# <a name="exporting-de-identified-data-preview"></a>Exportieren anonymisierter Daten (Vorschauversion)

> [!Note] 
> Die Ergebnisse variieren bei Verwendung des anonymisierten Exports je nach Faktoren, z. B. den eingegebenen Daten oder den vom Kunden ausgewählten Funktionen. Microsoft kann die anonymisierten Exportausgaben nicht auswerten oder die Geeignetheit für Anwendungsfälle und Complianceanforderungen des Kunden bestimmen. Es ist nicht garantiert, dass der anonymisierte Export bestimmte gesetzliche, behördliche oder Complianceanforderungen erfüllt.

Der Befehl $export kann auch verwendet werden, um anonymisierte Daten vom FHIR-Server zu exportieren. Er verwendet die Anonymisierungs-Engine der [FHIR-Tools für die Anonymisierung](https://github.com/microsoft/FHIR-Tools-for-Anonymization) und nimmt Details zur Anonymisierungskonfiguration in den Abfrageparametern entgegen. Sie können eine eigene Anonymisierungskonfigurationsdatei erstellen oder die [Beispielkonfigurationsdatei](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#sample-configuration-file) für die HIPAA Safe Harbor-Methode als Ausgangspunkt verwenden. 

## <a name="configuration-file"></a>Konfigurationsdatei

Die Anonymisierungs-Engine enthält eine Beispielkonfigurationsdatei, um die Anforderungen der HIPAA-Tresor Einer-Methode zu erfüllen. Die Konfigurationsdatei ist eine JSON-Datei mit vier Abschnitten: `fhirVersion` `processingErrors` , , , `fhirPathRules` `parameters` . 
* `fhirVersion` gibt die FHIR-Version für die Anonymisierungs-Engine an.
* `processingErrors` gibt an, welche Aktion für die Verarbeitungsfehler, die während der Anonymisierung auftreten können, zu ergreifen ist. Sie können _die_ Ausnahmen _je_ nach Ihren Anforderungen auslösen oder behalten.
* `fhirPathRules` gibt an, welche Anonymisierungsmethode verwendet werden soll. Die Regeln werden in der Reihenfolge ihrer Darstellung in der Konfigurationsdatei ausgeführt.
* `parameters` legt Regeln für das in _fhirPathRules_ angegebene Anonymisierungsverhalten fest.

Hier ist eine Beispielkonfigurationsdatei für R4:

```json
{
  "fhirVersion": "R4",
  "processingError":"raise",
  "fhirPathRules": [
    {"path": "nodesByType('Extension')", "method": "redact"},
    {"path": "Organization.identifier", "method": "keep"},
    {"path": "nodesByType('Address').country", "method": "keep"},
    {"path": "Resource.id", "method": "cryptoHash"},
    {"path": "nodesByType('Reference').reference", "method": "cryptoHash"},
    {"path": "Group.name", "method": "redact"}
  ],
  "parameters": {
    "dateShiftKey": "",
    "cryptoHashKey": "",
    "encryptKey": "",
    "enablePartialAgesForRedact": true
  }
}
```

Ausführlichere Informationen zu jedem dieser vier Abschnitte der Konfigurationsdatei finden Sie [hier.](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#configuration-file-format)
## <a name="using-export-command-for-the-de-identified-data"></a>Verwenden $export Befehls für die nicht identifizierten Daten
 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> Derzeit unterstützt der FHIR-Dienst nur den de-identified Export auf Systemebene ($export).

|Query parameter (Abfrageparameter)            | Beispiel |Optionalität| BESCHREIBUNG|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.json|Erforderlich für den anonymisierten Export |Name der Konfigurationsdatei. Weitere Informationen finden Sie im Format der Konfigurationsdatei [hier](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Diese Datei sollte in einem Container mit dem Namen **anonymization** in dem Azure Storage-Konto gespeichert werden, das als Exportspeicherort konfiguriert ist. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Optional für den anonymisierten Export|Dies ist das ETag der Konfigurationsdatei. Sie können das ETag mit dem Azure Storage-Explorer aus der Blobeigenschaft abrufen.|

> [!IMPORTANT]
> Sowohl der unformatierte als auch der anonymisierte Export wird in dasselbe Azure Storage-Konto geschrieben, das im Rahmen der Exportkonfiguration angegeben wurde. Es wird empfohlen, für verschiedene anonymisierte Konfigurationen unterschiedliche Container zu verwenden und den Benutzerzugriff auf Containerebene zu verwalten.
