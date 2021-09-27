---
title: Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Partnervereinbarung – Azure CSP
description: Grundlegendes zum Abrechnungskonto für eine Microsoft-Partnervereinbarung (CSP)
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 71a3ce7a33f920c361dfe1e7d0004b52e8211336
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559610"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Partnervereinbarung

Ein Abrechnungskonto wird erstellt, wenn Sie sich für die Nutzung von Azure registrieren. Sie verwenden Ihr Abrechnungskonto zum Verwalten Ihrer Rechnungen und Zahlungen sowie zum Nachverfolgen von Kosten. Sie können über Zugriff auf mehrere Abrechnungskonten verfügen. Es kann beispielsweise sein, dass Sie sich für Ihre persönlichen Projekte für Azure registriert haben. Der Zugriff auf Azure kann auch im Rahmen eines Konzernvertrags, einer Microsoft-Kundenvereinbarung oder einer Microsoft-Partnervereinbarung Ihrer Organisation erfolgen. In diesen Szenarien verfügen Sie dann jeweils über ein separates Abrechnungskonto.

Dieser Artikel gilt für Abrechnungskonten für Microsoft-Partnervereinbarungen. Diese Konten werden für Cloudlösungsanbieter (Cloud Solution Providers, CSPs) erstellt, um die Abrechnung für ihre Kunden in der neuen Handelsumgebung zu verwalten. Die neue Umgebung ist nur für Partner verfügbar, die über mindestens einen Kunden verfügen, der eine Microsoft-Kundenvereinbarung akzeptiert hat und einen Azure-Plan besitzt. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Partnervereinbarung haben.](#check-access-to-a-microsoft-partner-agreement) Ein [Azure-Plan](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ermöglicht Kunden den Zugriff auf Azure-Dienste zu den Preisen der nutzungsbasierten Bezahlung im Rahmen der Microsoft-Kundenvereinbarung.

## <a name="your-billing-account"></a>Ihr Abrechnungskonto

Ihr Abrechnungskonto für die Microsoft-Partnervereinbarung enthält ein Abrechnungsprofil für jede Währung, die Sie bei Ihrer Geschäftstätigkeit verwenden. Mit dem Abrechnungsprofil können Sie Ihre Rechnungen für die jeweilige Währung verwalten. Wenn Sie Beziehungen mit Kunden aufbauen, werden Azure-Abonnements und andere Käufe in Abhängigkeit von der jeweiligen Währung über das entsprechende Abrechnungsprofil abgerechnet.

Das folgende Diagramm zeigt die Beziehung zwischen einem Abrechnungskonto, Abrechnungsprofilen, Kunden und Handelspartnern:

![Diagramm: Abrechnungshierarchie für eine Microsoft-Partnervereinbarung](./media/mpa-overview/mpa-hierarchy.svg)

Benutzer mit der Rolle **Globaler Administrator** oder **Administrator-Agent** in Ihrer Organisation können Abrechnungskonten, Abrechnungsprofile und Kunden verwalten. Weitere Informationen finden Sie unter [Zuweisen von Rollen und Berechtigungen zu Benutzern](/partner-center/permissions-overview).

## <a name="billing-profiles"></a>Abrechnungsprofile

Mit einem Abrechnungsprofil können Sie Ihre Rechnungen für eine Währung verwalten. Am Anfang jedes Kalendermonats wird in Ihrem Konto eine monatliche Rechnung für jedes Abrechnungsprofil erstellt. Die Rechnung enthält Gebühren für alle Azure-Abonnements und andere Käufe aus dem Vormonat in der Währung des Abrechnungsprofils.

Sie können die Rechnung im Azure-Portal anzeigen und die zugehörigen Dokumente wie Nutzungsdatei und Preisblatt herunterladen. Weitere Informationen finden Sie unter [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](download-azure-invoice.md).

> [!IMPORTANT]
>
> Die Rechnungen für Abrechnungsprofile enthalten Gebühren für Kunden mit Azure-Plänen sowie SaaS-, Azure Marketplace- und Reservierungskäufe für Kunden, die die Microsoft-Kundenvereinbarung nicht akzeptiert haben und über keine Azure-Pläne verfügen.

## <a name="customers"></a>Kunden

Kunden, die eine Microsoft-Kundenvereinbarung akzeptiert haben und über einen Azure-Plan verfügen, können im Azure-Portal angezeigt und verwaltet werden. Sie können Gebühren und Transaktionen anzeigen und Azure-Abonnements für diese Kunden erstellen und verwalten.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Aktivieren einer Richtlinie für Kostentransparenz

Wenden Sie eine Richtlinie an, um zu steuern, ob Benutzer in Kundenorganisationen Kosten für ihre Azure-Nutzung zu Sätzen für die nutzungsbasierte Bezahlung anzeigen und analysieren können. Standardmäßig ist die Richtlinie deaktiviert, und Benutzer können die Kosten nicht anzeigen. Nach Aktivierung der Richtlinie können Benutzer mit entsprechendem [Azure RBAC](../../role-based-access-control/overview.md)-Abonnementzugriff die Kosten für das Abonnement anzeigen und analysieren.

So aktivieren Sie die Richtlinie:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/mpa-overview/search-cmb.png)

1. Wählen Sie auf der linken Seite **Kunden** und anschließend einen Kunden aus der Liste aus.

   ![Screenshot: Auswählen des Kunden](./media/mpa-overview/mpa-customers.png)

1. Wählen Sie auf der linken Seite **Richtlinien** aus.

   ![Screenshot: Richtlinien](./media/mpa-overview/mpa-change-policy.png)

1. Wählen Sie **Ja** aus.

## <a name="resellers"></a>Handelspartner

Indirekte Anbieter im [CSP-Modell mit zwei Ebenen](/partner-center) können beim Erstellen von Kunden im Azure-Portal einen Handelspartner auswählen. Nach der Erstellung können sie die Abonnementliste anzeigen, nach einem Handelspartner filtern und die Kosten für einen Kunden nach Handelspartnern in der Azure-Kostenanalyse analysieren.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Partnervereinbarung
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um Informationen zu einem Abrechnungskonto zu erhalten:

- [Erstellen eines zusätzlichen Azure-Abonnements im Azure-Portal](../manage/create-subscription.md)
- Integrieren von Abrechnungsdaten in Ihr eigenes Berichterstellungssystem mithilfe der [Azure-Abrechnungs-APIs](/rest/api/billing/)
- [Erste Schritte mit Azure Cost Management für Partner](../costs/get-started-partners.md)