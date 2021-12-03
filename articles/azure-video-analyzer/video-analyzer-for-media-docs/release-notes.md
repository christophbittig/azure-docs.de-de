---
title: Versionshinweise zu Azure Video Analyzer for Media (früher Video Indexer) | Microsoft-Dokumentation
description: Damit Sie bezüglich der aktuellen Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Azure Video Analyzer for Media (früher Video Indexer).
ms.topic: article
ms.custom: references_regions
ms.date: 08/01/2021
ms.author: juliako
ms.openlocfilehash: 7341112e545e4fc0c74de8d32e9c2d54d3461057
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491919"
---
# <a name="video-analyzer-for-media-release-notes"></a>Versionshinweise zu Video Analyzer for Media

>Sie können eine Benachrichtigung erhalten, wann auf dieser Seite Updates vorhanden sind, indem Sie die URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` kopieren und in Ihren RSS-Feedreader einfügen.

Damit Sie bei den aktuellen Entwicklungen in Bezug auf Azure Video Analyzer for Media (früher Video Indexer) auf dem neuesten Stand bleiben, enthält dieser Artikel folgende Informationen:

* Neueste Versionen
* Bekannte Probleme
* Behebung von Programmfehlern
* Veraltete Funktionen

## <a name="november-2021"></a>November 2021
 
### <a name="public-preview-of-azure-video-analyzer-for-media-account-management-based-on-arm"></a>Öffentliche Vorschauversion von Azure Video Analyzer für die Medienkontoverwaltung basierend auf ARM

Azure Video Analyzer für Medien führt eine öffentliche Vorschauversion der Azure Resource Manager (ARM)-basierten Kontoverwaltung ein. Sie können ARM-basierte APIs zum Erstellen, Bearbeiten und Löschen eines Kontos aus dem Azure-Portal nutzen.

Weitere Informationen finden Sie unter [Erstellen eines Video Analyzer for Media](https://techcommunity.microsoft.com/t5/azure-ai/azure-video-analyzer-for-media-is-now-available-as-an-azure/ba-p/2912422).

### <a name="peoples-clothing-detection"></a>Erkennung der Bekleidung von Personen

Wenn Sie ein Video über die erweiterten Videoeinstellungen indizieren, können Sie die neue Funktion zur **Erkennung von Kleidung** sehen. Wenn in Ihrer Mediendatei Personen erkannt werden, können Sie nun über den Media Player den Kleidungstyp anzeigen, den sie tragen. 

## <a name="october-2021"></a>Oktober 2021

### <a name="embed-widgets-in-your-app-using-azure-video-analyzer-for-media-package"></a>Einbetten von Widgets in Ihre App mithilfe des Azure Video Analyzer für Medienpaket

Verwenden Sie das neue `@azure/video-analyzer-for-media-widgets` NPM-Paket von Azure Video Analyzer für Medien (AVAM), um Ihrer App `insights` Widgets hinzuzufügen und sie ihren Anforderungen entsprechend anzupassen.

Mit dem neuen AVAM-Paket können Sie ganz einfach unsere Widgets und Ihre App einbetten und kommunizieren, anstatt ein `iframe`-Element zum Einbetten des Erkenntniswidgets hinzuzufügen. Weitere Informationen finden Sie unter [Einbetten und Anpassen von Video Analyzer for Media-Widgets in Ihre App](https://techcommunity.microsoft.com/t5/azure-media-services/embed-and-customize-azure-video-analyzer-for-media-widgets-in/ba-p/2847063). 

## <a name="august-2021"></a>August 2021

### <a name="re-index-video-or-audio-files"></a>Erneutes Indizieren von Video- oder Audiodateien

Es gibt jetzt eine Option zum erneuten Indizieren von Video- oder Audiodateien, bei denen während des Indizierungsprozesses ein Fehler aufgetreten ist.

### <a name="improve-accessibility-support"></a>Verbesserte Unterstützung für Barrierefreiheit

Fehler im Zusammenhang mit CSS, Designs und Barrierefreiheit wurden behoben:

* Hoher Kontrast
* Kontoeinstellungen und Ansichten zu Erkenntnissen im [Portal](https://www.videoindexer.ai).  

## <a name="july-2021"></a>Juli 2021

### <a name="automatic-scaling-of-media-reserved-units"></a>Automatische Skalierung reservierter Einheiten für Medien
 
Seit dem 1. August 2021 ermöglicht Azure Video Analyzer for Media (ehemals Video Indexer) die automatische Skalierung von [reservierten Einheiten für Medien](../../media-services/latest/concept-media-reserved-units.md)(Media Reservced Units, MRUs) durch [Azure Media Services](../../media-services/latest/media-services-overview.md), sodass Sie diese nicht mehr über Azure Video Analyzer for Media verwalten müssen. Dies ermöglicht eine Preisoptimierung, z. B. in vielen Fällen eine Preissenkung, die sich an den Anforderungen Ihres Unternehmens orientiert, während es automatisch skaliert wird.

## <a name="june-2021"></a>Juni 2021
 
### <a name="video-analyzer-for-media-deployed-in-six-new-regions"></a>Video Analyzer for Media wurde in sechs neuen Regionen bereitgestellt
 
Sie können jetzt ein kostenpflichtiges Video Analyzer for Media-Konto in den Regionen „Frankreich, Mitte“, „USA, Mitte“, „Brasilien, Süden“, „USA, Westen-Mitte“, „Südkorea, Mitte“ und „Japan, Westen“ erstellen.
  
## <a name="may-2021"></a>Mai 2021

### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Unterstützung neuer Quellsprachen für Sprache-in-Text (STT), Übersetzung und Suche

Video Analyzer for Media unterstützt jetzt STT, Übersetzung und Suche in Chinesisch (Kantonesisch) ('zh-HK'), Niederländisch (Niederlande) ('Nl-NL'), Tschechisch ('Cs-CZ'), Polnisch ('Pl-PL'), Schwedisch (Schweden) ('Sv-SE'), Norwegisch ('nb-NO'), Finnisch ('fi-FI'), Französisch (Kanada) ('fr-CA'), Thailändisch ('th-TH'), Arabisch: (Vereinigte Arabische Emirate) ('ar-AE', 'ar-EG'), (Irak) ('ar-IQ'), (Jordanien) ('ar-JO'), (Kuwait) ('ar-KW'), (Libanon) ('ar-LB'), (Oman) ('ar-OM'), (Katar) ('ar-QA'), (Palästinensische Autonomiebehörde) ('ar-PS'), (Syrien) ('ar-SY') und Türkisch ('tr-TR'). 

Diese Sprachen sind sowohl in der API als auch auf der Video Analyzer for Media-Website verfügbar. Wählen Sie im Kombinationsfeld unter **Ausgangssprache des Videos** die Sprache aus.

### <a name="new-theme-for-azure-video-analyzer-for-media"></a>Neues Design für Azure Video Analyzer for Media

Es ist ein neues Design verfügbar: „Azure“ zusammen mit den Designs „hell“ und „dunkel“. Klicken Sie zum Auswählen eines Designs in der oberen rechten Ecke der Website auf das Zahnradsymbol, und suchen Sie unter **Benutzereinstellungen** nach Designs.
 
### <a name="new-open-source-code-you-can-leverage"></a>Neuer Open-Source-Code, den Sie nutzen können 

Drei neue GitHub-Projekte sind in unserem [GitHub-Repository](https://github.com/Azure-Samples/media-services-video-indexer) verfügbar:

* Code, der Ihnen hilft, die neu hinzugefügte [Widget-Anpassung](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) zu nutzen.
* Lösung, mit der Sie Ihren Videobibliotheken eine [benutzerdefinierte Suche](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/VideoSearchWithAutoMLVision) hinzufügen können.
* Lösung, mit der Sie Ihren Videobibliotheken eine [Deduplizierung](https://github.com/Azure-Samples/media-services-video-indexer/commit/6b828f598f5bf61ce1b6dbcbea9e8b87ba11c7b1) hinzufügen können.
 
### <a name="new-option-to-toggle-bounding-boxes-for-observed-people-on-the-player"></a>Neue Option zum Umschalten von Begrenzungsrahmen (für beobachtete Personen) für den Player  

Wenn Sie ein Video über unsere erweiterten Videoeinstellungen indizieren, werden unsere neuen Funktionen für beobachtete Personen angezeigt. Wenn in Ihrer Mediendatei Personen erkannt werden, können Sie über den Media Player einen Begrenzungsrahmen für die erkannte Person aktivieren.

## <a name="april-2021"></a>April 2021

Der Video Indexer-Dienst wurde in Azure Video Analyzer for Media umbenannt.

### <a name="improved-upload-experience-in-the-portal"></a>Verbesserte Uploadfunktionalität im Portal
 
Video Analyzer for Media verfügt über eine neue Uploadfunktionalität im [Portal](https://www.videoindexer.ai): Um Ihre Mediendatei hochzuladen, klicken Sie auf der Registerkarte **Mediendateien** auf die Schaltfläche **Hochladen**.

### <a name="new-developer-portal-in-available-in-gov-cloud"></a>Neues Entwicklerportal in gov-cloud verfügbar
 
Das [Video Analyzer for Media-Entwicklerportal](https://api-portal.videoindexer.ai) ist jetzt auch in Azure für die US-Regierung verfügbar.

### <a name="observed-people-tracing-preview"></a>Verfolgung von beobachteten Personen (Vorschau)  

Azure Video Analyzer for Media erkennt jetzt beobachtete Personen in Videos und liefert Informationen wie die Position der Person im Video-Frame und den genauen Zeitstempel (Start, Ende), wann eine Person erscheint. Die API gibt die Begrenzungsfeldkoordinaten (in Pixel) für jede erkannte Personeninstanz einschließlich Konfidenz zurück. 

Wenn ein Video beispielsweise eine Person enthält, listet der Erkennungsvorgang das Erscheinen dieser Person zusammen mit ihren Koordinaten in den Videoframes auf. Sie können diese Funktion verwenden, um den Weg der Person in einem Video zu bestimmen. Sie können außerdem ermitteln, ob mehrere Instanzen derselben Person einem Video enthalten sind. 

Die neu hinzugefügte Funktion für die Verfolgung von beobachteten Personen ist verfügbar, wenn Sie Ihre Datei mit der Voreinstellung **Erweiterte Option** -> **Erweitertes Video** oder **Erweitertes Video + Audio** (unter „Video + Audio Indizierung“) indizieren. Dieses neue erweiterte Modell ist nicht in Standard- und Basisindizierungsvoreinstellungen enthalten. 

Wenn Sie sich dafür entscheiden, Erkenntnisse des Videos auf der Video Analyzer for Media-Website anzuzeigen, wird die Verfolgung von beobachteten Personen auf der Seite mit allen erkannten Personenminiaturansichten angezeigt. Sie können eine Miniaturansicht einer Person auswählen und sehen, wo die Person im Videoplayer erscheint.  

Das Feature ist auch in der JSON-Datei verfügbar, die von Video Analyzer for Media generiert wird. Weitere Informationen finden Sie im Artikel zum [Verfolgen beobachteter Personen in einem Video](observed-people-tracing.md).

### <a name="detected-acoustic-events-with-audio-effects-detection-preview"></a>Mit **Audioeffekterkennung** erkannte Akustikereignisse (Vorschau)

Sie können jetzt die erkannten Akustikereignisse in der Untertiteldatei sehen. Die Datei kann vom Video Analyzer for Media-Portal heruntergeladen werden und ist als Artefakt in der GetArtifact-API verfügbar.

Die Komponente **Erkennung von Audioeffekten** (Vorschau) erkennt verschiedene Akustikereignisse und klassifiziert sie in verschiedene Akustikkategorien (z. B. Schüsse, Schreie, Reaktionen einer Menschenmenge usw.). Weitere Informationen finden Sie im Artikel zum [Erkennen von Audioeffekten.](audio-effects-detection.md)

## <a name="march-2021"></a>März 2021

### <a name="audio-analysis"></a>Audioanalyse 

Die Audioanalyse ist jetzt in einem zusätzlichen neuen Paket mit Audiofeatures zu einem anderen Preis verfügbar. Die neue Analysevoreinstellung **Grundlegende Audiofunktionen** bietet eine kostengünstige Option zum reinen Extrahieren von Transkriptionen für gesprochenen Text sowie zum Übersetzen und Formatieren von Beschriftungstext und Untertiteln. Durch die Voreinstellung **Grundlegende Audiofunktionen** werden auf Ihrer Rechnung zwei separate Verbrauchseinheiten generiert. Diese umfassen eine Zeile für die Transkription und eine separate Zeile für die Formatierung von Beschriftungstext und Untertiteln. Weitere Preisinformationen finden Sie auf der Seite mit [Media Services-Preisen](https://azure.microsoft.com/pricing/details/media-services/).

Das neu hinzugefügte Paket ist verfügbar, wenn Sie beim Indizieren oder Neuindizieren Ihrer Datei die Voreinstellung **Erweiterte Option** -> **Grundlegende Audiofunktionen** (im Dropdownfeld **Video- und Audioindizierung**) auswählen.

### <a name="new-developer-portal"></a>Neues Entwicklerportal 

Video Analyzer for Media verfügt über ein [Neues Entwicklerportal](https://api-portal.videoindexer.ai/) – testen Sie die neuen Video Analyzer for Media-APIs, und finden Sie alle relevanten Ressourcen an einem Ort: [GitHub-Repository](https://github.com/Azure-Samples/media-services-video-indexer), [Stack Overflow](https://stackoverflow.com/questions/tagged/video-indexer), [Video Analyzer for Media Tech Community](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) mit relevanten Blogbeiträgen, [Häufig gestellte Fragen (FAQs) zu Video Analyzer for Media](faq.yml), [User Voice](https://feedback.azure.com/d365community/forum/09041fae-0b25-ec11-b6e6-000d3a4f0858), um Feedback zu geben und Features vorzuschlagen, und ["CodePen"-Link](https://codepen.io/videoindexer) mit Codebeispielen für Widgets. 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Erweiterte Anpassungsmöglichkeiten für das Erkenntnis-Widget 

Das SDK ist jetzt verfügbar, um das Erkenntnis-Widget von Video Analyzer for Media in Ihren eigenen Dienst einzubetten und dessen Formatvorlage und Daten anzupassen. Das SDK unterstützt das Erkenntnis-Standard-Widget von Video Analyzer for Media und ein vollständig anpassbares Erkenntnis-Widget. Das Codebeispiel ist im [GitHub-Repository für Video Analyzer for Media](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization) verfügbar. Mit diesen erweiterten Anpassungsfunktionen kann der Lösungsentwickler benutzerdefinierte Formatvorlagen anwenden und die eigenen KI-Daten des Kunden im Erkenntnis-Widget präsentieren (mit oder ohne Video Analyzer for Media-Erkenntnisse). 

### <a name="video-analyzer-for-media-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Analyzer for Media in „USA, Norden-Mitte“, „USA, Westen“ und „Kanada, Mitte“ verfügbar 

Sie können jetzt in den Regionen „USA, Norden-Mitte“, „USA, Westen“ und „Kanada, Mitte“ ein gebührenpflichtiges Video Analyzer for Media-Konto erstellen.
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Unterstützung neuer Quellsprachen für Sprache-in-Text (STT), Übersetzung und Suche 

Video Analyzer for Media unterstützt jetzt STT, Übersetzung und Suche in Dänisch ('da-DK'), Norwegisch ('nb-NO'), Schwedisch ('sv-SE'), Finnisch ('fi-FI'), Französisch (Kanada) ('fr-CA'), Thai ('th-TH'), Arabisch ('ar-BH', 'ar-EG', 'ar-IQ', 'ar-JO', 'ar-KW', 'ar-LB', 'ar-OM', 'ar-QA', 'ar-S' und 'ar-SY') und Türkisch ('tr-TR'). Diese Sprachen sind sowohl in der API als auch auf der Video Analyzer for Media-Website verfügbar. 
 
### <a name="search-by-topic-in-video-analyzer-for-media-website"></a>Thematische Suche in der Video Analyzer for Media-Website 

Sie können jetzt die Suchfunktion oben auf der Seite der [Video Analyzer for Media-Website](https://www.videoindexer.ai/account/login) zum Suchen nach Videos mit bestimmten Themen verwenden. 

## <a name="february-2021"></a>Februar 2021

### <a name="multiple-account-owners"></a>Mehrere Kontobesitzer 

Die Rolle "Kontobesitzer" wurde Video Analyzer for Media hinzugefügt. Sie können Benutzer hinzufügen, ändern und entfernen sowie deren Rolle ändern. Ausführliche Informationen zum Teilen eines Kontos finden Sie unter [Schnellstart: Einladen von Benutzern zu Video Indexer](invite-users.md).

### <a name="audio-event-detection-public-preview"></a>Audioereigniserkennung (Public Preview)

> [!NOTE]
> Dieses Feature steht nur in Testkonten zur Verfügung. 

Von Video Analyzer for Media werden nun die folgenden Audioeffekte in Inhaltsabschnitten ohne Sprache erkannt: Schüsse, Glasbruch, Alarme, Sirenen, Explosionen, Hundegebell, Schreie, Gelächter, Reaktionen einer Menschenmenge (Begeisterung, Applaudieren und Buhrufe) sowie Stille. 

Das neu hinzugefügte Feature für Audioeffekte ist verfügbar, wenn Sie Ihre Datei mit der Voreinstellung **Erweiterte Option** -> **Erweiterte Audiodaten** (unter „Video- und Audioindizierung“) indizieren. Die Standardindizierung umfasst nur **Stille** und **Reaktionen einer Menschenmenge**. 

Der Ereignistyp **Applaus** aus dem vorherigen Modell für Audioeffekte wird nun im Rahmen des Ereignistyps **Reaktionen einer Menschenmenge** extrahiert.

Wenn Sie sich auf der [Video Analyzer for Media-Website](https://www.videoindexer.ai/) **Erkenntnisse** Ihres Videos anzeigen möchten, werden die Audioeffekte auf der Seite angezeigt.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Audioereigniserkennung":::

### <a name="named-entities-enhancement"></a>Erweiterung benannter Entitäten  

Die extrahierte Personen- und Ortsliste wurde allgemein erweitert und aktualisiert. 

Außerdem enthält das Modell jetzt Personen und Orte im Kontext, die nicht berühmt sind (beispielsweise „Sam“ oder „Zuhause“ im Video). 

## <a name="january-2021"></a>Januar 2021

### <a name="video-analyzer-for-media-is-deployed-on-us-government-cloud"></a>Video Analyzer for Media in Cloud der US-Regierung verfügbar 

Sie können jetzt ein kostenpflichtiges Video Analyzer for Media-Konto in einer Cloud der US-Regierung in den Regionen „US Gov Virginia“ und „US Gov Arizona“ erstellen. Die kostenlose Testversion von Video Analyzer for Media ist in den genannten Regionen nicht verfügbar. Weitere Informationen finden Sie in der Video Analyzer for Media-Dokumentation. 

### <a name="video-analyzer-for-media-deployed-in-the-india-central-region"></a>Video Analyzer for Media in der Region „Indien, Mitte“ verfügbar 

Sie können jetzt ein kostenpflichtiges Video Analyzer for Media-Konto in der Region „Indien, Mitte“ erstellen. 

### <a name="new-dark-mode-for-the-video-analyzer-for-media-website-experience"></a>Neuer dunkler Modus für die Video Analyzer for Media-Website

Für die Video Analyzer for Media-Website ist nun auch ein dunkler Modus verfügbar. Um den dunklen Modus zu aktivieren, öffnen Sie den Einstellungsbereich und aktivieren die Option **Dunkler Modus**. 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Einstellung für den dunklen Modus":::

## <a name="december-2020"></a>Dezember 2020

### <a name="video-analyzer-for-media-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Analyzer for Media in den Regionen „Schweiz, Westen“ und „Schweiz, Norden“ verfügbar

Sie können jetzt in den Regionen „Schweiz, Westen“ und „Schweiz, Norden“ ein kostenpflichtiges Video Analyzer for Media-Konto erstellen.

## <a name="october-2020"></a>Oktober 2020

### <a name="animated-character-identification-improvements"></a>Verbesserungen bei der Identifizierung animierter Figuren  

Video Analyzer for Media unterstützt die Erkennung und Gruppierung von Figuren in animierten Inhalten mittels Integration der Cognitive Services-API „Custom Vision“. Wir haben dem KI-Algorithmus in Bezug auf die Erkennung von Figuren wesentlich verbessert. So lassen sich genauere Erkenntnisse erzielen und Figuren besser identifizieren.

### <a name="planned-video-analyzer-for-media-website-authenticatication-changes"></a>Geplante Änderungen bei der Authentifizierung bei der Video Analyzer for Media-Website

Ab 1. März 2021 können Sie sich nicht mehr über Facebook oder LinkedIn beim [Entwicklerportal](video-indexer-use-apis.md) der [Video Analyzer for Media](https://www.videoindexer.ai/)-Website registrieren und anmelden.

Sie können sich über einen der folgenden Anbieter registrieren und anmelden: Azure AD, Microsoft und Google.

> [!NOTE]
> Auf Video Analyzer for Media-Konten, die mit LinkedIn und Facebook verknüpft sind, kann nach dem 1. März 2021 nicht mehr zugegriffen werden. 
> 
> Sie sollten ein Azure AD-, Microsoft- oder Google-E-Mail-Konto, das Sie bereits besitzen, zum Video Analyzer for Media-Konto [einladen](invite-users.md), damit Sie weiterhin Zugriff haben. Sie können einen zusätzlichen Besitzer von unterstützten Anbietern hinzufügen, wie unter [Einladen](invite-users.md) beschrieben. <br/>
> Alternativ dazu können Sie ein kostenpflichtiges Konto erstellen und die Daten migrieren.

## <a name="august-2020"></a>August 2020

### <a name="mobile-design-for-the-video-analyzer-for-media-website"></a>Mobiles Design für die Video Analyzer for Media-Website

Die Video Analyzer for Media-Websiteoberfläche unterstützt jetzt mobile Geräte. Die Benutzeroberfläche passt sich als responsive Design an die Bildschirmgröße Ihres mobilen Geräts an (ausgenommen angepasste Benutzeroberflächen). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Verbesserte Barrierefreiheit und Fehlerbehebungen 

Als Teil von WCAG (Barrierefreiheitsrichtlinien für Webinhalte) wurde die Benutzerumgebung der Video Analyzer for Media-Website im Rahmen der Microsoft-Barrierefreiheits-Standards an Klasse C ausgerichtet. Mehrere Fehler im Zusammenhang mit Tastaturnavigation, programmgesteuertem Zugriff und Sprachausgabe wurden gelöst und Verbesserungen eingeführt. 

## <a name="july-2020"></a>Juli 2020

### <a name="ga-for-multi-language-identification"></a>Allgemeine Verfügbarkeit für die Erkennung mehrerer Sprachen

Die Erkennung mehrerer Sprachen geht von der Vorschau in die allgemeine Verfügbarkeit über und ist für den Einsatz in Produktionsumgebungen bereit.

Der Übergang von „Vorschauversion in allgemeine Verfügbarkeit“ hat keine Auswirkungen auf die Preisgestaltung.

### <a name="video-analyzer-for-media-website-improvements"></a>Verbesserungen der Video Analyzer for Media-Website

#### <a name="adjustments-in-the-video-gallery"></a>Anpassungen im Videokatalog

Es wurde eine neue Suchleiste für die Suche nach detaillierten Erkenntnissen mit zusätzlichen Filterfunktionen hinzugefügt. Die Suchergebnisse wurden ebenfalls verbessert.

Es gibt eine neue Listenansicht mit der Möglichkeit zum Sortieren und Verwalten eines Videoarchivs mit vielen Dateien.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Neues Panel zur einfachen Auswahl und Konfiguration

Es wurde ein seitliches Panel für die einfache Auswahl und Benutzerkonfiguration hinzugefügt, über das Sie schnell und einfach ein Konto erstellen und freigeben sowie Einstellungen konfigurieren können.

In diesem Panel können Sie auch Benutzereinstellungen verwalten und Hilfe erhalten.

## <a name="june-2020"></a>Juni 2020

### <a name="search-by-topics"></a>Suchen nach Themen

Sie können jetzt die Such-API verwenden, um nach Videos mit bestimmten Themen zu suchen (nur API).

Themen werden als Teil von `textScope` hinzugefügt (optionaler Parameter). Ausführliche Informationen finden Sie unter [API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos).  

### <a name="labels-enhancement"></a>Bezeichnungserweiterung

Der Bezeichnungs-Tagger wurde aktualisiert und enthält jetzt mehr visuelle Bezeichnungen, die identifiziert werden können.

## <a name="may-2020"></a>Mai 2020

### <a name="video-analyzer-for-media-deployed-in-the-east-us"></a>Video Analyzer for Media in der Region „USA, Osten“ verfügbar

Sie können jetzt ein kostenpflichtiges Video Analyzer for Media-Konto in der Region „USA, Osten“ erstellen.
 
### <a name="video-analyzer-for-media-url"></a>Video Analyzer for Media-URL

Regionale Video Analyzer for Media-Endpunkte wurden vereinheitlich und beginnen nun alle mit „www“. Keine Aktion erforderlich.

Ab sofort greifen Sie unabhängig davon, ob Sie Widgets einbetten oder sich bei Video Analyzer for Media-Webanwendungen anmelden, auf „www.videoindexer.ai“ zu.

Beim Aufruf von wus.videoindexer.ai erfolgt eine Umleitung zur www-Adresse. Weitere Informationen finden Sie im Artikel zum [Einbetten von Video Analyzer for Media-Widgets in Ihre Apps](video-indexer-embed-widgets.md).

## <a name="april-2020"></a>April 2020

### <a name="new-widget-parameters-capabilities"></a>Neue Funktionen für Widgetparameter

Das Widget **Insights** weist neue Parameter auf: `language` und `control`.

Das Widget **Player** weist den neuen Parameter `locale` auf. Die Parameter `locale` und `language` steuern die Sprache des Players.

Weitere Informationen finden Sie im Abschnitt [Widgettypen](video-indexer-embed-widgets.md#widget-types). 

### <a name="new-player-skin"></a>Neue Playerskin

Eine neue Playerskin mit aktualisiertem Design wurde herausgegeben.

### <a name="prepare-for-upcoming-changes"></a>Vorbereitung auf bevorstehende Änderungen

* Heute geben die folgenden APIs ein Kontoobjekt zurück:

    * [Create-Paid-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    Das Account-Objekt weist ein Feld `Url` auf, das auf den Speicherort der [Video Analyzer for Media-Website](https://www.videoindexer.ai/) verweist.
Für kostenpflichtige Konten verweist das Feld `Url` zurzeit auf eine interne URL anstelle der öffentlichen Website.
In den nächsten Wochen werden wir dies ändern und die URL der [Video Analyzer for Media-Website](https://www.videoindexer.ai/) für alle Konten zurückgeben (Testkonten und kostenpflichtige Konten).

    Verwenden Sie nicht die internen URLs, Sie sollten die [öffentlichen Video Analyzer for Media-APIs](https://api-portal.videoindexer.ai/) verwenden.
* Wenn Sie Video Analyzer for Media-URLs in Ihre Anwendungen einbetten und die URLs nicht auf die [Video Analyzer for Media-Website](https://www.videoindexer.ai/) oder den Video Analyzer for Media-API-Endpunkt (`https://api.videoindexer.ai`) sondern stattdessen auf einen regionalen Endpunkt verweisen (beispielsweise `https://wus2.videoindexer.ai`), generieren Sie die URLs erneut.

   Dies kann auf einer der folgenden Weisen erfolgen:

    * Ersetzen der URL durch eine URL, die auf die Video Analyzer for Media-Widget-APIs verweist (beispielsweise das [Widget „Kognitive Erkenntnisse“](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget))
    * Generieren einer neuen eingebetteten URL mit der Video Analyzer for Media-Website:
         
         Drücken Sie **Wiedergabe**, um zur Seite Ihres Videos zu gelangen > klicken Sie auf die Schaltfläche **&lt;/&gt; Einbinden** > kopieren Sie die URL in Ihre Anwendung:
   
    Die regionalen URLs werden nicht unterstützt und werden in den nächsten Wochen blockiert.

