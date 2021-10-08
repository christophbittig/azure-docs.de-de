---
title: Häufige Schlüsseltresorfehler in Application Gateway
titleSuffix: Azure Application Gateway
description: In diesem Artikel werden die wichtigsten Probleme im Zusammenhang mit dem Schlüsseltresor identifiziert, und Sie erhalten Hilfe bei der Behebung dieser Probleme, um einen reibungslosen Betrieb von Application Gateway sicherzustellen.
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: 4f873e6fb751f3b368d86413c201b70634d9c7ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644085"
---
# <a name="common-key-vault-errors-in-azure-application-gateway"></a>Häufige Schlüsseltresorfehler in Azure Application Gateway

Dieser Artikel hilft Ihnen, die Details der Fehlercodes des Schlüsseltresors zu verstehen, die auftreten könnten, einschließlich der Ursachen für diese Fehler. Dieser Artikel enthält auch Schritte zum Beheben solcher Konfigurationsfehler.

> [!TIP]
> Verwenden Sie einen geheimen Bezeichner, der keine Version angibt. Auf diese Weise rotiert Azure Application Gateway das Zertifikat automatisch, wenn eine neuere Version in Azure Key Vault verfügbar ist. Ein Beispiel für einen Geheimnis-URI ohne Version ist: `https://myvault.vault.azure.net/secrets/mysecret/`.

## <a name="list-of-error-codes-and-their-details"></a>Liste der Fehlercodes und ihrer Details

