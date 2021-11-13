---
title: 'Installieren des Azure Monitor-Agents:'
description: Optionen für die Installation des Azure Monitor Agent (AMA) auf virtuellen Azure-Maschinen und Azure Arc-aktivierten Servern.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 3c1b3ac13f79037b3357f3ecba0d3d668f88d0b5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850886"
---
# <a name="install-the-azure-monitor-agent"></a>Installieren des Azure Monitor-Agents:
Dieser Artikel stellt die verschiedenen Optionen vor, die derzeit für die Installation des [Azure Monitor-Agenten](azure-monitor-agent-overview.md) sowohl auf virtuellen Azure-Maschinen als auch auf Azure Arc-fähigen Servern zur Verfügung stehen, sowie die Optionen zur Erstellung von [Verknüpfungen mit Datensammelregeln](data-collection-rule-azure-monitor-agent.md), die festlegen, welche Daten der Agent sammeln soll.

## <a name="prerequisites"></a>Voraussetzungen
Vor der Installation des Azure Monitor-Agents müssen die folgenden Voraussetzungen erfüllt sein.

- Auf Azure-VMs muss eine [verwaltete Systemidentität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) aktiviert sein. Dies ist für Azure Arc-fähige Server nicht erforderlich. Die Systemidentität wird automatisch aktiviert, wenn der Agent als Teil des Prozesses zum [Erstellen und Zuweisen einer Datensammlungsregel über das Azure-Portal](#install-with-azure-portal) installiert wird.
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
Es wird dringend empfohlen, auf die unten aufgeführten GA+-Versionen zu aktualisieren, statt Vorschau- oder Zwischenversionen zu verwenden.

| Veröffentlichungsdatum | Versionshinweise | Windows | Linux |
|:---|:---|:---|:---|:---|
| Juni 2021 | Allgemeine Verfügbarkeit angekündigt. <ul><li>Alle Features mit Ausnahme des Metrikenziels jetzt allgemein verfügbar.</li><li>Produktionsqualität, Sicherheit und Compliance</li><li>Verfügbarkeit in allen öffentlichen Regionen</li><li>Leistungs- und Skalierungsverbesserungen für höhere EPS-Werte</li></ul> [Weitere Informationen](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |
| Juli 2021 | <ul><li>Unterstützung für direkte Proxys</li><li>Unterstützung des Log Analytics-Gateways</li></ul> [Weitere Informationen](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 |
| August 2021 | Das Problem, das Azure Monitor-Metriken als einziges Ziel zulässt, wurde behoben. | 1.1.2.0 | 1.10.9.0<sup>1</sup> |
| September 2021 | <ul><li>Problem behoben, das zu Datenverlusten beim Neustart des Agenten führte</li><li>Die in 1.1.3.1<sup>2</sup> für Arc Windows-Server einführte Regression wurde behoben</li></ul> | 1.1.3.2 | 1.12.2.0 <sup>2</sup> |  

<sup>1</sup> Verwenden Sie keine AMA Linux-Version 1.10.7.0 <sup>2</sup> Bekannte Regression, wenn sie auf Arc-fähigen Servern nicht funktioniert


## <a name="install-with-azure-portal"></a>Installieren über das Azure-Portal
Zum Installieren des Azure Monitor-Agents über das Azure-Portal führen Sie das Verfahren zum [Erstellen einer Datensammlungsregel](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) im Azure-Portal aus. So können Sie die Datensammelregel mit einer oder mehreren virtuellen Azure-Maschinen oder Azure Arc-fähigen Servern verknüpfen. Der Agent wird auf allen VMs installiert, auf denen er noch nicht installiert ist.


## <a name="install-with-resource-manager-template"></a>Installieren mit einer Resource Manager-Vorlage
Sie können Resource Manager-Vorlagen verwenden, um den Azure Monitor-Agenten auf virtuellen Azure-Maschinen und auf Azure Arc-fähigen Servern zu installieren und eine Zuordnung zu Datensammlungsregeln zu erstellen. Die entsprechenden Datensammlungsregeln müssen Sie vor dem Erstellen der Zuordnung erstellen.

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
Sie können den Azure Monitor-Agenten auf virtuellen Azure-Maschinen und auf Azure Arc-fähigen Servern mithilfe des PowerShell-Befehls zum Hinzufügen einer Erweiterung für virtuelle Maschinen installieren. 

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

### <a name="azure-arc-enabled-servers"></a>Server mit Azure Arc-Unterstützung
Verwenden Sie die folgenden PowerShell-Befehle, um den Azure Monitor-Agenten auf Azure Arc-aktivierten Servern zu installieren.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
---
## <a name="install-with-azure-cli"></a>Installieren mit Azure CLI
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
### <a name="azure-arc-enabled-servers"></a>Server mit Azure Arc-Unterstützung
Verwenden Sie die folgenden CLI-Befehle, um den Azure Monitor-Agenten auf Azure Arc-aktivierten Servern zu installieren.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
---


## <a name="install-with-azure-policy"></a>Installieren mit Azure Policy
Verwenden Sie die folgenden Richtlinien und Richtlinieninitiativen, um den Agent bei jeder Erstellung eines virtuellen Computers automatisch zu installieren und einer Datensammlungsregel zuzuordnen.

### <a name="built-in-policy-initiatives"></a>Integrierte Richtlinieninitiativen
[Anzeigen der Voraussetzungen für die Agent-Installation](azure-monitor-agent-install.md#prerequisites). 

Richtlinieninitiativen für virtuelle Windows- und Linux-Computer bestehen aus einzelnen Richtlinien, die

- die Azure Monitor-Agent-Erweiterung auf dem virtuellen Computer installieren.
- die Zuordnung zum Verknüpfen des virtuellen Computers mit einer Datensammlungsregel erstellen und bereitstellen.

![Teilscreenshot der Seite mit Azure Policy-Definitionen mit zwei integrierten Richtlinieninitiativen zum Konfigurieren des Azure Monitor-Agents.](media/azure-monitor-agent-install/built-in-ama-dcr-initiatives.png)  

### <a name="built-in-policies"></a>Integrierte Richtlinien 
Sie können die einzelnen Richtlinien aus ihren jeweiligen Richtlinieninitiativen basierend auf Ihren Anforderungen zur Verwendung auswählen. Wenn Sie beispielsweise nur den Agent automatisch installieren möchten, verwenden Sie die erste Richtlinie der Initiative, wie im folgenden Beispiel gezeigt.  

![Teilscreenshot der Seite mit Azure Policy-Definitionen mit Richtlinien, die in der Initiative zum Konfigurieren des Azure Monitor-Agents enthalten sind.](media/azure-monitor-agent-install/built-in-ama-dcr-policy.png)  

### <a name="remediation"></a>Wiederherstellung
Die Initiativen oder Richtlinien gelten für jeden virtuellen Computer, während er erstellt wird. Mit einer [Wartungsaufgabe](../../governance/policy/how-to/remediate-resources.md) werden die Richtliniendefinitionen in der Initiative für *vorhandene Ressourcen* bereitgestellt, sodass Sie den Azure Monitor-Agent für alle Ressourcen konfigurieren können, die bereits erstellt wurden. 

Wenn Sie die Zuweisung mithilfe des Azure-Portals erstellen, können Sie gleichzeitig einen Wartungstask erstellen. Weitere Informationen zur Wartung finden Sie unter [Korrigieren nicht konformer Ressourcen mit Azure Policy](../../governance/policy/how-to/remediate-resources.md).

![Screenshot der Initiativenwartung für den Azure Monitor-Agent.](media/azure-monitor-agent-install/built-in-ama-dcr-remediation.png)

## <a name="diagnostic-settings"></a>Diagnoseeinstellungen
[Diagnoseeinstellungen](../essentials/diagnostic-settings.md) erfassen Ressourcenprotokolle und Metriken von Azure-Ressourcen und leiten sie an mehrere Orte weiter. Ein typischer Ort ist ein Log Analytics-Arbeitsbereich, mit dem Sie die Daten mittels [Protokollabfragen](../logs/log-query-overview.md) und [Protokollwarnungen](../alerts/alerts-log.md) analysieren können. Verwenden Sie Azure Policy, um automatisch jedes Mal, wenn Sie eine Ressource erstellen, eine Diagnoseeinstellung zu erstellen.


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie eine Datensammlungsregel](data-collection-rule-azure-monitor-agent.md), um Daten des Agents zu sammeln und an Azure Monitor zu senden.
