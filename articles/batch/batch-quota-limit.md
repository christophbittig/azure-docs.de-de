---
title: Dienstkontingente und Limits
description: Erfahren Sie mehr über Azure Batch-Standardkontingente, Limits und Einschränkungen sowie die Anforderung von Kontingentsteigerungen.
ms.topic: conceptual
ms.date: 07/20/2021
ms.custom: seodec18
ms.openlocfilehash: 799258258e1215977aea5b176d133bd121624bd6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239113"
---
# <a name="batch-service-quotas-and-limits"></a>Batch-Dienst – Kontingente und Limits

Ebenso wie bei anderen Azure-Diensten gelten bei bestimmten Ressourcen in Verbindung mit dem Batch-Dienst Limits. Viele dieser Limits sind Standardkontingente, die von Azure auf Abonnement- oder Kontoebene angewendet werden.

Bedenken Sie diese Kontingente beim Entwerfen und Hochskalieren Ihrer Batchworkloads. Wenn Ihr Pool beispielsweise nicht die von Ihnen vorgegebene Anzahl von Computeknoten erreicht, haben Sie möglicherweise die Kernkontingentgrenze für Ihr Batch-Konto erreicht.

Sie können mehrere Batch-Workloads in einem Batch-Konto ausführen oder Ihre Workloads auf Batch-Konten in demselben Abonnement, aber verschiedenen Azure-Regionen aufteilen.

