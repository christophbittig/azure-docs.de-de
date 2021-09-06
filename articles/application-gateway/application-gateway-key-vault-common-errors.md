---
title: Häufige Key Vault-Fehler in Application Gateway
titleSuffix: Azure Application Gateway
description: Dieser Artikel hilft ihnen, Probleme mit Key Vault zu identifizieren und zu beheben, um einen reibungslose Betrieb von Application Gateway zu gewährleisten.
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: 9be1caf74a7eeaa225c2025767eeef86053c17a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349391"
---
# <a name="common-key-vault-errors-in-application-gateway"></a>Häufige Key Vault-Fehler in Application Gateway

Dieser Leitfaden zur Problembehandlung hilft Ihnen dabei, die Details der Key Vault-Fehlercodes, deren Ursache und die zugeordnete Key Vault-Ressource, die das Problem verursacht, zu verstehen. Er enthält auch eine Schritt-für-Schritt-Anleitung für die Auflösung solcher Fehlkonfigurationen.

> [!NOTE]
> Die Protokolle für Key Vault-Diagnosen in Application Gateway werden alle vier Stunden generiert. Daher müssen Sie in einigen Fällen möglicherweise warten, bis die Protokolle aktualisiert werden, wenn die Diagnose weiterhin den Fehler zeigt, nachdem Sie die Konfiguration korrigiert haben.

> [!TIP]
> Es wird empfohlen, einen versionslosen Geheimnisbezeichner zu verwenden. Auf diese Weise rotiert Application Gateway das Zertifikat automatisch, wenn eine neuere Version in Key Vault verfügbar ist.  Ein Beispiel für einen Geheimnis-URI ohne Version: `https://myvault.vault.azure.net/secrets/mysecret/`.

### <a name="list-of-error-codes-and-their-details"></a>Liste der Fehlercodes und ihrer Details

[comment]: # (Fehlercode 1)
#### <a name="1-error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>**1) Fehlercode:** UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**Beschreibung**: Die zugeordnete, benutzerseitig zugewiesene verwaltete Identität verfügt nicht über die GET-Berechtigung. 

**Lösung**: Konfigurieren Sie die Zugriffsrichtlinie von Key Vault so, dass der zugeordneten, benutzerseitig zugewiesenen verwalteten Identität GET-Berechtigungen für Geheimnisse gewährt werden. 
1. Navigieren Sie zum verknüpften Key Vault im Azure-Portal.
2. Öffnen Sie das Blatt „Zugriffsrichtlinien“.
3. Wählen Sie „Tresorzugriffsrichtlinie“ als Berechtigungsmodell aus.
4. Wählen Sie die Berechtigung „Get“ für das „Geheimnis“ der angegebenen benutzerseitig zugewiesenen verwalteten Identität aus.
5. Speichern der Konfiguration


:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text=" Benutzerseitig zugewiesene verwaltete Identität besitzt keine GET-Berechtigung für Key Vault.":::

Eine vollständige Anleitung zur Key Vault-Zugriffsrichtlinie finden Sie in diesem [Artikel](../key-vault/general/assign-access-policy-portal.md).
</br></br>



[comment]: # (Fehlercode 2)
#### <a name="2-error-code-secretdisabled"></a>**2) Fehlercode:** SecretDisabled 

**Beschreibung**: Das zugeordnete Zertifikat wurde in Key Vault deaktiviert. 

**Lösung**: Aktivieren Sie die Zertifikatversion erneut, die derzeit für Application Gateway verwendet wird.
1. Navigieren Sie zum verknüpften Key Vault im Azure-Portal.
2. Öffnen Sie das Blatt „Zertifikate“.
3. Klicken Sie auf den erforderlichen Zertifikatnamen und dann auf die deaktivierte Version.
4. Verwenden Sie die Umschaltfläche auf der Verwaltungsseite, um diese Zertifikatversion zu aktivieren.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="Erneutes Aktivieren eines Geheimnisses.":::
</br></br>


[comment]: # (Fehlercode 3)
#### <a name="3-error-code-secretdeletedfromkeyvault"></a>**3) Fehlercode**: SecretDeletedFromKeyVault 

**Beschreibung**: Das zugeordnete Zertifikat wurde in Key Vault gelöscht. 

**Lösung**: Das gelöschte Zertifikatobjekt in einem Key Vault kann mithilfe des Wiederherstellungsfeatures für vorläufiges Löschen (Soft-Delete) wiederhergestellt werden. So stellen Sie ein gelöschtes Zertifikat wieder her 
1. Navigieren Sie zum verknüpften Key Vault im Azure-Portal.
2. Öffnen Sie das Blatt „Zertifikate“.
3. Verwenden Sie die Registerkarte „Verwaltete gelöschte Zertifikate“, um ein gelöschtes Zertifikat wiederherzustellen.

