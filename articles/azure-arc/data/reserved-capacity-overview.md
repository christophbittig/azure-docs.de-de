---
title: Sparen von Kosten mit reservierten Kapazitäten
description: Hier erfahren Sie, wie Sie reservierte Kapazitäten für SQL Managed Instance mit Azure Arc-Unterstützung erwerben, um Kosten zu sparen.
services: sql-database
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mikeray
ms.date: 10/27/2021
ms.openlocfilehash: 70a6f67fb5a7443e84c937c9a5bf56db5980022e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095070"
---
# <a name="reserved-capacity---azure-arc-enabled-sql-managed-instance"></a>Reservierte Kapazitäten für SQL Managed Instance mit Azure Arc-Unterstützung

Sparen Sie bei SQL Managed Instance mit Azure Arc-Unterstützung im Vergleich zur nutzungsbasierten Bezahlung, indem Sie eine Reservierung abschließen. Mit reservierten Kapazitäten gehen Sie eine ein- oder dreijährige Verpflichtung für die Nutzung von SQL Managed Instance mit Azure Arc-Unterstützung ein und erhalten dafür einen erheblichen Rabatt auf die Dienstgebühr. Um reservierte Kapazität zu erwerben, müssen Sie die Azure-Region, den Bereitstellungstyp, die Leistungsstufe und die Laufzeit angeben.

Die Reservierung muss keiner bestimmten Datenbank oder verwalteten Instanz zugewiesen werden. Entsprechende vorhandene Bereitstellungen, die bereits ausgeführt oder neu bereitgestellt werden, profitieren automatisch von dem Vorteil. Durch den Erwerb einer Reservierung bestätigen Sie die Nutzung des Azure Arc-Diensts für ein oder drei Jahre. Sobald Sie eine Reservierung erworben haben, werden die Dienstgebühren, die den Reservierungsattributen entsprechen, nicht mehr zu den Preisen der nutzungsbasierten Bezahlung abgerechnet. 

Eine Reservierung gilt nur für die Kosten des Azure Arc-Diensts und deckt nicht die Kosten für SQL-IPs oder andere Gebühren ab. Nach Ablauf der Reservierungslaufzeit erlischt der Abrechnungsvorteil, und die verwaltete Instanz wird mit den Preisen für die nutzungsbasierte Bezahlung in Rechnung gestellt. Reservierungen werden nicht automatisch verlängert. Weitere Informationen zu den Preisen finden Sie unter [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Reservierte Kapazität kann über das [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/reservationsBrowse) erworben werden. Bezahlen Sie die Reservierung [im Voraus oder monatlich](../../cost-management-billing/reservations/prepare-buy-reservation.md). So erwerben Sie reservierte Kapazität:

- Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement oder ein individuelles Abonnement mit nutzungsbasierter Bezahlung zugeordnet sein.
- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein. Reservierte Kapazität.

Weitere Informationen zur Berechnung der Reservierung von Kapazitäten für Unternehmenskunden und Kunden mit nutzungsbasierter Bezahlung finden Sie unter [Grundlegendes zur Nutzung von Azure-Reservierungen für die Enterprise-Registrierung](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) und [Informationen zur Azure-Reservierungsnutzung bei Ihrem einzelnen Abonnement mit nutzungsbasierter Bezahlung](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-correct-size-before-purchase"></a>Ermitteln der richtigen Größe vor dem Erwerb

Die Größe der Reservierung sollte auf der Gesamtmenge der Computeressourcen (in virtuellen Kernen gemessen) basieren, die von den vorhandenen oder in Kürze bereitgestellten verwalteten Instanzen innerhalb des Reservierungsbereichs einer bestimmten Region verwendet werden.

In der folgenden Liste wird ein Szenario für die Ressourcenreservierung veranschaulicht: 

* **Aktueller Bestand:** 
  - Eine verwaltete Instanz im Tarif „Universell“ mit 16 virtuellen Kernen
  - Zwei verwaltete Instanzen im Tarif „Unternehmenskritisch“ mit 8 virtuellen Kernen

* **Im nächsten Jahr fügen Sie hinzu:** 
  - Eine weitere verwaltete Instanz im Tarif „Universell“ mit 16 virtuellen Kernen
  - Eine weitere verwaltete Instanz im Tarif „Unternehmenskritisch“ mit 32 virtuellen Kernen

* **Erwerben Sie Reservierungen für:**
  - Einjährige Reservierung für eine verwaltete Instanz im Tarif „Universell“ mit 32 (2 × 16) virtuellen Kernen
  - Einjährige Reservierung für eine verwaltete Instanz im Tarif „Unternehmenskritisch“ mit 48 (2 × 8 + 32) virtuellen Kernen 

## <a name="buy-reserved-capacity"></a>Erwerben von reservierter Kapazität

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3. Wählen Sie im Bereich **Reservierungen erwerben** zuerst **Hinzufügen** und dann **SQL Managed Instance** aus, um eine neue Reservierung für SQL Managed Instance mit Azure Arc-Unterstützung zu erwerben.
4. Füllen Sie die erforderlichen Felder aus. Der Rabatt für reservierte Kapazitäten wird auf vorhandene SQL Managed Instance-Ressourcen angewendet, die den von Ihnen ausgewählten Attributen entsprechen. Die tatsächliche Anzahl von Datenbanken oder verwalteten Instanzen, die den Rabatt erhalten, hängt vom ausgewählten Bereich und der ausgewählten Menge ab.

    In der folgenden Tabelle werden die erforderlichen Felder beschrieben.
    
    | Feld      | BESCHREIBUNG|
    |------------|--------------|
    |Subscription|Das zum Bezahlen für die Kapazitätsreservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die Reservierung belastet. Als Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummer MS-AZR-0017P oder MS-AZR-0148P) oder ein Einzelvertrag mit nutzungsbasierter Bezahlung (Angebotsnummer MS-AZR-0003P oder MS-AZR-0023P) festgelegt werden. Bei einem Enterprise-Abonnement werden die Gebühren vom Saldo der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) der Registrierung abgezogen oder als Überschreitung berechnet. Bei einem individuellen Abonnement mit Preisen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung für das Abonnement in Rechnung gestellt.|
    |`Scope`       |Die Reservierung virtueller Kerne kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen: <br/><br/>**Gemeinsam**: Der Rabatt auf die Reservierung virtueller Kerne wird auf die Datenbank oder verwaltete Instanz angewendet, die in einem beliebigen Abonnement innerhalb des Abrechnungskontexts ausgeführt wird. Für Enterprise-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Zahlung stellt der freigegebene Bereich alle Abonnements mit nutzungsbasierter Zahlung dar, die vom Kontoadministrator erstellt wurden.<br/><br/>**Einzelabonnement**: Der Rabatt auf die Reservierung virtueller Kerne wird auf die Datenbank oder verwaltete Instanz in diesem Abonnement angewendet. <br/><br/>**Einzelne Ressourcengruppe**: Der Reservierungsrabatt wird auf die verwalteten Instanzen und die ausgewählte Ressourcengruppe im ausgewählten Abonnement angewendet.</br></br>**Verwaltungsgruppe**: Der Reservierungsrabatt wird auf die verwalteten Instanzen in der Liste der Abonnements angewendet, die sowohl Teil der Verwaltungsgruppe als auch des Abrechnungsbereichs sind.|
    |Region      |Die Azure-Region, die durch die Kapazitätsreservierung abgedeckt wird.|
    |Bereitstellungstyp|Der SQL-Ressourcentyp, für den Sie die Reservierung erwerben möchten.|
    |Leistungsstufe|Die Dienstebene für die Datenbanken oder verwalteten Instanzen. |
    |Begriff        |Ein Jahr oder drei Jahre|
    |Menge    |Die Menge an Computeressourcen, die innerhalb der Kapazitätsreservierung erworben werden. Die Menge entspricht einer Anzahl der virtuellen Kernen in der ausgewählten Azure-Region und der Leistungsstufe, die reserviert werden und den Abrechnungsrabatt erhalten. Wenn Sie beispielsweise mehrere verwaltete Instanzen mit der gesamten Computekapazität von 16 virtuellen Gen5-Kernen in der Region „USA, Osten“ ausführen oder ausführen möchten, geben Sie als Menge „16“ an, um die Vorteile für alle Datenbanken zu maximieren. |

