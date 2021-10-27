---
title: Erstellen eines Angebots für verwaltete Dienste für den kommerziellen Marketplace
description: Planen Sie ein Angebot für verwaltete Dienste für Azure Marketplace in Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: 0f167cd82c6a66a175923b3b318623ab9a2bd1cf
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070848"
---
# <a name="plan-a-managed-service-offer"></a>Planen eines Angebots für einen verwalteten Dienst

In diesem Artikel werden die Anforderungen zum Veröffentlichen eines Angebots für verwaltete Dienste für den kommerziellen Marketplace über das Partner Center erläutert.

Verwaltete Dienste sind Azure Marketplace-Angebote, die mandantenübergreifende und mehrinstanzenfähige Verwaltung mit Azure Lighthouse ermöglichen. Weitere Informationen finden Sie unter [Was ist Azure Lighthouse?](../lighthouse/overview.md) Wenn ein Kunde ein Angebot für verwaltete Dienste erwirbt, kann er ein oder mehrere Abonnements oder Ressourcengruppen delegieren. Anschließend können Sie mithilfe der Funktionen für die [delegierte Azure-Ressourcenverwaltung](../lighthouse/concepts/architecture.md) von Azure Lighthouse mit diesen Ressourcen arbeiten.

## <a name="eligibility-requirements"></a>Qualifizierungsanforderungen

