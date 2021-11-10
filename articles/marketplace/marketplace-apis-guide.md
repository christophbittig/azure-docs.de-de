---
title: Anpassen Ihrer Geschäftsvorgänge an unsere E-Commerce-Plattform und Azure Marketplace
description: Passen Sie Ihre Geschäftsvorgänge an unsere E-Commerce-Plattform (Azure Marketplace) an.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: MarlEva
ms.author: maevan
ms.date: 05/13/2021
ms.openlocfilehash: ee5613a316d1a45e296ca25af759590192342c3e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057402"
---
# <a name="align-your-business-with-our-e-commerce-platform"></a>Anpassen Ihrer Geschäftsvorgänge an unsere E-Commerce-Plattform

In diesem Artikel wird beschrieben, wie die Benutzeroberfläche (UI) des kommerziellen Marketplace und programmgesteuerten APIs (Anwendungsprogrammierschnittstellen) gemeinsam Ihre Geschäftsprozesse unterstützen. Die Links unter der API verweisen auf die spezifischen Schnittstellen, mit denen Entwickler ihr CRM-System in den kommerziellen Marketplace integrieren können.

## <a name="overview-of-activities"></a>Übersicht über Aktivitäten

Die folgenden Aktivitäten sind nicht nach Reihenfolge aufgeführt. Die von Ihnen verwendete Aktivität hängt von Ihren Geschäftsanforderungen und Vertriebsprozessen ab. In diesem Leitfaden erfahren Sie, wie Sie verschiedene APIs zum Automatisieren der einzelnen Aktivitäten integrieren.

| <center>Aktivität | ISV-Vertriebsaktivitäten | Entsprechende Marketplace-API | Entsprechende Marketplace-UI |
| --- | --- | --- | --- |
| <center>**1. Produktmarketing**<br><img src="media/api-guide/icon-product-marketing.png" alt="The icon for product marketing"> | Erstellen von Messaging, Positionierung, Werbung und Preisen für Produkte | Erstellen von Messaging, Positionierung, Werbung und Preisen für Produkte<br>[Partner Ingestion-API](https://apidocs.microsoft.com/services/partneringestion/)<br>[Onboarding-API für Azure-Apps](azure-app-apis.md)</ul> | Erstellen von Messaging, Positionierung, Werbung und Preisen für Produkte<br>Partner Center (PC) → Angebotserstellung |
| <center>**2. Generierung von Nachfrage**<br><img src="media/api-guide/icon-demand-generation.png" alt="The icon for demand generation"> | Produktwerbung<br>Leadpflege<br>Auswertung, Test und Proof of Concept | Produktwerbung<br>Leadpflege<br>Auswertung, Test und Proof of Concept<br>[Lead-CRM-Connector für D365, SFDC and Marketo](partner-center-portal/commercial-marketplace-get-customer-leads.md)<br>[Co-Selling-Connector für SalesForce-CRM](/partner-center/connector-salesforce)<br>[Co-Selling-Connector für Dynamics 365-CRM](/partner-center/connector-dynamics) | Produktwerbung<br>Leadpflege<br>Auswertung, Test und Proof of Concept<br>Azure Marketplace und AppSource<br>PC: Marketplace-Erkenntnisse<br>PC: Co-Selling-Verkaufschancen |
| <center>**3. Aushandlung und Angebotserstellung**<br><img src="media/api-guide/icon-negotiation-quote-creation.png" alt="The icon for negotiation and quote creation"> | Bedingungen<br>Preise<br>Rabattgenehmigungen<br>Abschließendes Angebot | Bedingungen<br>Preise<br>Rabattgenehmigungen<br>Abschließendes Angebot<br>[CPP-API für VMs](cloud-partner-portal-api-overview.md)<br>[Microsoft Graph für AAD-API](../active-directory/reports-monitoring/concept-reporting-api.md)<br>[Partner Center-API-Familie „7“](https://apidocs.microsoft.com/services/partnercenter) | Bedingungen<br>Preise<br>Rabattgenehmigungen<br>Abschließendes Angebot<br>PC → Pläne (öffentlich oder privat) |
| <center>**4. Verkauf**<br><img src="media/api-guide/icon-sale.png" alt="The icon for sale"> | Vertragsunterzeichnung<br>Umsatzrealisierung<br>Rechnungsstellung<br>Abrechnung | Vertragsunterzeichnung<br>Umsatzrealisierung<br>Rechnungsstellung<br>Abrechnung<br>[SaaS-Fulfillment-API v.2](partner-center-portal/pc-saas-fulfillment-apis.md)<br>[Berichterstellungs-APIs](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf) | Vertragsunterzeichnung<br>Umsatzrealisierung<br>Rechnungsstellung<br>Abrechnung<br>Azure-Portal/Admin Center<br>PC: Marketplace-Prämien<br>PC: Auszahlungsberichte<br>PC: Marketplace-Analysen<br>PC: Co-Selling-Abschluss |
| <center>**5. Betreuung**<br><img src="media/api-guide/icon-maintenance.png" alt="The icon for maintenance"> | Wiederkehrende Abrechnung<br>Überschreitungen<br>Produktsupport | Wiederkehrende Abrechnung<br>Überschreitungen<br>Produktsupport<br>[SaaS/AMAs: Abrechnungs-APIs](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf)<br>[SaaS-Fulfillment-API v.2](partner-center-portal/pc-saas-fulfillment-apis.md)<br>[Partnerauszahlungs-API](https://apidocs.microsoft.com/services/partnerpayouts) <br>[github](https://github.com/microsoft/Partner-Center-Payout-APIs)<br>[API für getaktete Abrechnung](marketplace-metering-service-apis.md)<br>[(EA-Kunde) Azure-Nutzungs-API](/rest/api/consumption/)<br>[(EA-Kunde) Azure-Gebührenlisten-API](/rest/api/consumption/charges/list) | Wiederkehrende Abrechnung<br>Überschreitungen<br>Produktsupport<br>PC: Auszahlungsberichte<br>PC: Marketplace-Analysen |
| <center>**6. Vertragsende**<br><img src="media/api-guide/icon-contract-end.png" alt="The icon for contract end"> | Verlängern oder<br>Terminate |Verlängern oder<br>Terminate <br>[SaaS-Fulfillment-API v.2](./partner-center-portal/pc-saas-fulfillment-apis.md)<br>AMA/VMs: automatische Verlängerung | Verlängern oder<br>Terminate<br>PC: Marketplace-Analysen |
|

## <a name="next-steps"></a>Nächste Schritte

- Klicken Sie bei Bedarf oben auf die Links für die einzelnen APIs.