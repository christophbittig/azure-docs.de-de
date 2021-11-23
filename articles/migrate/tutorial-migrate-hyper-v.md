---
title: Migrieren von Hyper-V-VMs zu Azure per Azure Migrate-Servermigration
description: Es wird beschrieben, wie Sie lokale virtuelle Hyper-V-Computer per Azure Migrate-Servermigration zu Azure migrieren.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 4588e782d8d397d09b6d66f4855884c859e69e2b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278073"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrieren von virtuellen Hyper-V-Computern zu Azure

In diesem Artikel wird veranschaulicht, wie Sie lokale Hyper-V-VMs mithilfe des Tools für die [Azure Migrate-Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) zu Azure migrieren.

Dieses Tutorial ist das dritte in einer Reihe zur Bewertung von Computern und ihrer Migration zu Azure.

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert, damit Sie schnell einen Proof of Concept einrichten können. Die Tutorials verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade.

 In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen des Tools für die Azure Migrate-Servermigration
> * Ermitteln der zu migrierenden VMs
> * Beginnen mit der Replikation von VMs
> * Führen Sie eine Testmigration aus, um sicherzustellen, dass alles wie erwartet funktioniert.
> * Durchführen einer vollständigen VM-Migration

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.


## <a name="prerequisites"></a>Voraussetzungen


Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

