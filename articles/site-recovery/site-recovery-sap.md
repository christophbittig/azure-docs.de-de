---
title: Einrichten der SAP NetWeaver-Notfallwiederherstellung mit Azure Site Recovery
description: Erfahren Sie, wie Sie die SAP NetWeaver-Notfallwiederherstellung mit Azure Site Recovery einrichten.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 4ada2c92b541a4a0d89aad1791d3bb9c97377f83
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441645"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP NetWeaver-App mit mehreren Ebenen

Die meisten großen und mittleren SAP-Bereitstellungen weisen eine Form von Notfallwiederherstellungslösung auf. Die Bedeutung robuster und testfähiger Notfallwiederherstellungslösungen ist weiter gewachsen, da immer mehr Kerngeschäftsprozesse in Anwendungen wie SAP verschoben werden. Azure Site Recovery wurde mit SAP-Anwendungen getestet und integriert. Azure Site Recovery übersteigt die Möglichkeiten der meisten lokalen Notfallwiederherstellungslösungen, und dies zu geringeren Gesamtkosten als Lösungen von Mitbewerbern.

Mit Azure Site Recovery haben Sie folgende Möglichkeiten:
* Ermöglichen Sie den Schutz von lokal ausgeführten SAP NetWeaver-Produktionsanwendungen und Anwendungen, die nicht von NetWeaver stammen, indem Sie die Komponenten in Azure replizieren.
* Ermöglichen Sie den Schutz von in Azure ausgeführten SAP NetWeaver-Produktionsanwendungen und Anwendungen, die nicht von NetWeaver stammen, indem Sie die Komponenten in einem anderen Azure-Datencenter replizieren.
* Vereinfachen der Cloudmigration mithilfe von Site Recovery zum Migrieren Ihrer SAP-Bereitstellung in Azure.
* Vereinfachen Sie SAP-Projektupgrades, Testläufe und Prototyperstellung, indem Sie bei Bedarf einen Produktionsklon zum Testen von SAP-Anwendungen erstellen.

Sie können SAP NetWeaver-Anwendungsbereitstellungen mit [Azure Site Recovery](site-recovery-overview.md) schützen. Dieser Artikel umfasst bewährte Methoden für den Schutz einer SAP NetWeaver-Bereitstellung mit drei Ebenen in Azure, wenn Sie eine Replikation mit Azure Site Recovery in ein anderes Azure-Rechenzentrum durchführen. Im Artikel werden die unterstützten Szenarien und Konfigurationen sowie das Ausführen von Testfailovern (Übungen zur Notfallwiederherstellung) und tatsächlichen Failovern beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass Sie wissen, wie Sie die folgenden Aufgaben ausführen:

