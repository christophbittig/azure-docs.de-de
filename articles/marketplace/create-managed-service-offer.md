---
title: Erstellen eines Angebots für verwaltete Dienste im Azure Marketplace
description: Erstellen eines neuen Angebots für verwaltete Dienste für den Azure Marketplace
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 545e868a902390929254ae6c03d668f8e0d1d10f
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079718"
---
# <a name="create-a-managed-service-offer-for-the-commercial-marketplace"></a>Erstellen eines Angebots für verwaltete Dienste für den kommerziellen Marketplace

In diesem Artikel wird erläutert, wie Sie über Partner Center ein Angebot für einen verwalteten Dienst für den kommerziellen Microsoft-Marketplace erstellen.

Zum Veröffentlichen eines Angebots für einen verwalteten Dienst müssen Sie über eine Gold- oder Silver-Kompetenz von Microsoft in der Kategorie „Cloudplattform“ verfügen. Lesen Sie den Artikel [Vorgehensweise beim Planen eines Angebots für verwaltete Dienste für den kommerziellen Microsoft-Marketplace](./plan-managed-service-offer.md), wenn Sie dies noch nicht getan haben. Anhand der Informationen dieses Artikels können Sie die Ressourcen vorbereiten, die Sie zum Erstellen des Angebots in Partner Center benötigen.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002) an.

1. Wählen Sie auf der Startseite die Kachel **Marketplace offers** (Marketplace-Angebote) aus.

    [ ![Veranschaulichung der Kachel „Marketplace offers“ (Marketplace-Angebote) auf der Startseite in Partner Center](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Klicken Sie auf der Seite „Marketplace offers“ (Marketplace-Angebote) auf **+ Neues Angebot** > **Verwalteter Dienst**.

    [ ![Veranschaulichung der Angebotstypoption „Verwalteter Dienst“](./media/new-offer-managed-service-workspaces.png) ](./media/new-offer-managed-service-workspaces.png#lightbox)

1. Geben Sie im Dialogfeld **New Managed service** (Neuer verwalteter Dienst) eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto. Diese ID ist in der URL der Auflistung im kommerziellen Marketplace und ggf. in den Azure Resource Manager-Vorlagen sichtbar. Wenn Sie in diesem Feld z. B. test-offer-1 eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    - Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
    - Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
    - Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

1. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird. Er wird nicht in den Onlinegeschäften angezeigt und unterscheidet sich vom Angebotsnamen, der Kunden angezeigt wird.
1. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
1. Wählen Sie auf der Registerkarte „Übersicht“ die Option **+ Neues Angebot** > **Verwalteter Dienst** aus.

    :::image type="content" source="./media/new-offer-managed-service.png" alt-text="Darstellung des Navigationsmenüs auf der linken Seite.":::

1. Geben Sie im Dialogfeld **Neues Angebot** eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto. Diese ID ist in der URL der Auflistung im kommerziellen Marketplace und ggf. in den Azure Resource Manager-Vorlagen sichtbar. Wenn Sie in diesem Feld z. B. test-offer-1 eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    - Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
    - Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
    - Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

1. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird. Er wird nicht in den Onlinegeschäften angezeigt und unterscheidet sich vom Angebotsnamen, der Kunden angezeigt wird.
1. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

---

## <a name="setup-details"></a>Details zur Konfiguration

Dieser Abschnitt gilt nicht für diesen Angebotstyp.

## <a name="customer-leads"></a>Kundenleads

Verbinden Sie Ihr CRM-System (Customer Relationship Management) mit Ihrem Angebot im kommerziellen Marketplace, damit Sie Kundenkontaktinformationen erhalten, wenn ein Kunde Interesse an Ihrem Beratungsdienst äußert. Sie können diese Verbindung während oder nach der Erstellung des Angebots jederzeit ändern. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

So konfigurieren Sie die Leadverwaltung in Partner Center:

1. Navigieren Sie in Partner Center zur Registerkarte **Angebotseinrichtung**.
2. Wählen Sie unter **Kundenleads** den Link **Verbinden** aus.
3. Wählen Sie im Dialogfeld **Verbindungsdetails** eine Leadzielgruppe aus der Liste aus.
4. Füllen Sie alle angezeigten Felder aus. Ausführliche Schritte finden Sie in den folgenden Artikeln:

    - [Konfigurieren des Angebots zum Senden von Leads an die Azure-Tabelle](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    - [Konfigurieren Ihres Angebots zum Senden von Leads an Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (früher Dynamics CRM Online)
    - [Konfigurieren Ihres Angebots zum Senden von Leads an den HTTPS-Endpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    - [Konfigurieren Ihres Angebots zum Senden von Leads zu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    - [Konfigurieren Ihres Angebots zum Senden von Leads zu Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Klicken Sie auf den Link **Überprüfen**, um die angegebene Konfiguration zu überprüfen.
6. Wenn Sie die Verbindungsdetails konfiguriert haben, klicken Sie auf **Verbinden**.
7. Wähen Sie **Entwurf speichern** aus.

Nachdem Sie Ihr Angebot zur Veröffentlichung in Partner Center eingereicht haben, wird die Verbindung überprüft und Ihnen wird ein Testlead gesendet. Testen Sie die Leadverbindung, während sich Ihr Angebot vor der Liveschaltung in der Vorschauphase befindet, indem Sie selbst versuchen, das Angebot in der Vorschauumgebung zu kaufen.

> [!TIP]
> Achten Sie darauf, dass die Verbindung mit dem Leadziel immer auf dem neuesten Stand ist, damit keine Leads verloren gehen.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: **Eigenschaften**.

## <a name="next-step"></a>Nächster Schritt

- Konfigurieren von [Angebotseigenschaften](create-managed-service-offer-properties.md)
