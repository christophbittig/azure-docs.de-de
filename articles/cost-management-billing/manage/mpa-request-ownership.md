---
title: Übernehmen des Abrechnungsbesitzes für Azure-Abonnements im Falle einer Microsoft-Partnervereinbarung (Microsoft Partner Agreement, MPA)
description: Hier erfahren Sie, wie Sie von anderen Benutzern den Abrechnungsbesitz von Azure-Abonnements für die Microsoft Partner-Vereinbarung (Microsoft Partner Agreement, MPA) anfordern.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 682c07b4ab34bacbca280cea54e5df8a06c735c5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616496"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Übernehmen des Abrechnungsbesitzes für Azure-Abonnements für Ihr MPA-Konto

Ein Cloudlösungsanbieter (Cloud Solution Provider, CSP) kann den Abrechnungsbesitz für Azure-Abonnements von seinen Kunden mit direktem Enterprise Agreement (EA) übernehmen, um eine einzelne, kombinierte Rechnung für verwaltete Dienste und die Azure-Nutzung bereitzustellen.

Dieses Feature steht nur für CSP-Partner mit direkter Abrechnung zur Verfügung, die als [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) zertifiziert sind. Es unterliegt der Governance und den Richtlinien von Microsoft und erfordert ggf. eine Überprüfung und Genehmigung für bestimmte Kunden.

Um den Abrechnungsbesitz anfordern zu können, müssen Sie über die Rolle **Globaler Administrator** oder **Administrator-Agent** verfügen. Weitere Informationen finden Sie unter [Zuweisen von Rollen und Berechtigungen zu Benutzern](/partner-center/permissions-overview).

