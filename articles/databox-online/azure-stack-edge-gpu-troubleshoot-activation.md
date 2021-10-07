---
title: Behandeln von Aktivierungsfehlern bei Azure Stack Edge Pro mit GPU im Azure-Portal | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Probleme bei der Aktivierung von Azure Stack Edge Pro mit GPU und Schlüsseltresorprobleme behandeln können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 1f6729fc0a723a21f66380a397a222bef23cba9e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750306"
---
# <a name="troubleshoot-activation-or-secret-deletion-issues-on-azure-key-vault-for-your-azure-stack-edge-pro-gpu-device"></a>Behandeln von Problemen beim Aktivieren oder Löschen von Geheimnissen in Azure Key Vault für Ihr Azure Stack Edge Pro-GPU-Gerät 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie Aktivierungsprobleme auf Ihrem „Azure Stack Edge Pro mit GPU“-Gerät behandeln können. 


## <a name="activation-errors"></a>Aktivierungsfehler

In der folgenden Tabelle werden die Fehler im Zusammenhang mit der Geräteaktivierung und die jeweils empfohlene Lösung zusammengefasst.

| Fehlermeldung| Empfohlene Lösung |
|------------------------------------------------------|--------------------------------------|
| Wenn die zur Aktivierung verwendete Azure Key Vault-Instanz gelöscht wird, bevor das Gerät mit dem Aktivierungsschlüssel aktiviert wird, wird diese Fehlermeldung angezeigt. <br> ![Schlüsseltresorfehler 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Wenn der Schlüsseltresor gelöscht wurde, können Sie ihn wiederherstellen, wenn sich der Tresor in der Löschschutzphase befindet. Führen Sie die Schritte in [Wiederherstellen eines Schlüsseltresors](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) aus. <br>Wenn die Dauer des Löschschutzes abgelaufen ist, müssen Sie über das Blatt [Wiederherstellen eines Schlüsseltresors](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) einen neuen Schlüsseltresor erstellen. |
| Wenn die Azure Key Vault-Instanz gelöscht wird, nachdem das Gerät aktiviert wurde, und Sie dann versuchen, Vorgänge auszuführen, die eine Verschlüsselung umfassen , z. B. **Benutzer hinzufügen,** **Freigabe hinzufügen** oder **Compute konfigurieren**, erhalten Sie diesen Fehler. <br> ![Schlüsseltresorfehler 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Wenn der Schlüsseltresor gelöscht wurde, können Sie ihn wiederherstellen, wenn sich der Tresor in der Löschschutzphase befindet. Führen Sie die Schritte in [Wiederherstellen eines Schlüsseltresors](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) aus. <br>Wenn die Dauer des Löschschutzes abgelaufen ist, müssen Sie wie unter [Integration von Azure Key Vault in Azure Stack Edge](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) beschrieben einen neuen Schlüsseltresor erstellen. |
| Wenn bei der Generierung des Aktivierungsschlüssels ein Fehler auftritt, wird dieser Fehler gemeldet. Die Benachrichtigung enthält weitere Details. <br> ![Schlüsseltresorfehler 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Stellen Sie sicher, dass die in [Zugreifen auf Azure Key Vault hinter einer Firewall](../key-vault/general/access-behind-firewall.md) angegebenen Ports und URLs in Ihrer Firewall geöffnet sind, damit der Zugriff auf den Schlüsseltresor möglich ist. Warten Sie ein paar Minuten, bevor Sie den Vorgang wiederholen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht. |
| Wenn der Benutzer über Nur-Lese-Berechtigungen verfügt, ist er nicht berechtigt, einen Aktivierungsschlüssel zu generieren, und dieser Fehler wird gemeldet. <br> ![Schlüsseltresorfehler 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Dies liegt möglicherweise daran, dass Sie nicht über die richtigen Zugriffsrechte verfügen oder `Microsoft.KeyVault` nicht registriert ist.<li>Stellen Sie sicher, dass Sie auf der Ressourcengruppenebene, die für Ihre Azure Stack Edge-Ressource verwendet wird, über Besitzer- oder Mitwirkendenzugriff verfügen.</li><li>Vergewissern Sie sich, dass der Ressourcenanbieter `Microsoft.KeyVault` registriert ist. Um einen Ressourcenanbieter zu registrieren, navigieren Sie zu dem Abonnement, das für die Azure Stack Edge-Ressource verwendet wird. Wechseln Sie zu **Ressourcenanbieter**, suchen Sie nach *Microsoft.KeyVault*, und wählen Sie **Registrieren** aus. Weitere Informationen finden Sie unter [Registrieren von Ressourcenanbietern](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).</li> |


## <a name="unregistered-resource-provider-errors"></a>Fehler bei nicht registrierten Ressourcenanbietern

| Fehlermeldung| Empfohlene Lösung |
|------------------------------------------------------|--------------------------------------|
| Wenn der Key Vault-Ressourcenanbieter nicht registriert ist, wird beim Erstellen eines Schlüsseltresors während der Aktivierungsschlüsselgenerierung ein Fehler angezeigt. <br> <!--![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Der Aktivierungsschlüssel wird nicht generiert. <br>Vergewissern Sie sich, dass der Ressourcenanbieter `Microsoft.KeyVault` registriert ist. Um einen Ressourcenanbieter zu registrieren, navigieren Sie zu dem Abonnement, das für die Azure Stack Edge-Ressource verwendet wird. Wechseln Sie zu **Ressourcenanbieter**, suchen Sie nach *Microsoft.KeyVault*, und wählen Sie **Registrieren** aus. <br>Weitere Informationen finden Sie unter [Registrieren von Ressourcenanbietern](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).</li> |
| Wenn der Storage-Ressourcenanbieter nicht registriert ist, wird beim Erstellen eines Speicherkontos für Überwachungsprotokolle ein Fehler angezeigt. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Dieser Fehler ist kein Blockierungsfehler, und der Aktivierungsschlüssel wird generiert. <br>Der Storage-Ressourcenanbieter wird in der Regel automatisch registriert. Falls dies jedoch nicht der Fall ist, führen Sie die Schritte unter [Registrieren von Ressourcenanbietern](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers) aus, um `Microsoft.Storage` für Ihr Abonnement zu registrieren.  |

## <a name="key-vault-or-secret-deletion-errors"></a>Fehler beim Löschen von Schlüsseltresoren oder Geheimnissen

| Fehlermeldung| Empfohlene Lösung |
|------------------------------------------------------|--------------------------------------|
| Wenn der Kanalintegritätsschlüssel in der Azure Key Vault-Instanz gelöscht wurde, und Sie versuchen, Vorgänge auszuführen, die eine Verschlüsselung umfassen (z. B. **Benutzer hinzufügen**, **Freigabe hinzufügen** oder **Compute konfigurieren**), erhalten Sie diesen Fehler.  |Wenn der Kanalintegritätsschlüssel im Schlüsseltresor gelöscht wurde, aber noch innerhalb der Löschschutzphase liegt, führen Sie die Schritte im Artikel zu [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval) aus. <br>Wenn die Löschschutzphase abgelaufen ist und Sie den Schlüssel gesichert haben, können Sie den Schlüssel aus der Sicherung wiederherstellen. Andernfalls können Sie den Schlüssel nicht wiederherstellen. Erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten. |
<!--|Wenn Sie dieses Element zusammen mit Noopur überprüft haben, können Sie es entfernen. Der Kunde muss keine Aktion durchführen. – Wenn der Kanalintegritätsschlüssel in Azure Key Vault vor der Aktivierung des Geräts gelöscht wurde und der Aktivierungsschlüssel neu generiert wird, erhalten Sie diesen Fehler. <br> ![Schlüsseltresorfehler 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png) | Der Kanalintegritätsschlüssel wird neu erstellt, und die Metadaten werden aktualisiert.
|-->

## <a name="audit-logging-errors"></a>Überwachungsprotokollierungsfehler

| Fehlermeldung| Empfohlene Lösung |
|------------------------------------------------------|--------------------------------------|
| Wenn bei der Erstellung der Diagnoseeinstellung für Ihren Schlüsseltresor ein Fehler auftritt, wird dieser Fehler angezeigt. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Dies ist kein Blockierungsfehler, und der Aktivierungsschlüssel wird generiert. <br> Sie können [Diagnoseeinstellungen zum Speichern Ihrer Überwachungsprotokolle manuell erstellen](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs). |
| Wenn bei der Erstellung des Speicherkontos ein Fehler auftritt, da beispielsweise für den von Ihnen angegebenen Namen bereits ein Konto vorhanden ist, wird dieser Fehler angezeigt. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Sie können manuell ein Speicherkonto erstellen und es mit der Diagnoseeinstellung in Ihrem Schlüsseltresor verknüpfen. Dieses Konto wird dann zum Speichern von Überwachungsprotokollen verwendet. <br> Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos für Ihre Protokolle](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs).  |
|Wenn die systemseitig zugewiesene verwaltete Identität für Ihre Azure Stack Edge-Ressource gelöscht wird, wird dieser Fehler angezeigt. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Auf dem Blatt „Sicherheit“ wird eine Warnung für Ihre Azure Stack Edge- Ressource angezeigt. Klicken Sie auf diese Warnung, um [über das Blatt „Wiederherstellen eines Schlüsseltresors“ eine neue verwaltete Identität zu erstellen](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).  |
| Wenn die verwaltete Identität keinen Zugriff auf den Schlüsseltresor hat, wird dieser Fehler angezeigt. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Auf dem Blatt „Sicherheit“ wird eine Warnung für Ihre Azure Stack Edge- Ressource angezeigt. Klicken Sie auf diese Warnung, um [einer verwalteten Identität über das Blatt „Wiederherstellen eines Schlüsseltresors“ Zugriff auf den Schlüsseltresor zu gewähren](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).  |

## <a name="resource-move-errors"></a>Fehler beim Verschieben von Ressourcen

| Fehlermeldung| Empfohlene Lösung |
|------------------------------------------------------|--------------------------------------|
| Wenn die Key Vault-Ressource zwischen Ressourcengruppen oder Abonnements verschoben wird, wird dieser Fehler angezeigt. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Das Verschieben von Key Vault-Ressourcen wird genauso behandelt wie das Löschen des Schlüsseltresors. Sie können den Schlüsseltresor wiederherstellen, wenn sich dieser in der Löschschutzphase befindet. Wenn die Dauer des Löschschutzes abgelaufen ist, müssen Sie einen neuen Schlüsseltresor erstellen. Weitere Informationen zu einem der oben genannten Fälle finden Sie unter [Integration von Azure Key Vault in Azure Stack Edge](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).    |
| Wenn das von Ihnen verwendete Abonnement mandantenübergreifend verschoben wird, wird dieser Fehler angezeigt. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Konfigurieren Sie die verwaltete Identität neu, und erstellen Sie einen neuen Schlüsseltresor. Sie können die Key Vault-Ressource auch verschieben. In diesem Fall muss nur die verwaltete Identität neu konfiguriert werden. Informationen zu allen zuvor genannten Fällen finden Sie unter [Integration von Azure Key Vault in Azure Stack Edge](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).   |
| Wenn die Speicherkontoressource, die für Überwachungsprotokolle verwendet wird, zwischen Ressourcengruppen oder Abonnements verschoben wird, wird kein Fehler angezeigt.  | Sie können [ein neues Speicherkonto erstellen und für das Speichern der Überwachungsprotokolle konfigurieren](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs). |

## <a name="other-errors"></a>Sonstige Fehler

| Fehlermeldung| Empfohlene Lösung |
|------------------------------------------------------|--------------------------------------|
| Wenn die Netzwerkeinschränkungen für den Schlüsseltresor konfiguriert sind, wird dieser Fehler angezeigt. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Der Dienst kann aufgrund von Netzwerkeinschränkungen nicht zwischen dem Löschen eines Schlüsseltresors oder dem Szenario unterscheiden, in dem kein Zugriff auf den Schlüsseltresor möglich ist. In jedem Fall werden Sie zum Blatt [Wiederherstellen eines Schlüsseltresors](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) weitergeleitet.     |


## <a name="next-steps"></a>Nächste Schritte

- [Installieren von Azure Stack Edge Pro mit GPU](azure-stack-edge-gpu-deploy-install.md)
