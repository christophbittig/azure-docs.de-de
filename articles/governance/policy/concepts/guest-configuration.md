---
title: Grundlegende Informationen zum Gastkonfigurationsfeature von Azure Policy
description: Hier erfahren Sie, wie Azure Policy mithilfe des Gastkonfigurationsfeatures Einstellungen in VMs überwacht und konfiguriert.
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: 6a40469b6cd391672ba953bac37402285ac4a097
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040414"
---
# <a name="understand-the-guest-configuration-feature-of-azure-policy"></a>Grundlegende Informationen zum Gastkonfigurationsfeature von Azure Policy

Das Gastkonfigurationsfeature von Azure Policy bietet native Funktionen zum Überprüfen oder Konfigurieren von Betriebssystemeinstellungen als Code, sowohl für Computer, die in Azure ausgeführt werden, als auch für hybride [Computer mit Arc-Unterstützung](../../../azure-arc/servers/overview.md).
Das Feature kann direkt pro Computer oder in großem Umfang von Azure Policy orchestriert verwendet werden.

Konfigurationsressourcen in Azure sind als [Erweiterungsressourcen](../../../azure-resource-manager/management/extension-resource-types.md) konzipiert.
Sie können sich jede Konfiguration als zusätzlichen Satz von Eigenschaften für den Computer vorstellen. Konfigurationen können Einstellungen enthalten wie:

- Betriebssystemeinstellungen
- Die Konfiguration oder das Vorhandensein der Anwendung
- Umgebungseinstellungen

