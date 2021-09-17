---
title: Vorauszahlung für Azure Dedicated Hosts, um Geld zu sparen
description: Erfahren Sie, wie Sie reservierte Instanzen von Azure Dedicated Hosts kaufen, um Computekosten einzusparen.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 464ffc8b5d4d04aeb7e4013f5b25d240d5ee9b06
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688963"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Einsparen von Kosten mit Azure Dedicated Host-Reservierungen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie sich für eine reservierte Instanz von Azure Dedicated Hosts entscheiden, können Sie Geld sparen. Der Reservierungsrabatt wird automatisch auf die Anzahl der ausgeführten dedizierten Hosts angewendet, die dem Reservierungsbereich und den Reservierungsattributen entsprechen. Sie müssen einem dedizierten Host keine Reservierung zuweisen, um die Rabatte zu erhalten. Bei einem Erwerb reservierter Instanzen wird nur der Computebereich ihrer Nutzung abgedeckt, und es werden Softwarelizenzierungskosten berücksichtigt. Weitere Informationen finden Sie unter [Übersicht über Azure Dedicated Hosts für virtuelle Computer](./dedicated-hosts.md).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Ermitteln der richtigen SKU für dedizierten Hosts vor dem Kauf


Bevor Sie eine Reservierung erwerben, sollten Sie die ermitteln, welchen dedizierten Host Sie benötigen. Eine SKU ist für einen dedizierten Host definiert und stellt die VM-Serie und den VM-Typ dar. 

Beginnen Sie, indem Sie die unterstützten Größen für [virtuellen Windows-Computer](./sizes.md) oder [Linux](./sizes.md) ermitteln, um die VM-Serie zu identifizieren.

Überprüfen Sie dann, ob sie unter Azure Dedicated Hosts unterstützt wird. Die Seite [Preise für Azure Dedicated Hosts](https://aka.ms/ADHPricing) enthält die vollständige Liste der SKUs für dedizierte Hosts, ihre CPU-Informationen sowie verschiedene Preisoptionen (auch für reservierte Instanzen).

Möglicherweise sind mehrere SKUs vorhanden, die Ihre ausgewählte VM-Serie unterstützen (mit unterschiedlichen Typen). Identifizieren Sie die beste SKU, indem Sie die Kapazität des Hosts (Anzahl der vCPUs) vergleichen. Beachten Sie, dass Sie Ihre Reservierung auf mehrere SKUs für dedizierte Hosts anwenden können, die dieselbe VM-Serie unterstützen (z. B. DSv3_Type1 und DSv3_Type2), aber nicht über verschiedene VM-Serien hinweg (z. B. DSv3 und ESv3).



## <a name="purchase-restriction-considerations"></a>Überlegungen zu Kaufeinschränkungen

Reservierte Instanzen sind für die meisten Größen dedizierter Hosts bis auf einige Ausnahmen verfügbar.

Rabatte für Reservierungen gelten nicht für Folgendes:

- **Clouds** – Reservierungen können in den Regionen Deutschland und China nicht käuflich erworben werden.

- **Nicht genügend Kontingent:** Für eine Reservierung, die einem Einzelabonnement zugeordnet ist, muss im Abonnement ein vCPU-Kontingent für die neue reservierte Instanz verfügbar sein. Beispiel: Wenn für das Zielabonnement eine Kontingentgrenze von 10 vCPUs für die DSv3-Serie gilt, können Sie keine Reservierung für dedizierte Hosts erwerben, die diese Serie unterstützen. Bei der Kontingentüberprüfung für Reservierungen werden die im Abonnement bereits bereitgestellten virtuellen Computer und dedizierten Hosts berücksichtigt. Um dieses Problem zu beheben, können Sie eine [Anforderung zur Kontingenterhöhung erstellen](../azure-portal/supportability/resource-manager-core-quotas-request.md).

- **Kapazitätsbeschränkungen:** In seltenen Fällen beschränkt Azure den Erwerb neuer Reservierungen für einen Teil der Dedicated Host-SKUs aufgrund von geringer Kapazität in einer Region.

## <a name="buy-a-reservation"></a>Kaufen einer Reservierung

Sie können eine reservierte Instanz einer Azure Dedicated Host-Instanz im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) erwerben.

Bezahlen Sie die Reservierung [im Voraus oder monatlich](../cost-management-billing/reservations/prepare-buy-reservation.md). Diese Anforderungen gelten für den Erwerb einer reservierten Dedicated Host-Instanz:

- Sie müssen in einer „Besitzer“-Rolle für mindestens ein EA-Abonnement oder ein Abonnement mit einem Satz für nutzungsbasierte Bezahlung sein.

- Bei EA-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.

- Für das Cloud Solution Provider (CSP)-Programm können nur die Administrator- oder Vertriebs-Agents Reservierungen kaufen.

So kaufen Sie ein Instanz:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Alle Dienste** \> **Reservierungen** aus.

3. Wählen Sie **Hinzufügen** aus, um eine neue Reservierung zu erwerben, und klicken Sie dann auf **Dedicated Host**.

4. Geben Sie die erforderlichen Felder ein. Ausgeführte Dedicated Host-Instanzen, die den ausgewählten Attributen entsprechen, sind für den Reservierungsrabatt berechtigt. Die tatsächliche Anzahl Ihrer Dedicated Host-Instanzen, die den Rabatt erhalten, hängt vom ausgewählten Bereich und der ausgewählten Menge ab.

Wenn Sie über eine EA-Vereinbarung verfügen, können Sie die Option **Weitere hinzufügen** verwenden, um schnell weitere Instanzen hinzuzufügen. Die Option ist für andere Abonnementtypen nicht verfügbar.

| **Feld**           | **Beschreibung**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription        | Das zum Bezahlen für die Reservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Zahlungen für die Reservierung belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Microsoft-Kundenvereinbarung“ oder ein einzelnes Abonnement mit Sätzen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Die Gebühren werden ggf. von der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) abgezogen oder als Überschreitung belastet. Bei einem Abonnement mit Sätzen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung in Rechnung gestellt. |
| `Scope`               | Der Bereich der Reservierung kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | Die Azure-Region, die durch die Reservierung abgedeckt wird                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedicated Host-Größe | Die Größe der Dedicated Host-Instanzen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Begriff                | Ein Jahr oder drei Jahre                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Menge            | Die Anzahl von Instanzen, die innerhalb der Reservierung erworben werden. Die Menge ist die Anzahl der ausgeführten Dedicated Host-Instanzen, auf die der Abrechnungsrabatt angewendet werden kann.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Einzelne Ressourcengruppe**: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.

- **Einzelnes Abonnement**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.

- **Gemeinsam genutzt**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für EA-Kunden ist der Abrechnungskontext die Registrierung. Für Kunden mit individuellen Abonnements mit nutzungsbasierten Tarifen handelt es sich beim Abrechnungsbereich um alle berechtigten Abonnements, die vom Kontoadministrator erstellt wurden.

## <a name="usage-data-and-reservation-utilization"></a>Nutzungsdaten und Reservierungsnutzung

Ihre Nutzungsdaten haben bei der Nutzung, für die der Reservierungsrabatt gilt, effektiv den Preis „0“. Sie können sehen, welche VM-Instanz den Reservierungsrabatt für jede Reservierung erhalten hat.

Wenn Sie ein EA-Kunde sind, finden Sie weitere Informationen zur Anzeige von Reservierungsrabatten in Nutzungsdaten unter [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md). Wenn Sie ein einzelnes Abonnement haben, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen für Ihr Abonnement mit nutzungsbasierter Bezahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Ändern einer Reservierung nach dem Kauf

Nach dem Kauf können Sie folgende Änderungen an einer Reservierung vornehmen:

- Reservierungsumfang aktualisieren

- Instanzgrößenflexibilität (sofern zutreffend)

- Besitz

Sie können eine Reservierung auch in kleinere Blöcke aufteilen und bereits aufgeteilte Reservierungen zusammenführen. Keine dieser Änderungen führt zu einer neuen Geschäftstransaktion, und das Enddatum der Reservierung bleibt von den Änderungen unberührt.

Folgendes können Sie nach dem Kauf nicht direkt ändern:

- Die Region einer vorhandenen Reservierung

- SKU

- Menge

- Duration

Sie können jedoch eine Reservierung *umtauschen*, wenn Sie Änderungen vornehmen möchten.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwalten einer Reservierung finden Sie unter [Verwalten von Azure-Reservierungen](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Verwenden von Azure Dedicated Hosts](./dedicated-hosts.md)

- [Preise von Dedicated Hosts](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Verwalten von Reservierungen für Ressourcen in Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [Grundlegendes zur Anwendung des Rabatts für Azure-Reservierungen auf virtuelle Computer](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [Grundlegendes zur Reservierungsnutzung bei einem Abonnement mit Sätzen für nutzungsbasierte Bezahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [Verkaufen Microsoft Azure Reserved Instances](/partner-center/azure-reservations)
