---
title: az arcdata dc status reference
titleSuffix: Azure Arc-enabled data services
description: Dies ist der Referenzartikel zu „az arcdata dc status“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9e795c71a5316960bfc7f0b1138afce3ecc4b957
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339129"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata dc status show](#az-arcdata-dc-status-show) | Zeigt den Status des Datencontrollers.
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status show
Zeigt den Status des Datencontrollers.
```bash
az arcdata dc status show [--k8s-namespace -k] 
                          [--use-k8s]
```
### <a name="examples"></a>Beispiele
Zeigt den Status des Datencontrollers in einem bestimmten Kubernetes-Namespace.
```bash
az arcdata dc status show --k8s-namespace <ns>
```
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem der Datencontroller vorhanden ist
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
Der Name oder die ID des Abonnements. Sie können das Standardabonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
