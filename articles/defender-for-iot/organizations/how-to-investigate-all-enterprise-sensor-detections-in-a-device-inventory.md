---
title: Informationen zu Geräten, die von allen Unternehmenssensoren erkannt werden
description: Sie können sich über den Gerätebestand in der lokalen Verwaltungskonsole einen umfassenden Überblick über die Geräteinformationen von verbundenen Sensoren verschaffen. Verwenden Sie die Tools zum Importieren, Exportieren und Filtern, um diese Informationen zu verwalten.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 0025016dd449bf4083c707d4a6db5cd25bf66cfd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343665"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Untersuchen aller Unternehmenssensorerkennungen im Gerätebestand

Über den *Gerätebestand* in der lokalen Verwaltungskonsole können Sie die Geräteinformationen von verbundenen Sensoren anzeigen. Dieses Feature bietet einen umfassenden Überblick über alle Netzwerkinformationen. Verwenden Sie die Tools zum Importieren, Exportieren und Filtern, um diese Informationen zu verwalten. Die Statusinformationen zu den Versionen des verbundenen Sensors werden ebenfalls angezeigt.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Screenshot: Tabelle mit Gerätebestandsdaten":::

Im Folgenden werden die Spalten in der Gerätebestandstabelle beschrieben.

| Parameter | BESCHREIBUNG |
|--|--|
| **Nicht bestätigte Warnungen** | Die Anzahl der nicht behandelten Warnungen, die diesem Gerät zugeordnet sind |
| **Geschäftseinheit** | Die Geschäftseinheit, in der dieses Gerät enthalten ist |
| **Region** | Die Region, in der dieses Gerät enthalten ist |
| **Website** | Der Standort, der dieses Gerät enthält |
| **Zone** | Die Zone, in der dieses Gerät enthalten ist |
| **Appliance** | Der Microsoft Defender für IoT-Sensor, der dieses Gerät schützt. |
| **Name** | Der Name des Geräts, wie von Defender für IoT erkannt |
| **Type** | Der Typ des Geräts, z. B. PLC oder HMI |
| **Hersteller** | Der Name des Herstellers des Geräts, wie in der MAC-Adresse angegeben |
| **Betriebssystem** | Das Betriebssystem des Geräts |
| **Firmware** | Die Firmware des Geräts |
| **IP-Adresse** | Die IP-Adresse des Geräts |
| **VLAN** | Das VLAN des Geräts |
| **MAC-Adresse** | Die MAC-Adresse des Geräts. |
| **Protokolle** | Die Protokolle, die vom Gerät verwendet werden |
| **Nicht bestätigte Warnungen** | Die Anzahl der nicht behandelten Warnungen, die diesem Gerät zugeordnet sind |
| **Ist autorisiert** | Der Autorisierungsstatus des Geräts:<br />- **true**: Das Gerät wurde autorisiert.<br />- **false**: Das Gerät wurde nicht autorisiert. |
| **Als Scanner bekannt** | Gibt an, ob dieses Gerät scannerähnliche Aktivitäten im Netzwerk durchführt |
| **Ist Programmiergerät** | Gibt an, ob es sich um ein Programmiergerät handelt:<br />- **true**: Das Gerät führt Programmieraktivitäten für PLCs, RTUs und Controller aus, die für Engineeringstationen relevant sind.<br />- **false**: Das Gerät ist kein Programmiergerät. |
| **Gruppen** | Gruppen, an denen dieses Gerät teilnimmt |
| **Letzte Aktivität** | Die letzte Aktivität, die das Gerät ausgeführt hat |
| **Discovered** | Wann dieses Gerät zum ersten Mal im Netzwerk erkannt wurde |
| **PLC-Modus (Vorschau)** | Der PLC-Betriebsmodus umfasst den Schlüsselzustand (physisch) und den Ausführungszustand (logisch). Mögliche **Schlüsselzustände** sind: „Ausführen“, „Programm“, „Remote“, „Beenden“, „Ungültig“, „Programmieren deaktiviert.Mögliche Ausführung“. Die möglichen **Ausführungszustände** sind: „Ausführen“, „Programm", „Beenden“, „Pausiert“, „Ausnahme“, „Angehalten“, „Eingeschlossen“, „Leerlauf“, „Offline“. Wenn beide Zustände identisch sind, wird nur ein Zustand angezeigt. |

