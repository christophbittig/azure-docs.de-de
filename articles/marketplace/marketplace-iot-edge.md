---
title: IoT Edge-Modulangebote in Azure Marketplace
description: Erfahren Sie, wie Sie ein IoT Edge-Modulangebot im Azure Marketplace veröffentlichen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/30/2021
ms.openlocfilehash: 3ab11ffb7779255a5b45a55d0098657834203d80
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072972"
---
# <a name="plan-an-iot-edge-module-offer"></a>Planen eines IoT Edge-Modulangebots

Die [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/)-Plattform wird von Microsoft Azure unterstützt. Diese Plattform ermöglicht es Benutzern, Cloud-Workloads zum direkten Ausführen auf IoT-Geräten bereitzustellen.  Mit einem IoT Edge-Modul können Workloads offline ausgeführt und Datenanalysen lokal durchgeführt werden. Mit diesem Angebot können Sie Bandbreite sparen sowie lokale und vertrauliche Daten schützen. Zudem bietet es Antwortzeiten mit geringer Latenz.  Von diesen vorgefertigten Workloads können Sie ab sofort profitieren. Bislang sind nur wenige Erstanbieterlösungen von Microsoft verfügbar. Und so mussten Sie bisher Zeit und Ressourcen in die Erstellung eigener benutzerdefinierter IoT-Lösungen investieren.

Mit den IoT-Edge-Modulen im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1) haben wir jetzt eine zentrale Anlaufstelle für Anbieter, um ihre Lösungen dem IoT-Publikum vorzustellen und zu verkaufen. Letztlich können IoT-Entwickler hier Funktionen zur Beschleunigung ihrer Lösungsentwicklung finden und erwerben.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Die wichtigsten Vorteile von IoT Edge-Modulen im Azure Marketplace

| **Für Herausgeber**    | **Für Kunden (IoT-Entwickler)**  |
| :------------------- | :-------------------|
| Sie erreichen Millionen von Entwicklern, die IoT Edge-Lösungen erstellen und bereitstellen möchten.  | Erstellen Sie eine IoT Edge-Lösung mit der Gewissheit, sichere und getestete Komponenten zu verwenden. |
| Sie müssen Ihre Lösung nur einmal veröffentlichen. Diese kann dann auf jeder IoT Edge-Hardware ausgeführt werden, die Container unterstützt. | Verkürzen Sie die Markteinführungszeit, indem Sie hier nach IoT Edge-Modulen von Erst- und Drittanbietern für spezifische Anforderungen suchen und diese bereitstellen. |
| Profitieren Sie von flexiblen Abrechnungsoptionen. <ul><li>Kostenlos und BYOL (Bring Your Own License)</li></ul> | Kaufen Sie mit den Abrechnungsmodellen Ihrer Wahl.<ul><li>Kostenlos und BYOL (Bring Your Own License)</li></ul> |

## <a name="what-is-an-iot-edge-module"></a>Was ist ein IoT Edge-Modul?

Azure IoT Edge ermöglicht Ihnen die Bereitstellung und Verwaltung von Geschäftslogik im Edge in Form von Modulen. Azure IoT Edge-Module sind die kleinste von IoT Edge verwaltete Recheneinheit und können Microsoft-Dienste (wie Azure Stream Analytics), Dienste von Drittanbietern oder Ihren eigenen lösungsspezifischen Code enthalten. Weitere Informationen zu IoT Edge-Modulen finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](../iot-edge/iot-edge-modules.md).

### <a name="what-is-the-difference-between-a-container-offer-type-and-an-iot-edge-module-offer-type"></a>Was ist der Unterschied zwischen Container-Angebotstyp und einem IoT Edge-Modul-Angebotstyp?

Beim IoT Edge-Modul-Angebotstyp handelt es sich um einen speziellen Containertyp, der auf einem IoT Edge-Gerät ausgeführt wird. Er wird mit Standardkonfigurationseinstellungen zur Ausführung im IoT Edge-Kontext ausgeliefert und verwendet optional das IoT Edge-Modul SDK zur Integration in die IoT Edge-Laufzeit.

## <a name="select-the-right-online-store"></a>Auswählen des passenden Onlineshops

IoT-Edge-Module werden nur im Azure Marketplace veröffentlicht; AppSource ist nicht anwendbar. Weitere Informationen zu den Unterschieden der jeweiligen Onlineshops finden Sie unter [Bestimmung Ihrer Veröffentlichungsoption](determine-your-listing-type.md).