Konfigurationen unterscheiden sich von Richtliniendefinitionen. Die Gastkonfiguration nutzt Azure Policy, um Computern Konfigurationen dynamisch zuzuweisen. Sie können Computern Konfigurationen auch [manuell](guest-configuration-assignments.md#manually-creating-guest-configuration-assignments) oder mithilfe anderer Azure-Dienste wie [AutoManage](../../../automanage/automanage-virtual-machines.md) zuweisen.

Beispiele für jedes Szenario sind in der folgenden Tabelle aufgeführt.

| type | BESCHREIBUNG | Beispielstory |
| - | - | - |
| [Konfigurationsverwaltung](guest-configuration-assignments.md) | Sie möchten eine vollständige Darstellung eines Servers als Code in der Quellcodeverwaltung. Die Bereitstellung sollte Eigenschaften des Servers (Größe, Netzwerk, Speicher) und die Konfiguration von Betriebssystem- und Anwendungseinstellungen enthalten. | "Dieser Computer sollte ein Webserver sein, der zum Hosten meiner Websete konfiguriert ist." |
| [Compliance](../assign-policy-portal.md) | Sie möchten Einstellungen für alle Computer im Bereich überwachen oder bereitstellen, entweder reaktiv auf vorhandenen Computern oder proaktiv auf neuen Computern, wenn sie bereitgestellt werden. | "Alle Computer sollten TLS 1.2 verwenden. Überwachen Sie vorhandene Computer, damit ich Änderungen an den benötigten Stellen auf kontrollierte Weise und bedarfsorientiert freigeben kann. Erzwingen Sie für neue Computer die Einstellung, wenn sie bereitgestellt werden." |

Die Ergebnisse der einzelnen Konfigurationen können entweder auf der Seite [Gastzuweisungen](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) angezeigt werden, oder wenn die Konfiguration durch eine Azure Policy-Zuweisung orchestriert wird, indem Sie auf der Seite [Compliance details](../how-to/determine-non-compliance.md#view-configuration-assignment-details-at-scale) auf den Link „Zuletzt ausgewertete Ressource“ klicken.

[Für dieses Dokument ist ein Video zur exemplarischen Vorgehensweise verfügbar](https://youtu.be/t9L8COY-BkM). (Update in Kürze verfügbar)

## <a name="enable-guest-configuration"></a>Aktivieren der Gastkonfiguration

Sehen Sie sich die folgenden ausführlichen Informationen an, um den Status der Computer in Ihrer Umgebung zu verwalten, einschließlich Computern in Azure und Arc-fähiger Server.

## <a name="resource-provider"></a>Ressourcenanbieter

Um das Gastkonfigurationsfeature von Azure Policy verwenden zu können, müssen Sie den Ressourcenanbieter `Microsoft.GuestConfiguration` registrieren. Wenn die Zuweisung einer Gastkonfigurationsrichtlinie über das Portal erfolgt oder das Abonnement in Azure Security Center registriert ist, wird der Ressourcenanbieter automatisch registriert. Hierfür können Sie über das [Portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), die [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) oder [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) manuell registrieren.

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Bereitstellen von Anforderungen für virtuelle Azure-Computer

Für die Verwaltung von Einstellungen innerhalb eines Computers ist eine [VM-Erweiterung](../../../virtual-machines/extensions/overview.md) aktiviert, und der Computer muss über eine systemseitig verwaltete Identität verfügen. Die Erweiterung lädt die richtige Gastkonfigurationszuweisung sowie die entsprechenden Abhängigkeiten herunter. Die Identität wird verwendet, um den Computer zu authentifizieren, wenn Lese- und Schreibvorgänge im Gastkonfigurationsdienst ausgeführt werden. Bei Arc-fähigen Servern wird die Erweiterung nicht benötigt, da sie bereits im Arc Connected Machine-Agent enthalten ist.

> [!IMPORTANT]
> Für die Verwaltung von virtuellen Azure-Computern sind die Gastkonfigurationserweiterung und eine verwaltete Identität erforderlich.

Wenn Sie die Erweiterung im großen Stil auf vielen Computern bereitstellen möchten, weisen Sie die Richtlinieninitiative `Deploy prerequisites to enable guest configuration policies on virtual machines`
einer Verwaltungsgruppe, einem Abonnement oder einer Ressourcengruppe mit den Computern zu, die Sie verwalten möchten.

Wenn Sie die Erweiterung und die verwaltete Identität lieber auf einem einzelnen Computer bereitstellen möchten, befolgen Sie den jeweiligen Leitfaden:

- [Übersicht über die Azure Policy-Gastkonfigurationserweiterung](../../../virtual-machines/extensions/guest-configuration.md)
- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

Für die Verwendung von Gastkonfigurationspaketen, die Konfigurationen anwenden, ist die Azure-VM-Gastkonfigurationserweiterung, Version **1.29.24** oder höher, erforderlich.

### <a name="limits-set-on-the-extension"></a>Für die Erweiterung festgelegte Grenzwerte

Um die Auswirkungen der Erweiterung auf die auf dem Computer ausgeführten Anwendungen zu beschränken, darf der Gastkonfigurations-Agent höchstens 5 % der CPU auslasten. Diese Einschränkung gilt sowohl für integrierte als auch für angepasste Definitionen. Dies gilt auch für den Gastkonfigurationsdienst im Arc Connected Machine-Agent.

### <a name="validation-tools"></a>Überprüfungstools

Auf dem Computer verwendet der Gastkonfigurations-Agent für die Ausführung von Tasks lokale Tools.

In der folgenden Tabelle sind die lokalen Tools aufgeführt, die unter den jeweiligen unterstützten Betriebssystemen verwendet werden. Bei integrierten Inhalten werden diese Tools in der Gastkonfiguration automatisch geladen.

|Betriebssystem|Überprüfungstool|Notizen|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview), Version 3| Wird mittels Sideloading in einen Ordner quergeladen, der nur von Azure Policy verwendet wird. Verursacht keinen Konflikt mit Windows PowerShell DSC. PowerShell Core wird nicht zum Systempfad hinzugefügt.|
|Linux|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview), Version 3| Wird mittels Sideloading in einen Ordner quergeladen, der nur von Azure Policy verwendet wird. PowerShell Core wird nicht zum Systempfad hinzugefügt.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/) | Installiert Chef InSpec (Version 2.2.61) am Standardspeicherort und wird dem Systempfad hinzugefügt. Abhängigkeiten für das InSpec-Paket einschließlich Ruby und Python werden ebenfalls installiert. |

### <a name="validation-frequency"></a>Validierungshäufigkeit

Der Gastkonfigurations-Agent überprüft alle 5 Minuten, ob Gastzuweisungen hinzugefügt oder geändert wurden. Nachdem eine Gastzuweisung empfangen wurde, werden die Einstellungen für diese Konfiguration alle 15 Minuten erneut überprüft. Wenn mehrere Konfigurationen zugewiesen sind, werden diese einzeln nacheinander ausgewertet. Konfigurationen mit langer Ausführungsdauer wirken sich auf das Intervall für alle Konfigurationen aus, da die nächste Konfiguration erst ausgeführt wird, wenn die vorherige abgeschlossen ist.

