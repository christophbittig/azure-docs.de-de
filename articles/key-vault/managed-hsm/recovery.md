---
title: Übersicht über die Azure Key Vault-Wiederherstellung mit verwaltetem HSM | Microsoft-Dokumentation
description: Die Wiederherstellungsfunktionen des verwaltete HSM sind darauf ausgelegt, das versehentliche oder böswillige Löschen Ihrer HSM-Ressource und der Schlüssel zu verhindern.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.author: mbaldwin
author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: c0688d40e0ccb71b98e598da7d5ff8100b735229
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122356421"
---
# <a name="managed-hsm-soft-delete-and-purge-protection"></a>Vorläufiges Löschen und Löschschutz des verwalteten HSM

In diesem Artikel werden zwei Wiederherstellungsfeatures des verwalteten HSM beschrieben: vorläufiges Löschen und Löschschutz. Er enthält eine Übersicht über diese Features und demonstriert, wie Sie sie über die Azure CLI und Azure PowerShell verwalten.

Weitere Informationen finden Sie in der [Übersicht zum verwalteten HSM](overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/dotnet)
* Das [PowerShell-Modul](/powershell/azure/install-az-ps).
* Azure CLI 2.25.0 oder höher. Führen Sie `az --version` aus, um zu ermitteln, über welche Version Sie verfügen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).
* Ein verwaltetes HSM. Sie können eins mithilfe der [Azure CLI](./quick-create-cli.md) oder mithilfe von [Azure PowerShell](./quick-create-powershell.md) erstellen.
* Benutzer benötigen die folgenden Berechtigungen, um Vorgänge auf vorläufig gelöschten HSMs oder mit Schlüsseln durchzuführen:

  | Rollenzuweisung | BESCHREIBUNG |
  |---|---|
  |[Mitwirkender für verwaltete HSMs](../../role-based-access-control/built-in-roles.md#managed-hsm-contributor)|Auflisten, Wiederherstellen und Bereinigen von vorläufig gelöschten HSMs|
  |[Managed HSM Crypto User (Kryptografiebenutzer für verwaltete HSMs)](./built-in-roles.md)|Auflisten von vorläufig gelöschten Schlüsseln|
  |[Managed HSM Crypto Officer (Kryptoverantwortlicher für verwaltete HSMs)](./built-in-roles.md)|Bereinigen und Wiederherstellen von vorläufig gelöschten Schlüsseln|



## <a name="what-are-soft-delete-and-purge-protection"></a>Was sind vorläufiges Löschen und Löschschutz?

[Vorläufiges Löschen](soft-delete-overview.md) und Löschschutz sind Wiederherstellungsfunktionen.


*Vorläufiges Löschen* soll verhindern, dass Ihr HSM und Ihre Schlüssel versehentlich gelöscht werden. Vorläufiges Löschen funktioniert wie ein Papierkorb. Wenn Sie ein HSM oder einen Schlüssel löschen, bleibt das Element für eine konfigurierbare Aufbewahrungsdauer oder für den Standardzeitraum von 90 Tagen wiederherstellbar. HSMs und Schlüssel, die vorläufig gelöscht wurden, können auch *bereinigt* und dadurch endgültig gelöscht werden. Durch Bereinigen können Sie HSMs und Schlüssel mit demselben Namen wie dem des bereinigten Elements neu erstellen. Sowohl für das Wiederherstellen als auch das Löschen von HSMs und Schlüsseln sind bestimmte Rollenzuweisungen erforderlich. Vorläufiges Löschen kann nicht deaktiviert werden.

> [!NOTE]
> Da die zugrunde liegenden Ressourcen Ihrem HSM auch dann zugeordnet bleiben, wenn es sich in einem gelöschten Zustand befindet, fallen für die HSM-Ressource weiterhin stündliche Gebühren an, solange sie sich in diesem Zustand befindet.

Namen von verwalteten HSMs sind in jeder Cloudumgebung global eindeutig. Sie können also kein verwaltetes HSM mit demselben Namen wie dem eines HSM erstellen, das vorläufig gelöscht ist. Ebenso sind die Namen von Schlüsseln innerhalb eines HSM eindeutig. Sie können keinen Schlüssel mit demselben Namen wie dem eines Schlüssels erstellen, der vorläufig gelöscht ist.

Weitere Informationen finden Sie in der [Übersicht zum vorläufigen Löschen bei verwalteten HSMs](soft-delete-overview.md).

Der *Löschschutz* soll das Löschen Ihrer HSMs und Schlüssel durch böswillige interne Benutzer verhindern. Er funktioniert wie ein Papierkorb mit einer zeitbasierten Sperre. Sie können Elemente während des konfigurierbaren Aufbewahrungszeitraums jederzeit wiederherstellen. Sie können ein HSM oder einen Schlüssel erst dann endgültig löschen oder bereinigen, wenn der Aufbewahrungszeitraum abgelaufen ist. Wenn der Aufbewahrungszeitraum endet, wird das HSM oder der Schlüssel automatisch bereinigt.

> [!NOTE]
> Keine Administratorrolle oder -berechtigung kann den Löschschutz außer Kraft setzen, deaktivieren oder umgehen. *Wenn der Löschschutz aktiviert wurde, kann er von niemandem mehr deaktiviert oder außer Kraft gesetzt werden, auch nicht von Microsoft.* Daher müssen Sie ein gelöschtes HSM wiederherstellen oder warten, bis der Aufbewahrungszeitraum endet, bevor Sie den HSM-Namen wiederverwenden können.

## <a name="manage-keys-and-managed-hsms"></a>Verwalten von Schlüsseln und verwalteten HSMs

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="managed-hsms-cli"></a>Verwaltete HSMs (CLI) 

* So überprüfen Sie den Status von „Vorläufiges Löschen“ und Löschschutz für ein verwaltetes HSM

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```

* So löschen Sie ein HSM

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```
    
  Diese Aktion kann wiederhergestellt werden, weil vorläufiges Löschen standardmäßig aktiviert ist.

* So listen Sie alle vorläufig gelöschten HSMs auf

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type hsm
    ```

* So stellen Sie ein vorläufig gelöschtes HSM wieder her

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```


* So bereinigen Sie ein vorläufig gelöschtes HSM

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```
    > [!WARNING] 
    > Durch diesen Vorgang wird Ihr HSM endgültig gelöscht.

* So aktivieren Sie den Löschschutz für ein HSM

    ```azurecli
    az keyvault update-hsm --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME} --enable-purge-protection true
    ```

### <a name="keys-cli"></a>Schlüssel (Befehlszeilenschnittstelle)

* So löschen Sie einen Schlüssel

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* So listen Sie gelöschte Schlüssel auf

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME}
    ```

