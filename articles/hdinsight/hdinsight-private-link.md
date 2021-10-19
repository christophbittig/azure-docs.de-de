---
title: Aktivieren von Private Link in einem eingeschränkten HDInsight-Cluster (Vorschau)
description: Erfahren Sie, wie Sie über Azure Private Link eine Verbindung mit einem HDInsight-Cluster herstellen.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 9f432d37e58069decdc9a97e55d926aed3b7277f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710019"
---
# <a name="enable-private-link-on-hdinsight-cluster-preview"></a>Aktivieren von Private Link in einem HDInsight-Cluster (Vorschau)

## <a name="overview"></a>Übersicht
In diesem Artikel erfahren Sie, wie Sie Azure Private Link nutzen, um netzwerkübergreifend eine private Verbindung mit Ihrem HDInsight-Cluster über das Microsoft-Backbonenetzwerk herzustellen. Dieser Artikel ist eine Erweiterung unseres Hauptartikels [Einschränken der öffentlichen Clusterkonnektivität in Azure HDInsight](./hdinsight-restrict-public-connectivity.md), in dem es primär um die Einschränkung der öffentlichen Konnektivität geht. Falls Sie öffentliche Konnektivität mit Ihren HDInsight-Clustern und abhängigen Ressourcen zulassen müssen, sollten Sie die Konnektivität des Clusters einschränken, indem Sie die Richtlinien zum [Steuern des Netzwerkdatenverkehrs in Azure HDInsight](./control-network-traffic.md) befolgen.

Private Link kann in VNet-übergreifenden Szenarien genutzt werden, in denen VNet-Peering nicht verfügbar oder aktiviert ist. Ein Beispiel: Sie möchten Azure Data Factory in Azure HDInsight integrieren, und Azure Data Factory muss aus Compliance- und Sicherheitsgründen eine Verbindung mit HDInsight-Clustern über ein privates Netzwerk (d. h. Private Link) herstellen.

> [!NOTE]
> Das Einschränken der öffentlichen Konnektivität ist eine Voraussetzung für die Aktivierung von Private Link und sollte nicht als dieselbe Funktion betrachtet werden.

Private Link ist ein optionales Feature und standardmäßig deaktiviert. Das Feature ist nur verfügbar, wenn die Netzwerkeigenschaft `resourceProviderConnection` auf *ausgehend* festgelegt ist, wie im Artikel [Einschränken der Clusterkonnektivität in Azure HDInsight](./hdinsight-restrict-public-connectivity.md) beschrieben.

Wenn `privateLink` auf *Enabled* (Aktiviert) festgelegt ist, werden interne [Standard-Lastenausgleichsmodule](../load-balancer/load-balancer-overview.md) erstellt, und für jedes dieser Module wird ein Azure Private Link-Dienst bereitgestellt. Der Private Link-Dienst ermöglicht Ihnen den Zugriff auf den HDInsight-Cluster von privaten Endpunkten.

## <a name="prerequisites"></a>Voraussetzungen

Standard-Lastenausgleichsmodule stellen nicht automatisch eine [öffentliche Netzwerkadressenübersetzung in ausgehender Richtung](../load-balancer/load-balancer-outbound-connections.md) bereit, so wie es bei Basic-Lastenausgleichsmodulen der Fall ist. Sie müssen eine eigene NAT-Lösung bereitstellen, z. B. ein NAT-Gateway oder eine von Ihrer [Firewall](./hdinsight-restrict-outbound-traffic.md) bereitgestellte Adressübersetzung, um eine Verbindung mit ausgehenden öffentlichen HDInsight-Abhängigkeiten herzustellen. Ihr HDInsight-Cluster benötigt weiterhin Zugriff auf seine ausgehenden Abhängigkeiten. Falls diese ausgehenden Abhängigkeiten nicht zulässig sind, kann der Cluster ggf. nicht erstellt werden. Es muss auch eine Netzwerksicherheitsgruppe im Subnetz konfiguriert werden, um ausgehende Konnektivität zu ermöglichen.

### <a name="1--configure-a-default-network-security-group-nsg-on-the-subnet"></a>1. Konfigurieren einer Standard-Netzwerksicherheitsgruppe (NSG) im Subnetz

Erstellen Sie eine Netzwerksicherheitsgruppe in dem Subnetz, in dem Sie den HDInsight-Cluster bereitstellen möchten, und fügen Sie die Gruppe hinzu.

