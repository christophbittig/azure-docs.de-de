---
title: Behandeln von häufig auftretenden Problemen mit Azure Chaos Studio
description: Erfahren Sie, wie Sie häufig auftretende Probleme bei der Nutzung von Azure Chaos Studio beheben.
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 9d56666f42d837f59ec22031646d46e22f0b298d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372490"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Behandeln von Problemen mit Azure Chaos Studio

Wenn Sie Chaos Studio verwenden, können gelegentlich Probleme auftreten. In diesem Artikel werden die am häufigsten auftretenden Probleme sowie die Schritte zur Problembehandlung ausführlich erläutert.

## <a name="general-troubleshooting-tips"></a>Allgemeine Tipps für die Problembehandlung

Die folgenden Quellen sind bei der Behandlung von Problemen mit Chaos Studio nützlich:
1. **Aktivitätsprotokoll**: Das [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) verfügt über einen Datensatz aller Erstellungs-, Aktualisierungs- und Löschvorgänge in einem Abonnement, einschließlich Chaos Studio-Vorgängen wie dem Aktivieren eines Ziels und/oder von Funktionen, dem Installieren des Agents und dem Erstellen oder Ausführen eines Experiments. Fehler im Aktivitätsprotokoll deuten darauf hin, dass eine Benutzeraktion, die für die Verwendung von Chaos Studio wichtig ist, möglicherweise nicht abgeschlossen wurde. Die meisten service-direct-Fehler injizieren auch Fehler durch die Ausführung eines Azure Resource Manager-Vorgangs, sodass das Aktivitätsprotokoll auch die Aufzeichnung von Fehlern enthält, die während eines Experiments für einige service-direct-Fehler injiziert werden.
2. **Experimentdetails**: Details zur Experimentausführung zeigen den Status und die Fehler einer einzelnen Experimentausführung an. Wenn Sie einen bestimmten Fehler in den Experimentdetails öffnen, werden die fehlerhaften Ressourcen und die Fehlermeldungen für einen Fehler angezeigt. [Erfahren Sie mehr über den Zugriff auf Experimentdetails](chaos-studio-run-experiment.md#view-experiment-history-and-details).
3. **Agent-Protokolle**: Wenn Sie einen agent-basierten Fehler verwenden, müssen Sie möglicherweise RDP oder SSH auf der VM verwenden, um zu verstehen, warum der Agent einen Fehler nicht ausgeführt hat. Die Anweisungen für den Zugriff auf Agent-Protokolle hängen vom Betriebssystem ab:
    * **Chaos Windows-Agent**: Agent-Protokolle befinden sich im Windows-Ereignisprotokoll in der Kategorie „Anwendung“ mit der Quelle AzureChaosAgent. Der Agent fügt diesem Protokoll Fehleraktivitäten und regelmäßige Integritätsüberprüfungen (Fähigkeit zur Authentifizierung bei und Kommunikation mit dem Chaos Studio-Agent-Dienst) hinzu.
    * **Chaos Linux-Agent**: Der Linux-Agent verwendet systemd, um den Agent-Prozess als Linux-Dienst zu verwalten. Führen Sie den Befehl `journalctl -u azure-chaos-agent` aus, um das systemd-Journal für den Agent (die vom Agent-Dienst protokollierten Ereignisse) anzuzeigen.
4. **VM-Erweiterungsstatus**: Wenn Sie einen agent-basierten Fehler verwenden, müssen Sie möglicherweise auch überprüfen, ob die VM-Erweiterung installiert und fehlerfrei ist. Navigieren Sie im Azure-Portal zu Ihrer VM, und navigieren Sie zu **Erweiterungen** oder **Erweiterungen und Anwendungen**. Klicken Sie auf die ChaosAgent-Erweiterung, und suchen Sie nach den folgenden Feldern:
    * **Status** sollte als „Provisioning succeeded“ (Bereitstellung erfolgreich) angezeigt werden. Jeder andere Status gibt an, dass der Agent nicht installiert werden konnte. Vergewissern Sie sich, dass alle [Systemanforderungen](chaos-studio-limitations.md#limitations) erfüllt sind, und versuchen Sie erneut, den Agent zu installieren.
    * **Handlerstatus** sollte „Ready“ (Bereit) anzeigen. Jeder andere Status gibt an, dass der Agent installiert wurde, aber keine Verbindung mit dem Chaos Studio-Dienst herstellen kann. Vergewissern Sie sich, dass alle [Netzwerkanforderungen](chaos-studio-limitations.md#limitations) erfüllt sind und dass die vom Benutzer zugewiesene verwaltete Identität der VM hinzugefügt wurde, und versuchen Sie, den Computer neu zu starten.

## <a name="issues-onboarding-a-resource"></a>Probleme beim Onboarding einer Ressource

### <a name="resources-do-not-show-up-in-the-targets-list-in-the-azure-portal"></a>Ressourcen werden nicht in der Liste der Ziele im Azure-Portal angezeigt
Wenn die Ressourcen, die Sie aktivieren möchten, nicht in der Chaos Studio-Zielliste angezeigt werden, kann dies durch eines der folgenden Probleme verursacht werden:
* Die Ressourcen befinden sich nicht in [einer unterstützten Region für Chaos Studio](https://azure.microsoft.com/global-infrastructure/services/?products=chaos-studio).
* Die Ressourcen weisen keinen [unterstützten Ressourcentyp in Chaos Studio](chaos-studio-fault-providers.md) auf.
* Die Ressourcen befinden sich in einem Abonnement oder einer Ressourcengruppe, die in den Filtern für die Zielliste herausgefiltert werden. Ändern Sie die Filter für das Abonnement und die Ressourcengruppe, um Ihre Ressourcen anzuzeigen.

### <a name="target-andor-capability-enablement-fails-or-doesnt-show-correctly-in-the-target-list"></a>Die Ziel- und/oder Funktionsaktivierung schlägt fehl oder wird nicht ordnungsgemäß in der Zielliste angezeigt
Wenn beim Aktivieren von Zielen und/oder Funktionen ein Fehler auftritt, führen Sie die folgenden Schritte aus:
1. Vergewissern Sie sich, dass Sie über die entsprechenden Berechtigungen für die Ressourcen verfügen, die Sie Onboarding durchführen. Zum Aktivieren eines Ziels und/oder von Funktionen ist die Microsoft.Chaos/\*-Berechtigung im Bereich der Ressource erforderlich. Integrierte Rollen wie „Mitwirkender“ verfügen über platzhalterbasierte Lese- und Schreibberechtigungen, die Berechtigungen für alle Microsoft.Chaos-Vorgänge beinhalten.
2. Warten Sie einige Minuten, bis die Ziel- und Funktionsliste aktualisiert wurde. Das Azure-Portal verwendet Azure Resource Graph, um Informationen zum Onboarding von Zielen und Funktionen zu erfassen, und es kann bis zu fünf Minuten dauern, bis die Aktualisierung verfügbar ist.
3. Wenn die Ressource weiterhin „Not enabled“ (Nicht aktiviert) anzeigt, führen Sie die folgenden Schritte aus:
    1. Versuchen Sie erneut, die Ressource zu aktivieren.
    2. Wenn die Ressourcenaktivierung weiterhin fehlschlägt, besuchen Sie das Aktivitätsprotokoll, und suchen Sie den fehlgeschlagenen Vorgang zur Zielerstellung, um ausführliche Fehlerinformationen anzuzeigen.
4. Wenn die Ressource „Enabled“ (Aktiviert) anzeigt, die Onboardingfunktionen aber fehlgeschlagen sind, führen Sie die folgenden Schritte aus:
    1. Klicken Sie in der Zielliste auf die Schaltfläche **Manage actions** (Aktionen verwalten) für die Ressource. Überprüfen Sie alle Funktionen, die nicht überprüft wurden, und klicken Sie auf **Save** (Speichern).
    2. Wenn die Funktionsaktivierung weiterhin fehlschlägt, besuchen Sie das Aktivitätsprotokoll, und suchen Sie den fehlgeschlagenen Vorgang zur Zielerstellung, um ausführliche Fehlerinformationen anzuzeigen.

## <a name="prerequisite-issues"></a>Probleme mit den Voraussetzungen

Einige Probleme werden durch fehlende Voraussetzungen verursacht. 

### <a name="agent-based-faults-fail-on-a-virtual-machine"></a>Agent-basierte Fehler auf einer VM
Agent-basierte Fehler können aus einer Vielzahl von Gründen fehlschlagen, die mit fehlenden Voraussetzungen in Zusammenhang stehen:
* Auf Linux-VMs muss für die Fehler [CPU Pressure](chaos-studio-fault-library.md#cpu-pressure), [Physical Memory Pressure](chaos-studio-fault-library.md#physical-memory-pressure), [Disk I/O pressure](chaos-studio-fault-library.md#disk-io-pressure-linux) und [Arbitrary Stress-ng Stress](chaos-studio-fault-library.md#arbitrary-stress-ng-stress) das Hilfsprogramm [stress-ng](https://wiki.ubuntu.com/Kernel/Reference/stress-ng) auf Ihrer VM installiert sein. Weitere Informationen zum Installieren von stress-ng finden Sie in den Abschnitten zu den Fehlervoraussetzungen.
* Auf Linux- oder Windows-VMs muss die vom Benutzer zugewiesene verwaltete Identität, die während der agent-basierten Zielaktivierung bereitgestellt wird, auch der VM hinzugefügt werden.
* Auf Linux- oder Windows-VMs muss der vom System zugewiesenen verwalteten Identität für das Experiment die Rolle „Leser“ auf der VM erteilt werden (scheinbar erhöhte Rollen wie „VM-Mitwirkender“ enthalten nicht den \*/Read-Vorgang, der erforderlich ist, damit der Chaos Studio-Agent-Dienst die Zielproxyressource microsoft-agent auf der VM lesen kann).

### <a name="aks-chaos-mesh-faults-fail"></a>Fehler bei AKS Chaos Mesh
AKS Chaos Mesh-Fehler können aus einer Vielzahl von Gründen fehlschlagen, die mit fehlenden Voraussetzungen in Zusammenhang stehen:
* Chaos Mesh muss zuerst im AKS-Cluster installiert werden, bevor die AKS Chaos Mesh-Fehler verwendet werden können. Weitere Anweisungen finden Sie im [Tutorial zu Chaos Mesh-Fehlern für AKS](chaos-studio-tutorial-aks-portal.md#set-up-chaos-mesh-on-your-aks-cluster).
* Chaos Mesh muss Version 2.0.4 oder höher aufweisen. Sie können die Chaos Mesh-Version abrufen, indem Sie eine Verbindung mit Ihrem AKS-Cluster herstellen und `helm version chaos-mesh` ausführen.
* Chaos Mesh muss mit dem Namespace `chaos-testing` installiert werden. Andere Namespacenamen werden für Chaos Mesh nicht unterstützt.
* Die Azure Kubernetes Service-Clusteradministratorrolle muss der vom System zugewiesenen verwalteten Identität für das Chaos-Experiment zugewiesen werden.

## <a name="issues-creating-or-designing-an-experiment"></a>Probleme beim Erstellen oder Entwerfen eines Experiments

### <a name="when-adding-a-fault-my-resource-does-not-show-in-the-target-resources-list"></a>Beim Hinzufügen eines Fehlers wird meine Ressource nicht in der Liste der Zielressourcen angezeigt
Wenn beim Hinzufügen eines Fehlers die Ressource, die Sie als Ziel für einen Fehler verwenden möchten, nicht in der Liste Zielressourcen angezeigt wird, kann dies an einem der folgenden Probleme liegen:
* Der Filter **Subscription** (Abonnement) ist so festgelegt, dass das Abonnement ausgeschlossen wird, in dem Ihr Ziel bereitgestellt wird. Klicken Sie auf den Abonnementfilter, und ändern Sie die ausgewählten Abonnements.
* Für die Ressource wurde noch kein Onboarding durchgeführt. Besuchen Sie die Ansicht **Targets** (Ziele), und aktivieren Sie das Ziel. Nachdem dieser Vorgang abgeschlossen wurde, müssen Sie den Bereich „Add Fault“ (Fehler hinzufügen) schließen und erneut öffnen, um eine aktualisierte Zielliste anzuzeigen.
* Die Ressource wurde noch nicht für den Zieltyp dieses Fehlers aktiviert. Sehen Sie in der [Fehlerbibliothek](chaos-studio-fault-library.md) nach, welcher Zieltyp für den Fehler verwendet wird, besuchen Sie dann die Ansicht **Targets** (Ziele), und aktivieren Sie diesen Zieltyp (entweder agent-basiert für microsoft-agent-Fehler oder service-direct für alle anderen Zieltypen). Nachdem dieser Vorgang abgeschlossen wurde, müssen Sie den Bereich „Add Fault“ (Fehler hinzufügen) schließen und erneut öffnen, um eine aktualisierte Zielliste anzuzeigen.
* Für die Ressource ist die Funktion für diesen Fehler noch nicht aktiviert. Schauen Sie den Funktionsnamen des Fehlers in der [Fehlerbibliothek](chaos-studio-fault-library.md) nach, besuchen Sie dann die Ansicht **Targets** (Ziele), und klicken Sie auf **Manage actions** (Aktionen verwalten) für die Zielressource. Aktivieren Sie das Kontrollkästchen für die Funktion, die dem Fehler entspricht, den Sie ausführen möchten, und klicken Sie auf **Save** (Speichern). Nachdem dieser Vorgang abgeschlossen wurde, müssen Sie den Bereich „Add Fault“ (Fehler hinzufügen) schließen und erneut öffnen, um eine aktualisierte Zielliste anzuzeigen.
* Das Onboarding der Ressource wurde erst vor Kurzem durchgeführt und noch nicht in Azure Resource Graph angezeigt. Die Liste der Zielressourcen wird aus Azure Resource Graph abgefragt, und nach der Aktivierung eines neuen Ziels kann es bis zu fünf Minuten dauern, bis die Aktualisierung in Azure Resource Graph angezeigt wird. Warten Sie einige Minuten, und öffnen Sie dann den Bereich „Add Fault“ (Fehler hinzufügen) erneut.

### <a name="when-creating-an-experiment-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity"></a>Beim Erstellen eines Experiments wird der Fehler `The microsoft:agent provider requires a managed identity` angezeigt

Dieser Fehler tritt auf, wenn der Agent nicht auf Ihrer VM bereitgestellt wurde. Installationsanweisungen finden Sie unter [Erstellen und Ausführen eines Experiments, das Agent-basierte Fehler verwendet](chaos-studio-tutorial-agent-based-portal.md).

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported"></a>Beim Erstellen eines Experiments wird der Fehler `The content media type '<null>' is not supported. Only 'application/json' is supported.` angezeigt

Dieser Fehler kann auftreten, wenn Sie Ihr Experiment mit einer ARM-Vorlage oder der Chaos Studio-REST-API erstellen. Der Fehler weist darauf hin, dass ihre Experimentdefinition falsch formatierten JSON-Code enthält. Überprüfen Sie mit einem JSON-Linter wie Visual Studio Code, ob Syntaxfehler vorliegen, z. B. fehlende geschweifte oder eckige Klammern ({} und \[\]).

## <a name="issues-running-an-experiment"></a>Probleme beim Ausführen eines Experiments

### <a name="the-execution-status-of-my-experiment-after-starting-is-failed"></a>Der Ausführungsstatus meines Experiments lautet nach dem Start „Failed“ (Fehler)

Klicken Sie in der Liste **Experiments** im Azure-Portal auf den Namen des Experiments, um die **Experimentübersicht** anzuzeigen. Klicken Sie im Abschnitt **History** (Verlauf) neben der fehlgeschlagenen Experimentausführung auf **Details**, um detaillierte Fehlerinformationen anzuzeigen.

![Experimentverlauf](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>Fehler bei meinem agent-basierten Fehler: Vergewissern Sie sich, dass das Onboarding des Ziels ordnungsgemäß durchgeführt wurde und dem Experiment-Msi die ordnungsgemäßen Leseberechtigungen erteilt wurden.

Dies kann passieren, wenn Sie das Onboarding des Agents mithilfe des Azure-Portals durchgeführt haben, das ein bekanntes Problem aufweist: Beim Aktivieren eines agent-basierten Ziels wird die vom Benutzer zugewiesene verwaltete Identität nicht der VM oder der VM-Skalierungsgruppe zugewiesen.

Navigieren Sie zum Beheben dieses Problems im Azure-Portal zur VM oder VM-Skalierungsgruppe, navigieren Sie zu **Identität**, öffnen Sie die Registerkarte **Benutzerseitig zugewiesen**, und fügen Sie der VM Ihre benutzerseitig zugewiesene Identität **hinzu**. Nach Abschluss dieses Vorgangs müssen Sie die VM möglicherweise neu starten, damit der Agent eine Verbindung herstellen kann.
