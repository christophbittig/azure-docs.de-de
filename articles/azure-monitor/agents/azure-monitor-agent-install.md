---
title: 'Installieren des Azure Monitor-Agents:'
description: Optionen zum Installieren des Azure Monitor-Agents (AMA) auf Azure-VMs und Servern mit Azure Arc-Unterstützung.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/19/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a5390b2bd4888c0fe43fb0b0d94b07563d1a68
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434896"
---
# <a name="install-the-azure-monitor-agent"></a>Installieren des Azure Monitor-Agents:
In diesem Artikel werden die verschiedenen Optionen beschrieben, die derzeit für die Installation des [Azure Monitor-Agents](azure-monitor-agent-overview.md) auf Azure-VMs und Servern mit Azure Arc-Unterstützung verfügbar sind, sowie die Optionen zum Erstellen von [Zuordnungen für Datensammlungsregeln](data-collection-rule-azure-monitor-agent.md), die definieren, welche Daten der Agent erfassen soll.

## <a name="prerequisites"></a>Voraussetzungen
Vor der Installation des Azure Monitor-Agents müssen die folgenden Voraussetzungen erfüllt sein.

- Auf Azure-VMs muss eine [verwaltete Systemidentität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) aktiviert sein. Bei Servern mit Azure Arc-Unterstützung ist dies nicht erforderlich. Die Systemidentität wird automatisch aktiviert, wenn der Agent als Teil des Prozesses zum [Erstellen und Zuweisen einer Datensammlungsregel über das Azure-Portal](#install-with-azure-portal) installiert wird.
- Das [Diensttag „AzureResourceManager“](../../virtual-network/service-tags-overview.md) muss im virtuellen Netzwerk für die VM aktiviert sein.
- Der virtuelle Computer benötigt Zugriff auf die folgenden HTTPS-Endpunkte:
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com

> [!IMPORTANT]
> Der Azure Monitor-Agent unterstützt derzeit keine privaten Verbindungen.

## <a name="virtual-machine-extension-details"></a>Details zur VM-Erweiterung
Der Azure Monitor-Agent wird als [Azure-VM-Erweiterung](../../virtual-machines/extensions/overview.md) mit den Details aus der folgenden Tabelle implementiert: Er kann mit einer der Methoden zum Installieren von VM-Erweiterungen installiert werden, einschließlich der in diesem Artikel beschriebenen Methoden.

| Eigenschaft | Windows | Linux |
|:---|:---|:---|
| Herausgeber | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

## <a name="extension-versions"></a>Erweiterungsversionen
Es wird dringend empfohlen, auf GA+-Versionen zu aktualisieren, anstatt Vorschauversionen zu verwenden.

| Veröffentlichungsdatum | Versionshinweise | Windows | Linux |
|:---|:---|:---|:---|:---|
| Juni 2021 | Allgemeine Verfügbarkeit angekündigt. <ul><li>Alle Features mit Ausnahme des Metrikenziels jetzt allgemein verfügbar.</li><li>Produktionsqualität, Sicherheit und Compliance</li><li>Verfügbarkeit in allen öffentlichen Regionen</li><li>Leistungs- und Skalierungsverbesserungen für höhere EPS-Werte</li></ul> [Weitere Informationen](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |
| Juli 2021 | <ul><li>Unterstützung für direkte Proxys</li><li>Unterstützung des Log Analytics-Gateways</li></ul> [Weitere Informationen](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 |
| August 2021 | Das Problem, das Azure Monitor-Metriken als einziges Ziel zulässt, wurde behoben. | 1.1.2.0 | 1.10.9.0 (nicht 1.10.7.0 verwenden) |


## <a name="install-with-azure-portal"></a>Installieren über das Azure-Portal
Zum Installieren des Azure Monitor-Agents über das Azure-Portal führen Sie das Verfahren zum [Erstellen einer Datensammlungsregel](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) im Azure-Portal aus. Auf diese Weise können Sie die Datensammlungsregel einer oder mehreren Azure-VMs oder einem oder mehreren Servern mit Azure Arc-Unterstützung zuordnen. Der Agent wird auf allen VMs installiert, auf denen er noch nicht installiert ist.


## <a name="install-with-resource-manager-template"></a>Installieren mit einer Resource Manager-Vorlage
Mithilfe von Resource Manager-Vorlagen können Sie den Azure Monitor-Agent auf Azure-VMs und Servern mit Azure Arc-Unterstützung installieren und eine Zuordnung zu Datensammlungsregeln erstellen. Die entsprechenden Datensammlungsregeln müssen Sie vor dem Erstellen der Zuordnung erstellen.

Hier finden Sie Beispielvorlagen für die Installation des Agents und die Erstellung der Zuordnung: 

- [Vorlage zum Installieren des Azure Monitor-Agents (Azure und Azure Arc)](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Vorlage zum Erstellen einer Zuordnung zu einer Datensammlungsregel](./resource-manager-data-collection-rules.md)

Installieren Sie die Vorlagen mit [einer Bereitstellungsmethode für Resource Manager-Vorlagen](../../azure-resource-manager/templates/deploy-powershell.md), z. B. mit den folgenden Befehlen.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Installieren mit PowerShell
Sie können den Azure Monitor-Agent auf Azure-VMs und Servern mit Azure Arc-Unterstützung über den PowerShell-Befehl zum Hinzufügen einer VM-Erweiterung installieren. 

### <a name="azure-virtual-machines"></a>Virtuelle Azure-Computer
Verwenden Sie die folgenden PowerShell-Befehle, um den Azure Monitor-Agent auf Azure-VMs zu installieren.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5
```
---

### <a name="azure-arc-enabled-servers"></a>Server mit Azure Arc-Unterstützung
Verwenden Sie die folgenden PowerShell-Befehle, um den Azure Monitor-Agent auf Servern mit Azure Arc-Unterstützung zu installieren.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
---
## <a name="azure-cli"></a>Azure CLI
Sie können den Azure Monitor-Agent auf Azure-VMs und Servern mit Azure Arc-Unterstützung über den Azure CLI-Befehl zum Hinzufügen einer VM-Erweiterung installieren. 

### <a name="azure-virtual-machines"></a>Virtuelle Azure-Computer
Verwenden Sie die folgenden CLI-Befehle, um den Azure Monitor-Agent auf Azure-VMs zu installieren.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Server mit Azure Arc-Unterstützung
Verwenden Sie die folgenden CLI-Befehle, um den Azure Monitor-Agent auf Servern mit Azure Arc-Unterstützung zu installieren.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
---


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie eine Datensammlungsregel](data-collection-rule-azure-monitor-agent.md), um Daten des Agents zu sammeln und an Azure Monitor zu senden.
