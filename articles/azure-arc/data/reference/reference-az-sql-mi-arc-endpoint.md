---
title: Referenz zu „az sql mi-arc endpoint“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql mi-arc endpoint“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ca3685c6769e45d43b513ea11355795af332a849
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346621"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az sql mi-arc endpoint list](#az-sql-mi-arc-endpoint-list) | Damit werden die SQL-Endpunkte aufgelistet.
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint list
Damit werden die SQL-Endpunkte aufgelistet.
```bash
az sql mi-arc endpoint list [--name -n] 
                            [--k8s-namespace -k]  
                            
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Listen Sie die Endpunkte für eine verwaltete SQL-Instanz auf.
```bash
az sql mi-arc endpoint list -n sqlmi1
```
### <a name="optional-parameters"></a>Optionale Parameter
#### `--name -n`
Der Name der SQL-Instanz, die angezeigt werden soll. Wenn dieser Parameter nicht angegeben wird, werden alle Endpunkte für alle Instanzen angezeigt.
#### `--k8s-namespace -k`
Namespace, in dem sich die verwalteten SQL-Instanzen befinden. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
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
