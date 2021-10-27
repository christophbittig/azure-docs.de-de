---
title: Azure-Abonnement- und Reservierungsübertragungs-Hub
description: Dieser Artikel hilft Ihnen zu verstehen, was für die Übertragung von Azure-Abonnements erforderlich ist, und enthält Links zu anderen Artikeln für detailliertere Informationen.
author: bandersmsft
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: dbe893dc471f055dd5b062e7ce5dcf492dd0f421
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007253"
---
# <a name="azure-subscription-and-reservation-transfer-hub"></a>Azure-Abonnement- und Reservierungsübertragungs-Hub

Dieser Artikel beschreibt die Arten der unterstützten Übertragungen für Azure-Abonnements und Azure-Reservierungen, die als _Produkte_ bezeichnet werden. Er hilft Ihnen auch zu verstehen, was erforderlich ist, um Azure-Produkte zwischen verschiedenen Abrechnungsvereinbarungen zu übertragen, und bietet Links zu anderen Artikeln für detailliertere Informationen über spezifische Übertragungen. Azure-Produkte werden auf der Grundlage verschiedener Azure-Vereinbarungstypen erstellt, und eine Übertragung von einem Quellvereinbarungstyp auf einen anderen hängt von den Quell- und Zielvereinbarungstypen ab. Azure-Produktübertragungen können ein automatischer oder ein manueller Prozess sein, je nach Quell- und Zielvertragstyp. Wenn es sich um einen manuellen Prozess handelt, bestimmen die Vertragstypen, wie viel manueller Aufwand erforderlich ist.

> [!NOTE]
> Es gibt viele Arten von Azure-Produkten, jedoch kann nicht jedes Produkt von einem Typ auf einen anderen übertragen werden. In diesem Artikel werden nur unterstützte Produktübertragungen dokumentiert. Wenn Sie Hilfe in einer Situation benötigen, die in diesem Artikel nicht behandelt wird, können Sie eine [Azure-Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458) erstellen.

Dieser Artikel hilft Ihnen auch, die Dinge zu verstehen, die Sie wissen sollten, _bevor_ Sie das Rechnungseigentum an einem Azure-Produkt auf ein anderes Konto übertragen. Möglicherweise möchten Sie die Abrechnungseigentümerschaft Ihres Azure-Produkts übertragen, wenn Sie Ihr Unternehmen verlassen, oder Sie möchten, dass Ihr Produkt einem anderen Konto in Rechnung gestellt wird. Die Übertragung des Abrechnungsbesitzes an ein anderes Konto ermöglicht es den Administratoren des neuen Kontos, Rechnungsaufgaben durchzuführen. Sie können die Zahlungsmethode ändern, Gebühren einsehen und das Produkt kündigen.

Wenn Sie den Eigentümer der Abrechnung beibehalten, aber den Produkttyp ändern möchten, siehe [Wechseln Sie Ihr Azure-Abonnement zu einem anderen Angebot](switch-azure-offer.md). Um zu steuern, wer auf Ressourcen im Produkt zugreifen kann, siehe [Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md).

