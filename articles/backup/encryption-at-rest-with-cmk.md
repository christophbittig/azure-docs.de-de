---
title: Verschlüsselung von Sicherungsdaten mit von Kunden verwalteten Schlüsseln
description: Hier erfahren Sie, wie Sie mit Azure Backup Sicherungsdaten mithilfe von kundenseitig verwalteten Schlüsseln (Customer-Managed Keys, CMK) verschlüsseln können.
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f16974d00f4801f288180814daf9ff5ed4558748
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778719"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Verschlüsselung von Sicherungsdaten mit von Kunden verwalteten Schlüsseln

Azure Backup ermöglicht es Ihnen, Ihre Sicherungsdaten mit von Kunden verwalteten Schlüsseln zu verschlüsseln, anstatt die standardmäßig aktivierten von der Plattform verwalteten Schlüssel zu verwenden. Die Schlüssel, die zum Verschlüsseln der Sicherungsdaten verwendet werden, müssen in [Azure Key Vault](../key-vault/index.yml) gespeichert werden.

Der Verschlüsselungsschlüssel, der zum Verschlüsseln von Sicherungen verwendet wird, kann sich von dem für die Quelle verwendeten Verschlüsselungsschlüssel unterscheiden. Die Daten werden mithilfe eines AES-256 basierten Datenverschlüsselungsschlüssels (DEK) geschützt, der wiederum mit Ihren Schlüsseln (KEK) geschützt wird. Dadurch erhalten Sie vollständige Kontrolle über die Daten und Schlüssel. Um die Verschlüsselung zu ermöglichen, muss dem Recovery Services-Tresor der Zugriff auf den Verschlüsselungsschlüssel in Azure Key Vault gewährt werden. Sie können den Schlüssel bei Bedarf ändern.

In diesem Artikel werden die folgenden Themen behandelt:

- Erstellen eines Recovery Services-Tresors
- Konfigurieren Ihres Recovery Services-Tresors zum Verschlüsseln von Sicherungsdaten mithilfe von kundenseitig verwalteten Schlüsseln
- Ausführen einer Sicherung in Tresore, die mit kundenseitig verwalteten Schlüsseln verschlüsselt wurden
- Wiederherstellen von Daten aus Sicherungen

## <a name="before-you-start"></a>Vorbereitung

- Diese Funktion ermöglicht Ihnen eine Verschlüsselung **nur bei neuen Recovery Services-Tresoren**. Eine Registrierung und der Versuch einer Registrierung von Tresoren, die vorhandene Elemente enthalten, werden nicht unterstützt.

- Nach der Aktivierung für einen Recovery Services-Tresor kann die Verschlüsselung mit kundenseitig verwalteten Schlüsseln nicht wieder auf die Verwendung plattformseitig verwalteter Schlüssel (Standardeinstellung) zurückgesetzt werden. Sie können die Verschlüsselungsschlüssel gemäß Ihren Anforderungen ändern.

- Diese Funktion **unterstützt derzeit keine Sicherung mit dem MARS-Agent**, und mit dem CMK verschlüsselte Tresore können dafür möglicherweise nicht verwendet werden. Der MARS-Agent verwendet eine Verschlüsselung, die auf einer Passphrase des Benutzers basiert. Diese Funktion unterstützt außerdem keine Sicherung von klassischen VMs.

- Dieses Feature hängt nicht mit [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) zusammen, das die gastbasierte Verschlüsselung der Datenträger eines virtuellen Computers mithilfe von BitLocker (für Windows) und DM-Crypt (für Linux) verwendet.

- Der Recovery Services-Tresor kann nur mit Schlüsseln verschlüsselt werden, die in einem Azure Key Vault gespeichert sind, der sich in der **gleichen Region** befindet. Außerdem dürfen nur **RSA-Schlüssel** im Zustand **Aktiviert** als Schlüssel verwendet werden.

