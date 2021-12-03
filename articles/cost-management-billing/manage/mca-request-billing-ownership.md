---
title: Übertragen des Abrechnungsbesitzes für ein Azure-Abonnement für eine Microsoft-Kundenvereinbarung
description: Hier erfahren Sie, wie Sie den Abrechnungsbesitz von Azure-Abonnements übertragen.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/09/2021
ms.author: banders
ms.openlocfilehash: 482db68e048c645eddf16849b32e39f1dc397d09
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179396"
---
# <a name="transfer-azure-subscription-billing-ownership-for-a-microsoft-customer-agreement"></a>Übertragen des Abrechnungsbesitzes für ein Azure-Abonnement für eine Microsoft-Kundenvereinbarung

Sie müssen den Abrechnungsbesitz für Ihre Azure-Abonnements übertragen, wenn:

- Sie die Abrechnungszuständigkeiten für die Abonnements auf einen anderen Abrechnungsbesitzer übertragen möchten.
- Sie Ihre Azure-Abonnements von einem Lizenzvertrag zu einem anderen übertragen. Beispielsweise von einem Enterprise Agreement oder einem Microsoft Online-Abonnementvertrag (MOSA) zu einer Microsoft-Kundenvereinbarung.

[Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben.](#check-for-access)

Der Übergang verschiebt nur die Abrechnungszuteilung für Ihre Azure-Abonnements; die an Ihre Abonnements gebundenen Azure-Ressourcen werden nicht verschoben, sodass der Übergang Ihre Azure-Dienste nicht unterbricht.

Dieser Prozess enthält die folgenden Aufgaben, die Sie Schritt für Schritt durchlaufen:

1. Anfordern des Abrechnungsbesitzes
2. Überprüfen/Genehmigen der Übertragungsanforderung
3. Überprüfen des Status der Übertragungsanforderung

Stellen Sie zunächst sicher, dass die Person, von der Sie den Abrechnungsbesitz anfordern, über eine der folgenden Rollen verfügt:

- Für eine Microsoft-Kundenvereinbarung muss die Person über die Rolle „Besitzer“ oder „Mitwirkender“ für das Abrechnungskonto oder das entsprechende Abrechnungsprofil bzw. den Rechnungsabschnitt verfügen. Weitere Informationen finden Sie unter [Rollen und Aufgaben für Abrechnungen](understand-mca-roles.md#invoice-section-roles-and-tasks).
- Bei einem Konzernvertrag (Enterprise Agreement, EA) muss die Person ein Kontobesitzer sein.
- Für einen Microsoft Online-Abonnementvertrag muss die Person ein Kontoadministrator sein.

Wenn Sie bereit sind, befolgen Sie die folgenden Anweisungen. Sie können sich auch das folgende Video ansehen, in dem die einzelnen Schritte des Prozesses beschrieben werden.

>[!VIDEO https://www.youtube.com/embed/gfiUI2YLsgc]

## <a name="request-billing-ownership"></a>Anfordern des Abrechnungsbesitzes

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) als Rechnungsabschnitt-Besitzer oder -Mitwirkender eines Abrechnungskontos für die Microsoft-Kundenvereinbarung an. Verwenden Sie die gleichen Anmeldeinformationen, die Sie zum Akzeptieren Ihrer Microsoft-Kundenvereinbarung verwendet haben.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. Wählen Sie auf der Seite mit den Abrechnungsbereichen die Option **Abrechnungsbereiche** und anschließend das Abrechnungskonto aus, das zur Zahlung für Ihre Azure-Abonnementnutzung verwendet wird. Wählen Sie das Abrechnungskonto für Ihre **Microsoft-Kundenvereinbarung** aus.  
    [![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Portal](./media/mca-request-billing-ownership/list-of-scopes.png)](./media/mca-request-billing-ownership/list-of-scopes.png#lightbox)  
    > [!NOTE]
    >Das Azure-Portal speichert den letzten Abrechnungsbereich, auf den Sie zugreifen, und zeigt den Bereich an, wenn Sie das nächste Mal auf die Seite „Kostenverwaltung + Abrechnung“ gelangen. Wenn Sie „Kostenverwaltung + Abrechnung“ bereits besucht haben, wird die Abrechnungsbereicheseite nicht angezeigt. Wenn dies der Fall ist, überprüfen Sie, ob Sie sich im [richtigen Bereich](#check-for-access) befinden. Wenn nicht, [wechseln Sie den Bereich](view-all-accounts.md#switch-billing-scope-in-the-azure-portal), um das Abrechnungskonto für eine Microsoft-Kundenvereinbarung auszuwählen.
1. Wählen Sie auf der linken Seite die Option **Abrechnungsprofile** aus.  
    [![Screenshot: Auswählen von Abrechnungsprofilen](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
    > [!NOTE]
    > Wenn keine Abrechnungsprofile angezeigt werden, befinden Sie sich nicht im richtigen Abrechnungsbereich. Sie müssen ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung und dann Abrechnungsprofile auswählen. Weitere Informationen zum Ändern von Bereichen finden Sie unter [Wechseln des Abrechnungsbereichs im Azure-Portal](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).
1. Wählen Sie ein **Abrechnungsprofil** in der Liste aus. Nachdem Sie den Besitz der Abonnements übernommen haben, wird deren Nutzung über dieses Abrechnungsprofil abgerechnet.
1. Wählen Sie links die Option **Rechnungsabschnitte**.  
    [![Screenshot: Auswählen von Rechnungsabschnitten](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Wählen Sie in der Liste einen Rechnungsabschnitt aus. Jedes Abrechnungsprofil enthält standardmäßig einen Rechnungsabschnitt. Wählen Sie die Rechnung aus, auf die Sie Ihre Azure-Abonnementabrechnung verschieben möchten. Dort wird die Nutzung des Azure-Abonnements übertragen.
1. Wählen Sie links unten die Option **Übertragungsanforderungen** und anschließend **Neue Anforderung hinzufügen** aus.  
    [![Screenshot: Auswählen von Übertragungsanforderungen](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests.png#lightbox)
1. Geben Sie die E-Mail-Adresse des Benutzers ein, dessen Abrechnungsbesitz Sie übernehmen möchten. Der Benutzer muss über eine Kontoadministratorrolle für die alten Abonnements verfügen. Wählen Sie **Übertragungsanforderung senden** aus.  
    [![Screenshot: Senden einer Übertragungsanforderung](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)](./media/mca-request-billing-ownership/mca-send-transfer-requests.png#lightbox)

## <a name="review-and-approve-transfer-request"></a>Überprüfen und genehmigen der Übertragungsanforderung

1. Der Benutzer erhält eine E-Mail mit der Aufforderung, Ihre Übertragungsanforderung zu prüfen.  
    ![Screenshot: E-Mail mit Aufforderung zur Prüfung der Übertragungsanforderung](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)
1. Um die Übertragungsanforderung zu genehmigen, muss der Benutzer auf den Link in der E-Mail klicken und den Anweisungen folgen.  

    Der Benutzer wählt das Abrechnungskonto aus, von dem er Azure-Produkte übertragen möchte. Nach der Auswahl werden zur Übertragung berechtigte Produkte angezeigt. Nachdem Sie die zu übertragenden Azure-Produkte ausgewählt haben, klicken Sie auf **Überprüfen**.

    >[!NOTE]
    > Deaktivierte Abonnements können nicht übertragen werden und werden ggf. in der Liste „Nicht übertragbare Azure-Produkte“ angezeigt. 

    [![Screenshot, der die Prüfung der Übertragungsanforderung zeigt](./media/mca-request-billing-ownership/review-transfer-requests.png)](./media/mca-request-billing-ownership/review-transfer-requests.png#lightbox)
1. Im Bereich **Ergebnis der Übertragungsprüfung** werden die Auswirkungen der zu übertragenden Azure-Produkte angezeigt. Die folgenden Status sind möglich:
    * **Erfolgreich**: Die Überprüfung für dieses Azure-Produkt war erfolgreich, und das Produkt kann übertragen werden.
    * **Warnung**: Es besteht eine Warnung für das ausgewählte Azure-Produkt. Zwar kann das Produkt übertragen werden, doch hat dies Auswirkungen, die dem Benutzer bekannt sein sollten, falls er Maßnahmen zur Minderung ergreifen möchte. Beispielsweise nutzt das zu übertragende Azure-Abonnement die Vorteile einer RI. Nach der Übertragung sind diese Vorteile für das Abonnement nicht mehr vorhanden. Um die Einsparungen zu erhöhen, stellen Sie sicher, dass die RI einem anderen Abonnement zugeordnet wird, das diese Vorteile nutzen kann. Der Benutzer kann stattdessen auch zur Auswahlseite zurückkehren und die Auswahl dieses Azure-Abonnements aufheben.
    * **Fehler**: Das ausgewählte Azure-Produkt kann aufgrund eines Fehlers nicht übertragen werden. Der Benutzer muss zur Auswahlseite zurückkehren und die Auswahl dieses Produkts aufheben, um die anderen ausgewählten Azure-Produkte zu übertragen.  
    ![Screenshot: Überprüfungsoberfläche](./media/mca-request-billing-ownership/validate-transfer-request.png)
1. Wählen Sie nach Abschluss der Überprüfung als **Übergeben** die Option **Übertragen** aus. Es wird eine Meldung `Transfer is in progress` angezeigt, und nach Abschluss des Vorgangs wird eine Meldung `Transfer completed successfully` angezeigt.

## <a name="check-the-transfer-request-status"></a>Überprüfen des Status der Übertragungsanforderung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. Wählen Sie auf der Abrechnungsbereicheseite das Abrechnungskonto aus, für das die Anforderung übertragen wurde.
1. Wählen Sie auf der linken Seite die Option **Abrechnungsprofile** aus.  
    [![Screenshot: Auswählen von Abrechnungsprofilen](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
1. Wählen Sie das **Abrechnungsprofil** aus, für das die Übertragungsanforderung gesendet wurde.
1. Wählen Sie links die Option **Rechnungsabschnitte**.  
    [![Screenshot: Auswählen von Rechnungsabschnitten](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Wählen Sie den Rechnungsabschnitt, für den die Übertragungsanforderung gesendet wurde, in der Liste aus.
1. Wählen Sie links unten die Option **Übertragungsanforderungen** aus. Auf der Seite „Übertragungsanforderungen“ werden folgende Informationen angezeigt:  
    [![Screenshot: Liste mit Übertragungsanforderungen](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png#lightbox)

   |Column|Definition|
   |---------|---------|
   |Anforderungsdatum|Das Datum, an dem die Übertragungsanforderung gesendet wurde.|
   |Recipient|Die E-Mail-Adresse des Benutzers, an den Sie die Anforderung zur Übertragung des Abrechnungsbesitzes gesendet haben.|
   |Ablaufdatum|Das Datum, bis zu dem die Anforderung gültig ist.|
   |Status|Der Status der Übertragungsanforderung.|

    Die Übertragungsanforderung kann folgende Statuswerte besitzen:

   |Status|Definition|
   |---------|---------|
   |In Bearbeitung|Der Benutzer hat die Übertragungsanforderung nicht akzeptiert.|
   |Verarbeitung|Der Benutzer hat die Übertragungsanforderung genehmigt. Die Abrechnung wird für die vom Benutzer ausgewählten Abonnements auf Ihren Rechnungsabschnitt übertragen.|
   |Abgeschlossen| Die Abrechnung wurde für die vom Benutzer ausgewählten Abonnements auf Ihren Rechnungsabschnitt übertragen.|
   |Mit Fehlern abgeschlossen|Die Anforderung wurde abgeschlossen, für einige vom Benutzer ausgewählte Abonnements konnte die Abrechnung jedoch nicht übertragen werden.|
   |Abgelaufen|Die Anforderung ist abgelaufen, da der Benutzer sie nicht rechtzeitig akzeptiert hat.|
   |Canceled|Die Anforderung wurde von einem Benutzer mit Zugriff auf die Übertragungsanforderung abgebrochen.|
   |Abgelehnt|Der Benutzer hat die Übertragungsanforderung abgelehnt.|

1. Wählen Sie eine Übertragungsanforderung aus, um Details anzuzeigen. Auf der Seite mit den Übertragungsdetails werden folgende Informationen angezeigt:  
    [![Screenshot: Liste mit übertragenen Abonnements](./media/mca-request-billing-ownership/mca-transfer-completed.png)](./media/mca-request-billing-ownership/mca-transfer-completed.png#lightbox)

   |Column  |Definition|
   |---------|---------|
   |ID der Übertragungsanforderung|Die eindeutige ID für Ihre Übertragungsanforderung. Wenn Sie eine Supportanfrage senden, teilen Sie dem Azure-Support die ID mit, um die Bearbeitung Ihrer Supportanfrage zu beschleunigen.|
   |Übertragung angefordert am|Das Datum, an dem die Übertragungsanforderung gesendet wurde.|
   |Übertragung angefordert von|Die E-Mail-Adresse des Benutzers, der die Übertragungsanforderung gesendet hat.|
   |Übertragungsanforderung läuft ab am| Das Datum, an dem die Übertragungsanforderung abläuft.|
   |E-Mail-Adresse des Empfängers|Die E-Mail-Adresse des Benutzers, an den Sie die Anforderung zur Übertragung des Abrechnungsbesitzes gesendet haben.|
   |An Empfänger gesendeter Übertragungslink|Die URL, die zur Prüfung der Übertragungsanforderung an den Benutzer gesendet wurde.|

## <a name="supported-subscription-types"></a>Unterstützte Abonnementtypen

Sie können für die unten angegebenen Abonnementtypen den Abrechnungsbesitz anfordern.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure in Open-Lizenzierung](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass-Sponsorship](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Kostenlose Testversion](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure-Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Von Microsoft Azure gesponsertes Angebot](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft-Kundenvereinbarung](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise-Abonnenten (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise-Abonnenten (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise-Abonnenten](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional-Abonnenten](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Alle Gutschriften für das Abonnement sind unter dem neuen Konto nach der Übertragung nicht mehr verfügbar.

\*\* Wird nur für Abonnements in Konten unterstützt, die bei der Registrierung auf der Azure-Website erstellt werden.

## <a name="additional-information"></a>Zusätzliche Informationen

Die folgenden Abschnitte enthalten zusätzliche Informationen zur Übertragung von Abonnements.

### <a name="cancel-a-prior-support-plan"></a>Kündigen eines früheren Supportplans

Wenn Sie über einen Azure-Supportplan verfügen und alle Ihre Azure-Abonnements in eine neue Vereinbarung übertragen, müssen Sie den Supportplan kündigen, da er nicht mit den Abonnements übertragen wird. Wenn Sie z. B. einen Microsoft Online-Abonnementvertrag (ein im Web erworbenes Azure-Abonnement) an die Microsoft-Kundenvereinbarung übertragen. So beenden Sie Ihren Supportplan ab

Sie müssen die Kontoadministrator-Anmeldeinformationen für Ihr altes Konto verwenden, wenn sich die Anmeldeinformationen von denen unterscheiden, die für den Zugriff auf Ihr neues Konto der Microsoft-Kundenvereinbarung verwendet.

1.  Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1.  Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1.  Wählen Sie im linken Bereich **Abrechnungsbereiche** aus.
1.  Wählen Sie das Abrechnungskonto aus, das Ihrem Microsoft-Supportplan zugeordnet ist.
    - Für eine Microsoft-Kundenvereinbarung:
        - Wählen Sie im linken Bereich **Laufende Gebühren** aus.
        - Wählen Sie im rechten Bereich rechts neben dem Zeilenelement „Supportplan“ die Auslassungspunkte ( **...** ) und dann **Automatische Verlängerung deaktivieren** aus.
    - Für eine einen Microsoft Online-Abonnementvertrag (MOSA):
        - Wählen Sie im linken Bereich die Option **Abonnements** aus.
        - Wählen Sie im rechten Bereich das Supportplanabonnement und dann **Abbrechen** aus.

### <a name="access-your-historical-invoices"></a>Zugreifen auf Ihre historischen Rechnungen

Möglicherweise möchten Sie auf die Rechnungen für das Konto Ihres alten Microsoft Online-Abonnementvertrags (einem im Web erworbenen Azure-Abonnement) zugreifen, nachdem Sie den Abrechnungsbesitz auf Ihre neue Microsoft-Kundenvereinbarung übertragen haben. Führen Sie dazu die folgenden Schritte aus:

Sie müssen die Kontoadministrator-Anmeldeinformationen für Ihr altes Konto verwenden, wenn sich die Anmeldeinformationen von denen unterscheiden, die für den Zugriff auf Ihr neues Konto der Microsoft-Kundenvereinbarung verwendet.

1.  Melden Sie sich unter https://portal.azure.com/ beim Azure-Portal an.
1.  Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1.  Wählen Sie im linken Bereich **Abrechnungsbereiche** aus.
1.  Wählen Sie das Abrechnungskonto aus, das Ihrem Microsoft Online-Abonnementvertrag zugeordnet ist.
1.  Wählen Sie im linken Bereich **Rechnungen** aus, um auf Ihre historischen Rechnungen zuzugreifen.

### <a name="no-service-downtime"></a>Keine Dienstunterbrechung

Die Azure-Dienste in dem Abonnement werden ohne Unterbrechung weiter ausgeführt. Wir ändern lediglich die Abrechnungsbeziehung für die Azure-Abonnements, die der Benutzer für die Übertragung auswählt.

### <a name="disabled-subscriptions"></a>Deaktivierte Abonnements

Deaktivierte Abonnements können nicht übertragen werden. Abonnements müssen sich im Zustand „Aktiv“ befinden, um den Abrechnungsbesitz übertragen zu können.

### <a name="azure-resources-transfer"></a>Übertragung von Azure-Ressourcen

Alle Ressourcen aus den Abonnements (wie etwa virtuelle Computer, Datenträger und Websites) werden übertragen.

### <a name="azure-marketplace-products-transfer"></a>Übertragung von Azure Marketplace-Produkten

Azure Marketplace-Produkte werden zusammen mit den entsprechenden Abonnements übertragen.

### <a name="azure-reservations-transfer"></a>Übertragung von Azure-Reservierungen

Bei der Übertragung von EA-Abonnements (Enterprise Agreement) oder Microsoft-Kundenvereinbarungen werden Azure-Reservierungen automatisch mit den Abonnements verschoben.

### <a name="access-to-azure-services"></a>Zugriff auf Azure-Dienste

Der Zugriff für vorhandene Benutzer, Gruppen oder Dienstprinzipale, der per [rollenbasierter Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) zugewiesen wurde, ist von dieser Übertragung nicht betroffen.

### <a name="charges-for-transferred-subscription"></a>Gebühren für übertragene Abonnements

Der ursprüngliche Abrechnungsbesitzer des Abonnements ist für alle Gebühren verantwortlich, die bis zum Abschluss der Übertragung gemeldet wurden. Gebühren, die nach Abschluss der Übertragung gemeldet werden, werden Ihrem Abrechnungsabschnitt zugeordnet. Es kann vorkommen, dass Gebühren, die vor der Übertragung angefallen sind, erst nach der Übertragung gemeldet werden. Diese Gebühren werden in Ihrem Rechnungsabschnitt aufgeführt.

### <a name="cancel-a-transfer-request"></a>Abbrechen einer Übertragungsanforderung

Eine Übertragungsanforderung kann abgebrochen werden, bis sie entweder genehmigt oder abgelehnt wurde. Wenn Sie eine Übertragungsanforderung abbrechen möchten, navigieren Sie zur [Seite mit den Übertragungsdetails](#check-the-transfer-request-status), und wählen Sie am unteren Seitenrand die Option „Abbrechen“.

### <a name="software-as-a-service-saas-transfer"></a>SaaS-Übertragung (Software-as-a-Service)

SaaS-Produkte werden nicht zusammen mit den Abonnements übertragen. Bitten Sie den Benutzer, sich zwecks Übertragung des Abrechnungsbesitzes von SaaS-Produkten [an den Azure-Support zu wenden](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Zusammen mit dem Abrechnungsbesitz kann der Benutzer auch den Ressourcenbesitz übertragen. Der Ressourcenbesitz ermöglicht das Ausführen von Verwaltungsvorgängen (etwa das Löschen und Anzeigen der Produktdetails). Bei dem Benutzer muss es sich um einen Ressourcenbesitzer für das SaaS-Produkt handeln, um den Ressourcenbesitz übertragen zu können.

## <a name="check-for-access"></a>Überprüfen des Zugriffs
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

- Der Abrechnungsbesitz für die Azure-Abonnements geht auf Ihren Rechnungsabschnitt über. Überwachen Sie die Gebühren für die entsprechenden Abonnements über das [Azure-Portal](https://portal.azure.com).
- Weisen Sie anderen Benutzern Berechtigungen zum Anzeigen und Verwalten der Abrechnung für diese Abonnements zu. Weitere Informationen finden Sie unter [Rollen und Aufgaben für einen Rechnungsabschnitt](understand-mca-roles.md#invoice-section-roles-and-tasks).
