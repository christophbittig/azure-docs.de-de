---
title: Arbeiten mit Gebäudeplänen in Azure Maps Creator
description: In diesem Artikel werden Konzepte vorgestellt, die für die Azure Maps Creator-Dienste gelten.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 0e4f25e140c9b7d2693c94ead25b375e2298adbc
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400013"
---
# <a name="creator-for-indoor-maps"></a>Creator für Gebäudepläne

In diesem Artikel werden Konzepte und Tools vorgestellt, die für Azure Maps Creator gelten. Lesen Sie diesen Artikel, bevor Sie mit der Verwendung der Azure Maps Creator-API und des SDK beginnen.

Sie können Creator verwenden, um Anwendungen mit Kartenfunktionen zu entwickeln, die auf Daten von Gebäudeplänen basieren. In diesem Artikel wird beschrieben, wie Sie Ihre Kartendaten hochladen, konvertieren, erstellen und verwenden.  In der Regel wird der Workflow von zwei verschiedenen Personas mit jeweils unterschiedlichen Kompetenz- und Zuständigkeitsbereichen ausgeführt:

- Kartenersteller: Zuständig für die Zusammenstellung und Vorbereitung der Kartendaten.
- Creator-Kartendatenbenutzer: Nutzt Kundenkartendaten in Anwendungen.

Das folgende Diagramm veranschaulicht den vollständigen Workflow:

![Workflow für Creator-Kartendaten](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>Azure Maps Creator erstellen

Damit Sie Creator-Dienste verwenden können, muss Azure Maps Creator in einem Azure Maps-Konto mit dem Gen 2-Tarif erstellt werden. Informationen zum Erstellen von Azure Maps Creator in Azure Maps finden Sie unter [Verwalten von Azure Maps Creator](how-to-manage-creator.md).

> [!TIP]
> Preisinformationen finden Sie im Abschnitt *Creator* unter [Azure Maps – Preise](https://aka.ms/CreatorPricing).

## <a name="creator-authentication"></a>Creator-Authentifizierung

Von Creator werden die Zugriffssteuerungseinstellungen (IAM) von Azure Maps geerbt. Alle API-Aufrufe für Datenzugriff müssen mit Authentifizierungs- und Autorisierungsregeln gesendet werden.

Creator-Nutzungsdaten fließen in Ihre Azure Maps-Nutzungsdiagramme und in das Aktivitätsprotokoll mit ein. Weitere Informationen finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).

>[!Important]
>Es wird empfohlen, Folgendes zu verwenden:
>
> - Azure Active Directory (Azure AD) in allen Lösungen, die mit einem Azure Maps-Konto und unter Verwendung von Creator-Diensten erstellt werden. Weitere Informationen zu Azure AD finden Sie unter [Azure AD-Authentifizierung](azure-maps-authentication.md#azure-ad-authentication).
>
>- Einstellungen für die rollenbasierte Zugriffssteuerung. Mithilfe dieser Einstellungen können Kartenersteller als Rolle „Azure Maps-Datenmitwirkender“ und Creator-Kartendatenbenutzer als Rolle „Azure Maps-Datenleser“ fungieren. Weitere Informationen finden Sie unter [Autorisierung mit rollenbasierter Zugriffssteuerung](azure-maps-authentication.md#authorization-with-role-based-access-control).

## <a name="creator-data-item-types"></a>Creator-Datenelementtypen

Creator-Dienste erstellen, speichern und verwenden verschiedene Arten von Daten. Diese werden in den folgenden Abschnitten definiert und erläutert. Zu den möglichen Datentypen von Creator-Datenelementen zählen:

- Konvertierte Daten
- Dataset
- Kachelset
- Featurezustandsset

## <a name="upload-a-drawing-package"></a>Hochladen eines Zeichnungspakets

Creator erfasst Daten von Gebäudeplänen, indem er ein hochgeladenes Zeichnungspaket konvertiert. Das Zeichnungspaket stellt eine gebaute oder umgebaute Einrichtung dar. Informationen zu den Anforderungen für Zeichnungspakete finden Sie unter [Anforderungen für Zeichnungspakete](drawing-requirements.md).

Verwenden Sie die [Datenupload-API von Azure Maps](/rest/api/maps/data-v2/update-preview), um ein Zeichnungspaket hochzuladen. Nach dem Hochladen des Zeichnungspakets wird von der Datenupload-API ein Benutzerdatenbezeichner (`udid`) zurückgegeben. Der Benutzerdatenbezeichner (`udid`) kann dann verwendet werden, um das hochgeladene Paket in Gebäudeplandaten zu konvertieren.

## <a name="convert-a-drawing-package"></a>Konvertieren eines Zeichnungspakets

Das hochgeladene Zeichnungspaket wird vom [Azure Maps-Konvertierungsdienst](/rest/api/maps/v2/conversion) in Gebäudeplandaten konvertiert. Der Konvertierungsdienst überprüft auch das Paket. Validierungsprobleme werden in zwei Typen unterteilt:

- Fehler: Wenn Fehler erkannt werden, tritt beim Konvertierungsvorgang ein Fehler auf. Wenn ein Fehler auftritt, stellt der Konvertierungsdienst einen Link zur eigenständigen Webanwendung [Azure Maps-Schnellansicht für Zeichnungsfehler](drawing-error-visualizer.md) bereit. Mithilfe der Schnellansicht für Zeichnungsfehler können Sie [Warnungen und Fehler im Zusammenhang mit Zeichnungspaketen](drawing-conversion-error-codes.md) überprüfen, die während des Konvertierungsvorgangs aufgetreten sind. Nachdem Sie die Fehler korrigiert haben, können Sie versuchen, das Paket hochzuladen und zu konvertieren.
- Warnungen: Wenn Warnungen erkannt werden, ist die Konvertierung erfolgreich. Es empfiehlt sich jedoch, alle Warnungen zu überprüfen und zu beheben. Im Falle einer Warnung wurde ein Teil der Konvertierung ignoriert oder automatisch korrigiert. Wird die Warnung nicht behoben, kann dies Fehler in späteren Prozessen zur Folge haben.
Weitere Informationen finden Sie unter [Warnungen und Fehler im Zusammenhang mit Zeichnungspaketen](drawing-conversion-error-codes.md).

## <a name="create-indoor-map-data"></a>Erstellen von Gebäudeplandaten

Azure Maps Creator bietet folgende Dienste zur Unterstützung der Kartenerstellung:

- [Datasetdienst](/rest/api/maps/v2/dataset).
- [Kachelsetdienst](/rest/api/maps/v2/tileset).
Verwenden Sie den Kachelsetdienst, um eine vektorbasierte Darstellung eines Datasets zu erstellen. Anwendungen können ein Kachelset verwenden, um eine auf visuellen Kacheln basierende Ansicht des Datasets darzustellen.
- [Featurezustandsdienst](/rest/api/maps/v2/feature-state). Verwenden Sie den Featurezustandsdienst, um dynamische Kartenstile zu unterstützen. Von Anwendungen können dynamische Kartenstile verwendet werden, um Echtzeitereignisse für vom IoT-System bereitgestellte Bereiche darzustellen.

### <a name="datasets"></a>Datasets

Bei einem Dataset handelt es sich um eine Sammlung von Gebäudeplanfeatures. Die Gebäudeplanfeatures stellen Einrichtungen dar, die in einem konvertierten Zeichnungspaket definiert sind. Nachdem Sie ein Dataset mit dem [Datasetdienst](/rest/api/maps/v2/dataset) erstellt haben, können Sie eine beliebige Anzahl von [Kachelsets](#tilesets) oder [Featurezustandssets](#feature-statesets) erstellen.

Entwickler können jederzeit den [Datasetdienst](/rest/api/maps/v2/dataset) verwenden, um einem vorhandenen Dataset Einrichtungen hinzuzufügen oder Einrichtungen daraus zu entfernen. Weitere Informationen zum Aktualisieren eines vorhandenen Datasets mithilfe der API finden Sie unter den Anfügeoptionen des [Datasetdiensts](/rest/api/maps/v2/dataset). Ein Beispiel für die Aktualisierung eines Datasets finden Sie unter [Datenpflege](#data-maintenance).

### <a name="tilesets"></a>Kachelsets

Ein Kachelset ist eine Sammlung von Vektordaten, die einen Satz von einheitlichen Rasterkacheln darstellt. Entwickler können den [Kachelsetdienst](/rest/api/maps/v2/tileset) verwenden, um Kachelsets aus einem Dataset zu erstellen.

Erstellen Sie mehrere Kachelsets aus dem gleichen Dataset, um unterschiedliche Inhaltsebenen darzustellen. Sie können beispielsweise ein Kachelset mit Möbeln und Ausstattung und ein weiteres ohne Möbel und Ausstattung erstellen. Oder Sie erstellen ein Kachelset mit den neuesten Datenaktualisierungen und ein weiteres Kachelset ohne die neuesten Datenaktualisierungen.

Zusätzlich zu den Vektordaten stellt das Kachelset Metadaten für die Kartenrenderingoptimierung bereit. Kachelsetmetadaten enthalten beispielsweise einen Mindest- und Höchstzoomfaktor für das Kachelset. Die Metadaten bieten auch einen Begrenzungsrahmen, der den geografischen Bereich des Kachelsets definiert. Der Begrenzungsrahmen ermöglicht es einer Anwendung, den korrekten Mittelpunkt programmgesteuert festzulegen. Weitere Informationen zu Kachelsetmetadaten finden Sie unter [Kachelsetlisten-API](/rest/api/maps/v2/tileset/list).

Nachdem ein Kachelset erstellt wurde, kann es vom [Render V2-Dienst](#render-v2-get-map-tile-api) abgerufen werden.

Wenn ein Kachelset veraltet ist und nicht mehr genutzt wird, können Sie das Kachelset löschen. Informationen zum Löschen von Kachelsets finden Sie unter [Datenpflege](#data-maintenance).

>[!NOTE]
>Ein Kachelset ist vom Dataset unabhängig, aus dem es erstellt wurde. Wenn Sie Kachelsets auf der Grundlage eines Datasets erstellen und dieses Dataset anschließend aktualisieren, werden die Kachelsets nicht aktualisiert. 
>
>Um Änderungen in einem Dataset widerzuspiegeln, müssen Sie neue Kachelsets erstellen. Wenn Sie ein Kachelset löschen, hat dies keine Auswirkungen auf das Dataset.

### <a name="feature-statesets"></a>Featurezustandssets

Featurezustandssets sind Sammlungen dynamischer Eigenschaften (*Zustände*), die Datasetfeatures wie Räumen oder Ausstattung zugewiesen sind. Ein Beispiel für einen *Zustand* ist etwa die Temperatur oder die Belegung. Jeder *Zustand* ist ein Schlüssel-Wert-Paar, das den Namen der Eigenschaft, den Wert und den Zeitstempel der letzten Aktualisierung enthält.

Mit dem [Featurezustandsdienst](/rest/api/maps/v2/feature-state/create-stateset) können Sie einen Featurezustand für ein Dataset erstellen und verwalten. Das Zustandsset wird durch einen oder mehrere *Zustände* definiert. Zu jedem Feature wie etwa einem Raum kann ein *Zustand* hinzugefügt werden.

Der Wert jedes *Zustands* in einem Zustandsset kann von IoT-Geräten oder anderen Anwendungen aktualisiert oder abgerufen werden.  Mithilfe der [API zur Featurezustandsaktualisierung](/rest/api/maps/v2/feature-state/update-states) können Geräte, die die Raumbelegung messen, systematisch die Zustandsänderung eines Raums veröffentlichen.

Eine Anwendung kann ein Featurezustandsset verwenden, um Features in einer Einrichtung dem aktuellen Zustand und dem jeweiligen Kartenstil entsprechend dynamisch zu rendern. Weitere Informationen zum Festlegen des Stils von Features in einer Renderingkarte mithilfe von Featurezustandssets finden Sie unter [Modul für Gebäudepläne](#indoor-maps-module).

>[!NOTE]
>Genau wie bei Kachelsets wirkt sich das Ändern eines Datasets nicht auf das vorhandene Featurezustandsset aus, und das Löschen eines Featurezustandssets hat keine Auswirkung auf das Dataset, an das es angefügt ist.

## <a name="using-indoor-maps"></a>Verwenden von Gebäudeplänen

### <a name="render-v2-get-map-tile-api"></a>Render V2-API zum Abrufen von Kartenkacheln

Die [Render V2-API zum Abrufen von Kartenkacheln](/rest/api/maps/render-v2/get-map-tile) von Azure Maps wurde erweitert und unterstützt jetzt Creator-Kartenkacheln.

Anwendungen können die Render V2-API zum Abrufen von Kartenkacheln verwenden, um Kachelsets abzurufen. Die Kachelsets können anschließend in ein Kartensteuerelement oder SDK integriert werden. Ein Beispiel für ein Kartensteuerelement, das den Render V2-Dienst verwendet, finden Sie unter [Modul für Gebäudepläne](#indoor-maps-module).

### <a name="web-feature-service-api"></a>Web Feature Service-API

Sie können die [WFS-API (Web Feature Service)](/rest/api/maps/v2/wfs) zum Abfragen von Datasets verwenden. WFS hält die [Features der Open Geospatial Consortium-API](http://docs.opengeospatial.org/DRAFTS/17-069r1.html) ein. Mit der WFS-API können Sie Features innerhalb des Datasets abfragen. Beispielsweise können Sie WFS verwenden, um alle mittelgroßen Besprechungsräume in einem bestimmten Stockwerk einer bestimmten Einrichtung zu finden.

### <a name="alias-api"></a>Alias-API

Von Creator-Diensten wie Konvertierung, Dataset, Kachelset und Featurezustand wird ein Bezeichner für jede Ressource zurückgegeben, die über die APIs erstellt wird. Mit der [Alias-API](/rest/api/maps/v2/alias) können Sie einen Alias zuweisen, um auf einen Ressourcenbezeichner zu verweisen.

### <a name="indoor-maps-module"></a>Modul für Gebäudepläne

Das [Azure Maps Web-SDK](./index.yml) umfasst das Modul für Gebäudepläne. Dieses Modul bietet erweiterte Funktionen für die Azure Maps-Bibliothek für *Kartensteuerelemente*. Das Modul für Gebäudepläne rendert Gebäudepläne, die in Creator erstellt wurden. Es integriert Widgets wie etwa die *Stockwerkauswahl*, die Benutzer bei der Visualisierung der unterschiedlichen Stockwerke unterstützt.

Das Modul für Gebäudepläne ermöglicht die Erstellung von Webanwendungen, von denen Gebäudeplandaten in andere [Azure Maps-Dienste](./index.yml) integriert werden. Dies wird in der Regel dazu genutzt, Gebäudepläne mit Informationen aus anderen Karten anzureichern. Hierzu zählen beispielsweise Straßen und Bilder sowie Wetter- und Verkehrsdaten.

Das Modul für Gebäudepläne unterstützt auch dynamische Kartenstile. Eine ausführliche exemplarische Vorgehensweise für die Implementierung dynamischer Stile für Featurezustandssets in einer Anwendung finden Sie unter [Verwenden des Moduls „Gebäudepläne“ von Azure Maps](how-to-use-indoor-module.md).

### <a name="azure-maps-integration"></a>Integration von Azure Maps

Wenn Sie mit der Entwicklung von Lösungen für Gebäudepläne beginnen, haben Sie verschiedene Möglichkeiten, um vorhandene Azure Maps-Funktionen zu integrieren. So können beispielsweise Assetnachverfolgungs- oder Sicherheitsszenarien implementieren, indem Sie die [Geofence-API von Azure Maps](/rest/api/maps/spatial/postgeofence) mit Creator-Gebäudeplänen kombinieren. Mithilfe der Geofence-API kann beispielsweise bestimmt werden, ob ein Mitarbeiter bestimmte Gebäudebereiche betritt oder verlässt. Weitere Informationen zum Herstellen einer Verbindung zwischen Azure Maps und IoT-Telemetrie finden Sie im Tutorial [Implementieren der räumlichen IoT-Analyse mit Azure Maps](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Datenpflege

 Mit der Azure Maps Creator-API zum Auflisten, Aktualisieren und Löschen können Sie Ihre Datasets, Kachelsets und Featurezustandssets auflisten, aktualisieren und löschen.

>[!NOTE]
>Wenn Sie eine Liste von Elementen überprüfen, um zu entscheiden, ob sie gelöscht werden sollen, müssen die Auswirkungen dieses Löschvorgangs auf alle abhängigen APIs oder Anwendungen berücksichtigt werden. Wenn Sie beispielsweise ein Kachelset löschen, das von einer Anwendung über die [Render V2-API zum Abrufen von Kartenkacheln](/rest/api/maps/render-v2/get-map-tile) verwendet wird, kann dieses Kachelset von der Anwendung nicht mehr gerendert werden.

### <a name="example-updating-a-dataset"></a>Beispiel: Aktualisieren eines Datasets

Im folgenden Beispiel wird gezeigt, wie Sie ein Dataset aktualisieren, ein neues Kachelset erstellen und ein altes Kachelset löschen:

1. Führen Sie die Schritte in den Abschnitten [Hochladen eines Zeichnungspakets](#upload-a-drawing-package) und [Konvertieren eines Zeichnungspakets](#convert-a-drawing-package) aus, um das neue Zeichnungspaket hochzuladen und zu konvertieren.
2. Verwenden Sie die [Dataseterstellungs-API](/rest/api/maps/v2/dataset/create), um die konvertierten Daten an das vorhandene Dataset anzufügen.
3. Verwenden Sie die [Kachelseterstellungs-API](/rest/api/maps/v2/tileset/create), um ein neues Kachelset aus dem aktualisierten Dataset zu generieren.
4. Speichern Sie die neue Kachelset-ID (**tilesetId**) für den nächsten Schritt.
5. Aktualisieren Sie den Kachelsetbezeichner in Ihrer Anwendung, um die Visualisierung des aktualisierten Campusdatasets zu ermöglichen. Wird das alte Kachelset nicht mehr verwendet, können Sie es löschen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen von Gebäudeplänen mithilfe von Creator](tutorial-creator-indoor-maps.md)
