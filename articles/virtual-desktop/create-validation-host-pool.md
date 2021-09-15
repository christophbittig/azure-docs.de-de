---
title: Azure Virtual Desktop-Hostpool zum Überwachen von Dienstupdates – Azure
description: Erfahren Sie, wie Sie einen Hostpool für die Überwachung von Dienstupdates erstellen, bevor Updates in der Produktion bereitgestellt werden.
author: Heidilohr
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 13d340d427d2478d226b966e17bf98bcf2561004
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123110160"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Erstellen eines Hostpools zum Überprüfen von Dienstupdates

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

Hostpools sind eine Sammlung identischer virtueller Computer innerhalb von Azure Virtual Desktop-Umgebungen. Es wird dringend empfohlen, einen Überprüfungshostpool zu erstellen, in dem Dienstupdates zuerst angewendet werden. Auf diese Weise können Sie Dienstupdates überwachen, bevor sie vom Dienst auf Ihre Standardumgebung oder Nicht-Überprüfungsumgebung angewendet werden. Ohne einen Überprüfungshostpool können Sie keine Änderungen erkennen, die Fehler einführen, was zu Downtime für Benutzer in der Standardumgebung führen könnte.

Um sicherzustellen, dass Ihre Apps mit den neuesten Updates funktionieren, sollte der Überprüfungshostpool Hostpools in Ihrer Nicht-Überprüfungsumgebung so ähnlich wie möglich sein. Benutzer sollten so häufig eine Verbindung mit dem Überprüfungshostpool herstellen wie mit dem Standardhostpool. Wenn Sie automatisierte Tests mit Ihrem Hostpool durchführen, sollten Sie auch beim Überprüfungshostpool automatisierte Tests durchführen.

Sie können Probleme beim Überprüfungshostpool entweder mit der [Diagnosefunktion](diagnostics-role-service.md) oder den [Artikeln zur Problembehandlung bei Azure Virtual Desktop](troubleshoot-set-up-overview.md) debuggen.

>[!NOTE]
> Wir empfehlen, dass Sie den Überprüfungshostpool eingerichtet lassen, um alle zukünftigen Updates zu testen.

>[!IMPORTANT]
>Bei Azure Virtual Desktop mit Integration der Azure-Ressourcenverwaltung treten derzeit Probleme beim Aktivieren und Deaktivieren von Überprüfungsumgebungen auf. Dieser Artikel wird aktualisiert, wenn das Problem behoben wurde.

## <a name="create-your-host-pool"></a>Erstellen Ihres Hostpools

Sie können einen Hostpool erstellen, indem Sie die Anweisungen in diesen Artikeln befolgen:
- [Tutorial: Erstellen eines Hostpools mit Azure Marketplace oder der Azure CLI](create-host-pools-azure-marketplace.md)
- [Erstellen eines Hostpools mithilfe von PowerShell oder der Azure CLI](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definieren Ihres Hostpools als Überprüfungshostpool

### <a name="portal"></a>[Portal](#tab/azure-portal)

So verwenden Sie das Azure-Portal, um Ihren Überprüfungshostpool zu konfigurieren

1. Melden Sie sich unter <https://portal.azure.com> beim Azure-Portal an.
2. Suchen Sie nach **Azure Virtual Desktop**, und wählen Sie diese Option aus.
3. Wählen Sie auf der Seite „Azure Virtual Desktop“ die Option **Hostpools** aus.
4. Wählen Sie den Namen des Hostpools aus, den Sie bearbeiten möchten.
5. Wählen Sie **Eigenschaften** aus.
6. Wählen Sie im Feld „Überprüfungsumgebung“ **Ja** aus, um die Überprüfungsumgebung zu aktivieren.
7. Wählen Sie **Speichern** aus. Dadurch werden die neuen Einstellungen angewendet.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Sofern dies noch nicht erfolgt ist, befolgen Sie die Anweisungen unter [Einrichten des Azure Virtual Desktop PowerShell-Moduls](powershell-module.md), um Ihr PowerShell-Modul einzurichten und sich bei Azure anzumelden.

Führen Sie die folgenden PowerShell-Cmdlets aus, um den neuen Hostpool als Überprüfungshostpool zu definieren. Ersetzen Sie die Werte in Klammern durch die für Ihre Sitzung relevanten Werte:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Führen Sie das folgende PowerShell-Cmdlet aus, um zu bestätigen, dass die Überprüfungseigenschaft festgelegt ist. Ersetzen Sie die Werte in Klammern durch die für Ihre Sitzung relevanten Werte.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Die Ergebnisse des Cmdlets sollte der folgenden Ausgabe ähneln:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bereiten Sie Ihre Umgebung für die Azure CLI vor und melden Sie sich an, falls Sie dies noch nicht erledigt haben.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Verwenden Sie den Befehl [az desktopvirtualization hostpool update](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_update), um den neuen Hostpool als Überprüfungshostpool zu definieren:

```azurecli
az desktopvirtualization hostpool update --name "MyHostPool" \
    --resource-group "MyResourceGroup" \
    --validation-environment true
```

Verwenden Sie den folgenden Befehl, um zu bestätigen, dass die Überprüfungseigenschaft festgelegt ist.

```azurecli
az desktopvirtualization hostpool show --name "MyHostPool" \
    --resource-group "MyResourceGroup" 
```
---

## <a name="update-schedule"></a>Zeitplan für Updates

Dienstupdates werden monatlich bereitgestellt. Wenn schwerwiegende Probleme vorliegen, werden kritische Updates in häufigeren Abständen bereitgestellt.

Wenn Dienstupdates verfügbar sind, stellen Sie sicher, dass sich jeden Tag zumindest eine kleine Gruppe von Benutzern anmeldet, um die Umgebung zu überprüfen. Es empfiehlt sich, regelmäßig die [TechCommunity-Website](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) zu besuchen und alle Beiträge mit dem Tag „WVDUPdate“ zu verfolgen, um über Dienstupdates auf dem Laufenden zu bleiben.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun einen Überprüfungshostpool erstellt haben, können Sie sich darüber informieren, wie Sie Azure Service Health zum Überwachen Ihrer Azure Virtual Desktop-Bereitstellung verwenden.

> [!div class="nextstepaction"]
> [Einrichten von Dienstwarnungen](./set-up-service-alerts.md)
