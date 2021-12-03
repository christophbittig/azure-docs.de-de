---
title: Automatisches Erweiterungsupgrade (Vorschau) für Server mit Azure Arc-Unterstützung
description: Erfahren Sie, wie Sie das automatische Erweiterungsupgrade (Vorschauversion) für Ihre Server mit Azure Arc-Unterstützung aktivieren.
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: aea00dbe8609d0a8481d9ca742c12e6d0215f189
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132033759"
---
# <a name="automatic-extension-upgrade-preview-for-azure-arc-enabled-servers"></a>Automatisches Erweiterungsupgrade (Vorschau) für Server mit Azure Arc-Unterstützung

Automatisches Erweiterungsupgrade (Vorschauversion) ist für Server mit Azure Arc-Unterstützung verfügbar, auf denen unterstützte VM-Erweiterungen installiert sind. Wenn das automatische Erweiterungsupgrade (Vorschau) auf einem Computer aktiviert ist, wird die Erweiterung automatisch aktualisiert, sobald der Erweiterungsherausgeber eine neue Version für diese Erweiterung freigibt.

 Das automatische Erweiterungsupgrade verfügt über die folgenden Features:

- Sie können automatische Upgrades jederzeit abonnieren und kündigen.
- Jede unterstützte Erweiterung wird einzeln registriert, und Sie können auswählen, welche Erweiterungen automatisch aktualisiert werden sollen.
- Wird in allen Regionen der öffentlichen Cloud unterstützt.

> [!NOTE]
> In diesem Release wird nur die Azure CLI für die Konfiguration des automatischen Erweiterungsupgrades unterstützt.

## <a name="how-does-automatic-extension-upgrade-work"></a>Wie funktioniert das automatische Erweiterungsupgrade?

Der Erweiterungsupgradeprozess ersetzt die vorhandene, von Servern mit Azure Arc-Unterstützung unterstützte Erweiterungsversion auf einer Azure-VM durch eine neue Version derselben Erweiterung, wenn diese vom Erweiterungsherausgeber veröffentlicht wird.

Ein fehlerhaftes Erweiterungsupdate wird automatisch wiederholt. Ein Wiederholungsversuch wird regelmäßig nach einigen Tagen ohne Benutzereingriff automatisch durchgeführt.

### <a name="availability-first-updates"></a>Updates nach Verfügbarkeit

Mit dem Verfügbarkeitsmodell für plattformorchestrierte Updates wird sichergestellt, dass die Verfügbarkeitskonfigurationen in Azure über mehrere Verfügbarkeitsstufen hinweg berücksichtigt werden.

Für eine Gruppe von Servern mit Arc-Unterstützung, die einem Update unterzogen wird, orchestriert die Azure-Plattform Updates nach dem Modell, das unter [Upgrade der Automation-Erweiterung](../../virtual-machines/automatic-extension-upgrade.md#availability-first-updates) beschrieben ist. Es gibt jedoch einige beachtenswerte Unterschiede zwischen Servern mit Arc-Unterstützung und Azure-VMs:

**Über Regionen hinweg:**

- Geografisch gekoppelte Regionen sind nicht anwendbar.

**Innerhalb einer Region:**

- Verfügbarkeitszonen sind nicht anwendbar.
- Computer werden nach Best Effort in Batches erfasst, um gleichzeitige Updates für alle Computer zu vermeiden, die bei Servern mit Arc-Unterstützung in einem Abonnement registriert sind.  

## <a name="supported-extensions"></a>Unterstützte Erweiterungen

Das automatische Erweiterungsupgrade (Vorschau) unterstützt die folgenden Erweiterungen (weitere werden regelmäßig hinzugefügt):

- Azure Monitor-Agent – Linux und Windows
- Azure Security-Agent – Linux und Windows
- Dependency-Agent – Linux und Windows
- Key Vault-Erweiterung – nur Linux
- Log Analytics-Agent (OMS-Agent) – nur Linux

## <a name="enabling-automatic-extension-upgrade-preview"></a>Aktivieren des automatischen Erweiterungsupgrades (Vorschau)

Um das automatische Erweiterungsupgrade (Vorschau) für eine Erweiterung zu aktivieren, müssen Sie sicherstellen, dass die Eigenschaft `enable-auto-upgrade` auf `true` festgelegt ist und jeder Erweiterungsdefinition einzeln hinzugefügt wird.

Verwenden Sie das Cmdlet [az connectedmachine extension](/cli/azure/connectedmachine/extension) mit den Parametern `--name`, `--machine-name`, `--enable-auto-upgrade` und `--resource-group`.

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade true
```

Führen Sie den folgenden Befehl aus, um den Status des automatischen Erweiterungsupgrades (Vorschau) für alle Erweiterungen auf einem Server mit Arc-Unterstützung zu überprüfen:

```azurecli
az connectedmachine extension list --resource-group resourceGroupName --machine-name machineName --query "[].{Name:name, AutoUpgrade:properties.enableAutoUpgrade}" --output table
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Erweiterungsupgrades mit mehreren Erweiterungen

Auf einem Computer, der von Servern mit Arc-Unterstützung verwaltet wird, können mehrere Erweiterungen mit aktiviertem automatischem Erweiterungsupgrade vorhanden sein. Derselbe Computer kann auch über andere Erweiterungen verfügen, ohne dass das automatische Erweiterungsupgrade aktiviert ist.

Wenn für einen Computer mehrere Erweiterungsupgrades verfügbar sind, werden die Upgrades möglicherweise in einem Batch zusammengefasst, aber jedes Erweiterungsupgrade wird einzeln auf einem Computer angewendet. Ein Fehler bei einer Erweiterung hat keine Auswirkung auf die anderen zu upgradenden Erweiterungen. Wenn z. B. zwei Erweiterungen für ein Upgrade geplant sind und das erste Erweiterungsupgrade fehlschlägt, wird die zweite Erweiterung trotzdem aktualisiert.

## <a name="disable-automatic-extension-upgrade"></a>Deaktivieren des automatischen Erweiterungsupgrades

Um das automatische Erweiterungsupgrade (Vorschau) für eine Erweiterung zu deaktivieren, müssen Sie sicherstellen, dass die Eigenschaft `enable-auto-upgrade` auf `false` festgelegt ist und jeder Erweiterungsdefinition einzeln hinzugefügt wird.

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Verwenden Sie das Cmdlet [az connectedmachine extension](/cli/azure/connectedmachine/extension) mit den Parametern `--name`, `--machine-name`, `--enable-auto-upgrade` und `--resource-group`.

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade false
```

## <a name="next-steps"></a>Nächste Schritte

- Sie können VM-Erweiterungen über die [Azure-Befehlszeilenschnittstelle](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md) oder [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md) bereitstellen, verwalten und entfernen.

- Informationen zur Problembehandlung finden Sie im [Problembehandlungs-Handbuch für VM-Erweiterungen](troubleshoot-vm-extensions.md).
