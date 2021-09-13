---
title: Sichern von Azure Managed Disks
description: Erfahren Sie, wie Sie Azure Managed Disks über das Azure-Portal sichern können.
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: 9bbb29aa4741f71448876a0af947964f728b0fb0
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2021
ms.locfileid: "113300734"
---
# <a name="back-up-azure-managed-disks"></a>Sichern von Azure Managed Disks

In diesem Artikel wird beschrieben, wie Sie [Azure Managed Disk](../virtual-machines/managed-disks-overview.md) über das Azure-Portal sichern.

In diesem Artikel lernen Sie Folgendes:

- Erstellen eines Sicherungstresors

- Erstellen einer Sicherungsrichtlinie

- Konfigurieren der Sicherung eines Azure-Datenträgers

- Ausführen eines bedarfsgesteuerten Sicherungsauftrag

Informationen zur regionalen Verfügbarkeit von Azure Disk Backup, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Ein Sicherungstresor ist eine Speicherentität in Azure, die Sicherungsdaten für verschiedene neuere Workloads enthält, die von Azure Backup unterstützt werden, z. B. Azure Database for PostgreSQL Server und Azure-Datenträger. Sicherungstresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand. Sicherungstresore basieren auf dem Azure Resource Manager-Modell von Azure, das erweiterte Funktionen bietet, die das Schützen von Sicherungsdaten erleichtern.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.
1. Geben Sie im Suchfeld **Backup Center** ein.
1. Wählen Sie unter **Dienste** die Option **Backup Center** aus.
1. Wählen Sie auf der Seite **Backup Center** die Option **Tresor** aus.

   ![Tresor im Backup Center auswählen](./media/backup-managed-disks/backup-center.png)

1. Wählen Sie auf dem Bildschirm **Initiieren: Tresor erstellen** die Option **Sicherungstresor** und dann **Fortsetzen** aus.

   ![Initiieren: Tresor erstellen](./media/backup-managed-disks/initiate-create-vault.png)

