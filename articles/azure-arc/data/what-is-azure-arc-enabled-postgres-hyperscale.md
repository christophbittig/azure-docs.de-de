---
title: Was ist PostgreSQL Hyperscale mit Azure Arc-Unterstützung?
description: Was ist PostgreSQL Hyperscale mit Azure Arc-Unterstützung?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 39856b916e93e928c754add5ed25cf5192fba04f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298245"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Was ist PostgreSQL Hyperscale mit Azure Arc-Unterstützung?

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="what-is-azure-arc-vs-azure-arc-enabled-data-services-vs-azure-arc-enabled-postgresql-hyperscale"></a>Was ist Azure Arc im Vergleich zu Azure Arc-fähigen Datendiensten und PostgreSQL Hyperscale mit Azure Arc-Unterstützung?

**Azure Arc** ist eine der Säulen der Azure-Hybridfamilie: Azure Arc, Azure Stack und Azure IoT. Azure Arc unterstützt Kunden dabei, die Komplexität ihrer hybriden Bereitstellungen zu beherrschen, indem es die Kundenfreundlichkeit verbessert. Bei Azure Stack stellen Microsoft oder seine Partner die Hardware und die Software (eine Appliance) bereit. Bei Azure Arc stellt Microsoft nur die Software bereit. Der Kunde oder seine Partner stellen die unterstützende Infrastruktur bereit und betreiben die Lösung. Azure Arc wird in Azure Stack unterstützt. Azure Arc ermöglicht Ihnen die Ausführung von Azure-Diensten in Infrastrukturen, die sich außerhalb der Azure-Rechenzentren befinden, und erlaubt Ihnen die Integration in andere verwaltete Azure-Dienste, wenn Sie dies wünschen.

**Azure Arc-fähige Datendienste** sind Bestandteil von Azure Arc. Es handelt sich um eine Sammlung von Produkten und Diensten, mit denen Kunden ihre Daten verwalten können. Sie bieten den Kunden folgende Möglichkeiten:

- Azure-Datendienste können in einer beliebigen physischen Infrastruktur ausgeführt werden.
- Abläufe werden optimiert, indem überall die gleiche Cloudtechnologie zum Einsatz kommt.
- Die Anwendungsentwicklung wird optimiert, indem unabhängig davon, wo die Anwendung oder Datenbank gehostet wird – in Azure PaaS oder in Azure Arc – dieselbe zugrunde liegende Technologie genutzt wird.
- Cloudtechnologien kommen in eigenen Rechenzentrum zum Einsatz, bei gleichzeitiger Einhaltung gesetzlicher Vorschriften (Datenresidenz und Kundenkontrolle). Anders ausgedrückt: Wenn ein Umzug in die Cloud nicht möglich ist, kommt die Cloud zu Ihnen.

Azure Arc-fähige Datendienste bieten Ihnen u. a. folgende Vorteile:
- Immer aktuell
- Elastische Skalierung
- Self-Service-Bereitstellung
- Einheitliche Verwaltung
- Cloudabrechnung
- Unterstützung für Szenarien mit dauerhafter Verbindung (mit Azure) und gelegentlicher Verbindung (mit Azure): direkter und indirekter Konnektivitätsmodus

**PostgreSQL Hyperscale mit Azure Arc-Unterstützung** ist eine der Datenbank-Engines, die im Rahmen der Azure Arc-fähigen Datendienste zur Verfügung stehen. 


## <a name="compare-postgres-solutions-provided-by-microsoft-in-azure"></a>Vergleichen der von Microsoft in Azure bereitgestellten Postgres-Lösungen

Microsoft bietet Postgres-Datenbankdienste in Azure auf zwei Arten an:
- Als verwalteter Dienst in Azure PaaS (Platform-as-a-Service)
- Als teilweise verwalteter Dienst mit Azure Arc, da der Betrieb durch Kunden oder deren Partner/Anbieter erfolgt

### <a name="in-azure-paas"></a>In Azure PaaS
**In [Azure PaaS](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)** bietet Microsoft mehrere Bereitstellungsoptionen für Postgres als verwalteten Dienst an:

