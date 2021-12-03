---
title: Erstellen von Updatebereitstellungen für die Azure Automation-Updateverwaltung
description: In diesem Artikel wird beschrieben, wie Sie Updatebereitstellungen planen und deren Status anzeigen.
services: automation
ms.subservice: update-management
ms.date: 11/05/2021
ms.topic: conceptual
ms.openlocfilehash: 6830c06de1fc687f8c408d438086f6c08550d9f0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551404"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Bereitstellen von Updates und Überprüfen von Ergebnissen

In diesem Artikel wird beschrieben, wie Sie eine Updatebereitstellung planen und nach Abschluss der Bereitstellung den Prozess überprüfen. Sie können eine Update-Bereitstellung von einer ausgewählten virtuellen Azure-Maschine, von einem ausgewählten Azure Arc-aktivierten Server oder vom Automatisierungskonto für alle konfigurierten Maschinen und Server konfigurieren.

In jedem Szenario zielt die von Ihnen erstellte Bereitstellung auf diesen ausgewählten Computer oder Server ab, oder wenn Sie eine Bereitstellung aus Ihrem Automation-Konto heraus erstellen, können Sie auf einen oder mehrere Computer abzielen. Wenn Sie eine Update-Bereitstellung von einer Azure-VM oder einem Azure Arc-aktivierten Server aus planen, sind die Schritte dieselben wie bei der Bereitstellung über Ihr Automatisierungskonto, mit den folgenden Ausnahmen:

* Das Betriebssystem wird automatisch vorab ausgewählt, basierend auf dem Betriebssystem des Computers.
* Der zu aktualisierende Zielcomputer wird automatisch auf sich selbst als Ziel festgelegt.
* Wenn Sie den Zeitplan konfigurieren, können Sie **Jetzt aktualisieren**, „Einmalig am“ oder „Verwendet einen wiederkehrenden Zeitplan“ angeben.

> [!IMPORTANT]
> Mit dem Erstellen einer Updatebereitstellung akzeptieren Sie die Software-Lizenzbedingungen (EULA) des Unternehmens, das die Updates für das Betriebssystem anbietet.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Planen einer Updatebereitstellung

Bei der Planung einer Updatebereitstellung wird eine [Zeitplanressource](../shared-resources/schedules.md) erstellt. Diese wird mit dem Runbook **Patch-MicrosoftOMSComputers** verknüpft, das die Updatebereitstellung auf dem oder den Zielcomputern verarbeitet. Sie müssen eine Bereitstellung planen, die Ihrem Releasezeitplan und Wartungsfenster entspricht, um Updates zu installieren. Sie können auswählen, welche Updatetypen in die Bereitstellung eingeschlossen werden sollen. Beispielsweise können Sie kritische oder Sicherheitsupdates einschließen und Updaterollups ausschließen.

>[!NOTE]
>Wenn Sie die Zeitplanressource nach der Bereitstellungserstellung über das Azure-Portal oder mithilfe von PowerShell löschen, wird die geplante Updatebereitstellung unterbrochen, und es wird ein Fehler angezeigt, wenn Sie versuchen, die Konfiguration der Zeitplanressource über das Portal zu ändern. Sie können die Zeitplanressource nur löschen, indem Sie den entsprechenden Bereitstellungszeitplan löschen.  

Um eine neue Updatebereitstellung durchzuführen, führen Sie die folgenden Schritte aus. Je nach ausgewählter Ressource (d. h. Automatisierungskonto, Azure Arc-aktivierter Server, Azure VM) gelten die nachstehenden Schritte für alle mit geringfügigen Unterschieden bei der Konfiguration des Zeitplans für die Bereitstellung.

1. Im Portal zum Planen einer Bereitstellung:

   * Für einen oder mehrere Computer navigieren Sie zu **Automation-Konten**, und wählen Sie in der Liste Ihr Automation-Konto mit aktivierter Updateverwaltung aus.
   * Für einen virtuellen Azure-Computer navigieren Sie zu **Virtuelle Computer**, und wählen Sie Ihren virtuellen Computer in der Liste aus.
   * Für einen Azure Arc-aktivierten Server navigieren Sie zu **Server - Azure Arc** und wählen Sie Ihren Server in der Liste aus.

