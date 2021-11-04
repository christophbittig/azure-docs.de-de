---
title: Konfigurieren von Preisen für die monatliche Abrechnung in Azure Marketplace
description: Erfahren Sie, wie Preise für VMs konfiguriert werden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshah
ms.author: iqshah
ms.date: 09/28/2021
ms.openlocfilehash: 38eecfefab8bbb8690b8125ad034ca3c3b076a1a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076526"
---
# <a name="configure-prices-for-usage-based-monthly-billing"></a>Konfigurieren von Preisen für die nutzungsbasierte monatliche Abrechnung

Beim nutzungsbasierten monatlichen Abrechnungsplan wird dem Kunden seine Nutzung pro Stunde monatlich in Rechnung gestellt. Dies ist unser Plan „Nutzungsbasierte Bezahlung“, bei dem Kunden nur die genutzten Stunden in Rechnung gestellt werden.
Wenn Sie sich für diesen Plan entscheiden, wählen Sie eine der folgenden Preisoptionen aus:

- **Kostenlos**: Ihr VM-Angebot ist kostenlos.
- **Pauschalpreis (empfohlen)** : Ihr VM-Angebot weist immer denselben Preis auf, unabhängig von der Hardware, auf der es ausgeführt wird.
- **Pro Kern**: Die Preise für Ihr VM-Angebot gelten je nach Anzahl von CPU-Kernen. (Sie geben uns den Preis für einen CPU-Kern an, und wir erhöhen die Preise basierend auf der Größe der Hardware.)
- **Pro Kerngröße**: Sie weisen Preise basierend auf der Anzahl der CPU-Kerne auf der Hardware zu, auf der die Bereitstellung erfolgt.
- **Pro Markt und Kerngröße**: Sie weisen Preise basierend auf der Anzahl der CPU-Kerne auf der Hardware, auf der die Bereitstellung erfolgt, sowie für alle Märkte zu. (Die Währungsumrechnung wird von Ihnen als Herausgeber durchgeführt. Diese Option ist einfacher, wenn Sie das Importpreisfeature verwenden.)

Bei der Auswahl einer Preisoption sind einige Dinge zu berücksichtigen:

- Bei den ersten vier Optionen führt Microsoft die Währungsumrechnung durch.
- Microsoft empfiehlt die Verwendung eines Pauschalpreises für Softwarelösungen.
- Die Preise sind festgelegt und können nach der Veröffentlichung nicht mehr angepasst werden. Wenn Sie die Preise für Ihre VM-Angebote jedoch reduzieren möchten, können Sie ein [Supportticket](./support.md) erstellen.

## <a name="new-offering-pricing"></a>Preise für neue Angebote

In Microsoft Azure wird regelmäßig eine neue VM-Infrastruktur hinzugefügt. Gelegentlich fügen wir einen Computer mit einer CPU-Anzahl hinzu, die zuvor nicht angeboten wurde. Microsoft bestimmt den Preis für die neue Kerngröße basierend auf vorherigen Preisen und fügt ihn als Preisvorschlag hinzu.

Herausgeber erhalten eine E-Mail, wenn der Preis für neue Kerngrößen festgelegt wird, und haben dann Zeit, ihn zu überprüfen und nach Bedarf Anpassungen vorzunehmen. Nach Ablauf der Frist veröffentlicht Microsoft die Preise für die neu hinzugefügten Kerngrößen.

Wenn der Herausgeber die Größe „Kostenlos“, „Pauschalpreis“ oder „Pro Kern“ ausgewählt hat, hat der Herausgeber bereits die erforderlichen Details zum Angebotspreis für neue Kerngrößen angegeben, und es ist keine weitere Aktion erforderlich. Wenn der Herausgeber jedoch zuvor die Größe „Pro Kerngröße“ oder „Pro Markt und Kerngröße“ ausgewählt hat, muss er sich mit den aktualisierten Preisinformationen an Microsoft wenden.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie Fragen haben, erstellen Sie ein [Supportticket](./support.md).