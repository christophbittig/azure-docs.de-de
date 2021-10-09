---
title: Zuweisen einer Azure Key Vault-Zugriffsrichtlinie (Befehlszeilenschnittstelle)
description: Verwenden der Azure-Befehlszeilenschnittstelle zum Zuweisen einer Key Vault-Zugriffsrichtlinie zu einem Sicherheitsprinzipal oder einer Anwendungsidentität.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b194bec5f03d5985e282b7c8d220e268de1a763c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838150"
---
# <a name="assign-a-key-vault-access-policy"></a>Zuweisen einer Key Vault-Zugriffsrichtlinie

Eine Key Vault-Zugriffsrichtlinie legt fest, ob ein bestimmter Sicherheitsprinzipal (eine Anwendung oder eine Benutzergruppe) verschiedene Vorgänge für [Geheimnisse](../secrets/index.yml), [Schlüssel](../keys/index.yml) und [Zertifikate](../certificates/index.yml) in Key Vault ausführen kann. Sie können Zugriffsrichtlinien über das [Azure-Portal](assign-access-policy-portal.md), die Azure-CLI oder [Azure PowerShell](assign-access-policy-powershell.md) zuweisen.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

## <a name="assign-an-access-policy"></a>Zuweisen einer Zugriffsrichtlinie

1.  Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Key Vault-Ressource. 

1.  Wählen Sie unter **Einstellungen** die Option **Zugriffsrichtlinien** und dann **Zugriffsrichtlinie hinzufügen** aus:

    ![Auswählen von „Zugriffsrichtlinien“, Auswählen von „Rollenzuweisung hinzufügen“](../media/authentication/assign-policy-portal-01.png)

1.  Wählen Sie die gewünschten Berechtigungen unter **Zertifikatberechtigungen**, **Schlüsselberechtigungen** und **Geheimnisberechtigungen** aus. Sie können auch eine Vorlage auswählen, die gebräuchliche Kombinationen aus Berechtigungen enthält:

    ![Angeben von Zugriffsrichtlinienberechtigungen](../media/authentication/assign-policy-portal-02.png)

