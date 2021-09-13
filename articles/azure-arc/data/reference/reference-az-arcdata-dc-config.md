---
title: az arcdata dc config reference
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu az arcdata dc-Konfigurationsbefehlen
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: a7936f1bbe56b105ec65dfa76cb7346fa41953ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338982"
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
az arcdata dc config init [--path -p] 
                          [--source -s]  
                          
[--force -f]
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
### <a name="optional-parameters"></a>Optionale Parameter
#### `--path -p`
Der Dateipfad, in dem das Konfigurationsprofil platziert werden soll, ist standardmäßig <cwd>/custom.
#### `--source -s`
Quelle des Konfigurationsprofils: ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
#### `--force -f`
Erzwingen der Überschreibung der Zieldatei
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
Listet die verfügbaren Optionen für das Konfigurationsprofil zur Verwendung in `arcdata dc config init` auf
```bash
az arcdata dc config list [--config-profile -c] 
                          
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
### <a name="optional-parameters"></a>Optionale Parameter
#### `--config-profile -c`
Standardkonfigurationsprofil: ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
Fügt den Wert im JSON-Pfad in der Konfigurationsdatei hinzu Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az arcdata dc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>Beispiele
Hinzufügen eines Datencontrollerspeichers
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Pfad für die Konfigurationsdatei eines Datencontrollers der Konfiguration, die Sie festlegen möchten, z. B. custom/control.json
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
Entfernt den Wert im JSON-Pfad in der Konfigurationsdatei  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az arcdata dc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>Beispiele
Beispiel 1: Entfernen des Datencontrollerspeichers.
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Pfad für die Konfigurationsdatei eines Datencontrollers der Konfiguration, die Sie festlegen möchten, z. B. custom/control.json
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
Ersetzt den Wert im JSON-Pfad in der Konfigurationsdatei  Alle nachfolgenden Beispiele sind in Bash angegeben.  Bei Verwendung einer anderen Befehlszeile müssen Sie möglicherweise entsprechende Escapezeichen hinzufügen.  Alternativ können Sie auch die Patchdateifunktionen verwenden.
```bash
az arcdata dc config replace --path -p 
                             --json-values -j
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
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Pfad für die Konfigurationsdatei eines Datencontrollers der Konfiguration, die Sie festlegen möchten, z. B. custom/control.json
#### `--json-values -j`
Eine Schlüssel-Wert-Paar-Liste von JSON-Pfaden zu Werten: key1.subkey1=value1,key2.subkey2=value2 Sie können JSON-Inlinewerte angeben, z. B.: key='{"kind":"cluster","name":"test-cluster"}' oder einen Dateipfad, z. B. key=./values.json. Der Befehl „replace“ unterstützt Bedingungen über die JSONPATH-Bibliothek.  Um diesen Parameter zu verwenden, starten Sie den Pfad mit „$“. Dies ermöglicht die Verwendung einer Bedingung, z. B. -j $.key1.key2[?(@.key3=="someValue"].key4=value. Wenn der Inlinewert, den Sie bereitstellen, selbst ein Schlüssel-Wert-Paar mit „=“ und „,“ ist, verwenden Sie an den entsprechenden Stellen Escapezeichen.  Zum Beispiel key1=„key2\=val2\,key3\=val3“. Nachfolgend sehen Sie hierzu Beispiele. Weitere Hilfe finden Sie unter: https://jsonpath.com/
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
Patcht die Konfigurationsdatei entsprechend der angegebenen Patchdatei Informationen zum besseren Verständnis der Zusammensetzung der Pfade finden Sie unter http://jsonpatch.com/. Der Vorgang „Ersetzen“ kann Bedingungen im Pfad aufgrund der JSONPATH-Bibliothek verwenden (https://jsonpath.com/ ). Alle JSON-Patchdateien müssen mit einem „Patch“-Schlüssel beginnen, der über ein Array von Patches mit dem entsprechenden Vorgang (Hinzufügen, Ersetzen, Entfernen), Pfad und Wert verfügt. Der Vorgang „Entfernen“ erfordert keinen Wert, sondern nur einen Pfad. Beispiele hierzu finden Sie weiter unten.
```bash
az arcdata dc config patch --path 
                           --patch-file -p
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
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path`
Pfad für die Konfigurationsdatei eines Datencontrollers der Konfiguration, die Sie festlegen möchten, z. B. custom/control.json
#### `--patch-file -p`
Pfad zu einer JSON-Patchdatei, die auf der JSONPATCH-Bibliothek basiert: http://jsonpatch.com/ Sie müssen die JSON-Patchdatei mit einem Schlüssel mit der Bezeichnung „Patch“ starten, bei dessen Wert es sich um ein Array von Patchvorgängen handelt, die Sie ausführen möchten. Für den Pfad eines Patchvorgangs können Sie für die meisten Vorgänge die Punktnotation verwenden, z. B. „key1.key2“. Wenn Sie einen „Ersetzen“-Vorgang durchführen möchten und einen Wert in einem Array ersetzen, das eine Bedingung erfordert, verwenden Sie die JSONPATH-Notation, indem Sie den Pfad mit einem Dollarzeichen („$“) beginnen. Dies ermöglicht die Verwendung einer Bedingung wie z. B. $.key1.key2[?(@.key3=="someValue"].key4. Beispiele hierzu finden Sie weiter unten. Zusätzliche Hilfe zu Bedingungen finden Sie unter https://jsonpath.com/.
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
