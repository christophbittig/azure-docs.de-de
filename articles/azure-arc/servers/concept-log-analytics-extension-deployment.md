---
title: Bereitstellen eines Log Analytics-Agents auf Arc-fähigen Servern
description: In diesem Artikel werden die verschiedenen Methoden zum Bereitstellen des Log Analytics-Agents auf Windows- und Linux-Computern beschrieben, die bei Azure Arc-fähigen Servern in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung registriert sind.
ms.date: 10/22/2021
ms.topic: conceptual
ms.openlocfilehash: 5ba1ccc8407903ca40677938b19846a7e6c205a0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369453"
---
# <a name="understand-deployment-options-for-the-log-analytics-agent-on-azure-arc-enabled-servers"></a>Grundlegendes zu Bereitstellungsoptionen für den Log Analytics-Agent auf Azure Arc-fähigen Servern

Azure Monitor unterstützt mehrere Methoden, um den Log Analytics-Agent zu installieren und Ihren Computer oder Server, der bei Azure Arc-fähigen Servern registriert ist, mit dem Dienst zu verbinden. Azure Arc-fähige Server unterstützen das Azure-VM-Erweiterungsframework, das Konfigurations- und Automatisierungsaufgaben nach der Bereitstellung bietet, sodass Sie die Verwaltung Ihrer Hybridcomputer wie bei Azure-VMs vereinfachen können.

Der Log Analytics-Agent ist für Folgendes erforderlich:

* Zum Überwachen des Betriebssystems und der Workloads, die auf dem Computer oder Server ausgeführt werden, mit [VM Insights](../../azure-monitor/vm/vminsights-overview.md). Zum weiteren Analysieren und Warnen mithilfe anderer Features von [Azure Monitor](../../azure-monitor/overview.md).
* Führen Sie die Sicherheitsüberwachung in Azure mithilfe von [Microsoft Defender für Cloud](../../defender-for-cloud/defender-for-cloud-introduction.md) oder [Microsoft Sentinel](../../sentinel/overview.md) aus.
* Zum Verwalten von Betriebssystemupdates mit der [Updateverwaltung von Azure Automation](../../automation/update-management/overview.md).
* Zum Erfassen von Bestands- und Nachverfolgungsänderungen mithilfe von [Änderungsnachverfolgung und Bestand von Azure Automation](../../automation/change-tracking/overview.md).
* Zum Ausführen von Automation-Runbooks mithilfe eines [Hybrid Runbook Workers von Azure Automation](../../automation/automation-hybrid-runbook-worker.md) direkt auf dem Computer und für Ressourcen in der Umgebung.

