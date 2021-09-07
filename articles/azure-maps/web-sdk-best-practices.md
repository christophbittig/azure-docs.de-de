---
title: Best Practices für das Azure Maps-Web-SDK | Microsoft Azure Maps
description: Hier lernen Sie Tipps und Tricks für eine optimale Verwendung des Azure Maps-Web-SDK kennen.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 630b354768e3ad0882911f11a99cca06fc87154e
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112234427"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Best Practices für das Azure Maps-Web-SDK

In diesem Dokument liegt der Fokus auf Best Practices für das Azure Maps-Web-SDK. Viele der beschriebenen bewährten Methoden und Optimierungen können jedoch auch auf alle anderen Azure Maps-SDKs angewendet werden.

Das Azure Maps-Web-SDK bietet eine leistungsstarke Canvas für das Rendern von großen räumlichen Datasets auf viele verschiedene Arten. Manchmal gibt es mehrere Möglichkeiten, Daten auf die gleiche Weise zu rendern. In diesen Fällen bietet jedoch abhängig von der Größe des Datasets und der gewünschten Funktionalität möglicherweise eine Methode eine bessere Leistung als die anderen. In diesem Artikel werden Best Practices sowie Tipps und Tricks hervorgehoben, mit denen Sie die Leistung maximieren und eine hohe Benutzerfreundlichkeit erzielen können.

Allgemein sollten Sie, wenn Sie die Leistung der Karte verbessern möchten, nach Möglichkeiten suchen, die Anzahl der Ebenen und Quellen sowie die Komplexität der verwendeten Datasets und Renderingstile zu reduzieren.

## <a name="security-basics"></a>Grundlagen zur Sicherheit

Der wichtigste Teil Ihrer Anwendung ist deren Sicherheit. Wenn Ihre Anwendung nicht sicher ist, können Hacker Schaden verursachen, unabhängig davon, wie gut die Benutzerfreundlichkeit möglicherweise ist. Im Folgenden sind einige Tipps aufgeführt, die Ihnen dabei helfen, die Sicherheit Ihrer Azure Maps-Anwendung zu gewährleisten. Wenn Sie Azure verwenden, denken Sie daran, sich mit den Sicherheitstools vertraut zu machen, die Ihnen zur Verfügung stehen. Eine Einführung zum Thema Sicherheit in Azure finden Sie in [diesem Dokument](../security/fundamentals/overview.md).

