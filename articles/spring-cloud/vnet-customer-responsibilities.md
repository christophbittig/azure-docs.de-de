---
title: Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNET
description: In diesem Artikel werden die Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNET beschrieben.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: devx-track-java
ms.openlocfilehash: 3c21d89329db89b73a0e37046062eae876084e2b
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399766"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNET

In diesem Artikel finden Sie Spezifikationen für die Verwendung von Azure Spring Cloud in einem virtuellen Netzwerk.

Wenn Azure Spring Cloud in Ihrem virtuellen Netzwerk bereitgestellt wird, verfügt der Dienst über ausgehende Abhängigkeiten von Diensten außerhalb des virtuellen Netzwerks. Zu Verwaltungs- und Betriebszwecken muss Azure Spring Cloud auf bestimmte Ports und vollqualifizierte Domänennamen (FQDNs) zugreifen. Azure Spring Cloud benötigt diese Endpunkte für die Kommunikation mit der Verwaltungsebene und zum Herunterladen und Installieren von Kernkomponenten des Kubernetes-Clusters und Sicherheitsupdates.

Standardmäßig verfügt Azure Spring Cloud über uneingeschränkten Internetzugriff in ausgehender Richtung. Diese Ebene des Netzwerkzugriffs ermöglicht es, dass ausgeführte Anwendungen je nach Bedarf auf externe Ressourcen zugreifen können. Wenn Sie den ausgehenden Datenverkehr einschränken möchten, muss eine begrenzte Anzahl von Ports und Adressen für Wartungsaufgaben zugänglich sein. Die einfachste Lösung zum Schützen ausgehender Adressen besteht in der Verwendung eines Firewallgeräts, das den ausgehenden Datenverkehr auf der Grundlage von Domänennamen kontrolliert. Von Azure Firewall kann beispielsweise ausgehender HTTP- und HTTPS-Datenverkehr auf der Grundlage des FQDN des Ziels eingeschränkt werden. Darüber hinaus können Sie Ihre bevorzugten Firewall- und Sicherheitsregeln konfigurieren, um diese erforderlichen Ports und Adressen zuzulassen.

## <a name="azure-spring-cloud-resource-requirements"></a>Anforderungen für Azure Spring Cloud-Ressourcen

Die folgende Liste enthält die Ressourcenanforderungen für Azure Spring Cloud-Dienste. Eine allgemeine Anforderung besteht darin, dass keine Ressourcengruppen geändert werden sollten, die von Azure Spring Cloud und den zugrunde liegenden Netzwerkressourcen erstellt wurden.

- Ändern Sie keine Ressourcengruppen, die vom Azure Spring Cloud-Dienst erstellt werden und sich in dessen Besitz befinden.
  - Standardmäßig werden diese Ressourcengruppen mit `ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]*` und `ap_[SERVICE-INSTANCE-NAME]_[REGION]*` bezeichnet.
  - Hindern Sie Azure Spring Cloud nicht am Aktualisieren von Ressourcen in diesen Ressourcengruppen.
- Ändern Sie keine Subnetze, die von Azure Spring Cloud verwendet werden.
- Erstellen Sie im selben Subnetz nicht mehr als eine Instanz des Azure Spring Cloud-Diensts.
- Bei Verwendung einer Firewall zum Steuern des Datenverkehrs sollten Sie nicht den folgenden ausgehenden Datenverkehr für Azure Spring Cloud-Komponenten blockieren, mit denen die Dienstinstanz betrieben, verwaltet und unterstützt wird.

## <a name="azure-spring-cloud-network-requirements"></a>Netzwerkanforderungen für Azure Spring Cloud