1. Überprüfen Sie die Kosten für die Kapazitätsreservierung im Abschnitt **Kosten**.
1. Wählen Sie die Option **Kaufen**.
1. Klicken Sie auf **Diese Reservierung anzeigen**, um den Status des Einkaufs anzuzeigen.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilität der V-Kern-Größe

Die Flexibilität der V-Kern-Größe ermöglicht ein Hoch- oder Herunterskalieren innerhalb einer Leistungsstufe und Region, ohne den Vorteil reservierter Kapazität einzubüßen. Durch einen nicht angewendeten Puffer in Ihrer Reservierung können Sie Leistungsspitzen effektiv verwalten, ohne Ihr Budget zu überschreiten.

## <a name="limitation"></a>Einschränkung

Preise für reservierte Kapazität werden nur für Features und Produkte unterstützt, die den Status „Allgemeine Verfügbarkeit“ aufweisen. 

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Der Rabatt auf die Reservierung virtueller Kerne wird automatisch auf die Anzahl der verwalteten Instanzen angewendet, die dem Kapazitätsreservierungsumfang und Attributen entsprechen. Sie können den Umfang der Kapazitätsreservierung im [Azure-Portal](https://portal.azure.com), mit PowerShell, der Azure-Befehlszeilenschnittstelle oder der API aktualisieren.

Weitere Informationen zu Dienstebenen für SQL Managed Instance mit Azure Arc-Unterstützung finden Sie unter [SQL Managed Instance mit Azure Arc-Unterstützung: Dienstebenen](service-tiers.md).

- Weitere Informationen zu Azure SQL Managed Instance-Dienstebenen für das Modell mit virtuellen Kernen finden Sie unter [Azure SQL Managed Instance: Computehardware für die vCore-Dienstebene](../../azure-sql/managed-instance/service-tiers-managed-instance-vcore.md).

Informationen zum Verwalten der Kapazitätsreservierung finden Sie unter [Verwalten von Reservekapazität](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Verwalten von Azure-Reservierungen](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Grundlegendes zum Rabatt für Azure-Reservierungen](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Verkaufen Microsoft Azure Reserved Instances](/partner-center/azure-reservations)
