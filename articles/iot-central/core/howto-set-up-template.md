---
title: Definieren eines neuen IoT-Gerätetyps in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie in Ihrer Azure IoT Central-Anwendung eine neue Azure IoT-Gerätevorlage erstellen. Sie definieren die Telemetriedaten, den Zustand, die Eigenschaften und die Befehle für den Typ.
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 59c95633322f279504ded61bbf02a7415b1337b9
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446290"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definieren eines neuen IoT-Gerätetyps in Ihrer Azure IoT Central-Anwendung

Eine Gerätevorlage ist eine Blaupause, die die Merkmale und das Verhalten eines Gerätetyps definiert, der eine Verbindung mit einer [Azure IoT Central-Anwendung](concepts-app-templates.md) herstellt.

In diesem Artikel wird die Erstellung einer Gerätevorlage in IoT Central beschrieben. Sie können z. B. eine Gerätevorlage für einen Sensor erstellen, der Telemetriedaten z. B. die Temperatur und Eigenschaften wie den Standort sendet. Anhand dieser Gerätevorlage kann ein Bediener echte Geräte erstellen und verbinden.

Der folgende Screenshot zeigt ein Beispiel für eine Gerätevorlage:

:::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Ein Screenshot, der eine Gerätevorlage zeigt":::.

Die Gerätevorlage beinhaltet die folgenden Abschnitte:

- Modell – Verwenden Sie das Modell, um zu definieren, wie Ihr Gerät mit Ihrer IoT Central-Anwendung interagiert. Jedes Modell verfügt über eine eindeutige Modell-ID und definiert die Funktionen des Geräts. Zusammengehörige Funktionen sind in Schnittstellen gruppiert. Mit Schnittstellen können Sie Komponenten modellübergreifend wiederverwenden oder eine Vererbung verwenden, um den Satz von Funktionen zu erweitern.
- Cloudeigenschaften – Verwenden Sie die Cloudeigenschaften, um die Informationen zu definieren, die Ihre IoT Central-Anwendung über Ihre Geräte speichert. Beispielsweise kann eine Cloudeigenschaft das Datum der letzten Wartung eines Geräts erfassen.
- Anpassen – Verwenden Sie die Anpassungen, um Funktionen zu ändern. Geben Sie beispielsweise die Minimal- und Höchsttemperaturwerte für eine Eigenschaft an.
- Ansichten – Verwenden Sie die Ansichten, um die Daten des Geräts zu visualisieren und die Formulare zum Verwalten und Steuern eines Geräts.

Weitere Informationen finden Sie unter [Was sind Gerätevorlagen?](concepts-device-templates.md).

## <a name="create-a-device-template"></a>Erstellen einer Gerätevorlage

Sie haben mehrere Möglichkeiten zum Erstellen von Gerätevorlagen:

- Entwerfen Sie die Gerätevorlage in der IoT Central GUI.
- Importieren Sie eine Gerätevorlage aus dem [Azure Certified for IoT-Gerätekatalog](https://aka.ms/iotdevcat). Optional, können Sie die Gerätevorlage Ihren Anforderungen in IoT Central anpassen.
- Wenn das Gerät eine Verbindung mit Azure IoT-Central herstellt, lassen Sie es die Modell-ID des Modells senden, das es implementiert. IoT Central verwendet die Modell-ID, um das Modell von dem Modell-Repository abzurufen und um eine Gerätevorlage zu erstellen. Fügen Sie alle Cloudeigenschaften, Anpassungen und Ansichten zu der Gerätevorlage hinzu, die Ihre IoT Central-Anwendung benötigt.
- Wenn das Gerät eine Verbindung mit dem IoT Central herstellt, lassen Sie IoT Central [automatisch eine Gerätevorlagendefinition](#autogenerate-a-device-template) aus den Daten generieren, die das Gerät sendet.
- Erstellen Sie ein Gerätemodell mithilfe der [Digital Twins Definition Language (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Importieren Sie das Gerätemodell manuell in Ihre IoT Central-Anwendung. Fügen Sie dann alle Cloudeigenschaften, Anpassungen und Ansichten hinzu, die Ihre IoT Central-Anwendung benötigt.
- Sie können einer IoT Central-Anwendung Gerätevorlagen auch mithilfe der [REST-API](/learn/modules/manage-iot-central-apps-with-rest-api/) oder der [CLI](howto-manage-iot-central-from-cli.md) hinzufügen.

> [!NOTE]
> In jedem Fall muss der Gerätecode die im Modell definierten Funktionen implementieren. Die Implementierung des Gerätecodes wird nicht von den Abschnitten der Cloudeigenschaften, Anpassungen und Ansichten der Gerätevorlage beeinflusst.

In diesem Abschnitt erfahren Sie, wie Sie eine Gerätevorlage aus dem Katalog importieren und mithilfe der IoT Central GUI anpassen. Dieses Beispiel verwendet die **ESP32-Azure IoT Kit** Gerätevorlage aus dem Gerätekatalog:

1. Wählen Sie zum Hinzufügen einer neuen Gerätevorlage auf der Seite **Gerätevorlagen** die Option **+ Neu** aus.
1. Scrollen Sie auf der Seite **Typ auswählen** nach unten, bis im Abschnitt **Vorkonfigurierte Gerätevorlage verwenden** die Kachel **ESP32-Azure IoT-Kit** angezeigt wird.
1. Wählen Sie die Kachel **ESP32-Azure IoT-Kit** und dann die folgende Option aus: **Next: Review** (Weiter: Überprüfen).
1. Wählen Sie auf der Seite **Überprüfen** die Option **Erstellen** aus.
Der Name der Vorlage, die Sie erstellt haben, lautet **Sensor Controller**. Das Modell enthält Komponenten, z. B. **Sensor Controller**, **SensorTemp** und **Schnittstelle „Geräteinformationen“** . Mit Komponenten werden die Funktionen eines ESP32-Geräts definiert. Zu den Funktionen gehören die Bereiche Telemetrie, Eigenschaften und Befehle.

:::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Ein Screenshot, der eine Gerätevorlage für einen Sensor-Controller zeigt":::.

## <a name="autogenerate-a-device-template"></a>Automatisches Generieren einer Gerätevorlage

Sie können auch automatisch eine Gerätevorlage aus einem verbundenen Gerät erstellen, das noch keiner Gerätevorlage zugewiesen ist. IoT Central verwendet die Telemetrie- und Eigenschaftswerte, die das Gerät sendet, um ein Gerätemodell abzuleiten.

> [!NOTE]
> Derzeit kann diese Previewfunktion keine Telemetriedaten und Eigenschaften von Komponenten verwenden. Funktionen können nur aus Stammtelemetriedaten und -eigenschaften generiert werden.

Die folgenden Schritte zeigen, wie Sie diese Funktion verwenden:

1. Verbinden Ihr Gerät mit IoT Central, und beginnen Sie mit dem Senden der Daten. Wenn Sie die Daten in der Ansicht **Rohdaten** sehen, wählen Sie in der Dropdown-Liste **Vorlage verwalten** die Option **Vorlage automatisch erstellen** aus:

    :::image type="content" source="media/howto-set-up-template/infer-model-1.png" alt-text="Ein Screenshot, der die Rohdaten von nicht zugewiesenen Geräten zeigt":::.

1. Nehmen Sie auf der Seite **Datenvorschau** die erforderlichen Änderungen an den Rohdaten vor, und wählen Sie **Vorlage erstellen** aus:

    :::image type="content" source="media/howto-set-up-template/infer-model-2.png" alt-text="Ein Screenshot, der die Änderung der Datenvorschau, mit der Sie die Daten bearbeiten können, die IoT Central zur Erstellung der Gerätevorlage verwendet, zeigt":::.

1. IoT Central generiert eine Vorlage basierend auf dem Datenformat, das auf der Seite **Datenvorschau** angezeigt wird, und weist ihr das Gerät zu. Sie können auf der Seite **Gerätevorlagen** weitere Änderungen an der Gerätevorlage vornehmen, Sie können sie z. B. umbenennen oder ihr weitere Funktionen hinzufügen:

    :::image type="content" source="media/howto-set-up-template/infer-model-3.png" alt-text="Ein Screenshot, der das Umbenennen der automatisch generierten Gerätevorlage zeigt":::.

## <a name="manage-a-device-template"></a>Verwalten einer Gerätevorlage

Sie können eine Vorlage auf der jeweiligen Editor-Seite umbenennen oder löschen.

Nachdem Sie die Vorlage definiert haben, können Sie sie veröffentlichen. Bis die Vorlage veröffentlicht wurde, können Sie kein Gerät damit verbinden, und das Gerät wird auf der Seite **Geräte** nicht angezeigt.

Weitere Informationen zum Ändern von Gerätevorlagen und deren Versionsverwaltung finden Sie unter [Bearbeiten einer vorhandenen Gerätevorlage](howto-edit-device-template.md).

## <a name="models"></a>Modelle

Ein Gerätemodell definiert, wie ein Gerät mit Ihrer IoT Central-Anwendung interagiert. Passen Sie Ihr Modell mit weiteren Funktionen an, fügen Sie Schnittstellen hinzu, um Funktionen zu erben oder fügen Sie neue Komponenten hinzu, die auf anderen Schnittstellen basieren.

Um ein Gerätemodell zu erstellen, haben Sie folgende Möglichkeiten:

- Verwenden Sie IoT Central, um ein benutzerdefiniertes Modell ohne Vorlage zu erstellen.
- Importieren Sie ein DTDL-Modell aus einer JSON-Datei. Der Ersteller eines Geräts hat möglicherweise Visual Studio Code verwendet, um ein Gerätemodell für Ihre Anwendung zu erstellen.
- Wählen Sie eines der Geräte aus dem Gerätekatalog aus. Mit dieser Option wird das Gerätemodell importiert, das der Hersteller für dieses Gerät veröffentlicht hat. Ein auf diese Weise importiertes Gerätemodell wird automatisch veröffentlicht.

1. Wählen Sie zum Anzeigen der Modell-ID die Stammschnittstelle in dem Modell und dann **Identität bearbeiten** aus:

    :::image type="content" source="media/howto-set-up-template/view-id.png" alt-text="Ein Screenshot, der die Modell-ID für die Stammschnittstelle der Gerätevorlage zeigt":::.

1. Um die Komponenten-ID anzuzeigen, wählen Sie **Identität bearbeiten** auf einer der Komponentenschnittstellen in dem Modell aus.

Weitere Informationen finden Sie im [Modellleitfaden für IoT Plug & Play](../../iot-pnp/concepts-modeling-guide.md).

### <a name="interfaces-and-components"></a>Schnittstellen und Komponenten

So zeigen Sie die Schnittstellen in Ihrem Gerätemodell an und verwalten sie:

1. Navigieren Sie zur Seite **Gerätevorlagen**, und wählen Sie die Gerätevorlage, die Sie erstellt haben, aus. Die Schnittstellen sind in dem Abschnitt **Modelle** in der Gerätevorlage aufgeführt. Der folgende Screenshot zeigt ein Beispiel für die **Sensor-Controller** Stammschnittstelle in einer Gerätevorlage:

    :::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Ein Screenshot, der die Stammschnittstelle für ein Modell zeigt"::: 

1. Wählen Sie die Auslassungszeichen aus, um der Stammschnittstelle eine geerbte Schnittstelle oder Komponente hinzuzufügen. Weitere Informationen über die Schnittstellen und Komponenten finden Sie im Leitfaden zu Modellierungen unter [Mehrere Komponenten](../../iot-pnp/concepts-modeling-guide.md#multiple-components).

    :::image type="content" source="media/howto-set-up-template/add-interface.png" alt-text="Hinzufügen einer Schnittstelle oder Komponente":::

1. Um ein Modell oder eine Schnittstelle zu exportieren, wählen Sie **Exportieren** aus.

1. Um die DTDL für eine Schnittstelle oder Funktion anzuzeigen oder zu bearbeiten, wählen Sie **DTDL bearbeiten** aus.

### <a name="capabilities"></a>Funktionen

Wählen Sie **+ Funktion hinzufügen** aus, um eine Funktion zu einer Schnittstelle oder Komponente hinzuzufügen. Sie können zum Beispiel einer **SensorTemp**-Komponente die Funktion **Zieltemperatur** hinzufügen.

:::image type="content" source="media/howto-set-up-template/add-capability.png" alt-text="Eine Anleitung zum Hinzufügen einer Funktion":::

#### <a name="telemetry"></a>Telemetrie

Telemetrie ist ein Wertedatenstrom, der vom Gerät gesendet wird, üblicherweise von einem Sensor. Beispielsweise kann ein Sensor die Umgebungstemperatur, wie unten dargestellt, melden:

:::image type="content" source="media/howto-set-up-template/telemetry.png" alt-text="Eine Anleitung zum Hinzufügen von Telemetriedaten":::

In der folgenden Tabelle sind die Konfigurationseinstellungen für eine Telemetriefunktion angegeben:

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anzeigename | Der Anzeigename für den Telemetriewert, der in Ansichten und Formularen verwendet wird. |
| Name | Der Name des Felds in der Telemetrienachricht. IoT Central generiert einen Wert für dieses Feld aus dem Anzeigenamen, Sie können aber ggf. einen eigenen Wert auswählen. Dieses Feld muss alphanumerisch sein. |
| Funktionstyp | Telemetrie. |
| Semantischer Typ | Der semantische Typ der Telemetriedaten, z. B. Temperatur, Zustand oder Ereignis. Die Auswahl des semantischen Typs bestimmt, welches der folgenden Felder verfügbar ist. |
| Schema | Der Telemetriedatentyp, z.B. „double“, „string“ oder „vector“. Die verfügbaren Optionen werden durch den semantischen Typ bestimmt. Schema ist für die semantischen Typen „Ereignis“ und „Zustand“ nicht verfügbar. |
| severity | Nur für den semantischen Typ „Ereignis“ verfügbar. Die Schweregrade lauten **Fehler**, **Information** und **Warnung**. |
| Zustandswerte | Nur für den semantischen Typ „Zustand“ verfügbar. Definieren Sie die möglichen Zustandswerte, die jeweils einen Anzeigenamen, Namen, Enumerationstyp und Wert umfassen. |
| Einheit | Eine Einheit für den Telemetriewert, z. B. **km/h**, **%** oder **&deg;C**. |
| Anzeigeeinheit | Eine Anzeigeeinheit zur Verwendung in Ansichten und Formularen. |
| Comment | Beliebige Kommentare zur Telemetriefunktion. |
| BESCHREIBUNG | Eine Beschreibung der Telemetriefunktion. |

#### <a name="properties"></a>Eigenschaften

Eigenschaften stellen Zeitpunktwerte dar. Sie können schreibbare Eigenschaften über IoT Central festlegen.
Beispielsweise kann ein Gerät eine beschreibbare Eigenschaft verwenden, damit ein Bediener die Zieltemperatur, wie unten gezeigt, festlegen kann:

:::image type="content" source="media/howto-set-up-template/property.png" alt-text="Eine Anleitung zum Hinzufügen einer Eigenschaft":::

In der folgenden Tabelle sind die Konfigurationseinstellungen für eine Eigenschaftsfunktion angegeben:

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anzeigename | Der Anzeigename für den Eigenschaftswert, der in Ansichten und Formularen verwendet wird. |
| Name | Der Name der Eigenschaft. IoT Central generiert einen Wert für dieses Feld aus dem Anzeigenamen, Sie können aber ggf. einen eigenen Wert auswählen. Dieses Feld muss alphanumerisch sein. |
| Funktionstyp | Eigenschaft. |
| Semantischer Typ | Der semantische Typ der Eigenschaft, z. B. Temperatur, Zustand oder Ereignis. Die Auswahl des semantischen Typs bestimmt, welches der folgenden Felder verfügbar ist. |
| Schema | Der Eigenschaftsdatentyp, z.B. „double“, „string“ oder „vector“. Die verfügbaren Optionen werden durch den semantischen Typ bestimmt. Schema ist für die semantischen Typen „Ereignis“ und „Zustand“ nicht verfügbar. |
| Schreibbar | Wenn die Eigenschaft nicht schreibbar ist, kann das Gerät Eigenschaftswerte an IoT Central melden. Ist die Eigenschaft schreibbar, kann das Gerät Eigenschaftswerte an IoT Central melden, und IoT Central kann Aktualisierungen der Eigenschaft an das Gerät senden.
| severity | Nur für den semantischen Typ „Ereignis“ verfügbar. Die Schweregrade lauten **Fehler**, **Information** und **Warnung**. |
| Zustandswerte | Nur für den semantischen Typ „Zustand“ verfügbar. Definieren Sie die möglichen Zustandswerte, die jeweils einen Anzeigenamen, Namen, Enumerationstyp und Wert umfassen. |
| Einheit | Eine Einheit für den Eigenschaftswert, z. B. **km/h**, **%** oder **&deg;C**. |
| Anzeigeeinheit | Eine Anzeigeeinheit zur Verwendung in Ansichten und Formularen. |
| Comment | Beliebige Kommentare zur Eigenschaftsfunktion. |
| BESCHREIBUNG | Eine Beschreibung der Eigenschaftsfunktion. |

#### <a name="commands"></a>Befehle

Sie können Gerätebefehle über IoT Central aufrufen. Befehle übergeben optional Parameter an das Gerät und empfangen eine Antwort vom Gerät. Beispielsweise können Sie einen Befehl zum Neustarten eines Geräts in 10 Sekunden, wie unten dargestellt, aufrufen:

:::image type="content" source="media/howto-set-up-template/command.png" alt-text="Eine Anleitung zum Hinzufügen von Befehlen":::

In der folgenden Tabelle sind die Konfigurationseinstellungen für eine Befehlsfunktion angegeben:

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anzeigename | Der Anzeigename für den Befehl, der in Ansichten und Formularen verwendet wird. |
| Name | Der Name des Befehls. IoT Central generiert einen Wert für dieses Feld aus dem Anzeigenamen, Sie können aber ggf. einen eigenen Wert auswählen. Dieses Feld muss alphanumerisch sein. |
| Funktionstyp | Befehl. |
| Warteschlange (wenn offline) | Wenn diese Option aktiviert ist, können Sie den Befehl sogar dann aufrufen, wenn das Gerät offline ist. Wenn diese Option nicht aktiviert ist, können Sie den Befehl nur aufrufen, wenn das Gerät online ist. |
| Comment | Beliebige Kommentare zur Befehlsfunktion. |
| BESCHREIBUNG | Eine Beschreibung der Befehlsfunktion. |
| Anforderung | Wenn aktiviert, eine Definition des Anforderungsparameters, einschließlich Name, Anzeigename, Schema, Einheit und Anzeigeeinheit. |
| Antwort | Wenn aktiviert, eine Definition der Befehlsantwort, einschließlich Name, Anzeigename, Schema, Einheit und Anzeigeeinheit. |

Weitere Informationen zur Implementierung von Befehlen durch Geräte finden Sie unter [Telemetrie-, Eigenschaften- und Befehlsnutzlasten – Befehle](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Offlinebefehle

Wenn ein Gerät zurzeit offline ist, können Sie Warteschlangenbefehle auswählen, indem Sie in der Gerätevorlage die Option **Warteschlange (falls offline)** für einen Befehl aktivieren.

:::image type="content" source="media/howto-set-up-template/offline-commands.png" alt-text="Eine Anleitung zum Hinzufügen von Offline-Befehlen":::

Bei dieser Option werden Benachrichtigungen mithilfe von IoT Hub-Cloud-zu-Gerät (C2D)-Nachrichten an Geräte gesendet. Weitere Informationen finden Sie im IoT Hub-Artikel [Senden von C2D-Nachrichten](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Cloud-zu-Gerät-Nachrichten:

- Sind unidirektionale Benachrichtigungen aus Ihrer Lösung an das Gerät.
- Gewährleisten mindestens einmal eine Nachrichtenübermittlung. IoT Hub speichert Cloud-zu-Gerät-Nachrichten in Warteschlangen pro Gerät, und gewährleistet so Resilienz bei Verbindungs- und Gerätefehlern.
- Erfordert, dass das Gerät einen Meldungshandler zur Verarbeitung der Cloud-zu-Gerät-Nachricht implementiert.

> [!NOTE]
> Diese Option steht nur in der IoT Central-Webbenutzeroberfläche zur Verfügung. Diese Einstellung ist nicht enthalten, wenn Sie ein Modell oder eine Komponente aus der Gerätevorlage exportieren.

## <a name="cloud-properties"></a>Cloudeigenschaften

Verwenden Sie Cloudeigenschaften, um Informationen zu Geräten in IoT Central zu speichern. Cloudeigenschaften werden niemals an ein Gerät gesendet. Sie können Cloudeigenschaften beispielsweise verwenden, um den Namen des Kunden, der das Gerät installiert hat, oder das Datum der letzten Wartung des Geräts zu speichern.

:::image type="content" source="media/howto-set-up-template/cloud-properties.png" alt-text="Eine Anleitung zum Hinzufügen von Cloudeigenschaften"::: 

In der folgenden Tabelle sind die Konfigurationseinstellungen für eine Cloudeigenschaft angegeben:

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anzeigename | Der Anzeigename für den Cloudeigenschaftswert, der in Ansichten und Formularen verwendet wird. |
| Name | Der Name der Cloudeigenschaft. IoT Central generiert einen Wert für dieses Feld aus dem Anzeigenamen, Sie können aber ggf. einen eigenen Wert auswählen. |
| Semantischer Typ | Der semantische Typ der Eigenschaft, z. B. Temperatur, Zustand oder Ereignis. Die Auswahl des semantischen Typs bestimmt, welches der folgenden Felder verfügbar ist. |
| Schema | Der Datentyp der Cloudeigenschaft, z.B. „double“, „string“ oder „vector“. Die verfügbaren Optionen werden durch den semantischen Typ bestimmt. |

## <a name="customizations"></a>Anpassungen

Verwenden Sie Anpassungen, wenn Sie eine importierte Komponente ändern oder IoT Central-spezifische Features einer Funktion hinzufügen müssen. Beispielsweise können Sie den Anzeigenamen und die Einheiten einer Eigenschaft, wie unten gezeigt, ändern:

:::image type="content" source="media/howto-set-up-template/customize.png" alt-text="Eine Anleitung zum Durchführen von Anpassungen":::

In der folgenden Tabelle sind die Konfigurationseinstellungen für Anpassungen angegeben:

| Feld | BESCHREIBUNG |
| ----- | ----------- |
|Anzeigename | Anzeigenamen aus Modell überschreiben. |
|Semantischer Typ | Sematiktyp aus Model überschreiben. |
|Einheit | Einheit vom Modell überschreiben. |
|Anzeigeeinheit | Überschreiben von Modell. |
|Kommentar | Überschreiben von Modell. |
|BESCHREIBUNG | Überschreiben von Modell. |
|Color | IoT Central-spezifische Option. |
|Mindestwert | Mindestwert festlegen – IoT Central-spezifische Option. |
|Höchstwert | Maximalwert festlegen – IoT Central-spezifische Option. |
|Dezimalstellen | IoT Central-spezifische Option. |
|Anfangswert | Nur Befehle IoT Central-spezifischer Wert – Standardparameterwert. |

## <a name="views"></a>Sichten

Mit Ansichten können Sie Ansichten und Formulare definieren, die es einem Bediener ermöglichen, ein Gerät zu überwachen und mit ihm zu interagieren. Ansichten verwenden Visualisierungen wie Diagramme, um Telemetrie- und Eigenschaftswerte anzuzeigen.

Das Generieren von Standardansichten ist eine schnelle Möglichkeit, Ihre wichtigen Geräteinformationen zu visualisieren. Die drei Standardansichten sind:

### <a name="default-views"></a>Standardansichten

- **Befehle:** Eine Ansicht der Gerätebefehle, die dem Bediener ermöglicht, diese an Ihr Gerät auszugeben.
- **Übersicht**: Eine Ansicht mit Gerätetelemetrie, in der Diagramme und Metriken angezeigt werden.
- **Info**: Eine Ansicht mit Geräteinformationen und -eigenschaften.

Nachdem Sie die Option **Standardansichten generieren** gewählt haben, werden diese im Abschnitt **Ansichten** Ihrer Gerätevorlage automatisch hinzugefügt.

### <a name="custom-views"></a>Benutzerdefinierte Ansichten

Fügen Sie einer Gerätevorlage Ansichten hinzu, um Bedienern die Visualisierung eines Geräts mithilfe von Diagrammen und Metriken zu ermöglichen. Sie können einer Gerätevorlage eigene benutzerdefinierte Ansichten hinzufügen.

So fügen Sie einer Gerätevorlage eine Ansicht hinzu:

1. Navigieren Sie zu Ihrer Gerätevorlage, und wählen Sie **Ansichten** aus.
1. Wählen Sie die Option **Gerät visualisieren** aus.
1. Geben Sie in **Anzeigename** einen Namen für Ihre Ansicht ein.
1. Wählen Sie unter Kacheln hinzufügen die Option **Mit einem Visual** beginnen aus, und wählen Sie den Typ des Visuals für Ihre Kachel aus. Wählen Sie dann entweder die Option **Kachel hinzufügen** oder ziehen Sie das Visual in den Zeichenbereich und legen Sie es dort ab. Um die Kachel zu konfigurieren, wählen Sie das Zahnradsymbol aus.

:::image type="content" source="media/howto-set-up-template/start-visual.png" alt-text="Eine Anleitung zum Beginnen mit einem Visual"::: 

:::image type="content" source="media/howto-set-up-template/tile.png" alt-text="Kachel konfigurieren"::: 

Wählen Sie **Vorschaugerät konfigurieren** aus, um Ihre Ansicht anzuzeigen und zu testen. Mit dieser Funktion können Sie die Ansicht so anzeigen, wie sie dem Bediener nach ihrer Veröffentlichung angezeigt wird. Überprüfen Sie mit diesem Feature, ob in Ihren Ansichten die richtigen Daten angezeigt werden. Wählen Sie aus den folgenden Optionen:

- Kein Vorschaugerät
- Das tatsächliche Testgerät, das Sie für Ihre Gerätevorlage konfiguriert haben
- Ein vorhandenes Gerät in Ihrer Anwendung mithilfe der Geräte-ID

### <a name="forms"></a>Formulare

Fügen Sie Formulare zu einer Gerätevorlage hinzu, damit Bediener ein Gerät durch Anzeigen und Festlegen von Eigenschaften verwalten können. Bediener können nur Cloudeigenschaften und schreibbare Geräteeigenschaften bearbeiten. Sie können über mehrere Formulare für eine Gerätevorlage verfügen.

1. Wählen Sie den Knoten **Ansichten** und anschließend die Kachel **Geräte- und Clouddaten bearbeiten** aus, um eine neue Ansicht hinzuzufügen.

1. Ändern Sie den Formularnamen in **Manage device**.

1. Wählen Sie die Cloudeigenschaften **Kundenname** und **Datum der letzten Wartung** sowie die Eigenschaft **Zieltemperatur** aus. Wählen Sie anschließend **Abschnitt hinzufügen** aus.

1. Wählen Sie **Speichern** aus, um Ihr neues Formular zu speichern.

:::image type="content" source="media/howto-set-up-template/form.png" alt-text="Formular konfigurieren":::

## <a name="publish-a-device-template"></a>Veröffentlichen einer Gerätevorlage

Bevor Sie eine Verbindung mit einem Gerät herstellen können, das Ihr Gerätemodell implementiert, müssen Sie die Gerätevorlage veröffentlichen.

Wenn Sie eine Gerätevorlage veröffentlichen möchten, navigieren Sie zu der Gerätevorlage, und wählen Sie **Veröffentlichen** aus.

Nachdem Sie eine Gerätevorlage veröffentlicht haben, kann ein Bediener zur Seite **Geräte** wechseln und echte oder simulierte Geräte hinzufügen, die Ihre Gerätevorlage verwenden. Sie können Ihre Gerätevorlage weiter bearbeiten und speichern, während Sie Änderungen vornehmen. Immer wenn Sie diese Änderungen auf der Seite **Geräte** an die Bediener übermitteln möchten, müssen Sie **Veröffentlichen** auswählen.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes sollten Sie sich zum Thema [Vornehmen von Änderungen an einer vorhandenen Gerätevorlage](howto-edit-device-template.md) informieren.
