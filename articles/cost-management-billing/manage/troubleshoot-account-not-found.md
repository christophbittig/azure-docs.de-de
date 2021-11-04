---
title: Problembehandlung für das Anzeigen Ihres Abrechnungskontos im Azure-Portal
description: Dieser Artikel enthält hilfreiche Informationen zur Behebung von Problemen, die beim Anzeigen Ihres Abrechnungskontos im Azure-Portal auftreten.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: 64d577f72765cfe87ada9d7c30c2a1b69b373c73
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433931"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Problembehandlung für das Anzeigen Ihres Abrechnungskontos im Azure-Portal

Ein Abrechnungskonto wird erstellt, wenn Sie sich für die Nutzung von Azure registrieren. Sie verwenden Ihr Abrechnungskonto zum Verwalten Ihrer Rechnungen und Zahlungen sowie zum Nachverfolgen von Kosten. Unter Umständen verfügen Sie über Zugriff auf mehrere Abrechnungskonten. Es kann beispielsweise sein, dass Sie sich zu persönlichen Zwecken für Azure registriert haben. Und außerdem könnten Sie auch über das Enterprise Agreement Ihrer Organisation oder über eine Microsoft-Kundenvereinbarung auf Azure zugreifen. In diesen Szenarien verfügen Sie dann jeweils über ein separates Abrechnungskonto. Dieser Artikel enthält hilfreiche Informationen zur Behebung von Problemen, die beim Anzeigen Ihres Abrechnungskontos im Azure-Portal auftreten.

Sie können Ihre Abrechnungskonten auf der Seite [Cost Management + Billing](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) anzeigen.

Weitere Informationen zu Abrechnungskonten und zur Ermittlung der Art Ihres Abrechnungskontos finden Sie unter [Anzeigen Ihrer Abrechnungskonten im Azure-Portal](view-all-accounts.md).

Falls Sie Ihr Abrechnungskonto im Azure-Portal nicht anzeigen können, sollten Sie es mit den folgenden Optionen versuchen:

## <a name="sign-in-to-a-different-tenant"></a>Anmelden bei einem anderen Mandanten

Ihr Abrechnungskonto ist einem einzelnen Azure Active Directory-Mandanten zugeordnet. Das Abrechnungskonto wird auf der Seite „Kostenverwaltung + Abrechnung“ nicht angezeigt, wenn Sie sich nicht beim richtigen Mandanten angemeldet haben. Führen Sie die folgenden Schritte aus, um im Azure-Portal zu einem anderen Mandanten zu wechseln und Ihre Abrechnungskonten unter diesem Mandanten anzuzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie oben rechts auf der Seite Ihr Profil (E-Mail-Adresse) aus.
1. Wählen Sie **Verzeichnis wechseln** aus.  
    ![Screenshot: Auswahl von „Verzeichnis wechseln“ im Portal](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. Wählen Sie im Abschnitt **Alle Verzeichnisse** ein Verzeichnis aus.  
    ![Screenshot: Auswahl eines Verzeichnisses im Portal](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Anmelden mit einer anderen E-Mail-Adresse

Einige Benutzer verfügen über mehrere E-Mail-Adressen, mit denen sie sich beim [Azure-Portal](https://portal.azure.com) anmelden können. Nicht alle E-Mail-Adressen haben Zugriff auf ein Abrechnungskonto. Wenn Sie sich mit einer E-Mail-Adresse anmelden, die über Berechtigungen zum Verwalten von Ressourcen verfügt, aber nicht zum Anzeigen eines Abrechnungskontos, wird das Abrechnungskonto im Azure-Portal auf der Seite [Kostenverwaltung + Abrechnung](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) nicht angezeigt.

Melden Sie sich beim Azure-Portal mit einer E-Mail-Adresse an, die über die Berechtigung zum Zugreifen auf das Abrechnungskonto verfügt.

## <a name="sign-in-with-a-different-identity"></a>Anmelden mit einer anderen Identität

Einige Benutzer verfügen über zwei Identitäten mit derselben E-Mail-Adresse: ein Geschäfts-, Schul-oder Unikonto und ein persönliches Konto. Normalerweise verfügt nur eine der Identitäten über Berechtigungen zum Anzeigen eines Abrechnungskontos. Es kann auch sein, dass Sie zwei Identitäten mit nur einer E-Mail-Adresse nutzen. Wenn Sie sich mit einer Identität anmelden, die nicht über die Berechtigung zum Anzeigen eines Abrechnungskontos verfügt, wird das Abrechnungskonto auf der Seite [Kostenverwaltung + Abrechnung](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) nicht angezeigt. Verwenden Sie die folgenden Schritte, um Ihre Identität zu wechseln:

1. Melden Sie sich über ein InPrivate/Inkognito-Fenster beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Ihre E-Mail-Adresse für zwei Identitäten gilt, wird eine Option zum Auswählen eines persönlichen Kontos oder eines Geschäfts-, Schul- oder Unikontos angezeigt. Wählen Sie eines der Konten aus.
1. Wiederholen Sie die Schritte 1 und 2, und wählen Sie die andere Identität aus, falls das Abrechnungskonto im Azure-Portal auf der Seite „Kostenverwaltung + Abrechnung“ nicht angezeigt wird.

## <a name="contact-us-for-help"></a>Setzen Sie sich mit uns in Verbindung, um Hilfe zu erhalten.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel zur Abrechnung und zu Abonnements als Hilfe bei der Problembehandlung.

- [Karte abgelehnt](./troubleshoot-declined-card.md)
- [Beheben von Problemen bei der Anmeldung für ein Azure-Abonnement](./troubleshoot-sign-in-issue.md)
- [Keine Abonnements gefunden](./no-subscriptions-found.md)
- [Enterprise-Kostenansicht deaktiviert](./enterprise-mgmt-grp-troubleshoot-cost-view.md)