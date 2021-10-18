---
title: Purview-Netzwerkarchitektur und bewährte Methoden
description: Dieser Artikel stellt Beispiele für Azure Purview-Netzwerkarchitekturoptionen bereit und beschreibt bewährte Methoden.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: b49709a5fb31e597b6a6f85ac0648018ea35a744
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661789"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Azure Purview-Netzwerkarchitektur und bewährte Methoden

Azure Purview ist eine PaaS-Datengovernancelösung (Platform-as-a-Service). 

Azure Purview-Konten verfügen über öffentliche Endpunkte, die über das Internet zugänglich sind, um eine Verbindung mit Purview herzustellen. Alle Endpunkte werden jedoch durch AAD-Anmeldungen und RBAC mit differenzierter Zugriffssteuerung gesichert.
Für eine zusätzliche Sicherheitsebene können Sie auch private Endpunkte für Ihr Azure Purview-Konto erstellen, wodurch dem Purview-Konto und seinen verwalteten Ressourcen eine private IP-Adresse aus Ihrem virtuellen Netzwerk in Azure zugewiesen wird. Dadurch wird der gesamte Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Purview-Konto auf eine private Verbindung beschränkt, wenn Benutzer mit den APIs, Purview Studio oder zum Überprüfen und Erfassen interagieren. Derzeit stellt die Azure Purview-Firewall Zugriffssteuerung für den öffentlichen Endpunkt Ihres Purview-Kontos bereit. Sie können die Firewall zum Zulassen oder Blockieren des gesamten Zugriffs über den öffentlichen Endpunkt einsetzen, wenn private Endpunkte verwendet werden. 

Basierend auf Ihren Netzwerk- und Sicherheitsanforderungen können Sie Azure Purview-Konten für den Zugriff auf die Purview zugrunde liegenden Dienste oder die Erfassung auf der Grundlage einer der folgenden Netzwerkoptionen festlegen und verwalten: 

