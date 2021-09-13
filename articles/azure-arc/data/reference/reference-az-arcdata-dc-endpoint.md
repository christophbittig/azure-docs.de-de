---
title: az arcdata dc endpoint – Referenz
titleSuffix: Azure Arc-enabled data services
description: Dies ist der Referenzartikel für az arcdata dc endpoint-Befehle.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c024050777d01bb93fbbcba63a636d4e34c163b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354837"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | Hiermit wird der Datencontrollerendpunkt angezeigt.
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
Hiermit wird der Datencontrollerendpunkt angezeigt.
```bash
az arcdata dc endpoint list --k8s-namespace -k 
                            [--endpoint-name -e]  
                            
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Listet alle verfügbaren Endpunkte für den Datencontroller auf
```bash
az arcdata dc endpoint list --k8s-namespace namespace
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem der Datencontroller vorhanden ist.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--endpoint-name -e`
Dies ist der Name des Arc-Datencontrollerendpunkts.
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
