---
title: Migrieren von lokalen Computern mit Azure Migrate
description: In diesem Artikel wird erläutert, wie Sie Computer zu Azure migrieren. Dazu wird Azure Migrate empfohlen.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: e4eb6bdd2153cd67fb6dbdf442a09485ca2ef4e1
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177951"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrieren von lokalen Computern zu Azure

In diesem Artikel werden die Optionen für die Migration lokaler Computer zu Azure beschrieben. 

## <a name="migrate-with-azure-migrate"></a>Migrieren mit Azure Migrate

Es wird empfohlen, den [Azure Migrate](../migrate/migrate-services-overview.md)-Dienst zu verwenden, um Computer zu Azure zu migrieren. Azure Migrate ist speziell für die Servermigration konzipiert. Azure Migrate bietet einen zentralen Hub für die Ermittlung und Bewertung von lokalen Computern und für ihre Migration zu Azure.

Nutzen Sie für die Migration mit Azure Migrate die folgenden Links:

- Erfahren Sie mehr über die [Migrationsoptionen](../migrate/server-migrate-overview.md) für virtuelle VMware-Computer, und migrieren Sie dann virtuelle Computer mithilfe der Migration [ohne Agent](../migrate/tutorial-migrate-vmware.md) oder der [Agent-basierten](../migrate/tutorial-migrate-vmware-agent.md) Migration zu Azure.
- [Migrieren Sie virtuelle Hyper-V-Computer zu Azure.](../migrate/tutorial-migrate-hyper-v.md)
- Migrieren Sie [physische Server oder andere virtuelle Computer](../migrate/tutorial-migrate-physical-virtual-machines.md), einschließlich [AWS-Instanzen](../migrate/tutorial-migrate-aws-virtual-machines.md) zu Azure.

## <a name="migrate-with-site-recovery"></a>Migrieren mit Site Recovery
Site Recovery sollte nur für die Notfallwiederherstellung und nicht für die Migration verwendet werden.

Wenn Sie Azure Site Recovery bereits verwenden und weiterhin für die Migration verwenden möchten, führen Sie dieselben Schritte aus wie für die Notfallwiederherstellung.

- VMware-VMs: [Vorbereiten von Azure](tutorial-prepare-azure.md) und [VMware](vmware-azure-tutorial-prepare-on-premises.md), Starten der [Replikation von Computern](vmware-azure-tutorial.md), [Überprüfen](tutorial-dr-drill-azure.md), ob alles richtig funktioniert und [Ausführen eines Failovers](vmware-azure-tutorial-failover-failback.md)
- Hyper-V-VMs: [Vorbereiten von Azure](tutorial-prepare-azure-for-hyperv.md) und [Hyper-V](hyper-v-prepare-on-premises-tutorial.md), Starten der [Replikation von Computern](hyper-v-azure-tutorial.md), [Überprüfen](tutorial-dr-drill-azure.md), ob alles richtig funktioniert und [Ausführen eines Failovers](hyper-v-azure-failover-failback-tutorial.md)
- Physische Server: [Befolgen der exemplarischen Vorgehensweise](physical-azure-disaster-recovery.md) zur Vorbereitung von Azure, Vorbereiten der Computer für die Notfallwiederherstellung und Einrichten der Replikation

> [!NOTE]
> Wenn Sie ein Failover für Notfallwiederherstellung ausführen, führen Sie als letzten Schritt ein Commit für das Failover aus. Beim Migrieren von lokalen Computern ist die Option **Commit** nicht relevant. Sie wählen stattdessen die Option **Migration abschließen** aus. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lesen Sie Antworten auf gängige Fragen](../migrate/resources-faq.md) zu Azure Migrate.

  