Die Ergebnisse werden nach Abschluss der Überprüfung an den Gastkonfigurationsdienst gesendet.
Wenn ein [Auswertungstrigger](../how-to/get-compliance-data.md#evaluation-triggers) für Richtlinien auftritt, wird der Status des Computers an den Ressourcenanbieter der Gastkonfiguration übermittelt. Dieses Update bewirkt, dass Azure Policy die Azure Resource Manager-Eigenschaften auswertet. Bei einer bei Bedarf ausgelösten Auswertung durch Azure Policy wird der aktuelle Wert beim Ressourcenanbieter der Gastkonfiguration abgerufen. Es wird jedoch keine neue Aktivität auf dem Computer ausgelöst. Der Status wird anschließend an Azure Resource Graph übermittelt.

## <a name="supported-client-types"></a>Unterstützte Clienttypen

Richtliniendefinitionen für die Gastkonfiguration umfassen neue Versionen. Ältere Versionen von Betriebssystemen, die im Azure Marketplace verfügbar sind, sind ausgeschlossen, wenn der Gastkonfigurationsclient nicht kompatibel ist. In der folgenden Tabelle werden die für Azure-Images unterstützten Betriebssysteme aufgeführt.
Die Angabe „.x“ steht symbolisch für neue Nebenversionen von Linux-Distributionen.

|Herausgeber|Name|Versionen|
|-|-|-|
|Amazon|Linux|2|
|Canonical|Ubuntu Server|14.04 - 20.x|
|Credativ|Debian|8 - 10.x|
|Microsoft|Windows Server|2012–2019|
|Microsoft|Windows-Client|Windows 10|
|Oracle|Oracle-Linux|7.x–8.x|
|OpenLogic|CentOS|7.3 -8.x|
|Red Hat|Red Hat Enterprise Linux\*|7.4 - 8.x|
|SUSE|SLES|12 SP3-SP5, 15.x|

\* Red Hat CoreOS wird nicht unterstützt.

Benutzerdefinierte VM-Images werden von Richtliniendefinitionen für die Gastkonfiguration unterstützt, sofern es sich um eines der Betriebssysteme in der obigen Tabelle handelt.

## <a name="network-requirements"></a>Netzwerkanforderungen

Virtuelle Computer in Azure können entweder ihren lokalen Netzwerkadapter oder eine private Verbindung verwenden, um mit dem Gastkonfigurationsdienst zu kommunizieren.

Azure Arc-Computer stellen mithilfe der lokalen Netzwerkinfrastruktur eine Verbindung her, um Azure-Dienste zu erreichen und den Compliancestatus zu melden.

### <a name="communicate-over-virtual-networks-in-azure"></a>Kommunizieren über virtuelle Netzwerke in Azure

Für die Kommunikation mit dem Ressourcenanbieter der Gastkonfiguration in Azure benötigen Computer ausgehenden Zugriff auf Azure-Rechenzentren über Port **443**. Wenn ein Netzwerk in Azure keinen ausgehenden Datenverkehr zulässt, müssen Ausnahmen über [Netzwerksicherheitsgruppen](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)-Regeln konfiguriert werden. Die [Diensttags](../../../virtual-network/service-tags-overview.md) „AzureArcInfrastructure“ und „Storage“ können verwendet werden, um auf den Gastkonfigurationsdienst und den Storage-Dienst zu verweisen, damit die [Liste der IP-Adressbereiche](https://www.microsoft.com/download/details.aspx?id=56519) für Azure-Rechenzentren nicht manuell geführt werden muss. Beide Tags sind erforderlich, da Inhaltspakete für Gastkonfigurationen von Azure Storage gehostet werden.

### <a name="communicate-over-private-link-in-azure"></a>Kommunizieren über Private Link in Azure

Für die Kommunikation mit dem Gastkonfigurationsdienst können virtuelle Computer eine [private Verbindung](../../../private-link/private-link-overview.md) verwenden. Wenden Sie ein Tag mit dem Namen `EnablePrivateNetworkGC` und dem Wert `TRUE` an, um dieses Feature zu aktivieren. Das Tag kann vor oder nach der Anwendung der Richtliniendefinitionen der Gastkonfiguration auf den Computer angewendet werden.

Der Datenverkehr wird mithilfe der [virtuellen öffentlichen IP-Adresse](../../../virtual-network/what-is-ip-address-168-63-129-16.md) von Azure weitergeleitet, um einen sicheren, authentifizierten Kanal mit Azure-Plattformressourcen einzurichten.

### <a name="azure-arc-enabled-servers"></a>Server mit Azure Arc-Unterstützung

Knoten, die sich außerhalb von Azure befinden und mit denen eine Verbindung über Azure Arc besteht, benötigen Konnektivität mit dem Gastkonfigurationsdienst. Details zu den Netzwerk- und Proxyanforderungen finden Sie in der [Azure Arc-Dokumentation](../../../azure-arc/servers/overview.md).

Lassen Sie für Arc-fähige Server in privaten Rechenzentren Datenverkehr mithilfe der folgenden Muster zu:

- Port: Für ausgehenden Zugriff auf das Internet ist nur TCP 443 erforderlich
- Globale URL: `*.guestconfiguration.azure.com`

## <a name="assigning-policies-to-machines-outside-of-azure"></a>Zuweisen von Richtlinien zu Computern außerhalb von Azure

Die für die Gastkonfiguration verfügbaren Überwachungsrichtliniendefinitionen umfassen den Ressourcentyp **Microsoft.HybridCompute/machines**. Alle Computer, die in [Azure Arc für Server](../../../azure-arc/servers/overview.md) integriert sind und sich im Geltungsbereich der Richtlinienzuweisung befinden, werden automatisch eingeschlossen.

## <a name="managed-identity-requirements"></a>Anforderungen für verwaltete Identitäten

Durch die Richtliniendefinitionen in der Initiative _Voraussetzungen zum Aktivieren der Gastkonfigurationsrichtlinien auf VMs bereitstellen_ wird eine systemseitig zugewiesene verwaltete Identität aktiviert, sofern noch keine solche vorhanden ist. Die Initiative enthält zwei Richtliniendefinitionen, durch die die Identitätserstellung verwaltet wird. Die IF-Bedingungen in den Richtliniendefinitionen gewährleisten das korrekte Verhalten basierend auf dem aktuellen Zustand der Computerressource in Azure.

Wenn der Computer derzeit über keine verwalteten Identitäten verfügt, ist die effektive Richtlinie: [Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs ohne Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e).

Wenn der Computer derzeit über eine benutzerseitig zugewiesene Systemidentität verfügt, ist die effektive Richtlinie: [Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs mit einer benutzerseitig zugewiesenen verwalteten Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6).

## <a name="availability"></a>Verfügbarkeit

Kunden, die eine hochverfügbare Lösung entwerfen, sollten die Anforderungen an die Redundanzplanung für [virtuelle Computer](../../../virtual-machines/availability.md) berücksichtigen, da Gastzuweisungen Erweiterungen von Computerressourcen in Azure sind. Wenn Gastzuweisungsressourcen in einer Azure-Region bereitgestellt werden, die [gekoppelt](../../../best-practices-availability-paired-regions.md) ist, sind Gastzuweisungsberichte verfügbar, solange mindestens eine Region des Paars verfügbar ist. Wenn die Azure-Region nicht gekoppelt und nicht mehr verfügbar ist, ist es erst nach der Wiederherstellung der Region möglich, auf Berichte für eine Gastzuweisung zuzugreifen.

Wenn Sie eine Architektur für hochverfügbare Anwendungen in Betracht ziehen, insbesondere wenn virtuelle Computer in [Verfügbarkeitsgruppen](../../../virtual-machines/availability.md#availability-sets) hinter einer Lastenausgleichslösung bereitgestellt werden, um Hochverfügbarkeit zu gewährleisten, wird empfohlen, allen Computern in der Lösung dieselben Richtliniendefinitionen mit denselben Parametern zu zuweisen. Wenn möglich, würde eine einzelne Richtlinienzuweisung, die alle Computer umfasst, den geringsten Verwaltungsaufwand bieten.

Stellen Sie für Computer, die von [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) geschützt werden, sicher, dass die Computer an einem sekundären Standort innerhalb des Bereichs von Azure Policy-Zuweisungen für dieselben Definitionen liegen, die dieselben Parameterwerte wie Computer am primären Standort verwenden.

## <a name="data-residency"></a>Datenresidenz

Die Gastkonfiguration speichert/verarbeitet Kundendaten. Standardmäßig werden Kundendaten in die [gekoppelte Region](../../../best-practices-availability-paired-regions.md) repliziert.
Für eine einzelne gebietsansässige Region werden alle Kundendaten in der Region gespeichert und verarbeitet.

## <a name="troubleshooting-guest-configuration"></a>Problembehandlung bei der Gastkonfiguration

Weitere Informationen zur Problembehandlung bei der Gastkonfiguration finden Sie unter [Problembehandlung mit Azure Policy](../troubleshoot/general.md).

### <a name="multiple-assignments"></a>Mehrere Zuweisungen

Aufgrund der Richtliniendefinitionen für die Gastkonfiguration kann die gleiche Gastzuweisung derzeit lediglich einmal pro Computer zugewiesen werden, auch wenn bei der Richtlinienzuweisung verschiedene Parameter verwendet werden.

### <a name="assignments-to-azure-management-groups"></a>Zuweisungen zu Azure-Verwaltungsgruppen

Azure Policy-Definitionen in der Kategorie „Gastkonfiguration“ können Verwaltungsgruppen nur zugewiesen werden, wenn die Auswirkung „AuditIfNotExists“ ist. Bei Richtliniendefinitionen mit der Auswirkung „DeployIfNotExists“ wird das Zuweisen für Verwaltungsgruppen nicht unterstützt.

### <a name="client-log-files"></a>Protokolldateien des Clients

Die Gastkonfigurationserweiterung schreibt Protokolldateien an die folgenden Speicherorte:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Azure-VM: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Arc-fähiger Server: `/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>Remotesammeln von Protokollen

Im ersten Schritt bei der Problembehandlung von Konfigurationen oder Modulen der Gastkonfiguration sollten die Cmdlets entsprechend den unter [Testen von Gastkonfigurations-Paketartefakten](../how-to/guest-configuration-create-test.md) beschriebenen Schritten ausgeführt werden.
Wenn dies nicht erfolgreich ist, kann das Sammeln von Clientprotokollen helfen, Probleme zu diagnostizieren.

#### <a name="windows"></a>Windows

Erfassen Sie Informationen aus Protokolldateien mithilfe des Azure-VM-Befehls [Ausführen](../../../virtual-machines/windows/run-command.md), wobei das folgende PowerShell-Beispiel hilfreich sein kann.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Erfassen Sie Informationen aus Protokolldateien mithilfe des Azure VM-Befehls [Ausführen](../../../virtual-machines/linux/run-command.md), wobei das folgende Bash-Beispiel hilfreich sein kann.

```bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="agent-files"></a>Agent-Dateien

Der Gastkonfigurations-Agent lädt Inhaltspakete auf einen Computer herunter und extrahiert deren Inhalte. Um zu überprüfen, welche Inhalte heruntergeladen und gespeichert wurden, sehen Sie sich die unten angegebenen Ordnerspeicherorte an.

Windows: `c:\programdata\guestconfig\configuration`

Linux: `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>Gastkonfigurationsbeispiele

Beispiele für integrierte Gastkonfigurationsrichtlinien finden Sie unter:

- [Integrierte Richtliniendefinitionen: Gastkonfiguration](../samples/built-in-policies.md#guest-configuration)
- [Integrierte Initiativen: Gastkonfiguration](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy-Beispiele: GitHub-Repository](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Nächste Schritte

- Richten Sie eine [Entwicklungsumgebung](../how-to/guest-configuration-create-setup.md) für ein benutzerdefiniertes Gastkonfigurationspaket ein.
- [Erstellen Sie ein Paketartefakt](../how-to/guest-configuration-create.md) für Gastkonfigurationen.
- [Testen Sie das Paketartefakt](../how-to/guest-configuration-create-test.md) in Ihrer Entwicklungsumgebung.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy-Definition](../how-to/guest-configuration-create-definition.md) für die Verwaltung Ihrer Umgebung im großen Stil.
- [Weisen Sie Ihre benutzerdefinierte Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Informieren Sie sich, wie Sie die [Compliancedetails für die Richtlinienzuweisungen der Gastkonfiguration](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
