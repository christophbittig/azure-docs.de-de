---
title: Berichte in Azure Front Door Standard/Premium (Vorschau)
description: Dieser Artikel erläutert die Funktionsweise der Berichterstellung in Azure Front Door.
services: frontdoor
author: jessie-jyy
ms.service: frontdoor
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: yuajia
ms.openlocfilehash: 8e43bf01be44c8d82bb8b5f9d466552fc9d7ad17
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2021
ms.locfileid: "113595001"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Berichte in Azure Front Door Standard/Premium (Vorschau)

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Analyseberichte in Azure Front Door Standard/Premium bieten eine integrierte Übersicht über das Verhalten von Azure Front Door sowie entsprechende Web Application Firewall-Metriken. Sie können auch für die weitere Problembehandlung und das Debuggen Zugriffsprotokolle nutzen. Azure Front Door-Analyseberichte umfassen Berichte zum Datenverkehr und zur Sicherheit.

| Berichte | Details |
|---------|---------|
| Overview of key metrics (Übersicht über Schlüsselmetriken) | Zeigt allgemeine Daten an, die von Azure Front Door-Edges an Clients gesendet wurden<br/>– Spitzenbandbreite<br/>– Anforderungen <br/>– Cachetrefferquote<br/> – Gesamtlatenz<br/>– 5xx-Fehlerrate |
| Traffic by Domain (Datenverkehr nach Domäne) | – Bietet eine Übersicht über alle Domänen im Profil<br/>– Aufschlüsselung der Daten, die vom AFDS-Edge an den Client übertragen wurden<br/>– Anforderungen insgesamt<br/>– 3xx-/4xx-/5xx-Antwortcode nach Domäne |
| Traffic by location (Datenverkehr nach Standort) | – Zeigt eine Kartenansicht der Anforderungen und Verwendung nach Top-Land/Region an<br/>– Trendansicht der Top-Länder/-Regionen |
| Verwendung | – Zeigt die Datenübertragung vom Azure Front Door-Edge an Clients an<br/>– Datenübertragung vom Ursprung zum AFDS-Edge<br/>– Bandbreite von AFDS-Edge zu Clients<br/>– Bandbreite vom Ursprung zum AFDS-Edge<br/>– Anforderungen<br/>– Gesamtlatenz<br/>– Trend der Anforderungsanzahl nach HTTP-Statuscode |
| Caching | – Zeigt die Cachetrefferquote nach Anforderungsanzahl an<br/>– Trendansicht der Anforderungen mit Cachetreffern und -fehlern |
| Top URL (Top-URL) | – Zeigt die Anforderungsanzahl an <br/>– Übertragene Daten <br/>– Cachetrefferquote <br/>– Verteilung der Antwortstatuscodes für die 50 am häufigsten angeforderten Ressourcen |
| Top-Referrer | – Zeigt die Anforderungsanzahl an <br/>– Übertragene Daten <br/>– Cachetrefferquote <br/>– Verteilung der Antwortstatuscodes für die Top 50-Referrer, die Datenverkehr generieren |
| Top User Agent (Top-Benutzer-Agent) | – Zeigt die Anforderungsanzahl an <br/>– Übertragene Daten <br/>– Cachetrefferquote <br/>– Verteilung der Antwortstatuscodes für die Top 50-Benutzer-Agents, die zum Anfordern von Inhalten verwendet wurden |

| Sicherheitsberichte | Details |
|---------|---------|
| Overview of key metrics (Übersicht über Schlüsselmetriken) | – Zeigt übereinstimmende WAF-Regeln an<br/>– Übereinstimmende OWASP-Regeln<br/>– Übereinstimmende Botregeln<br/>– Übereinstimmende benutzerdefinierte Regeln |
| Metrics by dimensions (Metriken nach Dimension) | – Aufschlüsselung der übereinstimmenden WAF-Regeltrends nach Aktion<br/>– Ringdiagramme der Ereignisse nach Regelsatztyp und der Ereignisse nach Regelgruppe<br/>– Listenförmige Aufschlüsselung der wichtigsten Ereignisse nach Regel-ID, Land/Region, IP-Adresse, URL und Benutzer-Agent  |

> [!NOTE]
> Sicherheitsberichte sind nur mit der Azure Front Door Premium-SKU verfügbar.

