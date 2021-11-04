---
title: Verwalten des Zugriffs auf die Azure-Abrechnung
description: Hier erfahren Sie, wie Sie Mitgliedern Ihres Teams Zugriff auf Ihre Azure-Abrechnungsinformationen gewähren.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 06/27/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 5f325b0e8adb0a20389bfc09cc37f42fbddbd477
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450018"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Verwalten des Zugriffs auf Abrechnungsinformationen für Azure

Sie können anderen Personen im Azure-Portal Zugriff auf die Abrechnungsinformationen für Ihr Konto gewähren. Der Typ der Abrechnungsrollen und die Anleitung zum Gewähren des Zugriffs auf die Abrechnungsinformationen variieren je nach Typ Ihres Abrechnungskontos. Informationen zur Ermittlung des Typs Ihres Abrechnungskontos finden Sie unter [Überprüfen des Typs Ihres Abrechnungskontos](#check-the-type-of-your-billing-account).

Der Artikel gilt für Kunden mit Microsoft Online Services-Programmkonten. Wenn Sie Azure-Kunde mit einem Enterprise Agreement (EA) und Unternehmensadministrator sind, können Sie Abteilungsadministratoren und Kontobesitzern im Enterprise Portal Berechtigungen erteilen. Weitere Informationen finden Sie unter [Informationen zu Azure Enterprise Agreement-Administratorrollen in Azure](understand-ea-roles.md). Wenn Sie ein Kunde mit Microsoft-Kundenvereinbarung sind, helfen Ihnen die Informationen unter [Grundlegendes zu Verwaltungsrollen für Microsoft-Kundenvereinbarungen in Azure](understand-mca-roles.md) weiter.

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Kontoadministratoren für Microsoft Online Services-Programmkonten

Ein Kontoadministrator ist der einzige Besitzer eines Abrechnungskontos des Microsoft Online Services-Programms. Die Rolle wird einer Person zugewiesen, die sich für Azure registriert hat. Kontoadministratoren sind berechtigt, verschiedene Abrechnungsaufgaben wie das Erstellen von Abonnements, das Anzeigen von Rechnungen oder das Ändern der Abrechnung für ein Abonnement durchzuführen.

## <a name="give-others-access-to-view-billing-information"></a>Gewähren des Zugriffs für andere Personen zum Anzeigen von Abrechnungsinformationen

Der Kontoadministrator kann anderen Personen Zugriff auf Azure-Abrechnungsinformationen gewähren, indem unter einem Abonnement ihrer Konten eine der folgenden Rollen zugewiesen wird.

- Dienstadministrator
- Co-Admin
- Besitzer
- Mitwirkender
- Leser
- Abrechnungsleser

Diese Rollen haben Zugriff auf Abrechnungsinformationen im [Azure-Portal](https://portal.azure.com/). Benutzer, denen diese Rollen zugewiesen sind, können auch die [Abrechnungs-APIs](consumption-api-overview.md#usage-details-api) verwenden, um Rechnungen und Nutzungsdetails programmgesteuert abzurufen.

Informationen zum Zuweisen von Rollen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

> [!note]
> Wenn Sie EA-Kunde sind, kann ein Kontobesitzer die obige Rolle anderen Benutzern seines Teams zuweisen. Damit diese Benutzer Abrechnungsinformationen anzeigen können, muss der Unternehmensadministrator im Enterprise Portal aber die Option „AO-Ansichtsgebühren“ aktivieren.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> Berechtigen von Benutzern zum Herunterladen von Rechnungen

Nachdem ein Kontoadministrator anderen Benutzern die entsprechenden Rollen zugewiesen hat, müssen diese den Zugriff aktivieren, um Rechnungen im Azure-Portal herunterzuladen. Rechnungen vor Dezember 2016 stehen nur dem Kontoadministrator zur Verfügung.

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com/) an.

1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot mit hervorgehobener „Kostenverwaltung + Abrechnung“ im Abschnitt „Dienste“](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Wählen Sie links die Option **Abonnements**. Je nach Zugriff müssen Sie unter Umständen einen Abrechnungsbereich und dann **Abonnements** auswählen.

    ![Screenshot: Auswählen von „Abonnements“](./media/manage-billing-access/billing-select-subscriptions.png)

1. Wählen Sie **Rechnungen** und dann **Zugriff auf Rechnung**.

    ![Screenshot: Delegieren des Zugriffs auf Rechnungen](./media/manage-billing-access/aa-optin01.png)

1. Wählen Sie **An** und „Speichern“ aus.

    ![Der Screenshot zeigt das Ein/Aus-Schalten zum Delegieren des Zugriffs auf Rechnungen](./media/manage-billing-access/aa-optinallow01.png)

Der Kontoadministrator kann auch konfigurieren, dass ihm Rechnungen per E-Mail gesendet werden. Weitere Informationen finden Sie unter [Empfangen Ihrer Rechnung per E-Mail](download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Gewähren des Lesezugriffs auf Abrechnungen

Weisen Sie die Rolle „Abrechnungsleser“ einem Benutzer zu, der Lesezugriff auf die Abrechnungsinformationen des Abonnements erhalten soll, Azure-Dienste jedoch nicht verwalten oder erstellen darf. Diese Rolle eignet sich für die Benutzer in einer Organisation, die für die Finanz- und Kostenverwaltung für Azure-Abonnements zuständig sind.

Das Feature Abrechnungsleser befindet sich in der Vorschauversion und unterstützt noch keine nicht globalen Clouds.

- Weisen Sie die Rolle „Abrechnungsleser“ einem Benutzer im Abonnementbereich zu.  
     Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

> [!NOTE]
> Wenn Sie EA-Kunde sind, kann ein Kontobesitzer oder Abteilungsadministrator Teammitgliedern die Rolle „Abrechnungsleser“ zuweisen. Damit Abrechnungsleser jedoch auf Abrechnungsinformationen für die Abteilung oder das Konto zugreifen können, muss der Unternehmensadministrator im Enterprise Portal die Richtlinie **Kontobesitzer können Gebühren anzeigen** bzw. **Abteilungsadministratoren können Gebühren anzeigen** aktivieren.

## <a name="check-the-type-of-your-billing-account"></a>Überprüfen des Typs Ihres Abrechnungskontos
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- Benutzer in anderen Rollen, z.B. „Besitzer“ oder „Mitwirkender“, können nicht nur auf Abrechnungsinformationen, sondern auch auf Azure-Dienste zugreifen. Informationen zum Verwalten dieser Rollen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
- Weitere Informationen zu Rollen finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).