In diesem Artikel werden die Bereitstellungsmethoden für die VM-Erweiterung des Log Analytics-Agents auf mehreren physischen Produktionsservern oder virtuellen Computern in der Umgebung beschrieben. So können Sie herausfinden, welche für Ihre Organisation am besten funktioniert. Wenn Sie an dem neuen Azure Monitor-Agent interessiert sind und einen detaillierten Vergleich lesen möchten, sehen Sie sich den Artikel [Übersicht über Azure Monitor-Agents](../../azure-monitor//agents/agents-overview.md) an.  

## <a name="installation-options"></a>Installationsoptionen

Sie können verschiedene Methoden verwenden, um die VM-Erweiterung zu installieren, entweder mit einer Methode oder einer Kombination. In diesem Abschnitt wird jede davon beschrieben.

### <a name="using-arc-enabled-servers"></a>Verwenden von Arc-fähigen Servern

Diese Methode unterstützt die Installation, Verwaltung und Entfernung von VM-Erweiterungen aus dem [Azure-Portal](manage-vm-extensions-portal.md) mithilfe von [PowerShell](manage-vm-extensions-powershell.md), der [Azure CLI](manage-vm-extensions-cli.md) oder einer [Azure Resource Manager-Vorlage (ARM)](manage-vm-extensions-template.md).

#### <a name="advantages"></a>Vorteile

* Kann zu Testzwecken verwendet werden.
* Ist nützlich, wenn Sie mehrere Computer verwalten müssen.

#### <a name="disadvantages"></a>Nachteile

* Eingeschränkte Automatisierung bei der Verwendung einer Azure Resource Manager-Vorlage, andernfalls zeitaufwändig.
* Kann sich nur mit einem einzelnen Arc-fähigen Server befassen, nicht mit mehreren Instanzen.
* Unterstützt nur die Angabe eines einzelnen Arbeitsbereich zum Senden von Berichten. Benötigt PowerShell oder die Azure CLI zum Konfigurieren der VM-Erweiterung des Log Analytics-Agents für Windows, um Berichte an bis zu vier Arbeitsbereiche zu senden.
* Unterstützt die Bereitstellung des Dependency-Agents über das Portal nicht. Sie können nur PowerShell, die Azure CLI, oder eine ARM-Vorlage verwenden.

### <a name="using-azure-policy"></a>Verwenden von Azure Policy

Sie können Azure Policy verwenden, um die VM-Erweiterung des Log Analytics-Agents im großen Stil auf Computern in Ihrer Umgebung bereitzustellen und die Konfigurationscompliance aufrechtzuerhalten. Dies wird erreicht, indem Sie entweder die Richtliniendefinition **Log Analytics-Erweiterung auf Azure Arc-fähigen Linux-Servern konfigurieren** / **Log Analytics-Erweiterung auf Azure Arc-fähigen Windows-Servern konfigurieren** oder die Richtlinieninitiative **Azure Monitor für VMs aktivieren** verwenden.

Azure Policy enthält mehrere vordefinierte Definitionen im Zusammenhang mit Azure Monitor. Eine umfassende Liste der integrierten Richtlinien in der Kategorie **Überwachung** finden Sie unter [Integrierte Azure Policy-Definitionen für Azure Monitor](../../azure-monitor/policy-reference.md).

#### <a name="advantages"></a>Vorteile

* Wenn die VM-Erweiterung entfernt wird, wird sie nach der Richtlinienauswertung neu installiert.
* Identifiziert und installiert die VM-Erweiterung, wenn ein neuer Azure Arc-fähiger Server bei Azure registriert wird.
* Unterstützt nur die Angabe eines einzelnen Arbeitsbereich zum Senden von Berichten. Benötigt PowerShell oder die Azure CLI zum Konfigurieren der VM-Erweiterung des Log Analytics-Agents für Windows, um Berichte an bis zu vier Arbeitsbereiche zu senden.

#### <a name="disadvantages"></a>Nachteile

* Die Richtlinie **Konfigurieren der Log Analytics-Erweiterung auf Azure Arc-fähigen** *Betriebssystem* **Servern** installiert nur die VM-Erweiterung von Log Analytics und konfiguriert den Agent so, dass er Berichte an einen angegebenen Log Analytics-Arbeitsbereich sendet. Wenn Sie an VM Insights interessiert sind, um die Betriebssystemleistung zu überwachen und ausgeführte Prozesse und Abhängigkeiten von anderen Ressourcen zuzuordnen, wenden Sie die Richtlinieninitiative **Azure Monitor für VMs aktivieren** an. Sie installiert und konfiguriert sowohl die VM-Erweiterung von Log Analytics als auch die VM-Erweiterung des Dependency-Agents, die beide erforderlich sind.
* Der Standard-Complianceauswertungszyklus findet einmal alle 24 Stunden statt. Ein Auswertungsscan für ein Abonnement oder eine Ressourcengruppe kann mit Azure CLI, Azure PowerShell, einem Aufruf an die REST-API oder mithilfe der GitHub-Aktion für die Azure Policy-Konformitätsprüfung gestartet werden. Weitere Informationen finden Sie unter [Auswertungsauslöser](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

### <a name="using-azure-automation"></a>Verwenden von Azure Automation

Die Betriebsumgebung zur Prozessautomatisierung in Azure Automation und die Unterstützung für PowerShell- und Python-Runbooks ermöglichen es Ihnen, die Bereitstellung der VM-Erweiterung des Log Analytics-Agents auf Computern in Ihrer Umgebung im großen Stil zu automatisieren.

#### <a name="advantages"></a>Vorteile

* Kann eine Skriptmethode verwenden, um die Bereitstellung und Konfiguration mithilfe von Skriptsprachen zu automatisieren, mit denen Sie vertraut sind.
* Wird nach einem Zeitplan ausgeführt, den Sie definieren und steuern.
* Authentifiziert sicher mithilfe einer verwalteten Identität über das Automation-Konto zu Arc-fähigen Servern.

#### <a name="disadvantages"></a>Nachteile

* Erfordert ein Azure Automation-Konto.
* Erfordert Erfahrung mit der Erstellung und Verwaltung von Runbooks in Azure Automation.
* Erfordert je nach Zielbetriebssystem das Erstellen eines Runbooks basierend auf PowerShell oder Python.

## <a name="next-steps"></a>Nächste Schritte

* Um Betriebssystemupdates mithilfe der Updateverwaltung von Azure Automation zu verwalten, lesen Sie [Aktivieren über ein Automation-Konto](../../automation/update-management/enable-from-automation-account.md), und führen Sie dann die Schritte zum Aktivieren von Computern aus, die Berichte an den Arbeitsbereich senden.

* Um Änderungen mithilfe von Änderungsnachverfolgung und Bestand von Azure Automation nachzuverfolgen, lesen Sie [Aktivieren über ein Automation-Konto](../../automation/change-tracking/enable-from-automation-account.md), und führen Sie dann die Schritte zum Aktivieren von Computern aus, die Berichte an den Arbeitsbereich senden.

* Sie können das Benutzerfeature Hybrid Runbook Worker von Azure Automation verwenden, um Runbooks direkt auf Servern oder Computern auszuführen, die bei Arc-fähigen Servern registriert sind. Weitere Informationen finden Sie im Artikel [Bereitstellen der VM-Erweiterung für den Hybrid Runbook Worker](../../automation/extension-based-hybrid-runbook-worker-install.md).

* Informationen zum Sammeln von sicherheitsbezogenen Ereignissen mit Microsoft Sentinel finden Sie unter Onboarding in [Microsoft Sentinel](scenario-onboard-azure-sentinel.md), oder zum Sammeln mit Microsoft Defender für Cloud finden Sie Informationen unter Onboarding in[Microsoft Defender für Cloud](../../security-center/quickstart-onboard-machines.md).

* Lesen Sie die Artikel [Leistung überwachen](../../azure-monitor/vm/vminsights-performance.md) und [Abhängigkeiten zuordnen](../../azure-monitor/vm/vminsights-maps.md) von VM Insights, um die Leistung Ihres Computers herauszufinden und erkannte Anwendungskomponenten einzusehen.
