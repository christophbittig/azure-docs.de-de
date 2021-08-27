---
title: Datei einfügen
description: include file
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/17/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 5c4a4ab57e2e4a63579cc753e183cd9cecc4b765
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868094"
---
Wir stellen verschiedene Vorlagen bereit, um bestimmte Onboardingszenarien zu behandeln. Wählen Sie die am besten geeignete Option aus, und achten Sie darauf, die Parameterdatei an Ihre Umgebung anzupassen. Weitere Informationen zur Verwendung dieser Dateien in der Bereitstellung finden Sie unter [Onboarding eines Kunden für Azure Lighthouse durchführen](../articles/lighthouse/how-to/onboard-customer.md).

| **Vorlage** | **Beschreibung** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Onboarding eines Kundenabonnements in Azure Lighthouse durchführen Für jedes Abonnement muss eine separate Bereitstellung durchgeführt werden. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg) | Führt das Onboarding einer oder mehrerer Ressourcengruppen eines Kunden für Azure Lighthouse durch. Verwenden Sie **rgDelegatedResourceManagement**  für eine einzelne Ressourcengruppe oder **multipleRgDelegatedResourceManagement**, um das Onboarding für mehrere Ressourcengruppen im selben Abonnement durchzuführen. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Wenn Sie [ein Angebot für verwaltete Dienste im Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md) veröffentlicht haben, können Sie diese Vorlage optional zum Onboarding von Ressourcen für Kunden verwenden, die das Angebot angenommen haben. Die Marketplace-Werte in der Parameterdatei müssen mit den Werten identisch sein, die Sie beim Veröffentlichen Ihres Angebots verwendet haben. |

Wenn Sie [berechtigte Autorisierungen](../articles/lighthouse/how-to/create-eligible-authorizations.md) (derzeit als Public Preview verfügbar) aufnehmen möchten, wählen Sie im Abschnitt [delegated-resource-management-eligible-authorizations](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) des Beispielrepositorys die entsprechende Vorlage aus.

In der Regel ist für jedes Abonnement, für das ein Onboarding durchgeführt wird, eine separate Bereitstellung erforderlich. Sie können jedoch auch Vorlagen für mehrere Abonnements bereitstellen.

| **Vorlage** | **Beschreibung** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Stellt Azure Resource Manager-Vorlagen in mehreren Abonnements bereit. |


