---
title: Verschieben von Azure-VMs zwischen Government- und öffentlichen Regionen mit Azure Site Recovery
description: Verwenden Sie Azure Site Recovery, um virtuelle Azure-Computer zwischen Azure Government- und öffentlichen Regionen zu verschieben.
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 57e608348e460c0598f7003af96ea1ac55bbf951
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022814"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Verschieben von virtuellen Azure IaaS-Computern zwischen Azure Government und öffentlichen Regionen 

Möglicherweise möchten Sie Ihre virtuellen Azure IaaS-Computer zwischen Azure Government und öffentlichen Regionen verschieben, um die Verfügbarkeit Ihrer vorhandenen virtuellen Computer zu erhöhen, die Verwaltbarkeit zu verbessern, oder aus Gründen der Governance, wie [hier](azure-to-azure-move-overview.md) beschrieben.

Zusätzlich zur Verwendung des Diensts [Azure Site Recovery](site-recovery-overview.md) zur Verwaltung und Orchestrierung der Notfallwiederherstellung von lokalen Computern und virtuellen Azure-Computern für BCDR-Zwecke (Business Continuity/Disaster Recovery, Geschäftskontinuität und Notfallwiederherstellung) können Sie mit Site Recovery auch die Migration von virtuellen Azure-Computern zu einer sekundären Region verwalten.       

In diesem Tutorial erfahren Sie, wie Sie virtuelle Azure-Computer mithilfe von Azure Site Recovery zwischen Azure Government und öffentlichen Regionen verschieben. Das Verfahren kann erweitert werden, um virtuelle Computer zwischen Regionspaaren zu verschieben, die sich nicht im gleichen geografischen Cluster befinden. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen
> * Vorbereiten der virtuellen Quellcomputer
> * Vorbereiten der Zielregion
> * Kopieren von Daten in die Zielregion
> * Testen der Konfiguration
> * Durchführen der Verschiebung
> * Verwerfen der Ressourcen in der Quellregion

