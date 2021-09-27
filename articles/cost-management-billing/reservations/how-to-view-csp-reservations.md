---
title: Anzeigen von Azure-Reservierungen als Cloud Solution Provider
description: Hier erfahren Sie, wie Sie Azure-Reservierungen als Cloud Solution Provider anzeigen können.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 490adca7f1e0f7c5e614e164f9e21af9bc36d77b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653322"
---
# <a name="view-azure-reservations-as-a-cloud-solution-provider-csp"></a>Anzeigen von Azure-Reservierungen als Cloud Solution Provider (CSP)

Cloud Solution Provider können auf Reservierungen zugreifen, die für ihre Kunden erworben wurden. Anhand der folgenden Informationen können Sie Reservierungen im Azure-Portal anzeigen.

1. Bitten Sie Ihren globalen Administrator, Sie als **Administrator-Agent** in Ihrem Mandanten hinzuzufügen.
    Diese Option steht im Partner Center für globale Administratoren zur Verfügung. Sie finden sie unter **Einstellungen** (das Zahnradsymbol oben rechts auf der Seite) > **Benutzerverwaltung**.  
1. Wenn Sie über die Berechtigung für Administrator-Agents verfügen, wechseln Sie über den Link **Admin on Behalf Of** (Administrator im Auftrag von) zum Azure-Portal.
1. Navigieren Sie zu „Partner Center“ > **Kunden** > erweitern Sie „Kundendetails“ > **Microsoft Azure-Verwaltungsportal**.
1. Wechseln Sie im Azure-Portal zu **Reservierungen**.

> [!NOTE]
> Wenn Sie ein Gast im Mandanten des Kunden sind, werden Sie daran gehindert, Reservierungen anzuzeigen. Falls Sie Gastzugriff haben, müssen Sie ihn aus dem Mandanten entfernen. Die Berechtigung für Administrator-Agents setzt den Gastzugriff nicht außer Kraft.

- Zum Entfernen Ihres Gastzugriffs im Partner Center navigieren Sie zu **Mein Konto** >  **[Organisationen](https://myaccount.microsoft.com/organizations)** , und wählen Sie **Organisation verlassen** aus.

Bitten Sie alternativ einen anderen Benutzer, der auf die Reservierung zugreifen kann, Ihr Gastkonto dem Reservierungsauftrag hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen von Azure-Reservierungen](view-reservations.md)