## <a name="technical-requirements"></a>Technische Anforderungen

Die technischen Anforderungen, um ein IoT-Edge-Modul zu zertifizieren und im Azure Marketplace zu veröffentlichen, sind in der [ Bereiten Sie Ihr IoT-Edge-Modul technisch vor](iot-edge-technical-asset.md).

## <a name="eligibility-prerequisites"></a>Voraussetzungen für die Berechtigung

Für IoT Edge-Modul-Angebote gelten die Bestimmungen der Microsoft Azure Marketplace-Vereinbarungen und -Richtlinien.  Darüber hinaus müssen für IoT Edge-Module zusätzliche Voraussetzungen und technische Anforderungen erfüllt sein.  

Um ein IoT Edge-Modul im Azure Marketplace zu veröffentlichen, müssen Sie die folgenden Voraussetzungen erfüllen:

- Sie benötigen Zugriff auf Partner Center. Weitere Informationen finden Sie unter [Erstellen eines Kontos im kommerziellen Marketplace in Partner Center](create-account.md).
- Sie müssen Ihr IoT Edge-Modul in einer Azure Container Registry hosten.
- Sie müssen unter anderem folgende Metadaten Ihres IoT Edge-Moduls bereithalten:
    - Titel
    - Beschreibung (im HTML-Format)
    - Logobild (in den Größen 48 × 48 (optional), 90 × 90 (optional) und von 216 × 216 bis 350 × 350 Pixel, alle im PNG-Format)
    - Nutzungsbedingungen und Datenschutzrichtlinie
    - Standardmodulkonfiguration (Route, gewünschte Eigenschaften von Modulzwillingen, createOptions, Umgebungsvariable)
    - Dokumentation
    - Supportkontakte

## <a name="licensing-options"></a>Lizenzierungsoptionen

Für Azure-Containerangebote sind die folgenden Lizenzierungsoptionen verfügbar:

| Lizenzierungsoption | Transaktionsprozess |
| --- | --- |
| Kostenlos | Listen Sie Ihr Angebot für Kunden als kostenlos auf. |
| BYOL | Die Option „Bring Your Own License“ ermöglicht es Ihren Kunden, bereits vorhandene Softwarelizenzen in Azure zu übernehmen.\* |
|

\* Als Herausgeber sind Sie für alle Aspekte der Softwarelizenztransaktion verantwortlich, einschließlich (unter anderem) Auftrag, Auftragserfüllung, Messung, Abrechnung, Rechnungsstellung, Zahlung und Eintreibung.

## <a name="publishing-options"></a>Veröffentlichungsoptionen

Für IoT Edge-Module sollten Sie jedenfalls die Veröffentlichungsoption **Transact** auswählen.  Weitere Informationen zu Veröffentlichungsoptionen finden Sie unter [Auswählen einer Veröffentlichungsoption](determine-your-listing-type.md).  

## <a name="customer-leads"></a>Kundenleads

Wenn Sie ein Angebot mithilfe von Partner Center im kommerziellen Marketplace veröffentlichen, sollten Sie es mit Ihrem CRM-System (Customer Relationship Management) verbinden. Dadurch empfangen Sie Kontaktinformationen zum Kunden, sobald jemand sein Interesse an Ihrem Produkt bekundet oder es verwendet. Das Herstellen einer Verbindung mit einem CRM-System ist erforderlich, wenn Sie eine Testversion aktivieren möchten, andernfalls ist dieser Schritt optional. Partner Center unterstützt Azure Table Storage, Dynamics 365 Customer Engagement, HTTPS-Endpunkte, Marketo und Salesforce.

## <a name="legal-contracts"></a>Verträge

Zur Vereinfachung des Beschaffungsprozesses für Kunden und zur Reduzierung der rechtlichen Komplexität für Softwareanbieter stellt Microsoft eine Vorlage für Standardverträge bereit, die Sie für Ihre Angebote im kommerziellen Marketplace verwenden können. Wenn Sie Ihre Software unter dem Standardvertrag anbieten, müssen Kunden ihn nur einmal lesen und akzeptieren, und Sie müssen keine benutzerdefinierten Geschäftsbedingungen erstellen.

Sie können anstelle des Standardvertrags Ihre eigenen Geschäftsbedingungen angeben. Kunden müssen diesen Bestimmungen zustimmen, bevor sie Ihr Angebot testen können.

