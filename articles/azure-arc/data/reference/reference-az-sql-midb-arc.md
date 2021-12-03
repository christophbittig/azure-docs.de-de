---
title: az sql midb-arc
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql midb-arc“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 8053ea28fe1ce1f7f05c1bb710265fb86839dcd2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858589"
---
# <a name="az-sql-midb-arc"></a>az sql midb-arc
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az sql midb-arc restore](#az-sql-midb-arc-restore) | Stellt eine Datenbank in einer SQL Managed Instance mit Azure Arc-Unterstützung wieder her.
## <a name="az-sql-midb-arc-restore"></a>az sql midb-arc restore
Stellt eine Datenbank in einer SQL Managed Instance mit Azure Arc-Unterstützung wieder her.
```bash
az sql midb-arc restore 
```
### <a name="examples"></a>Beispiele
Beispiel 1: Wiederherstellen einer Datenbank mithilfe der Point-in-Time-Wiederherstellung.
```bash
az sql midb-arc restore --managed-instance sqlmi1 --name mysourcedb
 --dest-name mynewdb --time "2021-10-20T05:34:22Z" --k8s-namespace
 arc --use-k8s --dry-run
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
