---
title: Übersicht über Sicherungstresore
description: Übersicht über Sicherungstresore
ms.topic: conceptual
ms.date: 10/31/2021
ms.custom: references_regions
ms.openlocfilehash: 1a3909e525adadc2e5983e2792e407301a5b321d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473640"
---
# <a name="backup-vaults-overview"></a>Übersicht über Sicherungstresore

In diesem Artikel werden die Features eines Sicherungstresors beschrieben. Ein Sicherungstresor ist eine Speicherentität in Azure für Sicherungsdaten bestimmter neuerer, von Azure Backup unterstützter Workloads. Sie können Sicherungstresore für Sicherungsdaten von verschiedenen Azure-Diensten verwenden, z. B. von Azure Database for PostgreSQL-Servern und neueren Workloads, die Azure Backup unterstützen. Sicherungstresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand. Sicherungstresore basieren auf dem Azure Resource Manager-Modell von Azure, das Features wie die folgenden bietet:

- **Erweiterte Funktionen zum Schutz von Sicherungsdaten**: Durch Sicherungstresore bietet Azure Backup Sicherheitsfunktionen zum Schutz von Cloudsicherungen. Mit diesen Sicherheitsfunktionen wird sichergestellt, dass Sie Ihre Sicherungen schützen und Daten sicher wiederherstellen können, selbst wenn Produktions- und Sicherungsserver kompromittiert sind. [Weitere Informationen](backup-azure-security-feature.md)

