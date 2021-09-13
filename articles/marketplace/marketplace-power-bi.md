---
title: Planungshandbuch für Power BI-App-Angebote in Microsoft AppSource (Azure Marketplace)
description: Bei diesem Artikel handelt es sich um das Planungshandbuch für Power BI-App-Angebote in Microsoft AppSource (Azure Marketplace).
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/26/2021
ms.openlocfilehash: 7f21e3b7e95bf4d735d394a9dfebe04023218faa
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061234"
---
# <a name="plan-a-power-bi-app-offer"></a>Planen eines Power BI-App-Angebots

In diesem Artikel werden die Inhalte und Anforderungen behandelt, die zum Veröffentlichen einer Power BI-App in Microsoft [AppSource](https://appsource.microsoft.com/) erforderlich sind. In einer Power BI-App sind anpassbare Inhalte wie Datasets, Berichte und Dashboards gepackt. Sie können die App dann mit anderen Power BI-Plattformen bereitstellen, die AppSource verwenden, vom Entwickler zugelassene Änderungen und Anpassungen vornehmen und die App mit Ihren eigenen Daten verbinden.

Sehen Sie sich zunächst die folgenden Links mit Vorlagen, Tipps und Beispielen an:

- [Erstellen einer Power BI-App](/power-bi/service-template-apps-create)
- [Tipps zum Erstellen einer Power BI-App](/power-bi/service-template-apps-tips)
- [Beispiele](/power-bi/service-template-apps-samples)


## <a name="publishing-benefits"></a>Vorteile der Veröffentlichung

Vorteile der Veröffentlichung im kommerziellen Marketplace:

- Bewerben Sie Ihr Unternehmen, indem Sie die Marke Microsoft nutzen.
- In AppSource können Sie mehr als 100 Millionen Microsoft 365- und Dynamics 365-Benutzer erreichen und im Azure Marketplace über 200.000 Organisationen.
- Nutzen Sie in diesen Marketplaces hochwertige Leads.
- Lassen Sie Ihre Dienste durch die Außendienst- und Telefonmarketingteams von Microsoft bewerben.

## <a name="overview"></a>Übersicht

:::image type="content" source="media/power-bi/power-bi-app-publishing-steps.png" alt-text="Übersicht über die Schritte zum Veröffentlichen eines Power BI-App-Angebots" border="false":::

Wenn Sie bereit sind, Ihr Angebot zu erstellen, fahren Sie weiter unten mit den [nächsten Schritten](#next-steps) fort. Lesen Sie andernfalls hier weiter, um sicherzustellen, dass Sie optimal vorbereitet sind, bevor Sie mit der Angebotserstellung beginnen.

In den nächsten Themen werden die folgenden zentralen Veröffentlichungsschritte behandelt:

1. Erstellen Sie eine Anwendung in Power BI. Sie erhalten einen Link zur Paketinstallation. Dies ist die wichtigste technische Ressource für das Angebot. Senden Sie das Testpaket an die Vorproduktion, bevor Sie das Angebot in Partner Center erstellen. Weitere Informationen finden Sie unter [Was sind Power BI-Apps?](/power-bi/service-template-apps-overview).
2. Fügen Sie das Marketingmaterial hinzu, z. B. einen offiziellen Namen, eine Beschreibung und Logos.
3. Schließen Sie die rechtlichen und unterstützenden Dokumente ein, z. B. Nutzungsbedingungen, Datenschutzrichtlinie, Unterstützungsrichtlinie und Benutzerhilfe.
4. Erstellen Sie das Angebot. Verwenden Sie Partner Center, um die Details des Angebots zu bearbeiten, darunter die Angebotsbeschreibung, Marketingmaterial, rechtliche Hinweise und Supportinformationen sowie Ressourcenspezifikationen.
5. Übermitteln Sie es zur Veröffentlichung.
6. Überwachen Sie den Fortschritt in Partner Center, wo das AppSource-Onboardingteam Ihre App testet, überprüft und zertifiziert.
7. Nach der Zertifizierung sollten Sie die App in der Testumgebung überprüfen und sie dann freigeben. Dadurch wird sie in AppSource gelistet (in den Onlinemodus geschaltet).
8. Überführen Sie das Paket in Power BI in die Produktionsumgebung. Ausführliche Informationen finden Sie unter [Verwalten der Veröffentlichung von Power BI-Apps](/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="requirements"></a>Requirements (Anforderungen)

Um im kommerziellen Marketplace veröffentlicht zu werden, muss Ihre Power BI-App die folgenden technischen und geschäftlichen Anforderungen erfüllen.

### <a name="technical-requirements"></a>Technische Anforderungen

Die technische Hauptressource, die Sie benötigen, ist eine [Power BI-App](/power-bi/connect-data/service-template-apps-overview). Dabei handelt es sich um eine Sammlung von primären Datasets, Berichten oder Dashboards. Außerdem sind optionale verbundene Dienste und eingebettete Datasets enthalten. Dies wurde zuvor als [Inhaltspaket](/power-bi/service-organizational-content-pack-introduction) bezeichnet. Weitere Informationen zum Entwickeln dieser Art von App finden Sie unter [Was sind Power BI-Apps?](/power-bi/connect-data/service-template-apps-overview).

#### <a name="get-an-installation-web-address"></a>Erhalten einer Webadresse für die Installation

Eine Power BI-App kann nur innerhalb der [Power BI](https://powerbi.microsoft.com/)-Umgebung erstellt werden.

1. Melden Sie sich mit einer [Power BI Pro-Lizenz](/power-bi/service-admin-purchasing-power-bi-pro) an.
2. Erstellen und testen Sie Ihre App in Power BI.
3. Wenn Sie die Webadresse für die App-Installation erhalten, fügen Sie sie auf der Seite **Technische Konfiguration** in Partner Center hinzu.

Nachdem Ihre App in Power BI erstellt und getestet wurde, speichern Sie die Webadresse für die Anwendungsinstallation, da Sie diese benötigen, um [ein Power BI-App-Angebot zu erstellen](power-bi-app-offer-setup.md).

### <a name="business-requirements"></a>Geschäftliche Anforderungen

Die geschäftlichen Anforderungen umfassen verfahrenstechnische, vertragliche und rechtliche Verpflichtungen. Die Voraussetzungen lauten wie folgt:

- Sie müssen ein registrierter Herausgeber im kommerziellen Marketplace sein. Wenn Sie nicht registriert sind, führen Sie die Schritte unter [Erstellen eines Kontos im kommerziellen Marketplace in Partner Center](create-account.md) aus.
- Stellen Sie Inhalte bereit, die die Kriterien erfüllen, damit Ihr Angebot in AppSource gelistet wird. Weitere Informationen finden Sie im Blogbeitrag [Have an app to list on AppSource? Here’s how](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how) (Informationen zum Listen einer Anwendung in AppSource).
- Akzeptieren Sie die [Datenschutzbestimmungen von Microsoft](https://privacy.microsoft.com/privacystatement), und befolgen Sie sie.

## <a name="licensing-options"></a>Lizenzierungsoptionen

Für Power BI-App-Angebote steht nur die folgende Lizenzierungsoption zur Verfügung:

| Lizenzierungsoption | Transaktionsprozess |
| --- | --- |
| Jetzt abrufen (kostenlos) | Listen Sie Ihr Angebot für Kunden als kostenlos auf. |
|

\* Als Herausgeber sind Sie für alle Aspekte der Softwarelizenztransaktion verantwortlich, einschließlich (unter anderem) Auftrag, Auftragserfüllung, Messung, Abrechnung, Rechnungsstellung, Zahlung und Eintreibung.

## <a name="customer-leads"></a>Kundenleads

Wenn Sie ein Angebot mithilfe von Partner Center im kommerziellen Marketplace veröffentlichen, sollten Sie es mit Ihrem CRM-System (Customer Relationship Management) verbinden. Dadurch empfangen Sie Kontaktinformationen zum Kunden, sobald jemand sein Interesse an Ihrem Produkt bekundet oder es verwendet. Das Herstellen einer Verbindung mit einem CRM-System ist erforderlich, wenn Sie eine Testversion aktivieren möchten, andernfalls ist dieser Schritt optional. Partner Center unterstützt Azure Table Storage, Dynamics 365 Customer Engagement, HTTPS-Endpunkte, Marketo und Salesforce.

## <a name="legal-contracts"></a>Verträge

Sie benötigen Geschäftsbedingungen, die Kunden akzeptieren müssen, bevor sie Ihr Angebot ausprobieren können, oder einen Link zu dem Ort, an dem sich die Geschäftsbedingungen befinden.

## <a name="offer-listing-details"></a>Details zur Angebotsauflistung

> [!NOTE]
> Der Inhalt der Angebotsauflistung muss nicht in englischer Sprache verfasst werden, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt.

Um das Angebot einfacher zu gestalten, können Sie diese Elemente vorab vorbereiten. Sofern nicht anders angegeben sind alle erforderlich.

- **Name:** Dieser Name wird als Titel Ihres Angebots im kommerziellen Marketplace angezeigt. Der Name ist möglicherweise markenrechtlich geschützt. Er darf keine Emojis enthalten (außer diese sind Teil der Markenzeichen oder Copyrightsymbole) und maximal 50 Zeichen lang sein.
- **Zusammenfassung der Suchergebnisse:** Geben Sie den Zweck oder die Funktion des Angebots in einem Satz ohne Zeilenumbrüche und mit höchstens 100 Zeichen an. Diese Zusammenfassung wird in den Suchergebnissen verwendet, die aus Auflistungen im kommerziellen Marketplace erfasst werden.
- **Beschreibung:** Diese Beschreibung wird in der Übersicht der Auflistungen im kommerziellen Marketplace angezeigt. Sie können z. B ein Wertversprechen, wichtige Vorteile, Zielgruppe, Kategorie- oder Branchenzuordnungen, Möglichkeiten für In-App-Käufe, erforderliche Veröffentlichungen und einen Link zu weiteren Informationen eingeben. Dieses Textfeld enthält Rich-Text-Editor-Steuerelemente, mit denen Sie die Beschreibung ansprechender gestalten können. Verwenden Sie optional HTML-Tags für die Formatierung.
- **Suchbegriffe** (optional): Geben Sie bis zu drei Suchbegriffe an, mit denen Kunden nach Ihrem Angebot suchen können. Sie müssen den **Namen** und die **Beschreibung** des Angebots nicht einschließen, da diese automatisch in die Suche aufgenommen werden.
- **Produkte, mit denen Ihre App zusammenarbeitet** (optional): Geben Sie die Namen von bis zu drei Produkten an, mit denen Ihr Angebot zusammenarbeitet.
- **Links zur Hilfe und Datenschutzrichtlinie:** Geben Sie die URL für die Hilfe und die Datenschutzrichtlinie Ihres Unternehmens an. Es liegt in Ihrer Verantwortung sicherzustellen, dass Ihre App den Gesetzen und Regelungen zum Datenschutz entspricht.
- **Kontaktinformationen**
  - **Supportkontakt:** Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse für Microsoft-Partner an. Diese werden dann von Kunden bei der Eröffnung von Supporttickets verwendet. Schließen Sie auch die URL für Ihre Supportwebsite ein.
  - **Technischer Ansprechpartner:** Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse an, die Microsoft direkt verwenden kann, wenn es Probleme mit Ihrem Angebot gibt. Diese Kontaktinformationen sind nicht im kommerziellen Marketplace aufgeführt.
  - **Unterstützende Dokumente** (optional): Sie können bis zu drei kundenorientierte Dokumente im PDF-Format bereitstellen – beispielsweise Whitepaper, Broschüren, Prüflisten oder PowerPoint-Präsentationen.
- **Medien**
    - **Logos:** Stellen Sie eine PNG-Datei für das **große** Logo bereit. Dieses wird in Partner Center verwendet, um die anderen erforderlichen Logogrößen zu erstellen. Sie können diese Logos später durch andere Bilder ersetzen.
    - **Screenshots:** Fügen Sie mindestens einen und bis zu fünf Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Bilder müssen 1280 × 720 Pixel im PNG-Format aufweisen und eine Beschriftung enthalten.
    - **Videos** (optional): Fügen Sie bis zu vier Videos hinzu, die Ihr Angebot vorstellen. Geben Sie einen Namen, eine URL für YouTube oder Vimeo sowie eine PNG-Miniaturansicht mit 1280 × 720 Pixel an.

> [!Note]
> Ihr Angebot muss für die Veröffentlichung im kommerziellen Marketplace die allgemeinen [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#100-general) erfüllen.

## <a name="additional-sales-opportunities"></a>Zusätzliche Verkaufschancen

Sie haben die Möglichkeit, sich für von Microsoft unterstützte Marketing- und Vertriebskanäle zu entscheiden. Wenn Sie Ihr Angebot im Partner Center erstellen, werden gegen Ende des Prozesses zwei Registerkarten angezeigt:

- **Co-Selling mit Microsoft:** Microsoft-Verkaufsteams können Ihre für IP-Co-Selling in Frage kommende Lösung in Betracht ziehen, wenn sie Kundenanforderungen evaluieren. Einzelheiten zur Berechtigung für das Co-Selling finden Sie unter [Requirements for Co-sell Status](/legal/marketplace/certification-policies) (Anforderungen für den Co-Selling-Status). Ausführliche Informationen dazu, wie Sie Ihr Angebot auf die Auswertung vorbereiten, finden Sie unter [Co-Selling-Option in Partner Center](./co-sell-configure.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Power BI-App-Angebots](power-bi-app-offer-setup.md)
