---
title: Einrichten der Hyper-V-Notfallwiederherstellung (mit VMM) mit Azure Site Recovery
description: Erfahren Sie, wie Sie die Notfallwiederherstellung für lokale Hyper-V-VMs in System Center VMM-Clouds nach Azure mit Site Recovery einrichten.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: 97cd0a103dab9b46205355da223bd65259f93fee
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055886"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Einrichten der Notfallwiederherstellung von lokalen Hyper-V-VMs in VMM-Clouds nach Azure

In diesem Tutorial erfahren Sie, wie Sie die Replikation lokaler virtueller Hyper-V-Computer, die von System Center Virtual Machine Manager (VMM) verwaltet werden, mithilfe von [Azure Site Recovery](site-recovery-overview.md) in Azure replizieren. Wenn Sie VMM nicht verwenden, finden Sie entsprechende Informationen in [diesem Tutorial](hyper-v-azure-tutorial.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Wählen Sie die Replikationsquelle und das Replikationsziel aus.
> * Richten Sie die Quellreplikationsumgebung ein, einschließlich der lokalen Site Recovery-Komponenten und der Zielreplikationsumgebung.
> * Richten Sie Netzwerkzuordnungen zwischen VMM-VM-Netzwerken und virtuellen Azure-Netzwerken ein.
> * Erstellen einer Replikationsrichtlinie
> * Aktivieren der Replikation für eine VM

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in den Artikeln im Abschnitt **Schrittanleitungen** der [Dokumentation zu Site Recovery](./index.yml).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird davon ausgegangen, dass Sie bereits die folgenden Tutorials abgeschlossen haben:

1. [Vorbereiten von Azure](tutorial-prepare-azure.md)
1. [Vorbereiten lokaler Hyper-V-Server](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Auswählen eines Replikationsziels

1. Navigieren Sie im Azure-Portal zu **Recovery Services-Tresore**, und wählen Sie den Tresor **ContosoVMVault** aus, der im Tutorial [Vorbereiten von Azure für die lokale Notfallwiederherstellung in Azure](tutorial-prepare-azure.md#create-a-recovery-services-vault) erstellt wurde.
1. Wählen Sie unter **Erste Schritte** die Optionen **Site Recovery** > **Infrastruktur vorbereiten** aus, und konfigurieren Sie die folgenden Einstellungen:
    1. Wählen Sie unter **Schutzziel** > **Wo befinden sich Ihre Computer?** die Option **Lokal** aus.
    1. Wählen Sie unter **Wohin möchten Sie Ihre Computer replizieren?** die Option **Nach Azure** aus.
    1. Wählen Sie unter **Sind Ihre Computer virtualisiert?** die Option **Ja, mit Hyper-V** aus.
    1. Wählen Sie unter **Are you using System Center VMM to manage your Hyper-V hosts?** (Verwenden Sie System Center VMM für die Verwaltung Ihrer Hyper-V-Hosts?) die Option **Ja** aus.
1. Klicken Sie auf **OK**.

   ![Replikationsziel](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bestätigen der Bereitstellungsplanung

1. Falls Sie eine umfangreiche Bereitstellung planen, laden Sie unter **Bereitstellungsplanung** über den auf der Seite bereitgestellten Link den Bereitstellungsplaner für Hyper-V herunter. Weitere Informationen zur Hyper-V-Bereitstellungsplanung finden Sie [hier](hyper-v-deployment-planner-overview.md).
1. In diesem Tutorial wird der Bereitstellungsplaner nicht benötigt. Wählen Sie unter **Haben Sie die Bereitstellungsplanung abgeschlossen?** die Option **Wird später durchgeführt** und dann **OK** aus.

## <a name="set-up-the-source-environment"></a>Einrichten der Quellumgebung

Beim Einrichten der Quellumgebung installieren Sie den Azure Site Recovery-Anbieter auf dem VMM-Server und registrieren den Server im Tresor. Sie installieren den Azure Recovery Services-Agent auf den einzelnen Hyper-V-Hosts.

1. **Infrastruktur vorbereiten**: Wählen Sie **Quelle** aus.
1. **Quelle vorbereiten**: Wählen Sie die Option **+ VMM** aus, um einen VMM-Server hinzuzufügen. Überprüfen Sie unter **Server hinzufügen**, ob unter **Servertyp** die Option **System Center VMM-Server** angezeigt wird.
1. Laden Sie das Installationsprogramm für den Microsoft Azure Site Recovery-Anbieter herunter.
1. Laden Sie den Tresorregistrierungsschlüssel herunter. Sie benötigen diesen Schlüssel, wenn Sie das Anbieter-Setup ausführen. Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.
1. Laden Sie das Installationsprogramm für den Microsoft Azure Recovery Services-Agent herunter.

   ![Herunterladen von Anbieter, Registrierungsschlüssel und Agent](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installieren des Anbieters auf dem VMM-Server

1. Wählen Sie im Setup-Assistenten für Azure Site Recovery-Anbieter die Option **Microsoft Update** aus. Registrieren Sie sich für die Verwendung von Microsoft Update, um nach Anbieterupdates zu suchen.
1. **Installation**: Akzeptieren Sie den standardmäßigen Installationsspeicherort für den Anbieter, und wählen Sie **Installieren** aus.
1. Wählen Sie nach der Installation im Microsoft Azure Site Recovery-Registrierungs-Assistenten die Optionen **Tresoreinstellungen** > **Durchsuchen** und unter **Schlüsseldatei** die Tresorschlüsseldatei aus, die Sie heruntergeladen haben.
1. Geben Sie das Azure Site Recovery-Abonnement und den Tresornamen (**ContosoVMVault**) an. Geben Sie einen Anzeigenamen für den VMM-Server an, mit dem er im Tresor identifiziert werden soll.
1. **Proxyeinstellungen**: Wählen Sie **Direkt mit Azure Site Recovery verbinden (ohne Proxyserver)** aus.
1. Akzeptieren Sie den Standardspeicherort für das Zertifikat, mit dem Daten verschlüsselt werden. Wenn Sie ein Failover ausführen, werden verschlüsselte Daten entschlüsselt.
1. **Cloudmetadaten synchronisieren**: Wählen Sie **Die Cloud-Metadaten sollen mit dem Site Recovery-Portal synchronisiert werden.** aus. Diese Aktion muss für jeden Server nur einmal ausgeführt werden. Wählen Sie dann **Registrieren**.
1. Nachdem der Server im Tresor registriert wurde, wählen Sie **Fertig stellen** aus.

Nach der Registrierung ruft Azure Site Recovery Metadaten vom Server ab, und der VMM-Server wird unter **Site Recovery-Infrastruktur** angezeigt.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installieren des Recovery Services-Agent auf den Hyper-V-Hosts

Installieren Sie den Agent auf jedem Hyper-V-Host, auf dem sich virtuelle Computer befinden, die Sie replizieren möchten.

Konfigurieren Sie im Setup-Assistenten für den Microsoft Azure Recovery Services-Agent die folgenden Einstellungen:

1. **Voraussetzungsüberprüfung**: Wählen Sie **Weiter** aus. Alle fehlenden Komponenten werden automatisch installiert.
1. **Installationseinstellungen**: Akzeptieren Sie den Installationsspeicherort. Wählen Sie dann **Installieren** aus.

    >[!NOTE]
    >Der **Cachespeicherort** ist für Azure Site Recovery nicht erforderlich.

1. **Installation**: Wählen Sie nach Abschluss der Installation **Schließen** aus, um den Assistenten zu beenden.

   ![Agent installieren](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

### <a name="install-the-recovery-services-agent-on-windows-core-hyper-v-hosts"></a>Installieren des Recovery Services-Agents auf den Windows Core-Hyper-V-Hosts

Installieren Sie den Agent auf jedem Windows Core-Hyper-V-Host, auf dem sich virtuelle Computer befinden, die Sie replizieren möchten.

1. Erstellen Sie auf dem Windows Core-Hyper-V-Hosts ein Verzeichnis, indem Sie den folgenden Befehl ausführen:

   ```powershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

2. Laden Sie das Installationsprogramm für den Microsoft Azure Recovery Services-Agent herunter.

   ```powershell
   Invoke-WebRequest -Uri <put the URI here> -OutFile .\ASR\MARSsoftware.exe
   ```
   
3. Führen Sie das Installationsprogramm mithilfe des folgenden Befehls aus:

   ```powershell
   .\MARSsoftware.exe
   ```

4. Sobald die Installation des Microsoft Azure Recovery Services-Agents abgeschlossen ist, können Sie die Konsole des Assistenten schließen.

## <a name="set-up-the-target-environment"></a>Einrichten der Zielumgebung

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Ziel**.
1. Wählen Sie das Abonnement und die Ressourcengruppe (**ContosoRG**) aus, in denen Sie die Azure-VMs nach dem Failover erstellen möchten.
1. Wählen Sie das Bereitstellungsmodell **Resource Manager** aus.

Site Recovery prüft, ob ein oder mehrere kompatible Azure-Speicherkonten und -Netzwerke vorhanden sind.

## <a name="configure-network-mapping"></a>Konfigurieren der Netzwerkzuordnung

1. **Site Recovery-Infrastruktur** > **Netzwerkzuordnungen** > **Netzwerkzuordnung**: Wählen Sie das Symbol **+Netzwerkzuordnung** aus.
1. **Netzwerkzuordnung hinzufügen**: Wählen Sie den Server **Source System Center VMM** (System Center-Quell-VMM) aus. Wählen Sie „Azure“ unter **Ziel** aus.
1. Überprüfen Sie das Abonnement und das Bereitstellungsmodell nach einem Failover.
1. **Quellnetzwerk**: Wählen Sie das lokale VM-Quellnetzwerk aus.
1. **Zielnetzwerk**: Wählen Sie das Azure-Netzwerk aus, in dem Replikate von Azure-VMs nach der Erstellung im Anschluss an ein Failover platziert werden. Wählen Sie anschließend **OK** aus.

   ![Netzwerkzuordnung](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Einrichten einer Replikationsrichtlinie

1. Wählen Sie **Infrastruktur vorbereiten** > **Replikationseinstellungen** >  **+Erstellen und zuordnen** aus.
1. Geben Sie unter **Richtlinie erstellen und zuordnen** einen Richtliniennamen an. Hier wird **ContosoReplicationPolicy** verwendet.
1. Übernehmen Sie die Standardeinstellungen, und wählen Sie **OK** aus:
   - **Kopierhäufigkeit** gibt an, dass Deltadaten nach der ersten Replikation alle fünf Minuten repliziert werden.
   - **Aufbewahrungszeitraum des Wiederherstellungspunkts** gibt an, dass jeder Wiederherstellungspunkt zwei Stunden lang aufbewahrt wird.
   - **App-konsistente Momentaufnahmehäufigkeit** gibt an, dass Wiederherstellungspunkte mit anwendungskonsistenten Momentaufnahmen jede Stunde erstellt werden sollen.
   - **Startzeit der ersten Replikation** gibt an, dass die erste Replikation sofort beginnen soll.
   - **In Azure gespeicherte Daten verschlüsseln:** Die Standardeinstellung **Deaktiviert** gibt an, dass ruhende Daten in Azure nicht verschlüsselt werden.
1. Nachdem die Richtlinie erstellt wurde, wählen Sie **OK** aus. Wenn Sie eine neue Richtlinie erstellen, wird sie der VMM-Cloud automatisch zugeordnet.

## <a name="enable-replication"></a>Aktivieren der Replikation

1. **Anwendung replizieren**: Wählen Sie **Quelle** aus.
1. **Source** (Quelle): Wählen Sie die VMM-Cloud aus. Wählen Sie anschließend **OK** aus.
1. **Ziel**: Überprüfen Sie das Ziel (Azure) und das Tresorabonnement, und wählen Sie das **Resource Manager**-Modell aus.
1. Wählen Sie das Speicherkonto **contosovmsacct1910171607** und das Azure-Netzwerk **ContosoASRnet** aus.
1. **Virtuelle Computer** > **Auswählen**: Wählen Sie den virtuellen Computer aus, den Sie replizieren möchten. Wählen Sie anschließend **OK** aus.

   Sie können den Fortschritt der Aktion **Schutz aktivieren** unter **Aufträge** > **Site Recovery-Aufträge** verfolgen. Nach der Ausführung des Auftrags **Schutz abschließen** ist die erste Replikation abgeschlossen und die VM bereit zum Failover.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Durchführen eines Notfallwiederherstellungsverfahrens](tutorial-dr-drill-azure.md)