:::row:::
    :::column:::
        Azure Database for Postgres Single Server und Azure Database for Postgres Flexible Server. Bei diesen Diensten handelt es sich um einen von Microsoft verwalteten Postgres-Formfaktor mit einem einzelnen Knoten/einer einzelnen Instanz. Azure Database for Postgres Flexible Server ist die neueste Weiterentwicklung dieses Diensts.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/azure-database-for-postgresql-bigger.png" alt-text="Azure-Datenbank für PostgreSQL":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Azure Database for PostgreSQL Hyperscale (Citus): Bei diesen Dienst handelt es sich um den von Microsoft verwalteten Postgres-Formfaktor mit mehreren Knoten/Instanzen. Er basiert auf der Citus-Erweiterung für Postgres, durch die Postgres für Einzelknoten in ein verteiltes Datenbanksystem transformiert wird. Beim Aufskalieren werden die Daten und Abfragen verteilt, sodass Ihre Workload ein noch nie dagewesenes Maß an Skalierung und Leistung erreichen kann. Die Anwendung erkennt eine einzelne Postgres-Instanz, die auch als Servergruppe bezeichnet wird. Im Hintergrund besteht diese Servergruppe jedoch aus mehreren Postgres-Instanzen, die zusammenarbeiten. Beim Aufskalieren erhöhen Sie die Anzahl von Postgres-Instanzen innerhalb der Servergruppe, was die Leistung und Skalierbarkeit Ihrer Workload verbessern kann. Sie entscheiden abhängig von Ihren Anforderungen und den Eigenschaften der Workload, wie viele Postgres-Instanzen Sie der Servergruppe hinzufügen. 
        Weitere Informationen erhalten Sie in den folgenden Videos:
        - [DEMO – Hochleistungs-HTAP mit Postgres Hyperscale (Citus):](https://youtu.be/W_3e07nGFxY )
        - [DEMO – Entwickeln von HTAP-Anwendungen mit Python und Postgres in Azure](https://youtu.be/YDT8_riLLs0 )
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure Database for PostgreSQL Hyperscale (Citus)":::
    :::column-end:::
:::row-end:::



### <a name="with-azure-arc"></a>Mit Azure Arc

:::row:::
    :::column:::
        **Mit Azure Arc** stellt Microsoft **ein einziges** Postgres-Produkt bzw. einen einzigen Postgres-Dienst bereit: **PostgreSQL Hyperscale mit Azure Arc-Unterstützung**. Durch Azure Arc wurden die Produktdefinition und das Kundenerlebnis für Postgres im Vergleich zu Azure PaaS verbessert, indem **ein Postgres-Produkt** angeboten wird, das Folgendes ermöglicht:
        - Bereitstellung von Postgres mit Einzelknoten/Einzelinstanz wie bei Azure Database for Postgres Single/Flexible Server.
        - Bereitstellung von Postgres mit mehreren Knoten/Instanzen wie bei Azure Database for PostgreSQL Hyperscale (Citus).
        - Hohe Flexibilität, da die Kunden ihre Postgres-Implementierungen auf Wunsch von einem auf mehrere Postgres-Knoten und umgekehrt umstellen können. Diese Umstellung kann ohne Datenmigration und über eine einfache Funktion durchgeführt werden.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="PostgreSQL Hyperscale mit Azure Arc-Unterstützung":::
    :::column-end:::
:::row-end:::

Wie sein Äquivalent in Azure PaaS wird Postgres in der Variante für mehrere Knoten/Instanzen von der Citus-Erweiterung unterstützt, die Postgres für Einzelknoten in ein verteiltes Datenbanksystem transformiert. Beim Aufskalieren werden die Daten und Abfragen verteilt, sodass Ihre Workload ein noch nie dagewesenes Maß an Skalierung und Leistung erreichen kann. Die Anwendung erkennt eine einzelne Postgres-Instanz, die auch als Servergruppe bezeichnet wird. Im Hintergrund besteht diese Servergruppe jedoch aus mehreren Postgres-Instanzen, die zusammenarbeiten. Beim Aufskalieren erhöhen Sie die Anzahl von Postgres-Instanzen innerhalb der Servergruppe, was die Leistung und Skalierbarkeit Ihrer Workload verbessern kann. Sie entscheiden abhängig von Ihren Anforderungen und den Eigenschaften der Workload, wie viele Postgres-Instanzen Sie der Servergruppe hinzufügen. Falls gewünscht, können Sie die Anzahl von Postgres-Instanzen in der Servergruppe auf 1 reduzieren.  Mit Azure Arc bieten alle von Microsoft bereitgestellten Postgres-Bereitstellungen Unterstützung für Hyperscale.


Mithilfe des direkten Konnektivitätsmodus, der von Azure Arc-fähigen Datendiensten angeboten wird, können Sie PostgreSQL Hyperscale mit Azure Arc-Unterstützung über das Azure-Portal bereitstellen. Wenn Sie den Modus für indirekte Verbindungen verwenden, stellen Sie PostgreSQL Hyperscale mit Azure Arc-Unterstützung aus der Infrastruktur bereit, über die das Hosting erfolgt.

**PostgreSQL Hyperscale mit Azure Arc-Unterstützung ermöglicht Folgendes:**
- Einfaches Verwalten von Postgres
    - Bereitstellen/Aufheben der Bereitstellung von Postgres-Instanzen mit einem Befehl
    - Beliebige Skalierung: Aufskalieren/Abskalieren, Hochskalieren/Herunterskalieren 
- Vereinfachen von Überwachung, Failover, Sicherung, Patchen/Upgrade, Zugriffssteuerung und mehr
- Entwickeln von Postgres-Apps mit beispielloser Skalierung und Leistung
    - Horizontales Skalieren von Computeressourcen über mehrere Postgres-Instanzen hinweg
    - Verteilen von Daten und Abfragen
    - Ausführen der Citus-Erweiterung
    - Transformieren von PostgreSQL Standard in ein verteiltes Datenbanksystem
- Bereitstellen von Postgres in einer beliebigen Infrastruktur
    - Lokal, Multi-Cloud (AWS, GCP, Azure), Edge
- Integration in Azure (optional)
- Nutzungsbasierte Bezahlung (Abrechnung nach Verbrauch)
- Support von Microsoft für Postgres

**Weitere Überlegungen**:
- PostgreSQL Hyperscale mit Azure Arc-Unterstützung ist weder eine neue Datenbank-Engine noch eine spezielle Version einer bestehenden Datenbank-Engine. Es handelt sich um dieselbe Datenbank-Engine, die auch in Azure PaaS ausgeführt wird. Denken Sie daran: Wenn Sie nicht in die Microsoft-Cloud umziehen können, kommt mit Azure Arc die Microsoft-Cloud zu Ihnen. Die Innovation von Azure Arc beruht auf der Art und Weise, in der Microsoft diese Datenbank-Engine bereitstellt, sowie auf den damit verbundenen Nutzungsmöglichkeiten. 

- PostgreSQL Hyperscale mit Azure Arc-Unterstützung ist ebenfalls keine Datenreplikationslösung. Ihre Geschäftsdaten verbleiben in Ihrer Arc-Bereitstellung. Es erfolgt keine Replikation in die Azure-Cloud. Einzige Ausnahme: Sie haben sich entschieden, ein Feature wie z. B. Datenreplikation/Lesereplikate der Datenbank-Engine einzurichten. In diesem Fall werden Ihre Daten möglicherweise außerhalb Ihrer Postgres-Bereitstellung repliziert: nicht aufgrund von Azure Arc, sondern weil Sie sich entschieden haben, eine Datenreplikationsfunktion einzurichten.

- Sie müssen keinen speziellen Treiber oder Anbieter für Ihre Workload verwenden, um mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung zu arbeiten. Jede „Postgres-Anwendung“ sollte mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung ausgeführt werden können.

- Das Auf- und Abskalieren erfolgt nicht automatisch. Die Skalierung wird von den Benutzern gesteuert. Benutzer können Skripts für diese Vorgänge erstellen und die Ausführung dieser Skripts automatisieren. Nicht alle Workloads können von einer Aufskalierung profitieren. Weitere Informationen zu diesem Thema finden Sie im Abschnitt „Nächste Schritte“.

**Wenn Sie mehr über diese Funktionen erfahren möchten, können Sie sich diese Data Exposed-Folge ansehen:**
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]


## <a name="roles-and-responsibilities-azure-managed-services-platform-as-a-service-paas-_vs_-azure-arc-enabled-data-services"></a>Rollen und Zuständigkeiten: verwaltete Azure-Dienste (Platform-as-a-Service, PaaS) _im Vergleich zu_ Azure Arc-fähigen Datendiensten
:::image type="content" source="media/postgres-hyperscale/rr-azure-paas-vs-azure-arc.png" alt-text="Rollen und Zuständigkeiten: Azure PaaS und Azure Arc im Vergleich":::

## <a name="next-steps"></a>Nächste Schritte
- **Probieren Sie sie aus.** Mit dem [Azure Arc-Schnelleinstieg](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte ausführen. 

- **Führen Sie eine Bereitstellung durch, und erstellen Sie einen eigenen Cluster.** Führen Sie die folgenden Schritte aus, um einen eigenen Kubernetes-Cluster zu erstellen: 
   1. [Installieren der Clienttools](install-client-tools.md)
   2. [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md)
   3. [Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc](https://aka.ms/azurearc)
   - Azure Arc-fähige Datendienste: [hier](https://azure.microsoft.com/services/azure-arc/hybrid-data-services) und [hier](overview.md)
   - [Konnektivitätsmodi und -anforderungen](connectivity.md)



- **Lesen Sie die Konzeptleitfäden und Schrittanleitungen zu Azure Database for PostgreSQL Hyperscale, um Ihre Daten auf mehrere PostgreSQL Hyperscale-Knoten zu verteilen und potenziell von einer besseren Leistung zu profitieren**:
    * [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
    * [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
    * [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)* 

