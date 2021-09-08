---
title: Verwenden der Azure Maps-Schnellansicht für Zeichnungsfehler
description: In diesem Artikel erfahren Sie, wie Sie von der Creator-Konvertierungs-API zurückgegebene Warnungen und Fehler visualisieren.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: c541a35bf2ef79fd058a58713afd927413ec8fcf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346618"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator"></a>Verwenden der Azure Maps-Schnellansicht für Zeichnungsfehler mit Creator


Die Schnellansicht für Zeichnungsfehler ist eine eigenständige Webanwendung zum Anzeigen von [Warnungen und Fehlern im Zusammenhang mit Zeichnungspaketen](drawing-conversion-error-codes.md), die im Zuge des Konvertierungsprozesses erkannt wurden. Bei der Webanwendung für die Fehlerschnellansicht handelt es sich um eine statische Seite, die ohne Internetverbindung verwendet werden kann.  Die Fehlerschnellansicht kann zur Behebung von Fehlern und Warnungen verwendet werden, um die [Anforderungen für Zeichnungspakete](drawing-requirements.md) zu erfüllen. Im Falle eines erkannten Fehlers wird von der [Azure Maps-Konvertierungs-API](/rest/api/maps/v2/conversion) eine Antwort mit einem Link zur Fehlerschnellansicht zurückgegeben.

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Herunterladen der Schnellansicht für Zeichnungsfehler müssen folgende Schritte ausgeführt werden:

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)
3. [Erstellen einer Creator-Ressource](how-to-manage-creator.md)

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung verwenden.

## <a name="download"></a>Download

1. Laden Sie Ihr Zeichnungspaket in den Azure Maps Creator-Dienst hoch, um eine eindeutige Daten-ID (`udid`) für das hochgeladene Paket zu erhalten. Die Schritte zum Hochladen eines Pakets finden Sie unter [Hochladen eines Zeichnungspakets](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Nach dem Hochladen des Zeichnungspakets wird die eindeutige Daten-ID (`udid`) für das hochgeladene Paket verwendet, um das Paket in Kartendaten zu konvertieren. Die Schritte zum Konvertieren eines Pakets finden Sie unter [Konvertieren eines Zeichnungspakets](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Nach erfolgreichem Abschluss des Konvertierungsvorgangs wird kein Link zum Tool für die Fehlerschnellansicht zurückgegeben.

3. Sehen Sie sich in der Postman-Anwendung auf der Registerkarte **Headers** (Header) die von der Konvertierungs-API zurückgegebene Eigenschaft `diagnosticPackageLocation` an. Die Antwort sollte in etwa wie der folgende JSON-Code aussehen:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "properties": {
            "diagnosticPackageLocation": "https://us.atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=2.0"
        }
    }
    ```

4. Laden Sie die Schnellansicht für Zeichnungspaketfehler herunter, indem Sie eine Anforderung vom Typ `HTTP-GET` an die URL `diagnosticPackageLocation` senden.

## <a name="setup"></a>Einrichten

Das unter dem Link `diagnosticPackageLocation` heruntergeladene ZIP-Paket enthält zwei Dateien:

* _VisualizationTool.zip_: Enthält den Quellcode, die Medien und die Webseite für die Schnellansicht für Zeichnungsfehler.
* _ConversionWarningsAndErrors.jsauf_: Enthält eine formatierte Liste mit Warnungen, Fehlern und anderen Details, die von der Schnellansicht für Zeichnungsfehler verwendet werden.

Entzippen Sie den Ordner _VisualizationTool.zip_. Er enthält folgende Elemente:

* Ordner _assets_: Bilder und Mediendateien
* Ordner _static_: Quellcode
* Datei _index.html_: die Webanwendung

Öffnen Sie die Datei _index.html_ mit einer der im Anschluss angegebenen Browserversionen. Sie können auch eine andere Version verwenden, sofern diese ein vergleichbar kompatibles Verhalten wie die aufgeführte Version bietet.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Verwenden des Schnellansichtstools für Zeichnungsfehler

Nachdem Sie das Schnellansichtstool für Zeichnungsfehler gestartet haben, wird die Uploadseite angezeigt. Die Uploadseite enthält ein Drag & Drop-Feld. Das Drag & Drop-Feld kann auch als Schaltfläche zum Starten eines Datei-Explorer-Dialogfelds verwendet werden.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Schnellansicht-App für Zeichnungsfehler: Startseite":::

Die Datei _ConversionWarningsAndErrors.json_ wurde am Stamm des heruntergeladenen Verzeichnisses platziert. Um die _ConversionWarningsAndErrors.json_ zu laden, ziehen Sie die Datei per Drag & Drop in das Feld. Oder klicken Sie auf das Feld, suchen Sie die Datei im `File Explorer dialogue`, und laden Sie die Datei hoch.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Schnellansicht-App für Zeichnungsfehler: Drag & Drop-Bereich zum Laden von Daten":::

Nachdem die Datei _ConversionWarningsAndErrors.json_ geladen wurde, wird eine Liste Ihrer Zeichnungspaketfehler und -warnungen angezeigt. Für alle Fehler bzw. Warnungen sind jeweils die Schicht, die Ebene und eine ausführliche Meldung angegeben. Ausführliche Informationen zu einem Fehler oder einer Warnung erhalten Sie durch Klicken auf den Link **Details**. Daraufhin wird unter der Liste ein entsprechender Abschnitt angezeigt. Nun können Sie zu den einzelnen Fehlern navigieren, um weitere Informationen zur Fehlerbehebung zu erhalten.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Schnellansicht-App für Zeichnungsfehler: Fehler und Warnungen":::

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr [Zeichnungspaket den Anforderungen entspricht](drawing-requirements.md), können Sie es mithilfe des [Azure Maps-Konvertierungsdiensts](/rest/api/maps/v2/conversion) in ein Dataset konvertieren. Anschließend können Sie das Webmodul für Gebäudepläne verwenden, um Ihre Anwendung zu entwickeln. Weitere Informationen finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Fehler und Warnungen bei der Zeichnungskonvertierung](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Leitfaden für Zeichnungspakete](drawing-package-guide.md)

> [!div class="nextstepaction"]
> [Creator für Gebäudepläne](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Verwenden des Moduls „Gebäudepläne“ von Azure Maps](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementieren dynamischer Stile für Creator-Gebäudepläne](indoor-map-dynamic-styling.md)