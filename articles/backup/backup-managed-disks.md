---
title: Sichern von Azure Managed Disks
description: Erfahren Sie, wie Sie Azure Managed Disks über das Azure-Portal sichern können.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 4561ff53588c6662759b0e86529f20e7cf837bbe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668136"
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

- Azure Disk Backup unterstützt nur die Sicherung auf Betriebsebene. Das Kopieren von Sicherungen auf die Tresorspeicherebene wird derzeit nicht unterstützt. Die Speicherredundanzeinstellung des Azure Backup-Tresors (LRS/GRS) gilt nicht für die auf der Betriebsebene gespeicherten Sicherungen. 
  Inkrementelle Momentaufnahmen werden in einem HDD Standard-Speicher gespeichert, unabhängig vom ausgewählten Speichertyp des Quelldatenträgers. Für höhere Zuverlässigkeit werden inkrementelle Momentaufnahmen standardmäßig in [zonenredundantem Speicher](/azure/storage/common/storage-redundancy) (ZRS) gespeichert, wenn die entsprechende Region ZRS unterstützt.

- Azure Disk Backup unterstützt abonnementübergreifende Sicherungen und Wiederherstellungen, bei denen sich der Sicherungstresor und der Quelldatenträger in unterschiedlichen Abonnements befinden. Daher werden die regionsübergreifenden Sicherungen und Wiederherstellungen nicht unterstützt. Dadurch können der Azure Backup-Tresor und der Datenträger in denselben oder verschiedenen Abonnements gesichert werden. Allerdings müssen sich der Sicherungstresor und der zu sichernde Datenträger in derselben Region befinden.

- Beim Konfigurieren der Datenträgersicherung können Sie die Ressourcengruppe für Momentaufnahmen, die einer Sicherungsinstanz zugewiesen ist, nicht ändern.

Gehen Sie zum Konfigurieren einer Datenträgersicherung wie folgt vor:

1. Wechseln Sie zu **Backup Center** -> **Übersicht**, und klicken Sie auf **+ Sicherung**, um mit dem Konfigurieren der Sicherung des Datenträgers zu beginnen.

   :::image type="content" source="./media/backup-managed-disks/start-configuring-backup-of-disk-inline.png" alt-text="Screenshot: Option zum Starten von Azure Disk Backup." lightbox="./media/backup-managed-disks/start-configuring-backup-of-disk-expanded.png":::

1. Wählen Sie in der Dropdownliste **Datenquellentyp** die Option **Azure Disks** aus, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/backup-managed-disks/select-azure-disks-as-datasource-type-inline.png" alt-text="Screenshot: Prozess zum Auswählen von Azure-Datenträgern als Datenschutztyp." lightbox="./media/backup-managed-disks/select-azure-disks-as-datasource-type-expanded.png":::

