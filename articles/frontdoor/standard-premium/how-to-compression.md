---
title: Verbessern der Leistung durch Komprimieren von Dateien in Azure Front Door Standard/Premium (Vorschau)
description: Hier erfahren Sie, wie Sie die Geschwindigkeit von Dateiübertragungen erhöhen und die Leistung beim Laden von Seiten verbessern, indem Sie Ihre Dateien in Azure Front Door komprimieren.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: ca9c2e3b4e9873d4880385479b701d36c92238b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750180"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Verbessern der Leistung durch Komprimieren von Dateien in Azure Front Door Standard/Premium (Vorschau)

> [!Note]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

Die Dateikomprimierung stellt ein effektives Verfahren zum Verbessern der Geschwindigkeit von Dateiübertragungen und der Leistung beim Laden von Seiten dar. Durch die Komprimierung wird die Größe der Datei verringert, bevor sie vom Server gesendet wird. Die Dateikomprimierung reduziert die Bandbreitenkosten und erhöht die Benutzerfreundlichkeit.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Es gibt zwei Methoden zur Aktivierung der Dateikomprimierung:

- Sie aktivieren die Komprimierung auf Ihrem Ursprungsserver. In diesem Fall leitet Azure Front Door die komprimierten Dateien weiter und übermittelt sie an die Clients, die sie angefordert haben.
- Sie aktivieren die Komprimierung direkt auf den Azure Front Door-POP-Servern (*Komprimierung im laufendem Betrieb*). In diesem Fall komprimiert Azure Front Door die Dateien und sendet sie an die Endbenutzer.

> [!NOTE]
> Bereichsanforderungen können in verschiedene Größen komprimiert werden. Bei Azure Front Door müssen die Inhaltslängenwerte für alle GET HTTP-Anforderungen identisch sein. Senden Clients Bytebereichsanforderungen mit dem Header `accept-encoding`, der dazu führt, dass das Ursprungsobjekt mit unterschiedlichen Inhaltslängen antwortet, gibt Azure Front Door den Fehler 503 zurück. Deaktivieren Sie in diesem Fall entweder die Komprimierung für das Ursprungsobjekt bzw. Azure Front Door, oder erstellen Sie eine Regelsatzregel, um `accept-encoding` aus der Anforderung für Bytebereichsanforderungen zu entfernen.

> [!IMPORTANT]
> Es kann bis zu 10 Minuten dauern, bis die Änderungen an der Konfiguration von Azure Front Door im gesamten Netzwerk verteilt wurden. Wenn Sie die Komprimierung für Ihren CDN-Endpunkt zum ersten Mal einrichten, sollten Sie jedoch 1–2 Stunden warten, um sicherzugehen, dass die Komprimierungseinstellungen an alle POPs verteilt wurden. Erst danach lohnt sich ggf. eine Problembehandlung.

## <a name="enabling-compression"></a>Aktivieren der Komprimierung

> [!Note]
> In Azure Front Door gehört die Komprimierung zu **Zwischenspeichern aktivieren** unter „Route“. Nur wenn Sie **Zwischenspeichern aktivieren** aktivieren, können Sie die Komprimierung in Azure Front Door nutzen.

Sie haben folgende Möglichkeiten, die Komprimierung zu aktivieren:
* Während der Schnellerfassung: Wenn Sie das Zwischenspeichern aktivieren, können Sie die Komprimierung aktivieren.
* Während der benutzerdefinierten Erstellung: Aktivieren Sie Zwischenspeichern und Komprimierung, wenn Sie eine Route hinzufügen. 
* Bei der Endpoint Manager-Route.
* Auf der Seite „Optimierung“.

### <a name="enable-compression-in-endpoint-manager"></a>Aktivieren der Komprimierung in Endpoint Manager

1. Gehen Sie auf der Profilseite von Azure Front Door Standard/Premium zu **Endpoint Manager**, und wählen Sie den Endpunkt aus, für den Sie die Komprimierung aktivieren möchten.

1. Wählen Sie **Endpunkt bearbeiten** und dann die **Route** aus, für die Sie die Komprimierung aktivieren möchten. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Screenshot der Endpoint Manager-Landing Page." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Vergewissern Sie sich, dass **Zwischenspeichern aktivieren** aktiviert ist, und aktivieren Sie dann das Kontrollkästchen **Komprimierung aktivieren**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Aktivieren der Komprimierung in Endpoint Manager.":::   

1. Wählen Sie zum Speichern der Konfiguration **Aktualisieren** aus.

### <a name="enable-compression-in-optimization"></a>Aktivieren der Komprimierung unter „Optimierungen“

1. Gehen Sie auf der Profilseite von Azure Front Door Standard/Premium unter „Einstellungen“ zu **Optimierungen**. Erweitern Sie den Endpunkt, um die Liste der Routen anzuzeigen. 

