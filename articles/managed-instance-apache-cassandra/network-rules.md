---
title: Erforderliche ausgehende Netzwerkregeln für Azure Managed Instance for Apache Cassandra
description: Lernen Sie die erforderlichen ausgehenden Netzwerkregeln und vollqualifizierten Domänennamen (FQDN) für Azure Managed Instance for Apache Cassandra kennen.
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: chrande
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6d52f1c72765b3e7d3b7dd171c53c84e85138a20
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005231"
---
# <a name="required-outbound-network-rules"></a>Erforderliche Netzwerkregeln für ausgehenden Datenverkehr

Der Azure Managed Instance for Apache Casandra-Dienst erfordert bestimmte Netzwerkregeln, um den Dienst ordnungsgemäß verwalten zu können. Indem Sie sicherstellen, dass die richtigen Regeln verfügbar gemacht werden, können Sie Ihren Dienst schützen und Betriebsprobleme verhindern.

## <a name="virtual-network-service-tags"></a>Diensttags in virtuellen Netzwerken

Wenn Sie Azure Firewall zum Einschränken des ausgehenden Zugriffs verwenden, empfiehlt es sich dringend, [Diensttags in virtuellen Netzwerken](../virtual-network/service-tags-overview.md) zu verwenden. Im Folgenden sehen Sie die Tags, die erforderlich sind, damit Azure Managed Instance for Apache Cassandra ordnungsgemäß funktioniert.

| Zieldiensttag                                                             | Protocol | Port    | Zweck  |
|----------------------------------------------------------------------------------|----------|---------|------|
| Speicher | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Storage für die Kommunikation und Konfiguration der Steuerungsebene.|
| AzureKeyVault | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Key Vault. Zertifikate und Schlüssel werden verwendet, um die Kommunikation innerhalb des Clusters zu sichern.|
| EventHub | HTTPS | 443 | Erforderlich zum Weiterleiten von Protokollen an Azure. |
| AzureMonitor | HTTPS | 443 | Erforderlich zum Weiterleiten von Metriken an Azure. |
| AzureActiveDirectory| HTTPS | 443 | Erforderlich für die Azure Active Directory-Authentifizierung.|
| AzureResourceManager| HTTPS | 443 | Erforderlich, um Informationen zu Cassandra-Knoten zu sammeln und zu verwalten (z. B. Neustart).|
| AzureFrontDoor.Firstparty| HTTPS | 443 | Erforderlich für Protokollierungsvorgänge.|
| GuestAndHybridManagement | HTTPS | 443 |  Erforderlich, um Informationen zu Cassandra-Knoten zu sammeln und zu verwalten (z. B. Neustart). |
| ApiManagement  | HTTPS | 443 | Erforderlich, um Informationen zu Cassandra-Knoten zu sammeln und zu verwalten (z. B. Neustart). |

> [!NOTE]
> Zusätzlich zu den oben genannten müssen Sie auch die folgenden Adresspräfixe hinzufügen, da für den betreffenden Dienst kein Dienst-Tag vorhanden ist: 104.40.0.0/13 13.104.0.0/14 40.64.0.0/10

## <a name="user-defined-routes"></a>Benutzerdefinierte Routen