Wenn Sie Produktionsworkloads in Batch ausführen möchten, müssen Sie möglicherweise ein oder mehrere Kontingente über den Standardwert erhöhen. Um eine Quote zu erhöhen, können Sie eine kostenlose [Quotenerhöhung anfordern](#increase-a-quota).

## <a name="resource-quotas"></a>Ressourcenkontingente

Bei einem Kontingent handelt es sich um einen Grenzwert und nicht um eine Kapazitätsgarantie. Wenn Sie einen umfangreichen Kapazitätsbedarf haben, wenden Sie sich an den Azure-Support.

Beachten Sie, dass Kontingente keine garantierten Werte darstellen. Kontingente können basierend auf Änderungen vom Batch-Dienst oder aufgrund einer Benutzeranforderung für eine Änderung des Kontingentwerts variieren.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Kernkontingente

### <a name="cores-quotas-in-batch-service-mode"></a>Kernkontingente im Batch-Dienstmodus

Für jede von der Batch unterstützten VM-Serie sind Kernquoten vorhanden. Diese Kernquoten werden auf der **Quoten**-Seite im Azure-Portal angezeigt. Kontingentgrenzen für VM-Serien können wie unten beschrieben über eine Supportanfrage aktualisiert werden. Bei dedizierten Knoten erzwingt Batch eine Kernquotengrenze für jede VM-Serie sowie eine Kernquotengrenze für das gesamte Batch-Konto. Bei Knoten mit niedriger Priorität erzwingt Batch nur ein Gesamtkernkontingent für das Batch-Konto ohne Unterscheidung zwischen unterschiedlichen VM-Serien.

### <a name="cores-quotas-in-user-subscription-mode"></a>Kontingente für Kerne im Modus „Benutzerabonnement“

Wenn Sie ein [Batch-Konto](accounts.md) mit dem Poolzuordnungsmodus **Benutzerabonnement** erstellt haben, werden Batch-VMs und andere Ressourcen bei der Poolerstellung oder der Größenänderung direkt in Ihrem Abonnement erstellt. Die Azure Batch-Kernkontingente gelten nicht. Stattdessen werden die Kontingente in Ihrem Abonnement für regionale Computekerne, Computekerne pro Serie und andere Ressourcen angewandt und erzwungen.

Weitere Informationen zu diesen Kontingenten finden Sie unter [Einschränkungen für Azure-Abonnements und -Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Poolgrößenbeschränkungen

Die Poolgrößenbeschränkungen werden vom Batch-Dienst festgelegt. Im Gegensatz zu [Ressourcenkontingenten](#resource-quotas) können diese Werte nicht geändert werden. Nur bei Pools mit Kommunikation zwischen den einzelnen Knoten und benutzerdefinierten Images weichen die Einschränkungen vom Standardkontingent ab.

| **Ressource** | **Maximales Limit** |
| --- | --- |
| **Serverknoten im Pool mit [aktivierter Kommunikation zwischen den Knoten](batch-mpi.md)**  ||
| Poolzuordnungsmodus für den Batch-Dienst | 100 |
| Poolzuordnungsmodus für das Batch-Abonnement | 80 |
| **Serverknoten in einem [Pool, der mit einer Ressource mit verwaltetem Image erstellt wurde](batch-custom-images.md)** <sup>1</sup> ||
| Dedizierte Knoten | 2000 |
| Knoten mit niedriger Priorität | 1000 |

<sup>1</sup> Für Pools, bei denen die Kommunikation zwischen den Knoten nicht aktiviert ist

## <a name="other-limits"></a>Andere Limits

Diese zusätzlichen Beschränkungen werden vom Batch-Dienst festgelegt. Im Gegensatz zu [Ressourcenkontingenten](#resource-quotas) können diese Werte nicht geändert werden.

| **Ressource** | **Maximales Limit** |
| --- | --- |
| [Gleichzeitige Aufgaben](batch-parallel-node-tasks.md) pro Computeknoten | 4 x Anzahl der Kerne des Knotens |
| [Anwendungen](batch-application-packages.md) pro Batch-Konto | 200 |
| Anwendungspakete pro Anwendung. | 40 |
| Anwendungspakete pro Pool | 10 |
| Maximale Lebensdauer von Tasks | 180 Tage<sup>1</sup> |
| [Bereitstellungen](virtual-file-mount.md) pro Computeknoten | 10 |
| Zertifikate pro Pool | 12 |

<sup>1</sup> Die maximale Lebensdauer eines Tasks (vom Hinzufügen zum Auftrag bis zum Abschluss) beträgt 180 Tage. Abgeschlossene Tasks bleiben sieben Tage lang erhalten. Daten für Tasks, die nicht innerhalb der maximalen Lebensdauer abgeschlossen wurden, stehen hingegen nicht zur Verfügung.

## <a name="view-batch-quotas"></a>Anzeigen von Batch-Kontingenten

So zeigen Sie die Kontingente Ihres Batch-Kontos im [Azure-Portal](https://portal.azure.com) an:

1. Wählen Sie **Batch-Konten** und dann das Batch-Konto aus, das Sie interessiert.
1. Wählen Sie im Menü des Batch-Kontos die Option **Kontingente** aus.
1. Zeigen Sie die Kontingente an, die derzeit für das Batch-Konto gelten.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Screenshot der Kontingente Ihres Batch-Kontos im Azure-Portal.":::

## <a name="increase-a-quota"></a>Erhöhen eines Kontingents

Sie können eine Quotenerhöhung für Ihr Batch-Konto oder Ihr Abonnement über das [Azure Portal](https://portal.azure.com) oder über die [Azure Quota REST API](#azure-quota-rest-api) anfordern.

Die Art der Kontingenterhöhung richtet sich nach dem Poolzuordnungsmodus Ihres Batch-Kontos. Um eine Erhöhung des Kontingents anzufordern, müssen Sie die VM-Serien angeben, deren Kontingent erhöht werden soll. Wenn die Erhöhung des Kontingents angewendet wird, gilt sie für alle VM-Serien.

Nachdem Sie die Supportanfrage übermittelt haben, wird sich der Azure-Support mit Ihnen in Verbindung setzen. Kontingentanfragen können innerhalb weniger Minuten abgeschlossen werden oder bis zu zwei Werktage dauern.

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie auf der **Quoten**-Seite die Option **Quotenerhöhung anfordern** aus. Alternativ können Sie die **Hilfe + Support**-Tafel auf Ihrem Portal-Dashboard (oder über das Fragezeichen ( **?** ) in der oberen rechten Ecke des Portals) auswählen und dann **Neue Supportanfrage** wählen.

1. Gehen Sie unter **Grundlegende Einstellungen** wie folgt vor:

    1. Wählen Sie unter **Problemtyp** den Eintrag **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.
    1. Wählen Sie Ihr Abonnement aus.
    1. Wählen Sie unter **Quotentyp** den Eintrag **Cosmos DB** aus.
    1. Klicken Sie auf **Weiter**, um fortzufahren.

1. Unter **Details** finden Sie folgende Informationen:

    1. Geben Sie im Abschnitt **Details bereitstellen** den Standort, den Quotentyp und das Batch-Konto (falls zutreffend) an und wählen Sie dann das/die zu erhöhenden Quoten aus.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Screenshot des Bildschirms mit den Details zu den Kontingenten beim Anfordern einer Kontingenterhöhung.":::

       Es gibt folgende Kontingenttypen:

       - **Pro Batch-Konto**  
         Verwenden Sie diese Option, um Kontingent Erhöhungen für ein spezifisches Batch-Konto anzufordern, einschließlich dedizierter und niedriger Prioritäts-Kerne, sowie die Anzahl der Aufträge und Pools.

         Wenn Sie diese Option auswählen, geben Sie das Batch-Konto an, auf das diese Anforderung angewendet werden soll, und wählen Sie dann die Quoten aus, die Sie aktualisieren möchten. Geben Sie den neuen Grenzwert an, den Sie für jede Ressource anfordern.

         Das Kontingent mit niedriger Priorität ist ein einzelner Wert für alle VM-Serien. Wenn Sie eingeschränkte SKUs benötigen, müssen Sie **Kerne mit niedriger Priorität** auswählen und die anzufordernden VM-Produktfamilien einschließen.

       - **Alle Konten in diesem Bereich**  
         Verwenden Sie diese Option, um Quotenerhöhungen anzufordern, die für alle Batch-Konten innerhalb eines Bereichs gelten, z. b. die Anzahl von Batch-Konten pro Region und Abonnement.

    1. Wählen Sie unter **Support-Methode** einen **Schweregrad** entsprechend der [Auswirkung auf Ihre Geschäftstätigkeit](https://aka.ms/supportseverity) und Ihre bevorzugte Kontaktmethode und Support-Sprache.

    1. Überprüfen Sie unter **Kontaktinformationen** die erforderlichen Kontaktinformationen, und geben Sie die Details ein.

1. Wählen Sie die Option **Überprüfen und erstellen** und dann **Erstellen** aus, um die Unterstützungsanforderung zu übermitteln.

### <a name="azure-quota-rest-api"></a>Azure Quota REST API

Sie können die Azure Quota REST-API verwenden, um eine Quotenerhöhung auf der Abonnementebene oder auf der Batch-Kontoebene anzufordern.

Weitere Informationen und Beispiele finden [Sie unter Anfordern einer Kontingent Erhöhung mithilfe der Azure-Support-REST-API](/rest/api/support/quota-payload#azure-batch).

## <a name="related-quotas-for-vm-pools"></a>Verknüpfte Kontingente für VM-Pools

[Batch-Pools in der Konfiguration von virtuellen Computern, die in einem virtuellen Azure-Network bereitgestellt wurden](batch-virtual-network.md), ordnen automatisch zusätzliche Azure-Netzwerkressourcen zu. Diese Ressourcen werden in dem Abonnement erstellt, welches das virtuelle Netzwerk enthält, das beim Erstellen des Batch-Pools bereitgestellt wurde.

Die folgenden Ressourcen sind für alle 100 Poolknoten in einem virtuellen Netzwerk erforderlich:

- Eine [Netzwerksicherheitsgruppe](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Eine [öffentliche IP-Adresse](../virtual-network/ip-services/public-ip-addresses.md)
- Ein [Load Balancer](../load-balancer/load-balancer-overview.md)

Diese Ressourcen werden durch die [Ressourcenkontingente](../azure-resource-manager/management/azure-subscription-service-limits.md) des Abonnements beschränkt. Wenn Sie umfangreiche Pool-Bereitstellungen in einem virtuellen Netzwerk planen, müssen Sie möglicherweise eine Erhöhung der Quoten für eine oder mehrere dieser Ressourcen anfordern.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Erfahren Sie mehr über [Einschränkungen für Azure-Abonnements und -Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).