---
title: Aktivieren von „VMs außerhalb der Geschäftszeiten starten/beenden“ in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie die Funktion „VMs außerhalb der Geschäftszeiten starten/beenden“ für Ihre Azure-VMs aktivieren.
services: automation
ms.subservice: process-automation
ms.date: 05/18/2021
ms.topic: conceptual
ms.openlocfilehash: 70b58b689798b6651a88674668497a27a2780429
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450930"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Aktivieren von „VMs außerhalb der Geschäftszeiten starten/beenden“

Führen Sie die Schritte in der vorliegenden Reihenfolge in diesem Thema aus, um die Funktion „VMs außerhalb der Geschäftszeiten starten/beenden“ für VMs mithilfe eines neuen oder vorhandenen Automation-Kontos sowie eines verknüpften Log Analytics-Arbeitsbereichs zu aktivieren. Nachdem Sie den Einrichtungsprozess abgeschlossen haben, konfigurieren Sie die Variablen zum Anpassen der Funktion.

## <a name="enable-and-configure"></a>Aktivieren und konfigurieren 

1. Melden Sie sich beim Azure-[Portal](https://portal.azure.com) an.
2. Suchen Sie nach **Automation-Konten**, und wählen Sie diese Option aus.
3. Wählen Sie auf der Seite **Automation-Konten** in der entsprechenden Liste Ihr Automation-Konto aus.
4. Wählen Sie in dem Automation-Konto unter **Verwandtes Ressourcen** die Option **VM starten/beenden** aus. Dort können Sie **Weitere Informationen anzeigen und Lösung aktivieren** anklicken. Wenn Sie die Funktion bereits bereitgestellt haben, können Sie auf **Lösung verwalten** klicken, und sie wird in der Liste angezeigt.

   ![Aktivieren über ein Automation-Konto](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Sie können die Ressource auch von überall im Azure-Portal erstellen, indem Sie auf **Ressource erstellen** klicken. Geben Sie auf der Seite „Marketplace“ ein Schlüsselwort ein, z.B. **Starten** oder **Starten/Beenden**. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Alternativ hierzu können Sie ein oder mehrere Schlüsselwörter des vollständigen Namens der Funktion eingeben und dann die **EINGABETASTE** drücken. Wählen Sie in den Suchergebnissen **VMs außerhalb der Geschäftszeiten starten/beenden** aus.

5. Überprüfen Sie auf der Seite „VMs außerhalb der Geschäftszeiten starten/beenden“ die Zusammenfassung der ausgewählten Bereitstellung, und klicken Sie dann auf **Erstellen**.

   ![Azure-Portal](media/automation-solution-vm-management/azure-portal-01.png)

   Nachdem die Ressource erstellt wurde, wird die Seite „Lösung hinzufügen“ angezeigt. Sie werden aufgefordert, die Funktion vor dem Importieren in Ihr Automation-Konto zu konfigurieren.

   ![Seite „Lösung hinzufügen“ der VM-Verwaltung](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

6. Wählen Sie auf der Seite **Lösung hinzufügen** die Option **Arbeitsbereich** aus. Wählen Sie einen vorhandenen Log Analytics-Arbeitsbereich aus der Liste aus. Wenn sich kein Automation-Konto in derselben unterstützten Region wie der Arbeitsbereich befindet, können Sie im nächsten Schritt ein neues Automation-Konto erstellen. 

   > [!NOTE]
   > Wenn Sie Funktionen aktivieren, werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto unterstützt. Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](how-to/region-mappings.md).

7. Wenn in der unterstützten Region des Arbeitsbereichs kein Automation-Konto verfügbar ist, wählen Sie auf der Seite **Lösung hinzufügen** die Option **Automation-Konto** aus. Sie können eine neues Automation-Konto für die Zuordnung erstellen, indem Sie **Automation-Konto erstellen** auswählen und auf der Seite **Automation-Konto hinzufügen** den Namen des Automation-Kontos im Feld **Name** angeben.

    Alle anderen Optionen werden basierend auf dem Log Analytics-Arbeitsbereich automatisch aufgefüllt. Sie können diese Optionen nicht ändern. Ein ausführendes Azure-Konto ist die Standardmethode für die Authentifizierung von Runbooks mit dieser Funktion. 
    
    Nach dem Klicken auf **OK** werden die Konfigurationsoptionen überprüft, und das Automation-Konto wird erstellt. Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

8. Wählen Sie auf der Seite „Lösung hinzufügen“ die Option **Parameter konfigurieren** aus. Die Seite **Parameter** wird angezeigt.

    ![Seite „Parameter“ für die Lösung](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

9. Geben Sie einen Wert für das Feld **ResourceGroup-Zielnamen** an. Das-Feld definiert Gruppennamen, die VMs enthalten, die von der Funktion verwaltet werden sollen. Sie können mehrere Namen eingeben und die Namen jeweils durch ein Komma trennen (Groß-/Kleinschreibung wird nicht berücksichtigt). Die Verwendung eines Platzhalterzeichens wird unterstützt, wenn Sie einen Vorgang für VMs in allen Ressourcengruppen des Abonnements durchführen möchten. Die Werte werden in den Variablen `External_Start_ResourceGroupNames` und `External_Stop_ResourceGroupNames` gespeichert.

    > [!IMPORTANT]
    > Der Standardwert für **ResourceGroup-Zielnamen** ist ein **&ast;** . Diese Einstellung betrifft alle VMs in einem Abonnement. Wenn Sie nicht möchten, dass die Funktion für alle VMs in Ihrem Abonnement gilt, müssen Sie vor der Auswahl eines Zeitplans eine Liste mit Ressourcengruppennamen angeben.
  
10. Geben Sie einen Wert für das Feld **VM-Ausschlussliste (Zeichenfolge)** an. Dies ist der Wert von einem oder mehreren virtuellen Computern der Zielressourcengruppe. Sie können mehrere Namen eingeben und die Namen jeweils durch ein Komma trennen (Groß-/Kleinschreibung wird nicht berücksichtigt). Platzhalter können verwendet werden. Dieser Wert wird in der Variablen `External_ExcludeVMNames` gespeichert.
  
11. Verwenden Sie das Feld **Zeitplan**, um einen Zeitplan für die VM-Verwaltung durch die Funktion auszuwählen. Wählen Sie ein Startdatum und eine Uhrzeit für Ihren Zeitplan aus, um einen sich täglich wiederholenden Zeitplan zu erstellen, der zur ausgewählten Uhrzeit startet. Es kann keine andere Region ausgewählt werden. Falls Sie den Zeitplan nach dem Konfigurieren der Funktion für Ihre Zeitzone konfigurieren möchten, helfen Ihnen die Informationen unter [Ändern des Zeitplans für das Starten und Herunterfahren](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules) weiter.

12. Um E-Mail-Benachrichtigungen von einer [Aktionsgruppe](../azure-monitor/alerts/action-groups.md) zu empfangen, übernehmen Sie den Standardwert **Ja** im Feld **E-Mail-Adresse**, und geben Sie eine gültige E-Mail-Adresse an. Wenn Sie **Nein** auswählen, sich aber zu einem späteren Zeitpunkt entscheiden, dass Sie E-Mail-Benachrichtigungen erhalten möchten, können Sie die erstellte Aktionsgruppe mit gültigen E-Mail-Adressen, die durch Kommas getrennt sind, aktualisieren. Die folgenden Warnungsregeln werden im Abonnement erstellt:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

13. Nachdem Sie die erforderlichen Anfangseinstellungen für die Funktion konfiguriert haben, klicken Sie auf **OK**, um die Seite **Parameter** zu schließen.

14. Klicken Sie auf **Erstellen**. Nachdem alle Einstellungen überprüft wurden, wird die Funktion in Ihrem Abonnement bereitgestellt. Dieser Vorgang kann einige Sekunden dauern, und Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

    > [!NOTE]
    > Wenn Sie ein Abonnement für Azure Cloud Solution Provider (Azure CSP) besitzen, wechseln Sie nach Abschluss der Bereitstellung in Ihrem Automation-Konto unter **Freigegebene Ressourcen** zu **Variablen**, und legen Sie die Variable [External_EnableClassicVMs](automation-solution-vm-management.md#variables) auf **False** fest. Dadurch hört die Lösung auf, nach klassischen VM-Ressourcen zu suchen.

## <a name="create-alerts"></a>Erstellen von Warnungen

„VMs außerhalb der Geschäftszeiten starten/beenden“ umfasst keinen vordefinierten Satz von Automation-Auftragswarnungen. Lesen Sie [Weiterleiten von Auftragsdaten an Azure Monitor-Protokolle](automation-manage-send-joblogs-log-analytics.md#azure-monitor-log-records), um mehr über Protokolldaten zu erfahren, die vom Automation-Konto im Zusammenhang mit den Ergebnissen des Runbookauftrags weitergeleitet werden, und wie Sie Warnungen wegen fehlgeschlagener Aufträge erstellen, um Ihre DevOps- oder Betriebsprozesse und -verfahren zu unterstützen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Einrichten des Features finden Sie unter [Konfigurieren von „VMs außerhalb der Geschäftszeiten starten/beenden“](automation-solution-vm-management-config.md).
* Informationen zum Beheben von Problemen mit dem Feature finden Sie unter [Problembehandlung von „VMs außerhalb der Geschäftszeiten starten/beenden“](troubleshoot/start-stop-vm.md).