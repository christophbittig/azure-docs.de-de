---
title: 'Virtuellen Computer bei Verbindung starten: Azure'
description: Vorgehensweise beim Konfigurieren des Features zum Starten eines virtuellen Computers bei Verbindung.
author: Heidilohr
ms.topic: how-to
ms.date: 09/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3f21a2d4eb0742bc5e91298816f9648e325b1955
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335326"
---
# <a name="start-virtual-machine-on-connect"></a>VM bei Verbindung starten

Mit dem Feature „VM bei Verbindung starten“ können Sie Kosten sparen, indem Sie Endbenutzern erlauben, ihre virtuellen Computer (VMs) nur dann zu aktivieren, wenn sie sie benötigen. Nicht benötigte VMs können Sie dann deaktivieren.

>[!NOTE]
>Dieses Feature wird von Azure Virtual Desktop (klassisch) nicht unterstützt.

## <a name="requirements-and-limitations"></a>Anforderungen und Einschränkungen

Sie können das Feature „VM bei Verbindung starten“ für persönliche oder gepoolte Hostpools mithilfe von PowerShell und dem Azure-Portal aktivieren.

Die folgenden Remotedesktopclients unterstützen das Feature „VM bei Verbindung starten“:

- [Der Webclient](./user-documentation/connect-web.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Der Windows-Client (Version 1.2.2061 oder höher)](./user-documentation/connect-windows-7-10.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Der Android-Client (Version 10.0.10 oder höher)](./user-documentation/connect-android.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Der macOS-Client (Version 10.6.4 oder höher)](./user-documentation/connect-macos.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Der iOS-Client (Version 10.2.5 oder höher)](./user-documentation/connect-ios.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Der Microsoft Store-Client (Version 10.2.2005.0 oder höher)](./user-documentation/connect-microsoft-store.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- Die unter [Thin Client-Unterstützung](./user-documentation/linux-overview.md?toc=/azure/virtual-desktop/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json) aufgeführten Thin Clients

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Erstellen einer benutzerdefinierten Rolle für „VM bei Verbindung starten“

Bevor Sie das Feature „VM bei Verbindung starten“ konfigurieren können, müssen Sie Ihrem virtuellen Computer eine benutzerdefinierte RBAC-Rolle (Role-Based Access Control, rollenbasierte Zugriffssteuerung) zuweisen. Diese Rolle ermöglicht Azure Virtual Desktop die Verwaltung der VMs in Ihrem Abonnement. Sie können diese Rolle auch verwenden, um VMs zu aktivieren, ihren Status zu überprüfen und Diagnoseinformationen zu melden. Wenn Sie mehr über die Aufgaben der einzelnen Rollen erfahren möchten, lesen Sie den Artikel [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md).

>[!NOTE]
>Wenn sich Ihre VMs und Ihr Hostpool in unterschiedlichen Abonnements befinden, muss die RBAC-Rolle dem Abonnement zugewiesen werden, in dem sich die VMs befinden.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

So weisen Sie eine benutzerdefinierte Rolle für „VM bei Verbindung starten“ im Azure-Portal zu:

1. Navigieren Sie im Azure-Portal zu **Abonnements**.

2. Wählen Sie das Abonnement aus, in dem sich Ihre VMs befinden.
 
3. Wechseln Sie zu **Zugriffssteuerung (IAM)** , und wählen Sie **Benutzerdefinierte Rolle hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Ein Screenshot eines Dropdownmenüs der Schaltfläche „Hinzufügen“ in „Zugriffssteuerung (IAM)“. „Benutzerdefinierte Rolle hinzufügen“ ist rot hervorgehoben.](media/add-custom-role.png)

4. Benennen Sie als Nächstes die benutzerdefinierte Rolle, und fügen Sie eine Beschreibung hinzu. Wir empfehlen Ihnen die Benennung „VM bei Verbindung starten“.

5. Fügen Sie auf der Registerkarte **Berechtigungen** dem Abonnement, dem Sie die Rolle zuweisen, einen der beiden folgenden Berechtigungssätze hinzu: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read 
   - Microsoft.Compute/virtualMachines/instanceView/read 

   Sie können stattdessen auch diese Berechtigungen verwenden:

   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/*/read 

6. Klicken Sie auf **OK**, wenn Sie fertig sind.

Danach müssen Sie die Rolle zuweisen, um Azure Virtual Desktop Zugriff zu gewähren.

So weisen Sie die benutzerdefinierten Rolle zu:

1. Wählen Sie auf der Registerkarte **Zugriffssteuerung (IAM)** die Option **Rollenzuweisungen hinzufügen** aus.

2. Wählen Sie die Rolle aus, die Sie gerade erstellt haben.

3. Geben Sie in der Suchleiste **Windows Virtual Desktop** (dies wird in Kürze auf „Azure Virtual Desktop“ aktualisiert) ein, und wählen Sie es aus.

      >[!NOTE]
      >Wenn Sie Azure Virtual Desktop (klassisch) bereitgestellt haben, werden möglicherweise zwei Apps angezeigt. Weisen Sie die Rolle beiden Apps zu, die Sie sehen.
      >
      > [!div class="mx-imgBorder"]
      > ![Ein Screenshot der Registerkarte „Zugriffssteuerung (IAM)“. In der Suchleiste ist sowohl Azure Virtual Desktop als auch Azure Virtual Desktop (klassisch) rot hervorgehoben.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Erstellen einer benutzerdefinierten Rolle mit einer JSON-Dateivorlage

Wenn Sie eine JSON-Datei verwenden, um die benutzerdefinierte Rolle zu erstellen, können Sie die im folgenden Beispiel gezeigte grundlegende Vorlage verwenden. Achten Sie darauf, den Wert der Abonnement-ID in *AssignableScopes* durch die Abonnement-ID zu ersetzen, der Sie die Rolle zuweisen möchten.

```json
{
  "Name": "Start VM on connect (Custom)",
  "IsCustom": true,
  "Description": "Start VM on connect with AVD (Custom)",
  "Actions": [
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/*/read"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

