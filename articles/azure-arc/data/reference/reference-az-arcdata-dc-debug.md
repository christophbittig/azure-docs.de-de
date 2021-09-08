---
title: Referenz zu „az arcdata dc debug“
titleSuffix: Azure Arc-enabled data services
description: Dies ist der Referenzartikel zu „az arcdata dc debug“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 219471cf793394c024061f39319a98cc89477c76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346252"
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
az arcdata dc debug copy-logs --k8s-namespace -k 
                              [--container -c]  
                              
[--target-folder -d]  
                              
[--pod]  
                              
[--resource-kind]  
                              
[--resource-name]  
                              
[--timeout -t]  
                              
[--skip-compress]  
                              
[--exclude-dumps]  
                              
[--exclude-system-logs ]  
                              
[--use-k8s]
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--k8s-namespace -k`
Kubernetes-Namespace des Datencontrollers
### <a name="optional-parameters"></a>Optionale Parameter
#### `--container -c`
Kopieren Sie die Protokolle für die Container mit ähnlichem Namen. Standardmäßig werden die Protokolle für alle Container kopiert. Dieser Parameter kann nicht mehrfach angegeben werden. Ist er mehrfach angegeben, wird der letzte Parameter verwendet.
#### `--target-folder -d`
Der Pfad des Zielordners, in den die Protokolle kopiert werden sollen. Optional. Standardmäßig wird das Ergebnis im lokalen Ordner erstellt.  Dieser Parameter kann nicht mehrfach angegeben werden. Ist er mehrfach angegeben, wird der letzte Parameter verwendet.
#### `--pod`
Kopieren Sie die Protokolle für die Pods mit ähnlichem Namen. Optional. Standardmäßig werden die Protokolle für alle Pods kopiert. Dieser Parameter kann nicht mehrfach angegeben werden. Ist er mehrfach angegeben, wird der letzte Parameter verwendet.
#### `--resource-kind`
Kopieren Sie die Protokolle für eine bestimmte Art von Ressource. Dieser Parameter kann nicht mehrfach angegeben werden. Ist er mehrfach angegeben, wird der letzte Parameter verwendet. Wenn dieser Parameter angegeben ist, sollte zum Identifizieren der Ressource auch --resource-name angegeben werden.
#### `--resource-name`
Kopieren Sie die Protokolle für die Ressource mit dem angegebenen Namen. Dieser Parameter kann nicht mehrfach angegeben werden. Ist er mehrfach angegeben, wird der letzte Parameter verwendet. Wenn dieser Parameter angegeben ist, sollte zum Identifizieren der Ressource auch --resource-kind angegeben werden.
#### `--timeout -t`
Die Zeit in Sekunden, während der auf das Abschließen des Befehls gewartet wird. Der Standardwert ist „0“, d. h., das Warten ist unbegrenzt.
#### `--skip-compress`
Gibt an, ob die Komprimierung des resultierenden Ordners übersprungen werden soll Der Standardwert ist „False“. Dadurch wird der resultierende Ordner komprimiert.
#### `--exclude-dumps`
Gibt an, ob Sicherungsdateien aus dem resultierenden Ordner ausgeschlossen werden sollen Der Standardwert ist „False“. Dadurch werden Sicherungsdateien eingeschlossen.
#### `--exclude-system-logs `
Gibt an, ob Systemprotokolle aus der Sammlung ausgeschlossen werden sollen. Mit dem Standardwert FALSE werden Systemprotokolle eingeschlossen.
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
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
Lösen Sie ein Speicherabbild aus, und kopieren Sie es aus dem Container. Hierfür ist eine Kubernetes-Konfiguration auf Ihrem System erforderlich.
```bash
az arcdata dc debug dump --k8s-namespace -k 
                         [--container -c]  
                         
[--target-folder -d]  
                         
[--use-k8s]
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--k8s-namespace -k`
Kubernetes-Namespace des Datencontrollers
### <a name="optional-parameters"></a>Optionale Parameter
#### `--container -c`
Hierbei handelt es sich um den Zielcontainer, der zum Sichern der aktiven Prozesse ausgelöst werden soll.
`controller`
#### `--target-folder -d`
Hierbei handelt es sich um den Zielordner, aus dem das Abbild kopiert wird. `./output/dump`
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
