---
title: Erstellen Sie ein Dynamics 365 for Customer Engagement & PowerApps Angebot auf Microsoft AppSource (Azure Marketplace).
description: Erstellen Sie ein Dynamics 365 for Customer Engagement & PowerApps Angebot auf Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: ea640bc4f5b191c3b01ed2a00ecb1d247f1a602d
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240285"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Wie man ein Dynamics 365 for Customer Engagement & PowerApps Angebot erstellt

In diesem Artikel wird erläutert, wie Sie ein neues Angebot für Dynamics 365 for Customer Engagement und PowerApps erstellen. Alle Angebote für Dynamics 365 for Operations müssen unseren Zertifizierungsprozess durchlaufen. Die Testversion ermöglicht es Benutzern, Ihre Lösung in einer Dynamics 365-Liveumgebung bereitzustellen.

Erstellen Sie, bevor Sie beginnen, ein Konto für den kommerziellen Marketplace in [Partner Center](./create-account.md), und stellen Sie sicher, dass es für das kommerzielle Marketplace-Programm registriert ist.

## <a name="before-you-begin"></a>Voraussetzungen

Überprüfen Sie [Planen Sie ein Dynamics 365-Angebot](marketplace-dynamics-365.md). Dort werden die technischen Anforderungen für dieses Angebot erläutert sowie die Informationen und Assets aufgelistet, die Sie bei der Erstellung des Angebots benötigen.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
3. Wählen Sie auf der Übersichtsseite **+ Neues Angebot** > **Dynamics 365 for Customer Engagement und Power Apps** aus.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Zeigt die Menüpunkte des linken Fensters und die Schaltfläche &quot;Neues Angebot&quot; mit der Auswahl &quot;Kundenbindung&quot;.":::

> [!IMPORTANT]
> Nachdem ein Angebot veröffentlicht wurde, werden sämtliche Änderungen, die Sie im Partner Center daran vornehmen, erst nach erneuter Veröffentlichung des Angebots in Microsoft AppSource angezeigt. Achten Sie darauf, ein Angebot nach einer Änderung stets neu zu veröffentlichen.

## <a name="new-offer"></a>Neues Angebot

Geben Sie eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID ist in der Webadresse für das Angebot und in Azure Resource Manager-Vorlagen für Kunden sichtbar, falls zutreffend.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Die ID kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt. Wenn die Herausgeber-ID z. B. `testpublisherid` lautet und Sie **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird bei AppSource nicht verwendet. Er unterscheidet sich vom Angebotsnamen und anderen Werten, die Kunden präsentiert werden.
- Dieser Name kann nach der Auswahl von **Erstellen** nicht mehr geändert werden.

Wählen Sie **Erstellen** aus, um das Angebot zu generieren. In Partner Center wird die Seite **Angebotseinrichtung** geöffnet.

## <a name="alias"></a>Alias

Geben Sie einen beschreibenden Namen ein, mit dem ausschließlich in Partner Center auf dieses Angebot verwiesen wird. Der Angebotsalias (vorausgefüllt mit dem, was Sie beim Erstellen des Angebots eingegeben haben) wird auf dem Marktplatz nicht verwendet und unterscheidet sich von dem Angebotsnamen, der den Kunden angezeigt wird. Wenn Sie den Angebotsnamen später ändern möchten, wechseln Sie zur Seite [Angebotsliste](dynamics-365-customer-engage-offer-listing.md).

## <a name="setup-details"></a>Details zur Konfiguration

Wählen Sie für **Wie sollen potenzielle Kunden mit diesem Angebot interagieren?** die Option aus, die Sie für dieses Angebot verwenden möchten:

- **App-Lizenzverwaltung über Microsoft aktivieren**: Verwalten Sie Ihre App-Lizenzen über Microsoft. Damit Kunden die Basisfunktionen Ihrer App ohne Lizenz und Premium-Features nach Erwerb einer Lizenz ausführen können, aktivieren Sie das Kontrollkästchen **Kunden die Installation meiner App erlauben, auch wenn keine Lizenzen zugewiesen sind**. Wenn Sie dieses zweite Kontrollkästchen aktivieren, müssen Sie Ihr Lösungspaket so konfigurieren, dass keine Lizenz erforderlich ist.

    > [!NOTE]
    > Sie können diese Einstellung nach dem Veröffentlichen Ihres Angebots nicht mehr ändern. Weitere Informationen zu dieser Einstellung finden Sie unter [ISV-App-Lizenzverwaltung](isv-app-license.md).

- **Jetzt abrufen (kostenlos):** Listen Sie Ihr Angebot für Kunden als kostenlos auf.
- **Kostenlose Testversion (Listing):** Listen Sie Ihr Angebot für Kunden als kostenlos auf, und stellen Sie einen Link zu einer kostenlose Testversion bereit. Kostenlose Testversionen in der Angebotsliste werden von Ihrem Dienst erstellt, verwaltet und konfiguriert und weisen keine von Microsoft verwalteten Abonnements auf.

    > [!NOTE]
    > Die Token, die Ihre Anwendung über den Testlink erhält, können nur verwendet werden, um zum Automatisieren der Kontoerstellung in Ihrer App Benutzerinformationen aus Azure Active Directory (Azure AD) abzurufen. Die Authentifizierung mit diesem Token wird für Microsoft-Konten nicht unterstützt.

- **Kontakt mit mir aufnehmen:** Sammeln Sie über Ihr CRM-System (Customer Relationship Management) Kundenkontaktinformationen. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Marketplace, auf dem der Kunde Ihr Angebot gefunden hat, werden an das CRM-System gesendet, das Sie konfiguriert haben. Weitere Informationen zum Konfigurieren des CRM-Systems finden Sie unter [Kundenleads](#customer-leads).

## <a name="test-drive"></a>Testversion

Eine Testversion ist eine hervorragende Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen für eine festgelegte Anzahl von Stunden Zugang zu einer vorkonfigurierten Umgebung gewähren. Wenn Sie eine Testversion anbieten, können Sie die Konvertierungsrate erhöhen und hochqualifizierte Leads generieren. Um mehr zu erfahren, beginnen Sie mit [Was ist eine Testversion?](what-is-test-drive.md).

> [!TIP]
> Eine Testversion unterscheidet sich von einer kostenlosen Testversion. Sie können entweder eine Testversion, eine kostenlose Testversion oder beides anbieten. Mit beiden können Kunden Ihre Lösung für einen festgelegten Zeitraum nutzen. Eine Testversion umfasst jedoch auch eine praktische, selbstgesteuerte Tour durch die wichtigsten Features und Vorteile Ihres Produkts, die in einem praxisnahen Implementierungsszenario veranschaulicht werden.

Um eine Testversion zu aktivieren, aktivieren Sie das Kontrollkästchen **Testversion aktivieren** und wählen Sie die **Art der Testversion**. Sie konfigurieren die Testversion später. Bei der Testversion müssen Sie Ihr Angebot auch auf ein CRM-System für Kundenleads konfigurieren (siehe nächsten Abschnitt). Deaktivieren Sie dieses Kontrollkästchen, um die Testversion aus Ihrem Angebot zu entfernen.

## <a name="customer-leads"></a>Kundenleads

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Weitere Informationen finden Sie unter [Kundenleads aus Ihrem Marketplace-Angebot](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü (**Eigenschaften**) fortfahren.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Angebotseigenschaften](dynamics-365-customer-engage-properties.md)
- [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md)