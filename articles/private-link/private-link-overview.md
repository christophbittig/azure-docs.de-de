---
title: Was ist Azure Private Link?
description: Übersicht über Features, Architektur und Implementierung von Azure Private Link. Es wird beschrieben, wie private Azure-Endpunkte und der Dienst „Azure Private Link“ funktionieren und wie Sie diese Features nutzen.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 3fc3dbbe453ebddd03f70082ff6974fd830b380c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305822"
---
# <a name="what-is-azure-private-link"></a>Was ist Azure Private Link? 
Mit Azure Private Link können Sie über einen [privaten Endpunkt](private-endpoint-overview.md) in Ihrem virtuellen Netzwerk auf Azure-PaaS-Dienste (beispielsweise Azure Storage und SQL Database) sowie auf in Azure gehostete kundeneigene Dienste/Partnerdienste zugreifen.

Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst verläuft über das Microsoft-Backbone-Netzwerk. Es ist nicht mehr erforderlich, dass Sie Ihren Dienst über das öffentliche Internet verfügbar machen. Sie können Ihren eigenen [Private Link-Dienst](private-link-service-overview.md) in Ihrem virtuellen Netzwerk erstellen und Ihren Kunden zur Verfügung stellen. Die Einrichtung und Nutzung von Azure Private Link ist in Azure PaaS-, Kunden- und gemeinsam genutzten Partnerdiensten einheitlich.

> [!IMPORTANT]
> Azure Private Link ist jetzt allgemein verfügbar. Sowohl der Dienst für private Endpunkte als auch der Dienst „Private Link“ (Dienst im Hintergrund von Load Balancer Standard) ist allgemein verfügbar. Für unterschiedliche Azure PaaS-Instanzen wird das Onboarding für Azure Private Link basierend auf verschiedenen Zeitplänen durchgeführt. Den genauen Azure PaaS-Status für Private Link finden Sie unter [Verfügbarkeit von Azure Private Link](availability.md). Weitere Informationen zu bekannten Einschränkungen finden Sie unter [Privater Endpunkt](private-endpoint-overview.md#limitations) und [Private Link-Dienst](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure Private Link Center im Azure-Portal" border="false":::

## <a name="key-benefits"></a>Hauptvorteile
Azure Private Link bietet folgende Vorteile:  
- **Private Zugriffsdienste auf der Azure-Plattform**: Verbinden Sie Ihr virtuelles Netzwerk mit Diensten in Azure ohne öffentliche IP-Adresse an der Quelle oder am Ziel. Dienstanbieter können ihre Dienste in ihrem eigenen virtuellen Netzwerk rendern, und Consumer können in ihrem lokalen virtuellen Netzwerk auf diese Dienste zugreifen. Die Private Link-Plattform verarbeitet die Konnektivität zwischen dem Consumer und den Diensten über das Azure-Backbone-Netzwerk. 
 
- **Lokale Netzwerke und Peernetzwerke**: Greifen Sie mithilfe privater Endpunkte von einer lokalen Umgebung über privates ExpressRoute-Peering, VPN-Tunnel und virtuelle Netzwerke mit Peering auf Dienste zu, die in Azure ausgeführt werden. Es ist nicht erforderlich, ExpressRoute-Microsoft-Peering einzurichten oder über das Internet auf den Dienst zuzugreifen. Private Link ist eine sichere Möglichkeit, um Workloads zu Azure zu migrieren.
 
- **Schutz vor Datenlecks**: Ein privater Endpunkt wird nicht dem gesamten Dienst, sondern der Instanz einer PaaS-Ressource zugeordnet. Consumer können nur eine Verbindung mit der entsprechenden Ressource herstellen. Der Zugriff auf alle anderen Ressourcen des Diensts ist blockiert. Dieser Mechanismus ermöglicht den Schutz vor Risiken aufgrund von Datenlecks. 
 
- **Globale Reichweite**: Stellen Sie private Verbindungen zu Diensten her, die in anderen Regionen ausgeführt werden. Das virtuelle Netzwerk des Consumers kann sich beispielsweise in Region A befinden und eine Verbindung mit Diensten hinter Private Link in Region B herstellen.  
 
- **Ausweiten auf Ihre eigenen Dienste**: Aktivieren Sie die gleichen Funktionen, um Ihren Dienst privat für Consumer in Azure zu rendern. Wenn Sie Ihren Dienst hinter einem Azure Load Balancer Standard platzieren, können Sie ihn für Private Link aktivieren. Der Consumer kann dann mithilfe eines privaten Endpunkts in seinem eigenen virtuellen Netzwerk eine direkte Verbindung mit dem Dienst herstellen. Die Verbindungsanforderungen können mithilfe eines Ablaufs für Genehmigungsaufrufe verwaltet werden. Azure Private Link funktioniert für Consumer und Dienste, die zu unterschiedlichen Azure Active Directory-Mandanten gehören. 

> [!NOTE]
> Azure Private Link erstreckt sich zusammen mit Azure Virtual Network über [Azure-Verfügbarkeitszonen](../availability-zones/az-overview.md) und ist somit zonenresilient. Um mithilfe eines privaten Endpunkts Hochverfügbarkeit für die Azure-Ressource zu ermöglichen, stellen Sie sicher, dass die Ressource zonenresilient ist.

## <a name="availability"></a>Verfügbarkeit 

Informationen zu Azure-Diensten, die Private Link unterstützen, finden Sie unter [Verfügbarkeit von Azure Private Link](availability.md).

Aktuelle Benachrichtigungen finden Sie auf der Seite mit den [Azure Private Link-Updates](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

Azure Private Link verfügt eine Integration in Azure Monitor. Diese Kombination ermöglicht Folgendes:

 - Archivierung von Protokollen in einem Speicherkonto
 - Streaming von Ereignissen an Ihren Event Hub
 - Azure Monitor-Protokollierung

Sie können über Azure Monitor auf die folgenden Informationen zugreifen: 
- **Privater Endpunkt**: 
    - Die vom privaten Endpunkt verarbeiteten Daten (ein-/ausgehend)
 
- **Private Link-Dienst**:
    - Die vom Private Link-Dienst verarbeiteten Daten (ein-/ausgehend)
    - NAT-Portverfügbarkeit  
 
## <a name="pricing"></a>Preise   
Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Häufig gestellte Fragen  
Häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen zu Azure Private Link](private-link-faq.yml).
 
## <a name="limits"></a>Einschränkungen  
Informationen zu Einschränkungen finden Sie unter [Einschränkungen für Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Vereinbarung zum Servicelevel
Informationen zur Vereinbarung zum Servicelevel (SLA) finden Sie unter [SLA für Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen eines privaten Endpunkts über das Azure-Portal](create-private-endpoint-portal.md)
- [Schnellstart: Erstellen eines Private Link-Diensts über das Azure-Portal](create-private-link-service-portal.md)
- [Lernmodul: Einführung in Azure Private Link](/learn/modules/introduction-azure-private-link/)
