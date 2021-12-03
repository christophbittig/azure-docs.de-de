---
title: 'Azure Front Door: Zwischenspeichern'
description: Dieser Artikel hilft Ihnen, das Verhalten von Azure Front Door Standard/Premium bei Verwendung von Routingregeln zu verstehen, bei denen Zwischenspeicherung aktiviert ist.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 687a18ff1f66f5bd14a76b620c4fda04e6b7a6f0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234975"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Zwischenspeichern mit Azure Front Door Standard/Premium (Vorschau)

> [!Note]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

In diesem Artikel wird das Verhalten der Routen und Regelsätze in Front Door Standard/Premium (Preview) bei aktivierter Zwischenspeicherung erläutert. Azure Front Door ist ein modernes Content Delivery Network (CDN) mit Beschleunigung dynamischer Websites und Lastenausgleich.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="request-methods"></a>Anforderungsmethoden

Nur die GET-Anforderungsmethode kann zwischengespeicherten Inhalt in Azure Front Door generieren. Alle anderen Anforderungsmethoden werden immer per Proxy über das Netzwerk gesendet.

## <a name="delivery-of-large-files"></a>Übermittlung großer Dateien

Front Door Standard/Premium (Preview) übermittelt große Dateien ohne Beschränkung der Dateigröße. Bei Azure Front Door Service kommt eine Technik namens Objektblockerstellung zum Einsatz. Wenn eine große Datei angefordert wird, ruft Azure Front Door Service kleinere Teile der Datei vom Ursprung ab. Nach dem Empfang einer vollständigen oder auf einen Bytebereich beschränkten Anforderung fordert eine Front Door-Umgebung die Datei in Blöcken von 8 MB vom Ursprung an.

Nachdem der Block in der Azure Front Door Service-Umgebung angekommen ist, wird er zwischengespeichert und sofort für den Benutzer bereitgestellt. Azure Front Door Service ruft den nächsten Block dann parallel dazu ab. Durch diesen Vorabruf wird sichergestellt, dass der Inhalt dem Benutzer immer einen Block voraus ist, sodass sich die Wartezeit reduziert. Dieser Prozess wird fortgesetzt, bis die gesamte Datei heruntergeladen wurde (falls angefordert) oder der Client die Verbindung beendet.

