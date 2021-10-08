---
title: Migrieren Ihrer Blockdaten zu Azure mit Cirrus Data
titleSuffix: Azure Storage
description: Hier finden Sie eine Schnellstartanleitung zum Implementieren von Cirrus Migrate Cloud und Migrieren Ihrer Daten zu Azure.
author: dukicn
ms.author: nikoduki
ms.date: 09/06/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 69ee8dd0f7acf0959ccbfb47ebde01e6d17cfe21
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598554"
---
# <a name="migrate-your-block-data-to-azure-with-cirrus-migrate-cloud"></a>Migrieren Ihrer Blockdaten zu Azure mit Cirrus Migrate Cloud

Cirrus Migrate Cloud (CMC) ermöglicht die Datenträgermigration von einem vorhandenen Speichersystem oder einer Cloud zu Azure. Die Migration wird ausgeführt, während das ursprüngliche System noch in Betrieb ist. In diesem Dokument wird die Vorgehensweise zum erfolgreichen Konfigurieren und Ausführen der Migration beschrieben.

## <a name="overview"></a>Übersicht

Die Lösung verwendet verteilte Migrations-Agents, die auf jedem Host ausgeführt werden und direkte Host-zu-Host-Verbindungen ermöglichen. Jede Host-zu-Host-Migration ist jeweils unabhängig, wodurch die Lösung unendlich skalierbar ist, ohne dass es zu zentralen Engpässen für den Datenfluss kommt. Die Migration erfolgt mithilfe der cMotion™-Technologie, um sicherzustellen, dass es zu keinen Auswirkungen auf die Produktion kommt. 

## <a name="use-cases"></a>Anwendungsfälle

In diesem Dokument wird ein allgemeiner Migrationsfall zum Verschieben der Anwendung von einem virtuellen Computer (lokal oder bei einem anderen Cloudanbieter ausgeführt) auf einen virtuellen Computer in Azure behandelt. Ausführlichere Schritt-für-Schritt-Anleitungen für verschiedene Anwendungsfälle finden Sie unter den folgenden Links:

- [Verschieben der Workload in Azure mit cMotion](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-on-premises-to-azure-1xo3nuf/)
- [Wechsel von Premium Datenträgern zu Ultra-Datenträgern](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-between-azure-tiers-sxhppt/)
- [Wechsel von AWS zu Azure](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-from-aws-to-azure-weegd9/.)

## <a name="components"></a>Komponenten

Cirrus Migrate Cloud besteht aus mehreren Komponenten:

- Das **cMotion™**-Feature von CMC führt eine Umstellung auf Speicherebene von einer Quelle in die Zielcloud durch, ohne dass es zu Ausfallzeiten auf dem Quellhost kommt. Mit cMotion™ wird die Workload vom ursprünglichen FC- oder iSCSI-Quelldatenträger auf den neuen Azure Managed Disk-Zieldatenträger übertragen.
- Das **webbasierte Verwaltungsportal** bietet eine webbasierte Verwaltung als Dienst. Hiermit können Benutzer die Migration verwalten und jeden Blockspeicher schützen. Das webbasierte Verwaltungsportal bietet Schnittstellen für alle CMC-Anwendungskonfigurationen, Verwaltungs- und administrativen Aufgaben.

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-web-portal.jpg" alt-text="Screenshot des CMC-Portals":::

## <a name="implementation-guide"></a>Implementierungsleitfaden

Der Benutzer sollte die bewährten Azure-Methoden zum Implementieren eines neuen virtuellen Computers befolgen. Wenn Sie mit der Vorgehensweise nicht vertraut sind, finden Sie weitere Informationen in der [Schnellstartanleitung](/azure/virtual-machines/windows/quick-create-portal).

Bevor Sie mit der Migration beginnen, sollten Sie sich vergewissern, dass die folgenden Voraussetzungen erfüllt sind:

- Überprüfen Sie, ob das Betriebssystem in Azure ordnungsgemäß lizenziert ist.
- Überprüfen Sie den Zugriff auf den virtuellen Azure-Computer.
- Prüfen Sie, ob die Anwendungs-/Datenbanklizenz für die Ausführung in Azure verfügbar ist.
- Prüfen Sie die Berechtigung zum automatischen Zuordnen der Größe des Zieldatenträgers
- Stellen Sie sicher, dass die Größe des verwalteten Datenträgers dem Quelldatenträger entspricht oder größer ist. 
- Vergewissern Sie sich, dass entweder die Quelle oder der virtuelle Zielcomputer über einen offenen Port verfügt, um die H2H-Verbindung zu ermöglichen.

