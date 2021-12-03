---
title: Überwachen einer App in Azure Container Apps (Vorschauversion)
description: Hier erfahren Sie, wie Anwendungen in Azure Container Apps überwacht und protokolliert werden.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: c9c324c90ad401d43be1e2d6768ea82121fc1ae8
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027831"
---
# <a name="monitor-an-app-in-azure-container-apps-preview"></a>Überwachen einer App in Azure Container Apps (Vorschauversion)

Azure Container Apps sammelt eine Vielzahl von Daten zu Ihrer Container-App und speichert sie mithilfe von [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md). In diesem Artikel werden die verfügbaren Protokolle sowie das Schreiben und Anzeigen von Protokollen beschrieben.

## <a name="writing-to-a-log"></a>Schreiben in ein Protokoll

Wenn Sie in die [Datenströme für die Standardausgabe (stdout) oder Standardfehler (stderr)](https://wikipedia.org/wiki/Standard_streams) schreiben, schreiben die Container Apps-Protokollierungs-Agents Protokolle für jede Nachricht.

Wenn eine Nachricht protokolliert wird, werden die folgenden Informationen in der Protokolltabelle erfasst:

| Eigenschaft | Hinweise |
|---|---|
| `RevisionName` | |
| `ContainerAppName` | |
| `ContainerGroupID` | |
| `ContainerGroupName` | |
| `ContainerImage` | |
| `ContainerID` | Der eindeutige Bezeichner des Containers. Sie können diesen Wert verwenden, um Containerabstürze zu identifizieren. |
| `Stream` | Zeigt an, ob `stdout` oder `stderr` für die Protokollierung verwendet wird. |
| `EnvironmentName` | |

### <a name="simple-text-vs-structured-data"></a>Einfacher Text im Vergleich zu strukturierten Daten

Sie können eine einzelne Textzeichenfolge oder Zeile serialisierter JSON-Daten protokollieren. Informationen werden je nach Art der protokollierten Daten unterschiedlich angezeigt.

| Datentyp | Beschreibung |
|---|---|
| Einzelne Textzeile | Text wird in der Spalte `Log_s` angezeigt. |
| Serialisierter JSON-Code | Daten werden vom Protokollierungs-Agent analysiert und in Spalten angezeigt, die den JSON-Objekteigenschaftennamen entsprechen. |

## <a name="viewing-logs"></a>Anzeigen von Protokollen

Daten, die über eine Container-App protokolliert werden, werden in der benutzerdefinierten Tabelle `ContainerAppConsoleLogs_CL` im Log Analytics-Arbeitsbereich gespeichert. Sie können Protokolle über das Azure-Portal oder mit der CLI anzeigen.

Legen Sie den Namen Ihrer Ressourcengruppe und des Log Analytics-Arbeitsbereichs fest, und rufen Sie dann `LOG_ANALYTICS_WORKSPACE_CLIENT_ID` mit den folgenden Befehlen ab.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"

LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"

$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv
```

---

Verwenden Sie den folgenden CLI-Befehl, um Protokolle in der Befehlszeile anzuzeigen.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" `
  --out table
```

---

Die folgende Ausgabe veranschaulicht den Typ der Antwort, die vom CLI-Befehl erwartet wird.

```console
ContainerAppName_s    Log_s                 TableName      TimeGenerated
--------------------  --------------------  -------------  ------------------------
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:09:00.168Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:36.197Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:43.171Z
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schützen Ihrer Container-App](secure-app.md)
