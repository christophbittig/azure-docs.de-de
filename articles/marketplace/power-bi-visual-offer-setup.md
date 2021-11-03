---
title: Erstellen eines Microsoft AppSource-Angebots für ein Power BI-Visual in Partner Center
description: Hier erfahren Sie, wie Sie ein Angebot für ein Power BI-Visual in Partner Center erstellen.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 916013d7bde10e63985324d736a755183200bf3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079356"
---
# <a name="create-a-power-bi-visual-offer"></a>Erstellen eines Angebots für ein Power BI-Visual

In diesem Artikel wird beschrieben, wie Sie über Partner Center ein Angebot für ein Power BI-Visual für [Microsoft AppSource](https://appsource.microsoft.com) erstellen.

Erstellen Sie, bevor Sie beginnen, ein Konto für den kommerziellen Marketplace in [Partner Center](./create-account.md), und stellen Sie sicher, dass es für das kommerzielle Marketplace-Programm registriert ist.

## <a name="before-you-begin"></a>Voraussetzungen

Lesen Sie den Artikel [Planen eines Angebots für ein Power BI-Visual](marketplace-power-bi-visual.md). Dort werden die technischen Anforderungen für dieses Angebot erläutert sowie die Informationen und Assets aufgelistet, die Sie bei der Erstellung des Angebots benötigen.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Arbeitsbereichsansicht](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

1. Wählen Sie auf der Startseite die Kachel **Marktplatzangebote**.

    [ ![Veranschaulichung der Kachel „Marketplace-Angebote“ auf der Startseite in Partner Center](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Klicken Sie auf der Marketplace-Angebotsseite auf **+ Neues Angebot** > **Power BI-Visual**.

    [ ![Abbildung der Menüoptionen im linken Fensterbereich und der Schaltfläche „Neues Angebot“](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png) ](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png#lightbox)

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
1. Klicken Sie auf der Übersichtsseite auf **+ Neues Angebot** > **Power BI-Visual**.

    :::image type="content" source="media/power-bi-visual/new-offer-power-bi-visual.png" alt-text="Abbildung der Menüoptionen im linken Fensterbereich und der Schaltfläche „Neues Angebot“":::

---

> [!IMPORTANT]
> Nachdem ein Angebot veröffentlicht wurde, werden sämtliche Änderungen, die Sie in Partner Center daran vornehmen, erst nach erneuter Veröffentlichung des Angebots in AppSource angezeigt. Achten Sie darauf, ein Angebot nach einer Änderung stets neu zu veröffentlichen.

## <a name="new-offer"></a>Neues Angebot

Geben Sie eine **Angebots-ID** ein. Dies ist ein eindeutiger Bezeichner für jedes Angebot in Ihrem Konto.

- Diese ID ist in der Webadresse für das Angebot und in Azure Resource Manager-Vorlagen für Kunden sichtbar, falls zutreffend.
- Verwenden Sie nur Kleinbuchstaben und Zahlen. Die ID kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt. Wenn die Herausgeber-ID z. B. `testpublisherid` lautet und Sie **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.
- Die Angebots-ID sollte innerhalb der Liste aller anderen Angebote für Power BI-Visuals in Partner Center eindeutig sein.

Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

- Dieser Name wird in AppSource nicht verwendet. Er unterscheidet sich vom Angebotsnamen und anderen Werten, die Kunden präsentiert werden.

Wählen Sie **Erstellen** aus, um das Angebot zu generieren. In Partner Center wird die Seite **Angebotseinrichtung** geöffnet.

## <a name="setup-details"></a>Details zur Konfiguration

Wählen Sie unter **Zusätzliche Einkäufe** aus, ob Ihr Angebot Käufe eines Diensts oder zusätzliche In-App-Käufe erfordert.

Lesen Sie sich bei **Power BI-Zertifizierung** (optional) die Beschreibung genau durch, und aktivieren Sie das Kontrollkästchen, wenn Sie eine Power BI-Zertifizierung beantragen möchte. [Zertifizierte](/power-bi/developer/visuals/power-bi-custom-visuals-certified) Power BI-Visuals erfüllen festgelegte Codeanforderungen, die das Microsoft Power BI-Team getestet und genehmigt hat. Es wird empfohlen, dass Sie Ihr Power BI-Visual übermitteln und veröffentlichen, *bevor* Sie die Zertifizierung beantragen, da der Zertifizierungsprozess zusätzliche Zeit in Anspruch nimmt, die die Veröffentlichung Ihres Angebots verzögern kann.

## <a name="customer-leads"></a>Kundenleads

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Eine Verbindung mit einem CRM ist optional. Weitere Informationen finden Sie unter [Kundenleads aus Ihrem Marketplace-Angebot](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü (**Eigenschaften**) fortfahren.

## <a name="next-steps"></a>Nächste Schritte

- [**Angebotseigenschaften**](power-bi-visual-properties.md)