> [!IMPORTANT]
> Dieses Tutorial veranschaulicht, wie Sie virtuelle Azure-Computer zwischen Azure Government und öffentlichen Regionen oder zwischen Regionspaaren verschieben, die von der normalen Notfallwiederherstellungslösung für virtuelle Azure-Computer nicht unterstützt werden. Falls Ihre Paare aus Quell- und Zielregionen [unterstützt](./azure-to-azure-support-matrix.md#region-support) werden, finden Sie in diesem [Dokument](azure-to-azure-tutorial-migrate.md) Informationen zum Verschieben. Falls Sie die Verfügbarkeit durch das Migrieren von virtuellen Computern in einer Verfügbarkeitsgruppe zu an Zonen angehefteten virtuellen Computern verbessern möchten, finden Sie in [diesem Tutorial](move-azure-VMs-AVset-Azone.md) weitere Informationen.

> [!IMPORTANT]
> Das Verwenden dieser Methode zum Konfigurieren von DR zwischen nicht unterstützten Regionspaaren ist nicht ratsam, da die Paare unter Berücksichtigung der Datenlatenz definiert werden, was für ein DR-Szenario kritisch ist.

## <a name="verify-prerequisites"></a>Überprüfen der Voraussetzungen

> [!NOTE]
> Stellen Sie sicher, dass Sie die [Architektur und die Komponenten](physical-azure-architecture.md) des Szenarios verstehen. Diese Architektur wird verwendet, um virtuelle Azure-Computer verschieben, **indem die virtuellen Computer wie physische Server behandelt werden**.

- Überprüfen Sie die [Supportanforderungen](vmware-physical-secondary-support-matrix.md) für alle Komponenten.
- Überprüfen Sie, ob die Server, die Sie replizieren möchten, die [Azure-VM-Anforderungen](vmware-physical-secondary-support-matrix.md#replicated-vm-support) erfüllen.
- Vorbereiten eines Kontos für die automatische Installation des Mobility Service auf jedem Server, den Sie replizieren möchten.

- Hinweis: Nach dem Failover zur Zielregion in Azure ist kein direktes Failback zur Quellregion möglich. Sie müssen die Replikation zurück an das Ziel erneut einrichten.

### <a name="verify-azure-account-permissions"></a>Überprüfen der Azure-Kontoberechtigungen

Vergewissern Sie sich, dass Ihr Azure-Konto über die Berechtigungen für die Replikation von virtuellen Computern in Azure verfügt.

- Überprüfen Sie die [Berechtigungen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines), die Sie für das Replizieren von Computern in Azure benötigen.
- Überprüfen und ändern Sie Berechtigungen der [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). 

### <a name="set-up-an-azure-network"></a>Richten Sie ein Azure-Netzwerk ein

Richten Sie das [Azure-Zielnetzwerk](../virtual-network/quick-create-portal.md) ein.

- Azure-VMs werden in dieses Netzwerk platziert, wenn sie nach dem Failover erstellt werden.
- Das Netzwerk muss sich in der gleichen Region befinden wie der Recovery Services-Tresor.

### <a name="set-up-an-azure-storage-account"></a>Richten Sie ein Azure-Speicherkonto ein

Richten Sie ein [Azure Storage-Konto](../storage/common/storage-account-create.md) ein.

- Site Recovery repliziert lokale Computer in den Azure-Speicher. Virtuelle Azure-Computer werden nach dem Failover aus dem Speicher erstellt.
- Das Speicherkonto muss sich in der gleichen Region wie der Recovery Services-Tresor befinden.


## <a name="prepare-the-source-vms"></a>Vorbereiten der virtuellen Quellcomputer

### <a name="prepare-an-account-for-mobility-service-installation"></a>Vorbereiten eines Kontos für die Installation des Mobility Services

Der Mobilitätsdienst muss auf jedem Computer installiert sein, den Sie replizieren möchten. Site Recovery installiert diesen Dienst automatisch, wenn Sie die Replikation für den Server aktivieren. Für die automatische Installation müssen Sie ein Konto vorbereiten, mit dem Site Recovery auf den Server zugreift.

- Sie können ein Domänenkonto oder ein lokales Konto verwenden
- Wenn Sie kein Domänenkonto verwenden, deaktivieren Sie für Windows-VMs die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer. Zu diesem Zweck fügen Sie in der Registrierung unter **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** den DWORD-Eintrag **LocalAccountTokenFilterPolicy** mit dem Wert 1 hinzu.
- Um den Registrierungseintrag zum Deaktivieren der Einstellung über eine Befehlszeilenschnittstelle hinzuzufügen, geben Sie Folgendes ein: `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.`
- Bei Linux muss auf dem Linux-Quellserver das root-Konto verwendet werden.


## <a name="prepare-the-target-region"></a>Vorbereiten der Zielregion

1. Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen von VMs in der für die Notfallwiederherstellung verwendeten Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

2. Stellen Sie sicher, dass Ihr Abonnement über ausreichend Ressourcen verfügt, um VMs zu unterstützen, die so groß wie die Quell-VMs sind. Wenn Sie Site Recovery verwenden, um Daten in die Zielregion zu kopieren, wird dieselbe oder eine möglichst ähnliche Größe für den virtuellen Zielcomputer ausgewählt.

3. Achten Sie darauf, für jede im Quellnetzwerklayout identifizierte Komponente eine Ressource zu erstellen. Dadurch wird sichergestellt, dass Ihre virtuellen Computer nach dem Verschieben in die Zielregion über alle Funktionen und Features verfügen, die Sie in der Quellregion genutzt haben.

    > [!NOTE]
    > Azure Site Recovery ermittelt und erstellt automatisch ein virtuelles Netzwerk, wenn Sie die Replikation für die Quell-VM aktivieren. Alternativ können Sie vorab ein Netzwerk erstellen und es der VM beim Aktivieren der Replikation zuweisen. Alle anderen Ressourcen (siehe unten) müssen jedoch manuell in der Zielregion erstellt werden.

     Informationen zum Erstellen der für Sie relevanten am häufigsten verwendeten Netzwerkressourcen auf Grundlage der Quell-VM-Konfiguration finden Sie in den folgenden Dokumenten.

    - [Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md)
    - [Load Balancer](../load-balancer/index.yml)
    - [Öffentliche IP-Adresse](../virtual-network/ip-services/virtual-network-public-ip-address.md)
    
    Informationen zu anderen Netzwerkkomponenten finden Sie in der [Dokumentation](../index.yml?pivot=products&panel=network) zum Netzwerk.

4. [Erstellen Sie manuell ein nicht für die Produktion vorgesehenes Netzwerk](../virtual-network/quick-create-portal.md) in der Zielregion, wenn Sie die Konfiguration vor der endgültigen Verschiebung in die Zielregion testen möchten. Diese Vorgehensweise wird empfohlen, da es so nur zu minimalen Beeinträchtigungen in der Produktionsumgebung kommt.

## <a name="copy-data-to-the-target-region"></a>Kopieren von Daten in die Zielregion
Im Folgenden wird erläutert, wie Sie Daten mithilfe von Azure Site Recovery in die Zielregion kopieren.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Erstellen Sie den Tresor in einer beliebigen Region außer der Quellregion.

1. Melden Sie sich unter [Azure-Portal](https://portal.azure.com) > **Recovery Services** an.
2. Klicken Sie auf **Ressource erstellen** > **Verwaltungstools** > **Backup & Site Recovery**.
3. Geben Sie in **Name** den Anzeigenamen **ContosoVMVault** ein. Wenn Sie mehrere Abonnements haben, wählen Sie das entsprechende Abonnement aus.
4. Erstellen Sie die Ressourcengruppe **ContosoRG**.
5. Geben Sie eine Azure-Region an. Eine Liste mit den unterstützten Regionen finden Sie in den [Preisdetails zu Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)unter „Geografische Verfügbarkeit“.
6. Klicken Sie unter „Recovery Services-Tresore“ auf **Übersicht** > **ConsotoVMVault** >  **+Replizieren**.
7. Wählen Sie **To Azure (Zu Azure)**  > **Nicht virtualisiert/Andere** aus.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Richten Sie den Konfigurationsserver für die Erkennung von virtuellen Computern ein.


Richten Sie den Konfigurationsserver ein, registrieren Sie ihn im Tresor, und ermitteln Sie die VMs.

1. Klicken Sie auf **Site Recovery** > **Infrastruktur vorbereiten** > **Quelle**.
2. Wenn Sie keinen Konfigurationsserver verwenden, klicken Sie auf **+Konfigurationsserver**.
3. Überprüfen Sie unter **Server hinzufügen**, ob unter **Servertyp** die Option **Konfigurationsserver** angezeigt wird.
4. Laden Sie die Installationsdatei für das einheitliche Setup von Site Recovery herunter.
5. Laden Sie den Tresorregistrierungsschlüssel herunter. Sie benötigen diesen beim Ausführen des einheitlichen Setups. Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.

   ![Quelle einrichten](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrieren des Konfigurationsservers im Tresor

Führen Sie zunächst folgende Schritte aus: 

#### <a name="verify-time-accuracy"></a>Überprüfen der Zeitgenauigkeit
Stellen Sie auf dem Konfigurationsservercomputer sicher, dass die Systemuhr mit einem [Zeitserver](/windows-server/networking/windows-time-service/windows-time-service-top) synchronisiert ist. Die Zeiten sollten übereinstimmen. Falls der Unterschied 15 Minuten beträgt, ist das Setup unter Umständen nicht erfolgreich.

#### <a name="verify-connectivity"></a>Überprüfen der Konnektivität
Stellen Sie sicher, dass der Computer ausgehend von Ihrer Umgebung auf die folgenden URLs zugreifen kann: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP-Adressen-basierte Firewallregeln sollten die Kommunikation mit allen Azure-URLs zulassen, die über dem HTTPS-Port (443) aufgelistet sind. Um die IP-Bereiche zu vereinfachen und einzuschränken, wird der Einsatz von URL-Filterung empfohlen.

- **Gewerbliche IPs**: Lassen Sie die [Azure Datacenter-IP-Bereiche](https://www.microsoft.com/download/confirmation.aspx?id=41653) und den HTTPS-Port (443) zu. Lassen Sie die IP-Adressbereiche für die Azure-Region Ihres Abonnements zu, um die URLs für AAD, Sicherung, Replikation und Speicher zu unterstützen.  
- **Behörden-IPs**: Lassen Sie die [Azure Government Datacenter-IP-Bereiche](https://www.microsoft.com/en-us/download/details.aspx?id=57063) und den HTTPS-Port (443) für alle USGov-Regionen (Virginia, Texas, Arizona und Iowa) zu, um die URLs für AAD, Sicherung, Replikation und Speicher zu unterstützen.  

#### <a name="run-setup"></a>Ausführen von Setup
Führen Sie das einheitliche Setup als lokaler Administrator auf dem Konfigurationsserver aus. Standardmäßig werden auch der Prozessserver und der Masterzielserver auf dem Konfigurationscomputer installiert.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Nach Abschluss der Registrierung wird der Konfigurationsserver auf der Seite **Einstellungen** > **Server** im Tresor angezeigt.

### <a name="configure-target-settings-for-replication"></a>Konfigurieren von Zieleinstellungen für die Replikation

Wählen Sie Zielressourcen aus, und überprüfen Sie sie.

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Ziel**, und wählen Sie das gewünschte Azure-Abonnement aus.
2. Geben Sie das Ziel-Bereitstellungsmodell an.
3. Site Recovery prüft, ob Sie über ein oder mehrere kompatible Azure-Speicherkonten und -Netzwerke verfügen.

   ![Ziel](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Erstellen einer Replikationsrichtlinie

1. Klicken Sie zum Erstellen einer neuen Replikationsrichtlinie auf **Site Recovery-Infrastruktur** > **Replikationsrichtlinien** >  **+Replikationsrichtlinie**.
2. Geben Sie unter **Replikationsrichtlinie erstellen** einen Richtliniennamen an.
3. Geben Sie in **RPO-Schwellenwert** den RPO-Grenzwert (Recovery Point Objective) an. Mit diesem Wert wird angegeben, wie oft Datenwiederherstellungspunkte erstellt werden. Wenn dieser Grenzwert bei der fortlaufenden Replikation überschritten wird, wird eine Warnung generiert.
4. Geben Sie unter **Aufbewahrungszeitraum des Wiederherstellungspunkts** die Größe des Aufbewahrungszeitfensters für die einzelnen Wiederherstellungspunkte in Stunden an. Replizierte VMs können für jeden Punkt eines Zeitfensters wiederhergestellt werden. Für nach Storage Premium replizierte Computer wird eine Aufbewahrungsdauer von bis zu 24 Stunden unterstützt (72 Stunden für Standardspeicher).
5. Geben Sie unter **App-konsistente Momentaufnahmehäufigkeit** an, wie häufig (in Minuten) Wiederherstellungspunkte erstellt werden sollen, die anwendungskonsistente Momentaufnahmen enthalten. Klicken Sie auf **OK**, um die Richtlinie zu erstellen.

    ![Replikationsrichtlinie](./media/physical-azure-disaster-recovery/replication-policy.png)


Die Richtlinie wird dem Konfigurationsserver automatisch zugeordnet. Standardmäßig wird für das Failback automatisch eine passende Richtlinie erstellt. Bei Verwendung der Replikationsrichtlinie **rep-policy** wird z.B. die Failbackrichtlinie **rep-policy-failback** erstellt. Diese Richtlinie wird erst verwendet, wenn Sie ein Failback über Azure initiieren.

### <a name="enable-replication"></a>Aktivieren der Replikation

- Site Recovery installiert den Mobility Service, wenn die Replikation aktiviert wird.
- Wenn Sie die Replikation für einen Server aktivieren, kann es länger als 15 Minuten dauern, bis die Änderungen wirksam sind und im Portal angezeigt werden.

1. Klicken Sie auf **Replicate application (Anwendung replizieren)**  > **Quelle**.
2. Wählen Sie unter **Quelle** den Konfigurationsserver aus.
3. Wählen Sie unter **Computertyp** die Option **Physische Computer** aus.
4. Wählen Sie den Prozessserver (Konfigurationsserver) aus. Klicken Sie dann auf **OK**.
5. Wählen Sie unter **Ziel** das Abonnement und die Ressourcengruppe aus, in dem bzw. der Sie die virtuellen Azure-Computer erstellen möchten, für die ein Failover durchgeführt wurde. Wählen Sie das Bereitstellungsmodell aus, das in Azure verwendet werden soll (klassisch oder Resource Manager).
6. Wählen Sie das Azure-Speicherkonto aus, das Sie für die Replikation von Daten verwenden möchten. 
7. Wählen Sie das Azure-Netzwerk und das Subnetz aus, mit dem virtuelle Azure-Computer, die nach einem Failover erstellt werden, eine Verbindung herstellen.
8. Wählen Sie die Option **Jetzt für die ausgewählten Computer konfigurieren** aus, um die Netzwerkeinstellung auf alle Computer anzuwenden, die geschützt werden sollen. Wählen Sie **Später konfigurieren** aus, um das Azure-Netzwerk pro Computer auszuwählen. 
9. Klicken Sie unter **Physische Computer** auf **+ Physischer Computer**. Geben Sie den Namen und die IP-Adresse an. Wählen Sie das Betriebssystem des Computers, den Sie replizieren möchten, aus. Es dauert einige Minuten, bis die Server ermittelt und aufgelistet werden. 

   > [!WARNING]
   > Sie müssen die IP-Adresse des virtuellen Azure-Computers eingeben, den Sie verschieben möchten.

10. Wählen Sie unter **Eigenschaften** > **Eigenschaften konfigurieren** das Konto aus, das der Prozessserver zum automatischen Installieren des Mobility Service auf dem Computer verwenden soll.
11. Überprüfen Sie unter **Replikationseinstellungen** > **Replikationseinstellungen konfigurieren**, ob die richtige Replikationsrichtlinie ausgewählt ist. 
12. Klicken Sie auf **Replikation aktivieren**. Sie können den Fortschritt des Auftrags **Schutz aktivieren** unter **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** verfolgen. Nachdem der Auftrag **Schutz abschließen** ausgeführt wurde, ist der Computer bereit für das Failover.


Zum Überwachen der hinzugefügten Server können Sie den letzten Zeitpunkt der Ermittlung unter **Konfigurationsserver** > **Letzter Kontakt um** überprüfen. Wenn Sie Computer hinzufügen möchten, ohne auf die planmäßige Ermittlung zu warten, markieren Sie den Konfigurationsserver (nicht darauf klicken) und klicken auf **Aktualisieren**.

## <a name="test-the-configuration"></a>Testen der Konfiguration


1. Navigieren Sie zum Tresor, klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf die VM, die Sie in die Zielregion verschieben möchten, und klicken Sie auf das Symbol **+ Testfailover**.
2. Wählen Sie unter **Testfailover** einen Wiederherstellungspunkt für das Failover aus:

   - **Letzte Verarbeitung**: Führt ein Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt aus, der vom Site Recovery-Dienst verarbeitet wurde. Der Zeitstempel wird angezeigt. Mit dieser Option wird keine Zeit für die Verarbeitung von Daten verwendet und die Recovery Time Objective (RTO) niedrig gehalten.
   - **Letzte App-Konsistenz**: Diese Option führt ein Failover aller virtuellen Computer auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
   - **Benutzerdefiniert**: Wählen Sie einen beliebigen Wiederherstellungspunkt aus.

3. Wählen Sie das virtuelle Azure-Zielnetzwerk aus, in das die Azure-VMs zum Testen der Konfiguration verschoben werden sollen. 

   > [!IMPORTANT]
   > Wir empfehlen, für das Testfailover ein separates Azure-VM-Netzwerk zu verwenden und nicht das Produktionsnetzwerk, das beim Aktivieren der Replikation eingerichtet wurde und in das Sie Ihre virtuellen Computer letztlich verschieben möchten.

4. Klicken Sie zum Testen der Verschiebung auf **OK**. Klicken Sie zum Überwachen des Fortschritts auf die VM, um die Eigenschaften aufzurufen. Alternativ können Sie auf den Auftrag **Testfailover** unter dem Tresornamen und dann auf **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** klicken.
5. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt. Stellen Sie sicher, dass die VM ausgeführt wird, die passende Größe hat und mit dem entsprechenden Netzwerk verbunden ist.
6. Wenn Sie den beim Testen der Verschiebung erstellten virtuellen Computer löschen möchten, klicken Sie im replizierten Element auf **Testfailover bereinigen**. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Test.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Durchführen und Bestätigen der Verschiebung in die Zielregion

1. Navigieren Sie zum Tresor, klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf die VM und dann auf **Failover**.
2. Wählen Sie unter **Failover** die Option **Neueste** aus. 
3. Wählen Sie **Computer vor Beginn des Failovers herunterfahren** aus. Site Recovery versucht, die Quell-VM herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt. 
4. Überprüfen Sie nach Abschluss des Auftrags, ob der virtuelle Computer wie erwartet in der Azure-Zielregion angezeigt wird.
5. Klicken Sie unter **Replizierte Elemente** mit der rechten Maustaste auf die VM und dann auf **Commit**. Dadurch wird die Verschiebung in die Zielregion abgeschlossen. Warten Sie, bis der Commitauftrag abgeschlossen ist.

## <a name="discard-the-resource-in-the-source-region"></a>Verwerfen der Ressourcen in der Quellregion 

- Navigieren Sie zum virtuellen Computer.  Klicken Sie auf **Replikation deaktivieren**.  Dadurch wird der Prozess zum Kopieren der Daten für den virtuellen Computer angehalten.  

   > [!IMPORTANT]
   > Führen Sie diesen Schritt unbedingt aus. Andernfalls werden Ihnen Gebühren für die Azure Site Recovery-Replikation in Rechnung gestellt.

Falls Sie nicht vorhaben, die Quellressourcen wiederzuverwenden, fahren Sie mit den nächsten Schritten fort.

1. Löschen Sie alle relevanten Netzwerkressourcen in der Quellregion, die Sie in Schritt 4 [Vorbereiten der Quell-VMs](#prepare-the-source-vms) aufgelistet haben. 
2. Löschen Sie das entsprechende Speicherkonto in der Quellregion.



## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen virtuellen Azure-Computer in eine andere Azure-Region verschoben. Nun können Sie die Notfallwiederherstellung für den verschobenen virtuellen Azure-Computer konfigurieren.

> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung nach der Migration](azure-to-azure-quickstart.md)