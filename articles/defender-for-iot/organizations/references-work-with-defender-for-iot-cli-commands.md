---
title: Arbeiten mit den CLI-Befehlen von Defender für IoT
description: In diesem Artikel werden die CLI-Befehle von Defender für IoT für Sensoren und lokale Verwaltungskonsolen beschrieben.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 4561f0e9697826fff808d03e3bf38f0d546d7c8a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347178"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Arbeiten mit den CLI-Befehlen von Defender für IoT

In diesem Artikel werden die CLI-Befehle für Sensoren und lokale Verwaltungskonsolen beschrieben. Die folgenden Benutzer können auf die Befehle zugreifen:

- Administrator
- CyberX 
- Support

Um die CLI zu verwenden, stellen Sie zunächst eine Verbindung mit einem Terminal her. Beispiel: Terminalname `Putty` und Benutzer `Support`. 

## <a name="create-local-alert-exclusion-rules"></a>Erstellen von lokalen Warnungsausschlussregeln

Sie können eine lokale Warnungsausschlussregel erstellen, indem Sie an der CLI den folgenden Befehl eingeben:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Mit Warnungsausschlussregeln können Sie folgende Attribute verwenden:

| attribute | Beschreibung |
|--|--|
| [-h] | Druckt die Hilfeinformationen für den Befehl. |
| -n NAME | Der Name der Regel, die erstellt wird. |
| [-ts TIMES] | Die Zeitspanne, in der die Regel aktiv ist. Diese sollte wie folgt angegeben werden:<br />`xx:yy-xx:yy`<br />Sie können mehr als einen Zeitraum definieren, indem Sie zwischen den Zeiträumen ein Komma eingeben. Beispiel: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-dir DIRECTION] | Die Richtung, in der die Regel angewendet wird. Diese sollte wie folgt angegeben werden:<br />`both | src | dst` |
| [-dev DEVICES] | Die IP-Adresse und der Adresstyp der Geräte, die durch die Regel ausgeschlossen werden sollen, in folgender Form:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a ALERTS] | Der Name der Warnung, die durch die Regel ausgeschlossen wird:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Anfügen von lokalen Warnungsausschlussregeln

Sie können lokale Warnungsausschlussregeln anfügen, indem Sie an der CLI den folgenden Befehl eingeben:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Die hier verwendeten Attribute sind identisch mit den Attributen, die im Abschnitt „Erstellen von lokalen Warnungsausschlussregeln“ erläutert werden. Der Unterschied bei der Verwendung besteht darin, dass die Attribute hier auf die vorhandenen Regeln angewendet werden.

## <a name="show-local-alert-exclusion-rules"></a>Anzeigen von lokalen Warnungsausschlussregeln

Geben Sie den folgenden Befehl ein, um die vorhandene Liste der Ausschlussregeln anzuzeigen:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Löschen von lokalen Warnungsausschlussregeln

Sie können eine vorhandene Ausschlussregel löschen, indem Sie den folgenden Befehl eingeben:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Mit Warnungsausschlussregeln können Sie folgendes Attribut verwenden:

| attribute | BESCHREIBUNG|
| --------- | ---------------------------------- |
| -n NAME | Der Name der zu löschenden Regel. |

## <a name="sync-time-from-the-ntp-server"></a>Zeitsynchronisierung vom NTP-Server

Sie können eine Zeitsynchronisierung von einem angegebenen NTP-Server aktivieren oder deaktivieren.

### <a name="enable-ntp-sync"></a>Aktivieren der NTP-Synchronisierung

Geben Sie den folgenden Befehl ein, um die Zeit regelmäßig vom angegebenen NTP-Server abzurufen:

```azurecli-interactive
ntp enable IP
```

Das Attribut, das Sie im Befehl definieren können, ist die IP-Adresse des NTP-Servers.

### <a name="disable-ntp-sync"></a>Deaktivieren der NTP-Synchronisierung

Mit dem folgenden Befehl wird die Zeitsynchronisierung mit dem angegebenen NTP-Server deaktiviert:

```azurecli-interactive
ntp disable IP
```

Das Attribut, das Sie mit dem Befehl definieren können, ist die IP-Adresse des NTP-Servers.

## <a name="network-configuration"></a>Netzwerkkonfiguration

In der folgenden Tabelle werden die Befehle beschrieben, die zum Konfigurieren der Netzwerkoptionen für Azure Defender für IoT verfügbar sind:

