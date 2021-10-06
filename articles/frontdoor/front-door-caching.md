---
title: Azure Front Door – Zwischenspeicherung | Microsoft-Dokumentation
description: Dieser Artikel hilft Ihnen dabei, das Verhalten von Front Door mit Routingregeln mit aktivierter Zwischenspeicherung zu verstehen.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: duau
ms.openlocfilehash: 1fb1aafed996fa79177157f6c20e6727ee532e7d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563113"
---
# <a name="caching-with-azure-front-door"></a>Zwischenspeicherung mit Azure Front Door
In diesem Dokument werden die Verhaltensweisen von Azure Front Door Service mit Routingregeln mit aktivierter Zwischenspeicherung erläutert. Front Door ist ein modernes Content Delivery Network (CDN) mit Beschleunigung dynamischer Websites und Lastenausgleich und unterstützt zudem wie jedes andere CDN auch Zwischenspeicherungsverhaltensweisen.

## <a name="delivery-of-large-files"></a>Übermittlung großer Dateien
Azure Front Door übermittelt große Dateien ohne Beschränkung der Dateigröße. Bei Azure Front Door Service kommt eine Technik namens Objektblockerstellung zum Einsatz. Wenn eine große Datei angefordert wird, ruft Azure Front Door Service kleinere Teile der Datei vom Back-End ab. Nach dem Empfang einer vollständigen oder auf einen Bytebereich beschränkten Anforderung fordert eine Front Door-Umgebung die Datei in Blöcken von 8 MB vom Back-End an.

Nachdem der Block in der Azure Front Door Service-Umgebung angekommen ist, wird er zwischengespeichert und sofort für den Benutzer bereitgestellt. Azure Front Door Service ruft den nächsten Block dann parallel dazu ab. Durch diesen Vorabruf wird sichergestellt, dass der Inhalt dem Benutzer immer einen Block voraus ist, sodass sich die Wartezeit reduziert. Dieser Prozess wird fortgesetzt, bis die gesamte Datei heruntergeladen wurde (falls angefordert) oder der Client die Verbindung beendet.