* [Replizieren eines virtuellen Computers in Azure](./azure-to-azure-tutorial-enable-replication.md)
* [Entwerfen eines Netzwerks für die Wiederherstellung](./azure-to-azure-about-networking.md)
* [Ausführen eines Testfailovers in Azure](./azure-to-azure-tutorial-dr-drill.md)
* [Ausführen eines Failovers in Azure](site-recovery-failover.md)
* [Replizieren eines Domänencontrollers](site-recovery-active-directory.md)
* [Replizieren einer SQL Server-Instanz](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Unterstützte Szenarios

Mit Azure Site Recovery können Sie eine Notfallwiederherstellungslösung in folgenden Szenarien implementieren:
* Sie verfügen über SAP-Systeme, die in einem Azure-Rechenzentrum ausgeführt werden, und replizieren diese in ein anderes Azure-Rechenzentrum (Azure-zu-Azure-Notfallwiederherstellung). 
   Weitere Informationen finden Sie unter [Architektur der Azure-zu-Azure-Replikation](./azure-to-azure-architecture.md).
* Sie verfügen über SAP-Systeme, die auf VMware-Servern (oder physischen Servern) lokal ausgeführt werden. Außerdem replizieren Sie die SAP-Systeme an einen Notfallwiederherstellungsstandort in einem Azure-Rechenzentrum (VMware-zu-Azure-Notfallwiederherstellung). 
   Dieses Szenario erfordert einige zusätzliche Komponenten. Weitere Informationen finden Sie unter [VMware in der Architektur für die Azure-Replikation](./vmware-azure-architecture.md).
* Sie verfügen über SAP-Systeme, die auf Hyper-V lokal ausgeführt werden. Außerdem replizieren Sie die SAP-Systeme an einen Notfallwiederherstellungsstandort in einem Azure-Rechenzentrum (Hyper-V-zu-Azure-Notfallwiederherstellung).
   Dieses Szenario erfordert einige zusätzliche Komponenten. Weitere Informationen finden Sie unter [Architektur der Replikation von Hyper-V in Azure](./hyper-v-azure-architecture.md).

In diesem Artikel verwenden wir ein **Azure-zu-Azure**-Notfallwiederherstellungsszenario. Das Szenario zeigt Ihnen die SAP-Notfallwiederherstellungsfunktionen von Site Recovery. Da die Replikation mit Site Recovery nicht anwendungsspezifisch ist, gilt der beschriebene Prozess vermutlich auch für andere Szenarien.

### <a name="required-foundation-services"></a>Erforderliche Grunddienste
In dem in diesem Artikel besprochenen Szenario werden die folgenden Foundation-Dienste bereitgestellt:
* Azure ExpressRoute oder Azure VPN Gateway
* Mindestens ein Azure Active Directory-Domänencontroller und ein DNS-Server unter Azure

Es wird empfohlen, diese Infrastruktur einzurichten, bevor Sie Site Recovery bereitstellen.

## <a name="reference-sap-application-deployment"></a>SAP-Anwendungsbereitstellung als Referenz

Diese Referenzarchitektur wird führt SAP NetWeaver in einer Windows-Umgebung in Azure mit Hochverfügbarkeit aus. Diese Architektur wird mit virtuellen Computern bestimmter Größen bereitgestellt, die Sie an die Anforderungen Ihres Unternehmens anpassen können.

![Diagramm eines typischen SAP-Bereitstellungsmusters](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Überlegungen zur Notfallwiederherstellung

Zur Notfallwiederherstellung muss es möglich sein, ein Failover zu einer sekundären Region ausführen zu können. Für jede Ebene wird eine andere Strategie genutzt, um per Notfallwiederherstellung für den erforderlichen Schutz zu sorgen.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Virtuelle Computer mit SAP Web Dispatcher-Pools

Die Web Dispatcher-Komponente wird als Lastenausgleichsmodul für SAP-Datenverkehr zwischen den SAP-Anwendungsservern verwendet. Um Hochverfügbarkeit für die Web Dispatcher-Komponente zu erreichen, implementiert Azure Load Balancer das parallele Web Dispatcher-Setup. Für Web Dispatcher wird eine Roundrobin-Konfiguration für HTTP(S)-Datenverkehrsverteilung zwischen den verfügbaren Web Dispatcher-Instanzen im Pool der Lastenausgleichsmodule verwendet.

#### <a name="vms-running-application-servers-pools"></a>Virtuelle Computer mit Anwendungsserverpools
Die SMLG-Transaktion verwaltet Anmeldegruppen für ABAP-Anwendungsserver. Dabei verwendet die Lastenausgleichsfunktion im Nachrichtenserver der Central Services, um Arbeitslast auf SAP-Anwendungsserverpools für SAPGUIs und RFC-Datenverkehr zu verteilen. Sie können diese Verwaltung mithilfe von Site Recovery replizieren.

#### <a name="vms-running-sap-central-services-clusters"></a>Virtuelle Computer mit SAP Central Services-Clustern
In dieser Referenzarchitektur wird Central Services auf virtuellen Computern auf der Logikschicht ausgeführt. Central Services, die sich auf einem einzelnen virtuellen Computer befinden, stellen einen potenziellen Single Point of Failure dar. Typische Bereitstellung und hohe Verfügbarkeit sind keine Anforderungen.

Für eine Hochverfügbarkeitslösung können Sie entweder einen Cluster mit freigegebenen Datenträgern oder einen Dateifreigabecluster verwenden. Um virtuelle Computer für einen Cluster mit freigegebenen Datenträgern zu konfigurieren, verwenden Sie Windows Server-Failovercluster. Wir empfehlen Ihnen, den Cloudzeugen als Quorumzeugen zu verwenden.

 > [!NOTE]
 > Da Site Recovery den Cloudzeugen nicht repliziert, wird empfohlen, den Cloudzeugen in der Notfallwiederherstellungsregion bereitzustellen.

Zur Unterstützung der Failoverclusterumgebung führt [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) die Funktion „Freigegebenes Clustervolume“ aus. In der Funktion repliziert SIOS DataKeeper Cluster unabhängige Datenträger, die sich im Besitz der Clusterknoten befinden. Da Azure nativ keine freigegebenen Datenträger unterstützt, sind Lösungen erforderlich, die von SIOS bereitgestellt werden.

Sie können das Clustering auch durch die Implementieren eines Dateifreigabeclusters durchführen. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) hat kürzlich das Bereitstellungsmuster von Central Services so geändert, dass über einen UNC-Pfad auf die globalen „/sapmnt“-Verzeichnisse zugegriffen wird. Es wird weiterhin empfohlen sicherzustellen, dass die UNC-Freigabe für „/sapmnt“ hochverfügbar ist. Sie können Ihre Central Services-Instanz überprüfen. Verwenden Sie dazu Windows Server-Failovercluster mit SOFS (Scale Out File Server, Dateiserver mit horizontaler Skalierung) und der Storage Spaces Direct-Funktion (S2D, „Direkte Speicherplätze“) in Windows Server 2016.

 > [!NOTE]
 > Site Recovery unterstützt zurzeit nur die Replikation absturzkonsistenter Punkte virtueller Computer mithilfe direkter Speicherplätze und passiver Knoten von SIOS Datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Weitere Überlegungen zur Notfallwiederherstellung

Mit Site Recovery können Sie das Failover einer vollständigen SAP-Bereitstellung in allen Azure-Regionen orchestrieren.
Nachfolgend sind die Schritte zum Einrichten der Notfallwiederherstellung angegeben:

1. Replizieren von virtuellen Computern
1. Entwerfen eines Netzwerks für die Wiederherstellung
1. Replizieren eines Domänencontrollers
1. Replizieren der Datenbankebene
1. Ausführen eines Testfailovers
1. Ausführen eines Failovers

Nachfolgend ist die Empfehlung für eine Notfallwiederherstellung der einzelnen Ebenen angegeben, die in diesem Beispiel verwendet werden.

 **SAP-Ebenen** | **Empfehlung**
 --- | ---
**SAP Web Dispatcher-Pool** |  Replizieren über Site Recovery 
**SAP-Anwendungsserverpool** |  Replizieren über Site Recovery 
**SAP Central Services-Cluster** |  Replizieren über Site Recovery 
**Virtuelle Active Directory-Computer** |  Verwenden von Active Directory-Replikation 
**SQL-Datenbank-Server** |  Verwenden von SQL Server Always On-Replikation

## <a name="replicate-virtual-machines"></a>Replizieren von virtuellen Computern

Zu Beginn des Replizieren aller virtuellen Computer für SAP-Anwendungen in ein Azure-Notfallwiederherstellungsrechenzentrum folgen Sie der Anleitung unter [Replikation eines virtuellen Computers in Azure](./azure-to-azure-tutorial-enable-replication.md).

* Eine Anleitung zum Schutz von Active Directory und DNS finden Sie unter [Schützen von Active Directory und DNS](site-recovery-active-directory.md).

* Eine Anleitung zum Schutz von Datenbankebenen auf SQL Server finden Sie unter [Schützen von SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Netzwerkkonfiguration

Wenn Sie eine statische IP-Adresse verwenden, können Sie die IP-Adresse angeben, die Sie dem virtuellen Computer zuordnen möchten. Um die IP-Adresse festzulegen, wechseln Sie zu **Netzwerkeinstellungen** > **Netzwerkschnittstellenkarte**.

![Screenshot, der zeigt, wie eine private IP-Adresse im Bereich „Site Recovery-Netzwerkschnittstellenkarte“ festgelegt wird](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans

Ein Wiederherstellungsplan unterstützt die Sequenzierung der verschiedenen Ebenen in einer Anwendung mit mehreren Ebenen während eines Failovers. Die Sequenzierung trägt zur Wahrung der Anwendungskonsistenz bei. Beim Erstellen eines Wiederherstellungsplans für eine Webanwendung mit mehreren Ebenen führen Sie die unter [Erstellen eines Wiederherstellungsplans mit Site Recovery](site-recovery-create-recovery-plans.md) beschriebenen Schritte aus.

### <a name="add-virtual-machines-to-failover-groups"></a>Hinzufügen von virtuellen Computern zu Failovergruppen

1. Erstellen Sie einen Wiederherstellungsplan, indem Sie den Anwendungsserver, Web Dispatcher und virtuelle Computer mit SAP Central Services hinzufügen.
1. Wählen Sie **Anpassen** aus, um die virtuellen Computer zu gruppieren. Standardmäßig gehören alle virtuellen Computer der Gruppe 1 an.

### <a name="add-scripts-to-the-recovery-plan"></a>Hinzufügen von Skripts zum Wiederherstellungsplan
Damit Ihre Anwendungen ordnungsgemäß funktionieren, müssen auf den virtuellen Computern ggf. einige Vorgänge ausgeführt werden. Führen Sie diese Vorgänge nach dem Failover bzw. während eines Testfailovers aus. Sie können auch einige Vorgänge nach einem Failover automatisieren. Durch Hinzufügen entsprechender Skripts zum Wiederherstellungsplan können Sie beispielsweise den DNS-Eintrag aktualisieren sowie Bindungen und Verbindungen ändern.

Sie können die am häufigsten verwendeten Site Recovery-Skripts in Ihrem Azure Automation-Konto bereitstellen, indem Sie **In Azure bereitstellen** auswählen. Wenn Sie ein beliebiges veröffentlichtes Skript verwenden, befolgen Sie die Anleitung im Skript.

[![In Azure bereitstellen](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Fügen Sie der Gruppe 1 ein Skript als vorausgehende Aktion hinzu, um für die SQL Server-Verfügbarkeitsgruppe ein Failover durchzuführen. Verwenden Sie das Skript „ASR-SQL-FailoverAG“ aus den Beispielskripts. Befolgen Sie die Anleitung im Skript, und nehmen Sie die erforderlichen Änderungen im Skript vor.
1. Fügen Sie ein Skript als nachfolgende Aktion hinzu, um den Failover-VMs der Webebene (Gruppe 1) einen Lastenausgleich anzufügen. Verwenden Sie das Skript „ASR-AddSingleLoadBalancer“ aus den Beispielskripts. Befolgen Sie die Anleitung im Skript, und nehmen Sie die erforderlichen Änderungen im Skript vor.

![SAP-Wiederherstellungsplan](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

1. Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
1. Wählen Sie den Wiederherstellungsplan aus, den Sie für die SAP-Anwendungen erstellt haben.
1. Wählen Sie **Testfailover** aus.
1. Wählen Sie den Wiederherstellungspunkt und das virtuelle Azure-Netzwerk aus, um den Prozess für das Testfailover zu starten.
1. Wenn die sekundäre Umgebung aktiv ist, können Sie Überprüfungen durchführen.
1. Wählen Sie nach Abschluss der Überprüfungen die Option **Testfailover bereinigen** aus, um die Failoverumgebung zu bereinigen.

Weitere Informationen finden Sie unter [Testfailover nach Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Ausführen eines Failovers

1. Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
1. Wählen Sie den Wiederherstellungsplan aus, den Sie für die SAP-Anwendungen erstellt haben.
1. Wählen Sie **Failover** aus.
1. Wählen Sie den Wiederherstellungspunkt aus, um den Failoverprozess zu starten.

Weitere Informationen finden Sie unter [Failover in Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Erstellen einer Notfallwiederherstellungslösung für SAP NetWeaver-Bereitstellungen mithilfe von Site Recovery finden Sie im herunterladbaren Whitepaper [SAP NetWeaver: Erstellen einer Notfallwiederherstellungslösung mit Site Recovery](/samples/browse/?redirectedfrom=TechNet-Gallery). Im Whitepaper werden Empfehlungen für verschiedene SAP-Architekturen besprochen. Sie lernen unterstützte Anwendungen und VM-Typen für SAP in Azure kennen. Es gibt auch Planoptionen für das Testen Ihrer Notfallwiederherstellungslösung.
* Erfahren Sie mehr über das [Replizieren anderer Workloads](site-recovery-workload.md) mit Site Recovery.