## <a name="what-is-an-inventory-device"></a>Was ist ein Bestandsgerät?

Der Defender für IoT-Gerätebestand zeigt eine Vielzahl von Geräteattributen an. Sie werden von Sensoren erkannt, die die Netzwerke und verwalteten Endpunkte der Organisation überwachen. Defender für IoT identifiziert und klassifiziert Geräte als ein einzelnes eindeutiges Netzwerkgerät im Bestand für:

1. Eigenständige IT-/OT-/IoT-Geräte (mit einem oder mehreren NICs)
1. Geräte, die aus mehreren Rückwandplatinenkomponenten bestehen (einschließlich aller Racks/Slots/Module)
1. Geräte, die als Netzwerkinfrastruktur fungieren, z. B. Switch/Router (mit mehreren NICs). 

Öffentliche Internet-IP-Adressen, Multicastgruppen und Broadcastgruppen werden nicht als Bestandsgeräte betrachtet.
Geräte, die seit mehr als 60 Tagen inaktiv sind, werden als inaktive Bestandsgeräte klassifiziert.

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Integrieren von Daten in den Gerätebestand des Unternehmens

Mit den Funktionen für die Datenintegration können Sie die Daten im Gerätebestand mit Informationen aus anderen Unternehmensressourcen verbessern. Zu diesen Quellen gehören Konfigurationsverwaltungsdatenbanken (CMDB), DNS, Firewalls und Web-APIs.

Sie können diese Informationen verwenden, um sich ein umfassenderes Bild zu machen. Beispiel:

- Kaufdatum des Geräts und Ende der Garantiezeit

- Benutzer, die für das jeweilige Gerät zuständig sind

- Geöffnete Gerätetickets

- Das letzte Datum, an dem die Firmware aktualisiert wurde

- Geräte, die Zugriff auf das Internet haben

- Geräte, die aktive Virenschutzanwendungen ausführen

- Benutzer, die sich bei Geräten angemeldet haben

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Datentabelle auf dem Gerätebestandbildschirm.":::

Sie können Daten wie folgt integrieren:

- Manuelles Hinzufügen

- Ausführen von benutzerdefinierten Skripts, die Defender für IoT bereitstellt

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagramm des Unternehmensdatenintegrators":::

Der technische Support für Defender für IoT kann Sie beim Einrichten Ihres Systems für den Empfang von Web-API-Abfragen unterstützen.

So fügen Sie Daten manuell hinzu

1. Klicken Sie im Seitenmenü auf **Gerätebestand**, und wählen Sie dann :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: aus.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Bearbeiten der Einstellungen für den Gerätebestand":::

2. Wählen Sie im Dialogfeld **Gerätebestandseinstellungen** die Option **BENUTZERDEFINIERTE SPALTE HINZUFÜGEN** aus.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Hinzufügen einer benutzerdefinierten Spalte zum Bestand":::

3. Fügen Sie im Dialogfeld **Benutzerdefinierte Spalte hinzufügen** den neuen Spaltennamen (bis zu 250 Zeichen UTF) hinzu, wählen Sie **Manuell** aus, und klicken Sie auf **SPEICHERN**. Das neue Element wird im Dialogfeld **Gerätebestandseinstellungen** angezeigt.

4. Klicken Sie in der oberen rechten Ecke des Fensters **Gerätebestand** auf :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, und wählen Sie die Option **Alle Gerätebestandsinformationen exportieren** aus. Die CSV-Datei wird generiert.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="Die exportierte CSV-Datei":::

5. Fügen Sie der neuen Spalte die Informationen manuell hinzu, und speichern Sie die Datei.

6. Klicken Sie in der oberen rechten Ecke des Fensters **Gerätebestand** auf :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, wählen Sie die Option **Manuelle Eingabespalten importieren** aus, und navigieren Sie zur CSV-Datei. Die neuen Daten werden in der Tabelle **Gerätebestand** angezeigt.

