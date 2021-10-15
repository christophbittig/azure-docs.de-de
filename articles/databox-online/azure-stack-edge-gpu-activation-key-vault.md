---
title: Integration von Azure Key Vault in eine Azure Stack Edge-Ressource und Geräteaktivierung
description: Hier wird beschrieben, wie Azure Key Vault während der Aktivierung eines Azure Stack Edge Pro-Geräts mit der Verwaltung von Geheimnissen in Verbindung steht.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 67cddd1839e666d4908706a1bbdaccbbd3565704
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351441"
---
# <a name="manage-azure-stack-edge-secrets-using-azure-key-vault"></a>Verwalten von Azure Stack Edge-Geheimnissen mithilfe von Azure Key Vault 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault wird zur Verwaltung von Geheimnissen in eine Azure Stack Edge-Ressource integriert. Dieser Artikel enthält ausführliche Informationen zum Erstellen einer Azure Key Vault-Instanz für eine Azure Stack Edge-Ressource während der Geräteaktivierung und der anschließenden Nutzung für die Verwaltung von Geheimnissen. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Informationen zu Key Vault und Azure Stack Edge

Mit dem Azure Key Vault-Clouddienst werden Token, Kennwörter, Zertifikate, API-Schlüssel und andere Geheimnisse sicher gespeichert und der Zugriff darauf gesteuert. Key Vault vereinfacht auch das Erstellen und Verwalten der zur Verschlüsselung Ihrer Daten verwendeten Verschlüsselungsschlüssel. 

Für den Azure Stack Edge-Dienst bringt die Integration in Key Vault die folgenden Vorteile:

