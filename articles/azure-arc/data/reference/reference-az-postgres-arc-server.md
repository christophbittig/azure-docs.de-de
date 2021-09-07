---
title: Referenz zu „az postgres arc-server“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az postgres arc-server“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c58819707eb5987bddeb0d8983c29e7519b69818
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355466"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az postgres arc-server create](#az-postgres-arc-server-create) | Erstellen Sie eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung.
[az postgres arc-server edit](#az-postgres-arc-server-edit) | Bearbeiten Sie die Konfiguration einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung.
[az postgres arc-server delete](#az-postgres-arc-server-delete) | Löscht eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
[az postgres arc-server show](#az-postgres-arc-server-show) | Zeigt Details einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung an
[az postgres arc-server list](#az-postgres-arc-server-list) | Listet die Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf
[az postgres arc-server endpoint](reference-az-postgres-arc-server-endpoint.md) | Verwalten der Endpunkte einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
## <a name="az-postgres-arc-server-create"></a>az postgres arc-server create
Legen Sie die Umgebungsvariable „AZDATA_PASSWORD“ fest, um das Kennwort für die Servergruppe festzulegen.
```bash
az postgres arc-server create --name -n 
                              [--path]  
                              
[--k8s-namespace -k]  
                              
[--cores-limit]  
                              
[--cores-request]  
                              
[--memory-limit]  
                              
[--memory-request]  
                              
[--storage-class-data]  
                              
[--storage-class-logs]  
                              
[--storage-class-backups]  
                              
[--volume-claim-mounts]  
                              
[--extensions]  
                              
[--volume-size-data]  
                              
[--volume-size-logs]  
                              
[--volume-size-backups]  
                              
[--workers -w]  
                              
[--engine-version]  
                              
[--no-external-endpoint]  
                              
[--port]  
                              
[--no-wait]  
                              
[--engine-settings]  
                              
[--coordinator-settings]  
                              
[--worker-settings]  
                              
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Erstellen Sie eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung.
```bash
az postgres arc-server create -n pg1 --k8s-namespace namespace --use-k8s
```
Erstellen Sie eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung mit Einstellungen für die Engine. Beide unten aufgeführten Beispiele sind gültig.
```bash
az postgres arc-server create -n pg1 --engine-settings "key1=val1" --k8s-namespace namespace 
az postgres arc-server create -n pg1 --engine-settings "key2=val2" --k8s-namespace namespace --use-k8s
```
Erstellen Sie eine PostgreSQL-Servergruppe mit Volumeanspruchseinbindungen.
```bash
az postgres arc-server create -n pg1 --volume-claim-mounts backup-pvc:backup 
```
Erstellen Sie eine PostgreSQL-Servergruppe mit einem bestimmten Arbeitsspeicherlimit für verschiedene Knotenrollen.
```bash
az postgres arc-server create -n pg1 --memory-limit "coordinator=2Gi,w=1Gi" --workers 1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Name der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--path`
Der Pfad zur JSON-Quelldatei für die Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung. Diese Eingabe ist optional.
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem die PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung bereitgestellt wird. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--cores-limit`
Die maximale Anzahl von CPU-Kernen, die pro Knoten in einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung verwendet werden kann. Teilkerne werden unterstützt. Optional kann eine durch Komma getrennte Liste von Rollen mit Werten im Format <role>= angegeben werden.<value>. Gültige Rollen sind „coordinator“ oder „c“, „worker“ oder „w“. Wenn keine Rollen angegeben werden, gelten die Einstellungen für alle Knoten der PostgreSQL Hyperscale-Servergruppe.
#### `--cores-request`
Dies ist die Mindestanzahl von CPU-Kernen, die pro Knoten verfügbar sein müssen, um den Dienst zu planen. Teilkerne werden unterstützt. Optional kann eine durch Komma getrennte Liste von Rollen mit Werten im Format <role>= angegeben werden.<value>. Gültige Rollen sind „coordinator“ oder „c“, „worker“ oder „w“. Wenn keine Rollen angegeben werden, gelten die Einstellungen für alle Knoten der PostgreSQL Hyperscale-Servergruppe.
#### `--memory-limit`
Der Arbeitsspeichergrenzwert für eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung als Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte). Optional kann eine durch Komma getrennte Liste von Rollen mit Werten im Format <role>= angegeben werden.<value>. Gültige Rollen sind „coordinator“ oder „c“, „worker“ oder „w“. Wenn keine Rollen angegeben werden, gelten die Einstellungen für alle Knoten der PostgreSQL Hyperscale-Servergruppe.
#### `--memory-request`
Die Arbeitsspeicheranforderung für eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung als Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte). Optional kann eine durch Komma getrennte Liste von Rollen mit Werten im Format <role>= angegeben werden.<value>. Gültige Rollen sind „coordinator“ oder „c“, „worker“ oder „w“. Wenn keine Rollen angegeben werden, gelten die Einstellungen für alle Knoten der PostgreSQL Hyperscale-Servergruppe.
#### `--storage-class-data`
Dies ist die für persistente Datenvolumes zu verwendende Speicherklasse.
#### `--storage-class-logs`
Dies ist die für persistente Protokollvolumes zu verwendende Speicherklasse.
#### `--storage-class-backups`
Dies ist die für persistente Sicherungsvolumes zu verwendende Speicherklasse.
#### `--volume-claim-mounts`
Eine durch Trennzeichen getrennte Liste mit Volumeanspruchseinbindungen. Bei einer Volumeanspruchseinbindung handelt es sich um ein durch einen Doppelpunkt getrenntes Paar, das aus einem Anspruch auf ein vorhandenes persistentes Volume im selben Namespace und dem Volumetyp (sowie je nach Volumetyp optionalen Metadaten) besteht. Das persistente Volume wird in jedem Pod für die PostgreSQL-Servergruppe eingebunden. Der Einbindungspfad hängt möglicherweise vom Volumetyp ab.
#### `--extensions`
Dies ist eine durch Trennzeichen getrennte Liste der Postgres-Erweiterungen, die beim Start geladen werden sollen. Unterstützte Werte finden Sie in der Postgres-Dokumentation.
#### `--volume-size-data`
Dies ist die Größe des Speichervolumes, das für Daten verwendet werden soll, als positive Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte).
#### `--volume-size-logs`
Dies ist die Größe des Speichervolumes, das für Datenprotokolle verwendet werden soll, als positive Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte).
#### `--volume-size-backups`
Dies ist die Größe des Speichervolumes, das für Sicherungen verwendet werden soll, als positive Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte).
#### `--workers -w`
Die Anzahl der Workerknoten, die in einer Servergruppe bereitgestellt werden sollen. In der Vorschau wird das Reduzieren der Anzahl von Workerknoten nicht unterstützt. Weitere Informationen finden Sie in der Dokumentation.
#### `--engine-version`
Dieser Wert muss 11 oder 12 sein. Der Standardwert ist 12.
`12`
#### `--no-external-endpoint`
Falls ein Wert angegeben ist, wird kein externer Dienst erstellt. Andernfalls wird ein externer Dienst mit demselben Diensttyp wie dem des Datencontrollers erstellt.
#### `--port`
Dies ist optional.
#### `--no-wait`
Wenn dieser Wert vorhanden ist, wartet der Befehl nicht darauf, dass die Instanz bereit ist, bis eine Rückgabe erfolgt.
#### `--engine-settings`
Dies ist eine durch Kommas getrennte Liste der Postgres-Engine-Einstellungen im Format „key1=val1, key2=val2“.
#### `--coordinator-settings`
Eine durch Komma getrennte Liste von Postgres-Engine-Einstellungen im Format „key1=val1, key2=val2“, die auf die Knotenrolle „coordinator“ angewendet werden soll. Wenn für die Knotenrolle spezifische Einstellungen angegeben werden, werden die Standardeinstellungen ignoriert und mit den hier angegebenen Einstellungen überschrieben.
#### `--worker-settings`
Eine durch Komma getrennte Liste von Postgres-Engine-Einstellungen im Format „key1=val1, key2=val2“, die auf die Knotenrolle „worker“ angewendet werden soll. Wenn für die Knotenrolle spezifische Einstellungen angegeben werden, werden die Standardeinstellungen ignoriert und mit den hier angegebenen Einstellungen überschrieben.
#### `--use-k8s`
Verwenden Sie lokale Kubernetes-APIs, um diese Aktion auszuführen.
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
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Bearbeitet die Konfiguration einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
```bash
az postgres arc-server edit --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--workers -w]  
                            
[--cores-limit]  
                            
[--cores-request]  
                            
[--memory-limit]  
                            
[--memory-request]  
                            
[--extensions]  
                            
[--port]  
                            
[--no-wait]  
                            
[--engine-settings]  
                            
[--replace-settings]  
                            
[--coordinator-settings]  
                            
[--worker-settings]  
                            
[--admin-password]  
                            
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Bearbeiten Sie die Konfiguration einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung.
```bash
az postgres arc-server edit --path ./spec.json -n pg1 --k8s-namespace namespace --use-k8s
```
Hier wird eine PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung mit Engine-Einstellungen für den Koordinatorknoten bearbeitet.
```bash
az postgres arc-server edit -n pg1 --coordinator-settings "key2=val2" --k8s-namespace namespace
```
Hiermit wird Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung bearbeitet, und vorhandene Engine-Einstellungen werden durch die neue Einstellung „key1=val1“ ersetzt.
```bash
az postgres arc-server edit -n pg1 --engine-settings "key1=val1" --replace-settings --k8s-namespace namespace
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Name der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung, die bearbeitet wird. Der Name, unter dem Ihre Instanz bereitgestellt wird, kann nicht geändert werden.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem die PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung bereitgestellt wird. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--path`
Der Pfad zur JSON-Quelldatei für die Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung. Diese Eingabe ist optional.
#### `--workers -w`
Die Anzahl der Workerknoten, die in einer Servergruppe bereitgestellt werden sollen. In der Vorschau wird das Reduzieren der Anzahl von Workerknoten nicht unterstützt. Weitere Informationen finden Sie in der Dokumentation.
#### `--cores-limit`
Die maximale Anzahl von CPU-Kernen, die pro Knoten in einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung verwendet werden kann (Teilkerne werden unterstützt). Geben Sie den entsprechenden Wert als leere Zeichenfolge an, um cores_limit zu entfernen. Optional kann eine durch Komma getrennte Liste von Rollen mit Werten im Format <role>= angegeben werden.<value>. Gültige Rollen sind „coordinator“ oder „c“, „worker“ oder „w“. Wenn keine Rollen angegeben werden, gelten die Einstellungen für alle Knoten der PostgreSQL Hyperscale-Servergruppe.
#### `--cores-request`
Dies ist die Mindestanzahl von CPU-Kernen, die pro Knoten verfügbar sein müssen, um den Dienst zu planen. Teilkerne werden unterstützt. Geben Sie den entsprechenden Wert als leere Zeichenfolge an, um cores_request zu entfernen. Optional kann eine durch Komma getrennte Liste von Rollen mit Werten im Format <role>= angegeben werden.<value>. Gültige Rollen sind „coordinator“ oder „c“, „worker“ oder „w“. Wenn keine Rollen angegeben werden, gelten die Einstellungen für alle Knoten der PostgreSQL Hyperscale-Servergruppe.
#### `--memory-limit`
Der Arbeitsspeichergrenzwert der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung als Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte). Geben Sie den entsprechenden Wert als leere Zeichenfolge an, um memory_limit zu entfernen. Optional kann eine durch Komma getrennte Liste von Rollen mit Werten im Format <role>= angegeben werden.<value>. Gültige Rollen sind „coordinator“ oder „c“, „worker“ oder „w“. Wenn keine Rollen angegeben werden, gelten die Einstellungen für alle Knoten der PostgreSQL Hyperscale-Servergruppe.
#### `--memory-request`
Die Arbeitsspeicheranforderung der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung als Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte). Geben Sie den entsprechenden Wert als leere Zeichenfolge an, um memory_request zu entfernen. Optional kann eine durch Komma getrennte Liste von Rollen mit Werten im Format <role>= angegeben werden.<value>. Gültige Rollen sind „coordinator“ oder „c“, „worker“ oder „w“. Wenn keine Rollen angegeben werden, gelten die Einstellungen für alle Knoten der PostgreSQL Hyperscale-Servergruppe.
#### `--extensions`
Dies ist eine durch Trennzeichen getrennte Liste der Postgres-Erweiterungen, die beim Start geladen werden sollen. Unterstützte Werte finden Sie in der Postgres-Dokumentation.
#### `--port`
Dies ist optional.
#### `--no-wait`
Wenn dieser Wert vorhanden ist, wartet der Befehl nicht darauf, dass die Instanz bereit ist, bis eine Rückgabe erfolgt.
#### `--engine-settings`
Dies ist eine durch Kommas getrennte Liste der Postgres-Engine-Einstellungen im Format „key1=val1, key2=val2“. Die angegebenen Einstellungen werden mit den vorhandenen Einstellungen zusammengeführt. Geben Sie einen leeren Wert wie „removedKey=“ an, um eine Einstellung zu entfernen. Wenn Sie eine Engine-Einstellung ändern, die einen Neustart erfordert, wird der Dienst neu gestartet, um die Einstellung sofort anzuwenden.
#### `--replace-settings`
Bei einer Angabe mit --engine-settings werden alle vorhandenen benutzerdefinierten Engine-Einstellungen durch neue Einstellungen und Werte ersetzt.
#### `--coordinator-settings`
Eine durch Komma getrennte Liste von Postgres-Engine-Einstellungen im Format „key1=val1, key2=val2“, die auf die Knotenrolle „coordinator“ angewendet werden soll. Wenn für die Knotenrolle spezifische Einstellungen angegeben werden, werden die Standardeinstellungen ignoriert und mit den hier angegebenen Einstellungen überschrieben.
#### `--worker-settings`
Eine durch Komma getrennte Liste von Postgres-Engine-Einstellungen im Format „key1=val1, key2=val2“, die auf die Knotenrolle „worker“ angewendet werden soll. Wenn für die Knotenrolle spezifische Einstellungen angegeben werden, werden die Standardeinstellungen ignoriert und mit den hier angegebenen Einstellungen überschrieben.
#### `--admin-password`
Bei angegebenem Wert wird das Administratorkennwort der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf den Wert der Umgebungsvariable AZDATA_PASSWORD (falls vorhanden) bzw. einen angeforderten Wert festgelegt.
#### `--use-k8s`
Verwenden Sie lokale Kubernetes-APIs, um diese Aktion auszuführen.
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
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Löscht eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
```bash
az postgres arc-server delete --name -n 
                              [--k8s-namespace -k]  
                              
[--force -f]  
                              
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Löscht eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Name der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem die PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung bereitgestellt wird. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--force -f`
Erzwingen Sie das Löschen der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung ohne Bestätigung.
#### `--use-k8s`
Verwenden Sie lokale Kubernetes-APIs, um diese Aktion auszuführen.
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
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Zeigt Details einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung an
```bash
az postgres arc-server show --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Zeigt Details einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung an
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Name der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem die PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung bereitgestellt wird. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--path`
Dies ist ein Pfad, unter dem die vollständige Spezifikation für die Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung geschrieben werden sollen. Wenn keine Angabe erfolgt, wird die Spezifikation in die Standardausgabe geschrieben.
#### `--use-k8s`
Verwenden Sie lokale Kubernetes-APIs, um diese Aktion auszuführen.
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
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Listet die Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf
```bash
az postgres arc-server list [--k8s-namespace -k] 
                            [--use-k8s]
```
### <a name="examples"></a>Beispiele
Listet die Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem die PostgreSQL Hyperscale-Servergruppen mit Azure Arc-Unterstützung bereitgestellt werden. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--use-k8s`
Verwenden Sie lokale Kubernetes-APIs, um diese Aktion auszuführen.
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
