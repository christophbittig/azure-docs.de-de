---
title: Azure Front Door Standard-/Premium-Abonnementangebote und Bandbreiteneinschränkung
description: Erfahren Sie mehr über die Verfügbarkeit von Azure Front Door standard/Premium für einen bestimmten Abonnementtyp.
services: front-door
author: duongau
manager: kumud
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 08/20/2021
ms.author: duau
ms.openlocfilehash: d98d69e616910d421b2b8bed5ea305039a0f13b2
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635239"
---
# <a name="azure-front-door-standardpremium-subscription-offers-and-bandwidth-throttling"></a>Azure Front Door Standard-/Premium-Abonnementangebote und Bandbreiteneinschränkung

Die Bandbreiteneinschränkung wird je nach Abonnementtyp auf Azure Front Door Standard/Premium-Profile angewendet.

## <a name="free-and-trial-subscription"></a>Kostenloses Abonnement und Testabonnement

Für diesen Abonnementtyp gilt Bandbreitendrosselung.

## <a name="pay-as-you-go"></a>Nutzungsbasierte Bezahlung

Die Bandbreite für Azure CDN von Microsoft und Verizon wird gedrosselt, bis bestätigt wird, dass mit Ihrem Abonnement alles in Ordnung ist und der Zahlungsverlauf ordnungsgemäß ist. Der Prozess zum Bestimmen des Abonnementstatus und zum Entfernen der Drosselung erfolgt automatisch nach Erhalt der ersten Zahlung.

Wenn Sie eine Zahlung getätigt haben und die Drosselung nicht entfernt wurde, können Sie dies über den [Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) anfordern.

## <a name="enterprise-agreements"></a>Enterprise Agreements

Für Enterprise Agreement-Abonnements gelten keine Bandbreiteneinschränkungen.

## <a name="other-offer-types"></a>Andere Angebotstypen

Für die folgenden Arten von Vereinbarungen gilt die gleiche Funktionalität wie bei nutzungsbasierter Bezahlung:

* Visual Studio
* MSDN
* Schüler und Studenten
* CSP

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Erstellen eines Profils für Azure Front Door Standard/Premium](create-front-door-portal.md).