### <a name="2--disable-network-policies-for-private-link-service"></a>2. Deaktivieren von Netzwerkrichtlinien für den Private Link-Dienst

Für die erfolgreiche Erstellung von Private Link-Diensten müssen Sie explizit die [Richtlinien für Private Link-Dienste deaktivieren](../private-link/disable-private-link-service-network-policy.md).

### <a name="3--configure-a-nat-gateway-on-the-subnet"></a>3. Konfigurieren eines NAT-Gateways im Subnetz

Sie können ein NAT-Gateway verwenden, wenn Sie keine Firewall oder virtuellen Netzwerkgeräte für die Netzwerkadressübersetzung verwenden möchten. Andernfalls fahren Sie mit der nächsten Voraussetzung fort.

Fügen Sie zum Einstieg einfach ein NAT-Gateway (mit einer neuen öffentlichen IP-Adresse in Ihrem virtuellen Netzwerk) zum konfigurierten Subnetz Ihres virtuellen Netzwerks hinzu. Dieses Gateway ist für die Übersetzung Ihrer privaten internen IP-Adresse in öffentliche Adressen zuständig, wenn Datenverkehr Ihr virtuelles Netzwerk verlassen muss.

### <a name="4--using-firewall-or-network-virtual-appliance-nvas-for-nat-optional"></a>4. Verwenden einer Firewall oder eines virtuellen Netzwerkgeräts (Network Virtual Appliance, NVA) für NAT (optional)
Um eine grundlegende Einrichtung zu konfigurieren, fügen Sie Ihrem virtuellen Netzwerk zunächst ein neues Subnetz namens „AzureFirewallSubnet“ hinzu. Verwenden Sie dieses Subnetz, um eine neue Firewall zu konfigurieren und Ihre Firewallrichtlinien hinzuzufügen. Nachdem Ihre Firewall eingerichtet wurde, verwenden Sie die private IP-Adresse dieser Firewall als `nextHopIpAddress` in Ihrer Routingtabelle. Fügen Sie diese Routingtabelle dem konfigurierten Subnetz Ihres virtuellen Netzwerks hinzu.
Weitere Informationen zum Einrichten einer Firewall finden Sie unter [Steuern des Netzwerkdatenverkehrs in Azure HDInsight](./control-network-traffic.md).

Das folgende Diagramm enthält ein Beispiel für die Netzwerkkonfiguration, die vor dem Erstellen eines Clusters erforderlich ist. In diesem Beispiel wird für den gesamten ausgehenden Datenverkehr per UDR der Weg über Azure Firewall erzwungen, und die erforderlichen ausgehenden Abhängigkeiten sollten in der Firewall „zugelassen“ werden, bevor ein Cluster erstellt wird. Bei Clustern mit Enterprise-Sicherheitspaket kann die Netzwerkkonnektivität mit Azure Active Directory Domain Services per VNET-Peering bereitgestellt werden.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagramm: Private Link-Umgebung vor der Clustererstellung":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Verwalten von privaten Endpunkten für Azure HDInsight

Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) für Ihre Azure HDInsight-Cluster verwenden, um Clients in einem virtuellen Netzwerk (VNet) sicheren Zugriff auf Ihren Cluster über [Private Link](../private-link/private-link-overview.md) zu ermöglichen. Der Netzwerkdatenverkehr zwischen den Clients im VNet und dem HDInsight-Cluster durchquert das Microsoft-Backbonenetzwerk, ohne in das öffentliche Internet zu gelangen.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="Diagramm der Verwaltungsoberfläche für private Endpunkte":::

Es gibt zwei Methoden zur Genehmigung von Verbindungen, aus denen ein Private Link-Dienstconsumer (z. B. Azure Data Factory) auswählen kann:
* **Automatisch**: Wenn der Dienstconsumer über Azure RBAC-Berechtigungen für die HDInsight-Ressource verfügt, kann der Consumer die automatische Genehmigungsmethode auswählen. In diesem Fall ist keine Aktion seitens der HDInsight-Ressource erforderlich, wenn die Anforderung diese Ressource erreicht, und die Verbindung wird automatisch genehmigt.
* **Manuell**: Im Gegensatz dazu kann der Dienstconsumer die manuelle Genehmigungsmethode auswählen, wenn der Consumer nicht über Azure RBAC-Berechtigungen für die HDInsight-Ressource verfügt. In diesem Fall wird die Verbindungsanforderung in den HDInsight-Ressourcen als „Ausstehend“ angezeigt. Die Anforderung muss manuell von der HDInsight-Ressource genehmigt werden, bevor Verbindungen hergestellt werden können. 

