---
title: Optimieren der Kosten für Azure Disk Storage mit Reservierungen
description: Hier finden Sie Informationen zum Erwerben von Azure Disk Storage-Reservierungen, um Kosten für verwaltete SSD Premium-Datenträger zu sparen.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 6e58548365be63ed13a8ac20c42cca81b66bc407
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593038"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Senken der Kosten durch Azure-Datenträgerreservierung

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Sparen Sie beim Azure Disk Storage-Verbrauch mit reservierter Kapazität. Mit Azure Disk Storage-Reservierungen in Kombination mit Azure Reserved Virtual Machine Instances können Sie die Gesamtkosten virtueller Computer (VM) senken. Der Reservierungsrabatt wird automatisch auf die entsprechenden Datenträger im ausgewählten Reservierungsbereich angewandt. Aufgrund dieser automatischen Anwendung müssen Sie einem verwalteten Datenträger keine Reservierung zuweisen, um die Rabatte zu erhalten.

Rabatte werden stundenweise in Abhängigkeit von der Datenträgernutzung angewandt. Nicht genutzte Reservekapazität wird nicht übertragen. Der Reservierungsrabatt für Azure Disk Storage gilt nicht für nicht verwaltete Datenträger, Ultra Disks oder die Nutzung von Seitenblobs.

## <a name="determine-your-storage-needs"></a>Ermitteln Ihrer Speicheranforderungen

Bevor Sie eine Reservierung erwerben, sollten Sie Ihre Speicheranforderungen ermitteln. Derzeit sind Azure Disk Storage-Reservierungen nur für ausgewählte Azure SSD Premium-SKUs verfügbar. Die SKU eines SSD Premium-Datenträgers bestimmt die Größe und Leistung des Datenträgers.

Berücksichtigen Sie beim Ermitteln Ihrer Speicheranforderungen nicht nur die Kapazität Ihrer Datenträger. Es ist beispielsweise nicht möglich, einen P40-Datenträger zu reservieren und damit zwei kleinere P30-Datenträger zu bezahlen. Wenn Sie eine Reservierung erwerben, erwerben Sie nur die Reservierung für die Gesamtanzahl von Datenträgern pro SKU.

Die Datenträgerreservierung erfolgt auf der Grundlage der Datenträger-SKU. Daher basiert der Reservierungsverbrauch auf der Einheit der Datenträger-SKUs und nicht auf der bereitgestellten Größe.

Nehmen Sie beispielsweise an, dass Sie einen P40-Datenträger mit 2 TiB bereitgestellter Speicherkapazität reservieren. Gehen Sie außerdem davon aus, dass Sie nur zwei P30-Datenträger zuordnen. Die P40-Reservierung wird in diesem Fall nicht für den P30-Verbrauch berücksichtigt, und Sie zahlen die Rate der nutzungsbasierten Bezahlung für die P30-Datenträger.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Überlegungen zum Kauf

Im Anschluss finden Sie einige bewährte Methoden im Zusammenhang mit dem Erwerb von Datenträgerreservierungen:

- Analysieren Sie Ihre Nutzungsinformationen, um besser ermitteln zu können, welche Reservierungen Sie erwerben sollten. Verfolgen Sie den Verbrauch in Datenträger-SKUs und nicht in bereitgestellter oder verwendeter Datenträgerkapazität.
- Untersuchen Sie Ihre Datenträgerreservierung zusammen mit Ihrer VM-Reservierung. Es wird dringend empfohlen, den VM-Verbrauch mit dem Datenträgerverbrauch zu kombinieren, um möglichst viel zu sparen. Sie können zunächst die passende VM-Reservierung und anschließend eine passende Datenträgerreservierung ermitteln. Im Allgemeinen verfügen Sie über eine Standardkonfiguration für jede Ihrer Workloads. Beispielsweise kann ein SQL Server-Server über zwei P40-Datenträger und einen P30-Datenträger für das Betriebssystem verfügen.
  
  Dieses Muster kann bei der Festlegung der zu erwerbenden Reservierungsmenge hilfreich sein. Diese Vorgehensweise vereinfacht den Evaluierungsprozess und stellt außerdem sicher, dass Sie über einen Tarif verfügen, der sowohl auf den virtuellen Computer als auch auf die Datenträger abgestimmt ist. Der Plan enthält Überlegungen wie Abonnements oder Regionen.

## <a name="purchase-restrictions"></a>Einschränkungen beim Kauf

Für Folgendes stehen derzeit keine Reservierungsrabatte zur Verfügung:

- Nicht verwaltete Datenträger und Seitenblobs
- SSD Standard und Standard-Festplattenlaufwerke (HDDs)
- SSD Premium-SKUs kleiner als P30: SSD-SKUs P1, P2, P3, P4, P6, P10, P15 und P20
- Datenträger in den Regionen Azure Government, Azure Deutschland und Azure China

In seltenen Fällen beschränkt Azure den Erwerb neuer Reservierungen für einen Teil der Datenträger-SKUs aufgrund von geringer Kapazität in einer Region.

## <a name="buy-a-disk-reservation"></a>Erwerben einer Datenträgerreservierung

