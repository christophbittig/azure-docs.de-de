---
title: Erwerben einer Azure-Reservierung
description: Informieren Sie sich über wichtige Aspekte, wenn Sie eine Azure-Reservierung erwerben.
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/21/2021
ms.author: banders
ms.openlocfilehash: f7c9551b3edee5c4e491cd4218e7b7973f01f287
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244151"
---
# <a name="buy-a-reservation"></a>Kaufen einer Reservierung

Mit Azure-Reservierungen können Sie Geld sparen, indem Sie sich bei zahlreichen Azure-Ressourcen für Pläne mit einer Laufzeit von einem Jahr oder drei Jahren entscheiden. Bevor Sie eine Verpflichtung zum Kauf einer Reservierung eingehen, lesen Sie unbedingt die folgenden Abschnitte, um den Kauf vorzubereiten.

## <a name="who-can-buy-a-reservation"></a>Wer kann eine Reservierung kaufen?

Um eine Reservierung kaufen zu können, müssen Sie für ein Enterprise-Abonnement (MS-AZR-0017P oder MS-AZR-0148P), für ein Abonnement mit nutzungsbasierter Zahlung (MS-AZR-0003P oder MS-AZR-0023P) oder für ein Abonnement mit Microsoft-Kundenvereinbarung über die Besitzer- oder Reservierungskäuferrolle verfügen. Cloudlösungsanbieter können Azure-Reservierungen über das Azure-Portal oder  [Partner Center](/partner-center/azure-reservations)  erwerben.

EA-Kunden (Enterprise Agreement) können Käufe für EA-Administratoren beschränken, indem sie die Option **Reservierte Instanzen hinzufügen** im EA-Portal deaktivieren. Direkte EA-Kunden können nun die Einstellung „Reservierte Instanz“ im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/BillingAccounts) deaktivieren. Navigieren Sie zum Menü „Richtlinien“, um die Einstellungen zu ändern.

EA-Administratoren benötigen für mindestens ein EA-Abonnement Zugriff als Besitzer oder Reservierungskäufer, um eine Reservierung erwerben zu können. Die Option ist nützlich für Unternehmen, die ein zentralisiertes Team für den Erwerb von Reservierungen benötigen.

Ein Reservierungsrabatt gilt nur für Ressourcen in Verbindung mit Abonnements, die über Enterprise, einen Cloud Solution Provider (CSP), eine Microsoft-Kundenvereinbarung und individuelle Pläne mit nutzungsbasierten Tarifen erworben wurden.

## <a name="scope-reservations"></a>Bereichsreservierungen

Sie können den Bereich für eine Reservierung auf ein Abonnement oder eine Ressourcengruppe festlegen. Wenn Sie den Bereich für eine Reservierung festlegen, wird ausgewählt, wo die Reservierungseinsparungen gelten. Wenn Sie den Bereich der Reservierung auf eine Ressourcengruppe festlegen, gelten die Reservierungsrabatte nur für die Ressourcengruppe und nicht für das gesamte Abonnement.

### <a name="reservation-scoping-options"></a>Optionen für Reservierungsbereiche

Ihnen stehen je nach Bedarf drei Optionen zur Verfügung, mit denen Sie den Bereich einer Reservierung definieren können:

- **Einzelne Ressourcengruppe**: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.
- **Einzelnes Abonnement**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.
- **Gemeinsam genutzt**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Wenn ein Abonnement in einen anderen Abrechnungskontext verschoben wurde, wird der Vorteil nicht mehr auf dieses Abonnement angewendet und gilt weiterhin für andere Abonnements im Abrechnungskontext.
    - Für Kunden mit einem Enterprise Agreement ist der Abrechnungskontext die Registrierung. Der Reservierungsbereich für die gemeinsame Nutzung umfasst mehrere Active Directory-Mandanten in einer Registrierung.
    - Für Microsoft-Kundenvereinbarung-Kunden ist der Abrechnungsbereich das Abrechnungsprofil.
    - Für Kunden mit individuellen Abonnements mit nutzungsbasierten Tarifen handelt es sich beim Abrechnungsbereich um alle berechtigten Abonnements, die vom Kontoadministrator erstellt wurden.