Weitere Informationen zur Bytebereichsanforderung finden Sie unter [RFC 7233](https://www.rfc-editor.org/info/rfc7233).
Alle Blöcke werden von Azure Front Door Service zwischengespeichert, sobald sie eingetroffen sind, sodass nicht die gesamte Datei im Cache von Front Door zwischengespeichert werden muss. Nachfolgende Anforderungen für die Datei oder Bytebereiche werden über den Cache verarbeitet. Wenn nicht alle Blöcke zwischengespeichert werden, werden mittels Vorabruf Blöcke vom Back-End angefordert. Diese Optimierung beruht auf der Fähigkeit des Ursprungs, Bytebereichsanforderungen zu unterstützen. Wenn der Ursprung keine Anforderungen für Bytebereiche unterstützt, ist diese Optimierung nicht effektiv.

## <a name="file-compression"></a>Dateikomprimierung

Weitere Informationen finden Sie unter [Verbessern der Leistung durch Komprimieren von Dateien in Azure Front Door](how-to-compression.md).

## <a name="query-string-behavior"></a>Verhalten von Abfragezeichenfolgen

Mit Azure Front Door Service können Sie steuern, wie Dateien für eine Webanforderung, die eine Abfragezeichenfolge enthält, zwischengespeichert werden. In einer Webanforderung mit einer Abfragezeichenfolge ist die Abfragezeichenfolge der Teil der Anforderung, der auf das Fragezeichen („?“) folgt. Eine Abfragezeichenfolge kann ein oder mehrere Schlüssel-Wert-Paare enthalten, wobei der Feldname und sein Wert durch ein Gleichheitszeichen („=“) getrennt sind. Die einzelnen Schlüssel-Wert-Paare sind durch ein kaufmännisches Und-Zeichen („&“) voneinander getrennt. Beispiel: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Falls mehrere Schlüssel-Wert-Paare in einer Abfragezeichenfolge derselben Anforderung vorhanden sind, spielt die Reihenfolge keine Rolle.

* **Ignorieren von Abfragezeichenfolgen:** In diesem Modus übergibt Azure Front Door Service die Abfragezeichenfolgen bei der ersten Anforderung vom Anforderer an den Ursprung und zwischenspeichert die Ressource. Für alle nachfolgenden Anforderungen der Ressource, die von der Azure Front Door Service-Umgebung verarbeitet werden, werden die Abfragezeichenfolgen bis zum Ablauf der zwischengespeicherten Ressource ignoriert.

* **Zwischenspeichern jeder eindeutigen URL:** In diesem Modus wird jede Anforderung mit einer eindeutigen URL, einschließlich der Abfragezeichenfolge, als eindeutiges Objekt mit eigenem Cache behandelt. So wird beispielsweise die Antwort vom Ursprung für eine Anforderung für `www.example.ashx?q=test1` in der Azure Front Door Service-Umgebung zwischengespeichert und für nachfolgende Caches mit der gleichen Abfragezeichenfolge zurückgegeben. Eine Anforderung für `www.example.ashx?q=test2` wird als separates Objekt mit eigener Einstellung für die Gültigkeitsdauer zwischengespeichert.
* Sie können mit dem Regelsatz auch das Verhalten der **Abfragezeichenfolge für Cacheschlüssel** angeben, um beim Generieren des Cacheschlüssels die angegebenen Parameter einzuschließen oder auszuschließen. Beispiel: Der Standardcacheschlüssel lautet „/foo/image/asset.html“, und die Beispielanforderung lautet `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200`. Es gibt eine Regelsatzregel, gemäß der die Abfragezeichenfolge „userid“ ausgeschlossen wird. Der Abfragezeichenfolgen-Cacheschlüssel lautet dann `/foo/image/asset.html?language=EN&sessionid=200`.

## <a name="cache-purge"></a>Cachebereinigung

Weitere Informationen finden Sie unter „Cachebereinigung“.

## <a name="cache-expiration"></a>Cacheablauf
Die folgende Headerreihenfolge wird verwendet, um zu bestimmen, wie lange ein Element im Cache gespeichert wird:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expires: \<http-date>

Cache-Control-Antwortheader, die angeben, dass die Antwort nicht zwischengespeichert wird (z. B. „Cache-Control: private“, „Cache-Control: no-cache“ und „Cache-Control: no-store“), werden berücksichtigt.  Wenn „Cache-Control“ nicht vorhanden ist, ist das Standardverhalten die Zwischenspeicherung der Ressource für die Zeitspanne X. Dabei wird für X eine Zeitspanne zwischen 1 und 3 Tagen nach dem Zufallsprinzip ausgewählt.

## <a name="request-headers"></a>Anforderungsheader

Die folgenden Anforderungsheader werden bei Verwendung der Zwischenspeicherung nicht an einen Ursprung weitergeleitet.
* Content-Length
* Transfer-Encoding

## <a name="cache-behavior-and-duration"></a>Cacheverhalten und -dauer

Cacheverhalten und Cachedauer können sowohl in der Front Door-Designer-Routingregel als auch in der Regel-Engine konfiguriert werden. Die Cachingkonfiguration der Regel-Engine setzt die Konfiguration der Front Door-Designer-Routingregel immer außer Kraft.

* Wenn das *Caching* **deaktiviert** ist, speichert Front Door den Inhalt der Antwort unabhängig von den ursprünglichen Anweisungen der Antwort nicht zwischen.

* Wenn das *Caching* **aktiviert** ist, ist das Cacheverhalten für verschiedene Werte von *Standardcachedauer verwenden* unterschiedlich.
    * Wenn *Standardcachedauer verwenden* auf **Ja** festgelegt ist, beachtet Front Door immer die ursprüngliche Anweisung des Antwortheaders. Wenn die ursprüngliche Anweisung fehlt, speichert Front Door den Inhalt ein bis drei Tage lang zwischen.
    * Wenn *Standardcachedauer verwenden* auf **Nein** festgelegt ist, setzt Front Door die Anweisung immer außer Kraft und verwendet den Wert von *Cachedauer* (erforderliches Feld). Das bedeutet, dass die Inhalte für die in diesem Feld angegebene Dauer zwischengespeichert und die Werte der ursprünglichen Anweisungen der Antwort ignoriert werden. 

> [!NOTE]
> * Die in der Front Door-Designer-Routingregel festgelegte *Cachedauer* ist die **minimale Cachedauer**. Diese Außerkraftsetzung funktioniert nicht, wenn der Cachesteuerungsheader vom Ursprung eine längere Gültigkeitsdauer aufweist als der Außerkraftsetzungswert.
> * Azure Front Door garantiert nicht, wie lange das Objekt mindestens im Cache gespeichert wird. Wenn Cacheinhalte nicht sehr häufig angefordert werden, können sie vor ihrem Ablauf aus dem Edgecache entfernt werden, um Platz für häufiger angeforderte Inhalte zu schaffen.
>

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Vergleichsbedingungen für Regelsätze](concept-rule-set-match-conditions.md)
* Informieren Sie sich über [Regelsatzaktionen](concept-rule-set-actions.md).
