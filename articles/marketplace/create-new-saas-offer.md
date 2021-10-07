---
title: Erstellen eines SaaS-Angebots im kommerziellen Marketplace
description: Erstellen Sie ein neues Software-as-a-Service (SaaS)-Angebot zum Auflisten oder Verkaufen in Microsoft AppSource, im Azure Marketplace oder durch das CSP (Cloud Solution Provider)-Programm im Azure Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 5da6232a9bedeeb8228caecc79c7a7160630a8cd
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080812"
---
# <a name="create-a-saas-offer"></a>Erstellen eines SaaS-Angebots

Als Herausgeber im kommerziellen Marketplace können Sie ein Software-as-a-Service (SaaS)-Angebot erstellen, damit potenzielle Kunden Ihre SaaS-basierte technische Lösung erwerben können. In diesem Artikel wird erläutert, wie Sie ein SaaS-Angebot für den kommerziellen Microsoft-Marketplace erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Wenn Sie dies noch nicht getan haben, lesen Sie [Planen eines SaaS-Angebots.](plan-saas-offer.md) Dort werden die technischen Anforderungen für Ihre SaaS-App erläutert. Außerdem finden Sie Informationen und Ressourcen, die Sie beim Erstellen Ihres Angebots benötigen. Sofern Sie nicht nur eine einfache Auflistung (Auflistungsoption **Kontakt mit mir aufnehmen**) im kommerziellen Marketplace veröffentlichen möchten, muss Ihre SaaS-Anwendung technische Anforderungen an die Authentifizierung erfüllen.

> [!IMPORTANT]
> Es wird empfohlen, ein separates Entwicklungs-/Testangebot (DEV) und ein separates Produktionsangebot (PROD) zu erstellen. In diesem Artikel wird beschrieben, wie ein PROD-Angebot erstellt wird. Ausführliche Informationen zum Erstellen eines DEV-Angebots finden Sie unter [Erstellen eines SaaS-Angebots zu Testzwecken](create-saas-dev-test-offer.md).

