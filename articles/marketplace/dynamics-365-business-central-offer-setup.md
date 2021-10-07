---
title: Erstellen eines Dynamics 365 for Business Central-Angebots in Microsoft AppSource (Azure Marketplace)
description: Erfahren Sie, wie Sie ein Dynamics 365 for Business Central-Angebot in Microsoft AppSource (Azure Marketplace) erstellen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: 6056259817c24079ae1a2efc912a8251feeaa1ad
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081990"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Erstellen eines Angebots für Dynamics 365 for Business Central

In diesem Artikel wird beschrieben, wie Sie ein Angebot für [Dynamics 365 for Business Central](https://dynamics.microsoft.com/business-central/overview) erstellen. Dieser Angebotstyp ist ein ERP-Dienst (Enterprise Resource Planning), der eine Vielzahl von Geschäftsprozessen unterstützt, z. B. für das Finanzwesen, operative Vorgänge, Lieferketten-, CRM- und Projektmanagement sowie E-Commerce. Alle Angebote für Dynamics 365 for Operations müssen unseren Zertifizierungsprozess durchlaufen.

Erstellen Sie, bevor Sie beginnen, ein Konto für den kommerziellen Marketplace in [Partner Center](create-account.md), und stellen Sie sicher, dass es für das kommerzielle Marketplace-Programm registriert ist.

## <a name="before-you-begin"></a>Voraussetzungen

Überprüfen Sie [Planen Sie ein Dynamics 365-Angebot](marketplace-dynamics-365.md). Dort werden die technischen Anforderungen für dieses Angebot erläutert sowie die Informationen und Ressourcen aufgelistet, die Sie bei der Erstellung des Angebots benötigen.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002) an.

