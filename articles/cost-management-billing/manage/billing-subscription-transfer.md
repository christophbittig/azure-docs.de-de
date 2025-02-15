---
title: Übertragen des Abrechnungsbesitzes eines Azure-Abonnements
description: Beschreibt das Übertragen des Abrechnungsbesitzes eines Azure-Abonnements an ein anderes Konto
keywords: Azure-Abonnement übertragen, Azure Übertragung Abonnement, Verschieben Azure-Abonnement in anderes Konto, Azure Änderung Abonnementsbesitzer, Übertragen Azure-Abonnement an anderes Konto, Azure Abrechnungsbesitz übertragen
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 1417d727565b349f9f18b0add73d443c22a4dbb3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992323"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Übertragen des Abrechnungsbesitzes eines Azure-Abonnements an ein anderes Konto

In diesem Artikel werden die Schritte zum Übertragen des Abrechnungsbesitzes eines Azure-Abonnements an ein anderes Konto beschrieben. Bevor Sie den Abrechnungsbesitz eines Abonnements übertragen, lesen Sie [Informationen zum Übertragen des Abrechnungsbesitzes für ein Azure-Abonnement](subscription-transfer.md).

Wenn Sie den Abrechnungsbesitz behalten, den Typ des Abonnements jedoch ändern möchten, finden Sie Informationen hierzu unter [Ändern Ihres Azure-Abonnements in ein anderes Angebot](switch-azure-offer.md). Informationen zur Steuerung, wer auf Ressourcen im Abonnement zugreifen kann, finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

