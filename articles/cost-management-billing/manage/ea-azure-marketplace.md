---
title: Azure Marketplace
description: Hier wird beschrieben, wie EA-Kunden den Azure Marketplace verwenden können.
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: banders
ms.openlocfilehash: f22dd59be380a7535d8bdc724d48152eeec4041f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244189"
---
# <a name="azure-marketplace"></a>Azure Marketplace

In diesem Artikel wird erläutert, wie EA-Kunden und -Partner die Marketplace-Gebühren anzeigen und Azure Marketplace-Käufe aktivieren können.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace für EA-Kunden

Direkten Kunden werden die Azure Marketplace-Gebühren im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/BillingAccounts) angezeigt. Alle Azure Marketplace-Käufe und deren Nutzung werden außerhalb der Azure-Vorauszahlung abgerechnet, wobei die Rechnungsstellung vierteljährlich oder monatlich und nachträglich erfolgt. Weitere Informationen finden Sie unter [Verwalten von Azure Marketplace im Azure-Portal](direct-ea-administration.md#enable-azure-marketplace-purchases).

Indirekte Kunden finden ihre Azure Marketplace-Abonnements im Azure Enterprise Portal auf der Seite **Abonnements verwalten**, wobei die Preise jedoch ausgeblendet sind. Kunden sollten ihren Lizenzierungsdienstanbieter (Licensing Solutions Provider, LSP) zwecks Informationen zu Azure Marketplace-Gebühren kontaktieren.

Neue monatlich oder jährlich wiederkehrende Azure Marketplace-Käufe werden innerhalb des Zeitraums, in dem Azure Marketplace-Artikel erworben werden, vollständig abgerechnet. Diese Artikel werden im folgenden Zeitraum am selben Tag des ursprünglichen Kaufs automatisch verlängert.

Vorhandene monatlich wiederkehrende Gebühren werden weiterhin am ersten jedes Kalendermonats verlängert. Die jährlichen Gebühren werden am Jahrestag des Kaufdatums verlängert.

Manche auf dem Azure Marketplace verfügbaren Wiederverkaufsdienste von Drittanbietern werden jetzt auf den Azure-Vorauszahlungssaldo Ihres Enterprise Agreement (EA) angerechnet. Vorher wurden diese Dienste außerhalb des Azure EA-Vorauszahlungssaldos und separat abgerechnet. Der Azure EA-Vorauszahlungssaldo für diese Dienste im Azure Marketplace vereinfacht die Verwaltung von kundenseitigen Einkäufen und Zahlungen. Eine umfassende Liste der Dienste, die jetzt auf die Azure-Vorauszahlung angerechnet werden, finden Sie im [Update der Azure-Website vom 6. März 2018](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partner

LSPs können eine Azure Marketplace-Preisliste von der Preisblattseite im Azure Enterprise Portal herunterladen. Wählen Sie den Link zur **Marketplace-Preisliste** in der oberen rechten Ecke aus. Die Azure Marketplace-Preisliste zeigt alle verfügbaren Dienste und ihre Preise an.

So laden Sie die Preisliste herunter:

1. Navigieren Sie im Azure Enterprise Portal zu **Berichte** > **Preisblatt**.
1. Suchen Sie in der oberen rechten Ecke den Link zur Azure Marketplace-Preisliste unter Ihrem Benutzernamen.
1. Klicken Sie mit der rechten Maustaste auf den Link, und wählen Sie **Ziel speichern unter** aus.
1. Ändern Sie im Fenster **Speichern** den Titel des Dokuments in `AzureMarketplacePricelist.zip`, wodurch die Datei von einer XLSX-Datei in eine ZIP-Datei geändert wird.
1. Nach dem Download verfügen Sie über eine ZIP-Datei mit länderspezifischen Preislisten.
1. LSPs sollten für länderspezifische Preise die jeweilige Länderdatei nutzen. LSPs können mithilfe der Registerkarte **Benachrichtigungen** erkennen, welche SKUs neu oder abgelaufen sind.
1. Preisänderungen treten selten auf. LSPs erhalten 30 Tage im voraus E-Mail-Benachrichtigungen über Preissteigerungen und Änderungen bei Devisenumrechnungen (Foreign Exchange, FX).
1. LSPs erhalten pro Registrierung, ISV und Quartal eine Rechnung.

### <a name="enabling-azure-marketplace-purchases"></a>Aktivieren von Azure Marketplace-Einkäufen

Unternehmensadministratoren können Azure Marketplace-Käufe für alle Azure-Abonnements unter dieser Registrierung deaktivieren oder aktivieren. Wenn der Unternehmensadministrator Käufe deaktiviert, und Azure-Abonnements vorhanden sind, die bereits über Azure Marketplace-Abonnements verfügen, werden diese Azure Marketplace-Abonnements nicht abgebrochen oder beeinträchtigt.

Obwohl Kunden ihre direkten Azure-Abonnements in Azure EA konvertieren können, indem sie sie ihrer Registrierung im Azure Enterprise Portal zuordnen, werden die untergeordneten Abonnements von dieser Aktion nicht automatisch konvertiert.

Zum Aktivieren von Azure Marketplace-Einkäufen im Azure Enterprise Portal führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim Azure Enterprise-Portal als Unternehmensadministrator an.
1. Navigieren Sie zu **Verwalten**.
1. Wählen Sie unter **Registrierungsdetails** das Stiftsymbol neben dem Zeilenelement **Azure Marketplace** aus.
1. Schalten Sie nach Bedarf zwischen **Aktiviert/Deaktiviert** oder **Nur kostenlose/BYOL-SKUs** um.
1. Wählen Sie **Speichern** aus.

Direktkunden können Azure Marketplace-Einkäufe im Azure-Portal aktivieren:

1. Melden Sie sich beim Azure-Portal an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü Abrechnungsbereiche und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im Menü links **Richtlinien** aus.
1. Legen Sie unter „Azure Marketplace“ die Richtlinie auf **Ein** fest.
1. Wählen Sie **Speichern** aus.

> [!NOTE]
> BYOL (Bring-Your-Own-License) und die Option „Nur kostenlos“ beschränken Kaufen und Erwerben von Azure Marketplace-SKUs auf BYOL- und kostenlose SKUs.

### <a name="services-billed-hourly-for-azure-ea"></a>Stündlich für Azure EA abgerechnete Dienste

Die folgenden Dienste werden stundenweise unter einem Enterprise Agreement und nicht in monatlichen Raten in MOSP abgerechnet:

- Application Delivery Network
- Web Application Firewall

### <a name="azure-remoteapp"></a>Azure RemoteApp

Wenn Sie über ein Enterprise Agreement verfügen, zahlen Sie für Azure RemoteApp auf Basis Ihrer Enterprise Agreement-Preisstufe. Es fallen keine zusätzlichen Gebühren an. Der Standardpreis umfasst die ersten 40 Stunden. Der unbegrenzte Preis deckt die ersten 80 Stunden ab. RemoteApp verhindert die 80 Stunden überschreitende Nutzung.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Preisen](ea-pricing-overview.md).
- Eine Liste der Fragen und Antworten zu Azure Marketplace-Diensten und Azure EA-Vorauszahlung finden Sie in den [häufig gestellten Fragen zu Cost Management + Billing FAQ](../cost-management-billing-faq.yml).