Die meisten Berichte basieren auf Zugriffsprotokollen und werden für Kunden in Azure Front Door kostenlos angeboten. Der Kunde muss keine Zugriffsprotokolle aktivieren und keine Konfiguration ausführen, um diese Berichte anzuzeigen. Auf die Berichte kann über das Portal und die API zugegriffen werden. Der CSV-Download wird ebenfalls unterstützt. 

Berichte unterstützen jeden ausgewählten Datumsbereich aus den letzten 90 Tagen. Mit Datenpunkten für alle 5 Minuten, jede Stunde oder jeden Tag, basierend auf dem ausgewählten Datumsbereich. Normalerweise können Sie Daten mit einer Verzögerung von bis zu einer Stunde und gelegentlich mit einer Verzögerung von bis zu einigen Stunden anzeigen. 

## <a name="access-reports-using-the-azure-portal"></a>Zugreifen auf Berichte mit dem Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie Ihr Azure Front Door Standard/Premium-Profil aus.

1. Wählen Sie im Navigationsbereich unter „Analyse“ **Berichte** oder *Sicherheit* aus.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Screenshot der Landing Page „Berichte“":::

1. Es sind sieben Registerkarten für unterschiedliche Dimensionen vorhanden. Wählen Sie die gewünschte Dimension aus.

   * Traffic by Domain (Datenverkehr nach Domäne)
   * Verwendung 
   * Traffic by location (Datenverkehr nach Standort)
   * Cache
   * Top URL (Top-URL)
   * Top-Referrer
   * Top User Agent (Top-Benutzer-Agent)