Andererseits müssen Sie, wenn ein Zertifikatobjekt endgültig gelöscht wurde, ein neues Zertifikat erstellen und das Application Gateway mit den neuen Zertifikatdetails aktualisieren. Bei der Konfiguration über Azure CLI oder Azure PowerShell empfiehlt es sich, einen versionslosen Geheimnisbezeichner-URI zu verwenden, damit Instanzen eine erneuerte Version des Zertifikats abrufen können, sofern vorhanden.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Wiederherstellen eines gelöschten Zertifikats in Key Vault.":::
</br></br>


[comment]: # (Fehlercode 4)
#### <a name="4-error-code-userassignedmanagedidentitynotfound"></a>**4) Fehlercode**: UserAssignedManagedIdentityNotFound 

**Beschreibung**: Die zugeordnete, benutzerseitig zugewiesene verwaltete Identität wurde gelöscht. 

**Lösung**: Befolgen Sie die unten stehenden Anweisungen, um dieses Problem zu beheben.
1. Erstellen Sie erneut eine verwaltete Identität mit demselben Namen, der zuvor verwendet wurde, und unter derselben Ressourcengruppe. Weitere Informationen finden Sie unter „Ressourcenaktivitätsprotokolle“. 
2. Nachdem die neue verwaltete Identität erstellt wurde, weisen Sie dieser mindestens die Rolle „Leser“ unter „Application Gateway – Zugriffssteuerung (IAM)“ zu.
3. Navigieren Sie schließlich zur gewünschten Key Vault-Ressource, und legen Sie deren Zugriffsrichtlinien fest, um dieser neuen verwalteten Identität „GET“-Berechtigungen für das Geheimnis zu gewähren. 

[Weitere Informationen](./key-vault-certs.md#how-integration-works)
</br></br>

[comment]: # (Fehlercode 5)
#### <a name="5-error-code-keyvaulthasrestrictedaccess"></a>**5) Fehlercode**: KeyVaultHasRestrictedAccess

**Beschreibung**: Eingeschränkte Netzwerkeinstellung für Key Vault. 

**Lösung**: Dieses Problem tritt auf, wenn Sie die Key Vault-Firewall für eingeschränkten Zugriff aktivieren. Sie können Ihre Application Gateway-Instanz weiterhin in einem eingeschränkten Key Vault-Netzwerk wie folgt konfigurieren.
1. Auf dem Blatt „Netzwerk“ von Key Vault
2. Wählen Sie auf der Registerkarte „Firewall und virtuelle Netzwerke“ die Option „Privater Endpunkt und ausgewählte Netzwerke“ aus.
3. Wählen Sie abschließend „Ja“ aus, damit vertrauenswürdige Dienste die Key Vault-Firewall umgehen können.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Key Vault hat eingeschränkten Zugriff.":::
</br></br>


[comment]: # (Fehlercode 6)
#### <a name="6-error-code-keyvaultsoftdeleted"></a>**6) Fehlercode:** KeyVaultSoftDeleted 

**Beschreibung**: Der zugeordnete Key Vault befindet sich im Zustand „Vorläufiges Löschen“ (Soft-Delete). 

**Lösung**: Das Wiederherstellen eines vorläufig gelöschten Key Vault ist recht einfach. Wechseln Sie im Azure-Portal zur Dienstseite „Key Vaults“.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Suchen Sie nach dem Key Vault-Dienst.":::
</br></br>
Klicken Sie auf die Registerkarte „Verwaltete gelöschte Tresore“. Von hier aus können Sie die gelöschte Key Vault-Ressource finden und wiederherstellen.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="Wiederherstellen eines gelöschten Key Vault mithilfe von „Vorläufiges Löschen“ (Soft-Delete).":::
</br></br>


[comment]: # (Fehlercode 7)
#### <a name="7-error-code-customerkeyvaultsubscriptiondisabled"></a>**7) Fehlercode**: CustomerKeyVaultSubscriptionDisabled 

**Beschreibung**: Das Abonnement für Key Vault ist deaktiviert. 

**Lösung**: Ihr Azure-Abonnement kann aus verschiedenen Gründen deaktiviert werden. Lesen Sie im Leitfaden zum [Reaktivieren eines deaktivierten Azure-Abonnements](../cost-management-billing/manage/subscription-disabled.md) nach, und ergreifen Sie die erforderlichen Maßnahmen.
</br></br>



