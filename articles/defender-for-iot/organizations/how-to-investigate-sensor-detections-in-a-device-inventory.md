---
title: Einblicke in Geräte, die von einem bestimmten Sensor erkannt werden
description: Die Gerätebestand zeigt verschiedenste Geräteattribute an, die von einem Sensor erkannt werden.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: a218cbb8413728813bc818b73e8292144c01541a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312474"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Grundlagen zu Sensorerkennungen in einem Gerätebestand

Die Gerätebestand zeigt verschiedenste Geräteattribute an, die von einem Sensor erkannt werden. Folgende Optionen stehen zur Verfügung:

 - Unkompliziertes Filtern der Informationen

 - Exportieren von Informationen in eine CSV-Datei

 - Importieren von Windows-Registrierungsdetails

 - Erstellen von Gruppen für die Anzeige in der Geräteübersicht

## <a name="view-device-attributes-in-the-device-inventory"></a>Anzeigen von Geräteattributen im Gerätebestand

Die folgenden Attribute werden in der Gerätebestandstabelle angezeigt.

| Parameter | Beschreibung |
|--|--|
| Name | Der Name des Geräts, wie er vom Sensor erkannt oder vom Benutzer eingegeben wurde. |
| type | Der Typ des Geräts, wie er vom Sensor ermittelt oder vom Benutzer eingegeben wurde. |
| Hersteller | Der Name des Herstellers des Geräts, wie in der MAC-Adresse angegeben |
| Betriebssystem | Das Betriebssystem des Geräts, falls es erkannt wurde. |
| Firmware version | Die Firmware des Geräts, falls sie erkannt wurde. |
| IP-Adresse | Die IP-Adresse des Geräts, wenn sie definiert wurde. |
| VLAN | Das VLAN des Geräts Ausführliche Informationen dazu, wie Sie den Sensor so einrichten können, dass er VLANs erkennt, finden Sie unter [Definieren von VLAN-Namen](how-to-manage-the-on-premises-management-console.md#define-vlan-names).(how-to-define-management-console-network-settings.md#define-vlan-names). |
| MAC-Adresse | Die MAC-Adresse des Geräts. |
| Protokolle | Die Protokolle, die vom Gerät verwendet werden |
| Unacknowledged Alerts (Nicht bestätigte Warnungen) | Die Anzahl der nicht bestätigten Warnungen, die mit diesem Gerät verknüpft sind |
| Is Authorized (Autorisiert) | Der vom Benutzer definierte Autorisierungsstatus:<br />- **true**: Das Gerät wurde autorisiert.<br />- **false**: Das Gerät wurde nicht autorisiert. |
| Is Known as Scanner (Als Scanner bekannt) | Wurde vom Benutzer als Netzwerkscanner definiert. |
| Is Programming device (Programmiergerät) | Gibt an, ob das Gerät vom Benutzer als autorisiertes Programmiergerät definiert wurde. <br />- **true**: Das Gerät führt Programmieraktivitäten für PLCs, RTUs und Controller aus, die für Engineeringstationen relevant sind. <br />- **false**: Das Gerät ist kein Programmiergerät. |
| Gruppen | Die Gruppen, in denen dieses Gerät Mitglied ist |
| Last Activity (Letzte Aktivität) | Die letzte Aktivität, die das Gerät ausgeführt hat |
| Discovered | Wann dieses Gerät zum ersten Mal im Netzwerk erkannt wurde |
| **PLC-Modus (Vorschau)** | Der PLC-Betriebsmodus umfasst den Schlüsselzustand (physisch) und den Ausführungszustand (logisch). Mögliche **Schlüsselzustände** sind: „Ausführen“, „Programm“, „Remote“, „Beenden“, „Ungültig“, „Programmieren deaktiviert.Mögliche Ausführung“. Die möglichen **Ausführungszustände** sind: „Ausführen“, „Programm", „Beenden“, „Pausiert“, „Ausnahme“, „Angehalten“, „Eingeschlossen“, „Leerlauf“, „Offline“. Wenn beide Zustände identisch sind, wird nur ein Zustand angezeigt. |

## <a name="what-is-an-inventory-device"></a>Was ist ein Bestandsgerät?

Der Defender für IoT-Gerätebestand zeigt eine Vielzahl von Ressourcenattributen an. Sie werden von Sensoren erkannt, die die Netzwerke und verwalteten Endpunkte der Organisation überwachen.

Defender für IoT identifiziert und klassifiziert Geräte als ein einzelnes eindeutiges Netzwerkgerät im Bestand für:

1. Eigenständige IT-/OT-/IoT-Geräte (mit einem oder mehreren NICs)
1. Geräte, die aus mehreren Rückwandplatinenkomponenten bestehen (einschließlich aller Racks/Slots/Module)
1. Geräte, die als Netzwerkinfrastruktur fungieren, z. B. Switch/Router (mit mehreren NICs). 

Öffentliche Internet-IP-Adressen, Multicastgruppen und Broadcastgruppen werden nicht als Bestandsgeräte betrachtet.
Geräte, die seit mehr als 60 Tagen inaktiv sind, werden als inaktive Bestandsgeräte klassifiziert.

**So können Sie den Gerätebestand anzeigen:**

1. Wählen Sie im linken Bereich die Option **Devices** (Geräte) aus. Der Bereich **Devices** wird auf der rechten Seite geöffnet.

2. Klicken Sie im Bereich **Intune** auf die Option :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false":::.

Passen Sie die Gerätebestandstabelle an, um Spalten aus- oder einzublenden:

1. Wählen Sie im Menü oben rechts im Gerätebestand das Symbol :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: aus.

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Bildschirm mit Gerätebestandseinstellungen":::

2. Wählen Sie im Fenster mit den **Gerätebestandseinstellungen** die Spalten aus, die in der Gerätebestandstabelle angezeigt werden sollen.

3. Sie können die Position der Spalten in der Tabelle mithilfe der Pfeile ändern.

4. Wählen Sie **Speichern** aus. Das Fenster mit den **Gerätebestandseinstellungen** wird geschlossen, und die neuen Einstellungen werden in der Tabelle widergespiegelt.

### <a name="create-temporary-device-inventory-filters"></a>Erstellen temporärer Filter für den Gerätebestand

Sie können einen Filter festlegen, der definiert, welche Informationen in der Tabelle angezeigt werden. Beispielsweise können Sie festlegen, dass nur die Informationen des PLC-Geräts angezeigt werden.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Geräteliste":::

Der Filter wird nicht gespeichert, wenn Sie aus dem Bestand weg navigieren.

### <a name="save-device-inventory-filters"></a>Speichern von Filtern für den Gerätebestand

Sie können einen Filter oder eine Kombination von Filtern speichern, wenn Sie diese in Zukunft wiederverwenden möchten. Sie können umfassendere oder spezifischere Filter nach Gerätetyp oder nach bestimmten Typen und Protokollen erstellen.

Die Filter, die Sie speichern, werden auch als Geräteübersichtsgruppe gespeichert. Durch dieses Feature ist eine zusätzliche Granularität bei der Anzeige von Netzwerkgeräten in der Übersicht möglich.

**So können Sie Filter erstellen:**

1. Wählen Sie in der Spalte, die Sie filtern möchten, das Symbol :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: aus.

2. Wählen Sie im Dialogfeld **Filter** den folgenden Filtertyp aus:

   - **Equals** (gleich): Der genaue Wert, nach dem Sie die Spalte filtern möchten. Wenn Sie z. B. die Protokollspalte nach **Equals** (gleich) und `value=ICMP` filtern, werden in der Spalte nur Geräte angezeigt, die das ICMP-Protokoll verwenden.

   - **Contains** (Enthält): Der Wert, der in anderen Werten in der Spalte enthalten ist. Wenn Sie z. B. die Protokollspalte nach **Contains** (Enthält) und `value=ICMP` filtern, werden in der Spalte nur Geräte angezeigt, die das ICMP-Protokoll als Teil der Liste der Protokolle, die das Gerät benutzt, verwenden.

3. Wählen Sie :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: aus, um die Spalteninformationen alphabetisch zu sortieren. Sie können die Reihenfolge mithilfe der Pfeile :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: und :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: anpassen.

4. Definieren Sie den Filter, und wählen Sie **Save As** (Speichern unter) aus, um den neuen Filter zu speichern.

5. Ändern Sie die Definitionen, und wählen Sie **Save Changes** (Änderungen speichern) aus, um die Filterdefinitionen anzupassen.

So können Sie Filter anzeigen:

- Öffnen Sie den linken Bereich, und zeigen Sie die gespeicherten Filter an:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Anzeige der Filter im linken Bereich":::

### <a name="view-filtered-information-as-a-map-group"></a>Anzeigen gefilterter Informationen als Übersichtsgruppe

Wenn Sie zur Übersichtsansicht wechseln, werden die gefilterten Geräte hervorgehoben und gefiltert. Die Filtergruppe, die Sie gespeichert haben, wird im Menü neben der Gruppe **Device Inventory Filters** (Gerätebestandsfilter) angezeigt.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Anzeigen von Filtern in der Übersichtsansicht":::

## <a name="learn-windows-registry-details"></a>Windows-Registrierungsdetails

Neben OT-Geräten können Sie Microsoft Windows-Arbeitsstationen und -Server ermitteln. Diese Geräte werden auch im Gerätebestand angezeigt. Nachdem Sie Geräte ermittelt haben, können Sie den Gerätebestand mit detaillierten Windows-Informationen erweitern, z. B. mit den folgenden Informationen:

- Installierte Windows-Version

- Installierte Anwendungen

- Informationen zum Patch

- Öffnen von Ports

- Zuverlässigere Informationen zu Betriebssystemversionen

Zum Abrufen dieser Informationen stehen zwei Optionen zur Verfügung:

- Aktives Abrufen mithilfe geplanter WMI-Scans 

- Lokale Untersuchung indem Sie ein Skript auf einem Gerät verteilen und ausführen. Wenn Sie mit lokalen Skripts arbeiten, umgehen Sie die Risiken, die bei der Ausführung des WMI-Abrufs an einem Endpunkt entstehen. Dies ist auch für regulierte Netzwerke mit Wasserfällen und unidirektionalen Elementen nützlich.

In diesem Artikel wird beschrieben, wie Sie die Windows-Endpunktregistrierung mithilfe eines Skripts lokal untersuchen können. Diese Informationen werden zum Erstellen von Warnungen, Benachrichtigungen, Data-Mining-Berichten, Risikobewertungen und Angriffsvektorberichten verwendet.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Data-Mining-Screenshot":::

Sie können die folgenden Windows-Betriebssysteme untersuchen:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Voraussetzungen

Sie müssen die folgenden Anforderungen erfüllen, um mit dem Skript arbeiten zu können:

- Zum Ausführen des Skripts auf dem Gerät sind Administratorberechtigungen erforderlich.

- Der Sensor sollte das Windows-Gerät bereits kennen. Das bedeutet Folgendes: Wenn das Gerät bereits vorhanden ist, ruft das Skript seine Informationen ab.

- Ein Sensor überwacht das Netzwerk, mit dem der Windows-PC verbunden ist.

### <a name="acquire-the-script"></a>Abrufen des Skripts

[Wenden Sie sich an den Kundensupport](mailto:support.microsoft.com), um das Skript zu erhalten.

### <a name="deploy-the-script"></a>Bereitstellen des Skripts

Sie können das Skript einmal bereitstellen oder laufende Abfragen mithilfe der automatisierten Standardbereitstellungsmethoden und -tools planen.

### <a name="about-the-script"></a>Informationen zum Skript

- Das Skript wird als Hilfsprogramm und nicht als installiertes Programm ausgeführt. Das Ausführen des Skripts wirkt sich nicht auf den Endpunkt aus.

- Die vom Skript generierten Dateien bleiben auf dem lokalen Laufwerk, bis Sie sie löschen.

- Die Dateien, die das Skript generiert, befinden sich nebeneinander. Verschieben Sie sich nicht an verschiedene Speicherorte.

- Wenn Sie das Skript wieder am selben Speicherort ausführen, werden diese Dateien überschrieben.

**So führen Sie das Skript aus** 

1. Kopieren Sie das Skript auf ein lokales Laufwerk, und entpacken Sie es. Die folgenden Dateien werden angezeigt:

    - „start.bat“

    - „settings.json“

    - „data.bin“

    - „run.bat“

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Ansicht der Dateien im Datei-Explorer":::

2. Führen Sie die Datei `run.bat` aus.

3. Nachdem die Registrierung durchsucht wurde, wird die CX-Momentaufnahmendatei mit den Registrierungsinformationen angezeigt.

4. Der Dateiname gibt den Systemnamen und das Datum und die Uhrzeit der Momentaufnahme an. Ein Dateiname kann z. B. so lauten: `CX-snaphot_SystemName_Month_Year_Time`.

### <a name="import-device-details"></a>Importieren von Gerätedetails

Informationen jedes Endpunkts sollten in den Sensor importiert werden.

Dateien, die von den Abfragen generiert werden, können in einem Ordner abgelegt werden, auf den Sie von den Sensoren aus zugreifen können. Verwenden Sie die automatisierten Standardmethoden und -tools, um die Dateien von jedem Windows-Endpunkt aus an den Speicherort zu verschieben, von dem aus Sie sie in den Sensor importieren.

Ändern Sie keine Dateinamen.

**So importieren Sie die Informationen:**

1. Wählen Sie im Dialogfeld **Import Windows Configuration** (Windows-Konfigurationen importieren) **Import Settings** (Einstellungen importieren) aus.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importieren der Windows-Konfigurationen":::

2. Wählen Sie **Add** (Hinzufügen) aus, und wählen Sie dann alle Dateien aus (STRG + A).

3. Klicken Sie auf **Schließen**. Die Geräteregistrierungsinformationen werden importiert. Wenn beim Hochladen einer der Dateien ein Problem auftritt, werden Sie darüber informiert, bei welchem Dateiupload ein Fehler aufgetreten ist.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="Screenshot: Erfolgreicher Upload":::

## <a name="view-and-delete-inactive-devices-from-the-inventory"></a>Anzeigen inaktiver Geräte und Löschen aus dem Bestand

Sie können Geräte in Ihrem Netzwerk anzeigen, die inaktiv waren, und sie löschen.
Geräte können aus folgenden Gründen inaktiv werden:
- Falsch konfigurierte SPAN-Ports
- Änderungen bei der Netzwerkabdeckung
- Trennen vom Netzwerk

Aus dem Löschen inaktiver Geräte resultieren folgende Vorteile:

- Defender für IoT kann eine genauere Darstellung der aktuellen Netzwerkaktivität erstellen
- Gebundene Geräte können beim Verwalten von Abonnements besser ausgewertet werden
- Größere Übersichtlichkeit des Bildschirms

### <a name="view-inactive-devices"></a>Anzeigen inaktiver Geräte

Sie können den Bestand filtern, um Geräte anzuzeigen, die für folgende Zeiträume inaktiv sind:

- seit mindestens 7 Tagen
- seit mindestens 14 Tagen
- seit mindestens 30 Tagen
- seit mindestens 90 Tagen

**So filtern Sie den Bestand:**
  
1. Wählen Sie im Bestand das Filtersymbol **Zuletzt angezeigt** aus.
1. Wählen Sie eine Filteroption aus.
1. Wählen Sie **Übernehmen**.

### <a name="delete-inactive-devices"></a>Löschen inaktiver Geräte

Geräte, die Sie aus dem Bestand löschen, werden aus der Karte entfernt und nicht berücksichtigt, wenn „Defender für IoT“-Berichte wie Data Mining-, Risikobewertungs- und Angriffsvektorberichte generiert werden.

Sie werden aufgefordert, einen Grund für das Löschen von Geräten aufzuzeichnen. Diese Informationen sowie Uhrzeit/Datum des Löschens und Anzahl der gelöschten Geräte werden auf der Ereigniszeitachse angezeigt.

**So löschen Sie Geräte aus dem Bestand:**
  
1. Wählen Sie im Bestand das Filtersymbol **Zuletzt angezeigt** aus.
1. Wählen Sie eine Filteroption aus.
1. Wählen Sie **Übernehmen**.
1. Wählen Sie **Löschen inaktiver Geräte** aus.
1. Geben Sie im angezeigten Bestätigungsdialogfeld den Grund für den Löschvorgang ein, und wählen Sie **Löschen** aus. Alle Geräte, die innerhalb des Bereichs des Filters erkannt werden, werden gelöscht. Wenn Sie eine große Anzahl von Geräten löschen, kann der Löschvorgang einige Minuten dauern.

## <a name="export-device-inventory-information"></a>Exportieren von Gerätebestandsinformationen

Sie können Gerätebestandsinformationen in eine Excel-Datei exportieren.

So exportieren Sie eine CSV-Datei:

- Wählen Sie im Menü oben rechts im Gerätebestand das Symbol :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: aus. Der CSV-Bericht wird generiert und heruntergeladen.

## <a name="see-also"></a>Weitere Informationen

[Untersuchen aller Unternehmenssensorerkennungen in einem Gerätebestand](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Arbeiten mit Siteübersichtansichten](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
