---
title: SaaS-Fulfillment-APIs im kommerziellen Microsoft-Marketplace
description: Hier finden Sie eine Einführung in die Fulfillment-APIs, mit denen Sie Ihre SaaS-Angebote in Microsoft AppSource und in den Azure Marketplace integrieren können.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: 50bb572d7fc9c05bb22c8ab35851df7a58dc2562
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455936"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>SaaS-Fulfillment-APIs im kommerziellen Microsoft-Marketplace

Mit den SaaS-Fulfillment-APIs können Herausgeber – auch als unabhängige Softwarehersteller (Independent Software Vendors, ISVs) bezeichnet – ihre SaaS-Anwendungen in Microsoft AppSource, in Azure Marketplace und im Azure-Portal veröffentlichen und vertreiben. Mit diesen APIs können ISVs für ihre Anwendungen alle Handelskanäle nutzen: direkt, über Partner (Reseller) und über den Vertrieb.  Die Integration dieser APIs ist eine Voraussetzung für das Erstellen und Veröffentlichen transaktionsfähiger SaaS-Angebote im Partner Center.

ISVs müssen die folgenden API-Flows implementieren und ihrem SaaS-Dienstcode hinzufügen, um sowohl für ISVs als auch für Microsoft denselben Abonnementstatus aufrechtzuerhalten:

* Flow „Angebotsseite“:  Microsoft benachrichtigt den Herausgeber, dass sein SaaS-Angebot von einem Kunden im Marketplace gekauft wurde.
* Flow „Aktivierung“:  Der Herausgeber benachrichtigt Microsoft, dass aufseiten des Herausgebers ein neu erworbenes SaaS-Konto konfiguriert wurde.
* Flow „Aktualisierung“: Änderung des erworbenen Plans und/oder der Anzahl erworbener Arbeitsplätze.
* Flow „Aussetzen und reaktivieren“: Das erworbene SaaS-Angebot wird ausgesetzt, falls die Zahlungsmethode des Kunden nicht mehr gültig ist. Das ausgesetzte Angebot kann reaktiviert werden, wenn das Problem mit der Zahlungsmethode behoben wurde.
* Flows „Webhook“: Microsoft benachrichtigt den Herausgeber über Änderungen und Kündigungen des SaaS-Abonnements, die der Kunde bei Microsoft veranlasst hat.

Bei einer Kündigung des erworbenen SaaS-Abonnements ist die Integration optional, da sie vom Kunden aufseiten von Microsoft vorgenommen werden kann.

Die ordnungsgemäße Integration der SaaS-Fulfillment-APIs ist wichtig, um Folgendes zu gewährleisten:

* Endkunden, die das SaaS-Angebot des Herausgebers erworben haben, werden ordnungsgemäß von Microsoft abgerechnet.
* Endkunden profitieren beim Kauf, der Konfiguration, der Nutzung und der Verwaltung der im Marketplace erworbenen SaaS-Abonnements von einer reibungslosen Benutzererfahrung.

Mit diesen APIs kann der Herausgeber alle Handelskanäle für seine Angebote nutzen:

* direkt
* Partner (Handelspartner, CSP)
* Vertrieb

Im Szenario für Handelspartner (CSP) erwirbt ein CSP das SaaS-Angebot im Namen des Endkunden. Es wird davon ausgegangen, dass der Kunde das SaaS-Angebot nutzt, folgende Abläufe aber in der Verantwortung des CSPs liegen:

* Abrechnen des Kunden
* Ändern von Abonnementplänen/der Anzahl erworbener Arbeitsplätze
* Kündigen von Abonnements

Es ist nicht erforderlich, dass der Herausgeber die Flows für API-Aufrufe für dieses Szenario auf andere Weise implementiert.

Weitere Informationen zum CSP finden Sie unter https://partner.microsoft.com/licensing.

>[!Warning]
>Die aktuelle Version dieser API ist Version 2, die für alle neuen SaaS-Angebote verwendet werden sollte. Version 1 der API ist veraltet und wird zur Unterstützung vorhandener Angebote noch gepflegt.

>[!Note]
>Die SaaS-Fulfillment-APIs müssen ausschließlich von einem Back-End-Dienst des Herausgebers aufgerufen werden. Die direkte Integration der APIs auf der Webseite des Herausgebers wird nicht unterstützt. Es sollte nur der Authentifizierungsflow von Dienst zu Dienst verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Wenn nicht bereits geschehen, registrieren Sie Ihre SaaS-Anwendung im [Azure-Portal](https://ms.portal.azure.com) wie unter [Registrieren einer Azure AD-Anwendung](./pc-saas-registration.md) erklärt.  Verwenden Sie anschließend für die Entwicklung die aktuelle Version dieser Schnittstelle: [SaaS Fulfillment API Version 2](./pc-saas-fulfillment-api-v2.md).