Wenn Sie ein Kunde mit Enterprise Agreement (EA) sind, können Administratoren in Ihrem Unternehmen den Abrechnungsbesitz Ihrer Abonnements zwischen den Konten übertragen. Weitere Informationen finden Sie unter [Ändern des Besitzes von Azure-Abonnements oder -Konten](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Nur der Abrechnungsadministrator eines Kontos kann den Besitz eines Abonnements übertragen.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Übertragen des Abrechnungsbesitzes eines Azure-Abonnements

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) als Administrator für das Abrechnungskonto des Abonnements an, das Sie übertragen möchten. Wenn Sie sich nicht sicher sind, ob Sie über Administratorrechte verfügen, oder wenn Sie ermitteln möchten, wer Administrator ist, finden Sie entsprechende Informationen unter [Ermitteln des Abrechnungsadministrators](add-change-subscription-administrator.md#whoisaa).
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
   ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. Wählen Sie links die Option **Abonnements**. Je nach Zugriff müssen Sie möglicherweise einen Abrechnungsbereich auswählen. Wählen Sie dann **Abonnements** oder **Azure-Abonnements** aus.
1. Wählen Sie für das Abonnement, das Sie übertragen möchten, **Abrechnungsbesitz übertragen** aus.  
   ![Auswählen des zu übertragenden Abonnements](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Geben Sie die E-Mail-Adresse eines Benutzer ein, der ein Abrechnungsadministrator für das Konto ist, das der neue Besitzer des Abonnements sein soll.
1. Wenn Sie Ihr Abonnement an ein Konto in einem anderen Azure AD-Mandanten übertragen, wählen Sie aus, ob das Abonnement in den Mandanten des neuen Kontos verschoben werden soll. Weitere Informationen finden Sie unter [Übertragen eines Abonnements an ein Konto in einem anderen Azure AD-Mandanten](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Wenn Sie das Abonnement in den Azure AD-Mandanten des neuen Kontos verschieben, werden alle [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) zum Zugreifen auf Ressourcen im Abonnement dauerhaft entfernt. Nur der Benutzer des neuen Kontos, der Ihre Übertragungsanforderung angenommen hat, hat Zugriff auf die Verwaltung von Ressourcen im Abonnement. Alternativ können Sie die Option **Azure AD-Mandant des Abonnements** deaktivieren, um den Abrechnungsbesitz zu übertragen, ohne das Abonnement in den Mandanten des neuen Kontos zu verschieben. Wenn Sie dies tun, werden vorhandene Azure-Rollenzuweisungen zum Zugreifen auf Azure-Ressourcen beibehalten.  
    ![Seite zum Senden der Übertragungsanforderung](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Wählen Sie **Übertragungsanforderung senden** aus.
1. Der Benutzer erhält eine E-Mail mit der Aufforderung, Ihre Übertragungsanforderung zu prüfen.  
   ![An den Empfänger gesendete E-Mail zur Abonnementübertragung](./media/billing-subscription-transfer/billing-receiver-email.png)
1. Um die Übertragungsanforderung zu genehmigen, muss der Benutzer auf den Link in der E-Mail klicken und den Anweisungen folgen. Anschließend muss der Benutzer eine Zahlungsmethode für das Abonnement auswählen. Sollte der Benutzer über kein Azure-Konto verfügen, muss er sich für ein neues Konto registrieren.  
   ![Erste Webseite für Abonnementübertragung](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Zweite Webseite für Abonnementübertragung](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Dritte Webseite für Abonnementübertragung](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Erfolg! Das Abonnement ist jetzt übertragen.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Übertragen eines Abonnements an ein anderes Azure AD-Mandantenkonto

Wenn Sie sich für Azure registrieren, wird ein Azure Active Directory (AD)-Mandant für Sie erstellt. Der Mandant stellt Ihr Konto dar. Sie verwenden den Mandanten zum Verwalten des Zugriffs auf Ihre Abonnements und Ressourcen.

Wenn Sie ein neues Abonnement erstellen, wird es vom Azure AD-Mandanten Ihres Kontos gehostet. Wenn Sie anderen Benutzern Zugriff auf Ihr Abonnement oder dessen Ressourcen gewähren möchten, müssen Sie diese dazu einladen, Ihrem Mandanten beizutreten. Dadurch können Sie den Zugriff auf Ihre Abonnements und Ressourcen besser steuern.

Wenn Sie den Abrechnungsbesitz Ihres Abonnements an ein Konto in einem anderen Azure AD-Mandanten übertragen, können Sie das Abonnement in den Mandanten des neuen Kontos verschieben. In diesem Fall verlieren alle Benutzer, Gruppen oder Dienstprinzipale, die über [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) zum Verwalten von Abonnements und deren Ressourcen verfügt haben, ihren Zugriff. Nur der Benutzer des neuen Kontos, der Ihre Übertragungsanforderung angenommen hat, hat Zugriff auf die Verwaltung der Ressourcen. Wenn der neue Besitzer den Benutzern, die den Zugriff verloren haben, erneut Zugriff gewähren möchte, muss er diese Benutzer manuell dem Abonnement hinzufügen. Weitere Informationen finden Sie unter [Übertragen eines Azure-Abonnements in ein anderes Azure AD-Verzeichnis](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Übertragen von Visual Studio- und Partner Network-Abonnements

Mit Abonnements für Visual Studio und Microsoft Partner Network sind monatliche Azure-Gutschriften verbunden. Wenn Sie derartige Abonnements übertragen, ist Ihr Guthaben im Zielabrechnungskonto nicht verfügbar. Für das Abonnement wird das Guthaben des Ziel-Abrechnungskontos verwendet. Beispiel: Bob überträgt am 9. September ein Visual Studio Enterprise-Abonnement an das Konto von Jane, die die Übertragung annimmt. Nach der Übertragung wird für das Abonnement die Gutschrift für Janes Konto verwendet. Das Guthaben wird jeweils am 9. des Monats zurückgesetzt.

## <a name="next-steps-after-accepting-billing-ownership"></a>Nächste Schritte nach dem Übernehmen des Abrechnungsbesitzes

Wenn Sie den Abrechnungsbesitz für ein Azure-Abonnement angenommen haben, empfiehlt es sich, die folgenden weiteren Schritte zu prüfen:

1. Überprüfen und aktualisieren Sie die Einstellungen für Dienstadministratoren, Co-Admins und Azure-Rollenzuweisungen. Weitere Informationen finden Sie unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](add-change-subscription-administrator.md) und [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
1. Aktualisieren Sie die Anmeldeinformationen für die Dienste dieses Abonnements, darunter:
   1. Verwaltungszertifikate, die dem Benutzer Administratorrechte auf Abonnementressourcen gewähren. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](../../cloud-services/cloud-services-certs-create.md).
   1. Tastenkombinationen für Dienste wie Storage. Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-account-create.md).
   1. Anmeldeinformationen für den Remotezugriff für Dienste wie Azure Virtual Machines.
1. Wenn Sie mit einem Partner arbeiten, sollten Sie die Partner-ID im Abonnement aktualisieren. Sie können die Partner-ID im [Azure-Portal](https://portal.azure.com) aktualisieren. Weitere Informationen finden Sie unter [Verknüpfen einer Partner-ID mit Ihren Azure-Konten](link-partner-id.md).

## <a name="cancel-a-transfer-request"></a>Abbrechen einer Übertragungsanforderung

Es ist jeweils nur eine Übertragungsanforderung aktiv. Eine Übertragungsanforderung ist 15 Tage lang gültig. Nach den 15 Tagen läuft die Übertragungsanforderung ab.

So brechen Sie eine Übertragungsanforderung ab:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Abonnements**. Wählen Sie das Abonnement, für das Sie eine Übertragungsanforderung gesendet haben, und anschließend **Abrechnungsbesitz übertragen** aus.
1. Wählen Sie unten auf der Seite **Übertragungsanforderung abbrechen** aus.

:::image type="content" source="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" alt-text="Beispiel für das Fenster „Abrechnungsbesitz übertragen“ mit der Option „Übertragungsanforderung abbrechen“" lightbox="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" :::

## <a name="troubleshooting"></a>Problembehandlung

Verwenden Sie die folgenden Informationen zur Problembehandlung, wenn beim Übertragen von Abonnements Probleme auftreten.

### <a name="original-azure-subscription-billing-owner-leaves-your-organization"></a>Ursprünglicher Abrechnungsbesitzer des Azure-Abonnements verlässt Ihre Organisation

> [!Note]
> Dieser Abschnitt bezieht sich speziell auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. Überprüfen Sie, ob Sie Zugriff auf eine [Microsoft-Kundenvereinbarung](mca-request-billing-ownership.md#check-for-access) haben.

Es kann vorkommen, dass der ursprüngliche Besitzer des Abrechnungskontos, der ein Azure-Konto und ein Azure-Abonnement erstellt hat, Ihre Organisation verlässt. Ist dies der Fall, ist dessen Benutzeridentität nicht mehr in der Azure Active Directory-Instanz der Organisation enthalten. Das Azure-Abonnement verfügt dann über keinen Abrechnungsbesitzer. Das führt wiederum dazu, dass für das Konto keine Abrechnungsvorgänge mehr ausgeführt werden können. Dazu zählt auch das Anzeigen und Bezahlen von Rechnungen. Das Abonnement kann dadurch in Zahlungsverzug geraten. Unter Umständen wird das Abonnement schließlich aufgrund ausbleibender Zahlungen deaktiviert. Das kann letztendlich die Löschung des Abonnements zur Folge haben, was Auswirkungen auf jeden Dienst hätte, der unter dem Abonnement ausgeführt wird.

Wenn ein Abonnement nicht mehr über einen gültigen Abrechnungskontobesitzer verfügt, wird von Azure eine E-Mail an andere Abrechnungskontobesitzer, Dienstadministratoren (sofern vorhanden), Co-Admins (sofern vorhanden) und Abonnementbesitzer gesendet, um sie über die Situation zu informieren und einen Link bereitzustellen, über den sie den Abrechnungsbesitz für das Abonnement akzeptieren können. Jeder dieser Benutzer kann den Link auswählen, um den Abrechnungsbesitz zu akzeptieren. Weitere Informationen zu Abrechnungsrollen finden Sie unter [Grundlegendes zu Verwaltungsrollen für Microsoft-Kundenvereinbarungen in Azure](understand-mca-roles.md) sowie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Die E-Mail sieht in etwa so aus:

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-email.png" alt-text="Screenshot: Beispiel-E-Mail zum Akzeptieren des Abrechnungsbesitzes" lightbox="./media/billing-subscription-transfer/orphaned-subscription-email.png" :::

Darüber hinaus wird Abrechnungsbesitzern, Dienstadministratoren, Co-Admins und Abonnementbesitzern im Azure-Portal ein Banner im Detailfenster des Abonnements angezeigt. Wählen Sie den Link im Banner aus, um den Abrechnungsbesitz zu akzeptieren.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-example.png" alt-text="Screenshot: Beispiel eines Abonnements ohne gültigen Abrechnungsbesitzer" lightbox="./media/billing-subscription-transfer/orphaned-subscription-example.png" :::

### <a name="the-transfer-subscription-option-is-unavailable"></a>Die Option „Abonnement übertragen“ ist nicht verfügbar

<a name="no-button"></a> 

Die Self-Service-Abonnementübertragung ist für Ihr Abrechnungskonto nicht verfügbar. Derzeit wird die Übertragung des Abrechnungsbesitzes von Abonnements in Enterprise Agreement (EA)-Konten im Azure-Portal nicht unterstützt. Darüber hinaus wird die Übertragung des Abrechnungsbesitzes für Konten mit Microsoft-Kundenvereinbarung, die in Zusammenarbeit mit einem Microsoft-Vertreter erstellt werden, nicht unterstützt.

###  <a name="not-all-subscription-types-can-transfer"></a>Nicht alle Abonnementtypen können übertragen werden

Nicht alle Abonnementtypen unterstützen die Übertragung des Abrechnungsbesitzes. Unter [Hub für Azure-Abonnementübertragung](subscription-transfer.md) sind die Abonnementtypen aufgelistet, die eine Übertragung unterstützen.

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Verweigern des Zugriffs beim Versuch, den Abrechnungsbesitz des Abonnements zu übertragen

Dieser Fehler wird angezeigt, wenn Sie versuchen, ein Abonnement für einen Microsoft Azure-Plan zu übertragen und nicht über die erforderliche Berechtigung verfügen. Um das Abonnement für einen Microsoft Azure-Plan zu übertragen, müssen Sie im Rechnungsabschnitt entweder Besitzer oder Mitwirkender sein, dem das Abonnement in Rechnung gestellt wird. Weitere Informationen finden Sie unter [Verwalten von Abonnements für den Rechnungsabschnitt](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen und aktualisieren Sie die Einstellungen für Dienstadministratoren, Co-Admins und Azure-Rollenzuweisungen. Weitere Informationen finden Sie unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](add-change-subscription-administrator.md) und [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).