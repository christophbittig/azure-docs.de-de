---
title: Planen von Dynamics 365-Angeboten für Microsoft AppSource – Azure
description: Planen von Dynamics 365-Angeboten für Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 0d31c44a5daa21c16cd7c598f407501104dc53c6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546308"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Planen eines Microsoft Dynamics 365-Angebots

In diesem Artikel werden die verschiedenen Optionen und Features für Dynamics 365-Angebote in Microsoft AppSource im kommerziellen Marketplace erläutert. AppSource enthält Angebote, die auf Microsoft 365, Dynamics 365, Power Apps und Power BI aufbauen oder diese Anwendungen erweitern.

Erstellen Sie, bevor Sie beginnen, ein Konto für den kommerziellen Marketplace in [Partner Center](./create-account.md), und stellen Sie sicher, dass es für das kommerzielle Marketplace-Programm registriert ist. Lesen Sie außerdem die Informationen zum [Veröffentlichungsprozess und die Richtlinien](/office/dev/store/submit-to-appsource-via-partner-center).

## <a name="licensing-options"></a>Lizenzierungsoptionen

Wenn Sie die Veröffentlichung eines neuen Angebots vorbereiten, müssen Sie sich für eine Lizenzierungsoption entscheiden. Davon hängt ab, welche zusätzlichen Informationen Sie später bei der Erstellung eines Angebots in Partner Center bereitstellen müssen.

Für Dynamics 365-Angebotstypen sind die folgenden Lizenzierungsoptionen verfügbar:

| Angebotstyp | Auflistungsoption |
| --- | --- |
| Dynamics 365 for Operations | Kontakt mit mir aufnehmen |
| Dynamics 365 Customer Engagement & Power Apps | Jetzt anfordern<br>Jetzt abrufen (kostenlos)<br>Kostenlose Testversion (Listing)<br>Kontakt mit mir aufnehmen |
| Dynamics 365 Business Central | Jetzt abrufen (kostenlos)<br>Kostenlose Testversion (Listing)<br>Kontakt mit mir aufnehmen |
|||

In der folgenden Tabelle wird der Transaktionsprozess jeder Auflistungsoption beschrieben.

| Lizenzierungsoption | Transaktionsprozess |
| --- | --- |
| Kontakt mit mir aufnehmen | Sammeln Sie über Ihr CRM-System (Customer Relationship Management) Kundenkontaktinformationen. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Marketplace, auf dem der Kunde Ihr Angebot gefunden hat, werden an das CRM-System gesendet, das Sie konfiguriert haben. Weitere Informationen zum Konfigurieren Ihres CRM finden Sie im Abschnitt **Kundenleads** auf der Seite **Angebotseinrichtung** Ihres Angebotstyps. |
| Kostenlose Testversion (Listing) | Bieten Sie Ihren Kunden eine kostenlose ein-, drei- oder sechsmonatige Testversion an. Kostenlose Testversionen in der Angebotsliste werden von Ihrem Dienst erstellt, verwaltet und konfiguriert und weisen keine von Microsoft verwalteten Abonnements auf. |
| Jetzt abrufen (kostenlos) | Listen Sie Ihr Angebot für Kunden als kostenlos auf. |
| Jetzt anfordern | Sie können Ihre ISV app0-Lizenzen in Partner Center verwalten.<br>Derzeit nur für den folgenden Angebotstyp verfügbar:<ul><li>Dynamics 365 for Customer Engagement und Power Apps</li></ul><br>Weitere Informationen zu dieser Option finden Sie unter [Verwaltung von ISV-App-Lizenzen](isv-app-license.md). |
|||

## <a name="test-drive"></a>Testversion

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Kundenleads

Wenn Sie ein Angebot mithilfe von Partner Center im kommerziellen Marketplace veröffentlichen, sollten Sie es mit Ihrem CRM-System (Customer Relationship Management) verbinden. Dadurch empfangen Sie Kontaktinformationen zum Kunden, sobald jemand sein Interesse an Ihrem Produkt bekundet oder es verwendet. Das Herstellen einer Verbindung mit einem CRM-System ist erforderlich, wenn Sie eine Testversion aktivieren möchten, andernfalls ist dieser Schritt optional. Partner Center unterstützt Azure Table Storage, Dynamics 365 Customer Engagement, HTTPS-Endpunkte, Marketo und Salesforce.

## <a name="legal"></a>Rechtliche Hinweise

Erstellen Sie Ihre **Geschäftsbedingungen**. Kunden müssen diese Bedingungen akzeptieren, bevor sie Ihr Angebot testen können. Microsoft verfügt über einen Standardvertrag, der jedoch nicht für Dynamics 365-Angebote gilt.

## <a name="offer-listing-details"></a>Details zur Angebotsauflistung

Hier sehen Sie ein Beispiel dafür, wie Angebotsinformationen in Microsoft AppSource angezeigt werden (die aufgelisteten Preise dienen lediglich als Beispiele und stellen nicht die tatsächlichen Kosten dar):

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Veranschaulicht, wie dieses Angebot in Microsoft AppSource angezeigt wird.":::

> [!NOTE]
> Der Inhalt der Angebotsauflistung muss nicht in englischer Sprache verfasst werden, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt.

Um das Angebot einfacher zu gestalten, können Sie diese Elemente vorab vorbereiten. Sofern nicht anders angegeben sind alle erforderlich.

