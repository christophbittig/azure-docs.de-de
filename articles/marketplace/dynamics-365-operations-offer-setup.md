---
title: Erstellen eines Angebots für Dynamics 365 for Operations in Microsoft AppSource (Azure Marketplace)
description: Erstellen Sie ein Angebot für Dynamics 365 for Operations in Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: 7b155c07edebf80ee45467829c57024d0ad2845f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552278"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Erstellen eines Angebots für Dynamics 365 for Operations

In diesem Artikel wird erläutert, wie Sie ein Angebot für [Dynamics 365 for Operations](https://dynamics.microsoft.com/finance-and-operations) erstellen. Bei diesem Angebotstyp handelt es sich um einen Enterprise Resource Planning-Dienst (ERP), der erweiterte Features für operative Vorgänge, Finanzierung, Fertigung und Lieferkettenmanagement unterstützt. Alle Angebote für Dynamics 365 for Operations müssen unseren Zertifizierungsprozess durchlaufen.

Erstellen Sie, bevor Sie beginnen, ein Konto für den kommerziellen Marketplace in [Partner Center](./create-account.md), und stellen Sie sicher, dass es für das kommerzielle Marketplace-Programm registriert ist.

## <a name="before-you-begin"></a>Voraussetzungen

Überprüfen Sie [Planen Sie ein Dynamics 365-Angebot](marketplace-dynamics-365.md). Dort werden die technischen Anforderungen für dieses Angebot erläutert sowie die Informationen und Assets aufgelistet, die Sie bei der Erstellung des Angebots benötigen.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002) an.

1. Wählen Sie auf der Startseite die Kachel **Marketplace offers** (Marketplace-Angebote) aus.

    [ ![Veranschaulicht die Registerkarte „Marktplatzangebote“ auf der Startseite des Partner Centers.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Wählen Sie auf der Seite Marketplace-Angebote **+ Neues Angebot** >  **Dynamics 365 for Operations** aus.

    [ ![Screenshot der Schaltfläche „Neues Angebot“ auf der Seite „Marketplace-Angebote“ mit Hervorhebung des Angebotstyps „Dynamics 365 for Operations“.](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png) ](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png#lightbox)

> [!IMPORTANT]
> Nachdem ein Angebot veröffentlicht wurde, werden sämtliche Änderungen, die Sie im Partner Center daran vornehmen, erst nach erneuter Veröffentlichung des Angebots in Microsoft AppSource angezeigt. Achten Sie darauf, ein Angebot nach einer Änderung stets neu zu veröffentlichen.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Klicken Sie im linken Navigationsmenü auf **Kommerzieller Marketplace** > **Übersicht**.
1. Klicken Sie auf der Übersichtsseite auf **+ Neues Angebot** > **Dynamics 365 for Operations**.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-operations.png" alt-text="Die Menüoptionen im linken Fensterbereich und die Schaltfläche „Neues Angebot“.":::

> [!IMPORTANT]
> Nachdem ein Angebot veröffentlicht wurde, werden sämtliche Änderungen, die Sie im Partner Center daran vornehmen, erst nach erneuter Veröffentlichung des Angebots in Microsoft AppSource angezeigt. Achten Sie darauf, ein Angebot nach einer Änderung stets neu zu veröffentlichen.

---

## <a name="new-offer"></a>Neues Angebot

Geben Sie eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID ist in der Webadresse für das Angebot und in Azure Resource Manager-Vorlagen für Kunden sichtbar, falls zutreffend.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Die ID kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt. Wenn Sie hier z. B. **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird bei AppSource nicht verwendet. Er unterscheidet sich vom Angebotsnamen und anderen Werten, die Kunden präsentiert werden.
- Dieser Name kann nach der Auswahl von **Erstellen** nicht mehr geändert werden.

Wählen Sie **Erstellen** aus, um das Angebot zu generieren. In Partner Center wird die Seite **Angebotseinrichtung** geöffnet.

## <a name="alias"></a>Alias

Geben Sie einen beschreibenden Namen ein, mit dem ausschließlich in Partner Center auf dieses Angebot verwiesen wird. Der Angebotsalias (vorausgefüllt mit dem, was Sie beim Erstellen des Angebots eingegeben haben) wird auf dem Marktplatz nicht verwendet und unterscheidet sich von dem Angebotsnamen, der den Kunden angezeigt wird. Wenn Sie den Angebotsnamen später ändern möchten, wechseln Sie zur Seite [Angebotsliste](dynamics-365-operations-offer-listing.md).

## <a name="setup-details"></a>Details zur Konfiguration

Wählen Sie für **Wie sollen potenzielle Kunden mit diesem Angebot interagieren?** die Option **Kontakt mit mir aufnehmen** aus.

Sammeln Sie über Ihr CRM-System (Customer Relationship Management) Kundenkontaktinformationen. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Marketplace, auf dem der Kunde Ihr Angebot gefunden hat, werden an das CRM-System gesendet, das Sie konfiguriert haben. Weitere Informationen zum Konfigurieren des CRM-Systems finden Sie unter [Kundenleads](#customer-leads).

## <a name="test-drive"></a>Testversion

Eine Testversion ist eine hervorragende Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen für eine festgelegte Anzahl von Stunden Zugang zu einer vorkonfigurierten Umgebung gewähren. Wenn Sie eine Testversion anbieten, können Sie die Konvertierungsrate erhöhen und hochqualifizierte Leads generieren. Um mehr zu erfahren, beginnen Sie mit [Was ist eine Testversion?](what-is-test-drive.md).

> [!TIP]
> Eine Testversion unterscheidet sich von einer kostenlosen Testversion. Sie können entweder eine Testversion, eine kostenlose Testversion oder beides anbieten. Mit beiden können Kunden Ihre Lösung für einen festgelegten Zeitraum nutzen. Eine Testversion umfasst jedoch auch eine praktische, selbstgesteuerte Tour durch die wichtigsten Features und Vorteile Ihres Produkts, die in einem praxisnahen Implementierungsszenario veranschaulicht werden.

Um eine Testversion zu aktivieren, aktivieren Sie das Kontrollkästchen **Testversion aktivieren** und wählen Sie die **Art der Testversion**. Sie konfigurieren die Testversion später. Bei der Testversion müssen Sie Ihr Angebot auch auf ein CRM-System für Kundenleads konfigurieren (siehe nächsten Abschnitt). Deaktivieren Sie dieses Kontrollkästchen, um die Testversion aus Ihrem Angebot zu entfernen.

## <a name="customer-leads"></a>Kundenleads

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Weitere Informationen finden Sie unter [Kundenleads aus Ihrem Marketplace-Angebot](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="business-applications-isv-program"></a>Business Applications ISV-Programm

Ihr Angebot ist zunächst im Standard-Tarif registriert.

Schließen Sie vor dem Veröffentlichen Ihres Angebots das [Co-Selling-Modul](https://aka.ms/BizAppsISVProgram) ab.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü (**Eigenschaften**) fortfahren.

## <a name="next-steps"></a>Nächste Schritte

- Konfigurieren von [Angebotseigenschaften](dynamics-365-operations-properties.md)
- [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md)