1. [Lesen Sie den Artikel zur Hyper-V-Migrationsarchitektur](hyper-v-migration-architecture.md).
1. [Beachten](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Sie die Anforderungen der Hyper-V-Hosts für die Migration sowie die Azure-URLs, auf die Hyper-V-Hosts und -Cluster bei der VM-Migration zugreifen müssen.
1. [Überprüfen](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) Sie die Anforderungen für Hyper-V-VMs, die Sie zu Azure migrieren möchten.
1. Es wird empfohlen, das zweite Tutorial zum [Bewerten von Hyper-V-VMs](tutorial-assess-hyper-v.md) durchzuarbeiten, bevor Sie sie zu Azure migrieren. Dies ist aber nicht unbedingt erforderlich.
1. Rufen Sie das bereits erstellte Projekt auf, oder [erstellen Sie ein neues Projekt](./create-manage-projects.md).
1. Überprüfen Sie die Berechtigungen für Ihr Azure-Konto: Ihr Azure-Konto benötigt Berechtigungen zum Erstellen eines virtuellen Computers sowie zum Schreiben auf einen verwalteten Azure-Datenträger.

## <a name="download-the-provider"></a>Herunterladen des Anbieters

Zum Migrieren von Hyper-V-VMs werden bei der Azure Migrate-Servermigration Softwareanbieter (Microsoft Azure Site Recovery-Anbieter und Microsoft Azure Recovery Services-Agent) auf Hyper-V-Hosts oder Clusterknoten installiert. Beachten Sie, dass die [Azure Migrate-Appliance](migrate-appliance.md) nicht für die Hyper-V-Migration verwendet wird.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Ermitteln**.
1. Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit Hyper-V**.
1. Wählen Sie unter **Zielregion** die Azure-Region aus, zu der Sie die Computer migrieren möchten.
1. Aktivieren Sie das Kontrollkästchen **Bestätigen Sie, dass die Zielregion für die Migration „<Name der Region>“ lautet**.
1. Klicken Sie auf **Ressourcen erstellen**. Daraufhin wird im Hintergrund ein Azure Site Recovery-Tresor erstellt.
    - Falls Sie die Migration bereits mit Azure Migrate-Servermigration eingerichtet haben, wird diese Option nicht angezeigt, da die Ressourcen bereits vorher eingerichtet wurden.
    - Nach dem Klicken auf diese Schaltfläche kann die Zielregion für dieses Projekt nicht mehr geändert werden.
    - Alle anschließenden Migrationen erfolgen zu dieser Region.

1. Laden Sie unter **Bereiten Sie die Hyper-V-Hostserver vor** den Hyper-V-Replikationsanbieter und die Datei mit dem Registrierungsschlüssel herunter.
    - Der Registrierungsschlüssel ist erforderlich, um den Hyper-V-Host bei „Azure Migrate-Servermigration“ zu registrieren.
    - Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.

    ![Anbieter und Schlüssel herunterladen](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

1. Kopieren Sie die Setupdatei für den Anbieter und die Datei mit dem Registrierungsschlüssel auf alle Hyper-V-Hosts (oder Clusterknoten), auf denen zu replizierende VMs ausgeführt werden.  

## <a name="install-and-register-the-provider"></a>Installieren und Registrieren des Anbieters

Kopieren Sie die Setupdatei für den Anbieter und die Datei mit dem Registrierungsschlüssel auf alle Hyper-V-Hosts (oder Clusterknoten), auf denen zu replizierende VMs ausgeführt werden. Führen Sie zum Installieren und Registrieren des Anbieters die folgenden Schritte auf der Benutzeroberfläche oder über Befehle aus:

# <a name="using-ui"></a>[Verwenden der Benutzeroberfläche](#tab/UI)

Führen Sie die Setupdatei für den Anbieter auf jedem Host aus, wie nachfolgend beschrieben:

1. Klicken Sie auf der Taskleiste auf das Dateisymbol, um den Ordner zu öffnen, in den die Installationsdatei und der Registrierungsschlüssel heruntergeladen werden.
1. Wählen Sie die Datei **AzureSiteRecoveryProvider.exe** aus.
    - Vergewissern Sie sich im Anbieterinstallations-Assistenten, dass **Ein (empfohlen)** aktiviert ist, und klicken Sie dann auf **Weiter**.
    - Wählen Sie **Installieren** aus, um den Standardinstallationsordner zu übernehmen.
    - Wählen Sie **Registrieren** aus, um diesen Server im Azure Site Recovery-Tresor zu registrieren.
    - Klicken Sie auf **Durchsuchen**.
    - Suchen Sie nach dem Registrierungsschlüssel, und klicken Sie auf **Öffnen**.
    - Klicken Sie auf **Weiter**.
    - Vergewissern Sie sich, dass **Direkt mit Azure Site Recovery verbinden (ohne Proxyserver)** aktiviert ist, und klicken Sie dann auf **Weiter**.
    - Klicken Sie auf **Fertig stellen**.


# <a name="using-commands"></a>[Verwenden von Befehlen](#tab/commands) 

Führen Sie die folgenden Befehle auf jedem Host aus, wie nachfolgend beschrieben:

1. Extrahieren Sie den Inhalt der Installationsprogrammdatei (AzureSiteRecoveryProvider.exe) wie folgt in einen lokalen Ordner auf dem Computer (z. B. „.\Temp“):

    ```
     AzureSiteRecoveryProvider.exe /q /x:.\Temp\Extracted
    ```

1. Wechseln Sie zum Ordner mit den extrahierten Dateien.

    ```
    cd .\Temp\Extracted
    ```
1. Installieren Sie den Hyper-V-Replikationsanbieter. Die Ergebnisse werden in „%Programdata%\ASRLogs\DRASetupWizard.log“ protokolliert.

    ```
    .\setupdr.exe /i 
    ```

1. Registrieren Sie den Hyper-V-Host bei Azure Migrate.

    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Credentials <key file path>
    ```

    **Konfigurieren von Proxyregeln:** Wenn Sie Verbindungen mit dem Internet über einen Proxy herstellen müssen, verwenden Sie die optionalen Parameter /proxyaddress und /proxyport, um die Proxyadresse (im Format http://ProxyIPAddress) und den lauschenden Port am Proxy anzugeben Für einen authentifizierten Proxy können Sie die optionalen Parameter /proxyusername und /proxypassword verwenden.

    ``` 
   "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r [/proxyaddress http://ProxyIPAddress] [/proxyport portnumber] [/proxyusername username] [/proxypassword password]
    ```
 
    **Konfigurieren von Proxyumgehungsregeln:** Verwenden Sie zum Konfigurieren von Proxyumgehungsregeln den optionalen Parameter /AddBypassUrls, geben Sie die Umgehungs-URLs für den Proxy getrennt durch Semikolons (;) an, und führen Sie die folgenden Befehle aus:

    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r [/proxyaddress http://ProxyIPAddress] [/proxyport portnumber] [/proxyusername username] [/proxypassword password] [/AddBypassUrls URLs]
    ```
    und
    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /configure /AddBypassUrls URLs 
    ```
---

Nachdem der Anbieter auf den Hosts installiert wurde, navigieren Sie zum Azure-Portal, und klicken Sie unter **Computer ermitteln** auf **Registrierung abschließen**.

 ![Registrierung abschließen](./media/tutorial-migrate-hyper-v/finalize-registration.png) 

Es kann nach dem Abschluss der Registrierung bis zu 15 Minuten dauern, bis ermittelte VMs in „Azure Migrate-Servermigration“ angezeigt werden. Wenn VMs ermittelt werden, erhöht sich die Anzahl unter **Ermittelte Server**.

 ![Ermittelte Server](./media/tutorial-migrate-hyper-v/discovered-servers.png)

## <a name="replicate-hyper-v-vms"></a>Replizieren von Hyper-V-VMs

Nachdem die Ermittlung abgeschlossen ist, können Sie mit der Replikation von Hyper-V-VMs in Azure beginnen.

> [!NOTE]
> Sie können bis zu zehn Computer gleichzeitig replizieren. Müssen Sie mehr Computer replizieren, führen Sie die Replikation in Batches mit jeweils zehn Computern durch.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Replizieren**.
1. Wählen Sie unter **Replizieren** > **Quelleinstellungen** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit Hyper-V**. Klicken Sie anschließend auf **Next: Virtuelle Computer**.
1. Wählen Sie unter **Virtuelle Computer** die Computer aus, die Sie replizieren möchten.
    - Wenn Sie eine Bewertung für die VMs ausgeführt haben, können Sie die Empfehlungen zur VM-Größenanpassung und zum Datenträgertyp (Premium/Standard) aus den Bewertungsergebnissen anwenden. Wählen Sie hierzu unter **Migrationseinstellungen aus einer Azure Migrate-Bewertung importieren?** die Option **Ja** aus.
    - Wählen Sie **Nein** aus, wenn Sie keine Bewertung ausgeführt haben oder die Bewertungseinstellungen nicht verwenden möchten.
    - Falls Sie sich für die Verwendung der Bewertung entschieden haben, wählen Sie die VM-Gruppe und den Bewertungsnamen aus.

        ![Auswählen der Bewertung](./media/tutorial-migrate-hyper-v/select-assessment.png)

1. Suchen Sie unter **Virtuelle Computer** je nach Bedarf nach VMs, und aktivieren Sie alle VMs, die Sie migrieren möchten. Klicken Sie anschließend auf **Weiter: Zieleinstellungen**.

    ![Auswählen von VMs](./media/tutorial-migrate-hyper-v/select-vms.png)

1. Wählen Sie unter **Zieleinstellungen** die Zielregion für die Migration, das Abonnement und die Ressourcengruppe aus, in der sich die Azure-VMs nach der Migration befinden.
1. Wählen Sie unter **Replikationsspeicherkonto** das Azure Storage-Konto aus, in dem replizierte Daten in Azure gespeichert werden.
1. Wählen Sie unter **Virtuelles Netzwerk** das Azure-VNET/-Subnetz aus, in das die Azure-VMs nach der Migration eingebunden werden.
1. Wählen Sie unter **Verfügbarkeitsoptionen** Folgendes aus:
    -  Verfügbarkeitszone, um den migrierten Computer einer bestimmten Verfügbarkeitszone in der Region anzuheften. Verteilen Sie mit dieser Option Server, die eine Anwendungsebene mit mehreren Knoten bilden, über Verfügbarkeitszonen. Wenn Sie diese Option auswählen, müssen Sie die Verfügbarkeitszone angeben, die für jeden auf der Registerkarte „Compute“ ausgewählten Computer verwendet werden soll. Diese Option ist nur verfügbar, wenn die für die Migration ausgewählte Zielregion Verfügbarkeitszonen unterstützt.
    -  Verfügbarkeitsgruppe, um den migrierten Computer in einer Verfügbarkeitsgruppe zu platzieren. Um diese Option verwenden zu können, muss die ausgewählte Zielressourcengruppe über mindestens eine Verfügbarkeitsgruppe verfügen.
    - Die Infrastrukturredundanzoption ist nicht erforderlich, wenn Sie keine dieser Verfügbarkeitskonfigurationen für die migrierten Computer benötigen.
1. Wählen Sie unter **Azure-Hybridvorteil**

    - die Option **Nein** aus, falls Sie den Azure-Hybridvorteil nicht anwenden möchten. Klicken Sie auf **Weiter**.
    - Wählen Sie **Ja** aus, wenn Sie über Windows Server-Computer verfügen, die durch aktive Software Assurance- oder Windows Server-Abonnements abgedeckt sind, und den Vorteil auf die zu migrierenden Computer anwenden möchten. Klicken Sie dann auf **Weiter**.

    ![Zieleinstellungen](./media/tutorial-migrate-hyper-v/target-settings.png)

1. Überprüfen Sie in **Compute** Name, Größe, Betriebssystem- Datenträger und Verfügbarkeitskonfiguration der VM (falls im vorherigen Schritt ausgewählt). Die VMs müssen die [Azure-Anforderungen](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) erfüllen.

    - **VM-Größe**: Bei Verwendung von Bewertungsempfehlungen enthält die Dropdownliste für die VM-Größe die empfohlene Größe. Andernfalls wählt Azure Migrate eine Größe basierend auf der höchsten Übereinstimmung im Azure-Abonnement aus. Alternativ können Sie unter **Azure-VM-Größe** manuell eine Größe auswählen.
    - **Betriebssystemdatenträger**: Geben Sie den Betriebssystemdatenträger (Startdatenträger) für die VM an. Der Betriebssystemdatenträger enthält den Bootloader und das Installationsprogramm des Betriebssystems.
    - **Verfügbarkeitsgruppe**: Wenn die VM nach der Migration in einer Azure-Verfügbarkeitsgruppe enthalten sein soll, geben Sie die Gruppe an. Die Gruppe muss Teil der Zielressourcengruppe sein, die Sie für die Migration angeben.

    ![VM-Computeeinstellungen](./media/tutorial-migrate-hyper-v/compute-settings.png)

1. Geben Sie unter **Datenträger** die VM-Datenträger an, die in Azure repliziert werden sollen. Klicken Sie dann auf **Weiter**.
    - Sie können Datenträger von der Replikation ausschließen.
    - Wenn Sie Datenträger ausschließen, sind diese nach der Migration nicht auf der Azure-VM vorhanden.

    ![Screenshot: Registerkarte „Datenträger“ des Dialogfelds „Replizieren“](./media/tutorial-migrate-hyper-v/disks.png)

1. Überprüfen Sie unter **Replikation prüfen und starten** die Einstellungen, und klicken Sie auf **Replizieren**, um die erste Replikation für die Server zu starten.

> [!NOTE]
> Sie können die Replikationseinstellungen vor Beginn der Replikation jederzeit unter **Verwalten** > **Aktuell replizierte Computer** aktualisieren. Die Einstellungen können nach dem Beginn der Replikation nicht mehr geändert werden.

## <a name="provision-for-the-first-time"></a>Erste Bereitstellung

Ist dies der erste virtuelle Computer, den Sie im Azure Migrate-Projekt replizieren, werden von der Azure Migrate-Servermigration diese Ressourcen automatisch in derselben Ressourcengruppe wie das Projekt bereitgestellt.
- **Cachespeicherkonto:** Die auf Hyper-V-Hosts installierte Azure Site Recovery-Anbietersoftware lädt Replikationsdaten für die virtuellen Computer, die für die Replikation konfiguriert wurden, in ein Speicherkonto (als Cachespeicherkonto oder Protokollspeicherkonto bezeichnet) in Ihrem Abonnement hoch. Der Azure Migrate-Dienst kopiert dann die hochgeladenen Replikationsdaten aus dem Speicherkonto auf die zum virtuellen Computer gehörenden verwalteten Replikatdatenträger. Das Cachespeicherkonto muss beim Konfigurieren der Replikation für einen virtuellen Computer angegeben werden. Im Azure Migrate-Portal wird automatisch ein Konto für das Azure Migrate-Projekt erstellt, wenn die Replikation zum ersten Mal im Projekt konfiguriert wird.

## <a name="track-and-monitor"></a>Nachverfolgen und Überwachen


- Wenn Sie auf **Replizieren** klicken, beginnt die Durchführung des Auftrags „Replikation starten“.
- Nachdem der Auftrag „Replikation starten“ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig in Azure repliziert.

Sie haben die Möglichkeit, den Auftragsstatus über die Portalbenachrichtigungen nachzuverfolgen.

Sie können den Replikationsstatus überwachen, indem Sie auf **Server werden repliziert** klicken (unter **Azure Migrate: Servermigration**).
![Überwachen der Replikation](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Ausführen einer Testmigration


Wenn die Deltareplikation beginnt, können Sie zunächst eine Testmigration für die VMs ausführen, bevor eine vollständige Migration zu Azure erfolgt. Dieser Schritt sollte vor der Migration mindestens einmal für jeden Computer ausgeführt werden.

- Bei einer Testmigration wird überprüft, ob die Migration wie erwartet funktioniert, ohne die lokalen Computer zu beeinträchtigen, und ob diese während der Replikation betriebsbereit bleiben.
- Mit der Testmigration wird die Migration simuliert, indem ein virtueller Azure-Computer mit replizierten Daten erstellt wird (normalerweise per Migration zu einem nicht für die Produktion bestimmten Azure VNET unter Ihrem Azure-Abonnement).
- Sie können den replizierten virtuellen Azure-Testcomputer verwenden, um die Migration zu überprüfen, App-Tests durchzuführen und Probleme zu beheben, bevor die vollständige Migration erfolgt.

Führen Sie die Testmigration wie folgt durch:


1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Servermigration** auf **Migrierte Server testen**.

     ![Testen der migrierten Server](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

1. Klicken Sie mit der rechten Maustaste auf die zu testende VM, und klicken Sie anschließend auf **Testmigration**.

    ![Testmigration](./media/tutorial-migrate-hyper-v/test-migrate.png)

1. Wählen Sie unter **Testmigration** das virtuelle Azure-Netzwerk aus, in dem sich der virtuelle Azure-Computer nach der Migration befindet. Es empfiehlt sich, ein nicht für die Produktion bestimmtes virtuelles Netzwerk zu verwenden.
1. Der Auftrag **Testmigration** wird gestartet. Überwachen Sie den Auftrag anhand der Portalbenachrichtigungen.
1. Zeigen Sie die migrierte Azure-VM nach Abschluss der Migration im Azure-Portal unter **Virtuelle Computer** an. Der Computername enthält das Suffix **-Test**.
1. Klicken Sie nach Abschluss des Tests mit der rechten Maustaste unter **Aktuell replizierte Computer** auf die Azure-VM, und klicken Sie anschließend auf **Testmigration bereinigen**.

    ![Bereinigen der Migration](./media/tutorial-migrate-hyper-v/clean-up.png)
    > [!NOTE]
    > Sie können jetzt Ihre Server, auf denen SQL Server ausgeführt wird, bei SQL VM RP registrieren, um die Vorteile automatisierter Patches, der automatisierten Sicherung und vereinfachten Lizenzverwaltung mit der Erweiterung für den SQL-IaaS-Agent zu nutzen.
    >- Wählen Sie für die Registrierung bei SQL VM RP die Optionen **Verwaltung** > **Server werden repliziert** > **Computer mit SQL Server** > **Compute und Netzwerk** und dann **Ja** aus.
    >- Wählen Sie „Azure-Hybridvorteil“ aus, wenn Sie über SQL Server-Instanzen verfügen, die durch aktive Software Assurance- oder SQL Server-Abonnements abgedeckt sind, und den Vorteil auf die zu migrierenden Computer anwenden möchten.

## <a name="migrate-vms"></a>Migrieren virtueller Computer

Nachdem Sie sich vergewissert haben, dass die Testmigration wie erwartet funktioniert, können Sie die lokalen Computer migrieren.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Server werden repliziert**.

    ![Replizieren der Server](./media/tutorial-migrate-hyper-v/replicate-servers.png)

1. Klicken Sie unter **Aktuell replizierte Computer** mit der rechten Maustaste auf die VM und dann auf **Migrieren**.
1. Wählen Sie unter **Migrieren** > **Virtuelle Computer herunterfahren und eine geplante Migration ohne Datenverlust durchführen?** die Option **Ja** >  und anschließend **OK** aus.
    - Azure Migrate fährt die lokale VM standardmäßig herunter und führt eine bedarfsabhängige Replikation aus, um alle VM-Änderungen zu synchronisieren, die seit der letzten Replikation vorgenommen wurden. So wird sichergestellt, dass keine Daten verloren gehen.
    - Falls Sie die VM nicht herunterfahren möchten, wählen Sie **Nein** aus.
1. Für den virtuellen Computer wird ein Migrationsauftrag gestartet. Verfolgen Sie den Auftrag anhand der Azure-Benachrichtigungen nach.
1. Nach Abschluss des Auftrags können Sie die VM auf der Seite **Virtuelle Computer** anzeigen und verwalten.

## <a name="complete-the-migration"></a>Fertigstellen der Migration

1. Klicken Sie nach Abschluss der Migration mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Replikation beenden** aus. Die folgenden Schritte werden ausgeführt:
    - Beendet die Replikation für den lokalen Computer.
    - Entfernt den Computer aus dem Zähler **Server werden repliziert.** in Azure Migrate: Servermigration.
    - Bereinigt die Replikationsstatusinformationen für den virtuellen Computer.
1. Überprüfen und [beheben Sie alle Windows-Aktivierungsprobleme auf dem virtuellen Azure-Computer](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems).
1. Führen Sie App-Anpassungen nach der Migration durch, z. B. die Aktualisierung von Hostnamen, Datenbankverbindungszeichenfolgen und Webserverkonfigurationen.
1. Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
1. Leiten Sie den Datenverkehr auf die migrierte Instanz der Azure-VM um.
1. Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
1. Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
1. Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs.

## <a name="post-migration-best-practices"></a>Bewährte Methoden nach der Migration

- Beachten Sie zur Steigerung der Resilienz Folgendes:
    - Schützen Sie Daten, indem Sie Azure-VMs mit dem Azure Backup-Dienst sichern. [Weitere Informationen](../backup/quick-backup-vm-portal.md)
    - Sorgen Sie für die kontinuierliche Ausführung und Verfügbarkeit von Workloads, indem Sie Azure-VMs mithilfe von Site Recovery in eine sekundäre Region replizieren. [Weitere Informationen](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Beachten Sie zur Steigerung der Sicherheit Folgendes:
    - Sperren und beschränken Sie den Zugriff von eingehendem Datenverkehr mit der [Just-In-Time-Verwaltung in Microsoft Defender für Cloud](../security-center/security-center-just-in-time.md).
    - Beschränken Sie den Netzwerkdatenverkehr mithilfe von [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) auf Verwaltungsendpunkte.
    - Stellen Sie [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) bereit, um Datenträger und Daten vor Diebstahl und unbefugtem Zugriff zu schützen.
    - Erfahren Sie mehr über das [Schützen von IaaS-Ressourcen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), und besuchen Sie die Website [Microsoft Defender für Cloud](https://azure.microsoft.com/services/security-center/).
- Beachten Sie zur Überwachung und Verwaltung Folgendes:
-  Ziehen Sie die Bereitstellung von [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) in Erwägung, um den Ressourceneinsatz und die Ausgaben zu überwachen.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Artikel zur [Cloudmigration](/azure/architecture/cloud-adoption/getting-started/migrate) des Frameworks für die Cloudeinführung (Cloud Adoption Framework) an.
