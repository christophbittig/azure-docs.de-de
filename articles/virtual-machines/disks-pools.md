---
title: Übersicht über Azure-Datenträgerpools (Vorschau)
description: Erfahren Sie mehr über Azure-Datenträgerpools (Vorschau).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: d6e2eda8fd7bc2ba3b41b911b5964c2a65e33c14
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074541"
---
# <a name="azure-disk-pools-preview"></a>Azure-Datenträgerpools (Vorschau)

Ein Azure-Datenträgerpool (Vorschau) ist eine Azure-Ressource, mit der Ihre Anwendungen und Workloads von einem einzelnen Endpunkt aus auf eine Gruppe verwalteter Datenträger zugreifen können. Ein Datenträgerpool kann ein iSCSI-Ziel (Internet Small Computer Systems Interface) verfügbar machen, um den Datenzugriff auf Datenträger in diesem Pool über iSCSI zu ermöglichen. Jeder Datenträgerpool kann über ein iSCSI-Ziel verfügen, und jeder Datenträger kann als iSCSI-LUN verfügbar gemacht werden. Sie können Datenträger im Datenträgerpool mit Azure VMware Solution-Hosts als Datenspeicher verbinden. Dadurch können Sie Ihren Speicher unabhängig von Ihren Azure VMware Solution-Hosts skalieren. Sobald ein Datenspeicher konfiguriert ist, können Sie darin Volumes erstellen und an Ihre VMware-Instanzen anfügen.

## <a name="how-it-works"></a>Funktionsweise

Wenn ein Datenträgerpool bereitgestellt wird, wird automatisch eine verwaltete Ressourcengruppe für Sie erstellt. Diese verwaltete Ressourcengruppe enthält alle Azure-Ressourcen, die für den Betrieb eines Datenträgerpools erforderlich sind. Die Benennungskonvention für diese Ressourcengruppen lautet: MSP_(Ressourcengruppenname)_(Diskpoolname)\_(Regionsname).

Wenn Sie dem Datenträgerpool einen verwalteten Datenträger hinzufügen, wird der Datenträger an verwaltete iSCSI-Controller angefügt. Einem Datenträgerpool können mehrere verwaltete Datenträger als Speicherziele hinzugefügt werden. Jedes Speicherziel wird als iSCSI-LUN unter dem iSCSI-Ziel des Datenträgerpools angezeigt. Datenträgerpools bieten native Unterstützung für Azure VMware Solution. Ein Azure VMware Solution-Cluster kann eine Verbindung mit einem Datenträgerpool herstellen, der alle Azure VMware Solution-Hosts in dieser Umgebung umfasst. Das folgende Diagramm zeigt, wie Sie Datenträgerpools mit Azure VMware Solution verwenden können.

:::image type="content" source="media/disks-pools/disk-pool-diagram.png" alt-text="Diagramm, das die Funktionsweise von Datenträgerpools veranschaulicht. Auf jeden Disk Ultra-Datenträger kann jeder iSCSI-Controller über iSCSI zugegriffen werden. Die Azure VMware Solution Hosts-können über iSCSI auf den iSCSI-Controller zugreifen.":::

## <a name="restrictions"></a>Beschränkungen

Für Datenträgerpools gelten in der Vorschauphase folgende Einschränkungen:

- Einem Datenträgerpool können nur Premium- oder Standard-SSD-Datenträger oder Disk Ultra-Datenträger hinzugefügt werden.
    - Ein Datenträgerpool kann nicht so konfiguriert werden, dass er sowohl Premium-/Standard-SSDs als auch Ultra-Datenträger enthält. Ein für Ultra-Datenträger konfigurierter Datenträgerpool kann nur Ultra-Datenträger enthalten. Ebenso kann ein Datenträgerpool, der für die Verwendung von Premium- und Standard-SSDs konfiguriert ist, nur Premium- und Standard-SSDs enthalten.
- Datenträger, die [zonenredundanten Speicher (ZRS)](disks-redundancy.md#zone-redundant-storage-for-managed-disks) verwenden, werden derzeit nicht unterstützt. 

### <a name="regional-availability"></a>Regionale Verfügbarkeit

Datenträgerpools sind derzeit in den folgenden Regionen verfügbar:

- Australien (Osten)
- Kanada, Mitte
- USA (Mitte)
- East US
- USA, Westen 2
- Japan, Osten
- Nordeuropa
- Europa, Westen
- Asien, Südosten
- UK, Süden


## <a name="billing"></a>Abrechnung

Wenn Sie einen Datenträgerpool bereitstellen, fallen in zwei Hauptbereichen abzurechnende Kosten an:

- Für die dem Datenträgerpool hinzugefügten Datenträger
- Für die Azure-Ressourcen, die in der verwalteten Ressourcengruppe bereitgestellt werden, die zum Datenträgerpool gehört, bei denen es sich um diese Ressourcen handelt: Diese Ressourcen sind:
    - Virtuelle Computer
    - Verwaltete Datenträger
    - Eine Netzwerkschnittstelle
    - Ein Speicherkonto für Diagnoseprotokolle und Metriken
        
Abgerechnet werden die Ressourcen innerhalb dieser verwalteten Ressourcengruppe und die einzelnen Datenträger, die den eigentlichen Datenspeicher darstellen. Wenn Sie beispielsweise einen Datenträgerpool mit einem P30-Datenträger hinzugefügt haben, werden Ihnen der P30-Datenträger und alle in der verwalteten Ressourcengruppe bereitgestellten Ressourcen in Rechnung gestellt. Abgesehen von diesen Ressourcen und Ihren Datenträgern fallen für einen Datenträgerpool keine zusätzlichen Dienstgebühren an. Ausführliche Informationen zur verwalteten Ressourcengruppe finden Sie im Abschnitt [Funktionsweise](#how-it-works).

Im [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) finden Sie regionale Preise für VMs und Datenträger, um die Kosten eines Datenträgerpools für Sie zu kalkulieren. Azure-Ressourcen, die vom Datenträgerpool genutzt werden, können in Azure-Reservierungen berücksichtigt werden, sofern vorhanden.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im [Planungshandbuch für Datenträgerpools](disks-pools-planning.md).
