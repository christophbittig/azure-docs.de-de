---
title: Installation von Defender für IoT
description: Hier erfahren Sie, wie Sie einen Sensor und die lokale Verwaltungskonsole für Microsoft Defender für IoT installieren.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 23776a9abf35cc8cdba512517163ab2f723b458d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343779"
---
# <a name="defender-for-iot-installation"></a>Installation von Defender für IoT

Dieser Artikel beschreibt, wie Sie die folgenden Microsoft Defender für IoT-Komponenten installieren:

- **Sensor**: Defender für IoT-Sensoren erfassen den ICS-Netzwerkdatenverkehr mithilfe von passiver Überwachung (ohne Agents). Da sie passiv und nicht intrusiv arbeiten, haben die Sensoren keinerlei Auswirkungen auf OT- und IoT-Netzwerke und -Geräte. Der Sensor wird mit einem SPAN-Port oder Netzwerk-TAP verbunden und beginnt sofort mit der Überwachung Ihres Netzwerks. Erkennungen werden in der Sensorkonsole angezeigt. Dort können Sie sie in einer Netzwerkübersicht, einem Geräteinventar sowie einer umfangreichen Palette von Berichten anzeigen, untersuchen und analysieren. Zu den Beispielen zählen Risikobewertungsberichte, Data Mining-Abfragen und Angriffsvektoren.

- **Lokale Verwaltungskonsole**: Über die lokale Verwaltungskonsole können Sie Geräteverwaltung, Risikomanagement und Verwaltung von Sicherheitsrisiken durchführen. Außerdem können Sie damit die Bedrohungsüberwachung und Reaktion auf Vorfälle im gesamten Unternehmen durchführen. So erhalten Sie eine einheitliche Übersicht über alle Netzwerkgeräte, Ihnen werden wichtige IoT- und OT-Risikoindikatoren sowie Warnungen angezeigt, die in Einrichtungen erkannt wurden, in denen Sensoren bereitgestellt werden. Über die lokale Verwaltungskonsole können Sie Sensoren in Air-Gap-Netzwerken anzeigen und verwalten.

In diesem Artikel werden die folgenden Installationsinformationen behandelt:

- **Hardware:** Details zur physischen Dell- und HPE-Appliance.

- **Software:** Installation der Software für Sensor und lokale Verwaltungskonsole

- **Virtuelle Geräte:** Details zum virtuellen Computer und zur Softwareinstallation.

Stellen Sie nach der Installation eine Verbindung zwischen Ihrem Sensor und Ihrem Netzwerk her.

## <a name="about-defender-for-iot-appliances"></a>Informationen zu Defender für IoT-Appliances

In den folgenden Abschnitten finden Sie Informationen zu Defender für IoT-Sensorappliances und zur Appliance für die lokale Defender für IoT-Verwaltungskonsole.

### <a name="physical-appliances"></a>Physische Appliances

Der Defender für IoT-Appliancesensor stellt eine Verbindung mit einem SPAN-Port oder einem Netzwerk-TAP her und beginnt mithilfe von passiver Überwachung (ohne Agents) sofort mit der Erfassung von ICS-Netzwerkdatenverkehr. Dieser Prozess hat keinerlei Auswirkungen auf OT-Netzwerke und -Geräte, weil er sich nicht im Datenpfad befindet und OT-Geräte nicht aktiv überprüft.

Die folgenden Appliances zum Einbau in ein Rack stehen zur Verfügung:

| **Bereitstellungstyp** | **Unternehmen** | **Enterprise** | **SMB** |**SMB, robust** |
|--|--|--|--|--|
| **Modell** | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| **Überwachungsports** | bis zu 15 RJ45 oder 8 OPT | bis zu 8 RJ45 oder 6 OPT | bis zu 4 RJ45 | bis zu 5 RJ45 |
| **Maximale Bandbreite\*** | 3 GB/s | 1 GB/s | 200 MBit/s | 100 MB/s |
| **Maximale Anzahl geschützter Geräte** | 10.000 | 10.000 | 1\.000 | 800 |

\* Die maximale Bandbreitenkapazität könnte je nach Protokollverteilung variieren.

### <a name="virtual-appliances"></a>Virtuelle Geräte

Die folgenden virtuellen Geräte stehen zur Verfügung:

| **Bereitstellungstyp** | **Unternehmen** | **Enterprise** | **SMB** |
|--|--|--|--|
| **Beschreibung** | Virtuelles Gerät für Unternehmensbereitstellungen | Virtuelles Gerät für Unternehmensbereitstellungen | Virtuelles Gerät für SMB-Bereitstellungen |
| **Maximale Bandbreite\*** | 2,5 GB/s | 800 MB/s | 160 MB/s |
| **Maximale Anzahl geschützter Geräte** | 10.000 | 10.000 | 800 |
| **Bereitstellungstyp** | Unternehmen | Enterprise | SMB |

\* Die maximale Bandbreitenkapazität könnte je nach Protokollverteilung variieren.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Hardwarespezifikationen für die lokale Verwaltungskonsole

 | Element | Beschreibung |
 |----|--|
 **Beschreibung** | In einer Architektur mit mehreren Ebenen bietet die lokale Verwaltungskonsole Transparenz und Kontrolle über geografisch verteilte Standorte. Sie ist in SOC-Sicherheitsstapel integriert, darunter SIEMs, Ticketsysteme, Firewalls der nächsten Generation, Plattformen für sicheren Remotezugriff und Defender für IoT ICS Malware-Sandbox. |
 **Bereitstellungstyp** | Enterprise |
 **Appliancetyp**  | Dell R340, VM |
 **Anzahl der verwalteten Sensoren** | Unbegrenzt |

## <a name="prepare-for-the-installation"></a>Vorbereiten der Installation

### <a name="access-the-iso-installation-image"></a>Zugreifen auf das ISO-Installationsimage

Das Installationsbild ist über Defender for IoT im Azure-Portal zugänglich.

So greifen Sie auf die Datei zu:

1. Melden Sie sich bei Ihrem Defender für IoT-Konto an.

1. Wechseln Sie zur Seite **Netzwerksensor** oder **Lokale Verwaltungskonsole**, und wählen Sie die Version aus, die heruntergeladen werden soll.

### <a name="install-from-dvd"></a>Installieren von einer DVD

Stellen Sie vor der Installation sicher, dass Sie Folgendes zur Verfügung haben:

- Ein tragbares DVD-Laufwerk mit dem USB-Connector.

- Ein ISO-Installationsimage.

So führen Sie die Installation durch:

1. Kopieren Sie das Image auf eine DVD, oder bereiten Sie einen Datenträger auf einem Schlüssel vor. Verbinden Sie ein tragbares DVD-Laufwerk mit Ihrem Computer, klicken Sie mit der rechten Maustaste auf das ISO-Image, und wählen Sie **Burn to disk** (Auf Datenträger kopieren) aus.

1. Verbinden Sie die DVD oder den Datenträger auf einem Schlüssel, und konfigurieren Sie die Appliance für den Start von einer DVD oder einem Datenträger auf einem Schlüssel.

### <a name="install-from-disk-on-a-key"></a>Installieren von einem Datenträger auf einem Schlüssel

Stellen Sie vor der Installation sicher, dass Sie Folgendes zur Verfügung haben:

- Rufus wurde installiert.
  
- Ein Datenträger auf einem Schlüssel mit USB-Version 3.0 und höher. Die Mindestgröße beträgt 4 GB.

- Eine ISO-Imagedatei des Installationsprogramms.

Der Datenträger auf einem Schlüssel wird in diesem Vorgang gelöscht.

So bereiten Sie einen Datenträger auf einem Schlüssel vor:

1. Führen Sie „Rufus“ aus, und wählen Sie **SENSOR-ISO** aus.

1. Verbinden Sie den Datenträger auf einem Schlüssel mit der Vorderseite.

1. Legen Sie fest, dass das BIOS des Servers über den USB-Stick gestartet werden soll.

## <a name="dell-poweredger340xl-installation"></a>Installation von Dell PowerEdge R340XL

Vor der Installation der Software auf der Dell-Appliance müssen Sie deren BIOS-Konfiguration anpassen:

- [Dell PowerEdge R340 – Vorderseite](#dell-poweredge-r340-front-panel) und [Dell PowerEdge R340 – Rückseite](#dell-poweredge-r340-back-panel) enthält die Beschreibung von Vorder- und Rückseite sowie Informationen, die für die Installation erforderlich sind (z. B. Treiber und Ports).

- [Dell BIOS-Konfiguration](#dell-bios-configuration) bietet Informationen zum Herstellen einer Verbindung mit der Dell-Appliance-Verwaltungsschnittstelle und zum Konfigurieren des BIOS.

- In [Softwareinstallation (Dell R340)](#software-installation-dell-r340) wird das Verfahren beschrieben, das zum Installieren der Defender für IoT-Sensorsoftware erforderlich ist.

### <a name="dell-poweredge-r340xl-requirements"></a>Anforderungen für Dell PowerEdge R340XL

Zum Installieren der Dell PowerEdge R340XL-Appliance benötigen Sie Folgendes:

- Unternehmenslizenz für Dell Remote Access Controller (iDrac)

- XML für BIOS-Konfiguration

- Serverfirmware-Versionen:

  - BIOS-Version 2.1.6

  - iDrac-Version 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Dell PowerEdge R340 – Vorderseite

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Dell PowerEdge R340 – Vorderseite":::

 1. Linkes Kontrollfeld
 1. Optisches Laufwerk (optional)
 1. Rechtes Bedienfeld
 1. Informationstag
 1. Laufwerke  

### <a name="dell-poweredge-r340-back-panel"></a>Dell PowerEdge R340 – Rückseite

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Dell PowerEdge R340 – Rückseite":::

1. Serieller Anschluss
1. NIC-Port (GB 1)
1. NIC-Port (GB 1)
1. PCIe – halbe Höhe
1. PCIe-Erweiterungskartenslot – volle Höhe
1. Stromversorgungseinheit 1
1. Stromversorgungseinheit 2
1. Systemidentifikation
1. Kabelanschluss für die Systemstatusanzeige (CMA) – Taste
1. USB 3.0-Anschluss (2)
1. Dedizierter iDRAC9-Netzwerkport
1. VGA-Anschluss

### <a name="dell-bios-configuration"></a>Dell-BIOS-Konfiguration

Die Dell-BIOS-Konfiguration ist erforderlich, um die Dell-Appliance für die Zusammenarbeit mit der Software anzupassen.

Die Dell-Appliance wird durch einen integrierten iDRAC mit Lifecycle Controller (LC) verwaltet. Der LC ist in jedem Dell PowerEdge-Server eingebettet und bietet Funktionen, mit deren Hilfe Sie Ihre Dell PowerEdge-Appliances bereitstellen, aktualisieren, überwachen und warten können.

Zum Einrichten der Kommunikation zwischen der Dell-Appliance und dem Verwaltungscomputer müssen Sie die iDRAC-IP-Adresse und die IP-Adresse des Verwaltungscomputers in demselben Subnetz definieren.

Wenn die Verbindung hergestellt wird, kann das BIOS konfiguriert werden.

**So konfigurieren Sie das Dell-BIOS**:

1. [Konfigurieren Sie die iDRAC-IP-Adresse.](#configure-idrac-ip-address)

1. [Konfigurieren des BIOS](#configuring-the-bios)

#### <a name="configure-idrac-ip-address"></a>Konfigurieren der iDRAC-IP-Adresse

1. Schalten Sie den Sensor ein.

1. Wenn das Betriebssystem bereits installiert wurde, drücken Sie die Taste F2, um die BIOS-Konfiguration einzugeben.

1. Wählen Sie **iDRAC-Einstellungen** aus.

1. Wählen Sie **Netzwerk** aus.

   > [!NOTE]
   > Während der Installation müssen Sie die standardmäßige iDRAC-IP-Adresse und das Kennwort konfigurieren, die in den folgenden Schritten angegeben werden. Nach der Installation müssen Sie diese Definitionen ändern.

1. Ändern Sie die statische IPv4-Adresse in **10.100.100.250**.

1. Ändern Sie die statische Subnetzmaske in **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Screenshot der statischen Subnetzmaske":::

1. Wählen Sie **Zurück** > **Fertigstellen** aus.

#### <a name="configuring-the-bios"></a>Konfigurieren des BIOS

Sie müssen das Appliance-BIOS in folgenden Fällen konfigurieren:

- Sie haben Ihre Appliance nicht von Arrow erworben.

- Sie haben zwar eine Appliance, aber keinen Zugriff auf die XML-Konfigurationsdatei.

Wechseln Sie nach dem Zugriff auf das BIOS zu **Geräteeinstellungen**.

**So konfigurieren Sie das BIOS**:

1. Greifen Sie direkt über eine Tastatur und einen Bildschirm auf das Appliance-BIOS zu, oder verwenden Sie iDRAC.

   - Wenn die Appliance keine Defender für IoT-Appliance ist, öffnen Sie einen Browser, und navigieren Sie zu der zuvor konfigurierten IP-Adresse. Melden Sie sich mit den Dell-Standardadministratorrechten an. Verwenden Sie **root** als Benutzername und **calvin** als Kennwort.

   - Wenn die Appliance eine Defender für IoT-Appliance ist, melden Sie sich mit **XXX** als Benutzername und **XXX** als Kennwort an.

1. Wechseln Sie nach dem Zugriff auf das BIOS zu **Geräteeinstellungen**.

1. Wählen Sie die RAID-gesteuerte Konfiguration aus, indem Sie **Integrierter RAID-Controller 1: Dell PERC\<PERC H330 Adapter\> Konfigurationshilfsprogramm** auswählen.

1. Wählen Sie **Konfigurationsverwaltung** aus.

1. Wählen Sie **Virtuellen Datenträger erstellen** aus.

1. Wählen Sie im Feld **RAID-Stufe auswählen** die Option **RAID5** aus. Geben Sie im Feld **Name des virtuellen Datenträgers** den Namen **root** ein, und wählen Sie **Physische Datenträger** aus.

1. Wählen Sie **Alle überprüfen** und dann **Änderungen übernehmen** aus.

1. Klicken Sie auf **OK**.

1. Scrollen Sie nach unten, und wählen Sie  **Virtuellen Datenträger erstellen** aus.

1. Aktivieren Sie das Kontrollkästchen **Bestätigen**, und wählen Sie **Ja** aus.

1. Klicken Sie auf **OK**.

1. Kehren Sie zum Hauptbildschirm zurück, und wählen Sie **System-BIOS** aus.

1. Wählen Sie **Starteinstellungen** aus.

1. Wählen Sie für **Startmodus** die Option **BIOS** aus.

1. Wählen Sie **Zurück** und dann **Fertigstellen** aus, um die BIOS-Einstellungen zu beenden.

### <a name="software-installation-dell-r340"></a>Softwareinstallation (Dell R340)

Der Installationsvorgang dauert ungefähr 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

So führen Sie die Installation durch:

1. Überprüfen Sie auf eine der folgenden Arten, ob die Versionsmedien in die Appliance eingebunden werden:

   - Verbinden Sie die externe CD oder den Datenträger auf einem Schlüssel mit dem Release.

   - Binden Sie das ISO-Image mithilfe von iDRAC ein. Wählen Sie nach der Anmeldung bei iDRAC die virtuelle Konsole und dann **Virtuelle Medien** aus.

1. Wählen Sie im Abschnitt **Map CD/DVD** (CD/DVD zuordnen) **Datei auswählen** aus.

1. Wählen Sie im daraufhin angezeigten Dialogfeld die ISO-Imagedatei der Version für diese Version aus.

1. Wählen Sie die Schaltfläche **Map Device** (Gerät zuordnen) aus.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Screenshot eines zugeordneten Geräts":::

1. Die Medien werden eingebunden. Klicken Sie auf **Schließen**.

1. Starten Sie die Appliance. Wenn Sie iDRAC verwenden, können Sie die Server neu starten, indem Sie die Schaltfläche **Consul Control** (Consul-Steuerung) auswählen. Wählen Sie dann in den **Tastaturmakros** die Schaltfläche **Anwenden** aus, wodurch die Tastenfolge STRG+ALT+ENTF gestartet wird.

1. Wählen Sie **Englisch** (?Deutsch?) aus.

1. Wählen Sie **SENSOR-RELEASE-\<version\> Enterprise** aus.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Wählen Sie Ihre Sensorversion und Ihren Unternehmenstyp aus.":::

1. Definieren Sie das Applianceprofil und die Netzwerkeigenschaften:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Screenshot mit dem Applianceprofil und den Netzwerkeigenschaften":::

   | Parameter | Konfiguration |
   |--|--|
   | **Hardwareprofil** | **enterprise** |
   | **Verwaltungsschnittstelle** | **eno1** |
   | **Netzwerkparameter (vom Kunden bereitgestellt)** | - |
   |**IP-Adresse des Verwaltungsnetzwerks:** | - |
   | **Subnetzmaske:** | - |
   | **Appliance-Hostname:** | - |
   | **DNS:** | - |
   | **IP-Adresse des Standardgateways:** | - |
   | **Eingabeschnittstellen:** |  Das System generiert automatisch die Liste der Eingabeschnittstellen. Kopieren Sie zum Spiegeln der Eingabeschnittstellen alle in der Liste mit einem Komma als Trennzeichen dargestellten Elemente. Sie müssen die Bridge-Schnittstelle nicht konfigurieren. Diese Option wird nur bei besonderen Anwendungsfällen verwendet. |

1. Nach ungefähr 10 Minuten werden die beiden Anmeldeinformationssätze angezeigt. Einer ist für einen **CyberX**-Benutzer und einer für einen **Support**-Benutzer bestimmt.  

1. Speichern Sie die Appliance-ID und die Kennwörter. Sie benötigen diese Anmeldeinformationen für den Zugriff auf die Plattform, wenn Sie sie zum ersten Mal verwenden.

1. Wählen Sie **Eingeben** aus, um den Vorgang fortzusetzen.

## <a name="hpe-proliant-dl20-installation"></a>HPE ProLiant DL20-Installation

In diesem Abschnitt wird der Vorgang zur Installation von HPE ProLiant DL20 beschrieben, der die folgenden Schritte umfasst:

- Aktivieren Sie den Remotezugriff, und aktualisieren Sie das Standardadministratorkennwort.
- Konfigurieren Sie die BIOS- und RAID-Einstellungen.
- Installieren Sie die Software.

### <a name="about-the-installation"></a>Informationen zur Installation

- Enterprise- und SMB-Appliances können installiert werden. Der Installationsvorgang ist bei beiden Appliancetypen identisch – mit Ausnahme der Arraykonfiguration.
- Ein Standardadministrator wird bereitgestellt. Wir empfehlen, dass Sie das Kennwort während des Netzwerkkonfigurationsvorgangs ändern.
- Während des Netzwerkkonfigurationsvorgangs werden Sie den iLO-Port am Netzwerkport 1 konfigurieren.
- Der Installationsvorgang dauert ungefähr 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

### <a name="hpe-proliant-dl20-front-panel"></a>HPE ProLiant DL20 – Vorderseite

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 – Vorderseite":::

### <a name="hpe-proliant-dl20-back-panel"></a>HPE ProLiant DL20 – Rückseite

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Die Rückseite des HPE ProLiant DL20":::

### <a name="enable-remote-access-and-update-the-password"></a>Aktivieren des Remotezugriffs und Aktualisieren des Kennworts

Mit dem folgenden Verfahren können Sie Netzwerkoptionen einrichten und das Standardkennwort aktualisieren.

So aktivieren und aktualisieren Sie das Kennwort:

1. Schließen Sie einen Bildschirm und eine Tastatur an die HP-Appliance an, schalten Sie die Appliance ein, und drücken Sie **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Screenshot des HPE ProLiant-Fensters":::

1. Wechseln Sie zu **Systemdienstprogramme** > **Systemkonfiguration** > **iLO 5-Konfigurationshilfsprogramm** > **Netzwerkoptionen**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Screenshot des Fensters „Systemkonfiguration“":::

    1. Wählen Sie im Feld **Netzwerkschnittstellenadapter** die Option **Freigegebene Netzwerkport-LOM** aus.

    1. Deaktivieren Sie DHCP.

    1. Geben Sie die IP-Adresse, die Subnetzmaske und die IP-Adresse des Gateways ein.

1. Wählen Sie **F10: Speichern** aus.

1. Drücken Sie **ESC**, um zum **iLO 5-Konfigurationsdienstprogramm** zurückzukehren, und wählen Sie **Benutzerverwaltung** aus.

1. Wählen Sie **Benutzer bearbeiten/entfernen** aus. Der Administrator ist der einzige definierte Standardbenutzer.

1. Ändern Sie das Standardkennwort, und wählen Sie **F10: Speichern** aus.

### <a name="configure-the-hpe-bios"></a>Konfigurieren des HPE-BIOS

Im folgenden Verfahren wird beschrieben, wie Sie das HPE-BIOS für die Enterprise- und SMB-Appliances konfigurieren.

**So konfigurieren Sie das HPE-BIOS**:

1. Wählen Sie **Systemdienstprogramme** > **Systemkonfiguration** > **BIOS/Plattformkonfiguration (RBSU)** aus.

1. Wählen Sie im Formular **BIOS/Plattformkonfiguration (RBSU)** die Option **Startoptionen** aus.

1. Ändern Sie **Startmodus** in **Legacy-BIOS-Modus**, und wählen Sie **F10: Speichern** aus.

1. Drücken Sie zweimal **ESC**, um das Formular **Systemkonfiguration** zu schließen.

#### <a name="for-the-enterprise-appliance"></a>Für die Enterprise-Appliance

1. Wählen Sie **Eingebettetes RAID 1: HPE Smart Array P408i-a SR Gen 10** > **Arraykonfiguration** > **Array erstellen** aus.

1. Wählen Sie im Formular **Create Array** (Array erstellen) alle Optionen aus. Für die **Enterprise**-Appliance gibt es drei Optionen.

#### <a name="for-the-smb-appliance"></a>Für die SMB-Appliance

1. Wählen Sie **Eingebettetes RAID 1: HPE Smart Array P208i-a SR Gen 10** > **Arraykonfiguration** > **Array erstellen** aus.

1. Wählen Sie **Proceed to Next Form** (Mit nächstem Formular fortfahren) aus.

1. Legen Sie im Formular **Set RAID Level** (RAID-Ebene festlegen) die Ebene auf **RAID 5** für Unternehmensbereitstellungen und **RAID 1** für SMB-Bereitstellungen fest.

1. Wählen Sie **Proceed to Next Form** (Mit nächstem Formular fortfahren) aus.

1. Geben Sie im Formular **Logical Drive Label** (Bezeichnung des logischen Laufwerks) **Logisches Laufwerk 1** ein.

1. Wählen Sie **Änderungen senden** aus.

1. Wählen Sie im Formular **Senden** die Option **Zurück zum Hauptmenü** aus.

1. Wählen Sie **F10: Speichern** aus, und drücken Sie zweimal **ESC**.

1. Wählen Sie im Fenster **Systemdienstprogramme** die Option **Menü für einmaliges Anmelden** aus.

1. Wählen Sie im Formular **Menü für einmaliges Anmelden** die Option **Legacy-BIOS – Menü für einmaliges Anmelden** aus.

1. Daraufhin werden die Fenster **Starten in Legacy** und **Start außer Kraft setzen** angezeigt. Wählen Sie eine Option für „Start außer Kraft setzen“ aus, beispielsweise eine CD-ROM, ein USB-Stick, ein Festplattenlaufwerk oder eine UEFI-Shell.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Screenshot des ersten Fensters für „Start außer Kraft setzen“":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Screenshot des zweiten Fensters für „Start außer Kraft setzen“":::

### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Softwareinstallation (HPE ProLiant DL20-Appliance)

Der Installationsvorgang dauert ungefähr 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

So installieren Sie die Software:

1. Schließen Sie den Bildschirm und die Tastatur an die Appliance an, und stellen Sie eine Verbindung mit der CLI her.

1. Schließen Sie eine externe CD oder einen Datenträger mit dem ISO-Bild, das Sie von der Seite **Updates** von Defender für IoT im Azure-Portal heruntergeladen haben, an den Schlüssel an.

1. Starten Sie die Appliance.

1. Wählen Sie **Englisch** (?Deutsch?) aus.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Auswahl von „Englisch“ (?Deutsch?) im CLI-Fenster":::

1. Wählen Sie **SENSOR-RELEASE-\<version> Enterprise** aus.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot des Bildschirms zum Auswählen einer Version":::

1. Definieren Sie im Installations-Assistenten das Hardwareprofil und die Netzwerkeigenschaften:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Screenshot des Installations-Assistenten":::

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Hardwareprofil** | Wählen Sie **Enterprise** oder **Office** für SMB-Bereitstellungen aus. |
    | **Verwaltungsschnittstelle** | **eno2** |
    | **Standardnetzwerkparameter (normalerweise werden die Parameter vom Kunden bereitgestellt)** | **IP-Adresse des Verwaltungsnetzwerks:** <br/> <br/>**Appliance-Hostname:** <br/>**DNS:** <br/>**die IP-Adresse des Standardgateways:**|
    | **Eingabeschnittstellen:** | Das System generiert automatisch die Liste der Eingabeschnittstellen.<br/><br/>Kopieren Sie zum Spiegeln der Eingabeschnittstellen alle in der Liste mit einem Komma als Trennzeichen dargestellten Elemente: **eno5, eno3, eno1, eno6, eno4**.<br/><br/>**Für HPE DL20: Listen Sie „eno1“ und „enp1s0f4u4“ (iLo-Schnittstellen) nicht auf**.<br/><br/>**BRIDGE**: Die Bridge-Schnittstelle muss nicht konfiguriert werden. Diese Option wird nur bei besonderen Anwendungsfällen verwendet. Drücken Sie die **EINGABETASTE**, um fortzufahren. |

1. Nach ungefähr 10 Minuten werden die beiden Anmeldeinformationssätze angezeigt. Einer ist für einen **CyberX**-Benutzer und einer für einen **Support**-Benutzer bestimmt.

1. Speichern Sie die Appliance-ID und die Kennwörter. Sie benötigen die Anmeldeinformationen für den ersten Zugriff auf die Plattform.

1. Wählen Sie **Eingeben** aus, um den Vorgang fortzusetzen.

## <a name="hpe-proliant-dl360-installation"></a>HPE ProLiant DL360-Installation

- Ein Standardadministrator wird bereitgestellt. Wir empfehlen, dass Sie das Kennwort während der Netzwerkkonfiguration ändern.

- Während der Netzwerkkonfiguration werden Sie den iLO-Port konfigurieren.

- Der Installationsvorgang dauert ungefähr 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

### <a name="hpe-proliant-dl360-front-panel"></a>HPE ProLiant DL360 – Vorderseite

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 – Vorderseite":::

### <a name="hpe-proliant-dl360-back-panel"></a>HPE ProLiant DL360 – Rückseite

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="HPE ProLiant DL360 – Rückseite":::

### <a name="enable-remote-access-and-update-the-password"></a>Aktivieren des Remotezugriffs und Aktualisieren des Kennworts

Weitere Informationen finden Sie in den vorhergehenden Abschnitten zur HPE ProLiant DL20-Installation:

- „Aktivieren des Remotezugriffs und Aktualisieren des Kennworts“

- „Konfigurieren des HPE-BIOS“

Die Unternehmenskonfiguration ist identisch.

> [!Note]
> Im Arrayformular müssen Sie alle Optionen auswählen.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>iLO-Remoteinstallation (von einem virtuellen Laufwerk)

In diesem Verfahren wird die iLO-Installation von einem virtuellen Laufwerk beschrieben.

So führen Sie die Installation durch:

1. Melden Sie sich bei der iLO-Konsole an, und klicken Sie mit der rechten Maustaste auf den Bildschirm des Servers.

1. Wählen Sie **HTML5-Konsole** aus.

1. Wählen Sie in der Konsole das CD-Symbol und dann die Option „CD/DVD“ aus.

1. Wählen Sie **Lokale ISO-Datei** aus.

1. Wählen Sie im Dialogfeld die relevante ISO-Datei aus.

1. Wechseln Sie zum linken Symbol, wählen Sie **Power** und dann **Zurücksetzen** aus.

1. Die Appliance wird neu gestartet und führt den Sensorinstallationsvorgang aus.

### <a name="software-installation-hpe-dl360"></a>Softwareinstallation (HPE DL360)

Der Installationsvorgang dauert ungefähr 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

So führen Sie die Installation durch:

1. Schließen Sie den Bildschirm und die Tastatur an die Appliance an, und stellen Sie eine Verbindung mit der CLI her.

1. Schließen Sie eine externe CD oder einen Datenträger mit dem ISO-Bild, das Sie von der Seite **Updates** von Defender für IoT im Azure-Portal heruntergeladen haben, an den Schlüssel an.

1. Starten Sie die Appliance.

1. Wählen Sie **Englisch** (?Deutsch?) aus.

1. Wählen Sie **SENSOR-RELEASE-\<version> Enterprise** aus.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot, in dem die Auswahl der Version gezeigt wird.":::

1. Definieren Sie im Installations-Assistenten das Applianceprofil und die Netzwerkeigenschaften.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Screenshot des Installations-Assistenten":::

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Hardwareprofil** | Wählen Sie **Unternehmen** aus. |
    | **Verwaltungsschnittstelle** | **eno2** |
    | **Standardnetzwerkparameter (vom Kunden bereitgestellt)** | **IP-Adresse des Verwaltungsnetzwerks:** <br> **Subnetzmaske:** <br/>**Appliance-Hostname:** <br/>**DNS:** <br/>**die IP-Adresse des Standardgateways:**|
    | **Eingabeschnittstellen:**  | Das System generiert automatisch eine Liste der Eingabeschnittstellen.<br/><br/>Kopieren Sie zum Spiegeln der Eingabeschnittstellen alle in der Liste mit einem Komma als Trennzeichen dargestellten Elemente.<br/><br/> Sie müssen die Bridge-Schnittstelle nicht konfigurieren. Diese Option wird nur bei besonderen Anwendungsfällen verwendet. |

1. Nach ungefähr 10 Minuten werden die beiden Anmeldeinformationssätze angezeigt. Einer ist für einen **CyberX**-Benutzer und einer für einen **Support**-Benutzer bestimmt.

1. Speichern Sie die Appliance-ID und die Kennwörter. Sie benötigen diese Anmeldeinformationen für den ersten Zugriff auf die Plattform.

1. Wählen Sie **Eingeben** aus, um den Vorgang fortzusetzen.

## <a name="hp-edgeline-300-installation"></a>Installation von HP EdgeLine 300

- Ein Standardadministrator wird bereitgestellt. Wir empfehlen, dass Sie das Kennwort während der Netzwerkkonfiguration ändern.

- Der Installationsvorgang dauert ungefähr 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

### <a name="hp-edgeline-300-back-panel"></a>Rückseite von HP EdgeLine 300

:::image type="content" source="media/tutorial-install-components/edgeline-el300-panel.png" alt-text="Ansicht der Rückseite von EL300":::

### <a name="enable-remote-access"></a>Aktivieren des Remotezugriffs

1. Geben Sie die ISM-IP-Adresse in Ihren Webbrowser ein.

1. Melden Sie sich mit dem Standardbenutzernamen und -kennwort an, das Sie auf Ihrer Appliance finden.

1. Navigieren Sie zu **Wired and Wireless Network**(Verkabeltes und drahtloses Netzwerk) > **IPV4**.

    :::image type="content" source="media/tutorial-install-components/wired-and-wireless.png" alt-text="Navigieren Sie zu hervorgehobenen Abschnitten.":::

1. Deaktivieren Sie den **DHCP-Umschalter**.

1. Konfigurieren Sie die IPv4-Adressen so:
    - **IPV4-Adresse**: `192.168.1.125`
    - **IPV4-Subnetzmaske**: `255.255.255.0`
    - **IPV4-Gateway**: `192.168.1.1`

1. Wählen Sie **Übernehmen**.

1. Melden Sie sich ab, und starten Sie die Appliance neu.

### <a name="configure-the-bios"></a>Konfigurieren des BIOS

Im folgenden Verfahren wird beschrieben, wie Sie das BIOS für die HP EL300-Appliance konfigurieren können.

**So konfigurieren Sie das BIOS**:

1. Schalten Sie die Appliance ein, und drücken Sie **F9**, um das BIOS zu aktivieren.

1. Wählen Sie **Erweitert** aus, und scrollen Sie nach unten zu **CSM Support**.

    :::image type="content" source="media/tutorial-install-components/csm-support.png" alt-text="Aktivieren Sie „CSM Support“, um das zusätzliche Menü zu öffnen.":::

1. Drücken Sie die **EINGABETASTE,** , um „CSM Support“ zu aktivieren.

1. Navigieren Sie zu **Speicher** und pushen Sie **+/-** , um ihn in „Legacy“ zu ändern.

1. Navigieren Sie zu **Video** und pushen Sie **+/-** , um es in „Legacy“ zu ändern.

    :::image type="content" source="media/tutorial-install-components/storage-and-video.png" alt-text="Navigieren Sie zu „Speicher“ und „Video“, und ändern Sie beide in „Legacy“.":::

1. Navigieren Sie zu **Boot** (Start) > **Boot mode select** (Auswahl des Startmodus).

1. Pushen Sie **+/-** , um ihn in „Legacy“ zu ändern.

    :::image type="content" source="media/tutorial-install-components/boot-mode.png" alt-text="„Boot mode select“ (Auswahl des Startmodus) in „Legacy“ ändern.":::

1. Navigieren Sie zu **Speichern und beenden**.

1. Wählen Sie **Änderungen speichern und beenden** aus.

    :::image type="content" source="media/tutorial-install-components/save-and-exit.png" alt-text="Speichern Sie Ihre Änderungen, und beenden Sie das System.":::

1. Wählen Sie **Ja** aus. Dann wird die Appliance neu gestartet.

1. Drücken Sie **F11**, um das **Startmenü** zu öffnen.

1. Wählen Sie das Gerät mit dem Sensorbild aus. Geben Sie **DVD** oder **USB** ein.

1. Wählen Sie Ihre Sprache aus.

1. Wählen Sie **sensor-10.0.3.12-62a2a3f724 Office: 4 CPUS, 8GB RAM, 100GB STORAGE** aus.

    :::image type="content" source="media/tutorial-install-components/sensor-select-screen.png" alt-text="Wählen Sie die Sensorversion wie gezeigt aus.":::

1. Definieren Sie im Installations-Assistenten das Applianceprofil und die Netzwerkeigenschaften:

    :::image type="content" source="media/tutorial-install-components/appliance-parameters.png" alt-text="Definieren Sie das Profil und die Netzwerkkonfigurationen der Appliance mit den folgenden Parametern.":::

    | Parameter | Konfiguration |
    |--|--|
    | **Konfigurieren eines Hardwareprofils** | **office** |
    | **Konfigurieren der Verwaltungsnetzwerkschnittstelle** | **enp3s0** <br /> oder <br />**möglicher Wert** |
    | **Konfigurieren der IP-Adresse des Verwaltungsnetzwerks:** | **Die vom Kunden angegebene IP-Adresse** |
    | **Konfigurieren der Subnetzmaske:** | **Die vom Kunden angegebene IP-Adresse** |
    | **Konfigurieren von DNS:** | **Die vom Kunden angegebene IP-Adresse** |
    | **Konfigurieren der IP-Adresse des Standardgateways:** | **Die vom Kunden angegebene IP-Adresse** |
    | **Konfigurieren von einer oder mehreren Eingabeschnittstelle(n)** | **enp4s0** <br /> oder <br />**möglicher Wert** |
    | **Konfigurieren von einer oder mehreren Bridge-Schnittstelle(n)** | – |

1. Akzeptieren Sie die Einstellungen, und setzen Sie den Vorgang durch Eingabe von `Y` fort.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Sensorinstallation für das virtuelle Gerät

Sie können den virtuellen Computer für den Defender für IoT-Sensor in den folgenden Architekturen bereitstellen:

| Aufbau | Spezifikationen | Verwendung | Kommentare |
|---|---|---|---|
| **Enterprise** | CPU: 8<br/>Memory: 32G RAM<br/>HDD: 1.800 GB | Produktionsumgebung | Standard und am häufigsten |
| **Kleinunternehmen** | CPU: 4 <br/>Memory: 8G RAM<br/>HDD: 500 GB | Test- oder kleine Produktionsumgebungen | -  |
| **Office** | CPU: 4<br/>Memory: 8G RAM<br/>HDD: 100 GB | Kleine Testumgebungen | -  |

### <a name="prerequisites"></a>Voraussetzungen

Die lokale Verwaltungskonsole unterstützt sowohl VMware- als auch Hyper-V-Bereitstellungsoptionen. Bevor Sie mit der Installation beginnen, müssen die folgenden Elemente vorhanden sein:

- VMware (ESXi 5.5 oder höher) oder Hyper-V-Hypervisor (Windows 10 Pro oder Enterprise) installiert und betriebsbereit

- Verfügbare Hardwareressourcen für den virtuellen Computer

- ISO-Installationsdatei für den Microsoft Defender für IoT-Sensor

Sorgen Sie dafür, dass der Hypervisor ausgeführt wird.

### <a name="create-the-virtual-machine-esxi"></a>Erstellen des virtuellen Computers (ESXi)

1. Melden Sie sich beim ESXi an, wählen Sie den relevanten **Datenspeicher** und dann **Datenspeicherbrowser** aus.

1. **Laden Sie das Image hoch**, und wählen Sie **Schließen** aus.

1. Wechseln Sie zu **Virtuelle Computer**, und wählen Sie **VM erstellen/registrieren** aus.

1. Wählen Sie **Neuen virtuellen Computer erstellen** und dann **Weiter** aus.

1. Fügen Sie einen Sensornamen hinzu, und wählen Sie:

   - Kompatibilität: **&lt;Neueste ESXi-Version&gt;** aus.

   - Gastbetriebssystemfamilie: **Linux**

   - Gastbetriebssystemversion: **Ubuntu Linux (64 Bit)**

1. Wählen Sie **Weiter** aus.

1. Wählen Sie den relevanten Datenspeicher und dann **Weiter** aus.

1. Ändern Sie die virtuellen Hardwareparameter entsprechend der erforderlichen Architektur.

1. Wählen Sie für **CD/DVD-Laufwerk 1** die Option **Datenspeicher-ISO-Datei** und dann die zuvor hochgeladene ISO-Datei aus.

1. Klicken Sie auf **Weiter** > **Fertig stellen**.

### <a name="create-the-virtual-machine-hyper-v"></a>Erstellen des virtuellen Computers (Hyper-V)

In diesem Verfahren wird beschrieben, wie Sie mithilfe von Hyper-V einen virtuellen Computer erstellen.

So erstellen Sie einen virtuellen Computer:

1. Erstellen Sie im Hyper-V-Manager einen virtuellen Datenträger.

1. Wählen Sie **format = VHDX** aus.

1. Wählen Sie **type = Dynamic Expanding** (Dynamisch erweiterbar) aus.

1. Geben Sie den Namen und Speicherort für die virtuelle Festplatte ein.

1. Geben Sie die erforderliche Größe (entsprechend der Architektur) ein.

1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Fertig stellen** aus.

1. Erstellen Sie im Menü **Aktionen** einen neuen virtuellen Computer.

1. Geben Sie einen Namen für den virtuellen Computer ein.

1. Wählen Sie **Generation angeben** > **Generation 1** aus.

1. Geben Sie die Speicherbelegung (entsprechend der Architektur) an, und aktivieren Sie das Kontrollkästchen für den dynamischen Arbeitsspeicher.

1. Konfigurieren Sie den Netzwerkadapter entsprechend Ihrer Server-Netzwerktopologie.

1. Verbinden Sie die zuvor erstellte VHDX-Datei mit dem virtuellen Computer.

1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Fertig stellen** aus.

1. Klicken Sie mit der rechten Maustaste auf den neuen virtuellen Computer, und wählen Sie **Einstellungen** aus.

1. Wählen Sie **Hardware hinzufügen** aus, und fügen Sie einen neuen Netzwerkadapter hinzu.

1. Wählen Sie den virtuellen Switch aus, der eine Verbindung mit dem Sensorverwaltungsnetzwerk herstellen wird.

1. Ordnen Sie CPU-Ressourcen (entsprechend der Architektur) zu.

1. Verbinden Sie das ISO-Image der Verwaltungskonsole mit einem virtuellen DVD-Laufwerk.

1. Starten Sie den virtuellen Computer.

1. Wählen Sie im Menü **Aktionen** die Option **Verbinden** aus, um die Softwareinstallation fortzusetzen.

### <a name="software-installation-esxi-and-hyper-v"></a>Softwareinstallation (ESXi und Hyper-V)

In diesem Abschnitt wird die ESXi- und Hyper-V-Softwareinstallation beschrieben.

So führen Sie die Installation durch:

1. Öffnen Sie die Konsole für virtuelle Computer.

1. Der virtuelle Computer wird aus dem ISO-Image gestartet, und der Bildschirm für die Sprachauswahl wird angezeigt. Wählen Sie **Englisch** (?Deutsch?) aus.

1. Wählen Sie die erforderliche Architektur aus.

1. Definieren Sie das Applianceprofil und die Netzwerkeigenschaften:

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Hardwareprofil** | &lt;erforderliche Architektur&gt; |
    | **Verwaltungsschnittstelle** | **ens192** |
    | **Netzwerkparameter (vom Kunden bereitgestellt)** | **IP-Adresse des Verwaltungsnetzwerks:** <br/>**Subnetzmaske:** <br/>**Appliance-Hostname:** <br/>**DNS:** <br/>**Standardgateway:** <br/>**Eingabeschnittstellen:**|
    | **Bridge-Schnittstellen:** | Die Bridge-Schnittstelle muss nicht konfiguriert werden. Diese Option wird nur bei besonderen Anwendungsfällen verwendet. |

1. Geben Sie **Y** ein, um die Einstellungen zu akzeptieren.

1. Anmeldeinformationen werden automatisch generiert und angezeigt. Kopieren Sie den Benutzernamen und das Kennwort an einen sicheren Ort, weil sie für die Anmeldung und Verwaltung erforderlich sind.

      - **Support**: Der Administrator für die Benutzerverwaltung.

      - **CyberX**: Die Entsprechung von „root“ für den Zugriff auf die Appliance.

1. Die Appliance wird neu gestartet.

1. Greifen Sie über die zuvor konfigurierte IP-Adresse auf die-Verwaltungskonsole zu: `https://ip_address`.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Screenshot des Zugriffs auf die Verwaltungskonsole":::

## <a name="on-premises-management-console-installation"></a>Installation der lokalen Verwaltungskonsole

Vor Installation der Software auf der Appliance müssen Sie deren BIOS-Konfiguration anpassen:

### <a name="bios-configuration"></a>BIOS-Konfiguration

**So konfigurieren Sie das BIOS für Ihre Appliance**:

1. [Aktivieren Sie den Remotezugriff, und aktualisieren Sie das Kennwort](#enable-remote-access-and-update-the-password).

1. [Konfigurieren Sie das BIOS](#configure-the-hpe-bios).

### <a name="software-installation"></a>Softwareinstallation

Der Installationsvorgang dauert ungefähr 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

Während des Installationsvorgangs können Sie eine sekundäre NIC hinzufügen. Wenn Sie die sekundäre NIC während der Installation nicht installieren möchten, können Sie zu einem späteren Zeitpunkt [eine sekundäre NIC hinzufügen](#add-a-secondary-nic).

So installieren Sie die Software:

1. Wählen Sie Ihre bevorzugte Sprache für den Installationsvorgang aus.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Wählen Sie Ihre bevorzugte Sprache für den Installationsvorgang aus.":::

1. Wählen Sie **MANAGEMENT-RELEASE-\<version\>\<deployment type\>** aus.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Wählen Sie Ihre Version aus.":::

1. Definieren Sie im Installations-Assistenten die Netzwerkeigenschaften:

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="Screenshot des Applianceprofils":::

   | Parameter | Konfiguration |
   |--|--|
   | **Konfigurieren der Verwaltungsnetzwerkschnittstelle** | Für Dell: **eth0, eth1** <br /> Für HP: **enu1, enu2** <br>  oder <br />**möglicher Wert** |
   | **Konfigurieren der IP-Adresse des Verwaltungsnetzwerks:** | **Die vom Kunden angegebene IP-Adresse** |
   | **Konfigurieren der Subnetzmaske:** | **Die vom Kunden angegebene IP-Adresse** |
   | **Konfigurieren von DNS:** | **Die vom Kunden angegebene IP-Adresse** |
   | **Konfigurieren der IP-Adresse des Standardgateways:** | **Die vom Kunden angegebene IP-Adresse** |

1. **(Optional)** Wenn Sie eine sekundäre Netzwerkschnittstellenkarte (Network Interface Card, NIC) installieren möchten, definieren Sie das folgende Applianceprofil und die folgenden Netzwerkeigenschaften:

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="Screenshot mit Fragen zur Installation der sekundären NIC":::

   | Parameter | Konfiguration |
   |--|--|
   | **Konfigurieren der Sensorüberwachungsschnittstelle (optional):** | **eth1** oder **möglicher Wert** |
   | **Konfigurieren einer IP-Adresse für die Sensorüberwachungsschnittstelle:** | **Die vom Kunden angegebene IP-Adresse** |
   | **Konfigurieren einer Subnetzmaske für die Sensorüberwachungsschnittstelle:** | **Die vom Kunden angegebene IP-Adresse** |

1. Akzeptieren Sie die Einstellungen, und setzen Sie den Vorgang durch Eingabe von `Y` fort.

1. Nach ungefähr 10 Minuten werden die beiden Anmeldeinformationssätze angezeigt. Einer ist für einen **CyberX**-Benutzer und einer für einen **Support**-Benutzer bestimmt.

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Kopieren Sie diese Anmeldeinformationen, weil sie nicht erneut angezeigt werden.":::  

   Speichern Sie die Benutzernamen und Kennwörter. Sie benötigen diese Anmeldeinformationen für den Zugriff auf die Plattform, wenn Sie sie zum ersten Mal verwenden.

1. Wählen Sie **Eingeben** aus, um den Vorgang fortzusetzen.

Informationen dazu, wie Sie den physischen Port auf Ihrer Appliance finden können, finden Sie unter [Suchen Ihres Ports](#find-your-port).

### <a name="add-a-secondary-nic"></a>Hinzufügen einer sekundären NIC

Sie können die Sicherheit Ihrer lokalen Verwaltungskonsole erhöhen, indem Sie eine sekundäre NIC hinzufügen. Durch das Hinzufügen einer sekundären NIC haben Sie eine dedizierte NIC für Ihre Benutzer, und die andere NIC unterstützt die Konfiguration eines Gateways für geroutete Netzwerke. Die sekundäre NIC wird für alle angefügten Sensoren innerhalb eines IP-Adressbereichs dediziert.

:::image type="content" source="media/tutorial-install-components/secondary-nic.png" alt-text="Die Gesamtarchitektur der sekundären NIC":::

Beide NICs unterstützen die Benutzeroberfläche. Wenn Sie wählen, dass Sie keine sekundäre NIC bereitstellen möchten, stehen alle Features über die primäre NIC zur Verfügung.

Wenn Sie Ihre lokale Verwaltungskonsole bereits konfiguriert haben und ihr eine sekundäre NIC hinzufügen möchten, führen Sie die folgenden Schritte aus:

1. Verwenden Sie den Befehl zum Neukonfigurieren des Netzwerks („network reconfigure“):

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Geben Sie auf die folgenden Fragen die folgenden Antworten ein:

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="Geben Sie die folgenden Antworten zum Konfigurieren Ihrer Appliance ein.":::

    | Parameter | Einzugebende Antwort |
    |--|--|
    | **IP-Adresse des Verwaltungsnetzwerks** | `N` |
    | **Subnetzmaske** | `N` |
    | **DNS** | `N` |
    | **IP-Adresse des Standardgateways** | `N` |
    | **Sensorüberwachungsschnittstelle (Optional. Anwendbar, wenn sich Sensoren in einem anderen Netzwerksegment befinden. Weitere Informationen finden Sie in den „Installationsanleitungen“.)**| `Y`, **wählen Sie einen möglichen Wert aus.** |
    | **Eine IP-Adresse für die Sensorüberwachungsschnittstelle (auf die die Sensoren zugreifen können)** | `Y`, **die vom Kunden angegebene IP-Adresse**|
    | **Eine Subnetzmaske für die Sensorüberwachungsschnittstelle (auf die die Sensoren zugreifen können)** | `Y`, **die vom Kunden angegebene IP-Adresse** |
    | **Hostname** | **wird vom Kunden angegeben** |

1. Überprüfen Sie alle Auswahlmöglichkeiten, und geben Sie `Y` ein, um die Änderungen zu akzeptieren. Das System wird neu gestartet.

### <a name="find-your-port"></a>Suchen Ihres Ports

Wenn Sie Probleme bei der Suche nach dem physischen Port auf Ihrem Gerät haben, können Sie den folgenden Befehl verwenden:

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

Dieser Befehl führt dazu, dass das Licht am Port während des angegebenen Zeitraums blinkt. Wenn Sie beispielsweise `sudo ethtool -p eno1 120` eingeben, blinkt Port „eno1“ 2 Minuten lang, sodass Sie den Port auf der Rückseite Ihrer Appliance finden können.

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Virtuelles Gerät: Installation der lokalen Verwaltungskonsole

Der virtuelle Computer der lokalen Verwaltungskonsole unterstützt die folgenden Architekturen:

| Aufbau | Spezifikationen | Verwendung |
|--|--|--|
| Enterprise <br/>(Standard und am häufigsten) | CPU: 8 <br/>Memory: 32G RAM<br/> HDD: 1,8 TB | Große Produktionsumgebungen |
| Klein | CPU: 4 <br/> Memory: 8G RAM<br/> HDD: 500 GB | Große Produktionsumgebungen |
| Office | CPU: 4 <br/>Memory: 8G RAM <br/> HDD: 100 GB | Kleine Testumgebungen |

### <a name="prerequisites"></a>Voraussetzungen

Die lokale Verwaltungskonsole unterstützt sowohl VMware- als auch Hyper-V-Bereitstellungsoptionen. Überprüfen Sie vor Installationsbeginn die folgenden Punkte:

- VMware (ESXi 5.5 oder höher) oder Hyper-V-Hypervisor (Windows 10 Pro oder Enterprise) wurde installiert und ist betriebsbereit.

- Die Hardwareressourcen stehen für den virtuellen Computer zur Verfügung.

- Sie haben die ISO-Installationsdatei für die lokale Verwaltungskonsole.

- Der Hypervisor wird ausgeführt.

### <a name="create-the-virtual-machine-esxi"></a>Erstellen des virtuellen Computers (ESXi)

So erstellen Sie den virtuellen Computer (ESXi):

1. Melden Sie sich beim ESXi an, wählen Sie den relevanten **Datenspeicher** und dann **Datenspeicherbrowser** aus.

1. Laden Sie das Image hoch, und wählen Sie **Schließen** aus.

1. Wechseln Sie zu **Virtuelle Computer**.

1. Wählen Sie **VM erstellen/registrieren** aus.

1. Wählen Sie **Neuen virtuellen Computer erstellen** und dann **Weiter** aus.

1. Fügen Sie einen Sensornamen hinzu, und wählen Sie:

   - Kompatibilität: \<latest ESXi version> aus.

   - Gastbetriebssystemfamilie: Linux

   - Gastbetriebssystemversion: Ubuntu Linux (64 Bit)

1. Wählen Sie **Weiter** aus.

1. Wählen Sie den relevanten Datenspeicher und dann **Weiter** aus.

1. Ändern Sie die virtuellen Hardwareparameter entsprechend der erforderlichen Architektur.

1. Wählen Sie für **CD/DVD-Laufwerk 1** die Option **Datenspeicher-ISO-Datei** und dann die zuvor hochgeladene ISO-Datei aus.

1. Klicken Sie auf **Weiter** > **Fertig stellen**.

### <a name="create-the-virtual-machine-hyper-v"></a>Erstellen des virtuellen Computers (Hyper-V)

So erstellen Sie einen virtuellen Computer mithilfe von Hyper-V:

1. Erstellen Sie im Hyper-V-Manager einen virtuellen Datenträger.

1. Wählen Sie das Format **VHDX** aus.

1. Wählen Sie **Weiter** aus.

1. Wählen Sie den Typ **Dynamic expanding** (Dynamisch erweiterbar) aus.

1. Wählen Sie **Weiter** aus.

1. Geben Sie den Namen und Speicherort für die virtuelle Festplatte ein.

1. Wählen Sie **Weiter** aus.

1. Geben Sie die erforderliche Größe (entsprechend der Architektur) ein.

1. Wählen Sie **Weiter** aus.

1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Fertig stellen** aus.

1. Erstellen Sie im Menü **Aktionen** einen neuen virtuellen Computer.

1. Wählen Sie **Weiter** aus.

1. Geben Sie einen Namen für den virtuellen Computer ein.

1. Wählen Sie **Weiter** aus.

1. Wählen Sie **Generation** aus, und legen Sie den Wert auf **Generation 1** fest.

1. Wählen Sie **Weiter** aus.

1. Geben Sie die Speicherbelegung (entsprechend der Architektur) an, und aktivieren Sie das Kontrollkästchen für den dynamischen Arbeitsspeicher.

1. Wählen Sie **Weiter** aus.

1. Konfigurieren Sie den Netzwerkadapter entsprechend Ihrer Server-Netzwerktopologie.

1. Wählen Sie **Weiter** aus.

1. Verbinden Sie die zuvor erstellte VHDX-Datei mit dem virtuellen Computer.

1. Wählen Sie **Weiter** aus.

1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Fertig stellen** aus.

1. Klicken Sie mit der rechten Maustaste auf den neuen virtuellen Computer, und wählen Sie **Einstellungen** aus.

1. Wählen Sie **Hardware hinzufügen** aus, und fügen Sie für **Netzwerkadapter** einen neuen Adapter hinzu.

1. Wählen Sie für **Virtueller Switch** den Switch aus, der eine Verbindung mit dem Sensorverwaltungsnetzwerk herstellen wird.

1. Ordnen Sie CPU-Ressourcen (entsprechend der Architektur) zu.

1. Verbinden Sie das ISO-Image der Verwaltungskonsole mit einem virtuellen DVD-Laufwerk.

1. Starten Sie den virtuellen Computer.

1. Wählen Sie im Menü **Aktionen** die Option **Verbinden** aus, um die Softwareinstallation fortzusetzen.

### <a name="software-installation-esxi-and-hyper-v"></a>Softwareinstallation (ESXi und Hyper-V)

Mit dem Start des virtuellen Computers wird der Installationsvorgang aus dem ISO-Image gestartet.

So installieren Sie die Software:

1. Wählen Sie **Englisch** (?Deutsch?) aus.

1. Wählen Sie die erforderliche Architektur für Ihre Bereitstellung aus.

1. Definieren Sie die Netzwerkschnittstelle für das Sensorverwaltungsnetzwerk: Schnittstelle, IP, Subnetz, DNS-Server und Standardgateway.

1. Anmeldeinformationen werden automatisch generiert. Speichern Sie den Benutzernamen und die Kennwörter. Sie benötigen diese Anmeldeinformationen für den Zugriff auf die Plattform, wenn Sie sie zum ersten Mal verwenden.

   Die Appliance wird dann neu gestartet.

1. Greifen Sie über die zuvor konfigurierte IP-Adresse auf die Verwaltungskonsole zu: `<https://ip_address>`.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Screenshot des Anmeldebildschirms auf der Verwaltungskonsole":::

## <a name="legacy-appliances"></a>Legacyappliances

In diesem Abschnitt werden die Installationsverfahren nur für *Legacyappliances* beschrieben. Weitere Informationen finden Sie unter [Identifizieren erforderlicher Appliances](how-to-identify-required-appliances.md#identify-required-appliances), wenn Sie eine neue Appliance erwerben.

### <a name="nuvo-5006lp-installation"></a>Nuvo 5006LP-Installation

In diesem Abschnitt wird das Installationsverfahren für Nuvo 5006LP beschrieben. Vor der Installation der Software auf der Nuvo 5006LP-Appliance müssen Sie deren BIOS-Konfiguration anpassen.

#### <a name="nuvo-5006lp-front-panel"></a>Vorderseite des Nuvo 5006LP-Geräts

:::image type="content" source="media/tutorial-install-components/nuvo5006lp_frontpanel.png" alt-text="Ansicht der Vorderseite des Nuvo 5006LP-Geräts":::

1. Netzschalter, Betriebsanzeige
1. DVI-Videoanschlüsse
1. HDMI-Videoanschlüsse
1. VGA-Videoanschlüsse
1. Ein-/Ausschalten der Remotesteuerung und Status-LED-Ausgabe
1. Taste zum Zurücksetzen
1. Verwaltungsnetzwerkadapter
1. Ports zum Empfang gespiegelter Daten

#### <a name="nuvo-back-panel"></a>Nuvo-Rückseite

:::image type="content" source="media/tutorial-install-components/nuvo5006lp_backpanel.png" alt-text="Ansicht des Rückseite des Nuvo 5006LP-Geräts":::

1. SIM-Kartenslot
1. Mikrofon und Lautsprecher
1. COM-Ports
1. USB-Anschlüsse
1. Gleichstrom-Netzanschluss (DC IN)

#### <a name="configure-the-nuvo-5006lp-bios"></a>Konfigurieren des Nuvo 5006LP-BIOS

Im folgenden Verfahren wird beschrieben, wie Sie das BIOS eines Nuvo 5006LP-Geräts konfigurieren. Stellen Sie sicher, dass das Betriebssystem vorab auf der Appliance installiert wurde.

**So konfigurieren Sie das BIOS**:

1. Schalten Sie die Appliance ein.

1. Drücken Sie **F2**, um die BIOS-Konfiguration zu starten.

1. Navigieren Sie zu **Power** (Stromversorgung), und ändern Sie „Power On after Power Failure“ (Nach Stromausfall einschalten) in „S0-Power On“ (S0 – einschalten).

    :::image type="content" source="media/tutorial-install-components/nuvo-power-on.png" alt-text="Ändern des Nuvo 5006LP-Geräts für das Einschalten nach einem Stromausfall":::

1. Navigieren Sie zu **Boot** (Start), und stellen Sie sicher, dass **PXE Boot to LAN** (PXE-LAN-Start) auf **Disabled** (Deaktiviert) festgelegt ist.

1. Drücken Sie zum Speichern **F10**, und wählen Sie dann **Exit** (Beenden) aus.

#### <a name="software-installation-nuvo-5006lp"></a>Softwareinstallation (Nuvo 5006LP)

Der Installationsvorgang dauert ca. 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

**So installieren Sie die Software**:

1. Verbinden Sie die externe CD oder den USB-Stick mit dem ISO-Image.

1. Starten Sie die Appliance.

1. Wählen Sie **Englisch** (?Deutsch?) aus.

1. Wählen Sie **XSENSE-RELEASE-\<version> Office...** aus.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Auswählen der zu installierenden Version des Sensors":::

1. Definieren Sie die Appliancearchitektur und die Netzwerkeigenschaften:

    :::image type="content" source="media/tutorial-install-components/nuvo-profile-appliance.png" alt-text="Definieren der Nuvo-Architektur und der Netzwerkeigenschaften":::

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Hardwareprofil** | Wählen Sie **office** aus. |
    | **Verwaltungsschnittstelle** | **eth0** |
    | **IP-Adresse des Verwaltungsnetzwerks** | **Die vom Kunden angegebene IP-Adresse** |
    | **Subnetzmaske des Verwaltungsnetzwerks** | **Die vom Kunden angegebene IP-Adresse** |
    | **DNS** | **Die vom Kunden angegebene IP-Adresse** |
    | **IP-Adresse des Standardgateways** | **0.0.0.0** |
    | **Eingabeschnittstelle** | Die Liste der Eingabeschnittstellen wird vom System für Sie generiert. <br />Kopieren Sie zum Spiegeln der Eingabeschnittstellen alle in der Liste mit einem Komma als Trennzeichen dargestellten Elemente. |
    | **Brückenschnittstelle** | - |

1. Akzeptieren Sie die Einstellungen, und setzen Sie den Vorgang durch Eingabe von `Y` fort.

Nach etwa 10 Minuten werden automatisch Anmeldeinformationen generiert. Speichern Sie den Benutzernamen und die Kennwörter. Sie benötigen diese Anmeldeinformationen für den Zugriff auf die Plattform, wenn Sie sie zum ersten Mal verwenden.

### <a name="fitlet2-mini-sensor-installation"></a>Installation des Fitlet2-Minisensors

In diesem Abschnitt wird das Installationsverfahren für Fitlet2 beschrieben. Vor der Installation der Software auf der Fitlet-Appliance müssen Sie deren BIOS-Konfiguration anpassen.

#### <a name="fitlet2-front-panel"></a>Vorderseite des Fitlet2-Geräts

:::image type="content" source="media/tutorial-install-components/fitlet-front-panel.png" alt-text="Ansicht der Vorderseite des Fitlet2-Geräts":::

#### <a name="fitlet2-back-panel"></a>Rückseite des Fitlet2-Geräts

:::image type="content" source="media/tutorial-install-components/fitlet2-back-panel.png" alt-text="Ansicht der Rückseite des Fitlet2-Geräts":::

#### <a name="configure-the-fitlet2-bios"></a>Konfigurieren des Fitlet2-BIOS

**So konfigurieren Sie das Fitlet2-BIOS**:

1. Schalten Sie die Appliance ein.

1. Navigieren Sie zu **Main** > **OS Selection** (Hauptbereich > Betriebssystemauswahl).

1. Drücken Sie **+/-** , um **Linux** auszuwählen.

    :::image type="content" source="media/tutorial-install-components/fitlet-linux.png" alt-text="Festlegen von Linux als Betriebssystem auf dem Fitlet2-Gerät":::

1. Überprüfen Sie, ob das Systemdatum und die Systemzeit mit dem Datum und der Uhrzeit der Installation aktualisiert werden.

1. Navigieren Sie zu **Advanced** (Erweitert), und wählen Sie **ACPI Settings** (ACPI-Einstellungen) aus.

1. Wählen Sie **Enable Hibernation** (Ruhezustand aktivieren) aus, und drücken Sie **+/-** , um **Disabled** (Deaktiviert) auszuwählen.

    :::image type="content" source="media/tutorial-install-components/disable-hibernation.png" alt-text="Deaktivieren des Ruhezustands auf dem Fitlet2-Gerät":::

1. Drücken Sie die **ESC**-Taste.

1. Navigieren Sie zu **Advanced** > **TPM Configuration** (Erweitert > TPM-Konfiguration).

1. Wählen Sie **fTPM** aus, und drücken Sie **+/-** , um **Disabled** (Deaktiviert) auszuwählen.

1. Drücken Sie die **ESC**-Taste.

1. Navigieren Sie zu **CPU Configuration** > **VT-d** (CPU-Konfiguration > VT-d).

1. Drücken Sie **+/-** , um **Enabled** (Aktiviert) auszuwählen.

1. Navigieren Sie zu **CSM Configuration** > **CSM Support** (CSM-Konfiguration > CSM-Unterstützung).

1. Drücken Sie **+/-** , um **Enabled** (Aktiviert) auszuwählen.

1. Navigieren Sie zu **Advanced** > **Boot option filter [Legacy only]** (Erweitert > Startoptionsfilter [nur Legacy]), und ändern Sie die Einstellung in den folgenden Feldern in **Legacy**:

    - Netzwerk
    - Storage
    - Video
    - Andere PCI

    :::image type="content" source="media/tutorial-install-components/legacy-only.png" alt-text="Festlegen aller Felder auf „Legacy“":::

1. Drücken Sie die **ESC**-Taste.

1. Navigieren Sie zu **Security** > **Secure Boot Customization** (Sicherheit > Anpassung für sicheren Start).

1. Drücken Sie **+/-** , um **Disabled** (Deaktiviert) auszuwählen.

1. Drücken Sie die **ESC**-Taste.

1. Navigieren Sie zu **Boot** > **Boot mode select** (Start > Startmodus auswählen), und wählen Sie **Legacy** aus.

1. Wählen Sie **Boot Option #1 – [USB CD/DVD]** (Startoption 1: [USB CD/DVD]) aus.

1. Wählen Sie **Save & Exit** (Speichern und beenden) aus.

#### <a name="software-installation-fitlet2"></a>Softwareinstallation (Fitlet2)

Der Installationsvorgang dauert ca. 20 Minuten. Nach der Installation wird das System mehrmals neu gestartet.

1. Verbinden Sie die externe CD oder den USB-Stick mit dem ISO-Image.

1. Starten Sie die Appliance.

1. Wählen Sie **Englisch** (?Deutsch?) aus.

1. Wählen Sie **XSENSE-RELEASE-\<version> Office...** aus.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Auswählen der zu installierenden Version des Sensors":::

    > [!Note]
    > Wählen Sie nicht „Ruggedized“ aus.

1. Definieren Sie die Appliancearchitektur und die Netzwerkeigenschaften:

    :::image type="content" source="media/tutorial-install-components/nuvo-profile-appliance.png" alt-text="Definieren der Nuvo-Architektur und der Netzwerkeigenschaften":::

    | Parameter | Konfiguration |
    | ----------| ------------- |
    | **Hardwareprofil** | Wählen Sie **office** aus. |
    | **Verwaltungsschnittstelle** | **em1** |
    | **IP-Adresse des Verwaltungsnetzwerks** | **Die vom Kunden angegebene IP-Adresse** |
    | **Subnetzmaske des Verwaltungsnetzwerks** | **Die vom Kunden angegebene IP-Adresse** |
    | **DNS** | **Die vom Kunden angegebene IP-Adresse** |
    | **IP-Adresse des Standardgateways** | **0.0.0.0** |
    | **Eingabeschnittstelle** | Die Liste der Eingabeschnittstellen wird vom System für Sie generiert. <br />Kopieren Sie zum Spiegeln der Eingabeschnittstellen alle in der Liste mit einem Komma als Trennzeichen dargestellten Elemente. |
    | **Brückenschnittstelle** | - |

1. Akzeptieren Sie die Einstellungen, und setzen Sie den Vorgang durch Eingabe von `Y` fort.

Nach etwa 10 Minuten werden automatisch Anmeldeinformationen generiert. Speichern Sie den Benutzernamen und die Kennwörter. Sie benötigen diese Anmeldeinformationen für den Zugriff auf die Plattform, wenn Sie sie zum ersten Mal verwenden.

## <a name="post-installation-validation"></a>Überprüfung nach der Installation

Zum Überprüfen der Installation einer physischen Appliance müssen Sie viele Tests durchführen. Derselbe Überprüfungsvorgang gilt für alle Appliancetypen.

Führen Sie die Überprüfung über die GUI oder die CLI durch. Die Überprüfung steht für den Benutzer **Support** und den Benutzer **CyberX** zur Verfügung.

Die Überprüfung nach der Installation muss die folgenden Tests enthalten:

- **Integritätstest**: Überprüfen Sie, ob das System ausgeführt wird.

- **Version**: Überprüfen Sie, ob die Version richtig ist.

- **ifconfig**: Überprüfen Sie, ob alle während des Installationsvorgangs konfigurierten Eingabeschnittstellen ausgeführt werden.

### <a name="check-system-health-by-using-the-gui"></a>Überprüfen der Systemintegrität über die GUI

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Screenshot der Systemintegritätsprüfung":::

#### <a name="sanity"></a>Integrität

- **Appliance**: Führt die Integritätsprüfung der Appliance durch. Sie können dieselbe Überprüfung mit dem CLI-Befehl `system-sanity` durchführen.

- **Version**: Zeigt die Applianceversion an.

- **Netzwerkeigenschaften**: Zeigt die Netzwerkparameter des Sensors an.

#### <a name="redis"></a>Redis

- **Arbeitsspeicher**: Liefert das Gesamtbild der Speicherauslastung, z. B. wie viel Arbeitsspeicher belegt wurde und wie viel noch übrig ist.

- **Längster Schlüssel**: Zeigt die längsten Schlüssel an, die zu einer übermäßigen Speicherauslastung führen können.

#### <a name="system"></a>System

- **Kernprotokoll**: Stellt die letzten 500 Zeilen des Kernprotokolls bereit, sodass Sie die neuesten Protokollzeilen anzeigen können, ohne das gesamte Systemprotokoll exportieren zu müssen.

- **Task-Manager**: Übersetzt die in der Tabelle der Prozesse angezeigten Aufgaben in die folgenden Ebenen:
  
  - Persistente Ebene (Redis)

  - Cacheebene (SQL)

- **Netzwerkstatistik**: Zeigt Ihre Netzwerkstatistik an.

- **TOP**: Zeigt die Tabelle der Prozesse (Table Of Processes) an. Dies ist ein Linux-Befehl, der eine dynamische Echtzeitansicht des ausgeführten Systems bereitstellt.

- **Überprüfung des Sicherungsspeichers**: Gibt den Status des Sicherungsspeichers an und überprüft Folgendes:

  - Den Speicherort des Sicherungsordners

  - Die Größe des Sicherungsordners

  - Die Einschränkungen des Sicherungsordners

  - Den Zeitpunkt der letzten Sicherung

  - Wie viel Speicherplatz für die zusätzlichen Sicherungsdateien vorhanden ist

- **ifconfig**: Zeigt die Parameter für die physischen Schnittstellen der Appliance an.

- **CyberX nload**: Zeigt den Netzwerkdatenverkehr und die Bandbreite mithilfe der 6-Sekunden-Tests an.

- **Fehler aus Core.log**: Zeigt Fehler aus der Kernprotokolldatei an.

**So greifen Sie auf das Tool zu**:

1. Melden Sie sich beim Sensor mit den Benutzeranmeldeinformationen für **Support** an.

1. Wählen Sie im Fenster **Systemeinstellungen** die Option **Systemstatistik** aus.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="check-system-health-by-using-the-cli"></a>Überprüfen der Systemintegrität über die CLI

Vergewissern Sie sich, dass das System betriebsbereit ist und ausgeführt wird, bevor Sie die Integrität des Systems testen.

**So testen Sie die Integrität des Systems**:

1. Stellen Sie eine Verbindung mit der CLI mit dem Linux-Terminal (z. b. PuTTY) und dem Benutzer **Support** her.

1. Geben Sie `system sanity` ein.

1. Überprüfen Sie, ob alle Dienste grün sind (d. h., sie werden ausgeführt).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Der Screenshot zeigt, dass Dienste ausgeführt werden.":::

1. Überprüfen Sie, ob unten **System is UP! (prod)** (System ist AKTIV (Prod)) angezeigt wird.

Überprüfen Sie, ob die richtige Version verwendet wird:

**So überprüfen Sie die Version des Systems**:

1. Stellen Sie eine Verbindung mit der CLI mit dem Linux-Terminal (z. b. PuTTY) und dem Benutzer **Support** her.

1. Geben Sie `system version` ein.

1. Überprüfen Sie, ob die richtige Version angezeigt wird.

Überprüfen Sie, ob alle während des Installationsvorgangs konfigurierten Eingabeschnittstellen ausgeführt werden:

**So überprüfen Sie den Netzwerkstatus des Systems**:

1. Stellen Sie eine Verbindung mit der CLI mit dem Linux-Terminal (z. b. PuTTY) und dem Benutzer **Support** her.

1. Geben Sie `network list` ein (entspricht dem Linux-Befehl `ifconfig`).

1. Überprüfen Sie, ob die erforderlichen Eingabeschnittstellen angezeigt werden. Wenn beispielsweise zwei Quad-Kupfer-NICs installiert wurden, sollte die Liste 10 Schnittstellen enthalten.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Screenshot der Liste von Schnittstellen":::

Überprüfen Sie, ob Sie auf die Web-GUI der Konsole zugreifen können:

**So überprüfen Sie, ob die Verwaltung auf die Benutzeroberfläche zugreifen kann**:

1. Schließen Sie einen Laptop mit einem Ethernet-Kabel an den Verwaltungsport (**Gb1**) an.

1. Definieren Sie für die Laptop-NIC-Adresse, dass sie sich in demselben Bereich wie die Appliance befinden soll.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Screenshot des Verwaltungszugriffs auf die Benutzeroberfläche":::

1. Pingen Sie die IP-Adresse der Appliance vom Laptop aus, um die Konnektivität zu überprüfen (Standard: 10.100.10.1).

1. Öffnen Sie den Chrome-Browser auf dem Laptop, und geben Sie die IP-Adresse der Appliance ein.

1. Wählen Sie im Fenster **Ihre Verbindung ist nicht privat** die Option **Erweitert** aus, und setzen Sie den Vorgang fort.

1. Der Test ist erfolgreich, wenn der Defender für IoT-Anmeldebildschirm angezeigt wird.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Screenshot des Zugriffs auf die Verwaltungskonsole":::

## <a name="troubleshooting"></a>Problembehandlung

### <a name="you-cant-connect-by-using-a-web-interface"></a>Sie können keine Verbindung über eine Weboberfläche herstellen

1. Überprüfen Sie, ob sich der Computer, mit dem Sie die Verbindung herstellen möchten, in demselben Netzwerk wie die Appliance befindet.

1. Überprüfen Sie, ob das GUI-Netzwerk mit dem Verwaltungsport verbunden ist.

1. Pingen Sie die IP-Adresse der Appliance. Wenn Pingen nicht möglich ist:

   1. Schließen Sie einen Monitor und eine Tastatur an die Appliance an.

   1. Verwenden Sie den Benutzer **Support** und dessen Kennwort für die Anmeldung.

   1. Verwenden Sie den Befehl `network list`, um die aktuelle IP-Adresse anzuzeigen.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Screenshot der Netzwerkliste":::

1. Wenn die Netzwerkparameter falsch konfiguriert wurden, ändern Sie sie mit dem folgenden Verfahren:

   1. Verwenden Sie den Befehl `network edit-settings`.

   1. Wählen Sie zum Ändern der IP-Adresse des Verwaltungsnetzwerks **Y** aus.

   1. Wählen Sie zum Ändern der Subnetzmaske **Y** aus.

   1. Wählen Sie zum Ändern des DNS **Y** aus.

   1. Wählen Sie zum Ändern der IP-Adresse des Standardgateways **Y** aus.

   1. Wählen Sie für eine Änderung der Eingabeschnittstelle (nur Sensor) **N** aus.

   1. Wählen Sie zum Übernehmen der Einstellungen **Y** aus.

1. Stellen Sie nach dem Neustart eine Verbindung mit den Anmeldeinformationen des Supports her, und überprüfen Sie mit dem Befehl `network list`, ob die Parameter geändert wurden.

1. Versuchen Sie von der GUI aus erneut, zu pingen und eine Verbindung herzustellen.

### <a name="the-appliance-isnt-responding"></a>Die Appliance reagiert nicht

1. Schließen Sie einen Monitor und eine Tastatur an die Appliance an, oder verwenden Sie PuTTY, um eine Remoteverbindung mit der CLI herzustellen.

1. Verwenden Sie die Anmeldeinformationen für den Benutzer **Support**, um sich anzumelden.

1. Verwenden Sie den Befehl `system sanity`, um zu überprüfen, ob alle Prozesse ausgeführt werden.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Screenshot des Befehls „system sanity“ für Systemintegrität":::

Wenden Sie sich bei allen anderen Problemen an den [Microsoft-Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="configure-a-span-port"></a>Konfigurieren eines SPAN-Ports

Ein virtueller Switch verfügt nicht über Spiegelungsfunktionen. Sie können jedoch den promisken Modus in einer Umgebung mit virtuellen Switches verwenden. Der promiske Modus ist ein Betriebsmodus sowie eine Sicherheits-, Überwachungs- und Verwaltungstechnik, die auf der Ebene des virtuellen Switches oder der Portgruppe definiert ist. Der promiske Modus ist standardmäßig deaktiviert. Bei Aktivierung des promisken Modus verwenden die Netzwerkschnittstellen des virtuellen Computers, die sich in derselben Portgruppe befinden, den promisken Modus, um den gesamten Netzwerkdatenverkehr anzuzeigen, der diesen virtuellen Switch durchläuft. Sie können eine Problemumgehung entweder mit ESXi oder Hyper-V implementieren.

:::image type="content" source="media/tutorial-install-components/purdue-model.png" alt-text="Screenshot der Stelle in Ihrer Architektur, an der der Sensor platziert werden soll.":::

### <a name="configure-a-span-port-with-esxi"></a>Konfigurieren eines SPAN-Ports mit ESXi

**So konfigurieren Sie einen SPAN-Port mit ESXi:**

1. Öffnen Sie „vSwitch“-Eigenschaften.

1. Wählen Sie **Hinzufügen**.

1. Wählen Sie **Virtueller Computer** > **Weiter** aus.

1. Fügen Sie die Netzwerkbezeichnung **SPAN-Netzwerk** ein, wählen Sie **VLAN-ID** > **Alle** und dann **Weiter** aus.

1. Wählen Sie **Fertig stellen** aus.

1. Wählen Sie **SPAN-Netzwerk** > **Bearbeiten* aus.

1. Wählen Sie **Sicherheit** aus, und überprüfen Sie, ob die Richtlinie für **Promisker Modus** auf den Modus **Akzeptieren** festgelegt wurde.

1. Wählen Sie **OK** und dann **Schließen** aus, um die vSwitch-Eigenschaften zu schließen.

1. Öffnen Sie die **XSense VM**-Eigenschaften.

1. Wählen Sie für **Netzwerkadapter 2** das **SPAN**-Netzwerk aus.

1. Klicken Sie auf **OK**.

1. Stellen Sie eine Verbindung mit dem Sensor her, und überprüfen Sie, ob die Spiegelung funktioniert.

### <a name="configure-a-span-port-with-hyper-v"></a>Konfigurieren eines SPAN-Ports mit Hyper-V

Bevor Sie beginnen, müssen Sie Folgendes sicherstellen:

- Es wird keine Instanz einer virtuellen Appliance ausgeführt.

- SPAN ist für den Datenport und nicht für den Verwaltungsport aktiviert.

- Die SPAN-Konfiguration des Datenports ist nicht mit einer IP-Adresse konfiguriert.

**So konfigurieren Sie einen SPAN-Port mit Hyper-V:**

1. Öffnen Sie den Manager für virtuelle Switches.

1. Wählen Sie in der Liste virtueller Switches **Neuer virtueller Netzwerkswitch** > **Extern** als Typ für den dedizierten übergreifenden Netzwerkadapter aus.

    :::image type="content" source="media/tutorial-install-components/new-virtual-network.png" alt-text="Screenshot: Auswählen von „Neuer virtueller Netzwerkswitch“ und „Extern“ vor dem Erstellen des virtuellen Switches":::

1. Wählen Sie **Virtuellen Switch erstellen** aus.

1. Wählen Sie unter „Verbindungstyp“ die Option **Externes Netzwerk** aus.

1. Vergewissern Sie sich, dass **Gemeinsames Verwenden dieses Netzwerkadapters für das Verwaltungsbetriebssystem zulassen** aktiviert ist.

   :::image type="content" source="media/tutorial-install-components/external-network.png" alt-text="Auswählen von „Externes Netzwerk“ und „Gemeinsames Verwenden dieses Netzwerkadapters für das Verwaltungsbetriebssystem zulassen“":::

1. Klicken Sie auf **OK**.

#### <a name="attach-a-span-virtual-interface-to-the-virtual-switch"></a>Anfügen einer virtuellen SPAN-Schnittstelle an den virtuellen Switch

Sie können über Windows PowerShell oder den Hyper-V-Manager eine virtuelle SPAN-Schnittstelle an den virtuellen Switch anfügen.

**So fügen Sie über PowerShell eine virtuelle SPAN-Schnittstelle an den virtuellen Switch an**

1. Wählen Sie den neu hinzugefügten virtuellen SPAN-Switch aus, und fügen Sie mit dem folgenden Befehl einen neuen Netzwerkadapter hinzu:

    ```bash
    ADD-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 -Name Monitor -SwitchName vSwitch_Span
    ```

1. Aktivieren Sie mit dem folgenden Befehl die Portspiegelung für die ausgewählte Schnittstelle als SPAN-Ziel:

    ```bash
    Get-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 | ? Name -eq Monitor | Set-VMNetworkAdapter -PortMirroring Destination
    ```

    | Parameter | BESCHREIBUNG |
    |--|--|
    | VK-C1000V-LongRunning-650 | Name der virtuellen CPPM-Appliance |
    |vSwitch_Span |Name des neu hinzugefügten virtuellen SPAN-Switches |
    |Monitor |Name des neu hinzugefügten Adapters |

1. Klicken Sie auf **OK**.

Mit diesen Befehlen wird der Name der neu hinzugefügten Adapterhardware auf `Monitor` festgelegt. Bei Verwendung des Hyper-V-Managers wird der Name der neu hinzugefügten Adapterhardware auf `Network Adapter` festgelegt.

**So fügen Sie über den Hyper-V-Manager eine virtuelle SPAN-Schnittstelle an den virtuellen Switch an**

1. Wählen Sie in der Hardwareliste die Option **Netzwerkadapter** aus.

1. Wählen Sie im Feld „Virtueller Switch“ die Option **vSwitch_Span** aus.

    :::image type="content" source="media/tutorial-install-components/vswitch-span.png" alt-text="Screenshot: Auswählen der folgenden Optionen auf dem Bildschirm des virtuellen Switches":::

1. Wählen Sie in der Hardwareliste in der Dropdownliste „Netzwerkadapter“ die Option **Erweiterte Features** aus.

1. Wählen Sie im Abschnitt „Portspiegelung“ die Option **Ziel** als Spiegelungsmodus für die neue virtuelle Schnittstelle aus.

    :::image type="content" source="media/tutorial-install-components/destination.png" alt-text="Screenshot: Erforderliche Auswahl zum Konfigurieren des Spiegelungsmodus":::

1. Klicken Sie auf **OK**.

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>Aktivieren der Microsoft NDIS-Aufzeichnungserweiterungen für den virtuellen Switch

Die Microsoft NDIS-Aufzeichnungserweiterungen müssen für den neuen virtuellen Switch aktiviert werden.

**So aktivieren Sie Microsoft NDIS-Aufzeichnungserweiterungen für den neu hinzugefügten virtuellen Switch**:

1. Öffnen Sie auf dem Hyper-V-Host den Manager für virtuelle Switches.

1. Erweitern Sie in der Liste „Virtuelle Switches“ den Namen des virtuellen Switches (`vSwitch_Span`), und wählen Sie **Erweiterungen** aus.

1. Wählen Sie im Feld „Switcherweiterungen“ die Option **Microsoft NDIS-Aufzeichnung** aus.

    :::image type="content" source="media/tutorial-install-components/microsoft-ndis.png" alt-text="Screenshot: Aktivieren von „Microsoft NDIS-Aufzeichnung“ durch Auswahl im Menü „Switcherweiterungen“":::

1. Klicken Sie auf **OK**.

#### <a name="set-the-mirroring-mode-on-the-external-port"></a>Festlegen des Spiegelungsmodus für den externen Port

Der Spiegelungsmodus muss für den externen Port des neuen virtuellen Switches festgelegt werden, der als Quelle fungieren soll.

Sie müssen den virtuellen Hyper-V-Switch (vSwitch_Span) so konfigurieren, dass der gesamte Datenverkehr, der an den externen Quellport gesendet wird, an den virtuellen Netzwerkadapter weitergeleitet wird, den Sie als Ziel konfiguriert haben.

Verwenden Sie die folgenden PowerShell-Befehle, um den Port des externen virtuellen Switches auf den Quellspiegelungsmodus festzulegen:

```bash
$ExtPortFeature=Get-VMSystemSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings"
$ExtPortFeature.SettingData.MonitorMode=2
Add-VMSwitchExtensionPortFeature -ExternalPort -SwitchName vSwitch_Span -VMSwitchExtensionFeature $ExtPortFeature
```

| Parameter | BESCHREIBUNG |
|--|--|
| vSwitch_Span | Name des neu hinzugefügten virtuellen SPAN-Switches |
| MonitorMode=2 | `Source` |
| MonitorMode=1 | Destination |
| MonitorMode=0 | Keine |

Verwenden Sie den folgenden PowerShell-Befehl, um den Status des Überwachungsmodus zu überprüfen:

```bash
Get-VMSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings" -SwitchName vSwitch_Span -ExternalPort | select -ExpandProperty SettingData
```

| Parameter | BESCHREIBUNG |
|--|--|
| vSwitch_Span | Name des neu hinzugefügten virtuellen SPAN-Switches |

## <a name="access-sensors-from-the-on-premises-management-console"></a>Zugreifen auf Sensoren über die lokale Verwaltungskonsole

Sie können die Systemsicherheit verbessern, indem Sie den direkten Benutzerzugriff auf den Sensor verhindern. Verwenden Sie stattdessen Proxy-Tunnelung, damit Benutzer mit einer einzigen Firewallregel über die lokale Verwaltungskonsole auf den Sensor zugreifen können. Diese Vorgehensweise schränkt die Möglichkeit eines nicht autorisierten Zugriffs auf die Netzwerkumgebung außerhalb des Sensors ein. Die Benutzeroberfläche bleibt bei der Anmeldung beim Sensor unverändert.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Screenshot des Zugriffs auf den Sensor":::

**So aktivieren Sie Tunnelung**:

1. Melden Sie sich bei der Befehlszeilenschnittstelle der lokalen Verwaltungskonsole mit den Benutzeranmeldeinformationen für **CyberX** oder **Support** an.

1. Geben Sie `sudo cyberx-management-tunnel-enable` ein.

1. Drücken Sie die **EINGABETASTE**.

1. Geben Sie `--port 10000` ein.

### <a name="next-steps"></a>Nächste Schritte

[Einrichten Ihres Netzwerks](how-to-set-up-your-network.md)
