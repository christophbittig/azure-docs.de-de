---
title: Beheben von Netzwerkproblemen mit der Registrierung
description: Enthält eine Beschreibung der Symptome, Ursachen und Lösungen häufiger Probleme, die beim Zugreifen auf eine Azure-Containerregistrierung in einem virtuellen Netzwerk oder hinter einer Firewall auftreten.
ms.topic: article
ms.date: 05/10/2021
ms.openlocfilehash: 4d3962a99fd462cfe3b613a4f0a9409b309b462f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287132"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Beheben von Netzwerkproblemen mit der Registrierung

Dieser Artikel hilft Ihnen bei der Fehlerbehebung von Problemen, die beim Zugriff auf eine Azure-Container-Registrierung in einem virtuellen Netzwerk oder hinter einer Firewall oder einem Proxy-Server auftreten können. 

## <a name="symptoms"></a>Symptome

Beispiele für Symptome sind:

* Images können nicht gepusht oder gepullt werden, und Sie erhalten den Fehler `dial tcp: lookup myregistry.azurecr.io`.
* Images können nicht gepusht oder gepullt werden, und Sie erhalten den Fehler `Client.Timeout exceeded while awaiting headers`.
* Images können nicht gepusht oder gepullt werden, und Sie erhalten den Azure CLI-Fehler `Could not connect to the registry login server`.
* Images können nicht aus der Registrierung in Azure Kubernetes Service oder einen anderen Azure-Dienst gepullt werden.
* Auf eine Registrierung hinter einem HTTPS-Proxy kann nicht zugegriffen werden, und Sie erhalten den Fehler `Error response from daemon: login attempt failed with status: 403 Forbidden` oder `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* Die Einstellungen für das virtuelle Netzwerk können nicht konfiguriert werden, und die Fehlermeldung `Failed to save firewall and virtual network settings for container registry` wird angezeigt.
* Das Zugreifen auf oder das Anzeigen von Registrierungseinstellungen im Azure-Portal oder das Verwalten der Registrierung mithilfe der Azure CLI ist nicht möglich.
* Einstellungen für das virtuelle Netzwerk oder Regeln für den öffentlichen Zugriff können nicht hinzugefügt oder geändert werden.
* Mit ACR Tasks können keine Images gepusht oder gepullt werden.
* Microsoft Defender für Cloud kann keine Images in der Registrierung überprüfen oder Überprüfungsergebnisse werden in Microsoft Defender für Cloud nicht angezeigt.
* Sie erhalten einen `host is not reachable`-Fehler, wenn Sie versuchen, auf eine Registrierung zuzugreifen, die mit einem privaten Endpunkt konfiguriert ist.

## <a name="causes"></a>Ursachen

* Eine Clientfirewall oder ein Proxy verhindert den Zugriff ([Lösung](#configure-client-firewall-access)).
* Regeln für den Zugriff auf das öffentliche Netzwerk in der Registrierung verhindern den Zugriff ([Lösung](#configure-public-access-to-registry)).
* Die Konfiguration des virtuellen Netzwerks oder privaten Endpunkts verhindert den Zugriff ([Lösung](#configure-vnet-access)).
* Sie versuchen, Microsoft Defender für Cloud oder bestimmte andere Azure-Dienste in eine Registrierung zu integrieren, die über einen privaten Endpunkt, Dienstendpunkte oder Zugriffsregeln für öffentliche IP-Adressen verfügt - [Lösung](#configure-service-access).

## <a name="further-diagnosis"></a>Weitere Diagnose 

Führen Sie den Befehl [az acr check-health](/cli/azure/acr#az_acr_check_health) aus, um weitere Informationen zur Integrität der Registrierungsumgebung abzurufen und optional Zugriff auf eine Zielregistrierung zu erhalten. Diagnostizieren Sie beispielsweise bestimmte Probleme mit der Netzwerkkonnektivität oder -konfiguration. 

Befehlsbeispiele finden Sie unter [Überprüfen der Integrität einer Azure-Containerregistrierung](container-registry-check-health.md). Wenn Fehler gemeldet werden, überprüfen Sie die [Fehlerreferenz](container-registry-health-error-reference.md) und die folgenden Abschnitte für empfohlene Lösungen.

Wenn Sie Probleme bei der Verwendung eines Azure-Kubernetes-Dienstes mit integrierter Registry haben, führen Sie den Befehl [az aks check-acrr](/cli/azure/aks#az_aks_check_acr) aus, um zu überprüfen, ob der AKS-Cluster die Registry erreichen kann.

> [!NOTE]
> Einige Symptome in Bezug auf die Netzwerkkonnektivität können auch auftreten, wenn es Probleme mit der Authentifizierung oder Autorisierung der Registrierung gibt. Weitere Informationen finden Sie unter [Beheben von Problemen mit der Registrierungsanmeldung](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Mögliche Lösungen

### <a name="configure-client-firewall-access"></a>Konfigurieren des Zugriffs über die Clientfirewall

Sie müssen die Firewallregeln für den Zugriff auf die öffentlichen REST- und Datenendpunkte der Registrierung konfigurieren, um den Zugriff auf eine Registrierung von einem Ort hinter einer Clientfirewall oder einem Proxyserver zu ermöglichen. Wenn [dedizierte Datenendpunkte](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) aktiviert sind, benötigen Sie Regeln für den Zugriff auf die folgenden Komponenten:

* REST-Endpunkt: `<registryname>.azurecr.io`
* Datenendpunkt(e): `<registry-name>.<region>.data.azurecr.io`

Konfigurieren Sie für eine Registrierung mit Georeplikation den Zugriff auf den Datenendpunkt für jedes regionale Replikat.

Stellen Sie hinter einem HTTPS-Proxy sicher, dass sowohl Ihr Docker-Client als auch Ihr Docker-Daemon für das Proxyverhalten konfiguriert sind. Wenn Sie die Proxy Einstellungen für den Locker-Daemon ändern, stellen Sie sicher, dass Sie den Daemon neu starten. 

Registrierungsressourcenprotokolle in der Tabelle „ContainerRegistryLoginEvents“ können als Hilfe beim Diagnostizieren einer blockierten Verbindungsherstellung dienen.

Verwandte Links:

* [Konfigurieren von Regeln für den Zugriff auf eine Azure-Containerregistrierung von einem Standort hinter einer Firewall](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS-Proxykonfiguration](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Georeplikation in Azure Container Registry](container-registry-geo-replication.md)
* [Überwachen von Azure Container Registry](monitor-service.md)

### <a name="configure-public-access-to-registry"></a>Konfigurieren des öffentlichen Zugriffs auf die Registrierung

Wenn Sie über das Internet auf eine Registrierung zugreifen, sollten Sie sich vergewissern, dass für die Registrierung der Zugriff auf das öffentliche Netzwerk über Ihren Client zulässig ist. Standardmäßig ist für eine Azure Container Registry-Instanz der Zugriff auf die öffentlichen Registrierungsendpunkte aus allen Netzwerken zugelassen. Eine Registrierung kann den Zugriff auf ausgewählte Netzwerke oder IP-Adressen beschränken. 

Falls die Registrierung für ein virtuelles Netzwerk mit einem Dienstendpunkt konfiguriert ist, wird mit dem Deaktivieren des öffentlichen Netzwerkzugriffs auch der Zugriff über den Dienstendpunkt deaktiviert. Wenn Ihre Registrierung für ein virtuelles Netzwerk mit Private Link konfiguriert ist, gelten IP-Netzwerkregeln nicht für die privaten Endpunkte der Registrierung. 

Verwandte Links:

* [Konfigurieren von Netzwerkregeln für öffentliche IP-Adressen](container-registry-access-selected-networks.md)
* [Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link](container-registry-private-link.md)
* [Beschränken des Zugriffs auf eine Containerregistrierung mithilfe eines Dienstendpunkts in einem virtuellen Azure-Netzwerk](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Konfigurieren des VNET-Zugriffs

Vergewissern Sie sich, dass für das virtuelle Netzwerk entweder ein privater Endpunkt für Private Link oder ein Dienstendpunkt (Vorschau) konfiguriert ist. Ein Azure Bastion-Endpunkt wird derzeit nicht unterstützt.

Wenn ein privater Endpunkt konfiguriert ist, vergewissern Sie sich, dass das DNS den öffentlichen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) der Registrierung (z. B. *myregistry.azurecr.io*) in die private IP-Adresse der Registrierung auflöst.

  * Führen Sie den Befehl [az acr check-health](/cli/azure/acr#az_acr_check_health) mit dem Parameter `--vnet` aus, um das DNS-Routing an den privaten Endpunkt im virtuellen Netzwerk sicherzustellen.
  * Verwenden Sie für das DNS-Lookup ein Netzwerkhilfsprogramm, z. B. `dig` oder `nslookup`. 
  * Stellen Sie sicher, dass [DNS-Einträge](container-registry-private-link.md#dns-configuration-options) für den Registrierungs-FQDN und für jeden der Datenendpunkt-FQDNs konfiguriert sind. 

Überprüfen Sie die NSG-Regeln und die Diensttags, mit denen der Datenverkehr von anderen Ressourcen im Netzwerk zur Registrierung eingeschränkt wird. 

Wenn ein Dienstendpunkt für die Registrierung konfiguriert ist, sollten Sie sich vergewissern, dass der Registrierung eine Netzwerkregel hinzugefügt wird, die den Zugriff über dieses Netzwerksubnetz zulässt. Der Dienstendpunkt unterstützt nur den Zugriff über virtuelle Computer und AKS-Cluster im Netzwerk.

Wenn Sie den Registrierungszugriff mithilfe eines virtuellen Netzwerks in einem anderen Azure-Abonnement einschränken möchten, stellen Sie sicher, dass Sie den Ressourcenanbieter `Microsoft.ContainerRegistry` in diesem Abonnement registrieren. [Registrieren Sie den Ressourcenanbieter](../azure-resource-manager/management/resource-providers-and-types.md) für Azure Container Registry über das Azure-Portal, die Azure CLI oder andere Azure-Tools.

Wenn im Netzwerk Azure Firewall oder eine ähnliche Lösung konfiguriert ist, sollten Sie überprüfen, ob der ausgehende Datenverkehr von anderen Ressourcen, z. B. einem AKS-Cluster, möglich ist, damit die Registrierungsendpunkte erreicht werden können.

Verwandte Links:

* [Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link](container-registry-private-link.md)
* [Behandeln von Problemen mit der Konnektivität privater Azure-Endpunkte](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [Beschränken des Zugriffs auf eine Containerregistrierung mithilfe eines Dienstendpunkts in einem virtuellen Azure-Netzwerk](container-registry-vnet.md)
* [Erforderliche Netzwerkregeln für ausgehenden Datenverkehr und FQDNs für AKS-Cluster](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: Debuggen der DNS-Auflösung](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Diensttags für virtuelle Netzwerke](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Konfigurieren des Dienstzugriffs

Derzeit ist der Zugriff auf eine Containerregistrierung mit Netzwerkeinschränkungen von mehreren Azure-Diensten aus nicht zulässig:

* Microsoft Defender für Cloud kann keine [Überprüfung von Images auf Sicherheitsrisiken](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) in einer Registrierung durchführen, die den Zugriff auf private Endpunkte, ausgewählte Subnetze oder IP-Adressen beschränkt. 
* Ressourcen bestimmter Azure-Dienste, einschließlich Azure App Service und Azure Container Instances, können nicht auf eine Containerregistrierung mit Netzwerkeinschränkung zugreifen.

Wenn Zugriff oder eine Integration dieser Azure-Dienste mit Ihrer Containerregistrierung erforderlich ist, entfernen Sie die Netzwerkbeschränkung. Entfernen Sie z. B. die privaten Endpunkte der Registrierung, oder entfernen oder ändern Sie die Regeln der Registrierung für öffentlichen Zugriff.

Seit Januar 2021 können Sie eine Registrierung mit Netzwerkeinschränkung so konfigurieren, dass der Zugriff von ausgewählten vertrauenswürdigen Diensten [zugelassen](allow-access-trusted-services.md) wird.

Verwandte Links:

* [Die Imageüberprüfung von Azure Container Registry durch Microsoft Defender für Containerregister](../security-center/defender-for-container-registries-introduction.md)
* [Feedback](https://feedback.azure.com/d365community/idea/cbe6351a-0525-ec11-b6e6-000d3a4f07b8)
* [Zulassen des sicheren Zugriffs vertrauenswürdiger Dienste auf eine Containerregistrierung mit Netzwerkeinschränkungen](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Erweiterte Problembehandlung

Wenn [Sammlung von Ressourcenprotokollen](monitor-service.md) in der Registrierung aktiviert ist, überprüfen Sie das Protokoll „ContainterRegistryLoginEvents“. In diesem Protokoll werden Registrierungauthentifizierungsereignisse und der -status gespeichert, einschließlich der eingehenden Identität und IP-Adresse. Fragen Sie das Protokoll nach [Authentifizierungsfehlern für die Registrierung](monitor-service.md#registry-authentication-failures) ab. 

Verwandte Links:

* [Protokolle für die Diagnoseauswertung und -überwachung](./monitor-service.md)
* [Häufig gestellte Fragen (FAQ) zur Containerregistrierung](container-registry-faq.yml)
* [Azure-Sicherheitsbaseline für Azure Container Registry](security-baseline.md)
* [Bewährte Methoden für Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Ihr Problem hier nicht lösen können, sehen Sie sich die folgenden Optionen an.

* Weitere Themen zur Problembehandlung der Registrierung sind:
  * [Beheben von Problemen mit der Registrierungsanmeldung](container-registry-troubleshoot-login.md) 
  * [Beheben von Problemen mit der Registrierungsleistung](container-registry-troubleshoot-performance.md)
* [Optionen für Support durch die Community](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A (Fragen und Antworten)](/answers/products/)
* [Öffnen eines Supporttickets](https://azure.microsoft.com/support/create-ticket/)
