---
title: Kündigen Ihres Azure-Abonnements
description: Beschreibt, wie Sie Ihr Azure-Abonnement, etwa das Abonnement für die kostenlose Testversion, kündigen.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: banders
ms.openlocfilehash: 71070f56eaa8b9e1d0a4bef42d609b10ac28b7e5
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321628"
---
# <a name="cancel-your-azure-subscription"></a>Kündigen Ihres Azure-Abonnements

Sie können Ihr Azure-Abonnement im Azure-Portal kündigen, wenn Sie es nicht länger benötigen.

Die folgenden Schritte sind zwar nicht obligatorisch, aber Microsoft *empfiehlt* Ihnen, sie vor dem Kündigen Ihres Abonnements auszuführen:

* Sichern Sie Ihre Daten. Wenn Sie Ihre Daten beispielsweise im Azure-Speicher oder SQL speichern, laden Sie eine Kopie herunter. Wenn Sie einen virtuellen Computer haben, speichern Sie lokal ein Image davon.
* Beenden Sie Ihre Dienste. Wechseln Sie zur [Ressourcenseite im Verwaltungsportal](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), und **beenden** Sie alle ausgeführten virtuellen Computer, Anwendungen oder anderen Dienste.
* Ziehen Sie die Migration Ihrer Daten in Betracht. Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Löschen Sie alle Ressourcen und Ressourcengruppen.
* Wenn Sie über benutzerdefinierte Rollen verfügen, die in `AssignableScopes` auf das Abonnement verweisen, sollten Sie die benutzerdefinierten Rollen aktualisieren, um das Abonnement zu entfernen. Wenn Sie versuchen, eine benutzerdefinierte Rolle zu aktualisieren, nachdem Sie ein Abonnement gekündigt haben, wird möglicherweise ein Fehler ausgegeben. Weitere Informationen finden Sie unter [Probleme mit benutzerdefinierten Rollen](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) und [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md).