- Speicherung von Kundengeheimnissen. Eines der für den Azure Stack Edge-Dienst verwendeten Geheimnisse ist der Kanalintegritätsschlüssel (Channel Integrity Key, CIK). Dieser Schlüssel ermöglicht die Verschlüsselung Ihrer Geheimnisse und ist sicher in Key Vault gespeichert. Gerätegeheimnisse wie BitLocker-Wiederherstellungsschlüssel und BMC-Benutzerkennwort (Baseboard-Verwaltungscontroller) werden ebenfalls in Key Vault gespeichert. 

    Weitere Informationen finden Sie unter [Sicheres Speichern von Geheimnissen und Schlüsseln](../key-vault/general/overview.md#securely-store-secrets-and-keys).
- Übergabe verschlüsselter Kundengeheimnisse an das Gerät.
- Anzeige von Gerätegeheimnissen für den einfachen Zugriff bei einem Ausfall des Geräts.


## <a name="generate-activation-key-and-create-key-vault"></a>Generieren eines Aktivierungsschlüssels und Erstellen eines Schlüsseltresors

Bei der Generierung von Aktivierungsschlüsseln wird eine Key Vault-Instanz für eine Azure Stack Edge-Ressource erstellt. Der Schlüsseltresor wird in der gleichen Ressourcengruppe erstellt, in der sich die Azure Stack Edge-Ressource befindet. Für den Schlüsseltresor ist eine Berechtigung für Mitwirkende erforderlich. 

### <a name="prerequisites-for-key-vault"></a>Key Vault-Voraussetzungen

Vor der Erstellung des Schlüsseltresors während der Aktivierung müssen die folgenden Voraussetzungen erfüllt sein:

- Registrieren Sie den *Microsoft.KeyVault*-Ressourcenanbieter, bevor Sie die Azure Stack Edge-Ressource erstellen. Der Ressourcenanbieter wird automatisch registriert, wenn Sie als Besitzer oder Mitwirkender Zugriff auf das Abonnement haben. Die Key Vault-Instanz wird im gleichen Abonnement und der gleichen Ressourcengruppe erstellt wie die Azure Stack Edge-Ressource. 

- Beim Erstellen einer Azure Stack Edge-Ressource wird auch eine systemseitig zugewiesene verwaltete Identität erstellt, die für die Lebensdauer der Ressource beibehalten wird und mit dem Ressourcenanbieter in der Cloud kommuniziert. 

    Wenn die verwaltete Identität aktiviert ist, erstellt Azure eine vertrauenswürdige Identität für die Azure Stack Edge-Ressource.

### <a name="key-vault-creation"></a>Erstellen einer Key Vault-Instanz

Nachdem Sie die Ressource erstellt haben, müssen Sie die Ressource mit dem Gerät aktivieren. Dazu generieren Sie einen Aktivierungsschlüssel im Azure-Portal. 

Das Generieren eines Aktivierungsschlüssels läuft folgendermaßen ab: 

![Ablauf der Aktivierungsschlüsselgenerierung](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-1.png)

- Sie fordern einen Aktivierungsschlüssel im Azure-Portal an. Die Anforderung wird dann an den Key Vault-Ressourcenanbieter gesendet. 
- Ein Schlüsseltresor im Standard-Tarif mit Zugriffsrichtlinie wird erstellt und standardmäßig gesperrt. 
    - Dieser Schlüsseltresor verwendet den Standardnamen oder einen benutzerdefinierten Namen mit drei bis 24 Zeichen, den Sie angegeben haben. Sie können keine Key Vault-Instanz verwenden, die bereits verwendet wird. 
    - Die Schlüsseltresordetails werden im Dienst gespeichert. Diese Key Vault-Instanz wird für die Verwaltung von Geheimnissen verwendet und bleibt so lange bestehen, wie die Azure Stack Edge-Ressource vorhanden ist. 

        ![Während der Generierung von Aktivierungsschlüsseln erstellte Key Vault-Instanz](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)
- Um ein versehentliches Löschen zu verhindern, wird eine Ressourcensperre für den Schlüsseltresor aktiviert. Außerdem wird ein vorläufiges Löschen für die Key Vault-Instanz aktiviert, wodurch sie im Fall eines versehentlichen Löschens innerhalb von 90 Tagen wiederhergestellt werden kann. Weitere Informationen finden Sie unter [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](../key-vault/general/soft-delete-overview.md).
- Eine systemseitig zugewiesene verwaltete Identität, die beim Erstellen der Azure Stack Edge-Ressource erstellt wurde, ist jetzt aktiviert.
- Ein Kanalintegritätsschlüssel (Channel Integrity Key, CIK) wird generiert und im Schlüsseltresor platziert. Die CIK-Details werden im Dienst angezeigt.
- Ein zonenredundantes Speicherkonto (ZRS-Konto) wird ebenfalls im selben Bereich wie die Azure Stack Edge Ressource erstellt, und für das Konto wird eine Sperre eingerichtet. 
    - Dieses Konto wird zum Speichern von Überwachungsprotokollen verwendet. 
    - Die Erstellung des Speicherkontos ist ein zeitintensiver Prozess und dauert einige Minuten.
    - Das Speicherkonto wird mit dem Namen des Schlüsseltresors gekennzeichnet.
- Zum Schlüsseltresor wird eine Diagnoseeinstellung hinzugefügt, und die Protokollierung wird aktiviert. 
- Die verwaltete Identität wird der Zugriffsrichtlinie für den Schlüsseltresor hinzugefügt, um Zugriff auf den Schlüsseltresor zu ermöglichen, da das Gerät den Schlüsseltresor zum Speichern und Abrufen von Geheimnissen verwendet. 
- Der Schlüsseltresor authentifiziert die Anforderung mit der verwalteten Identität, um den Aktivierungsschlüssel zu generieren. Der Aktivierungsschlüssel wird an das Azure-Portal zurückgegeben. Anschließend können Sie diesen Schlüssel kopieren und in der lokalen Benutzeroberfläche verwenden, um das Gerät zu aktivieren.

> [!NOTE]
> - Wenn Sie bereits vor der Integration der Azure Key Vault-Instanz in die Azure Stack Edge-Ressource über eine Azure Stack Edge-Ressource verfügten, sind Sie nicht betroffen. Sie können weiterhin die vorhandene Azure Stack Edge-Ressource verwenden.
> -  Die Erstellung von Schlüsseltresor und Speicherkonto erhöht die Gesamtressourcenkosten. Weitere Informationen zu zulässigen Transaktionen und den entsprechenden Gebühren finden Sie unter [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/) und [Azure Blob Storage – Preise](https://azure.microsoft.com/pricing/details/storage/blobs/).

Wenn Probleme im Zusammenhang mit der Key Vault-Instanz und der Geräteaktivierung auftreten, finden Sie weitere Informationen unter [Beheben von Problemen mit der Geräteaktivierung](azure-stack-edge-gpu-troubleshoot-activation.md).


## <a name="view-key-vault-properties"></a>Anzeigen von Key Vault-Eigenschaften

Nachdem der Aktivierungsschlüssel generiert und der Schlüsseltresor erstellt wurde, können Sie auf den Schlüsseltresor zugreifen, um die Geheimnisse, Zugriffsrichtlinien, Diagnosen und Erkenntnisse anzuzeigen. Die einzelnen Vorgänge werden nachfolgend detailliert beschrieben.

### <a name="view-secrets"></a>Anzeigen von Geheimnissen

Nachdem der Aktivierungsschlüssel generiert und der Schlüsseltresor erstellt wurde, können Sie auf den Schlüsseltresor zugreifen. 

Führen Sie die folgenden Schritte aus, um auf den Schlüsseltresor zuzugreifen und die Geheimnisse anzuzeigen:

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Sicherheit**. 
1. Im rechten Bereich unter **Sicherheit** werden die **Geheimnisse** angezeigt. 
1. Sie können auch zum Schlüsseltresor navigieren, der Ihrer Azure Stack Edge-Ressource zugeordnet ist. Wählen Sie **Schlüsseltresorname** aus. 

    ![Navigation zum Schlüsseltresor des Geräts](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Um die im Schlüsseltresor gespeicherten Geheimnisse anzuzeigen, navigieren Sie zu **Geheimnisse**. Kanalintegritätsschlüssel, BitLocker-Wiederherstellungsschlüssel und BMC-Benutzerkennwörter (Baseboard-Verwaltungscontroller) werden im Schlüsseltresor gespeichert. Bei einem Ausfall des Geräts ermöglicht das Portal einfachen Zugriff auf den BitLocker-Wiederherstellungsschlüssel und das BMC-Benutzerkennwort.
    
    ![Anzeigen von Gerätegeheimnissen im Schlüsseltresor](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-secrets-1.png)

### <a name="view-managed-identity-access-policies"></a>Anzeigen der Zugriffsrichtlinien für die verwaltete Identität

Führen Sie die folgenden Schritte aus, um auf die Zugriffsrichtlinien für Ihren Schlüsseltresor und die verwaltete Identität zuzugreifen:

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Sicherheit**. 
1. Wählen Sie den entsprechenden Link für **Schlüsseltresorname** aus, um zu dem Schlüsseltresor zu navigieren, der Ihrer Azure Stack Edge-Ressource zugeordnet ist. 

    ![Navigation zum Schlüsseltresor des Geräts](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Um die Zugriffsrichtlinien anzuzeigen, die Ihrem Schlüsseltresor zugeordnet sind, navigieren Sie zu **Zugriffsrichtlinien**. Sie können sehen, dass der verwalteten Identität Zugriff gewährt wurde. Wählen Sie **Geheimnisberechtigungen** aus. Sie können sehen, dass der Zugriff der verwalteten Identität nur auf **Abrufen** und **Festlegen** für das Geheimnis beschränkt ist. 
    
    ![Anzeigen von Zugriffsrichtlinien für den Schlüsseltresor](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-access-policies-1.png)

    
### <a name="view-audit-logs"></a>Anzeigen von Überwachungsprotokollen

Führen Sie die folgenden Schritte aus, um auf den Schlüsseltresor zuzugreifen und die Diagnoseeinstellungen und Überwachungsprotokolle anzuzeigen:

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Sicherheit**. 
1. Wählen Sie den entsprechenden Link für **Schlüsseltresorname** aus, um zu dem Schlüsseltresor zu navigieren, der Ihrer Azure Stack Edge-Ressource zugeordnet ist. 

    ![Navigation zum Schlüsseltresor des Geräts](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Um die Diagnoseeinstellungen anzuzeigen, die Ihrem Schlüsseltresor zugeordnet sind, navigieren Sie zu **Diagnoseeinstellungen**. Diese Einstellung bietet die Möglichkeit, zu überwachen, wie, wann und von wem auf die Schlüsseltresore zugegriffen wurde. Sie können sehen, dass eine Diagnoseeinstellung erstellt wurde. Protokolle werden an das Speicherkonto übertragen, das ebenfalls erstellt wurde. Überwachungsereignisse werden ebenfalls im Schlüsseltresor erstellt.
    
    ![Anzeigen der Diagnoseeinstellungen für den Schlüsseltresor](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-diagnostics-settings-1.png)

Wenn Sie ein anderes Speicherziel für Protokolle im Schlüsseltresor konfiguriert haben, können Sie die Protokolle direkt im betreffenden Speicherkonto anzeigen.

### <a name="view-insights"></a>Einblicke anzeigen 

Führen Sie die folgenden Schritte aus, um auf die Schlüsseltresorerkenntnisse, u. a. die für den Schlüsseltresor ausgeführten Vorgänge, zuzugreifen:

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Sicherheit**. 
1. Wählen Sie den entsprechenden Link für die **Schlüsseltresordiagnose** aus. 

    ![Navigation zum Schlüsseltresor des Geräts](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Das Blatt **Erkenntnisse** enthält eine Übersicht über die Vorgänge, die für den Schlüsseltresor ausgeführt wurden.

    ![Anzeigen von Erkenntnissen für den Schlüsseltresor](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-insights-1.png) 

### <a name="view-managed-identity-status"></a>Anzeigen des Status der verwalteten Identität

Führen Sie die folgenden Schritte aus, um den Status der systemseitig zugewiesenen verwalteten Identität anzuzeigen, die ihrer Azure Stack Edge-Ressource zugeordnet ist:

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Sicherheit**. 
1. Navigieren Sie im rechten Bereich zu **systemseitig zugewiesene verwaltete Identität**, um festzustellen, ob die vom System zugewiesene verwaltete Identität aktiviert oder deaktiviert ist.

    ![Navigation zum Schlüsseltresor des Geräts](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

### <a name="view-key-vault-locks"></a>Anzeigen von Schlüsseltresorsperren

Führen Sie die folgenden Schritte aus, um auf den Schlüsseltresor zuzugreifen und die Sperren anzuzeigen:

1. Navigieren Sie im Azure-Portal für Ihre Azure Stack Edge-Ressource zu **Sicherheit**. 
1. Wählen Sie den entsprechenden Link für **Schlüsseltresorname** aus, um zu dem Schlüsseltresor zu navigieren, der Ihrer Azure Stack Edge-Ressource zugeordnet ist. 

    ![Navigation zum Schlüsseltresor des Geräts](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)
1. Um die Sperren für Ihren Schlüsseltresor anzuzeigen, navigieren Sie zu **Sperren**. Um ein versehentliches Löschen zu verhindern, wird eine Ressourcensperre für die Key Vault-Instanz aktiviert. 
    
    ![Anzeigen von Sperren für den Schlüsseltresor](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-locks-1.png)


## <a name="regenerate-activation-key"></a>Neugenerieren des Aktivierungsschlüssels

In bestimmten Fällen müssen Sie den Aktivierungsschlüssel u. U. neu generieren. Das Neugenerieren eines Aktivierungsschlüssels läuft folgendermaßen ab:

1. Sie fordern die Neugenerierung eines Aktivierungsschlüssels im Azure-Portal an. 
1. Der Aktivierungsschlüssel wird an das Azure-Portal zurückgegeben. Anschließend können Sie diesen Schlüssel kopieren und verwenden. 

Auf den Schlüsseltresor wird nicht zugegriffen, wenn der Aktivierungsschlüssel neu generiert wird. 

## <a name="recover-device-secrets"></a>Wiederherstellen von Gerätegeheimnissen

Wenn der CIK versehentlich gelöscht wird oder Geheimnisse (z. B. das BMC-Benutzerkennwort) im Schlüsseltresor veraltet sind, müssen Sie Geheimnisse vom Gerät pushen, um die Schlüsseltresorgeheimnisse zu aktualisieren. 

Führen Sie die folgenden Schritte aus, um Gerätegeheimnisse zu synchronisieren:  

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Stack Edge-Ressource und dann zu **Sicherheit**.
1. Wählen Sie im rechten Bereich in der oberen Befehlsleiste **Gerätegeheimnisse synchronisieren** aus.
1. Die Gerätegeheimnisse werden an den Schlüsseltresor gepusht, um die Geheimnisse im Schlüsseltresor wiederherzustellen oder zu aktualisieren. Sie erhalten eine Benachrichtigung, wenn die Synchronisierung abgeschlossen wurde.

    ![Synchronisieren von Gerätegeheimnissen im Schlüsseltresor](media/azure-stack-edge-gpu-activation-key-vault/sync-device-secrets-1.png)

## <a name="delete-key-vault"></a>Löschen von Schlüsseltresoren

Es gibt zwei Möglichkeiten, den Schlüsseltresor zu löschen, der der Azure Stack Edge-Ressource zugeordnet ist:

- Sie löschen die Azure Stack Edge-Ressource, und wählen gleichzeitig aus, dass der zugeordnete Schlüsseltresor gelöscht werden soll.
- Sie haben den Schlüsseltresor versehentlich direkt gelöscht.

Wenn die Azure Stack Edge-Ressource gelöscht wird, wird der Schlüsseltresor ebenfalls mit der Ressource gelöscht. Sie werden aufgefordert, diesen Schritt zu bestätigen. Wenn andere Schlüssel in diesem Schlüsseltresor gespeichert sind, und Sie diesen Schlüsseltresor nicht löschen möchten, können Sie die Einwilligung ablehnen. Es wird dann nur die Azure Stack Edge-Ressource gelöscht, und die Key Vault-Instanz bleibt erhalten. 

Führen Sie die folgenden Schritte aus, um die Azure Stack Edge-Ressource und den zugehörigen Schlüsseltresor zu löschen:

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Stack Edge-Ressource und dann zu **Übersicht**.
1. Wählen Sie im rechten Bereich **Löschen** aus. Durch diese Aktion wird die Azure Stack Edge-Ressource gelöscht.

   ![Löschen der Azure Stack Edge-Ressource und des zugehörigen Schlüsseltresors](media/azure-stack-edge-gpu-activation-key-vault/delete-azure-stack-edge-resource-1.png)  

1. Es wird ein Bestätigungsblatt angezeigt. Geben Sie den Namen der Azure Stack Edge-Ressource ein. Geben Sie **Ja** ein, um das Löschen des zugehörigen Schlüsseltresors zu bestätigen.

    ![Löschbestätigung für die Azure Stack Edge-Ressource und den zugehörigen Schlüsseltresor](media/azure-stack-edge-gpu-activation-key-vault/confirm-delete-azure-stack-edge-resource-1.png)   
1. Klicken Sie auf **Löschen**.

Die Azure Stack Edge-Ressource und der Schlüsseltresor werden gelöscht.

Der Schlüsseltresor kann versehentlich gelöscht werden, obwohl die Azure Stack Edge-Ressource verwendet wird. In diesem Fall wird eine kritische Warnung auf der Seite **Sicherheit** für Ihre Azure Stack Edge-Ressource ausgelöst. Sie können zu dieser Seite navigieren, um Ihren Schlüsseltresor wiederherzustellen. 


## <a name="recover-key-vault"></a>Wiederherstellen des Schlüsseltresors

Sie können den Schlüsseltresor wiederherstellen, der Ihrer Azure Stack Edge-Ressource zugeordnet ist, wenn er versehentlich gelöscht oder endgültig gelöscht wurde. Wenn der Schlüsseltresor zum Speichern anderer Schlüssel verwendet wurde, müssen Sie die betreffenden Schlüssel wiederherstellen, indem Sie den Schlüsseltresor wiederherstellen.

- Innerhalb von 90 Tagen nach dem Löschen können Sie den gelöschten Schlüsseltresor wiederherstellen.
- Wenn der Schutzzeitraum von 90 Tagen, nach dem der Schlüsseltresor endgültig gelöscht wird, bereits abgelaufen ist, können Sie den Schlüsseltresor nicht wiederherstellen. Stattdessen müssen Sie einen neuen Schlüsseltresor erstellen.

Führen Sie innerhalb von 90 Tagen nach dem Löschen die folgenden Schritte aus, um den Schlüsseltresor wiederherzustellen:

- Navigieren Sie im Azure-Portal zur Seite **Sicherheit** Ihrer Azure Stack Edge-Ressource. Es wird eine Benachrichtigung mit dem Hinweis angezeigt, dass der zugehörige Schlüsseltresor für die Ressource gelöscht wurde. Sie können die Benachrichtigung auswählen oder unter **Sicherheitseinstellungen** die Option **Neu konfigurieren** für den Schlüsseltresornamen auswählen, um den Schlüsseltresor wiederherzustellen.

    ![Navigieren zur Seite „Sicherheit“](media/azure-stack-edge-gpu-activation-key-vault/security-page-1.png) 

- Wählen Sie im Blatt **Schlüsseltresor wiederherstellen** die Option **Konfigurieren** aus. Im Rahmen der Wiederherstellung werden die folgenden Vorgänge ausgeführt:  

    ![Wiederherstellungsschritte](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-2.png) 

    - Ein Schlüsseltresor wird mit dem gleichen Namen wiederhergestellt, und für die Schlüsseltresorressource wird eine Sperre aktiviert. 
    
        > [!NOTE]
        > Wenn der Schlüsseltresor gelöscht wird und der Schutzzeitraum von 90 Tagen, nach dem der Schlüsseltresor endgültig gelöscht wird, nicht abgelaufen ist, kann der Schlüsseltresorname in diesem Zeitraum nicht zum Erstellen eines neuen Schlüsseltresors verwendet werden.
    - Zum Speichern der Überwachungsprotokolle wird ein Speicherkonto erstellt. 
    - Der systemseitig zugewiesenen verwalteten Identität wird Zugriff auf den Schlüsseltresor gewährt.
    - Gerätegeheimnisse werden in den Schlüsseltresor gepusht. 
    
    Wählen Sie **Konfigurieren** aus. 
 
    ![Blatt „Schlüsseltresor wiederherstellen“](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-1.png)  

    Der Schlüsseltresor wird wiederhergestellt, und nach Abschluss der Wiederherstellung wird eine entsprechende Benachrichtigung angezeigt.

Wenn der Schlüsseltresor gelöscht wird und der Schutzzeitraum von 90 Tagen, nach dem der Schlüsseltresor endgültig gelöscht wird, abgelaufen ist, haben Sie die Möglichkeit, einen neuen Schlüsseltresor zu erstellen (siehe Beschreibung zum [Wiederherstellen des Schlüsseltresors](#recover-key-vault) weiter oben). In diesem Fall müssen Sie einen neuen Namen für den Schlüsseltresor angeben. Ein neues Speicherkonto wird erstellt, die verwaltete Identität erhält Zugriff auf diesen Schlüsseltresor, und Gerätegeheimnisse werden an diesen Schlüsseltresor gepusht.
  
<!--To recover the key vault using the follow these steps to [Recover your key vault](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates).-->

## <a name="recover-managed-identity-access"></a>Wiederherstellen des Zugriffs der verwalteten Identität

Wird die Zugriffsrichtlinie für die systemseitig zugewiesene verwaltete Identität gelöscht, wird eine Warnung ausgelöst, wenn das Gerät die Schlüsseltresorgeheimnisse nicht wieder synchronisieren kann. Wenn die verwaltete Identität keinen Zugriff auf den Schlüsseltresor hat, wird wiederum eine Gerätewarnung ausgelöst. Wählen Sie in jedem Fall die Warnung aus, um das Blatt **Schlüsseltresor wiederherstellen** zu öffnen, und führen Sie eine Neukonfiguration durch. Dabei sollte der Zugriff der verwalteten Identität wiederhergestellt werden. 

![Ablauf zum Gewähren des Zugriffs auf den Schlüsseltresor durch die verwaltete Identität](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-2.png)


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Generieren eines Aktivierungsschlüssels](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).
- [Problembehandlung für Schlüsseltresorfehler](azure-stack-edge-gpu-troubleshoot-activation.md) auf Ihrem Azure Stack Edge-Gerät.
