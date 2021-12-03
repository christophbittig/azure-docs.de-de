---
title: Entfernen von Maschinen aus Azure Automation Change Tracking und Inventory
description: In diesem Artikel erfahren Sie, wie Sie Azure- und Nicht-Azure-Rechner aus der Änderungsverfolgung und Inventarisierung entfernen.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: 810b2ac388b0f387183f735437ccc2b2a3918cfd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026297"
---
# <a name="remove-machines-from-change-tracking-and-inventory"></a>Maschinen aus der Änderungsverfolgung und Inventarisierung entfernen

Wenn Sie mit der Verfolgung von Änderungen auf Ihren Azure- oder Nicht-Azure-Rechnern in Ihrer Umgebung fertig sind, können Sie deren Verwaltung mit der Funktion [Änderungsverfolgung und Inventarisierung](overview.md) beenden. Um die Bearbeitung zu beenden, bearbeiten Sie die gespeicherte Suchabfrage `MicrosoftDefaultComputerGroup` in Ihrem Log Analytics-Arbeitsbereich, der mit Ihrem Automation-Konto verknüpft ist.

## <a name="sign-into-the-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="to-remove-your-machines"></a>So entfernen Sie Ihre Geräte

1. Starten Sie **Cloud Shell** über den oberen Navigationsbereich im Azure-Portal. Wenn Sie mit Azure Cloud Shell nicht vertraut sind, finden Sie weitere Informationen unter [Übersicht über Azure Cloud Shell](../../cloud-shell/overview.md).

2. Verwenden Sie die folgende Methode, um die UUID einer virtuellen Azure-Maschine oder einer Nicht-Azure-Maschine zu identifizieren, die Sie aus der Verwaltung entfernen möchten.

   # <a name="azure-vm"></a>[Azure-VM](#tab/azure-vm)

   ```azurecli
   az vm show -g MyResourceGroup -n MyVm -d
   ```

   # <a name="non-azure-machine"></a>[Nicht-Azure-Computer](#tab/non-azure-machine)

   ```kusto
   Heartbeat
   | where TimeGenerated > ago(30d)
   | where ComputerEnvironment == "Non-Azure"
   | summarize by Computer, VMUUID
   ```

   ---

3. Navigieren Sie im Azure-Portal zu **Log Analytics-Arbeitsbereiche**. Wählen Sie Ihren Arbeitsbereich in der Liste aus.

4. Wählen Sie in Ihrem Log Analytics-Arbeitsbereich im linken Menü **Computergruppen** aus.

5. Im rechten Bereich unter **Computergruppen** wird standardmäßig die Registerkarte **Gespeicherte Gruppen** angezeigt.

6. Klicken Sie in der Tabelle auf das Symbol **Abfrage ausführen** rechts neben dem Element **MicrosoftDefaultComputerGroup** mit dem **Legacykategorie**-Wert **ChangeTracking**.

7. Überprüfen Sie die Abfrage im Abfrage-Editor, und suchen Sie die UUID für den virtuellen Computer. Entfernen Sie die UUID für den virtuellen Computer, und wiederholen Sie die Schritte für alle anderen VMs, die Sie entfernen möchten.

   > [!NOTE]
   > Erstellen Sie als zusätzlichen Schutz unbedingt eine Kopie der Abfrage, bevor Sie Bearbeitungen vornehmen. Auf diese Weise können Sie sie wiederherstellen, wenn ein Problem auftritt.

   Wenn Sie mit der ursprünglichen Abfrage beginnen und VMs zum Zweck einer Bereinigungs- oder Wartungsaktivität erneut hinzufügen möchten, kopieren Sie die folgende Abfrage:

   ```kusto
   Heartbeat
   | where Computer in~ ("") or VMUUID in~ ("")
   | distinct Computer
   ```

8. Speichern Sie die gespeicherte Suche, wenn Sie die Bearbeitung abgeschlossen haben, indem Sie in der oberen Leiste **Speichern > Als Funktion speichern** auswählen. Geben Sie bei Aufforderung Folgendes an:

    * **Name**: ChangeTracking__MicrosoftDefaultComputerGroup
    * **Als Computergruppe speichern** ist ausgewählt
    * **Legacykategorie:** ChangeTracking

>[!NOTE]
>Computer werden weiterhin angezeigt, nachdem Sie die Registrierung aufgehoben haben, da wir alle in den letzten 24 Stunden bewerteten Computer melden. Nachdem Sie den Computer entfernt haben, müssen Sie 24 Stunden warten, bis er nicht mehr aufgelistet wird.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum erneuten Aktivieren dieses Features finden Sie unter [Aktivieren des Features für Änderungsnachverfolgung und Bestand über ein Automation-Konto](enable-from-automation-account.md), [Aktivieren des Features für Änderungsnachverfolgung und Bestand durch das Durchsuchen des Azure-Portals](enable-from-portal.md), [Aktivieren des Features für Änderungsnachverfolgung und Bestand über ein Runbook](enable-from-runbook.md) oder [Aktivieren des Features für Änderungsnachverfolgung und Bestand über eine Azure-VM](enable-from-vm.md).