|Name|Befehl|BESCHREIBUNG|
|-----------|-------|-----------|
|Ping|`ping IP`| Sendet einen Ping an eine Adresse außerhalb der Defender für IoT-Plattform.|
|Blink|`network blink`| Identifizieren Sie eine Verbindung, indem Sie die Schnittstellenlichter blinken lassen. |
|Neukonfigurieren des Netzwerks |`network edit-settings`| Ermöglicht das Ändern der Parameter der Netzwerkkonfiguration. |
|Anzeigen der Netzwerkeinstellungen |`network list`|Zeigt die Parameter für den Netzwerkadapter an. |
|Überprüfen der Netzwerkkonfiguration |`network validate` |Zeigt die Einstellungen des Ausgabenetzwerks an. <br /> <br />Beispiel: <br /> <br />Aktuelle Netzwerkeinstellungen: <br /> interface: eth0 <br /> ip: 10.100.100.1 <br />subnet: 255.255.255.0 <br />default gateway: 10.100.100.254 <br />dns: 10.100.100.254 <br />monitor interfaces: eth1|
|Importieren eines Zertifikats |`certificate import FILE` |Importiert das HTTPS-Zertifikat. Sie müssen den vollständigen Pfad angeben, der zu einer \*.crt-Datei führt. |
|Anzeigen des Datums |`date` |Gibt das aktuelle Datum auf dem Host im GMT-Format zurück. |

## <a name="network-capture-filter-configuration"></a>Konfiguration des Netzwerkerfassungsfilters

Mit dem Befehl `network capture-filter` können Administratoren Netzwerkdatenverkehr eliminieren, der nicht analysiert werden muss. Sie können den Datenverkehr mithilfe einer Einschluss- oder Ausschlussliste filtern.

```azurecli-interactive
network capture-filter
```

Nach Eingabe des Befehls erhalten Sie die folgende Eingabeaufforderung:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Wählen Sie `Y` aus, um eine Nano-Datei zu öffnen, in der Sie ein Gerät, einen Kanal, einen Port und eine Teilmenge mit der folgenden Syntax hinzufügen können:

| attribute | BESCHREIBUNG |
|--|--|
| 1.1.1.1 | Schließt den gesamten Datenverkehr für dieses Gerät ein. |
| 1.1.1.1,2.2.2.2 | Schließt den gesamten Datenverkehr für diesen Kanal ein. |
| 1.1.1,2.2.2 | Schließt den gesamten Datenverkehr für dieses Subnetz ein. |

Trennen Sie Argumente durch Entfernen einer Zeile.

Wenn Sie ein Gerät, einen Kanal oder ein Subnetz einschließen, verarbeitet der Sensor den gesamten gültigen Datenverkehr für dieses Argument, einschließlich Ports und Datenverkehr, die normalerweise nicht verarbeitet würden.

Ihnen wird dann folgende Frage gestellt:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Wählen Sie `Y` aus, um eine Nano-Datei zu öffnen, in der Sie ein Gerät, einen Kanal, einen Port und eine Teilmenge mit der folgenden Syntax hinzufügen können:

| attribute | BESCHREIBUNG |
|--|--|
| 1.1.1.1 | Schließt den gesamten Datenverkehr für dieses Gerät aus. |
| 1.1.1.1,2.2.2.2 | Schließt den gesamten Datenverkehr für diesen Kanal (d. h. den gesamten Datenverkehr zwischen zwei Geräten) aus. |
| 1.1.1.1,2.2.2.2,443 | Schließt den Datenverkehr für diesen Kanal nach Port aus. |
| 1.1.1 | Schließt den gesamten Datenverkehr für dieses Subnetz aus. |
| 1.1.1,2.2.2 | Schließt den gesamten Datenverkehr zwischen Subnetzen aus. |

Trennen Sie Argumente durch Entfernen einer Zeile.

Wenn Sie ein Gerät, einen Kanal oder ein Subnetz ausschließen, schließt der Sensor den gesamten gültigen Datenverkehr für dieses Argument aus.

### <a name="ports"></a>Ports

Schließen Sie UDP- und TCP-Ports für den gesamten Datenverkehr ein oder aus.

>`502`: einzelner Port

>`502,443`: beide Ports

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Komponenten

Ihnen wird die folgende Frage gestellt:

>`In which component do you wish to apply this capture filter?`

Sie haben folgende Optionen: `all`, `dissector`, `collector`, `statistics-collector`, `rpc-parser` oder `smb-parser`.

Wählen Sie in den meisten Anwendungsfällen `all` aus.

### <a name="custom-base-capture-filter"></a>Benutzerdefinierter Basiserfassungsfilter

Der Basiserfassungsfilter ist die Baseline für die Komponenten. Der Filter bestimmt beispielsweise, welche Ports für die Komponente verfügbar sind.

Wählen Sie bei allen folgenden Optionen `Y` aus. Alle Filter werden der Baseline hinzugefügt, nachdem die Änderungen festgelegt wurden. Wenn Sie eine Änderung vornehmen, wird die vorhandene Baseline überschrieben.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Wenn ein Subnetz ausgeschlossen werden soll (z. B. 1.1.1:

- `internal`), wird nur dieses Subnetz ausgeschlossen.

- Mit `all-connected` wird dieses Subnetz und der gesamte ein- und ausgehende Datenverkehr für dieses Subnetz ausgeschlossen.

Es wird empfohlen, `internal` auszuwählen.

