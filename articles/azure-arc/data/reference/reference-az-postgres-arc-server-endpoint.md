---
title: Referenz zu „az postgres arc-server endpoint“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az postgres arc-server endpoint“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 78111c030c1641cab22664d889e26505de879cf7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847696"
---
# <a name="az-postgres-arc-server-endpoint"></a>az postgres arc-server endpoint
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az postgres arc-server endpoint list](#az-postgres-arc-server-endpoint-list) | Listet die Endpunkte einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf
## <a name="az-postgres-arc-server-endpoint-list"></a>az postgres arc-server endpoint list
Listet die Endpunkte einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf
```bash
az postgres arc-server endpoint list 
```
### <a name="examples"></a>Beispiele
Listet die Endpunkte einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf
```bash
az postgres arc-server endpoint list --name postgres01  --k8s-namespace namespace --use-k8s
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
