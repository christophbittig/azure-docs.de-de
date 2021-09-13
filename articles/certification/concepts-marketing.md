---
title: Marketingeigenschaften
description: Eine Beschreibung der verschiedenen Marketingfelder, die im Portal gesammelt werden, und wie diese im Azure Certified Device-Katalog angezeigt werden.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 06/22/2021
ms.custom: template-concept
ms.openlocfilehash: 7786cd64ebf46038cc4d41f13ef76bb4d543752f
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970119"
---
# <a name="marketing-properties"></a>Marketingeigenschaften

Im Rahmen des [Hinzufügens Ihrer Gerätedetails](tutorial-02-adding-device-details.md) müssen Sie Marketinginformationen angeben, die im [Azure Certified Device-Katalog](https://devicecatalog.azure.com) angezeigt werden. Diese Informationen werden während des Einreichungsvorgangs der Zertifizierung im Azure Certified Device-Portal gesammelt und im Katalog als Filterparameter verwendet. Dieser Artikel bietet eine Zuordnung zwischen den im Portal gesammelten Feldern und ihrer Darstellung im Katalog. Nach der Lektüre dieses Artikels sollten die Partner besser verstehen, welche Informationen sie während des Zertifizierungsprozesses bereitstellen müssen, um ihr Produkt im Katalog optimal zu repräsentieren.

![PDP-Übersicht](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Azure Certified Device-Katalog: Produktkachel

Besucher des Katalogs interagieren mit Ihrem Gerät zuerst in Form einer Katalogproduktkachel auf der Suchseite. Dadurch erhalten sie eine grundlegende Übersicht über das Gerät und die Zertifizierungen, die es erhalten hat.

![Produktkachelvorlage](./media/concepts-marketing/product-tile.png)

| Feld | BESCHREIBUNG                  | Einfügeposition im Portal                |
|---------------|-------------------------|----------------------------------|
| Gerätename | Öffentlicher Name Ihres zertifizierten Geräts         | Registerkarte "Grundlagen" der Gerätedetails|
| Unternehmensname| Öffentlicher Name Ihres Unternehmens  | Im Portal nicht bearbeitbar. Aus dem MPN-Kontonamen extrahiert |
| Produktfoto  | Bild Ihres Geräts mit einer Minimalauflösung von 200 p x 200  p  | Marketingdetails |
| Zertifizierungsklassifizierung  | Obligatorische Azure Certified Device-Zertifizierungsbezeichnung und optionale Zertifizierungs-Badges  | Registerkarte "Grundlagen" der Gerätedetails. Die entsprechenden Tests im Abschnitt „Verbinden und testen“ müssen bestanden werden. |

## <a name="product-description-page-information"></a>Informationen zur Produktbeschreibungsseite

Sobald ein Kunde auf der Suchseite des Katalogs auf Ihre Gerätekachel geklickt hat, wird er zur Produktbeschreibungsseite Ihres Geräts navigiert. Dies ist die Stelle, an der der größte Teil der während des Zertifizierungsprozesses bereitgestellten Informationen zu finden ist.

Im oberen Bereich der Produktbeschreibungsseite werden die wichtigsten Merkmale hervorgehoben, von denen einige bereits für die Produktkachel verwendet wurden.

![PDP – obere Leiste](./media/concepts-marketing/pdp-top.png)

| Feld | BESCHREIBUNG                  | Einfügeposition im Portal                |
|---------------|-------------------------|----------------------------------|
| Geräteklasse | Klassifizierung von Formfaktor und Hauptzweck Ihres Geräts ([Weitere Informationen](./resources-glossary.md))       | Registerkarte "Grundlagen" der Gerätedetails|
| Gerätetyp | Klassifizierung des Geräts ausgehend von der Implementierungsbereitschaft ([Weitere Informationen](./resources-glossary.md)) | Registerkarte "Grundlagen" der Gerätedetails |
| Geografische Verfügbarkeit | Regionen, in denen Ihr Gerät zum Kauf angeboten wird  | Marketingdetails |
| Betriebssysteme  | Betriebssysteme, die von Ihrem Gerät unterstützt werden  | Registerkarte „Produktdetails“ der Gerätedetails |
| Zielbranchen  | Die 3 wichtigsten Branchen, für die Ihr Gerät optimiert ist  | Marketingdetails |
| Produktbeschreibung  | Freies Textfeld, in das Sie die Marketingbeschreibung Ihres Produkts schreiben können. Hier können Details angegeben werden, die nicht im Portal aufgeführt sind, oder zusätzlicher Kontext für den Nutzen der Verwendung Ihres Geräts hinzugefügt werden. | Marketingdetails|

Der Rest der Seite konzentriert sich auf die Darstellung der technischen Spezifikationen Ihres Geräts in Form einer Tabelle, die Ihrem Kunden hilft, Ihr Produkt besser zu verstehen. Der Einfachheit halber werden die oben auf der Seite angezeigten Informationen auch hier aufgeführt und durch zusätzliche Geräteinformationen ergänzt. Der Rest der Tabelle ist von den im Portal angegebenen Komponenten getrennt.

![PDP – Seite unten](./media/concepts-marketing/pdp-bottom.png)

| Feld | BESCHREIBUNG                  | Einfügeposition im Portal                |
|---------------|-------------------------|----------------------------------|
| Umgebungszertifizierungen | Offizielle Zertifizierungen, die für die Leistung in verschiedenen Umgebungen vergeben werden     | Details zur Gerätehardware  |
| Betriebsbedingungen | Eingangsschutzwert oder Temperaturbereiche, für die sich das Gerät eignet     | Details zur Gerätesoftware  |
| Azure-Softwareeinrichtung | Klassifizierung des Einrichtungsprozesses zum Herstellen einer Verbindung zwischen dem Gerät und Azure ([Weitere Informationen](./how-to-software-levels.md))     | Details zur Gerätesoftware  |
| Komponententyp | Klassifizierung von Formfaktor und Hauptzweck Ihres Geräts ([Weitere Informationen](./resources-glossary.md))       | Details zur Gerätehardware|
| Komponentenname| Der Name der Komponente, die Sie beschreiben. | Produktdetails in den Gerätedetails |
| Zusätzliche Komponenteninformationen | Zusätzliche Hardwarespezifikationen, wie z. B. enthaltene Sensoren, Konnektivität, Beschleuniger usw.  | Zusätzliche Komponenteninformationen in den Gerätedetails ([Weitere Informationen](./how-to-using-the-components-feature.md))  |
| Text zur Geräteabhängigkeit | Vom Partner bereitgestellter Text, in dem die verschiedenen Abhängigkeiten beschrieben werden, die das Produkt für die Herstellung von Verbindungen mit Azure benötigt ([Weitere Informationen](./how-to-indirectly-connected-devices.md))   | Für den Kunden sichtbarer Kommentarabschnitt der Registerkarte „Abhängigkeiten“ in den Gerätedetails |
| Link zur Geräteabhängigkeit  | Link zu einem zertifizierten Gerät, das für Ihr aktuelles Produkt erforderlich ist  | Registerkarte "Abhängigkeiten" in den Gerätedetails |

## <a name="shop-links"></a>Links zum Shop
Sowohl auf der Seite mit der Produktkachel als auch auf der Seite mit der Produktbeschreibung ist eine Schaltfläche zum Shop verfügbar. Wenn der Kunde auf sie klickt, wird ein Fenster geöffnet, das ihm die Wahl eines Vertriebspartners ermöglicht (Sie dürfen bis zu 5 Vertriebspartner auflisten). Nach der Auswahl wird der Kunde an die vom Partner angegebene URL weitergeleitet.

![Abbildung einer Shop-Popupoberfläche](./media/concepts-marketing/shop.png)

| Feld | BESCHREIBUNG                  | Einfügeposition im Portal                |
|---------------|-------------------------|----------------------------------|
| Verteilername | Der Name des Vertriebspartners, der Ihr Produkt verkauft. | Marketingdetails|
| Get Device| Link zu einer externen Website, auf der der Kunde das Gerät erwerben (oder ein Angebot vom Vertriebspartner anfordern) kann. Dies kann die gleiche Seite wie die des Herstellers sein, wenn der Händler mit dem Gerätehersteller identisch ist. Wenn keine Kaufseite verfügbar ist, bewirkt dies eine Weiterleitung an die Seite des Vertriebspartners, damit der Kunde sich direkt an ihn wenden kann.  | URL der Produktseite des Vertriebspartners in den Marketingdetails. Wenn keine Kaufseite verfügbar ist, ist der Standardwert für den Link die URL des Vertriebspartners in den Marketingdetails. |

## <a name="external-links"></a>Externe Links

Außerdem befinden sich auf der Produktbeschreibungsseite Links, die zu von Partnern bereitgestellten Seiten oder Dateien führen, die dem Kunden helfen, das Produkt besser zu verstehen. Sie werden im oberen Bereich der Seite unterhalb des Texts zur Produktbeschreibung angezeigt. Die angezeigten Links unterscheiden sich für verschiedene Gerätetypen und Zertifizierungsprogramme.

| Link | BESCHREIBUNG                  | Einfügeposition im Portal                |
|---------------|-------------------------|----------------------------------|
| Leitfaden „Erste Schritte“* | PDF-Datei mit Benutzeranweisungen zum Verbinden und Verwenden Ihres Geräts mit Azure-Diensten | Abschnitt Leitfaden „Erste Schritte“ im Portal hinzufügen|
| Herstellerseite*|Link zur Seite des Herstellers Bei dieser Seite kann es sich um die spezifische Produktseite für Ihr Gerät oder um die Startseite des Unternehmens handeln, wenn keine Marketingseite zur Verfügung steht. | Marketingseite des Herstellers in Marketingdetails |
| Gerätemodell | Öffentliche DTDL-Modelle für IoT Plug & Play-Lösungen  | Im Portal nicht bearbeitbar. Das Gerätemodell muss in das ([öffentliche Modellrepository](https://aka.ms/modelrepo)) hochgeladen werden.  |
| Gerätequellcode | URL zum Gerätequellcode für Dev Kit-Gerätetypen| Registerkarte "Grundlagen" der Gerätedetails  |

 **Für alle veröffentlichten Geräte erforderlich*

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun verstehen, wie wir die Informationen verwenden, die Sie im Rahmen der Zertifizierung angeben, können Sie Ihr Gerät zertifizieren. Beginnen Sie Ihr Zertifizierungsprojekt, oder springen Sie zurück zur Phase der Gerätedetails, um Ihre eigenen Marketinginformationen hinzuzufügen.

- [Ihren Zertifizierungsweg beginnen](./tutorial-00-selecting-your-certification.md)
- [Gerätedetails hinzufügen](./tutorial-02-adding-device-details.md)
