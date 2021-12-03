---
title: Erstellen eines zusätzlichen Azure-Abonnements
description: Erfahren Sie, wie Sie ein neues Azure-Abonnement im Azure-Portal erstellen. Lesen Sie die Informationen zu Formularen für Abrechnungskonten, und zeigen Sie zusätzliche verfügbare Ressourcen an.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: banders
ms.openlocfilehash: 4632cd70192387da98f62a3ee96fcf0a3650aad1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284377"
---
# <a name="create-an-additional-azure-subscription"></a>Erstellen eines zusätzlichen Azure-Abonnements

Sie können ein zusätzliches Abonnement für Ihr Abrechnungskonto unter dem [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), der [Microsoft-Kundenvereinbarung](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) oder der [Microsoft Partnervereinbarung](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) im Azure-Portal erstellen. Mit einem zusätzlichen Abonnement können Sie beispielsweise Abonnementgrenzwerte vermeiden, separate Umgebungen für die Sicherheit schaffen oder Daten aus Compliancegründen isolieren.

Wenn Sie über ein Abrechnungskonto des Microsoft Online Service-Programms (MOSP) verfügen, können Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) zusätzliche Abonnements erstellen.

Weitere Informationen zu Abrechnungskonten und zum Identifizieren des Typs Ihres Abrechnungskontos finden Sie unter [Anzeigen Ihrer Abrechnungskonten im Azure-Portal](view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Erforderliche Berechtigungen zum Erstellen von Azure-Abonnements

Zum Erstellen von Abonnements benötigen Sie die folgenden Berechtigungen:

|Abrechnungskonto  |Berechtigung  |
|---------|---------|
|Enterprise Agreement (EA) |  Rolle „Kontobesitzer“ für die Enterprise Agreement-Registrierung. Weitere Informationen finden Sie unter [Informationen zu Azure Enterprise Agreement-Administratorrollen in Azure](understand-ea-roles.md).    |
|Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) |  Rolle „Besitzer“ oder „Mitwirkender“ im Rechnungsabschnitt, Abrechnungsprofil oder Abrechnungskonto. Oder die Rolle „Azure-Abonnementersteller“ im Rechnungsabschnitt.  Weitere Informationen finden Sie unter [Rollen und Aufgaben für die Abonnementabrechnung](understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Microsoft-Partnervereinbarung (MPA) |   Rolle „Globaler Administrator“ und „Administrator-Agent“ in der CSP-Partnerorganisation. Weitere Informationen finden Sie unter [Zuweisen von Rollen und Berechtigungen zu Benutzern](/partner-center/permissions-overview).  Der Benutzer muss sich beim Partnermandanten anmelden, um Azure-Abonnements zu erstellen.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Erstellen eines Abonnements im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Abonnements**.

   ![Screenshot der Suche im Portal nach Abonnements](./media/create-subscription/billing-search-subscription-portal.png)

1. Wählen Sie **Hinzufügen**.

   ![Screenshot der Schaltfläche „Hinzufügen“ in der Ansicht „Abonnements“](./media/create-subscription/subscription-add.png)

1. Wenn Sie über Zugriff auf mehrere Abrechnungskonten verfügen, wählen Sie das Abrechnungskonto aus, für das Sie das Abonnement erstellen möchten.

1. Füllen Sie das Formular aus, und klicken Sie auf **Erstellen**. In den folgenden Tabellen sind die Felder im Formular für die einzelnen Abrechnungskontotypen aufgelistet.

**Enterprise Agreement**

|Feld  |Definition  |
|---------|---------|
|Name     | Anhand des Anzeigenamens können Sie das Abonnement im Azure-Portal einfach identifizieren.  |
|Angebot     | Wählen Sie „EA Dev/Test“ aus, wenn Sie dieses Abonnement für Entwicklungs- oder Testworkloads verwenden möchten. Für alle anderen Workloads verwenden Sie „Microsoft Azure Enterprise“. Das DevTest-Angebot muss für Ihr Registrierungskonto aktiviert sein, damit Sie EA Dev/Test-Abonnements erstellen können.|

**Microsoft-Kundenvereinbarung**

|Feld  |Definition  |
|---------|---------|
|Abrechnungsprofil     | Die Gebühren für Ihr Abonnement werden dem ausgewählten Abrechnungsprofil in Rechnung gestellt. Wenn Sie nur auf ein Abrechnungsprofil zugreifen können, wird die Auswahl abgeblendet dargestellt.     |
|Rechnungsabschnitt     | Die Gebühren für Ihr Abonnement werden in diesem Abschnitt der Rechnung des Abrechnungsprofils angezeigt. Wenn Sie nur auf einen Rechnungsabschnitt zugreifen können, wird die Auswahl abgeblendet dargestellt.  |
|Planen     | Wählen Sie „Microsoft Azure-Plan für Dev/Test“ aus, wenn Sie dieses Abonnement für Entwicklungs- oder Testworkloads verwenden möchten. Für alle anderen Workloads verwenden Sie „Microsoft Azure-Plan“. Wenn nur ein Plan für das Abrechnungsprofil aktiviert ist, wird die Auswahl abgeblendet dargestellt.  |
|Name     | Anhand des Anzeigenamens können Sie das Abonnement im Azure-Portal einfach identifizieren.  |

**Microsoft-Partnervereinbarung**

|Feld  |Definition  |
|---------|---------|
|Kunde    | Das Abonnement wird für den von Ihnen ausgewählten Kunden erstellt. Wenn Sie über nur einen Kunden verfügen, wird die Auswahl abgeblendet dargestellt.  |
|Reseller    | Der Handelspartner, der die Dienste für den Kunden bereitstellt. Dies ist ein optionales Feld, das nur für indirekte Anbieter im CSP-Modell mit zwei Ebenen anwendbar ist. |
|Name     | Anhand des Anzeigenamens können Sie das Abonnement im Azure-Portal einfach identifizieren.  |

## <a name="create-a-subscription-as-a-partner-for-a-customer"></a>Erstellen eines Abonnements als Partner für einen Kunden

Partner mit einer Microsoft Partner-Vereinbarung führen die folgenden Schritte aus, um ein neues Microsoft Azure Plan-Abonnement für ihre Kunden zu erstellen. Das Abonnement wird unter dem Abrechnungskonto und Abrechnungsprofil des Partners erstellt.

1.  Melden Sie sich mit Ihrem Partner-Center-Konto beim Azure Portal an.
Stellen Sie sicher, dass Sie sich in Ihrem Verzeichnis des Partner-Centers (Mandant) und nicht im Mandanten eines Kunden befinden.
1.  Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1.  Wählen Sie den Abrechnungsbereich für das Abrechnungskonto aus, in dem sich das Kundenkonto befindet.
1.  Wählen Sie im Menü auf der linken Seite unter **Abrechnung** die Option **Kunden** aus.
1.  Wählen Sie auf der Seite Kunden den Kunden aus.
1.  Wählen Sie im linken Menü unter **Produkte + Dienste** die Option **Azure-Abonnements** aus.
1.  Wählen Sie auf der Seite Azure-Abonnement die Option **+ Hinzufügen** aus, um ein Abonnement zu erstellen.
1.  Geben Sie die Angaben zum Abonnement ein und wählen Sie nach Abschluss des Vorgangs **Überprüfen + erstellen** aus.


## <a name="create-an-additional-azure-subscription-programmatically"></a>Programmgesteuertes Erstellen eines zusätzlichen Azure-Abonnements

Sie können zusätzliche Abonnements auch programmgesteuert erstellen. Weitere Informationen finden Sie unter

- [Programmgesteuertes Erstellen von EA-Abonnements mit der aktuellen API](programmatically-create-subscription-enterprise-agreement.md)
- [Programmgesteuertes Erstellen von MCA-Abonnements mit der aktuellen API](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Programmgesteuertes Erstellen von MPA-Abonnements mit der aktuellen API](Programmatically-create-subscription-microsoft-customer-agreement.md)

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](add-change-subscription-administrator.md)
- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Erstellen von Verwaltungsgruppen zum Organisieren und Verwalten von Ressourcen](../../governance/management-groups/create-management-group-portal.md)
- [Kündigen Ihres Abonnements für Azure](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).