1. **Bereiten Sie den virtuellen Azure-Computer vor**. In diesem Dokument wird davon ausgegangen, dass der virtuelle Computer vollständig implementiert ist. Sobald die Datenträger migriert wurden, kann der Zielhost die Anwendung sofort starten und online stellen. Der Zustand der Daten entspricht der Quelle, als diese vor wenigen Sekunden heruntergefahren wurde. CMC migriert nicht den Betriebssystemdatenträger von der Quelle zum Ziel.

1. **Bereiten Sie die Anwendung auf dem virtuellen Azure-Computer vor**. In diesem Beispiel ist die Quelle ein Linux-Host. Er kann jede Benutzeranwendung ausführen, die auf den entsprechenden BSD-Speicher zugreift. Es wird eine Datenbankanwendung verwendet, die an der Quelle mit einem 1-GiB-Datenträger als Quellspeichergerät ausgeführt wird. Es kann stattdessen aber auch eine beliebige Anwendung verwendet werden. Richten Sie einen virtuellen Computer in Azure ein, der als virtueller Zielcomputer verwendet werden kann. Stellen Sie sicher, dass Ressourcenkonfiguration und Betriebssystem mit der Anwendung kompatibel sind und die Migration von der Quelle über das CMC-Portal empfangen können. Die Zielblockspeichergeräte werden während des Migrationsprozesses automatisch zugeordnet und erstellt.