- Verwenden [öffentlicher Azure-Endpunkte](#option-1---use-public-endpoints). 
- Verwenden [privater Endpunkte](#option-2---use-private-endpoints). 
- Verwenden [privater Endpunkte und Zulassen des öffentlichen Zugriffs auf dasselbe Purview-Konto](#option-3---use-both-private-endpoint-and-public-endpoints). 

Um herauszufinden, welche Option für Ihre Umgebung am besten geeignet ist, empfehlen wir Ihnen, zunächst die folgenden Aktionen durchzuführen: 

- Überprüfen Sie Ihre Netzwerktopologie und Sicherheitsanforderungen, bevor Sie Datenquellen in Azure Purview registrieren und überprüfen. Weitere Informationen finden Sie unter [Definieren einer Azure-Netzwerktopologie](/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology). 

- Definieren Sie Ihr [Netzwerkkonnektivitätsmodell für PaaS-Dienste](/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services). 

## <a name="intended-audience"></a>Zielpublikum  

- Datenarchitekturteam 
- Netzwerkteam  
- Datensicherheitsteam 

## <a name="why-do-you-need-to-consider-defining-a-networking-design-part-of-azure-purview-architecture"></a>Warum müssen Sie die Definition eines Netzwerkentwurfs als Teil der Azure Purview-Architektur in Betracht ziehen? 

Verwenden Sie diesen Leitfaden für bewährte Methoden, um Ihre Netzwerkumgebung für Azure Purview zu definieren und vorzubereiten, damit Sie auf Purview zugreifen und Datenquellen basierend auf den Netzwerk-, Verbindungs- und Sicherheitsanforderungen von verschiedenen Standorten in Ihrem Netzwerk oder in der Cloud überprüfen können.

Wir haben in diesem Leitfaden einige der gängigsten Netzwerkarchitekturszenarien für Azure Purview dokumentiert, die Optionen sind jedoch nicht auf die in diesem Dokument beschriebenen beschränkt. Überprüfen Sie die Einschränkungen von Purview bei der Planung von Netzwerken für Ihre Purview-Konten. 

## <a name="option-1---use-public-endpoints"></a>Option 1: Verwenden öffentlicher Endpunkte 

Standardmäßig können Sie Azure Purview-Konten über öffentliche Endpunkte verwenden, auf die über das Internet zugegriffen werden kann. Lassen Sie ein öffentliches Netzwerk in Ihrem Purview-Konto zu, wenn die folgenden Anforderungen erfüllt werden müssen: 

- Beim Überprüfen oder Herstellen einer Verbindung mit Azure Purview-Endpunkten ist keine private Konnektivität erforderlich. 
- Alle Datenquellen sind ausschließlich SaaS-Anwendungen. 
- Alle Datenquellen verfügen über einen öffentlichen Endpunkt, auf den über das Internet zugegriffen werden kann. 
- Geschäftsbenutzer benötigen Zugriff auf das Purview-Konto und auf Purview Studio über das Internet. 

### <a name="integration-runtime-options"></a>Integration Runtime-Optionen 

Um Datenquellen zu überprüfen, während die Firewall des Purview-Kontos auf „Öffentlichen Zugriff zulassen“ festgelegt ist, können Sie sowohl Azure Integration Runtime als auch eine [selbstgehostete Integration Runtime](./manage-integration-runtimes.md) verwenden, je nachdem, wie Ihre [Datenquellen unterstützt werden](manage-data-sources.md).  

- Sie können Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden, um Azure-Datenquellen wie Azure SQL-Datenbank oder Azure Blob Storage zu überprüfen.  

- Es wird empfohlen, nach Möglichkeit Azure Integration Runtime zum Überprüfen von Azure-Datenquellen zu verwenden, um Kosten und Verwaltungsmehraufwand zu reduzieren. 
  
- Um mehrere Azure-Datenquellen zu überprüfen, verwenden Sie das öffentliche Netzwerk und Azure Integration Runtime. Die folgenden Schritte zeigen den Kommunikationsfluss auf sehr hoher Ebene, wenn Sie Azure Integration Runtime verwenden, um eine Datenquelle in Azure zu überprüfen:

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Screenshot: Verbindungsfluss zwischen Azure Purview, Runtime und Datenquellen."lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. Eine manuelle oder automatische Überprüfung wird über die Purview-Datenzuordnung mithilfe von Azure Integration Runtime initiiert. 
   
  2. Azure Integration Runtime stellt eine Verbindung mit der Datenquelle her, um Metadaten zu extrahieren.

  3. Metadaten werden im verwalteten Speicher von Azure Purview in die Warteschlange eingereiht und in Azure Blob Storage gespeichert. 

  4. Metadaten werden an die Azure Purview-Datenzuordnung gesendet. 

- Für das Überprüfen einer lokalen und einer VM-basierten Datenquelle ist immer die Verwendung einer selbstgehosteten Integration Runtime erforderlich. Azure Integration Runtime wird für diese Datenquellen nicht unterstützt. Die folgenden Schritte zeigen den Kommunikationsfluss auf sehr hoher Ebene, wenn Sie eine selbstgehostete Integration Runtime verwenden, um eine Datenquelle zu überprüfen:

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Screenshot: Verbindungsfluss zwischen Azure Purview, einer selbstgehosteten Runtime und Datenquellen."lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. Eine manuelle oder automatische Überprüfung wird ausgelöst. Azure Purview stellt eine Verbindung mit Azure Key Vault her, um die Anmeldeinformationen für den Zugriff auf eine Datenquelle abzurufen.
   
  2. Die Überprüfung wird über die Purview-Datenzuordnung mithilfe einer selbstgehosteten Integration Runtime initiiert. 
   
  3. Der selbstgehostete Integration Runtime-Dienst des virtuellen Computers stellt eine Verbindung mit der Datenquelle her, um Metadaten zu extrahieren.

  4. Metadaten werden im VM-Speicher der selbstgehosteten Integration Runtime verarbeitet. Metadaten werden im verwalteten Speicher von Azure Purview in die Warteschlange eingereiht und dann in Azure Blob Storage gespeichert. 

  5. Metadaten werden an die Azure Purview-Datenzuordnung gesendet. 

### <a name="authentication-options"></a>Authentifizierungsoptionen  

Beim Überprüfen einer Datenquelle in Azure Purview müssen Sie Anmeldeinformationen angeben, damit Azure Purview Metadaten der Ressourcen mithilfe der Azure Integration Runtime in der Zieldatenquelle lesen kann. Bei Verwendung eines öffentlichen Netzwerks variieren die Authentifizierungsoptionen und -anforderungen abhängig von den folgenden Faktoren: 

- Datenquellentyp. Wenn die Datenquelle beispielsweise eine Azure SQL-Datenbank ist, müssen Sie eine SQL-Authentifizierung mit db_datareader-Zugriff auf jede Datenbank verwenden. Dies kann ein Benutzer, eine verwaltete Azure Purview-Identität oder ein Dienstprinzipal in Ihrer Azure Active Directory-Instanz sein, der Azure SQL-Datenbank als db_datareader hinzugefügt wurde. Wenn es sich bei der Datenquelle um eine Azure Blob Storage-Instanz handelt, können Sie die verwaltete Azure Purview-Identität oder einen Dienstprinzipal in Ihrer Azure Active Directory-Instanz verwenden, der als Rolle „Storage Blob-Datenleser“ für das Azure Storage-Konto hinzugefügt wurde, oder einfach den Schlüssel des Storage-Kontos verwenden.  

- Es wird empfohlen, nach Möglichkeit die verwaltete Identität von Azure Purview zum Überprüfen von Azure-Datenquellen zu verwenden, um Verwaltungsmehraufwand zu reduzieren. Für alle anderen Authentifizierungstypen müssen Sie [Anmeldeinformationen für Quellauthentifizierung in Azure Purview einrichten](manage-credentials.md): 

  - Generieren Sie ein Geheimnis innerhalb einer Azure Key Vault-Instanz. 
  - Registrieren Sie den Schlüsseltresor in Azure Purview.  
  - Erstellen Sie in Azure Purview neue Anmeldeinformationen mithilfe des in Azure Key Vault gespeicherten Geheimnisses. 

- Typ der Laufzeit, die bei der Überprüfung verwendet wird. Derzeit können Sie die verwaltete Azure Purview-Identität nicht mit einer selbstgehosteten Integration Runtime verwenden. 

### <a name="additional-considerations"></a>Weitere Überlegungen  

- Wenn Sie Datenquellen mithilfe öffentlicher Endpunkte überprüfen möchten, müssen Ihre lokalen oder VM-basierten Datenquellen über ausgehende Konnektivität mit Azure-Endpunkten verfügen. 

- Ihre selbstgehosteten Integration Runtime-VMs müssen über [ausgehende Konnektivität mit Azure-Endpunkten](manage-integration-runtimes.md#networking-requirements) verfügen. 

- Ihre Azure-Datenquellen müssen öffentlichen Zugriff zulassen. Wenn jedoch der Dienstendpunkt für die Datenquelle aktiviert ist, stellen Sie sicher, dass _Azure-Dienste in der Liste der vertrauenswürdigen Dienste_ auf Ihre Azure-Datenquellen zugreifen können. Der Dienstendpunkt leitet Datenverkehr aus dem VNet über einen optimalen Pfad an Azure weiter. 

## <a name="option-2---use-private-endpoints"></a>Option 2: Verwenden privater Endpunkte 

Sie können [private Azure-Endpunkte](../private-link/private-endpoint-overview.md) für Ihre Azure Purview-Konten verwenden, wenn Sie Azure IaaS- und PaaS-Datenquellen in virtuellen Azure-Netzwerken und lokalen Datenquellen über eine private Verbindung überprüfen oder Benutzern in einem virtuellen Netzwerk den sicheren Zugriff auf Azure Purview über eine [Private Link](../private-link/private-link-overview.md)-Verbindung erlauben möchten. 

Ähnlich wie andere PaaS-Lösungen (Platform as a Service) unterstützt Azure Purview keine direkte Bereitstellung in einem virtuellen Netzwerk. Daher können Sie bestimmte Netzwerkfeatures, z. B. Netzwerksicherheitsgruppen, Routingtabellen oder andere netzwerkabhängige Appliances wie Azure Firewall, nicht mit den Ressourcen des Angebots nutzen. Stattdessen können Sie private Endpunkte verwenden, die für Ihr virtuelles Netzwerk aktiviert werden können, und Sie können den öffentlichen Internetzugriff deaktivieren, um eine sichere Verbindung mit Purview herzustellen. 

Sie müssen private Endpunkte für Ihr Azure Purview-Konto verwenden, wenn Sie eine der folgenden Anforderungen erfüllen müssen: 

- Sie benötigen eine End-to-End-Netzwerkisolation für Purview-Konten und Datenquellen. 

- Sie müssen öffentlichen Zugriff auf Ihre Purview-Konten [blockieren](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access). 

- Ihre PaaS-Datenquellen werden mit privaten Endpunkten bereitgestellt, und Sie haben den gesamten Zugriff über den öffentlichen Endpunkt blockiert. 

- Ihre lokalen oder IaaS-Datenquellen können keine öffentlichen Endpunkte erreichen. 

### <a name="design-considerations"></a>Überlegungen zum Entwurf  

- Um eine private und sichere Verbindung mit Ihrem Purview-Konto herzustellen, müssen Sie ein Konto und einen privaten Portalendpunkt bereitstellen. (Beispielsweise, wenn Sie über die API eine Verbindung mit Purview herstellen oder Azure Purview Studio starten möchten.) 

- Wenn Sie eine Verbindung mit Purview Studio über private Endpunkte herstellen müssen, müssen Sie sowohl Konto- als auch private Portalendpunkte bereitstellen. 

- Um Datenquellen über private Konnektivität zu überprüfen, müssen Sie mindestens ein Konto und einen privaten Erfassungsendpunkt für Purview konfigurieren. Überprüfungen müssen mithilfe einer selbstgehosteten Integration Runtime mit einer anderen Authentifizierungsmethode als der verwalteten Azure Purview-Identität konfiguriert werden. 

- Ziehen Sie die [Unterstützungsmatrix für das Überprüfen von Datenquellen über einen privaten Erfassungsendpunkt](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint) zu Rate, bevor Sie Überprüfungen einrichten.

- Lesen Sie die [DNS-Anforderungen](catalog-private-link-name-resolution.md#deployment-options). Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den FQDN für Purview-Kontoendpunkte in die IP-Adresse des privaten Endpunkts aufzulösen. 

### <a name="integration-runtime-options"></a>Integration Runtime-Optionen 

- Wenn sich Ihre Datenquellen in Azure befinden, müssen Sie eine selbstgehostete Integration Runtime auf einem virtuellen Windows-Computer einrichten und verwenden, der in demselben virtuellen Netzwerk bereitgestellt wird, in dem private Azure Purview-Erfassungsendpunkte bereitgestellt werden. Azure Integration Runtime funktioniert nicht mit privaten Erfassungsendpunkten. 

- Um lokale Datenquellen zu überprüfen, können Sie auch eine selbstgehostete Integration Runtime auf einem lokalen Windows-Computer oder einer VM innerhalb des virtuellen Azure-Netzwerks installieren. 

- Wenn Sie einen privaten Endpunkt mit Azure Purview verwenden, müssen Sie Netzwerkkonnektivität von Datenquellen mit einer selbstgehosteten Integration-VM im virtuellen Azure-Netzwerk zulassen, in dem private Purview-Endpunkte bereitgestellt werden.  

- Es wird empfohlen, automatisches Upgrade der selbstgehosteten Integration Runtime zuzulassen. Stellen Sie sicher, dass Sie die erforderlichen Ausgangsregeln in Ihrem virtuellen Azure-Netzwerk oder in Ihrer Unternehmensfirewall öffnen, um automatisches Upgrade zu ermöglichen. Weitere Informationen finden Sie unter [Netzwerkanforderungen für selbstgehostete Integration Runtime](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Authentifizierungsoptionen  

- Sie können die verwaltete Identität (Managed Identity, MSI) von Azure Purview nicht verwenden, um Datenquellen über private Erfassungsendpunkte zu überprüfen. Verwenden Sie den Dienstprinzipal, den Kontoschlüssel oder SQL-Authentifizierung basierend auf dem Datenquellentyp.

- Stellen Sie sicher, dass Ihre Anmeldeinformationen in einem Azure-Schlüsseltresor gespeichert und in Azure Purview registriert sind.

- Sie müssen Anmeldeinformationen in Azure Purview basierend auf jedem Geheimnis erstellen, das Sie in der Azure Key Vault-Instanz erstellen. Sie müssen mindestens _get_- und _list_-Zugriff für Geheimnisse für Ihre Azure Purview-Instanz für die Key Vault-Ressource in Azure zuweisen, andernfalls funktionieren die Anmeldedaten im Azure Purview-Konto nicht. 

### <a name="current-limitations"></a>Aktuelle Einschränkungen 

- Das Überprüfen von mehreren Azure-Quellen unter Verwendung des gesamten Abonnements oder der gesamten Ressourcengruppe unter Verwendung von privaten Erfassungsendpunkten und einer selbstgehosteten Integration Runtime wird nicht unterstützt, wenn private Endpunkte für die Erfassung verwendet werden. Stattdessen können Sie Datenquellen einzeln registrieren und überprüfen. 

- Informationen zu Einschränkungen im Zusammenhang mit privaten Azure Purview-Endpunkten finden Sie unter [Bekannte Einschränkungen](catalog-private-link-troubleshoot.md#known-limitations). 

- Informationen zu Einschränkungen im Zusammenhang mit dem Private Link-Dienst finden Sie unter [Grenzwerte für Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits). 

### <a name="private-endpoint-scenarios"></a>Szenarien für private Endpunkte 

#### <a name="scenario-1-single-vnet--single-region"></a>Szenario 1: Einzelnes VNet – einzelne Region  

In diesem Szenario werden alle Azure-Datenquellen, selbstgegehosteten Integration Runtime-VMs und privaten Purview-Endpunkte in demselben VNet in einem Azure-Abonnement bereitgestellt.   

Wenn lokale Datenquellen vorhanden sind, wird die Konnektivität über eine Site-to-Site-VPN- oder ExpressRoute-Verbindung mit dem virtuellen Azure-Netzwerk bereitgestellt, in dem private Azure Purview-Endpunkte bereitgestellt werden. 

Diese Architektur eignet sich hauptsächlich für kleine Organisationen oder Entwicklungs-, Test- und Proof of Concept-Szenarien. 

  :::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="Screenshot: Azure Purview mit privaten Endpunkten in einem Szenario mit einem einzelnen VNet."lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="scenario-2-single-region--multiple-vnets"></a>Szenario 2: Einzelne Region – mehrere VNets 

Um mindestens zwei virtuelle Netzwerke in Azure zu verbinden, können Sie [Peering virtueller Netzwerke](../virtual-network/virtual-network-peering-overview.md) verwenden. Der Netzwerkdatenverkehr zwischen mittels Peering verbundenen virtuellen Netzwerken ist privat und wird im Azure-Backbone-Netzwerk verwaltet. 

Viele Kunden erstellen ihre Netzwerkinfrastruktur in Azure mithilfe der Hub-and-Spoke-Netzwerkarchitektur. Dabei gilt Folgendes: 

- Freigegebene Netzwerkdienste (z. B. virtuelle Netzwerkgeräte, ExpressRoute-/VPN-Gateways oder DNS-Server) werden im virtuellen Hub-Netzwerk (VNet) bereitgestellt. 
- Virtuelle Spoke-Netzwerke nutzen diese freigegebenen Dienste über das VNet-Peering. 

In Hub-and-Spoke-Netzwerkarchitekturen kann für das Datengovernanceteam Ihrer Organisation ein Azure-Abonnement bereitgestellt werden, das ein virtuelles Netzwerk (Hub) umfasst, und alle Datendienste können sich in wenigen anderen Abonnements befinden, die über VNet-Peering oder eine Site-to-Site-VPN-Verbindung mit dem virtuellen Hubnetzwerk verbunden sind. In dieser Architektur können Sie Azure Purview und mindestens eine selbstgehostete Integration Runtime-VM im Hubabonnement und im virtuellen Netzwerk bereitstellen und Datenquellen aus anderen VNETs aus mehreren Abonnements in derselben Region registrieren und überprüfen. 

Selbstgehostete Integration Runtime-VMs müssen sich im gleichen VNet wie der private Erfassungsendpunkt befinden, können jedoch in einem separaten Subnetz vorhanden sein.

  :::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="Screenshot: Azure Purview mit privaten Endpunkten in einem Szenario mit mehreren VNets."lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

Optional können Sie zusätzliche selbstgehostete Integration Runtimes in den virtuellen Spoke-Netzwerken bereitstellen. In diesem Fall müssen ein zusätzliches Konto und ein privater Erfassungsendpunkt im virtuellen Spoke-Netzwerk bereitgestellt werden. 

#### <a name="scenario-3-multiple-regions--multiple-vnets"></a>Szenario 3: Mehrere Regionen – mehrere VNets

Wenn Ihre Datenquellen auf mehrere Azure-Regionen in einem oder mehreren Azure-Abonnements verteilt sind, können Sie dieses Szenario verwenden.

Aus Leistungs- und Kostenoptimierungsgründen wird dringend empfohlen, mindestens eine selbstgehostete Integration Runtime-VM in jeder Region bereitzustellen, in der sich Datenquellen befinden. In diesem Fall müssen Sie ein zusätzliches Konto und einen privaten Erfassungsendpunkt für das Azure Purview-Konto in der Region und in dem VNet bereitstellen, in dem sich selbstgehostete Integration Runtime-VMs befinden.  

Wenn Sie Azure Data Lake Storage-Ressourcen (Gen 2) aus anderen Regionen registrieren und überprüfen müssen, ist eine lokale selbstgehostete Integration Runtime-VM in der Region erforderlich, in der sich die Datenquelle befindet. 

  :::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="Screenshot: Azure Purview mit privaten Endpunkten in einem Szenario mit mehreren VNets und Regionen."lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3---use-both-private-endpoint-and-public-endpoints"></a>Option 3: Verwenden von privaten und öffentlichen Endpunkten

Diese Option ist erforderlich, wenn eine Teilmenge Ihrer Datenquellen private Endpunkte aufweist und Sie gleichzeitig andere Datenquellen überprüfen müssen, die mit [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) konfiguriert sind, oder Datenquellen, die einen öffentlichen Endpunkt besitzen, auf den über das Internet zugegriffen werden kann.

Sie können private Endpunkte für Ihr Azure Purview-Konto verwenden und **Öffentlichen Netzwerkzugriff** so festlegen, dass er für Ihr Purview-Konto _zulässig_ ist, wenn Sie eine der folgenden Anforderungen erfüllen müssen:
  
- Sie müssen einige Datenquellen mithilfe eines privaten Erfassungsendpunkts und einige Datenquellen mithilfe öffentlicher Endpunkte oder des Dienstendpunkts überprüfen.

### <a name="integration-runtime-options"></a>Integration Runtime-Optionen 

- Wenn Sie eine mit einem privaten Endpunkt konfigurierte Azure-Datenquellen überprüfen müssen, müssen Sie eine selbstgehostete Integration Runtime auf einem virtuellen Windows-Computer einrichten und verwenden, der in demselben virtuellen Netzwerk bereitgestellt wird, in dem private Azure Purview-Erfassungsendpunkte bereitgestellt werden. Wenn Sie einen privaten Endpunkt mit Azure Purview verwenden, müssen Sie Netzwerkkonnektivität von Datenquellen mit einer selbstgehosteten Integration-VM im virtuellen Azure-Netzwerk zulassen, in dem private Purview-Endpunkte bereitgestellt werden.  

- Sie können Azure Integration Runtime verwenden, um eine Azure-Datenquelle zu überprüfen, die für das Zulassen eines öffentlichen Endpunkts konfiguriert ist. 

- Um lokale Datenquellen zu überprüfen, können Sie auch eine selbstgehostete Integration Runtime auf einem lokalen Windows-Computer oder einer VM innerhalb des virtuellen Azure-Netzwerks installieren. 

- Es wird empfohlen, automatisches Upgrade der selbstgehosteten Integration Runtime zuzulassen. Stellen Sie sicher, dass Sie die erforderlichen Ausgangsregeln in Ihrem virtuellen Azure-Netzwerk oder in Ihrer Unternehmensfirewall öffnen, um automatisches Upgrade zu ermöglichen. Weitere Informationen finden Sie unter [Netzwerkanforderungen für selbstgehostete Integration Runtime](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Authentifizierungsoptionen  

- Um eine Azure-Datenquelle zu überprüfen, die so konfiguriert ist, dass sie einen öffentlichen Endpunkt zulässt, können Sie basierend auf dem Datenquellentyp eine beliebige Authentifizierungsoption verwenden.
  
- Verwenden des privaten Erfassungsendpunkts zum Überprüfen einer Azure-Datenquelle, die mit einem privaten Endpunkt konfiguriert ist:
  - Sie können die verwaltete Identität (Managed Identity, MSI) von Azure Purview nicht verwenden. Verwenden Sie stattdessen basierend auf dem Datenquellentyp den Dienstprinzipal, den Kontoschlüssel oder SQL-Authentifizierung. 
  
  - Stellen Sie sicher, dass Ihre Anmeldeinformationen in einem Azure-Schlüsseltresor gespeichert und in Azure Purview registriert sind.

  - Sie müssen Anmeldeinformationen in Azure Purview basierend auf jedem Geheimnis erstellen, das Sie in der Azure Key Vault-Instanz erstellen. Sie müssen mindestens _get_- und _list_-Zugriff für Geheimnisse für Ihre Azure Purview-Instanz für die Key Vault-Ressource in Azure zuweisen, andernfalls funktionieren die Anmeldedaten im Azure Purview-Konto nicht. 

## <a name="next-steps"></a>Nächste Schritte
-  [Verwenden privater Endpunkte für den sicheren Zugriff auf Purview](./catalog-private-link.md)
