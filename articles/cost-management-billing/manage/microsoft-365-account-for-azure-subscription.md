---
title: Registrieren für Azure mit einem Microsoft 365-Konto
description: Hier erfahren Sie, wie Sie mit einem Microsoft 365-Konto ein Azure-Abonnement erstellen. Sie können auch vorhandene Azure- und Microsoft 365 Konten einander zuordnen.
author: bandersmsft
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 4e70cdf3c99de8bf45b73eb741bcd28813e99d02
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438776"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>Registrieren für ein Azure-Abonnement mit Ihrem Microsoft 365-Konto

Wenn Sie über ein Microsoft 365-Abonnement verfügen, können Sie mit Ihrem Microsoft 365-Konto ein Azure-Abonnement erstellen. Melden Sie sich mit Ihrem Microsoft 365-Benutzernamen und -Kennwort beim [Azure-Portal](https://portal.azure.com/) an. Wenn Sie virtuelle Computer einrichten oder andere Azure-Dienste verwenden möchten, müssen Sie sich für ein Azure-Abonnement registrieren. Sie können Ihr Azure-Abonnement gemeinsam mit anderen nutzen und die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) verwenden, um den Zugriff auf Ihr Azure-Abonnement und die entsprechenden Ressourcen zu verwalten](../../role-based-access-control/role-assignments-portal.md).

Wenn Sie bereits über ein Microsoft 365-Konto und ein Azure-Abonnement verfügen, finden Sie weitere Informationen unter [Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>Abschließen eines Azure-Abonnements mit Ihrem Microsoft 365-Konto

Sparen Sie Zeit, und vermeiden Sie die Anhäufung von Konten, indem Sie sich mit Ihrem Microsoft 365-Benutzernamen und-Kennwort für Azure registrieren.

1. Registrieren Sie sich unter [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Melden Sie sich mit Ihrem Microsoft 365-Benutzernamen und -Kennwort an. Das Konto, das Sie verwenden, muss nicht über Administratorberechtigungen verfügen. Wenn Sie mehrere Microsoft 365-Konten haben, stellen Sie sicher, dass Sie die Anmeldeinformationen für das Microsoft 365-Konto verwenden, das Ihrem Azure-Abonnement zugeordnet werden soll.

   ![Screenshot der Anmeldeseite.](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Geben Sie die erforderlichen Informationen ein, und schließen Sie den Registrierungsvorgang ab. Einige Informationen sind möglicherweise nicht erforderlich, wenn Sie bereits über ein Microsoft 365-Konto verfügen.

    ![Screenshot des Registrierungsformulars.](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Wenn Sie dem Azure-Abonnement weitere Benutzer aus Ihrer Organisation hinzufügen möchten, finden Sie weitere Informationen unter [Erste Schritte mit der Zugriffsverwaltung im Azure-Portal](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Weitere Informationen zu Azure- und Microsoft 365-Abonnements</a>

Microsoft 365 und Azure verwenden den Azure AD-Dienst zum Verwalten von Benutzern und Abonnements. Das Azure-Verzeichnis funktioniert wie ein Container, in dem Sie Benutzer und Abonnements gruppieren können. Damit Sie für Ihre Azure- und Microsoft 365-Abonnements die gleichen Benutzerkonten verwenden können, müssen Sie sicherstellen, dass die Azure-Abonnements im gleichen Verzeichnis wie die Microsoft 365-Abonnements erstellt werden. Berücksichtigen Sie dabei Folgendes:

* Ein Abonnement wird in einem Verzeichnis erstellt.
* Benutzer gehören Verzeichnissen an.
* Ein Abonnement wird in dem Verzeichnis des Benutzers erstellt, der das Abonnement erstellt. Das heißt, Ihr Microsoft 365-Abonnement ist mit dem gleichen Konto verknüpft wie Ihr Azure-Abonnement.
* Azure-Abonnements gehören einzelnen Benutzern im Verzeichnis.
* Microsoft 365-Abonnements gehören dem Verzeichnis selbst. Benutzer mit den richtigen Berechtigungen im Verzeichnis können diese Abonnements verwalten.

![Screenshot der Beziehung zwischen dem Verzeichnis, Benutzern und Abonnements.](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

Weitere Informationen finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- Nutzen Sie Ihr Azure-Abonnement gemeinsam mit anderen, und [verwenden Sie die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC), um den Zugriff auf Ihr Azure-Abonnement und die entsprechenden Ressourcen zu verwalten](../../role-based-access-control/role-assignments-portal.md).