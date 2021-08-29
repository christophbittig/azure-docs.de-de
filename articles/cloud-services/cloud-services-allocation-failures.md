---
title: Behandeln von Zuordnungsfehlern von Clouddiensten (klassisch) | Microsoft-Dokumentation
description: Behandeln eines Zuordnungsfehlers bei der Bereitstellung von Azure Cloud Services. Erfahren Sie, wie die Zuordnung funktioniert und warum bei der Zuordnung ein Fehler auftreten kann.
ms.topic: troubleshooting
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f8aaff0cf7261dc8fa845a178488dd449066ff85
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824794"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Behandeln von Zuordnungsfehlern bei der Bereitstellung von Cloud Services (klassisch) in Azure

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]


## <a name="summary"></a>Zusammenfassung

Wenn Sie Instanzen für einen Clouddienst bereitstellen oder neue Instanzen von Web- oder Workerrollen hinzufügen, weist Microsoft Azure Compute-Ressourcen zu. Unter Umständen erhalten Sie beim Ausführen dieser Schritte auch dann gelegentlich Fehler, bevor Sie die Grenzwerte des Azure-Abonnements erreichen. In diesem Artikel werden die Ursachen einiger häufig auftretender Zuordnungsfehler erläutert und mögliche Abhilfemaßnahmen vorgeschlagen. Diese Informationen können auch hilfreich sein, wenn Sie die Bereitstellung Ihrer Dienste planen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Hintergrund – Funktionsweise der Zuordnung

Für die Server in Azure-Rechenzentren wird eine Partitionierung in Cluster vorgenommen. Es wird eine neue Zuordnungsanforderung für einen Clouddienst in mehreren Clustern versucht. Wenn die erste Instanz in einem Clouddienst bereitgestellt wird (entweder in Staging oder Produktion), wird der Clouddienst fest mit einem Cluster verknüpft. Alle weiteren Bereitstellungen für den Clouddienst werden im selben Cluster ausgeführt. In diesem Artikel wird dies als „verknüpft mit einem Cluster“ bezeichnet. In Diagramm 1 unten ist eine normale Zuordnung dargestellt, für die versucht wird, sie für mehrere Cluster durchzuführen. In Diagramm 2 ist eine Zuordnung zu sehen, die mit Cluster 2 verknüpft ist, da dies der Ort ist, an dem der vorhandene Clouddienst „CS_1“ gehostet wird.

