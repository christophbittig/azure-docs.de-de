---
title: Erstellen eines virtuellen Computers und Hinzufügen der VM zu einem Lab
description: Hier erhalten Sie Informationen zum Verwenden des Azure-Portals zum Hinzufügen einer VM zu einem Lab in Azure DevTest Labs. Sie können als Basis entweder ein benutzerdefiniertes Image oder eine Formel verwenden.
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 326d8923b27abff2ee480f6b981392a311be4f30
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235286"
---
# <a name="create-and-add-virtual-machines-to-a-lab-in-azure-devtest-labs"></a>Erstellen von virtuellen Computern und Hinzufügen zu einem Lab in Azure DevTest Labs

Dieser Artikel führt Sie durch das Erstellen und Hinzufügen von virtuellen Azure-Computern (VMs) zu einem Lab in Ihren vorhandenen DevTest Labs mithilfe des Azure-Portals.

## <a name="create-and-add-virtual-machines"></a>Erstellen und Hinzufügen virtueller Computer

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie in **DevTest Labs** zu Ihrem Lab.

1. Wählen Sie auf der Seite **Übersicht** die Option **+ Hinzufügen** aus.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-add-vm.png" alt-text="Übersichtsseite des Labs mit der Schaltfläche „Hinzufügen“.":::

1. Wählen Sie auf der Seite **Basis auswählen** ein Marketplace-Image für den virtuellen Computer aus. Dieser Leitfaden verwendet **Windows 11 Pro**. Bestimmte Optionen können sich unterscheiden, wenn Sie ein anderes Image verwenden.

1. Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen an:

    |Eigenschaft |Beschreibung |
    |---|---|
    |Name&nbsp;des virtuellen&nbsp;Computers| Im Textfeld wird bereits ein eindeutiger, automatisch generierter Name angegeben. Der Name entspricht dem Benutzernamen in Ihrer E-Mail-Adresse, gefolgt von einer eindeutigen dreistelligen Zahl. Behalten Sie diese Angabe bei, oder geben Sie einen eindeutigen Namen Ihrer Wahl ein.|
    |Benutzername| Im Textfeld wird bereits ein eindeutiger, automatisch generierter Name angegeben. Der Name entspricht dem Benutzernamen in Ihrer E-Mail-Adresse. Behalten Sie diese Angabe bei, oder geben Sie einen Namen Ihrer Wahl ein. Dieser Benutzer erhält auf dem virtuellen Computer **Administratorberechtigungen**.|
    |Gespeichertes Geheimnis verwenden| Lassen Sie das Kontrollkästchen für diese exemplarische Vorgehensweise deaktiviert. Sie können Geheimnisse zunächst in Azure Key Vault speichern und dann hier verwenden. Weitere Informationen finden Sie unter [Speichern eines Geheimnisses in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md). Wenn Sie lieber ein gespeichertes Geheimnis verwenden möchten, aktivieren Sie das Kontrollkästchen, und wählen Sie dann das Geheimnis aus der Dropdownliste **Geheimnis** aus.|
    |Kennwort|Geben Sie ein Kennwort ein, das zwischen 8 und 123 Zeichen lang ist.|
    |Als Standardkennwort speichern| Aktivieren Sie das Kontrollkästchen, um das Kennwort in der Azure Key Vault-Instanz zu speichern, die dem Lab zugeordnet ist.|
    |Größe des virtuellen Computers| Behalten Sie den Standardwert bei, oder wählen Sie **Größe ändern** aus, um andere physische Komponenten auszuwählen. In dieser exemplarischen Vorgehensweise wird **Standard_B2_v3** verwendet.|
    |Typ des Betriebssystemdatenträgers|Behalten Sie den Standardwert bei, oder wählen Sie eine andere Option aus der Dropdownliste aus.|
    |Artifacts| Wählen Sie **Artefakte hinzufügen oder entfernen**. Wählen Sie die Artefakte aus, die Sie dem Basisimage hinzufügen möchten, und konfigurieren Sie sie. Jedes Lab enthält Artefakte aus dem öffentlichen DevTest Labs-Artefaktrepository und Artefakte, die Sie erstellt und Ihrem eigenen Artefaktrepository hinzugefügt haben. Eine ausführliche Anleitung finden Sie weiter unten unter [Hinzufügen von Artefakten während der Installation](#add-artifacts-during-installation).|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-basic-settings.png" alt-text="Seite „Grundeinstellungen“ der VM.":::

1. Wählen Sie die Registerkarte **Erweiterte Einstellungen** aus, und geben Sie die folgenden Informationen an:

    |Eigenschaft |BESCHREIBUNG |
    |---|---|
    |Virtuelles Netzwerk| Behalten Sie die Angabe unverändert bei, oder wählen Sie in der Dropdownliste ein anderes Netzwerk aus.|
    |&nbsp;Subnetzauswahl| Behalten Sie die Angabe unverändert bei, oder wählen Sie in der Dropdownliste ein anderes Subnetz aus.|
    |IP-Adresse| Für diese exemplarische Vorgehensweise behalten Sie den Standardwert **Freigegeben** bei.|
    |Ablaufdatum| Behalten Sie die Angabe unverändert bei, um kein Ablaufdatum festzulegen, oder wählen Sie das Kalendersymbol aus, um ein Ablaufdatum festzulegen.|
    |Diesen Computer als abrufbar festlegen| Um die VM für einen Lab-Benutzer abrufbar zu machen, wählen Sie **Ja** aus. Das Markieren des Computers als abrufbar bedeutet, dass ihm zum Zeitpunkt der Erstellung kein Besitzer zugewiesen wird. In dieser exemplarischen Vorgehensweise wird **Ja** verwendet.|
    |Anzahl von Instanzen| Geben Sie für diese exemplarische Vorgehensweise **2** ein. Die Anzahl der zu erstellenden VM-Instanzen.|
    |Automatisierung | Optional. Wenn Sie **ARM-Vorlage anzeigen** auswählen, wird die Vorlage auf einer neuen Seite geöffnet. Sie können die Vorlage kopieren und speichern, um den gleichen virtuellen Computer später zu erstellen. Nach dem Speichern können Sie die Azure Resource Manager-Vorlage zum [Bereitstellen neuer virtueller Computer mit Azure PowerShell](../azure-resource-manager/templates/overview.md) nutzen.|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-advanced-settings.png" alt-text="Seite „Erweiterte Einstellungen“ des virtuellen Computers.":::

1. Kehren Sie zur Registerkarte **Grundeinstellungen** zurück, und wählen Sie dann **Erstellen** aus.

1. Wählen Sie auf der Seite **DevTest Lab** unter **Mein Lab** die Option **Abrufbare virtuelle Computer** aus.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-creation-status.png" alt-text="Seite zum Status der Lab-VM-Erstellung.":::

1. Wählen Sie nach einigen Minuten **Aktualisieren** aus, wenn Ihre virtuellen Computer nicht angezeigt werden. Die Installationszeiten variieren basierend auf der ausgewählten Hardware, dem Basisimage und den Artefakten. Die Installation der in dieser exemplarischen Vorgehensweise verwendeten Konfigurationen hat etwa 25 Minuten betragen.

## <a name="add-artifacts-during-installation"></a>Hinzufügen von Artefakten während der Installation

Diese Schritte sind erweiterte Anweisungen zum vorherigen Abschnitt. Die Schritte beginnen, nachdem Sie **Artefakte hinzufügen oder entfernen** auf der Registerkarte **Grundeinstellungen** ausgewählt haben. Weitere Informationen zu Artefakten finden Sie unter [Erfahren Sie, wie Sie Ihre eigenen Artefakte für die Verwendung mit DevTest Labs erstellen](devtest-lab-artifact-author.md).

1. Identifizieren Sie auf der Seite **Artefakte hinzufügen** ein Artefakt, und wählen Sie dann **>** (Größer-als-Symbol) aus. Klicken Sie anschließend auf **OK**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-add-artifact-during.png" alt-text="Hinzufügen eines Artefakts zur VM.":::

1. Wählen Sie ein anderes Artefakt aus: **PowerShell-Modul installieren**. Dieses Artefakt erfordert zusätzliche Informationen, insbesondere den Namen eines PowerShell-Moduls. Wählen Sie **Az** aus, und wählen Sie dann **OK** aus.

1. Fügen Sie weiterhin nach Bedarf für Ihren virtuellen Computer Artefakte hinzu.

1. Wählen Sie **...** (Auslassungszeichen) aus einem ihrer ausgewählten Artefakte aus, und beachten Sie die verschiedenen Optionen, einschließlich der Möglichkeit, die Installationsreihenfolge zu ändern.

1. Wenn Sie mit dem Hinzufügen von Artefakten fertig sind, wählen Sie **OK** aus, um zur Registerkarte **Grundeinstellungen** zurückzukehren.

## <a name="add-artifacts-after-installation"></a>Hinzufügen von Artefakten nach der Installation

Sie können auch Artefakte hinzufügen, nachdem die VM erstellt wurde. 

1. Wählen Sie auf der Seite **DevTest Lab** unter **Mein Lab** die Option **Alle Ressourcen** aus. **Alle Ressourcen** listet sowohl abgerufene als auch nicht abgerufene VMs auf.

    :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-all-resources.png" alt-text="Lab zeigt den Status aller Ressourcen an.":::

1. Wählen Sie Ihre VM aus, sobald der **Status** als **Verfügbar** angezeigt wird.

1. Wählen Sie auf Ihrer Seite **VM** die Option **Start** aus, um die VM zu starten.

1. Einige Augenblicke nachdem die Seite **Wird ausgeführt** anzeigt wird, wählen Sie unter **Vorgänge** die Option **Artefakte** aus.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-overview.png" alt-text="Übersicht über die Lab-VM mit der Schaltfläche „Start“.":::

1. Wählen Sie **Artefakte anwenden** aus, um die Seite **Artefakte hinzufügen** zu öffnen.

1. Ab hier sind die Schritte im Grunde die gleichen wie weiter oben ab [Hinzufügen von Artefakten während der Installation](#add-artifacts-during-installation).

## <a name="next-steps"></a>Nächste Schritte

* Nach der Erstellung des virtuellen Computers können Sie im Bereich des virtuellen Computers die Option **Verbinden** wählen, um eine Verbindung mit dem virtuellen Computer herzustellen.
* Informieren Sie sich über das [Erstellen von benutzerdefinierten Artefakten für Ihre DevTest Lab-VM](devtest-lab-artifact-author.md).
* Erkunden Sie den [DevTest Labs-Azure Resource Manager-Katalog mit Schnellstartvorlagen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