1. Wählen Sie die drei Punkte neben der **Route** aus, für die die Komprimierung *deaktiviert* ist. Wählen Sie anschließend **Konfigurieren** aus.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Screenshot von der Aktivierung der Komprimierung auf der Seite „Optimierungen“." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Vergewissern Sie sich, dass **Zwischenspeichern aktivieren** aktiviert ist, und aktivieren Sie dann das Kontrollkästchen **Komprimierung aktivieren**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Screenshot der Aktivierung der Komprimierung in Endpunkt-Manager."::: 

1. Klicken Sie auf **Aktualisieren**.

## <a name="modify-compression-content-type"></a>Ändern von Komprimierungsinhaltstypen

Sie können die Standardliste der MIME-Typen auf der Seite „Optimierungen“ ändern.

1. Gehen Sie auf der Profilseite von Azure Front Door Standard/Premium unter „Einstellungen“ zu **Optimierungen**. Wählen Sie dann die **Route** aus, für die die Komprimierung *aktiviert* ist.

1. Wählen Sie die drei Punkte neben der **Route** aus, für die die Komprimierung *aktiviert* ist. Wählen Sie dann **View Compressed file types** (Zu komprimierende Dateitypen anzeigen) aus.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Screenshot der Seite „Optimierungen“." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Löschen Sie Standardformate, oder wählen Sie **Hinzufügen** aus, um neue Inhaltstypen hinzuzufügen.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Screenshot der Seite zum Anpassen der Dateikomprimierung."::: 

1. Wählen Sie **Speichern** aus, um die Komprimierungskonfiguration zu aktualisieren.

## <a name="disabling-compression"></a>Deaktivieren der Komprimierung

Sie haben folgende Möglichkeiten, die Komprimierung zu deaktivieren:
* Deaktivieren der Komprimierung in der Endpoint Manager-Route.
* Deaktivieren der Komprimierung auf der Seite „Optimierungen“.

### <a name="disable-compression-in-endpoint-manager"></a>Deaktivieren der Komprimierung in Endpoint Manager.

1. Gehen Sie auf der Profilseite von Azure Front Door Standard/Premium unter „Einstellungen“ zu **Endpoint Manager**. Wählen Sie den Endpunkt aus, für den Sie die Komprimierung deaktivieren möchten.

1. Wählen Sie **Endpunkt bearbeiten** und dann die **Route** aus, für die Sie die Komprimierung deaktivieren möchten. Deaktivieren Sie das Kontrollkästchen **Komprimierung aktivieren**.

1. Wählen Sie zum Speichern der Konfiguration **Aktualisieren** aus.

### <a name="disable-compression-in-optimizations"></a>Deaktivieren der Komprimierung unter „Optimierungen“

1. Gehen Sie auf der Profilseite von Azure Front Door Standard/Premium unter „Einstellungen“ zu **Optimierungen**. Wählen Sie dann die **Route** aus, für die die Komprimierung *aktiviert* ist.

1. Wählen Sie die drei Punkte neben der **Route** aus, für die die Komprimierung *aktiviert* ist, und wählen Sie dann *Route konfigurieren* aus.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Screenshot des Deaktivierens der Komprimierung auf der Seite „Optimierungen“."::: 

1. Deaktivieren Sie das Kontrollkästchen **Komprimierung aktivieren**.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Screenshot der Seite „Route aktualisieren“ zum Deaktivieren der Komprimierung."::: 

1. Wählen Sie zum Speichern der Konfiguration **Aktualisieren** aus.

## <a name="compression-rules"></a>Komprimierungsregeln

In Azure Front Door werden nur geeignete Dateien komprimiert. Eine Datei ist für die Komprimierung geeignet, wenn sie folgende Bedingungen erfüllt:
* Besitzt einen MIME-Typ 
* Größer als 1 KB
* Kleiner als 8 MB

Diese Profile unterstützen die folgenden Komprimierungscodierungen:
* GZIP (GNU Zip)
* Brotli 

Falls die Anforderung mehrere Komprimierungstypen unterstützt, hat die Brotli-Komprimierung Vorrang.

Wenn in einer Anforderung für eine Ressource Gzip-Komprimierung angegeben ist und die Anforderung zu einem Cachefehler führt, führt Azure Front Door die Gzip-Komprimierung der Ressource direkt auf dem POP-Server durch. Anschließend wird die komprimierte Datei aus dem Cache bereitgestellt.

Wenn der Ursprung die Codierung für segmentierte Übertragung (CTE) verwendet, um komprimierte Daten an das Azure Front Door-POP zu senden, werden Antwortgrößen von mehr als 8 MB nicht unterstützt. 

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Konfigurieren eines ersten [Regelsatzes](how-to-configure-rule-set.md)
- Weitere Informationen zu [Vergleichsbedingungen für Regelsätze](concept-rule-set-match-conditions.md)
- Weitere Informationen zum [Azure Front Door-Regelsatz](concept-rule-set.md)