## <a name="january-2020"></a>Januar 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Benutzerdefinierte Sprachunterstützung für weitere Sprachen

Video Analyzer for Media unterstützt jetzt benutzerdefinierte Sprachmodelle für `ar-SY`, `en-UK` und `en-AU` (nur API).
 
### <a name="delete-account-timeframe-action-update"></a>Aktualisierung des Zeitrahmens der Aktion „Konto löschen“

Beim Ausführen der Aktion „Konto löschen“ wird das Konto jetzt innerhalb von 90 Tagen und nicht innerhalb von 48 Stunden gelöscht.
 
### <a name="new-video-analyzer-for-media-github-repository"></a>Neues GitHub-Repository für Video Analyzer for Media

Ein neues Video Analyzer for Media-GitHub mit verschiedenen Projekten, Leitfäden mit ersten Schritten und Codebeispielen ist jetzt unter https://github.com/Azure-Samples/media-services-video-indexer verfügbar.
 
### <a name="swagger-update"></a>Swagger-Update

Video Analyzer for Media hat **Authentifizierungen** und **Vorgänge** in einer einzigen [Video Analyzer for Media-OpenAPI-Spezifikation (Swagger)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation) vereinheitlicht. Entwickler finden die APIs im [Video Analyzer for Media-Entwicklerportal](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Dezember 2019

### <a name="update-transcript-with-the-new-api"></a>Aktualisieren des Transkripts mit der neuen API

Aktualisieren Sie einen bestimmten Abschnitt im Transkript mit der [Update-Video-Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index)-API.

### <a name="fix-account-configuration-from-the-video-analyzer-for-media-portal"></a>Korrigieren der Kontokonfiguration über das Video Analyzer for Media-Portal

Sie können jetzt die Konfiguration der Media Services-Verbindung aktualisieren, um sich selbst zu helfen bei Problemen wie: 

* Falsche Azure Media Services-Ressource
* Kennwortänderungen
* Verschieben von Media Services-Ressourcen zwischen Abonnements  

Um die Kontokonfiguration zu korrigieren, navigieren Sie im Video Analyzer for Media-Portal zur Registerkarte „Einstellungen > Konto“ (als Besitzer).

### <a name="configure-the-custom-vision-account"></a>Konfigurieren des Custom Vision-Kontos

Konfigurieren Sie das Custom Vision-Konto für kostenpflichtige Konten über das Video Analyzer for Media-Portal (zuvor wurde dies nur von der API unterstützt). Melden Sie sich dazu im Video Analyzer for Media-Portal an, und wählen Sie „Modellanpassung > Animierte Zeichen > Konfigurieren“ aus. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Szenen, Aufnahmen und Keyframes – jetzt in einem Erkenntnisbereich

Szenen, Aufnahmen und Keyframes werden jetzt zu einer Erkenntnis zusammengeführt, um Nutzung und Navigation zu erleichtern. Wenn Sie die gewünschte Szene auswählen, können Sie sehen, aus welchen Aufnahmen und Keyframes sie besteht. 

### <a name="notification-about-a-long-video-name"></a>Benachrichtigung über einen langen Videonamen

Wenn ein Videoname mehr als 80 Zeichen umfasst, zeigt Video Analyzer for Media beim Hochladen einen beschreibenden Fehler an.

### <a name="streaming-endpoint-is-disabled-notification"></a>Benachrichtigung über deaktivierten Streamingendpunkt

Wenn der Streamingendpunkt deaktiviert ist, zeigt Video Analyzer for Media einen beschreibenden Fehler auf der Playerseite an.

### <a name="error-handling-improvement"></a>Verbesserung der Fehlerbehandlung

Der Statuscode 409 wird jetzt von den APIs [Video erneut indizieren](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) und [Videoindex aktualisieren](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) zurückgegeben, falls ein Video aktiv indiziert wird, um zu verhindern, dass die aktuellen Änderungen der Neuindizierung versehentlich überschrieben werden.

## <a name="november-2019"></a>November 2019
 
* Unterstützung benutzerdefinierte koreanische Sprachmodelle

    Video Analyzer for Media unterstützt jetzt benutzerdefinierte Sprachmodelle in Koreanisch (`ko-KR`) sowohl in der API als auch im Portal. 
* Neue unterstützte Sprachen für die Spracherkennung (STT)

    Video Analyzer for Media-APIs unterstützen jetzt die Spracherkennung in Arabisch-Levantine (ar-SY), English UK-Dialekt (en-GB) und English Australiendialekt (en-AU).
    
    Für den Video Upload haben wir zh-HANS durch zh-CN ersetzt, zwar werden beide unterstützt, zh-CN wird jedoch empfohlen und ist genauer.
    
## <a name="october-2019"></a>Oktober 2019
 
* Suchen nach animierten Figuren im Katalog

    Wenn Sie animierte Figuren indizieren, können Sie sie jetzt im Videokatalog des Kontos danach suchen. Weitere Informationen finden Sie unter [Erkennung animierter Figuren](animated-characters-recognition.md).

## <a name="september-2019"></a>September 2019
 
Auf der IBC 2019 wurden mehrere Verbesserungen angekündigt:
 
* Erkennung animierter Figuren (öffentliche Vorschauversion)

    Die Fähigkeit zum Auffinden, Gruppieren und Erkennen von Figuren in animierten Inhalten mithilfe der Integration in Custom Vision. Weitere Informationen finden Sie unter [Erkennung animierter Figuren](animated-characters-recognition.md).
* Mehrsprachige Identifikation (öffentliche Vorschauversion)

    Erkennen von Segmenten in mehreren Sprachen in der Audiospur und Erstellen eines mehrsprachigen Transkripts auf ihrer Grundlage. Zu Anfang unterstützt: Englisch, Spanisch, Deutsch und Französisch. Weitere Informationen finden Sie unter [Automatisches Erkennen und Transkribieren mehrsprachiger Inhalte](multi-language-identification-transcription.md).
* Extraktion benannter Entitäten für Personen und Ort

    Extrahiert mithilfe der Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) Marken, Standorte und Personen aus Sprache und visuellem Text.