Wenn Sie ein Enterprise Agreement (EA)-Kunde sind, können Ihre Unternehmensadministratoren das Rechnungseigentum an Ihren Produkten zwischen Konten im EA-Portal übertragen. Weitere Informationen finden Sie unter [Ändern des Besitzes von Azure-Abonnements oder -Konten](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Dieser Artikel konzentriert sich auf Produkttransfers. Es wird jedoch auch auf die Ressourcenübertragung eingegangen, da sie für einige Produktübertragungsszenarien erforderlich ist.

Weitere Informationen zu Produktübertragungen zwischen verschiedenen Azure AD-Mandanten finden Sie unter [Übertragen eines Azure-Abonnements auf ein anderes Azure AD-Verzeichnis](../../role-based-access-control/transfer-subscription.md).

> [!NOTE]
> Die meisten Übertragungen von Rechnungseigentum ändern den Service-Tenant der zugrunde liegenden Produkte nicht. Sie verursachen keine Ausfallzeiten. Aber auch wenn sich ein Abrechnungsmandant ändert, hat die Änderung keine Auswirkungen auf laufende Dienste oder Ressourcen.

## <a name="product-transfer-planning"></a>Planung der Produktübertragung

Berücksichtigen Sie bei der Planung des Produkttransfers die Informationen, die Sie zur Beantwortung der folgenden Fragen benötigen:

- Warum ist der Produkttransfer erforderlich?
- Welcher Zeitrahmen ist für den Produkttransfer vorgesehen?
- Welches ist der aktuelle Angebotstyp des Produkts, und auf welchen möchten Sie es übertragen?
  - Microsoft Online Service Program (MOSP), auch bekannt als Pay-As-You-Go (PAYG)
  - Vorheriges Azure-Angebot in CSP
  - Neues Azure-Angebot im CSP, auch Azure Plan mit Microsoft Partner Agreement (MPA) genannt
  - Enterprise Agreement (EA)
  - Microsoft Customer Agreement (MCA) in der Enterprise-Motion, wenn Sie Azure-Dienste über einen Microsoft-Vertreter kaufen, und individuelles MCA, wenn Sie Azure-Dienste über Azure.com kaufen
  - Andere wie MSDN, BizSpark, EOPEN, Azure Pass und kostenlose Testversion
- Verfügen Sie über die erforderlichen Berechtigungen für das Produkt, um eine Übertragung durchführen zu können? Die für die einzelnen Übertragungsarten erforderlichen Berechtigungen sind in der folgenden Tabelle zur Unterstützung von Produktübertragungen aufgeführt.
  - Nur der Rechnungsadministrator eines Kontos kann das Eigentum an einem Abonnement übertragen.
  - Nur der Eigentümer eines Rechnungsadministrators kann das Eigentum an einer Reservierung übertragen.
- Gibt es bestehende Abonnements, die von Reservierungen profitieren, und müssen die Reservierungen zusammen mit dem Abonnement übertragen werden?

Sie sollten eine Antwort auf jede Frage haben, bevor Sie mit der Übertragung fortfahren.

Die Antworten auf die obigen Fragen können Ihnen helfen, frühzeitig mit anderen zu kommunizieren, um Erwartungen und Zeitpläne festzulegen. Der Aufwand für die Produktübertragung ist sehr unterschiedlich, aber eine Übertragung wird wahrscheinlich länger dauern als erwartet.

Die Antworten auf die Fragen zur Art des Quell- und Zielangebots helfen dabei, die technischen Pfade zu definieren, die Sie einschlagen müssen, und die Einschränkungen zu ermitteln, die eine Transferkombination haben könnte. Auf Einschränkungen wird im nächsten Abschnitt näher eingegangen.

## <a name="product-transfer-support"></a>Unterstützung von Produkttransfers

In der folgenden Tabelle wird die Unterstützung für den Produkttransfer zwischen den verschiedenen Vertragsarten beschrieben. Es werden Links für weitere Informationen zu jeder Art von Übertragung angegeben.

Derzeit wird die Übertragung nicht für [Free Trial](https://azure.microsoft.com/offers/ms-azr-0044p/) oder [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) Produkte unterstützt. Wie Sie dies umgehen können, erfahren Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Supportpläne können nicht übertragen werden. Wenn Sie einen Support-Plan haben, sollten Sie ihn für den alten Vertrag kündigen und dann einen neuen für den neuen Vertrag erwerben.

Entwicklungs-/Testprodukte sind in der folgenden Tabelle nicht aufgeführt. Übertragungen für Entwicklungs-/Testprodukte werden genauso gehandhabt wie die anderer Produkttypen. Ein EA Dev/Test-Produkttransfer wird zum Beispiel wie ein EA-Produkttransfer behandelt.

> [!NOTE]
> Reservierungen werden bei den meisten unterstützten Produkttransfers übertragen. Es gibt jedoch einige Transfers, bei denen Reservierungen nicht übertragen werden, wie in der folgenden Tabelle angegeben.

| Quell-Produktvereinbarungstyp (aktuell) | Ziel (zukünftiger) Produktvereinbarungs-Typ | Hinweise |
| --- | --- | --- |
| EA | MOSP (PAYG) | <ul><li>Die Übertragung von einer EA-Anmeldung zu einem MOSP-Abonnement erfordert ein Support-Ticket [für die Rechnungsstellung](https://azure.microsoft.com/support/create-ticket/). <li> Reservierungen werden nicht automatisch übertragen und ihre Übertragung wird nicht unterstützt. |
| EA | MCA - Einzelperson | <ul><li>Weitere Informationen finden Sie unter [Übertragung des Eigentums an Azure-Abonnementabrechnungen für ein Microsoft Customer Agreement](mca-request-billing-ownership.md). <li> Reservierungen werden nicht automatisch übertragen, daher müssen Sie ein [Supportticket für die Abrechnung](https://azure.microsoft.com/support/create-ticket/) öffnen, um sie zu übertragen. |
| EA | EA | <ul><li>Für die Übertragung zwischen EA-Registrierungen ist ein [Billing Support Ticket](https://azure.microsoft.com/support/create-ticket/) erforderlich. <li>Reservierungen werden nicht automatisch von einer Registrierung zur nächsten übertragen, daher müssen Sie ein [Billing Support Ticket](https://azure.microsoft.com/support/create-ticket/) öffnen, um sie zu übertragen. <li> Die Übertragung innerhalb der gleichen Registrierung ist die gleiche Aktion wie die Änderung des Kontoinhabers. Einzelheiten finden Sie unter [Ändern des EA-Abonnements oder des Kontoinhabers](ea-portal-administration.md#change-azure-subscription-or-account-ownership). |
| EA | MCA - Unternehmen | <ul><li> Die Übertragung aller Registrierungsprodukte wird als Teil des MCA-Übergangsprozesses von einem EA abgeschlossen. Weitere Informationen finden Sie unter [Aufgaben des Enterprise Agreement in Ihrem Abrechnungskonto für ein Microsoft Customer Agreement abschließen](mca-enterprise-operations.md). <li> Wenn Sie nicht alle Produkte einer Registrierung, sondern nur bestimmte Produkte übertragen möchten, finden Sie weitere Informationen unter [Übertragung des Eigentums an Azure-Abonnements für ein Microsoft-Kundenabkommen](mca-request-billing-ownership.md). |
| EA | MPA | <ul><li> Nur CSP-Direktabrechnungspartner, die als [Azure Expert Managed Services Provider (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) zertifiziert sind, können die Übertragung von Azure-Produkten für ihre Kunden beantragen, die ein Direct Enterprise Agreement (EA) haben. Weitere Informationen finden Sie unter [Abrechnung von Azure-Abonnements auf Ihr MPA-Konto](mpa-request-ownership.md). Produktübertragungen sind nur für Kunden zulässig, die ein Microsoft Customer Agreement (MCA) akzeptiert und einen Azure-Plan mit dem CSP-Programm erworben haben. <li> Es gibt Beschränkungen und Einschränkungen. Weitere Informationen finden Sie unter [Übertragung von EA-Abonnements auf einen CSP-Partner](transfer-subscriptions-subscribers-csp.md#transfer-ea-subscriptions-to-a-csp-partner).  |
| MCA - Einzelperson | MOSP (PAYG) | <ul><li> Einzelheiten finden Sie unter [Übertragung der Abrechnungseigenschaft eines Azure-Abonnements auf ein anderes Konto](billing-subscription-transfer.md). <li> Reservierungen werden nicht automatisch übertragen und ihre Übertragung wird nicht unterstützt. |
| MCA - Einzelperson | MCA - Einzelperson | <ul><li>Weitere Informationen finden Sie unter [Übertragung des Eigentums an Azure-Abonnementabrechnungen für ein Microsoft Customer Agreement](mca-request-billing-ownership.md). <li> Reservierungsübertragungen in Selbstbedienung werden unterstützt. |
| MCA - Einzelperson | EA | <ul><li> Einzelheiten finden Sie unter [Übertragung eines Abonnements auf einen EA](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea). <li> Reservierungsübertragungen in Selbstbedienung werden unterstützt. |
| MCA - Einzelperson | MCA - Unternehmen | <ul><li> Weitere Informationen finden Sie unter [Übertragung des Eigentums an Azure-Abonnementabrechnungen für ein Microsoft Customer Agreement](mca-request-billing-ownership.md).<li> Reservierungsübertragungen in Selbstbedienung werden unterstützt. |
| MCA - Unternehmen | MOSP | <ul><li> Erfordert ein Support-Ticket [für die Rechnungsstellung](https://azure.microsoft.com/support/create-ticket/). <li> Reservierungen werden nicht automatisch übertragen und ihre Übertragung wird nicht unterstützt. |
| MCA - Unternehmen | MCA - Einzelperson | <ul><li> Weitere Informationen finden Sie unter [Übertragung des Eigentums an Azure-Abonnementabrechnungen für ein Microsoft Customer Agreement](mca-request-billing-ownership.md). <li> Reservierungen werden nicht automatisch übertragen, daher müssen Sie ein [Supportticket für die Abrechnung](https://azure.microsoft.com/support/create-ticket/) öffnen, um sie zu übertragen. |
| MCA - Unternehmen | MCA - Unternehmen | <ul><li> Weitere Informationen finden Sie unter [Übertragung des Eigentums an Azure-Abonnementabrechnungen für ein Microsoft Customer Agreement](mca-request-billing-ownership.md). <li> Reservierungen werden nicht automatisch übertragen, daher müssen Sie ein [Supportticket für die Abrechnung](https://azure.microsoft.com/support/create-ticket/) öffnen, um sie zu übertragen.  |
| Vorheriges Azure-Angebot in CSP | Vorheriges Azure-Angebot in CSP | <ul><li> Erfordert ein Support-Ticket [für die Rechnungsstellung](https://azure.microsoft.com/support/create-ticket/). <li> Reservierungen werden nicht automatisch übertragen und ihre Übertragung wird nicht unterstützt. |
| Vorheriges Azure-Angebot in CSP | MPA | Einzelheiten finden Sie unter [Übertragen der Azure-Abonnements eines Kunden auf einen anderen CSP (unter einem Azure-Plan)](/partner-center/transfer-azure-subscriptions-under-azure-plan). |
| MPA | EA | <ul><li> Die automatische Übertragung wird nicht unterstützt. Jede Übertragung erfordert eine manuelle Übertragung von Ressourcen aus dem bestehenden MPA-Produkt in ein neu erstelltes oder ein bestehendes EA-Produkt. <li> Verwenden Sie die Informationen im Abschnitt [Durchführen von Ressourcentransfers](#perform-resource-transfers). <li> Reservierungen werden nicht automatisch übertragen und ihre Übertragung wird nicht unterstützt. |
| MPA | MPA | <ul><li> Einzelheiten finden Sie unter [Übertragen der Azure-Abonnements eines Kunden auf einen anderen CSP (unter einem Azure-Plan)](/partner-center/transfer-azure-subscriptions-under-azure-plan). <li> Reservierungen werden nicht automatisch übertragen und ihre Übertragung wird nicht unterstützt. |
| MOSP (PAYG) | MOSP (PAYG) | <ul><li> Wenn Sie den Abrechnungseigentümer des Abonnements ändern, siehe [Übertragung des Abrechnungseigentums eines Azure-Abonnements auf ein anderes Konto](billing-subscription-transfer.md). <li> Reservierungen werden nicht automatisch übertragen, daher müssen Sie ein [Supportticket für die Abrechnung](https://azure.microsoft.com/support/create-ticket/) öffnen, um sie zu übertragen.  |
| MOSP (PAYG) | MCA - Einzelperson | <ul><li> Weitere Informationen finden Sie unter [Übertragung des Eigentums an Azure-Abonnementabrechnungen für ein Microsoft Customer Agreement](mca-request-billing-ownership.md). <li> Reservierungsübertragungen in Selbstbedienung werden unterstützt. |
| MOSP (PAYG) | EA | Einzelheiten finden Sie unter [Übertragung eines Abonnements auf einen EA](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea). |
| MOSP (PAYG) | MCA - Unternehmen | <ul><li> Weitere Informationen finden Sie unter [Übertragung des Eigentums an Azure-Abonnementabrechnungen für ein Microsoft Customer Agreement](mca-request-billing-ownership.md). <li> Reservierungsübertragungen in Selbstbedienung werden unterstützt. |

## <a name="perform-resource-transfers"></a>Ressourcentransfers durchführen

Bei einigen Produktübertragungen müssen Sie Azure-Ressourcen manuell zwischen Abonnements verschieben. Das Verschieben von Ressourcen kann zu Ausfallzeiten führen, und es gibt verschiedene Einschränkungen für das Verschieben von Azure-Ressourcentypen wie VMs, NSGs, App Services und anderen.

Microsoft stellt kein Tool zum automatischen Verschieben von Ressourcen zwischen Abonnements zur Verfügung. Falls erforderlich, müssen Sie Azure-Ressourcen manuell zwischen Abonnements verschieben. Details finden Sie unter [Ressourcen in eine neue Ressourcengruppe oder Subscription verschieben](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Zusätzliche Zeit und Planung sind erforderlich, wenn Sie eine große Anzahl von Ressourcen verschieben müssen.

## <a name="other-planning-considerations"></a>Weitere Überlegungen zur Planung

Lesen Sie die folgenden Abschnitte, um mehr über andere Überlegungen zu erfahren, bevor Sie einen Produkttransfer starten.

### <a name="resources-transfer-with-subscriptions"></a>Ressourcenübertragung mit Abonnements

Wenn sich nur die Eigentumsverhältnisse bei der Rechnungsstellung ändern, sind die Ressourcen nicht betroffen. Wenn Sie jedoch eine Ressourcenverschiebung durchführen oder den Servicemieter ändern, können Ressourcen betroffen sein.

### <a name="transfer-a-product-from-one-account-to-another"></a>Übertragen eines Produkts von einem Konto auf ein anderes

Wenn Sie Administrator von zwei Konten sind, können Sie ein Produkt zwischen Ihren Konten übertragen. Ihre Konten werden konzeptionell als Konten von zwei verschiedenen Benutzern betrachtet, so dass Sie Produkte zwischen Ihren Konten übertragen können.

Die für die Übertragung Ihres Produkts erforderlichen Schritte finden Sie unter [Übertragung des Rechnungseigentums an einem Azure-Abonnement](billing-subscription-transfer.md).

### <a name="transferring-a-product-shouldnt-create-downtime"></a>Das Übertragen eines Produkts sollte keine Ausfallzeit verursachen

Wenn Sie ein Produkt auf ein Konto im selben Azure AD-Tenant übertragen, hat dies keine Auswirkungen auf die Ressourcen, die im Abonnement laufen. In PowerShell gespeicherte Kontextinformationen werden jedoch nicht aktualisiert. Daher müssen Sie diese unter Umständen löschen oder die Einstellungen ändern. Wenn Sie eine Ressourcenverschiebung durchführen oder den Servicemieter wechseln, können Ressourcen davon betroffen sein.

### <a name="new-account-usage-and-billing-history"></a>Nutzungs- und Abrechnungsverlauf des neuen Kontos

Die einzigen Informationen, die den Benutzern für das neue Konto zur Verfügung stehen, sind die Nutzungs- und Abrechnungshistorie ab dem Zeitpunkt der Übertragung. Die Nutzungs- und Abrechnungshistorie wird nicht mit dem Produkt übertragen.

### <a name="remaining-product-credits"></a>Verbleibende Produktguthaben

Wenn Sie ein Visual Studio- oder Microsoft Partner Network-Produkt besitzen, erhalten Sie monatliches Guthaben. Ihr Guthaben wird nicht mit dem Produkt auf das neue Konto übertragen. Der Benutzer, der die Übertragungsanforderung annimmt, muss eine Visual Studio-Lizenz haben, um die Übertragungsanforderung annehmen zu können. Das Produkt verwendet das Visual Studio-Guthaben, das im Konto des Benutzers verfügbar ist. Weitere Informationen finden Sie unter [Übertragen von Visual Studio- und Partner Network-Abonnements](billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

### <a name="users-keep-access-to-transferred-resources"></a>Die Benutzer haben weiterhin Zugriff auf übertragene Ressourcen

Beachten Sie, dass Benutzer, die Zugriff auf Ressourcen in einem Produkt haben, ihren Zugriff behalten, wenn das Eigentum an der Abrechnung übertragen wird. Es kann aber sein, dass [Administratorrollen](add-change-subscription-administrator.md) und [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) entfernt werden. Der Verlust des Zugriffs tritt ein, wenn sich Ihr Konto in einem anderen Azure AD-Tenant als dem des Produkts befindet und der Benutzer, der die Übertragungsanfrage gesendet hat, das Produkt in den Tenant Ihres Kontos verschiebt.

Sie können die Benutzer, die über Azure-Rollenzuweisungen für den Zugriff auf Ressourcen im Produkt verfügen, im Azure-Portal anzeigen. Navigieren Sie [im Azure-Portal zur Seite „Abonnements“](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wählen Sie dann das Produkt, das Sie prüfen möchten, und wählen Sie dann **Zugriffskontrolle (IAM)** im linken Fensterbereich. Wählen Sie dann **Rollenzuweisungen** oben auf der Seite. Auf der Seite Rollenzuweisungen werden alle Benutzer aufgelistet, die Zugriff auf das Produkt haben.

Selbst wenn die [Azure-Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md) während der Übertragung entfernt werden, haben die Benutzer des ursprünglichen Besitzerkontos möglicherweise weiterhin über andere Sicherheitsmechanismen Zugriff auf das Produkt, einschließlich:

- Verwaltungszertifikate, die dem Benutzer Administratorrechte auf Abonnementressourcen gewähren. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](../../cloud-services/cloud-services-certs-create.md).
- Tastenkombinationen für Dienste wie Storage. Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-account-create.md).
- Anmeldeinformationen für den Remotezugriff für Dienste wie Azure Virtual Machines.

Wenn der Empfänger den Zugang zu Ressourcen einschränken muss, sollte er in Erwägung ziehen, alle mit dem Dienst verbundenen Geheimnisse zu aktualisieren. Die meisten Ressourcen können aktualisiert werden. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie dann im Menü „Hub“ **Alle Ressourcen** aus. Wählen Sie dann die Ressource aus. Wählen Sie anschließend auf der Ressourcenseite die Option **Einstellungen** aus. Hier können Sie die vorhandenen Schlüssel anzeigen und aktualisieren.

### <a name="you-pay-for-usage-when-you-receive-ownership"></a>Sie zahlen für die Nutzung, wenn der Besitz an Sie übertragen wurde

Ihr Konto ist verantwortlich für die Bezahlung jeglicher Nutzung, die ab dem Zeitpunkt der Übertragung gemeldet wird. Es kann vorkommen, dass eine Nutzung vor der Übertragung stattgefunden hat, aber erst danach gemeldet wurde. Diese Nutzung ist in der Abrechnung für Ihr Konto enthalten.

### <a name="transfer-enterprise-agreement-product-ownership"></a>Übertragung des Eigentums an den Produkten des Enterprise Agreement

Der Unternehmensadministrator kann den Kontobesitz für beliebige Konten aktualisieren, auch wenn der ursprüngliche Kontobesitzer nicht mehr der Organisation angehört. Weitere Informationen zum Übertragen von Konten mit Azure Enterprise Agreement finden Sie unter [Azure Enterprise-Übertragungen](ea-transfers.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
