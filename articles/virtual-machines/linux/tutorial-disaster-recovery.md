---
title: 'Tutorial: Einrichten der Notfallwiederherstellung für Linux-VMs mit Azure Site Recovery'
description: Erfahren Sie, wie Sie die Notfallwiederherstellung für Linux-VMs in eine andere Region mit dem Dienst „Azure Site Recovery“ durchführen.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ff3c4dc893a6760c317b39b4a76f4de2e24da96e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690903"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>Tutorial: Einrichten der Notfallwiederherstellung für virtuelle Linux-Computer

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Tutorial wird veranschaulicht, wie Sie die Notfallwiederherstellung für virtuelle Azure-Computer einrichten, auf denen Linux ausgeführt wird. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Aktivieren der Notfallwiederherstellung für einen virtuellen Linux-Computer
> * Ausführen eines Notfallwiederherstellungsverfahrens, um zu überprüfen, ob es erwartungsgemäß funktioniert
> * Beenden der Replikation des virtuellen Computers nach dem Übungslauf

Wenn Sie die Replikation für eine VM aktivieren, wird darauf die Site Recovery Mobility Service-Erweiterung installiert und die Registrierung bei [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) durchgeführt. Während der Replikation werden Schreibvorgänge des VM-Datenträgers an ein Cachespeicherkonto in der VM-Quellregion gesendet. Daten werden von dort aus in die Zielregion gesendet, und aus den Daten werden Wiederherstellungspunkte generiert.  Wenn Sie während der Notfallwiederherstellung ein Failover für einen virtuellen Computer in eine andere Region ausführen, wird ein Wiederherstellungspunkt genutzt, um den virtuellen Computer in der Zielregion wiederherzustellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

1. Überprüfen Sie, ob Ihr Azure-Abonnement das Erstellen einer VM in der Zielregion zulässt. Wenn Sie gerade Ihr kostenloses Azure-Konto erstellt haben, sind Sie der Administrator des Abonnements und besitzen die erforderlichen Berechtigungen.
2. Falls Sie nicht der Abonnementadministrator sind, können Sie sich an den Administrator wenden und ihn bitten, Ihnen Folgendes zuzuweisen:
    - Entweder die integrierte Rolle „Mitwirkender für virtuelle Computer“ oder spezifische Berechtigungen der folgenden Art:
        - Erstellen einer VM im ausgewählten virtuellen Netzwerk
        - Schreiben in ein Azure-Speicherkonto
        - Schreiben auf einen verwalteten Azure-Datenträger
     - Integrierte Rolle „Site Recovery-Mitwirkender“ zum Verwalten von Site Recovery-Vorgängen im Tresor. 