- **Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)** : Azure RBAC ermöglicht eine differenzierte Verwaltung der Zugriffssteuerung in Azure. [Azure bietet verschiedene integrierte Rollen](../role-based-access-control/built-in-roles.md), während Azure Backup über drei [integrierte Rollen zum Verwalten von Wiederherstellungspunkten](backup-rbac-rs-vault.md) verfügt. Sicherungstresore sind mit der Azure RBAC kompatibel, die den Zugriff auf Sicherungen und Wiederherstellungen auf die definierten Benutzerrollen beschränkt. [Weitere Informationen](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Speichereinstellungen im Sicherungstresor

Ein Sicherungstresor ist eine Entität, in der die im Lauf der Zeit erstellten Sicherungen und Wiederherstellungspunkte gespeichert werden. Der Sicherungstresor enthält außerdem die den geschützten VMs zugeordneten Sicherungsrichtlinien.

- Azure Backup übernimmt automatisch die Speicherung für den Tresor. Wählen Sie beim Erstellen des Sicherungstresors die Speicherredundanz entsprechend Ihren Geschäftsanforderungen aus.

- Weitere Informationen zur Speicherredundanz finden Sie in diesen Artikeln zur [Georedundanz](../storage/common/storage-redundancy.md#geo-redundant-storage) und zur [lokalen](../storage/common/storage-redundancy.md#locally-redundant-storage) Redundanz.

## <a name="encryption-settings-in-the-backup-vault"></a>Verschlüsselungseinstellungen im Sicherungstresor

In diesem Abschnitt werden die verfügbaren Optionen zur Verschlüsselung Ihrer im Sicherungstresor gespeicherten Sicherungsdaten behandelt. Der Azure Backup-Dienst verwendet die **Backup Management Service**-App, um auf Azure Key Vault, aber nicht auf die verwaltete Identität des Sicherungstresors zu zugreifen.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Verschlüsselung von Sicherungsdaten mit von der Plattform verwalteten Schlüsseln

Standardmäßig werden alle Ihre Daten mit von der Plattform verwalteten Schlüsseln verschlüsselt. Sie müssen Ihrerseits keine besonderen Maßnahmen ergreifen, um diese Verschlüsselung zu aktivieren. Dies gilt für alle Workloads, die in Ihrem Sicherungstresor gesichert werden.

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Ein Sicherungstresor ist eine Verwaltungsentität, in der die im Lauf der Zeit erstellten Wiederherstellungspunkte gespeichert werden. Er bietet eine Schnittstelle zum Durchführen von sicherungsbezogenen Vorgängen. Hierzu gehören das Erstellen von bedarfsgesteuerten Sicherungen, Durchführen von Wiederherstellungen und Erstellen von Sicherungsrichtlinien.

Gehen Sie folgendermaßen vor, um einen Sicherungstresor zu erstellen.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter <https://portal.azure.com> beim Azure-Portal an.

### <a name="create-backup-vault"></a>Erstellen des Sicherungstresors

1. Geben Sie im Suchfeld **Sicherungstresore** ein.
2. Wählen Sie unter **Dienste** die Option **Sicherungstresore** aus.
3. Wählen Sie auf der Seite **Sicherungstresore** die Option **Hinzufügen** aus.
4. Stellen Sie auf der Registerkarte **Grundlagen** unter **Projektdetails** sicher, dass das richtige Abonnement ausgewählt ist, und wählen Sie dann **Neue Ressourcengruppe erstellen** aus. Geben Sie als Namen *myResourceGroup* ein.

    ![Erstellen einer neuen Ressourcengruppe](./media/backup-vault-overview/new-resource-group.png)

5. Geben Sie unter **Instanzendetails** die Zeichenfolge *myVault* als **Sicherungstresornamen** ein, und wählen Sie die gewünschte **Region** aus, in diesem Fall *USA, Osten*.
6. Wählen Sie nun die **Speicherredundanz** aus. Die Speicherredundanz kann nach dem Schützen von Elementen im Tresor nicht mehr geändert werden.
7. Wir empfehlen, dass Sie, wenn Sie Azure als primären Endpunkt für den Sicherungsspeicher verwenden, weiterhin die Standardeinstellung **Georedundant** verwenden.
8. Wenn Sie Azure nicht als primären Speicherendpunkt für die Sicherung verwenden, wählen Sie **Lokal redundant** aus. Dadurch verringern sich die Kosten für Azure-Speicher. Erfahren Sie mehr über [Georedundanz](../storage/common/storage-redundancy.md#geo-redundant-storage) and [lokale Redundanz](../storage/common/storage-redundancy.md#locally-redundant-storage).

    ![Auswählen der Speicherredundanz](./media/backup-vault-overview/storage-redundancy.png)

9. Wählen Sie unten auf der Seite die Schaltfläche „Überprüfen + erstellen“ aus.

    ![Auswählen von „Überprüfen + erstellen“](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Löschen eines Backup-Tresors

In diesem Abschnitt wird das Löschen eines Sicherungstresors beschrieben. Außerdem enthält er Anweisungen zum Entfernen von Abhängigkeiten und anschließenden Löschen eines Tresors.

### <a name="before-you-start"></a>Vorbereitung

Es ist nicht möglich, einen Sicherungstresor zu löschen, der eine der folgenden Abhängigkeiten aufweist:

- Sie können keinen Tresor löschen, der geschützte Datenquellen (z. B. Azure Database for PostgreSQL-Server) enthält.
- Es ist nicht möglich, einen Tresor zu löschen, der Sicherungsdaten enthält.

Wenn Sie versuchen, den Tresor zu löschen, ohne die Abhängigkeiten zu entfernen, werden die folgenden Fehlermeldungen angezeigt:

>Der Backup-Tresor kann nicht gelöscht werden, da im Tresor vorhandene Sicherungsinstanzen oder Sicherungsrichtlinien enthalten sind. Löschen Sie alle Sicherungsinstanzen und Sicherungsrichtlinien, die im Tresor vorhanden sind, und versuchen Sie dann, den Tresor zu löschen.

Stellen Sie sicher, dass Sie die Filteroptionen des **Datenquellentyps** im **Backup Center** durchlaufen, um keine vorhandene Sicherungsinstanz oder Richtlinie zu übersehen, die entfernt werden muss, bevor Sie den Sicherungstresor löschen können.

![Datenquellentypen](./media/backup-vault-overview/datasource-types.png)

### <a name="proper-way-to-delete-a-vault"></a>So Löschen Sie einen Tresor ordnungsgemäß

>[!WARNING]
Der folgende Vorgang ist destruktiv und kann nicht rückgängig gemacht werden. Alle Sicherungsdaten und Sicherungselemente, die dem geschützten Server zugeordnet sind, werden dauerhaft gelöscht. Gehen Sie daher mit Bedacht vor.

Zum ordnungsgemäßen Löschen eines Tresors müssen Sie die Schritte in dieser Reihenfolge ausführen:

- Überprüfen Sie, ob geschützte Elemente vorhanden sind:
  - Wechseln Sie in der linken Navigationsleiste zu **Sicherungsinstanzen**. Alle hier aufgelisteten Elemente müssen zuerst gelöscht werden.

Nachdem Sie diese Schritte abgeschlossen haben, können Sie mit dem Löschen des Tresors fortfahren.

### <a name="delete-the-backup-vault"></a>Löschen des Sicherungstresors

Wenn sich keine Elemente mehr im Tresor befinden, klicken Sie im Tresordashboard auf **Löschen**. Sie werden in einem Bestätigungstext gefragt, ob Sie den Tresor löschen möchten.

![Löschen des Tresors](./media/backup-vault-overview/delete-vault.png)

1. Wählen Sie **Ja** aus, um das Löschen des Tresors zu bestätigen. Der Tresor wird gelöscht. Sie werden im Portal zum Dienstmenü **Neu** zurückgeführt.

## <a name="monitor-and-manage-the-backup-vault"></a>Überwachen und Verwalten des Sicherungstresors

In diesem Abschnitt wird erläutert, wie Sie das Dashboard **Übersicht** für Sicherungstresore zum Überwachen und Verwalten von Sicherungstresoren verwenden. Der Übersichtsbereich enthält zwei Kacheln: **Aufträge** und **Instanzen**.

![Übersichtsdashboard](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Verwalten von Sicherungsinstanzen

Die Kachel **Aufträge** bietet eine zusammenfassende Ansicht aller Sicherungs- und Wiederherstellungsaufträge in Ihrem Sicherungstresor. Wenn Sie eine der Zahlen auf dieser Kachel auswählen, können Sie weitere Informationen zu Aufträgen für einen bestimmten Datenquellentyp, Vorgangstyp und Status anzeigen.

![Sicherungsinstanzen](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Verwalten von Sicherungsaufträgen

Die Kachel **Sicherungsinstanzen** bietet eine zusammenfassende Ansicht aller Sicherungsinstanzen im Sicherungstresor. Durch Auswählen einer der Zahlen auf dieser Kachel können Sie weitere Informationen zu Sicherungsinstanzen für einen bestimmten Datenquellentyp und Schutzstatus anzeigen.

![Sicherungsaufträge](./media/backup-vault-overview/backup-jobs.png)

## <a name="move-a-backup-vault-across-azure-subscriptionsresource-groups-public-preview"></a>Verschieben eines Azure Backup-Tresors zwischen Azure-Abonnements/-Ressourcengruppen (öffentliche Vorschauversion)

In diesem Abschnitt wird erläutert, wie Sie einen (für Azure Backup konfigurierten) Sicherungstresor über Azure-Abonnements und -Ressourcengruppen hinweg mithilfe des Azure-Portals verschieben.

>[!Note]
>Sie können Azure Backup-Tresore auch mithilfe von [PowerShell](/powershell/module/az.resources/move-azresource?view=azps-6.3.0&preserve-view=true) und [CLI](/cli/azure/resource?view=azure-cli-latest&preserve-view=true#az_resource_move) in eine andere Ressourcengruppe oder ein anderes Abonnement verschieben.

### <a name="supported-regions"></a>Unterstützte Regionen

Das Verschieben des Tresors zwischen Abonnements und Ressourcengruppen wird in allen öffentlichen Bereichen unterstützt.

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-resource-group"></a>Verwenden des Azure-Portals zum Verschieben eines Azure Backup-Tresors in eine andere Ressourcengruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie die Liste mit den Azure Backup-Tresoren, und wählen Sie den Tresor aus, den Sie verschieben möchten.

   Im Tresordashboard werden die Tresordetails angezeigt.

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-inline.png" alt-text="Screenshot: Dashboard des Tresors, der in eine andere Ressourcengruppe verschoben werden soll." lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-expanded.png"::: 

1. Klicken Sie im Menü **Overview** des Tresors auf **Verschieben**, und wählen Sie dann **In eine andere Ressourcengruppe verschieben** aus.

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-resource-group-inline.png" alt-text="Screenshot: Option zum Verschieben des Azure Backup-Tresors in eine andere Ressourcengruppe." lightbox="./media/backup-vault-overview/select-move-to-another-resource-group-expanded.png":::
   >[!Note]
   >Nur das Administratorabonnement verfügt über die erforderlichen Berechtigungen zum Verschieben eines Tresors.

1. Wählen Sie in der Dropdownliste **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen.

   Das Abonnement bleibt unverändert und wird automatisch aufgefüllt.

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-inline.png" alt-text="Screenshot: Auswahl einer vorhandenen Ressourcengruppe oder Erstellung einer neuen Ressourcengruppe." lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-expanded.png":::

1. Auf der Registerkarte **Zu verschiebende Ressourcen** wird der Azure Backup-Tresor, der verschoben werden muss, einer Überprüfung unterzogen. Dieser Vorgang kann einige Minuten in Anspruch nehmen. Warten Sie, bis die Überprüfung abgeschlossen ist.

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-inline.png" alt-text="Screenshot: Überprüfungsstatus des Azure Backup-Tresors." lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-expanded.png"::: 

1. Aktivieren Sie zur Bestätigung das Kontrollkästchen _Mir ist bewusst, dass Tools und Skripts, die den verschobenen Ressourcen zugeordnet sind, erst funktionieren, wenn ich sie auf die Verwendung neuer Ressourcen-IDs aktualisiere_, und wählen Sie dann **Verschieben** aus.
 
   >[!Note]
   >Der Ressourcenpfad ändert sich nach dem Verschieben des Tresors zwischen Ressourcengruppen oder Abonnements. Stellen Sie sicher, dass Sie die Tools und Skripts nach Abschluss des Verschiebens mit dem neuen Ressourcenpfad aktualisieren.

Warten Sie, bis die Verschiebung abgeschlossen ist, und führen Sie erst danach weitere Vorgänge für den Tresor durch. Alle für den Azure Backup-Tresor während der Verschiebung ausgeführten Vorgänge schlagen fehl. Nach Abschluss des Vorgangs sollte der Azure Backup-Tresor in der Zielressourcengruppe angezeigt werden.

>[!Important]
>Wenn beim Verschieben des Tresors ein Fehler auftritt, lesen Sie den Abschnitt [Fehlercodes und Problembehandlung](#error-codes-and-troubleshooting).  

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-subscription"></a>Verwenden des Azure-Portals zum Verschieben eines Azure Backup-Tresors in ein anderes Abonnement

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie die Liste mit den Azure Backup-Tresoren, und wählen Sie den Tresor aus, den Sie verschieben möchten.
   
   Im Tresordashboard werden die Tresordetails angezeigt.

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-inline.png" alt-text="Screenshot: Dashboard des Tresors, der in ein anderes Abonnement verschoben werden soll." lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-expanded.png"::: 

1. Klicken Sie im Menü **Übersicht** des Tresors auf **Verschieben**, und wählen Sie dann **In ein anderes Abonnement verschieben** aus.

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-subscription-inline.png" alt-text="Screenshot: Option zum Verschieben des Azure Backup-Tresors in ein anderes Azure-Abonnement." lightbox="./media/backup-vault-overview/select-move-to-another-subscription-expanded.png"::: 
   >[!Note]
   >Nur das Administratorabonnement verfügt über die erforderlichen Berechtigungen zum Verschieben eines Tresors.

1. Wählen Sie in der Dropdownliste **Abonnement** ein vorhandenes Abonnement aus.

   Beim Verschieben von Tresoren zwischen Abonnements muss sich das Zielabonnement im selben Mandanten wie das Quellabonnement befinden. Informationen zum Verschieben eines Tresors in einen anderen Mandanten finden Sie unter [Übertragen eines Abonnements in ein anderes Verzeichnis](../role-based-access-control/transfer-subscription.md).

1. Wählen Sie in der Dropdownliste **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen.

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-inline.png" alt-text="Screenshot: Auswahl einer vorhandenen Ressourcengruppe oder Erstellung einer neuen Ressourcengruppe in einem anderen Abonnement." lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-expanded.png":::

1. Auf der Registerkarte **Zu verschiebende Ressourcen** wird der Azure Backup-Tresor, der verschoben werden muss, einer Überprüfung unterzogen. Dieser Vorgang kann einige Minuten in Anspruch nehmen. Warten Sie, bis die Überprüfung abgeschlossen ist.

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-inline.png" alt-text="Screenshot: Überprüfungsstatus des Azure Backup-Tresors, der in ein anderes Azure-Abonnement verschoben werden soll." lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-expanded.png"::: 

1. Aktivieren Sie zur Bestätigung das Kontrollkästchen _Mir ist bewusst, dass Tools und Skripts, die den verschobenen Ressourcen zugeordnet sind, erst funktionieren, wenn ich sie auf die Verwendung neuer Ressourcen-IDs aktualisiere_, und wählen Sie dann **Verschieben** aus.
 
   >[!Note]
   >Der Ressourcenpfad ändert sich nach dem Verschieben des Tresors zwischen Ressourcengruppen oder Abonnements. Stellen Sie sicher, dass Sie die Tools und Skripts nach Abschluss des Verschiebens mit dem neuen Ressourcenpfad aktualisieren.

Warten Sie, bis die Verschiebung abgeschlossen ist, und führen Sie erst danach weitere Vorgänge für den Tresor durch. Alle für den Azure Backup-Tresor während der Verschiebung ausgeführten Vorgänge schlagen fehl. Nach Abschluss des Vorgangs sollte der Azure Backup-Tresor im Zielabonnement und in der Zielressourcengruppe angezeigt werden.

>[!Important]
>Wenn beim Verschieben des Tresors ein Fehler auftritt, lesen Sie den Abschnitt [Fehlercodes und Problembehandlung](#error-codes-and-troubleshooting).

### <a name="error-codes-and-troubleshooting"></a>Fehlercodes und Problembehandlung

Behandeln Sie die folgenden allgemeinen Probleme, die beim Verschieben des Azure Backup-Tresors auftreten können:

#### <a name="backupvaultmoveresourcespartiallysucceeded"></a>BackupVaultMoveResourcesPartiallySucceeded   

**Ursache**: Dieser Fehler tritt möglicherweise auf, wenn das Verschieben des Azure Backup-Tresors nur teilweise erfolgreich ist.

**Empfehlung**: Das Problem sollte innerhalb von 36 Stunden automatisch behoben werden. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support.

#### <a name="backupvaultmoveresourcescriticalfailure"></a>BackupVaultMoveResourcesCriticalFailure 

**Ursache**: Dieser Fehler tritt möglicherweise auf, wenn das Verschieben des Azure Backup-Tresors mit einem kritischen Fehler fehlschlägt. 

**Empfehlung**: Das Problem sollte innerhalb von 36 Stunden automatisch behoben werden. Wenn das Problem weiterhin auftritt, wenden Sie sich an den Microsoft-Support. 

#### <a name="usererrorbackupvaultresourcemoveinprogress"></a>UserErrorBackupVaultResourceMoveInProgress 

**Ursache**: Dieser Fehler tritt möglicherweise auf, wenn Sie versuchen, Vorgänge für den Azure Backup-Tresor durchzuführen, während er verschoben wird. 

**Empfehlung**: Warten Sie, bis die Verschiebung abgeschlossen ist, und wiederholen Sie dann den Vorgang. 
#### <a name="usererrorbackupvaultresourcemovenotallowedformultipleresources"></a>UserErrorBackupVaultResourceMoveNotAllowedForMultipleResources

**Ursache**: Dieser Fehler tritt möglicherweise auf, wenn Sie versuchen, mehrere Azure Backup-Tresore in einem einzigen Schritt zu verschieben. 

**Empfehlung**: Stellen Sie sicher, dass für jeden Verschiebungsvorgang nur ein Azure Backup-Tresor ausgewählt ist. 
#### <a name="usererrorbackupvaultresourcemovenotalloweduntilresourceprovisioned"></a>UserErrorBackupVaultResourceMoveNotAllowedUntilResourceProvisioned

**Ursache**: Dieser Fehler tritt möglicherweise auf, wenn der Tresor noch nicht bereitgestellt wurde. 

**Empfehlung**: Wiederholen Sie den Vorgang nach einiger Zeit.

#### <a name="backupvaultresourcemoveisnotenabled"></a>BackupVaultResourceMoveIsNotEnabled 

**Ursache**: Das Verschieben von Ressourcen für den Azure Backup-Tresor wird in der ausgewählten Azure-Region derzeit nicht unterstützt.

**Empfehlung**: Stellen Sie sicher, dass Sie eine der Regionen ausgewählt haben, die das Verschieben von Azure Backup-Tresoren unterstützt. Siehe [Unterstützte Regionen](#supported-regions).

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren der Sicherung für Azure-PostgreSQL-Datenbanken](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