Weitere Informationen zur Bytebereichsanforderung finden Sie unter [RFC 7233](https://www.rfc-editor.org/info/rfc7233).
Alle Blöcke werden von Azure Front Door Service zwischengespeichert, sobald sie eingetroffen sind, sodass nicht die gesamte Datei im Cache von Front Door zwischengespeichert werden muss. Nachfolgende Anforderungen für die Datei oder Bytebereiche werden über den Cache verarbeitet. Wenn nicht alle Blöcke zwischengespeichert werden, werden mittels Vorabruf Blöcke vom Back-End angefordert. Diese Optimierung beruht auf der Fähigkeit des Back-Ends, Bytebereichanforderungen zu unterstützen. Wenn das Back-End keine Anforderungen für Bytebereiche unterstützt, ist diese Optimierung nicht effektiv.

## <a name="file-compression"></a>Dateikomprimierung
Azure Front Door Service kann Inhalte dynamisch am Edge komprimieren, wodurch die Antwort an Ihre Clients kleiner ist und schneller erfolgt. Damit eine Datei für die Komprimierung geeignet ist, muss die Zwischenspeicherung aktiviert sein, und die Datei muss einen MIME-Typ aufweisen, damit sie komprimiert werden kann. Gegenwärtig lässt Front Door keine Änderungen an dieser Liste zu. Die aktuelle Liste umfasst folgende Typen:
- application/eot
- application/font
- application/font-sfnt
- application/javascript
- "application/json"
- application/opentype
- application/otf
- application/pkcs7-mime
- application/truetype
- application/ttf
- application/vnd.ms-fontobject
- application/xhtml+xml
- application/xml
- application/xml+rss
- application/x-font-opentype
- application/x-font-truetype
- application/x-font-ttf
- application/x-httpd-cgi
- application/x-mpegurl
- application/x-opentype
- application/x-otf
- application/x-perl
- application/x-ttf
- application/x-javascript
- font/eot
- font/ttf
- font/otf
- font/opentype
- image/svg+xml
- text/css
- text/csv
- text/html
- text/javascript
- text/js, text/plain
- text/richtext
- text/tab-separated-values
- text/xml
- text/x-script
- text/x-component
- text/x-java-source

Darüber hinaus muss die Datei zwischen 1 KB und 8 MB groß sein.

Diese Profile unterstützen die folgenden Komprimierungscodierungen:
- [GZip (GNU Zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Unterstützt eine Anforderung die GZip- und Brotli-Komprimierung, hat die Brotli-Komprimierung Vorrang.</br>
Wenn in einer Anforderung für eine Ressource eine Komprimierung angegeben ist und die Anforderung zu einem Cachefehler führt, führt Azure Front Door Service die Komprimierung der Ressource direkt auf dem POP-Server durch. Anschließend wird die komprimierte Datei aus dem Cache bereitgestellt. Das resultierende Element wird mit „transfer-encoding: chunked“ zurückgegeben.

> [!NOTE]
> Bereichsanforderungen können in verschiedene Größen komprimiert werden. Azure Front Door verlangt, dass die Werte für die Inhaltslänge für jede GET-HTTP-Anforderung gleich sind. Wenn Clients Byte-Bereichsanforderungen mit dem `accept-encoding` Header senden, was dazu führt, dass der Ursprung mit unterschiedlichen Inhaltslängen antwortet: dann gibt Azure Front Door einen 503-Fehler zurück. Sie können entweder die Komprimierung auf Origin/Azure Front Door deaktivieren oder eine Regelsatzregel erstellen, um `accept-encoding` aus der Anforderung für Byte-Bereichsanforderungen zu entfernen.

## <a name="query-string-behavior"></a>Verhalten von Abfragezeichenfolgen
Mit Azure Front Door Service können Sie steuern, wie Dateien für eine Webanforderung, die eine Abfragezeichenfolge enthält, zwischengespeichert werden. In einer Webanforderung mit einer Abfragezeichenfolge ist die Abfragezeichenfolge der Teil der Anforderung, der auf das Fragezeichen („?“) folgt. Eine Abfragezeichenfolge kann ein oder mehrere Schlüssel-Wert-Paare enthalten, wobei der Feldname und sein Wert durch ein Gleichheitszeichen („=“) getrennt sind. Die einzelnen Schlüssel-Wert-Paare sind durch ein kaufmännisches Und-Zeichen („&“) voneinander getrennt. Beispiel: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Falls mehrere Schlüssel-Wert-Paare in einer Abfragezeichenfolge derselben Anforderung vorhanden sind, spielt die Reihenfolge keine Rolle.
- **Ignorieren von Abfragezeichenfolgen:** In diesem Modus übergibt Azure Front Door Service die Abfragezeichenfolgen bei der ersten Anforderung vom Anforderer an das Back-End und speichert die Ressource im Cache zwischen. Für alle nachfolgenden Anforderungen der Ressource, die von der Azure Front Door Service-Umgebung verarbeitet werden, werden die Abfragezeichenfolgen bis zum Ablauf der zwischengespeicherten Ressource ignoriert.

- **Zwischenspeichern jeder eindeutigen URL:** In diesem Modus wird jede Anforderung mit einer eindeutigen URL, einschließlich der Abfragezeichenfolge, als eindeutiges Objekt mit eigenem Cache behandelt. So wird beispielsweise die Antwort vom Back-End für eine Anforderung für `www.example.ashx?q=test1` in der Azure Front Door Service-Umgebung zwischengespeichert und für nachfolgende Caches mit der gleichen Abfragezeichenfolge zurückgegeben. Eine Anforderung für `www.example.ashx?q=test2` wird als separates Objekt mit eigener Einstellung für die Gültigkeitsdauer zwischengespeichert.

## <a name="cache-purge"></a>Cachebereinigung

Azure Front Door Service speichert Ressourcen zwischen, bis deren Gültigkeitsdauer (Time-to-live, TTL) abläuft. Wenn ein Client eine Ressource nach Ablauf ihrer Gültigkeitsdauer anfordert, ruft die Front Door-Umgebung eine neue aktualisierte Kopie der Ressource ab, um die Anforderung zu erfüllen und den aktualisierten Cache zu speichern.

Die bewährte Methode, um sicherzustellen, dass Ihre Benutzer immer die neueste Kopie Ihrer Assets abrufen, besteht darin, Ihre Assets für jedes Update mit einer Version zu versehen und sie als neue URLs zu veröffentlichen. Azure Front Door Service ruft sofort die neuen Ressourcen für die nächsten Clientanforderungen ab. Manchmal möchten Sie möglicherweise zwischengespeicherten Inhalt aus allen Edgeknoten löschen und sie zwingen, neue aktualisierte Assets abzurufen. Gründe hierfür können Updates Ihrer Webanwendung oder eine schnelle Aktualisierung von Ressourcen sein, die falsche Informationen enthalten.

Wählen Sie die Ressourcen aus, die Sie von den Edgeknoten löschen möchten. Wählen Sie **Alles löschen** aus, um alle Ressourcen zu löschen. Andernfalls geben Sie in **Pfad** den Pfad jeder Ressource ein, die Sie bereinigen möchten.

Diese Formate werden in der Liste der zu löschenden Pfade unterstützt:

- **Löschen eines einzelnen Pfads**: Löschen Sie einzelne Ressourcen, indem Sie den vollständigen Pfad der Ressource, ohne Protokoll und Domäne, aber mit der Dateierweiterung angeben. Beispiel: /pictures/strasbourg.png.
- **Mit Platzhalter löschen**: Das Sternchen (\*) kann als Platzhalterzeichen verwendet werden. Löschen Sie alle Ordner, Unterordner und Dateien unter einem Endpunkt, indem Sie „/\*“ im Pfad angeben, oder löschen Sie alle Unterordner und Dateien unter einem bestimmten Ordner, indem Sie den Ordner gefolgt von „/\*“ angeben. Beispiel: „/pictures/\*“.
- **Stammdomäne löschen**: Löschen Sie den Stamm des Endpunkts, indem Sie „/“ im Pfad angeben.

> [!NOTE]
> **Löschen von Platzhalterdomänen**: Das Angeben zwischengespeicherter Pfade für das Löschen, wie in diesem Abschnitt beschrieben, gilt nicht für Platzhalterdomänen, die mit Front Door verknüpft sind. Derzeit wird das direkte Löschen von Platzhalterdomänen nicht unterstützt. Sie können Pfade aus bestimmten Unterdomänen löschen, indem Sie die jeweilige Unterdomäne und den Löschpfad angeben. Wenn meine Front Door-Instanz beispielsweise `*.contoso.com` aufweist, kann ich Ressourcen meiner Unterdomäne `foo.contoso.com` bereinigen, indem ich `foo.contoso.com/path/*` eingebe. Gegenwärtig ist die Angabe von Hostnamen im Pfad für die Bereinigung von Inhalten ggf. auf Unterdomänen von Platzhalterdomänen beschränkt.
>

Bei Cachebereinigungen in Azure Front Door Service muss die Groß-/Kleinschreibung beachtet werden. Darüber hinaus sind Cachebereinigungen abfragezeichenfolgenagnostisch, d. h. beim Bereinigen einer URL werden alle Abfragezeichenfolgenvariationen der URL gelöscht. 

## <a name="cache-expiration"></a>Cacheablauf
Die folgende Headerreihenfolge wird verwendet, um zu bestimmen, wie lange ein Element im Cache gespeichert wird:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expires: \<http-date>

Cache-Control-Antwortheader, die angeben, dass die Antwort nicht zwischengespeichert wird (z. B. „Cache-Control: private“, „Cache-Control: no-cache“ und „Cache-Control: no-store“), werden berücksichtigt.  Ist keine Angabe für die Cachesteuerung vorhanden, ist das Standardverhalten wie folgt: Front Door speichert die Ressource für einen Zeitraum X zwischen. Für X wird dabei ein zufällig gewählter Wert von ein bis drei Tagen verwendet.

## <a name="request-headers"></a>Anforderungsheader

Die folgenden Anforderungsheader werden bei Verwendung der Zwischenspeicherung nicht an ein Back-End weitergeleitet.
- Content-Length
- Transfer-Encoding

## <a name="cache-behavior-and-duration"></a>Cache-Verhalten und Dauer

Das Cache-Verhalten und die Cache-Dauer können sowohl in der Front Door Designer Routingregel als auch in der Rules Engine konfiguriert werden. Die Zwischenspeichern-Konfiguration der Rules Engine hat immer Vorrang vor der Konfiguration der Routingregel des Front Door Designers.

* Wenn *Caching***deaktiviert** ist, zwischenspeichert Front Door den Inhalt der Antwort unabhängig von den Urpsprungsantwortsanweisungen nicht.

* Wenn *Caching***aktiviert** ist, ist das Cache-Verhalten für verschiedene Werte von *standardmäßige Cache-Dauer verwenden* unterschiedlich.
    * Wenn *standardmäßige Cache-Dauer verwenden* auf **Ja** gesetzt ist, wird Front Door immer die Ursprungsantwortheader-Anweisung beachten. Wenn die Ursprungsrichtlinie fehlt, wird Front Door den Inhalt 1 bis 3 Tage lang zwischen speichern.
    * Wenn *standardmäßige Cache-Dauer verwenden* auf **Nein** gesetzt ist,setzt Front Door immer die *Cache-Dauer* (erforderliche Felder) außer Kraft setzen, was bedeutet, dass es den Inhalt für die Cache-Dauer zwischenspeichert und dabei die Werte der Urpsprungsantwortsanweisungen ignoriert. 

> [!NOTE]
> * Die in der Front Door-Designer-Routingregel festgelegte *Cache-Dauer* ist die **Mindest-Cache-Dauer**. Diese Außerkraftsetzung funktioniert nicht, wenn der Cachesteuerungs-Header vom Ursprung eine größere TTL hat als der Außerkraftsetzungsungswert hat.
> * Cache-Inhalte können aus dem Azure Front Door entfernt werden, bevor sie abgelaufen sind, wenn die Inhalte nicht so häufig angefordert werden, um Platz für häufiger angeforderte Inhalte zu schaffen.
>

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