3. Überprüfen Sie, ob auf der Linux-VM ein [unterstütztes Betriebssystem](../../site-recovery/azure-to-azure-support-matrix.md#linux) ausgeführt wird.
4. Falls für ausgehende Verbindungen der VM ein URL-basierter Proxy verwendet wird, sollten Sie sicherstellen, dass darüber auf diese URLs zugegriffen werden kann. Die Verwendung eines authentifizierten Proxys wird nicht unterstützt.

    **Name** | **Öffentliche Cloud** | **Government Cloud** | **Details**
    --- | --- | --- | ---
    Storage | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Schreiben von Daten von der VM in das Cachespeicherkonto in der Quellregion 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Durchführen der Autorisierung und Authentifizierung für Site Recovery-Dienst-URLs 
    Replikation | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |VM-Kommunikation mit dem Site Recovery-Dienst 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM-Schreibvorgänge für Site Recovery für Überwachungs- und Diagnosedaten 

4. Erstellen Sie bei Verwendung von Netzwerksicherheitsgruppen (NSGs) zum Beschränken des Netzwerkdatenverkehrs für VMs NSG-Regeln, die die Konnektivität in ausgehender Richtung (HTTPS 443) für die VM mit diesen Diensttags (Gruppe mit IP-Adressen) zulassen. Probieren Sie die Regeln zunächst mit einer Test-Netzwerksicherheitsgruppe aus.

    **Tag** | **Zulassen** 
    --- | --- 
    Storage-Tag | Ermöglicht das Schreiben von Daten von der VM in das Cachespeicherkonto.
    Azure AD-Tag | Ermöglicht den Zugriff auf alle IP-Adressen, die zu Azure AD gehören.
    EventsHub-Tag | Ermöglicht den Zugriff auf die Site Recovery-Überwachung.
    AzureSiteRecovery-Tag | Ermöglicht den Zugriff auf den Site Recovery-Dienst in beliebigen Regionen.
    GuestAndHybridManagement | Ermöglicht die Durchführung von automatischen Upgrades des Mobilitäts-Agents von Site Recovery, der auf VMs mit Aktivierung für die Replikation ausgeführt wird.
5. Stellen Sie sicher, dass die VMs über die aktuellen Stammzertifikate verfügen. Befolgen Sie auf virtuellen Linux-Computern die Anleitung Ihres Linux-Distributors, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatsperrliste für den virtuellen Computer abzurufen.

## <a name="create-a-vm-and-enable-disaster-recovery"></a>Erstellen eines virtuellen Computers und Aktivieren der Notfallwiederherstellung

Optional können Sie bei der Erstellung eines virtuellen Computers die Notfallwiederherstellung aktivieren.

1. [Erstellen Sie einen virtuellen Linux-Computer.](quick-create-portal.md)
2. Wählen Sie auf der Registerkarte **Verwaltung** unter **Site Recovery** die Option **Notfallwiederherstellung aktivieren** aus.
3. Wählen Sie unter **Sekundäre Region** die Zielregion aus, in der Sie den virtuellen Computer zur Notfallwiederherstellung replizieren möchten.
4. Wählen Sie unter **Sekundäres Abonnement** das Zielabonnement aus, in dem der virtuelle Zielcomputer erstellt wird. Der virtuelle Zielcomputer wird erstellt, wenn Sie ein Failover der Quell-VM aus der Quellregion in die Zielregion durchführen.
5. Wählen Sie in **Recovery Services** Tresor den Tresor aus, den Sie für die Replikation verwenden möchten. Wenn Sie über keinen Tresor verfügen, klicken Sie auf **Neu erstellen**. Wählen Sie eine Ressourcengruppe aus, in der der Tresor platziert werden soll, und einen Tresor Namen.
6. Überlassen Sie in **Site Recovery Richtlinie** die Standard Richtlinie, oder wählen Sie **neu erstellen** aus, um benutzerdefinierte Werte festzulegen.

    - Wiederherstellungspunkte werden aus Momentaufnahmen von VM-Datenträgern zu einem bestimmten Zeitpunkt erstellt. Wenn Sie ein Failover eines VM ausführen, verwenden Sie einen Wiederherstellungspunkt, um die VM am Zielstandort wiederherzustellen. 
    - Alle fünf Minuten wird ein Absturz einheitlicher Wiederherstellungspunkt erstellt. Diese Einstellung kann nicht geändert werden. Eine absturzkonsistente Momentaufnahme erfasst Daten, die sich zum Zeitpunkt der Erstellung der Momentaufnahme auf dem Datenträger befunden haben. Sie enthält keine Daten aus dem Arbeitsspeicher. 
    - Standardmäßig behält Site Recovery Wiederherstellungspunkte 24 Stunden lang bei. Sie können einen benutzerdefinierten Wert zwischen 0 und 72 Stunden festlegen.
    - Alle vier Stunden wird eine anwendungskonsistente Momentaufnahme erstellt.
    - Wiederherstellungspunkte werden von Site Recovery standardmäßig 24 Stunden lang gespeichert.

7. Geben Sie unter **Verfügbarkeitsoptionen** an, ob der virtuelle Computer eigenständig, in einer Verfügbarkeitszone oder in einer Verfügbarkeitsgruppe bereitgestellt werden soll.

    :::image type="content" source="./media/tutorial-disaster-recovery/create-vm.png" alt-text="Aktivieren der Replikation auf der Seite mit den VM-Verwaltungseigenschaften":::

8. Schließen Sie die Erstellung des virtuellen Computers ab.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>Aktivieren der Notfallwiederherstellung für einen vorhandenen virtuellen Computer

Wenn Sie die Notfallwiederherstellung für einen vorhandenen virtuellen Computer aktivieren möchten, verwenden Sie dieses Verfahren.

1. Öffnen Sie im Azure-Portal die Seite mit den VM-Eigenschaften.
2. Wählen Sie unter **Vorgänge** die Option **Notfallwiederherstellung** aus.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="Öffnen Sie Notfallwiederherstellungsoptionen für einen vorhandenen VM":::

3. Wenn der VM in einer Verfügbarkeits Zone bereitgestellt wird, können Sie unter **Grundlagen** die Notfallwiederherstellung zwischen Verfügbarkeits Zonen auswählen.
4. Wählen Sie unter **Zielregion** die Region aus, in der Sie die VM replizieren möchten. Die Quell- und Zielregionen müssen sich jeweils auf demselben Azure Active Directory-Mandanten befinden.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="Legen Sie die grundlegenden Notfallwiederherstellungsoptionen für eine VM fest.":::

5. Klicken Sie auf **Weiter: Erweiterte Einstellungen**.
6. Unter **Erweiterte Einstellungen** können Sie Einstellungen überprüfen und Werte in benutzerdefinierte Einstellungen ändern. Standardmäßig werden die Quelleinstellungen von Site Recovery gespiegelt, um die Zielressourcen zu erstellen.

    - **Zielabonnement**. Das Abonnement, in dem die Ziel-VM nach einem Failover erstellt wird.
    - **Azure-VM-Ziel-Ressourcengruppe**. Die Ressourcengruppe in der die Ziel-VM nach einem Failover erstellt wird.
    - **Virtuelles Zielnetzwerk:** Das virtuelle Azure-Netzwerk, in dem sich die Ziel-VM befindet, wenn Sie nach einem Failover erstellt wird.
    - **Zielverfügbarkeit**. Wenn die Ziel-VM als einzelne Instanz, in einer Verfügbarkeits Gruppe oder in einer Verfügbarkeits Zone erstellt wird.
    - **Näherungsplatzierungsgruppe**. Wählen Sie ggf. die Näherungsplatzierungsgruppe aus, in der sich die Ziel-VM nach einem Failover befindet.
    - **Speichereinstellungen – Cachespeicherkonto**: Bei der Wiederherstellung wird ein Speicherkonto in der Quellregion als temporärer Datenspeicher verwendet. Änderungen der Quell-VM werden unter diesem Konto zwischengespeichert, bevor sie am Zielort repliziert werden.
        - Standardmäßig wird ein Cache Speicherkonto pro Tresor erstellt und wieder verwendet.
        - Wenn Sie das Cache Konto für das VM anpassen möchten, können Sie ein anderes Speicherkonto auswählen.
    - **Speichereinstellungen – Replikat verwalteter Datenträgerdatenträger**. Site Recovery erstellt standardmäßig in der Zielregion ein Replikat verwalteter Datenträger.
        -  Standardmäßig spiegelt der verwaltete Ziel Datenträger die verwalteten Datenträger der Quell-VM mit demselben Speichertyp (Standard HDD/SSD oder Premium SSD) wider.
        - Sie können den Speichertyp nach Bedarf anpassen.
    - **Replikationseinstellungen**: Zeigt den Tresor an, in dem sich der VM befindet, und die für den virtuellen Computer verwendete Replikations Richtlinie. Standardmäßig werden Wiederherstellungspunkte, die von Site Recovery für die VM erstellt werden, 24 Stunden lang aufbewahrt.
    - **Erweiterungseinstellungen**: Hier wird angegeben, dass Site Recovery die Updates für die Site Recovery Mobility Service-Erweiterung verwaltet, die auf den von Ihnen replizierten VMs installiert ist.
        - Mit dem angegebenen Azure Automation-Konto wird der Updateprozess verwaltet.
        - Sie können das Automation-Konto anpassen.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Seite mit einer Zusammenfassung der Ziel- und Replikationseinstellungen":::

6. Wählen Sie **Replikation überprüfen und starten** aus.

7. Wählen Sie **Replikation überprüfen und starten** aus. Die Bereitstellung wird gestartet, und Site Recovery beginnt mit dem Erstellen von Zielressourcen. Sie können den Fortschrittsstatus der Replikation in den Benachrichtigungen überwachen.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Benachrichtigung zum Status der Replikation":::

## <a name="check-vm-status"></a>Überprüfen des VM-Status

Nachdem der Replikationsauftrag abgeschlossen ist, können Sie den Status der VM-Replikation überprüfen.

1. Öffnen Sie die Seite mit den VM-Eigenschaften.
2. Wählen Sie unter **Vorgänge** die Option **Notfallwiederherstellung** aus.
3. Erweitern Sie den Abschnitt **Grundlagen**, um die Standardeinstellungen für den Tresor, die Replikationsrichtlinie und die Zieleinstellungen anzuzeigen.
4. Rufen Sie unter **Integrität und Status** die Informationen zum Replikationszustand für die VM, zur Agent-Version, Failoverbereitschaft und zu den letzten Wiederherstellungspunkten ab. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Ansicht „Grundlagen“ für die VM-Notfallwiederherstellung":::

5. Rufen Sie in der **Infrastrukturansicht** eine grafische Übersicht über die Quell- und Ziel-VMs, die verwalteten Datenträger und das Cachespeicherkonto ab.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="Grafische Infrastrukturübersicht für VM-Notfallwiederherstellung":::

## <a name="run-a-drill"></a>Durchführen einer Übung

Führen Sie einen Übungslauf durch, um sicherzustellen, dass die Notfallwiederherstellung wie erwartet funktioniert. Beim Ausführen eines Testfailovers wird eine Kopie der VM erstellt, ohne dass sich dies auf laufende Replikationsprozesse oder Ihre Produktionsumgebung auswirkt.

1. Wählen Sie auf der Seite für die VM-Notfallwiederherstellung die Option **Testfailover** aus.
2. Übernehmen Sie unter **Testfailover** die Standardeinstellung **Letzte Verarbeitung (niedrigste RPO)** für den Wiederherstellungspunkt.

   Diese Option bietet das niedrigste Recovery Point Objective (RPO) und startet die VM in der Regel am schnellsten in der Zielregion. Hierbei werden zuerst alle Daten verarbeitet, die an den Site Recovery-Dienst gesendet wurden, um vor dem Failover einen Wiederherstellungspunkt für jede VM zu erstellen. Bei diesem Wiederherstellungspunkt sind alle Daten in Site Recovery repliziert, wenn das Failover ausgelöst wird.

3. Wählen Sie das virtuelle Netzwerk aus, in dem sich die VM nach dem Failover befinden soll. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Seite zum Festlegen der Optionen für das Testfailover":::

4. Der Prozess für das Testfailover beginnt. Sie können den Fortschrittsstatus über die Benachrichtigungen überwachen.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Benachrichtigungen zum Testfailover"::: 
    
   Nach Abschluss des Testfailovers befindet sich die VM auf der Seite **Grundlagen** im Status *Bereinigung des Testfailovers steht aus*. 
  
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Die VM wird von Site Recovery nach dem Übungslauf automatisch bereinigt. 
 
1. Wählen Sie die Option **Testfailover bereinigen** aus, um die automatische Bereinigung zu starten.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Starten der Bereinigung auf der Seite „Grundlagen“"::: 

6. Geben Sie unter **Testfailoverbereinigung** die Hinweise ein, die Sie für das Failover angeben möchten, und wählen Sie anschließend **Die Tests sind abgeschlossen. Löschen Sie die virtuellen Computer für das Testfailover.** aus. Klicken Sie anschließend auf **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Seite zum Eingeben von Hinweisen und Löschen der Test-VM"::: 

7. Der Löschvorgang beginnt. Sie können den Fortschrittsstatus über die Benachrichtigungen überwachen.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Benachrichtigungen zum Überwachen des Löschvorgangs für die Test-VM"::: 


### <a name="stop-replicating-the-vm"></a>Beenden der VM-Replikation

Wir schlagen vor, dass Sie nach Abschluss eines Übungslaufs für die Notfallwiederherstellung testen, ob ein vollständiges Failover ausgeführt werden kann. Falls Sie kein vollständiges Failover durchführen möchten, können Sie die Replikation deaktivieren. Die folgenden Schritte werden ausgeführt:

- Entfernen der VM aus der Site Recovery-Liste mit den replizierten Computern
- Beenden der Site Recovery-Abrechnung für die VM
- Automatisches Bereinigen der Quellreplikationseinstellungen

Beenden Sie die Replikation wie folgt:

1. Wählen Sie auf der Seite für die VM-Notfallwiederherstellung die Option **Replikation deaktivieren** aus.
2. Wählen Sie unter **Replikation deaktivieren** die Gründe aus, aus denen Sie die Replikation deaktivieren möchten. Klicken Sie anschließend auf **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Seite zum Deaktivieren der Replikation und Angeben eines Grunds"::: 


Die Site Recovery-Erweiterung, die während der Replikation auf der VM installiert wird, wird nicht automatisch entfernt. Wenn Sie die Replikation für die VM deaktivieren und nicht zu einem späteren Zeitpunkt noch einmal durchführen möchten, können Sie die Site Recovery-Erweiterung wie folgt manuell entfernen: 

1. Navigieren Sie zu „VM“ > **Einstellungen** > **Erweiterungen**.
2. Wählen Sie auf der Seite **Erweiterungen** alle *Microsoft.Azure.RecoveryServices*-Einträge für Linux aus.
3. Wählen Sie auf der Eigenschaftenseite für die Erweiterung die Option **Deinstallieren** aus.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Notfallwiederherstellung für eine Azure-VM konfiguriert und einen Übungslauf für die Notfallwiederherstellung durchgeführt. Nun können Sie ein vollständiges Failover für die VM ausführen.

> [!div class="nextstepaction"]
> [Ausführen eines Failovers für einen virtuellen Computers auf eine andere Region](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
