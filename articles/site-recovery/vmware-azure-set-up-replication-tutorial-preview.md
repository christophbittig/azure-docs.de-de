---
title: Einrichten der Notfallwiederherstellung für VMware-VMs in Azure mit Azure Site Recovery – Vorschau
description: Hier erfahren Sie, wie Sie mit Azure Site Recovery für lokale VMware-VMs die Notfallwiederherstellung in Azure einrichten – Vorschau.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: cfed44e293ce95dbe174f1bb92cbfa96669ab551
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071775"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms---preview"></a>Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs – Vorschau

In diesem Artikel wird beschrieben, wie Sie mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) die Replikation für lokale VMware-VMs für die Notfallwiederherstellung in Azure aktivieren – Vorschau.

Informationen zum Einrichten der Notfallwiederherstellung in klassischen Releases von Azure Site Recovery finden Sie [im Tutorial](vmware-azure-tutorial.md).

Dies ist das dritte in einer Reihe von Tutorials zur Einrichtung der Notfallwiederherstellung in Azure für lokale VMware-VMs. Im vorherigen Tutorial haben Sie sich mit der [Vorbereitung der lokalen VMware-Umgebung](vmware-azure-tutorial-prepare-on-premises.md) für die Notfallwiederherstellung in Azure befasst.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten der Quellreplikationseinstellungen
> * Einrichten der Einstellungen des Replikationsziels
> * Aktivieren der Replikation für eine VMware-VM

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in dem Artikel, der im Anleitungsabschnitt des Site Recovery-Inhaltsverzeichnisses angegeben ist.

## <a name="get-started"></a>Erste Schritte

Die Replikation von VMware zu Azure umfasst die folgenden Verfahren:

- Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.
- Navigieren Sie für die ersten Schritte zum [Azure-Vorschauportal](https://aka.ms/rcmcanary). Führen Sie dann die in den folgenden Abschnitten beschriebenen Schritte aus.
- Vorbereiten des Azure-Kontos
- Infrastruktur vorbereiten
- [Erstellen eines Recovery Services-Tresor](./quickstart-create-vault-template.md?tabs=CLI)
- [Bereitstellen einer Azure Site Recovery-Replikationsappliance](deploy-vmware-azure-replication-appliance-preview.md)
- Aktivieren der Replikation

## <a name="prepare-azure-account"></a>Vorbereiten des Azure-Kontos

Zum Erstellen und Registrieren der Azure Site Recovery-Appliance benötigen Sie ein Azure-Konto mit den folgenden Berechtigungen:

- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für das Azure-Abonnement.
- Berechtigungen zum Registrieren von AAD-Apps (Azure Active Directory)
- Berechtigungen vom Typ „Besitzer“ und „Mitwirkender“ sowie Berechtigungen vom Typ „Benutzerzugriffsadministrator“ für das Azure-Abonnement, um einen Schlüsseltresor zu erstellen, der bei der VMware-Migration ohne Agent verwendet wird.

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, arbeiten Sie zum Zwecke der erforderlichen Berechtigungen mit dem Besitzer zusammen.

Verwenden Sie die folgenden Schritte, um die erforderlichen Berechtigungen zuzuweisen:

1. Suchen Sie im Azure-Portal nach **Abonnements**, und wählen Sie unter **Dienste** das Kontrollkästchen **Abonnements** aus, um nach dem erforderlichen Azure-Abonnement zu suchen.

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie den Recovery Services-Tresor erstellt haben.

3. Wählen Sie im Abonnement die Option **Zugriffssteuerung** (IAM) > **Zugriff überprüfen** aus. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.

4. Wählen Sie unter **Rollenzuweisung hinzufügen** die Option **Hinzufügen**, dann die Rolle „Mitwirkender“ oder „Besitzer“ und anschließend das Konto aus. Wählen Sie dann **Speichern** aus.

5. Um die Azure Site Recovery-Replikationsappliance zu registrieren, benötigt Ihr Azure-Konto die Berechtigung zur Registrierung der AAD-Apps.

**Führen Sie die folgenden Schritte aus, um erforderliche Berechtigungen zuzuweisen**:

1. Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Benutzer** > **Benutzereinstellungen**. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf *Ja* festgelegt).