> [!IMPORTANT]
> Azure Maps bietet zwei Authentifizierungsmethoden.
> * Authentifizierung mit einem Abonnementschlüssel
> * Authentifizierung mit Azure Active Directory. Verwenden Sie Azure Active Directory in allen Produktionsanwendungen.
> Die Authentifizierung mit einem Abonnementschlüssel ist einfach. Die meisten Kartenplattformen nutzen diese als einfache Methode, um die Nutzung der Plattform für Abrechnungszwecke zu messen. Es handelt sich dabei jedoch um eine Art der Authentifizierung, die nicht sicher ist und nur lokal bei der Entwicklung von Apps verwendet werden sollte. Einige Plattformen bieten die Möglichkeit, Einschränkungen für die in Anforderungen enthaltenen IP-Adressen und/oder HTTP-Referrer festzulegen. Diese Informationen können jedoch leicht gespooft werden. Wenn Sie Abonnementschlüssel verwenden, stellen Sie sicher, dass Sie [diese regelmäßig rotieren](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> Azure Active Directory ist ein Identitätsdienst für Unternehmen, der eine große Auswahl an Sicherheitsfeatures und -einstellungen für alle Arten von Anwendungsszenarios bietet. Microsoft empfiehlt für alle Produktionsanwendungen, die Azure Maps verwenden, die Verwendung von Azure Active Directory für die Authentifizierung.
> Weitere Informationen zur Verwaltung der Authentifizierung in Azure Maps finden Sie in [diesem Dokument](how-to-manage-authentication.md).

### <a name="secure-your-private-data"></a>Schützen privater Daten

Wenn Daten zu den Azure Maps-SDKs mit interaktiven Karten hinzugefügt werden, werden diese lokal auf dem Gerät des Endbenutzers gerendert und in keinem Fall und aus keinem Grund wieder an das Internet gesendet.

Wenn Ihre Anwendung Daten lädt, die nicht öffentlich zugänglich sein sollten, stellen Sie sicher, dass die Daten an einem sicheren Ort gespeichert werden, dass auf sichere Weise auf sie zugegriffen wird und dass die Anwendung selbst gesperrt und nur für die gewünschten Benutzer verfügbar ist. Wenn einer dieser Schritte übersprungen wird, können nicht autorisierte Personen möglicherweise auf diese Daten zugreifen. Azure Active Directory kann Sie bei dieser Sperrung unterstützen.

Weitere Informationen finden Sie im Tutorial [Hinzufügen der Authentifizierung zu Ihrer Web-App in Azure App Service](../app-service/scenario-secure-app-authentication-app-service.md).

### <a name="use-the-latest-versions-of-azure-maps"></a>Verwenden der aktuellen Versionen von Azure Maps

Für die Azure Maps-SDKs und alle möglicherweise von diesen verwendeten externen Abhängigkeitsbibliotheken werden regelmäßige Sicherheitstests durchgeführt. Alle bekannten Sicherheitsprobleme werden zeitnah behoben und die Behebungen für Produktionsumgebungen veröffentlicht. Wenn Ihre Anwendung auf die aktuelle Hauptversion der gehosteten Version des Azure Maps-Web-SDK verweist, werden automatisch alle Nebenversionsupdates abgerufen, die sicherheitsrelevante Fixes enthalten.

Wenn Sie das Azure Maps-Web-SDK selbst über das NPM-Modul hosten, stellen Sie sicher, dass die Datei `package.json` das Caretzeichen (^) in Kombination mit der Versionsnummer des Azure Maps-NPM-Pakets enthält, damit immer auf die aktuelle Nebenversion verwiesen wird.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Optimieren des anfänglichen Ladens von Karten

Wenn eine Webseite geladen wird, sollte direkt so schnell wie möglich mit dem Rendern von Inhalten begonnen werden, damit dem Benutzer kein leerer Bildschirm angezeigt wird.

### <a name="watch-the-maps-ready-event"></a>Ansehen des ready-Ereignisses für Karten

Analog dazu wird oft gewünscht, dass beim anfänglichen Laden der Karte die Daten darauf so schnell wie möglich geladen werden, damit der Benutzer keine leere Karte sieht. Da die Karte Ressourcen asynchron lädt, müssen Sie warten, bis die Karte bereit für Interaktionen ist, bevor Sie versuchen, Ihre eigenen Daten darauf zu rendern. Es gibt zwei Ereignisse, auf die Sie warten können, das `load`- und das `ready`-Ereignis. Das load-Ereignis wird ausgelöst, wenn das Laden der anfänglichen Ansicht der Karte vollständig abgeschlossen ist und alle Kartenkacheln geladen wurden. Das ready-Ereignis wird ausgelöst, wenn die für eine Interaktion mit der Karte mindestens erforderlichen Kartenressourcen geladen wurden. Das ready-Ereignis kann oft bereits nach der Hälfte der für das load-Ereignis benötigten Zeit ausgelöst werden, wodurch Sie schneller mit dem Laden Ihrer Daten in die Karte beginnen können.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Lazy Loading des Azure Maps-Web-SDK

Wenn die Karte nicht sofort benötigt wird, laden Sie das Azure Maps-Web-SDK per Lazy Loading („träges Laden“), bis es benötigt wird. Dadurch wird das Laden der JavaScript- und CSS-Dateien, die das Azure Maps-Web-SDK verwendet, so lange verzögert, bis diese benötigt werden. Ein häufiges Szenario hierfür ist das Laden der Karte auf einer Registerkarte oder in einem Flyoutpanel, die bzw. das beim Laden der Seite nicht angezeigt wird.
Im folgenden Codebeispiel wird gezeigt, wie Sie das Laden des Azure Maps-Web-SDK verzögern, bis auf eine Schaltfläche geklickt wird.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lazy Loading der Karte" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Sehen Sie sich den Pen <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>Lazy load the map</a> (Lazy Loading der Karte) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a> an.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Hinzufügen eines Platzhalters für die Karte

Wenn das Laden der Karte aufgrund von Netzwerkeinschränkungen oder anderen Prioritäten in Ihrer Anwendung eine Weile dauern kann, ziehen Sie das Hinzufügen eines kleinen Hintergrundbilds zur Karte `div` als Platzhalter für die Karte in Betracht. Dadurch wird an der ansonsten leeren Stelle, an der die Karte `div` geladen wird, während des Ladevorgangs etwas angezeigt.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Festlegen des anfänglichen Kartenstils und der Kameraoptionen bei der Initialisierung

Häufig möchten Apps die Karte mit einem bestimmten Ort oder Stil laden. Manchmal warten Entwickler, bis die Karte geladen wurde, (oder auf das `ready`-Ereignis) und verwenden dann die Funktion `setCemer` oder `setStyle` der Karte. Hierdurch dauert es häufig länger, bis die gewünschte anfängliche Kartenansicht angezeigt wird, da vor dem Laden der für die gewünschte Kartenansicht erforderlichen Ressourcen erst viele andere Ressourcen standardmäßig geladen werden müssen. Ein besserer Ansatz besteht darin, die gewünschten Kartenkamera- und Stiloptionen bei der Initialisierung der Karte an diese zu übergeben.

## <a name="optimize-data-sources"></a>Optimieren von Datenquellen

Das Web-SDK verfügt über zwei Datenquellen:

* **GeoJSON-Quelle:** Diese als `DataSource`-Klasse bekannte Quelle verwaltet Rohdaten für Orte lokal im GeoJSON-Format. Sie ist gut geeignet für kleine bis mittlere Datasets (mehr als hunderttausend Features).
* **Vektorkachelquelle:** Diese als `VectorTileSource`-Klasse bekannte Quelle lädt Daten, die als Vektorkacheln für die aktuelle Kartenansicht formatiert sind, basierend auf dem Kartenkachelsystem. Sie eignet sich hervorragend für große bis sehr große Datasets (Millionen oder Milliarden von Features).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Verwenden von kachelbasierten Lösungen für große Datasets

Wenn Sie mit größeren Datasets mit Millionen von Features arbeiten, ist die empfohlene Methode für eine optimale Leistung, die Daten über eine serverseitige Lösung wie einen Dienst für Vektor- oder Rasterbildkacheln zur Verfügung zu stellen.  
Vektorkacheln sind so optimiert, dass nur die angezeigten Daten geladen werden, wobei die Geometrien auf den Fokusbereich der Kachel zugeschnitten und so generalisiert werden, dass die Auflösung der Karte zum Zoomfaktor der Kachel passt.

Die [Azure Maps Creator-Plattform](creator-indoor-maps.md) bietet die Möglichkeit, Daten im Vektorkachelformat abzurufen. Für andere Datenformate können Tools wie [Tippecanoe](https://github.com/mapbox/tippecanoe) oder eine der vielen auf [dieser Seite](https://github.com/mapbox/awesome-vector-tiles) aufgeführten Ressourcen verwendet werden.

Ebenfalls möglich ist das Erstellen eines benutzerdefinierten Diensts, der Datasets auf Serverseite als Rasterbildkacheln rendert. In diesem Fall können die Daten mit der TileLayer-Klasse in das Karten-SDK geladen werden. Dies bietet eine außergewöhnlich gute Leistung, da die Karte nur höchstens ein paar Dutzend Bilder laden und verwalten muss. Es gibt jedoch einige Einschränkungen bei der Verwendung von Rasterkacheln, da die Rohdaten nicht lokal verfügbar sind. Häufig ist für Interaktionen ein sekundärer Dienst erforderlich, z. B. um herauszufinden, auf welche Form ein Benutzer geklickt hat. Außerdem ist die Dateigröße einer Rasterkachel oft größer als die einer komprimierten Vektorkachel, die generalisierte und für den jeweiligen Zoomfaktor optimierte Geometrien enthält.

Weitere Informationen zu Datenquellen finden Sie im Dokument [Erstellen einer Datenquelle](create-data-source-web-sdk.md).

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Kombinieren mehrerer Datasets in einer einzelnen Vektorkachelquelle

Je weniger Datenquellen die Karte verwalten muss, desto schneller kann sie alle anzuzeigenden Features verarbeiten. Bei Kachelquellen kann insbesondere durch das Kombinieren zweier Vektorkachelquellen die Anzahl der HTTP-Anforderungen für das Abrufen der Kacheln halbiert und die Gesamtdatenmenge etwas verringert werden, da es nur einen Dateiheader gibt.

Zum Kombinieren mehrerer Datasets in einer einzelnen Vektorkachelquelle können Tools wie [Tippecanoe](https://github.com/mapbox/tippecanoe) verwendet werden. Datasets können zu einer einzelnen Featuresammlung zusammengefasst oder auf verschiedene Ebenen innerhalb der Vektorkachel aufgeteilt werden, die Quellebenen genannt werden. In diesem Fall würden Sie beim Verbinden einer Vektorkachelquelle mit einer Renderingebene die Quellebene angeben, die die Daten enthält, die Sie mit der Ebene rendern möchten.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Reduzieren der Anzahl der Canvasaktualisierungen aufgrund von Datenaktualisierungen

Es gibt mehrere Möglichkeiten für das Hinzufügen oder Aktualisieren von Daten in einer `DataSource`-Klasse. Im Folgenden sind die verschiedenen Methoden zusammen mit einigen Überlegungen zur Sicherstellung einer guten Leistung aufgeführt.

* Die `add`-Funktion für Datenquellen kann verwendet werden, um einer Datenquelle ein oder mehrere Features hinzuzufügen. Immer wenn diese Funktion aufgerufen wird, löst sie eine Aktualisierung der Kartencanvas aus. Wenn Sie viele Features hinzufügen, kombinieren Sie sie in einem Array oder einer Featuresammlung, und übergeben Sie sie so einmal der Funktion statt ein Dataset mit einer Schleife zu durchlaufen und die Funktion für jedes Feature einzeln aufzurufen.
* Die `setShapes`-Funktion für Datenquellen kann verwendet werden, um alle Formen in einer Datenquelle zu überschreiben. Unter der Haube werden hierbei die `clear`- und die `add`-Funktion für Datenquellen kombiniert, und die Kartencanvas wird nur einmal aktualisiert statt zweimal, was deutlich schneller geht. Stellen Sie sicher, dass Sie diese Funktion verwenden, wenn Sie alle Daten in einer Datenquelle aktualisieren möchten.
* Die `importDataFromUrl`-Funktion für Datenquellen kann verwendet werden, um eine GeoJSON-Datei über eine URL in eine Datenquelle zu laden. Sobald die Daten heruntergeladen wurden, werden sie an die `add`-Funktion für Datenquellen übergeben. Wenn die GeoJSON-Datei in einer anderen Domäne gehostet wird, stellen Sie sicher, dass die andere Domäne domänenübergreifende Anforderungen (Cross Domain Requests, CORs) unterstützt. Wenn dies nicht der Fall ist, ziehen Sie das Kopieren der Daten in eine lokale Datei in Ihrer Domäne oder das Erstellen eines Proxydiensts in Betracht, für den CORs aktiviert sind. Wenn die Datei groß ist, sollten Sie in Erwägung ziehen, diese in eine Vektorkachelquelle zu konvertieren.
* Wenn Features mit der `Shape`-Klasse umschlossen werden, lösen die Funktionen `addProperty`, `setCoordinates` und `setProperties` für die Form alle ein Aktualisieren der Datenquelle und der Kartencanvas aus. Alle von der `getShapes`- und `getShapeById`-Funktion für Datenquellen zurückgegebenen Features werden automatisch mit der `Shape`-Klasse umschlossen. Wenn Sie mehrere Formen aktualisieren möchten, können Sie diesen Vorgang beschleunigen, indem Sie die Formen mit der `toJson`-Funktion für Datenquellen in das JSON-Format konvertieren, die GeoJSON-Datei bearbeiten und anschließend diese Daten an die `setShapes`-Funktion für Datenquellen übergeben.

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Vermeiden von unnötigen Aufrufen der clear-Funktion für Datenquellen

Durch das Aufrufen der clear-Funktion der `DataSource`-Klasse wird eine Aktualisierung der Kartencanvas ausgelöst. Wenn die `clear`-Funktion mehrmals in einer Zeile aufgerufen wird, kann es zu einer Verzögerung kommen, während die Karte darauf wartet, dass die einzelnen Aktualisierungen ausgeführt werden.

Ein gängiges Szenario, in dem dies bei Anwendungen oft der Fall ist, ist wenn die App die Datenquelle löscht, neue Daten herunterlädt, die Datenquelle noch mal löscht und dann die neuen Daten der Datenquelle hinzufügt. Je nach gewünschter Benutzerfreundlichkeit sind die folgenden Alternativen besser geeignet.

* Löschen Sie die Daten, bevor Sie die neuen Daten herunterladen, und übergeben Sie dann die neuen Daten an die `add`- oder `setShapes`-Funktion für Datenquellen. Wenn dies das einzige Dataset auf der Karte ist, ist die Karte leer, während die neuen Daten heruntergeladen werden.
* Laden Sie die neuen Daten herunter, und übergeben Sie sie an die `setShapes`-Funktion für Datenquellen. Dadurch werden alle Daten auf der Karte ersetzt.

### <a name="remove-unused-features-and-properties"></a>Entfernen nicht verwendeter Features und Eigenschaften

Wenn Ihr Dataset Features enthält, die in Ihrer App nicht verwendet werden, entfernen Sie diese. Entfernen Sie ebenso alle Eigenschaften für Features, die nicht benötigt werden. Dies hat mehrere Vorteile:

* Die Datenmenge, die heruntergeladen werden muss, wird verringert.
* Die Anzahl der beim Rendern der Daten in einer Schleife zu durchlaufenden Features wird verringert.
* Manchmal kann es hilfreich sein, datengesteuerte Ausdrücke und Filter zu vereinfachen oder zu entfernen. So muss zur Renderingzeit weniger verarbeitet werden.

Wenn Features über viele Eigenschaften oder Inhalte verfügen, kann eine deutlich bessere Leistung erzielt werden, wenn der Datenquelle nur die für das Rendering benötigten Eigenschaften oder Inhalte hinzugefügt werden und zum Abrufen weiterer Eigenschaften oder Inhalte bei Bedarf eine separate Methode oder ein separater Dienst verwendet wird. Ein Beispiel wäre eine einfache Karte mit verschiedenen Orten, für die ausführliche Inhalte angezeigt werden, wenn der Benutzer darauf klickt. Wenn Sie datengesteuerte Stile verwenden möchten, um anzupassen, wie die Orte auf der Karte gerendert werden, laden Sie nur die benötigten Eigenschaften in die Datenquelle. Wenn Sie die ausführlichen Inhalte anzeigen möchten, verwenden Sie die ID des Features, um die zusätzlichen Inhalte separat abzurufen. Wenn die Inhalte auf Serverseite gespeichert werden, können sie mit einem Dienst asynchron abgerufen werden, wodurch die beim anfänglichen Laden der Karte herunterzuladende Datenmenge stark verringert wird.

Darüber hinaus kann durch das Reduzieren der Anzahl signifikanter Stellen in den Koordinaten von Features die Datengröße deutlich reduziert werden. Es ist nicht unüblich, dass Koordinaten zwölf oder mehr Dezimalstellen enthalten. Allerdings bieten sechs Dezimalstellen bereits eine Genauigkeit von ungefähr 0,1 Metern, was oft schon genauer als nötig für den Ort ist, für den die Koordinate steht (sechs Dezimalstellen werden empfohlen, wenn mit Ortsdaten für ein kleines Gebiet gearbeitet wird, z. B. solchen in Bezug auf die Raumaufteilung in Gebäuden). Die Verwendung von mehr als sechs Dezimalstellen wirkt sich wahrscheinlich nicht darauf aus, wie die Daten gerendert werden, und sorgt lediglich dafür, dass der Benutzer mehr Daten herunterladen muss, ohne dass dies irgendwelche Vorteile bietet.

Eine Liste mit nützlichen Tools für die Arbeit mit GeoJSON-Daten finden Sie [hier](https://github.com/tmcw/awesome-geojson).

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Verwenden einer separaten Datenquelle für sich schnell ändernde Daten

Manchmal müssen Daten auf einer Karte schnell aktualisiert werden, z. B. wenn Liveupdates für gestreamte Daten oder Features mit Animationen angezeigt werden. Beim Aktualisieren einer Datenquelle durchläuft die Rendering-Engine die Datenquelle mit Schleifen und rendert alle darin enthaltenen Features. Durch die Aufteilung von statischen und sich schnell ändernden Daten auf verschiedene Datenquellen kann die Anzahl der beim Aktualisieren der Datenquelle erneut gerenderten Features deutlich verringert und die Gesamtleistung verbessert werden.

Wenn Vektorkacheln mit Livedaten verwendet werden, kann für eine Unterstützung von Updates einfach der `expires`-Antwortheader verwendet werden. Standardmäßig laden Vektorkachelquellen und Rasterkachelebenen die Kacheln am `expires`-Datum automatisch neu. Die Kartenkacheln für Verkehrsfluss und Verkehrsereignisse verwenden dieses Feature, um sicherzustellen, dass auf der Karte Verkehrsinfos in Echtzeit angezeigt werden. Sie können dieses Feature deaktivieren, indem Sie den Kartendienst `refreshExpiredTiles` auf `false` festlegen.

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>Anpassen der Puffer- und Toleranzoptionen in GeoJSON-Datenquellen

Die `DataSource`-Klasse konvertiert Rohdaten für Orte in lokale Vektorkacheln für direktes Rendering. Diese lokalen Vektorkacheln schneiden die Rohdaten auf die Grenzen des Kachelbereichs mit etwas Puffer zu, um ein reibungsloses Rendering zwischen Kacheln sicherzustellen. Je kleiner der Wert für die Option `buffer` ist, desto weniger überlappende Daten werden in den lokalen Vektorkacheln gespeichert und desto besser ist die Leistung, desto mehr ändern sich jedoch auch die Renderingartefakte. Probieren Sie verschiedene Werte für diese Option aus, um die richtige Mischung aus Leistung und so wenig Renderingartefakten wie möglich zu finden.

Die `DataSource`-Klasse verfügt auch über eine `tolerance`-Option, die zusammen mit dem Douglas-Peucker-Vereinfachungsalgorithmus verwendet wird, um die Auflösung von Geometrien für Renderingzwecke zu verringern. Durch das Erhöhen dieses Toleranzwerts wird die Auflösung von Geometrien verringert, was zu einer besseren Leistung führt. Probieren Sie verschiedene Werte für diese Option aus, um die richtige Mischung aus Geometrieauflösung und Leistung für Ihr Dataset zu finden.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>Festlegen der Option für den maximalen Zoomfaktor für GeoJSON-Datenquellen

Die `DataSource`-Klasse konvertiert Rohdaten für Orte in lokale Vektorkacheln für direktes Rendering. Dies erfolgt standardmäßig bis zu einem Zoomfaktor von 18. Wenn weiter gezoomt wird, verwendet die Klasse die Daten aus den für den Zoomfaktor 18 generierten Kacheln. Dies funktioniert gut für die meisten Datasets, bei denen bei einem so hohen Zoomfaktor eine hohe Auflösung erforderlich ist. Wenn Sie jedoch mit Datasets arbeiten, bei denen beim Anzeigen eher nicht so weit gezoomt wird (z. B. wenn Polygone für Staaten oder Provinzen angezeigt werden), werden durch das Festlegen der Option `minZoom` auf einen niedrigeren Wert (z. B. `12`) der Umfang der Berechnungen und der Generierung lokaler Kacheln sowie der von der Datenquelle genutzte Arbeitsspeicher verringert, und die Leistung wird verbessert.

### <a name="minimize-geojson-response"></a>Minimieren der GeoJSON-Antwort

Wenn Sie entweder über einen Dienst oder durch das Laden einer Flatfile GeoJSON-Daten von einem Server laden, stellen Sie sicher, dass die Daten minimiert werden, sodass nicht benötigte Leerzeichen entfernt werden, die die Downloadgröße unnötig erhöhen.

### <a name="access-raw-geojson-using-a-url"></a>Zugreifen auf GeoJSON-Rohdaten über eine URL

Das Speichern von GeoJSON-Objekten in JavaScript-Code ist möglich. Hierfür wird jedoch viel Arbeitsspeicher benötigt, da Kopien davon in der Variable gespeichert werden, die Sie für dieses Objekt erstellt haben, sowie in der Datenquelleninstanz, die es in einem separaten Worker verwaltet. Machen Sie stattdessen das GeoJSON-Objekt über eine URL für Ihre App verfügbar. Bei dieser Vorgehensweise lädt die Datenquelle eine einzelne Kopie der Daten direkt in ihren Worker.  

## <a name="optimize-rendering-layers"></a>Optimieren der Renderingebenen

Azure Maps stellt verschiedene Ebenen für das Rendern von Daten auf einer Karte bereit. Dank zahlreicher Optimierungsmöglichkeiten können Sie diese Ebenen an Ihr Szenario anpassen und die Leistung sowie die allgemeine Benutzerfreundlichkeit erhöhen.

### <a name="create-layers-once-and-reuse-them"></a>Einmaliges Erstellen und Wiederverwenden von Ebenen

Für das Azure Maps-Web-SDK wird ein datengesteuerter Ansatz verwendet. Daten werden an Datenquellen gesendet, die mit Renderingebenen verbunden sind. Wenn Sie die Daten auf der Karte ändern möchten, aktualisieren Sie die Daten in der Datenquelle, oder ändern Sie die Stiloptionen einer Ebene. Dies dauert oft deutlich kürzer als ein Entfernen und anschließendes Neuerstellen von Ebenen bei jeder Änderung.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Inbetrachtziehen der Verwendung einer Blasenebene statt einer Symbolebene

Die Blasenebene rendert Punkte als Kreise auf der Karte, deren Radius und Farbe leicht über datengesteuerte Ausdrücke festgelegt werden kann. Da es sich bei Kreisen um Formen handelt, die leicht von WebGL gezeichnet werden können, kann die Rendering-Engine diese deutlich schneller rendern als eine Symbolebene, bei der ein Bild geladen und gerendert werden muss. Der Leistungsunterschied zwischen diesen beiden Renderingebenen wird deutlich, wenn Zehntausende von Punkten gerendert werden.

### <a name="use-html-markers-and-popups-sparingly"></a>Sparsames Verwenden von HTML-Markern und Pop-ups

Im Gegensatz zu den meisten Ebenen in der Azure Maps-Websteuerung, die für das Rendering WebGL nutzen, verwenden HTML-Marker und Pop-ups zum Rendern herkömmliche DOM-Elemente. Je mehr HTML-Marker und Pop-ups einer Seite hinzugefügt werden, desto mehr DOM-Elemente gibt es folglich. Wenn ein paar Hundert HTML-Marker oder Pop-ups hinzugefügt wurden, kann die Leistung sich verschlechtern. Ziehen Sie daher bei größeren Datasets entweder ein Clustering Ihrer Daten oder die Verwendung einer Symbol- oder Blasenebene in Betracht. Eine gängige Strategie für Pop-ups besteht darin, ein einzelnes Pop-up zu erstellen und wiederzuverwenden, indem jeweils der Inhalt und die Position aktualisiert werden. Dies wird im folgenden Beispiel veranschaulicht:

<br/>

<iframe height='500' scrolling='no' title='Wiederverwenden eines Popups für mehrere Stecknadeln' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Informationen hierzu finden Sie unter <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a> (Wiederverwenden eines Popups mit mehreren Stecknadeln) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Wenn nur ein paar wenige Punkte auf der Karte gerendert werden müssen, sind jedoch die einfacheren HTML-Marker möglicherweise vorzuziehen. Darüber hinaus können HTML-Marker bei Bedarf leicht ziehbar gemacht werden.

### <a name="combine-layers"></a>Kombinieren von Ebenen

Die Karte kann Hunderte von Ebenen rendern. Je mehr Ebenen es gibt, desto länger dauert jedoch das Rendern einer Szene. Eine Strategie zur Reduzierung der Anzahl der Ebenen ist das Kombinieren von Ebenen, die ähnliche Stile verwenden oder für die [datengesteuerte Stile](data-driven-style-expressions-web-sdk.md) verwendet werden können.

Angenommen, Sie verfügen über ein Dataset, in dem alle Features über die Eigenschaft `isHealthy` verfügen, die den Wert `true` oder `false` aufweisen kann. Für die Erstellung einer Blasenebene, die basierend auf dieser Eigenschaft verschiedene farbige Blasen rendert, gibt es mehrere Möglichkeiten, die im Folgenden aufsteigend nach Leistungsfähigkeit geordnet aufgeführt sind.

* Teilen Sie die Daten basierend auf dem Wert für `isHealthy` auf zwei Datenquellen auf, und fügen Sie eine Blasenebene mit einer hartcodierten Farboption für jede Datenquelle an.
* Verwenden Sie für alle Daten eine gemeinsame Datenquelle, und erstellen Sie zwei Blasenebenen mit einer hartcodierten Farboption sowie einen Filter, der auf der Eigenschaft `isHealthy` basiert. 
* Verwenden Sie für alle Daten eine gemeinsame Datenquelle, und erstellen Sie nur eine Blasenebene mit dem Stilausdruck `case` für die Farboption, der auf der Eigenschaft `isHealthy` basiert. Hier sehen Sie ein Codebeispiel zur Veranschaulichung.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Erstellen flüssiger Animationen auf Symbolebenen

Für Symbolebenen ist die Konflikterkennung standardmäßig aktiviert. Diese soll sicherstellen, dass sich keine zwei Symbole überlappen. Für Icons und Texte auf einer Symbolebene stehen zwei Optionen zur Verfügung:

* `allowOverlap`: Hiermit wird angegeben, ob das Symbol sichtbar ist, wenn es mit anderen Symbolen in Konflikt steht.
* `ignorePlacement`: Hiermit wird angegeben, ob die anderen Symbole mit diesem Symbol in Konflikt stehen können sollen.

Beide Optionen sind standardmäßig auf `false` festgelegt. Wenn Sie ein Symbol animieren, werden die Berechnungen der Konflikterkennung für jeden Frame der Animation ausgeführt, was die Animation verlangsamen und sie weniger flüssig aussehen lassen kann. Legen Sie für eine flüssigere Animation beide Optionen auf `true` fest.

Im folgenden Codebeispiel sehen Sie eine einfache Vorgehensweise für das Animieren auf einer Symbolebene.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Animation auf einer Symbolebene" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Sehen Sie sich den Pen <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>Symbol layer animation</a> (Animation auf einer Symbolebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a> an.
</iframe>

### <a name="specify-zoom-level-range"></a>Angeben des Zoomfaktorbereichs

Wenn Ihre Daten eines der folgenden Kriterien erfüllen, achten Sie darauf, den minimalen und maximalen Zoomfaktor der Ebene anzugeben, damit die Rendering-Engine Zoomvorgänge außerhalb des Zoomfaktorbereichs überspringen kann.

* Wenn die Daten aus einer Vektorkachelquelle stammen, sind häufig Quellebenen für verschiedene Datentypen nur für einen bestimmten Zoomfaktorbereich verfügbar.
* Sie verwenden eine Kachelebene, die nicht über Kacheln für alle Zoomfaktoren von 0 bis 24 verfügt, und möchten, dass diese Ebene nur bei Zoomfaktoren gerendert wird, für die Kacheln verfügbar sind, und nicht versucht wird, bei fehlenden Kacheln solche für andere Zoomfaktoren zu verwenden.
* Sie möchten eine Ebene nur für bestimmte Zoomfaktoren rendern.
Alle Ebenen verfügen über die Optionen `minZoom` und `maxZoom`, bei denen die Ebene basierend auf der Logik ` maxZoom > zoom >= minZoom` zwischen den angegebenen Zoomfaktoren gerendert wird.

**Beispiel**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Angeben von Grenzen für Kachelebenen und Zoombereichen für Quellen

Standardmäßig laden Kachelebenen Kacheln für die gesamte Erde. Wenn der Kacheldienst jedoch nur über Kacheln für einen bestimmten Bereich verfügt, versucht die Karte, Kacheln außerhalb dieses Bereichs zu laden. Hierbei wird eine Anforderung für jede Kachel gesendet und auf eine entsprechende Antwort gewartet. Hierdurch können andere Anforderungen der Karte blockiert werden, was das Rendern anderer Ebenen verlangsamen kann. Wenn Grenzen für eine Kachelebene angegeben werden, führt dies dazu, dass die Karte nur Kacheln innerhalb dieses Begrenzungsrahmens anfordert. Aus demselben Grund sollten Sie den minimalen und maximalen Zoomfaktor für die Quelle angeben, wenn die Kachelebene nur in einem bestimmten Bereich von Zoomfaktoren verfügbar ist.

**Beispiel**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Verwenden des leeren Kartenstils bei nicht sichtbarer Basiskarte

Wenn eine Ebene über die Karte gelegt wird, die die Basiskarte vollständig verdeckt, ziehen Sie in Erwägung, den Kartenstil auf `blank` oder `blank_accessible` festzulegen, damit die Basiskarte nicht gerendert wird. Ein gängiges Szenario für dieses Vorgehen ist, wenn die Basiskarte mit einer Kachel mit der gesamten Erde überdeckt wird, für die keine Deckkraft eingestellt werden kann oder die über keine transparenten Bereiche verfügt.

### <a name="smoothly-animate-image-or-tile-layers"></a>Flüssiges Animieren für Bild- oder Kachelebenen

Wenn Sie Animationen für eine Reihe von Bild- oder Kachelebenen auf der Karte erstellen möchten, geht das Erstellen einer Ebene für jede Bild- oder Kachelebene und Ändern der Deckkraft oft schneller als das Aktualisieren der Quelle einer einzelnen Ebene in jedem Animationsframe. Das Ausblenden einer Ebene, indem die Deckkraft auf null (0) festgelegt wird, und Einblenden einer neuen Ebene durch Festlegen der Deckkraft auf einen höheren Wert als null dauert deutlich weniger lang als das Aktualisieren der Quelle auf der Ebene. Alternativ kann die Sichtbarkeit der Ebenen ein- oder ausgeschaltet werden. Stellen Sie hierbei jedoch sicher, dass Sie die Ausblenddauer der Ebene auf null festlegen, da sonst die Ebene beim Anzeigen entsprechend animiert wird, was zu einem Flackereffekt führt, da die vorherige Ebene bereits ausgeblendet ist, bevor die neue die neue Ebene sichtbar ist.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Optimieren der Logik für die Konflikterkennung für Symbolebenen

Die Symbolebene verfügt über die beiden sowohl für Icons als auch für Texte verfügbaren Optionen `allowOverlap` und `ignorePlacement`. Mit diesen wird angegeben, ob das Icon oder der Text eines Symbols über etwas gelegt oder von etwas verdeckt werden kann. Wenn für diese Optionen `false` festgelegt ist, führt die Symbolebene beim Rendern jedes Punkts Berechnungen aus, um zu überprüfen, ob dieser mit anderen, bereits gerenderten Symbolen auf der Ebene in Konflikt steht. Ist dies der Fall, so wird das Symbol nicht gerendert. So kann gut die Übersichtlichkeit der Karte verbessert und die Anzahl gerenderter Objekte reduziert werden. Wenn Sie diese Optionen auf `false` festlegen, wird diese Konflikterkennungslogik übersprungen, und alle Symbole werden auf der Karte gerendert. Experimentieren Sie etwas mit diesen Optionen, um die beste Kombination aus Leistung und Benutzerfreundlichkeit zu erzielen.

### <a name="cluster-large-point-data-sets"></a>Clustering großer Datasets mit Datenpunkten

Wenn Sie mit großen Datasets mit Datenpunkten arbeiten, stellen Sie möglicherweise fest, dass sich bei bestimmten Zoomfaktoren viele der gerenderten Punkte überlappen und nur teilweise sichtbar sind, wenn überhaupt. Beim Clustering werden Punkte, die nah beieinander liegen, gruppiert und als einzelner Clusterpunkt dargestellt. Wenn der Benutzer auf der Karte zoomt, werden die Punkte im Cluster wieder einzeln angezeigt. Dadurch kann die Menge der Daten, die gerendert werden müssen, deutlich reduziert werden, die Karte wirkt weniger unordentlich, und die Leistung wird ebenfalls verbessert. Die `DataSource`-Klasse verfügt über Optionen für ein lokales Clustering von Daten. Viele Tools, die Vektorkacheln generieren, verfügen ebenfalls über Clusteringoptionen.

Durch das Vergrößern des Clusterradius können Sie die Leistung außerdem weiter verbessern. Je größer der Clusterradius ist, desto weniger geclusterte Punkte gibt es, auf die geachtet werden muss und die gerendert werden müssen.
Weitere Informationen finden Sie im Dokument [Clustering von Punktdaten](clustering-point-data-web-sdk.md).

### <a name="use-weighted-clustered-heat-maps"></a>Verwenden gewichteter geclusterter Wärmebilder

Auf der Wärmebildebene können problemlos Zehntausende von Datenpunkten gerendert werden. Bei größeren Datasets sollten Sie das Aktivieren des Clusterings für die Datenquelle und die Verwendung eines kleinen Clusterradius in Erwägung ziehen und die Clustereigenschaft `point_count` als Gewicht für das Wärmebild verwenden. Wenn der Clusterradius nur ein paar Pixel groß ist, gibt es nur wenige visuelle Unterschiede auf dem gerenderten Wärmebild. Durch die Verwendung eines größeren Clusterradius wird die Leistung verbessert, die Auflösung des gerenderten Wärmebilds wird jedoch möglicherweise verringert.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

Weitere Informationen finden Sie im Abschnitt über Clustering und Wärmebilder in [diesem Dokument](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer).

### <a name="keep-image-resources-small"></a>Verwenden kleiner Bildressourcen

Bilder können dem Kartenbildsprite zum Rendern von Icons auf einer Symbolebene oder Mustern auf einer Polygonebene hinzugefügt werden. Verwenden Sie hierfür kleine Bilder, um die Datenmenge, die heruntergeladen werden muss, sowie den von diesen Bildern genutzten Platz im Kartenbildsprite zu minimieren. Wenn Sie eine Symbolebene verwenden, die das Icon mithilfe der Option `size` skaliert, verwenden Sie ein Bild, das die für das Anzeigen auf der Karte geplante Maximalgröße aufweist und nicht mehr. Dadurch wird sichergestellt, dass das Icon mit einer hohen Auflösung gerendert wird, während die verwendeten Ressourcen minimiert werden. Darüber hinaus kann auch SVG als kleineres Dateiformat für einfache Iconbilder verwendet werden.

## <a name="optimize-expressions"></a>Optimieren von Ausdrücken

[Datengesteuerte Stilausdrücke](data-driven-style-expressions-web-sdk.md) bieten eine hohe Flexibilität und Leistungsfähigkeit für das Filtern und Formatieren von Daten auf der Karte. Es gibt viele Möglichkeiten, Ausdrücke zu optimieren. Nachfolgend finden Sie einige Tipps.

### <a name="reduce-the-complexity-of-filters"></a>Verringern der Komplexität von Filtern

Filter durchlaufen alle Daten in einer Datenquelle mit einer Schleife, und Sie können überprüfen, ob die einzelnen Ergebnisse der Logik im Filter entsprechen. Wenn Filter komplex werden, kann dies zu Leistungsproblemen führen. Im Folgenden sind einige mögliche Strategien für die Behandlung dieses Problems aufgeführt.

* Wenn Sie Vektorkacheln verwenden, teilen Sie die Daten auf verschiedene Quellebenen auf.
* Wenn Sie die `DataSource`-Klasse verwenden, teilen Sie die Daten auf verschiedene Datenquellen auf. Versuchen Sie, eine Balance zwischen der Anzahl der Datenquellen und der Komplexität des Filters zu finden. Zu viele Datenquellen können ebenfalls Leistungsprobleme verursachen. Daher müssen Sie möglicherweise einige Tests durchführen, um herauszufinden, was für Ihr Szenario am besten funktioniert.
* Wenn Sie einen komplexen Filter für eine Ebene verwenden, sollten Sie die Verwendung mehrerer Ebenen mit Stilausdrücken in Erwägung ziehen, um die Komplexität des Filters zu verringern. Vermeiden Sie das Erstellen vieler Ebenen mit hartcodierten Stilen, wenn Sie stattdessen Stilausdrücke verwenden können, da auch eine hohe Anzahl von Ebenen zu Leistungsproblemen führen kann.

### <a name="make-sure-expressions-dont-produce-errors"></a>Sicherstellen, dass Ausdrücke keine Fehler verursachen

Ausdrücke werden häufig verwendet, um Code zum Ausführen von Berechnungen oder logischen Vorgängen zur Renderingzeit zu generieren. Stellen Sie genau wie beim Code im Rest Ihrer Anwendung sicher, dass die Berechnungen und die Logik Sinn ergeben und nicht fehleranfällig sind. Fehler in Ausdrücken verursachen Probleme bei der Auswertung des jeweiligen Ausdrucks, was zu einer schlechteren Leistung und Renderingproblemen führen kann.

Ein häufiger Fehler, auf den Sie achten sollten, ist die Verwendung von Ausdrücken, die auf einer Featureeigenschaft basieren, die möglicherweise nicht bei allen Features vorhanden ist. Im folgenden Code wird z. B. ein Ausdruck verwendet, mit dem der Wert für die Farbeigenschaft einer Blasenebene über die Eigenschaft `myColor` eines Features festgelegt wird.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

Der obige Code funktioniert problemlos, wenn alle Features in der Datenquelle über die Eigenschaft `myColor` verfügen und der Wert dieser Eigenschaft eine Farbe ist. Wenn Sie über die vollständige Kontrolle über die Daten in der Datenquelle verfügen und sicher wissen, dass alle Features über eine gültige Farbe als Wert für die Eigenschaft `myColor` verfügen, stellt dies möglicherweise kein Problem dar. Sie können im obigen Code als Schutz vor Fehlern jedoch einen `case`-Ausdruck zusammen mit einem `has`-Ausdruck verwenden, um zu überprüfen, ob das betreffende Feature über die Eigenschaft `myColor` verfügt. Wenn dies der Fall ist, kann dann der Typausdruck `to-color` verwendet werden, um zu versuchen, den Wert dieser Eigenschaft in eine Farbe umzuwandeln. Wenn die Farbe ungültig ist, kann eine Ausweichfarbe verwendet werden. Dies wird im folgenden Code veranschaulicht, in dem als Ausweichfarbe grün festgelegt ist.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Sortieren boolescher Ausdrücke vom spezifischsten zum am wenigsten spezifischen

Wenn Sie boolesche Ausdrücke verwenden, die mehrere bedingte Tests enthalten, ordnen Sie diese bedingten Tests so an, dass der spezifischste am Anfang steht und der am wenigsten spezifische am Ende. So sollte die erste Bedingung die Datenmenge verringern, die für die zweite Bedingung getestet werden muss, wodurch die Gesamtanzahl der durchzuführenden bedingten Tests reduziert wird.

### <a name="simplify-expressions"></a>Vereinfachen von Ausdrücken

Ausdrücke können leistungsstark, manchmal aber auch komplex sein. Je einfacher ein Ausdruck ist, desto schneller wird er ausgewertet. Wenn z. B. nur ein einfacher Vergleich erforderlich ist, wäre es besser, einen Ausdruck wie `['==', ['get', 'category'], 'restaurant']` zu verwenden als einen match-Ausdruck wie `['match', ['get', 'category'], 'restaurant', true, false]`. Wenn die zu überprüfende Eigenschaft in diesem Fall einen booleschen Wert aufweist, wäre ein `get`-Ausdruck sogar noch einfacher: `['get','isRestaurant']`.

## <a name="web-sdk-troubleshooting"></a>Problembehandlung für das Web-SDK

Im Folgenden sind Tipps zum Debuggen für einige bei der Entwicklung mit dem Azure Maps-Web-SDK häufig auftretende Probleme aufgeführt.

**Warum wird die Karte nicht angezeigt, wenn ich die Websteuerung lade?**

Gehen Sie folgendermaßen vor:

* Stellen Sie sicher, dass Sie der Karte Ihre hinzugefügten Authentifizierungsoptionen hinzugefügt haben. Wenn diese nicht hinzugefügt werden, lädt die Karte eine leere Canvas, da sie ohne Authentifizierung nicht auf die Basiskartendaten zugreifen kann, und auf der Netzwerk-Registerkarte der Entwicklertools des Browsers werden 401-Fehler angezeigt.
* Stellen Sie sicher, dass Sie mit dem Internet verbunden sind.
* Überprüfen Sie die Konsole in den Entwicklertools des Browsers auf Fehler. Einige Fehler können dazu führen, dass die Karte nicht gerendert wird. Debuggen Sie die Anwendung.
* Vergewissern Sie sich, dass Sie einen [unterstützten Browser](supported-browsers.md) verwenden.

**Alle meine Daten werden auf der anderen Seite der Welt angezeigt. Was ist passiert?**
Für Koordinaten (auch Positionen genannt) in den Azure Maps-SDKs wird das in der Branche für räumliche Daten übliche Standardformat `[longitude, latitude]` verwendet. Im GeoJSON-Schema wird für Koordinaten ebenfalls dieses Format verwendet. Hierbei handelt es sich um das Datenformat, das hauptsächlich in den Azure Maps-SDKs verwendet wird. Wenn Ihre Daten auf der gegenüberliegenden Seite der Welt angezeigt werden, liegt dies höchstwahrscheinlich daran, dass die Werte für die Längen- und Breitengrade in den Koordinaten-/Positionsinformationen umgekehrt wurden.

**Warum werden HTML-Marker in der Websteuerung an der falschen Stelle angezeigt?**

Überprüfen Sie Folgendes:

* Wenn Sie benutzerdefinierte Inhalte für den Marker verwenden, stellen Sie sicher, dass die Werte für die Optionen `anchor` und `pixelOffset` richtig sind. Standardmäßig wird die untere Mitte des Inhalts an der Position auf der Karte ausgerichtet.
* Stellen Sie sicher, dass die CSS-Datei für Azure Maps geladen wurde.
* Sehen Sie sich das DOM-Element des HTML-Markers an, um zu überprüfen, ob sich CSS-Code aus Ihrer App an den Marker angehängt hat und die Position beeinflusst.

**Warum werden Icons oder Text auf der Symbolebene an der falschen Stelle angezeigt?**
Überprüfen Sie, ob die Optionen `anchor` und `offset` richtig so konfiguriert sind, dass der gewünschte Teil Ihres Bilds oder Texts an der Koordinate auf der Karte ausgerichtet wird.
Wenn das Symbol nur an der falschen Stelle ist, wenn die Karte gedreht wird, überprüfen Sie die Option `rotationAlignment`. Standardmäßig drehen sich die Symbole mit dem Kartenviewport, sodass sie dem Benutzer stehend angezeigt werden. Abhängig von Ihrem Szenario kann es jedoch wünschenswert sein, die Ausrichtung der Karte als Grundlage für die Ausrichtung des Symbols zu verwenden. Legen Sie hierzu die Option `rotationAlignment` auf `’map’` fest.
Wenn das Symbol nur dann an der falschen Stelle angezeigt wird, wenn die Karte geneigt/gekippt wird, überprüfen Sie die Option `pitchAlignment`. Standardmäßig werden Symbole für den Kartenviewport stehend angezeigt, wenn die Karte geneigt oder gekippt wird. Abhängig von Ihrem Szenario kann es jedoch wünschenswert sein, die Neigung der Karte als Grundlage für die Ausrichtung des Symbols zu verwenden. Legen Sie hierzu die Option `pitchAlignment` auf `’map’` fest.

**Warum werden keine meiner Daten auf der Karte angezeigt?**

Überprüfen Sie Folgendes:

* Überprüfen Sie die Konsole in den Entwicklertools des Browsers auf Fehler.
* Stellen Sie sicher, dass eine Datenquelle erstellt, der Karte hinzugefügt und mit einer Renderingebene verbunden wurde, die ebenfalls der Karte hinzugefügt wurde.
* Fügen Sie Haltepunkte in Ihren Code ein, und führen Sie ihn schrittweise aus, um sicherzustellen, dass in der Datenquelle Daten ergänzt und die Datenquelle und die Ebenen der Karte hinzugefügt werden, ohne dass dabei Fehler auftreten.
* Testen Sie, was passiert, wenn Sie datengesteuerte Ausdrücke auf Ihrer Renderingebene entfernen. Es kann sein, dass einer davon einen Fehler enthält, der das Problem verursacht.

**Kann ich das Azure Maps-Web-SDK in einem iframe in einer Sandbox verwenden?**

Ja. Beachten Sie, dass [ein Fehler in Safari](https://bugs.webkit.org/show_bug.cgi?id=170075) verhindert, dass iframes in einer Sandbox Worker ausführen. Dies ist für die Verwendung des Azure Maps-Web-SDK jedoch erforderlich. Die Lösung für dieses Problem besteht darin, der Sandboxeigenschaft des iframe das Tag `"allow-same-origin"` hinzuzufügen.

## <a name="get-support"></a>Support

Im Folgenden sind die verschiedenen Wege aufgeführt, auf denen Sie je nach Art des Problems Support für Azure Maps anfordern können.

**Wie melde ich ein Datenproblem oder ein Problem mit einer Adresse?**

Azure Maps verfügt über ein Tool für Feedback zu Daten, in dem Datenprobleme gemeldet und nachverfolgt werden können ([https://feedback.azuremaps.com/](https://feedback.azuremaps.com/)). Für jedes gemeldete Datenproblem wird eine eindeutige URL generiert, über die Sie den Fortschritt beim Lösen des Problems nachverfolgen können. Die für das Lösen eines Datenproblems benötigte Zeit variiert abhängig von der Art des Problems sowie davon, wie leicht überprüft werden kann, ob die Änderung richtig ist. Wenn das Problem behoben wurde, wird das Update für den Renderingdienst im Rahmen des wöchentlichen Updates bereitgestellt, bei anderen Diensten wie Geocodierungs- und Routingdiensten im monatlichen Update. Eine ausführliche Anleitung für das Melden von Datenproblemen finden Sie in [diesem Dokument](how-to-use-feedback-tool.md).

**Wie melde ich einen Fehler in einem Dienst oder einer API?**

https://azure.com/support

**Wo erhalte ich technische Hilfe zu Azure Maps?**

Für das Azure Maps-Visual in Power BI erhalten Sie technische Hilfe unter https://powerbi.microsoft.com/support/, für alle anderen Azure Maps-Dienste unter https://azure.com/support. Alternativ können Sie die Entwicklerforen unter [https://docs.microsoft.com/answers/topics/azure-maps.html](/answers/topics/azure-maps.html) nutzen.

**Wie stelle ich eine Featureanfrage?**

Featureanfragen können auf unserer UserVoice-Website unter https://feedback.azure.com/forums/909172-azure-maps gestellt werden.

## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel finden Sie weitere Tipps zur Verbesserung der Benutzerfreundlichkeit Ihrer Anwendung.

> [!div class="nextstepaction"]
> [Erstellen einer barrierefreien Anwendung](map-accessibility.md)

Informieren Sie sich ausführlicher über die Terminologie, die in Azure Maps und in der Branche für räumliche Daten verwendet wird.

> [!div class="nextstepaction"]
> [Azure Maps-Glossar](glossary.md)