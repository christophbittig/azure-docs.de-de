---
title: Optimieren der Kosten für Azure Synapse Analytics mit einem Plan mit Vorauszahlung
description: Erfahren Sie, wie Sie Kosten für Azure Synapse Analytics sparen können, indem Sie Azure Synapse-Commit-Einheiten (SCUs) für ein Jahr im Voraus erwerben.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/08/2021
ms.author: banders
ms.openlocfilehash: 031dc79bfabf03bfadf0c7e01171cbb72a15008e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007274"
---
# <a name="optimize-azure-synapse-analytics-costs-with-a-pre-purchase-plan"></a>Optimieren der Kosten für Azure Synapse Analytics mit einem Plan mit Vorauszahlung

Sie können Kosten für Azure Synapse Analytics sparen, indem Sie Azure Synapse-Commit-Einheiten (SCUs) für ein Jahr im Voraus erwerben. Sie können die vorab erworbenen SCUs jederzeit während der Laufzeit des Kaufs nutzen. Im Gegensatz zu VMs laufen die vorab erworbenen Einheiten nicht stündlich ab, und Sie können sie zu einem beliebigen Zeitpunkt während der Laufzeit des Kaufs nutzen.

Jede Nutzung von Azure Synapse Analytics wird automatisch von den vorab erworbenen SCUs abgezogen. Sie müssen Ihre Azure Synapse Analytics-Arbeitsbereiche weder erneut bereitstellen noch einen Plan mit Vorauszahlung zuweisen, um die Vorauszahlungsrabatte zu erhalten.

## <a name="determine-the-right-size-to-buy"></a>Bestimmen der richtigen Größe für den Kauf

Ein Synapse-Voraberwerb gilt für alle Synapse-Workloads und -Tarife. Sie können sich den Plan mit Vorauszahlung als einen Pool mit vorab bezahlten Synapse-Commit-Einheiten vorstellen. Die Nutzung wird unabhängig von der Arbeitsauslastung und dem Tarif auf den Pool angerechnet. Andere Gebühren, z. B. Compute-, Speicher- und Netzwerkgebühren, werden separat abgerechnet.

Der Synapse-Vorauszahlungsrabatt gilt für die Nutzung der folgenden Produkte:

- Azure Synapse Analytics – Dedizierter SQL-Pool
- Azure Synapse Analytics – Verwaltetes VNET
- Azure Synapse Analytics – Pipelines
- Azure Synapse Analytics – Serverloser SQL-Pool
- Azure Synapse Analytics – Serverloser Apache Spark-Pool (speicheroptimiert)
- Azure Synapse Analytics – Datenfluss (Basic)
- Azure Synapse Analytics – Datenfluss (Standard)

Weitere Informationen zu verfügbaren SCU-Tarifen und Preisrabatten finden Sie im folgenden Abschnitt zum Reservierungserwerb.

## <a name="purchase-synapse-commit-units"></a>Erwerben von Synapse-Commit-Einheiten

Sie kaufen Synapse-Pläne im [Azure-Portal](https://portal.azure.com). Zum Erwerben eines Plans mit Vorauszahlung müssen Sie über die Besitzerrolle für mindestens ein Enterprise-Abonnement verfügen.

- Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement zugeordnet sein (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Microsoft-Kundenvereinbarung“ oder ein einzelnes Abonnement mit Sätzen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein.
- Bei EA-Abonnements (Enterprise Agreement) muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator des Abonnements sein.

### <a name="to-purchase"></a>Vorgehensweise für den Erwerb:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/?synapse=true#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand/autoOpenSpecPicker//productType/Reservation).
1. Navigieren Sie bei Bedarf zu **Reservierungen,**, und wählen Sie dann oben auf der Seite die Option **+ Hinzufügen** aus.
1. Wählen Sie auf der Seite „Reservierungen erwerben“ die Option **Azure Synapse Analytics-Plan mit Vorauszahlung** aus.
1. Wählen Sie auf der Seite „Wählen Sie das Produkt aus, das Sie erwerben möchten“ ein Abonnement aus. Wählen Sie in der Liste **Abonnement** das Abonnement aus, das für den Kauf der reservierten Kapazität verwendet wird. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die reservierte Kapazität belastet. Die Gebühren werden vom Saldo der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) der Registrierung abgezogen oder als Überschreitung belastet.
1. Wählen Sie einen Bereich aus. Wählen Sie in der Liste **Bereich** einen Abonnementbereich aus:
    - **Einzelne Ressourcengruppe**: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.
    - **Einzelabonnement**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.
    - **Gemeinsam genutzt**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für Kunden mit einem Enterprise Agreement ist der Abrechnungskontext die Registrierung.
    - **Verwaltungsgruppe** – Wendet den Reservierungsrabatt auf die entsprechende Ressource in der Liste der Abonnements an, die sowohl Teil der Verwaltungsgruppe als auch des Abrechnungsbereichs sind.
1. Wählen Sie aus, wie viele Azure Synapse-Commit-Einheiten Sie erwerben möchten, und schließen Sie dann den Kauf ab.  
    :::image type="content" source="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" alt-text="Screenshot der Benutzeroberfläche zum Auswählen des Produkts für den Azure Synapse Analytics-Plan mit Vorauszahlung" lightbox="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" :::

## <a name="change-scope-and-ownership"></a>Ändern des Bereichs und Besitzers

Nach dem Kauf können Sie folgende Änderungen an einer Reservierung vornehmen:

- Reservierungsumfang aktualisieren
- Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)

Sie können einen Plan mit Vorauszahlung für Synapse-Commit-Einheiten nicht aufteilen oder zusammenführen. Weitere Informationen zum Verwalten von Reservierungen finden Sie unter [Verwalten von Reservierungen nach dem Kauf](manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Stornierungen und Umtausch

Stornierung und Umtausch werden für Synapse-Pläne mit Vorauszahlung nicht unterstützt. Alle Käufe sind endgültig.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zum Rabatt für Azure-Reservierungen](understand-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)