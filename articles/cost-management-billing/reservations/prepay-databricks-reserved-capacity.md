---
title: Verringern der Kosten für Azure Databricks durch Vorauszahlung
description: Hier erfahren Sie, wie Sie für Azure Databricks-Gebühren mit reservierter Kapazität vorauszahlen und so Geld sparen können.
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/19/2021
ms.author: banders
ms.openlocfilehash: 72bb3403a49431d1dbc1cbd9d15ac37348818118
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130243826"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Verringern der Kosten für Azure Databricks durch Vorauszahlung

Wenn Sie Azure Databricks-Commit-Einheiten (Azure Databricks Commit Units, DBCUs) für ein oder drei Jahre vorab erwerben, können Sie DBU-Kosten (Azure Databricks Units) sparen. Sie können die vorab erworbenen DBCUs jederzeit während der Laufzeit des Kaufs nutzen. Im Gegensatz zu virtuellen Computern laufen die vorab erworbenen Einheiten nicht stündlich ab, und Sie können sie zu einem beliebigen Zeitpunkt während der Laufzeit des Kaufs nutzen.

Jede Azure Databricks-Nutzung wird automatisch auf die vorab erworbenen DBUs angerechnet. Um die Vorauszahlungsrabatte zu erhalten, ist es nicht erforderlich, für die DBU-Nutzung einen Plan mit Vorauszahlung erneut bereitzustellen oder Ihren Azure Databricks-Arbeitsbereichen zuzuweisen.

Der Vorauszahlungsrabatt gilt nur für die DBU-Nutzung. Andere Gebühren, z. B. Compute-, Speicher- und Netzwerkgebühren, werden separat abgerechnet.

## <a name="determine-the-right-size-to-buy"></a>Bestimmen der richtigen Größe für den Kauf

Databricks mit Vorauszahlung gilt für alle Databricks-Workloads und -Tarife. Sie können den Plan mit Vorauszahlung als Pool im Voraus bezahlter Databricks-Commit-Einheiten vorstellen. Die Nutzung wird unabhängig von der Arbeitsauslastung und dem Tarif auf den Pool angerechnet. Für die Anrechnung der Nutzung gilt das folgende Verhältnis:

| **Workload** | **Verhältnis der Anwendung von DBUs – Standard-Tarif** | **Verhältnis der Anwendung von DBUs – Premium-Tarif** |
| --- | --- | --- |
| Datenanalyse | 0,4 | 0,55 |
| Datentechnik | 0,15 | 0,30 |
| Datentechnik Light | 0,07 | 0,22 |

Wenn z. B. ein Betrag von Datenanalysen – Standard-Tarif genutzt wird, werden auf die vorab erworbenen Databricks-Commit-Einheiten 0,4 Einheiten angerechnet.

Berechnen Sie vor dem Kauf die DBU-Gesamtmenge, die für unterschiedliche Workloads und Tarife verwendet wird. Normalisieren Sie die DBCU-Menge anhand der oben genannten Verhältnisse, und führen Sie dann eine Projektion der Gesamtnutzung über die nächsten ein oder drei Jahre aus.

## <a name="purchase-databricks-commit-units"></a>Erwerben von Databricks-Commit-Einheiten

Sie können Databricks-Pläne im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D) erwerben. Zum Erwerben reservierter Kapazität müssen Sie über die Besitzerrolle für mindestens ein Enterprise-Abonnement verfügen.

- Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement zugeordnet sein (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Microsoft-Kundenvereinbarung“ oder ein einzelnes Abonnement mit Sätzen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein.
- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator des Abonnements sein, um sie zu aktivieren. Direkte EA-Kunden können nun die Einstellung **Reservierte Instanz** im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) aktualisieren. Navigieren Sie zum Menü „Richtlinien“, um die Einstellungen zu ändern.


**Vorgehensweise für den Erwerb:**

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Wählen Sie ein Abonnement aus. Wählen Sie in der Liste **Abonnement** das Abonnement aus, das für den Kauf der reservierten Kapazität verwendet wird. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die reservierte Kapazität belastet. Die Gebühren werden vom Saldo der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) der Registrierung abgezogen oder als Überschreitung belastet.
1. Wählen Sie einen Bereich aus. Wählen Sie in der Liste **Bereich** einen Abonnementbereich aus:
    - **Einzelne Ressourcengruppe**: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.
    - **Einzelnes Abonnement**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.
    - **Gemeinsam genutzt**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für Kunden mit einem Enterprise Agreement ist der Abrechnungskontext die Registrierung.
    - **Verwaltungsgruppe** – Wendet den Reservierungsrabatt auf die entsprechende Ressource in der Liste der Abonnements an, die sowohl Teil der Verwaltungsgruppe als auch des Abrechnungsbereichs sind.
1. Wählen Sie aus, wie viele Azure Databricks-Commit-Einheiten Sie erwerben möchten, und schließen Sie den Kauf ab.


![Beispiel für Azure Databricks-Kauf im Azure-Portal](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Ändern des Bereichs und Besitzers

Nach dem Kauf können Sie folgende Änderungen an einer Reservierung vornehmen:

- Reservierungsumfang aktualisieren
- Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)

Die vorab erworbenen Databricks-Commit-Einheiten können nicht aufgeteilt oder zusammengeführt werden. Weitere Informationen zum Verwalten von Reservierungen finden Sie unter [Verwalten von Reservierungen nach dem Kauf](manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Stornierungen und Umtausch

Stornierung und Umtausch werden für vorab erworbene Databricks-Pläne nicht unterstützt. Alle Käufe sind endgültig.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
  - [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
  - [Anwenden eines Azure Databricks-Vorauszahlungsrabatts](reservation-discount-databricks.md)
  - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