* Redaktionelle Typklassifizierung für Aufnahmen

    Markierung von Aufnahmen mit redaktionellen Typen wie Nahaufnahme, Halbtotale, zwei Personen, innen, außen usw. Weitere Informationen finden Sie unter [Erkennung von redaktionellen Aufnahmetypen](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Verbesserte Themendeduktion – jetzt mit Abdeckung von Stufe 2
    
    Das Themendeduktionsmodell unterstützt jetzt mehr Detailebenen der IPTC-Taxonomie. Sämtliche Details finden Sie unter [Azure Media Services new AI-powered innovation](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/) (Neue, KI-gestützte Innovationen in Azure Media Services).

## <a name="august-2019"></a>August 2019
 
### <a name="video-analyzer-for-media-deployed-in-uk-south"></a>Video Analyzer for Media in der Region „Vereinigtes Königreich, Süden“ verfügbar

Sie können jetzt ein kostenpflichtiges Video Analyzer for Media-Konto in der Region „Vereinigtes Königreich, Süden“ erstellen.

### <a name="new-editorial-shot-type-insights-available"></a>Neue redaktionelle Aufnahmearten verfügbar

Neue Tags, die Videoaufnahmen hinzugefügt werden, bieten redaktionelle „Aufnahmearten“, um sie mit gängigen redaktionellen Phrasen zu kennzeichnen, die im Workflow der Inhaltserstellung verwendet werden, z.B.: extreme Nahaufnahme (extreme closeup), Nahaufnahme (closeup), Weitwinkel (wide), Medium, Zwei-Personen-Aufnahme (two shot), Outdoor, Indoor, linkes Gesicht (left face) und rechtes Gesicht (right face) (verfügbar im JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Neue Extraktion von Personen- und Standortentitäten verfügbar

Video Analyzer for Media kennzeichnet benannte Orte und Personen über Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) aus der OCR und der Transkription des Videos. Video Analyzer for Media verwendet einen Machine Learning-Algorithmus, um zu erkennen, wann bestimmte Standorte (z. B. Eiffelturm) oder Personen (z. B. John Doe) in einem Video ausgesprochen werden.

### <a name="keyframes-extraction-in-native-resolution"></a>Extraktion von Keyframes in nativer Auflösung

Keyframes, die von Video Analyzer for Media extrahiert wurden, sind in der Originalauflösung des Videos verfügbar.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Allgemeine Verfügbarkeit für Trainieren von benutzerdefinierten Gesichtern aus Bildern

Das Trainieren von Gesichtern aus Bildern wurde vom Vorschaumodus in die allgemeine Verfügbarkeit überführt (verfügbar über eine API und im Portal).

> [!NOTE]
> Der Übergang von „Vorschauversion in allgemeine Verfügbarkeit“ hat keine Auswirkungen auf die Preisgestaltung.

### <a name="hide-gallery-toggle-option"></a>Option zum Ausblenden von „Katalog“

Sie können nun die Registerkarte „Katalog“ im Portal ausblenden (ähnlich dem Ausblenden der Registerkarte „Beispiele“).
 
### <a name="maximum-url-size-increased"></a>Maximale URL-Größe erhöht

Unterstützung für URL-Abfragezeichenfolgen von 4096 (anstelle von 2048) bei der Indizierung eines Videos.
 
### <a name="support-for-multi-lingual-projects"></a>Unterstützung für mehrsprachige Projekte

Projekte können nun auf Basis von Videos erstellt werden, die in verschiedenen Sprachen indiziert sind (nur API).

## <a name="july-2019"></a>Juli 2019

### <a name="editor-as-a-widget"></a>Editor als Widget

Der KI-Editor von Video Analyzer for Media ist nun als Widget verfügbar und kann in Kundenanwendungen eingebettet werden.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualisieren des benutzerdefinierten Sprachmodells aus der Untertiteldatei über das Portal

Kunden können im Portal auf der Seite zur Anpassung als Eingabe für Sprachmodelle die Dateiformate VTT, SRT und TTML angeben.

## <a name="june-2019"></a>Juni 2019

### <a name="video-analyzer-for-media-deployed-to-japan-east"></a>Video Analyzer for Media in der Region „Japan, Osten“ verfügbar

Sie können jetzt ein kostenpflichtiges Video Analyzer for Media-Konto in der Region „Japan, Osten“ erstellen.

### <a name="create-and-repair-account-api-preview"></a>Erstellen und Reparieren der Konto-API (Vorschau)

Eine neue API wurde hinzugefügt, die es Ihnen ermöglicht, [den Azure Media Services-Verbindungsendpunkt oder -Schlüssel zu aktualisieren](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services).

### <a name="improve-error-handling-on-upload"></a>Verbesserung der Fehlerbehandlung beim Hochladen 

Eine beschreibende Meldung wird im Falle einer Fehlkonfiguration des zugrunde liegenden Azure Media Services-Kontos zurückgegeben.

### <a name="player-timeline-keyframes-preview"></a>Vorschau der Keyframes in der Player-Zeitachse 

Sie können jetzt eine Bildvorschau für jeden Zeitpunkt auf der Zeitachse des Players anzeigen.

### <a name="editor-semi-select"></a>Editor-Teilauswahl

Sie können jetzt eine Vorschau aller Erkenntnisse anzeigen, die als Ergebnis der Auswahl eines bestimmten Erkenntniszeitraums im Editor ausgewählt sind.

## <a name="may-2019"></a>Mai 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualisieren des benutzerdefinierte Sprachmodells aus der Untertiteldatei

Die APIs [Benutzerdefiniertes Sprachmodell erstellen](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) und [Benutzerdefiniertes Sprachmodell aktualisieren](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) unterstützen jetzt VTT-, SRT-, und TTML-Dateiformate als Eingabe für Sprachmodelle.

Beim Aufrufen der [API „Video-Transkript aktualisieren“](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript) wird die Transkription automatisch hinzugefügt. Das dem Video zugeordnete Trainingsmodell wird ebenfalls automatisch aktualisiert. Informationen zum Anpassen und Trainieren Ihres Sprachmodells finden Sie unter [Anpassen eines Sprachmodells mit Video Analyzer for Media](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Neue Transkriptformate zum Herunterladen – TXT und CSV

Zusätzlich zum bereits unterstützten Untertitelformat (SRT VTT und TTML) unterstützt Video Analyzer for Media jetzt das Herunterladen des Transkripts im TXT- und CSV-Format.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](video-indexer-overview.md)
