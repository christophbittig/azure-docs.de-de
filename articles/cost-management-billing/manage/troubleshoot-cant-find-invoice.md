---
title: 'Problembehandlung: Rechnungen werden nicht im Azure-Portal angezeigt'
description: Beheben eines Problems beim Anzeigen Ihrer Rechnung im Azure-Portal
services: cost-management-billing
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: banders
ms.openlocfilehash: 6f90d077b9cb7e1836170084c683e47cdafc480e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464678"
---
# <a name="troubleshoot-issues-while-trying-to-view-invoice-in-the-azure-portal"></a>Beheben von Problemen beim Anzeigen von Rechnungen im Azure-Portal

Beim Anzeigen Ihrer Rechnung im Azure-Portal können mitunter Probleme auftreten. In dieser kurzen Anleitung werden einige häufige Probleme erläutert.
 
## <a name="common-issues-and-solutions"></a>Bekannte Probleme und Lösungen

#### <a name="you-see-the-message-we-cant-display-the-invoices-for-your-subscription-this-typically-happens-when-you-sign-in-with-an-email-which-doesnt-have-access-to-view-invoices-check-youve-signed-in-with-the-correct-email-address-if-you-are-still-seeing-the-error-see-why-you-might-not-see-an-invoice"></a><a name="subnotfound"></a> Die folgende Meldung wird angezeigt: Die Rechnungen für Ihr Abonnement können nicht angezeigt werden. Dies ist normalerweise dann der Fall, wenn Sie sich mit einer E-Mail-Adresse anmelden, die keinen Zugriff auf die Anzeige von Rechnungen besitzt. Überprüfen Sie, ob Sie sich mit der richtigen E-Mail-Adresse angemeldet haben. Wenn der Fehler weiterhin auftritt, finden Sie weitere Informationen unter „Mögliche Gründe für nicht angezeigte Rechnungen“.

Dieser Fehler tritt auf, wenn die Identität, mit der Sie sich angemeldet haben, keinen Zugriff auf das Abonnement hat.

Versuchen Sie, das Problem mit einer der folgenden Optionen zu beheben: 

**Vergewissern Sie sich, dass Sie mit der richtigen E-Mail-Adresse angemeldet sind:**

Die Rechnungen für ein Abonnement können nur mit der E-Mail-Adresse angezeigt werden, die über die Rolle „Kontoadministrator“ für das Abonnement verfügt. Vergewissern Sie sich, dass Sie mit der richtigen E-Mail-Adresse angemeldet sind. Die E-Mail-Adresse steht in der E-Mail, die Sie erhalten haben, als Ihre Rechnung generiert wurde.  

  ![Screenshot mit der E-Mail-Adresse für Rechnungen](./media/troubleshoot-cant-find-invoice/invoice-email.png)

**Vergewissern Sie sich, dass Sie mit dem richtigen Konto angemeldet sind:**

Einige Kunden verfügen über zwei Konten mit derselben E-Mail-Adresse – ein Geschäfts-, Schul- oder Unikonto und ein persönliches Konto. Normalerweise verfügt nur eines der Konten über die Berechtigung zum Anzeigen von Rechnungen. Möglicherweise haben Sie zwei Konten mit Ihrer E-Mail-Adresse. Wenn Sie sich mit dem Konto anmelden, das nicht über die Berechtigung verfügt, wird die Rechnung nicht angezeigt. Führen Sie die folgenden Schritte aus, um festzustellen, ob Sie über mehrere Konten verfügen, und ein anderes Konto zu verwenden:

1. Melden Sie sich über ein InPrivate/Inkognito-Fenster beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie über mehrere Konten mit derselben E-Mail-Adresse verfügen, werden Sie aufgefordert, ein **Geschäfts-, Schul- oder Unikonto** oder ein **persönliches Konto** auszuwählen. Wählen Sie eines der Konten aus, und befolgen Sie dann diese [Anweisungen zum Anzeigen Ihrer Rechnung](../understand/download-azure-invoice.md#download-your-mosp-azure-subscription-invoice).  

    ![Screenshot zum Auswählen des Kontos](./media/troubleshoot-cant-find-invoice/two-accounts.png)

1. Versuchen Sie es mit einem anderen Konto, falls die Rechnung weiterhin nicht im Azure-Portal angezeigt wird.

**Vergewissern Sie sich, dass Sie beim richtigen Azure Active Directory-Mandanten (AAD) angemeldet sind:**

Ihr Abrechnungskonto und Ihr Abonnement sind einem AAD-Mandanten zugeordnet. Wenn Sie bei einem falschen Mandanten angemeldet sind, wird die Rechnung für Ihr Abonnement nicht angezeigt. Führen Sie die folgenden Schritte im Azure-Portal aus, um den Mandanten zu wechseln:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie oben rechts auf der Seite Ihre E-Mail-Adresse aus.
1. Klicken Sie dann auf Verzeichnis wechseln.  

    ![Screenshot zum Auswählen von „Verzeichnis wechseln“](./media/troubleshoot-cant-find-invoice/select-switch-tenant.png)

1. Wählen Sie im Abschnitt „Alle Verzeichnisse“ einen Mandanten aus. Wenn der Abschnitt „Alle Verzeichnisse“ nicht angezeigt wird, haben Sie keinen Zugriff auf mehrere Mandanten.  

    ![Screenshot zum Auswählen eines anderen Verzeichnisses](./media/troubleshoot-cant-find-invoice/select-another-tenant.png)

#### <a name="you-couldnt-find-the-invoice-that-you-see-on-your-credit-card-statement"></a><a name="cantsearchinvoice"></a>Sie konnten die in Ihrer Kreditkartenabrechnung ausgewiesene Rechnung nicht finden.

In Ihrer Kreditkartenabrechnung ist eine Abbuchung **Microsoft Gxxxxxxxxx** aufgeführt. Sie finden alle anderen Rechnungen im Portal, aber nicht Gxxxxxxxxx. Dieses Problem tritt auf, wenn die Rechnung zu einem anderen Abonnement oder Abrechnungsprofil gehört. Führen Sie die folgenden Schritte aus, um die Rechnung anzuzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie über die Suchleiste im Azure-Portal nach der Rechnungsnummer.
1. Wählen Sie **View your invoice** (Rechnung anzeigen) aus.  

    ![Screenshot zur Suche nach einer Rechnung](./media/troubleshoot-cant-find-invoice/search-invoice.png)

## <a name="contact-us-for-help"></a>Setzen Sie sich mit uns in Verbindung, um Hilfe zu erhalten.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen und Herunterladen Ihrer Azure-Rechnung](../understand/download-azure-invoice.md)
- [Anzeigen und Herunterladen der Azure-Nutzung und -Gebühren](../understand/download-azure-daily-usage.md)
- [Anmeldefehler „Keine Abonnements gefunden“ für das Azure-Portal](no-subscriptions-found.md)