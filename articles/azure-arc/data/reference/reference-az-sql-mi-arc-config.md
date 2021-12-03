---
title: Referenz zu „az sql mi-arc config“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql mi-arc config“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 2dbe786f8a33528d028b4dc5f5476fa6f38a62b4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845679"
---
# <a name="az-sql-mi-arc-config"></a>az sql mi-arc config
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az sql mi-arc config init](#az-sql-mi-arc-config-init) | Hiermit werden die CRD-Datei und die Spezifikationsdatei für eine verwaltete SQL-Instanz initialisiert.
[az sql mi-arc config add](#az-sql-mi-arc-config-add) | Fügt einen Wert in einem JSON-Pfad in einer Konfigurationsdatei hinzu.
[az sql mi-arc config remove](#az-sql-mi-arc-config-remove) | Entfernt einen Wert in einem JSON-Pfad in einer Konfigurationsdatei.
[az sql mi-arc config replace](#az-sql-mi-arc-config-replace) | Ersetzt einen Wert in einem JSON-Pfad in einer Konfigurationsdatei.
[az sql mi-arc config patch](#az-sql-mi-arc-config-patch) | Patcht eine Konfigurationsdatei auf Grundlage einer JSON-Patchdatei.
## <a name="az-sql-mi-arc-config-init"></a>az sql mi-arc config init
Hiermit werden die CRD-Datei und die Spezifikationsdatei für eine verwaltete SQL-Instanz initialisiert.
```bash
az sql mi-arc config init 
```
### <a name="examples"></a>Beispiele
Hiermit werden die CRD-Datei und die Spezifikationsdatei für eine verwaltete SQL-Instanz initialisiert.
```bash
az sql mi-arc config init --path ./template
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
## <a name="az-sql-mi-arc-config-add"></a>az sql mi-arc config add
Fügt den Wert im JSON-Pfad in der Konfigurationsdatei hinzu.  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az sql mi-arc config add 
```
### <a name="examples"></a>Beispiele
Bsp. 1: Hinzufügen von Speicher
```bash
az sql mi-arc config add --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-sql-mi-arc-config-remove"></a>az sql mi-arc config remove
Entfernt den Wert im JSON-Pfad in der Konfigurationsdatei. Alle nachfolgenden Beispiele sind in Bash angegeben. Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen. Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az sql mi-arc config remove 
```
### <a name="examples"></a>Beispiele
Bsp. 1: Entfernen von Speicher
```bash
az sql mi-arc config remove --path custom/spec.json --json-path ".spec.storage"
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
## <a name="az-sql-mi-arc-config-replace"></a>az sql mi-arc config replace
Ersetzt den Wert im JSON-Pfad in der Konfigurationsdatei.  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az sql mi-arc config replace 
```
### <a name="examples"></a>Beispiele
Bsp. 1: Ersetzen des Ports eines einzelnen Endpunkts
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
Bsp. 2: Ersetzen des Speichers
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-sql-mi-arc-config-patch"></a>az sql mi-arc config patch
Patcht die Konfigurationsdatei entsprechend der angegebenen Patchdatei. Informationen zum besseren Verständnis der Zusammensetzung der Pfade finden Sie unter http://jsonpatch.com/. Der Vorgang „Ersetzen“ kann Bedingungen im Pfad aufgrund der JSONPATH-Bibliothek verwenden (https://jsonpath.com/ ). Alle JSON-Patchdateien müssen mit einem `patch`-Schlüssel beginnen, der über ein Array von Patches mit dem entsprechenden Vorgang (Hinzufügen, Ersetzen, Entfernen), Pfad und Wert verfügt. Der Vorgang `remove` (Entfernen) erfordert keinen Wert, sondern nur einen Pfad. Beispiele hierzu finden Sie weiter unten.
```bash
az sql mi-arc config patch 
```
### <a name="examples"></a>Beispiele
Bsp. 1: Ersetzen des Ports eines einzelnen Endpunkts mit Patchdatei
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
Bsp. 2: Ersetzen des Speichers mit Patchdatei
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":".spec.storage","value":{"accessMode":"ReadWriteMany","className":"managed-premium","size":"10Gi"}}]}
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
