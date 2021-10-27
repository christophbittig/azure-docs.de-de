---
title: Azure Purview-Netzwerkarchitektur und bewährte Methoden
description: Dieser Artikel stellt Beispiele für Azure Purview-Netzwerkarchitekturoptionen bereit und beschreibt bewährte Methoden.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 04121992c3de5e061e54687c8d23469850607870
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073979"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Azure Purview-Netzwerkarchitektur und bewährte Methoden

Azure Purview ist eine Platform-as-a-Service(PaaS)-Lösung für Datengovernance. Azure Purview-Konten verfügen über öffentliche Endpunkte, die über das Internet zugänglich sind, um eine Verbindung mit den Dienst herzustellen. Allerdings werden alle Endpunkte durch Anmeldungen in Azure Active Directory (Azure AD) und die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) geschützt.

Durch Erstellen privater Endpunkte für Ihr Azure Purview-Konto können Sie die Sicherheit weiter erhöhen. Sie erhalten dann eine private IP-Adresse aus Ihrem virtuellen Netzwerk in Azure für das Azure Purview-Konto und dessen verwaltete Ressourcen. Diese Adresse beschränkt den gesamten Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Azure Purview-Konto auf eine private Verbindung für die Benutzerinteraktion mit den APIs und Azure Purview Studio oder zum Überprüfen und Erfassen von Daten. 

Derzeit stellt die Azure Purview-Firewall eine Zugriffssteuerung für den öffentlichen Endpunkt Ihres Purview-Kontos bereit. Bei Verwendung privater Endpunkte können Sie mit der Firewall den gesamten Zugriff über den öffentlichen Endpunkt zulassen oder blockieren. 

Basierend auf Ihren Netzwerk-, Konnektivitäts- und Sicherheitsanforderungen können Sie Azure Purview-Konten für den Zugriff auf die zugrunde liegenden Dienste oder die Erfassung festlegen und verwalten. Verwenden Sie diesen Leitfaden für bewährte Methoden, um Ihre Netzwerkumgebung zu definieren und vorzubereiten, damit Sie auf Azure Purview zugreifen und Datenquellen von verschiedenen Standorten in Ihrem Netzwerk oder in der Cloud überprüfen können. 

In diesem Leitfaden werden die folgenden Netzwerkoptionen behandelt: 