* So stellen Sie einen gelöschten Schlüssel wieder her:

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* So bereinigen Sie einen vorläufig gelöschten Schlüssel 

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```
    > [!WARNING] 
    > Durch diesen Vorgang wird Ihr Schlüssel endgültig gelöscht.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="managed-hsms-powershell"></a>Verwaltete HSMs (PowerShell)

* So überprüfen Sie den Status von „Vorläufiges Löschen“ und Löschschutz für ein verwaltetes HSM

    ```powershell
    Get-AzKeyVaultManagedHsm -Name "ContosoHSM"
    ```

* So löschen Sie ein HSM

    ```powershell
    Remove-AzKeyVaultManagedHsm -Name 'ContosoHSM'
    ```
  Diese Aktion kann wiederhergestellt werden, weil vorläufiges Löschen standardmäßig aktiviert ist.

### <a name="keys-powershell"></a>Schlüssel (PowerShell)

* So löschen Sie einen Schlüssel

  ```powershell
  Remove-AzKeyVaultKey -HsmName ContosoHSM -Name 'MyKey'
  ```

* So listen Sie alle gelöschten Schlüssel auf 

  ```powershell
  Get-AzKeyVaultKey -HsmName ContosoHSM -InRemovedState
  ```

* So stellen Sie einen vorläufig gelöschten Schlüssel wieder her:

    ```powershell
    Undo-AzKeyVaultKeyRemoval -HsmName ContosoHSM -Name ContosoFirstKey
    ```

* So bereinigen Sie einen vorläufig gelöschten Schlüssel

    ```powershell
    Remove-AzKeyVaultKey -HsmName ContosoHSM -Name ContosoFirstKey -InRemovedState
    ```
    > [!WARNING] 
    > Durch diesen Vorgang wird Ihr Schlüssel endgültig gelöscht.
    
---

## <a name="next-steps"></a>Nächste Schritte

- [PowerShell-Cmdlets für verwaltete HSMs](/powershell/module/az.keyvault)
- [Azure CLI-Befehle für Key Vault](/cli/azure/keyvault)
- [Vollständige Sicherung und Wiederherstellung von verwalteten HSMs](backup-restore.md)
- [Aktivieren der Protokollierung für verwaltete HSMs](logging.md)
