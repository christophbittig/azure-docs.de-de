---
title: Preise für reserviertes Compute – Azure Database for PostgreSQL – Hyperscale (Citus)
description: Vorauszahlung für Azure Database for PostgreSQL – Hyperscale (Citus)-Computeressourcen mit reservierter Kapazität
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 422f8533c78d2242e1ef520d42dd09982990871a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577971"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Vorauszahlung für Azure Database for PostgreSQL – Hyperscale (Citus)-Computeressourcen mit reservierter Kapazität

Mit Azure Database for PostgreSQL – Hyperscale (Citus) können Sie jetzt Geld im Vergleich zur nutzungsbasierten Bezahlung sparen, indem Sie Computeressourcen im Voraus bezahlen. Mit reservierter Hyperscale (Citus)-Kapazität leisten Sie eine Vorauszahlung für eine Hyperscale (Citus)-Servergruppe für einen Zeitraum von einem Jahr oder drei Jahren und erhalten dafür einen umfangreichen Rabatt auf die Computekosten. Um reservierte Hyperscale (Citus)-Kapazität zu erwerben, müssen Sie die Azure-Region, den Reservierungszeitraum und die Abrechnungshäufigkeit angeben.

> [!IMPORTANT]
> In diesem Artikel geht es um reservierte Kapazität für Azure Database for PostgreSQL – Hyperscale (Citus). Informationen zu Reservekapazität für Azure Database for PostgreSQL – Einzelserver finden Sie unter [Vorauszahlung für Azure Database for PostgreSQL – Einzelserver-Computeressourcen mit reservierter Kapazität](./concept-reserved-pricing.md).

Sie müssen die Reservierung nicht bestimmten Hyperscale (Citus)-Servergruppen zuweisen. Eine Hyperscale (Citus)-Servergruppe, die bereits ausgeführt oder neu bereitgestellt wird, profitiert automatisch vom Reservierungspreisvorteil. Beim Kauf einer Reservierung bezahlen Sie im Voraus die Computekosten für einen Zeitraum von einem oder drei Jahren. Sobald Sie eine Reservierung erworben haben, werden die Hyperscale (Citus)-Computegebühren, die den Reservierungsattributen entsprechen, nicht mehr zu den Preisen der nutzungsbasierten Bezahlung abgerechnet. 