1. Wählen Sie auf der Startseite die Kachel **Marketplace-Angebote** aus.

    [ ![Veranschaulichung der Kachel „Marketplace-Angebote“ auf der Startseite in Partner Center.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Wählen Sie auf der Seite Marketplace-Angebote **+ Neues Angebot** >  **Dynamics 365 for Business Central** aus.

    [ ![Veranschaulicht die Schaltfläche „Neues Angebot“. Der Angebotstyp Dynamics 365 Business Central ist hervorgehoben.](media/dynamics-365/new-offer-dynamics-365-business-central-workspaces.png) ](media/dynamics-365/new-offer-dynamics-365-business-central-workspaces.png#lightbox)

> [!IMPORTANT]
> Nachdem ein Angebot veröffentlicht wurde, werden sämtliche Änderungen, die Sie im Partner Center daran vornehmen, erst nach erneuter Veröffentlichung des Angebots in Microsoft AppSource angezeigt. Achten Sie darauf, ein Angebot nach einer Änderung stets neu zu veröffentlichen.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
1. Wählen Sie in der Übersicht **+ Neues Angebot** > **Dynamics 365 for Business Central** aus.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-business-central.png" alt-text="Die Menüoptionen im linken Fensterbereich und die Schaltfläche „Neues Angebot“.":::

> [!IMPORTANT]
> Nachdem ein Angebot veröffentlicht wurde, werden sämtliche Änderungen, die Sie im Partner Center daran vornehmen, erst nach erneuter Veröffentlichung des Angebots in Microsoft AppSource angezeigt. Achten Sie darauf, ein Angebot nach einer Änderung stets neu zu veröffentlichen.

---

## <a name="new-offer"></a>Neues Angebot

Geben Sie im angezeigten Dialogfeld eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID ist in der Webadresse für das Angebot und in Azure Resource Manager-Vorlagen für Kunden sichtbar, falls zutreffend.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Die ID kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt. Wenn die Herausgeber-ID z. B. `testpublisherid` lautet und Sie **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird bei AppSource nicht verwendet. Er unterscheidet sich vom Angebotsnamen und anderen Werten, die Kunden präsentiert werden.
- Dieser Name kann nach der Auswahl von **Erstellen** nicht mehr geändert werden.

Wählen Sie **Erstellen** aus, um das Angebot zu generieren. In Partner Center wird die Seite **Angebotseinrichtung** geöffnet.

## <a name="alias"></a>Alias

Geben Sie einen beschreibenden Namen ein, mit dem ausschließlich in Partner Center auf dieses Angebot verwiesen wird. Der Angebotsalias (vorausgefüllt mit dem, was Sie beim Erstellen des Angebots eingegeben haben) wird auf dem Marktplatz nicht verwendet und unterscheidet sich von dem Angebotsnamen, der den Kunden angezeigt wird. Wenn Sie den Angebotsnamen später ändern möchten, wechseln Sie zur Seite [Angebotsliste](dynamics-365-business-central-offer-listing.md).

## <a name="setup-details"></a>Details zur Konfiguration

Wählen Sie den **Pakettyp** aus, der für Ihr Angebot gilt: (Biz Central).

- Eine **Add-On**-App erweitert die Oberfläche und vorhandene Funktionalität von Dynamics 365 Business Central. Weitere Informationen finden Sie unter [Add-On-Apps](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- Eine **Connect**-App kann in Szenarien verwendet werden, in denen eine Point-to-Point-Verbindung zwischen Dynamics 365 Business Central und einer Drittanbieterlösung oder einem Drittanbieterdienst hergestellt werden muss. Weitere Informationen finden Sie unter [Connect-Apps](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

Wählen Sie für **Wie sollen potenzielle Kunden mit diesem Angebot interagieren?** die Option aus, die Sie für dieses Angebot verwenden möchten.

- **Jetzt abrufen (kostenlos):** Listen Sie Ihr Angebot für Kunden als kostenlos auf.
- **Kostenlose Testversion (Listing):** Listen Sie Ihr Angebot für Kunden als kostenlos auf, und stellen Sie einen Link zu einer kostenlose Testversion bereit. Kostenlose Testversionen in der Angebotsliste werden von Ihrem Dienst erstellt, verwaltet und konfiguriert und weisen keine von Microsoft verwalteten Abonnements auf.

    > [!NOTE]
    > Die Token, die Ihre Anwendung über den Testlink erhält, können nur verwendet werden, um zum Automatisieren der Kontoerstellung in Ihrer App Benutzerinformationen aus Azure Active Directory (Azure AD) abzurufen. Die Authentifizierung mit diesem Token wird für Microsoft-Konten nicht unterstützt.

- **Kontakt mit mir aufnehmen:** Sammeln Sie über Ihr CRM-System (Customer Relationship Management) Kundenkontaktinformationen. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Marketplace, auf dem der Kunde Ihr Angebot gefunden hat, werden an das CRM-System gesendet, das Sie konfiguriert haben. Weitere Informationen zum Konfigurieren des CRM-Systems finden Sie unter [Kundenleads](#customer-leads).

## <a name="test-drive"></a>Testversion

Eine Testversion ist eine hervorragende Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen die Möglichkeit geben, das Produkt vor dem Kauf zu testen. Dies führt zu einer höheren Konversionsrate und zur Generierung von aussichtsreichen Leads. Weitere Informationen finden Sie unter [Was ist eine Testversion?](what-is-test-drive.md).

Aktivieren Sie das Kontrollkästchen **Testversion aktivieren**, um eine Testversion für einen festen Zeitraum zu aktivieren. Deaktivieren Sie dieses Kontrollkästchen, um die Testversion aus Ihrem Angebot zu entfernen.

> [!TIP]
> Eine Testversion unterscheidet sich von einer kostenlosen Testversion. Sie können entweder eine Testversion, eine kostenlose Testversion oder beides anbieten. Mit beiden können Kunden Ihre Lösung für einen festgelegten Zeitraum nutzen. Eine Testversion umfasst jedoch auch eine praktische, selbstgesteuerte Tour durch die wichtigsten Features und Vorteile Ihres Produkts, die in einem praxisnahen Implementierungsszenario veranschaulicht werden.

## <a name="customer-leads"></a>Kundenleads

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Weitere Informationen finden Sie unter [Kundenleads aus Ihrem Marketplace-Angebot](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü (**Eigenschaften**) fortfahren.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Angebotseigenschaften](dynamics-365-business-central-properties.md)
- [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md)
