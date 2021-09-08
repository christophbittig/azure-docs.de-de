---
title: Einrichten einer Instanz und der Authentifizierung (PowerShell)
titleSuffix: Azure Digital Twins
description: Hier erhalten Sie Informationen zum Einrichten einer Instanz des Azure Digital Twins-Diensts mithilfe von Azure PowerShell.
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1e2751c7df1cd3b43ce6c700bc51b51faf95963f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468694"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Einrichten einer Azure Digital Twins-Instanz und der Authentifizierung (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Dieser Artikel behandelt die Schritte zum **Einrichten einer neuen Azure Digital Twins-Instanz**, einschließlich des Erstellens der Instanz und des Einrichtens der Authentifizierung. Nachdem Sie diesen Artikel durchgearbeitet haben, verfügen Sie über eine Azure Digital Twins-Instanz, die für die Programmierung bereitsteht.

In dieser Version dieses Artikels werden diese Schritte manuell nacheinander mithilfe von [Azure PowerShell](/powershell/azure/new-azureps-module-az) durchlaufen.

* Wenn Sie diese Schritte manuell im Azure-Portal durchlaufen möchten, finden Sie weitere Informationen in der Portal-Version dieses Artikels unter [Einrichten einer Instanz und der Authentifizierung (Portal)](how-to-set-up-instance-portal.md).
* Ein Beispiel zur Ausführung einer automatisierten Einrichtung mit einem Bereitstellungsskript finden Sie in der Skriptversion dieses Artikels unter [Einrichten einer Instanz und der Authentifizierung (per Skript)](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

1. Wählen Sie zunächst aus, wo die Befehle in diesem Artikel ausgeführt werden sollen. Sie können auswählen, dass die Azure PowerShell-Befehle mithilfe einer lokalen Installation von Azure PowerShell ausgeführt werden, oder mithilfe von [Azure Cloud Shell](https://shell.azure.com) in einem Browserfenster.
    * Bei lokaler Verwendung von Azure PowerShell:
       1. [Installieren Sie das Az-Modul von PowerShell](/powershell/azure/install-az-ps).
       1. Öffnen Sie ein PowerShell-Fenster auf dem Computer.
       1. Stellen Sie eine Verbindung mit Ihrem Azure-Konto mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) her.
    * Bei Verwendung von Azure Cloud Shell:
        1. Weitere Informationen zu Cloud Shell finden Sie in der [Übersicht über Azure Cloud Shell](../cloud-shell/overview.md).
        1. [Öffnen Sie in Ihrem Browser ein Azure Cloud Shell-Fenster](https://shell.azure.com).
        1. Stellen Sie in der Cloud Shell-Symbolleiste sicher, dass Ihre Cloud Shell für die Ausführung der PowerShell-Version festgelegt ist.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Screenshot: Fenster „Cloud Shell“ im Azure-Portal mit Auswahl der PowerShell-Version.":::
    
1. Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Wenn Sie Azure Digital Twins mit diesem Abonnement erstmalig verwenden, müssen Sie den Ressourcenanbieter **Microsoft.DigitalTwins** registrieren. (Falls Sie sich nicht sicher sind, ist es in Ordnung, ihn erneut auszuführen, auch wenn Sie ihn irgendwann zuvor schon einmal ausgeführt haben.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. Verwenden Sie den folgenden Befehl, um das PowerShell-Modul **Az.DigitalTwins** zu installieren.
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.DigitalTwins** verfügbar ist, müssen Sie es wie oben beschrieben separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

## <a name="create-the-azure-digital-twins-instance"></a>Erstellen der Azure Digital Twins-Instanz

In diesem Abschnitt **erstellen Sie eine neue Azure Digital Twins-Instanz** mithilfe von Azure PowerShell.
Hierzu müssen Sie Folgendes bereitstellen:

* Eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md), in der die Instanz bereitstellt wird. Falls Sie nicht bereits über eine Ressourcengruppe verfügen, können Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue erstellen:

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Eine Region für die Bereitstellung. Informationen zu Regionen mit Unterstützung von Azure Digital Twins finden Sie unter [Verfügbare Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Ein Name für Ihre Instanz. Der Name der neuen Instanz muss innerhalb der Region für Ihr Abonnement eindeutig sein. Wenn in Ihrem Abonnement für die Region eine weitere Azure Digital Twins-Instanz vorhanden ist, die den angegeben Namen bereits verwendet, werden Sie aufgefordert, einen anderen Namen auszuwählen.

Verwenden Sie im folgenden Befehl diese Werte, um die Instanz zu erstellen:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Überprüfen des Erfolgs und Erfassen wichtiger Werte

Wenn die Instanz erfolgreich erstellt wurde, sieht das Ergebnis in etwa wie folgt aus und gibt Informationen über die von Ihnen erstellte Ressource aus:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

Als Nächstes zeigen Sie die Eigenschaften der neuen Instanz an, indem Sie `Get-AzDigitalTwinsInstance` ausführen und folgendermaßen an `Select-Object -Property *` weiterleiten:

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> Sie können diesen Befehl jederzeit verwenden, um alle Eigenschaften Ihrer Instanz anzuzeigen.

Beachten Sie die Angaben für **host name**, **name** und **resource group** der Azure Digital Twins-Instanz. Dies sind wichtige Werte, die Sie möglicherweise benötigen, wenn Sie mit Ihrer Azure Digital Twins-Instanz weiterarbeiten, um die Authentifizierung und die zugehörigen Azure-Ressourcen einzurichten. Wenn andere Benutzer in der Instanz programmieren, sollten Sie diese Werte mit ihnen teilen.

Die Azure Digital Twins-Instanz ist jetzt einsatzbereit. Im nächsten Schritt stellen Sie die entsprechenden Azure-Benutzerberechtigungen für die Verwaltung der Instanz zur Verfügung.

## <a name="set-up-user-access-permissions"></a>Einrichten von Benutzerzugriffsberechtigungen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>Voraussetzungen: Berechtigungsanforderungen
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>Zuweisen der Rolle

Um einem Benutzer Berechtigungen zum Verwalten einer Azure Digital Twins-Instanz zu gewähren, müssen Sie ihm die Rolle _**Azure Digital Twins Data Owner**_ (Azure Digital Twins-Datenbesitzer) innerhalb der Instanz zuweisen.

Ermitteln Sie zunächst den Wert für **ObjectId** für das Azure AD-Konto des Benutzers, dem die Rolle zugewiesen werden soll. Diesen Wert finden Sie, indem Sie das Cmdlet [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) verwenden. Dabei übergeben Sie den Benutzerprinzipalnamen für das Azure AD-Konto, um den dazugehörigen ObjectId-Wert (und weitere Informationen zum Benutzer) abzurufen. In den meisten Fällen entspricht der Benutzerprinzipalname der E-Mail-Adresse des Benutzers im Azure AD-Konto.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Als Nächstes verwenden Sie den Wert für **ObjectId** im folgenden Befehl, um die Rolle zuzuweisen. Für den Befehl müssen Sie außerdem dieselben Werte für Abonnement-ID, Ressourcengruppenname und Azure Digital Twins-Instanzname eingeben, die bei Erstellung der Instanz ausgewählt wurden. Der Befehl muss von einem Benutzer mit [ausreichenden Berechtigungen](#prerequisites-permission-requirements) im Azure-Abonnement ausgeführt werden.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-ID-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Die Ausgabe dieses Befehls enthält Informationen zur erstellten Rollenzuweisung.

### <a name="verify-success"></a>Überprüfen des erfolgreichen Abschlusses

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Sie verfügen nun über eine einsatzbereite Azure Digital Twins-Instanz und haben Berechtigungen zugewiesen, um diese zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

Sie können auch eine Verbindung zwischen Ihrer Clientanwendung und Ihrer Instanz herstellen, indem Sie Authentifizierungscode verwenden:
* [Schreiben von App-Authentifizierungscode](how-to-authenticate-client.md)
