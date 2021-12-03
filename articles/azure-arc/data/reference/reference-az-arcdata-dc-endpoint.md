---
title: az arcdata dc endpoint – Referenz
titleSuffix: Azure Arc-enabled data services
description: Dies ist der Referenzartikel für az arcdata dc endpoint-Befehle.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9d07d5e5263fd887af49552bb45eb98ae7f35441
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845739"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | Hiermit wird der Datencontrollerendpunkt angezeigt.
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
Hiermit wird der Datencontrollerendpunkt angezeigt.
```bash
az arcdata dc endpoint list 
```
### <a name="examples"></a>Beispiele
Listet alle verfügbaren Endpunkte für den Datencontroller auf
```bash
az arcdata dc endpoint list --k8s-namespace namespace
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