- **Verwaltungsgruppe**: Wendet den Reservierungsrabatt auf die entsprechende Ressource in der Liste der Abonnements an, die ein Teil des Verwaltungsgruppen- und Abrechnungsbereichs sind. Um eine Reservierung für eine Verwaltungsgruppe zu erwerben, müssen Sie mindestens über Leseberechtigungen für die Verwaltungsgruppe verfügen und ein Reservierungsbesitzer oder Reservierungskäufer für das Abrechnungsabonnement sein.

Während Reservierungsrabatte auf Ihre Nutzung angewendet werden, verarbeitet Azure die Reservierung in der folgenden Reihenfolge:

1. Reservierungen mit dem Bereich „Einzelne Ressourcengruppe“
2. Reservierungen mit dem Bereich „Einzelabonnement“
3. Reservierungen mit einer Verwaltungsgruppe als Bereich
4. Reservierungen mit einem gemeinsam genutzten Bereich (mehrere Abonnements), wie weiter oben beschrieben

Sie können den Bereich nach dem Erwerb einer Reservierung immer aktualisieren. Navigieren Sie zu diesem Zweck zu der Reservierung, klicken Sie auf **Konfiguration**, und legen Sie den Bereich für die Reservierung erneut fest. Das Neuzuweisen eines Bereichs für eine Reservierung ist keine kommerzielle Transaktion. Die Reservierungsbedingungen ändern sich nicht. Weitere Informationen zum Aktualisieren des Bereichs finden Sie unter [Aktualisieren des Bereichs nach dem Erwerb einer Reservierung](manage-reserved-vm-instance.md#change-the-reservation-scope).

:::image type="content" source="./media/prepare-buy-reservation/rescope-reservation-management-group.png" alt-text="Beispiel für eine Änderung des Reservierungsbereichs" lightbox="./media/prepare-buy-reservation/rescope-reservation-management-group.png" :::

## <a name="discounted-subscription-and-offer-types"></a>Abonnements und Angebotstypen mit Rabatt

Reservierungsrabatte gelten für die folgenden berechtigten Abonnements und Angebotstypen.

- Enterprise Agreement (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P)
- Abonnements im Rahmen einer Microsoft-Kundenvereinbarung
- Individuelle Pläne mit Tarifen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P)
- CSP-Abonnements

Für Ressourcen, die in einem Abonnement mit anderen Angebotstypen ausgeführt werden, gilt der Reservierungsrabatt nicht.

## <a name="purchase-reservations"></a>Erwerben von Reservierungen

Sie können Reservierungen über das Azure-Portal, APIs, PowerShell und die CLI erwerben. Lesen Sie die folgenden Artikel, wenn Sie Reservierungen kaufen möchten:

- [App Service](prepay-app-service.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Disk Storage](../../virtual-machines/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Softwarepläne](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL-Datenbank](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure-Datenbank für PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Azure Database for MySQL](../../mysql/concept-reserved-pricing.md)
- [Azure Database for MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [Azure VMware Solution](../../azure-vmware/reserved-instance.md)
- [Virtuelle Computer](../../virtual-machines/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Erwerben von Reservierungen mit monatlicher Zahlung

Sie können für Reservierungen auch monatlich bezahlen. Bei einem Kauf mit Vorauszahlung zahlen Sie den gesamten Betrag. Bei der monatlichen Zahlungsoption werden dagegen die Gesamtkosten der Reservierung gleichmäßig auf die einzelnen Monate der Laufzeit aufgeteilt. Die Gesamtkosten für vorab bezahlte und monatliche Reservierungen sind gleich. Es fallen keine zusätzlichen Gebühren an, wenn Sie sich für die monatliche Zahlung entscheiden.

Wird eine Reservierung über die Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) erworben, variiert Ihr monatlicher Zahlungsbetrag möglicherweise abhängig vom Wechselkurs des aktuellen Monats für Ihre lokale Währung.

Monatliche Zahlungen sind für Folgendes nicht verfügbar: Databricks, SUSE Linux-Reservierungen, Red Hat-Pläne und Azure Red Hat OpenShift-Lizenzen.

### <a name="view-payments-made"></a>Anzeigen geleisteter Zahlungen

Geleistete Zahlungen können mithilfe von APIs und Nutzungsdaten sowie in der Kostenanalyse angezeigt werden. Bei Reservierungen mit monatlicher Zahlung wird der Häufigkeitswert in Nutzungsdaten und in der Reservierungsgebühren-API als **Serie** angezeigt. Bei Reservierungen mit Vorauszahlung wird der Wert als **Einmalig** angezeigt.

In der Standardansicht der Kostenanalyse werden monatliche Käufe angezeigt. Wenden Sie auf **Gebührentyp** den Filter **Einkauf** und auf **Häufigkeit** den Filter **Serie** an, um alle Käufe anzuzeigen. Sollen nur Reservierungen angezeigt werden, wenden Sie einen Filter für **Reservierung** an.

![Beispiel für Reservierungserwerbskosten in der Kostenanalyse](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Umtausch und Rückerstattungen

Mit monatlicher Abrechnung erworbene Reservierungen können genau wie andere Reservierungen erstattet oder umgetauscht werden. 

Wenn Sie eine Reservierung mit monatlicher Zahlung umtauschen, müssen die Kosten für die gesamte Lebensdauer des neuen Einkaufs höher sein als die Restzahlungen, die für die zurückgegebene Reservierung storniert werden. Ansonsten gelten für den Umtausch keine weiteren Einschränkungen, und es fallen auch keine Gebühren an. Sie können eine Reservierung mit Vorauszahlung umtauschen, um eine neue Reservierung mit monatlicher Abrechnung zu erwerben. Der Wert für die Lebensdauer der neuen Reservierung muss jedoch höher sein als der anteilige Wert der zurückgegebenen Reservierung.

Wenn Sie eine Reservierung stornieren, die monatlich bezahlt wird, werden zukünftige Zahlungen mit dem Rückerstattungslimit von 50.000 USD verrechnet.

Weitere Informationen zu Umtausch und Rückerstattungen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](exchange-and-refund-azure-reservations.md).

## <a name="reservation-notifications"></a>Reservierungsbenachrichtigungen

Je nachdem, wie Sie für Ihr Azure-Abonnement bezahlen, werden per E-Mail Reservierungsbenachrichtigungen an die folgenden Benutzer Ihrer Organisation gesendet. Benachrichtigungen werden für verschiedene Ereignisse gesendet, z. B.: 

- Purchase
- Bevorstehender Reservierungsablauf
- Expiry
- Verlängerung
- Abbruch
- Bereichsänderung

Für Kunden mit EA-Abonnements:

- Benachrichtigungen werden nur an die EA-Benachrichtigungskontakte gesendet.
- Benutzer, die einer Reservierung per Azure RBAC-Berechtigung (IAM) hinzugefügt werden, erhalten keine E-Mail-Benachrichtigungen.

Für Kunden mit Einzelabonnements:

- Der Käufer erhält eine Kaufbenachrichtigung.
- Zum Zeitpunkt des Kaufs erhält der Besitzer des Abrechnungskontos eines Abonnements eine Kaufbenachrichtigung.
- Der Kontobesitzer erhält alle anderen Benachrichtigungen.

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu Reservierungsberechtigungen](view-reservations.md)
- [Verwalten von Reservierungen für Azure-Ressourcen](manage-reserved-vm-instance.md)
- [Automatisieren mithilfe von REST-APIs](/rest/api/reserved-vm-instances/reservationorder)
- [Automatisieren mithilfe von Azure PowerShell](/powershell/module/az.reservations)
- [Automatisieren mithilfe der CLI](/cli/azure/reservations)