1. Nachdem Sie die Dimension ausgewählt haben, können Sie verschiedene Filter auswählen.
  
    1. **Daten anzeigen für**: Wählen Sie den Datumsbereich aus, für den Sie den Datenverkehr nach Domäne anzeigen möchten. Verfügbare Bereiche:
        
        * Letzte 24 Stunden
        * Letzte 7 Tage
        * Letzte 30 Tage
        * Letzte 90 Tage
        * This month
        * Letzter Monat
        * Benutzerdefiniertes Datum

         Standardmäßig werden Daten für die letzten sieben Tage angezeigt. Auf Registerkarten mit Liniendiagrammen entspricht die Granularität der Daten den Datumsbereichen, die Sie als Standardverhalten ausgewählt haben. 
    
        * 5 Minuten: Für Datumsbereiche, die kleiner oder gleich 24 Stunden sind, ein Datenpunkt alle 5 Minuten
        * Nach Stunde: Für Datumsbereiche zwischen 24 Stunden und 30 Tagen ein Datenpunkt jede Stunde
        * Nach Tag: Für Datumsbereiche, die größer als 30 Tage sind, ein Datenpunkt pro Tag

        Sie können immer mithilfe von Aggregation die Standardgranularität der Aggregation ändern. Hinweis: „5 Minuten“ kann nicht für Datumsbereiche verwendet werden, die länger als 14 Tage sind. 

    1. **Standort**: Wählen Sie einen oder mehrere Clientstandorte nach Land/Region aus. Länder/Regionen sind nach sechs Regionen gruppiert: Nordamerika, Asien, Europa, Afrika, Ozeanien und Südamerika. Ziehen Sie die [Länder/Regionen-Zuordnung](https://en.wikipedia.org/wiki/Subregion) zurate. Standardmäßig sind alle Länder ausgewählt.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Screenshot der Berichte für die Dimension „Standorte“":::
   
    1. **Protokoll**: Wählen Sie „HTTP“ oder „HTTPS“ aus, um Datenverkehrsdaten anzuzeigen.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Screenshot der Berichte für die Dimension „Protokoll“":::
    
    1. **Domänen**: Wählen Sie einen oder mehrere Endpunkte oder benutzerdefinierte Domänen aus. Standardmäßig sind alle Endpunkte und benutzerdefinierten Domänen ausgewählt. 
    
        * Wenn Sie einen Endpunkt oder eine benutzerdefinierte Domäne in einem Profil löschen und dann denselben Endpunkt oder diese Domäne in einem anderen Profil neu erstellen, gilt der Endpunkt als zweiter Endpunkt.  
        * Wenn Sie Berichte nach benutzerdefinierter Domäne anzeigen: Wenn Sie eine benutzerdefinierte Domäne löschen und sie an einen anderen Endpunkt binden, werden der Endpunkt und die gelöschte benutzerdefinierte Domäne als eine benutzerdefinierte Domäne behandelt. Wenn Sie Domänen nach Endpunkt anzeigen, werden sie als separate Elemente behandelt. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Screenshot der Berichte für die Dimension „Domänen“":::

1. Wenn Sie die Daten in eine CSV-Datei exportieren möchten, klicken Sie auf der ausgewählten Registerkarte auf den Link *CSV herunterladen*.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Screenshot: Herunterladen von CSV-Datei für Berichte":::

### <a name="key-metrics-for-all-reports"></a>Schlüsselmetriken für alle Berichte

| Metrik | BESCHREIBUNG |
|---------|---------|
| Übertragene Daten | Zeigt Daten an, die für den ausgewählten Zeitraum sowie für die ausgewählten Clientstandorte, Domänen und Protokolle von AFDS-Edge-POPs an den Client übertragen werden. |
| Spitzenbandbreite | Die Spitzenbreite in Bits pro Sekunde bei der Übertragung von Azure Front Door-Edge-POPs an Clients für den ausgewählten Zeitraum sowie für die ausgewählten Clientstandorte, Domänen und Protokolle | 
| Anzahl von Anforderungen | Die Anzahl der Anforderungen, die von AFDS-Edge-POPs als Antwort an den Client gesendet wurden, für den ausgewählten Zeitraum sowie die ausgewählten Clientstandorte, Domänen und Protokolle |
| Cachetrefferquote | Der Prozentsatz aller zwischenspeicherbaren Anforderungen, für die AFDS den Inhalt von seinen Edge-Caches gesendet hat, für den ausgewählten Zeitraum sowie die ausgewählten Clientstandorte, Domänen und Protokolle |
| 5xx-Fehlerrate | Der Prozentsatz der Anforderungen, für die der HTTP-Statuscode „5xx“ gelautet hat, für den ausgewählten Zeitraum sowie die ausgewählten Clientstandorte, Domänen und Protokolle |
| Gesamtlatenz | Die durchschnittliche Latenz aller Anforderungen für den ausgewählten Zeitraum sowie die ausgewählten Clientstandorte, Domänen und Protokolle. Die Latenz für die einzelnen Anforderungen wird als Gesamtzeit gemessen, die vom Empfang der Clientanforderung durch Azure Front Door bis zum letzten von Azure Front Door an den Client gesendeten Antwortbyte vergeht. |

## <a name="traffic-by-domain"></a>Traffic by Domain (Datenverkehr nach Domäne)

Traffic by Domain (Datenverkehr nach Domäne) bietet eine Rasteransicht aller Domänen unter dem Azure Front Door-Profil. In diesem Bericht können Sie Folgendes anzeigen: 
* Requests
* Von Azure Front Door an den Client übertragene Daten
* Anforderungen mit dem Statuscode (3xx, 4xx und 5xx) der einzelnen Domänen

Zu den Domänen zählen Endpunkte und benutzerdefinierte Domänen, wie im Abschnitt über den Zugriff auf die Berichte erläutert.  

Wenn die Metriken Ihre Erwartungen nicht erfüllen, können Sie zu anderen Registerkarten wechseln, um weitere Informationen zu erhalten oder das Zugriffsprotokoll anzuzeigen. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Screenshot der Landing Page für Berichte":::


## <a name="usage"></a>Verwendung

Dieser Bericht zeigt die Trends des Datenverkehrs und der Antwortstatuscodes nach unterschiedlichen Dimensionen an, darunter die folgenden:

* In einem Liniendiagramm Daten, die vom Edge zum Client und vom Ursprung zum Edge übertragen wurden 

* In einem Liniendiagramm Daten, die vom Edge zum Client übertragen wurden, nach Protokoll 

* In einem Liniendiagramm die Anzahl der Anforderungen vom Edge zu den Clients  

* In einem Liniendiagramm die Anzahl der Anforderungen vom Edge an die Clients nach den Protokollen HTTP und HTTPS 

* In einem Liniendiagramm die Bandbreite vom Edge zu den Clients 

* Die Gesamtlatenz, d. h. die Gesamtzeit zwischen dem Empfang der Clientanforderung in Front Door und dem letzten von Front Door an den Client gesendeten Antwortbyte

* In einem Liniendiagramm die Anzahl der Anforderungen vom Edge an die Clients nach HTTP-Statuscode. Jede Anforderung generiert einen HTTP-Statuscode. Der HTTP-Statuscode wird im unformatierten Protokoll in „HTTPStatusCode“ angezeigt. Der Statuscode beschreibt, wie die Anforderung vom CDN-Edge behandelt wurde. Beispielsweise gibt ein 2xx-Statuscode an, dass die Anforderung erfolgreich an einen Client übermittelt wurde. Ein 4xx-Statuscode zeigt an, dass ein Fehler aufgetreten ist. Weitere Informationen zu HTTP-Statuscodes finden Sie unter List of HTTP status codes (Liste mit HTTP-Statuscodes). 

* Die Anzahl der Anforderungen vom Edge an die Clients nach HTTP-Statuscode. Der Prozentsatz der Anforderungen nach HTTP-Statuscode unter allen Anforderungen im Raster. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Screenshot der Berichte nach Verwendung" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Traffic by location (Datenverkehr nach Standort)

In diesem Bericht werden die Top 50-Standorte nach Land/Region der Besucher angezeigt, die am häufigsten auf Ihre Ressource zugreifen. Der Bericht enthält auch eine Aufschlüsselung der Metriken nach Land/Region und bietet Ihnen einen Überblick über die Länder/Regionen, in denen der größte Datenverkehr generiert wird. Zudem können Sie erkennen, welches Land bzw. welche Region eine höhere Cachetrefferquote oder eine höhere Anzahl von 4xx-/5xx-Fehlercodes aufweist.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Screenshot der Berichte nach Standorten" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

Folgendes ist in den Berichten enthalten:

* Eine Weltkarte der Top 50-Länder/-Regionen nach gesendeten Daten oder Anforderungen Ihrer Wahl.
* Zwei Liniendiagramme mit Trendansichten der Top 5-Länder/-Regionen nach gesendeten Daten und Anforderungen Ihrer Wahl. 
* Ein Raster der Top-Länder/-Regionen, in dem die von AFDS an Clients gesendeten Daten, Prozentsätze der gesendeten Daten für alle Länder/Regionen, Anforderungen, Anforderungen in Prozent für alle Länder/Regionen, die Cachetrefferquote, der 4xx-Antwortcode und der 5xx-Antwortcode angezeigt werden.

## <a name="caching"></a>Caching

Zwischenspeicherungsberichte bieten eine Diagrammansicht der Cachetreffer/-fehler und der Cachetrefferquote auf der Grundlage von Anforderungen. Diese Schlüsselmetriken erläutern, wie CDN Inhalt zwischenspeichert, da die höchste Leistung aus Cachetreffern resultiert. Sie können die Geschwindigkeit der Datenübermittlung optimieren, indem Sie Cachefehler minimieren. Dieser Bericht enthält Folgendes:

* Trend der Anzahl der Cachetreffer und -fehler in einem Liniendiagramm

* Cachetrefferquote in einem Liniendiagramm

„Cachetreffer“/„Cachefehler“ gibt die Anzahl der Anforderungen mit Cachetreffern bzw. Cachefehlern für Clientanforderungen an.

* Treffer: Die Clientanforderungen, die direkt von Azure CDN Edge-Servern übermittelt werden. Dies sind die Anforderungen, deren Wert für „CacheStatus“ in unformatierten Protokollen „HIT“, „PARTIAL_HIT“ oder „REMOTE_HIT“ lautet. 

* Fehler: Die Clientanforderungen, die von Azure CDN Edge-Servern bereitgestellt werden, die Inhalte vom Ursprung abrufen. Dies sind die Anforderungen, deren Wert für das Feld „CacheStatus“ in unformatierten Protokollen „MISS“ lautet. 

**Cachetrefferquote** gibt den Prozentsatz zwischengespeicherter Anforderungen an, die vom Edge direkt übermittelt werden. Die Formel für die Cachetrefferquote lautet: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%`. 

In diesem Bericht werden Zwischenspeicherungsszenarien berücksichtigt, und Anforderungen, die die folgenden Bedingungen erfüllt haben, werden in die Berechnung einbezogen. 

* Der angeforderte Inhalt wurde auf dem POP zwischengespeichert, der der anfordernden Person oder Origin Shield am nächsten war. 

* Teilweise zwischengespeicherte Inhalte für die Objektsegmentierung

Alle folgenden Fälle sind ausgenommen: 

* Anforderungen, die aufgrund des Regelsatzes abgelehnt wurden

* Anforderungen, die einen übereinstimmenden Regelsatz enthalten, in dem Zwischenspeicherung deaktiviert wurde 

* Von WAF blockierte Anforderungen 

* Origin-Antwortheader geben an, dass keine Zwischenspeicherung erfolgen soll. Beispielsweise verhindern die Header "Cache-Control: private", "Cache-Control: no-cache" oder "Pragma: no-cache", dass ein Medienobjekt zwischengespeichert wird. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Screenshot der Berichte für Zwischenspeicherung":::

## <a name="top-urls"></a>Top URL (Top-URL)

Mit „Top-URL“ (Top-URL) können Sie den Umfang des Datenverkehrs anzeigen, der über einen bestimmten Endpunkt oder über eine bestimmte benutzerdefinierte Domäne angefallen ist. Es werden Daten für die 50 am häufigsten angeforderten Ressourcen in einem beliebigen Zeitraum der letzten 90 Tage angezeigt. Beliebte URLs werden mit den folgenden Werten angezeigt. Der Benutzer kann die URLs nach Anforderungsanzahl, Anforderungsprozentsatz, übertragenen Daten und Prozentsatz der übertragenen Daten sortieren. Alle Metriken werden pro Stunde aggregiert, und sie können je nach ausgewähltem Zeitrahmen variieren. Die URL entspricht dem Wert von „RequestUri“ im Zugriffsprotokoll. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Screenshot der Berichte für „Top-URLs“":::

* Die URL gibt den vollständigen Pfad der angeforderten Ressource im Format `http(s)://contoso.com/index.html/images/example.jpg` an. 
* Anforderungsanzahl 
* Prozentualer Anteil der Anforderungen an der Gesamtzahl der von Azure Front Door übermittelten Anforderungen 
* Übertragene Daten 
* Prozentsatz der übertragenen Daten 
* Cachetrefferquote in Prozent
* Anforderungen mit Antwortcode 4xx
* Anforderungen mit Antwortcode 5xx

> [!NOTE]
> Die Top-URLs können sich im Laufe der Zeit ändern. Um eine genaue Liste der Top 50-URLs zu erhalten, zählt Azure Front Door alle URL-Anforderungen pro Stunde und protokolliert die laufende Summe im Tagesverlauf. Die Position der URLs in der Liste der 500 URLs kann sich im Laufe des Tages ändern, oder URLs werden aus der Liste entfernt. Daher ist die Gesamtzahl dieser URL ein Schätzwert.  
>
> Die Position der Top 50-URLs in der Liste kann sich ändern, sie werden jedoch selten aus der Liste entfernt. Deshalb sind die Zahlen für die Top-URLs in der Regel zuverlässig. Wenn eine URL aus der Liste entfernt wird und im Laufe des Tages wieder in sie aufgenommen wird, wird die Anforderungsanzahl in dem Zeitraum, in dem sie in der Liste fehlen, anhand der Anforderungsanzahl der URLs geschätzt, die in diesem Zeitraum angezeigt werden.  
>
> Die gleiche Logik gilt für „Top User Agent“ (Top-Benutzer-Agent). 

## <a name="top-referrers"></a>Top-Referrer

Mit „Top Referrer“ können Kunden die 50 Referrer anzeigen, die an einem bestimmten Endpunkt oder in einer bestimmten benutzerdefinierten Domäne die meisten Anforderungen erzeugt haben. Sie können Daten für einen beliebigen Zeitraum innerhalb der letzten 90 Tage anzeigen. Ein Referrer gibt die URL an, von der eine Anforderung generiert wurde. Referrer können von einer Suchmaschine oder anderen Websites stammen. Wenn ein Benutzer eine URL (z. B. http(s)://contoso.com/index.html) direkt in der Adresszeile eines Browsers eingibt, lautet der Referrer für die angeforderte URL „Empty“. Der Bericht „Top Referrer“ enthält die folgenden Werte. Sie können sie nach Anforderungsanzahl, Anforderungsprozentsatz, übertragenen Daten und Prozentsatz der übertragenen Daten sortieren. Alle Metriken werden pro Stunde aggregiert, und sie können je nach ausgewähltem Zeitrahmen variieren. 

* Referrer entspricht dem Wert von „Referrer“ in unformatierten Protokollen 
* Anforderungsanzahl 
* Prozentualer Anteil der Anforderungen an der Gesamtanzahl der Anforderungen, die von Azure CDN im ausgewählten Zeitraum übermittelt wurden 
* Übertragene Daten 
* Prozentsatz der übertragenen Daten 
* Cachetrefferquote in Prozent
* Anforderungen mit Antwortcode 4xx
* Anforderungen mit Antwortcode 5xx

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Screenshot der Berichte für „Top-Referrer“":::

## <a name="top-user-agent"></a>Top User Agent (Top-Benutzer-Agent)

Dieser Bericht bietet Ihnen eine grafische Ansicht und Statistikansicht der Top 50 Benutzer-Agents, die zum Anfordern von Inhalten verwendet wurden. Beispiel:
* Mozilla/5.0 (Windows NT 10.0, WOW64) 
* AppleWebKit/537.36 (KHTML, z. B. Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

In einem Raster werden die Anzahl der Anforderungen, der prozentuale Anteil der Anforderungen, die übertragenen Daten und der Prozentsatz der übertragenen Daten, die Cachetrefferquote in Prozent, die Anforderungen mit Antwortcode 4xx und die Anforderungen mit Antwortcode 5xx angezeigt. „Top User Agent“ (Top-Benutzer-Agent) entspricht dem Wert von „UserAgent“ in Zugriffsprotokollen.

## <a name="security-report"></a>Sicherheitsbericht

Dieser Bericht bietet Ihnen eine grafische Ansicht und Statistikansicht von WAF-Mustern nach verschiedenen Dimensionen.

| Dimensionen | BESCHREIBUNG |
|---------|---------|
| Übersichtsmetriken – Matched WAF rules (Übereinstimmende WAF-Regeln) | Anforderungen, die benutzerdefinierten WAF-Regeln, verwalteten WAF-Regeln und dem Bot-Manager entsprechen. |
| Übersichtsmetriken – Blockierte Anforderungen | Der Prozentsatz der durch WAF-Regeln blockierten Anforderungen im Verhältnis zu allen Anforderungen, die den WAF-Regeln entsprochen haben. |
| Übersichtsmetriken – Matched Managed Rules (Übereinstimmende verwaltete Regeln) | Vier Liniendiagramme mit Trendansichten für Anforderungen vom Typ „Blockieren“, „Protokollieren“, „Zulassen“ und „Umleiten“ |
| Übersichtsmetriken – Matched Custom Rule (Übereinstimmende benutzerdefinierte Regel) | Anforderungen, die benutzerdefinierten WAF-Regeln entsprechen |
| Übersichtsmetriken – Matched Bot Rule (Übereinstimmende Botregel) | Anforderungen, die dem Bot-Manager entsprechen |
| WAF request trend by action (Trend der WAF-Anforderungen nach Aktion) | Vier Liniendiagramme mit Trendansichten für Anforderungen vom Typ „Blockieren“, „Protokollieren“, „Zulassen“ und „Umleiten“ |
| Events by Rule Type (Ereignisse nach Regeltyp) | Ringdiagramm der Verteilung der WAF-Anforderungen nach Regeltyp, z. B. Botregel, benutzerdefinierte Regeln und verwaltete Regeln |
| Events by Rule Group (Ereignisse nach Regelgruppe) | Ringdiagramm der Verteilung der WAF-Anforderungen nach Regelgruppe |
| Requests by actions (Anforderungen nach Aktion) | Eine Tabelle mit Anforderungen nach Aktion, in absteigender Reihenfolge |
| Requests by top Rule IDs (Anforderungen nach Top-Regel-IDs) | Eine Tabelle mit Anforderungen nach Top 50-Regel-IDs, in absteigender Reihenfolge |
| Anforderungen nach Top-Ländern/-Regionen |  Eine Tabelle mit Anforderungen nach Top 50-Ländern/-Regionen, in absteigender Reihenfolge. |
| Requests by top client IPs (Anforderungen nach Top-Client-IP-Adressen) |  Eine Tabelle mit Anforderungen nach den Top 50-IP-Adressen, in absteigender Reihenfolge |
| Requests by top Request URL (Anforderungen nach Top-Anforderungs-URL) |  Eine Tabelle mit Anforderungen nach den Top 50-URLs, in absteigender Reihenfolge |
| Request by top Hostnames (Anforderungen nach Top-Hostnamen) | Eine Tabelle mit Anforderungen nach den Top 50-Hostnamen, in absteigender Reihenfolge |
| Requests by top user agents (Anforderungen nach Top-Benutzer-Agents) | Eine Tabelle mit Anforderungen nach den Top 50-Benutzer-Agents, in absteigender Reihenfolge |

## <a name="cvs-format"></a>CVS-Format

Sie können für verschiedene Registerkarten in Berichten CSV-Dateien herunterladen. In diesem Abschnitt werden die Werte in den einzelnen CSV-Dateien beschrieben.

### <a name="general-information-about-the-cvs-report"></a>Allgemeine Informationen zum CSV-Bericht

Jeder CSV-Bericht enthält einige allgemeine Informationen, und diese sind in allen CSV-Dateien verfügbar, mit Variablen, die auf dem Bericht basieren, den Sie herunterladen. 


| Wert | BESCHREIBUNG |
|---------|---------|
| Bericht | Der Berichtsname. | 
| Domänen | Die Liste der Endpunkte oder benutzerdefinierten Domänen für den Bericht |
| StartDateUTC | Der Beginn des Datumsbereichs, für den Sie den Bericht generiert haben, in koordinierter Weltzeit (UTC) |
| EndDateUTC | Das Ende des Datumsbereichs, für den Sie den Bericht generiert haben, in koordinierter Weltzeit (UTC) |
| GeneratedTimeUTC | Das Datum und die Uhrzeit der Berichtsgenerierung, in koordinierter Weltzeit (UTC) |
| Standort | Die Liste der Länder/Regionen, aus denen die Clientanforderungen stammen. Der Standardwert lautet „ALL“. Gilt nicht für den Sicherheitsbericht. |
| Protocol | Das Protokoll der Anforderung, HTTP oder HTTPS. Gilt nicht für „Top URL“ (Top-URL) und „Traffic by User Agent“ (Datenverkehr nach Benutzer-Agent) in Berichten und im Sicherheitsbericht. |
| Aggregation | Die Granularität der Datenaggregation in jeder Zeile: alle 5 Minuten, jede Stunde oder jeden Tag. Gilt nicht für „Traffic by Domain“ (Datenverkehr nach Domäne) „Top URL“ (Top-URL) und „Traffic by User Agent“ (Datenverkehr nach Benutzer-Agent) in Berichten und im Sicherheitsbericht. |

### <a name="data-in-traffic-by-domain"></a>Daten in „Traffic by Domain“ (Datenverkehr nach Domäne)

* Domain 
* TotalRequests 
* Cachetrefferquote 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Daten in „Datenverkehr nach Standort“

* Standort
* TotalRequests
* Request%
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Daten in „Verwendung“

Diese CSV-Datei enthält drei Berichte. Einen für das HTTP-Protokoll, einen für das HTTPS-Protokoll und einen für den HTTP-Statuscode. 

Die Berichte für HTTP und HTTPS nutzen dasselbe Dataset. 

* Zeit 
* Protocol 
* DataTransferred(bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Bericht für HTTP-Statuscode 

* Zeit 
* DataTransferred(bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Daten in „Zwischenspeicherung“ 

* Zeit
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Daten in „Top URL“ (Top-URL) 

* URL 
* TotalRequests 
* Request% 
* DataTransferred(bytes) 
* DataTransferred% 

### <a name="data-in-user-agent"></a>Daten in „Top User Agent“ (Top-Benutzer-Agent) 

* UserAgent 
* TotalRequests 
* Request% 
* DataTransferred(bytes) 
* DataTransferred% 

### <a name="security-report"></a>Sicherheitsbericht 

Der Bericht enthält sieben Tabellen, die jeweils die folgenden Felder aufweisen.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

Die sieben Tabellen enthalten Daten für Zeit, Regel-ID, Länder/Regionen, IP-Adresse, URL, Hostname und Benutzer-Agent. 

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Echtzeitüberwachungsmetriken in Azure Front Door Standard/Premium](how-to-monitor-metrics.md).