2. Gehen Sie wie folgt vor, um abhängig von der ausgewählten Ressource zur Updateverwaltung zu navigieren:

   * Wenn Sie Ihr Automation-Konto ausgewählt haben, wechseln Sie unter **Updateverwaltung** zu **Updateverwaltung**, und wählen Sie dann **Updatebereitstellung planen** aus.
   * Wenn Sie einen virtuellen Azure-Computer ausgewählt haben, wechseln Sie zu **Gast- und Hostupdates**, und wählen Sie dann **Zur Updateverwaltung wechseln** aus.
   * Wenn Sie einen Azure Arc-aktivierten Server ausgewählt haben, gehen Sie zu **Updateverwaltung** und wählen Sie dann **Planen Sie die Bereitstellung von Updates**.

3. Geben Sie unter **Neue Updatebereitstellung**  im Feld **Name** einen eindeutigen Namen für Ihre Bereitstellung ein.

4. Wählen Sie das Betriebssystem aus, das als Ziel für die Updatebereitstellung dienen soll.

    > [!NOTE]
    > Diese Option ist nicht verfügbar, wenn Sie eine Azure VM oder einen Azure Arc-aktivierten Server ausgewählt haben. Das Betriebssystem wird automatisch erkannt.

5. Definieren Sie im Bereich **Zu aktualisierende Gruppen** eine Abfrage mit einer Kombination aus Abonnement, Ressourcengruppen, Standorten und Tags zur Erstellung einer dynamischen Gruppe von virtuellen Azure-Computern, die in Ihre Bereitstellung eingeschlossen werden sollen. Weitere Informationen finden Sie unter [Verwenden dynamischer Gruppen mit der Updateverwaltung](configure-groups.md).

    > [!NOTE]
    > Diese Option ist nicht verfügbar, wenn Sie eine Azure VM oder einen Azure Arc-aktivierten Server ausgewählt haben. Der Computer wird automatisch als Ziel für die geplante Bereitstellung festgelegt.

   > [!IMPORTANT]
   > Beim Erstellen einer dynamischen Gruppe von Azure-VMs unterstützt Updateverwaltung max. 500 Abfragen, die Abonnements oder Ressourcengruppen im Bereich der Gruppe kombinieren.

6. Wählen Sie im Bereich **Zu aktualisierende Computer** eine gespeicherte Suche oder eine importierte Gruppe aus, oder wählen Sie im Dropdownmenü die Option **Computer** und anschließend einzelne Computer aus. Mit dieser Option können Sie die Bereitschaft des Log Analytics-Agents für jeden Computer erkennen. Weitere Informationen zu den verschiedenen Methoden zum Erstellen von Computergruppen in Azure Monitor-Protokollen finden Sie unter [Computergruppen in Azure Monitor-Protokollen](../../azure-monitor/logs/computer-groups.md). Sie können maximal 1.000 Computer in eine geplante Updatebereitstellung miteinbeziehen.

    > [!NOTE]
    > Diese Option ist nicht verfügbar, wenn Sie eine Azure VM oder einen Azure Arc-aktivierten Server ausgewählt haben. Der Computer wird automatisch als Ziel für die geplante Bereitstellung festgelegt.