1. Wählen Sie einen Azure Backup-Tresor aus, und klicken Sie auf **Weiter**, um fortzufahren.

   >[!Note]
   >- Stellen Sie sicher, dass sich der Sicherungstresor und der zu sichernde Datenträger am gleichen Speicherort befinden.
   >- Azure Backup verwendet [_inkrementelle Momentaufnahmen_](/azure/virtual-machines/disks-incremental-snapshots#restrictions) von verwalteten Datenträgern, auf denen nur die Deltaänderungen auf dem Datenträger seit der letzten Momentaufnahme im HDD Standard-Speicher gespeichert werden. Dies ist unabhängig vom Speichertyp des übergeordneten Datenträgers. Für höhere Zuverlässigkeit werden inkrementelle Momentaufnahmen standardmäßig in „zonenredundantem Speicher (ZRS) gespeichert, wenn die entsprechende Region ZRS unterstützt. Derzeit unterstützt Azure Disk Backup operative Sicherungen von verwalteten Datenträgern, die Sicherungen nicht in den Speicher des Azure Backup-Tresors kopieren. Die Redundanzeinstellung für den Sicherungsspeicher des Azure Backup-Tresors gilt daher nicht für die Wiederherstellungspunkte.

   :::image type="content" source="./media/backup-managed-disks/select-backup-vault-inline.png" alt-text="Screenshot: Prozess zum Auswählen eines Azure Backup-Tresors." lightbox="./media/backup-managed-disks/select-backup-vault-expanded.png":::

1. Wählen Sie auf der Registerkarte **Sicherungsrichtlinie** eine Sicherungsrichtlinie aus.

   :::image type="content" source="./media/backup-managed-disks/choose-backup-policy-inline.png" alt-text="Screenshot: Prozess zum Auswählen einer Sicherungsrichtlinie." lightbox="./media/backup-managed-disks/choose-backup-policy-expanded.png":::

1. Klicken Sie auf der Registerkarte **Datenquellen** auf **+ Hinzufügen/Bearbeiten**, um eine oder mehrere verwaltete Azure-Datenträger auszuwählen, für die Sie die Sicherung konfigurieren möchten.

   :::image type="content" source="./media/backup-managed-disks/choose-azure-managed-disks-inline.png" alt-text="Screenshot: Prozess zum Auswählen von verwalteten Azure-Datenträgern." lightbox="./media/backup-managed-disks/choose-azure-managed-disks-expanded.png":::

   >[!Note]
   >Im Portal können Sie zwar mehrere Datenträger auswählen und die Sicherung konfigurieren, jeder Datenträger ist jedoch eine einzelne Sicherungsinstanz. Derzeit unterstützt Azure Disk Backup nur die Sicherung einzelner Datenträger. Zeitpunktsicherungen von mehreren an einen virtuellen Computer angefügten Datenträgern werden nicht unterstützt.
   >
   >Im Azure-Portal können Sie nur Datenträger innerhalb desselben Abonnements auswählen. Wenn mehrere Datenträger gesichert werden sollen oder sich die Datenträger in verschiedenen Abonnements befinden, können Sie Skripts ([PowerShell](/azure/backup/backup-managed-disks-ps)/[CLI](/azure/backup/backup-managed-disks-cli)) zur Automatisierung verwenden. 
   >
   >In der [Unterstützungsmatrix](/azure/backup/disk-backup-support-matrix) finden Sie weitere Informationen zur regionalen Verfügbarkeit von Azure Disk Backup, zu unterstützten Szenarien und zu Einschränkungen.

1. Wählen Sie die **Ressourcengruppe „Momentaufnahme“** aus, und klicken Sie auf **Überprüfen**, um Voraussetzungsprüfungen zu initiieren.

   Auswählen der Ressourcengruppe zum Speichern und Verwalten von Momentaufnahmen:

   - Wählen Sie nicht dieselbe Ressourcengruppe wie die des Quelldatenträgers aus.
   
   - Als Richtlinie wird empfohlen, eine dedizierte Ressourcengruppe als Momentaufnahmen-Datenspeicher zu erstellen, der vom Azure Backup-Dienst verwendet werden soll. Mit einer dedizierten Ressourcengruppe lassen sich die Zugriffsrechte auf die Ressourcengruppe beschränken, was Sicherheit gewährleistet und eine einfache Verwaltung der Sicherungsdaten ermöglicht.

   - Sie können diese Ressourcengruppe zum Speichern von Momentaufnahmen auf mehreren Datenträgern verwenden, die gesichert werden (oder deren Sicherung geplant ist).

   - Sie können keine inkrementelle Momentaufnahme für einen bestimmten Datenträger außerhalb des Abonnements dieses Datenträgers erstellen. Wählen Sie daher eine Ressourcengruppe im selben Abonnement wie der zu sichernde Datenträger aus. [Erfahren Sie mehr](/azure/virtual-machines/disks-incremental-snapshots#restrictions) über inkrementelle Momentaufnahmen für verwaltete Datenträger.

   - Beim Konfigurieren der Sicherung eines Datenträgers können Sie die Ressourcengruppe für Momentaufnahmen, die einer Sicherungsinstanz zugewiesen ist, nicht ändern.

   - Azure Backup erstellt während eines Sicherungsvorgangs ein Speicherkonto in der Ressourcengruppe „Momentaufnahme“. Pro Ressourcengruppe für Momentaufnahmen wird nur ein Speicherkonto erstellt. Das Konto wird auf mehreren Datenträgersicherungsinstanzen wiederverwendet, die dieselbe Ressourcengruppe wie die Ressourcengruppe für Momentaufnahmen verwenden.

     - Im Speicherkonto werden die Momentaufnahmen nicht gespeichert. Die inkrementellen Momentaufnahmen des verwalteten Datenträgers sind ARM-Ressourcen, die in der Ressourcengruppe und nicht in einem Speicherkonto erstellt werden.
     - Das Speicherkonto speichert die Metadaten für jeden Wiederherstellungspunkt. Azure Backup Dienst erstellt einen Blobcontainer pro Datenträgersicherungsinstanz. Für jeden Wiederherstellungspunkt wird ein Blockblob erstellt, um Metadaten zu speichern, die den Wiederherstellungspunkt beschreiben (z. B. Abonnement, Datenträger-ID, Datenträgerattribute usw.) und kaum Speicherplatz (wenige KiBs) belegen.
     - Das Speicherkonto wird als RA-GZRS erstellt, wenn die Region Zonenredundanz unterstützt. Unterstützt die Region keine Zonenredundanz, wird das Speicherkonto als RA-GRS erstellt.
       Wenn eine vorhandene Richtlinie die Erstellung eines Speicherkontos mit GRS-Redundanz für das Abonnement oder die Ressourcengruppe verhindert, wird das Speicherkonto als LRS erstellt. Das erstellte Speicherkonto ist **Universell v2**, wobei Blockblobs auf der heißen Ebene im Blobcontainer gespeichert werden.
     - Die Anzahl der Wiederherstellungspunkte wird durch die Sicherungsrichtlinie bestimmt, die zum Konfigurieren der Sicherung der Datenträgersicherungsinstanz verwendet wird. Gemäß dem Garbage Collection-Prozess werden sie älteren Blockblobs gelöscht, wenn die entsprechenden älteren Wiederherstellungspunkte gelöscht werden.

   - Wenden Sie keine Ressourcensperren, Richtlinien oder Firewall auf vom Azure Backup-Dienst erstellte Ressourcengruppen für Momentaufnahmen oder Speicherkonten an. Der Dienst erstellt und verwaltet Ressourcen in dieser Ressourcengruppe „Momentaufnahme“, die einer Sicherungsinstanz zugewiesen ist, wenn Sie eine Datenträgersicherung konfigurieren. Der Dienst erstellt das Speicherkonto und seine Ressourcen, und diese sollten nicht gelöscht oder verschoben werden.

     >[!Note]
     >Wenn ein Speicherkonto gelöscht wird, schlagen Sicherungen fehl, und die Wiederherstellung aller vorhandenen Wiederherstellungspunkte schlägt fehl.

   :::image type="content" source="./media/backup-managed-disks/validate-snapshot-resource-group-inline.png" alt-text="Screenshot: Prozess zum Initiieren von Voraussetzungsprüfungen." lightbox="./media/backup-managed-disks/validate-snapshot-resource-group-expanded.png":::

1. Überprüfen Sie nach Abschluss der Überprüfung, ob in der Spalte „Sicherungsbereitschaft“ Fehler gemeldet werden.

   >[!Note]
   >Die Überprüfung kann einige Minuten in Anspruch nehmen. Die Überprüfung kann fehlschlagen, wenn:
   >
   >- Ein Datenträger wird nicht unterstützt. Informationen zu nicht unterstützten Szenarien finden Sie in der [Unterstützungsmatrix](/azure/backup/disk-backup-support-matrix).
   >- Die verwaltete Identität des Azure Backup-Tresors auf dem _Datenträger_, der gesichert werden soll, oder in der _Ressourcengruppe für Momentaufnahmen_, in die inkrementellen Momentaufnahmen gespeichert werden, keine gültigen Rollenzuweisungen aufweist.

   Wenn in der Spalte **Sicherungsbereitschaft** die Fehlermeldung _Rollenzuweisung nicht durchgeführt_ angezeigt wird, benötigt die verwaltete Identität des Azure Backup-Tresors Rollenberechtigungen für die ausgewählten Datenträger und/oder für die Ressourcengruppe „Momentaufnahme“. 

   :::image type="content" source="./media/backup-managed-disks/role-assignment-not-done-error-inline.png" alt-text="Screenshot: Fehlermeldung „Rollenzuweisung nicht durchgeführt“." lightbox="./media/backup-managed-disks/role-assignment-not-done-error-expanded.png":::

   Die folgenden Voraussetzungen müssen erfüllt sein, um das Sichern von verwalteten Datenträgern zu konfigurieren:

   >[!Note]
   >Der Sicherungstresor verwendet die verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Zum Konfigurieren einer Sicherung verwalteter Datenträger benötigt die verwaltete Identität des Azure Backup-Tresors einen Satz von Berechtigungen für die Quelldatenträger und Ressourcengruppen, in denen Momentaufnahmen erstellt und verwaltet werden.

   Eine systemseitig zugewiesene verwaltete Identität ist auf eine Ressource beschränkt und an den Lebenszyklus dieser Ressource gebunden. Um der verwalteten Identität Berechtigungen zu erteilen, verwenden Sie die rollenbasierten Zugriffssteuerung von Azure (Azure RBAC). Eine verwaltete Identität ist ein spezieller Dienstprinzipal, der nur zusammen mit Azure-Ressourcen verwendet werden kann. Informieren Sie sich ausführlicher über [verwaltete Identitäten](/azure/active-directory/managed-identities-azure-resources/overview).

   - Weisen Sie der verwalteten Identität des Azure Backup-Tresors auf dem Quelldatenträger, der gesichert werden muss, die Rolle **Leser für die Datenträgersicherung** zu.
   - Weisen Sie der verwalteten Identität des Sicherungstresors in der Ressourcengruppe, in der Sicherungen vom Azure Backup-Dienst erstellt und verwaltet werden, die Rolle Mitwirkender für die Datenträgermomentaufnahme zu. Die Datenträgermomentaufnahmen werden in einer Ressourcengruppe innerhalb Ihres Abonnements gespeichert. Damit der Azure Backup-Dienst Momentaufnahmen erstellen, speichern und verwalten kann, müssen Sie Berechtigungen für den Sicherungstresor bereitstellen.

   >[!Note]
   >Der Flow „Sicherung konfigurieren“ im Azure-Portal hilft Ihnen beim Erteilen der erforderlichen Rollenberechtigungen für die oben genannten Ressourcen. 

1. Aktivieren Sie das Kontrollkästchen neben jeder Zeile mit dem Fehlermeldungstatus _Rollenzuweisung nicht durchgeführt_ in der Spalte „Sicherungsbereitschaft“, und klicken Sie auf **Fehlende Rollen hinzufügen**, um automatisch die erforderlichen Rollenberechtigungen für die verwaltete Identität des Azure Backup-Tresors für ausgewählte Ressourcen zu erteilen.

   :::image type="content" source="./media/backup-managed-disks/add-missing-roles-inline.png" alt-text="Screenshot: Prozess zum Hinzufügen fehlender Rollen." lightbox="./media/backup-managed-disks/add-missing-roles-expanded.png":::

1. Klicken Sie auf **Bestätigen**, um ihre Zustimmung zu erteilen. Azure Backup gibt Änderungen an Rollenzuweisungen automatisch in Ihrem Namen weiter und versucht, diese erneut zu validieren.

   Wenn Sie die Berechtigung für die verwaltete Identität des Azure Backup-Tresors für die ausgewählten Datenträger und die Momentaufnahme-Ressourcengruppen erteilen möchten, wählen Sie in der Dropdownliste **Bereich** die Option **Ressource** aus. 

   :::image type="content" source="./media/backup-managed-disks/confirm-role-propagation-inline.png" alt-text="Screenshot: Option zum Erteilen der Zustimmung für die automatische Rollenzuweisung an Azure Backup." lightbox="./media/backup-managed-disks/confirm-role-propagation-expanded.png":::

   >[!Tip]
   >Wenn Sie planen, in Zukunft die Sicherung für andere Datenträger in der gleichen Ressourcengruppe bzw. demselben Abonnement zu konfigurieren, können Sie die Berechtigung auf der Ebene der Ressourcengruppe oder des Abonnements erteilen.

   :::image type="content" source="./media/backup-managed-disks/permission-deployment-in-progress-inline.png" alt-text="Screenshot: Bereitstellung der Berechtigung." lightbox="./media/backup-managed-disks/permission-deployment-in-progress-expanded.png":::

   :::image type="content" source="./media/backup-managed-disks/permission-waiting-to-propagate-inline.png" alt-text="Screenshot: Berechtigung, die auf die Weitergabe an die Ressourcengruppe wartet." lightbox="./media/backup-managed-disks/permission-waiting-to-propagate-expanded.png":::

   :::image type="content" source="./media/backup-managed-disks/revalidating-permission-propagation-inline.png" alt-text="Screenshot: Versuche, die Berechtigungsweitergabe bei fehlgeschlagenen Szenarien erneut zu validieren." lightbox="./media/backup-managed-disks/revalidating-permission-propagation-expanded.png":::

   >[!Note]
   >- In einigen Fällen kann es bis zu 30 Minuten dauern, bis die Rollenzuweisungen weitergegeben werden, was zu einem Fehler bei der erneuten Validierung führt. Wiederholen Sie in diesem Szenario den Vorgang nach einiger Zeit.
   >- Wenn die Aktion **Fehlende Rollen hinzufügen** keine Berechtigungen zuweisen kann und in der Spalte „Sicherungsbereitschaft“ der Fehler „Unzureichende Berechtigung für die Rollenzuweisung“ angezeigt wird, bedeutet dies, dass Sie nicht über die Berechtigung zum Zuweisen von Rollenberechtigungen verfügen. Wählen Sie „Rollenzuweisungsvorlage herunterladen“ aus, um Rollenzuweisungen als Skripts herunterzuladen, und wenden Sie sich an Ihren IT-Administrator, um die Skripts auszuführen, um die Voraussetzungen zu erfüllen. 

   :::image type="content" source="./media/backup-managed-disks/permission-propagation-taking-long-time-inline.png" alt-text="Screenshot: Berechtigungsweitergabe-Instanzen dauert länger (bis zu 30 Sekunden)." lightbox="./media/backup-managed-disks/permission-propagation-taking-long-time-expanded.png":::

1. Klicken Sie nach erfolgreicher Überprüfung auf **Weiter**, um zur Registerkarte **Überprüfen und konfigurieren** zu gelangen, und klicken Sie dann auf **Sicherung konfigurieren**, um die Sicherung der ausgewählten Datenträger zu konfigurieren.

   :::image type="content" source="./media/backup-managed-disks/configure-backup-of-selected-disks-inline.png" alt-text="Screenshot: Prozess zum Konfigurieren der Sicherung ausgewählter Datenträger." lightbox="./media/backup-managed-disks/configure-backup-of-selected-disks-expanded.png":::

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