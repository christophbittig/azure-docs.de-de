---
title: 'Übersicht: Zugriff auf virtuelle Azure-Netzwerke'
description: Hier erfahren Sie, wie Sie mithilfe einer Integrationsdienstumgebung (ISE) von Azure Logic Apps aus auf virtuelle Azure-Netzwerke (VNETs) zugreifen können.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/16/2021
ms.openlocfilehash: 6abcc030d77f9b7d06f9d5f43d32611a0670053b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162375"
---
# <a name="access-to-azure-virtual-networks-from-azure-logic-apps-using-an-integration-service-environment-ise"></a>Zugreifen auf virtuelle Azure-Netzwerken aus Azure Logic Apps mithilfe einer Integrationsdienstumgebung (ISE)

Manchmal benötigen Ihre Logik-App-Flows Zugriff auf geschützte Ressourcen wie virtuelle Computer (virtual machines, VMs) und andere Systeme oder Dienste, die sich innerhalb eines virtuellen Azure-Netzwerks befinden oder damit verbunden sind. Um direkt aus Workflows heraus auf diese Ressourcen zuzugreifen, die in der Regel in mehrinstanzenfähigen Azure Logic Apps ausgeführt werden, können Sie Ihre Logik-Apps stattdessen in einer *Integrationsdienstumgebung* (ISE) erstellen und ausführen. Eine ISE ist eigentlich eine Instanz von Azure Logic Apps, die unabhängig von der globalen mehrinstanzenfähigen Azure-Umgebung separat auf dedizierten Ressourcen ausgeführt wird und keine [Daten außerhalb der Region speichert, verarbeitet oder repliziert, in der Sie die ISE bereitstellen](https://azure.microsoft.com/global-infrastructure/data-residency#select-geography).

Einige virtuelle Azure-Netzwerke verwenden beispielsweise private Endpunkte ([Azure Private Link](../private-link/private-link-overview.md)), um den Zugriff auf Azure-PaaS-Dienste wie Azure Storage, Azure Cosmos DB oder Azure SQL-Datenbank sowie auf Partnerdienste oder auf Kundendienste zu ermöglichen, die in Azure gehostet werden. Wenn Ihre Logik-App-Workflows Zugriff auf virtuelle Netzwerke erfordern, die private Endpunkte verwenden, haben Sie die folgenden Optionen:

* Wenn Sie Workflows mithilfe des Ressourcentyps **Logik-App (Verbrauch)** entwickeln möchten und Ihre Workflows private Endpunkte verwenden müssen, *müssen* Sie Ihre Logik-Apps in einer Integrationsdienstumgebung erstellen und ausführen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit virtuellen Azure-Netzwerken in Azure Logic Apps mithilfe einer Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Wenn Sie Workflows mit dem Ressourcentyp **Logik-App (Standard)** entwickeln möchten und Ihre Workflows private Endpunkte verwenden müssen, benötigen Sie keine ISE. Stattdessen können Ihre Workflows privat und sicher mit virtuellen Netzwerken kommunizieren, indem für eingehenden Datenverkehr private Endpunkte und für ausgehenden Datenverkehr die Integration virtueller Netzwerke verwendet werden. Weitere Informationen finden Sie unter [Schützen des Datenverkehrs zwischen virtuellen Netzwerken und Workflows mit nur einem Mandanten in Azure Logic Apps mithilfe privater Endpunkte](secure-single-tenant-workflow-virtual-network-private-endpoint.md).

Weitere Informationen finden Sie in den [Unterschieden zwischen mehrinstanzenfähigen Azure Logic Apps und Integrationsdienstumgebungen](logic-apps-overview.md#resource-environment-differences).

## <a name="how-an-ise-works-with-a-virtual-network"></a>Funktionsweise einer ISE mit einem virtuellen Netzwerk

Wenn Sie eine ISE erstellen, wählen Sie das virtuelle Azure-Netzwerk aus, in das Azure Ihre ISE *einschleusen* bzw. darin bereitstellen soll. Wenn Sie Logik-Apps und Integrationskonten erstellen, die Zugriff auf dieses virtuelle Netzwerk benötigen, können Sie Ihre ISE als Hoststandort für diese Logik-Apps und Integrationskonten auswählen. Innerhalb der ISE werden Logik-Apps auf dedizierten Ressourcen getrennt von anderen in der mehrinstanzenfähigen Azure Logic Apps-Umgebung ausgeführt. Daten in einer ISE verbleiben in [derselben Region, in der Sie diese ISE erstellen und bereitstellen](https://azure.microsoft.com/global-infrastructure/data-residency/).

![Auswählen der Integrationsdienstumgebung](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Für mehr Kontrolle über die von Azure Storage verwendeten Verschlüsselungsschlüssel können Sie mit [Azure Key Vault](../key-vault/general/overview.md) Ihren eigenen Schlüssel einrichten, verwenden und verwalten. Diese Funktion wird auch als „Bring Your Own Key“ (BYOK) bezeichnet, und Ihr Schlüssel wird als „vom Kunden verwalteter Schlüssel“ bezeichnet. Weitere Informationen finden Sie unter [Einrichten von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von ruhenden Daten für Integrationsdienstumgebungen (Integration Service Environment, ISE) in Azure Logic Apps](../logic-apps/customer-managed-keys-integration-service-environment.md).

Diese Übersicht enthält weitere Informationen zu den [Gründen, warum Sie eine ISE verwenden sollten](#benefits), zu den [Unterschieden zwischen dem dedizierten und dem mehrinstanzenfähigen Logic Apps-Dienst](#difference) und wie Sie direkt auf Ressourcen zugreifen können, die sich in Ihrem virtuellen Azure-Netzwerk befinden bzw. mit diesem verbunden sind.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Gründe für die Verwendung einer ISE

Das Ausführen von Logik-Apps als eigene separate dedizierte Instanz trägt dazu bei, mögliche Auswirkungen anderer Azure-Mandanten auf die Leistung Ihrer Apps zu verringern. Dies ist auch als [„Noisy Neighbors“-Problem](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) bekannt. Eine ISE bietet darüber hinaus die folgenden Vorteile:

* Direkter Zugriff auf Ressourcen, die sich in Ihrem virtuellen Netzwerk befinden oder mit diesem verbunden sind.

  Logik-Apps, die Sie in einer ISE erstellen und ausführen, können [speziell entworfene Connectors verwenden, die in ihrer ISE ausgeführt werden](../connectors/managed.md#ise-connectors). Wenn ein ISE-Connector für ein lokales System oder eine lokale Datenquelle vorhanden ist, können Sie direkt eine Verbindung damit herstellen, ohne das [lokale Datengateway](../logic-apps/logic-apps-gateway-connection.md) verwenden zu müssen. Weitere Informationen finden Sie unter [Dediziert im Vergleich zu mehrinstanzenfähig](#difference) und [Zugriff auf lokale Systeme](#on-premises) weiter unten in diesem Thema.

* Fortwährender Zugriff auf Ressourcen, die sich außerhalb Ihres virtuellen Netzwerks befinden oder nicht mit diesem verbunden sind.

  Logik-Apps, die Sie in einer ISE erstellen und ausführen, können weiterhin Connectors verwenden, die in dem mehrinstanzenfähigen Logic Apps-Dienst ausgeführt werden, wenn ein ISE-spezifischer Connector nicht verfügbar ist. Weitere Informationen finden Sie unter [Dediziert im Vergleich zu mehrinstanzenfähig](#difference).

* Ihre eigenen statischen IP-Adressen. Diese sind gesondert von den statischen IP-Adressen, die von den Logik-Apps im mehrinstanzenfähigen Dienst gemeinsam verwendet werden. Sie können auch eine einzelne öffentliche, statische und vorhersagbare ausgehende IP-Adresse für die Kommunikation mit Zielsystemen einrichten. Auf diese Weise müssen Sie nicht für jede ISE an den Zielsystemen zusätzliche Firewallzugänge einrichten.

* Höhere Grenzwerte für Ausführungsdauer, Speicheraufbewahrung, Durchsatz, Zeitlimits für HTTP-Anforderungen und -Antworten, Nachrichtengröße und benutzerdefinierte Connectoranforderungen. Weitere Informationen finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dediziert im Vergleich zu mehrinstanzenfähig

Wenn Sie Logik-Apps in einer ISE erstellen und ausführen, erhalten Sie die gleiche Benutzeroberfläche und ähnliche Funktionen wie mit dem mehrinstanzenfähigen Logic Apps-Dienst. Sie können dieselben integrierten Trigger, Aktionen und verwalteten Connectors verwenden, die im mehrinstanzenfähigen Logic Apps-Dienst verfügbar sind. Einige verwaltete Connectors bieten zusätzliche ISE-Versionen. Der Unterschied zwischen ISE-Connectors und Nicht-ISE-Connectors besteht darin, wo sie ausgeführt werden und welche Bezeichnungen im Logik-App-Designer angezeigt werden, wenn Sie in einer ISE arbeiten.

![Connectors mit und ohne Bezeichnungen in einer ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Integrierte Trigger und Aktionen wie HTTP weisen die Bezeichnung **CORE** auf und werden in derselben ISE wie Ihre Logik-App ausgeführt.

* Verwaltete Connectors, die die Bezeichnung **ISE** aufweisen, sind speziell für ISEs entworfen und werden *immer in derselben ISE wie Ihre Logik-App ausgeführt*. Im Folgenden finden Sie beispielsweise einige [Connectors, die ISE-Versionen bieten](../connectors/managed.md#ise-connectors):<p>

  * Azure Blob Storage, File Storage und Table Storage
  * Azure Service Bus, Azure Queues, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid und Azure Monitor-Protokolle
  * FTP, SFTP-SSH, Dateisystem und SMTP
  * SAP, IBM MQ, IBM DB2 und IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 und EDIFACT

  Mit nur seltenen Ausnahmen können Sie, wenn ein ISE-Connector für ein lokales System oder eine lokale Datenquelle verfügbar ist, direkt eine Verbindung damit herstellen, ohne das [lokale Datengateway](../logic-apps/logic-apps-gateway-connection.md) zu verwenden. Weitere Informationen finden Sie unter [Zugriff auf lokale Systeme](#on-premises) weiter unten in diesem Thema.

* Verwaltete Connectors, die nicht die Bezeichnung **ISE** aufweisen, funktionieren auch weiterhin für Logik-Apps innerhalb einer ISE. Diese Connectors werden *immer im mehrinstanzenfähigen Logic Apps-Dienst ausgeführt*, nicht in einer ISE.

* Benutzerdefinierte Connectors, die Sie *außerhalb einer ISE* erstellen, unabhängig davon, ob sie das [lokale Datengateway](../logic-apps/logic-apps-gateway-connection.md) benötigen oder nicht, funktionieren weiterhin für Logik-Apps innerhalb einer ISE. Benutzerdefinierte Connectors, die Sie *in einer ISE* erstellen, funktionieren jedoch nicht mit dem lokalen Datengateway. Weitere Informationen finden Sie unter [Zugriff auf lokale Systeme](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Zugriff auf lokale Systeme

Logik-Apps, die in einer ISE ausgeführt werden, können direkt auf lokale Systeme und lokale Datenquellen zugreifen, die sich innerhalb eines virtuellen Azure-Netzwerks befinden oder damit verbunden sind, indem sie diese Elemente verwenden:<p>

* Den HTTP-Trigger oder die Aktion, der/die die Bezeichnung **CORE** aufweist.

* Den **ISE**-Connector, falls verfügbar, für ein lokales System oder eine lokale Datenquelle.

  Wenn ein ISE-Connector verfügbar ist, können Sie direkt auf das System oder die Datenquelle zugreifen, ohne das [lokale Datengateway](../logic-apps/logic-apps-gateway-connection.md) zu verwenden. Auch wenn Sie aus einer ISE auf SQL Server zugreifen müssen und Windows-Authentifizierung verwenden, müssen Sie die Nicht-ISE-Version des Connectors und das lokale Datengateway verwenden. Die ISE-Version des Connectors unterstützt keine Windows-Authentifizierung. Weitere Informationen finden Sie unter [ISE-Connectors](../connectors/managed.md#ise-connectors) und [Herstellen einer Verbindung aus einer Integrationsdienstumgebung](../connectors/managed.md#integration-account-connectors).

* Ein benutzerdefinierter Connector

  * Benutzerdefinierte Connectors, die Sie *außerhalb einer ISE* erstellen, unabhängig davon, ob sie das [lokale Datengateway](../logic-apps/logic-apps-gateway-connection.md) benötigen oder nicht, funktionieren weiterhin für Logik-Apps innerhalb einer ISE.

  * Benutzerdefinierte Connectors, die Sie *in einer ISE* erstellen, funktionieren nicht mit dem lokalen Datengateway. Diese Connectors können jedoch direkt auf lokale Systeme und Datenquellen zugreifen, die sich innerhalb des virtuellen Netzwerks, das Ihre ISE hostet, befinden oder damit verbunden sind. Deshalb benötigen Logik-Apps, die sich in einer ISE befinden, in der Regel das Datengateway nicht, wenn sie auf diese Ressourcen zugreifen.

Um auf lokale Systeme und Datenquellen zuzugreifen, die über keine ISE-Connectors verfügen, sich außerhalb Ihres virtuellen Netzwerks befinden oder nicht mit Ihrem virtuellen Netzwerk verbunden sind, müssen Sie weiterhin das lokale Datengateway verwenden. Logik-Apps in einer ISE können weiterhin Connectors verwenden, die keine der Bezeichnungen **CORE** oder **ISE** aufweisen. Diese Connectors werden im mehrinstanzenfähigen Logic Apps-Dienst ausgeführt, anstatt in Ihrer ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE-SKUs

Wenn Sie Ihre ISE erstellen, können Sie die Developer-SKU oder die Premium-SKU auswählen. Diese SKU-Option ist nur bei der ISE-Erstellung verfügbar und kann später nicht mehr geändert werden. Im Folgenden finden Sie die Unterschiede zwischen den beiden SKUs:

* **Developer**

  Bietet eine kostengünstigere ISE, die Sie für die Erkundung, für Experimente, Entwicklung und Tests, jedoch nicht für Produktion oder Leistungstests verwenden können. Die Developer-SKU umfasst integrierte Trigger und Aktionen, Standardconnectors, Unternehmensconnectors und ein einzelnes [Free-Tarif](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)-Integrationskonto für einen [festen monatlichen Preis](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Diese SKU umfasst keine Vereinbarung zum Servicelevel (Service-Level Agreement, SLA), Funktion zum zentralen Hochskalieren der Kapazität oder Redundanz während der Wiederverwendung. Dies bedeutet, dass Verzögerungen oder Ausfallzeiten auftreten können. Der Dienst kann vorübergehend von Back-End-Updates unterbrochen werden.

  Informationen zu Grenzwerten und Kapazitäten finden Sie unter [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Weitere Informationen zur Abrechnung für ISEs finden Sie unter [Feststehendes Preismodell](../logic-apps/logic-apps-pricing.md#ise-pricing).

* **Premium**

  Bietet eine ISE, die Sie für Produktion und Leistungstests verwenden können. Die Premium-SKU umfasst SLA-Unterstützung, integrierte Trigger und Aktionen, Standardconnectors, Unternehmensconnectors, ein einzelnes Integrationskonto des [Standard-Tarifs](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), Hochskalierungsfunktionen und Redundanz während der Wiederverwendung für einen [festen monatlichen Preis](https://azure.microsoft.com/pricing/details/logic-apps).

  Informationen zu Grenzwerten und Kapazitäten finden Sie unter [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Weitere Informationen zur Abrechnung für ISEs finden Sie unter [Feststehendes Preismodell](../logic-apps/logic-apps-pricing.md#ise-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-Endpunktzugriff

Beim Erstellen Ihrer ISE können Sie auswählen, ob interne oder externe Zugriffsendpunkte verwendet werden sollen. Ihre Auswahl bestimmt, ob Anforderungs- oder Webhooktrigger für Logik-Apps in Ihrer ISE Aufrufe von außerhalb Ihres virtuellen Netzwerks empfangen können. Diese Endpunkte haben auch Einfluss auf die Methode, mit der Sie auf die Eingaben und Ausgaben aus dem Ausführungsverlauf Ihrer Logik-App zugreifen können.

> [!IMPORTANT]
> Sie können den Zugriffsendpunkt nur während der Erstellung der ISE auswählen und diese Option später nicht mehr ändern.

* **Intern:** Private Endpunkte, die Aufrufe von Logik-Apps in Ihrer ISE *nur innerhalb des virtuellen Netzwerks* zulassen, wo Sie auf Ein- und Ausgaben aus dem Ausführungsverlauf von Logik-Apps zugreifen können.

  > [!IMPORTANT]
  > Wenn Sie diese Webhook-basierten Trigger verwenden müssen und sich der Dienst außerhalb Ihres virtuellen Netzwerks und gepeerter virtueller Netzwerke befindet, verwenden Sie externe Endpunkte, *keine* internen Endpunkte, wenn Sie Ihre ISE erstellen:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (multi-tenant version)
  > 
  > Stellen Sie auch sicher, dass Sie über eine Netzwerkverbindung zwischen den privaten Endpunkten und dem Computer verfügen, von dem aus Sie auf den Ausführungsverlauf zugreifen möchten. Andernfalls wird beim Versuch, den Ausführungsverlauf der Logik-App anzuzeigen, Folgendes angezeigt: „Unerwarteter Fehler. Fehler beim Abrufen.“
  >
  > ![Fehler bei Azure Storage-Aktion, weil kein Datenverkehr über die Firewall gesendet werden kann](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Beispielsweise kann sich Ihr Clientcomputer im virtuellen Netzwerk der ISE oder in einem virtuellen Netzwerk befinden, das per Peering oder VPN mit dem virtuellen Netzwerk der ISE verbunden ist. 

* **Extern:** Öffentliche Endpunkte, die Aufrufe von Logik-Apps in Ihrer ISE *von außerhalb des virtuellen Netzwerks* zulassen, wo Sie auf Ein- und Ausgaben aus dem Ausführungsverlauf von Logik-Apps zugreifen können. Wenn Sie Netzwerksicherheitsgruppen (NSGs) verwenden, stellen Sie sicher, dass Sie mit eingehenden Regeln eingerichtet sind, um den Zugriff auf die Eingaben und Ausgaben des Ausführungsverlaufs zuzulassen. Weitere Informationen finden Sie unter [Aktivieren des Zugriffs für ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Um zu ermitteln, ob Ihre ISE einen internen oder externen Zugriffsendpunkt verwendet, wählen Sie im Menü Ihrer ISE unter **Einstellungen** die Option **Eigenschaften** aus, und suchen Sie die Eigenschaft **Zugriffsendpunkt**:

![ISE-Zugriffsendpunkt suchen](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Preismodell

Für Logik-Apps, integrierte Trigger, integrierte Aktionen und Connectors, die in Ihrer ISE ausgeführt werden, gilt ein fester Tarif, der von dem nutzungsbasierten Tarif abweicht. Weitere Informationen finden Sie unter [Logic Apps – Preismodell](../logic-apps/logic-apps-pricing.md#ise-pricing). Eine Preisübersicht finden Sie unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrationskonten mit ISE

In einer Integrationsdienstumgebung (ISE) haben Sie die Möglichkeit, Integrationskonten mit Logik-Apps zu verwenden. Diese Integrationskonten müssen jedoch auf *dieselbe ISE* wie die verknüpften Logik-Apps zugreifen. Logik-Apps in einer ISE können nur auf die Integrationskonten verweisen, die sich in derselben ISE befinden. Wenn Sie ein Integrationskonto erstellen, können Sie Ihre ISE als Speicherort für Ihr Integrationskonto auswählen. Informationen zur Preisgestaltung und Abrechnung für Integrationskonten mit einer ISE finden Sie unter [Integrationskonten](../logic-apps/logic-apps-pricing.md#ise-pricing). Eine Preisübersicht finden Sie unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/). Informationen zu Grenzwerten finden Sie unter [Grenzwerte für Integrationskonten](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung mit virtuellen Azure-Netzwerken über Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Erfahren Sie mehr über [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Erfahren Sie mehr über die [Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md).