Wenn Sie einen kostenpflichtigen Azure-Supportplan kündigen, werden Ihnen die Gebühren für die verbleibende Abonnementlaufzeit in Rechnung gestellt. Weitere Informationen finden Sie unter [Azure-Supportpläne](https://azure.microsoft.com/support/plans/).

## <a name="who-can-cancel-a-subscription"></a>Wer kann ein Abonnement kündigen?

Die Tabelle unten beschreibt die Berechtigungen, die zum Kündigen eines Abonnements benötigt werden.

|Abonnementtyp     |Wer kann kündigen?  |
|---------|---------|
|Abonnements, die bei der Registrierung für Azure über die Azure-Website erstellt wurden. Beispiele hierfür sind die Registrierung für ein [Kostenloses Azure-Konto](https://azure.microsoft.com/offers/ms-azr-0044p/), ein [Angebot mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) oder als [Visual Studio-Abonnent](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Dienstadministrator und Abonnementbesitzer  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) und [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Dienstadministrator und Abonnementbesitzer       |
|[Azure-Plan](https://azure.microsoft.com/offers/ms-azr-0017g/) und [Azure-Plan für DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Besitzer des Abonnements      |

Ein Kontoadministrator ohne die Rolle „Dienstadministrator“ oder „Abonnementbesitzer“ kann ein Azure-Abonnement nicht kündigen. Ein Kontoadministrator kann sich jedoch selbst zum Dienstadministrator ernennen und dann ein Abonnement kündigen. Weitere Informationen finden Sie unter [Ändern des Dienstadministrators](../../role-based-access-control/classic-administrators.md#change-the-service-administrator).


## <a name="cancel-subscription-in-the-azure-portal"></a>Kündigen eines Abonnements im Azure-Portal

1. Wählen Sie auf der [Seite „Abonnements“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Ihr Abonnement aus.
1. Wählen Sie das Abonnement aus, das Sie kündigen möchten.
1. Wählen Sie **Übersicht** und dann **Abonnement kündigen** aus.
    ![Screenshot mit der Schaltfläche zum Kündigen](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Befolgen Sie die Anweisungen, und schließen Sie die Kündigung ab.

> [!NOTE]
> Partner können ein Abonnement aus folgenden Gründen aussetzen oder kündigen: Anforderung durch einen Kunden, bei Nichtbezahlung oder Betrug. Weitere Informationen finden Sie unter [Aussetzen oder Stornieren eines Abonnements](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="cancel-a-support-plan"></a>Kündigen eines Supportplans

Wenn Sie Ihren Supportplan über die Azure-Website, das Azure-Portal, erworben haben oder wenn Sie einen Supportplan im Rahmen einer Microsoft-Kundenvereinbarung haben, können Sie einen Supportplan kündigen. Wenn Sie Ihren Supportplan über einen Microsoft-Vertreter oder -Partner erworben haben, wenden Sie sich zur Unterstützung an ihn. 

1. Navigieren Sie im Azure-Portal zu **Cost Management + Billing*** (Kostenverwaltung + Abrechnung).
1. Wählen Sie unter **Abrechnung** die Option **Laufende Gebühren** aus.
1. Wählen Sie auf der rechten Seite für das Zeilenelement „Supportplan“ die Auslassungspunkte ( **...** ) und dann **Automatische Verlängerung deaktivieren** aus.

## <a name="what-happens-after-subscription-cancellation"></a>Was geschieht nach der Kündigung eines Abonnements?

Nach Ihrer Kündigung wird die Abrechnung sofort beendet. Bis die Kündigung im Portal angezeigt wird, kann es allerdings bis zu zehn Minuten dauern. Wenn Sie das Abonnement mitten in einem Abrechnungszeitraum kündigen, senden wir Ihnen die Schlussrechnung zum üblichen Rechnungsdatum nach Ende des Abrechnungszeitraums zu.

Nach der Kündigung werden Ihre Dienste deaktiviert. Dies bedeutet, dass die Zuordnung Ihrer virtuellen Computer aufgehoben wird, die vorübergehenden IP-Adressen freigegeben werden und der Speicher nur noch im Lesezugriff verfügbar ist.

Nach der Kündigung Ihres Abonnements warten wir noch 30 bis 90 Tage mit der endgültigen Löschung Ihrer Daten, falls Sie doch noch einmal darauf zugreifen müssen oder Ihre Meinung ändern sollten. Für die Aufbewahrung der Daten rechnen wir keine Gebühr ab. Weitere Informationen finden Sie in [Microsoft Trust Center – Wie wir Ihre Daten verwalten](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="delete-free-account-or-pay-as-you-go-subscription"></a>Löschen eines kostenlosen Kontos oder eines Abonnements nutzungsbasierter Bezahlung

Wenn Sie über ein kostenloses Konto oder ein Abonnement mit nutzungsbasierter Bezahlung verfügen, müssen Sie nicht 30 bis 90 Tage warten, bis das Abonnement automatisch gelöscht wird. Die Option **Löschen** ist 3 Tage nach dem Kündigen eines Abonnements verfügbar. Wenn Sie nach 3 Tagen keine Ressourcen unter dem stornierten Abonnement haben, können Sie das Abonnement löschen.

Führen Sie die folgenden Schritte aus, um ein Abonnement zu löschen:

1. Warten Sie 3 Tage, nachdem Sie das Abonnement gekündigt haben.
2. Stellen Sie sicher, dass das Abonnement keine Ressourcen enthält, bevor Sie versuchen, es zu löschen.
3. Navigieren Sie im Azure-Portal zu [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
4. Wählen Sie das Abonnement aus, das Sie löschen möchten.
5. Wählen Sie **Übersicht** und anschließend **Löschen** aus.
6. Geben Sie den Abonnementnamen in die Bestätigungsaufforderung ein, und wählen Sie dann **Abonnement löschen** aus.

## <a name="delete-other-subscriptions"></a>Löschen anderer Abonnements

Die einzigen Abonnementtypen, die Sie manuell löschen können, sind ein kostenloses Konto oder ein Abonnement mit nutzungsbasierter Bezahlung. Alle anderen Abonnementtypen werden ausschließlich über den Prozess zur [Abonnementkündigung](#cancel-subscription-in-the-azure-portal) gelöscht. Anders ausgedrückt: Sie können ein Abonnement nicht direkt löschen, es sei denn, es handelt sich um ein kostenloses Konto oder ein Abonnement mit nutzungsbasierter Bezahlung. Nachdem Sie ein Abonnement gekündigt haben, können Sie jedoch eine [Azure-Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458) erstellen, um die sofortige Löschung des Abonnements anzufordern.

## <a name="reactivate-a-subscription"></a>Reaktivieren eines Abonnements

Wenn Sie Ihr Abonnement mit nutzungsbasierter Bezahlung versehentlich gekündigt haben, können Sie es [im Azure-Portal reaktivieren](subscription-disabled.md).

Wenn es sich bei Ihrem Abonnement nicht um ein Abonnement mit nutzungsbasierter Bezahlung handelt, nehmen Sie bitte innerhalb von 90 Tagen nach der Kündigung Kontakt zum Support auf, um Ihr Abonnement zu reaktivieren.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Warum wird die Option „Abonnement kündigen“ im Azure-Portal nicht angezeigt? 

Möglicherweise verfügen Sie nicht über die erforderlichen Berechtigungen, um ein Abonnement zu kündigen. Unter [Wer kann ein Abonnement kündigen?](#who-can-cancel-a-subscription) erfahren Sie, wer die verschiedenen Arten von Abonnements kündigen kann.

## <a name="how-do-i-delete-my-azure-account"></a>Wie lösche ich mein Azure-Konto?

*Ich muss mein Konto einschließlich aller personenbezogenen Informationen entfernen. Ich habe bereits meine aktiven Abonnements (kostenlose Testversion) gekündigt. Ich verfüge über keine aktiven Abonnements und möchte mein Konto vollständig löschen.*

* Wenn Sie über ein im Unternehmen bereitgestelltes Azure Active Directory-Konto verfügen, kann das Konto vom Azure AD-Administrator gelöscht werden. Danach werden Ihre Dienste deaktiviert. Dies bedeutet, dass die Zuordnung Ihrer virtuellen Computer aufgehoben wird, die vorübergehenden IP-Adressen freigegeben werden und der Speicher nur noch im Lesezugriff verfügbar ist. Sobald Sie kündigen, wird die Berechnung sofort beendet.

* Wenn Sie kein Azure AD-Konto über Ihr Unternehmen verwenden, können Sie Ihre Azure-Abonnements kündigen und dann löschen und anschließend Ihre Kreditkarteninformationen aus dem Konto entfernen. Das Konto wird dadurch zwar nicht gelöscht, aber unbrauchbar gemacht. Sie können einen Schritt weiter gehen und auch das zugehörige Microsoft-Konto löschen, solange es nicht für einen anderen Zweck verwendet wird.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Wie kann ich ein Visual Studio Professional-Konto kündigen?

Weitere Informationen finden Sie im Artikel [Verlängerung und Kündigung](/visualstudio/subscriptions/faq/admin/renewal-cancellation). Wenn Sie über Visual Studio Azure-Abonnements verfügen, müssen diese ebenfalls gekündigt und gelöscht werden.

## <a name="next-steps"></a>Nächste Schritte

- Bei Bedarf können Sie ein Abonnement mit nutzungsbasierter Bezahlung im [Azure-Portal](subscription-disabled.md) reaktivieren.
