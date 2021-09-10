---
title: 'Azure ExpressRoute: Konfigurieren von MACsec'
description: In diesem Artikel wird erläutert, wie Sie MACsec zum Sichern der Verbindungen zwischen Ihren Edgeroutern und den Edgeroutern von Microsoft konfigurieren.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 445010e345e92913f32705fc801a6521703ba84f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350872"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Konfigurieren von MACsec für ExpressRoute Direct-Ports

In diesem Artikel wird erläutert, wie Sie mithilfe von PowerShell MACsec zum Sichern der Verbindungen zwischen Ihren Edgeroutern und den Edgeroutern von Microsoft konfigurieren.

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie mit der Konfiguration beginnen:

* Sie sind mit den [Workflows zur Bereitstellung von ExpressRoute Direct](expressroute-erdirect-about.md) vertraut.
* Sie haben eine [ExpressRoute Direct-Portressource](expressroute-howto-erdirect.md) erstellt.
* Wenn Sie PowerShell lokal ausführen möchten, stellen Sie sicher, dass die neueste Version von Azure PowerShell auf Ihrem Computer installiert ist.

### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Anmelden und Auswählen des richtigen Abonnements

Um mit der Konfiguration zu beginnen, melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie das zu verwendende Abonnement aus.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Erstellen Sie den Azure Schlüsseltresor, MACsec-Geheimnisse und die Benutzeridentität

1. Erstellen Sie eine Key Vault-Instanz, um MACsec-Geheimnisse in einer neuen Ressourcengruppe zu speichern.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Wenn Sie bereits über einen Schlüsseltresor oder eine Ressourcengruppe verfügen, können Sie diese wiederverwenden. Es ist jedoch wichtig, dass Sie das [**vorläufige Löschen**](../key-vault/general/soft-delete-overview.md) für Ihren vorhandenen Schlüsseltresor aktivieren. Wenn das vorläufige Löschen nicht aktiviert ist, können Sie die Funktion mit den folgenden Befehlen aktivieren:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Erstellen Sie eine Benutzeridentität.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Wenn New-AzUserAssignedIdentity nicht als gültiges PowerShell-Cmdlet erkannt wird, installieren Sie das folgende Modul (im Administratormodus), und führen Sie den obigen Befehl erneut aus.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Erstellen Sie einen CAK (Connectivity Association Key) und einen CKN (Connectivity Association Key Name), und speichern Sie diese im Schlüsseltresor.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
   > [!NOTE]
   > Der CKN muss eine Zeichenfolge mit einer geraden Anzahl von bis zu 64 Zeichen im Hexadezimalformat (0–9, A–F) sein.
   >
   > Die CAK-Länge hängt vom angegebenen Verschlüsselungsverfahren ab:
   >
   > * Für GcmAes128 muss der CAK eine Zeichenfolge mit einer geraden Anzahl von bis zu 32 Zeichen im Hexadezimalformat (0–9, A–F) sein.
   >
   > * Für GcmAes256 muss der CAK eine Zeichenfolge mit einer geraden Anzahl von bis zu 64 Zeichen im Hexadezimalformat (0–9, A–F) sein.
   >

4. Weisen Sie der Benutzeridentität die GET-Berechtigung zu.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Nun kann diese Identität die Geheimnisse (z. B. CAK und CKN) aus dem Schlüsseltresor abrufen.
5. Legen Sie diese Benutzeridentität für die Verwendung durch ExpressRoute fest.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Konfigurieren Sie MACsec für ExpressRoute Direkt-Ports

### <a name="to-enable-macsec"></a>So aktivieren Sie MACsec

Jede ExpressRoute Direct-Instanz verfügt über zwei physische Ports. Sie können MACsec an beiden Ports gleichzeitig oder an einem Port aktivieren. Bei der Aktivierung an einem Port (durch Umschalten des Datenverkehrs an einen aktiven Port, während der andere Port gewartet wird) können Sie die Unterbrechung minimieren, wenn ExpressRoute Direct bereits in Betrieb ist.

1. Legen Sie MACsec-Geheimnisse und -Verschlüsselungsverfahren fest, und ordnen Sie die Benutzeridentität dem Port zu, sodass der ExpressRoute-Verwaltungscode bei Bedarf auf die MACsec-Geheimnisse zugreifen kann.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (Optional) Wenn die Ports den Status „Administrative Down“ aufweisen, können Sie die folgenden Befehle ausführen, um die Ports wieder zu aktivieren.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    An diesem Punkt wird MACsec an den ExpressRoute Direct-Ports auf der Microsoft-Seite aktiviert. Wenn Sie die Konfiguration auf Ihren Edgegeräten noch nicht ausgeführt haben, können Sie diese im Folgenden mit denselben MACsec-Geheimnissen und -Verschlüsselungsverfahren konfigurieren.

### <a name="to-disable-macsec"></a>So deaktivieren Sie MACsec

Wenn Sie MACsec nicht mehr auf Ihrer ExpressRoute Direct-Instanz benötigen, können Sie die folgenden Befehle ausführen, um MACsec zu deaktivieren.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

An diesem Punkt wird MACsec an den ExpressRoute Direct-Ports auf der Microsoft-Seite deaktiviert.

### <a name="test-connectivity"></a>Testen der Konnektivität
Wenn Sie MACsec (einschließlich MACsec-Schlüsselaktualisierung) an Ihren ExpressRoute Direct-Ports konfiguriert haben, [überprüfen](expressroute-troubleshooting-expressroute-overview.md) Sie, ob die BGP-Sitzungen der Verbindungen aktiv sind und ausgeführt werden. Wenn noch keine Verbindung mit den Ports vorhanden ist, erstellen Sie zuerst eine, und richten Sie das private Azure-Peering oder das Microsoft-Peering für die Verbindung ein. Wenn MACsec falsch konfiguriert ist (u. a. bei nicht übereinstimmenden MACsec-Schlüsseln zwischen Ihren Netzwerkgeräten und den Netzwerkgeräten von Microsoft), werden die ARP-Auflösung auf Ebene 2 und die BGP-Einrichtung auf Ebene 3 nicht angezeigt. Wenn alle Einstellungen ordnungsgemäß konfiguriert sind, wird angezeigt, dass die BGP-Routen ordnungsgemäß in beiden Richtungen angekündigt werden und Ihre Daten entsprechend über ExpressRoute fließen.

## <a name="next-steps"></a>Nächste Schritte
1. [Erstellen einer ExpressRoute-Verbindung in ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Verknüpfen einer ExpressRoute-Leitung mit einem virtuellen Azure-Netzwerk](expressroute-howto-linkvnet-arm.md)
3. [Überprüfen der ExpressRoute-Konnektivität](expressroute-troubleshooting-expressroute-overview.md)