- Das Verschieben eines mit CMK verschlüsselten Recovery Services-Tresors in andere Ressourcengruppen und Abonnements wird derzeit nicht unterstützt.
- Recovery Services-Tresore, die mit kundenseitig verwalteten Schlüsseln verschlüsselt sind, unterstützen keine regionsübergreifende Wiederherstellung gesicherter Instanzen.
- Wenn Sie einen Recovery Services-Tresor, der bereits mit vom Kunden verwalteten Schlüsseln (CMK) verschlüsselt ist, auf einen neuen Mandanten verschieben, müssen Sie den Recovery Services-Tresor aktualisieren, um die verwaltete Identität des Tresors und die CMK (die sich auf dem neuen Mandanten befinden sollen) neu zu erstellen und zu konfigurieren. Wenn dies nicht getan wird, kommt es zu Fehlern bei den Sicherungs- und Wiederherstellungsvorgängen. Außerdem müssen alle innerhalb des Abonnements eingerichteten Berechtigungen der rollenbasierten Zugriffssteuerung (RBAC) neu konfiguriert werden.

- Diese Funktion kann über das Azure-Portal und PowerShell konfiguriert werden.

    >[!NOTE]
    >Verwenden Sie das Az-Modul 5.3.0 oder höher, um für Sicherungen im Recovery Services-Tresor kundenseitig verwaltete Schlüssel zu verwenden.
    
    >[!Warning]
    >Wenn Sie PowerShell zum Verwalten von Verschlüsselungsschlüsseln für die Sicherung verwenden, empfiehlt es sich nicht, die Schlüssel über das Portal zu aktualisieren.<br>Wenn Sie den Schlüssel über das Portal aktualisieren, können Sie PowerShell nicht verwenden, um den Verschlüsselungsschlüssel weiter zu aktualisieren, bis ein PowerShell-Update zur Unterstützung des neuen Modells verfügbar ist. Sie können jedoch weiterhin den Schlüssel aus der Azure-Portal aktualisieren.

Wenn Sie Ihren Recovery Services-Tresor noch nicht erstellt und konfiguriert haben, erfahren Sie [hier](backup-create-rs-vault.md), wie Sie vorgehen können.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurieren eines Tresors mithilfe von kundenseitig verwalteten Schlüsseln

Dieser Vorgang umfasst die folgenden Schritte:

1. Aktivieren der verwalteten Identität für den Recovery Services-Tresor

1. Zuweisen von Berechtigungen zum Tresor für den Zugriff auf den Verschlüsselungsschlüssel im Azure Key Vault

1. Aktivieren des vorläufigen Löschens und des Löschschutzes im Azure Key Vault

1. Zuweisen des Verschlüsselungsschlüssels zum Recovery Services-Tresor

Alle diese Schritte müssen in der oben genannten Reihenfolge ausgeführt werden, um das gewünschte Ergebnis zu erzielen. Die einzelnen Schritte werden im Folgenden näher erläutert.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Aktivieren der verwalteten Identität für den Recovery Services-Tresor

Azure Backup verwendet die vom System zugewiesene verwaltete Identität, um den Recovery Services-Tresor für den Zugriff auf die Verschlüsselungsschlüssel zu authentifizieren, die im Azure Key Vault gespeichert sind. Führen Sie die unten aufgeführten Schritte aus, um die verwaltete Identität für den Recovery Services-Tresor zu aktivieren.

>[!NOTE]
>Nach der Aktivierung darf die verwaltete Identität **nicht** deaktiviert werden (auch nicht vorübergehend). Die Deaktivierung der verwalteten Identität kann zu inkonsistentem Verhalten führen.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>Aktivieren einer systemseitig zugewiesenen verwalteten Identität für den Vault

**Im Portal:**

