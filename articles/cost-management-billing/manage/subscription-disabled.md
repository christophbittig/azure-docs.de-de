---
title: Reaktivieren eines deaktivierten Azure-Abonnements
description: Es wird beschrieben, wie Sie ein ggf. deaktiviertes Azure-Abonnement reaktivieren.
keywords: Deaktiviertes Azure-Abonnement
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/15/2021
ms.author: banders
ms.openlocfilehash: 1e669a794abe848d326a6d08156621dcf3883ec5
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292234"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Reaktivieren eines deaktivierten Azure-Abonnements

Ihr Azure-Abonnement kann deaktiviert werden, weil Ihr Guthaben abgelaufen ist, Sie Ihr Ausgabenlimit erreicht haben, eine Rechnung überfällig ist, das Kreditkartenlimit erreicht ist oder das Abonnement vom Kontoadministrator gekündigt wurde. Prüfen Sie, welches Problem für Sie vorliegen könnte, und führen Sie die Schritte in diesem Artikel aus, um Ihr Abonnement reaktivieren zu lassen.

## <a name="your-credit-is-expired"></a>Ihre Gutschrift ist abgelaufen

Wenn Sie sich für ein kostenloses Azure-Konto registrieren, erhalten Sie ein kostenloses Testabonnement, bei dem Sie über eine Azure-Gutschrift in Höhe von 200 US-Dollar in Ihrer Abrechnungswährung für 30 Tage sowie 12 Monate lang über kostenlose Dienste verfügen. Nach Ablauf der 30 Tage deaktiviert Azure Ihr Abonnement. Ihr Abonnement wird deaktiviert, um zu verhindern, dass versehentlich Kosten für die Nutzung anfallen, die die für das Abonnement festgelegte Gutschrift und die kostenlosen Dienste übersteigen. Um Azure-Dienste weiterhin zu verwenden, ist [ein Upgrade Ihres Abonnements](upgrade-azure-subscription.md) erforderlich. Nach dem Upgrade hat Ihr Abonnement noch 12 Monate lang Zugriff auf kostenlose Dienste. Ihnen wird nur die Nutzung in Rechnung gestellt, die über die Mengengrenzwerte für kostenlose Dienste hinausgeht.

## <a name="you-reached-your-spending-limit"></a>Sie haben Ihr Ausgabenlimit erreicht

Für Azure-Abonnements mit Gutschrift, wie etwa die kostenlose Testversion und Visual Studio Enterprise, gelten Ausgabenlimits. Sie können nur Dienste bis zur Höhe der enthaltenen Gutschrift verwenden. Wenn Ihre Nutzung das Ausgabenlimit erreicht, deaktiviert Azure Ihr Abonnement für den Rest des Abrechnungszeitraums. Ihr Abonnement wird deaktiviert, um zu verhindern, dass versehentlich Kosten für die Nutzung anfallen, die die für das Abonnement festgelegte Gutschrift übersteigen. Informationen zum Entfernen Ihres Ausgabenlimits finden Sie unter [Entfernen des Ausgabenlimits im Azure-Portal](spending-limit.md#remove).

> [!NOTE]
> Wenn Sie über ein Abonnement des Typs „Kostenlose Testversion“ verfügen und das Ausgabenlimit entfernen, wird das Abonnement am Ende des kostenlosen Testzeitraums auf ein individuelles Abonnement mit nutzungsbasierten Tarifen umgestellt. Sie behalten Ihr Restguthaben für die vollen 30 Tage nach Erstellung des Abonnements. Sie haben auch Zugang zu kostenlosen Diensten für 12 Monate.

Informationen zum Überwachen und Verwalten von Abrechnungsaktivitäten für Azure finden Sie unter [Planen der Verwaltung von Azure-Kosten](../understand/plan-manage-costs.md).

## <a name="your-bill-is-past-due"></a>Ihre Rechnung ist überfällig

Informationen zum Begleichen überfälliger Zahlungen finden Sie in folgenden Artikeln:

- Informationen zu Abonnements des Microsoft-Onlineabonnementprogramms (einschließlich nutzungsbasierte Bezahlung) finden Sie unter [Begleichen überfälliger Zahlungen für Ihr Azure-Abonnement](resolve-past-due-balance.md).
- Informationen zu Abonnements mit Microsoft-Kundenvereinbarung finden Sie unter [Bezahlen Ihrer Rechnung für Microsoft Azure](../understand/pay-bill.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>Die Rechnung überschreitet Ihr Kreditkartenlimit

[Verwenden Sie eine andere Kreditkarte](change-credit-card.md), um dieses Problem zu beheben. Wenn Sie das Abonnement als Mitarbeiter eines Unternehmens nutzen, können Sie auch auf [Zahlung per Rechnung](pay-by-invoice.md) umstellen.

## <a name="the-subscription-was-accidentally-canceled"></a>Das Abonnement wurde versehentlich gekündigt

Wenn Sie der Kontoadministrator sind und versehentlich ein Abonnement mit nutzungsbasierter Bezahlung gekündigt haben, können Sie es im Azure-Portal reaktivieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu „Abonnements“, und wählen Sie das stornierte Abonnement aus.
1. Wählen Sie **Reaktivieren** aus.
1. Bestätigen Sie die Reaktivierung durch Auswählen von **OK**.  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="Screenshot: Bestätigen der Reaktivierung" :::

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie andere Abonnementtypen verwenden, um Ihr Abonnement reaktivieren zu lassen.

## <a name="after-reactivation"></a>Nach der Reaktivierung

Nach der erneuten Aktivierung Ihres Abonnements kann es zu einer Verzögerung beim Erstellen oder Verwalten von Ressourcen kommen. Sollte die Verzögerung 30 Minuten übersteigen, wenden Sie sich an den [Azure-Abrechnungssupport](https://go.microsoft.com/fwlink/?linkid=2083458), um Hilfe zu erhalten. Die meisten Azure-Ressourcen werden automatisch fortgesetzt, sodass keine Aktion erforderlich ist. Es wird jedoch empfohlen, die Azure-Dienstressourcen zu überprüfen und alle Ressourcen, die nicht automatisch fortgesetzt werden, neu zu starten.

## <a name="upgrade-a-disabled-free-account"></a>Durchführen eines Upgrades für ein deaktiviertes kostenloses Konto

Wenn Sie kostenpflichtige Ressourcen verwenden und Ihr Abonnement deaktiviert wird, weil Ihnen kein Guthaben mehr zur Verfügung steht, und Sie dann ein Upgrade für Ihr Abonnement durchführen, werden die Ressourcen nach dem Upgrade aktiviert. Dies führt dazu, dass Ihnen die verwendeten Ressourcen in Rechnung gestellt werden. Weitere Informationen zum Durchführen eines Upgrades für ein kostenloses Konto finden Sie unter [Durchführen eines Upgrades für Ihr kostenloses Azure-Konto oder Ihr Azure for Students Starter-Konto](upgrade-azure-subscription.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über das [Planen der Verwaltung von Azure-Kosten](../understand/plan-manage-costs.md).