Zum Veröffentlichen eines Angebots für einen verwalteten Dienst müssen Sie über eine Gold- oder Silver-Kompetenz von Microsoft in der Kategorie „Cloudplattform“ verfügen. Diese Kompetenz belegt Ihr Fachwissen gegenüber Kunden. Weitere Informationen finden Sie unter [Kompetenzen für das Microsoft Partner Network](https://partner.microsoft.com/membership/competencies).

Für die Veröffentlichung im Azure Marketplace müssen Angebote allen gültigen [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies) entsprechen.

## <a name="customer-leads"></a>Kundenleads

Um Kundeninformationen zu sammeln, müssen Sie Ihr Angebot mit Ihrem CRM-System (Customer Relationship Management) verbinden. Der Kunde wird gebeten, die Berechtigung zur Freigabe seiner Informationen zu erteilen. Diese Kundeninformationen sowie der Angebotsname, die Angebots-ID und der Onlineshop, auf dem der Kunde Ihr Angebot gefunden hat, werden an das von Ihnen konfigurierte CRM-System gesendet. Der kommerzielle Marketplace unterstützt eine Vielzahl von CRM-Systemen. Außerdem können Sie optional eine Azure-Tabelle verwenden oder einen HTTPS-Endpunkt mithilfe von Power Automate konfigurieren.

Eine CRM-Verbindung kann während der Angebotserstellung zu einem beliebigen Zeitpunkt hinzugefügt oder geändert werden. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Verträge

Auf der Seite „Eigenschaften“ von Partner Center werden Sie aufgefordert, **Geschäftsbedingungen** für die Verwendung Ihres Angebots bereitzustellen. Sie können Ihre Bedingungen entweder direkt in Partner Center eingeben oder die URL bereitstellen, unter der sie zu finden sind. Kunden müssen diese Geschäftsbedingungen akzeptieren, bevor sie Ihr Angebot erwerben.

## <a name="offer-listing-details"></a>Details zur Angebotsauflistung

Wenn Sie ein Angebot für verwaltete Dienste in Partner Center erstellen, fügen Sie Text, Bilder, Dokumente und andere Angebotsdetails hinzu. Dies sind die Informationen, die Kunden angezeigt werden, wenn sie Ihr Angebot im Azure Marketplace entdecken. Sehen Sie sich folgendes Beispiel an:

:::image type="content" source="media/example-managed-service.png" alt-text="Veranschaulicht, wie ein Angebot für verwaltete Dienste in Azure Marketplace angezeigt wird.":::

**Beschreibungen zu den Nummern**

1. Logo
1. Name
1. Kurze Beschreibung
1. Kategorien
1. Verträge und Datenschutzrichtlinie
1. BESCHREIBUNG
1. Screenshots/Videos
1. Nützliche Links

Hier sehen Sie ein Beispiel dafür, wie die Angebotsauflistung im Azure-Portal angezeigt wird:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Veranschaulicht, wie dieses Angebot im Azure-Portal angezeigt wird.":::

**Beschreibungen zu den Nummern**

1. Name
2. BESCHREIBUNG
3. Nützliche Links
4. Screenshots/Videos

> [!NOTE]
> Wenn Sie Ihr Angebot nicht in englischer Sprache anbieten, kann die Angebotsauflistung auch in einer anderen Sprache verfasst werden. Die Beschreibung muss jedoch mit dem englischen Satz „This service is available in &lt;Sprache Ihres Angebotsinhalts>” (Dieser Dienst ist in <Sprache Ihres Angebotsinhalts> verfügbar) beginnen oder enden. Sie können auch zusätzliche Dokumente in einer anderen Sprache als der für die Details der Angebotsliste verwendeten beifügen.

Um das Angebot einfacher zu gestalten, können Sie einige dieser Elemente vorab vorbereiten. Die folgenden Elemente sind erforderlich, sofern nicht anders angegeben:

**Name**: Dieser Name wird als Titel Ihrer Angebotsauflistung im kommerziellen Marketplace angezeigt. Der Name ist möglicherweise markenrechtlich geschützt. Er darf keine Emojis (mit Ausnahme des Markenzeichen- und Copyrightsymbols) enthalten und ist auf 50 Zeichen begrenzt.

**Zusammenfassung der Suchergebnisse**: Beschreiben Sie hier den Zweck oder das Ziel Ihres Angebots mit höchstens 100 Zeichen. Diese Zusammenfassung wird in den Suchergebnissen verwendet, die aus den Angeboten im kommerziellen Marketplace erfasst werden. Sie sollte nicht mit dem Titel identisch sein. Fügen Sie ggf. Ihre wichtigsten SEO-Schlüsselwörter ein.

**Kurze Beschreibung**: Geben Sie eine kurze Beschreibung Ihres Angebots ein (bis zu 256 Zeichen). Sie wird in der Angebotsliste im Azure-Portal angezeigt.

**Beschreibung**: Beschreiben Sie Ihr Angebot in maximal 3.000 Zeichen. Diese Beschreibung wird im Angebot im kommerziellen Marketplace angezeigt. Fügen Sie gegebenenfalls auch ein Nutzenversprechen, die wichtigsten Vorteile, die Zusammenhänge mit Kategorien oder Branchen und etwaige Offenlegungen hinzu.

Hier einige Tipps zum Verfassen Ihrer Beschreibung:

* Schildern Sie in den ersten Sätzen deutlich den Nutzen Ihres Angebots, und geben Sie u. a. Folgendes an:
    * Typ von Benutzern, die von dem Angebot profitieren
    * Kundenbedürfnisse oder -probleme, die das Angebot anspricht.
* Beachten Sie, dass die ersten Sätze möglicherweise in den Suchergebnissen angezeigt werden.
* Verwenden Sie branchenspezifisches Vokabular.

Die Beschreibung kann mithilfe von HTML-Tags formatiert werden. Weitere Informationen zur HTML-Formatierung finden Sie unter [In Angebotsbeschreibungen des kommerziellen Marketplace unterstützte HTML-Tags](./supported-html-tags.md).

**Link zur Datenschutzrichtlinie**: Geben Sie eine URL zu der Datenschutzrichtlinie an, die auf Ihrer Site gehostet wird. Sie müssen sicherstellen, dass Ihr Angebot die Datenschutzgesetze und -bestimmungen erfüllt, und Sie müssen eine gültige Datenschutzrichtlinie bereitstellen.

**Nützliche Links** (optional): Laden Sie ergänzende Onlinedokumente zu Ihrem Angebot hoch.

**Kontaktinformationen**: Geben Sie den Namen, die E-Mail-Adresse und die Telefonnummer von zwei Personen in Ihrem Unternehmen ein: eine Kontaktperson für Support und eine Kontaktperson für technische Fragen (Eine dieser Personen können Sie selbst sein.). Wir werden über diese Informationen bezüglich Ihres Angebots mit Ihnen kommunizieren. Die Angaben sind für Kunden nicht sichtbar, werden aber unter Umständen an Cloudlösungsanbieter (Cloud Solution Provider, CSP) weitergegeben.

**Support-URLs** (optional): Wenn Sie Supportwebsites für Azure Global-Kunden und/oder Azure Government-Kunden anbieten, geben Sie deren URLs hier an.

**Marketplace-Medien – Logos**: Stellen Sie eine PNG-Datei für das große Logo Ihres Angebots bereit. Hiermit werden in Partner Center mittelgroße und kleine Logos erstellt. Sie können diese Logos später durch ein anderes Bild ersetzen.

* Das große Logo (zwischen 216 x 216 und 350 x 350 Pixel) wird in Ihrem Angebotseintrag im Azure Marketplace angezeigt.
* Das mittlere Logo (90 x 90 Pixel) wird angezeigt, wenn eine neue Ressource erstellt wird.
* In den Azure Marketplace-Suchergebnissen wird das kleine Logo (48 x 48 Pixel) verwendet.

Befolgen Sie die folgenden Richtlinien für Ihre Logos:

* Stellen Sie sicher, dass das Bild nicht verzerrt wird.
* Die Farbpalette des Azure-Designs ist einfach und geradlinig. Beschränken Sie die Anzahl der Primär- und Sekundärfarben auf Ihrem Logo.
* Die Farben des Azure-Portals sind Weiß und Schwarz. Verwenden Sie diese nicht als Hintergrund für Ihr Logo. Wir empfehlen einfache Primärfarben, die Ihr Logo hervorheben.
* Wenn Sie einen transparenten Hintergrund verwenden, stellen Sie sicher, dass das Logo und der Text nicht weiß, schwarz oder blau sind.
* Ihr Logo sollte klar aussehen. Vermeiden Sie Farbverläufe. Platzieren Sie keinen Text auf dem Logo, auch nicht Ihren Firmen- oder Markennamen.

**Marketplace-Medien – Screenshots** (optional): Fügen Sie bis zu fünf Bilder hinzu, die zeigen, wie Ihr Angebot funktioniert. Alle Bilder müssen 1280 x 720 Pixel groß sein und im PNG-Format vorliegen.

**Marketplace-Medien – Videos** (optional): Laden Sie bis zu fünf Videos hoch, die Ihr Angebot veranschaulichen. Die Videos müssen auf YouTube oder Vimeo gehostet werden und eine Vorschauminiatur aufweisen (1280 × 720, PNG-Datei).

## <a name="preview-audience"></a>Vorschauzielgruppe

Eine Vorschauzielgruppe kann auf das Angebot zugreifen, bevor es im Azure Marketplace live veröffentlicht wird, um es zu testen. Auf der Seite **Vorschauzielgruppe** im Partner Center können Sie eine eingeschränkte Vorschauzielgruppe definieren.

> [!NOTE]
> Eine Vorschauzielgruppe unterscheidet sich von einem privaten Plan. Ein privater Plan ist ein Angebot, das nur einem bestimmten Publikum zur Verfügung steht. Darüber hinaus können Sie einen benutzerdefinierten Plan mit bestimmten Kunden aushandeln.

Sie können Einladungen an E-Mail-Adressen mit Microsoft-Konto (MSA) oder in Azure Active Directory (Azure AD) senden. Fügen Sie bis zu 10 E-Mail-Adressen manuell hinzu, oder importieren Sie über eine CSV-Datei bis zu 20 E-Mail-Adressen. Wenn Ihr Angebot bereits live geschaltet ist, können Sie dennoch eine Vorschauzielgruppe für das Testen von Änderungen oder für Aktualisierungen Ihres Angebots definieren.

## <a name="plans-and-pricing"></a>Pläne und Preise

Für Angebote verwalteter Dienste ist mindestens ein Plan erforderlich. Ein Plan definiert den Umfang einer Lösung, die Einschränkungen sowie ggf. die zugehörigen Preise. Sie können mehrere Pläne für Ihr Angebot erstellen, um Ihren Kunden verschiedene technische und preisliche Optionen zu bieten. Eine allgemeine Anleitung zu Plänen einschließlich privater Pläne finden Sie unter [Pläne und Preise für Angebote im kommerziellen Marketplace](plans-pricing.md).

Verwaltete Dienste unterstützen nur ein Preismodell: **BYOL** (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz). Dies bedeutet, dass Sie mit Ihren Kunden direkt abrechnen, und Microsoft berechnet Ihnen keine Gebühren.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Angebots für einen verwalteten Dienst](./create-managed-service-offer.md)
* [Bewährte Methoden für Angebotslistung](./gtm-offer-listing-best-practices.md)