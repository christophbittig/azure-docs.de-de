---
title: Verwenden von Azure Firewall zum Schützen von AKS-Bereitstellungen (Azure Kubernetes Service)
description: Erfahren Sie, wie Sie Azure Firewall verwenden, um AKS-Bereitstellungen (Azure Kubernetes Service) zu schützen.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 08/03/2021
ms.author: victorh
ms.openlocfilehash: b331180e40d1baf92a3c1408f3e003a257fa114a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339593"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Verwenden von Azure Firewall zum Schützen von AKS-Bereitstellungen (Azure Kubernetes Service)

Azure Kubernetes Service (AKS) bietet einen verwalteten Kubernetes-Cluster in Azure. Er verringert die Komplexität und den operativen Mehraufwand für die Kubernetes-Verwaltung, indem ein Großteil dieser Verantwortung an Azure übertragen wird. AKS übernimmt für Sie wichtige Aufgaben, etwa die Überwachung und Verwaltung der Integrität, und stellt einen unternehmensweiten und sicheren Cluster unter Berücksichtigung von Governance bereit.

Kubernetes orchestriert Cluster von virtuellen Computern und plant die Ausführung von Containern auf diesen virtuellen Computern basierend auf den verfügbaren Computeressourcen und dem Ressourcenbedarf der einzelnen Container. Container werden in Pods gruppiert, der grundlegenden Betriebseinheit für Kubernetes, und werden dann in den von Ihnen gewünschten Zustand skaliert.

Zu Verwaltungs- und Betriebszwecken müssen Knoten in einem AKS-Cluster auf bestimmte Ports und vollqualifizierte Domänennamen (FQDNs) zugreifen. Mögliche Aktionen sind die Kommunikation mit dem API-Server oder das Herunterladen und anschließende Installieren von Kernkomponenten des Kubernetes-Clusters und Sicherheitsupdates für Knoten. Mithilfe von Azure Firewall können Sie Ihre Umgebung sperren und ausgehenden Datenverkehr filtern.

Eine Übersicht finden Sie im folgenden Video von Jorge Cortes:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWIcAo]

Beachten Sie die Richtlinien in diesem Artikel, um mithilfe von Azure Firewall zusätzlichen Schutz für Ihren Azure Kubernetes-Cluster bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein bereitgestellter Azure Kubernetes-Cluster mit aktuell ausgeführter Anwendung.

   Weitere Informationen finden Sie im [Tutorial: Bereitstellen eines Azure Kubernetes Service-Clusters (AKS)](../aks/tutorial-kubernetes-deploy-cluster.md) und [Tutorial: Ausführen von Anwendungen in Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-deploy-application.md).


## <a name="securing-aks"></a>Sichern von AKS

Azure Firewall bietet ein AKS FQDN-Tag, um die Konfiguration zu vereinfachen. Führen Sie die folgenden Schritte aus, um ausgehenden Plattformdatenverkehr für AKS zuzulassen:

- Wenn Sie Azure Firewall verwenden, um ausgehenden Datenverkehr einzuschränken, und eine benutzerdefinierte Route (UDR) erstellen, um den gesamten ausgehenden Datenverkehr weiterzuleiten, stellen Sie sicher, dass Sie in der Firewall eine entsprechende DNAT-Regel erstellen, um eingehenden Datenverkehr ordnungsgemäß zuzulassen. 

   Durch die Verwendung von Azure Firewall mit einer UDR wird das Setup für eingehenden Datenverkehr aufgrund von asymmetrischem Routing unterbrochen. Das Problem tritt auf, wenn das AKS-Subnetz über eine Standardroute verfügt, die zur privaten IP-Adresse der Firewall wechselt, Sie aber einen öffentlichen Load Balancer verwenden. Beispielsweise Eingangs- oder Kubernetes-Dienst vom Typ *LoadBalancer*.

   In diesem Fall wird der eingehende Load Balancer-Datenverkehr über die öffentliche IP-Adresse empfangen, während der Rückpfad über die private IP-Adresse der Firewall verläuft. Die Firewall löscht das zurückgegebene Paket, weil sie zustandsbehaftet ist und nichts über eine vorhandene Sitzung weiß. Informationen zur Integration von Azure Firewall in ihren Eingangs- oder Service Dienst-Load Balancer finden Sie unter [Integrieren von Azure Firewall mit Azure Load Balancer Standard](integrate-lb.md).
- Erstellen Sie eine Anwendungsregelsammlung, und fügen Sie eine Regel hinzu, um das FQDN-Tag *AzureKubernetesService* zu aktivieren. Der Quell-IP-Adressbereich ist das virtuelle Netzwerk des Hostpools, das Protokoll ist HTTPS, und das Ziel ist AzureKubernetesService.
- Die folgenden ausgehenden Ports und Netzwerkregeln sind für einen AKS-Cluster erforderlich:

   - TCP-Port 443
   - TCP [*IPAddrOfYourAPIServer*]:443 ist erforderlich, wenn Sie über eine App verfügen, die mit dem API-Server kommunizieren muss. Diese Änderung kann festgelegt werden, nachdem der Cluster erstellt wurde.
   - Der TCP-Port 9000 und der UDP-Port 1194 für die Kommunikation zwischen dem Tunnelfrontpod und dem Tunnelende auf dem API-Server.

      Genauere Informationen können Sie den Adressen in der folgenden Tabelle entnehmen:

   | Zielendpunkt                                                             | Protokoll | Port    | Zweck  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Oder* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1.194      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene |
   | **`*:9000`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Oder* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene |

   - UDP-Port 123 für die NTP-Zeitsynchronisierung (Network Time Protocol) (Linux-Knoten).
   - UDP-Port 53 für DNS ist ebenfalls erforderlich, wenn Sie über Pods verfügen, die direkt auf den API-Server zugreifen.

   Weitere Informationen finden Sie unter [Steuern des ausgehenden Datenverkehrs für Clusterknoten in Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md).
- Konfigurieren Sie die AzureMonitor- und Storage-Diensttags. Azure Monitor empfängt Log Analytics-Daten.

   Sie können Ihre Arbeitsbereich-URL auch einzeln zulassen: `<worksapceguid>.ods.opinsights.azure.com`und `<worksapceguid>.oms.opinsights.azure.com`. Sie haben die folgenden Möglichkeiten, um dies zu ändern:

    - Gestatten Sie den HTTPS-Zugriff aus Ihrem Hostpoolsubnetz auf `*. ods.opinsights.azure.com` und `*.oms. opinsights.azure.com`. Diese Platzhalter-FQDNs ermöglichen den erforderlichen Zugriff, sind aber weniger restriktiv.
    - Verwenden Sie die folgende Log Analytics-Abfrage, um die erforderlichen FQDNs aufzulisten, und lassen Sie sie dann explizit in den Firewallanwendungsregeln zu:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Kubernetes Service finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)](../aks/concepts-clusters-workloads.md).
