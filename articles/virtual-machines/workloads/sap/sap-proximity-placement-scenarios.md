---
title: Azure-Näherungsplatzierungsgruppen bei SAP-Anwendungen | Microsoft-Dokumentation
description: Beschreibt SAP-Bereitstellungsszenarien mit Azure-Näherungsplatzierungsgruppen
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccbced3ce40856f858fa7e0028a27c1e1c59460
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484669"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen

> [!IMPORTANT]
> Im November 2021 haben wir erhebliche Änderungen an der Verwendung von Näherungsplatzierungsgruppen mit SAP-Workload in zonenbezogenen Bereitstellungen vorgenommen. 

SAP-Anwendungen, die auf der SAP NetWeaver- oder SAP S/4HANA-Architektur basieren, sind von der Netzwerklatenz zwischen der SAP-Anwendungsschicht und der SAP-Datenbankschicht abhängig. Diese Abhängigkeit ist das Ergebnis davon, dass der größte Teil der Geschäftslogik in der Anwendungsschicht ausgeführt wird. Da die SAP-Anwendungsschicht die Geschäftslogik ausführt, gibt sie mit einer hohen Frequenz Abfragen an die Datenbankschicht aus (tausende oder zehntausende von Abfragen pro Sekunde). In den meisten Fällen handelt es sich um einfache Abfragen. Sie können oft in 500 Mikrosekunden oder noch kürzerer Zeit in der Datenbankschicht ausgeführt werden.

