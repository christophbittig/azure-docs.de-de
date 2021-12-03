---
title: SaaS-Fulfillment-APIs im kommerziellen Microsoft-Marketplace
description: Hier finden Sie eine Einführung in die Fulfillment-APIs, mit denen Sie Ihre SaaS-Angebote in Microsoft AppSource und in den Azure Marketplace integrieren können.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: fbe417eca2e636f5af7fea2b9e200205dcc255af
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398381"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>SaaS-Fulfillment-APIs im kommerziellen Microsoft-Marketplace

Mit den SaaS-Fulfillment-APIs können Herausgeber – auch als unabhängige Softwarehersteller (Independent Software Vendors, ISVs) bezeichnet – ihre SaaS-Anwendungen in Microsoft AppSource, in Azure Marketplace und im Azure-Portal veröffentlichen und vertreiben. Mit diesen APIs können ISVs für ihre Anwendungen alle Handelskanäle nutzen: direkt, über Partner (Reseller) und über den Vertrieb. Die Integration dieser APIs ist eine Voraussetzung für das Erstellen und Veröffentlichen transaktionsfähiger SaaS-Angebote im Partner Center.

Wenn Sie mehr über die SaaS-Fulfillment-APIs erfahren möchten, empfehlen wir Ihnen, auch die folgenden Artikel zu lesen:
- [Verwalten des Lebenszyklus von SaaS-Abonnements](pc-saas-fulfillment-life-cycle.md)
- [SaaS-Fulfillment-Abonnement-APIs der Version 2](pc-saas-fulfillment-subscription-api.md)
- [SaaS-Fulfillment-Vorgangs-APIs der Version 2](pc-saas-fulfillment-operations-api.md)
- [Implementieren eines Webhooks für den SaaS-Dienst](pc-saas-fulfillment-webhook.md)
- [Häufig gestellte Fragen zu SaaS-Fulfillment-APIs](saas-fulfillment-apis-faq.yml)

## <a name="api-flows"></a>API-Flows

ISVs müssen die folgenden API-Flows implementieren und ihrem SaaS-Dienstcode hinzufügen, um sowohl für ISVs als auch für Microsoft denselben Abonnementstatus aufrechtzuerhalten:

* Flow „Angebotsseite“:  Microsoft benachrichtigt den Herausgeber, dass sein SaaS-Angebot von einem Kunden im Marketplace gekauft wurde.
* Flow „Aktivierung“:  Der Herausgeber benachrichtigt Microsoft, dass aufseiten des Herausgebers ein neu erworbenes SaaS-Konto konfiguriert wurde.
* Flow „Aktualisierung“: Änderung des erworbenen Plans oder der Anzahl erworbener Arbeitsplätze oder beides.
* Flow „Aussetzen und reaktivieren“: Das erworbene SaaS-Angebot wird ausgesetzt, falls die Zahlungsmethode des Kunden nicht mehr gültig ist. Das ausgesetzte Angebot kann reaktiviert werden, wenn das Problem mit der Zahlungsmethode behoben wurde.
* Flows „Webhook“: Microsoft benachrichtigt den Herausgeber über Änderungen und Kündigungen des SaaS-Abonnements, die der Kunde bei Microsoft veranlasst hat.

Bei einer Kündigung des erworbenen SaaS-Abonnements ist die Integration optional, da sie vom Kunden aufseiten von Microsoft vorgenommen werden kann.

Die ordnungsgemäße Integration der SaaS-Fulfillment-APIs ist wichtig, um Folgendes zu gewährleisten:

* Endkunden, die das SaaS-Angebot des Herausgebers erworben haben, werden ordnungsgemäß von Microsoft abgerechnet.
* Endkunden profitieren beim Kauf, der Konfiguration, der Nutzung und der Verwaltung der im Marketplace erworbenen SaaS-Abonnements von einer reibungslosen Benutzererfahrung.

Mit diesen APIs kann der Herausgeber alle Handelskanäle für seine Angebote nutzen:

* Direkt
* Partner (Handelspartner, CSP)
* Vertrieb

Im Szenario für Handelspartner (CSP) erwirbt ein CSP das SaaS-Angebot im Namen des Endkunden. Es wird davon ausgegangen, dass der Kunde das SaaS-Angebot nutzt, folgende Aufgaben aber in der Verantwortung des CSPs liegen:

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

- Wenn nicht bereits geschehen, registrieren Sie Ihre SaaS-Anwendung im [Azure-Portal](https://ms.portal.azure.com) wie unter [Registrieren einer Azure AD-Anwendung](./pc-saas-registration.md) erklärt.  Verwenden Sie anschließend die aktuellste Version dieser Schnittstelle für die Entwicklung: [SaaS-Fulfillment-Abonnement-APIs der Version 2](pc-saas-fulfillment-subscription-api.md) und [SaaS-Fulfillment-Vorgangs-APIs der Version 2.](pc-saas-fulfillment-operations-api.md)
