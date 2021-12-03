---
title: Übersicht über die Problembehandlung für ARM-Vorlagen und Bicep-Dateien
description: Hier wird die Problembehandlung für die Azure-Ressourcenbereitstellung mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) und Bicep-Dateien erläutert.
ms.topic: overview
ms.date: 10/26/2021
ms.custom: troubleshooting-overview
ms.openlocfilehash: 604ba2be3fc1adcf22df9774400237a1b07f93e1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101403"
---
# <a name="what-is-deployment-troubleshooting"></a>Was ist die Problembehandlung für Bereitstellungen?

Wenn Sie Bicep-Dateien oder Azure Resource Manager-Vorlagen (ARM-Vorlagen) bereitstellen, wird möglicherweise eine Fehlermeldung angezeigt. Diese Dokumentation hilft Ihnen bei der Ermittlung möglicher Lösungen für Fehler.

## <a name="error-types"></a>Fehlertypen

Es können zwei Arten von Fehlern auftreten: **Validierungsfehler** und **Bereitstellungsfehler**.

Validierungsfehler treten auf, bevor die Bereitstellung gestartet wird. Diese Fehler können ermittelt werden, ohne mit Ihrer aktuellen Azure-Umgebung zu interagieren. Die Validierung macht Sie beispielsweise auf Syntaxfehler oder fehlende Argumente für eine Funktion aufmerksam, bevor Ihre Bereitstellung gestartet wird.

Bereitstellungsfehler können nur ermittelt werden, indem Sie versuchen, die Bereitstellung zu starten und mit Ihrer Azure-Umgebung zu interagieren. Für eine VM (virtueller Computer) ist beispielsweise ein Netzwerkadapter erforderlich. Wenn der Netzwerkadapter bei der Bereitstellung der VM nicht vorhanden ist, wird ein Bereitstellungsfehler ausgelöst.

## <a name="troubleshooting-tools"></a>Problembehandlungstools

Verwenden Sie die neueste Version von [Visual Studio Code](https://code.visualstudio.com), um Syntaxfehler vor einer Bereitstellung zu ermitteln. Installieren der aktuellen Version von:

* [Bicep-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)
* [Azure Resource Manager Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Zur Problembehandlung bei Bereitstellungen ist es hilfreich, sich über die Eigenschaften oder API-Versionen eines Ressourcenanbieters zu informieren. Weitere Informationen finden Sie unter [Definieren von Ressourcen mit Bicep- und ARM-Vorlagen](/azure/templates).

Verwenden Sie eine der folgenden Methoden, um die Best Practices für die Vorlagenerstellung einzuhalten:

* [Bicep-Linter](../bicep/linter.md)
* [Resource Manager-Vorlagen-Testtoolkit](../templates/test-toolkit.md)

Bei der Bereitstellung mit Azure finden Sie die Fehlerursache im Azure-Portal im Abschnitt **Bereitstellungen** oder **Aktivitätsprotokoll** einer Ressourcengruppe. Wenn Sie Azure PowerShell verwenden, können Sie Befehle wie [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) oder [Get-AzActivityLog](/powershell/module/az.monitor/get-azactivitylog) ausführen. In der Azure CLI können Sie Befehle wie [az deployment operation group](/cli/azure/deployment/operation/group) oder [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) ausführen.

## <a name="next-steps"></a>Nächste Schritte

- Unter [Problembehandlung für häufige Azure-Bereitstellungsfehler](common-deployment-errors.md) werden Lösungen für verschiedene Fehlercodes aufgeführt.
- Eine Einführung in das Suchen des Fehlercodes erhalten Sie unter [Schnellstart: Problembehandlung für ARM-Vorlagenbereitstellungen](quickstart-troubleshoot-arm-deployment.md) oder [Schnellstart: Problembehandlung für Bicep-Dateibereitstellungen](quickstart-troubleshoot-bicep-deployment.md).
