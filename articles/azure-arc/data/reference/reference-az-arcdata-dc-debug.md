---
title: Referenz zu „az arcdata dc debug“
titleSuffix: Azure Arc-enabled data services
description: Dies ist der Referenzartikel zu „az arcdata dc debug“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e230d788a06865a8868f90fb1195c9fb701c2750
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847791"
---
# <a name="az-arcdata-dc-debug"></a>az arcdata dc debug
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata dc debug copy-logs](#az-arcdata-dc-debug-copy-logs) | Kopieren von Protokollen.
[az arcdata dc debug dump](#az-arcdata-dc-debug-dump) | Auslösen eines Speicherabbilds
## <a name="az-arcdata-dc-debug-copy-logs"></a>az arcdata dc debug copy-logs
Hiermit werden die Debugprotokolle aus dem Datencontroller kopiert – auf Ihrem System ist eine Kubernetes-Konfiguration erforderlich.
```bash
az arcdata dc debug copy-logs 
```
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: json, jsonc, table, tsv.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org/](http://jmespath.org).
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. Verwenden Sie `--debug`, um vollständige Debugprotokolle zu erhalten.
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
Lösen Sie ein Speicherabbild aus, und kopieren Sie es aus dem Container. Hierfür ist eine Kubernetes-Konfiguration auf Ihrem System erforderlich.
```bash
az arcdata dc debug dump 
```
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: json, jsonc, table, tsv.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org/](http://jmespath.org).
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. Verwenden Sie `--debug`, um vollständige Debugprotokolle zu erhalten.
