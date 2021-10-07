---
title: Verbinden von Computern über die Azure Automation-Updateverwaltung
description: In diesem Artikel erfahren Sie, wie Sie Hybridcomputer mit Azure Arc mit Automation-Updateverwaltung verbinden.
ms.date: 09/14/2021
ms.topic: conceptual
ms.openlocfilehash: fc5de9146ea07e490e69760397a661f4bbb99eec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700217"
---
# <a name="connect-hybrid-machines-to-azure-from-automation-update-management"></a>Verbinden von Hybridcomputern mit Azure über die Automation-Updateverwaltung

Sie können Azure Arc-fähige Server für einen oder mehrere Ihrer virtuellen Windows- oder Linux-Computer oder physische Server aktivieren, die lokal oder in einer anderen Cloudumgebung gehostet werden und mit Azure Automation-Updateverwaltung verwaltet werden. Dieser Onboardingprozess automatisiert den Download und die Installation des [Connected Machine-Agents](agent-overview.md). Zur Verbindung der Computer mit Azure Arc-fähigen Servern wird ein Azure Active Directory-[Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md) verwendet, anstatt Ihre privilegierte Identität zum [interaktiven Verbinden des Computers](onboard-portal.md) zu nutzen. Dieser Dienstprinzipal wird automatisch im Rahmen des Onboardingprozesses für diese Computer erstellt.

