---
title: az arcdata dc status reference
titleSuffix: Azure Arc-enabled data services
description: Dies ist der Referenzartikel zu „az arcdata dc status“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 542f0f9568150abf0646e1a494e14eec2be6e524
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841634"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata dc status show](#az-arcdata-dc-status-show) | Zeigt den Status des Datencontrollers.
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status show
Zeigt den Status des Datencontrollers.
```bash
az arcdata dc status show 
```
### <a name="examples"></a>Beispiele
Zeigt den Status des Datencontrollers in einem bestimmten Kubernetes-Namespace.
```bash
az arcdata dc status show --k8s-namespace namespace --use-k8s
```
Anzeigen des Status eines direkt verbundenen Datencontrollers in einer bestimmten Ressourcengruppe.
```bash
az arcdata dc status show --resource-group resource-group    
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
