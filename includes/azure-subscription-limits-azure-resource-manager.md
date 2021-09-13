---
title: Datei einfügen
description: include file
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 06/04/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 1197b15f313746c0a8542bfadeb4e0123818fca8
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575632"
---
| Resource | Begrenzung |
| --- | --- |
| [Einem Azure Active Directory-Mandanten zugeordnete](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) Abonnements | Unbegrenzt |
| [Co-Admins](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) pro Abonnement |Unbegrenzt |
| [Ressourcengruppen](../articles/azure-resource-manager/management/overview.md) pro Abonnement |980 |
| Anforderungsgröße für Azure Resource Manager-API |4.194.304 Bytes |
| Tags pro Abonnement<sup>1</sup> |50 |
| Eindeutige Tag-Berechnungen pro Abonnement<sup>1</sup> | 80.000 |
| [Bereitstellungen auf Abonnementebene](../articles/azure-resource-manager/templates/deploy-to-subscription.md) pro Standort | 800<sup>2</sup> |
| Speicherorte von [Bereitstellungen auf Abonnementebene](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 10 |

<sup>1</sup> Sie können bis zu 50 Tags direkt auf ein Abonnement anwenden. Das Abonnement kann jedoch eine unbegrenzte Anzahl von Tags enthalten, die auf Ressourcengruppen und Ressourcen innerhalb des Abonnements angewendet werden. Die Anzahl von Tags pro Ressource oder Ressourcengruppe ist auf 50 beschränkt. Resource Manager gibt nur eine [Liste mit eindeutigen Tagnamen und Werten](/rest/api/resources/tags) im Abonnement zurück, wenn die Anzahl der Tags maximal 80.000 beträgt. Sie können eine Ressource jedoch auch anhand des Tags finden, wenn die Anzahl 80.000 überschreitet.

<sup>2</sup>Bereitstellungen werden automatisch aus dem Verlauf gelöscht, wenn Sie den Grenzwert fast erreicht haben. Weitere Informationen finden Sie unter [Automatische Löschungen aus dem Bereitstellungsverlauf](../articles/azure-resource-manager/templates/deployment-history-deletions.md).