Zur Verwendung der JSON-Vorlage speichern Sie die JSON-Datei, fügen unter *Zuweisbare Bereiche* die entsprechenden Abonnementinformationen hinzu und führen dann das folgende Cmdlet in PowerShell aus:

```powershell
New-AzRoleDefinition -InputFile "C:\temp\filename"
```

Weitere Informationen zum Erstellen benutzerdefinierter Rollen finden Sie unter [Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen mithilfe von Azure PowerShell](../role-based-access-control/custom-roles-powershell.md#create-a-custom-role-with-json-template).

## <a name="configure-the-start-vm-on-connect-feature"></a>Konfigurieren des Features „VM bei Verbindung starten“

Nachdem Sie Ihrem Abonnement die Rolle zugewiesen haben, können Sie das Feature „VM bei Verbindung starten“ konfigurieren.

### <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung 

„VM bei Verbindung starten“ ist eine Hostpooleinstellung. Wenn nur eine ausgewählte Gruppe von Benutzern dieses Feature verwenden soll, stellen Sie sicher, dass Sie nur den Benutzern, die Sie hinzufügen möchten, die erforderliche Rolle zuweisen.

Bei persönlichen Desktops aktiviert das Feature nur einen vorhandenen virtuellen Computer, den der Dienst bereits zugewiesen hat oder einem Benutzer zuweisen wird. In einem Szenario mit gepooltem Hostpool aktiviert der Dienst nur dann einen virtuellen Computer, wenn keine aktiviert sind. Das Feature aktiviert nur dann zusätzliche virtuelle Computer, wenn der erste virtuelle Computer den Sitzungsgrenzwert erreicht.

>[!IMPORTANT]
> Dieses Feature kann nur in vorhandenen Hostpools konfiguriert werden. Wenn Sie einen neuen Hostpool erstellen, ist dieses Feature nicht verfügbar.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

So verwenden Sie das Azure-Portal zum Konfigurieren von „VM bei Verbindung starten“:

1. Öffnen Sie Ihren Browser, und wechseln Sie zum [Azure-Portal](https://portal.azure.com).

2. Wechseln Sie im Azure-Portal zu **Azure Virtual Desktop**.

3. Wählen Sie **Hostpools** aus, und wechseln Sie dann zum Hostpool, in dem Sie die Einstellung aktivieren möchten.

4. Wählen Sie im Hostpool **Eigenschaften** aus. Wählen Sie unter **VM bei Verbindung starten** die Option **Ja** und dann **Speichern** aus, damit die Einstellung sofort angewendet wird.

    > [!div class="mx-imgBorder"]
    > ![Screenshot des Fensters „Eigenschaften“. Die Option „VM bei Verbindung starten“ ist rot hervorgehoben.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>Verwenden von PowerShell

Um diese Einstellung mit PowerShell zu konfigurieren, müssen Sie sicherstellen, dass Sie über die Namen der Ressourcengruppe und der Hostpools verfügen, die Sie konfigurieren möchten. Außerdem müssen Sie das [Azure PowerShell-Modul (Version 2.1.0 oder höher)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0) installieren.

So konfigurieren Sie „VM bei Verbindung starten“ mit PowerShell:

1. Öffnen Sie ein PowerShell-Befehlsfenster.

2. Führen Sie das folgende Cmdlet aus, um „VM bei Verbindung starten“ zu aktivieren:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Führen Sie das folgende Cmdlet aus, um „VM bei Verbindung starten“ zu deaktivieren:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Benutzerfreundlichkeit

In normalen Sitzungen nimmt die Zeit zu, die ein Benutzer zum Herstellen der Verbindung mit einer VM benötigt, deren Zuordnung aufgehoben wurde, da das erneute Einschalten der VM ähnlich wie das Einschalten eines physischen Computers eine Weile dauert. Der Remotedesktopclient hat einen Indikator, der dem Benutzer mitteilt, dass der PC eingeschaltet wird, während er die Verbindung herstellt.

## <a name="troubleshooting"></a>Problembehandlung

Wenn bei dem Feature Probleme auftreten, sollten Sie mit dem [Diagnosefeature](diagnostics-log-analytics.md) von Azure Virtual Desktop nach der Ursache suchen. Wenn Sie eine Fehlermeldung erhalten, beachten Sie deren Inhalt genau, und notieren Sie sich den Fehlernamen.

Sie können auch [Azure Monitor für Azure Virtual Desktop](azure-monitor.md) verwenden, um Vorschläge zum Beheben von Problemen zu erhalten.

Wenn sich der virtuelle Computer nicht aktivieren lässt, müssen Sie die Integrität des virtuellen Computers überprüfen, den Sie zu aktivieren versuchen, bevor Sie andere Schritte unternehmen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Probleme auftreten, die mithilfe der Dokumentation zur Problembehandlung oder des Diagnosefeatures nicht gelöst werden konnten, besuchen Sie die [FAQ zu „VM bei Verbindung starten“](start-virtual-machine-connect-faq.md).