Navigieren Sie zum Verwalten privater Endpunkte im Azure-Portal in der Ansicht Ihres Clusters unter „Sicherheit und Netzwerkbetrieb“ zum Abschnitt „Netzwerk (Vorschau)“. Hier können Sie alle vorhandenen Verbindungen, Verbindungszustände und Details zu privaten Endpunkten anzeigen.
Sie können vorhandene Verbindungen auch genehmigen, ablehnen oder entfernen. Beim Erstellen einer privaten Verbindung können Sie angeben, mit welcher HDInsight-Unterressource (Gateway, Hauptknoten usw.) ebenfalls eine Verbindung hergestellt werden soll.

Die folgende Tabelle zeigt die verschiedenen HDInsight-Ressourcenaktionen und die resultierenden Verbindungszustände für private Endpunkte. Die HDInsight-Ressource kann den Verbindungszustand der privaten Endpunktverbindung auch zu einem späteren Zeitpunkt ohne Eingriff des Consumers ändern. Durch die Aktion wird der Zustand des Endpunkts auf der Consumerseite aktualisiert.

| Dienstanbieteraktion | Privater Endpunktzustand des Dienstconsumers | BESCHREIBUNG |
| --------- | --------- | --------- |
| Keine | Ausstehend | Die Verbindung wird manuell erstellt, und die Genehmigung des Besitzers der Private Link-Ressource steht aus. |
| Genehmigen | Genehmigt | Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit. |
| Reject | Rejected (Abgelehnt) | Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt. |
| Remove (Entfernen) | Getrennt | Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt, der private Endpunkt wird informativ und sollte zur Bereinigung gelöscht werden. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>Konfigurieren von DNS für die Verbindung über private Endpunkte

Nachdem Sie das Netzwerk eingerichtet haben, können Sie einen Cluster mit einer ausgehenden Ressourcenanbieterverbindung und Private Link-Aktivierung erstellen. Dies ist in der folgenden Abbildung dargestellt.
Für den Zugriff auf private Cluster können Sie Private Link-DNS-Erweiterungen und private Endpunkte verwenden. Wenn die Einstellung `privateLink` auf „aktiviert“ festgelegt ist, können Sie private Endpunkte erstellen und die DNS-Auflösung über private DNS-Zonen konfigurieren.
In der von Azure verwalteten öffentlichen DNS-Zone `azurehdinsight.net` werden die folgenden Private Link-Einträge erstellt:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
Die folgende Abbildung zeigt ein Beispiel für die privaten DNS-Einträge, die für den Zugriff auf einen Cluster aus einem virtuellen Netzwerk konfiguriert werden, das weder per Peering verbunden ist noch über eine direkte Sichtverbindung mit dem Cluster verfügt. Sie können eine private Azure-Zone verwenden, um FQDNs vom Typ `*.privatelink.azurehdinsight.net` außer Kraft zu setzen und IP-Adressen für private Endpunkte im Netzwerk des Clients aufzulösen.
Dies wird nur für `<clustername>.azurehdinsight.net` angezeigt, gilt aber auch für andere Clusterendpunkte.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagramm: Private Link-Architektur":::

## <a name="how-to-create-clusters"></a>Wie werden Cluster erstellt?
### <a name="use-arm-template-properties"></a>Verwenden der Eigenschaften von ARM-Vorlagen

Der folgende JSON-Codeausschnitt enthält die beiden Netzwerkeigenschaften, die Sie in Ihrer ARM-Vorlage konfigurieren müssen, um einen privaten HDInsight-Cluster zu erstellen.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

Eine vollständige Vorlage mit vielen dieser HDInsight-Sicherheitsfunktionen für Unternehmen, z. B. Private Link, finden Sie im Artikel zur [Enterprise-Sicherheitsvorlage für HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

Informationen zur Verwendung von PowerShell finden Sie [hier in dem Beispiel](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

### <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle
Informationen zur Verwendung der Azure CLI finden Sie [hier](/cli/azure/hdinsight#az_hdinsight_create-examples) in dem Beispiel.

## <a name="next-steps"></a>Nächste Schritte

* [Enterprise-Sicherheitspaket für Azure HDInsight](enterprise-security-package.md)
* [Allgemeine Informationen und Richtlinien für die Unternehmenssicherheit in Azure HDInsight](./domain-joined/general-guidelines.md)