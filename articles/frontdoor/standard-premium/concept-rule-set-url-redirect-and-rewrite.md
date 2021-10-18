---
title: URL-Umleitung und URL-Rewrite mit Azure Front Door Standard/Premium (Vorschau)
description: In diesem Artikel erfahren Sie, wie Azure Front Door URL-Umleitung und URL-Rewrite mit dem Azure Front Door-Regelsatz unterstützt.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 2e758560eed1ffb01117764f9399aa6f4f4b1395
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857548"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>URL-Umleitung und URL-Rewrite mit Azure Front Door Standard/Premium (Vorschau)

> [!Note]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

In diesem Artikel erfahren Sie, wie Azure Front Door Standard/Premium URL-Umleitung und URL-Rewrite in einem Regelsatz unterstützt.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>URL Redirect

Azure Front Door kann Datenverkehr auf jeder der folgenden Ebenen umleiten: Protokoll, Hostname, Pfad, Abfragezeichenfolge und Fragment. Diese Funktionen können für einzelne Microservices konfiguriert werden, da die Umleitung pfadbasiert ist. Mit der URL-Umleitung lässt sich die Anwendungskonfiguration vereinfachen, indem die Ressourcennutzung optimiert wird und neue Umleitungsszenarien wie etwa globale und pfadbasierte Umleitung unterstützt werden.

Sie können die URL-Umleitung mithilfe eines Regelsatzes konfigurieren.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Screenshot: Erstellen einer URL-Umleitung mit einem Regelsatz" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Umleitungstypen
Ein Umleitungstyp legt den Antwortstatuscode für die Clients fest und informiert so über den Zweck der Umleitung. Die folgenden Umleitungstypen werden unterstützt:

* **301 Moved permanently (301 Permanent verschoben)** : Gibt an, dass der Zielressource ein neuer dauerhafter URI zugewiesen wurde. Zukünftige Verweise auf diese Ressource verwenden einen der eingeschlossenen URIs. Verwenden Sie den Statuscode 301 für die HTTP-HTTPS-Umleitung.
* **302 Found (302 Gefunden)** : Gibt an, dass sich die Zielressource vorübergehend unter einem anderen URI befindet. Da sich die Umleitung gelegentlich ändern kann, sollte der Client weiterhin den effektiven Anforderungs-URI für künftige Anforderungen verwenden.
* **307 Temporary redirect (307 Temporäre Umleitung)** : Gibt an, dass sich die Zielressource vorübergehend unter einem anderen URI befindet. Der Benutzer-Agent darf die Anforderungsmethode NICHT ändern, wenn er eine automatische Umleitung zu diesem URI durchführt. Da die Umleitung im Laufe der Zeit geändert werden kann, sollte der Client weiterhin den ursprünglichen, effektiven Anforderungs-URI für künftige Anforderungen verwenden.
* **308 Permanent redirect (308 Dauerhafte Umleitung)** : Gibt an, dass der Zielressource ein neuer dauerhafter URI zugewiesen wurde. Zukünftige Verweise auf diese Ressource sollten einen der eingeschlossenen URIs verwenden.

### <a name="redirection-protocol"></a>Umleitungsprotokoll
Sie können das Protokoll festlegen, das für die Umleitung verwendet werden soll. Der häufigste Anwendungsfall der Umleitungsfunktion besteht darin, HTTP auf HTTPS-Umleitung festzulegen.

* **Nur HTTPS**: Legen Sie das Protokoll auf „Nur HTTPS“ fest, wenn der Datenverkehr von HTTP zu HTTPS umgeleitet werden soll. Es wird empfohlen, die Umleitung in Azure Front Door immer auf „Nur HTTPS“ zu setzen.
* **Nur HTTP**: Leitet die eingehende Anforderung zu HTTP um. Verwenden Sie diese Einstellung nur, wenn der Datenverkehr im HTTP-Format, d.h. nicht verschlüsselt, bleiben soll.
* **Übereinstimmungsanforderung** : Über diese Option wird das von der eingehenden Anforderung verwendete Protokoll beibehalten. Das bedeutet, dass eine HTTP-Anforderung nach der Umleitung im HTTP-Format und eine HTTPS-Anforderung im HTTPS-Format bleibt.

### <a name="destination-host"></a>Zielhost
Bei der Konfiguration einer Umleitung können Sie auch den Hostnamen und die Domäne für die Umleitungsanforderung ändern. Über dieses Feld können Sie festlegen, dass der Hostname in der URL für die Umleitung geändert wird, oder aber den Hostnamen aus der eingehenden Anforderung beibehalten. Mit anderen Worten: Über dieses Feld lassen sich alle an `https://www.contoso.com/*` gesendeten Anforderungen zu `https://www.fabrikam.com/*` umleiten.

### <a name="destination-path"></a>Zielpfad
Für den Fall, dass Sie das Pfadsegment einer URL im Zuge der Umleitung ersetzen möchten, können Sie in diesem Feld den neuen Pfadwert festlegen. Alternativ können Sie den Pfadwert als Teil der Umleitung auch beibehalten. Mit anderen Worten: Über dieses Feld lassen sich alle an `https://www.contoso.com/\*` gesendeten Anforderungen zu `https://www.contoso.com/redirected-site` umleiten.