![Zuordnungsdiagramm](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Gründe für Zuordnungsfehler

Wenn eine Zuordnungsanforderung mit einem Cluster verknüpft ist, ist die Wahrscheinlichkeit höher, dass keine freien Ressourcen gefunden werden, da der verfügbare Ressourcenpool auf einen Cluster beschränkt ist. Falls Ihre Zuordnungsanforderung mit einem Cluster verknüpft ist, der von Ihnen angeforderte Ressourcentyp für diesen Cluster aber nicht unterstützt wird, schlägt die Anforderung auch dann fehl, wenn der Cluster über eine freie Ressource verfügt. In Diagramm 3 unten ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, da der einzige Kandidatencluster keine freien Ressourcen aufweist. In Diagramm 4 ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, weil der einzige Kandidatencluster die angeforderte Größe des virtuellen Computers nicht unterstützt, obwohl der Cluster über freie Ressourcen verfügt.

![Verknüpfte Zuordnung, Fehler](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Behandeln von Zuweisungsfehlern für Clouddienste

### <a name="error-message"></a>Fehlermeldung

Navigieren Sie im Azure-Portal zu Ihrem Clouddienst, und wählen Sie in der Randleiste *Vorgangsprotokolle (klassisch)* aus, um die Protokolle anzuzeigen.

Im Folgenden sind Lösungen für weitere Ausnahmen aufgeführt:

|Ausnahmetyp  |Fehlermeldung  |Lösung  |
|---------|---------|---------|
|FabricInternalServerError     |Fehler beim Vorgang. Fehlercode „InternalError“ und errorMessage „Interner Serverfehler. Versuchen Sie die Anfrage noch einmal.“|[Problembehandlung bei FabricInternalServerError](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |Fehler beim Vorgang. Fehlercode „InternalError“ und errorMessage „Interner Serverfehler. Versuchen Sie die Anfrage noch einmal.“|[Problembehandlung bei ServiceAllocationFailure](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |Fehler beim Vorgang „`{Operation ID}`“: „Der angeforderte VM-Tarif ist in „`{Region ID}`“ für dieses Abonnement zurzeit nicht verfügbar. Verwenden Sie einen anderen Tarif, oder führen Sie die Bereitstellung an einem anderen Standort durch.“|[Problembehandlung bei LocationNotFoundForRoleSize](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |Fehler bei Azure-Vorgang '`{Operation ID}`‘ mit dem Code Compute.ConstrainedAllocationFailed. Details: Fehler bei der Zuordnung. Einschränkungen in der Anforderung konnten nicht erfüllt werden. Die angeforderte neue Dienstbereitstellung ist an eine Affinitätsgruppe gebunden oder auf ein virtuelles Netzwerk ausgerichtet, oder unter diesem gehosteten Dienst befindet sich eine vorhandene Bereitstellung. Alle diese Bedingungen beschränken die neue Bereitstellung auf bestimmte Azure-Ressourcen. Wiederholen Sie den Vorgang später. Reduzieren Sie die VM-Größe oder die Anzahl der Rolleninstanzen. Sie können, wenn möglich, auch die zuvor erwähnten Einschränkungen entfernen oder den virtuellen Computer in einer anderen Region bereitstellen.|[Problembehandlung bei ConstrainedAllocationFailed](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |Die für diese Bereitstellung erforderliche VM-Größe (oder Kombination aus VM-Größen) kann aufgrund von Einschränkungen bei der Bereitstellungsanforderung nicht bereitgestellt werden. Lockern Sie nach Möglichkeit Einschränkungen wie z. B. die Bindungen des virtuellen Netzwerks, versuchen Sie es mit einer Bereitstellung in einem gehosteten Dienst, der keine weitere Bereitstellung enthält, in einer anderen Affinitätsgruppe oder ohne Affinitätsgruppe, oder führen Sie die Bereitstellung in einer anderen Region durch.|[Problembehandlung bei OverconstrainedAllocationRequest](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

Beispiel für Fehlermeldung:

> „Fehler bei Azure-Vorgang '{Vorgangs-ID}“ mit dem Code Compute.ConstrainedAllocationFailed. Details: Fehler bei der Zuordnung. Einschränkungen in der Anforderung konnten nicht erfüllt werden. Die angeforderte neue Dienstbereitstellung ist an eine Affinitätsgruppe gebunden oder auf ein virtuelles Netzwerk ausgerichtet, oder unter diesem gehosteten Dienst befindet sich eine vorhandene Bereitstellung. Alle diese Bedingungen beschränken die neue Bereitstellung auf bestimmte Azure-Ressourcen. Wiederholen Sie den Vorgang später. Reduzieren Sie die VM-Größe oder die Anzahl der Rolleninstanzen. Sie können, wenn möglich, auch die zuvor erwähnten Einschränkungen entfernen oder den virtuellen Computer in einer anderen Region bereitstellen.“

### <a name="common-issues"></a>Häufige Probleme

Dies sind häufig vorkommende Zuordnungsszenarios, die bewirken, dass eine Zuordnungsanforderung mit einem einzelnen Cluster „verknüpft“ wird.

* Bereitstellung in Stagingslots: Besitzt ein Clouddienst eine Bereitstellung in einem der Slots, wird der gesamte Clouddienst mit einem bestimmten Cluster verknüpft.  Wenn also bereits eine Bereitstellung im Produktionsslot vorhanden ist, kann eine neue Stagingbereitstellung nur demselben Cluster wie der Produktionsslot zugeordnet werden. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl.
* Skalierung: Beim Hinzufügen neuer Instanzen zu einem vorhandenen Clouddienst muss die Zuordnung in demselben Cluster erfolgen.  Kleine Skalierungsanfragen können i. d. R. zugeordnet werden können, dies gilt aber nicht immer. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl.
* Affinitätsgruppe: Eine neue Bereitstellung für einen leeren Clouddienst kann von dem Fabric in jedem Cluster in dieser Region zugeordnet werden, es sei denn, der Clouddienst ist mit einer Affinitätsgruppe verknüpft. Bereitstellungen in derselben Affinitätsgruppe werden auch im selben Cluster versucht. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl.
* Affinitätsgruppenbasiertes VNET: Ältere virtuelle Netzwerke waren mit Affinitätsgruppen anstelle von Regionen verknüpft, und Clouddienste in diesen virtuellen Netzwerken wurden mit dem Cluster der Affinitätsgruppe verknüpft. Bereitstellungen für diesen Typ von virtuellen Netzwerken werden im verknüpften Cluster versucht. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl.

## <a name="solutions"></a>Lösungen

1. Erneute Bereitstellung in einem neuen Clouddienst: Diese Lösung ist wahrscheinlich am erfolgreichsten, da hierbei die Plattform aus allen Clustern in dieser Region auswählen kann.

   * Bereitstellen der Workload in einem neuen Clouddienst  
   * Aktualisieren des CNAME- oder A-Datensatzes zur Umleitung des Datenverkehrs an den neuen Clouddienst
   * Wenn kein Datenverkehr mehr an die alte Website geleitet wird, können Sie den alten Clouddienst löschen. Bei dieser Lösung sollten keine Ausfallzeiten entstehen.
2. Löschen von Produktions- und Stagingslots: Bei dieser Lösung behalten Sie Ihren vorhandenen DNS-Namen, sie führt aber zu Ausfallzeiten der Anwendung.

   * Löschen Sie die Produktions- und Stagingslots eines vorhandenen Clouddiensts, sodass der Clouddienst leer ist.
   * Erstellen Sie dann eine neue Bereitstellung im vorhandenen Clouddienst. Damit wird eine erneute Zuweisung auf allen Clustern in der Region versucht. Stellen Sie sicher, dass der Clouddienst mit keiner Affinitätsgruppe verknüpft ist.
3. Reservierte IP: Bei dieser Lösung behalten Sie Ihre IP-Adresse bei, sie führt aber zu Ausfallzeiten der Anwendung.  

   * Erstellen Sie mithilfe von PowerShell eine reservierte IP-Adresse für die vorhandene Bereitstellung:

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * Befolgen Sie Lösung 2 oben, und geben Sie die neue reservierte IP-Adresse in der CSCFG des Diensts an.
4. Entfernen der Affinitätsgruppe für neue Bereitstellungen: Affinitätsgruppen werden nicht mehr empfohlen. Führen Sie die Schritte für Lösung 1 oben aus, um einen neuen Clouddienst bereitzustellen. Stellen Sie sicher, dass der Clouddienst zu keiner Affinitätsgruppe gehört.
5. Konvertieren in ein regionales virtuelles Netzwerk: Weitere Informationen finden Sie unter [Migrieren von Affinitätsgruppen zu einem regionalen virtuellen Netzwerk (VNet)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet).