Dieser Artikel gilt für Abrechnungskonten für Microsoft-Partnervereinbarungen. Diese Konten werden für Cloudlösungsanbieter (Cloud Solution Providers, CSPs) erstellt, um die Abrechnung für ihre Kunden in der neuen Handelsumgebung zu verwalten. Die neue Umgebung ist nur für Partner verfügbar, die über mindestens einen Kunden verfügen, der eine Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) akzeptiert hat und einen Azure-Plan besitzt. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Partnervereinbarung haben.](#check-access-to-a-microsoft-partner-agreement)

## <a name="prerequisites"></a>Voraussetzungen

1. Etablieren Sie eine [Handelspartnerbeziehung](/partner-center/request-a-relationship-with-a-customer) mit dem Kunden. Vergewissern Sie sich anhand der [regionalen CSP-Autorisierungsübersicht](/partner-center/regional-authorization-overview), dass sich Kunden- und Partnermandant in den gleichen autorisierten Regionen befinden.
1. [Vergewissern Sie sich, dass der Kunde die Microsoft-Kundenvereinbarung akzeptiert hat.](/partner-center/confirm-customer-agreement)
1. Richten Sie einen [Azure-Plan](/partner-center/purchase-azure-plan) für den Kunden ein. Falls der Kunde bei mehreren Handelspartnern einkauft, muss für jede Kombination aus Kunde und Handelspartner ein Azure-Plan eingerichtet werden.

## <a name="request-billing-ownership"></a>Anfordern des Abrechnungsbesitzes

1. Melden Sie sich mit CSP-Administrator-Agent-Anmeldeinformationen im CSP-Mandanten beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Azure-Portal zum Anfordern des Abrechnungsbesitzes](./media/mpa-request-ownership/search-cmb.png)
1. Wählen Sie auf der linken Seite **Kunden** und anschließend einen Kunden aus der Liste aus.  
    [![Screenshot: Auswählen von Kunden](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Wählen Sie links unten die Option **Übertragungsanforderungen** und anschließend **Neue Anforderung hinzufügen** aus.  
    [![Screenshot: Auswählen von Übertragungsanforderungen](./media/mpa-request-ownership/mpa-select-transfer-requests.png)](./media/mpa-request-ownership/mpa-select-transfer-requests.png#lightbox)
1. Geben Sie die E-Mail-Adresse des Benutzers in der Kundenorganisation ein, der die Übertragungsanforderung akzeptieren soll. Bei dem Benutzer muss es sich um einen Kontobesitzer für einen Konzernvertrag handeln. Wählen Sie **Übertragungsanforderung senden** aus.  
    [![Screenshot: Senden einer Übertragungsanforderung](./media/mpa-request-ownership/mpa-send-transfer-requests.png)](./media/mpa-request-ownership/mpa-send-transfer-requests.png#lightbox)
1. Der Benutzer erhält eine E-Mail mit der Aufforderung, Ihre Übertragungsanforderung zu prüfen.  
    ![Screenshot: E-Mail mit Aufforderung zur Prüfung der Übertragungsanforderung](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)
1. Um die Übertragungsanforderung zu genehmigen, muss der Benutzer auf den Link in der E-Mail klicken und den Anweisungen folgen.  
    [![Screenshot: Überprüfen der Übertragungsanforderung](./media/mpa-request-ownership/review-transfer-requests.png)](./media/mpa-request-ownership/review-transfer-requests.png#lightbox) Der Benutzer kann das Abrechnungskonto auswählen, von dem er Azure-Produkte übertragen möchte. Nach der Auswahl werden zur Übertragung berechtigte Produkte angezeigt. **Hinweis:** Deaktivierte Abonnements können nicht übertragen werden und werden ggf. in der Liste „Nicht übertragbare Azure-Produkte“ angezeigt. Nachdem Sie die zu übertragenden Azure-Produkte ausgewählt haben, klicken Sie auf **Überprüfen**.
1. Im Bereich **Ergebnis der Übertragungsprüfung** werden die Auswirkungen der zu übertragenden Azure-Produkte angezeigt. Die folgenden Status sind möglich:
    * **Erfolgreich**: Die Überprüfung für dieses Azure-Produkt war erfolgreich, und das Produkt kann übertragen werden.
    * **Warnung**: Es besteht eine Warnung für das ausgewählte Azure-Produkt. Zwar kann das Produkt übertragen werden, doch hat dies Auswirkungen, die dem Benutzer bekannt sein sollten, falls er Maßnahmen zur Minderung ergreifen möchte. Beispielsweise nutzt das zu übertragende Azure-Abonnement die Vorteile einer RI. Nach der Übertragung sind diese Vorteile für das Abonnement nicht mehr vorhanden. Um die Einsparungen zu erhöhen, stellen Sie sicher, dass die RI einem anderen Abonnement zugeordnet wird, das diese Vorteile nutzen kann. Der Benutzer kann stattdessen auch zur Auswahlseite zurückkehren und die Auswahl dieses Azure-Abonnements aufheben.
    * **Fehler**: Das ausgewählte Azure-Produkt kann aufgrund eines Fehlers nicht übertragen werden. Der Benutzer muss zur Auswahlseite zurückkehren und die Auswahl dieses Produkts aufheben, um die anderen ausgewählten Azure-Produkte zu übertragen.  
    ![Screenshot: Überprüfungsoberfläche](./media/mpa-request-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Überprüfen des Status der Übertragungsanforderung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Azure-Portal zum Überprüfen des Status der Übertragungsanforderung](./media/mpa-request-ownership/billing-search-cost-management-billing.png)
1. Wählen Sie auf der linken Seite **Kunden** aus.  
    [![Screenshot: Auswählen von Kunden](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Wählen Sie in der Liste den Kunden aus, für den Sie die Übertragungsanforderung gesendet haben.
1. Wählen Sie links unten die Option **Übertragungsanforderungen** aus. Auf der Seite „Übertragungsanforderungen“ werden folgende Informationen angezeigt: [![Screenshot: Liste mit Übertragungsanforderungen](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png#lightbox)

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
   |Verarbeitung|Der Benutzer hat die Übertragungsanforderung genehmigt. Die Abrechnung für vom Benutzer ausgewählte Abonnements wird auf Ihr Konto übertragen.|
   |Abgeschlossen| Die Abrechnung für vom Benutzer ausgewählte Abonnements wurde auf Ihr Konto übertragen.|
   |Mit Fehlern abgeschlossen|Die Anforderung wurde abgeschlossen, für einige vom Benutzer ausgewählte Abonnements konnte die Abrechnung jedoch nicht übertragen werden.|
   |Abgelaufen|Die Anforderung ist abgelaufen, da der Benutzer sie nicht rechtzeitig akzeptiert hat.|
   |Canceled|Die Anforderung wurde von einem Benutzer mit Zugriff auf die Übertragungsanforderung abgebrochen.|
   |Abgelehnt|Der Benutzer hat die Übertragungsanforderung abgelehnt.|

1. Wählen Sie eine Übertragungsanforderung aus, um Details anzuzeigen. Auf der Seite mit den Übertragungsdetails werden folgende Informationen angezeigt: [![Screenshot: Liste mit übertragenen Abonnements](./media/mpa-request-ownership/mpa-transfer-completed.png)](./media/mpa-request-ownership/mpa-transfer-completed.png#lightbox)

   |Column  |Definition|
   |---------|---------|
   |ID der Übertragungsanforderung|Die eindeutige ID für Ihre Übertragungsanforderung. Wenn Sie eine Supportanfrage senden, teilen Sie dem Azure-Support die ID mit, um die Bearbeitung der Supportanfrage zu beschleunigen.|
   |Übertragung angefordert am|Das Datum, an dem die Übertragungsanforderung gesendet wurde.|
   |Übertragung angefordert von|Die E-Mail-Adresse des Benutzers, der die Übertragungsanforderung gesendet hat.|
   |Übertragungsanforderung läuft ab am| Das Datum, an dem die Übertragungsanforderung abläuft.|
   |E-Mail-Adresse des Empfängers|Die E-Mail-Adresse des Benutzers, an den Sie die Anforderung zur Übertragung des Abrechnungsbesitzes gesendet haben.|
   |An Empfänger gesendeter Übertragungslink|Die URL, die zur Prüfung der Übertragungsanforderung an den Benutzer gesendet wurde.|

## <a name="supported-subscription-types"></a>Unterstützte Abonnementtypen

Sie können für die unten angegebenen Abonnementtypen den Abrechnungsbesitz anfordern.

* [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
* [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* Sie müssen ein Dev/Test-Abonnement über ein Supportticket in ein EA-Unternehmensangebot konvertieren. Das Enterprise Dev/Test-Abonnement wird nach der Übertragung gemäß dem Satz für nutzungsbasierte Bezahlung abgerechnet. Rabatte, die im Rahmen des Enterprise Dev/Test-Angebots über das Enterprise Agreement (EA) des Kunden angeboten wurden, stehen für den CSP-Partner nicht zur Verfügung.

## <a name="additional-information"></a>Zusätzliche Informationen

Der folgende Abschnitt enthält zusätzliche Informationen zur Übertragung von Abonnements.

### <a name="no-service-downtime"></a>Keine Dienstunterbrechung

Die Azure-Dienste in dem Abonnement werden ohne Unterbrechung weiter ausgeführt. Wir ändern lediglich die Abrechnungsbeziehung für die Azure-Abonnements, die der Benutzer für die Übertragung auswählt.

### <a name="disabled-subscriptions"></a>Deaktivierte Abonnements

Deaktivierte Abonnements können nicht übertragen werden. Abonnements müssen sich im Zustand „Aktiv“ befinden, um den Abrechnungsbesitz übertragen zu können.

### <a name="azure-resources-transfer"></a>Übertragung von Azure-Ressourcen

Alle Ressourcen aus den Abonnements (wie etwa virtuelle Computer, Datenträger und Websites) werden übertragen. Bei der Übertragung werden Abonnement-IDs und Ressourcen-IDs beibehalten. 

### <a name="azure-marketplace-products-transfer"></a>Übertragung von Azure Marketplace-Produkten

Azure Marketplace-Produkte, die für von Cloudlösungsanbietern (Cloud Solution Providers, CSPs) verwaltete Abonnements zur Verfügung stehen, werden zusammen mit ihren jeweiligen Abonnements übertragen. Abonnements mit Azure Marketplace-Produkten, die nicht für CSPs freigegeben sind, können nicht übertragen werden.

### <a name="azure-reservations-transfer"></a>Übertragung von Azure-Reservierungen

Azure-Reservierungen werden nicht automatisch zusammen mit Abonnements verschoben. Sie können die Reservierung in EA für andere Abonnements behalten oder [die Reservierung stornieren](../reservations/exchange-and-refund-azure-reservations.md), und Partner können sie in CSP erneut erwerben.

### <a name="access-to-azure-services"></a>Zugriff auf Azure-Dienste

Der Zugriff für vorhandene Benutzer, Gruppen oder Dienstprinzipale, der per [rollenbasierter Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) zugewiesen wurde, ist von dieser Übertragung nicht betroffen. Der Partner erhält keinen neuen Azure RBAC-Zugriff auf die Abonnements.

Die Partner müssen mit dem Kunden zusammenarbeiten, um Zugriff auf Abonnements zu erhalten. Die Partner benötigen Zugriff vom Typ [Administrator im Auftrag von](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) (Admin on Behalf of, AOBO) oder [Azure Lighthouse](../../lighthouse/concepts/cloud-solution-provider.md) auf offene Supporttickets.

### <a name="azure-support-plan"></a>Azure-Supportplan

Der Azure-Support wird nicht zusammen mit den Abonnements übertragen. Wenn der Benutzer alle Azure-Abonnements überträgt, bitten Sie ihn, den Supportplan zu kündigen. Nach der Übertragung ist der CSP-Partner für den Support verantwortlich. Der Kunde muss bei Supportanfragen mit dem CSP-Partner zusammenarbeiten.  

### <a name="charges-for-transferred-subscription"></a>Gebühren für übertragene Abonnements

Der ursprüngliche Abrechnungsbesitzer des Abonnements ist für alle Gebühren verantwortlich, die bis zum Abschluss der Übertragung gemeldet wurden. Für Gebühren, die nach Abschluss der Übertragung gemeldet werden, sind Sie zuständig. Es kann vorkommen, dass Gebühren, die vor der Übertragung angefallen sind, erst nach der Übertragung gemeldet werden. Diese Gebühren werden auf Ihrer Rechnung aufgeführt.

### <a name="cancel-a-transfer-request"></a>Abbrechen einer Übertragungsanforderung

Eine Übertragungsanforderung kann abgebrochen werden, bis sie entweder genehmigt oder abgelehnt wurde. Wenn Sie eine Übertragungsanforderung abbrechen möchten, navigieren Sie zur [Seite mit den Übertragungsdetails](#check-the-transfer-request-status), und wählen Sie am unteren Seitenrand die Option „Abbrechen“.

### <a name="software-as-a-service-saas-transfer"></a>SaaS-Übertragung (Software-as-a-Service)

SaaS-Produkte werden nicht zusammen mit den Abonnements übertragen. Bitten Sie den Benutzer, sich zwecks Übertragung des Abrechnungsbesitzes von SaaS-Produkten [an den Azure-Support zu wenden](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Zusammen mit dem Abrechnungsbesitz kann der Benutzer auch den Ressourcenbesitz übertragen. Der Ressourcenbesitz ermöglicht das Ausführen von Verwaltungsvorgängen wie dem Löschen und Anzeigen von Produktdetails. Bei dem Benutzer muss es sich um einen Ressourcenbesitzer für das SaaS-Produkt handeln, um den Ressourcenbesitz übertragen zu können.

### <a name="additional-approval-for-certain-customers"></a>Zusätzliche Genehmigung für bestimmte Kunden

Bei bestimmten Kundenumstellungsanforderungen ist aufgrund der Art der aktuellen Unternehmensregistrierungsstruktur des Kunden ggf. ein zusätzlicher Überprüfungsprozess mit Microsoft erforderlich. Über solche Anforderungen wird der Partner informiert, wenn er versucht, eine Einladung an Kunden zu senden. Partner werden aufgefordert, mit ihrem Partner Development Manager und dem Kontoteam des Kunden zusammenzuarbeiten, um diesen Überprüfungsprozess abzuschließen.

### <a name="azure-subscription-directory"></a>Azure-Abonnementverzeichnis

Das Verzeichnis der übertragenen Azure-Abonnements muss dem Verzeichnis des Kunden entsprechen, das beim Etablieren der CSP-Beziehung ausgewählt wurde.

Stimmen diese beiden Verzeichnisse nicht überein, konnten die Abonnements nicht übertragen werden. Sie müssen entweder eine neue CSP-Handelspartnerbeziehung mit dem Kunden etablieren, indem Sie das Verzeichnis der Azure-Abonnements auswählen, oder das Verzeichnis der Azure-Abonnements ändern, sodass es dem CSP-Beziehungsverzeichnis des Kunden entspricht. Weitere Informationen finden Sie unter [So weisen Sie Ihrem Azure AD-Verzeichnis ein vorhandenes Abonnement zu](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory).

### <a name="ea-subscription-in-the-non-organization-directory"></a>EA-Abonnement in einem Verzeichnis außerhalb der Organisation

Die EA-Abonnements aus Verzeichnissen außerhalb der Organisation können übertragen werden, sofern das Verzeichnis über eine Handelspartnerbeziehung mit dem CSP verfügt. Hat das Verzeichnis keine Handelspartnerbeziehung, müssen Sie sicherstellen, dass der Organisationsbenutzer im Verzeichnis Berechtigungen vom Typ *Globaler Administrator* besitzt, damit er die Partnerbeziehung akzeptieren kann. Der Domänenteil des Benutzernamens muss entweder der Name der anfängliche Standarddomänenname „[domain name]. onmicrosoft.com" oder eine überprüfte, nicht im Verbund konfigurierte benutzerdefinierte Domäne wie „contoso.com“ sein.  

Informationen zum Hinzufügen eines Benutzers zum Verzeichnis finden Sie unter [ Hinzufügen oder Löschen von Benutzern in Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Partnervereinbarung

[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

* Der Abrechnungsbesitz für die Azure-Abonnements wird auf Sie übertragen. Überwachen Sie die Gebühren für die entsprechenden Abonnements über das [Azure-Portal](https://portal.azure.com).
* Arbeiten Sie mit dem Kunden zusammen, um Zugriff auf die übertragenen Azure-Abonnements zu erhalten. [Weisen Sie Azure-Rollen über das Azure-Portal zu](../../role-based-access-control/role-assignments-portal.md).