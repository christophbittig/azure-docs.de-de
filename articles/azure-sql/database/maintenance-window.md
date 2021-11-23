---
title: Wartungsfenster
description: Hier finden Sie grundlegende Informationen zur Konfiguration von Wartungsfenstern für Azure SQL-Datenbank und Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.custom: references_regions
ms.date: 11/12/2021
ms.openlocfilehash: cea06c5731fd17b05987e2c070264588f5428345
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491372"
---
# <a name="maintenance-window-preview"></a>Wartungsfenster (Vorschau)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Die Funktion „Wartungsfenster“ ermöglicht Ihnen das Konfigurieren von Wartungszeitplänen für Ressourcen in [Azure SQL-Datenbank](sql-database-paas-overview.md) und [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), sodass beeinträchtigende Wartungsereignisse vorhersehbar werden und weniger störend für Ihre Workloads sind. 

> [!Note]
> Das Wartungsfensterfeature schützt nur vor geplanten Auswirkungen von Upgrades oder geplanten Wartungen. Es schützt jedoch nicht vor allen Failoverursachen, also Ausnahmen, die außerhalb eines Wartungsfensters zu kurzen Verbindungsunterbrechungen führen können, einschließlich Hardwarefehlern, Clusterlastenausgleichen und Neukonfigurationen von Datenbanken aufgrund von Ereignissen wie einer Änderung des Servicelevelziels der Datenbank. 

## <a name="overview"></a>Übersicht

