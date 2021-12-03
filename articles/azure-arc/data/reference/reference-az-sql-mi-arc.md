---
title: Referenz zu „az sql mi-arc“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql mi-arc“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 0a98cd873de2da0daaf793a2e40df5c4d5c38285
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852292"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | Anzeigen und Verwalten von SQL-Endpunkten
[az sql mi-arc create](#az-sql-mi-arc-create) | Hiermit wird eine verwaltete SQL-Instanz erstellt.
[az sql mi-arc update](#az-sql-mi-arc-update) | Hiermit wird die Konfiguration einer verwalteten SQL-Instanz aktualisiert.
[az sql mi-arc delete](#az-sql-mi-arc-delete) | Hiermit wird eine verwaltete SQL-Instanz gelöscht.
[az sql mi-arc show](#az-sql-mi-arc-show) | Hiermit werden die Details einer verwalteten SQL-Instanz angezeigt.
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | Ruft das Zertifikat des Spiegelungsendpunkts der Verfügbarkeitsgruppe von sqlmi ab, und speichert es in einer Datei.
[az sql mi-arc upgrade](#az-sql-mi-arc-upgrade) | Hiermit wird eine verwaltete SQL-Instanz geupgradet.
[az sql mi-arc list](#az-sql-mi-arc-list) | Hiermit werden verwaltete SQL-Instanzen aufgelistet.
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | Konfigurationsbefehle
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | Erstellt oder löscht eine verteilte Verfügbarkeitsgruppe.
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
Um das Kennwort für die verwaltete SQL-Instanz festzulegen, legen Sie die Umgebungsvariable AZDATA_PASSWORD fest.
```bash
az sql mi-arc create 
```
### <a name="examples"></a>Beispiele
Erstellen einer indirekt verbundenen, verwalteten SQL-Instanz.
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace --use-k8s
```
Erstellen einer indirekt verbundenen, verwalteten SQL-Instanz mit 3 Replikaten in einem Hochverfügbarkeitsszenario.
```bash
az sql mi-arc create -n sqlmi2 --replicas 3  --k8s-namespace namespace --use-k8s
```
Erstellen einer direkt verbundenen, verwalteten SQL-Instanz.
```bash
az sql mi-arc create --name name --resource-group group  --location location --subscription subscription   --custom-location custom-location
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
## <a name="az-sql-mi-arc-update"></a>az sql mi-arc update
Hiermit wird die Konfiguration einer verwalteten SQL-Instanz aktualisiert.
```bash
az sql mi-arc update 
```
### <a name="examples"></a>Beispiele
Hiermit wird die Konfiguration einer verwalteten SQL-Instanz aktualisiert.
```bash
az sql mi-arc update --path ./spec.json -n sqlmi1 --use-k8s
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
Hiermit wird eine verwaltete SQL-Instanz gelöscht.
```bash
az sql mi-arc delete 
```
### <a name="examples"></a>Beispiele
Löschen einer verwalteten SQL-Instanz mithilfe des bereitgestellten Namespaces.
```bash
az sql mi-arc delete --name sqlmi1 --k8s-namespace namespace --use-k8s
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
Hiermit werden die Details einer verwalteten SQL-Instanz angezeigt.
```bash
az sql mi-arc show 
```
### <a name="examples"></a>Beispiele
Anzeigen der Details einer indirekt verbundenen, verwalteten SQL-Instanz.
```bash
az sql mi-arc show --name sqlmi1 --k8s-namespace namespace --use-k8s
```
Anzeigen der Details einer direkt verbundenen, verwalteten SQL-Instanz.
```bash
az sql mi-arc show --name sqlmi1 --resource-group resource-group            
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
Ruft das Zertifikat des Spiegelungsendpunkts der Verfügbarkeitsgruppe von sqlmi ab, und speichert es in einer Datei.
```bash
az sql mi-arc get-mirroring-cert 
```
### <a name="examples"></a>Beispiele
Ruft das Zertifikat des Spiegelungsendpunkts der Verfügbarkeitsgruppe von sqlmi1 ab, und speichert es in der Datei „fileName1“.
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
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
## <a name="az-sql-mi-arc-upgrade"></a>az sql mi-arc upgrade
Führt ein Upgrade einer verwalteten SQL-Instanz auf die angegebene gewünschte Version (desired-version) durch.  Wenn die gewünschte Version (desired-version) nicht angegeben ist, wird die Datencontrollerversion verwendet.
```bash
az sql mi-arc upgrade 
```
### <a name="examples"></a>Beispiele
Hiermit wird eine verwaltete SQL-Instanz geupgradet.
```bash
az sql mi-arc upgrade -n sqlmi1 -k arc --desired-version v1.1.0 --use-k8s
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
Hiermit werden verwaltete SQL-Instanzen aufgelistet.
```bash
az sql mi-arc list 
```
### <a name="examples"></a>Beispiele
Hiermit werden verwaltete SQL-Instanzen aufgelistet.
```bash
az sql mi-arc list --use-k8s
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
