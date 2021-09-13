---
title: Behandeln von häufigen Fehlern mit dem Onboarding von Azure Automanage
description: Häufige Fehler beim Onboarding von Automanage und deren Behandlung
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 7acca746acf17a6ef6dd837907a159cddb20be10
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004194"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Behandeln von häufigen Fehlern mit dem Onboarding von Automanage
Die Integration eines Computers in den Dienst durch Automanage kann fehlschlagen. In diesem Dokument wird erläutert, wie Sie Bereitstellungsfehler behandeln können, Sie erfahren einige häufige Gründe für das Fehlschlagen von Bereitstellungen, und es werden mögliche nächste Schritte bei der Entschärfung beschrieben.

## <a name="troubleshooting-deployment-failures"></a>Beheben von Problemen bei der Bereitstellung
Das Onboarding eines Computers in Automanage führt dazu, dass eine Azure Resource Manager-Bereitstellung erstellt wird. Weitere Informationen zu Fehlerursachen finden Sie in der Bereitstellung. In dem unten dargestellten Fehlerdetails-Flyout finden Sie Links zu den Bereitstellungen.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Fehlerdetail-Flyout von Automanage.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-machine"></a>Überprüfen der Bereitstellungen auf die Ressourcengruppe, die den fehlgeschlagenen Computer enthält
Das Fehler-Flyout beinhaltet einen Link zu den Bereitstellungen in der Ressourcengruppe, die den Computer mit dem Onboardingfehler enthält. Das Flyout umfasst zudem einen Präfixnamen, den Sie zum Filtern von Bereitstellungen verwenden können. Wenn Sie auf den Bereitstellungslink klicken, gelangen Sie zum Bereitstellungsblatt. Dort können Sie die Bereitstellungen filtern, um die Automanage-Bereitstellungen für Ihren Computer anzuzeigen. Wenn Sie in mehreren Regionen bereitstellen, sollten Sie sicherstellen, dass Sie auf die Bereitstellung in der richtigen Region klicken.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-machine"></a>Überprüfen der Bereitstellungen auf das Abonnement, das den fehlgeschlagenen Computer enthält
Wenn Sie in der Ressourcengruppenbereitstellung keine Fehler finden können, besteht Ihr nächster Schritt darin, die Bereitstellungen in Ihrem Abonnement zu untersuchen, das den Computer enthält, dessen Onboarding fehlgeschlagen ist. Klicken Sie auf den Link **Bereitstellungen für Abonnement** im Fehlerflyout, und filtern Sie Bereitstellungen mithilfe des Filters **Automanage-DefaultResourceGroup**. Verwenden Sie den Ressourcengruppennamen vom Blatt „Fehler“, um Bereitstellungen zu filtern. Dem Bereitstellungsnamen wird ein Regionsname als Suffix angefügt. Wenn Sie in mehreren Regionen bereitstellen, sollten Sie sicherstellen, dass Sie auf die Bereitstellung in der richtigen Region klicken.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Überprüfen von Bereitstellungen in einem Abonnement, das mit einem Log Analytics-Arbeitsbereich verknüpft ist
Wenn keine Bereitstellungen mit Fehler in der Ressourcengruppe oder dem Abonnement, die bzw. das Ihren Computer mit Fehler enthält, angezeigt werden, und wenn Ihr Computer mit Fehler mit einem Log Analytics-Arbeitsbereich in einem anderen Abonnement verbunden ist, wechseln Sie zu dem Abonnement, das mit Ihrem Log Analytics-Arbeitsbereich verknüpft ist, und suchen Sie nach Bereitstellungen mit Fehlern.

## <a name="common-deployment-errors"></a>Häufige Bereitstellungsfehler

