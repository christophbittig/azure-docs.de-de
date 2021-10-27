---
title: Erstellen eines Beratungsdienstangebots für den kommerziellen Marketplace
description: Erstellen Sie mithilfe von Partner Center ein Beratungsdienstangebot für Microsoft AppSource oder Azure Marketplace.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 5556859fb350354fd2c307412ceaed1215620df8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042161"
---
# <a name="create-a-consulting-service-offer"></a>Erstellen eines Beratungsdienstangebots

In diesem Artikel wird erläutert, wie Sie ein Beratungsdienstangebot für den kommerziellen Marketplace mithilfe von Partner Center erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Zum Veröffentlichen eines Beratungsdienstangebots müssen Sie gewisse Berechtigungsvoraussetzungen erfüllen, die Ihre Kenntnisse in Ihrem Feld belegen. Lesen Sie den Artikel [Planen eines Beratungsdienstangebots](./plan-consulting-service-offer.md), wenn Sie dies noch nicht getan haben. Hier werden die Voraussetzungen und Ressourcen beschrieben, die Sie zum Erstellen eines Beratungsdienstangebots in Partner Center benötigen.

## <a name="create-a-consulting-service-offer"></a>Erstellen eines Beratungsdienstangebots

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie auf der Startseite die Kachel **Marktplatzangebote**.

    [ ![Veranschaulichung der Kachel „Marketplace offers“ (Marketplace-Angebote) auf der Startseite in Partner Center](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Klicken Sie auf der Seite „Marketplace offers“ (Marketplace-Angebote) auf **+ Neues Angebot** > **Beratungsdienst**.

    [ ![Veranschaulichung der Liste „Neues Angebot“ auf der Seite „Marketplace offers“ (Marketplace-Angebote)](./media/new-offer-consulting-service-workspaces.png) ](./media/new-offer-consulting-service-workspaces.png#lightbox)

1. Geben Sie im Dialogfeld **New consulting service** (Neuer Beratungsdienst) eine **Angebots-ID** ein. Diese ID ist in der URL des kommerziellen Marketplace-Angebots sichtbar. Wenn Sie in diesem Feld z. B. test-offer-1 eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    * Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
    * Verwenden Sie nur Kleinbuchstaben und Zahlen. Die Angebots-ID kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
    * Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

1. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird. Er wird nicht in den Onlinegeschäften angezeigt und unterscheidet sich vom Angebotsnamen, der Kunden angezeigt wird.
1. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
1. Klicken Sie auf der Registerkarte „Übersicht“ auf **+ Neues Angebot** > **Beratungsdienst**.

    ![Darstellung des Navigationsmenüs auf der linken Seite.](./media/new-offer-consulting-service.png)

1. Geben Sie im Dialogfeld **Neues Angebot** eine **Angebots-ID** ein. Diese ID ist in der URL des kommerziellen Marketplace-Angebots sichtbar. Wenn Sie in diesem Feld z. B. test-offer-1 eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    * Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
    * Verwenden Sie nur Kleinbuchstaben und Zahlen. Die Angebots-ID kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
    * Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

1. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird. Er wird nicht in den Onlinegeschäften angezeigt und unterscheidet sich vom Angebotsnamen, der Kunden angezeigt wird.
1. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

---

## <a name="configure-lead-management"></a>Konfigurieren der Leadverwaltung

Verbinden Sie Ihr CRM-System (Customer Relationship Management) mit Ihrem Angebot im kommerziellen Marketplace, damit Sie Kundenkontaktinformationen erhalten, wenn ein Kunde Interesse an Ihrem Beratungsdienst äußert. Sie können diese Verbindung während oder nach der Erstellung des Angebots jederzeit ändern. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

So konfigurieren Sie die Leadverwaltung in Partner Center:

1.  Navigieren Sie in Partner Center zur Registerkarte **Angebotseinrichtung**.
2.  Wählen Sie unter **Kundenleads** den Link **Verbinden** aus.
3.  Wählen Sie im Dialogfeld **Verbindungsdetails** eine Leadzielgruppe aus der Liste aus.
4.  Füllen Sie alle angezeigten Felder aus. Ausführliche Schritte finden Sie in den folgenden Artikeln:

    * [Konfigurieren des Angebots zum Senden von Leads an die Azure-Tabelle](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Konfigurieren Ihres Angebots zum Senden von Leads an Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (früher Dynamics CRM Online)
    * [Konfigurieren Ihres Angebots zum Senden von Leads an den HTTPS-Endpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Konfigurieren Ihres Angebots zum Senden von Leads zu Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Konfigurieren Ihres Angebots zum Senden von Leads zu Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Klicken Sie auf den Link **Überprüfen**, um die angegebene Konfiguration zu überprüfen.
6.  Wenn Sie die Verbindungsdetails konfiguriert haben, klicken Sie auf **Verbinden**.
7.  Wähen Sie **Entwurf speichern** aus.

Nachdem Sie Ihr Angebot zur Veröffentlichung in Partner Center eingereicht haben, wird die Verbindung überprüft und Ihnen wird ein Testlead gesendet. Testen Sie die Leadverbindung, während sich Ihr Angebot vor der Liveschaltung in der Vorschauphase befindet, indem Sie selbst versuchen, das Angebot in der Vorschauumgebung zu kaufen.

> [!TIP]
> Achten Sie darauf, dass die Verbindung mit dem Leadziel immer auf dem neuesten Stand ist, damit keine Leads verloren gehen.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren der Eigenschaften eines Beratungsdienstangebots](./create-consulting-service-offer-properties.md)