> [!NOTE]
> Ihre Auswahl wird für alle Filter im Tool verwendet und ist nicht sitzungsabhängig. Das heißt: Sie können keinesfalls für einige Filter `internal` und für andere Filter  `all-connected` auswählen.

### <a name="comments"></a>Kommentare

Sie können Filter in  ```/var/cyberx/properties/cybershark.properties``` anzeigen:

- **statistics-collector**:  `bpf_filter property` in  ```/var/cyberx/properties/net.stats.collector.properties```

- **dissektor**: `override.capture_filter` -Eigenschaft in  ```/var/cyberx/properties/cybershark.properties```

- **rpc-parser**: `override.capture_filter`-Eigenschaft in  ```/var/cyberx/properties/rpc-parser.properties```

- **smb-parser**:  `override.capture_filter` -Eigenschaft in  ```/var/cyberx/properties/smb-parser.properties```

- **collector**: `general.bpf_filter` -Eigenschaft in  ```/var/cyberx/properties/collector.properties```

Sie können die Standardkonfiguration wiederherstellen, indem Sie den folgenden Code für den CyberX-Benutzer eingeben:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Definieren von Client- und Serverhosts

Wenn Client- und Serverhosts von Defender für IoT nicht automatisch erkannt wurden, geben Sie den folgenden Befehl ein, um die Client- und Serverhosts festzulegen:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Mit dem Befehl `directions` können Sie die folgenden Attribute verwenden:

| Attribut | BESCHREIBUNG |
|--|--|
| [-h] | Druckt Hilfeinformationen für den Befehl. |
| [--identifier IDENTIFIER] | Der Serverbezeichner. |
| [--port PORT] | Der Serverport. |
| [--remove] | Entfernt einen Client- oder Serverhost aus der Liste. |
| [--add] | Fügt einen Client- oder Serverhost zur Liste hinzu. |
| [--tcp] | Verwendet TCP bei der Kommunikation mit diesem Host. |
| [--udp] | Verwendet UDP bei der Kommunikation mit diesem Host. |

## <a name="system-actions"></a>Systemaktionen
In der folgenden Tabelle werden die verfügbaren Befehle zum Ausführen verschiedener Systemaktionen mit Defender für IOT beschrieben:

|Name|Code|BESCHREIBUNG|
|----|----|-----------|
|Anzeigen des Datums|`date`|Gibt das aktuelle Datum auf dem Host im GMT-Format zurück.|
|Host neu starten|`system reboot`|Startet das Hostgerät neu.|
|Host herunterfahren|`system shutdown`|Fährt den Host herunter.|
|System sichern|`system backup`|Initiiert eine sofortige Sicherung (nicht geplante Sicherung).|
|System aus Sicherung wiederherstellen|`system restore`|Eine Wiederherstellung aus der letzten Sicherung wird ausgeführt.|
|Sicherungsdateien auflisten|`system backup-list`|Listet die verfügbaren Sicherungsdaten auf.|
|Status aller Defender für IOT-Plattformdienste anzeigen|`system sanity`|Überprüft die Leistung des Systems durch Auflisten des aktuellen Status aller Defender für IOT-Plattformdienste.|
|Softwareversion anzeigen|`system version`|Zeigt die Version der Software an, die aktuell auf dem System ausgeführt wird.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>SSL- und TLS-Zertifikate für Appliances bereitstellen

Geben Sie den folgenden Befehl ein, um SSL- und TLS-Unternehmenszertifikate in die CLI zu importieren:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Um das Tool verwenden zu können, müssen Sie die Zertifikatdateien auf das Gerät hochladen. Hierfür können Sie Tools wie WinSCP oder Wget verwenden. 

Der Befehl unterstützt die folgenden Eingabeflags:

| Flag | Beschreibung |
|--|--|
| -h | Zeigt die Syntax in der Befehlszeilenhilfe an. |
| --crt | Der Pfad zur Zertifikatdatei (CRT-Dateierweiterung). |
| --key | Die \*.key-Datei. Die Schlüssellänge muss mindestens 2.048 Bits betragen. |
| --chain | Der Pfad zur Zertifikatskettendatei (optional). |
| --pass | Die Passphrase, die zum Verschlüsseln des Zertifikats verwendet wird (optional). |
| --passphrase-set | Die Standardeinstellung lautet **False**, **unused**. <br />Legen Sie die Einstellung auf **True** fest, um die vorherige Passphrase zu verwenden, die mit dem vorherigen Zertifikat bereitgestellt wurde (optional). | 

Bei Verwendung des Tools gilt Folgendes:

- Überprüfen Sie, ob die Zertifikatdateien auf der Appliance lesbar sind. 

- Vergewissern Sie sich bei der IT-Abteilung, dass die Appliance-Domäne (wie im Zertifikat angezeigt) mit Ihrem DNS-Server und der entsprechenden IP-Adresse angezeigt wird. 
    
## <a name="see-also"></a>Weitere Informationen

[API-Sensor und Verwaltungskonsolen-APIs für Defender für IOT](references-work-with-defender-for-iot-apis.md)