Bevor Sie beginnen, sollten Sie die [Voraussetzungen](agent-overview.md#prerequisites) überprüfen und sicherstellen, dass Ihr Abonnement und Ihre Ressourcen auch diese Anforderungen erfüllen. Informationen zu unterstützten Regionen und andere Überlegungen finden Sie unter [Unterstützte Azure-Regionen](overview.md#supported-regions).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="how-it-works"></a>Funktionsweise

Wenn der Onboardingprozess gestartet wird, wird im Mandanten ein Active Directory-[Dienstprinzipal](../../active-directory/fundamentals/service-accounts-principal.md) erstellt. 

Zum Installieren und Konfigurieren des Connected Machine-Agents auf dem Zielcomputer wird ein Master-Runbook mit dem Namen **Add-AzureConnectedMachines** in der Azure-Sandbox ausgeführt. Basierend auf dem auf dem Computer erkannten Betriebssystem ruft das Master-Runbook ein untergeordnetes Runbook namens **Add-AzureConnectedMachineWindows** oder **Add-AzureConnectedMachineLinux** auf, das unter der Systemrolle [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) direkt auf dem Computer ausgeführt wird. Die Ausgabe der Runbookaufträge wird in den Auftragsverlauf geschrieben, und Sie können ihre [Statuszusammenfassung](../../automation/automation-runbook-execution.md#job-statuses) anzeigen oder im [Azure-Portal](../../automation/manage-runbooks.md#view-statuses-in-the-azure-portal) oder mithilfe von [Azure PowerShell](../../automation/manage-runbooks.md#retrieve-job-statuses-using-powershell) weitere Details zu einem bestimmten Runbookauftrag aufführen. Bei der Ausführung von Runbooks in Azure Automation werden Details in ein Aktivitätsprotokoll für das Automation-Konto geschrieben. Ausführliche Informationen zur Verwendung des Protokolls finden Sie unter [Abrufen von Details aus dem Aktivitätsprotokoll](../../automation/manage-runbooks.md#retrieve-details-from-activity-log).

Im letzten Schritt wird die Verbindung mit Azure Arc mithilfe des Befehls `azcmagent` hergestellt, indem der Dienstprinzipal verwendet wird, um den Computer als Ressource in Azure zu registrieren.

## <a name="prerequisites"></a>Voraussetzungen

Diese Methode erfordert, dass Sie Mitglied der Rolle [Automation-Auftragsoperator](../../automation/automation-role-based-access-control.md#automation-job-operator) oder höher sind, damit Sie Runbookaufträge im Automation-Konto erstellen können. 

Wenn Sie Azure Policy für die [Verwaltung der Runbookausführung](../../automation/enforce-job-execution-hybrid-worker.md) aktiviert haben und die Ausrichtung der Runbookausführung auf eine Hybrid Runbook Worker-Gruppe erzwingen, muss diese Richtlinie deaktiviert werden. Andernfalls können Runbookaufträge, die für die Computer ein Onboarding auf Arc-fähige Server durchführen, nicht ausgeführt werden. 

## <a name="add-machines-from-the-azure-portal"></a>Hinzufügen von Computern über das Azure-Portal

Führen Sie die folgenden Schritte aus, um den Hybridcomputer mit Arc-fähigen Servern zu konfigurieren. Der Server oder Computer muss eingeschaltet und online sein, damit der Prozess erfolgreich abgeschlossen werden kann.

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

1. Navigieren Sie zur Seite **Server – Azure Arc**, und wählen Sie dann oben links **Hinzufügen** aus.

1. Wählen Sie auf der Seite **Methode auswählen** die Kachel **Add managed servers from Update Management (preview)** (Verwaltete Server über Updateverwaltung hinzufügen (Vorschau)) und dann **Server hinzufügen** aus.

1. Konfigurieren Sie auf der Seite **Grundlagen** Folgendes:

    1. Wählen Sie in der Dropdownliste **Ressourcengruppe** die Ressourcengruppe aus, über die der Computer verwaltet wird.
    1. Wählen Sie in der Dropdownliste **Regionen** die Azure-Region aus, in der die Metadaten des Servers gespeichert werden sollen.
    1. Wenn der Computer über einen Proxyserver kommuniziert, um eine Verbindung mit dem Internet herzustellen, geben Sie die IP-Adresse des Proxyservers oder den Namen und die Portnummer an, die der Computer für die Kommunikation mit dem Proxyserver verwenden wird. Geben Sie den Wert im Format `http://<proxyURL>:<proxyport>` ein.
    1. Wählen Sie **Next: Machines** (Weiter: Computer) aus.

1. Wählen Sie auf der Seite **Computer** das **Abonnement** und das **Automation-Konto** aus der Dropdownliste aus, für das das Feature „Updateverwaltung“ aktiviert ist und das die Computer enthält, für die Sie ein Onboarding auf Azure Arc-fähige Server durchführen möchten.

   Nach der Angabe des Automation-Kontos gibt die folgende Liste Nicht-Azure-Computer zurück, die von der Updateverwaltung für dieses Automation-Konto verwaltet werden. Sowohl Windows- als auch Linux-Computer sind aufgeführt; wählen Sie für jeden Computer **hinzufügen** aus.

   Sie können Ihre Auswahl überprüfen, indem Sie **Auswahl überprüfen** auswählen. Wenn Sie einen Computer entfernen möchten, wählen Sie **entfernen** aus der Spalte **Aktion** aus. 

   Nachdem Sie Ihre Auswahl bestätigt haben, wählen Sie **Weiter: Tags** aus.

1. Geben Sie auf der Seite **Tags** mindestens ein **Name**/**Wert**-Paar an, um Ihre Standards zu unterstützen. Wählen Sie **Weiter: Überprüfen + hinzufügen** aus.

1. Überprüfen Sie die auf der Seite **Review _ add** (Überprüfen_hinzufügen) zusammengefassten Informationen, und wählen Sie dann **Computer hinzufügen** aus. Sollten noch Änderungen erforderlich sein, wählen Sie **Zurück** aus.

## <a name="verify-the-connection-with-azure-arc"></a>Überprüfen der Verbindung mit Azure Arc

Vergewissern Sie sich im Azure-Portal, dass die Serververbindung erfolgreich hergestellt wurde, nachdem der Agent installiert und für die Verbindungsherstellung mit Azure Arc-fähigen Servern konfiguriert wurde. Zeigen Sie Ihre Computer im [Azure-Portal](https://aka.ms/hybridmachineportal) an.

![Erfolgreiche Serververbindung](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

- Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), um die Bereitstellung von Servern mit Azure Arc-Unterstützung in beliebiger Größenordnung zu planen und eine zentrale Verwaltung und Überwachung zu implementieren.

- Erfahren Sie, wie Sie Ihren Computer mithilfe von [Azure Policy](../../governance/policy/overview.md) verwalten, wie z. B. bei der VM-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md), dem Überprüfen, ob der Computer dem erwarteten Log Analytics-Arbeitsbereich Bericht erstattet, beim Aktivieren der Überwachung mit [VM Insights](../../azure-monitor/vm/vminsights-enable-policy.md) und vieles mehr.