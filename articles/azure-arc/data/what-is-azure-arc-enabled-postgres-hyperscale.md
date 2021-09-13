---
title: Was ist PostgreSQL Hyperscale mit Azure Arc-Unterstützung?
description: Was ist PostgreSQL Hyperscale mit Azure Arc-Unterstützung?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d43753084f79184b338b16223ed45cb00a2dfb33
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732456"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Was ist PostgreSQL Hyperscale mit Azure Arc-Unterstützung?

PostgreSQL Hyperscale mit Azure Arc-Unterstützung ist einer der Datenbankdienste, die als Teil von Azure Arc-fähigen Datendiensten verfügbar sind. Azure Arc ermöglicht das lokale Ausführen von Azure Data Services in Edgeumgebungen und in öffentlichen Clouds mithilfe von Kubernetes und der Infrastruktur Ihrer Wahl. Das Wertversprechen von Azure Arc-fähigen Datendiensten umfasst Folgendes:
- Immer aktuell
- Elastische Skalierung
- Self-Service-Bereitstellung
- Einheitliche Verwaltung
- Unterstützung für nicht verbundenes Szenario

Weitere Informationen finden Sie in den folgenden Themen:
- [Was sind Datendienste mit Azure Arc-Unterstützung?](overview.md)
- [Konnektivitätsmodi und -anforderungen](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Wenn Sie mehr zu diesen Funktionen erfahren möchten, können Sie sich auch diese Data Exposed-Folge ansehen.
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]

## <a name="compare-solutions"></a>Vergleichen von Lösungen

In diesem Abschnitt wird beschrieben, inwiefern sich PostgreSQL Hyperscale mit Azure Arc-Unterstützung von Azure Database for PostgreSQL Hyperscale (Citus) unterscheidet.

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL Hyperscale (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database for PostgreSQL Hyperscale (Citus)":::

Dies ist der Hyperscale-Formfaktor der Postgres-Datenbank-Engine, der als Database-as-a-Service in Azure (PaaS) verfügbar ist. Er wird von der Citus-Erweiterung unterstützt, die die Hyperscale-Umgebung ermöglicht. In diesem Formfaktor wird der Dienst in den Microsoft-Rechenzentren ausgeführt und von Microsoft betrieben.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale mit Azure Arc-Unterstützung

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="PostgreSQL Hyperscale mit Azure Arc-Unterstützung":::

Dies ist der Hyperscale-Formfaktor der Postgres-Datenbank-Engine, der mit den Azure Arc-fähigen Datendiensten verfügbar ist. Er wird auch von der Citus-Erweiterung unterstützt, die die Hyperscale-Umgebung ermöglicht. In diesem Formfaktor stellen unsere Kunden die Infrastruktur bereit, die die Systeme hostet, und betreiben sie.

## <a name="next-steps"></a>Nächste Schritte
- **Probieren Sie sie aus.** Mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte ausführen. 

- **Erstellen Sie einen eigenen Cluster.** Führen Sie die folgenden Schritte aus, um einen eigenen Kubernetes-Cluster zu erstellen: 
   1. [Installieren der Clienttools](install-client-tools.md)
   2. [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md)
   3. [Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Weitere Informationen zu Datendiensten mit Azure Arc-Unterstützung](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Weitere Informationen zu Azure Arc](https://aka.ms/azurearc)
