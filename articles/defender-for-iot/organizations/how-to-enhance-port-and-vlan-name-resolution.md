---
title: Verbessern der Port- und VLAN-Namensauflösung
description: Hier erfahren Sie, wie Sie Port- und VLAN-Namen auf ihren Sensoren anpassen können, um die Geräteauflösung zu erweitern.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 5c4f29f43534a797c5e8ac7142fd9633830f7859
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331488"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>Verbessern der Namensauflösung für Ports, VLANs und Betriebssysteme

Sie können Port- und VLAN-Namen in Ihren Sensoren anpassen, um die Geräteauflösung zu erweitern.

## <a name="customize-port-names"></a>Anpassen von Portnamen

Microsoft Defender für IoT weist den meisten allgemein reservierten Ports, wie DHCP oder HTTP, automatisch Namen zu. Sie können Portnamen für andere Ports anpassen, die von Azure Defender for IoT erkannt werden. Weisen Sie z. B. einen Namen einem nicht reservierten Port zu, wenn dieser Port ungewöhnlich hohe Aktivitäten aufweist.

Diese Namen werden unter den folgenden Bedingungen angezeigt:

  - Wenn Sie in der Geräteübersicht **Device groups** (Gerätegruppen) auswählen

  - Wenn Sie Widgets erstellen, die Portinformationen bereitstellen

### <a name="view-custom-port-names-in-the-device-map"></a>Anzeigen von benutzerdefinierten Portnamen in der Geräteübersicht

Ports, die einen benutzerdefinierten Namen enthalten, werden in der Geräteübersicht in der Gruppe **Known Applications** (Bekannte Anwendungen) angezeigt.

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Screenshot: Geräteübersicht mit der Gruppe „Known Applications“ (Bekannte Anwendungen)":::

### <a name="view-custom-port-names-in-widgets"></a>Anzeigen von benutzerdefinierten Portnamen in Widgets

Die von Ihnen definierten Portnamen werden in den Widgets angezeigt, die den Datenverkehr nach Port darstellen.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Datenverkehr nach Port":::

So definieren Sie benutzerdefinierte Portnamen:

1. Wählen Sie **System Settings** (Systemeinstellungen) aus, und wählen Sie dann **Standard Aliases** (Standardaliase) aus.

2. Wählen Sie **Add Port Alias** (Portalias hinzufügen) aus.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Portalias hinzufügen":::

3. Geben Sie die Portnummer ein, wählen Sie **TCP/UDP** aus, oder wählen Sie **Both** (Beide) aus, und fügen Sie den Namen hinzu.

4. Wählen Sie **Speichern** aus.

## <a name="configure-vlan-names"></a>Konfigurieren von VLAN-Namen

Sie können Gerätebestandsdaten durch Geräte-VLAN-Nummern und -Tags erweitern. Zusätzlich zur Datenanreicherung können Sie die Anzahl der Geräte pro VLAN sowie die Bandbreite nach VLAN-Widgets anzeigen.

VLAN-Unterstützung basiert auf 802.1q (bis VLAN-ID 4094).

Zum Abrufen von VLAN-Informationen sind zwei Methoden möglich:

- **Automatisch ermittelt**: Der Sensor ermittelt VLANs standardmäßig automatisch. Mit Datenverkehr ermittelte VLANs werden in der Ansicht der VLAN-Konfiguration, in Data-Mining-Berichten und in anderen Berichten, die VLAN-Informationen enthalten, angezeigt. Nicht verwendete VLANs werden nicht angezeigt. Diese VLANs können nicht bearbeitet oder gelöscht werden. 

  Sie sollten jedem VLAN einen eindeutigen Namen geben. Wenn Sie keinen Namen hinzufügen, wird die VLAN-Nummer an allen Speicherorten angezeigt, an denen das VLAN gemeldet wird.

- **Manuell hinzugefügt**: VLANs können manuell hinzugefügt werden. Sie müssen einen eindeutigen Namen für jedes VLAN hinzufügen, das manuell hinzugefügt wurde, und Sie können diese VLANs bearbeiten oder löschen.

VLAN-Namen können bis zu 50 ASCII-Zeichen enthalten.

> [!NOTE]
> VLAN-Namen werden nicht zwischen dem Sensor und der Verwaltungskonsole synchronisiert. Sie müssen den Namen auch in der Verwaltungskonsole definieren.  
Fügen Sie für Cisco-Switchs der SPAN-Konfiguration die folgende Zeile hinzu: `monitor session 1 destination interface XX/XX encapsulation dot1q`. In diesem Befehl sind *XX/XX* der Name und die Nummer des Ports.

So konfigurieren Sie VLAN-Namen:

1. Wählen Sie im Menü an der Seite **System Settings** (Systemeinstellungen) aus.

2. Wählen Sie im Fenster **System Settings** (Systemeinstellungen) die Option **VLAN** aus.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Verwenden Sie die Systemeinstellungen, um Ihre VLANs zu bearbeiten.":::

3. Fügen Sie neben jeder VLAN-ID einen eindeutigen Namen ein.

## <a name="improve-device-operating-system-classification-data-enhancement"></a>Verbessern der Klassifizierung von Gerätebetriebssystemen: Datenaufbereitung

Sensoren erkennen automatisch und kontinuierlich neue Geräte sowie Änderungen an zuvor erkannten Geräten, einschließlich Betriebssystemen.

Unter bestimmten Umständen werden möglicherweise Konflikte bei ermittelten Betriebssystemen erkannt. Das kann beispielsweise der Fall sein, wenn eine Betriebssystemversion vorliegt, die auf Desktop- oder Serversysteme verweist. Wenn ein solcher Konflikt erkannt wird, erhalten Sie eine Benachrichtigung mit optionalen Betriebssystemklassifizierungen.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Enhance Data (Daten verbessern)":::

Sehen Sie sich die Empfehlungen an, um die Betriebssystemklassifizierung anzureichern. Diese Klassifizierungen werden unter anderem im Gerätebestand und in Data-Mining-Berichten angezeigt. Wenn Sie dafür sorgen, dass diese Informationen auf dem neuesten Stand sind, können Sie die Genauigkeit von Warnungen, Bedrohungen und Risikoanalyseberichten verbessern.

So greifen Sie auf Betriebssystemempfehlungen zu:

1. Wählen Sie **Systemeinstellungen** aus.
1. Wählen Sie **Datenaufbereitung** aus.

## <a name="next-steps"></a>Nächste Schritte

Anzeigen von angereicherten Geräteinformationen in verschiedenen Berichten:

- [Grundlagen zu Sensorerkennungen in einem Gerätebestand](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Sensortrend- und Statistikberichte](how-to-create-trends-and-statistics-reports.md)
- [Data Mining-Abfragen für Sensoren](how-to-create-data-mining-queries.md)