1. **Registrieren Sie sich für ein CMC-Konto**. Auf der Supportseite finden Sie genaue Anweisungen dazu, wie Sie ein CMC-Konto erhalten. Weitere Informationen finden Sie [hier](https://support.cirrusdata.cloud/en/article/licensing-m4lhll/).

1. **Erstellen Sie ein Migrationsprojekt**, das die spezifischen Migrationsmerkmale, den Typ, den Besitzer der Migration und alle für die Definition der Vorgänge erforderlichen Details enthält. 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-create-project.jpg" alt-text="Screenshot: Erstellen eines neuen Projekts":::

1. **Definieren Sie die Parameter des Migrationsprojekts**. Verwenden Sie das webbasierte CMC-Portal, um die Migration zu konfigurieren. Definieren Sie dazu die Quelle, das Ziel und andere Parameter.

1. **Installieren Sie die CMC-Migrations-Agents auf den Quell- und Zielhosts**. Wählen Sie im webbasierten CMC-Verwaltungsportal die Option **Deploy Cirrus Migrate Cloud** (Cirrus Migrate Cloud bereitstellen) aus, um den curl-Befehl für **New Installation** (Neue Installation) abzurufen. Führen Sie den Befehl auf der Befehlszeilenschnittstelle von Quelle und Ziel aus.

1. **Erstellen Sie eine bidirektionale Verbindung zwischen Quell- und Zielhosts**. Verwenden Sie die Registerkarte **H2H** im webbasierten CMC-Verwaltungsportal und die Schaltfläche **Create New Connection** (Neue Verbindung erstellen). Wählen Sie das von der Anwendung verwendete Gerät aus, und nicht das Gerät, das vom Linux-Betriebssystem verwendet wird.

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-1.jpg" alt-text="Screenshot: Liste der bereitgestellten Hosts":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-2.jpg" alt-text="Screenshot: Liste der Host-zu-Host-Verbindungen":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-3.jpg" alt-text="Screenshot: Liste der migrierten Geräte":::

1. **Starten Sie die Migration zum virtuellen Zielcomputer** über das webbasierte CMC-Verwaltungsportal mit der Option **Migrate Host Volumes** (Hostvolumes mirgrieren). Befolgen Sie die Anweisungen für den Remotespeicherort. Verwenden Sie den Bildschirm auf der rechten Seite des CMC-Portals, um Zielvolumes automatisch zuzuordnen (**Auto allocate destination volumes**). 
 
1. Als Nächstes müssen Sie Azure-Anmeldeinformationen hinzufügen, um Konnektivität und Datenträgerbereitstellung zu ermöglichen. Dazu verwenden Sie die Registerkarte **Integrations** (Integrationen) im CMC-Portal. Füllen Sie die erforderlichen Felder mit den Werten Ihres Unternehmens für Azure aus: **Integration Name** (Integrationsname), **Tenant ID** (Mandanten-ID), **Client/Application ID** (Client/Anwendungs-ID) und **Secret** (Geheimnis). Klicken Sie auf **Speichern**. 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-4.jpg" alt-text="Screenshot: Eingeben von Azure-Anmeldeinformationen":::

    Ausführliche Informationen zum Erstellen einer Azure AD-Anwendung finden Sie in den [Schritt-für-Schritt-Anleitungen](https://support.cirrusdata.cloud/en/article/creating-an-azure-service-account-for-cirrus-data-cloud-tw2c9n/). Indem Sie die Azure AD-Anwendung für CMC erstellen und registrieren, ermöglichen Sie die automatische Erstellung von Azure Managed Disks-Datenträgern auf dem virtuellen Zielcomputer.

    >[!NOTE]
    >Da Sie im vorherigen Schritt die Option zum automatischen Zuordnen von Zielvolumes (**Auto allocate destination volumes**) ausgewählt haben, klicken Sie nicht erneut darauf, um eine neue Zuordnung durchzuführen. Falls dies geschieht, wird ein Fehler ausgegeben. Klicken Sie stattdessen auf **Weiter**.

## <a name="migration-guide"></a>Migrationsleitfaden

Nachdem Sie im vorherigen Schritt auf **Speichern** geklickt haben, wird das Fenster **New Migration Session** (Neue Migrationssitzung) angezeigt. Füllen Sie die Felder aus:
   - **Session description** (Sitzungsbeschreibung): Geben Sie eine aussagekräftige Beschreibung ein.
   - **Auto Resync Interval** (Intervall für automatische Neusynchronisierung): Hiermit wird ein Migrationszeitplan aktiviert. 
   - Verwenden Sie iQoS, um die Auswirkungen der Migration auf die Produktion auszuwählen:
     - **Minimum** (Minimal) drosselt die Migrationsrate auf 25 % der verfügbaren Bandbreite.
     - **Moderate** (Mittel) drosselt die Migrationsrate auf 50 % der verfügbaren Bandbreite.
     - **Aggressive** (Hoch) drosselt die Migrationsrate auf 75 % der verfügbaren Bandbreite.
     - **Relentless** (Uneingeschränkt) nimmt keine Drosselung der Migration vor.

       :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-iqos.jpg" alt-text="Screenshot: Optionen für iQoS-Einstellungen":::

Klicken Sie auf **Create Session** (Sitzung erstellen), um die Migration zu starten.

Vom Beginn der ersten Synchronisierung der Migration bis zum Start von cMotion ist keine Benutzerinteraktion mit CMC erforderlich.  Die einzige Ausnahme ist die Überwachung des Fortschritts. Mit dem Dashboard können Sie den aktuellen Status und die Sitzungsvolumes überwachen und die Änderungen nachverfolgen. 

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-1.jpg" alt-text="Screenshot: Überwachung des Fortschritts":::

Während der Migration können Sie die auf dem Quellgerät geänderten Blöcke beobachten, indem Sie auf die „Changed Data Map“ (Geänderte Datenzuordnung) klicken.  

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-2.jpg" alt-text="Screenshot: Geänderte Datenzuordnung":::

In den Details zu iQoS werden synchronisierte Blöcke und der Migrationsstatus angezeigt. Außerdem ist hier angegeben, dass es keine Auswirkungen auf die Produktions-E/A gibt.

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-3.jpg" alt-text="Screenshot: iQoS-Details":::

## <a name="moving-the-workload-to-azure-with-cmotion"></a>Verschieben der Workload in Azure mit cMotion

Nach Abschluss der Erstsynchronisierung wird das Verschieben der Workload vom Quelldatenträger auf den Azure Managed Disk-Zieldatenträger mithilfe von cMotion™ vorbereitet.

### <a name="start-cmotion"></a>Starten von cMotion™

An diesem Punkt sind die Systeme für die Umstellung der Migration auf cMotion™ bereit. 

1. Wählen Sie im CMS-Portal über Session die Option **Trigger cMotion™** (cMotion™ auslösen) aus, um die Workload vom Quell- auf den Zieldatenträger umzuschalten. Zum Überprüfen, ob der Prozess abgeschlossen wurde, können Sie „iostat“ oder einen entsprechenden Befehl verwenden. Wechseln Sie zum Terminal auf dem virtuellen Azure-Computer, und führen Sie *iostat /dev/<gerätename>* (z. B. „/dev/sdc“) aus, und beobachten Sie, ob die E/As von der Anwendung auf den Zieldatenträger in der Azure-Cloud geschrieben werden.

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-4.jpg" alt-text="Screenshot: Aktueller Überwachungsstatus":::

In diesem Zustand kann die Workload jederzeit verschoben oder auf den Quelldatenträger zurückgeschoben werden. Wenn Sie den virtuellen Produktionscomputer wiederherstellen möchten, verwenden Sie die Schaltfläche **Session Actions** (Sitzungsaktionen), und wählen Sie die Option **Revert cMotion™** (cMotion™ zurücksetzen) aus. Sie können beliebig oft hin- und herwechseln, während die Anwendung auf dem Quellhost/virtuellen Computer ausgeführt wird.

Wenn die endgültige Umstellung auf den virtuellen Zielcomputer erforderlich ist, führen Sie die folgenden Schritte aus:
1. Wählen Sie **Session Actions** (Sitzungsaktionen) aus.
2. Klicken Sie auf die Option **Finalize Cutover** (Umstellung abschließen), um die Umstellung auf den neuen virtuellen Azure-Computer endgültig festzulegen, und deaktivieren Sie die Option zum Entfernen des Quelldatenträgers. Beenden Sie zur endgültigen Hostumstellung alle anderen Anwendungen, die auf dem Quellhost ausgeführt werden. 

### <a name="move-the-application-to-the-destination-virtual-machine"></a>Verschieben der Anwendung auf den virtuellen Zielcomputer

Sobald die Umstellung abgeschlossen ist, muss die Anwendung auf den neuen virtuellen Computer umgeschaltet werden. Führen Sie dazu die folgenden Schritte aus:

1. Beenden Sie die Anwendung.
2. Heben Sie die Bereitstellung des migrierten Geräts auf.
3. Stellen Sie das neue migrierte Gerät auf dem virtuellen Azure-Computer bereit. 
4. Starten Sie dieselbe Anwendung auf dem virtuellen Azure-Computer auf dem neuen migrierten Datenträger. 
 
Vergewissern Sie sich, dass keine E/As an die Quellhostgeräte geleitet werden, indem Sie den Befehl „iostat“ auf dem Quellhost ausführen. Das Ausführen von „iostat“ auf einem virtuellen Azure-Computer zeigt an, dass E/A auf dem Terminal des virtuellen Azure-Computers ausgeführt wird.

### <a name="complete-the-migration-session-in-cmc-gui"></a>Abschließen der Migrationssitzung auf der CMC-GUI 

Der Migrationsschritt ist abgeschlossen, wenn alle E/As nach dem Auslösen von cMotion™ an die Zielgeräte umgeleitet wurden. Sie können die Sitzung jetzt über **Session Actions** (Sitzungsaktionen) schließen. Klicken Sie auf **Delete Session** (Sitzung löschen), um die Migrationssitzung zu schließen. Im letzten Schritt entfernen Sie die **Cirrus Migrate Cloud-Agents** vom Quellhost und vom virtuellen Azure-Computer. Zur Deinstallation rufen Sie den **curl-Befehl „Uninstall“** über die Schaltfläche **Deploy Cirrus Migrate Cloud** (Cirrus Migrate Cloud bereitstellen) auf. Die Option befindet sich im Abschnitt **Hosts** des Portals. 

Nachdem die Agents entfernt wurden, ist die Migration vollständig abgeschlossen. Jetzt wird die Quellanwendung in der Produktion auf dem virtuellen Azure-Zielcomputer mit lokal bereitgestellten Datenträgern ausgeführt.    

## <a name="support"></a>Support

### <a name="how-to-open-a-case-with-azure"></a>So erstellen Sie einen Supportfall für Azure-Probleme

Suchen Sie im [Azure-Portal](https://portal.azure.com) auf der Suchleiste oben nach Support. Wählen Sie **Hilfe und Support** -> **Neue Supportanfrage**.

### <a name="engaging-cirrus-support"></a>Anfordern von Cirrus-Support

Wählen Sie im CMC-Portal die Registerkarte **Help Center** (Hilfecenter) aus, um sich mit dem Support von Cirrus Data Solutions in Verbindung zu setzen, oder rufen Sie die [CDSI-Website](https://support.cirrusdata.cloud/en/) auf, und stellen Sie eine Supportanfrage.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [virtuellen Azure-Computern](/azure/virtual-machines/windows/overview)
- Weitere Informationen zu [Azure Managed Disks](/azure/virtual-machines/managed-disks-overview)
- Weitere Informationen zur [Speichermigration](/azure/storage/common/storage-migration-overview)
- [Cirrus Data-Website](https://www.cirrusdata.com/)
- Schrittweise Anleitungen für [cMotion](https://support.cirrusdata.cloud/en/category/howtos-1un623w/)