- **Name:** Dieser Name wird als Titel Ihres Angebots im kommerziellen Marketplace angezeigt. Der Name ist möglicherweise markenrechtlich geschützt. Er darf keine Emojis enthalten (außer diese sind Teil der Markenzeichen oder Copyrightsymbole) und maximal 50 Zeichen lang sein.
- **Zusammenfassung der Suchergebnisse:** Geben Sie den Zweck oder die Funktion des Angebots in einem Satz ohne Zeilenumbrüche und mit höchstens 100 Zeichen an. Diese Zusammenfassung wird in den Suchergebnissen verwendet, die aus Auflistungen im kommerziellen Marketplace erfasst werden.
- **Beschreibung:** Diese Beschreibung wird in der Übersicht der Auflistungen im kommerziellen Marketplace angezeigt. Sie können z. B ein Wertversprechen, wichtige Vorteile, Zielgruppe, Kategorie- oder Branchenzuordnungen, Möglichkeiten für In-App-Käufe, erforderliche Veröffentlichungen und einen Link zu weiteren Informationen eingeben. Dieses Textfeld enthält Rich-Text-Editor-Steuerelemente, mit denen Sie die Beschreibung ansprechender gestalten können. Verwenden Sie optional HTML-Tags für die Formatierung.
- **Suchbegriffe** (optional): Geben Sie bis zu drei Suchbegriffe an, mit denen Kunden nach Ihrem Angebot suchen können. Sie müssen den **Namen** und die **Beschreibung** des Angebots nicht einschließen, da diese automatisch in die Suche aufgenommen werden.
- **Produkte, mit denen Ihre App zusammenarbeitet** (optional): Geben Sie die Namen von bis zu drei Produkten an, mit denen Ihr Angebot zusammenarbeitet.
- **Links zur Hilfe und Datenschutzrichtlinie:** Geben Sie die URLs für die Hilfe zu Ihrem Angebot und zur Datenschutzrichtlinie Ihres Unternehmens an. Es liegt in Ihrer Verantwortung sicherzustellen, dass Ihre App den Gesetzen und Regelungen zum Datenschutz entspricht.
- **Kontaktinformationen**
  - **Supportkontakt:** Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse für Microsoft-Partner an. Diese werden dann von Kunden bei der Eröffnung von Supporttickets verwendet. Schließen Sie auch die URL für Ihre Supportwebsite ein.
  - **Technischer Ansprechpartner:** Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse an, die Microsoft direkt verwenden kann, wenn es Probleme mit Ihrem Angebot gibt. Diese Kontaktinformationen sind nicht im kommerziellen Marketplace aufgeführt.
- **Unterstützende Dokumente:** Sie können bis zu drei kundenorientierte Dokumente im PDF-Format bereitstellen, z. B. Whitepaper, Broschüren, Prüflisten oder PowerPoint-Präsentationen.
- **Medien**
    - **Logos:** Stellen Sie eine PNG-Datei für das **große** Logo bereit. Dieses wird in Partner Center verwendet, um die anderen erforderlichen Logogrößen zu erstellen. Sie können diese Logos später durch andere Bilder ersetzen.
    - **Screenshots:** Fügen Sie mindestens einen und bis zu fünf Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Bilder müssen 1280 × 720 Pixel im PNG-Format aufweisen und eine Beschriftung enthalten.
    - **Videos** (optional): Fügen Sie bis zu vier Videos hinzu, die Ihr Angebot vorstellen. Geben Sie einen Namen, eine URL für YouTube oder Vimeo sowie eine PNG-Miniaturansicht mit 1280 × 720 Pixel an.

> [!Note]
> Ihr Angebot muss für die Veröffentlichung im kommerziellen Marketplace die allgemeinen [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#100-general) erfüllen.

## <a name="additional-sales-opportunities"></a>Zusätzliche Verkaufschancen

Sie haben die Möglichkeit, sich für von Microsoft unterstützte Marketing- und Vertriebskanäle zu entscheiden. Wenn Sie Ihr Angebot in Partner Center erstellen, wird gegen Ende des Prozesses eine Registerkarte **Co-Selling mit Microsoft** angezeigt. Mit dieser Option können die Microsoft-Verkaufsteams Ihre für IP-Co-Selling in Frage kommende Lösung in Betracht ziehen, wenn sie Kundenanforderungen evaluieren. Ausführliche Informationen dazu, wie Sie Ihr Angebot auf die Evaluierung vorbereiten können, finden Sie unter [Co-Selling-Features im Partner Center](./co-sell-configure.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die oben beschriebenen Planungselemente berücksichtigt haben, wählen Sie eines der folgenden Elemente aus (auch im Inhaltsverzeichnis auf der linken Seite verfügbar), um mit der Erstellung Ihres Angebots zu beginnen.

| Leitfaden zur Veröffentlichung    | Notizen  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md) | Lesen Sie bei der Erstellung für die Enterprise Edition zunächst diese zusätzlichen Informationen zum [Veröffentlichungsprozess und den Richtlinien](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source). Zu den Produkttypen gehören Commerce, Finance, Human Resources, Project Operations und Supply Chain Management. |
| [Dynamics 365 Business Central](dynamics-365-business-central-offer-setup.md) | – |
| [Dynamics 365 for Customer Engagement und Power Apps](dynamics-365-customer-engage-offer-setup.md) | Sehen Sie sich zunächst diese zusätzlichen [Veröffentlichungsprozesse und Richtlinien](/dynamics365/customer-engagement/developer/publish-app-appsource) an. Zu den Produkttypen gehören: Customer Service, Customer Voice, Project Operations, Field Service, Marketing, Mixed Reality, Power Apps, Power Automate, Power Virtual Agents, Project Service Automation oder Sales. |
|
