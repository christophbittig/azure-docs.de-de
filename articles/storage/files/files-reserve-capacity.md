---
title: Optimieren der Kosten für Azure Files mit reservierter Kapazität
titleSuffix: Azure Files
description: Erfahren Sie, wie Sie Kosten für Azure-Dateifreigabebereitstellungen mit reservierter Azure Files-Kapazität sparen.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7fc4bb71327b2621d60a525ab277305ef13f40c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673234"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Optimieren der Kosten für Azure Files mit reservierter Kapazität
Sie können Sie Geld bei den Speicherkosten für Azure-Dateifreigaben mit reservierter Kapazität sparen. Die reservierte Kapazität von Azure Files bietet Ihnen einen Rabatt auf die Speicherkosten, wenn Sie sich zu einer Reservierung für ein Jahr oder drei Jahre verpflichten. Eine Reservierung stellt eine feste Speicherkapazität für den Zeitraum der Reservierung zur Verfügung.

Reservierte Azure Files-Kapazität kann die Kapazitätskosten für das Speichern von Daten in ihren Azure-Dateifreigaben erheblich verringern. Wie viel Sie speichern, hängt von der Dauer Ihrer Reservierung, der Gesamtkapazität, die Sie reservieren möchten, und den Einstellungen für die Ebene und Redundanz ab, die Sie für Ihre Azure-Dateifreigaben ausgewählt haben. Die reservierte Kapazität ermöglicht lediglich einen Abrechnungsrabatt, der keine Auswirkungen auf den Zustand Ihrer Azure Dateifreigabe hat.