Die Zeit, die im Netzwerk verbracht wird, um eine solche Abfrage von der Anwendungsschicht an die Datenbankschicht zu senden, und das zurückgesendete Ergebnis beeinflussen maßgeblich die Zeit, die zur Ausführung von Geschäftsprozessen benötigt wird. Diese Empfindlichkeit gegenüber Netzwerklatenzen ist der Grund, warum es sinnvoll ist, in SAP-Bereitstellungsprojekten eine bestimmte minimale Netzwerklatenz zu erreichen. Weitere Informationen finden Sie im [SAP-Hinweis 1100926 – Häufig gestellte Fragen: Netzwerkleistung](https://launchpad.support.sap.com/#/notes/1100926/E). Dort finden Sie einige Richtlinien zum Klassifizieren der Netzwerklatenz.

In vielen Azure-Regionen ist die Zahl der Rechenzentren gestiegen. Gleichzeitig verwenden Kunden, insbesondere für High-End-SAP-Systeme, speziellere VM-Familien wie die M- oder Mv2-Familie oder in seltenen Fällen große HANA-Instanzen. Diese Azure-VM-Typen sind nicht immer in jedem der Rechenzentren verfügbar, die in einer Azure-Region gesammelt werden. Diese Tatsachen können die Chance eröffnen, die Netzwerklatenz zwischen der SAP-Anwendungsschicht und der SAP DBMS-Schicht zu optimieren.

Damit Sie die Möglichkeit zum Optimieren der Netzwerklatenz erhalten, bietet Azure [Näherungsplatzierungsgruppen](../../co-location.md). Näherungsplatzierungsgruppen können verwendet werden, um die Gruppierung verschiedener VM-Typen unter einer einzigen Netzwerkunterteilung zu erzwingen, die eine ausreichende geringe Netzwerklatenz zwischen diesen verschiedenen VM-Typen bietet, sofern sie bisher noch nicht bereitgestellt wurden. Im Prozess der Bereitstellung der ersten VM in einer solchen Näherungsplatzierungsgruppe wird die VM an ein bestimmtes Netzwerk-Rückgrat gebunden. Wie alle anderen VMs, die in derselben Näherungsplatzierungsgruppe bereitgestellt werden sollen, werden diese VMs unter demselben Netzwerk-Rückgrat gruppiert. So reizvoll diese Aussicht klingt, die Nutzung dieses Konstrukts bringt auch einige Einschränkungen und Nachteile mit sich:

- Sie können nicht davon ausgehen, dass alle Azure-VM-Typen in allen Azure-Rechenzentren oder in jedem Netzwerk-Rückgrat verfügbar sind. Das hat zur Folge, dass die Kombination verschiedener VM-Typen innerhalb einer Näherungsplatzierungsgruppe stark eingeschränkt sein kann. Diese Einschränkungen treten auf, weil die Hosthardware, die zum Ausführen eines bestimmten VM-Typs benötigt wird, möglicherweise nicht im Rechenzentrum oder unter dem Netzwerk-Rückgrat vorhanden ist, dem die Näherungsplatzierungsgruppe zugewiesen wurde.
- Wenn Sie die Größe von Teilen der VMs ändern, die sich in einer Näherungsplatzierungsgruppe befinden, können Sie nicht automatisch davon ausgehen, dass der neue VM-Typ in allen Fällen im selben Rechenzentrum oder unter dem Netzwerk-Rückgrat verfügbar ist, dem die Näherungsplatzierungsgruppe zugewiesen wurde.
- Wenn Azure Hardware außer Betrieb nimmt, werden bestimmte VMs einer Näherungsplatzierungsgruppe möglicherweise in ein anderes Azure-Rechenzentrum oder Netzwerk-Rückgrat gezwungen. Ausführliche Informationen zu diesem Fall finden Sie im Dokument [Näherungsplatzierungsgruppen](../../co-location.md#planned-maintenance-and-proximity-placement-groups)  

> [!IMPORTANT]
> Als Ergebnis der potenziellen Einschränkungen sollten diese Regeln nur für die Verwendung von Näherungsplatzierungsgruppen beachtet werden:
>
> - Bei Bedarf in bestimmten Szenarien (siehe weiter unten)
> - Wenn die Netzwerklatenz zwischen Anwendungsebene und DBMS-Ebene zu hoch ist und sich auf die Workload auswirkt
> - Nur im Detailgrad eines einzelnen SAP-Systems einsetzen, nicht für eine gesamte Systemlandschaft oder eine gesamte SAP-Landschaft
> - So einsetzen, dass die verschiedenen VM-Typen und die Anzahl der VMs innerhalb einer Näherungsplatzierungsgruppe so gering wie möglich gehalten wird


Bisher haben Sie Näherungsplatzierungsgruppen in folgenden Szenarien verwendet:

- Bereitstellen von SAP-Workloads mit Verfügbarkeitsgruppen. Dabei wurden die SAP-Datenschicht, die SAP-Logikschicht und ASCS/SCS-VMs in drei verschiedenen Verfügbarkeitsgruppen gruppiert. In einem solchen Fall sollten Sie sicherstellen, dass die Verfügbarkeitsgruppen nicht auf die gesamte Azure-Region verteilt sind, da dies abhängig von der Azure-Region zu einer Netzwerklatenz führen könnte, die sich negativ auf die SAP-Workload auswirken könnte.
- Sie sollten die kritischen Ressourcen Ihrer SAP-Workload über verschiedene Verfügbarkeitszonen bereitstellen und andererseits sicherstellen, dass die VMs der Anwendungsebene in jeder der Zonen mithilfe von Verfügbarkeitsgruppen auf verschiedene Fehlerdomänen verteilt werden. In diesem Fall, wie später in dem Dokument beschrieben, sind die Näherungsplatzierungsgruppen das nötige Verbindungsstück.
- Sie haben Näherungsplatzierungsgruppen verwendet, um VMs zu gruppieren, um eine optimale Netzwerklatenz zwischen den auf den VMs gehosteten Diensten zu erzielen.

Wie bei Bereitstellungsszenario 1 ist in vielen Regionen, insbesondere in Regionen ohne Verfügbarkeitszonen und den meisten Regionen mit Verfügbarkeitszonen, die Netzwerklatenz unabhängig davon, wo die VMs landen, akzeptabel. Es gibt jedoch einige Regionen von Azure, die ohne die Anordnung der drei verschiedenen Verfügbarkeitsgruppen mit der Verwendung von Verfügbarkeitsgruppen keine ausreichend gute Erfahrung bieten können. Ab Bereitstellungsszenario 2 empfehlen wir in den folgenden Abschnitten dieses Dokuments eine andere Methode zur Verwendung von Näherungsplatzierungsgruppen.



## <a name="what-are-proximity-placement-groups"></a>Was sind Näherungsplatzierungsgruppen? 
Eine Azure-Näherungsplatzierungsgruppe ist ein logisches Konstrukt. Wenn eine Näherungsplatzierungsgruppe definiert wird, wird sie an eine Azure-Region und eine Azure-Ressourcengruppe gebunden. Wenn VMs bereitgestellt werden, wird wie folgt auf eine Azure-Näherungsplatzierungsgruppe verwiesen:

- Die erste Azure-VM, die unter einem Netzwerk-Rückgrat bereitgestellt wird, ist mit vielen Azure-Compute-Einheiten und geringer Netzwerklatenz ausgestattet. Ein solches Netzwerk entspricht häufig einem einzelnen Azure-Rechenzentrum. Sie können sich die erste VM als „Bereichs-VM“ vorstellen, der in einer Compute-Skalierungseinheit basierend auf Azure-Zuteilungsalgorithmen bereitgestellt wird, die schließlich mit Bereitstellungsparametern kombiniert werden.
- Alle nachfolgenden bereitgestellten VMs, die auf die Näherungsplatzierungsgruppe verweisen, werden unter demselben Netzwerk-Rückgrat bereitgestellt wie der erste virtuelle Computer.

> [!NOTE]
> Wenn keine Hosthardware bereitgestellt wird, die einen bestimmten VM-Typ unter dem Netzwerk-Rückgrat ausführen kann, in dem die erste VM platziert wurde, ist die Bereitstellung des angeforderten VM-Typs nicht erfolgreich. Sie erhalten eine Zuteilungsfehlermeldung, die angibt, dass die VM im Umkreis der Näherungsplatzierungsgruppe nicht unterstützt werden kann.

Einer einzelnen [Azure-Ressourcengruppe](../../../azure-resource-manager/management/manage-resources-portal.md) können mehrere Näherungsplatzierungsgruppen zugewiesen werden. Eine Näherungsplatzierungsgruppe kann jedoch nur einer Azure-Ressourcengruppe zugeordnet werden.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Näherungsplatzierungsgruppen mit SAP-Systemen, die ausschließlich Azure-VMs verwenden
In diesem Abschnitt werden die bisher verwendeten Bereitstellungsarchitekturen und neue Empfehlungen erläutert.

### <a name="proximity-placement-groups-with-zonal-deployments"></a>Näherungsplatzierungsgruppen mit zonenbezogenen Bereitstellungen
Für Bereitstellungen, die keine HANA Large-Instanzen verwenden, ist es wichtig, eine optimale Leistung zwischen der SAP-Logikschicht und der DBMS-Ebene zu gewährleisten. Um eine solche relativ niedrige Netzwerklatenz für eine begrenzte Anzahl von Szenarien zu ermöglichen, kann eine Azure-Näherungsplatzierungsgruppe für ein solches SAP-System definiert werden.

Eine Bündelung mehrerer SAP-Produktions- oder -Nicht-Produktionssysteme in einer einzigen Näherungsplatzierungsgruppe sollte vermieden werden. Vermeiden Sie SAP-Systembündelungen: Je mehr Systeme Sie in einer Näherungsplatzierungsgruppe gruppieren, desto höher ist die Wahrscheinlichkeit für Folgendes:

- Sie benötigen einen VM-Typ, der unter dem Netzwerk-Rückgrat, dem die Näherungsplatzierungsgruppe zugewiesen wurde, nicht verfügbar ist.
- Diese Ressourcen nichtgängiger VMs, z. B. der M-Serie, könnten schließlich nicht mehr erfüllt werden, wenn Sie die Anzahl der VMs im Laufe der Zeit in eine Näherungsplatzierungsgruppe erweitern müssen.

Die Verwendung der Näherungsplatzierungsgruppe, die wir bisher empfohlen haben, sieht wie in dieser Grafik aus.

![Alte Näherungsplatzierungsgruppen mit Zonen](./media/sap-proximity-placement-scenarios/vm-ppg-zone-old.png)

Sie haben in jeder der beiden Verfügbarkeitszonen, in denen Sie Ihr SAP-System bereitgestellt haben, eine Näherungsplatzierungsgruppe (Proximity Placement Group, PPG) erstellt. Alle VMs einer bestimmten Zone sind Teil der einzelnen Näherungsplatzierungsgruppe dieser bestimmten Zone. Sie begannen in jeder Zone mit der Bereitstellung der DBMS-VM, um die PPG zu erfassen, und stellten dann die ASCS-VM in derselben Zone und PPG bereit. In einem dritten Schritt haben Sie eine Azure-Verfügbarkeitsgruppen erstellt, die Verfügbarkeitsgruppen der bereichsgebundenen PPG zugewiesen und die SAP-Anwendungsebene darin bereitgestellt. Der Vorteil dieser Konfiguration bestand darin, dass alle Komponenten unter demselben Netzwerk-Rückgrat gut ausgerichtet waren. Der große Nachteil besteht darin, dass Ihre Flexibilität bei der VM-Größenänderung eingeschränkt werden kann.


Basierend auf vielen Verbesserungen, die von Microsoft in den Azure-Regionen bereitgestellt wurden, um die Netzwerklatenz innerhalb einer Azure-Verfügbarkeitszone zu reduzieren, sieht der neue Bereitstellungsleitfaden für zonenbezogene Bereitstellungen wie folgt aus:

![Neue Näherungsplatzierungsgruppen mit Zonen](./media/sap-proximity-placement-scenarios/vm-ppg-zone.png)

Der Unterschied zur bisherigen Empfehlung besteht darin, dass die Datenbank-VMs in den beiden Zonen nicht mehr Teil der Näherungsplatzierungsgruppen sind. Die Näherungsplatzierungsgruppen pro Zone sind jetzt auf die Bereitstellung der VM, auf der die SAP-ASCS/SCS-Instanzen ausgeführt werden, zugewiesen. Dies bedeutet auch, dass für die Regionen, in denen Verfügbarkeitszonen von mehreren Rechenzentren gesammelt werden, die ASCS/SCS-Instanz und die Logikschicht unter einem Netzwerk-Rückgrat ausgeführt werden können und die Datenbank-VMs unter einem anderen Netzwerk-Rückgrat ausgeführt werden können. Obwohl die Netzwerkverbesserungen vorgenommen wurden, sollte die Netzwerklatenz zwischen der SAP-Logikschicht und der DBMS-Ebene weiterhin ausreichen, um eine ausreichend gute Leistung und einen ausreichend guten Durchsatz zu erzielen. Der Vorteil dieser neuen Konfiguration besteht darin, dass Sie mehr Flexibilität bei der Größenänderung von VMs oder beim Wechsel zu neuen VM-Typen mit der DBMS-Ebene oder/und der Anwendungsebene des SAP-Systems haben. 


### <a name="proximity-placement-groups-with-availability-set-deployments"></a>Näherungsplatzierungsgruppen mit Bereitstellungen von Verfügbarkeitsgruppen
In diesem Fall besteht der Zweck darin, Näherungsplatzierungsgruppen zu verwenden, um die VMs zu anzuordnen, die über verschiedene Verfügbarkeitsgruppen bereitgestellt werden. In diesem Verwendungsszenario verwenden Sie keine kontrollierte Bereitstellung für verschiedene Verfügbarkeitszonen in einer Region. Stattdessen möchten Sie das SAP-System mithilfe von Verfügbarkeitsgruppen bereitstellen. Daher verfügen Sie mindestens über eine Verfügbarkeitsgruppe für die DBMS-VMs, ASCS/SCS-VMs und die Logikschicht-VMs. Da Sie zum Zeitpunkt der Bereitstellung einer VM keine Verfügbarkeitsgruppen UND eine Verfügbarkeitszone angeben können, können Sie nicht steuern, wo die VMs in den verschiedenen Verfügbarkeitsgruppen zugeordnet werden. Dies kann dazu führen, dass die Netzwerklatenz zwischen verschiedenen VMs in einigen Azure-Regionen immer noch zu hoch ist, um eine ausreichend gute Leistung zu erzielen. Die resultierende Architektur würde also wie folgt aussehen:


![Näherungsplatzierungsgruppen mit Verfügbarkeitsgruppen](./media/sap-proximity-placement-scenarios/vm-ppg-avsets.png)

In dieser Grafik wird einem einzelnen SAP-System eine einzelne Näherungsplatzierungsgruppe zugewiesen. Diese PPG wird den drei Verfügbarkeitsgruppen zugewiesen. Der Bereich der Näherungsplatzierungsgruppe wird dann durch Bereitstellen der ersten VMs der Datenbankschicht in der DBMS-Verfügbarkeitsgruppe festgelegt. Mit dieser Architekturempfehlung werden alle virtuellen Computer unter demselben Netzwerk-Rückgrat angeordnet. Sie führt die weiter oben in diesem Artikel erwähnten Einschränkungen ein. Daher sollte die Näherungsplatzierungsgruppenarchitektur nur selten verwendet werden.


## <a name="proximity-placement-groups-and-hana-large-instances"></a>Näherungsplatzierungsgruppen und HANA Large Instances
Wenn einige Ihrer SAP-Systeme auf [HANA Large Instances](./hana-overview-architecture.md) als Anwendungsschicht angewiesen sind, können erhebliche Verbesserungen in der Netzwerklatenz zwischen HANA Large Instances und Azure-VMs erzielt werden, wenn HANA Large Instances-Einheiten verwendet werden, die in [Reihen oder Stempeln der Version 4](./hana-network-architecture.md#networking-architecture-for-hana-large-instance) bereitgestellt werden. Eine der Verbesserungen besteht darin, dass HANA Large Instances-Einheiten direkt bei der Bereitstellung eine Näherungsplatzierungsgruppe erhalten. Sie können diese Näherungsplatzierungsgruppe verwenden, um VMs der Anwendungsschicht bereitzustellen. Folglich werden diese VMs im selben Rechenzentrum bereitgestellt, in dem sich auch die HANA Large Instances-Einheit befindet.

Um zu erkennen, ob Ihre HANA Large Instances-Einheit in einem Stempel oder einer Reihe der Revision 4 bereitgestellt wird, lesen Sie den Artikel [Steuerung von HANA Large Instances in Azure über das Azure-Portal](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). In der Attributübersicht Ihrer HANA Large Instances-Einheit können Sie auch den Namen der Näherungsplatzierungsgruppe ermitteln, da sie bei der Bereitstellung Ihrer HANA Large Instances-Einheit erstellt wurde. Der in der Attributübersicht angezeigte Name ist der Name der Näherungsplatzierungsgruppe, in der Sie VMs der Anwendungsschicht bereitstellen sollten.

Im Vergleich zu SAP-Systemen, die nur virtuelle Azure-Computer verwenden, haben Sie bei Verwendung von HANA Large Instances weniger Flexibilität bei der Entscheidung, wie viele [Azure-Ressourcengruppen](../../../azure-resource-manager/management/manage-resources-portal.md) Sie verwenden möchten. Alle HANA-Einheiten eines [HANA Large Instances-Mandanten](./hana-know-terms.md) werden wie in [diesem Artikel](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal) beschrieben in einer einzigen Ressourcengruppe zusammengefasst. Wenn Sie die Bereitstellung nicht auf verschiedene Mandanten aufteilen, um z.B. Produktions- und Nicht-Produktionssysteme oder andere Systeme zu trennen, werden alle Ihre HANA Large Instances-Einheiten in einem HANA Large Instances-Mandanten bereitgestellt. Dieser Mandant weist eine 1:1-Beziehung mit einer Ressourcengruppe auf. Für jede einzelne Einheit wird jedoch eine separate Näherungsplatzierungsgruppe definiert.

Infolgedessen sehen die Beziehungen zwischen Azure-Ressourcengruppen und Näherungsplatzierungsgruppen für einen einzelnen Mandanten wie hier gezeigt aus:

![Näherungsplatzierungsgruppen und HANA Large Instances](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Beispiel für die Bereitstellung mit Näherungsplatzierungsgruppen
Im Folgenden finden Sie einige PowerShell-Befehle, mit denen Sie Ihre VMs mit Azure-Näherungsplatzierungsgruppen bereitstellen können.

Nachdem Sie sich bei [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) angemeldet haben, besteht der erste Schritt darin, zu überprüfen, ob Sie sich in dem Azure-Abonnement befinden, das Sie für die Bereitstellung verwenden möchten:

<pre><code>
Get-AzureRmContext
</code></pre>

Wenn Sie zu einem anderen Abonnement wechseln müssen, können Sie dazu den folgenden Befehl ausführen:

<pre><code>
Set-AzureRmContext -Subscription "PPG test subscription"
</code></pre>

Erstellen Sie eine neue Azure-Ressourcengruppe, indem Sie diesen Befehl ausführen:

<pre><code>
New-AzResourceGroup -Name "ppgexercise" -Location "westus2"
</code></pre>

Erstellen Sie die neue Näherungsplatzierungsgruppe, indem Sie den folgenden Befehl ausführen:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate" -Location "westus2"
</code></pre>

Stellen Sie Ihre erste VM mit einem Befehl wie dem folgenden in der Näherungsplatzierungsgruppe bereit:

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "ppgscopevm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

Mit dem vorhergehenden Befehl wird eine Windows-basierte VM bereitgestellt. Nachdem die VM-Bereitstellung erfolgreich war, wird der Bereich des Netzwerk-Rückgrats der Näherungsplatzierungsgruppe innerhalb der Azure-Region definiert. Alle nachfolgenden VM-Bereitstellungen, die auf die Näherungsplatzierungsgruppe verweisen, wie im vorherigen Befehl gezeigt, werden unter demselben Netzwerk-Rückgrat bereitgestellt, solange der VM-Typ auf Hardware gehostet werden kann, die unter diesem Netzwerk-Rückgrat platziert ist und Kapazität für diesen VM-Typ verfügbar ist.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Kombinieren von Verfügbarkeitsgruppen und Verfügbarkeitszonen mit Näherungsplatzierungsgruppen
Eines der Probleme bei der Verwendung von Verfügbarkeitszonen für SAP-Systembereitstellungen besteht darin, dass Sie die SAP-Logikschicht nicht mit Verfügbarkeitsgruppen innerhalb der jeweiligen Verfügbarkeitszone bereitstellen können. Sie möchten, dass die SAP-Logikschicht in denselben Zonen wie die SAP-ASCS/SCS-VMs bereitgestellt wird. Das Verweisen auf eine Verfügbarkeitszone und eine Verfügbarkeitsgruppe beim Bereitstellen einer einzelnen VM wird derzeit nicht unterstützt. Wenn Sie jedoch nur eine VM bereitstellen, die eine Verfügbarkeitszone anweisen soll, können Sie nicht mehr sicherstellen, dass die VMs der Anwendungsebene auf verschiedene Update- und Fehlerdomänen verteilt sind.

Durch die Verwendung von Näherungsplatzierungsgruppen können Sie diese Einschränkung umgehen. Dies ist die Bereitstellungssequenz:

- Erstellen Sie eine Näherungsplatzierungsgruppe.
- Stellen Sie Ihre Anker-VM (empfohlen als ASCS/SCS-VM) durch Verweisen auf eine Verfügbarkeitszone zur bereit.
- Erstellen Sie eine Verfügbarkeitsgruppe, die auf die Näherungsplatzierungsgruppe verweist. (Siehe den Befehl weiter unten in diesem Artikel.)
- Stellen Sie die VMs der Anwendungsschicht bereit, indem Sie auf die Verfügbarkeitsgruppe und die Näherungsplatzierungsgruppe verweisen.

Anstatt die erste VM wie im vorherigen Abschnitt gezeigt bereitzustellen, verweisen Sie beim Bereitstellen der VM auf eine Verfügbarkeitszone und die Näherungsplatzierungsgruppe:

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "centralserviceszone1" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "collocate" -Size "Standard_E8s_v4"
</code></pre>

Eine erfolgreiche Bereitstellung dieses virtuellen Computers würde die ASCS/SCS-Instanz des SAP-Systems in einer Verfügbarkeitszone hosten. Der Bereich der Näherungsplatzierungsgruppe ist auf eines der Netzwerk-Rückgrate festgelegt, die sich in der von Ihnen definierten Verfügbarkeitszone befinden.

Im nächsten Schritt müssen Sie die Verfügbarkeitsgruppen erstellen, die Sie für die Anwendungsschicht des SAP-Systems verwenden möchten.

Definieren und erstellen Sie die Näherungsplatzierungsgruppe. Der Befehl zum Erstellen der Verfügbarkeitsgruppe erfordert einen zusätzlichen Verweis auf die ID der Näherungsplatzierungsgruppe (nicht den Namen). Sie können die ID der Näherungsplatzierungsgruppe abrufen, indem Sie den folgenden Befehl verwenden:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate"
</code></pre>

Beim Erstellen der Verfügbarkeitsgruppe müssen Sie zusätzliche Parameter berücksichtigen, wenn Sie verwaltete Datenträger (Standard, sofern nicht anders angegeben) und Näherungsplatzierungsgruppen verwenden:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "ppgexercise" -Name "ppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Im Idealfall sollten Sie drei Fehlerdomänen verwenden. Die Anzahl der unterstützten Fehlerdomänen kann jedoch je nach Region unterschiedlich sein. In diesem Fall beträgt die maximal mögliche Anzahl von Fehlerdomänen für die einzelnen Regionen zwei Domänen. Für die Bereitstellung von Anwendungsschicht-VMs müssen Sie einen Verweis auf den Namen der Verfügbarkeitsgruppe und den Namen der Näherungsplatzierungsgruppe hinzufügen, wie hier gezeigt:

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "appinstance1" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

Dies ist das Ergebnis dieser Bereitstellung:
- Zentrale Dienste für Ihr SAP-System, die sich in einer bestimmten Verfügbarkeitszone oder mehreren Verfügbarkeitszonen befinden.
- Eine SAP-Anwendungsebene, die sich über Verfügbarkeitsgruppen im selben Netzwerk befindet wie die SAP-(ASCS/SCS-)VM oder -VMs der zentralen Dienste.

> [!NOTE]
> Da Sie eine DBMS-VM und ASCS/SCS-VMs in einer Zone und die zweite DBMS-VM und ASCS/SCS-VMs in eine anderer Zone bereitstellen, um eine Hochverfügbarkeitskonfiguration zu erhalten, benötigen Sie für jede der Zonen eine andere Näherungsplatzierungsgruppe. Das gleiche gilt für jede Verfügbarkeitsgruppe, die Sie verwenden.

## <a name="change-proximity-placement-group-configurations-of-an-existing-system"></a>Ändern der Konfigurationen der Näherungsplatzierungsgruppe eines vorhandenen Systems
Wenn Sie Näherungsplatzierungsgruppen als die bisher angegebenen Empfehlungen implementiert haben und sie an die neue Konfiguration anpassen möchten, können Sie dies mit den in den folgenden Artikeln beschriebenen Methoden tun:

- [Bereitstellen von VMs für Näherungsplatzierungsgruppen mit Azure CLI](../../linux/proximity-placement-groups.md)
- [Bereitstellen von VMs für Näherungsplatzierungsgruppen mit PowerShell](../../windows/proximity-placement-groups.md) 

Sie können diese Befehle auch für Fälle verwenden, in denen Zuteilungsfehler auftreten, wenn Sie nicht zu einem neuen VM-Typ mit eine vorhandenen VM in der Näherungsplatzierungsgruppe wechseln können.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der Dokumentation:

- [Prüfliste für die Planung und Bereitstellung von SAP-Workloads in Azure](./sap-deployment-checklist.md)
- [Bereitstellen von VMs für Näherungsplatzierungsgruppen mit Azure CLI](../../linux/proximity-placement-groups.md)
- [Bereitstellen von VMs für Näherungsplatzierungsgruppen mit PowerShell](../../windows/proximity-placement-groups.md)
- [Überlegungen zu Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workloads](./dbms_guide_general.md)