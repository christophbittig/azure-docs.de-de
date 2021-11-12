---
title: Aktivieren von VM-Erweiterungen über das Azure-Portal
description: In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal VM-Erweiterungen auf Azure Arc-fähigen Servern bereitstellen, die in Hybrid Cloud-Umgebungen ausgeführt werden.
ms.date: 10/15/2021
ms.topic: conceptual
ms.openlocfilehash: b3bb6e6b6dcb70dc229dc01a02d04cf195c89700
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423905"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Aktivieren von Azure-VM-Erweiterungen über das Azure-Portal

In diesem Artikel erfahren Sie, wie Sie das Azure-Portal verwenden, um Azure-VM-Erweiterungen, die von Servern mit Azure Arc-Unterstützung unterstützt werden, auf einem Linux- oder Windows-Hybridcomputer bereitzustellen, zu aktualisieren oder zu deinstallieren.

> [!NOTE]
> Die Key Vault-VM-Erweiterung unterstützt keine Bereitstellung über das Azure-Portal, sondern lediglich über die Azure-Befehlszeilenschnittstelle, Azure PowerShell oder eine Azure Resource Manager-Vorlage.

> [!NOTE]
> Das Bereitstellen und Verwalten von VM-Erweiterungen auf Azure-VMs wird auf Servern mit Azure Arc-Unterstützung nicht unterstützt. Informationen zu virtuellen Azure-Computern finden Sie im Artikel [Erweiterungen und Features für virtuelle Azure-Computer](../../virtual-machines/extensions/overview.md).

## <a name="enable-extensions"></a>Aktivieren von Erweiterungen

VM-Erweiterungen können über das Azure-Portal auf Ihren durch einen Azure Arc-fähigen Server verwalteten Computer angewendet werden.

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

2. Navigieren Sie im Portal zu **Server - Azure Arc**, und wählen Sie in der Liste Ihren Hybridcomputer aus.