| Zielendpunkt                                         | Port             | Zweck                                       | Hinweis                                                         |
| ------------------------------------------------------------ | ---------------- | ----------------------------------------- | ------------------------------------------------------------ |
| \*:1194 *oder* [Diensttag](../virtual-network/service-tags-overview.md#available-service-tags) AzureCloud:1194 | UDP:1194         | Verwaltung des zugrunde liegenden Kubernetes-Clusters |                                                              |
| \*:443 *oder* [Diensttag](../virtual-network/service-tags-overview.md#available-service-tags) AzureCloud:443 | TCP:443          | Azure Spring Cloud-Dienstverwaltung.    | Informationen zur Dienstinstanz „requiredTraffics“ sind in den Ressourcennutzdaten ggf. bekannt (Abschnitt „networkProfile“). |
| \*:9000 *oder* [Diensttag](../virtual-network/service-tags-overview.md#available-service-tags) AzureCloud:9000 | TCP:9000         | Verwaltung des zugrunde liegenden Kubernetes-Clusters |                                                              |
| \*:123 *oder* ntp.ubuntu.com:123                                | UDP:123          | NTP-Zeitsynchronisierung auf Linux-Knoten  |                                                              |
| \*.azure.io:443 *oder* [Diensttag](../virtual-network/service-tags-overview.md#available-service-tags) AzureContainerRegistry:443 | TCP:443          | Azure Container Registry.                 | Kann ersetzt werden, indem der *Azure Container Registry*-[Dienstendpunkt im virtuellen Netzwerk](../virtual-network/virtual-network-service-endpoints-overview.md) aktiviert wird. |
| \*.core.windows.net:443 und \*.core.windows.net:445 *oder* [Diensttag](../virtual-network/service-tags-overview.md#available-service-tags) Storage:443 und Storage:445 | TCP:443, TCP:445 | Azure Files                        | Kann ersetzt werden, indem der *Azure Storage*-[Dienstendpunkt im virtuellen Netzwerk](../virtual-network/virtual-network-service-endpoints-overview.md) aktiviert wird. |
| \*.servicebus.windows.net:443 *oder* [Diensttag](../virtual-network/service-tags-overview.md#available-service-tags) EventHub:443 | TCP:443          | Azure Event Hub                          | Kann ersetzt werden, indem der *Azure Event Hubs*-[Dienstendpunkt im virtuellen Netzwerk](../virtual-network/virtual-network-service-endpoints-overview.md) aktiviert wird. |

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>FQDN-Anforderungen/Anwendungsregeln für Azure Spring Cloud

Von Azure Firewall wird das FQDN-Tag **AzureKubernetesService** bereitgestellt, um die folgenden Konfigurationen zu vereinfachen:

| Ziel-FQDN                  | Port      | Zweck                                                          |
| --------------------------------- | --------- | ------------------------------------------------------------ |
| <i>*.azmk8s.io</i>                | HTTPS: 443 | Verwaltung des zugrunde liegenden Kubernetes-Clusters                    |
| <i>mcr.microsoft.com</i>          | HTTPS: 443 | Microsoft Container Registry (MCR)                          |
| <i>*.cdn.mscr.io</i>              | HTTPS: 443 | MCR-Speicher, der auf Azure CDN basiert                         |
| <i>*.data.mcr.microsoft.com</i>   | HTTPS: 443 | MCR-Speicher, der auf Azure CDN basiert                         |
| <i>management.azure.com</i>       | HTTPS: 443 | Verwaltung des zugrunde liegenden Kubernetes-Clusters                    |
| <i>*login.microsoftonline.com</i> | HTTPS: 443 | Azure Active Directory-Authentifizierung                       |
| <i>*login.microsoft.com</i>       | HTTPS: 443 | Azure Active Directory-Authentifizierung                       |
| <i>packages.microsoft.com</i>     | HTTPS: 443 | Microsoft-Paketrepository                               |
| <i>acs-mirror.azureedge.net</i>   | HTTPS: 443 | Repository, das zum Installieren erforderlicher Binärdateien, z. B. für kubenet und Azure CNI, benötigt wird. |
| *mscrl.microsoft.com*<sup>1</sup> | HTTPS:80  | Pfade zu erforderlichen Microsoft-Zertifikatketten.                  |
| *crl.microsoft.com*<sup>1</sup>   | HTTPS:80  | Pfade zu erforderlichen Microsoft-Zertifikatketten.                  |
| *crl3.digicert.com*<sup>1</sup>   | HTTPS:80  | Pfade zu TLS/SSL-Zertifikatketten von Drittanbietern                 |

<sup>1</sup> Beachten Sie, dass diese FQDNs nicht im FQDN-Tag enthalten sind.


## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Optionaler Azure Spring Cloud-FQDN für die Leistungsverwaltung von Drittanbieteranwendungen

Von Azure Firewall wird das FQDN-Tag **AzureKubernetesService** bereitgestellt, um die folgenden Konfigurationen zu vereinfachen:

| Ziel-FQDN                   | Port       | Zweck                                                          |
| ---------------------------------- | ---------- | ------------------------------------------------------------ |
| <i>collector*.newrelic.com</i>     | TCP:443/80 | Die benötigten Netzwerke von New Relic APM-Agents aus der USA-Region finden Sie auch unter [APM Agents Networks](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
| <i>collector*.eu01.nr-data.net</i> | TCP:443/80 | Die benötigten Netzwerke von New Relic APM-Agents aus der EU-Region finden Sie auch unter [APM Agents Networks](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
| <i>*.live.dynatrace.com</i>        | TCP:443    | Erforderliches Netzwerk von Dynatrace APM-Agents.                    |
| <i>*.live.ruxit.com</i>            | TCP:443    | Erforderliches Netzwerk von Dynatrace APM-Agents.                    |
| <i>*.saas.appdynamics.com</i>      | TCP:443/80 | Erforderliches Netzwerk von AppDynamics-APM-Agents. Weitere Informationen finden Sie unter [SaaS-Domänen und IP-Adressbereiche](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges). |

## <a name="see-also"></a>Siehe auch

* [Zugreifen auf eine Anwendung im privaten Netzwerk](access-app-virtual-network.md)
* [Anwendungen mit Hilfe von Application Gateway für das Internet freigeben](expose-apps-gateway.md)
