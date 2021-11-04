---
title: Anzeigen der Nutzung von Azure-Reservierungen
description: Hier erfahren Sie, wie Sie Reservierungsnutzung und -details abrufen.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: f69c39205ddc465ee4634ec621905fed2d701682
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446237"
---
# <a name="view-reservation-utilization-after-purchase"></a>Anzeigen der Reservierungsnutzung nach dem Kauf

Sie können im Azure-Portal und in der Cost Management-Power BI-App den Prozentsatz der Reservierungsnutzung und die Ressourcen anzeigen.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Anzeigen der Nutzung im Azure-Portal mit Azure RBAC-Zugriff

Zum Anzeigen der Reservierungsnutzung müssen Sie über Azure RBAC-Zugriff auf die Reservierung oder über erhöhte Zugriffsrechte verfügen, um alle Azure-Abonnements und -Verwaltungsgruppen verwalten zu können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu [Reservierungen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
1. Diese Liste zeigt alle Reservierungen, für die Sie die Rolle „Besitzer“ oder „Leser“ ausüben. Jede Reservierung zeigt den letzten bekannten Nutzungsprozentsatz.
1. Wählen Sie den Nutzungsprozentsatz aus, um den Verlauf und Details der Nutzung anzuzeigen. Im folgenden Video wird ein Beispiel gezeigt:
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Anzeigen der Nutzung als Abrechnungsadministrator

Ein EA-Administrator (Enterprise Agreement) oder MCA-Abrechnungsadministrator (Microsoft Customer Agreement, Microsoft-Kundenvereinbarung) kann die Nutzung über **Cost Management + Billing** anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wechseln Sie zu **Cost Management + Billing** > **Reservierungen**.
1. Wählen Sie den Nutzungsprozentsatz aus, um den Verlauf und Details der Nutzung anzuzeigen.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Abrufen der Reservierungen und Nutzung mithilfe von APIs, PowerShell und der CLI

Sie können die [Reservierungsnutzung](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) mithilfe der API für die Nutzung reservierter Instanzen abrufen.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Anzeigen der Reservierungen und Nutzung in Power BI

Es gibt zwei Optionen für Power BI-Benutzer:

- Cost Management-Connector für Power BI Desktop: Datum des Reservierungskaufs und Nutzungsdaten sind über den [Cost Management-Connector für Power BI Desktop](/power-bi/desktop-connect-azure-cost-management) verfügbar. Erstellen Sie mithilfe des Connectors die gewünschten Berichte.
- Cost Management-Power BI-App: Verwenden Sie die [Cost Management-Power BI-App](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) für vorgefertigte Berichte, die Sie weiter anpassen können.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md).
- [Grundlegendes zur Nutzung von Azure-Reservierungen für die Enterprise-Registrierung](understand-reserved-instance-usage-ea.md).
- [Grundlegendes zur Nutzung von Azure-Reservierungen für CSP-Abonnements](/partner-center/azure-reservations).
