---
title: Erstellen kostenloser Dienste mit kostenlosem Azure-Konto
description: Informationen zum Erstellen von Diensten, die im kostenlosen Azure-Konto enthalten sind. Sie können diese Dienste in jeder beliebigen Region erstellen, in der sie zur Verfügung stehen.
author: bandersmsft
ms.reviewer: amberrb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 34a176c4365f124029149a9663ea1a13409064a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457960"
---
# <a name="create-services-included-with-azure-free-account"></a>Erstellen von Diensten, die im kostenlosen Azure-Konto enthalten sind

Während der ersten 30 Tage nach dem Erstellen eines kostenlosen Azure-Kontos haben Sie ein Guthaben von 200 USD in der Abrechnungswährung, das Sie für jeden beliebigen Dienst – außer Marketplace-Käufe von Drittanbietern – nutzen können. Sie können mit verschiedenen Tarifen und Typen von Azure-Diensten experimentieren und zum Ausprobieren von Azure das kostenlose Guthaben nutzen. Wenn Sie Dienste oder Azure-Ressourcen nutzen, die während dieses Zeitraums nicht kostenlos sind, werden die entsprechenden Gebühren von Ihrem Guthaben abgezogen.

Wenn Sie Ihr gesamtes Guthaben nicht bis zum Ende der ersten 30 Tage nutzen, geht es verloren. Nach den ersten 30 Tagen und bis zu 12 Monate nach der Registrierung können Sie nur eine begrenzte Menge von *einigen Diensten* nutzen, weil nicht alle Azure-Dienste kostenlos sind. Wenn Sie vor Ablauf der 30 Tage ein Upgrade durchführen und Ihnen noch ein Guthaben verbleibt, können Sie den Rest davon mit einem Abonnement mit nutzungsbasierter Bezahlung während der restlichen Tage nutzen. Wenn Sie sich beispielsweise am 1. November für das kostenlose Konto registrieren und das Upgrade am 5. November durchführen, haben Sie bis zum 30. November Zeit, Ihr Guthaben im neuen Abonnement mit nutzungsbasierter Bezahlung zu nutzen. 

Ihr kostenloses Azure-Konto enthält eine *angegebene Menge* von kostenlosen Diensten für 12 Monate und eine Reihe von Diensten, die immer kostenlos sind. Es stehen nur einige Dienstebenen innerhalb bestimmter Mengen kostenlos zur Verfügung. So enthält Azure beispielsweise viele virtuelle Computer, die für unterschiedliche Anforderungen vorgesehen sind. Das kostenlose Konto enthält kostenlosen Zugriff auf nur einen VM-Typ – die burstfähige B1S B-Serie, die bis zu 750 Stunden pro Monat genutzt werden kann. Wenn Sie die Grenzwerte für das kostenlose Konto einhalten, können Sie die kostenlosen Dienste in verschiedenen Konfigurationen nutzen. Weitere Informationen zum kostenlosen Azure-Konto und den kostenlos verfügbaren Produkten finden Sie in [FAQ zum kostenlosen Azure-Konto](https://azure.microsoft.com/free/free-account-faq/).

## <a name="create-free-services-in-the-azure-portal"></a>Erstellen kostenloser Dienste im Azure-Portal

Wir empfehlen, dass Sie kostenlose Dienste über die [Seite „Kostenlose Dienste“](https://go.microsoft.com/fwlink/?linkid=859151) im Azure-Portal erstellen. Alternativ können Sie sich beim [Azure-Portal](https://portal.azure.com) anmelden und nach **Kostenlose Dienste** suchen. Wenn Sie Ressourcen außerhalb der Seiten für „Kostenlose Dienste“ erstellen, sind die Optionen für „Kostenlose Tarife“ oder „Kostenlose Ressourcenkonfiguration“ nicht immer standardmäßig ausgewählt. Wenn Sie Gebühren vermeiden möchten, müssen Sie Ressourcen auf der Seite „Kostenlose Dienste“ erstellen. Wenn Sie dann eine Ressource erstellen, müssen Sie den kostenlosen Tarif auswählen.

![Screenshot der Seite „Kostenlose Dienste“](./media/create-free-services/billing-freeservices-grid.png)

## <a name="services-can-be-created-in-any-region"></a>Dienste können in jeder Region erstellt werden

Solange Sie die Grenzwerte einhalten, können Sie Dienste kostenlos in einer beliebigen Region erstellen, in der Dienste zur Verfügung stehen. Beispielsweise erhalten Sie mit dem kostenlosen Azure-Konto jeden Monat kostenlos 750 Stunden eines virtuellen B1S-Windows-Computers. Sie können den virtuellen Computer in einer beliebigen Region erstellen, in der virtuelle Computer der B-Serie verfügbar sind. Azure berechnet Ihnen nichts, es sei denn, Sie überschreiten 750 Stunden. Ein Kunde in den USA kann z.B. einen virtuellen B1S-Windows-Computer in „Europa, Westen“ bereitstellen und 750 Stunden lang kostenlos verwenden.

Weitere Informationen zur Azure-Dienstverfügbarkeit nach Region finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

## <a name="create-multiple-service-instances-in-allowed-limits"></a>Erstellen von mehreren Dienstinstanzen innerhalb der zulässigen Grenzwerte

Sie können mehrere Instanzen von Diensten kostenlos erstellen, wenn Ihre Gesamtnutzung innerhalb des Nutzungslimits liegt. Beispielsweise erhalten Sie mit dem kostenlosen Azure-Konto jeden Monat kostenlos 750 Stunden eines virtuellen B1S-Windows-Computers. Verwenden Sie 750 Stunden in jeder beliebigen Kombination. Sie können 5 virtuelle B1S-Windows-Computer erstellen und jeweils 150 Stunden lang verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Erhalten Sie weitere Informationen zum [Überprüfen der Nutzung kostenloser Dienste, die in Ihrem kostenlosen Azure-Konto enthalten sind](check-free-service-usage.md).
- Lesen Sie den Artikel [Vermeiden Sie, dass Ihnen für Ihr kostenloses Azure-Konto Gebühren angezeigt werden](avoid-charges-free-account.md).
