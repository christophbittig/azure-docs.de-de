---
title: 'Problembehandlung für Azure-Datenträgerpools (Vorschau): Übersicht'
description: Behandeln von Problemen mit Azure-Datenträgerpools Erfahren Sie mehr über häufige Fehlercodes und deren Behebung.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 878cec7021ebcd7c3b63ae4af1f29a0793977280
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277277"
---
# <a name="troubleshoot-azure-disk-pools-preview"></a>Problembehandlung für Azure-Datenträgerpools (Vorschau)

In diesem Artikel werden einige häufige Fehlercodes im Zusammenhang mit Azure-Datenträgerpools (Vorschau) aufgeführt. Außerdem werden mögliche Lösungen und Informationen zu den Status von Datenträgerpools aufgeführt.

## <a name="disk-pool-status"></a>Datenträgerpoolstatus

Datenträgerpools und iSCSI-Ziele haben jeweils vier Zustände: **Unbekannt**, **Wird ausgeführt**, **Wird aktualisiert** und **Beendet (Zuordnung aufgehoben)** .

**Unbekannt** bedeutet, dass sich die Ressource in einem schlechten oder unbekannten Zustand befindet. Um eine Wiederherstellung zu versuchen, führen Sie einen Aktualisierungsvorgang für die Ressource durch (z. B. Hinzufügen oder Entfernen von Datenträgern/LUNS), oder löschen Sie Ihren Datenträgerpool und stellen Sie ihn dann erneut bereit.

**Wird ausgeführt** bedeutet, dass die Ressource ausgeführt wird und sich in einem fehlerfreien Zustand befindet.

**Wird aktualisiert** bedeutet, dass die Ressource gerade aktualisiert wird. Dies geschieht in der Regel während der Bereitstellung oder bei der Anwendung eines Updates wie dem Hinzufügen von Datenträgern oder LUNs.

**Beendet (Zuordnung aufgehoben)** bedeutet, dass die Ressource beendet und die Zuordnung der ihr zugrunde liegenden Ressourcen aufgehoben wurde. Sie können die Ressource neu starten, um Ihren Datenträgerpool oder Ihr iSCSI-Ziel wiederherzustellen.

## <a name="common-failure-codes-when-deploying-a-disk-pool"></a>Häufige Fehlercodes beim Bereitstellen eines Datenträgerpools
 
|Code  |BESCHREIBUNG  |
|---------|---------|
|UnexpectedError     |Tritt normalerweise auf, wenn ein nicht behebbarer Back-End-Fehler auftritt. Versuchen Sie die Bereitstellung erneut. Wenn das Problem nicht behoben wurde, wenden Sie sich an den Azure-Support, und geben Sie die Nachverfolgungs-ID der Fehlermeldung an.         |
|DeploymentFailureZonalAllocationFailed     |Dies tritt auf, wenn Azure keine Kapazität mehr für die Bereitstellung einer VM in der angegebenen Region/Zone hat. Versuchen Sie die Bereitstellung zu einem anderen Zeitpunkt erneut.         |
|DeploymentFailureQuotaExceeded     |Das Abonnement, das zum Bereitstellen des Datenträgerpools verwendet wird, ist nicht mehr im VM-Kernkontingent in dieser Region enthalten. Sie können eine [Erhöhung des vCPU-Kontingentgrenzwerts pro Azure VM-Serie für die Dsv3-Serie anfordern](../azure-portal/supportability/per-vm-quota-requests.md).         |
|DeploymentFailurePolicyViolation     |Eine Richtlinie für das Abonnement verhindert die Bereitstellung von Azure-Ressourcen, die zur Unterstützung eines Datenträgerpools erforderlich sind. Weitere Informationen finden Sie in der Fehlermeldung.         |
|DeploymentTimeout     |Dies tritt auf, wenn die Bereitstellung der Datenträgerpoolinfrastruktur hängen bleibt und innerhalb der zugewiesenen Zeit nicht abgeschlossen wird. Versuchen Sie die Bereitstellung erneut. Wenn das Problem weiterhin besteht, wenden Sie sich an den Azure-Support, und geben Sie die Nachverfolgungs-ID der Fehlermeldung an.         |
|GoalStateApplicationTimeoutError     |Tritt auf, wenn die Datenträgerpoolinfrastruktur nicht mehr auf den Ressourcenanbieter reagiert. Vergewissern Sie sich, dass Sie die [Netzwerkvoraussetzungen](disks-pools-deploy.md#prerequisites) erfüllen, und versuchen Sie dann die Bereitstellung erneut. Wenn das Problem weiterhin besteht, wenden Sie sich an den Azure-Support, und geben Sie die Nachverfolgungs-ID des Fehlers an.         |
|OngoingOperationInProgress     |Für den Datenträgerpool wird derzeit ein fortlaufender Vorgang durchgeführt. Warten Sie, bis dieser Vorgang abgeschlossen ist, und versuchen Sie die Bereitstellung dann erneut.         |

## <a name="common-failure-codes-when-enabling-iscsi-on-disk-pools"></a>Häufige Fehlercodes beim Aktivieren von iSCSI in Datenträgerpools

|Code  |BESCHREIBUNG  |
|---------|---------|
|GoalStateApplicationError     |Tritt auf, wenn die iSCSI-Zielkonfiguration ungültig ist und nicht auf den Datenträgerpool angewendet werden kann. Versuchen Sie die Bereitstellung erneut. Wenn das Problem weiterhin besteht, wenden Sie sich an den Azure-Support, und geben Sie die Nachverfolgungs-ID des Fehlers an.         |
|GoalStateApplicationTimeoutError     |Tritt auf, wenn die Datenträgerpoolinfrastruktur nicht mehr auf den Ressourcenanbieter reagiert. Vergewissern Sie sich, dass Sie die [Netzwerkvoraussetzungen](disks-pools-deploy.md#prerequisites) erfüllen, und versuchen Sie dann die Bereitstellung erneut. Wenn das Problem weiterhin besteht, wenden Sie sich an den Azure-Support, und geben Sie die Nachverfolgungs-ID des Fehlers an.         |
|OngoingOperationInProgress     |Für den Datenträgerpool wird derzeit ein fortlaufender Vorgang durchgeführt. Warten Sie, bis dieser Vorgang abgeschlossen ist, und versuchen Sie die Bereitstellung dann erneut.         |

## <a name="next-steps"></a>Nächste Schritte

[Verwalten eines Datenträgerpools (Vorschau)](disks-pools-manage.md)
