---
title: 'Erste Schritte mit dem Abrechnungskonto für eine Microsoft-Kundenvereinbarung: Azure'
description: Informieren Sie sich über Ihr Abrechnungskonto für eine Microsoft-Kundenvereinbarung, einschließlich Abrechnungsprofilen und Zahlungsmethoden für Rechnungen.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: d311efbc6b79443bcea9288d5150a7489deb6991
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653436"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Erste Schritte mit dem Abrechnungskonto für eine Microsoft-Kundenvereinbarung

Ein Abrechnungskonto wird erstellt, wenn Sie sich für die Nutzung von Azure registrieren. Sie verwenden Ihr Abrechnungskonto zum Verwalten Ihrer Rechnungen und Zahlungen sowie zum Nachverfolgen von Kosten. Sie können über Zugriff auf mehrere Abrechnungskonten verfügen. Es kann beispielsweise sein, dass Sie sich für Ihre persönlichen Projekte für Azure registriert haben. Und außerdem könnten Sie auch über das Enterprise Agreement Ihrer Organisation oder über eine Microsoft-Kundenvereinbarung auf Azure zugreifen. In diesen Szenarien verfügen Sie dann jeweils über ein separates Abrechnungskonto.

Dieser Artikel bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben.](#check-access-to-a-microsoft-customer-agreement)

## <a name="your-billing-account"></a>Ihr Abrechnungskonto

Ihr Abrechnungskonto für die Microsoft-Kundenvereinbarung enthält ein oder mehrere Abrechnungsprofile, mit denen Sie Ihre Rechnungen und Zahlungsmethoden verwalten können. Jedes Abrechnungsprofil enthält einen oder mehrere Rechnungsabschnitte, mit denen Sie die Kosten auf der Rechnung des Abrechnungsprofils organisieren können.

Das folgende Diagramm zeigt die Beziehung zwischen einem Abrechnungskonto, Abrechnungsprofilen und Rechnungsabschnitten.

![Diagramm mit der Abrechnungshierarchie für eine Microsoft-Kundenvereinbarung](./media/mca-overview/mca-billing-hierarchy.png)

Die Rollen für das Abrechnungskonto verfügen über die höchste Berechtigungsstufe. Standardmäßig erhält nur der Benutzer, der sich für Azure registriert hat, Zugriff auf das Abrechnungskonto. Diese Rollen sollten Benutzern zugewiesen werden, die Rechnungen anzeigen und Kosten für Ihre gesamte Organisation nachverfolgen müssen. Bei diesen Benutzern handelt es sich beispielsweise um Leiter der Finanzabteilung oder IT-Manager. Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungskonto](../manage/understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Abrechnungsprofile

Mit einem Abrechnungsprofil können Sie Ihre Rechnung(en) und Zahlungsmethoden verwalten. Am Anfang jedes Kalendermonats wird in Ihrem Konto eine monatliche Rechnung für jedes Abrechnungsprofil erstellt. Die Rechnung enthält die jeweiligen Gebühren für alle Azure-Abonnements und andere Käufe aus dem Vormonat.

Für Ihr Abrechnungskonto wird automatisch ein Abrechnungsprofil erstellt. Es enthält standardmäßig einen Rechnungsabschnitt. Sie können bei Bedarf weitere Rechnungsbereiche erstellen, damit Sie Ihre Kosten einfacher nachverfolgen und organisieren können, z.B. nach Projekt, Abteilung oder Entwicklungsumgebung. Diese Bereiche werden auf der Rechnung im Abrechnungsprofil angezeigt. Sie zeigen den Verbrauch der einzelnen Abonnements sowie die zugehörigen Käufe.

Die Rollen für die Abrechnungsprofile verfügen über Berechtigungen zum Anzeigen und Verwalten von Rechnungen und Zahlungsmethoden. Weisen Sie diese Rollen Benutzern zu, die Rechnungen bezahlen. Bei diesen Benutzern handelt es sich beispielsweise um Mitglieder des Buchhaltungsteams in Ihrer Organisation. Weitere Informationen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Jedes Abrechnungsprofil erhält eine monatliche Rechnung

Am Anfang jedes Monats wird eine monatliche Rechnung für jedes Abrechnungsprofil erstellt. Die Rechnung enthält alle Gebühren des vorherigen Monats.

Im Azure-Portal können Sie die Rechnung anzeigen, Dokumente herunterladen und die Einstellung ändern, um zukünftige Rechnungen per E-Mail zu erhalten. Weitere Informationen finden Sie unter [Herunterladen von Rechnungen für eine Microsoft-Kundenvereinbarung](../manage/download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Zahlungsmethoden für Rechnungen

Jedes Abrechnungsprofil verfügt über eigene Zahlungsmethoden, die zum Bezahlen der Rechnungen verwendet werden. Die folgenden Zahlungsmethoden werden unterstützt:

| type             | Definition  |
|------------------|-------------|
|Azure-Gutschriften    |  Guthaben werden automatisch auf die in Frage kommenden Gebühren auf Ihrer Rechnung angerechnet, wodurch sich der von Ihnen zu zahlende Betrag verringert. Weitere Informationen finden Sie unter [Nachverfolgen des Azure-Guthabens für das Abrechnungsprofil](../manage/mca-check-azure-credits-balance.md). |
|Scheck/Überweisung | Wenn Ihr Konto für die Zahlung per Scheck/Überweisung genehmigt wurde. Sie können den fälligen Rechnungsbetrag per Scheck oder Überweisung bezahlen. Die Anweisungen für die Zahlung sind auf der Rechnung angegeben. |
|Kreditkarte | Kunden, die sich über die Azure-Website für Azure registrieren, können per Kreditkarte zahlen. |

### <a name="apply-policies-to-control-purchases"></a>Anwenden von Richtlinien zum Steuern von Käufen

Wenden Sie Richtlinien an, um Azure Marketplace- und -Reservierungskäufe mithilfe eines Abrechnungsprofils zu steuern. Sie können Richtlinien festlegen, um den Erwerb von Azure-Reservierungen und Marketplace-Produkten zu deaktivieren. Wenn die Richtlinien angewendet werden, können Abonnements, die dem Abrechnungsprofil in Rechnung gestellt werden, nicht für diese Käufe verwendet werden.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure-Pläne legen Preise und Vereinbarungen zum Servicelevel für Abonnements fest

Azure-Pläne legen die Preise und Vereinbarungen zum Servicelevel für Azure-Abonnements fest. Sie werden beim Erstellen eines Abrechnungsprofils automatisch aktiviert. Diese Pläne können in allen Rechnungsabschnitten verwendet werden, die dem Abrechnungsprofil zugeordnet sind. Benutzer mit Zugriff auf den Rechnungsabschnitt können die Pläne verwenden, um Azure-Abonnements zu erstellen. Die folgenden Azure-Pläne werden in Abrechnungskonten für Microsoft-Kundenvereinbarungen unterstützt:

| Planen             | Definition  |
|------------------|-------------|
|Microsoft Azure-Plan   | Dieser Plan ermöglicht Benutzern das Erstellen von Abonnements, die beliebige Workloads ausführen können.  |
|Microsoft Azure-Plan für Dev/Test | Dieser Plan ermöglicht Visual Studio-Abonnenten das Erstellen von Abonnements, die auf Entwicklungs- oder Testworkloads beschränkt sind. Für diese Abonnements ergeben sich Vorteile wie niedrigere Tarife und der Zugriff auf exklusive Images virtueller Computer im Azure-Portal. |

## <a name="invoice-sections"></a>Rechnungsabschnitte

Erstellen Sie Rechnungsabschnitte, um die Kosten auf Ihrer Rechnung zu organisieren. Möglicherweise benötigen Sie z. B. eine einzelne Rechnung für Ihre Organisation, möchten jedoch die Kosten nach Abteilung, Team oder Projekt organisieren. In diesem Szenario verwenden Sie ein einzelnes Abrechnungsprofil, in dem Sie einen Rechnungsabschnitt für jede Abteilung, jedes Team oder Projekt erstellen.

Beim Erstellen eines Rechnungsabschnitts können Sie anderen Benutzern die Berechtigung zum Erstellen von Azure-Abonnements zuweisen, die dem Abschnitt in Rechnung gestellt werden. Alle Nutzungsgebühren und Einkäufe für die Abonnements werden dann dem Abschnitt in Rechnung gestellt.

Die Rollen für den Rechnungsabschnitt verfügen über Berechtigungen zum Steuern, wer Azure-Abonnements erstellen darf. Weisen Sie diese Rollen Benutzern zu, die Ihre Azure-Umgebung für Teams in der Organisation einrichten. Bei diesen Benutzern handelt es sich beispielsweise um technische Leiter und technische Architekten. Weitere Informationen finden Sie unter [Rollen und Aufgaben für einen Rechnungsabschnitt](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um Informationen zu einem Abrechnungskonto zu erhalten:

- [Grundlegendes zu Verwaltungsrollen für Microsoft-Kundenvereinbarungen in Azure](../manage/understand-mca-roles.md)
- [Erstellen eines zusätzlichen Azure-Abonnements für eine Microsoft-Kundenvereinbarung](../manage/create-subscription.md)
- [Erstellen von Abschnitten in Ihrer Rechnung zum Organisieren von Kosten](../manage/mca-section-invoice.md)
