---
title: Referenz zu „az sql mi-arc config“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql mi-arc config“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: a8fad1be3245792c6967f56c9026bf8efee4d6e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346548"
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
az sql mi-arc config init --path -p 
                          
```
### <a name="examples"></a>Beispiele
Hiermit werden die CRD-Datei und die Spezifikationsdatei für eine verwaltete SQL-Instanz initialisiert.
```bash
az sql mi-arc config init --path ./template
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Dies ist ein Pfad, unter dem die CRD und die Spezifikation für die verwaltete SQL-Instanz geschrieben werden sollen.
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Der Name oder die ID des Abonnements. Sie können das standardmäßig verwendete Abonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
## <a name="az-sql-mi-arc-config-add"></a>az sql mi-arc config add
Fügt den Wert im JSON-Pfad in der Konfigurationsdatei hinzu.  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az sql mi-arc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>Beispiele
Bsp. 1: Hinzufügen von Speicher
```bash
az sql mi-arc config add --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Pfad zur benutzerdefinierten Ressourcenspezifikation, d. h. custom/spec.json
#### `--json-values -j`
Eine Schlüssel-Wert-Paar-Liste von JSON-Pfaden zu Werten: key1.subkey1=value1,key2.subkey2=value2 Sie können JSON-Inlinewerte angeben, z. B.: key='{"kind":"cluster","name":"test-cluster"}' oder einen Dateipfad, z. B. key=./values.json. Der Befehl „add“ unterstützt KEINE Bedingungen.  Wenn der Inlinewert, den Sie bereitstellen, selbst ein Schlüssel-Wert-Paar mit „=“ und „,“ ist, verwenden Sie an den entsprechenden Stellen Escapezeichen.  Zum Beispiel key1=„key2\=val2\,key3\=val3“. Beispiele dazu, wie Ihr Pfad aussehen sollte, finden Sie unter http://jsonpatch.com/.  Wenn Sie auf ein Array zugreifen möchten, müssen Sie dazu den Index angeben, z. B. key.0=value.
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Der Name oder die ID des Abonnements. Sie können das standardmäßig verwendete Abonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
## <a name="az-sql-mi-arc-config-remove"></a>az sql mi-arc config remove
Entfernt den Wert im JSON-Pfad in der Konfigurationsdatei.  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az sql mi-arc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>Beispiele
Bsp. 1: Entfernen von Speicher
```bash
az sql mi-arc config remove --path custom/spec.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Pfad zur benutzerdefinierten Ressourcenspezifikation, d. h. custom/spec.json
#### `--json-path -j`
Eine Liste von JSON-Pfaden, die auf der JSONPATCH-Bibliothek basiert und angibt, welche Werte entfernt werden sollen, z. B.: key1.subkey1,key2.subkey2. Der Befehl „remove“ unterstützt KEINE Bedingungen. Beispiele dazu, wie Ihr Pfad aussehen sollte, finden Sie unter http://jsonpatch.com/.  Wenn Sie auf ein Array zugreifen möchten, müssen Sie dazu den Index angeben, z. B. key.0=value.
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Der Name oder die ID des Abonnements. Sie können das standardmäßig verwendete Abonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
## <a name="az-sql-mi-arc-config-replace"></a>az sql mi-arc config replace
Ersetzt den Wert im JSON-Pfad in der Konfigurationsdatei.  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az sql mi-arc config replace --path -p 
                             --json-values -j
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
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Pfad zur benutzerdefinierten Ressourcenspezifikation, d. h. custom/spec.json
#### `--json-values -j`
Eine Schlüssel-Wert-Paar-Liste von JSON-Pfaden zu Werten: key1.subkey1=value1,key2.subkey2=value2 Sie können JSON-Inlinewerte angeben, z. B.: key='{"kind":"cluster","name":"test-cluster"}' oder einen Dateipfad, z. B. key=./values.json. Der Befehl „replace“ unterstützt Bedingungen über die JSONPATH-Bibliothek.  Um diesen Parameter zu verwenden, starten Sie den Pfad mit „$“. Dies ermöglicht die Verwendung einer Bedingung, z. B. -j $.key1.key2[?(@.key3=="someValue"].key4=value. Wenn der Inlinewert, den Sie bereitstellen, selbst ein Schlüssel-Wert-Paar mit „=“ und „,“ ist, verwenden Sie an den entsprechenden Stellen Escapezeichen.  Zum Beispiel key1=„key2\=val2\,key3\=val3“. Nachfolgend sehen Sie hierzu Beispiele. Zusätzliche Hilfe finden Sie unter https://jsonpath.com/.
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Der Name oder die ID des Abonnements. Sie können das standardmäßig verwendete Abonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
## <a name="az-sql-mi-arc-config-patch"></a>az sql mi-arc config patch
Patcht die Konfigurationsdatei entsprechend der angegebenen Patchdatei. Informationen zum besseren Verständnis der Zusammensetzung der Pfade finden Sie unter http://jsonpatch.com/. Der Vorgang „Ersetzen“ kann Bedingungen im Pfad aufgrund der JSONPATH-Bibliothek verwenden (https://jsonpath.com/ ). Alle JSON-Patchdateien müssen mit einem „Patch“-Schlüssel beginnen, der über ein Array von Patches mit dem entsprechenden Vorgang (Hinzufügen, Ersetzen, Entfernen), Pfad und Wert verfügt. Der Vorgang „Entfernen“ erfordert keinen Wert, sondern nur einen Pfad. Beispiele hierzu finden Sie weiter unten.
```bash
az sql mi-arc config patch --path -p 
                           --patch-file
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
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Pfad zur benutzerdefinierten Ressourcenspezifikation, d. h. custom/spec.json
#### `--patch-file`
Pfad zu einer JSON-Patchdatei, die auf der JSONPATCH-Bibliothek basiert: http://jsonpatch.com/ Sie müssen die JSON-Patchdatei mit einem Schlüssel mit der Bezeichnung „Patch“ starten, bei dessen Wert es sich um ein Array von Patchvorgängen handelt, die Sie ausführen möchten. Für den Pfad eines Patchvorgangs können Sie für die meisten Vorgänge die Punktnotation verwenden, z. B. „key1.key2“. Wenn Sie einen „Ersetzen“-Vorgang durchführen möchten und einen Wert in einem Array ersetzen, das eine Bedingung erfordert, verwenden Sie die JSONPATH-Notation, indem Sie den Pfad mit einem Dollarzeichen („$“) beginnen. Dies ermöglicht die Verwendung einer Bedingung wie z. B. $.key1.key2[?(@.key3=="someValue"].key4. Siehe hierzu folgende Beispiele. Zusätzliche Hilfe zu Bedingungen finden Sie unter https://jsonpath.com/.
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Der Name oder die ID des Abonnements. Sie können das standardmäßig verwendete Abonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