Eine Reservierung deckt nicht die Software-, Netzwerk- oder Speichergebühren für die Hyperscale (Citus)-Servergruppen ab. Nach Ablauf des Reservierungszeitraums erlischt der Abrechnungsvorteil, und die Hyperscale (Citus)-Servergruppen werden mit den Preisen für die nutzungsbasierte Bezahlung in Rechnung gestellt. Reservierungen werden nicht automatisch verlängert. Weitere Informationen zu den Preisen finden Sie unter [Azure Database for PostgreSQL – Preise](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

Reservierte Hyperscale (Citus)-Kapazität kann über das [Azure-Portal](https://portal.azure.com/) erworben werden. Bezahlen Sie die Reservierung [im Voraus oder monatlich](../cost-management-billing/reservations/prepare-buy-reservation.md). So erwerben Sie reservierte Kapazität:

* Sie müssen für mindestens ein Enterprise Agreement (EA) oder ein individuelles Abonnement mit nutzungsbasierter Bezahlung über die Rolle „Besitzer“ verfügen.
* Bei Enterprise Agreement-Abonnements muss **Reservierte Instanzen hinzufügen** im [EA Portal](https://ea.azure.com/) aktiviert sein. Wenn diese Einstellung deaktiviert ist, müssen Sie ein Enterprise Agreement-Administrator für das Abonnement sein.
* Für das Cloud Solution Provider-Programm (CSP) können nur die Administrator- oder Vertriebs-Agents reservierte Hyperscale (Citus)-Kapazität erwerben.

Ausführliche Informationen dazu, wie Reservierungskäufe bei Kunden mit einem Enterprise Agreement und Kunden mit einem Abonnement mit nutzungsbasierter Bezahlung in Rechnung gestellt werden, finden Sie in den folgenden Artikeln:
- [Grundlegendes zur Nutzung von Azure-Reservierungen für die Enterprise Agreement-Registrierung](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Bestimmen der richtigen Servergruppengröße vor dem Kauf

Die Größe der Reservierung basiert auf der Gesamtmenge der Computeressourcen, die von den bereits vorhandenen oder in Kürze bereitzustellenden Koordinator- und Workerknoten in Hyperscale (Citus)-Servergruppen innerhalb einer bestimmten Region verwendet wird.

Beispiel: Sie führen eine Hyperscale (Citus)-Servergruppe mit einem Koordinatorknoten mit 16 virtuellen Kernen und drei Workerknoten mit 8 virtuellen Kernen aus. Angenommen, Sie planen die Bereitstellung einer zusätzlichen Hyperscale (Citus)-Servergruppe mit einem Koordinatorknoten mit 32 virtuellen Kernen und zwei Workerknoten mit 4 virtuellen Kernen. Außerdem benötigen Sie diese Ressourcen mindestens ein Jahr lang.

In diesem Fall erwerben Sie eine einjährige Reservierung für:

* Insgesamt 16 virtuelle Kerne + 32 virtuelle Kerne = 48 virtuelle Kerne für Koordinatorknoten
* Insgesamt 3 Knoten × 8 virtuelle Kerne + 2 Knoten × 4 virtuelle Kerne = 24 + 8 = 32 virtuelle Kerne für Workerknoten

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Kaufen von reservierter Kapazität für Azure Database for PostgreSQL

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
1. Wählen Sie **Hinzufügen**. Wählen Sie im Bereich **Reservierungen erwerben** die Option **Azure Database for PostgreSQL** aus, um eine neue Reservierung für Ihre PostgreSQL-Datenbanken zu erwerben.
1. Wählen Sie den **Hyperscale (Citus)-Computetyp** zum Kauf aus, und klicken Sie anschließend auf **Auswählen**.
1. Überprüfen Sie auf der Registerkarte **Produkte** die Menge für den ausgewählten Computetyp.
1. Fahren Sie mit der Registerkarte **Buy + Review** (Kaufen und überprüfen) fort, um Ihren Kauf abzuschließen.

In der folgenden Tabelle werden die erforderlichen Felder beschrieben.

| Feld        | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription | Das Abonnement, das für die Zahlung der Reservierung von Azure Database for PostgreSQL-Kapazitäten verwendet wird. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die Reservierung von Azure Database for PostgreSQL-Kapazitäten belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder eine einzelne Vereinbarung mit Preisen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Bei einem Enterprise Agreement-Abonnement werden die Gebühren vom Saldo der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) der Registrierung abgezogen oder als Überschreitung berechnet. Bei einem individuellen Abonnement mit Preisen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung für das Abonnement in Rechnung gestellt.                                                                                  |
| `Scope`        | Die Reservierung virtueller Kerne kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Wenn Sie **Gemeinsam** auswählen wird der Rabatt auf die Reservierung virtueller Kerne auf Hyperscale (Citus)-Servergruppen angewandt, die in einem beliebigen Abonnement innerhalb des Abrechnungskontexts ausgeführt werden. Für Enterprise Agreement-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Zahlung stellt der freigegebene Bereich alle Abonnements mit nutzungsbasierter Zahlung dar, die vom Kontoadministrator erstellt wurden. Wenn Sie **Verwaltungsgruppe** auswählen, wird der Rabatt für Reservierungen auf ausgeführte Servergruppen mit Hyperscale (Citus) in Abonnements angewandt, die sowohl Teil der Verwaltungsgruppe als auch des Abrechnungsbereichs sind. Wenn Sie **Einzelabonnement** auswählen, wird der Rabatt auf die Reservierung virtueller Kerne auf Hyperscale (Citus)-Servergruppen in diesem Abonnement angewandt. Wenn Sie **Einzelne Ressourcengruppe** auswählen, wird der Reservierungsrabatt auf Hyperscale (Citus)-Servergruppen im ausgewählten Abonnement und der ausgewählten Ressourcengruppe in diesem Abonnement angewandt. |
| Region       | Die Azure-Region, die durch die Reservierung von Azure Database for PostgreSQL – Hyperscale (Citus)-Kapazität abgedeckt wird                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Begriff         | Ein Jahr oder drei Jahre                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Menge     | Die Menge an Computeressourcen, die innerhalb der reservierten Hyperscale (Citus)-Kapazitätsreservierung erworben werden. Insbesondere die Anzahl der virtuellen Kerne der Koordinator- oder Workerknoten in der ausgewählten Azure-Region, die reserviert werden und den Abrechnungsrabatt erhalten. Wenn Sie z. B. Hyperscale (Citus)-Servergruppen mit einer gesamten Computekapazität von Koordinatorknoten mit 64 virtuellen Kernen und Workerknoten mit 32 virtuellen Kernen in der Region „USA, Osten“ ausführen (oder dies planen), geben Sie die Menge als 64 und 32 für Koordinator- bzw. Workerknoten an, um den Vorteil für alle Server zu maximieren.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilität der V-Kern-Größe

Die Flexibilität der V-Kern-Größe ermöglicht ein Hoch- oder Herunterskalieren der Koordinator- und Workerknoten innerhalb Region, ohne den Vorteil reservierter Kapazität einzubüßen.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Der Rabatt auf die Reservierung virtueller Kerne wird automatisch auf die Anzahl der Hyperscale (Citus)-Servergruppen angewendet, die dem Reservierungsumfang und den Attributen der Azure Database for PostgreSQL-Kapazitätsreservierung entsprechen. Sie können den Umfang der Azure Database for PostgreSQL – Hyperscale (Citus)-Kapazitätsreservierung über das Azure-Portal, PowerShell, die Azure-Befehlszeilenschnittstelle oder die API aktualisieren.

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

* [Was sind Azure-Reservierungen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Verwalten von Azure-Reservierungen](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Grundlegendes zum Rabatt für Azure-Reservierungen](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Grundlegendes zur Nutzung von Reservierungen für die Enterprise Agreement-Registrierung](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-Reservierungen im Azure Cloud Solution Provider-Programm im Partner Center](/partner-center/azure-reservations)