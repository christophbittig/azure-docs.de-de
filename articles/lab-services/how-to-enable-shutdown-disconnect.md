---
title: Konfigurieren des automatischen Herunterfahrens von VMs für ein Lab in Azure Lab Services
description: Hier erfahren Sie, wie Sie das automatische Herunterfahren von VMs aktivieren oder deaktivieren, wenn eine Remotedesktopverbindung getrennt wird.
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: ab0a78faff158b939e3c1dc540def67ae149f84c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241894"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Konfigurieren des automatischen Herunterfahrens von virtuellen Computern für ein Lab

In diesem Artikel erfahren Sie, wie Sie das automatische Herunterfahren von virtuellen Computern für ein Lab konfigurieren können.

Sie können mehrere Features zur Kostenkontrolle beim automatischen Herunterfahren aktivieren, um proaktiv zusätzliche Kosten zu verhindern, wenn die virtuellen Computer nicht aktiv genutzt werden. Die Kombination der folgenden drei Features zum automatischen Herunterfahren und Trennen deckt den größten Teil der Fälle ab, in denen Benutzer die Ausführung der virtuellen Computer versehentlich nicht beenden:
 
* Automatisches Trennen von Benutzern von virtuellen Computern, die vom Betriebssystem als im Leerlauf erkannt werden
* Automatisches Herunterfahren virtueller Computer, wenn Benutzer die Verbindung trennen
* Automatisches Herunterfahren von virtuellen Computern, die gestartet wurden, mit denen jedoch von den Benutzern keine Verbindung hergestellt wurde.

Weitere Informationen zu den Features zum automatischen Herunterfahren finden Sie im Abschnitt [Maximieren der Kostenkontrolle durch Einstellungen zum automatischen Herunterfahren](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

Ein Labkontoadministrator kann diese Einstellung für das Labkonto konfigurieren, in dem Sie Labs erstellen. Weitere Informationen finden Sie unter [Konfigurieren des automatischen Herunterfahrens von VMs für ein Labkonto](how-to-configure-lab-accounts.md). Als Labbesitzer können Sie die Einstellung beim oder nach dem Erstellen eines Labs außer Kraft setzen.

> [!IMPORTANT]
> Linux-Labs unterstützen das automatische Herunterfahren nur, wenn Benutzer*innen die Verbindung trennen und wenn VMs gestartet werden, Benutzer*innen aber keine Verbindung herstellen.  Die Unterstützung variiert auch abhängig von [bestimmten Distributionen und Versionen von Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions).  Einstellungen zum Herunterfahren werden vom Image [Data Science Virtual Machine – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) nicht unterstützt.

## <a name="configure-for-the-lab-level"></a>Konfigurieren für die Labebene

Sie können die Einstellung für das automatische Herunterfahren in den [Azure Lab Services](https://labs.azure.com/) konfigurieren.

* Beim Erstellen eines Labs (in **Labrichtlinien**) oder
* nach dem Erstellen des Labs (in **Einstellungen**).

> [!div class="mx-imgBorder"]
> ![Konfigurieren zum Zeitpunkt der Laberstellung](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Lesen Sie unbedingt die Informationen zum automatischen Herunterfahren im Abschnitt [Maximieren der Kostenkontrolle durch Einstellungen zum automatischen Herunterfahren](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

> [!WARNING]
> Wenn Sie das Linux- oder Windows-Betriebssystem einer VM herunterfahren, bevor Sie eine RDP-Sitzung mit der VM trennen, funktioniert das automatische Herunterfahren nicht ordnungsgemäß.  
## <a name="next-steps"></a>Nächste Schritte

[Dashboard für Labs](use-dashboard.md)
