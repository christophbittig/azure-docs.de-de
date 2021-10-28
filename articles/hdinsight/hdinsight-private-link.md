---
title: Aktivieren von Private Link in einem Azure HDInsight-Cluster
description: Hier erfahren Sie, wie Sie über Azure Private Link eine Verbindung mit einem HDInsight-Cluster herstellen.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 5ed9587c0c8bdb378206db70ca459cc8cd004b45
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215557"
---
# <a name="enable-private-link-on-an-hdinsight-cluster"></a>Aktivieren von Private Link in einem HDInsight-Cluster

In diesem Artikel erfahren Sie, wie Sie Azure Private Link nutzen, um netzwerkübergreifend eine private Verbindung mit einem HDInsight-Cluster über das Microsoft-Backbonenetzwerk herzustellen. Dieser Artikel ist eine Erweiterung des Artikels [Einschränken der öffentlichen Clusterkonnektivität in Azure HDInsight](./hdinsight-restrict-public-connectivity.md), in dem es primär um die Einschränkung der öffentlichen Konnektivität geht. Falls öffentliche Konnektivität zwischen Ihren HDInsight-Clustern und abhängigen Ressourcen erwünscht ist, sollten Sie die Konnektivität des Clusters einschränken, indem Sie die Richtlinien zum [Steuern des Netzwerkdatenverkehrs in Azure HDInsight](./control-network-traffic.md) befolgen.

Private Link kann in netzwerkübergreifenden Szenarios genutzt werden, in denen das Peering virtueller Netzwerke nicht verfügbar oder aktiviert ist.

> [!NOTE]
> Das Einschränken der öffentlichen Konnektivität ist nicht das Gleiche wie Private Link, sondern eine Voraussetzung für dessen Aktivierung.

Die Verwendung von Private Link zum Herstellen einer Verbindung mit einem HDInsight-Cluster ist ein optionales Feature und standardmäßig deaktiviert. Das Feature ist nur verfügbar, wenn die Netzwerkeigenschaft `resourceProviderConnection` auf *ausgehend* festgelegt ist, wie im Artikel [Einschränken der Clusterkonnektivität in Azure HDInsight](./hdinsight-restrict-public-connectivity.md) beschrieben.

Wenn `privateLink` auf *Enabled* (Aktiviert) festgelegt ist, werden interne [Standard-Lastenausgleichsmodule](../load-balancer/load-balancer-overview.md) erstellt, und für jedes dieser Module wird ein Azure Private Link-Dienst bereitgestellt. Der Private Link-Dienst ermöglicht Ihnen den Zugriff auf den HDInsight-Cluster von privaten Endpunkten.

## <a name="prerequisites"></a>Voraussetzungen

Standard-Lastenausgleichsmodule stellen nicht automatisch eine [öffentliche Netzwerkadressenübersetzung in ausgehender Richtung](../load-balancer/load-balancer-outbound-connections.md) bereit, so wie es bei Basic-Lastenausgleichsmodulen der Fall ist. Sie müssen eine eigene NAT-Lösung bereitstellen, z. B. ein NAT-Gateway oder eine von Ihrer [Firewall](./hdinsight-restrict-outbound-traffic.md) bereitgestellte Adressübersetzung, um eine Verbindung mit ausgehenden öffentlichen HDInsight-Abhängigkeiten herzustellen. 

Ihr HDInsight-Cluster benötigt weiterhin Zugriff auf seine ausgehenden Abhängigkeiten. Falls diese ausgehenden Abhängigkeiten nicht zulässig sind, kann der Cluster ggf. nicht erstellt werden. 

### <a name="configure-a-default-network-security-group-on-the-subnet"></a>Konfigurieren einer Standard-Netzwerksicherheitsgruppe im Subnetz

Erstellen Sie eine Netzwerksicherheitsgruppe in dem Subnetz, in dem Sie den HDInsight-Cluster bereitstellen möchten, und fügen Sie die Gruppe hinzu. Eine NSG ist erforderlich, um ausgehende Verbindungen zu ermöglichen.

### <a name="disable-network-policies-for-the-private-link-service"></a>Deaktivieren von Netzwerkrichtlinien für den Private Link-Dienst

