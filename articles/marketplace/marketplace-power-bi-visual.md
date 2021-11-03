---
title: Planen eines Angebots für Power BI-Visuals in Partner Center für Microsoft AppSource
description: Erfahren Sie, welche Informationen Sie benötigen, um Ihr Angebot für Power BI-Visuals in Partner Center zu übermitteln.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: 898fcfd97e6c73b530717a99d82f27e0ed3cfe53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082377"
---
# <a name="plan-a-power-bi-visual-offer"></a>Planen eines Angebots für Power BI-Visuals

In diesem Artikel werden die Inhalte und Anforderungen behandelt, die zum Veröffentlichen eines Angebots für Power BI-Visuals in Partner Center für Microsoft [AppSource](https://appsource.microsoft.com) erforderlich sind. Bei Power BI-Visuals handelt es sich um Pakete, mit denen Benutzer die Daten, die ihnen bereitgestellt werden, visualisieren können. Jeder Benutzer kann ein benutzerdefiniertes Visual erstellen und dieses in einer einzigen PBIVIZ-Datei packen, die in einen Power BI-Bericht importiert werden kann.

Erstellen Sie, bevor Sie beginnen, ein [Konto im kommerziellen Marketplace](./create-account.md) in Partner Center, und stellen Sie sicher, dass es für das kommerzielle Marketplace-Programm registriert ist. Lesen Sie außerdem die Informationen zum [Veröffentlichungsprozess](/office/dev/store/submit-to-appsource-via-partner-center) und die [Richtlinien](/legal/marketplace/rating-review-policies).

## <a name="publishing-benefits"></a>Vorteile der Veröffentlichung

Vorteile der Veröffentlichung in AppSource:

- Bewerben Sie Ihr Unternehmen, indem Sie die Marke Microsoft nutzen.
- In AppSource können Sie mehr als 100 Millionen Microsoft 365- und Dynamics 365-Benutzer erreichen und im Azure Marketplace über 200.000 Organisationen.
- Lassen Sie Ihre Dienste durch die Außendienst- und Telefonmarketingteams von Microsoft bewerben.

## <a name="overview"></a>Übersicht

Lesen Sie die folgenden Informationen, um sicherzustellen, dass Sie optimal vorbereitet sind, bevor Sie mit dem Erstellungsprozess beginnen.

In den nächsten Themen werden die folgenden zentralen Veröffentlichungsschritte behandelt:

1. [Packen](/power-bi/developer/visuals/package-visual) Sie Ihr Power BI-Visual.
2. Bereiten Sie die [Marketingdetails](./gtm-offer-listing-best-practices.md#online-store-offer-details) vor, z. B. den offiziellen Namen, eine Beschreibung und Logos.
3. Sammeln Sie Links zu den rechtlichen und unterstützenden Dokumenten für das Angebot, z. B. Nutzungsbedingungen, Datenschutzrichtlinie, Unterstützungsrichtlinie und Benutzerhilfe.
4. Erstellen Sie das Visual. Verwenden Sie Partner Center zum Eingeben der Details, darunter die Beschreibung des Visuals, Marketingmaterial, rechtliche Hinweise und Supportinformationen sowie Ressourcenspezifikationen.
5. Überwachen Sie den Fortschritt in Partner Center, wo das AppSource-Onboardingteam Ihr Visual testet, überprüft und zertifiziert. Nach der Zertifizierung sollten Sie die App in der Testumgebung überprüfen und sie dann freigeben. Durch diese „Liveschaltung“ wird es in AppSource aufgelistet.

## <a name="technical-requirements"></a>Technische Anforderungen

Die technischen Anforderungen zum Abrufen eines Angebots für Power BI-Visuals werden unter [Technische Konfiguration eines Angebots für Power BI-Visuals](power-bi-visual-technical-configuration.md) ausführlich beschrieben.

Bevor Sie ein Power BI-Visual an AppSource übermitteln, stellen Sie sicher, dass Sie die [Richtlinien](/power-bi/developer/visuals/guidelines-powerbi-visuals) für Power BI-Visuals gelesen und Ihr Visual [getestet](/power-bi/developer/visuals/submission-testing) haben.

## <a name="legal-contracts"></a>Verträge

Stellen Sie eine Datei mit dem **Endbenutzer-Lizenzvertrag (EULA)** für Ihr Power BI-Visual bereit.

## <a name="offer-listing-details"></a>Details zur Angebotsauflistung

> [!NOTE]
> Der Inhalt der Angebotsauflistung muss nicht in englischer Sprache verfasst werden, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt.

Um das Angebot einfacher zu gestalten, können Sie diese Elemente vorab vorbereiten. Sofern nicht anders angegeben sind alle erforderlich.

Das effektivste Angebot für Power BI-Visuals finden Sie in diesen [Richtlinien](./power-bi-visual-offer-listing.md).

- **Name**: Der [Name](/office/dev/store/reserve-solution-name) wird als Titel Ihrer Angebotsauflistung im kommerziellen Marketplace angezeigt. Der Name ist möglicherweise markenrechtlich geschützt. Er darf keine Emojis enthalten (außer diese sind Teil der Markenzeichen oder Copyrightsymbole) und maximal 50 Zeichen lang sein.
- **Zusammenfassung**: Hier sind Zweck oder Funktion des Angebots in einem einzigen Satz ohne Zeilenumbrüche und mit höchstens 100 Zeichen angegeben. Diese Zusammenfassung wird in den Suchergebnissen verwendet, die aus Auflistungen im kommerziellen Marketplace erfasst werden.
- **Beschreibung**: Diese wird in der Übersicht der Auflistungen im kommerziellen Marketplace angezeigt. Sie können z. B ein Wertversprechen, wichtige Vorteile, die Zielgruppe, Kategorie- oder Branchenzuordnungen, Möglichkeiten für [In-App-Käufe](./power-bi-visual-offer-setup.md), erforderliche Veröffentlichungen und einen Link zu weiteren Informationen eingeben. Dieses Textfeld enthält Rich-Text-Editor-Steuerelemente, mit denen Sie die Beschreibung ansprechender gestalten können. Verwenden Sie optional HTML-Tags für die Formatierung.
- **Links zur Hilfe und Datenschutzrichtlinie:** Geben Sie die URL für die Hilfe und die Datenschutzrichtlinie Ihres Unternehmens an. Es liegt in Ihrer Verantwortung sicherzustellen, dass Ihr Angebot den Gesetzen und Regelungen zum Datenschutz entspricht.
- **Medien**: 
    - **Logos:** Stellen Sie eine PNG-Datei für das **große** Logo bereit. Dieses wird in Partner Center verwendet, um die anderen erforderlichen Logogrößen zu erstellen. Sie können diese Logos später durch andere Bilder ersetzen.
    - **Screenshots:** Fügen Sie mindestens einen und bis zu fünf Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Bilder müssen 1280 × 720 Pixel im PNG-Format aufweisen und eine Beschriftung enthalten.
    - **Videos** (optional): Fügen Sie bis zu vier Videos hinzu, die Ihr Angebot vorstellen. Geben Sie einen Namen, eine URL für YouTube oder Vimeo sowie eine PNG-Miniaturansicht mit 1280 × 720 Pixel an.

>[!NOTE]
> Lesen Sie die [Richtlinien](./marketplace-criteria-content-validation.md) und [bewährten Methoden](./gtm-offer-listing-best-practices.md) für die Angebotsauflistung sorgfältig durch, bevor Sie Ihr Angebot übermitteln.
>
> Ihr Angebot muss für die Veröffentlichung im kommerziellen Marketplace den allgemeinen [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#100-general) entsprechen.

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte zum [Erstellen eines Angebots für ein Power BI-Visual](power-bi-visual-offer-setup.md)