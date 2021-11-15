---
title: Sichern mehrerer SQL Server-VMs über den Tresor
description: In diesem Artikel erfahren Sie, wie Sie SQL Server-Datenbanken auf virtuellen Azure-Maschinen mit Azure Backup aus dem Recovery Services Tresor sichern können
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 970c352bcb7f04d04cddaaa24769eb4a26896605
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431795"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Sichern mehrerer SQL Server-VMs aus dem Recovery Services-Depot

SQL Server-Datenbanken sind kritische Workloads, die eine niedrige Recovery Point Objective (RPO) und lange Aufbewahrungszeit erfordern. Sie können auf virtuellen Azure-Computern (VMs) ausgeführte SQL Server-Datenbanken mithilfe von [Azure Backup](backup-overview.md) sichern.

In diesem Artikel wird veranschaulicht, wie eine SQL Server-Datenbank, die auf einer Azure-VM ausgeführt wird, in einem Azure Backup Recovery Services-Tresor gesichert wird.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen und Konfigurieren eines Tresors
> * Ermitteln von Datenbanken und Einrichten von Sicherungen
> * Einrichten des automatischen Schutzes für Datenbanken

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie eine SQL Server-Datenbank sichern können, müssen folgende Kriterien erfüllt sein:

1. Bestimmen oder erstellen Sie einen [Recovery Services-Tresor](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) in derselben Region und demselben Abonnement wie die VM, die die SQL Server-Instanz hostet.
1. Stellen Sie sicher, dass der virtuelle Computer über [Netzwerkkonnektivität](backup-sql-server-database-azure-vms.md#establish-network-connectivity) verfügt.
1. Stellen Sie sicher, dass der [Azure-VM-Agent](../virtual-machines/extensions/agent-windows.md) auf der VM installiert ist.
1. Stellen Sie sicher, dass .NET 4.5.2 oder höher auf der VM installiert ist.
1. Vergewissern Sie sich, dass die SQL Server-Datenbanken die [Benennungsrichtlinien für Datenbanken für Azure Backup](#database-naming-guidelines-for-azure-backup) befolgen.
1. Der Name des virtuellen SQL Server-Computers und der Ressourcengruppenname dürfen bei virtuellen Computer (Azure Resource Manager) zusammen maximal 84 Zeichen lang sein (bzw. 77 Zeichen bei klassischen virtuellen Computern). Diese Einschränkung ist darauf zurückzuführen, dass einige Zeichen vom Dienst reserviert werden.
1. Stellen Sie sicher, dass keine anderen Sicherungslösungen für die Datenbank aktiviert sind. Deaktivieren Sie alle anderen SQL Server-Sicherungen, bevor Sie die Datenbank sichern.
1. Wenn Sie SQL Server 2008 R2 oder SQL Server 2012 verwenden, kann es vorkommen, dass das Zeit Zonen Problem für die Sicherung wie [hier](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of) beschrieben auftritt. Stellen Sie sicher, dass Sie über die aktuellen kumulativen Updates verfügen, um das oben beschriebene zeitzonenbezogene Problem zu vermeiden. Wenn das Anwenden der Updates auf die SQL Server Instanz auf der Azure-VM nicht möglich ist, deaktivieren Sie die Sommerzeit (DST) für die Zeitzone auf dem virtuellen Computer.

> [!NOTE]
> Sie können Azure Backup für eine Azure-VM und auch für eine auf der VM ausgeführte SQL Server-Datenbank ohne Konflikte aktivieren.

### <a name="establish-network-connectivity"></a>Herstellen der Netzwerkverbindung

Für alle Operationen benötigt ein virtueller SQL Server-Computer die Verbindung zum Azure Backup-Dienst, zu Azure Storage und zu Azure Active Directory. Dies kann durch die Verwendung privater Endpunkte oder durch die Gewährung des Zugriffs auf die erforderlichen öffentlichen IP-Adressen oder FQDNs erreicht werden. Wenn keine ordnungsgemäße Verbindung zu den erforderlichen Azure-Diensten zugelassen wird, kann dies zu Fehlern bei Operationen wie der Datenbankermittlung, der Konfiguration von Sicherungen, der Durchführung von Sicherungen und der Wiederherstellung von Daten führen.

In der folgenden Tabelle sind die verschiedenen Alternativen zum Herstellen der Konnektivität aufgeführt:

| **Option**                        | **Vorteile**                                               | **Nachteile**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Private Endpunkte                 | Sicherungen über private IPs innerhalb des virtuellen Netzwerks möglich  <br><br>   Genau abgestimmte Zugriffssteuerung auf Netzwerk- und Tresorseite | Standardmäßige [Kosten](https://azure.microsoft.com/pricing/details/private-link/) für private Endpunkte |
| NSG-Diensttags                  | Einfachere Verwaltung, da Bereichsänderungen automatisch zusammengeführt werden   <br><br>   Keine zusätzlichen Kosten | Nur mit NSGs möglich  <br><br>    Zugriff auf den gesamten Dienst |
| FQDN-Tags von Azure Firewall          | Einfachere Verwaltung, da die erforderlichen FQDNs automatisch verwaltet werden | Nur mit Azure Firewall möglich                         |
| Zugriff auf Dienst-FQDNs/-IPs | Keine zusätzlichen Kosten   <br><br>  Verwendung mit allen Netzwerksicherheits-Appliances und Firewalls möglich | Möglicherweise ist ein umfassender Satz von IP-Adressen oder FQDNs erforderlich.   |
| Verwenden eines HTTP-Proxys                 | Zentraler Internetzugriffspunkt für VMs                       | Zusätzliche Kosten für das Ausführen einer VM mit der Proxysoftware         |

Weitere Informationen zur Verwendung dieser Optionen finden Sie unten:

#### <a name="private-endpoints"></a>Private Endpunkte

Private Endpunkte ermöglichen Ihnen, sichere Verbindungen von Servern in einem virtuellen Netzwerk mit Ihrem Recovery Services-Tresor herzustellen. Der private Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihren Tresor. Der Netzwerkdatenverkehr zwischen Ihren Ressourcen innerhalb des virtuellen Netzwerks und dem Tresor wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbone-Netzwerk übertragen. Dadurch wird er vom öffentlichen Internet isoliert. Weitere Informationen zu privaten Endpunkten für Azure Backup finden Sie [hier](./private-endpoints.md).

#### <a name="nsg-tags"></a>NSG-Tags

Wenn Sie Netzwerksicherheitsgruppen (NSG) verwenden, können Sie den ausgehenden Zugriff auf Azure Backup mithilfe des Diensttags *AzureBackup* zulassen. Zusätzlich zum Tag „Azure Backup“ müssen Sie auch Konnektivität für Authentifizierung und Datenübertragung zulassen, indem Sie ähnliche [NSG-Regeln](../virtual-network/network-security-groups-overview.md#service-tags) für Azure AD (*AzureActiveDirectory*) und Azure Storage (*Storage*) erstellen.  Die folgenden Schritte beschreiben das Vorgehen zum Erstellen einer Regel für das Azure Backup-Tag:

1. Navigieren Sie unter **Alle Dienste** zu **Netzwerksicherheitsgruppen**, und wählen Sie die Netzwerksicherheitsgruppe aus.

1. Wählen Sie unter **Einstellungen** die Option **Ausgangssicherheitsregeln** aus.

1. Wählen Sie **Hinzufügen**. Geben Sie die erforderlichen Informationen zum Erstellen einer neuen Regel ein, wie unter [Einstellungen zu Sicherheitsregeln](../virtual-network/manage-network-security-group.md#security-rule-settings) beschrieben. Stellen Sie sicher, dass die Option **Ziel** auf *Diensttag* und **Zieldiensttag** auf *AzureBackup* festgelegt wurde.

1. Wählen Sie **Hinzufügen** aus, um die neu erstellte Ausgangssicherheitsregel zu speichern.

Auf ähnliche Weise können Sie ausgehende NSG-Sicherheitsregeln für Azure Storage und Azure AD erstellen.

#### <a name="azure-firewall-tags"></a>Azure Firewall-Tags

Wenn Sie Azure Firewall verwenden, erstellen Sie eine Anwendungsregel mithilfe des [Azure Firewall-FQDN-Tags](../firewall/fqdn-tags.md) *AzureBackup*. Dies erlaubt sämtlichen ausgehenden Zugriff auf Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Zulassen des Zugriffs auf Dienst-IP-Bereiche

Wenn Sie sich dafür entscheiden, Zugriffsdienst-IPs zuzulassen, beziehen Sie sich auf die IP-Bereiche in der [hier](https://www.microsoft.com/download/confirmation.aspx?id=56519) verfügbaren JSON-Datei. Sie müssen den Zugriff auf IPs für Azure Backup, Azure Storage und Azure Active Directory zulassen.

#### <a name="allow-access-to-service-fqdns"></a>Zugriff auf Dienst-FQDNs

Sie können auch die folgenden FQDNs verwenden, um den Zugriff auf die erforderlichen Dienste von ihren Servern aus zuzulassen:

| Dienst    | Domänennamen, auf die zugegriffen werden soll                             | Ports
| -------------- | ------------------------------------------------------------ | ---
| Azure Backup  | `*.backup.windowsazure.com`                             | 443
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` <br><br> `*.blob.storage.azure.net` | 443
| Azure AD      | Gewähren des Zugriffs auf FQDNs gemäß Abschnitt 56 und 59, wie in [diesem Artikel](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) beschrieben | Wie anwendbar

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Verwenden eines HTTP-Proxyservers für das Weiterleiten von Datenverkehr

Wenn Sie eine SQL Server-Datenbank auf einem virtuellen Azure-Computer sichern, verwendet die Sicherungserweiterung auf dem virtuellen Computer die HTTPS-APIs, um Verwaltungsbefehle an Azure Backup und Daten an Azure Storage zu senden. Die Sicherungserweiterung verwendet auch Azure AD zur Authentifizierung. Leiten Sie den Datenverkehr der Sicherungserweiterung für diese drei Dienste über den HTTP-Proxy weiter. Verwenden Sie die oben genannte Liste der IPs und FQDNs, um den Zugriff auf die erforderlichen Dienste zuzulassen. Authentifizierte Proxyserver werden nicht unterstützt.

### <a name="database-naming-guidelines-for-azure-backup"></a>Benennungsrichtlinien für Datenbanken in Azure Backup

- Verwenden Sie in Datenbanknamen nicht die folgenden Elemente:

  - Nachstehende und führende Leerzeichen
  - Nachstehende Ausrufezeichen (!)
  - Schließende eckige Klammern (])
  - Semikolon (;)
  - Schrägstrich (/)

- Obwohl Aliase für nicht unterstützte Zeichen möglich ist, wird empfohlen, sie zu vermeiden. Weitere Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](/rest/api/storageservices/understanding-the-table-service-data-model).

- Mehrere Datenbanken mit unterschiedlicher Groß-/Kleinschreibung auf derselben SQL-Instanz werden nicht unterstützt.

-   Das Ändern der Groß-/Kleinschreibung einer SQL-Datenbank wird nach dem Konfigurieren des Schutzes nicht unterstützt.

>[!NOTE]
>Der Vorgang **Schutz konfigurieren** wird für Datenbanken mit Sonderzeichen wie „+“ oder „&“ im Namen nicht unterstützt. Sie können den Datenbanknamen ändern oder den **automatischen Schutz** aktivieren, damit diese Datenbanken erfolgreich geschützt werden können.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Ermitteln von SQL Server-Datenbanken

Ermitteln von auf einer VM ausgeführten Datenbanken:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu **Backup Center**, und klicken Sie auf **+ Sicherung**.

1. Wählen Sie **SQL in Azure-VM** als Datenquellentyp und anschließend den von Ihnen erstellten Recovery Services-Tresor aus, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/backup-azure-sql-database/configure-sql-backup.png" alt-text="Screenshot der Auswahl von „Sicherung“ zum Anzeigen der auf einer VM ausgeführten Datenbanken":::

1. Wählen Sie in **Sicherungsziel** > **DBs in VMs ermitteln** die Option **Ermittlung starten** aus, um nach nicht geschützten virtuellen Computern im Abonnement zu suchen. Diese Suche kann je nach Anzahl ungeschützter VMs im Abonnement eine Weile dauern.

   * Nicht geschützte virtuelle Computer sollten nach der Ermittlung sortiert nach Name und Ressourcengruppe in der Liste angezeigt werden.
   * Wenn eine VM nicht wie erwartet aufgeführt wird, prüfen Sie, ob sie bereits in einem Tresor gesichert wird.
   * Mehrere VMs können den gleichen Namen aufweisen, gehören dann aber verschiedenen Ressourcengruppen an.

     ![Ausstehende Sicherung bei der Suche nach Datenbanken in VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

1. Wählen Sie in der Liste der virtuellen Computer den virtuellen Computer mit der SQL Server-Datenbank und dann **Datenbankermittlung** aus.

1. Verfolgen Sie die Datenbankermittlung im Bereich **Benachrichtigungen**. Der Zeitaufwand für diese Aktion hängt von der Anzahl von Datenbanken auf VMs ab. Wenn die ausgewählten Datenbanken ermittelt wurden, wird eine Erfolgsmeldung angezeigt.

    ![Meldung über erfolgreiche Bereitstellung](./media/backup-azure-sql-database/notifications-db-discovered.png)

1. Azure Backup ermittelt alle SQL Server-Datenbanken auf dem virtuellen Computer. Während der Ermittlung geschieht im Hintergrund Folgendes:

    * Azure Backup registriert die VM beim Tresor für die Sicherung der Workload. Alle Datenbanken auf der registrierten VM können nur in diesem Tresor gesichert werden.
    * Azure Backup installiert die Erweiterung AzureBackupWindowsWorkload auf der VM. In einer SQL Server-Datenbank ist kein Agent installiert.
    * Azure Backup erstellt das Dienstkonto „NT Service\AzureWLBackupPluginSvc“ auf der VM.
      * Für alle Sicherungs- und Wiederherstellungsvorgänge wird das Dienstkonto verwendet.
      * „NT Service\AzureWLBackupPluginSvc erfordert“ Systemadministratorberechtigungen für SQL Server. Die Erweiterung SqlIaaSExtension ist auf allen in Azure Marketplace erstellten SQL Server-VMs vorinstalliert. Die Erweiterung AzureBackupWindowsWorkload verwendet SQLIaaSExtension, um erforderliche Berechtigungen automatisch abzurufen.
    * Wenn Sie die VM nicht im Marketplace erstellt haben oder SQL Server 2008 und 2008 R2 verwenden, ist die Erweiterung „SqlIaaSExtension“ möglicherweise nicht auf der VM installiert, und der Ermittlungsvorgang schlägt mit der Fehlermeldung „UserErrorSQLNoSysAdminMembership“ fehl. Führen Sie die Schritte unter [Set VM permissions (Einrichten von VM-Berechtigungen)](backup-azure-sql-database.md#set-vm-permissions) aus, um dieses Problem zu beheben.

        ![Auswählen der VM und der Datenbank](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurieren der Sicherung  

1. In **Sicherungsziel** > **Schritt 2: Sicherung konfigurieren** wählen Sie **Sicherung konfigurieren** aus.

   ![Auswählen von „Sicherung konfigurieren“](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. Wählen Sie **Ressourcen hinzufügen**, um alle registrierten Verfügbarkeitsgruppen und eigenständigen SQL Server-Instanzen anzuzeigen.

    ![Auswählen von „Ressourcen hinzufügen“](./media/backup-azure-sql-database/add-resources.png)

1. Wählen Sie auf dem Bildschirm **Elemente für die Sicherung auswählen** den Pfeil links neben einer Zeile aus, um die Liste mit allen ungeschützten Datenbanken der Instanz bzw. Always On-Verfügbarkeitsgruppe zu erweitern.

    ![Auswählen von „Elemente für die Sicherung auswählen“](./media/backup-azure-sql-database/select-items-to-backup.png)

1. Wählen Sie alle Datenbanken, die Sie schützen möchten, und klicken Sie dann auf **OK**.

   ![Schützen der Datenbank](./media/backup-azure-sql-database/select-database-to-protect.png)

   Zum Optimieren von Sicherungslasten legt Azure Backup die maximale Anzahl von Datenbanken in einem Sicherungsauftrag auf 50 fest.

     * Zum Schützen von mehr als 50 Datenbanken konfigurieren Sie mehrere Sicherungen.
     * Zum [Aktivieren](#enable-auto-protection) der gesamten Instanz oder Always On-Verfügbarkeitsgruppe wählen Sie in der Dropdownliste **AUTOMATISCHER SCHUTZ** die Option **EIN** aus und klicken dann auf **OK**.

         > [!NOTE]
         > Das Feature [Automatischer Schutz](#enable-auto-protection) ermöglicht nicht nur den gleichzeitigen Schutz aller vorhandenen Datenbanken, sondern schützt auch automatisch alle neuen Datenbanken, die dieser Instanz oder Verfügbarkeitsgruppe hinzugefügt werden.  

1. Definieren Sie die **Sicherungsrichtlinie**. Führen Sie einen der folgenden Schritte aus:

   * Wählen Sie als Standardrichtlinie *HourlyLogBackup* aus.
   * Auswählen einer vorhandenen, zuvor für SQL erstellten Sicherungsrichtlinie
   * Definieren einer neuen Richtlinie basierend auf Ihrer RPO und Ihrer Beibehaltungsdauer

     ![Auswählen der Sicherungsrichtlinie](./media/backup-azure-sql-database/select-backup-policy.png)

1. Wählen Sie **Sicherung aktivieren**, um den Vorgang **Schutz konfigurieren** zu übermitteln, und verfolgen Sie den Konfigurationsstatus im Bereich **Benachrichtigungen** des Portals nach.

   ![Nachverfolgen des Konfigurationsstatus](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Eine Sicherungsrichtlinie legt fest, wann Sicherungen erstellt und wie lange sie aufbewahrt werden.

* Eine Richtlinie wird auf Tresorebene erstellt.
* Mehrere Tresore können die gleiche Sicherungsrichtlinie verwenden. Allerdings müssen Sie die Sicherungsrichtlinie auf jeden Tresor anwenden.
* Wenn Sie eine Sicherungsrichtlinie erstellen, entspricht eine tägliche vollständige Sicherung der Standardeinstellung.
* Sie können eine differenzielle Sicherung nur hinzufügen, wenn Sie für die vollständige Sicherung festlegen, dass diese wöchentlich erfolgt.
* Erfahren Sie mehr über [verschiedene Arten von Sicherungsrichtlinien](backup-architecture.md#sql-server-backup-types).

So erstellen Sie eine Sicherungsrichtlinie

1. Wechseln Sie zum **Backup Center**, und klicken Sie auf **+ Richtlinie**.

1. Wählen Sie **SQL Server in Azure-VM** als Datenquellentyp und anschließend den Tresor aus, unter dem die Richtlinie erstellt werden soll, und klicken Sie dann auf **Weiter**.

   :::image type="content" source="./media/backup-azure-sql-database/create-sql-policy.png" alt-text="Screenshot der Auswahl eines Richtlinientyps für die neue Sicherungsrichtlinie":::

1. Geben Sie unter **Richtlinienname** einen Namen für die neue Richtlinie ein.

   :::image type="content" source="./media/backup-azure-sql-database/sql-policy-summary.png" alt-text="Screenshot der Eingabe des Richtliniennamens":::

1. Wählen Sie den Link **Bearbeiten** für **Vollständige Sicherung**, um die Standardeinstellungen zu ändern.

   * Wählen Sie eine **Sicherungshäufigkeit** aus. Wählen Sie entweder **Täglich** oder **Wöchentlich**.
   * Wählen Sie für **Täglich** die Uhrzeit und die Zeitzone für den Beginn des Sicherungsauftrags aus. Sie können keine differenziellen Sicherungen für tägliche vollständige Sicherungen erstellen.

   :::image type="content" source="./media/backup-azure-sql-database/sql-backup-schedule-inline.png" alt-text="Screenshot der Felder für neue Sicherungsrichtlinien" lightbox="./media/backup-azure-sql-database/sql-backup-schedule-expanded.png":::

1. In **BEIBEHALTUNGSDAUER** sind standardmäßig alle Optionen aktiviert. Deaktivieren Sie alle Optionen für die Beibehaltungsdauer, die Sie nicht wünschen, und legen Sie dann die zu verwendenden Intervalle fest.

    * Die Mindestbeibehaltungsdauer beträgt für alle Sicherungstypen (vollständig/differenziell/Protokoll) sieben Tage.
    * Wiederherstellungspunkte werden unter Berücksichtigung ihrer Beibehaltungsdauer mit einer Markierung versehen. Wenn Sie beispielsweise eine tägliche vollständige Sicherung wählen, wird pro Tag nur eine vollständige Sicherung ausgelöst.
    * Die Sicherung für einen bestimmten Tag wird auf Grundlage der wöchentlichen Beibehaltungsdauer und der wöchentlichen Beibehaltungseinstellung markiert und beibehalten.
    * Mit der monatlichen und jährlichen Beibehaltungsdauer verhält es sich ähnlich.

    :::image type="content" source="./media/backup-azure-sql-database/sql-retention-range-inline.png" alt-text="Screenshot der Einstellungen für den Aufbewahrungszeitraum" lightbox="./media/backup-azure-sql-database/sql-retention-range-expanded.png":::

1. Wählen Sie **OK** aus, um die Einstellung für vollständige Sicherungen zu akzeptieren.
1. Wählen Sie den Link **Bearbeiten** für **Differenzielle Sicherung**, um die Standardeinstellungen zu ändern.

    * Wählen Sie in **Richtlinie für differenzielle Sicherung** die Option **Aktivieren** aus, um die Einstellungen für Häufigkeit und Beibehaltung vorzunehmen.
    * Pro Tag können Sie nur eine differenzielle Sicherung auslösen. Eine differenzielle Sicherung kann nicht am selben Tag wie eine vollständige Sicherung ausgelöst werden.
    * Differenzielle Sicherungen können maximal 180 Tage aufbewahrt werden.
    * Der Aufbewahrungszeitraum für differenzielle Sicherungen darf nicht größer als der für die vollständige Sicherung sein (da die differenziellen Sicherungen für die Wiederherstellung von den vollständigen Sicherungen abhängig sind).
    * Die differenzielle Sicherung wird für die Masterdatenbank nicht unterstützt.

    :::image type="content" source="./media/backup-azure-sql-database/sql-differential-backup-inline.png" alt-text="Screenshot der Richtlinie für differenzielle Sicherungen" lightbox="./media/backup-azure-sql-database/sql-differential-backup-expanded.png":::

1. Wählen Sie den Link **Bearbeiten** für **Protokollsicherung**, um die Standardeinstellungen zu ändern.

    * Wählen Sie in **Protokollsicherung** die Option **Aktivieren** aus, und legen Sie die Einstellungen für Häufigkeit und Aufbewahrung fest.
    * Transaktionsprotokollsicherungen können alle 15 Minuten erfolgen und bis zu 35 Tage aufbewahrt werden.
    * Wenn für die Datenbank das [einfache Wiederherstellungsmodell](/sql/relational-databases/backup-restore/recovery-models-sql-server) verwendet wird, wird der Zeitplan der Protokollsicherung für diese Datenbank angehalten, sodass keine Protokollsicherungen ausgelöst werden.
    * Wenn sich das Wiederherstellungsmodell der Datenbank von **Vollständig** in **Einfach** ändert, wird die Durchführung von Protokollsicherungen innerhalb von 24 Stunden nach der Änderung des Wiederherstellungsmodells angehalten. Analog hierzu gilt Folgendes: Wenn das Wiederherstellungsmodell von **Einfach** in einen anderen Zustand geändert wird und Protokollsicherungen für die Datenbank unterstützt werden, werden die Zeitpläne für die Protokollsicherungen entsprechend innerhalb von 24 Stunden nach der Änderung des Wiederherstellungsmodells aktiviert.

    :::image type="content" source="./media/backup-azure-sql-database/sql-log-backup-inline.png" alt-text="Screenshot der Richtlinie für Protokollsicherungen" lightbox="./media/backup-azure-sql-database/sql-log-backup-expanded.png":::

1. Legen Sie im Menü **Sicherungsrichtlinie** fest, ob die **SQL-Sicherungskomprimierung** aktiviert wird. Diese Option ist standardmäßig deaktiviert. Wenn sie aktiviert ist, sendet SQL Server einen komprimierten Sicherungsdatenstrom an die VDI. Von Azure Backup werden je nach Wert dieses Steuerelements die Standardwerte für Instanzebenen mit der Klausel „COMPRESSION/NO_COMPRESSION“ überschrieben.

1. Nachdem Sie die Sicherungsrichtlinie bearbeitet haben, wählen Sie **OK** aus.

> [!NOTE]
> Jede Protokollsicherung wird mit der vorherigen vollständigen Sicherung zu einer Wiederherstellungskette verkettet. Diese vollständige Sicherung wird aufbewahrt, bis die Aufbewahrungsdauer der letzten Protokollsicherung abgelaufen ist. Das kann bedeuten, dass die vollständige Sicherung für einen zusätzlichen Zeitraum aufbewahrt wird, um sicherzustellen, dass alle Protokolle wiederhergestellt werden können. Nehmen wir an, Sie haben eine wöchentliche Vollsicherung, eine tägliche differenzielle Sicherung und 2-Stunden-Protokolle. Alle werden 30 Tage lang aufbewahrt. Die wöchentliche vollständige Sicherung kann aber erst vollständig bereinigt/gelöscht werden, wenn die nächste vollständige Sicherung verfügbar ist, also nach 30 + 7 Tagen. Angenommen, am 16. November erfolgt eine wöchentliche vollständige Sicherung. Gemäß der Aufbewahrungsrichtlinie wird diese bis zum 16. Dezember aufbewahrt. Die letzte Protokollsicherung hierfür erfolgt vor der nächsten geplanten vollständigen Sicherung, und zwar am 22. November. Solange dieses Protokoll bis zum 22. Dezember verfügbar ist, kann die vollständige Sicherung vom 16. November nicht gelöscht werden. Die vollständige Sicherung vom 16. November wird also bis zum 22. Dezember aufbewahrt.

## <a name="enable-auto-protection"></a>Aktivieren des automatischen Schutzes  

Sie können den automatischen Schutz aktivieren, um automatisch alle vorhandenen und künftigen Datenbanken in einer eigenständigen SQL Server-Instanz oder Always On-Verfügbarkeitsgruppe zu sichern.

* Für den automatischen Schutz können gleichzeitig beliebig viele Datenbanken ausgewählt werden. Die Ermittlung wird in der Regel alle acht Stunden ausgeführt. Neue Datenbanken können aber auch sofort erkannt und geschützt werden, indem Sie **Datenbanken neu ermitteln** auswählen, um manuell eine Ermittlung durchzuführen.
* Es ist nicht möglich, beim Aktivieren des automatischen Schutzes Datenbanken selektiv zu schützen oder vom Schutz in einer Instanz auszuschließen.
* Falls Ihre Instanz bereits einige geschützte Datenbanken enthält, sind sie unter den jeweiligen Richtlinien auch dann weiterhin geschützt, wenn Sie den automatischen Schutz deaktivieren. Für alle nicht geschützten Datenbanken, die später hinzugefügt werden, gilt aber nur eine einzelne Richtlinie, die Sie zum Zeitpunkt der Aktivierung des automatischen Schutzes unter **Sicherung konfigurieren** definieren. Sie können die Richtlinie, die einer automatisch geschützten Datenbank zugeordnet ist, später aber ändern.  

So aktivieren Sie den automatischen Schutz

  1. Wählen Sie in **Elemente für Sicherung** die Instanz aus, für die Sie den automatischen Schutz aktivieren möchten.
  2. Wählen Sie in der Dropdownliste unter **AUTOMATISCHER SCHUTZ** die Option **EIN** aus, und klicken Sie dann auf **OK**.

      ![Aktivieren des automatischen Schutzes für die Verfügbarkeitsgruppe](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Die Sicherung wird für alle Datenbanken gemeinsam konfiguriert und kann unter **Sicherungsaufträge** nachverfolgt werden.

Wenn Sie den automatischen Schutz deaktivieren müssen, klicken Sie unter **Sicherung konfigurieren** auf den Namen der Instanz, und wählen Sie dann **Automatischen Schutz deaktivieren** für die Instanz aus. Alle Datenbanken werden weiterhin gesichert, aber zukünftige Datenbanken sind nicht automatisch geschützt.

![Deaktivieren des automatischen Schutzes für diese Instanz](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:

* [Wiederherstellen von gesicherten SQL Server-Datenbanken](restore-sql-database-azure-vm.md)
* [Verwalten von gesicherten SQL Server-Datenbanken](manage-monitor-sql-database-backup.md)
