---
title: Referenz zu „az arcdata resource-kind“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az arcdata resource-kind“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ad138bfc322aa109078e622b26d1f5254ec789ca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852980"
---
# <a name="az-arcdata-resource-kind"></a>az arcdata resource-kind
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata resource-kind list](#az-arcdata-resource-kind-list) | Listet die verfügbaren benutzerdefinierten Ressourcentypen für Arc auf, die definiert und erstellt werden können.
[az arcdata resource-kind get](#az-arcdata-resource-kind-get) | Ruft die Vorlagendatei für den Arc-Ressourcentyp (resource-kind) ab.
## <a name="az-arcdata-resource-kind-list"></a>az arcdata resource-kind list
Listet die verfügbaren benutzerdefinierten Ressourcentypen für Arc auf, die definiert und erstellt werden können. Nach dem Auflisten können Sie die Vorlagendatei abrufen, die zum Definieren oder Erstellen dieser benutzerdefinierten Ressource erforderlich ist.
```bash
az arcdata resource-kind list 
```
### <a name="examples"></a>Beispiele
Dies ist ein Beispielbefehl zum Auflisten der verfügbaren benutzerdefinierten Ressourcentypen für Arc.
```bash
az arcdata resource-kind list
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
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Ruft die Vorlagendatei für den Arc-Ressourcentyp (resource-kind) ab.
```bash
az arcdata resource-kind get 
```
### <a name="examples"></a>Beispiele
Dies ist ein Beispielbefehl zum Abrufen der CRD-Vorlagendatei für einen Arc-Ressourcentyp.
```bash
az arcdata resource-kind get --kind SqlManagedInstance
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
