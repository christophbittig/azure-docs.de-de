---
title: Konfigurieren des automatischen Herunterfahrens von VMs in Azure Lab Services
description: In diesem Artikel wird beschrieben, wie Sie das automatische Herunterfahren von VMs im Lab-Konto konfigurieren.
ms.topic: how-to
ms.date: 08/17/2020
ms.openlocfilehash: 40b4f3971d2fd9fc84337bb38d49d04212b3f5ba
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228523"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Konfigurieren des automatischen Herunterfahrens von virtuellen Computern für ein Labkonto

Sie können mehrere Features zur Kostenkontrolle beim automatischen Herunterfahren aktivieren, um proaktiv zusätzliche Kosten zu verhindern, wenn die virtuellen Computer nicht aktiv genutzt werden. Die Kombination der folgenden drei Features zum automatischen Herunterfahren und Trennen deckt den größten Teil der Fälle ab, in denen Benutzer die Ausführung der virtuellen Computer versehentlich nicht beenden:
 
- Automatisches Trennen von Benutzern von virtuellen Computern, die vom Betriebssystem als im Leerlauf erkannt werden
- Automatisches Herunterfahren virtueller Computer, wenn Benutzer die Verbindung trennen
- Automatisches Herunterfahren von virtuellen Computern, die gestartet wurden, mit denen jedoch von den Benutzern keine Verbindung hergestellt wurde.

Weitere Informationen zu den Features zum automatischen Herunterfahren finden Sie im Abschnitt [Maximieren der Kostenkontrolle durch Einstellungen zum automatischen Herunterfahren](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

> [!IMPORTANT]
> Linux-Labs unterstützen das automatische Herunterfahren nur, wenn Benutzer die Verbindung trennen und wenn VMs gestartet werden, Benutzer aber keine Verbindung herstellen.  Die Unterstützung variiert auch abhängig von [bestimmten Distributionen und Versionen von Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions).  Einstellungen zum Herunterfahren werden vom Image [Data Science Virtual Machine – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) nicht unterstützt. 

## <a name="enable-automatic-shutdown"></a>Aktivieren des automatischen Herunterfahrens

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zur Seite **Labkonto**.
1. Wählen Sie im linken Menü **Labeinstellungen** aus.
1. Wählen Sie die Einstellungen für das automatische Herunterfahren aus, die für Ihr Szenario geeignet sind.  

    > [!div class="mx-imgBorder"]
    > ![Einstellung für automatisches Herunterfahren in einem Labkonto](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    Diese Einstellungen gelten für alle im Labkonto erstellten Labs. Ein Lab-Ersteller (Lehrer/Dozent) kann diese Einstellung auf Lab-Ebene außer Kraft setzen. Die Änderung an dieser Einstellung im Labkonto wirkt sich nur auf Labs aus, die nach der Änderung erstellt werden.

    Deaktivieren Sie zum Deaktivieren der Einstellungen das Kontrollkästchen auf dieser Seite.

## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie ein Labbesitzer diese Einstellung auf Labebene konfigurieren oder außer Kraft setzen kann, finden Sie unter [Konfigurieren des automatischen Herunterfahrens von virtuellen Computern für ein Lab](how-to-enable-shutdown-disconnect.md).