Für die erfolgreiche Erstellung von Private Link-Diensten müssen Sie explizit die [Richtlinien für Private Link-Dienste deaktivieren](../private-link/disable-private-link-service-network-policy.md).

### <a name="configure-a-nat-gateway-on-the-subnet"></a>Konfigurieren eines NAT-Gateways im Subnetz

Sie können ein NAT-Gateway verwenden, wenn Sie keine Firewall oder ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) für NAT konfigurieren möchten. Machen Sie andernfalls mit dem nächsten Schritt weiter.

Fügen Sie zum Einstieg ein NAT-Gateway (mit einer neuen öffentlichen IP-Adresse in Ihrem virtuellen Netzwerk) zum konfigurierten Subnetz Ihres virtuellen Netzwerks hinzu. Dieses Gateway ist für die Übersetzung Ihrer privaten internen IP-Adresse in öffentliche Adressen zuständig, wenn Datenverkehr Ihr virtuelles Netzwerk verlassen muss.

### <a name="configure-a-firewall-optional"></a>Konfigurieren einer Firewall (optional)
Ein grundlegendes Setup für die ersten Schritte:

1. Fügen Sie Ihrem virtuellen Netzwerk ein neues Subnetz mit dem Namen *AzureFirewallSubnet* hinzu. 
1. Verwenden Sie das neue Subnetz, um eine neue Firewall zu konfigurieren und Ihre Firewallrichtlinien hinzuzufügen. 
1. Verwenden Sie die private IP-Adresse der neuen Firewall als `nextHopIpAddress`-Wert in der Routentabelle. 
1. Fügen Sie diese Routingtabelle dem konfigurierten Subnetz Ihres virtuellen Netzwerks hinzu.

Weitere Informationen zum Einrichten einer Firewall finden Sie unter [Steuern des Netzwerkdatenverkehrs in Azure HDInsight](./control-network-traffic.md).

Das folgende Diagramm enthält ein Beispiel für die Netzwerkkonfiguration, die vor dem Erstellen eines Clusters erforderlich ist. In diesem Beispiel wird der gesamte ausgehende Datenverkehr für Azure Firewall über eine benutzerdefinierte Route erzwungen. Die erforderlichen ausgehenden Abhängigkeiten sollten vor der Clustererstellung in der Firewall zulässig sein. Bei Clustern mit Enterprise-Sicherheitspaket kann das Peering virtueller Netzwerke die Netzwerkkonnektivität mit Azure Active Directory Domain Services bereitstellen.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagramm: Private Link-Umgebung vor der Clustererstellung":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Verwalten von privaten Endpunkten für Azure HDInsight

Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) für Ihre Azure HDInsight-Cluster verwenden, um Clients in einem virtuellen Netzwerk sicheren Zugriff auf Ihren Cluster über [Private Link](../private-link/private-link-overview.md) zu ermöglichen. Der Netzwerkdatenverkehr zwischen den Clients im virtuellen Netzwerk und dem HDInsight-Cluster durchquert das Microsoft-Backbonenetzwerk, ohne in das öffentliche Internet zu gelangen.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="Diagramm der Verwaltungsoberfläche für private Endpunkte":::

Es gibt zwei Methoden zur Genehmigung von Verbindungen, aus denen ein Private Link-Dienstconsumer (z. B. Azure Data Factory) auswählen kann:

* **Automatisch**: Wenn der Dienstconsumer für die HDInsight-Ressource über Berechtigungen der rollenbasierten Zugriffssteuerung (RBAC) in Azure verfügt, kann der Consumer die automatische Genehmigungsmethode auswählen. In diesem Fall ist keine Aktion seitens der HDInsight-Ressource erforderlich, wenn die Anforderung diese Ressource erreicht, und die Verbindung wird automatisch genehmigt.
* **Manuell**: Der Dienstconsumer kann die manuelle Genehmigungsmethode auswählen, wenn der Consumer nicht über Azure RBAC-Berechtigungen für die HDInsight-Ressource verfügt. In diesem Fall wird die Verbindungsanforderung in den HDInsight-Ressourcen als **Ausstehend** angezeigt. Die HDInsight-Ressource muss die Anforderung manuell genehmigen, bevor Verbindungen hergestellt werden können. 

