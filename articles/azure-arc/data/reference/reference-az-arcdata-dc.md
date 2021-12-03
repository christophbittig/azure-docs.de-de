---
title: Referenz zu „az arcdata dc“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu az arcdata dc-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 23cbe9c98e3c0ad86704061dadfad0e83f627ac3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852958"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | Hiermit wird ein Datencontroller erstellt.
[az arcdata dc upgrade](#az-arcdata-dc-upgrade) | Upgradet den Datencontroller.
[az arcdata dc list-upgrades](#az-arcdata-dc-list-upgrades) | Listet die verfügbaren Upgradeversionen auf.
[az arcdata dc delete](#az-arcdata-dc-delete) | Hiermit wird ein Datencontroller gelöscht.
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | Endpunktbefehle
[az arcdata dc status](reference-az-arcdata-dc-status.md) | Statusbefehle
[az arcdata dc config](reference-az-arcdata-dc-config.md) | Konfigurationsbefehle
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | Dient zum Debuggen des Datencontrollers.
[az arcdata dc export](#az-arcdata-dc-export) | Hiermit werden Metriken, Protokolle oder der Verbrauch exportiert.
[az arcdata dc upload](#az-arcdata-dc-upload) | Hiermit werden exportierte Datendateien hochgeladen.
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
Erstellt einen Datencontroller: Eine Kube-Konfiguration ist auf Ihrem System erforderlich, zusammen mit Anmeldeinformationen für die Überwachungsdashboards, die von den folgenden Umgebungsvariablen bereitgestellt werden: AZDATA_LOGSUI_USERNAME und AZDATA_LOGSUI_PASSWORD für das Protokolle-Dashboard und AZDATA_METRICSUI_USERNAME und AZDATA_METRICSUI_PASSWORD für das Metriken-Dashboard. Alternativ werden AZDATA_USERNAME und AZDATA_PASSWORD als Fallback verwendet, wenn eine der beiden Sätze von Umgebungsvariablen fehlt.
```bash
az arcdata dc create 
```
### <a name="examples"></a>Beispiele
Bereitstellen eines indirekt verbundenen Datencontrollers.
```bash
az arcdata dc create --name name --k8s-namespace namespace --connectivity-mode indirect --resource-group group  --location location --subscription subscription --use-k8s
```
Bereitstellen eines direkt verbundenen Datencontrollers.
```bash
az arcdata dc create --name name  --connectivity-mode direct --resource-group group  --location location --subscription subscription  --custom-location custom-location         
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
## <a name="az-arcdata-dc-upgrade"></a>az arcdata dc upgrade
Upgradet den Datencontroller auf die angegebene gewünschte Version (desired-version).  Wenn die gewünschte Version (desired-version) nicht angegeben ist, wird versucht, ein Upgrade auf die neueste Version durchzuführen. Wenn Sie sich der gewünschten Version nicht sicher sind, können Sie den Befehl „list-upgrades“ verwenden, um verfügbare Versionen anzuzeigen, oder Sie verwenden das Argument „--dry-run“, um anzuzeigen, welche Version verwendet würde.
```bash
az arcdata dc upgrade 
```
### <a name="examples"></a>Beispiele
Datencontrollerupgrade.
```bash
az arcdata dc upgrade --k8s-namespace namespace --use-k8s
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
## <a name="az-arcdata-dc-list-upgrades"></a>az arcdata dc list-upgrades
Versucht, Versionen auflisten, die in der Docker-Imageregistrierung für das Upgrade verfügbar sind. – Eine Kube-Konfiguration sowie die Umgebungsvariablen ['AZDATA_USERNAME', 'AZDATA_PASSWORD'] müssen auf Ihrem System vorhanden sein.
```bash
az arcdata dc list-upgrades 
```
### <a name="examples"></a>Beispiele
Datencontrollerupgrade.
```bash
az arcdata dc list-upgrades --k8s-namespace namespace --use-k8s            
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
Hiermit wird der Datencontroller gelöscht. Eine Kube-Konfiguration ist in Ihrem System erforderlich.
```bash
az arcdata dc delete 
```
### <a name="examples"></a>Beispiele
Löschen eines indirekt verbundenen Datencontrollers.
```bash
az arcdata dc delete --name name --k8s-namespace namespace --use-k8s
```
Löschen eines direkt verbundenen Datencontrollers.
```bash
az arcdata dc delete --name name --resource-group resource-group            
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
Hiermit werden Metriken, Protokolle oder der Verbrauch in eine Datei exportiert.
```bash
az arcdata dc export 
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
Hiermit werden von einem Datencontroller exportierte Datendateien zu Azure hochgeladen.
```bash
az arcdata dc upload 
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
