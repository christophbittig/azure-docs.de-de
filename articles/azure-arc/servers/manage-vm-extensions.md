---
title: Verwaltung von VM-Erweiterungen mit Azure Arc-fähigen Servern
description: Mit Azure Arc-fähigen Servern kann die Bereitstellung von Erweiterungen für virtuelle Computer verwaltet werden, die Konfigurations- und Automatisierungsaufgaben nach der Bereitstellung für nicht in Azure gehostete VMs bereitstellen.
ms.date: 10/28/2021
ms.topic: conceptual
ms.openlocfilehash: d853317fad2da9d8d7f27cece4fd7d219cdc42dc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294179"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Verwaltung von Erweiterungen für virtuelle Computer mit Azure Arc-fähigen Servern

Erweiterungen für virtuelle Computer sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen. Wenn z. B. Software auf einer VM installiert werden muss, Virenschutz oder die Ausführung eines Skripts erforderlich ist, kann eine VM-Erweiterung verwendet werden.

Mit Azure Arc-fähigen Servern können Sie Azure-VM-Erweiterungen für nicht in Azure gehostete Windows- und Linux-VMs bereitstellen, entfernen und aktualisieren, um die Verwaltung Ihrer Hybridcomputer über ihren gesamten Lebenszyklus zu vereinfachen. VM-Erweiterungen können mit den folgenden Methoden auf Ihren hybriden Computern oder Servern verwaltet werden, die von Azure Arc-fähigen Servern verwaltet werden:

- Das [Azure-Portal](manage-vm-extensions-portal.md)
- Die [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md)

> [!NOTE]
> Das Bereitstellen und Verwalten von VM-Erweiterungen auf Azure-VMs wird auf Servern mit Azure Arc-Unterstützung nicht unterstützt. Informationen zu virtuellen Azure-Computern finden Sie im Artikel [Erweiterungen und Features für virtuelle Azure-Computer](../../virtual-machines/extensions/overview.md).

> [!NOTE]
> Derzeit können Sie Erweiterungen lediglich über das Azure-Portal oder die Azure CLI aktualisieren. Das Ausführen dieses Vorgangs über Azure PowerShell oder mit einer Azure Resource Manager-Vorlage wird derzeit nicht unterstützt.

## <a name="key-benefits"></a>Hauptvorteile

Die Unterstützung von VM-Erweiterungen durch Azure Arc-fähige Server bietet die folgenden Hauptvorteile:

- Sammeln von Protokolldaten für die Analyse mit [Protokollen in Azure Monitor](../../azure-monitor/logs/data-platform-logs.md) durch Aktivieren der Log Analytics-Agent-VM-Erweiterung. Mit Log Analytics wird es nützlich, um komplexe Analysen über Protokolldaten aus unterschiedlichen Typen von Quellen hinweg auszuführen.

- Der Dienst analysiert mithilfe von [VM insights](../../azure-monitor/vm/vminsights-overview.md) die Leistung Ihrer Windows- und Linux-VMs und überwacht deren Prozesse und Abhängigkeiten von anderen Ressourcen und externen Prozessen. Dies wird durch Aktivieren sowohl der Log Analytics-Agent- als auch der Dependency-Agent-VM-Erweiterung erreicht.

- Herunterladen und Ausführen von Skripts auf hybrid verbundenen Computern mithilfe der Erweiterung für benutzerdefinierte Skripts. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations- oder Verwaltungsaufgaben.

- Automatisches Aktualisieren von Zertifikaten, die in einer [Azure Key Vault](../../key-vault/general/overview.md)-Instanz gespeichert sind.

## <a name="availability"></a>Verfügbarkeit