### <a name="query-string-parameters"></a>Abfragezeichenfolge-Parameter
Sie haben die Möglichkeit, die Abfragezeichenfolge-Parameter in der umgeleiteten URL zu ersetzen. Um die vorhandene Abfragezeichenfolge in der eingehenden Anforderungs-URL zu ersetzen, legen Sie dieses Feld auf „Ersetzen“ und dann den entsprechenden Wert fest. Alternativ können Sie die ursprünglichen Abfragezeichenfolgen auch übernehmen, indem Sie das Feld auf „Beibehalten“ festlegen. Beispiel: Über dieses Feld lässt sich der gesamte an `https://www.contoso.com/foo/bar` gesendete Datenverkehr zu `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` umleiten. 

### <a name="destination-fragment"></a>Zielfragment
Als Zielfragment wird der Teil der URL nach „#“ bezeichnet, der von Browsern verwendet wird, um einen bestimmten Abschnitt einer Webseite anzuzeigen. Über dieses Feld können Sie festlegen, dass ein Fragment zur Umleitungs-URL hinzugefügt wird.

## <a name="url-rewrite"></a>URL Rewrite

Azure Front Door unterstützt URL-Rewrite, um den Pfad einer Anforderung umzuschreiben, die an den Ursprung weitergeleitet wird. Mit URL-Rewrite können Sie Bedingungen hinzufügen, um sicherzustellen, dass die URL oder die angegebenen Header nur dann erneut generiert werden, wenn bestimmte Bedingungen erfüllt sind. Diese Bedingungen basieren auf den Anforderungs- und Antwortinformationen.

Mit dieser Funktion können Sie Benutzer basierend auf Szenario, Gerätetyp und angefordertem Dateityp an verschiedene Ursprünge umleiten.

Sie können die URL-Umleitung mithilfe eines Regelsatzes konfigurieren.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Screenshot: Erstellen von URL-Rewrite mit einem Regelsatz" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Quellmuster

Das Quellmuster ist der URL-Pfad in der zu ersetzenden Quellanforderung. Derzeit verwendet das Quellmuster eine auf Präfixen basierende Übereinstimmung. Verwenden Sie einen Schrägstrich (/) als Quellmusterwert, um alle URL-Pfade abzugleichen.

Bei URL-Umschreibungsmustern wird nur der Pfad nach der Routenkonfiguration "Muster, die übereinstimmen" berücksichtigt. Wenn Sie z. B. das folgende Format für die eingehende URL haben `<Frontend-domain>/<route-patterns-to-match-path>/<Rule-URL-Rewrite-Source-pattern>`, wird nur `/<Rule-URL-Rewrite-Source-pattern>` von der Regel-Engine als Quellmuster berücksichtigt, das umgeschrieben werden soll. Wenn Sie also eine URL-Umschreibungsregel mit Quellmusterübereinstimmung haben, wird das Format für die ausgehende URL `<Frontend-domain>/<route-patterns-to-match-path>/<Rule-URL-Rewrite-destination>` sein.

Für Szenarien, in denen das Segment `/<route-patterns-to-match-path` des URL-Pfads entfernt werden muss, setzen Sie den Ursprungspfad der Ursprungsgruppe in der Routenkonfiguration auf `/`.

### <a name="destination"></a>Destination

Sie können den Zielpfad definieren, der für das erneute Generieren verwendet werden soll. Der Zielpfad überschreibt das Quellmuster.

### <a name="preserve-unmatched-path"></a>Nicht übereinstimmenden Pfad beibehalten

Mit „Nicht übereinstimmenden Pfad beibehalten“ können Sie den restlichen Pfad nach dem Quellmuster an den neuen Pfad anfügen.

Beispiel: Sie legen **„Nicht übereinstimmenden Pfad beibehalten“ auf „Ja“** fest.
* Wenn die eingehende Anforderung `www.contoso.com/sub/1.jpg` lautet, wird das Quellmuster auf `/` und das Ziel auf `/foo/` festgelegt, und der Inhalt wird aus `/foo/sub/1` vom Ursprung bereitgestellt.

* Wenn die eingehende Anforderung `www.contoso.com/sub/image/1.jpg` lautet, wird das Quellmuster auf `/sub/` und das Ziel auf `/foo/` festgelegt, und der Inhalt wird aus `/foo/image/1.jpg` vom Ursprung bereitgestellt.

Beispiel: Sie legen **„Nicht übereinstimmenden Pfad beibehalten“ auf „Nein“** fest.
* Wenn die eingehende Anforderung `www.contoso.com/sub/image/1.jpg` lautet, wird das Quellmuster auf `/sub/` und das Ziel auf `/foo/2.jpg` festgelegt. Der Inhalt wird immer aus `/foo/2.jpg` vom Ursprung bereitgestellt, unabhängig davon, welche Pfade in `wwww.contoso.com/sub/` verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Regelsätze in Azure Front Door Standard/Premium](concept-rule-set.md).
