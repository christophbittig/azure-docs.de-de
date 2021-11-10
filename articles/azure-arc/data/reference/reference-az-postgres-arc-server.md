---
title: Referenz zu „az postgres arc-server“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az postgres arc-server“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9a5746f4a18cf0df2045c22c58cf26a9aa44fc74
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845701"
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
az postgres arc-server create 
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
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Bearbeitet die Konfiguration einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
```bash
az postgres arc-server edit 
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
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Löscht eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
```bash
az postgres arc-server delete 
```
### <a name="examples"></a>Beispiele
Löscht eine Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Zeigt Details einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung an
```bash
az postgres arc-server show 
```
### <a name="examples"></a>Beispiele
Zeigt Details einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung an
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Listet die Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf
```bash
az postgres arc-server list 
```
### <a name="examples"></a>Beispiele
Listet die Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
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