- Verwenden [öffentlicher Azure-Endpunkte](#option-1-use-public-endpoints). 
- Verwenden [privater Endpunkte](#option-2-use-private-endpoints). 
- Verwenden [privater Endpunkte und Zulassen des öffentlichen Zugriffs auf dasselbe Azure Purview-Konto](#option-3-use-both-private-and-public-endpoints). 

In diesem Leitfaden werden einige der gängigsten Netzwerkarchitekturszenarien für Azure Purview beschrieben. Obwohl Sie nicht auf diese Szenarien beschränkt sind, sollten Sie die [Einschränkungen](#current-limitations) des Diensts beachten, wenn Sie Netzwerkfunktionen für Ihre Azure Purview-Konten planen. 

Zur Zielgruppe für diesen Leitfaden gehören:

- Datenarchitekturteam 
- Netzwerkteam  
- Datensicherheitsteam 

## <a name="prerequisites"></a>Voraussetzungen

Um herauszufinden, welche Netzwerkoption für Ihre Umgebung am besten geeignet ist, empfehlen wir Ihnen, zunächst die folgenden Aktionen durchzuführen: 

- Überprüfen Sie Ihre Netzwerktopologie und Sicherheitsanforderungen, bevor Sie Datenquellen in Azure Purview registrieren und überprüfen. Weitere Informationen finden Sie unter [Definieren einer Azure-Netzwerktopologie](/azure/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology). 

- Definieren Sie Ihr [Netzwerkkonnektivitätsmodell für PaaS-Dienste](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services). 

## <a name="option-1-use-public-endpoints"></a>Option 1: Verwenden öffentlicher Endpunkte 

Standardmäßig können Sie Azure Purview-Konten über öffentliche Endpunkte verwenden, auf die über das Internet zugegriffen werden kann. Lassen Sie ein öffentliches Netzwerk in Ihrem Azure Purview-Konto zu, wenn die folgenden Anforderungen erfüllt werden müssen: 

- Beim Überprüfen oder Herstellen einer Verbindung mit Azure Purview-Endpunkten ist keine private Konnektivität erforderlich. 
- Alle Datenquellen sind ausschließlich SaaS-Anwendungen. 
- Alle Datenquellen verfügen über einen öffentlichen Endpunkt, auf den über das Internet zugegriffen werden kann. 
- Geschäftsbenutzer benötigen Zugriff auf ein Azure Purview-Konto und auf Azure Purview Studio über das Internet. 

### <a name="integration-runtime-options"></a>Integration Runtime-Optionen 

Um Datenquellen zu überprüfen, während die Firewall des Azure Purview-Kontos auf „Öffentlichen Zugriff zulassen“ festgelegt ist, können Sie sowohl die Azure Integration Runtime als auch eine [selbstgehostete Integration Runtime](./manage-integration-runtimes.md) verwenden. Wie Sie diese verwenden, hängt von der [Unterstützungsfähigkeit Ihrer Datenquellen ab](manage-data-sources.md).  

Hier sind einige bewährte Methoden angegeben:

- Mit der Azure Integration Runtime oder einer selbstgehosteten Integration Runtime können Sie Azure-Datenquellen wie Azure SQL-Datenbank oder Azure Blob Storage überprüfen. Nach Möglichkeit sollten Sie die Azure Integration Runtime zum Überprüfen von Azure-Datenquellen verwenden, um Kosten und Verwaltungsaufwand zu reduzieren. 
  
- Um mehrere Azure-Datenquellen zu überprüfen, verwenden Sie das öffentliche Netzwerk und die Azure Integration Runtime. Die folgenden Schritte zeigen den Kommunikationsfluss auf hoher Ebene, wenn Sie die Azure Integration Runtime verwenden, um eine Datenquelle in Azure zu überprüfen:

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Screenshot: Verbindungsfluss zwischen Azure Purview, der Azure-Runtime und Datenquellen"lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. Eine manuelle oder automatische Überprüfung wird über die Azure Purview-Datenzuordnung mittels der Azure Integration Runtime initiiert. 
   
  2. Die Azure Integration Runtime stellt eine Verbindung mit der Datenquelle her, um Metadaten zu extrahieren.

  3. Metadaten werden im verwalteten Speicher von Azure Purview in die Warteschlange eingereiht und in Azure Blob Storage gespeichert. 

  4. Metadaten werden an die Azure Purview-Datenzuordnung gesendet. 

- Für das Überprüfen lokaler und VM-basierter Datenquellen ist immer die Verwendung einer selbstgehosteten Integration Runtime erforderlich. Die Azure Integration Runtime wird für diese Datenquellen nicht unterstützt. Die folgenden Schritte zeigen den Kommunikationsfluss auf hoher Ebene, wenn Sie eine selbstgehostete Integration Runtime verwenden, um eine Datenquelle zu überprüfen:

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Screenshot: Verbindungsfluss zwischen Azure Purview, einer selbstgehosteten Runtime und Datenquellen."lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. Eine manuelle oder automatische Überprüfung wird ausgelöst. Azure Purview stellt eine Verbindung mit Azure Key Vault her, um die Anmeldeinformationen für den Zugriff auf eine Datenquelle abzurufen.
   
  2. Die Überprüfung wird von der Purview-Datenzuordnung aus durch eine selbstgehostete Integration Runtime initiiert. 
   
  3. Der selbstgehostete Integration Runtime-Dienst der VM stellt eine Verbindung mit der Datenquelle her, um Metadaten zu extrahieren.

  4. Metadaten werden im VM-Speicher für die selbstgehostete Integration Runtime verarbeitet. Metadaten werden im verwalteten Speicher von Azure Purview in die Warteschlange eingereiht und dann in Azure Blob Storage gespeichert. 

  5. Metadaten werden an die Azure Purview-Datenzuordnung gesendet. 

### <a name="authentication-options"></a>Authentifizierungsoptionen  

Wenn Sie eine Datenquelle in Azure Purview scannen, müssen Sie Anmeldeinformationen bereitstellen. Azure Purview kann dann die Metadaten der Ressourcen lesen, indem es die Azure Integration Runtime in der Zieldatenquelle verwendet. Bei Verwendung eines öffentlichen Netzwerks variieren die Authentifizierungsoptionen und -anforderungen abhängig von den folgenden Faktoren: 

- **Datenquellentyp** Wenn die Datenquelle beispielsweise Azure SQL-Datenbank ist, müssen Sie eine SQL-Authentifizierung mit db_datareader-Zugriff auf jede Datenbank verwenden. Dies kann eine vom Benutzer oder von Azure Purview verwaltete Identität sein. Oder es kann ein Dienstprinzipal sein, der der SQL-Datenbank in Azure Active Directory als db_datareader hinzugefügt wurde. 

  Wenn es sich bei der Datenquelle um Azure Blob Storage handelt, können Sie eine verwaltete Azure Purview-Identität oder einen Dienstprinzipal in Azure Active Directory verwenden, der mit der Rolle „Storage Blob-Datenleser“ für das Azure Storage-Konto hinzugefügt wurde. Oder verwenden Sie einfach den Schlüssel des Speicherkontos.  

- **Authentifizierungstyp** Nach Möglichkeit sollten Sie die verwaltete Identität von Azure Purview zum Überprüfen von Azure-Datenquellen verwenden, um den Verwaltungsaufwand zu reduzieren. Für alle anderen Authentifizierungstypen müssen Sie [Anmeldeinformationen für Quellauthentifizierung in Azure Purview einrichten](manage-credentials.md): 

  1. Generieren Sie ein Geheimnis innerhalb einem Azure Key Vault. 
  1. Registrieren Sie den Schlüsseltresor in Azure Purview.  
  1. Erstellen Sie in Azure Purview neue Anmeldeinformationen mithilfe des im der SQL-Datenbank gespeicherten Geheimnisses. 

- **Beim Überprüfen verwendeter Runtimetyp** Derzeit können Sie von Azure Purview verwaltete Identitäten nicht mit einer selbstgehosteten Integration Runtime verwenden. 

### <a name="additional-considerations"></a>Weitere Überlegungen  

- Wenn Sie Datenquellen über öffentliche Endpunkte überprüfen möchten, müssen Ihre lokalen oder VM-basierten Datenquellen über ausgehende Konnektivität mit Azure-Endpunkten verfügen. 

- Ihre selbstgehosteten Integration Runtime-VMs müssen über [ausgehende Konnektivität mit Azure-Endpunkten](manage-integration-runtimes.md#networking-requirements) verfügen. 

- Ihre Azure-Datenquellen müssen den öffentlichen Zugriff zulassen. Wenn der Dienstendpunkt für die Datenquelle aktiviert ist, stellen Sie sicher, dass _Azure-Dienste in der Liste der vertrauenswürdigen Dienste_ auf Ihre Azure-Datenquellen zugreifen dürfen. Der Dienstendpunkt leitet Datenverkehr aus dem virtuellen Netzwerk über einen optimalen Pfad an Azure weiter. 

## <a name="option-2-use-private-endpoints"></a>Option 2: Verwenden privater Endpunkte 

Sie können [private Azure-Endpunkte](../private-link/private-endpoint-overview.md) für Ihre Azure Purview-Konten verwenden. Diese Option ist nützlich, wenn Sie eine der folgenden Aufgaben durchführen müssen:

- Überprüfen Sie Azure Infrastructure-as-a-Service(IaaS)- und PaaS-Datenquellen in virtuellen Azure-Netzwerken und lokalen Datenquellen über eine private Verbindung.
- Benutzern in einem virtuellen Netzwerk den sicheren Zugriff auf Azure Purview über [Azure Private Link](../private-link/private-link-overview.md) gewähren. 

Ähnlich wie bei anderen PaaS-Lösungen unterstützt Azure Purview die direkte Bereitstellung in einem virtuellen Netzwerk nicht. Daher können Sie bestimmte Netzwerkfeatures nicht mit den Ressourcen dieses Angebots verwenden, z. B. Netzwerksicherheitsgruppen, Routingtabellen oder anderen netzwerkabhängige Appliances wie Azure Firewall. Stattdessen können Sie private Endpunkte verwenden, die in Ihrem virtuellen Netzwerk aktiviert werden können. Anschließend können Sie den öffentlichen Internetzugriff deaktivieren, um eine sichere Verbindung mit Azure Purview herzustellen. 

Sie müssen private Endpunkte für Ihr Azure Purview-Konto verwenden, wenn Sie eine der folgenden Anforderungen haben: 

- Sie benötigen End-to-End-Netzwerkisolation für Azure Purview-Konten und Datenquellen. 

- Sie müssen öffentlichen Zugriff auf Ihre Purview-Konten [blockieren](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access). 

- Ihre PaaS-Datenquellen werden mit privaten Endpunkten bereitgestellt, und Sie haben den gesamten Zugriff über den öffentlichen Endpunkt blockiert. 

- Ihre lokalen oder IaaS-Datenquellen können keine öffentlichen Endpunkte erreichen. 

### <a name="design-considerations"></a>Überlegungen zum Entwurf  

- Um eine private und sichere Verbindung mit Ihrem Azure Purview-Konto herzustellen, müssen Sie ein Konto und einen privaten Portalendpunkt bereitstellen. Diese Art der Bereitstellung ist beispielsweise erforderlich, wenn Sie beabsichtigen, über die API eine Verbindung mit Azure Purview herzustellen oder Azure Purview Studio zu verwenden.

- Wenn Sie eine Verbindung mit Azure Purview Studio über private Endpunkte herstellen müssen, müssen Sie sowohl Konto- als auch private Portalendpunkte bereitstellen. 

- Um Datenquellen über private Konnektivität zu überprüfen, müssen Sie mindestens ein Konto und einen privaten Erfassungsendpunkt für Azure Purview konfigurieren. Überprüfungen müssen mithilfe einer selbstgehosteten Integration Runtime mit einer anderen Authentifizierungsmethode als der verwalteten Azure Purview-Identität konfiguriert werden. 

- Ziehen Sie die [Unterstützungsmatrix für das Überprüfen von Datenquellen über einen privaten Erfassungsendpunkt](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint) zu Rate, bevor Sie Überprüfungen einrichten.

- Lesen Sie die [DNS-Anforderungen](catalog-private-link-name-resolution.md#deployment-options). Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für die Endpunkte des Azure Purview-Kontos als IP-Adresse des privaten Endpunkts aufzulösen. 

### <a name="integration-runtime-options"></a>Integration Runtime-Optionen 

- Wenn Ihre Datenquellen in Azure sind, müssen Sie eine selbstgehostete Integration Runtime auf einem virtuellen Windows-Computer einrichten und verwenden, der in demselben virtuellen Netzwerk bereitgestellt wird, in dem private Azure Purview-Erfassungsendpunkte bereitgestellt werden. Die Azure Integration Runtime funktioniert nicht mit privaten Erfassungsendpunkten. 

- Um lokale Datenquellen zu überprüfen, können Sie auch eine selbstgehostete Integration Runtime auf einem lokalen Windows-Computer oder einer VM innerhalb eines virtuellen Azure-Netzwerks installieren. 

- Wenn Sie einen privaten Endpunkt mit Azure Purview verwenden, müssen Sie Netzwerkkonnektivität von Datenquellen mit einer selbstgehosteten Integration-VM im virtuellen Azure-Netzwerk zulassen, in dem private Azure Purview-Endpunkte bereitgestellt werden.  

- Es wird empfohlen, das automatische Upgrade der selbstgehosteten Integration Runtime zuzulassen. Stellen Sie sicher, dass Sie die erforderlichen Ausgangsregeln in Ihrem virtuellen Azure-Netzwerk oder in Ihrer Unternehmensfirewall öffnen, um automatisches Upgrades zu ermöglichen. Weitere Informationen finden Sie unter [Netzwerkanforderungen für selbstgehostete Integration Runtime](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Authentifizierungsoptionen  

- Sie können die verwaltete Identität von Azure Purview nicht verwenden, um Datenquellen über private Erfassungsendpunkte zu überprüfen. Verwenden Sie abhängig vom Datenquellentyp einen Dienstprinzipal, einen Kontoschlüssel oder eine SQL-Authentifizierung.

- Stellen Sie sicher, dass Ihre Anmeldeinformationen in einem Azure-Schlüsseltresor gespeichert und in Azure Purview registriert sind.

- Sie müssen Anmeldeinformationen in Azure Purview basierend auf jedem Geheimnis erstellen, das Sie in der Azure Key Vault-Instanz erstellen. Sie müssen mindestens _get_- und _list_-Zugriff für Geheimnisse für Ihre Azure Purview-Instanz auf der Key Vault-Ressource in Azure zuweisen. Andernfalls funktionieren die Anmeldeinformationen im Azure Purview-Konto nicht. 

### <a name="current-limitations"></a>Aktuelle Einschränkungen 

- Das Überprüfen mehrerer Azure-Quellen unter Verwendung des gesamten Abonnements oder der gesamten Ressourcengruppe über private Erfassungsendpunkte und eine selbstgehostete Integration Runtime wird nicht unterstützt, wenn private Endpunkte für die Erfassung verwendet werden. Stattdessen können Sie Datenquellen einzeln registrieren und überprüfen. 

- Informationen zu Einschränkungen im Zusammenhang mit privaten Azure Purview-Endpunkten finden Sie unter [Bekannte Einschränkungen](catalog-private-link-troubleshoot.md#known-limitations).

- Informationen zu Einschränkungen im Zusammenhang mit dem Private Link-Dienst finden Sie unter [Grenzwerte für Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits). 

### <a name="private-endpoint-scenarios"></a>Szenarien für private Endpunkte 

#### <a name="single-virtual-network-single-region"></a>Einzelnes virtuelles Netzwerk, einzelne Region  

In diesem Szenario werden alle Azure-Datenquellen, selbstgegehosteten Integration Runtime-VMs und privaten Purview-Endpunkte in demselben virtuellen Netzwerk in einem Azure-Abonnement bereitgestellt.   

Wenn lokale Datenquellen vorhanden sind, wird die Konnektivität über eine Site-to-Site-VPN- oder Azure ExpressRoute-Verbindung mit einem virtuellen Azure-Netzwerk bereitgestellt, in dem private Azure Purview-Endpunkte bereitgestellt werden. 

Diese Architektur eignet sich hauptsächlich für kleine Organisationen oder Entwicklungs-, Test- und Proof of Concept-Szenarien. 

:::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="Screenshot: Azure Purview mit privaten Endpunkten in einem Szenario mit einem einzelnen virtuellen Netzwerk"lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="single-region-multiple-virtual-networks"></a>Einzelne Region, mehrere virtuelle Netzwerke 

Um mindestens zwei virtuelle Netzwerke in Azure zu verbinden, können Sie [Peering virtueller Netzwerke](../virtual-network/virtual-network-peering-overview.md) verwenden. Der Netzwerkdatenverkehr zwischen mittels Peering verbundenen virtuellen Netzwerken ist privat und wird im Azure-Backbone-Netzwerk verwaltet. 

Viele Kunden erstellen ihre Netzwerkinfrastruktur in Azure als Hub-and-Spoke-Netzwerkarchitektur. Dabei gilt Folgendes: 

- Freigegebene Netzwerkdienste (z. B. virtuelle Netzwerkgeräte, ExpressRoute-/VPN-Gateways oder DNS-Server) werden im virtuellen Hub-Netzwerk bereitgestellt. 
- Virtuelle Spoke-Netzwerke nutzen diese gemeinsamen Dienste über Peering virtueller Netzwerke. 

In Hub-and-Spoke-Netzwerkarchitekturen kann dem Datengovernanceteam Ihrer Organisation ein Azure-Abonnement bereitgestellt werden, das ein virtuelles Netzwerk (als Hub) enthält. Alle Datendienste können sich in einer kleinen Anzahl anderer Abonnements befinden, die durch Peering virtueller Netzwerke oder eine Site-to-Site-VPN-Verbindung mit dem virtuellen Hubnetzwerk verbunden sind. 

In einer Hub-and-Spoke-Architektur können Sie Azure Purview und eine oder mehrere selbstgehostete Integration Runtime-VMs im Hub-Abonnement und virtuellen Netzwerk bereitstellen. Sie können Datenquellen anderer virtueller Netzwerke aus mehreren Abonnements in derselben Region registrieren und überprüfen. 

Selbstgehostete Integration Runtime-VMs müssen sich im gleichen virtuellen Netzwerk wie der private Erfassungsendpunkt befinden, können jedoch in einem separaten Subnetz vorhanden sein.

:::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="Screenshot: Azure Purview mit privaten Endpunkten in einem Szenario mit mehreren virtuellen Netzwerken"lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

Optional können Sie eine zusätzliche selbstgehostete Integration Runtime in den virtuellen Spoke-Netzwerken bereitstellen. In diesem Fall müssen Sie ein zusätzliches Konto und einen privaten Erfassungsendpunkt im virtuellen Spoke-Netzwerk bereitstellen. 

#### <a name="multiple-regions-multiple-virtual-networks"></a>Mehrere Regionen, mehrere virtuelle Netzwerke

Wenn Ihre Datenquellen auf mehrere Azure-Regionen in einem oder mehreren Azure-Abonnements verteilt sind, können Sie dieses Szenario verwenden.

Für die Leistungs- und Kostenoptimierung wird dringend empfohlen, mindestens eine selbstgehostete Integration Runtime-VM in jeder Region bereitzustellen, in der sich Datenquellen befinden. In diesem Fall müssen Sie ein zusätzliches Konto und einen privaten Erfassungsendpunkt für das Azure Purview-Konto in der Region und in dem virtuellen Netzwerk bereitstellen, in dem sich selbstgehostete Integration Runtime-VMs befinden.  

Wenn Sie Azure Data Lake Storage-Ressourcen (Gen 2) aus anderen Regionen registrieren und überprüfen müssen, brauchen Sie eine lokale selbstgehostete Integration Runtime-VM in der Region, in der sich die Datenquelle befindet. 

:::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="Screenshot: Azure Purview mit privaten Endpunkten in einem Szenario mit mehreren virtuellen Netzwerken und mehreren Regionen"lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3-use-both-private-and-public-endpoints"></a>Option 3: Verwenden von privaten und öffentlichen Endpunkten

Sie können eine Option auswählen, bei der eine Teilmenge Ihrer Datenquellen private Endpunkte verwendet werden und Sie gleichzeitig eine der folgenden Quellen überprüfen müssen:

- Andere Datenquellen, die mit einem [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) konfiguriert sind
- Datenquellen, die über einen öffentlichen Endpunkt verfügen, auf den über das Internet zugegriffen werden kann

Wenn Sie einige Datenquellen mithilfe eines privaten Erfassungsendpunkts und einige Datenquellen mithilfe öffentlicher Endpunkte oder des Dienstendpunkts überprüfen, haben Sie folgende Möglichkeiten:

1. Private Endpunkte für Ihr Azure Purview-Konto verwenden
1. Den **Zugriff aus öffentlichen Netzwerken** für Ihr Azure Purview-Konto auf **Zulassen** stellen

### <a name="integration-runtime-options"></a>Integration Runtime-Optionen 

- Wenn Sie eine mit einem privaten Endpunkt konfigurierte Azure-Datenquelle überprüfen wollen, müssen Sie eine selbstgehostete Integration Runtime auf einer Windows-VM einrichten und verwenden, die im selben virtuellen Netzwerk wie die privaten Azure Purview-Erfassungsendpunkte bereitgestellt ist. 

  Wenn Sie einen privaten Endpunkt mit Azure Purview verwenden, müssen Sie Netzwerkkonnektivität von Datenquellen mit einer selbstgehosteten Integration-VM im virtuellen Azure-Netzwerk zulassen, in dem private Azure Purview-Endpunkte bereitgestellt werden.  

- Sie können Azure Integration Runtime verwenden, um eine Azure-Datenquelle zu überprüfen, die für das Zulassen eines öffentlichen Endpunkts konfiguriert ist. 

- Um lokale Datenquellen zu überprüfen, können Sie auch eine selbstgehostete Integration Runtime auf einem lokalen Windows-Computer oder einer VM innerhalb des virtuellen Azure-Netzwerks installieren. 

- Es wird empfohlen, das automatische Upgrade für selbstgehosteten Integration Runtimes zuzulassen. Stellen Sie sicher, dass Sie die erforderlichen Ausgangsregeln in Ihrem virtuellen Azure-Netzwerk oder in Ihrer Unternehmensfirewall öffnen, um automatisches Upgrades zu ermöglichen. Weitere Informationen finden Sie unter [Netzwerkanforderungen für selbstgehostete Integration Runtime](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Authentifizierungsoptionen  

- Um eine Azure-Datenquelle zu überprüfen, die so konfiguriert ist, dass sie einen öffentlichen Endpunkt zulässt, können Sie basierend auf dem Datenquellentyp eine beliebige Authentifizierungsoption verwenden.
  
- Wenn Sie einen privaten Erfassungsendpunkt zum Überprüfen einer Azure-Datenquelle verwenden, die mit einem privaten Endpunkt konfiguriert ist:

  - Sie können keine durch Azure Purview verwaltete Identität verwenden. Verwenden Sie stattdessen abhängig vom Datenquellentyp einen Dienstprinzipal, einen Kontoschlüssel oder eine SQL-Authentifizierung. 
  
  - Stellen Sie sicher, dass Ihre Anmeldeinformationen in einem Azure-Schlüsseltresor gespeichert und in Azure Purview registriert sind.

  - Sie müssen Anmeldeinformationen in Azure Purview basierend auf jedem Geheimnis erstellen, das Sie im Azure Key Vault erstellen. Weisen Sie mindestens _get_- und _list_-Zugriff für Geheimnisse für Azure Purview auf der Key Vault-Ressource in Azure zu. Andernfalls funktionieren die Anmeldeinformationen im Azure Purview-Konto nicht. 

## <a name="next-steps"></a>Nächste Schritte
-  [Verwenden privater Endpunkte für den sicheren Zugriff auf Azure Purview](./catalog-private-link.md)