1. Wählen Sie unter **Prinzipal auswählen** den Link **Keine ausgewählt** aus, um den Auswahlbereich **Prinzipal** zu öffnen. Geben Sie den Namen des Benutzers, der App oder des Dienstprinzipals in das Suchfeld ein, wählen Sie das entsprechende Ergebnis und wählen Sie dann **Auswählen**.

    ![Auswählen des Sicherheitsprinzipals für die Zugriffsrichtlinie](../media/authentication/assign-policy-portal-03.png)

    Wenn Sie für die App eine verwaltete Identität verwenden, suchen Sie nach dem Namen der App, und wählen Sie ihn aus. (Weitere Informationen zu Sicherheitsprinzipalen finden Sie unter [Key Vault-Authentifizierung](authentication.md).
 
1.  Wählen Sie im Bereich **Zugriffsrichtlinie hinzufügen** die Option **Hinzufügen** aus, um die Zugriffsrichtlinie zu speichern.

    ![Hinzufügen der Zugriffsrichtlinie mit dem zugewiesenen Sicherheitsprinzipal](../media/authentication/assign-policy-portal-04.png)

1. Vergewissern Sie sich auf der Seite **Zugriffsrichtlinien**, dass Ihre Zugriffsrichtlinie unter **Aktuelle Zugriffsrichtlinien** aufgeführt ist, und wählen Sie dann **Speichern** aus. Zugriffsrichtlinien werden erst angewandt, wenn Sie sie speichern.

    ![Speichern der Änderungen an den Zugriffsrichtlinien](../media/authentication/assign-policy-portal-05.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Weitere Informationen zum Erstellen von Gruppen in Azure Active Directory mithilfe der Azure-Befehlszeilenschnittstelle finden Sie unter [az ad group create](/cli/azure/ad/group#az_ad_group_create) und [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Konfigurieren der Azure-Befehlszeilenschnittstelle und Anmelden

1. Wenn Sie Azure CLI-Befehle lokal ausführen möchten, installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).
 
    Um Befehle direkt in der Cloud auszuführen, verwenden Sie [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Nur lokale Befehlszeilenschnittstelle: Melden Sie sich mit `az login` bei Azure an:

    ```bash
    az login
    ```

    Der Befehl `az login` öffnet ggf. ein Browserfenster zum Erfassen von Anmeldeinformationen.

## <a name="acquire-the-object-id"></a>Abrufen der Objekt-ID

Ermitteln Sie die Objekt-ID der Anwendung, der Gruppe oder des Benutzers, der oder dem Sie die Zugriffsrichtlinie zuweisen möchten:

- Anwendungen und andere Dienstprinzipale: Verwenden Sie den Befehl [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list), um Ihre Dienstprinzipale abzurufen. Untersuchen Sie die Ausgabe des Befehls, um die Objekt-ID des Sicherheitsprinzipals zu ermitteln, dem die Zugriffsrichtlinie zugewiesen werden soll.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Gruppen: Verwenden Sie den Befehl [az ad group list](/cli/azure/ad/group#az_ad_group_list), und filtern Sie dabei die Ergebnisse mit dem Parameter `--display-name`:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Benutzer: Verwenden Sie den Befehl [az ad user show](/cli/azure/ad/user#az_ad_user_show), und übergeben Sie die E-Mail-Adresse des Benutzers im Parameter `--id`:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Zuweisen der Zugriffsrichtlinie
    
Verwenden Sie den Befehl [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy), um die gewünschten Berechtigungen zuzuweisen:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Ersetzen Sie `<object-id>` durch die Objekt-ID des Sicherheitsprinzipals.

Sie müssen `--secret-permissions`, `--key-permissions` und `--certificate-permissions` nur einschließen, wenn Sie diesen spezifischen Typen Berechtigungen zuweisen. Die zulässigen Werte für `<secret-permissions>`, `<key-permissions>` und `<certificate-permissions>` sind in der Dokumentation zu [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) angegeben.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Weitere Informationen zum Erstellen von Gruppen in Azure Active Directory mithilfe von Azure PowerShell finden Sie unter [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) und [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Konfigurieren von PowerShell und Anmelden

1. Um Befehle lokal auszuführen, installieren Sie [Azure PowerShell](/powershell/azure/), sofern dies noch nicht geschehen ist.

    Um Befehle direkt in der Cloud auszuführen, verwenden Sie [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Nur lokale PowerShell:

    1. Installieren sie das [Azure Active Directory PowerShell-Modul](https://www.powershellgallery.com/packages/AzureAD).

    1. Melden Sie sich bei Azure an:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Abrufen der Objekt-ID

Ermitteln Sie die Objekt-ID der Anwendung, der Gruppe oder des Benutzers, der oder dem Sie die Zugriffsrichtlinie zuweisen möchten:

- Anwendungen und andere Dienstprinzipale: Verwenden Sie das Cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) mit dem Parameter `-SearchString`, um die Ergebnisse nach dem Namen des gewünschten Dienstprinzipals zu filtern:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Gruppen: Verwenden Sie das Cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) mit dem Parameter `-SearchString`, um die Ergebnisse nach dem Namen der gewünschten Gruppe zu filtern:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    In der Ausgabe wird die Objekt-ID als `Id` aufgeführt.

- Benutzer: Verwenden Sie das Cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser), und übergeben Sie dabei die E-Mail-Adresse des Benutzers im Parameter `-UserPrincipalName`.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    In der Ausgabe wird die Objekt-ID als `Id` aufgeführt.

## <a name="assign-the-access-policy"></a>Zuweisen der Zugriffsrichtlinie

Verwenden Sie das Cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), um die Zugriffsrichtlinie zuzuweisen:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Sie müssen `-PermissionsToSecrets`, `-PermissionsToKeys` und `-PermissionsToCertificates` nur einschließen, wenn Sie diesen spezifischen Typen Berechtigungen zuweisen. Die zulässigen Werte für `<secret-permissions>`, `<key-permissions>` und `<certificate-permissions>` sind in der Dokumentation [Set-AzKeyVaultAccessPolicy – Parameter](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) angegeben.

---

## <a name="next-steps"></a>Nächste Schritte

- [Azure Key Vault – Sicherheit](security-features.md)
- [Entwicklerhandbuch zu Azure Key Vault](developers-guide.md)
