---
title: Erforderliche ausgehende Netzwerkregeln für Azure Managed Instance for Apache Cassandra
description: Lernen Sie die erforderlichen ausgehenden Netzwerkregeln und vollqualifizierten Domänennamen (FQDN) für Azure Managed Instance for Apache Cassandra kennen.
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 05/21/2021
ms.author: chrande
ms.openlocfilehash: 7b9e7463811d4bd5cd092828759487557bab50b0
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122356232"
---
# <a name="required-outbound-network-rules"></a>Erforderliche Netzwerkregeln für ausgehenden Datenverkehr

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra befindet sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
| GuestAndHybridManagement | HTTPS | 443 |  Erforderlich, um Informationen zu Cassandra-Knoten zu sammeln und zu verwalten (z. B. Neustart). |
| ApiManagement  | HTTPS | 443 | Erforderlich, um Informationen zu Cassandra-Knoten zu sammeln und zu verwalten (z. B. Neustart). |
| `Storage.<Region>`  | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Storage für die Kommunikation und Konfiguration der Steuerungsebene. **Sie benötigen einen Eintrag für jede Region, in der Sie ein Rechenzentrum bereitgestellt haben.** |


## <a name="azure-global-required-network-rules"></a>Für Azure Global benötigte Netzwerkregeln

Wenn Sie Azure Firewall nicht verwenden, werden folgende Netzwerkregeln und IP-Adressabhängigkeiten benötigt:

| Zielendpunkt                                                             | Protokoll | Port    | Zweck  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap`<region>`.blob.core.windows.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Storage | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Storage für die Kommunikation und Konfiguration der Steuerungsebene.|
|*.store.core.windows.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Storage | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Storage für die Kommunikation und Konfiguration der Steuerungsebene.|
|*.blob.core.windows.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Storage | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Storage zum Speichern von Sicherungen. *Das Sicherungsfeature (Backup) wird überarbeitet, und der Speichername wird ein Muster der allgemeinen Verfügbarkeit (GA) einhalten.*|
|vmc-p-`<region>`.vault.azure.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure KeyVault | HTTPS | 443 | Erforderlich für die sichere Kommunikation zwischen den Knoten und Azure Key Vault. Zertifikate und Schlüssel werden verwendet, um die Kommunikation innerhalb des Clusters zu sichern.|
|management.azure.com:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Virtual Machine Scale Sets/Azure Management API | HTTPS | 443 | Erforderlich, um Informationen zu Cassandra-Knoten zu sammeln und zu verwalten (z. B. Neustart).|
|*.servicebus.windows.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure EventHub | HTTPS | 443 | Erforderlich zum Weiterleiten von Protokollen an Azure.|
|jarvis-west.dc.ad.msft.net:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure Monitor | HTTPS | 443 | Erforderlich zum Weiterleiten von Metriken an Azure. |
|login.microsoftonline.com:443</br> oder</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Azure AD | HTTPS | 443 | Erforderlich für die Azure Active Directory-Authentifizierung.|
| packages.microsoft.com | HTTPS | 443 | Erforderlich für Updates der Definition und Signaturen der Azure-Sicherheitsscanner. |

## <a name="managed-instance-for-apache-cassandra-internal-port-usage"></a>Verwendung interner Ports von Managed Instance for Apache Cassandra

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