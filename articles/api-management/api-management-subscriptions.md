---
title: Abonnements in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Konzept von Abonnements in Azure API Management. Consumer erhalten Zugriff auf APIs mithilfe von Abonnements in Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/27/2021
ms.author: apimpm
ms.openlocfilehash: 9bbdc914eae50a7226b22952b40cdb4b28849239
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111668"
---
# <a name="subscriptions-in-azure-api-management"></a>Abonnements in Azure API Management

In Azure API Management sind Abonnements die übliche Methode, durch die API-Nutzer Zugriff auf APIs erhalten, die über eine API Management-Instanz veröffentlicht werden. Dieser Artikel bietet eine Übersicht über das Konzept.

## <a name="what-are-subscriptions"></a>Was sind Abonnements?

Durch die Veröffentlichung von APIs über API Management können Sie den API-Zugriff ganz einfach mithilfe von Abonnementschlüsseln absichern. Nutzen Sie die veröffentlichten APIs, indem Sie beim Aufrufen dieser APIs einen gültigen Abonnementschlüssel in die HTTP-Anforderungen einschließen. Ohne gültigen Abonnementschlüssel geschieht mit Aufrufen Folgendes:
* Sie werden sofort vom API Management-Gateway abgelehnt. 
* Sie werden nicht an die Back-End-Dienste weitergeleitet.

Für den Zugriff auf APIs benötigen Sie ein Abonnement und einen Abonnementschlüssel. Ein *Abonnement* ist ein benannter Container für ein Abonnementschlüsselpaar. 

Das regelmäßige Neugenerieren von Schlüsseln ist eine gängige Sicherheitsvorkehrung. Daher werden Schlüssel von den meisten Azure-Produkten, die einen Abonnementschlüssel erfordern, paarweise generiert. Jede Anwendung, die den Dienst verwendet, kann von *Schlüssel A* zu *Schlüssel B* wechseln und Schlüssel A mit minimaler Unterbrechung neu generieren und umgekehrt. 

Außerdem:

* Entwickler können Abonnements ohne Genehmigung von API-Herausgebern abrufen. 
* API-Herausgeber können Abonnements für API-Nutzer direkt erstellen.

> [!TIP]
> API Management unterstützt auch andere Mechanismen zum Schützen des Zugriffs auf APIs, einschließlich der folgenden Beispiele:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Clientzertifikate](api-management-howto-mutual-certificates-for-clients.md)
> - [Beschränkung für Aufrufer-IP](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Bereich von Abonnements

Abonnements können verschiedenen Bereichen zugeordnet werden: einem Produkt, allen APIs oder einer bestimmten API.

### <a name="subscriptions-for-a-product"></a>Abonnements für ein Produkt

Ursprünglich wurden Abonnements in API Management einem einzelnen [API-Produkt](api-management-terminology.md) als Bereich zugeordnet. Entwickler:
* Fanden die Liste der Produkte im Entwicklerportal. 
* Sendeten Abonnementanforderungen für die Produkte, die sie verwenden wollten. 
* Verwendeten die Schlüssel in diesen Abonnements (entweder automatisch oder von API-Herausgebern genehmigt), um auf alle APIs im Produkt zuzugreifen. 
    * Sie können unabhängig vom Abonnementbereich (Produkt, global oder API) mit oder ohne Abonnementschlüssel auf APIs zugreifen.

Derzeit zeigt das Entwicklerportal nur die Abonnements auf Produktebene im Abschnitt **Benutzerprofil** an. 

> [!NOTE]
> Wenn Sie einen Abonnementschlüssel im API-Bereich verwenden, werden *Richtlinien*, die im Produktbereich konfiguriert wurden, nicht auf dieses Abonnement angewendet.

![Produktabonnements](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> In bestimmten Szenarien möchten API-Herausgeber vielleicht ein API-Produkt für die Öffentlichkeit veröffentlichen, ohne dass hierzu Abonnements erforderlich sind. Sie können hierzu im Azure-Portal auf der Seite **Einstellungen** die Option **Abonnement erforderlich** deaktivieren. Als Ergebnis kann auf alle APIs für das Produkt ohne einen API-Schlüssel zugegriffen werden.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Abonnements für alle APIs oder eine einzelne API

Durch Hinzufügen des API Management-Tarifs [Verbrauch](https://aka.ms/apimconsumptionblog) lässt sich die Verwaltung von Abonnementschlüsseln optimieren. 

#### <a name="two-more-subscription-scopes"></a>Zwei weitere Abonnementbereiche

Da Abonnementbereiche jetzt nicht mehr auf ein API-Produkt beschränkt sind, können Sie Schlüssel erstellen, die Zugriff auf eine der beiden folgenden Optionen gewähren:
* Einzelne API 
* Alle APIs innerhalb einer API Management-Instanz 

Sie müssen kein Produkt erstellen, bevor Sie APIs hinzufügen. 

Zu jeder API Management-Instanz gehört nun ein unveränderliches Abonnement für alle APIs. Dieses Abonnement erleichtert das Testen und Debuggen von APIs in der Testkonsole.

#### <a name="standalone-subscriptions"></a>Eigenständige Abonnements

API Management lässt jetzt *eigenständige* Abonnements zu. Sie müssen Abonnements nicht mehr einem Entwicklerkonto zuordnen. Dieses Feature ist nützlich in Szenarien, in denen mehrere Entwickler oder Teams ein Abonnement gemeinsam nutzen.

Wenn Sie ein Abonnement erstellen, ohne einen Besitzer zuzuweisen, wird es zu einem eigenständigen Abonnement. Um Entwicklern und dem Rest Ihres Teams Zugriff auf den eigenständigen Abonnementschlüssel zu gewähren, führen Sie einen der folgenden Schritte aus:
* Teilen Sie den Abonnementschlüssel manuell.
* Verwenden Sie ein benutzerdefiniertes System, um den Abonnementschlüssel für Ihr Team verfügbar zu machen.

#### <a name="creating-subscriptions-in-azure-portal"></a>Erstellen von Abonnements im Azure-Portal

API-Herausgeber können jetzt direkt im Azure-Portal [Abonnements erstellen](api-management-howto-create-subscriptions.md):

![Flexible Abonnements](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu API Management:

+ Informationen zu weiteren [Konzepten](api-management-terminology.md) in API Management
+ [Tutorials](import-and-publish.md) zum weiteren Kennenlernen von API Management
+ [FAQ-Seite](api-management-faq.yml) für häufig gestellte Fragen