Azure Disk Storage-Reservierungen können über das [Azure-Portal](https://portal.azure.com/) erworben werden. Sie können die Reservierung im Voraus oder monatlich bezahlen. Weitere Informationen zum Erwerb mit monatlicher Zahlung finden Sie unter [Erwerben von Reservierungen mit monatlicher Zahlung](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments).

Gehen Sie folgendermaßen vor, um reservierte Kapazität zu erwerben:

1. Navigieren Sie im Azure-Portal zum Bereich [Reservierungen erwerben](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand).

1. Wählen Sie **Azure Managed Disks** aus, um eine Reservierung zu erwerben.

    ![Bereich für den Erwerb von Reservierungen](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Geben Sie die Pflichtwerte wie in der folgenden Tabelle beschrieben an:

   |Element  |Beschreibung  |
   |---------|---------|
   |**Umfang**   |  Die Anzahl der Abonnements, die die mit der Reservierung verbundenen Abrechnungsvorteile nutzen können. Dieser Wert gibt auch an, wie die Reservierung auf bestimmte Abonnements angewandt wird. <br/><br/> Wenn Sie **Gemeinsam** auswählen, gilt der Reservierungsrabatt für die Azure Storage-Kapazität in jedem Abonnement innerhalb des Abrechnungskontexts. Der Abrechnungskontext basiert darauf, wie Sie sich für Azure registriert haben. Für Enterprise-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Bezahlung umfasst der freigegebene Umfang alle Abonnements mit Preisen für nutzungsbasierte Bezahlung, die vom Kontoadministrator erstellt wurden. </br></br>Wenn Sie **Verwaltungsgruppe** auswählen, wird der Reservierungsrabatt auf Azure Storage Kapazität in jedem Abonnement angewendet, das Teil der Verwaltungsgruppe und des Abrechnungsbereichs ist. <br/><br/>  Wenn Sie **Einzelabonnement** auswählen, gilt der Reservierungsrabatt für die Azure Storage-Kapazität im ausgewählten Abonnement. <br/><br/> Wenn Sie die Option **Einzelne Ressourcengruppe** auswählen, gilt der Reservierungsrabatt für die Azure Storage-Kapazität im ausgewählten Abonnement und in der ausgewählten Ressourcengruppe innerhalb dieses Abonnements. <br/><br/> Sie können den Reservierungsumfang nach dem Erwerb der Reservierung ändern.  |
   |**Abonnement**  | Das Abonnement, das für die Bezahlung der Azure Storage-Reservierung verwendet wird. Die Zahlungsmethode für das ausgewählte Abonnement wird für das Inrechnungstellen der Kosten verwendet. Es muss einer der folgenden Abonnementtypen vorliegen:<br/><ul><li> Enterprise Agreement (Angebotsnummern MS-AZR-0017P und MS-AZR-0148P). Bei einem Enterprise-Abonnement werden die Gebühren vom Saldo der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) der Reservierung abgezogen oder als Überschreitung belastet.</li><br/><li>Ein einzelnes Abonnement mit Raten für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P und MS-AZR-0023P). Bei einem individuellen Abonnement mit Preisen für nutzungsbasierte Bezahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung berechnet.</li></ul>    |
   | **Datenträger** | Die SKU, die Sie erstellen möchten. |
   | **Region** | Die Region, in der die Reservierung wirksam ist. |
   | **Fakturierungsintervall** | Gibt an, wie oft das Konto für die Reservierung belastet wird. Die Optionen umfassen **Monatlich** und **Im Voraus**. |

    ![Bereich für die Auswahl des Produkts, das Sie erwerben möchten.](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Nach Angabe der Werte für die Reservierung werden die Kosten im Azure-Portal angezeigt. Das Portal zeigt auch den prozentualen Rabatt im Vergleich zur nutzungsbasierten Zahlung an. Wählen Sie **Weiter** aus, um zum Bereich **Reservierungen erwerben** zu gelangen.

1. Im Bereich **Reservierungen erwerben** können Sie Ihre Reservierung benennen und die gewünschte Gesamtmenge der Reservierungen auswählen. Die Anzahl von Reservierungen entspricht der Anzahl von Datenträgern. Wenn Sie also beispielsweise hundert Datenträger reservieren möchten, legen Sie **Menge** auf den Wert **100** fest.

1. Überprüfen Sie die Gesamtkosten der Reservierung.

    ![Der Bereich „Reservierungen erwerben“](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Nach dem Erwerb einer Reservierung wird sie automatisch auf alle vorhandenen Disk Storage-Ressourcen angewandt, die den Reservierungsbedingungen entsprechen. Sollten Sie noch keine Disk Storage-Ressourcen erstellt haben, wird die Reservierung angewandt, wenn Sie eine Ressource erstellen, die den Reservierungsbedingungen entspricht. In beiden Fällen gelten die Reservierungsbedingungen sofort nach dem erfolgreichen Erwerb.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="expiration-of-a-reservation"></a>Ablauf einer Reservierung

Beim Ablauf einer Reservierung wird jegliche Azure Disk Storage-Kapazität, die Sie nach der Reservierung weiter nutzen, gemäß den Raten für die nutzungsbasierte Bezahlung in Rechnung gestellt. Reservierungen werden nicht automatisch verlängert.

Sie erhalten 30 Tage vor dem Ablauf der Reservierung und am Ablaufdatum eine E-Mail-Benachrichtigung. Um die Vorteile der Kosteneinsparungen einer Reservierung weiter zu nutzen, muss die Reservierung spätestens am Ablaufdatum verlängert werden.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Was sind Azure-Reservierungen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Grundlegendes zur Anwendung Ihres Reservierungsrabatts auf Azure Disk Storage](../cost-management-billing/reservations/understand-disk-reservations.md)