## <a name="create-a-saas-offer"></a>Erstellen eines SaaS-Angebots

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Arbeitsbereichsansicht](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

1. Wählen Sie auf der Startseite die Kachel **Marketplace offers** (Marketplace-Angebote) aus.

    [ ![Veranschaulichung der Kachel „Marketplace-Angebote“ auf der Startseite in Partner Center](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Klicken Sie auf der Marketplace-Angebotsseite auf **+ Neues Angebot** > **SaaS (Software-as-a-Service)** .

    [ ![Abbildung der SaaS-Angebotsangebot in der Liste „Neues Angebot“](./media/new-offer-saas-workspaces.png) ](./media/new-offer-saas-workspaces.png#lightbox)

1. Geben Sie in das Dialogfeld **New Software as a Service** (Neues SaaS-Angebot) eine **Angebots-ID** ein. Diese ID ist in der URL der Auflistung im kommerziellen Marketplace und ggf. in den Azure Resource Manager-Vorlagen sichtbar. Wenn Sie in diesem Feld z. B. **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
   + Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
   + Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
   + Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

1. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

   + Dieser Name ist nicht im kommerziellen Marketplace sichtbar und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.
   + Nachdem Sie **Erstellen** ausgewählt haben, kann der Angebotsalias nicht mehr geändert werden.
1. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Übersicht** aus.
1. Wählen Sie auf der Registerkarte **Übersicht** die Option **+ Neues Angebot** > **SaaS (Software-as-a-Service)** aus.

   :::image type="content" source="./media/new-offer-saas.png" alt-text="Screenshot des linken Navigationsmenüs mit der Liste „Neues Angebot“":::

1. Geben Sie im Dialogfeld **Neues Angebot** eine **Angebots-ID** ein. Diese ID ist in der URL der Auflistung im kommerziellen Marketplace und ggf. in den Azure Resource Manager-Vorlagen sichtbar. Wenn Sie in diesem Feld z. B. **test-offer-1** eingeben, lautet die Webadresse für das Angebot `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
   + Jedes Angebot in Ihrem Konto muss über eine eindeutige Angebots-ID verfügen.
   + Verwenden Sie nur Kleinbuchstaben und Zahlen. Sie kann Bindestriche und Unterstriche enthalten, jedoch keine Leerzeichen, und ist auf 50 Zeichen beschränkt.
   + Nachdem Sie **Erstellen** ausgewählt haben, kann die Angebots-ID nicht mehr geändert werden.

1. Geben Sie einen **Angebotsalias** ein. Dies ist der Name, der für das Angebot im Partner Center verwendet wird.

   + Dieser Name ist nicht im kommerziellen Marketplace sichtbar und unterscheidet sich vom Angebotsnamen und anderen Werten, die den Kunden angezeigt werden.
   + Nachdem Sie **Erstellen** ausgewählt haben, kann der Angebotsalias nicht mehr geändert werden.
1. Wählen Sie **Erstellen** aus, um das Angebot zu generieren und fortzufahren.

---

## <a name="configure-your-saas-offer-setup-details"></a>Details zur Einrichtung Ihres SaaS-Angebots

Auf der Registerkarte **Angebotseinrichtung** unter **Einrichtungsdetails** wählen Sie aus, ob Sie Ihr Angebot über Microsoft vertreiben oder Ihre Transaktionen unabhängig verwalten möchten. Über Microsoft vertriebene Angebote werden als _transaktionsfähige Angebote_ bezeichnet. Dabei wird der Geldfluss für eine Softwarelizenz im Namen des Herausgebers von Microsoft abgewickelt. Weitere Informationen zu diesen Optionen finden Sie unter [Auflisten von Optionen](plan-saas-offer.md#listing-options) und [Bestimmung Ihrer Veröffentlichungsoption](determine-your-listing-type.md).

1. Wählen Sie **Ja** aus, wenn der Verkauf über Microsoft erfolgen soll und die Transaktionen über uns abgewickelt werden sollen. Fahren Sie mit [Aktivieren einer Testversion](#enable-a-test-drive-optional) fort.
1. Um Ihr Angebot über den kommerziellen Marketplace aufzulisten und Transaktionen unabhängig abzuwickeln, wählen Sie **Nein** aus, und führen Sie dann eine der folgenden Aktionen aus:
   + Um ein kostenloses Abonnement für Ihr Angebot bereitzustellen, wählen Sie **Jetzt abrufen (kostenlos)** aus. Geben Sie dann im eingeblendeten Feld **Angebots-URL** die (mit *http* oder *https* beginnende) URL ein, unter der Kunden per [1-Klick-Authentifizierung über Azure Active Directory (Azure AD)](azure-ad-saas.md) eine Testversion erhalten können. Beispiel: `https://contoso.com/saas-app`.
   + Um eine kostenlose 30-Tage-Testversion bereitzustellen, wählen Sie **Kostenlose Testversion** aus und geben dann im eingeblendeten Feld **Test-URL** die (mit *http* oder *https* beginnende) URL ein, unter der Kunden per [1-Klick-Authentifizierung über Azure Active Directory (Azure AD)](azure-ad-saas.md) auf Ihre kostenlose Testversion zugreifen können. Beispiel: `https://contoso.com/trial/saas-app`.
   + Wählen Sie **Kontakt mit mir aufnehmen** aus, wenn potenzielle Kunden Kontakt zu Ihnen aufnehmen und Ihr Angebot erwerben sollen.

    > [!NOTE]
    > Sollten sich Ihre Umstände ändern, können Sie ein veröffentlichtes Auflistungsangebot konvertieren, sodass es über den kommerziellen Marketplace vertrieben wird. Es ist jedoch nicht möglich ein veröffentlichtes transaktionsfähiges Angebot in ein Auflistungsangebot zu konvertieren. Stattdessen müssen Sie ein neues Auflistungsangebot erstellen und die Verteilung des veröffentlichten transaktionsfähigen Angebots stoppen.

## <a name="enable-a-test-drive-optional"></a>Aktivieren einer Testversion (optional)

Eine Testversion ist eine hervorragende Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen für eine festgelegte Anzahl von Stunden Zugang zu einer vorkonfigurierten Umgebung gewähren. Wenn Sie eine Testversion anbieten, können Sie die Konvertierungsrate erhöhen und hochqualifizierte Leads generieren. Weitere Informationen zu Testversionen finden Sie unter [Was ist eine Testversion?](./what-is-test-drive.md)

> [!TIP]
> Eine Testversion unterscheidet sich von einer kostenlosen Testversion. Sie können entweder eine Testversion, eine kostenlose Testversion oder beides anbieten. Mit beiden können Kunden Ihre Lösung für einen festgelegten Zeitraum nutzen. Eine Testversion umfasst jedoch auch eine praktische, selbstgesteuerte Tour durch die wichtigsten Features und Vorteile Ihres Produkts, die in einem praxisnahen Implementierungsszenario veranschaulicht werden.

### <a name="to-enable-a-test-drive"></a>So aktivieren Sie eine Testversion

1. Aktivieren Sie unter **Testversion** das Kontrollkästchen **Aktivieren einer Testversion**.
1. Wählen Sie in der eingeblendeten Liste den Typ der Testversion aus.

## <a name="configure-lead-management"></a>Konfigurieren der Leadverwaltung

Verbinden Sie Ihr Customer Relationship Management (CRM)-System mit Ihrem Angebot im kommerziellen Marketplace, damit Sie Kundenkontaktinformationen erhalten, wenn ein Kunde Interesse bekundet oder Ihr Produkt einsetzt. Sie können diese Verbindung während oder nach der Erstellung des Angebots jederzeit ändern.

> [!NOTE]
> Sie müssen die Leadverwaltung konfigurieren, wenn Sie Ihr Angebot über Microsoft verkaufen oder die Auflistungsoption **Kontakt mit mir aufnehmen** ausgewählt haben. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

### <a name="configure-the-connection-details-in-partner-center"></a>Konfigurieren Sie die Verbindungsdetails im Partner Center

[!INCLUDE [Customer leads](includes/customer-leads.md)]

## <a name="configure-microsoft-365-app-integration"></a>Konfigurieren der Microsoft 365 App-Integration

Sie können eine [einheitliche Ermittlung und Bereitstellung](plan-SaaS-offer.md) Ihres Saas-Angebots und aller zugehörigen Microsoft 365 App-Nutzungen durch Verknüpfen der APP einrichten.

### <a name="integrate-with-microsoft-api"></a>Integrieren in Microsoft API

1. Wenn Ihr SaaS-Angebot nicht in Microsoft Graph-API integriert ist, wählen Sie **Nein** aus. Verknüpfen Sie weiterhin veröffentlichte Microsoft 365 App-Verbrauchsclients.  
1. Wenn Ihr SaaS-Angebot in Microsoft Graph API integriert ist, wählen Sie **Ja** aus, und geben Sie dann die Azure Active Directory APP-ID an, die Sie erstellt und für die Integration mit Microsoft Graph-API registriert haben.

### <a name="link-published-microsoft-365-app-consumption-clients"></a>Verknüpfen Sie veröffentlichte Microsoft 365 App-Verbrauchsclients

1. Wenn Sie nicht über veröffentlichte Office-Add-Ins, Teams-Apps oder SharePoint Framework-Lösungen verfügen, die mit Ihrem SaaS-Angebot zusammenarbeiten, wählen Sie **Nein** aus.
1. Wenn Sie Office-Add-in, Teams-APP oder SharePoint Framework-Lösungen veröffentlicht haben, die mit Ihrem SaaS-Angebot funktionieren, wählen Sie **Ja** aus, und wählen Sie dann **+ weiteren AppSource-Link hinzufügen** aus, um neue Links hinzuzufügen.  
1. Geben Sie einen gültigen AppSource-Link an.
1. Fügen Sie alle Links hinzu, indem Sie auf **+ weiteren AppSource-Link hinzufügen** klicken und gültige AppSource-Links angeben.  
1. Die Reihenfolge, in der die verknüpften Produkte auf der Listenseite des Saas-Angebots angezeigt werden, wird durch den Rangwert angezeigt. Sie können sie ändern, indem Sie das =-Icon in der Liste auswählen, gedrückt halten und nach oben und unten verschieben. 
1. Sie können ein verknüpftes Produkt löschen, indem Sie in der Zeile Produkt die Option **Löschen** auswählen.  

> [!IMPORTANT]
> Wenn Sie ein verknüpftes Produkt nicht mehr automatisch entfernen, wird es nicht automatisch mit dem SaaS-Angebot verknüpft. Sie müssen es aus der Liste der verknüpften Produkte löschen und das SaaS-Angebot erneut übermitteln.  

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von SaaS-Angebotseigenschaften](create-new-saas-offer-properties.md)
