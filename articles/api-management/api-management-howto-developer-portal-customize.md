---
title: 'Tutorial: Zugreifen auf und Anpassen des Entwicklerportals – Azure API Management | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie das API Management-Entwicklerportal anpassen. Hierbei handelt es sich um eine automatisch generierte und vollständig anpassbare Website mit der Dokumentation Ihrer APIs.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: apimpm
ms.openlocfilehash: c08fd4635232bdb84ce27fdc7a3e6f0d1791e921
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318574"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Tutorial: Zugreifen auf und Anpassen des Entwicklerportals

Das *Entwicklerportal* ist eine automatisch generierte und vollständig anpassbare Website mit der Dokumentation Ihrer APIs. Dort können API-Consumer Ihre APIs entdecken, erfahren, wie diese verwendet werden, und Zugriff anfordern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Zugreifen auf die verwaltete Version des Entwicklerportals
> * Navigieren durch die Administratoroberfläche
> * Anpassen des Inhalts
> * Veröffentlichen der Änderungen
> * Anzeigen des veröffentlichten Portals

Weitere Informationen zum Entwicklerportal finden Sie in der [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management-Entwicklerportal: Administratormodus" border="false":::

## <a name="prerequisites"></a>Voraussetzungen

- Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
- Importieren und veröffentlichen Sie eine Azure API Management-Instanz. Weitere Informationen finden Sie unter [Importieren und Veröffentlichen](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Zugreifen auf das Portal als Administrator

Führen Sie die folgenden Schritte aus, um auf die verwaltete Version des Portals zuzugreifen.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie in der oberen Navigationsleiste die Schaltfläche **Entwicklerportal** aus. Eine neue Browserregisterkarte mit einer administrativen Version des Portals wird geöffnet.


## <a name="developer-portal-architectural-concepts"></a>Architekturkonzepte des Entwicklerportals

Die Portalkomponenten können logisch in zwei Kategorien unterteilt werden: *Code* und *Inhalt*.

### <a name="code"></a>Code

Code wird im [GitHub-Repository](https://github.com/Azure/api-management-developer-portal) für das API Management-Entwicklerportal verwaltet und umfasst Folgendes:

- **Widgets**, die visuelle Elemente darstellen und HTML, JavaScript, Formatierungsfunktionen, Einstellungen und Inhaltszuordnung kombinieren. Beispiele hierfür sind ein Bild, ein Textabsatz, ein Formular, eine Liste mit APIs usw.
- **Formatierungsdefinitionen**, die angeben, wie Widgets formatiert werden können
- **Engine**, die statische Webseiten aus Portalinhalten generiert und in JavaScript geschrieben ist
- **Visueller Editor**, der das Anpassen und Erstellen im Browser ermöglicht

### <a name="content"></a>Inhalt

Inhalt wird in zwei Unterkategorien unterteilt: *Portalinhalt* und *API Management-Inhalt*.

*Portalinhalt* ist für das Portal spezifisch und umfasst Folgendes:

- **Seiten:** etwa Landing Page, API-Tutorials, Blogbeiträge
- **Medien:** Bilder, Animationen und andere dateibasierte Inhalte
- **Layouts:** Vorlagen, die mit einer URL abgeglichen werden und definieren, wie Seiten angezeigt werden
- **Formatvorlagen:** Werte für Formatierungsdefinitionen, z. B. Schriftarten, Farben, Rahmen
- **Einstellungen:** Konfigurationen, z.  B. Favicon, Websitemetadaten

    Portalinhalte (mit Ausnahme von Medien) werden als JSON-Dokumente ausgedrückt.

*API Management-Inhalte* umfassen Entitäten wie APIs, Vorgänge, Produkte und Abonnements.
## <a name="understand-the-portals-administrative-interface"></a>Grundlegendes zur Administratoroberfläche des Portals

### <a name="default-content"></a>Standardinhalt 

Wenn Sie zum ersten Mal auf das Portal zugreifen, wird der Standardinhalt automatisch im Hintergrund bereitgestellt. Der Standardinhalt ist dafür konzipiert, die Funktionen des Portals zu veranschaulichen und den Anpassungsaufwand zu minimieren, der für die Personalisierung des Portals erforderlich ist. Weitere Informationen dazu, was im Portal enthalten ist, finden Sie in der [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Visueller Editor

Sie können den Inhalt des Portals mithilfe des visuellen Editors anpassen. 
* Über die Menüabschnitte auf der linken Seite können Sie Seiten, Medien, Layouts, Menüs, Formatvorlagen oder Websiteeinstellungen erstellen und ändern. 
* Mit den Menüelementen im unteren Bereich können Sie zwischen Viewports wechseln (z. B. mobil und Desktop), die Elemente des Portals anzeigen, die für authentifizierte oder anonyme Benutzer sichtbar sind, und Aktionen speichern oder rückgängig machen.
* Fügen Sie einer Seite Zeilen hinzu, indem Sie auf ein blaues Symbol mit einem Pluszeichen klicken. 
* Widgets (z. B. Text, Bilder oder API-Listen) können durch Klicken auf ein graues Symbol mit Pluszeichen hinzugefügt werden.
* Ordnen Sie die Elemente auf einer Seite per Drag & Drop neu an. 

### <a name="layouts-and-pages"></a>Layouts und Seiten

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Seiten und Layouts" border="false":::

Layouts definieren, wie Seiten angezeigt werden. Im Standardinhalt gibt es beispielsweise zwei Layouts: eines für die Startseite und das andere für alle anderen Seiten.

Ein Layout wird auf eine Seite angewendet, indem die zugehörige URL-Vorlage mit der URL der Seite abgeglichen wird. Beispielsweise wird ein Layout mit einer URL-Vorlage vom Typ `/wiki/*` auf jede Seite mit dem Segment `/wiki/` angewendet: `/wiki/getting-started`, `/wiki/styles` usw.

In der obigen Abbildung ist zum Layout gehöriger Inhalt blau markiert und die Seite selbst rot umrandet. Die Menüabschnitte sind entsprechend gekennzeichnet.

### <a name="styling-guide"></a>Gestaltungsvorlagen

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Gestaltungsvorlagen" border="false":::

Gestaltungsvorlagen sind ein Panel, das speziell für Designer erstellt wurde. Hier können alle visuellen Elemente in Ihrem Portal angezeigt und gestaltet werden. Die Gestaltung erfolgt hierarchisch: Viele Elemente erben Eigenschaften von anderen Elementen. Ein Beispiel: Schaltflächenelemente verwenden Farben für Text und Hintergrund. Um die Farbe einer Schaltfläche zu ändern, müssen Sie die ursprüngliche Farbvariante ändern.

Um eine Variante zu bearbeiten, wählen Sie die gewünschte Variante und dann das angezeigte Stiftsymbol aus. Schließen Sie das Popupfenster, nachdem Sie darin die gewünschten Änderungen vorgenommen haben.

### <a name="save-button"></a>Schaltfläche „Speichern“

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Schaltfläche „Speichern“" border="false":::

Wenn Sie eine Änderung im Portal vorgenommen haben, müssen Sie diese manuell speichern, indem Sie im Menü unten die Schaltfläche **Speichern** auswählen oder STRG+S drücken. Nachdem Sie die Änderungen gespeichert haben, wird der geänderte Inhalt automatisch in Ihren API Management-Dienst hochgeladen.

## <a name="customize-the-portals-content"></a>Anpassen des Portalinhalts

Bevor Sie Ihr Portal Besuchern zur Verfügung stellen, sollten Sie den automatisch generierten Inhalt personalisieren. Empfohlen werden Änderungen an Layouts, Formatvorlagen und dem Inhalt der Startseite.

> [!NOTE]
> Aufgrund von Überlegungen in Bezug auf die Integration können die folgenden Seiten nicht entfernt oder zu einer anderen URL verschoben werden: `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso`, `/signup`.

### <a name="home-page"></a>Startseite

Die Standard-**Startseite** ist mit Platzhalterinhalt gefüllt. Sie können entweder ganze Abschnitte mit diesem Inhalt entfernen oder die Struktur beibehalten und die Elemente einzeln anpassen. Ersetzen Sie die generierten Texte und Bilder durch eigene, und stellen Sie sicher, dass die Links auf die gewünschte Speicherorte zeigen. Sie können Struktur und Inhalt der Startseite wie folgt bearbeiten:
* Ziehen und Ablegen von Seitenelementen an die gewünschte Position auf der Seite
* Auswählen von Text- und Überschriftenelementen zum Bearbeiten und Formatieren von Inhalten 
* Überprüfen, ob Schaltflächen auf die richtigen Ziele verweisen

### <a name="layouts"></a>Layouts

Ersetzen Sie das automatisch generierte Logo in der Navigationsleiste durch ein eigenes Bild.

1. Wählen Sie im Entwicklerportal oben links auf der Navigationsleiste das **Contoso**-Logo aus. 
1. Klicken Sie auf das Symbol **Bearbeiten**. 
1. Wählen Sie unterhalb des **Hauptabschnitts** die Option **Quelle** aus.
1. Führen Sie im Popupfenster **Medien** eine der folgenden Aufgaben aus:
    * Wählen Sie ein Bild aus, das bereits in Ihre Bibliothek hochgeladen wurde.
    * Klicken Sie auf **Datei hochladen**, um eine neue Bilddatei hochzuladen.
    * Wählen Sie **Keine** aus, um auf die Verwendung eines Logos zu verzichten.
1. Das Logo wird in Echtzeit aktualisiert.
1. Klicken Sie auf eine Stelle außerhalb der Popupfenster aus, um die Medienbibliothek zu schließen.
1. Klicken Sie auf **Speichern**.

### <a name="styling"></a>Gestaltung

Sie müssen zwar keine Formatvorlagen ändern, aber möglicherweise möchten Sie bestimmte Elemente anpassen. Ändern Sie beispielsweise die primäre Farbe in die Farbe Ihrer Marke. Hierzu stehen zwei Möglichkeiten zur Verfügung:

#### <a name="overall-site-style"></a>Allgemeiner Stil der Website

1. Wählen Sie im Entwicklerportal auf der links angezeigten Symbolleiste die Option **Stile** aus.
1. Wählen Sie im Abschnitt **Farben** das Farbstilelement aus, das Sie bearbeiten möchten.
1. Klicken Sie für dieses Stilelement auf das Symbol **Bearbeiten**.
1. Wählen Sie mithilfe der Farbauswahl eine Farbe aus, oder geben Sie den Hexadezimalcode der gewünschten Farbe an.
1. Fügen Sie ein weiteres Farbelement hinzu, indem Sie auf **Farbe hinzufügen** klicken, und benennen Sie das Farbelement.  
1. Klicken Sie auf **Speichern**.

#### <a name="container-style"></a>Containerstil

1. Wählen Sie auf der Hauptseite des Entwicklerportals den Containerhintergrund aus.
1. Klicken Sie auf das Symbol **Bearbeiten**.
1. Legen Sie im Popupfenster Folgendes fest:
    * Geben Sie als Hintergrund Transparenz, ein Bild, eine bestimmte Farbe oder einen Verlauf an.
    * Legen Sie Größe, Rand und Abstände für die Container fest.
    * Geben Sie die Containerposition und -höhe an.
1. Klicken Sie auf eine Stelle außerhalb der Popupfenster aus, um die Containereinstellungen zu schließen.
1. Klicken Sie auf **Speichern**.

### <a name="customization-example"></a>Anpassungsbeispiel

Im folgenden Video demonstrieren wir, wie Sie den Inhalt des Portals bearbeiten, das Aussehen der Website anpassen und die Änderungen veröffentlichen.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Veröffentlichen des Portals

Um Ihr Portal mit den neuesten Änderungen für Besucher zur Verfügung zu stellen, müssen Sie es *veröffentlichen*. Sie können das Portal über die Verwaltungsschnittstelle des Portals oder über das Azure-Portal veröffentlichen.

### <a name="publish-from-the-administrative-interface"></a>Veröffentlichen über die Verwaltungsschnittstelle

1. Stellen Sie sicher, dass Ihre Änderungen gespeichert wurden, indem Sie das Symbol **Speichern** auswählen.
1. Wählen Sie im Menüabschnitt **Vorgänge** die Option **Website veröffentlichen** aus. Dieser Vorgang kann einige Minuten dauern.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Portal veröffentlichen" border="false":::

### <a name="publish-from-the-azure-portal"></a>Veröffentlichen über das Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie im Menü auf der linken Seite unter **Entwicklerportal** die Option **Portalübersicht** aus.
1. Wählen Sie im Fenster **Portalübersicht** die Option **Veröffentlichen** aus.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Veröffentlichen des Portals über das Azure-Portal":::

> [!NOTE]
> Das Portal muss erneut veröffentlicht werden, nachdem sich die Konfiguration des API Management-Diensts geändert hat. Führen Sie beispielsweise eine erneute Veröffentlichung des Portals durch, nachdem eine benutzerdefinierte Domäne zugewiesen wurde, die Identitätsanbieter aktualisiert wurden, die Delegierung festgelegt wurde oder Bedingungen für die Anmeldung und für Produkte angegeben wurden.


## <a name="visit-the-published-portal"></a>Besuchen des veröffentlichten Portals

Nachdem Sie das Portal veröffentlicht haben, können Sie über die gleiche URL wie für das Verwaltungspanel darauf zugreifen, z. B. über `https://contoso-api.developer.azure-api.net`. Sie können es in einer separaten Browsersitzung (Inkognito oder privater Modus) als externer Besucher anzeigen.

## <a name="apply-the-cors-policy-on-apis"></a>Anwenden der CORS-Richtlinie auf APIs

Aktivieren Sie CORS (Cross-Origin Resource Sharing) für Ihre APIs, damit die Besucher Ihres Portals die APIs über die integrierte interaktive Konsole testen können. Ausführlichere Informationen finden Sie unter [API Management-Entwicklerportal: häufig gestellte Fragen](developer-portal-faq.md#cors).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)
- [Migrieren zum neuen Entwicklerportal](developer-portal-deprecated-migration.md) aus dem veralteten Legacyportal