1. Geben Sie auf der Registerkarte **Grundlagen** das Abonnement, die Ressourcengruppe, den Namen des Sicherungstresors, die Region und die Sicherungsspeicherredundanz an. Wählen Sie anschließend **Überprüfen und erstellen** aus. Weitere Informationen finden Sie unter [Erstellen eines Sicherungstresors](./backup-vault-overview.md#create-a-backup-vault).

   ![Tresor überprüfen und erstellen](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Erstellen der Sicherungsrichtlinie

1. Navigieren Sie in dem im vorherigen Schritt erstellten **Sicherungstresor** *DemoVault* zu **Sicherungsrichtlinien**, und wählen Sie **Hinzufügen** aus.

   ![Sicherungsrichtlinie hinzufügen](./media/backup-managed-disks/backup-policies.png)

1. Geben Sie auf der Registerkarte **Grundlagen** den Richtliniennamen an, und wählen Sie **Azure-Datenträger** als **DataSource-Typ** aus. Der Tresor wurde bereits ausgefüllt, und die ausgewählten Tresoreigenschaften werden angezeigt.

   >[!NOTE]
   > Obwohl der ausgewählte Tresor möglicherweise die Einstellung für die globale Redundanz aufweist, unterstützt Azure Disk Backup derzeit nur den Momentaufnahmen-Datenspeicher. Alle Sicherungen werden in einer Ressourcengruppe in Ihrem Abonnement gespeichert und nicht in den Sicherungstresorspeicher kopiert.

   ![Datenquellentyp auswählen](./media/backup-managed-disks/datasource-type.png)

1. Wählen Sie auf der Registerkarte **Sicherungsrichtlinie** die Sicherungszeitplan-Häufigkeit aus.

   ![Sicherungszeitplan-Häufigkeit auswählen](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure Disk Backup ermöglicht mehrere Sicherungen pro Tag. Wenn Sie häufigere Sicherungen benötigen, wählen Sie die **stündliche** Sicherungshäufigkeit mit der Möglichkeit, Sicherungen mit Intervallen von alle 4, 6, 8 oder 12 Stunden zu erstellen. Die Sicherungen werden basierend auf dem unter **Zeit** ausgewählten Intervall geplant. Wenn Sie z. B. **Alle vier Stunden** auswählen, werden die Sicherungen im Intervall von ungefähr 4 Stunden durchgeführt, sodass die Sicherungen gleichmäßig über den Tag verteilt sind. Wenn eine Sicherung pro Tag ausreichend ist, wählen Sie als Sicherungshäufigkeit **Täglich** aus. Bei der Sicherungshäufigkeit „Täglich“ können Sie die Uhrzeit angeben, zu der die Sicherungen erstellt werden. Es ist wichtig zu beachten, dass mit der Uhrzeit die Startzeit der Sicherung angegeben wird und nicht die Zeit, zu der die Sicherung abgeschlossen ist. Die für den Abschluss des Sicherungsvorgangs erforderliche Zeit hängt von verschiedenen Faktoren ab, einschließlich der Datenträgergröße und des Churns zwischen aufeinanderfolgenden Sicherungen. Azure Disk Backup ist jedoch ein agentloser Sicherungsdienst, der [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) verwendet, die sich nicht auf die Leistung der Anwendung in der Produktion auswirkt.

1. Wählen Sie auf der Registerkarte **Sicherungsrichtlinie** die Aufbewahrungseinstellungen aus, die die Recovery Point Objective (RPO) erfüllen.

   Die Standardaufbewahrungsregel gilt, wenn keine andere Aufbewahrungsregel angegeben ist. Die Standardaufbewahrungsregel kann geändert werden, um die Aufbewahrungsdauer zu ändern, Sie kann jedoch nicht gelöscht werden. Über **Aufbewahrungsregel hinzufügen** können Sie eine neue Aufbewahrungsregel hinzufügen.

   ![Aufbewahrungsregel hinzufügen](./media/backup-managed-disks/add-retention-rule.png)

   Sie können die **erste erfolgreiche Sicherung** auswählen, das täglich oder wöchentlich erstellt wird, und angeben, wie lange die spezifischen Sicherungen aufbewahrt werden, bevor sie gelöscht werden. Diese Option ist nützlich, um bestimmte Sicherungen des Tages oder der Woche über einen längeren Zeitraum aufzubewahren. Alle anderen häufigen Sicherungen können für eine kürzere Dauer aufbewahrt werden.

   ![Aufbewahrungseinstellungen](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Azure Backup für verwaltete Datenträger verwendet inkrementelle Momentaufnahmen, die auf 200 Momentaufnahmen pro Datenträger beschränkt sind. Um zusätzlich zu den geplanten Sicherungen noch bedarfsgesteuerte Sicherungen zu ermöglichen, schränkt die Sicherungsrichtlinie die Gesamtanzahl der Sicherungen auf 180 ein. Weitere Informationen finden Sie unter [Inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md#restrictions) für verwaltete Datenträger.

1. Schließen Sie die Erstellung der Sicherungsrichtlinie ab, indem Sie **Überprüfen und erstellen** auswählen.

## <a name="configure-backup"></a>Konfigurieren der Sicherung

- Azure Disk Backup unterstützt nur das Sichern der Betriebsebene. Das Kopieren von Sicherungen auf die Tresorspeicherebene ist derzeit nicht verfügbar. Die Speicherredundanzeinstellung des Sicherungstresors (LRS/GRS) gilt nicht für Sicherungen, die auf der Betriebsebene gespeichert sind.
Inkrementelle Momentaufnahmen werden im Standard-HDD- Speicher gespeichert, unabhängig vom Speichertyp des Quelldatenträgers. Für höhere Zuverlässigkeit werden inkrementelle Momentaufnahmen standardmäßig in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) (ZRS) gespeichert, wenn die entsprechende Region ZRS unterstützt.

- Azure Disk Backup unterstützt abonnementübergreifende Sicherungen und Wiederherstellungen, bei denen sich der Sicherungstresor und der Quelldatenträger in unterschiedlichen Abonnements befinden. Die regionsübergreifende Sicherung und Wiederherstellung wird hingegen nicht unterstützt. Der Sicherungstresor und die zu sichernden Datenträger können sich also im selben oder in unterschiedlichen Abonnements befinden. Allerdings müssen sich der Sicherungstresor und die zu sichernden Datenträger in derselben Region befinden.

- Beim Konfigurieren der Datenträgersicherung können Sie die Momentaufnahme-Ressourcengruppe, die einer Sicherungsinstanz zugewiesen ist, nicht ändern. 

Der Sicherungstresor verwendet die verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Zum Konfigurieren der Sicherung verwalteter Datenträger benötigt die verwaltete Identität des Sicherungstresors einen Satz von Berechtigungen für die Quelldatenträger und Ressourcengruppen, in denen Momentaufnahmen erstellt und verwaltet werden.

Eine systemseitig zugewiesene verwaltete Identität ist auf eine Ressource beschränkt und an den Lebenszyklus dieser Ressource gebunden. Sie können der verwalteten Identität mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) Berechtigungen erteilen. Eine verwaltete Identität ist ein spezieller Dienstprinzipal, der nur zusammen mit Azure-Ressourcen verwendet werden kann. Informieren Sie sich ausführlicher über [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

Die folgenden Voraussetzungen müssen erfüllt sein, um das Sichern von verwalteten Datenträgern zu konfigurieren:

1. Weisen Sie der verwalteten Identität des Sicherungstresors auf dem zu sichernden Quelldatenträger die Rolle **Datenträgersicherungsleser** zu.

   1. Wechseln Sie zu dem Datenträger, der gesichert werden muss.

   1. Wechseln Sie zur **Zugriffssteuerung (IAM)** , und wählen Sie anschließend **Rollenzuweisung hinzufügen** aus.

   1. Wählen Sie im rechten Kontextbereich in der Dropdownliste **Rolle** die Option **Datenträgersicherungsleser** aus. Wählen Sie die verwaltete Identität des Sicherungstresors und dann **Speichern** aus.

   >[!TIP]
   >Geben Sie den Namen des Sicherungstresors ein, um die verwaltete Identität des Tresors auszuwählen.

   ![Rolle „Datenträgersicherungsleser“ hinzufügen](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Weisen Sie der verwalteten Identität des Sicherungstresors in der Ressourcengruppe, in der Sicherungen vom Azure Backup-Dienst erstellt und verwaltet werden, die Rolle **Mitwirkender für die Datenträgermomentaufnahme** zu. Die Datenträgermomentaufnahmen werden in einer Ressourcengruppe innerhalb Ihres Abonnements gespeichert. Damit der Azure Backup-Dienst Momentaufnahmen erstellen, speichern und verwalten kann, müssen Sie Berechtigungen für den Sicherungstresor bereitstellen.

   **Auswählen der Ressourcengruppe zum Speichern und Verwalten von Momentaufnahmen:**

   - Wählen Sie nicht dieselbe Ressourcengruppe wie die des Quelldatenträgers aus.

   - Als Richtlinie wird empfohlen, eine dedizierte Ressourcengruppe als Momentaufnahmen-Datenspeicher zu erstellen, der vom Azure Backup-Dienst verwendet werden soll. Mit einer dedizierten Ressourcengruppe lassen sich die Zugriffsrechte auf die Ressourcengruppe beschränken, was Sicherheit gewährleistet und eine einfache Verwaltung der Sicherungsdaten ermöglicht.

   - Sie können diese Ressourcengruppe zum Speichern von Momentaufnahmen auf mehreren Datenträgern verwenden, die gesichert werden (oder deren Sicherung geplant ist).  

   - Sie können keine inkrementelle Momentaufnahme für einen bestimmten Datenträger außerhalb des Abonnements dieses Datenträgers erstellen. Wählen Sie daher eine Ressourcengruppe im selben Abonnement wie der zu sichernde Datenträger aus. Weitere Informationen finden Sie unter [Inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md#restrictions) für verwaltete Datenträger.

   - Beim Konfigurieren der Datenträgersicherung können Sie die Momentaufnahme-Ressourcengruppe, die einer Sicherungsinstanz zugewiesen ist, nicht ändern.

   - Azure Backup erstellt während eines Sicherungsvorgangs ein Speicherkonto in der Ressourcengruppe für Momentaufnahmen. Pro Momentaufnahme-Ressourcengruppe wird nur ein Speicherkonto erstellt. Das Konto wird auf mehreren Datenträger-Sicherungsinstanzen wiederverwendet, die dieselbe Ressourcengruppe wie die Momentaufnahme-Ressourcengruppe verwenden.
     
     - Momentaufnahmen werden nicht im Speicherkonto gespeichert. Inkrementelle Momentaufnahmen von verwalteten Datenträgern sind ARM-Ressourcen, die in der Ressourcengruppe und nicht in einem Speicherkonto erstellt werden. 
     
     - Das Speicherkonto wird verwendet, um Metadaten für jeden Wiederherstellungspunkt zu speichern. Azure Backup Dienst erstellt einen Blobcontainer pro Datenträgersicherungsinstanz. Für jeden Wiederherstellungspunkt wird ein Blockblob erstellt, um Metadaten zu speichern, die den Wiederherstellungspunkt beschreiben (z. B. Abonnement, Datenträger-ID, Datenträgerattribute usw.) und kaum Speicherplatz (wenige KiBs) belegen.
     
     - Das Speicherkonto wird als RA-GZRS erstellt, wenn die Region Zonenredundanz unterstützt. Wenn die Region keine Zonenredundanz unterstützt, wird das Speicherkonto als RA-GRS erstellt.<br>Wenn eine vorhandene Richtlinie die Erstellung eines Speicherkontos mit GRS-Redundanz für das Abonnement oder die Ressourcengruppe verhindert, wird das Speicherkonto als LRS erstellt. Das erstellte Speicherkonto ist **Universell v2**, wobei Blockblobs auf der heißen Ebene im Blobcontainer gespeichert werden. 
     
     - Die Anzahl der Wiederherstellungspunkte wird durch die Sicherungsrichtlinie bestimmt, die zum Konfigurieren der Sicherung der Datenträgersicherungsinstanz verwendet wird. Ältere Blockblobs werden gemäß dem Garbage Collection-Prozess gelöscht, wenn die entsprechenden älteren Wiederherstellungspunkte gelöscht werden.
   
   - Wenden Sie keine Ressourcensperren oder -richtlinien auf vom Azure Backup-Dienst erstellte Momentaufnahme-Ressourcengruppen oder Speicherkonten an. Der Dienst erstellt und verwaltet Ressourcen in dieser Momentaufnahme-Ressourcengruppe, die einer Sicherungsinstanz zugewiesen ist, wenn Sie die Sicherung eines Datenträgers konfigurieren. Das Speicherkonto und die darin gespeicherten Ressourcen werden vom Dienst erstellt und sollten nicht gelöscht oder verschoben werden.

     >[!NOTE]
     >Wenn ein Speicherkonto gelöscht wird, schlagen Sicherungen fehl, und die Wiederherstellung aller vorhandenen Wiederherstellungspunkte schlägt fehl.

Gehen Sie wie folgt vor, um eine Rolle zuzuweisen:

   1. Wechseln Sie zur Ressourcengruppe. Sie Ressourcengruppe ist z. B. *SnapshotRG* und befindet sich im selben Abonnement wie der zu sichernde Datenträger.

   1. Wechseln Sie zur **Zugriffssteuerung (IAM)** , und wählen Sie anschließend **Rollenzuweisung hinzufügen** aus.

   1. Wählen Sie im rechten Kontextbereich in der Dropdownliste **Rolle** die Option **Mitwirkender für die Datenträgermomentaufnahme** aus. Wählen Sie die verwaltete Identität des Sicherungstresors und dann **Speichern** aus.

   >[!TIP]
   >Geben Sie den Namen des Sicherungstresors ein, um die verwaltete Identität des Tresors auszuwählen.

   ![Rolle „Mitwirkender für die Datenträgermomentaufnahme“ hinzufügen](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Vergewissern Sie sich, dass die verwaltete Identität des Sicherungstresors über den richtigen Satz an Rollenzuweisungen für den Quelldatenträger und die Ressourcengruppe, die als Momentaufnahmen-Datenspeicher verfügt, fungiert.

   1. Wechseln Sie zu **Sicherungstresor > Identität**, und wählen Sie **Azure-Rollenzuweisungen**.

      ![Azure-Rollenzuweisungen auswählen](./media/backup-managed-disks/azure-role-assignments.png)

   1. Überprüfen Sie, ob Rolle, Ressourcenname und Ressourcentyp ordnungsgemäß angezeigt werden.

      ![Rolle, Ressourcennamen und Ressourcentyp überprüfen](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Während die Rollenzuweisungen im Portal richtig widergegeben werden, kann es ungefähr 15 Minuten dauern, bis die Berechtigung auf die verwaltete Identität des Sicherungstresors angewendet wird.

1. Nachdem die Voraussetzungen erfüllt sind, wechseln Sie zu **Sicherungstresor > Übersicht**, und wählen Sie **Sicherung** aus, um mit der Konfiguration der Datenträgersicherung zu beginnen.

   ![Sicherung auswählen](./media/backup-managed-disks/select-backup.png)

1. Wählen Sie auf der Registerkarte **Grundlagen** die Option **Azure-Datenträger** als DataSource-Typ aus.

   ![Azure-Datenträger auswählen](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure Backup verwendet [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md#restrictions) von verwalteten Datenträgern, auf denen nur die Deltaänderungen auf dem Datenträger seit der letzten Momentaufnahme im HDD Standard-Speicher gespeichert werden. Dies ist auch unabhängig vom Speichertyp des übergeordneten Datenträgers. Für höhere Zuverlässigkeit werden inkrementelle Momentaufnahmen standardmäßig in zonenredundantem Speicher (ZRS) in Regionen gespeichert, die ZRS unterstützen. Derzeit unterstützt Azure Disk Backup operative Sicherungen von verwalteten Datenträgern, die die Sicherungen nicht in den Speicher des Sicherungstresors kopieren. Die Redundanzeinstellung für den Sicherungsspeicher des Sicherungstresors gilt daher nicht für die Wiederherstellungspunkte.

1. Wählen Sie auf der Registerkarte **Sicherungsrichtlinie** eine Sicherungsrichtlinie aus.

   ![Auswählen der Sicherungsrichtlinie](./media/backup-managed-disks/choose-backup-policy.png)

1. Wählen Sie auf der Registerkarte **Ressourcen** die Option **Azure-Datenträger auswählen**. Wählen Sie im rechten Kontextbereich die zu sichernden Datenträger aus.

   ![Zu sichernde Datenträger auswählen](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >Im Portal können Sie zwar mehrere Datenträger auswählen und die Sicherung konfigurieren, jeder Datenträger ist jedoch eine einzelne Sicherungsinstanz. Derzeit unterstützt Azure Disk Backup nur die Sicherung einzelner Datenträger. Zeitpunktsicherungen von mehreren an einen virtuellen Computer angefügten Datenträgern werden nicht unterstützt.
   >
   >Bei der Verwendung des Portals sind Sie auf die Auswahl der Datenträger innerhalb eines Abonnements beschränkt. Wenn Sie mehrere Datenträger sichern müssen oder die Datenträger auf verschiedene Abonnements verteilt sind, können Sie Skripts zur Automatisierung verwenden.
   >
   >Weitere Informationen zur regionalen Verfügbarkeit von Azure Disk Backup, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).

1. Wählen Sie eine **Momentaufnahme-Ressourcengruppe** und dann **Überprüfen** aus. Dies ist die Ressourcengruppe, in der der Azure Backup-Dienst die inkrementellen Momentaufnahmen für den Sicherungstresor erstellt und verwaltet. Die verwaltete Identität wird mit den erforderlichen Rollenberechtigungen als Teil der Voraussetzungen zugewiesen.

   ![Momentaufnahme-Ressourcengruppe auswählen](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >Die Überprüfung kann einige Minuten dauern, bevor Sie die Konfiguration des Sicherungsworkflows abschließen können. Die Überprüfung kann fehlschlagen, wenn:
   >
   > - Ein Datenträger nicht unterstützt wird. Informationen zu nicht unterstützten Szenarien finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).
   > - Die verwaltete Identität des Sicherungstresors auf dem **Datenträger**, der gesichert werden soll, oder in der **Momentaufnahme-Ressourcengruppe**, in die inkrementellen Momentaufnahmen gespeichert werden, keine gültigen Rollenzuweisungen aufweist.

1. Wählen Sie nach erfolgreicher Überprüfung **Überprüfen und konfigurieren** aus, um die Sicherung der ausgewählten Datenträger zu konfigurieren.

## <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung

1. Navigieren Sie in dem im vorherigen Schritt erstellten **Sicherungstresor** *DemoVault* zu **Sicherungsinstanzen**, und wählen Sie eine Sicherungsinstanz aus.

   ![Sicherungsinstanz auswählen](./media/backup-managed-disks/select-backup-instance.png)

1. Auf dem Bildschirm **Sicherungsinstanzen** finden Sie:

   - **Wichtige** Informationen, wie z. B. den Namen des Quelldatenträgers, die Momentaufnahme-Ressourcengruppe, in der inkrementelle Momentaufnahmen gespeichert werden, den Sicherungstresor und die Sicherungsrichtlinie.
   - Den **Auftragsstatus**, der eine Zusammenfassung der Sicherungs- und Wiederherstellungsvorgänge und deren Status in den letzten sieben Tagen angibt.
   - Eine Liste der **Wiederherstellungspunkte** für den ausgewählten Zeitraum.

1. Wählen Sie **Sichern** aus, um eine bedarfsgesteuerte Sicherung zu starten.

   ![Auswählen von „Jetzt sichern“](./media/backup-managed-disks/backup-now.png)

1. Wählen Sie eine der Aufbewahrungsregeln aus, die der Sicherungsrichtlinie zugeordnet sind. Diese Aufbewahrungsregel bestimmt die Aufbewahrungsdauer für diese bedarfsgesteuerte Sicherung. Wählen Sie **Jetzt sichern** aus, um die Sicherung zu starten.

   ![Sicherung starten](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Nachverfolgen eines Sicherungsvorgangs

Der Azure Backup-Dienst erstellt einen Auftrag für geplante Sicherungen, und Sie können eine bedarfsgesteuerte Sicherung zur Nachverfolgung auslösen. So zeigen Sie den Status des Sicherungsauftrags an

1. Navigieren Sie zum Bildschirm **Sicherungsinstanz**. Hier wird das Dashboard „Aufträge“ mit Betrieb und Status der letzten sieben Tage angezeigt.

   ![Dashboard „Aufträge“](./media/backup-managed-disks/jobs-dashboard.png)

1. Wenn Sie den Status des Sicherungsvorgangs anzeigen möchten, wählen Sie **Alle anzeigen** aus, um laufende und vergangene Aufträge dieser Sicherungsinstanz anzuzeigen.

   ![„Alle anzeigen“ auswählen](./media/backup-managed-disks/view-all.png)

1. Überprüfen Sie die Liste der Sicherungs- und Wiederherstellungsaufträge und deren Status. Wählen Sie in der Liste mit den Aufträgen einen Auftrag aus, um die Auftragsdetails anzuzeigen.

   ![Auftrag auswählen, um Details anzuzeigen](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellen von Azure Managed Disks](restore-managed-disks.md)