Die Funktionalität der VM-Erweiterungen ist nur in den [unterstützten Regionen](overview.md#supported-regions) aus der Liste verfügbar. Achten Sie darauf, das Onboarding Ihres Computers in einer dieser Regionen durchzuführen.

## <a name="extensions"></a>Erweiterungen

In dieser Version unterstützen wir die folgenden VM-Erweiterungen auf Windows- und Linux-Computern.

Weitere Informationen zum Azure Connected Machine-Agent-Paket und Details zur Erweiterungs-Agent-Komponente finden Sie in der [Agent-Übersicht](agent-overview.md#agent-component-details).

> [!NOTE]
> Vor Kurzem wurde die Unterstützung für die DSC-VM-Erweiterung für Azure Arc-fähige Server entfernt. Alternativ empfiehlt es sich, die benutzerdefinierte Skripterweiterung zu verwenden, um die Konfiguration nach der Bereitstellung des Servers oder Computers zu verwalten.

Arc-fähige Server unterstützen das Verschieben von Computern, auf denen eine oder mehrere VM-Erweiterungen installiert sind, zwischen Ressourcengruppen oder einem anderen Azure-Abonnement, ohne dass sich dies auf die Konfiguration auswirkt. Quell- und Zielabonnement müssen im selben [Azure Active Directory-Mandanten](../../active-directory/develop/quickstart-create-new-tenant.md) vorhanden sein. Diese Unterstützung wird ab der Connected Machine-Agent-Version **1.8.21197.005** aktiviert. Weitere Informationen zum Verschieben von Ressourcen und Überlegungen vor dem Fortfahren finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="windows-extensions"></a>Windows-Erweiterungen

|Erweiterung |Herausgeber |type |Zusätzliche Informationen |
|----------|----------|-----|-----------------------|
|Integrierte Sicherheitsrisikoscanner von Microsoft Defender für Cloud |Qualys |WindowsAgent.AzureSecurityCenter |[Integrierte Microsoft Defender-für-Cloud-Lösung zur Sicherheitsrisikobewertung für Azure- und Hybridcomputer](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Microsoft Antimalware-Erweiterung |Microsoft.Azure.Security |IaaSAntimalware |[Microsoft Antimalware-Erweiterung für Windows](../../virtual-machines/extensions/iaas-antimalware-windows.md) |
|Benutzerdefinierte Skripterweiterung |Microsoft.Compute | CustomScriptExtension |[Erweiterung für benutzerdefinierte Windows-Skripts](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics-Agent |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Log Analytics-Erweiterung für virtuelle Computer für Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor für VMs (Insights) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [VM-Erweiterung für den Dependency-Agent für Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault-Zertifikatsynchronisierung | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Key Vault-VM-Erweiterung für Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor-Agent |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Installieren des Azure Monitor-Agents (Vorschau)](../../azure-monitor/agents/azure-monitor-agent-install.md) |
|Azure Automation: Hybrid Runbook Worker-Erweiterung (Vorschau) |Microsoft.Compute |HybridWorkerForWindows |[Stellen Sie einen erweiterungsbasierten Benutzer-Hybrid Runbook Worker](../../automation/extension-based-hybrid-runbook-worker-install.md) bereit, um Runbooks lokal auszuführen. |

### <a name="linux-extensions"></a>Linux-Erweiterungen

|Erweiterung |Herausgeber |type |Zusätzliche Informationen |
|----------|----------|-----|-----------------------|
|Integrierte Sicherheitsrisikoscanner von Microsoft Defender für Cloud |Qualys |LinuxAgent.AzureSecurityCenter |[Integrierte Microsoft Defender-für-Cloud-Lösung zur Sicherheitsrisikobewertung für Azure- und Hybridcomputer](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Benutzerdefinierte Skripterweiterung |Microsoft.Azure.Extensions |CustomScript |[Erweiterung für benutzerdefinierte Linux-Skripts, Version 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics-Agent |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Log Analytics VM-Erweiterung für Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor für VMs (Insights) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[VM-Erweiterung für den Dependency-Agent für Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault-Zertifikatsynchronisierung | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Key Vault-VM-Erweiterung für Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor-Agent |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Installieren des Azure Monitor-Agents (Vorschau)](../../azure-monitor/agents/azure-monitor-agent-install.md) |
|Azure Automation: Hybrid Runbook Worker-Erweiterung (Vorschau) |Microsoft.Compute |HybridWorkerForLinux |[Stellen Sie einen erweiterungsbasierten Benutzer-Hybrid Runbook Worker](../../automation/extension-based-hybrid-runbook-worker-install.md) bereit, um Runbooks lokal auszuführen.|

## <a name="prerequisites"></a>Voraussetzungen

Diese Funktion hängt von den folgenden Azure-Ressourcenanbietern in Ihrem Abonnement ab:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Wenn diese noch nicht registriert sind, führen Sie die Schritte unter [Registrieren von Azure-Ressourcenanbietern](agent-overview.md#register-azure-resource-providers) aus.

Lesen Sie unbedingt die Dokumentation zu jeder VM-Erweiterung, auf die in der vorherigen Tabelle verwiesen wird, um mehr über potenzielle Netzwerk- oder Systemanforderungen zu erfahren. Dies kann dabei helfen, Konnektivitätsprobleme mit einem Azure-Dienst oder -Feature zu vermeiden, der oder das von dieser VM-Erweiterung abhängt.

### <a name="log-analytics-vm-extension"></a>Log Analytics-Erweiterung für virtuelle Computer

Für die Log Analytics-Agent-VM-Erweiterung für Linux muss Python 2.x auf dem Zielcomputer installiert sein. 

Es wird empfohlen, dass Sie vor dem Installieren der Erweiterung die [Bereitstellungsoptionen für den Log Analytics-Agent](concept-log-analytics-extension-deployment.md) lesen, um zu verstehen, welche verschiedenen Methoden verfügbar sind und welche Ihre Anforderungen erfüllen.

### <a name="azure-key-vault-vm-extension"></a>Azure Key Vault-VM-Erweiterung

Die folgenden Linux-Betriebssysteme werden von der Key Vault-VM-Erweiterung nicht unterstützt:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Die Bereitstellung der Key Vault-VM-Erweiterung wird nur mit Folgendem unterstützt:

- Die Azure-CLI
- Azure PowerShell
- Azure Resource Manager-Vorlage

Vor der Bereitstellung der Erweiterung müssen Sie die folgenden Schritte ausführen:

1. [Erstellen Sie einen Tresor und ein Zertifikat](../../key-vault/certificates/quick-create-portal.md) (selbstsigniert oder importiert).

2. Gewähren Sie dem Azure Arc-fähigen Server Zugriff auf das Zertifikatgeheimnis. Wenn Sie die [rollenbasierte Zugriffssteuerung (Vorschau)](../../key-vault/general/rbac-guide.md) verwenden, suchen Sie den Namen der Azure Arc-Ressource, und weisen Sie sie der Rolle **Key Vault-Geheimnisbenutzer (Vorschau)** zu. Wenn Sie die [Key Vault-Zugriffsrichtlinie](../../key-vault/general/assign-access-policy-portal.md) verwenden, weisen Sie der vom System zugewiesenen Identität der Azure Arc-Ressource die **Get**-Berechtigungen des Geheimnisses zu.

### <a name="connected-machine-agent"></a>Connected Machine-Agent

Überprüfen Sie, ob Ihr Computer mit den [unterstützten Versionen](agent-overview.md#supported-operating-systems) des Windows- und Linux-Betriebssystems für den Azure Connected Machine-Agent übereinstimmt.

Die Mindestversion des Connected Machine-Agents, die mit dieser Funktion unter Windows und Linux unterstütz wird, ist Version 1.0.

Informationen für das Upgrade Ihres Computers auf die erforderliche Agent-Version finden Sie unter [Agent aktualisieren](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Nächste Schritte

Sie können VM-Erweiterungen über die [Azure-Befehlszeilenschnittstelle](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), das [Azure-Portal](manage-vm-extensions-portal.md) oder [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md) bereitstellen, verwalten und entfernen.