1. Navigieren Sie zu Ihrem Recovery Services-Tresor und dann zu **Identität**.

    ![Identitätseinstellungen](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. Navigieren Sie zur Registerkarte " **System zugewiesen** ".

1. Ändern Sie den **Status** zu **An**.

1. Klicken Sie auf **Speichern**, um die Identität für den Tresor zu aktivieren.

Es wird eine Objekt-ID generiert, welche die vom System zugewiesene verwaltete Identität des Tresors darstellt.

>[!NOTE]
>Nach der Aktivierung darf die verwaltete Identität nicht deaktiviert werden (auch nicht vorübergehend). Die Deaktivierung der verwalteten Identität kann zu inkonsistentem Verhalten führen.


**Mit PowerShell:**

Verwenden Sie den Befehl [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault), um die systemseitig zugewiesene verwaltete Identität für den Recovery Services-Tresor zu aktivieren.

Beispiel:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Ausgabe:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault-in-preview"></a>Zuweisen einer benutzerseitig zugewiesenen verwalteten Identität zum Tresor (in der Vorschau)

>[!Note]
>- Tresore, die benutzerseitig zugewiesene verwaltete Identitäten für die CMK-Verschlüsselung verwenden, unterstützen die Verwendung privater Endpunkte für Backup nicht.
>- Azure Key Vault-Instanzen, die den Zugriff auf bestimmte Netzwerke beschränken, werden noch nicht für die gemeinsame Verwendung mit benutzerseitig zugewiesenen verwalteten Identitäten für die CMK-Verschlüsselung unterstützt.

Führen Sie die folgenden Schritte aus, um die vom Benutzer zugewiesene verwaltete Identität für den Recovery Services Tresor zuzuweisen:

1.  Navigieren Sie zu Ihrem Recovery Services-Tresor und dann zu **Identität**.

    ![Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zum Tresor](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  Navigieren Sie zur Registerkarte **Benutzer zugewiesen**.

1.  Klicken Sie auf **+ Hinzufügen**, um eine vom Benutzer zugewiesene verwaltete Identität hinzuzufügen.

1.  Wählen Sie auf dem geöffneten Blatt vom **Benutzer zugewiesene verwaltete Identität hinzufügen** " das Abonnement für Ihre Identität aus.

1.  Wählen Sie die Identität aus der Liste aus. Sie können auch nach dem Namen der Identität oder der Ressourcengruppe filtern.

1.  Klicken Sie anschließend auf **Hinzufügen**, um das Zuweisen der Identität abzuschließen.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Zuweisen von Berechtigungen zum Recovery Services-Tresor für den Zugriff auf den Verschlüsselungsschlüssel im Azure Key Vault

>[!Note]
>Wenn Sie vom Benutzer zugewiesene Identitäten verwenden, müssen Sie der vom Benutzer zugewiesenen Identität dieselben Berechtigungen zuweisen.

Sie müssen jetzt dem Recovery Services-Tresor gestatten, auf den Azure Key Vault zuzugreifen, der den Verschlüsselungsschlüssel enthält. Dazu erlauben Sie der verwalteten Identität des Recovery Services-Tresors, auf den Key Vault zuzugreifen.

**Im Portal:**

1. Navigieren Sie zu Ihrem Azure Key Vault und dann zu **Zugriffsrichtlinien**. Klicken Sie anschließend auf **+ Zugriffsrichtlinie hinzufügen**.

    ![Zugriffsrichtlinie hinzufügen](./media/encryption-at-rest-with-cmk/access-policies.png)

1. Wählen Sie unter **Schlüsselberechtigungen** die Vorgänge **Abrufen**, **Auflisten**, **Schlüssel entpacken** und **Schlüssel packen** aus. Hiermit werden die zulässigen Aktionen für den Schlüssel angegeben.

    ![Schlüsselberechtigungen zuweisen](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Wechseln Sie zu **Prinzipal auswählen**, und suchen Sie über das Suchfeld nach dem Namen oder der verwalteten Identität für den Tresor. Sobald der Tresor angezeigt wird, wählen Sie ihn und dann unten im Bereich **Auswählen** aus.

    ![Prinzipal auswählen](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Wählen Sie anschließend **Hinzufügen** aus, um die neue Zugriffsrichtlinie hinzuzufügen.

1. Wählen Sie **Speichern** aus, um die Änderungen an der Zugriffsrichtlinie von Azure Key Vault zu speichern.

>[!NOTE] 
>Sie können dem Recovery Services-Tresor auch eine RBAC-Rolle zuweisen, die die oben genannten Berechtigungen enthält, z. B. die Rolle _[Key Vault-Kryptografiebeauftragter](../key-vault/general/rbac-guide.md#azure-built-in-roles-for-key-vault-data-plane-operations)_ .<br><br>Diese Rollen können neben den oben beschriebenen zusätzliche Berechtigungen enthalten.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Aktivieren des vorläufigen Löschens und des Löschschutzes im Azure Key Vault

Sie müssen für den Azure Key Vault, in dem Ihr Verschlüsselungsschlüssel gespeichert ist, **vorläufiges Löschen und Löschschutz aktivieren**. Dies können Sie über die Azure Key Vault-Benutzeroberfläche ausführen, wie unten gezeigt. (Alternativ können diese Eigenschaften beim Erstellen des Key Vault festgelegt werden.) Weitere Informationen zu diesen Key Vault-Eigenschaften finden Sie [hier](../key-vault/general/soft-delete-overview.md).

![Vorläufiges Löschen und Löschschutz aktivieren](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Sie können vorläufiges Löschen und Löschschutz auch über PowerShell aktivieren, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich bei Ihrem Azure-Konto an.

    ```azurepowershell
    Login-AzAccount
    ```

1. Wählen Sie das Abonnement mit Ihrem Tresor aus.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Aktivieren von „Vorläufiges Löschen“

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Aktivieren des Löschschutzes

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>Zuweisen eines Verschlüsselungsschlüssels zum RS-Tresor

>[!NOTE]
> Überprüfen Sie die folgenden Punkte, bevor Sie fortfahren:
>
> - Alle oben genannten Schritte wurden erfolgreich ausgeführt:
>   - Die verwaltete Identität des Recovery Services-Tresors wurde aktiviert, und dieser wurden die erforderlichen Berechtigungen zugewiesen.
>   - Vorläufiges Löschen und Löschschutz sind für den Azure Key Vault aktiviert.
> - Der Recovery Services-Tresor, für den Sie die CMK-Verschlüsselung aktivieren möchten, weist **keine** geschützten oder registrierten Elemente auf.

Sobald Sie sich versichert haben, dass die genannten Bedingungen erfüllt sind, können Sie mit der Auswahl des Verschlüsselungsschlüssels für Ihren Tresor fortfahren.

### <a name="to-assign-the-key-in-the-portal"></a>So weisen Sie den Schlüssel im Portal zu

1. Navigieren Sie zu Ihrem Recovery Services-Tresor und dann zu **Eigenschaften**.

    ![Verschlüsselungseinstellungen](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Wählen Sie unter **Verschlüsselungseinstellungen** die Option **Aktualisieren** aus.

1. Wählen Sie im Bereich „Verschlüsselungseinstellungen“ **Eigenen Schlüssel verwenden** aus, und geben Sie den Schlüssel mithilfe einer der folgenden Methoden an: **Stellen Sie sicher, dass es sich bei dem Schlüssel, den Sie verwenden möchten, um einen RSA 2048-Schlüssel handelt, der den Zustand „Aktiviert“ aufweist.**

    1. Geben Sie den **Schlüssel-URI** für den Schlüssel ein, mit dem Sie die Daten in diesem Recovery Services-Tresor verschlüsseln möchten. Außerdem müssen Sie das Abonnement angeben, in dem der Azure Key Vault (der diesen Schlüssel enthält) vorhanden ist. Dieser Schlüssel-URI kann aus dem entsprechenden Schlüssel in Ihrem Azure Key Vault abgerufen werden. Vergewissern Sie sich, dass Sie den Schlüssel-URI korrekt kopiert haben. Es wird empfohlen, dass Sie die Schaltfläche **in Zwischenablage kopieren** neben dem Schlüsselbezeichner verwenden.

        >[!NOTE]
        >Wenn Sie den Verschlüsselungsschlüssel mit dem Schlüssel-URI angeben, wird der Schlüssel nicht automatisch rotiert. Wichtige Updates müssen daher manuell durchgeführt werden, indem der neue Schlüssel bei Bedarf angegeben wird.

        ![Schlüssel-URI eingeben](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Suchen Sie über den Schlüsselauswahlbereich nach dem Schlüssel aus dem Key Vault, und wählen Sie ihn aus.

        >[!NOTE]
        >Wenn Sie den Verschlüsselungsschlüssel über den Bereich „Schlüsselauswahl“ angeben, wird er jedes Mal automatisch rotiert, wenn eine neue Version für den Schlüssel aktiviert wird. [Weitere Informationen finden Sie](#enabling-auto-rotation-of-encryption-keys) unter Aktivieren der automatischen Rotation von Verschlüsselungsschlüsseln.

        ![Auswählen des Schlüssels aus dem Schlüsseltresor](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Wählen Sie **Speichern** aus.

1. **Nachverfolgen des Aktualisierungsstatus für den Verschlüsselungsschlüssel:** Sie können den Status der Zuweisung des Verschlüsselungsschlüssels mithilfe der Ansicht **Sicherungsaufträge** auf der linken Navigationsleiste nachverfolgen. Der Status sollte nach kurzer Zeit in **Abgeschlossen** geändert werden. Ihr Tresor verschlüsselt nun alle Daten mit dem angegebenen Schlüssel als KEK.

    ![Status „Abgeschlossen“](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    Die Updates für den Verschlüsselungsschlüssel werden auch im Aktivitätsprotokoll des Tresors aufgezeichnet.

    ![Aktivitätsprotokoll](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>So weisen Sie den Schlüssel mit PowerShell zu

Verwenden Sie den Befehl [Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty), um die Verschlüsselung mithilfe von kundenseitig verwalteten Schlüsseln zu aktivieren und den zu verwendenden Verschlüsselungsschlüssel zuzuweisen oder zu aktualisieren.

Beispiel:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Ausgabe:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Den gleichen Prozess führen Sie aus, wenn Sie den Verschlüsselungsschlüssel aktualisieren oder ändern möchten. Wenn Sie den Schlüssel aktualisieren und einen Schlüssel aus einer anderen Key Vault-Instanz verwenden möchten (der sich von dem derzeit verwendeten unterscheidet), stellen Sie Folgendes sicher:
>
> - Der Schlüsseltresor muss sich in derselben Region wie der Recovery Services-Tresor befinden.
>
> - Vorläufiges Löschen und Löschschutz sind für den Schlüsseltresor aktiviert.
>
> - Der Recovery Services-Tresor verfügt über die für den Zugriff auf den Schlüsseltresor erforderlichen Berechtigungen.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Sichern in einem Tresor, der mit kundenseitig verwalteten Schlüsseln verschlüsselt ist

Bevor Sie mit der Konfiguration des Schutzes fortfahren, sollten Sie unbedingt die folgende Checkliste durchgehen und sicherstellen, dass alle Anforderungen erfüllt sind. Dies ist aus folgendem Grund wichtig: Sobald für ein Element eine Konfiguration vorgenommen oder versucht wurde, die eine Sicherung in einem nicht mit CMK verschlüsselten Tresor vorsieht, kann die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für dieses Element nicht aktiviert werden, und es werden weiterhin plattformseitig verwaltete Schlüssel verwendet.

>[!IMPORTANT]
> Bevor Sie mit dem Konfigurieren des Schutzes fortfahren, müssen Sie die folgenden Schritte **erfolgreich** ausgeführt haben:
>
>1. Ihr Recovery Services-Tresor wurde erstellt.
>1. Die vom System zugewiesene verwaltete Identität des Recovery Services Tresors wurde aktiviert, oder dem Tresor wurde eine vom Benutzer zugewiesene verwaltete Identität zugewiesen.
>1. Die Berechtigungen für Ihren Recovery Services-Tresor (oder die dem Benutzer zugewiesene verwaltete Identität) für den Zugriff auf Verschlüsselungsschlüssel aus Ihrem Key Vault wurden erteilt.
>1. Vorläufiges Löschen und Löschschutz wurden für Ihren Key Vault aktiviert.
>1. Dem Recovery Services-Tresor wurde ein gültiger Verschlüsselungsschlüssel zugewiesen.
>
>Wenn alle oben genannten Schritte bestätigt wurden, fahren Sie mit dem Konfigurieren der Sicherung fort.

Der Prozess zum Konfigurieren und Ausführen von Sicherungen für einen Recovery Services-Tresor, der mit kundenseitig verwalteten Schlüsseln verschlüsselt ist, ist identisch mit dem Prozess für einen Tresor, der plattformseitig verwaltete Schlüssel verwendet, und **die Funktionalität für den Benutzer ist die gleiche**. Dies gilt auch für die [Sicherung von virtuellen Azure-Computern](./quick-backup-vm-portal.md) sowie für die Sicherung von Workloads, die auf einem virtuellen Computer ausgeführt werden (z. B. [SAP HANA](./tutorial-backup-sap-hana-db.md)- oder [SQL Server](./tutorial-sql-backup.md)-Datenbanken).

## <a name="restoring-data-from-backup"></a>Wiederherstellen von Daten aus einer Sicherung

### <a name="vm-backup"></a>VM-Sicherung

Die im Recovery Services-Tresor gespeicherten Daten können gemäß den [hier](./backup-azure-arm-restore-vms.md) beschriebenen Schritten wiederhergestellt werden. Bei der Wiederherstellung aus einem Recovery Services-Tresor, der mit kundenseitig verwalteten Schlüsseln verschlüsselt wurde, können Sie die wiederhergestellten Daten mit einem Datenträgerverschlüsselungssatz (Disk Encryption Set, DES) verschlüsseln.

#### <a name="restoring-vm--disk"></a>Wiederherstellung von VMs/Datenträgern

1. Wenn Sie einen Datenträger oder eine VM aus einem Momentaufnahmen-Wiederherstellungspunkt wiederherstellen, werden die wiederhergestellten Daten mit dem DES verschlüsselt, der zum Verschlüsseln der Datenträger der virtuellen VM verwendet wurde.

1. Beim Wiederherstellen von Datenträgern/VMs von einem Wiederherstellungspunkt mit dem Wiederherstellungstyp „Tresor“ können Sie auswählen, ob die wiederhergestellten Daten mit dem zum Zeitpunkt der Wiederherstellung angegebenen DES verschlüsselt werden sollen. Alternativ können Sie die Wiederherstellung der Daten ohne Angabe eines DES fortsetzen. In diesem Fall werden sie mit von Microsoft verwalteten Schlüsseln verschlüsselt.

Sie können den wiederhergestellten Datenträger/virtuellen Computer nach Abschluss der Wiederherstellung verschlüsseln, unabhängig davon, welche Option Sie beim Starten der Wiederherstellung ausgewählt haben.

![Wiederherstellungspunkte](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Auswählen eines Datenträgerverschlüsselungssatzes beim Wiederherstellen vom Tresor-Wiederherstellungspunkt

**Im Portal:**

Der Datenträgerverschlüsselungssatz wird im Bereich „Wiederherstellung“ unter „Verschlüsselungseinstellungen“ angegeben, wie unten dargestellt:

1. Wählen Sie für die **Verschlüsselung der Datenträger mit Ihrem Schlüssel** die Option **Ja** aus.

1. Wählen Sie in der Dropdownliste den DES aus, den Sie für die wiederhergestellten Datenträger verwenden möchten. **Stellen Sie sicher, dass Sie auf den DES zugreifen können.**

>[!NOTE]
>Wenn Sie eine VM wiederherstellen, die Azure Disk Encryption verwendet, können Sie bei der Wiederherstellung keinen DES auswählen.

![Verschlüsseln des Datenträgers mit Ihrem Schlüssel](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Mit PowerShell:**

Verwenden Sie den Befehl [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) mit dem Parameter [`-DiskEncryptionSetId <string>`], um [den DES anzugeben](/powershell/module/az.compute/get-azdiskencryptionset), der zum Verschlüsseln des wiederhergestellten Datenträgers verwendet werden soll. Weitere Informationen zum Wiederherstellen von Datenträgern aus einer VM-Sicherung finden Sie in [diesem Artikel](./backup-azure-vms-automation.md#restore-an-azure-vm).

Beispiel:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Wiederherstellen von Dateien

Beim Ausführen einer Dateiwiederherstellung werden die wiederhergestellten Daten mit dem Schlüssel verschlüsselt, der zum Verschlüsseln des Zielspeicherorts verwendet wurde.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Wiederherstellen von SAP HANA-/SQL-Datenbanken auf Azure-VMs

Beim Wiederherstellen einer gesicherten SAP HANA-/SQL-Datenbank, die auf einer Azure-VM ausgeführt wird, werden die wiederhergestellten Daten mit dem Verschlüsselungsschlüssel verschlüsselt, der am Zielspeicherort verwendet wird. Dabei kann es sich um einen kundenseitig verwalteten Schlüssel oder einen plattformseitig verwalteten Schlüssel handeln, der zum Verschlüsseln der Datenträger der VM verwendet wurde.

## <a name="additional-topics"></a>Weitere Themen

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Aktivieren der Verschlüsselung mithilfe von Kunden verwalteten Schlüsseln bei der Tresorerstellung (in der Vorschau)

>[!NOTE]
>Das Aktivieren der Verschlüsselung bei der Tresorerstellung mithilfe von Kunden verwalteten Schlüsseln befindet sich in der begrenzten öffentlichen Vorschau und erfordert das Zulassen von Abonnements. Um sich für die Vorschau anzumelden, füllen Sie das [Formular](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) aus und schreiben Sie uns an [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

Wenn Ihr Abonnement zugelassen ist, wird die Registerkarte „**Sicherungsverschlüsselung**“ angezeigt. Dies ermöglicht es Ihnen, die Verschlüsselung für die Sicherung mithilfe von Kunden verwalteten Schlüsseln während der Erstellung eines neuen Recovery Services Tresors zu aktivieren. Um die Verschlüsselung zu aktivieren, führen Sie die folgenden Schritte aus:

1. Geben Sie neben der Registerkarte **Grundlagen** auf der Registerkarte **Sicherungsverschlüsselung** den Verschlüsselungsschlüssel und die Identität an, die für die Verschlüsselung verwendet werden sollen.

   ![Aktivieren der Verschlüsselung auf Tresor-Ebene](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >Die Einstellungen gelten nur für die Sicherung und sind optional.

1. Wählen Sie vom **Kunden verwalteten Schlüssel** als Verschlüsselungstyp verwenden aus.

1. Wählen Sie die entsprechende Option aus, um den Schlüssel anzugeben, der für die Verschlüsselung verwendet werden soll.

   Sie können den URI für den Verschlüsselungsschlüssel angeben oder den Schlüssel durchsuchen und auswählen. Wenn Sie den Schlüssel mithilfe der Option **Key Vault auswählen** angeben, wird die automatische Rotation des Verschlüsselungsschlüssels automatisch aktiviert. [Weitere Informationen finden Sie unter Automatische Rotation](#enabling-auto-rotation-of-encryption-keys). 

1. Geben Sie die vom Benutzer zugewiesene verwaltete Identität an, um die Verschlüsselung mit vom Kunden verwalteten Schlüsseln zu verwalten. Klicken Sie auf **auswählen**, um die erforderliche Identität zu durchsuchen und auszuwählen.

1. Fahren Sie anschließend mit dem Hinzufügen von Tags (optional) fort und fahren Sie mit dem Erstellen des Tresors fort.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>Weitere Informationen finden Sie unter Aktivieren der automatischen Rotation von Verschlüsselungsschlüsseln.

Wenn Sie den kundenseitig verwalteten Schlüssel angeben, der zum Verschlüsseln von Sicherungen verwendet werden muss, verwenden Sie die folgenden Methoden, um ihn anzugeben:

- Eingeben des Schlüssel-URI
- Aus Schlüsseltresor auswählen

Mithilfe der Option **Aus Schlüsseltresor auswählen können Sie** die automatische Drehung für den ausgewählten Schlüssel aktivieren. Dadurch entfällt der manuelle Versuch, auf die nächste Version zu aktualisieren. Mit dieser Option können Sie jedoch Folgendes ausführen:
- Die Aktualisierung der Aktualisierung der Schlüsselversion kann bis zu einer Stunde dauern.
- Wenn eine neue Version des Schlüssels in Kraft tritt, sollte die alte Version auch für mindestens einen nachfolgenden Sicherungsauftrag verfügbar sein (in aktiviertem Zustand), nachdem das Schlüsselupdate wirksam wurde.

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>Verwenden von Azure-Richtlinien zum Überwachen und Erzwingen der Verschlüsselung mithilfe kundenseitig verwalteter Schlüssel (Vorschau)

Azure Backup ermöglicht die Verwendung von Azure-Richtlinien, um die Verschlüsselung von Daten im Recovery Services-Tresor mithilfe kundenseitig verwalteter Schlüssel zu überwachen und zu erzwingen. Verwendung der Azure-Richtlinien:

- Die Überwachungsrichtlinie kann für das Überwachen von Tresoren mit Verschlüsselung mithilfe kundenseitig verwalteter Schlüssel (CMKs) verwendet werden, die nach dem 01.04.2021 aktiviert wurden. Bei Tresoren, bei denen die CMK-Verschlüsselung vor diesem Datum aktiviert wurde, kann die Richtlinie möglicherweise nicht angewendet werden oder führt zu falsch negativen Ergebnissen (d. h. diese Tresore werden möglicherweise als nicht konform gemeldet, obwohl die **CMK-Verschlüsselung** aktiviert ist).
- Um die Überwachungsrichtlinie zum Überwachen von Tresoren mit einer vor dem 01.04.2021 aktivierten **CMK-Verschlüsselung** zu verwenden, aktualisieren Sie einen Verschlüsselungsschlüssel über das Azure-Portal. Dies hilft beim Upgrade auf das neue Modell. Wenn Sie den Verschlüsselungsschlüssel nicht ändern möchten, geben Sie denselben Schlüssel über den Schlüssel-URI oder die Option zur Schlüsselauswahl erneut an. 

   >[!Warning]
    >Wenn Sie PowerShell zum Verwalten von Verschlüsselungsschlüsseln für die Sicherung verwenden, empfiehlt es sich nicht, die Schlüssel über das Portal zu aktualisieren.<br>Wenn Sie den Schlüssel über das Portal aktualisieren, können Sie PowerShell nicht verwenden, um den Verschlüsselungsschlüssel weiter zu aktualisieren, bis ein PowerShell-Update zur Unterstützung des neuen Modells verfügbar ist. Sie können jedoch weiterhin den Schlüssel aus der Azure-Portal aktualisieren.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Kann ich einen vorhandenen Sicherungstresor mit kundenseitig verwalteten Schlüsseln verschlüsseln?

Nein, die CMK-Verschlüsselung kann nur für neue Tresore aktiviert werden. Daher dürfen in dem Tresor noch nie Elemente geschützt worden sein. Es darf noch nicht einmal versucht worden sein, Elemente im Tresor zu schützen, bevor die Verschlüsselung mit kundenseitig verwalteten Schlüsseln aktiviert wird.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Ich habe versucht, ein Element in meinem Tresor zu schützen, dabei ist jedoch ein Fehler aufgetreten, und der Tresor enthält immer noch keine geschützten Elemente. Kann ich die CMK-Verschlüsselung für diesen Tresor aktivieren?

Nein, es darf in der Vergangenheit nicht versucht worden sein, Elemente im Tresor zu schützen.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Ich verfüge über einen Tresor, der die CMK-Verschlüsselung verwendet. Kann ich später die Verschlüsselung mit plattformseitig verwalteten Schlüsseln wiederherstellen, selbst wenn ich Sicherungselemente im Tresor geschützt habe?

Nein, wenn Sie die CMK-Verschlüsselung aktiviert haben, ist ein Wechsel zurück zu plattformseitig verwalteten Schlüsseln nicht möglich. Sie können die verwendeten Schlüssel gemäß Ihren Anforderungen ändern.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Gilt die CMK-Verschlüsselung für Azure Backup auch für Azure Site Recovery?

Nein, in diesem Artikel wird nur die Verschlüsselung von Sicherungsdaten behandelt. Für Azure Site Recovery müssen Sie die in diesem Dienst verfügbare Eigenschaft separat festlegen.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Ich habe einen der Schritte in diesem Artikel versäumt und das Schützen meiner Datenquelle fortgesetzt. Kann ich die CMK-Verschlüsselung trotzdem verwenden?

Wenn Sie die Schritte im Artikel nicht befolgen und weiterhin Elemente schützen, kann dies dazu führen, dass die Verschlüsselung mit kundenseitig verwalteten Schlüsseln bei diesem Tresor nicht möglich ist. Es wird daher empfohlen, sich auf [diese Checkliste](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) zu beziehen, bevor Sie mit dem Schützen von Elementen fortfahren.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Erhöhen sich bei Verwendung der CMK-Verschlüsselung die Kosten für meine Sicherungen?

Wenn Sie die CMK-Verschlüsselung für die Sicherung verwenden, fallen keine zusätzlichen Kosten an. Es können jedoch weiterhin Kosten für die Verwendung des Azure Key Vault anfallen, in dem Ihr Schlüssel gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Sicherheitsfeatures in Azure Backup](security-overview.md)