Preisinformationen zur Reservierungskapazität für Azure Files finden Sie unter [Preise für Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |

## <a name="reservation-terms-for-azure-files"></a>Reservierungsbedingungen für Azure Files
In den folgenden Abschnitten werden die Bedingungen einer Azure Files-Reservierung beschrieben.

### <a name="reservation-capacity"></a>Reservierungskapazität
Sie können reservierte Azure Files-Kapazität in Einheiten von 10 TiB und 100 TiB pro Monat für eine Laufzeit von einem Jahr oder drei Jahren erwerben.

### <a name="reservation-scope"></a>Reservierungsumfang
Reservierte Azure Files-Kapazität ist für ein einzelnes Abonnement, für mehrere Abonnements (freigegebener Bereich) sowie für Verwaltungsgruppen verfügbar. Bei Zuordnung zu einem einzelnen Abonnement wird der Reservierungsrabatt nur auf das ausgewählte Abonnement angewendet. Bei Zuordnung zu mehreren Abonnements wird der Reservierungsrabatt auf diese Abonnements innerhalb des Abrechnungskontexts des Kunden aufgeteilt. Wenn eine Verwaltungsgruppe den Geltungsbereich bildet, wird der Reservierungsrabatt auf Abonnements angewandt, die sowohl Teil der Verwaltungsgruppe als auch des Abrechnungsbereichs sind. Die Reservierung gilt für Ihre Nutzung im erworbenen Umfang. Die Reservierung kann nicht auf ein bestimmtes Speicherkonto, einen bestimmten Container oder ein bestimmtes Objekt innerhalb des Abonnements beschränkt werden.

Eine Azure Files-Kapazitätsreservierung deckt nur die Menge der Daten ab, die in einem Abonnement oder einer freigegebenen Ressourcengruppe gespeichert werden. Gebühren für Transaktionen, Bandbreite und Datenübertragung sind nicht in der Reservierung enthalten. Sobald Sie eine Reservierung erworben haben, werden die Kapazitätsgebühren für die jeweiligen Reservierungsattribute rabattiert und nicht mehr als nutzungsbasierte Bezahlung abgerechnet. Weitere Informationen zu Azure-Reservierungen finden Sie unter [Was sind Azure-Reservierungen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-tiers-and-redundancy-options"></a>Unterstützte Tarife und Redundanzoptionen
Reservierte Azure Files-Kapazität ist für Premium- sowie heiße und kalte Dateifreigaben verfügbar. Die reservierte Kapazität ist nicht für Azure-Dateifreigaben im transaktionsoptimierten Tarif verfügbar. Reservierungen werden von allen Speicherredundanzen unterstützt. Weitere Informationen zu Redundanzoptionen finden Sie unter [Azure Files-Redundanz](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Sicherheitsanforderungen für den Erwerb
So erwerben Sie reservierte Kapazität:

- Sie müssen für mindestens ein Enterprise-Abonnement oder ein individuelles Abonnement mit nutzungsbasierter Bezahlung über die Rolle **Besitzer** verfügen.
- Bei Enterprise-Abonnements muss **Reservierte Instanzen hinzufügen** im EA-Portal aktiviert sein. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
- Für das Cloud Solution Provider-Programm (CSP) können nur die Administrator- oder Vertriebs-Agents reservierte Azure Files-Kapazität erwerben.

## <a name="determine-required-capacity-before-purchase"></a>Ermitteln der erforderlichen Kapazität vor dem Erwerb
Wenn Sie eine Azure Files-Reservierung erwerben, müssen Sie Region, Ebene und Redundanzoption für die Reservierung auswählen. Die Reservierung gilt nur für Daten, die in der betreffenden Region, Ebene und Redundanzebene gespeichert werden. Nehmen Sie beispielsweise an, dass Sie eine Reservierung für „USA, Westen“ für die heiße Zugriffsebene mit zonenredundantem Speicher (ZRS) erwerben. Diese Reservierung gilt nicht für Daten in „USA, Osten“, für Daten auf der kalten Speicherebene oder für Daten im georedundanten Speicher (GRS). Sie können jedoch eine weitere Reservierung für die zusätzlichen Anforderungen erwerben.  

Reservierungen sind für 10 TiB- oder 100 TiB-Blöcke möglich (mit höheren Rabatten für 100 TiB-Blöcke). Wenn Sie eine Reservierung im Azure-Portal erwerben, kann Microsoft Ihnen Empfehlungen auf der Grundlage Ihrer bisherigen Nutzung geben, damit Sie die Reservierung bestimmen können, die Sie erwerben sollten.

## <a name="purchase-azure-files-reserved-capacity"></a>Erwerb von reservierter Azure Files-Kapazität
Sie können reservierte Azure Files-Kapazität über das [Azure-Portal](https://portal.azure.com) erwerben. Sie können die Reservierung im Voraus oder monatlich bezahlen. Weitere Informationen zum Erwerb mit monatlichen Zahlungen finden Sie unter [Erwerben von Azure-Reservierungen mit Vorauszahlung oder monatlicher Zahlung](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Hilfe bei der Ermittlung der geeigneten Reservierungsbedingungen für Ihr Szenario finden Sie unter [Grundlegendes zur Rabattierung von reservierter Azure Storage-Kapazität](../../cost-management-billing/reservations/understand-storage-charges.md).

Gehen Sie folgendermaßen vor, um reservierte Kapazität zu erwerben:

1. Navigieren Sie im Azure-Portal zum Blatt [Reservierungen erwerben](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand).  
1. Wählen Sie **Azure Files** aus, um eine neue Reservierung zu erwerben.  
1. Füllen Sie die Pflichtfelder aus, wie in der folgenden Tabelle beschrieben:

    ![Screenshot, der den Erwerb reservierter Kapazität veranschaulicht](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Feld  |BESCHREIBUNG  |
   |---------|---------|
   |**Umfang**   |  Zeigt an, wie viele Abonnements die mit der Reservierung verbundenen Abrechnungsvorteile nutzen können. Sie steuert auch, wie die Reservierung auf bestimmte Abonnements angewendet wird. <br/><br/> Wenn Sie **Gemeinsam** auswählen, gilt der Reservierungsrabatt für die Azure Files-Kapazität in einem beliebigen Abonnement innerhalb des Abrechnungskontexts. Der Abrechnungskontext basiert darauf, wie Sie sich für Azure registriert haben. Für Enterprise-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Bezahlung umfasst der freigegebene Umfang alle Abonnements mit Preisen für nutzungsbasierte Bezahlung, die vom Kontoadministrator erstellt wurden.  <br/><br/>  Wenn Sie **Einzelabonnement** auswählen, gilt der Reservierungsrabatt für die Azure Files-Kapazität im ausgewählten Abonnement. <br/><br/> Wenn Sie die Option **Einzelne Ressourcengruppe** wählen, gilt der Reservierungsrabatt für die Azure File-Kapazität im ausgewählten Abonnement und in der ausgewählten Ressourcengruppe in diesem Abonnement. <br/><br/> Sie können den Reservierungsumfang nach dem Erwerb der Reservierung ändern.  |
   |**Abonnement**  | Das Abonnement, das für die Bezahlung der Azure Files-Reservierung verwendet wird. Die Zahlungsmethode für das ausgewählte Abonnement wird für das Inrechnungstellen der Kosten verwendet. Es muss einer der folgenden Abonnementtypen vorliegen: <br/><br/>  Enterprise Agreement (Angebotsnummer: MS-AZR-0017P oder MS-AZR-0148P): Bei einem Enterprise-Abonnement werden die Gebühren vom Saldo der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) der Reservierung abgezogen oder als Überschreitung belastet. <br/><br/> Einzelnes Abonnement mit Preisen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P): Bei einem individuellen Abonnement mit Preisen für nutzungsbasierte Bezahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung berechnet.    |
   | **Region** | Die Region, in der die Reservierung wirksam ist. |
   | **Tier** | Die Ebene, für die die Reservierung wirksam ist. Zu den Optionen gehören *Premium*, *Heiß* und *Kalt*. |
   | **Redundanz** | Die Redundanzoption für die Reservierung. Die Optionen umfassen *LRS*, *ZRS*, *GRS* und *RA-GZRS*. Weitere Informationen zu Redundanzoptionen finden Sie unter [Azure Files-Redundanz](storage-files-planning.md#redundancy). |
   | **Fakturierungsintervall** | Gibt an, wie oft das Konto für die Reservierung belastet wird. Die Optionen umfassen *Monatlich* oder *Im Voraus*. |
   | **Größe** | Die Menge der zu reservierenden Kapazität |
   |**Begriff**  | Ein Jahr oder drei Jahre   |

1. Nach Auswahl der Parameter für die Reservierung werden die Kosten im Azure-Portal angezeigt. Das Portal zeigt auch den prozentualen Rabatt im Vergleich zur nutzungsbasierten Zahlung an.

1. Prüfen Sie im Bereich **Reservierungen erwerben** die Gesamtkosten der Reservierung. Sie können auch einen Namen für die Reservierung angeben.

Nach dem Erwerb einer Reservierung wird sie automatisch auf alle vorhandenen Azure-Dateifreigaben angewendet, die den Reservierungsbedingungen entsprechen. Wenn Sie noch keine Azure-Dateifreigaben erstellt haben, wird die Reservierung angewendet, wenn Sie eine Ressource erstellen, die den Reservierungsbedingungen entspricht. In beiden Fällen gelten die Reservierungsbedingungen sofort nach dem erfolgreichen Erwerb.

## <a name="exchange-or-refund-a-reservation"></a>Umtauschen oder Rückerstatten von Reservierungen
Reservierungen können unter bestimmten Einschränkungen umgetauscht oder rückerstattet werden. Diese Einschränkungen werden in den folgenden Abschnitten beschrieben.

Navigieren Sie zum Umtauschen oder Rückerstatten von Reservierungen zu den Reservierungsdetails im Azure-Portal. Wählen Sie **Umtauschen** oder **Rückerstatten** aus und befolgen Sie die Anweisungen, um eine Supportanfrage zu übermitteln. Nachdem die Anfrage verarbeitet wurde, erhalten Sie von Microsoft eine E-Mail, die die Erledigung der Anfrage bestätigt.

Weitere Informationen zu Azure-Reservierungsrichtlinien finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Umtauschen einer Reservierung
Durch Umtausch einer Reservierung können Sie eine anteilige Rückerstattung für den nicht genutzten Teil der Reservierung erhalten. Sie können die Rückerstattung dann auf den Kaufpreis einer neuen Azure-Files-Reservierung anwenden.

Die Anzahl der möglichen Umtausche ist nach oben hin nicht begrenzt. Zudem ist der Umtausch gebührenfrei. Der Wert der neu erworbenen Reservierung muss größer-gleich der anteiligen Gutschrift der ursprünglichen Reservierung sein. Eine Azure Files-Reservierung kann nur gegen eine andere Azure Files-Reservierung und nicht gegen eine Reservierung eines anderen Azure-Diensts umgetauscht werden.

### <a name="refund-a-reservation"></a>Rückerstatten einer Reservierung
Azure Files-Reservierungen können jederzeit storniert werden. Bei der Stornierung erhalten Sie eine anteilige Rückerstattung basierend auf der Restlaufzeit der Reservierung abzüglich einer Gebühr für vorzeitige Kündigung in Höhe von 12 Prozent. Die maximale Erstattung pro Jahr beträgt 50.000 USD.

Bei der Stornierung einer Reservierung wird die Reservierung sofort beendet und die verbleibenden Monate werden an Microsoft zurückgegeben. Der verbleibende anteilige Saldo wird in der ursprünglichen Kaufform erstattet, abzüglich der Gebühr.

## <a name="expiration-of-a-reservation"></a>Ablauf einer Reservierung
Wenn eine Reservierung abläuft, wird jegliche Azure Files-Kapazität, die Sie unter dieser Reservierung nutzen, mit dem Preis für nutzungsbasierte Bezahlung abgerechnet. Reservierungen werden nicht automatisch verlängert.

Sie erhalten 30 Tage vor dem Ablauf der Reservierung und am Ablaufdatum eine E-Mail-Benachrichtigung. Um die Vorteile der Kosteneinsparungen einer Reservierung weiter zu nutzen, muss die Reservierung spätestens am Ablaufdatum verlängert werden.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt
Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte
- [Was sind Azure-Reservierungen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Grundlegendes zur Anwendung von Reservierungsrabatten auf Azure Files-Dienste](../../cost-management-billing/reservations/understand-storage-charges.md)
