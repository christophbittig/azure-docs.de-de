---
title: Generieren von Trend- und Statistikberichten
description: Gewinnen Sie mithilfe der Trend- und Statistikwidgets von Defender für IoT Erkenntnisse zu Netzwerkaktivitäten, Statistiken und Trends.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: f84f739a9566bff2a9fe8286d301aa31d1e74199
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284035"
---
# <a name="sensor-trends-and-statistics-reports"></a>Sensortrend- und Statistikberichte

Sie können Widgetdiagramme und Kreisdiagramme erstellen, um Erkenntnisse über Netzwerktrends und Statistiken zu gewinnen. Widgets können unter benutzerdefinierten Dashboards gruppiert werden.

> [!NOTE]
> Administratoren und Sicherheitsanalysten können Trends und Statistikberichte erstellen.

Das Dashboard besteht aus Widgets, die die folgenden Informationstypen grafisch beschreiben:

- Datenverkehr nach Port
- Bester Datenverkehr nach Port
- Bandbreite des Kanals
- Bandbreite gesamt
- Aktive TCP-Verbindung
- Beste Bandbreite nach VLAN
- Geräte:
  - Neue Geräte
  - Ausgelastete Geräte
  - Geräte nach Hersteller
  - Geräte nach Betriebssystem
  - Anzahl der Geräte pro VLAN
  - Getrennte Geräte
- Verbindungsabbrüche nach Stunden
- Warnungen zu Vorfällen nach Typ
- Zugriff auf Datenbanktabellen
- Widgets zur Protokollanalyse
- DELTAV
  - Verteilung von DeltaV roc-Vorgängen
  - DeltaV roc-Ereignisse nach Name
  - DeltaV-Ereignisse nach Zeit
- AMS
  - AMS-Datenverkehr nach Serverport
  - AMS-Datenverkehr nach Befehl
- Ethernet- und IP-Adresse:
  - Ethernet- und IP-Adressdatenverkehr nach CIP-Dienst
  - Ethernet- und IP-Adressdatenverkehr nach CIP-Klasse
  - Ethernet- und IP-Adressdatenverkehr nach Befehl
- OPC:
  - Größte OPC-Verwaltungsvorgänge
  - Größte OPC-E/A-Vorgänge
- Siemens S7:
  - S7-Datenverkehr nach Steuerungsfunktion
  - S7-Datenverkehr nach Unterfunktion
- VLAN
  - Anzahl der Geräte pro VLAN
  - Beste Bandbreite nach VLAN
- 60870-5-104
  - IEC-60870-Datenverkehr nach ASDU
- BACNET
  - BACnet-Dienste
- DNP3
  - DNP3-Datenverkehr nach Funktion
- SRTP:
  - SRTP-Datenverkehr nach Dienstcode
  - SRTP-Fehler nach Tag
- SuiteLink:
  - Am meisten abgefragte SuiteLink-Tags
  - Verhalten von numerischen SuiteLink-Tags
- IEC-60870-Datenverkehr nach ASDU
- DNP3-Datenverkehr nach Funktion
- MMS-Datenverkehr nach Dienst
- Modbus-Datenverkehr nach Funktion
- OPC-UA-Datenverkehr nach Dienst

> [!NOTE]
>  Die Zeit in den Widgets wird entsprechend der Sensorzeit festgelegt.

## <a name="create-reports"></a>Erstellen von Berichten

So zeigen Sie Dashboards und Widgets an:

Wählen Sie im Seitenmenü die Option **Trends und Statistiken** aus.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Screenshot einer Untersuchung.":::

Standardmäßig werden Ergebnisse für Erkennungen in den letzten sieben Tagen angezeigt. Sie können Filtertools verwenden, um diesen Bereich zu ändern. Nutzen Sie beispielsweise eine Freitextsuche.

## <a name="create-a-dashboard"></a>Erstellen eines Dashboards

Erstellen Sie ein neues Dashboard, indem Sie das Dropdownmenü **Dashboard** auswählen. Sie können beliebig viele Widgets erstellen und einem Dashboard hinzufügen.

Sie können angepasste Dashboards mit den folgenden Optionen erstellen:

- Hinzufügen eines Widgets zum Dashboard

- Löschen eines Widgets aus dem Dashboard

- Ändern des Filters eines Widgets

- Ändern der Größe eines Widgets

- Ändern der Position eines Widgets

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Ändern Sie die Position eines Widgets.":::

So erstellen Sie ein angepasstes Dashboard

1. Wählen Sie im linken Panel **Trends und Statistiken** aus.

1. Wählen Sie das Dropdownmenü **Dashboard auswählen** aus, und klicken Sie auf die Schaltfläche **Dashboard erstellen**.

1. Geben Sie einen aussagekräftigen Namen für das neue Dashboard ein, und klicken Sie auf **Erstellen**.

1. Wählen Sie oben links auf der Seite **Widget hinzufügen** aus.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Wählen Sie das Widget aus dem Widget Store aus.":::

