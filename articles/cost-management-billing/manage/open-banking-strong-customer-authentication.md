---
title: Open Banking (PSD2) und strenge Kundenauthentifizierung (SCA) für Azure-Kunden
description: In diesem Artikel wird erläutert, weshalb die mehrstufige Authentifizierung für einige Azure-Käufe und zum Abschluss der Authentifizierung erforderlich ist.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 08ac504dafa230d788e5b7eab10bd8f16572ad07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603579"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Open Banking (PSD2) und strenge Kundenauthentifizierung (SCA) für Azure-Kunden

Seit dem 14. September 2019 müssen Banken in den 31 Ländern/Regionen des [Europäischen Wirtschaftsraums](https://en.wikipedia.org/wiki/European_Economic_Area) die Identität der Person, die einen Onlinekauf tätigt, überprüfen, bevor die Zahlung verarbeitet wird. Für diese Überprüfung ist eine mehrstufige Authentifizierung erforderlich, um sicherzustellen, dass Ihre Onlinekäufe sicher und geschützt sind. Diese Überprüfungsanforderung gilt in einigen Ländern/Regionen erst später. Weitere Informationen finden Sie in den [häufig gestellten Fragen zu PSD2](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Was bedeutet PSD2 für Azure-Kunden?

Wenn Sie für Azure mit einer Kreditkarte zahlen, die von einer Bank im [Europäischen Wirtschaftsraum](https://en.wikipedia.org/wiki/European_Economic_Area)ausgestellt wurde, müssen Sie für die Zahlungsmethode Ihres Kontos unter Umständen eine mehrstufige Authentifizierung durchführen. Möglicherweise werden Sie zur mehrstufigen Authentifizierung aufgefordert, wenn Sie Ihr Azure-Konto registrieren oder ein Upgrade dafür vornehmen, auch wenn Sie zu diesem Zeitpunkt nichts kaufen. Auch beim Ändern der Zahlungsmethode Ihres Azure-Kontos, beim Entfernen des Ausgabenlimits oder beim Vornehmen einer sofortigen Zahlung aus dem Azure-Portal (wie etwa zum Begleichen ausstehender Beträge oder zum Kaufen von Azure-Guthaben) ist eventuell eine mehrstufige Authentifizierung erforderlich.

Wenn Ihre Bank Ihre monatlichen Azure-Gebühren ablehnt, erhalten Sie eine E-Mail von Azure, in der Sie auf die überfällige Zahlung hingewiesen und darüber informiert werden, wie Sie dies beheben können. Im Azure-Portal können Sie die mehrstufige Authentifizierung durchführen und Ihre ausstehenden Beträge begleichen.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Durchführen der mehrstufigen Authentifizierung im Azure-Portal

In den folgenden Abschnitten wird beschrieben, wie Sie die mehrstufige Authentifizierung im Azure-Portal durchführen. Verwenden Sie die auf Sie zutreffenden Informationen.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Ändern der aktiven Zahlungsmethode Ihres Azure-Kontos

Sie können die aktive Zahlungsmethode Ihres Azure-Kontos ändern, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Kontoadministrator an, und navigieren Sie zu **Cost Management + Billing** (Kostenverwaltung und Abrechnung).
2. Wählen Sie auf der Seite **Übersicht** das entsprechende Abonnement aus dem Raster **Meine Abonnements** aus.
3. Wählen Sie unter „Abrechnung“ die Option **Zahlungsmethoden** aus. Sie können eine neue Kreditkarte hinzufügen oder eine vorhandene Karte als aktive Zahlungsmethode für das Abonnement festlegen. Wenn Ihre Bank die mehrstufige Authentifizierung erfordert, werden Sie aufgefordert, während des Vorgangs eine Authentifizierung durchzuführen.

Weitere Informationen finden Sie unter [Hinzufügen, Aktualisieren oder Entfernen einer Kreditkarte für Azure](change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Begleichen ausstehender Gebühren für Azure-Dienste

Wenn die Gebühren von Ihrer Bank abgelehnt werden, ändert sich der Status Ihres Azure-Kontos im Azure-Portal in **Überfällig**. Sie können den Status Ihres Kontos überprüfen, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com/) an.
2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
3. Überprüfen Sie in der **Übersicht** unter **Kostenverwaltung + Abrechnung** die Statusspalte im Raster **Meine Abonnements**.
4. Klicken Sie auf **Saldo ausgleichen**, wenn Ihr Abonnement als **Überfällig** gekennzeichnet ist. Während des Vorgangs werden Sie aufgefordert, die mehrstufige Authentifizierung durchzuführen.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Begleichen ausstehender Gebühren für Marketplace- und Reservierungseinkäufe

Marketplace- und Reservierungseinkäufe werden getrennt von Azure-Diensten abgerechnet. Wenn Ihre Bank die Marketplace- oder Reservierungsgebühren ablehnt, wird Ihre Rechnung überfällig, und im Azure-Portal wird die Option **Jetzt zahlen** angezeigt. Sie können für überfällige Marketplace- und Reservierungsrechnungen bezahlen, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com/) an.
2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
3. Wählen Sie unter „Abrechnung“ die Option **Rechnungen** aus.
5. Wählen Sie im Dropdownfilter für Abonnements das dem Marketplace- oder Reservierungskauf zugeordnete Abonnement aus.
6. Überprüfen Sie im Raster „Rechnungen“ die Typspalte. Lautet der Typ **Azure Marketplace und Reservierungen**, wird der Link **Jetzt zahlen** angezeigt, wenn die Rechnung fällig oder überfällig ist. Wird **Jetzt zahlen** nicht angezeigt, wurde Ihre Rechnung bereits bezahlt. Während des Bezahlvorgangs werden Sie aufgefordert, die mehrstufige Authentifizierung durchzuführen.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Bezahlen von Azure-Rechnungen finden Sie unter [Begleichen überfälliger Zahlungen für Ihr Azure-Abonnement](resolve-past-due-balance.md).