2. Wenn die Einstellungen für die **App-Registrierungen** auf *Nein* festgelegt sind, können Sie beim Mandantenadministrator/globalen Administrator anfordern, Ihnen die erforderliche Berechtigung zuzuweisen. Alternativ kann der Mandantenadministrator/globale Administrator einem Konto die Rolle Anwendungsentwickler zuweisen, um die Registrierung von AAD-Apps zuzulassen.

## <a name="prepare-infrastructure---set-up-azure-site-recovery-replication-appliance"></a>Vorbereiten der Infrastruktur: Einrichten der Azure Site Recovery-Replikationsappliance

Sie müssen eine [Azure Site Recovery-Replikationsappliance in der lokalen Umgebung einrichten](deploy-vmware-azure-replication-appliance-preview.md), um die Kommunikation der Mobilitäts-Agents zu kanalisieren.

![Replikationsappliance](./media/vmware-azure-set-up-replication-tutorial-preview/replication-appliance.png)

## <a name="enable-replication-of-vmware-vms"></a>Aktivieren der Replikation von virtuellen VMware-Computern

Nachdem eine Azure Site Recovery-Replikationsappliance einem Tresor hinzugefügt wurde, können Sie mit dem Schützen der Computer beginnen.

Stellen Sie sicher, dass die [Voraussetzungen](vmware-physical-azure-support-matrix.md) für Speicher und Netzwerk erfüllt sind.

Gehen Sie folgendermaßen vor, um die Replikation zu aktivieren:

1. Wählen Sie **Site Recovery** im Abschnitt **Erste Schritte** aus. Klicken Sie unter dem Abschnitt „VMware“ auf **Replikation aktivieren (Vorschau)** .

2. Wählen Sie den Computertyp aus, den Sie über Azure Site Recovery schützen möchten.

   > [!NOTE]
   > In der Vorschauversion ist die Unterstützung auf virtuelle Computer beschränkt.

   ![Auswählen von Quellcomputern](./media/vmware-azure-set-up-replication-tutorial-preview/select-source.png)

3. Nachdem Sie die virtuellen Computer ausgewählt haben, wählen Sie den vCenter-Server aus, der der Azure Site Recovery-Replikationsappliance hinzugefügt wurde, die in diesem Tresor registriert ist.

4. Suchen Sie später den Namen der Quell-VM, um die Computer Ihrer Wahl zu schützen. Wählen Sie **Ausgewählte Ressourcen** aus, um die ausgewählten virtuellen Computer zu überprüfen.

5. Nachdem Sie die Liste der virtuellen Computer ausgewählt haben, wählen Sie **Weiter** aus, um mit den Quelleinstellungen fortzufahren. Wählen Sie hier die Replikationsappliance und die VM-Anmeldeinformationen aus. Diese Anmeldeinformationen werden zum Pushen des Mobilitäts-Agents auf dem virtuellen Computer durch den Konfigurationsserver verwendet, um die Aktivierung von Azure Site Recovery abzuschließen. Stellen Sie sicher, dass die richtigen Anmeldeinformationen ausgewählt werden.

   >[!NOTE]
   >Stellen Sie für das Linux-Betriebssystem sicher, dass Sie die Stammanmeldeinformationen angeben. Für das Windows-Betriebssystem sollte ein Benutzerkonto mit Administratorrechten hinzugefügt werden. Diese Anmeldeinformationen werden verwendet, um den Mobilitätsdienst während der Aktivierung der Replikation auf den Quellcomputer zu pushen.

   ![Quelleinstellungen](./media/vmware-azure-set-up-replication-tutorial-preview/source-settings.png)

6. Wählen Sie **Weiter** aus, um die Eigenschaften der Zielregion bereitstellen. Standardmäßig sind Tresorabonnement und Tresorressourcengruppe ausgewählt. Sie können ein Abonnement und eine Ressourcengruppe Ihrer Wahl auswählen. Ihre Computer werden in diesem Abonnement und in dieser Ressourcengruppe bereitgestellt, wenn Sie in Zukunft ein Failover ausführen.

   ![Zieleigenschaften](./media/vmware-azure-set-up-replication-tutorial-preview/target-properties.png)