1. Widgets für **Sicherheit** und **Betrieb** sind oben rechts im Fenster verfügbar. Wählen Sie aus verschiedenen Kategorien und Protokollen aus. Für jedes Widget wird eine kurze Beschreibung mit einer Miniaturgrafik angezeigt. Verwenden Sie die Scrollleiste, um alle verfügbaren Widgets anzuzeigen.

1. Wählen Sie mithilfe der Schaltfläche **Zum Hinzufügen klicken** ein Widget aus. Das Widget wird sofort im Dashboard angezeigt.

So löschen Sie ein Dashboard

1. Wählen Sie im Dropdownmenü den Namen des Dashboards aus.

1. Klicken Sie auf das Symbol **Löschen**, und klicken Sie dann auf **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Klicken Sie auf das Löschsymbol, um das Dashboard zu löschen.":::

So bearbeiten Sie einen Dashboardnamen

1. Wählen Sie im Dropdownmenü den Namen des Dashboards aus.

1. Klicken Sie auf das Symbol **Bearbeiten**.
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Klicken Sie auf das Bearbeitungssymbol, um den Namen des Dashboards zu bearbeiten.":::

1. Geben Sie einen neuen Namen für das Dashboard ein, and klicken Sie auf **Speichern**.
 
So legen Sie das Standarddashboard fest

1. Wählen Sie im Dropdownmenü den Namen des Dashboards aus.

1. Klicken Sie auf das **Sternsymbol**, und wählen Sie das Dashboard aus, das als Standarddashboard festgelegt werden soll.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Klicken Sie auf das Sternsymbol, um das Standarddashboard auszuwählen."::: 

So ändern Sie die Filterdaten in einem Widget

1. Klicken Sie auf das **Filtersymbol**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Klicken Sie auf das Filtersymbol, um Parameter für Ihr Widget festzulegen.":::

1. Bearbeiten Sie die erforderlichen Parameter.

1. Klicken Sie auf **OK**.

So löschen Sie ein Widget

- Klicken auf das Symbol :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false":::.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Klicken Sie auf das X, um das Widget zu löschen.":::

## <a name="creating-widgets"></a>Erstellen von Widgets 

Im Widget Store können Sie Widgets nach Kategorie und Protokoll auswählen. Sie können die verfügbaren Widgets für **Sicherheit** oder **Betrieb** anzeigen, indem Sie sie auswählen.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Wählen Sie Ihr Widget aus dem Widget Store aus.":::

Jedes Widget enthält spezifische Informationen zu Systemdatenverkehr, Netzwerkstatistiken, Protokollstatistiken, Geräten und Warninformationen. Eine Meldung wird angezeigt, wenn für ein Widget keine Daten vorhanden sind.

In einem Kreisdiagramm können Sie ein Segment entfernen, um die relative Bedeutung der verbleibenden Segmente deutlicher hervorzuheben. Wählen Sie hierzu in der Legende im unteren Bildschirmbereich den Namen des Segments aus.

Die folgenden Abschnitte enthalten Beispiele für Anwendungsfälle einiger Widgets.

### <a name="busy-devices-widget"></a>Widget für ausgelastete Geräte

In diesem Widget werden die fünf am häufigsten ausgelasteten Geräte aufgeführt. Im **Bearbeitungsmodus** können Sie nach bekannten Protokollen filtern.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Eine Ansicht des Widgets für ausgelastete Geräte.":::

### <a name="total-bandwidth-widget"></a>Widget für Bandbreite gesamt

Mit diesem Widget wird die Bandbreite in MBit/s (Megabits pro Sekunde) nachverfolgt. Die Bandbreite wird auf der y-Achse angezeigt, während das Datum auf der x-Achse dargestellt wird. Im **Bearbeitungsmodus** können Sie Ergebnisse nach Client, Server, Serverport oder Subnetz filtern. Wenn Sie mit dem Cursor auf das Diagramm zeigen, wird eine QuickInfo angezeigt.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Eine Ansicht des Widgets für Bandbreite gesamt.":::

### <a name="channels-bandwidth-widget"></a>Widget für Kanalbandbreite

Dieses Widget zeigt die fünf wichtigsten Datenverkehrskanäle an. Sie können nach Adresse filtern und die Anzahl der dargestellten Ergebnisse festlegen. Wählen Sie den Pfeil nach unten aus, um weitere Kanäle anzuzeigen.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Eine Ansicht des Widgets für Kanalbandbreite.":::

### <a name="traffic-by-port-widget"></a>Widget für Datenverkehr nach Port

Dieses Widget zeigt den Datenverkehr nach Port an. Dieser wird durch ein Kreisdiagramm dargestellt, in dem jeder Port durch eine andere Farbe gekennzeichnet ist. Der Umfang des Datenverkehrs an den einzelnen Ports verhält sich proportional zur Größe des Kreissegments.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Eine Ansicht des Widgets für Datenverkehr nach Port.":::

### <a name="new-devices-widget"></a>Widget für neue Geräte