7. Verwenden Sie den Bereich **Updateklassifizierungen**, um [Updateklassifizierungen](view-update-assessments.md#work-with-update-classifications) für Produkte anzugeben. Deaktivieren Sie für jedes Produkt alle unterstützten Updateklassifizierungen, die nicht in Ihre Updatebereitstellung eingeschlossen werden sollen.

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="Beispiel für die Auswahl bestimmter Updateklassifizierungen":::

    Wenn Ihre Bereitstellung nur für ausgewählte Updates gelten soll, ist es erforderlich, alle vorab ausgewählten Updateklassifizierungen zu deaktivieren, wenn die Option  **Updates einschließen/ausschließen** wie im nächsten Schritt beschrieben konfiguriert wird. Dadurch wird sichergestellt, dass nur die Updates, die Sie in diese Bereitstellung *einschließen* möchten, auf den Zielcomputern installiert werden.

   >[!NOTE]
   > Das Bereitstellen von Updates nach Updateklassifizierung funktioniert unter RTM-Versionen von CentOS nicht. Wählen Sie zum ordnungsgemäßen Bereitstellen von Updates für CentOS alle Klassifizierungen aus, um sicherzustellen, dass die Updates angewendet werden. Aktuell steht keine unterstützte Methode zur Verfügung, mit der unter CentOS eine native Verfügbarkeit von Klassifizierungsdaten implementiert werden kann. Weitere Informationen zu Updateklassifizierungen finden Sie [hier](overview.md#update-classifications).

   >[!NOTE]
   > Die Bereitstellung von Updates nach Updateklassifizierung funktioniert möglicherweise nicht ordnungsgemäß für Linux-Distributionen, die von der Updateverwaltung unterstützt werden. Dies ist das Ergebnis eines Problems mit dem Namensschema der OVAL-Datei, das verhindert, dass die Updateverwaltung Klassifizierungen basierend auf Filterregeln ordnungsgemäß abgleicht. Aufgrund der unterschiedlichen Logik, die bei Bewertungen von Sicherheitsupdates verwendet wird, können sich die Ergebnisse von den bei der Bereitstellung angewendeten Sicherheitsupdates unterscheiden. Wenn Sie die Klassifizierung als **Kritisch** und **Sicherheit** festgelegt haben, funktioniert die Updatebereitstellung wie erwartet. Nur die *Klassifizierung von Updates* während einer Bewertung ist betroffen.
   >
   > Die Updateverwaltung für Windows Server-Computer bleibt davon unberührt, und die Updateklassifizierung und Bereitstellungen bleiben unverändert.

8. Über den Bereich **Updates ein-/ausschließen** können Sie ausgewählte Updates in die Bereitstellung einschließen oder von ihr ausschließen. Geben Sie auf der Seite **Updates ein-/ausschließen** die IDs der KB-Artikel ein, die bei Windows-Updates ein- oder ausgeschlossen werden sollen. Für unterstützte Linux-Distributionen geben Sie den Paketnamen an.

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="Beispiel für das Einschließen bestimmter Updates":::

   > [!IMPORTANT]
   > Denken Sie daran, dass Ausschlüsse eine höhere Priorität haben als Einschlüsse. Wenn Sie also beispielsweise die Ausschlussregel `*` definieren, schließt die Updateverwaltung alle Patches oder Pakete aus der Installation aus. Ausgeschlossene Patches werden weiterhin als auf dem Computer nicht vorhanden angezeigt. Wenn Sie für Linux-Computer ein Paket mit einem ausgeschlossenen abhängigen Paket einschließen, wird das Hauptpaket von der Updateverwaltung nicht installiert.

   > [!NOTE]
   > Sie können keine Updates angeben, die für die Aufnahme in die Updatebereitstellung ersetzt wurden.

   Es folgen einige Beispielszenarien zur gleichzeitigen Verwendung von Ein- und Ausschlüssen sowie Updateklassifizierung in Updatebereitstellungen:

   * Wenn Sie nur eine bestimmte Liste von Updates installieren möchten, sollten Sie keine **Updateklassifizierungen** auswählen und eine Liste der Updates angeben, die mit der Option **Einschließen** angewandt werden sollen.

   * Wenn Sie nur Sicherheits- und kritische Updates zusammen mit mindestens einem optionalen Update installieren möchten, sollten Sie unter **Updateklassifizierungen** die Optionen **Sicherheitsupdates** und **Wichtige Updates** auswählen. Geben Sie dann für die Option **Einschließen** die KBIDs für die optionalen Updates an.

   * Wenn Sie nur Sicherheits- und kritische Updates installieren möchten, jedoch Updates für Python überspringen möchten, um Fehler bei der Legacyanwendung zu vermeiden, sollten Sie unter **Updateklassifizierungen** die Optionen **Sicherheitsupdates** und **Wichtige Updates** auswählen. Fügen Sie dann unter der Option **Ausschließen** die zu überspringenden Python-Pakete hinzu.

9. Wählen Sie **Zeitplaneinstellungen** aus. Der Standard-Startzeitpunkt liegt 30 Minuten nach der aktuellen Uhrzeit. Als Startzeit können Sie einen beliebigen Wert festlegen, er muss jedoch mindestens 10 Minuten in der Zukunft liegen.

    > [!NOTE]
    > Diese Option ist anders, wenn Sie einen Azure Arc-aktivierten Server ausgewählt haben. Sie können **Jetzt aktualisieren** oder eine Startzeit, die 20 Minuten in der Zukunft liegt, auswählen.

10. Verwenden Sie **Wiederholung**, um anzugeben, ob die Bereitstellung einmal oder nach einem wiederkehrenden Zeitplan erfolgt, und wählen Sie dann **OK** aus.

11. Wählen Sie im Bereich **Vor und nach dem Vorgang auszuführende Skripts** die Skripts aus, die vor und nach Ihrer Bereitstellung ausgeführt werden sollen. Weitere Informationen finden Sie unter [Verwalten von Pre- und Post-Skripts](pre-post-scripts.md).

12. Geben Sie im Feld **Wartungsfenster (Minuten)** die zulässige Zeit für die Installation von Updates an. Bei der Angabe eines Wartungsfensters sind folgende Aspekte zu beachten:

    * Wartungsfenster steuern, wie viele Updates installiert werden.
    * Die Installation neuer Updates wird von der Updateverwaltung nicht beendet, wenn das Ende eines Wartungsfensters fast erreicht ist.
    * Bereits aktive Updates werden von der Updateverwaltung bei Überschreiten des Wartungsfensters nicht beendet. Alle verbleibenden Updates, die installiert werden sollen, werden nicht versucht. Wenn dies konsistent auftritt, sollten Sie die Dauer Ihres Wartungsfensters neu bewerten.
    * Unter Windows ist die Überschreitung des Wartungsfensters häufig auf eine lange dauernde Installation eines Service Pack-Updates zurückzuführen.

    > [!NOTE]
    > Damit unter Ubuntu keine Updates außerhalb der Wartungsfenster angewandt werden, konfigurieren Sie das Paket `Unattended-Upgrade` erneut, um automatische Updates zu deaktivieren. Weitere Informationen zur Konfiguration dieses Pakets finden Sie im [Thema zu automatischen Updates im Ubuntu-Serverhandbuch](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Verwenden Sie das Feld **Neustartoptionen**, um die Methode zum Behandeln von Neustarts während der Bereitstellung anzugeben. Die folgenden Optionen sind verfügbar: 
    * Neustart, falls erforderlich (Standard)
    * Immer neu starten
    * Nie neu starten
    * Nur Neustart: Mit dieser Option werden keine Updates installiert.

    > [!NOTE]
    > Die unter [Registrierungsschlüssel zum Verwalten des Neustarts](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) aufgeführten Registrierungsschlüssel können zu einem Neustartereignis führen, wenn **Neustartoptionen** auf **Nie neu starten** festgelegt ist.

14. Wählen Sie nach Abschluss der Konfiguration des Bereitstellungszeitplans **Erstellen** aus.

    ![Bereich für Updatezeitplan-Einstellungen](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Wenn Sie die Konfiguration des Bereitstellungsplans für einen ausgewählten Azure Arc-aktivierten Server abgeschlossen haben, wählen Sie **Überprüfen + Erstellen**.

15. Das Statusdashboard wird wieder angezeigt. Wählen Sie **Bereitstellungszeitpläne** aus, um den erstellten Bereitstellungszeitplan anzuzeigen. Es werden maximal 500 Zeitpläne aufgeführt. Wenn Sie über mehr als 500 Zeitpläne verfügen und die vollständige Liste überprüfen möchten, sehen Sie sich den Artikel zur entsprechenden REST-API-Methode unter [Softwareupdatekonfigurationen: Liste](/rest/api/automation/softwareupdateconfigurations/list) an. Geben Sie die API-Version 2019-06-01 oder höher an.

## <a name="schedule-an-update-deployment-programmatically"></a>Programmgesteuertes Planen einer Updatebereitstellung

Weitere Informationen zum Erstellen einer Updatebereitstellung mit der REST-API finden Sie unter [Softwareupdatekonfigurationen – Erstellen](/rest/api/automation/softwareupdateconfigurations/create).

Sie können auch ein Beispielrunbook zum Erstellen einer wöchentlichen Updatebereitstellung verwenden. Weitere Informationen zu diesem Runbook finden Sie unter [Erstellen einer wöchentlichen Updatebereitstellung für einen oder mehrere virtuelle Computer in einer Ressourcengruppe](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group).

## <a name="check-deployment-status"></a>Überprüfen des Bereitstellungsstatus

Nach dem Start Ihrer geplanten Bereitstellung wird ihr Status auf der Registerkarte **Verlauf** unter **Updateverwaltung** angezeigt. Der Status lautet **In Bearbeitung**, wenn die Bereitstellung derzeit ausgeführt wird. Nach erfolgreichem Abschluss der Bereitstellung ändert sich der Status in **Erfolgreich**. Wenn bei einzelnen oder mehreren Updates in der Bereitstellung Fehler auftreten, wird der Status als **Fehlgeschlagen** angezeigt.

## <a name="view-results-of-a-completed-update-deployment"></a>Anzeigen der Ergebnisse einer abgeschlossenen Updatebereitstellung

Wenn die Bereitstellung abgeschlossen ist, können Sie sie auswählen, um ihre Ergebnisse anzuzeigen.

[ ![Updatebereitstellungsstatus-Dashboard für eine bestimmte Bereitstellung](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Unter **Updateergebnisse** wird eine Zusammenfassung der Gesamtanzahl von Updates und der Bereitstellungsergebnisse auf den Ziel-VMs angegeben. Die Tabelle rechts enthält eine detaillierte Aufschlüsselung der einzelnen Updates und der jeweiligen Installationsergebnisse.

Die verfügbaren Werte sind:

* **Nicht versucht:** Das Update wurde nicht installiert, da aufgrund des definierten Wartungsfensters nicht genügend Zeit zur Verfügung stand.
* **Nicht ausgewählt:** Das Update wurde nicht zur Bereitstellung ausgewählt.
* **Erfolgreich:** Das Update wurde erfolgreich ausgeführt.
* **Fehler:** Beim Update ist ein Fehler aufgetreten.

Wählen Sie **Alle Protokolle** aus, um alle von der Bereitstellung erstellten Protokolleinträge anzuzeigen.

Wählen Sie **Ausgabe** aus, um den Auftragsdatenstrom des Runbooks anzuzeigen, das für die Verwaltung der Updatebereitstellung auf den Ziel-VMs verantwortlich ist.

Klicken Sie auf **Fehler**, um ausführliche Informationen zu Fehlern bei der Bereitstellung anzuzeigen.

## <a name="deploy-updates-across-azure-tenants"></a>Bereitstellen von Updates für Azure-Mandanten

Wenn Sie Computer in einem anderen Azure-Mandanten patchen müssen, der der Updateverwaltung unterliegt, müssen Sie wie folgt vorgehen, um sie in die Planung aufzunehmen. Sie können das Cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) mit dem Parameter `ForUpdateConfiguration` verwenden, um einen Zeitplan zu erstellen. Sie können das Cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) verwenden und die Computer im anderen Mandanten an den Parameter `NonAzureComputer` übergeben. Das folgende Beispiel zeigt die erforderliche Vorgehensweise.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule `
    -ResourceGroupName mygroup `
    -AutomationAccountName myaccount `
    -Name myupdateconfig `
    -Description test-OneTime `
    -OneTime `
    -StartTime $startTime `
    -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  `
    -ResourceGroupName $rg `
    -AutomationAccountName <automationAccountName> `
    -Schedule $sched `
    -Windows `
    -NonAzureComputer $nonAzurecomputers `
    -Duration (New-TimeSpan -Hours 2) `
    -IncludedUpdateClassification Security,UpdateRollup `
    -ExcludedKbNumber KB01,KB02 `
    -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erstellen von Warnungen, die Sie über die Ergebnisse der Updatebereitstellung informieren, finden Sie unter [Erstellen von Warnungen für die Updateverwaltung](configure-alerts.md).
* Informationen zum Behandeln von Fehlern bei der Updateverwaltung finden Sie unter [Behandeln von Problemen mit der Updateverwaltung](../troubleshoot/update-management.md).