7. Als nächstes können Sie ein vorhandenes Azure-Netzwerk auswählen oder ein neues Zielnetzwerk erstellen, das während des Failovers verwendet werden soll. Wenn Sie **Neu erstellen** auswählen, werden Sie zum Kontextblatt zum Erstellen des virtuellen Netzwerks umgeleitet und aufgefordert, Informationen zum Adressraum und Subnetz bereitzustellen. Dieses Netzwerk wird in dem Zielabonnement und der Zielressourcengruppe erstellt, die Sie im vorherigen Schritt ausgewählt haben.

8. Geben Sie dann die Details für das Testfailovernetzwerk an.

   > [!NOTE]
   > Stellen Sie sicher, dass sich das Testfailovernetzwerk vom Failovernetzwerk unterscheidet. Dadurch wird sichergestellt, dass das Failovernetzwerk im Falle eines tatsächlichen Notfalls sofort verfügbar ist.

9. Wählen Sie den Speicher aus.

    - Cachespeicherkonto: Wählen Sie jetzt das Cachespeicherkonto aus, das von Azure Site Recovery für Stagingzwecke verwendet wird – Zum Zwischenspeichern und Speichern von Protokollen, bevor die Änderungen auf die verwalteten Datenträger geschrieben werden.

      Standardmäßig wird von Azure Site Recovery ein neues Speicherkonto vom Typ „LRS v1“ für den ersten aktivierten Replikationsvorgang in einem Tresor erstellt. Für die nächsten Vorgänge wird dasselbe Cachespeicherkonto wiederverwendet.
    -  Verwaltete Datenträger

       Standardmäßig werden verwaltete HDD Standard-Datenträger in Azure erstellt. Sie können den Typ der verwalteten Datenträger anpassen, indem Sie **Anpassen** auswählen. Wählen Sie den Datenträgertyp basierend auf den geschäftlichen Anforderungen aus. Stellen Sie sicher, dass basierend auf den IOPS der Datenträger des Quellcomputers ein [geeigneter Datenträgertyp ausgewählt](../virtual-machines/disks-types.md#disk-type-comparison) wird. Preisinformationen finden Sie [hier](https://azure.microsoft.com/pricing/details/managed-disks/) in der Preisübersicht für verwaltete Datenträger.

       >[!NOTE]
       > Wenn der Mobilitätsdienst vor dem Aktivieren der Replikation manuell installiert wird, können Sie den Typ des verwalteten Datenträgers auf Datenträgerebene ändern. Andernfalls kann standardmäßig ein verwalteter Datenträgertyp auf Computerebene ausgewählt werden.

10. Erstellen Sie bei Bedarf eine neue Replikationsrichtlinie.

     Unter dem Tresor wird eine Standardreplikationsrichtlinie mit einer Wiederherstellungspunktaufbewahrung von 72 Stunden und einer App-Konsistenzhäufigkeit von vier Stunden erstellt.  Sie können eine neue Replikationsrichtlinie erstellen, die Ihren RPO-Anforderungen entspricht.

     - Wählen Sie **Neu erstellen**.

     - Geben Sie den Namen ein.

     - Geben Sie die **Aufbewahrung des Wiederherstellungspunkts** in Stunden ein.

     - Wählen Sie **App-konsistente Momentaufnahmehäufigkeit in Stunden** entsprechend den geschäftlichen Anforderungen aus.

     - Wählen Sie **OK** aus, um die Richtlinie zu speichern.

     Die Richtlinie wird erstellt und kann zum Schutz der ausgewählten Quellcomputer verwendet werden.

11. Nachdem Sie die Replikationsrichtlinie ausgewählt haben, wählen Sie **Weiter** aus. Überprüfen Sie die Eigenschaften von Quelle und Ziel. Wählen Sie **Replikation aktivieren** aus, um den Vorgang zu starten.

    ![Standortwiederherstellung](./media/vmware-azure-set-up-replication-tutorial-preview/enable-replication.png)

    Es wird ein Auftrag erstellt, um die Replikation der ausgewählten Computer zu aktivieren. Navigieren Sie zum Nachverfolgen des Fortschritts zu Site Recovery-Aufträgen im Recovery Services-Tresor.


## <a name="next-steps"></a>Nächste Schritte
Führen Sie nach dem Aktivieren der Replikation eine Übung durch, um sicherzustellen, dass alles wie erwartet funktioniert.
> [!div class="nextstepaction"]
> [Durchführen eines Notfallwiederherstellungsverfahrens](site-recovery-test-failover-to-azure.md)