Dieses Widget zeigt das Balkendiagramm für neue Geräte an. Darin wird angegeben, wie viele neue Geräte an einem bestimmten Datum ermittelt wurden.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Eine Ansicht des Widgets für neue Geräte.":::

### <a name="protocol-dissection-widgets"></a>Widgets zur Protokollanalyse

Dieses Widget zeigt ein Kreisdiagramm an, in dem Sie den Datenverkehr pro Protokoll unterteilt nach Funktionscodes und Diensten anzeigen können. Die Größe der einzelnen Kreissegmente verhält sich proportional zum Umfang des Datenverkehrs im Verhältnis zu den anderen Segmenten.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Eine Ansicht des Widgets zur Protokollanalyse.":::

### <a name="active-tcp-connections-widget"></a>Widget für aktive TCP-Verbindungen

Dieses Widget zeigt ein Diagramm an, in dem die Anzahl aktiver TCP-Verbindungen im System angezeigt wird.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Eine Ansicht des Widgets für aktive TCP-Verbindungen.":::

### <a name="incident-by-type-widget"></a>Widget für Incidents nach Typ

Dieses Widget zeigt ein Kreisdiagramm an, das die Anzahl von Incidents nach Typ darstellt. Hierbei handelt es sich um die Anzahl von Warnungen, die in einem vordefinierten Zeitraum von den einzelnen Engines generiert werden.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Eine Ansicht des Widgets für Incidents nach Typ.":::

## <a name="devices-by-vendor-widget"></a>Widget für Geräte nach Hersteller

Dieses Widget zeigt ein Kreisdiagramm an, das die Anzahl von Geräten nach Hersteller darstellt. Die Anzahl von Geräten für einen bestimmten Hersteller verhält sich proportional zur Größe des Herstellersegments für das jeweilige Gerät im Verhältnis zu anderen Geräteherstellern.

## <a name="number-of-devices-per-vlan-widget"></a>Widget für die Anzahl von Geräten pro VLAN

Dieses Widget zeigt ein Kreisdiagramm an, das die Anzahl ermittelter Geräte nach VLAN darstellt. Die Größe der einzelnen Kreissegmente verhält sich proportional zur Anzahl ermittelter Geräte im Verhältnis zu den anderen Segmenten.

Jedes VLAN wird mit dem VLAN-Tag angezeigt, das durch den von Ihnen manuell hinzugefügten Sensor oder Namen zugewiesen wurde.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Eine Ansicht des Widgets für die Geräteanzahl.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Widget für die höchste Bandbreitennutzung nach VLAN

Dieses Widget zeigt die Bandbreitennutzung nach VLAN an. Standardmäßig stellt das Widget die fünf VLANs mit der höchsten Bandbreitennutzung dar.

Sie können die Daten nach dem im Widget dargestellten Zeitraum filtern. Wählen Sie den Pfeil nach unten aus, um weitere Ergebnisse anzuzeigen.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Eine Ansicht des Widgets für die höchste Bandbreitennutzung nach VLAN.":::

## <a name="system-report"></a>Systembericht

So laden Sie den Systembericht herunter 

1. Wählen Sie im Seitenmenü die Option **Trends und Statistiken** aus.

1. Wählen Sie in der oberen rechten Ecke **Systembericht** aus. Der Bericht wird automatisch heruntergeladen.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Wählen Sie die Schaltfläche „Systembericht“ aus, um eine Kopie des Systemberichts herunterzuladen.":::

Der Systembericht ist eine PDF-Datei, die alle Daten des Systems enthält:

  - Geräte

  - Alerts

  - Informationen zu Netzwerkrichtlinien

## <a name="devices-in-a-system-report"></a>Geräte in einem Systembericht 

Der Systembericht umfasst eine Liste aller Geräte und der zugehörigen Informationen. Dazu gehören beispielsweise Typ, Name und verwendete Protokolle. Der Systembericht zeigt außerdem eine Liste der Geräte pro Hersteller an.

## <a name="alerts-in-system-report"></a>Warnungen im Systembericht 

Der Systembericht umfasst eine Liste aller Warnungen mit den zugehörigen Informationen, z. b. Datum und Schweregrad.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Eine Ansicht der Warnungen in Systemberichten.":::

## <a name="network-information-in-system-report"></a>Netzwerkinformationen im Systembericht 

Der Systembericht zeigt Ihre Netzwerkbaseline im Detail an. Hierzu gehören zum Beispiel der DNP3-Funktionscode und die offenen Ports pro Verbindung.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Eine Ansicht der DNP3-Funktion aus dem Systembericht.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Eine Ansicht des Berichts zu geöffneten Ports pro Verbindung.":::

## <a name="see-also"></a>Siehe auch

[Berichterstellung zur Risikobewertung](how-to-create-risk-assessment-reports.md)
[Sensor-Data Mining-Abfragen](how-to-create-data-mining-queries.md)
[Angriffsvektor-Berichterstellung](how-to-create-attack-vector-reports.md)