Wechseln Sie in Ihrer Clusteransicht im Azure-Portal zum Abschnitt **Netzwerk** unter **Sicherheit + Netzwerkbetrieb**, um private Endpunkte zu verwalten. Hier können Sie alle vorhandenen Verbindungen, Verbindungszustände und Details zu privaten Endpunkten anzeigen.

Sie können vorhandene Verbindungen auch genehmigen, ablehnen oder entfernen. Wenn Sie eine private Verbindung herstellen, können Sie angeben, mit welcher HDInsight-Unterressource (z. B. Gateway oder Hauptknoten) auch eine Verbindung hergestellt werden soll.

Die folgende Tabelle zeigt die verschiedenen HDInsight-Ressourcenaktionen und die resultierenden Verbindungszustände für private Endpunkte. Die HDInsight-Ressource kann den Verbindungszustand der privaten Endpunktverbindung auch zu einem späteren Zeitpunkt ohne Eingriff des Consumers ändern. Durch die Aktion wird der Zustand des Endpunkts auf der Consumerseite aktualisiert.

| Dienstanbieteraktion | Zustand des privaten Endpunkts des Dienstconsumers | BESCHREIBUNG |
| --------- | --------- | --------- |
| Keine | Ausstehend | Die Verbindung wird manuell erstellt, und die Genehmigung des Besitzers der Private Link-Ressource steht aus. |
| Genehmigen | Genehmigt | Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit. |
| Reject | Rejected (Abgelehnt) | Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt. |
| Remove (Entfernen) | Getrennt | Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt. Der private Endpunkt dient nur noch Informationszwecken und sollte zur Bereinigung gelöscht werden. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>Konfigurieren von DNS für die Verbindung über private Endpunkte

Nachdem Sie das Netzwerk eingerichtet haben, können Sie einen Cluster mit einer ausgehenden Ressourcenanbieterverbindung und Private Link-Aktivierung erstellen.

Für den Zugriff auf private Cluster können Sie Private Link-DNS-Erweiterungen und private Endpunkte verwenden. Wenn `privateLink` auf *aktiviert* festgelegt ist, können Sie private Endpunkte erstellen und die DNS-Auflösung über private DNS-Zonen konfigurieren.

In der von Azure verwalteten öffentlichen DNS-Zone `azurehdinsight.net` werden die folgenden Private Link-Einträge erstellt:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
Die folgende Abbildung zeigt ein Beispiel für die privaten DNS-Einträge, die für den Zugriff auf einen Cluster aus einem virtuellen Netzwerk konfiguriert werden, das weder per Peering verbunden ist noch über eine direkte Sichtverbindung mit dem Cluster verfügt. Sie können eine private Azure DNS-Zone verwenden, um vollqualifizierte Domänennamen (FQDNs) vom Typ `*.privatelink.azurehdinsight.net` außer Kraft zu setzen und IP-Adressen für private Endpunkte im Netzwerk des Clients aufzulösen. Die Konfiguration gilt nur für `<clustername>.azurehdinsight.net` im Beispiel, gilt aber auch für andere Clusterendpunkte.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagramm: Private Link-Architektur":::

## <a name="create-clusters"></a>Erstellen von Clustern

Der folgende JSON-Codeausschnitt enthält die beiden Netzwerkeigenschaften, die Sie in Ihrer Azure Resource Manager-Vorlage konfigurieren müssen, um einen privaten HDInsight-Cluster zu erstellen:

```json
networkProperties: {
    "resourceProviderConnection": "Outbound",
    "privateLink": "Enabled"
}
```

Eine vollständige Vorlage mit vielen dieser HDInsight-Sicherheitsfunktionen für Unternehmen, z. B. Private Link, finden Sie im Artikel zur [Enterprise-Sicherheitsvorlage für HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

Informationen zum Erstellen eines Clusters mithilfe von PowerShell finden Sie im [Beispiel](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

Informationen zum Erstellen eines Clusters mithilfe von Azure CLI finden Sie im [Beispiel](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Nächste Schritte

* [Enterprise-Sicherheitspaket für Azure HDInsight](enterprise-security-package.md)
* [Allgemeine Informationen und Richtlinien für die Unternehmenssicherheit in Azure HDInsight](./domain-joined/general-guidelines.md)