3. Wählen Sie **Erweiterungen** und dann **Hinzufügen** aus. Wählen Sie die Erweiterung aus, die Sie aus der Liste verfügbarer Erweiterungen erhalten, und befolgen Sie die Anweisungen im Assistenten. In diesem Beispiel stellen wir die Log Analytics-VM-Erweiterung bereit.

    ![Auswählen der VM-Erweiterung für den ausgewählten Computer](./media/manage-vm-extensions/add-vm-extensions.png)

    Im folgenden Beispiel wird die Installation der Log Analytics-VM-Erweiterung über das Azure-Portal gezeigt:

    ![Installieren der Log Analytics VM-Erweiterung](./media/manage-vm-extensions/mma-extension-config.png)

    Um die Installation abzuschließen, müssen Sie die Arbeitsbereichs-ID und den Primärschlüssel angeben. Wenn Sie mit dem Ermitteln dieser Informationen nicht vertraut sind, lesen Sie [Abrufen von Arbeitsbereichs-ID und Schlüssel](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

4. Nach dem Bestätigen der bereitgestellten erforderlichen Informationen wählen Sie **Überprüfen + Erstellen** aus. Es wird eine Zusammenfassung der Bereitstellung angezeigt, und Sie können den Status der Bereitstellung überprüfen.

>[!NOTE]
>Zwar können mehrere Erweiterungen in einem Batch zusammengefasst und verarbeitet werden, die Installation erfolgt jedoch nacheinander. Sobald die Installation der ersten Erweiterung abgeschlossen ist, wird die Installation der nächsten Erweiterung versucht.

## <a name="list-extensions-installed"></a>Auflisten der Installierten Erweiterungen

Sie können eine Liste der VM-Erweiterungen auf dem Azure Arc-fähigen Server im Azure-Portal abrufen. Führen Sie zu Ihrer Anzeige die folgenden Schritte aus.

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

2. Navigieren Sie im Portal zu **Server - Azure Arc**, und wählen Sie in der Liste Ihren Hybridcomputer aus.

3. Wählen Sie **Erweiterungen** aus, und die Liste der installierten Erweiterungen wird zurückgegeben.

    :::image type="content" source="media/manage-vm-extensions/list-vm-extensions.png" alt-text="Auflisten der auf dem ausgewählten Computer bereitgestellten VM-Erweiterungen" border="true":::

## <a name="upgrade-extensions"></a>Upgraden von Erweiterungen

Wenn eine neue Version einer unterstützten Erweiterung veröffentlicht wird, können Sie die Erweiterung auf diese neueste Version upgraden. Azure Arc-fähige Server zeigen im Azure-Portal ein Banner an, wenn Sie zu Azure Arc-fähigen Servern navigieren. Diese Banner informieren Sie darüber, dass Upgrades für eine oder mehrere Erweiterungen verfügbar sind, die auf einem Computer installiert sind. Wenn Sie die Liste der installierten Erweiterungen für einen ausgewählten Azure Arc-fähigen Server anzeigen, sehen Sie eine Spalte mit der Bezeichnung **Update verfügbar**. Wenn eine neuere Version einer Erweiterung veröffentlicht wird, zeigt der Wert **Update verfügbar** für diese Erweiterung **Ja** an.

>[!NOTE]
>Obwohl im Azure-Portal das Wort **Update** für diesen Vorgang verwendet wird, spiegelt dieses das Verhalten des Vorgangs nicht genau wieder. Für Erweiterungen wird ein Upgrade durchgeführt, indem eine neuere Version der derzeit installierten Erweiterung auf dem Computer oder dem Server installiert wird.

Eine Erweiterung auf die neueste Version zu upgraden beeinflusst nicht die Konfiguration dieser Erweiterung. Sie müssen keine Konfigurationsinformationen für die von Ihnen geupgradeten Erweiterungen neu anpassen.

:::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-status.png" alt-text="Auflisten des Updatestatus der VM-Erweiterung" border="true":::

Sie können ein Upgrade für eine Erweiterung durchführen oder mehrere Erweiterungen auswählen, die für ein Upgrade im Azure-Portal berechtigt sind, indem Sie die folgenden Schritte ausführen.

> [!NOTE]
> Derzeit können Sie Erweiterungen nur über das Azure-Portal upgraden. Das Ausführen dieses Vorgangs über die Azure CLI oder mit einer Azure Resource Manager-Vorlage wird derzeit nicht unterstützt.

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

2. Navigieren Sie im Portal zu **Server - Azure Arc**, und wählen Sie in der Liste Ihren Hybridcomputer aus.

3. Wählen Sie **Erweiterungen** aus, und überprüfen Sie den Status der Erweiterungen in der Spalte **Update verfügbar**.

Sie können eine Erweiterung auf eine von drei Arten upgraden:

* Wählen Sie eine Erweiterung aus der Liste der installierten Erweiterungen aus, und wählen Sie unter den Eigenschaften der Erweiterung die Option **Aktualisieren** aus.

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-from-extension.png" alt-text="Upgraden der Erweiterung aus der Liste der ausgewählten Erweiterung" border="true":::

* Wählen Sie die Erweiterung aus der Liste der installierten Erweiterungen aus, und wählen Sie oben auf der Seite die Option **Aktualisieren** aus.

* Wählen Sie aus der Liste der installierten Erweiterungen eine oder mehrere Erweiterungen aus, die für ein Upgrade berechtigt sind, und wählen Sie dann die Option **Aktualisieren** aus.

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-selected.png" alt-text="Aktualisieren der ausgewählten Erweiterung" border="true":::

## <a name="remove-extensions"></a>Entfernen von Erweiterungen

Sie können über das Azure-Portal Erweiterungen von einem Azure Arc-fähigen Server entfernen. Führen Sie dann die folgenden Schritte aus, um eine Erweiterung zu entfernen.

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

2. Navigieren Sie im Portal zu **Server - Azure Arc**, und wählen Sie in der Liste Ihren Hybridcomputer aus.

3. Wählen Sie **Erweiterungen** aus, und wählen Sie dann eine Erweiterung aus der Liste der installierten Erweiterungen aus.

4. Wählen Sie **Deinstallieren** aus. Wenn Sie zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus, um fortzufahren.

## <a name="next-steps"></a>Nächste Schritte

- Sie können VM-Erweiterungen über die [Azure-Befehlszeilenschnittstelle](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md) oder [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md) bereitstellen, verwalten und entfernen.

- Informationen zur Problembehandlung finden Sie im [Problembehandlungs-Handbuch für VM-Erweiterungen](troubleshoot-vm-extensions.md).