## <a name="offer-listing-details"></a>Details zur Angebotsauflistung

> [!NOTE]
> Der Inhalt der Angebotsauflistung muss nicht in englischer Sprache verfasst werden, sofern die Angebotsbeschreibung mit dem Satz „Diese Anwendung ist nur auf [nicht englische Sprache] erhältlich“ beginnt.

Um das Angebot einfacher zu gestalten, können Sie diese Elemente vorab vorbereiten. Sofern nicht anders angegeben sind alle erforderlich.

- **Name:** Dieser Name wird als Titel Ihres Angebots im kommerziellen Marketplace angezeigt. Der Name ist möglicherweise markenrechtlich geschützt. Er darf keine Emojis enthalten (außer diese sind Teil der Markenzeichen oder Copyrightsymbole) und maximal 50 Zeichen lang sein.
- **Zusammenfassung der Suchergebnisse:** Geben Sie den Zweck oder die Funktion des Angebots in einem Satz ohne Zeilenumbrüche und mit höchstens 100 Zeichen an. Diese Zusammenfassung wird in den Suchergebnissen verwendet, die aus Auflistungen im kommerziellen Marketplace erfasst werden.
- **Kurzbeschreibung:** Geben Sie den Zweck oder die Funktion des Angebots als Text ohne Formatierungen oder Zeilenumbrüche an. Diese Informationen werden auf der Detailseite Ihres Angebots angezeigt.
- **Beschreibung:** Diese Beschreibung wird in der Übersicht der Auflistungen im kommerziellen Marketplace angezeigt. Sie können z. B ein Wertversprechen, wichtige Vorteile, Zielgruppe, Kategorie- oder Branchenzuordnungen, Möglichkeiten für In-App-Käufe, erforderliche Veröffentlichungen und einen Link zu weiteren Informationen eingeben. Dieses Textfeld enthält Rich-Text-Editor-Steuerelemente, mit denen Sie die Beschreibung ansprechender gestalten können. Verwenden Sie optional HTML-Tags für die Formatierung.
- **Link zur Datenschutzrichtlinie:** URL zur Datenschutzrichtlinie Ihres Unternehmens. Es liegt in Ihrer Verantwortung sicherzustellen, dass Ihre App den Gesetzen und Regelungen zum Datenschutz entspricht.
- **Nützliche Links** (optional): Links für Benutzer Ihres Angebots zu verschiedenen Ressourcen. Beispiele: Foren, FAQ und Versionshinweise.
- **Kontaktinformationen**
  - **Supportkontakt:** Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse für Microsoft-Partner an. Diese werden dann von Kunden bei der Eröffnung von Supporttickets verwendet. Schließen Sie auch die URL für Ihre Supportwebsite ein.
  - **Technischer Ansprechpartner:** Geben Sie den Namen, die Telefonnummer und die E-Mail-Adresse an, die Microsoft direkt verwenden kann, wenn es Probleme mit Ihrem Angebot gibt. Diese Kontaktinformationen sind nicht im kommerziellen Marketplace aufgeführt.
  - **Kontaktdaten für das CSP-Programm** (optional): Name, Telefonnummer und E-Mail-Adresse, falls Sie sich für das CSP-Programm (Cloud Solution Provider) entscheiden, damit Sie bei Fragen von Partnern kontaktiert werden können. Sie können auch eine URL zu Ihren Marketingmaterialien hinzufügen.
- **Medien**
    - **Logos:** Stellen Sie eine PNG-Datei für das **große** Logo bereit. Dieses wird in Partner Center verwendet, um die anderen erforderlichen Logogrößen zu erstellen. Sie können diese Logos später durch andere Bilder ersetzen.
    - **Screenshots:** Fügen Sie mindestens einen und bis zu fünf Screenshots hinzu, die zeigen, wie Ihr Angebot funktioniert. Bilder müssen 1280 × 720 Pixel im PNG-Format aufweisen und eine Beschriftung enthalten.
    - **Videos** (optional): Fügen Sie bis zu vier Videos hinzu, die Ihr Angebot vorstellen. Geben Sie einen Namen, eine URL für YouTube oder Vimeo sowie eine PNG-Miniaturansicht mit 1280 × 720 Pixel an.

> [!Note]
> Ihr Angebot muss für die Veröffentlichung im kommerziellen Marketplace die allgemeinen [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#100-general) erfüllen.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines IoT Edge-Modulangebots](./iot-edge-offer-setup.md) in Partner Center