Azure führt regelmäßig eine [geplante Wartung](planned-maintenance.md) von Ressourcen in SQL-Datenbank und Azure SQL Managed Instance durch. Während des Azure SQL-Wartungsereignisses sind Datenbanken vollständig verfügbar, können jedoch innerhalb der entsprechenden Verfügbarkeits-SLAs für [SQL-Datenbank](https://azure.microsoft.com/support/legal/sla/azure-sql-database) und [Azure SQL Managed Instance](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance) kurzen Neukonfigurationen unterliegen.

Das Wartungsfenster ist für Produktionsworkloads vorgesehen, die gegenüber Neukonfigurationen von Datenbanken oder Instanzen nicht resilient sind und keine kurzen Verbindungsunterbrechungen durch geplante Wartungsereignisse auffangen können. Durch die Auswahl eines gewünschten Wartungsfensters können Sie die Auswirkungen geplanter Wartungen minimieren, da diese außerhalb Ihrer Hauptgeschäftszeiten stattfinden. Stabile Workloads und Nicht-Produktionsworkloads sind möglicherweise von der Azure SQL-Standardwartungsrichtlinie abhängig.

Das Wartungsfenster kann bei der Erstellung oder für vorhandene Azure SQL-Ressourcen konfiguriert werden. Es kann im Azure-Portal, mit PowerShell, der Befehlszeilenschnittstelle oder der Azure-API konfiguriert werden.

> [!Important]
> Das Konfigurieren des Wartungsfensters ist ein zeitintensiver, asynchroner Vorgang, ähnlich dem Ändern der Dienstebene der Azure SQL-Ressource. Die Ressource bleibt während des Vorgangs verfügbar – mit Ausnahme einer kurzen Neukonfiguration, die am Ende des Vorgangs erfolgt und normalerweise etwa 8 Sekunden dauert (auch bei unterbrochenen zeitintensiven Transaktionen). Wenn Sie die Auswirkungen der Neukonfiguration minimieren möchten, sollten Sie den Vorgang außerhalb der Spitzenzeiten durchführen.

### <a name="gain-more-predictability-with-maintenance-window"></a>Mehr Vorhersehbarkeit bei Wartungsfenstern

Die meisten bedeutsamen Updates werden durch die Azure SQL-Wartungsrichtlinie standardmäßig **täglich zwischen 8 und 17 Uhr Ortszeit** blockiert, um Unterbrechungen während der normalen Hauptgeschäftszeiten zu vermeiden. Die Ortszeit wird durch die [Azure-Region](https://azure.microsoft.com/global-infrastructure/geographies/) bestimmt, in der die Ressource gehostet wird, und die Sommerzeit kann gemäß der Definition der lokalen Zeitzone berücksichtigt werden. 

Sie können die Wartungsupdates weiter auf eine für Ihre Azure SQL-Ressourcen geeignete Zeit anpassen, indem Sie eines der zwei zusätzlichen Wartungszeitfenster auswählen:
 
* Wartungsfenster **Wochentag**: 22 Uhr bis 6 Uhr Ortszeit, Montag bis Donnerstag
* Wartungsfenster **Wochenende**: 22 Uhr bis 6 Uhr Ortszeit, Freitag bis Sonntag

In den aufgelisteten Tagen des Wartungsfensters wird der Starttag jedes achtstündigen Wartungsfensters angegeben. „22:00 Uhr bis 6:00 Uhr Ortszeit, Montag bis Donnerstag“ bedeutet beispielsweise, dass die Wartungsfenster an jedem Tag (Montag bis Donnerstag) um 22:00 Uhr Ortszeit beginnen und am folgenden Tag (Dienstag bis Freitag) um 6:00 Uhr Ortszeit abgeschlossen sind.

Nach Auswahl des Wartungsfensters und nach Abschluss der Dienstkonfiguration werden alle geplanten Wartungen nur im von Ihnen ausgewählten Wartungsfenster ausgeführt. Wartungsereignisse werden in der Regel innerhalb eines einzelnen Fensters abgeschlossen, einige Wartungsereignisse können sich jedoch über zwei oder mehr angrenzende Fenster erstrecken.   

> [!Important]
> In sehr seltenen Fällen, in denen eine Verschiebung von Aktionen zu schwerwiegenden Auswirkungen führen könnte, wie z. B. das Anwenden eines wichtigen Sicherheitspatches, kann das konfigurierte Wartungsfenster vorübergehend außer Kraft gesetzt werden. 

### <a name="cost-and-eligibility"></a>Kosten und Berechtigungen

Das Konfigurieren und Verwenden des Wartungsfensters ist für alle geeigneten [Angebotstypen](https://azure.microsoft.com/support/legal/offer-details/) (Nutzungsbasierte Bezahlung, Cloud Solution Provider (CSP), Microsoft Enterprise Agreement oder Microsoft-Kundenvereinbarung) kostenlos.

> [!Note]
> Ein Azure-Angebot ist der Typ von Azure-Abonnement, das Sie besitzen. Zum Beispiel handelt es sich bei Abonnements des Typs [Nutzungsbasierte Bezahlung (Pay-As-You-Go)](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) und [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) insgesamt um Azure-Angebote. Jedes Angebot bzw. jeder Plan hat unterschiedliche Vorteile und unterliegt unterschiedlichen Bestimmungen. Ihr Angebot bzw. Ihr Plan wird in der Abonnementsübersicht angezeigt. Wie Sie mit Ihrem Abonnement zu einem anderen Angebot wechseln können, erfahren Sie unter [Ändern Ihres Azure-Abonnements in ein anderes Angebot](../../cost-management-billing/manage/switch-azure-offer.md).

## <a name="advance-notifications"></a>Vorabbenachrichtigungen

Wartungsbenachrichtigungen können so konfiguriert werden, dass Sie über bevorstehende geplante Wartungsereignisse für Ihre Azure SQL-Datenbank 24 Stunden im Voraus, zum Zeitpunkt der Wartung und nach Abschluss der Wartung benachrichtigt werden. Weitere Informationen finden Sie unter [Vorabbenachrichtigungen](advance-notifications.md).

## <a name="availability"></a>Verfügbarkeit

### <a name="supported-service-level-objectives"></a>Unterstützte Servicelevelziele

Die Auswahl eines anderen Wartungsfensters anstelle des Standardwartungsfensters ist **mit folgenden Ausnahmen** für alle Servicelevelziele (Service Level Objectives, SLOs) verfügbar:
* Instanzenpools
* Legacy Gen4 vCore
* Basis, S0 und S1 
* DC, Fsv2, M-Serie

### <a name="azure-region-support"></a>Unterstützte Azure-Regionen

Die Auswahl eines anderen Wartungsfensters anstelle des Standardwartungsfensters ist derzeit in folgenden Regionen verfügbar:

| Azure-Region | Verwaltete SQL-Instanz | SQL-Datenbank | SQL-Datenbank in einer [Azure-Verfügbarkeitszone](high-availability-sla.md) | 
|:---|:---|:---|:---|
| Australien, Mitte 1 | Ja | | |
| Australien, Mitte 2 | Ja | | |
| Australien (Osten) | Ja | Ja | Ja |
| Australien, Südosten | Ja | Ja | |
| Brasilien Süd | Ja | Ja |  |
| Kanada, Mitte | Ja | Ja | Ja |
| Kanada, Osten | Ja | Ja | |
| Indien, Mitte | Ja | Ja | |
| USA (Mitte) | Ja | Ja | Ja |
| China, Osten 2 |Ja | Ja ||
| China, Norden 2 |Ja|Ja ||
| East US | Ja | Ja | Ja |
| USA (Ost) 2 | Ja | Ja | Ja |
| Asien, Osten | Ja | Ja | |
| Frankreich, Mitte | Ja | Ja | |
| Frankreich, Süden | Ja | Ja | |
| Deutschland, Westen-Mitte | Ja | Ja |  |
| Deutschland, Norden | Ja |  |  |
| Japan, Osten | Ja | Ja | Ja |
| Japan, Westen | Ja | Ja | |
| Korea, Mitte | Ja | | |
| Korea, Süden | Ja | | |
| USA Nord Mitte | Ja | Ja | |
| Nordeuropa | Ja | Ja | Ja |
| Südafrika, Norden | Ja | | | 
| Südafrika, Westen | Ja | | | 
| USA Süd Mitte | Ja | Ja | Ja |
| Indien (Süden) | Ja | Ja | |
| Asien, Südosten | Ja | Ja | Ja |
| Schweiz, Norden | Ja | Ja | |
| Schweiz, Westen | Ja | | |
| VAE, Mitte | Ja | | |
| Vereinigte Arabische Emirate, Norden | Ja | | |
| UK, Süden | Ja | Ja | Ja |
| UK, Westen | Ja | Ja | |
| USA, Westen-Mitte | Ja | Ja | |
| Europa, Westen | Ja | Ja | Ja |
| Indien, Westen | Ja | | |
| USA (Westen) | Ja | Ja |  |
| USA, Westen 2 | Ja | Ja | Ja |
| | | | | 

## <a name="gateway-maintenance-for-azure-sql-database"></a>Gatewaywartung für Azure SQL-Datenbank

Für die optimale Nutzung von Wartungsfenstern müssen Sie sicherstellen, dass Ihre Clientanwendungen die Verbindungsrichtlinie „Umleiten“ verwenden. Empfohlen wird die Verbindungsrichtlinie „Umleiten“, da die Clients Verbindungen direkt mit dem Knoten herstellen, der die Datenbank hostet. Dies führt zu niedrigerer Latenz und verbessertem Durchsatz.  

* In Azure SQL-Datenbank sind möglicherweise alle Verbindungen, die die Verbindungsrichtlinie „Proxy“ verwenden, von dem ausgewählten Wartungsfenster und einem Wartungsfenster für den Gatewayknoten betroffen. Dagegen sind Clientverbindungen, die die empfohlene Verbindungsrichtlinie „Umleiten“ verwenden, bei der Wartung des Gatewayknotens nicht von einer Neukonfiguration betroffen. 

* In Azure SQL Managed Instance werden die Gatewayknoten [im virtuellen Cluster](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) gehostet, und für sie gilt das gleiche Wartungsfenster wie für die verwaltete Instanz. Es wird jedoch trotzdem empfohlen, die Verbindungsrichtlinie „Umleiten“ zu verwenden, um die Anzahl der Unterbrechungen während des Wartungsereignisses zu minimieren.

Weitere Informationen zur Clientverbindungsrichtlinie in Azure SQL-Datenbank finden Sie unter [Azure SQL-Datenbank: Verbindungsrichtlinie](../database/connectivity-architecture.md#connection-policy). 

Weitere Informationen zur Clientverbindungsrichtlinie in Azure SQL Managed Instance finden Sie unter [Azure SQL Managed Instance: Verbindungstypen](../../azure-sql/managed-instance/connection-types-overview.md).

## <a name="considerations-for-azure-sql-managed-instance"></a>Hinweise zu Azure SQL Managed Instance

Azure SQL Managed Instance besteht aus Dienstkomponenten, die auf dedizierten isolierten virtuellen Computern gehostet werden, die wiederum im VNet-Subnetz des Kunden ausgeführt werden. Diese virtuellen Computer bilden [virtuelle Cluster](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture), in denen mehrere verwaltete Instanzen gehostet werden können. Das für die Instanzen eines Subnetzes konfigurierte Wartungsfenster kann Einfluss auf die Anzahl der virtuellen Cluster innerhalb des Subnetzes und auf die Verteilung von Instanzen zwischen virtuellen Clustern und Verwaltungsvorgänge für virtuelle Cluster haben. Dies kann die Berücksichtigung einiger Auswirkungen erfordern.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>Die Konfiguration des Wartungsfensters ist ein zeitintensiver Vorgang 
Für alle in einem virtuellen Cluster gehosteten Instanzen wird dasselbe Wartungsfenster verwendet. Standardmäßig werden alle verwalteten Instanzen im virtuellen Cluster mit dem Standardwartungsfenster gehostet. Wenn Sie während oder nach der Erstellung ein anderes Wartungsfenster für eine verwaltete Instanz angeben, muss sie im virtuellen Cluster mit dem entsprechenden Wartungsfenster platziert werden. Wenn kein solcher virtueller Cluster im Subnetz vorhanden ist, muss zuerst ein neuer Cluster erstellt werden, in dem die Instanz gehostet wird. Wenn Sie dem vorhandenen virtuellen Cluster eine zusätzliche Instanz hinzufügen möchten, muss möglicherweise die Clustergröße geändert werden. Beide Vorgänge wirken sich auf die Dauer der Konfiguration des Wartungsfensters für eine verwaltete Instanz aus.
Eine Orientierungshilfe zur Berechnung der erwarteten Dauer der Konfiguration des Wartungsfensters für eine verwaltete Instanz finden Sie unter [Geschätzte Dauer von Verwaltungsvorgängen für Instanzen](../managed-instance/management-operations-overview.md#duration).

> [!Important]
> Am Ende des Wartungsvorgangs erfolgt eine kurze Neukonfiguration, die normalerweise bis zu 8 Sekunden dauert (auch bei unterbrochenen zeitintensiven Transaktionen). Wenn Sie die Auswirkungen der Neukonfiguration minimieren möchten, sollten Sie den Vorgang außerhalb der Spitzenzeiten planen.

### <a name="ip-address-space-requirements"></a>Anforderungen an den IP-Adressraum
Für jeden neuen virtuellen Cluster im Subnetz sind zusätzliche IP-Adressen gemäß der [IP-Adresszuordnung des virtuellen Clusters](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size) erforderlich. Wenn Sie das Wartungsfenster für eine vorhandene verwaltete Instanz ändern möchten, ist auch [vorübergehend zusätzliche IP-Kapazität](../managed-instance/vnet-subnet-determine-size.md#update-scenarios) erforderlich – wie im Szenario „Skalieren von virtuellen Kernen“ für die entsprechende Dienstebene.

### <a name="ip-address-change"></a>Änderung der IP-Adresse
Das Konfigurieren und Ändern des Wartungsfensters führt dazu, dass die IP-Adresse der Instanz innerhalb des IP-Adressbereichs des Subnetzes geändert wird.

> [!Important]
>  Stellen Sie sicher, dass der Datenverkehr nach der Änderung der IP-Adresse nicht durch NSG- und Firewallregeln blockiert wird. 

### <a name="serialization-of-virtual-cluster-management-operations"></a>Serialisierung von Verwaltungsvorgängen für virtuelle Cluster

Vorgänge, die sich auf den virtuellen Cluster auswirken, z. B. Dienstupgrades und die Änderung der Größe virtueller Cluster (Hinzufügen neuer oder Entfernen nicht benötigter Computeknoten) werden serialisiert. Anders ausgedrückt: Ein neuer Verwaltungsvorgang für virtuelle Cluster kann erst gestartet werden, nachdem der vorherige beendet wurde. Wenn das Wartungsfenster geschlossen wird, bevor der laufende Dienstupgrade- oder Wartungsvorgang abgeschlossen ist, werden alle anderen in der Zwischenzeit übermittelten Verwaltungsvorgänge für virtuelle Cluster zurückgehalten, bis das nächste Wartungsfenster geöffnet und der Dienstupgrade- oder Wartungsvorgang abgeschlossen ist. Ein Wartungsvorgang für einen virtuellen Cluster dauert üblicherweise nicht länger als ein einzelnes Fenster. Dies kann jedoch bei sehr komplexen Wartungsvorgängen der Fall sein.

Die Serialisierung von Verwaltungsvorgängen für virtuelle Cluster ist ein allgemeines Verhalten, das auch für die Standardwartungsrichtlinie gilt. Wenn ein Wartungsfensterplan konfiguriert ist, kann der Zeitraum zwischen zwei angrenzenden Fenstern einige Tage betragen. Übermittelte Vorgänge können ebenfalls mehrere Tage lang zurückhalten werden, wenn sich der Wartungsvorgang auf zwei Fenster erstreckt. Dies ist sehr selten, die Erstellung neuer Instanzen oder das Ändern der Größe von vorhandenen Instanzen (wenn zusätzliche Computeknoten erforderlich sind) kann aber während dieses Zeitraums blockiert werden.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren von Wartungsfenstern](maintenance-window-configure.md)
* [Vorabbenachrichtigungen](advance-notifications.md)

## <a name="learn-more"></a>Weitere Informationen

* [Wartungsfenster – Häufig gestellte Fragen](maintenance-window-faq.yml)
* [Azure SQL-Datenbank](sql-database-paas-overview.md) 
* [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
* [Planen von Azure-Wartungsereignissen in Azure SQL-Datenbank und Azure SQL Managed Instance](planned-maintenance.md)