Fehler |  Minderung
:-----|:-------------|
Fehler wegen unzureichender Berechtigungen des Automanage-Kontos | Dieser Fehler tritt auf, wenn Sie vor Kurzem ein Abonnement, das ein neues Automanage-Konto enthält, in einen neuen Mandanten verschoben haben. Schritte zum Beheben des Fehlers finden Sie [hier](./repair-automanage-account.md).
Die Region des Arbeitsbereichs entspricht nicht den Anforderungen an die Regionszuordnung | Automanage konnte kein Onboarding Ihres Computers durchführen, weil der Log Analytics-Arbeitsbereich, mit dem der Computer zurzeit verknüpft ist, keiner unterstützten Automation-Region zugeordnet ist. Stellen Sie sicher, dass sich Ihr vorhandener Log Analytics-Arbeitsbereich und das Automation-Konto in einer [unterstützten Zuordnungsregion](../automation/how-to/region-mappings.md) befinden.
„Access denied because of the deny assignment with name 'System deny assignment created by managed application“ (Der Zugriff wurde aufgrund der Ablehnungszuweisung mit dem Namen „Durch eine verwaltete Anwendung erstellte Systemablehnungszuweisung“ verweigert) | Eine [denyAssignment](../role-based-access-control/deny-assignments.md)-Klasse wurde für Ihre Ressource erstellt, durch die Automanage am Zugriff auf Ihre Ressource gehindert wurde. Diese denyAssignment-Klasse wurde möglicherweise von einer [Blaupause](../governance/blueprints/concepts/resource-locking.md) oder von einer [verwalteten Anwendung](../azure-resource-manager/managed-applications/overview.md) erstellt.
„OS Information: Name='(null)', ver='(null)', agent status='Not Ready'.“ (Betriebssysteminformationen: Name='(null)', Version='(null)', Agent-Status='Nicht bereit'.) | Stellen Sie sicher, dass Sie eine [unterstützte Agent-Mindestversion](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) ausführen, dass der Agent ausgeführt wird ([Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) und [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) und dass der Agent auf dem neuesten Stand ist ([Linux](../virtual-machines/extensions/update-linux-agent.md) und [Windows](../virtual-machines/extensions/agent-windows.md)).
„Unable to determine the OS for the VM OS Name:, ver . Please check that the VM Agent is running, the current status is Ready.“ (Das Betriebssystem für VM-Betriebssystemname, Version konnte nicht ermittelt werden. Stellen Sie sicher, dass der VM-Agent ausgeführt wird und dass der aktuelle Status „Bereit“ lautet.) | Stellen Sie sicher, dass Sie eine [unterstützte Agent-Mindestversion](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) ausführen, dass der Agent ausgeführt wird ([Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) und [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) und dass der Agent auf dem neuesten Stand ist ([Linux](../virtual-machines/extensions/update-linux-agent.md) und [Windows](../virtual-machines/extensions/agent-windows.md)).
„Die VM hat beim Verarbeiten der Erweiterung ‚IaaSAntimalware‘ einen Fehler gemeldet“ | Vergewissern Sie sich, dass auf Ihrer VM noch kein anderes Antischadsoftware-/Antivirenangebot installiert wurde. Falls das Problem weiterhin besteht, wenden Sie sich an den Support.
ASC-Arbeitsbereich: Automanage unterstützt derzeit nicht den Log Analytics-Dienst in _Standort_. | Überprüfen Sie, ob sich Ihre VM in einer [unterstützten Region](./automanage-virtual-machines.md#supported-regions) befindet.
Bei der Vorlagenbereitstellung ist aufgrund einer Richtlinienverletzung ein Fehler aufgetreten. Weitere Informationen finden Sie in den Details. | Es gibt eine Richtlinie, die das Onboarding Ihrer VM durch Automanage verhindert. Überprüfen Sie die Richtlinien, die für Ihr Abonnement oder Ihre Ressourcengruppe mit ihrer VM gelten, die Sie in Automanage integrieren möchten.
„Fehler bei der Zuweisung. Es gibt keine zusätzlichen Informationen.“ | Öffnen Sie einen Fall beim Microsoft Azure-Support.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure Automanage finden Sie [hier](./automanage-virtual-machines.md).

> [!div class="nextstepaction"]
> [Aktivieren von Automanage für Computer im Azure-Portal](quick-create-virtual-machines-portal.md)