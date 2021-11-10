---
title: Referenz zu „az sql mi-arc endpoint“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql mi-arc endpoint“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: db7c1fb6366cda40a5978a7be55100ae7a12ac4a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852400"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az sql mi-arc endpoint list](#az-sql-mi-arc-endpoint-list) | Damit werden die SQL-Endpunkte aufgelistet.
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint list
Damit werden die SQL-Endpunkte aufgelistet.
```bash
az sql mi-arc endpoint list 
```
### <a name="examples"></a>Beispiele
Listen Sie die Endpunkte für eine verwaltete SQL-Instanz auf.
```bash
az sql mi-arc endpoint list -n sqlmi1
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