Wenn Sie eine Firewall eines Drittanbieters verwenden, um den ausgehenden Zugriff zu beschränken, empfehlen wir dringend, [benutzerdefinierte Routen (UDRs)](../virtual-network/virtual-networks-udr-overview.md#user-defined) für Microsoft-Adresspräfixe zu konfigurieren, anstatt zu versuchen, die Konnektivität durch Ihre eigene Firewall zu ermöglichen. Siehe Beispiel [bash-Skript](https://github.com/Azure-Samples/cassandra-managed-instance-tools/blob/main/configureUDR.sh) zum Hinzufügen der erforderlichen Adresspräfixe in benutzerdefinierte Routen.

## <a name="azure-global-required-network-rules"></a>Für Azure Global benötigte Netzwerkregeln

Folgende Netzwerkregeln und IP-Adressabhängigkeiten werden benötigt:

| Zielendpunkt                                                             | Protokoll | Port    | Zweck  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap\<region\>.blob.core.windows.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Storage | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Storage für die Kommunikation und Konfiguration der Steuerungsebene.|
|\*.store.core.windows.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Storage | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Storage für die Kommunikation und Konfiguration der Steuerungsebene.|
|\*.blob.core.windows.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Storage | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Storage zum Speichern von Sicherungen. *Das Sicherungsfeature (Backup) wird überarbeitet, und der Speichername wird ein Muster der allgemeinen Verfügbarkeit (GA) einhalten.*|
|vmc-p-\<region\>.vault.azure.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure KeyVault | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Key Vault. Zertifikate und Schlüssel werden verwendet, um die Kommunikation innerhalb des Clusters zu sichern.|
|management.azure.com:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Virtual Machine Scale Sets/Azure Management API | HTTPS | 443 | Erforderlich, um Informationen zu Cassandra-Knoten zu sammeln und zu verwalten (z. B. Neustart).|
|\*.servicebus.windows.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure EventHub | HTTPS | 443 | Erforderlich zum Weiterleiten von Protokollen an Azure.|
|jarvis-west.dc.ad.msft.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Monitor | HTTPS | 443 | Erforderlich zum Weiterleiten von Metriken an Azure. |
|login.microsoftonline.com:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure AD | HTTPS | 443 | Erforderlich für die Azure Active Directory-Authentifizierung.|
| packages.microsoft.com | HTTPS | 443 | Erforderlich für Updates der Definition und Signaturen der Azure-Sicherheitsscanner. |
| azure.microsoft.com | HTTPS | 443 | Erforderlich, um Informationen zu VM-Skalierungsgruppen abzurufen. |
| \<region\>-dsms.dsms.core.windows.net | HTTPS | 443 | Zertifikat für die Protokollierung. |
| gcs.prod.monitoring.core.windows.net | HTTPS | 443 | Für die Protokollierung erforderlicher Protokollierungsendpunkt. |
| global.prod.microsoftmetrics.com | HTTPS | 443 | Erforderlich für Metriken. |
| shavsalinuxscanpkg.blob.core.windows.net | HTTPS | 443 | Erforderlich zum Herunterladen/Aktualisieren der Sicherheitsscanner. |
| crl.microsoft.com | HTTPS | 443 | Erforderlich für den Zugriff auf öffentliche Microsoft-Zertifikate. |
| global-dsms.dsms.core.windows.net | HTTPS | 443 | Erforderlich für den Zugriff auf öffentliche Microsoft-Zertifikate. |

### <a name="dns-access"></a>DNS-Zugriff

Das System verwendet DNS-Namen, um die in diesem Artikel beschriebenen Azure-Dienste zu erreichen, sodass es Lastenausgleiche verwenden kann. Daher muss das virtuelle Netzwerk einen DNS-Server ausführen, der diese Adressen auflösen kann. Die virtuellen Computer im virtuellen Netzwerk beachten den Namensserver, der über das DHCP-Protokoll mitgeteilt wird. In den meisten Fällen richtet Azure automatisch einen DNS-Server für das virtuelle Netzwerk ein. Wenn dies in Ihrem Szenario nicht der Fall ist, sind die in diesem Artikel beschriebenen DNS-Namen ein guter Leitfaden für die ersten Schritte.

## <a name="internal-port-usage"></a>Verwendung interner Ports

Auf die folgenden Ports kann nur innerhalb des VNET (oder gepeerte vnets./ExpressRoutes) zugegriffen werden. Managed Instance for Apache Cassandra-Instanzen verfügen über keine öffentliche IP-Adresse und sollten nicht über das Internet zugänglich gemacht werden.

| Port | Zweck |
| ---- | --- |
| 8443 | Intern |
| 9443 | Intern |
| 7001 | Gossip: Wird von Cassandra-Knoten für die gegenseitige Kommunikation verwendet. |
| 9042 | Cassandra: Wird von Clients für Verbindungen mit Cassandra verwendet. |
| 7199 | Intern |

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Netzwerkregeln zum ordnungsgemäßen Verwalten des Diensts kennen gelernt. Weitere Informationen zu Azure Managed Instance for Apache Cassandra finden Sie in den folgenden Artikeln:

* [Was ist Azure Managed Instance for Apache Cassandra? (Vorschau)](introduction.md)
* [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