So integrieren Sie Daten aus anderen Unternehmensentitäten

1. Klicken Sie in der oberen rechten Ecke des Fensters **Gerätebestand** auf :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, und wählen Sie die Option **Alle Gerätebestandsinformationen exportieren** aus.

2. Wählen Sie im Dialogfeld **Gerätebestandseinstellungen** die Option **BENUTZERDEFINIERTE SPALTE HINZUFÜGEN** aus.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Hinzufügen einer benutzerdefinierten Spalte zum Bestand":::

3. Fügen Sie im Dialogfeld **Benutzerdefinierte Spalte hinzufügen** den neuen Spaltennamen (bis zu 250 Zeichen UTF) hinzu, und wählen Sie **Automatisch** aus. Die Optionen **SKRIPT HOCHLADEN** und **SKRIPT TESTEN** werden angezeigt.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Automatisches Hinzufügen von benutzerdefinierten Spalten":::

4. Laden Sie das Skript hoch, das Sie vom [Microsoft-Support](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099) erhalten haben, und testen Sie es.

## <a name="retrieve-information-from-the-device-inventory"></a>Abrufen von Informationen aus dem Gerätebestand

Sie können eine umfangreiche Bandbreite an Geräteinformationen abrufen, die von verwalteten Sensoren erkannt werden, und diese Informationen in Partnersysteme integrieren. Sie können zum Beispiel Sensor-, Zonen-, Standort-ID-, IP-Adress-, MAC-Adress-, Firmware-, Protokoll- und Herstellerinformationen abrufen. Die abgerufenen Informationen lassen sich nach folgenden Kriterien filtern:

- Autorisierte und nicht autorisierte Geräte

- Geräte, die bestimmten Standorten zugeordnet sind

- Geräte, die bestimmten Zonen zugeordnet sind

- Geräte, die bestimmten Sensoren zugeordnet sind

Verwenden Sie API-Befehle für Defender für IoT, um diese Informationen abzurufen und zu integrieren. Weitere Informationen finden Sie unter [Sensor- und Verwaltungskonsolen-APIs für Defender für IoT](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtern des Gerätebestands

Der Gerätebestand lässt sich filtern, um relevante Spalten anzuzeigen. So können Sie sich zum Beispiel PLC-Geräteinformationen anzeigen lassen.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Screenshot des Gerätebestands.":::

Der Filter wird gelöscht, wenn Sie das Fenster schließen.

Wenn Sie einen Filter mehrmals verwenden möchten, können Sie diesen Filter oder eine Kombination von Filtern speichern. Im linken Bereich können Sie die gespeicherten Filter anzeigen:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Bildschirm: Gerätebestände.":::

So filtern Sie den Gerätebestand

1. Wählen Sie in der Spalte, die Sie filtern möchten, das Symbol :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: aus.

2. Wählen Sie im Dialogfeld **Filter** den folgenden Filtertyp aus:

   - **Equals** (gleich): Der genaue Wert, nach dem Sie die Spalte filtern möchten. Wenn Sie z. B. die Protokollspalte nach **Equals** (gleich) und `value=ICMP` filtern, werden in der Spalte nur Geräte angezeigt, die das ICMP-Protokoll verwenden.

   - **Contains** (Enthält): Der Wert, der in anderen Werten in der Spalte enthalten ist. Wenn Sie z. B. die Protokollspalte nach **Contains** (Enthält) und `value=ICMP` filtern, werden in der Spalte nur Geräte angezeigt, die das ICMP-Protokoll als Teil der Liste der Protokolle, die das Gerät benutzt, verwenden.

3. Wählen Sie :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: aus, um die Spalteninformationen alphabetisch zu sortieren. Sie können die Reihenfolge mithilfe der Pfeile :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: und :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: anpassen.

4. Definieren Sie den Filter, und wählen Sie **Save As** (Speichern unter) aus, um den neuen Filter zu speichern.

5. Ändern Sie die Definitionen, und wählen Sie **Save Changes** (Änderungen speichern) aus, um die Filterdefinitionen anzupassen.

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen von Sensorerkennungen in einem Gerätebestand](how-to-investigate-sensor-detections-in-a-device-inventory.md)