In den folgenden Abschnitten werden verschiedene Fehler behandelt, die auftreten können. Sie können die Details in Azure Advisor finden und diesen Artikel zur Problembehandlung verwenden. Weitere Informationen finden Sie unter [Erstellen von Azure Advisor-Warnungen zu neuen Empfehlungen mithilfe des Azure-Portals](https://docs.microsoft.com/azure/advisor/advisor-alerts-portal).

> [!NOTE]
> Azure Application Gateway generiert alle vier Stunden Protokolle für die Schlüsseltresordiagnose. Wenn die Diagnose weiterhin den Fehler anzeigt, nachdem Sie die Konfiguration korrigiert haben, müssen Sie möglicherweise warten, bis die Protokolle aktualisiert werden.

[comment]: # (Fehlercode 1)
### <a name="error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>Fehlercode: UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**Beschreibung**: Die zugeordnete, benutzerseitig zugewiesene verwaltete Identität verfügt nicht über die Berechtigung „Get“ (Abrufen). 

**Lösung**: Konfigurieren Sie die Zugriffsrichtlinie von Key Vault, um der vom Benutzer zugewiesenen verwalteten Identität diese Berechtigung für Geheimnisse zu gewähren. 
1. Wechseln Sie zum verknüpften Schlüsseltresor im Azure-Portal.
1. Öffnen Sie den Bereich **Zugriffsrichtlinien**.
1. Wählen Sie für das **Berechtigungsmodell** die Option **Tresorzugriffsrichtlinie** aus.
1. Wählen Sie unter **Verwaltungsvorgänge für Geheimnisse** die Berechtigung **Get** (Abrufen) aus.
1. Wählen Sie **Speichern** aus.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text="Screenshot: Beheben des Fehlers bei der Berechtigung „Get“":::

Weitere Informationen finden Sie unter [Zuweisen einer Key Vault-Zugriffsrichtlinie über das Azure-Portal](../key-vault/general/assign-access-policy-portal.md).

[comment]: # (Fehlercode 2)
### <a name="error-code-secretdisabled"></a>Fehlercode: SecretDisabled 

**Beschreibung**: Das zugeordnete Zertifikat wurde in Key Vault deaktiviert. 

**Lösung**: Aktivieren Sie die Zertifikatversion erneut, die derzeit für Application Gateway verwendet wird.
1. Wechseln Sie zum verknüpften Schlüsseltresor im Azure-Portal.
1. Öffnen Sie den Bereich **Zertifikate**.
1. Wählen Sie den Namen des gewünschten Zertifikats und dann die deaktivierte Version aus.
1. Verwenden Sie auf der Verwaltungsseite die Umschaltfläche, um diese Zertifikatsversion zu aktivieren.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="Screenshot: Erneutes Aktivieren eines Geheimnisses":::

[comment]: # (Fehlercode 3)
### <a name="error-code-secretdeletedfromkeyvault"></a>Fehlercode: SecretDeletedFromKeyVault 

**Beschreibung**: Das zugeordnete Zertifikat wurde in Key Vault gelöscht. 

**Lösung:** So stellen Sie ein gelöschtes Zertifikat wieder her: 
1. Wechseln Sie zum verknüpften Schlüsseltresor im Azure-Portal.
1. Öffnen Sie den Bereich **Zertifikate**.
1. Verwenden Sie die Registerkarte **Verwaltete gelöschte Zertifikate**, um ein gelöschtes Zertifikat wiederherzustellen.

Andererseits müssen Sie, wenn ein Zertifikatobjekt endgültig gelöscht wurde, ein neues Zertifikat erstellen und Application Gateway mit den neuen Zertifikatdetails aktualisieren. Verwenden Sie bei der Konfiguration über die Azure CLI oder Azure PowerShell den URI eines geheimen Bezeichners ohne Version. Durch diese Auswahl können Instanzen eine erneuerte Version des Zertifikats abrufen, sofern vorhanden.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Screenshot: Wiederherstellen eines gelöschten Zertifikats in Key Vault":::

[comment]: # (Fehlercode 4)
### <a name="error-code-userassignedmanagedidentitynotfound"></a>Fehlercode: UserAssignedManagedIdentityNotFound 

**Beschreibung**: Die zugeordnete, benutzerseitig zugewiesene verwaltete Identität wurde gelöscht. 

**Lösung:** So verwenden Sie die Identität erneut:
1. Erstellen Sie erneut eine verwaltete Identität mit demselben Namen, der zuvor verwendet wurde, und unter derselben Ressourcengruppe. Ressourcenaktivitätsprotokolle enthalten weitere Details. 
1. Nachdem Sie die Identität erstellt haben, wechseln Sie zu **Application Gateway – Zugriffssteuerung (IAM)** . Weisen Sie der Identität mindestens die Rolle **Leser** zu.
1. Wechseln Sie schließlich zur gewünschten Key Vault-Ressource, und legen Sie die Zugriffsrichtlinien so fest, dass **Get** für diese neue verwaltete Identität geheime Berechtigungen erteilt werden. 

Weitere Informationen finden Sie unter [Funktionsweise der Integration](./key-vault-certs.md#how-integration-works).

[comment]: # (Fehlercode 5)
### <a name="error-code-keyvaulthasrestrictedaccess"></a>Fehlercode: KeyVaultHasRestrictedAccess

**Beschreibung:** Es gibt eine eingeschränkte Netzwerkeinstellung für Key Vault. 

**Lösung**: Dieser Fehler tritt auf, wenn Sie die Key Vault-Firewall für eingeschränkten Zugriff aktivieren. Sie können Application Gateway auch in einem eingeschränkten Netzwerk von Key Vault konfigurieren, indem Sie die folgenden Schritte ausführen:
1. Öffnen Sie in Key Vault den Bereich **Netzwerk**.
1. Wählen Sie auf der Registerkarte **Firewalls und virtuelle Netzwerke** die Option **Privater Endpunkt und ausgewählte Netzwerke** aus.
1. Verwenden Sie dann „Virtuelle Netzwerke“, und fügen Sie das virtuelle Netzwerk und Subnetz Ihrer Application Gateway-Instanz hinzu. Konfigurieren Sie während des Prozesses auch den Dienstendpunkt „Microsoft.KeyVault“, indem Sie das zugehörige Kontrollkästchen aktivieren.
1. Wählen Sie abschließend **Ja** aus, damit vertrauenswürdige Dienste die Key Vault-Firewall umgehen können.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Screenshot: Vorgehensweise beim Umgehen des Fehlers aufgrund des eingeschränkten Netzwerks":::

[comment]: # (Fehlercode 6)
### <a name="error-code-keyvaultsoftdeleted"></a>Fehlercode: KeyVaultSoftDeleted 

**Beschreibung**: Der zugeordnete Schlüsseltresor befindet sich im Zustand „Vorläufiges Löschen“ (Soft-Delete). 

**Lösung**: Suchen Sie im Azure-Portal nach *Schlüsseltresor*. Wählen Sie unter **Dienste** die Option **Schlüsseltresore** aus.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Screenshot: Suche nach dem Key Vault-Dienst":::

Wählen Sie **Verwaltete gelöschte Tresore** aus. Von hier aus können Sie die gelöschte Key Vault-Ressource finden und wiederherstellen.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="Screenshot: Wiederherstellen eines gelöschten Schlüsseltresors":::

[comment]: # (Fehlercode 7)
### <a name="error-code-customerkeyvaultsubscriptiondisabled"></a>Fehlercode: CustomerKeyVaultSubscriptionDisabled 

**Beschreibung**: Das Abonnement für Key Vault ist deaktiviert. 

**Lösung**: Ihr Azure-Abonnement kann aus verschiedenen Gründen deaktiviert werden. Informationen zu den erforderlichen Maßnahmen für die Lösung finden Sie unter [Reaktivieren eines deaktivierten Azure-Abonnements](../cost-management-billing/manage/subscription-disabled.md).

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel zur Problembehandlung können hilfreich sein, wenn Sie weiterhin Application Gateway verwenden:

- [Übersicht über Resource Health für Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/resource-health-overview)
- [Behandeln von Problemen mit der Azure Application Gateway-Sitzungsaffinität](https://docs.microsoft.com/azure/application-gateway/how-to-troubleshoot-application-gateway-session-affinity-issues)
