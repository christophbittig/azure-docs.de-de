---
title: Erstellen eines Power BI-App-Angebots auf Microsoft AppSource (Azure Marketplace)
description: Erstellen eines Power BI-App-Angebots auf Microsoft AppSource (Azure Marketplace)
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 09/27/2021
ms.openlocfilehash: 6020ff6b6152fa559de5f6afc6f6560f1d7252ba
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082522"
---
# <a name="create-a-power-bi-app-offer"></a>Erstellen eines Power BI-App-Angebots

In diesem Artikel wird beschrieben, wie Sie ein Power BI-App-Angebot erstellen. Alle Angebote durchlaufen unseren Zertifizierungsprozess, der Ihre Lösung auf Standardanforderungen, Kompatibilität und korrekte Praktiken prüft.

Erstellen Sie, bevor Sie beginnen, ein Konto für den kommerziellen Marketplace in [Partner Center](./create-account.md), und stellen Sie sicher, dass es für das kommerzielle Marketplace-Programm registriert ist.

## <a name="before-you-begin"></a>Voraussetzungen

Lesen Sie [Planen eines Power BI Angebots](marketplace-power-bi.md). Dort werden die technischen Anforderungen für dieses Angebot erläutert sowie die Informationen und Assets aufgelistet, die Sie bei der Erstellung des Angebots benötigen.

## <a name="create-a-new-offer"></a>Erstellen eines neuen Angebots

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht Arbeitsbereiche](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002) an.

1. Wählen Sie auf der Startseite die Kachel **Marktplatzangebote**.

    [ ![Veranschaulicht die Kachel mit den Microsoft Azure Marketplace-Angeboten auf der Startseite des Partner Centers.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Wählen Sie auf der Marketplace-Angebotsseite **+ Neues Angebot** > **Power BI app**.

    [ ![Illustriert den Power BI-App-Angebotstyp, der über die Schaltfläche Neues Angebot verfügbar ist.](./media/power-bi/new-offer-power-bi-app-workspaces.png) ](./media/power-bi/new-offer-power-bi-app-workspaces.png#lightbox)

> [!IMPORTANT]
> Nachdem ein Angebot veröffentlicht wurde, werden sämtliche Änderungen, die Sie im Partner Center daran vornehmen, erst nach erneuter Veröffentlichung des Angebots in Microsoft AppSource angezeigt. Achten Sie darauf, ein Angebot nach einer Änderung stets neu zu veröffentlichen.

Wenn die Option **Power BI-App** nicht angezeigt wird oder nicht aktiviert ist, verfügt Ihr Konto nicht über die Berechtigung zum Erstellen dieses Angebotstyps. Überprüfen Sie, ob Sie alle [Anforderungen](./marketplace-dynamics-365.md) für diesen Angebotstyp erfüllen (einschließlich der Registrierung für ein Entwicklerkonto).

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
2. Klicken Sie im linken Navigationsmenü auf **Kommerzieller Marketplace** > **Übersicht**.
3. Wählen Sie auf der Übersichtsseite die Option **+ Neues Angebot** > **Power BI-App** aus.

    :::image type="content" source="media/power-bi/new-offer-power-bi-app.png" alt-text="Die Menüoptionen im linken Fensterbereich und die Schaltfläche „Neues Angebot“.":::

> [!IMPORTANT]
> Nachdem ein Angebot veröffentlicht wurde, werden sämtliche Änderungen, die Sie im Partner Center daran vornehmen, erst nach erneuter Veröffentlichung des Angebots in Microsoft AppSource angezeigt. Achten Sie darauf, ein Angebot nach einer Änderung stets neu zu veröffentlichen.

Wenn die Option **Power BI-App** nicht angezeigt wird oder nicht aktiviert ist, verfügt Ihr Konto nicht über die Berechtigung zum Erstellen dieses Angebotstyps. Überprüfen Sie, ob Sie alle [Anforderungen](marketplace-dynamics-365.md) für diesen Angebotstyp erfüllen (einschließlich der Registrierung für ein Entwicklerkonto).

---

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

Geben Sie einen beschreibenden Namen ein, mit dem ausschließlich in Partner Center auf dieses Angebot verwiesen wird. Der Angebotsalias (vorausgefüllt mit dem, was Sie beim Erstellen des Angebots eingegeben haben) wird auf dem Marktplatz nicht verwendet und unterscheidet sich von dem Angebotsnamen, der den Kunden angezeigt wird. Wenn Sie den Angebotsnamen später ändern möchten, wechseln Sie zur Seite [Angebotsliste](power-bi-app-offer-listing.md).

## <a name="setup-details"></a>Details zur Konfiguration

Dieser Abschnitt ist leer und gilt nicht für Power BI-Apps.

## <a name="customer-leads"></a>Kundenleads

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Weitere Informationen finden Sie unter [Kundenleads aus Ihrem Marketplace-Angebot](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü (**Eigenschaften**) fortfahren.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Angebotseigenschaften](power-bi-app-properties.md)
