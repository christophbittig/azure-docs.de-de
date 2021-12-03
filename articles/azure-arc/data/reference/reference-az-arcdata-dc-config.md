---
title: az arcdata dc config reference
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu az arcdata dc-Konfigurationsbefehlen
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 72df8db00f1ab0366cc4daf8af8e5613645fc813
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852520"
---
# <a name="az-arcdata-dc-config"></a>az arcdata dc config
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata dc config init](#az-arcdata-dc-config-init) | Hiermit wird ein Konfigurationsprofil für einen Datencontroller initialisiert, das für `az arcdata dc create` verwendet werden kann.
[az arcdata dc config list](#az-arcdata-dc-config-list) | Listet die verfügbaren Optionen für das Konfigurationsprofil auf
[az arcdata dc config add](#az-arcdata-dc-config-add) | Fügt einen Wert in einem JSON-Pfad in einer Konfigurationsdatei hinzu.
[az arcdata dc config remove](#az-arcdata-dc-config-remove) | Entfernt einen Wert in einem JSON-Pfad in einer Konfigurationsdatei.
[az arcdata dc config replace](#az-arcdata-dc-config-replace) | Ersetzt einen Wert in einem JSON-Pfad in einer Konfigurationsdatei.
[az arcdata dc config patch](#az-arcdata-dc-config-patch) | Patcht eine Konfigurationsdatei auf Grundlage einer JSON-Patchdatei
## <a name="az-arcdata-dc-config-init"></a>az arcdata dc config init
Hiermit wird ein Konfigurationsprofil für einen Datencontroller initialisiert, das für `az arcdata dc create` verwendet werden kann. Die jeweilige Quelle des Konfigurationsprofils kann in den Argumenten angegeben werden.
```bash
az arcdata dc config init 
```
### <a name="examples"></a>Beispiele
Angeleitete Ausführung von „config init“: Sie erhalten Aufforderungen für die Eingabe der erforderlichen Werte.
```bash
az arcdata dc config init
```
arcdata dc config init mit Argumenten erstellt ein Konfigurationsprofil von aks-dev-test unter ./custom.
```bash
az arcdata dc config init --source azure-arc-kubeadm --path custom
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
Listet die verfügbaren Optionen für das Konfigurationsprofil zur Verwendung in `arcdata dc config init` auf
```bash
az arcdata dc config list 
```
### <a name="examples"></a>Beispiele
Listet alle verfügbaren Namen für das Konfigurationsprofil auf.
```bash
az arcdata dc config list
```
Zeigt JSON eines bestimmten Konfigurationsprofils an.
```bash
az arcdata dc config list --config-profile aks-dev-test
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
Fügt den Wert im JSON-Pfad in der Konfigurationsdatei hinzu Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az arcdata dc config add 
```
### <a name="examples"></a>Beispiele
Hinzufügen eines Datencontrollerspeichers
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
Entfernt den Wert im JSON-Pfad in der Konfigurationsdatei  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az arcdata dc config remove 
```
### <a name="examples"></a>Beispiele
Beispiel 1: Entfernen des Datencontrollerspeichers.
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
Ersetzt den Wert im JSON-Pfad in der Konfigurationsdatei  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az arcdata dc config replace 
```
### <a name="examples"></a>Beispiele
Beispiel 1: Ersetzen des Ports eines einzelnen Endpunkts (Datencontroller-Endpunkt).
```bash
az arcdata dc config replace --path custom/control.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
Beispiel 2: Ersetzen des Datencontrollerspeichers.
```bash
az arcdata dc config replace --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
Patcht die Konfigurationsdatei entsprechend der angegebenen Patchdatei Informationen zum besseren Verständnis der Zusammensetzung der Pfade finden Sie unter http://jsonpatch.com/. Der Vorgang „Ersetzen“ kann Bedingungen im Pfad aufgrund der JSONPATH-Bibliothek verwenden (https://jsonpath.com/ ). Alle JSON-Patchdateien müssen mit einem „Patch“-Schlüssel beginnen, der über ein Array von Patches mit dem entsprechenden Vorgang (Hinzufügen, Ersetzen, Entfernen), Pfad und Wert verfügt. Der Vorgang „Entfernen“ erfordert keinen Wert, sondern nur einen Pfad. Beispiele hierzu finden Sie weiter unten.
```bash
az arcdata dc config patch 
```
### <a name="examples"></a>Beispiele
Beispiel 1: Ersetzen des Ports eines einzelnen Endpunkts (Datencontroller-Endpunkt) durch Patchdatei.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
Beispiel 2: Ersetzen des Datencontrollerspeichers durch Patchdatei.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

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
