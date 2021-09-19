---
title: Verwalten von Gerätezertifikaten – Azure IoT Edge | Microsoft-Dokumentation
description: Sie können Testzertifikate erstellen und auf einem Azure IoT Edge-Gerät installieren und verwalten, um die Bereitstellung in der Produktion vorzubereiten.
author: kgremban
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3866217f0aa90cd3450d0f74e35eaa68cea3c559
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866544"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Verwalten von Zertifikaten auf einem IoT Edge-Gerät

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Alle IoT Edge-Geräte verwenden Zertifikate, um sichere Verbindungen zwischen der Runtime und allen Modulen zu erstellen, die auf dem Gerät ausgeführt werden. IoT Edge-Geräte, die als Gateways fungieren, verwenden dieselben Zertifikate, um auch eine Verbindung mit ihren Downstreamgeräten herzustellen.

## <a name="install-production-certificates"></a>Installieren von Produktionszertifikaten

Wenn Sie IoT Edge installieren und Ihr Gerät bereitstellen, wird das Gerät mit temporären Zertifikaten eingerichtet, sodass Sie den Dienst testen können.
Diese temporären Zertifikate laufen nach 90 Tagen ab oder können durch einen Neustart des Computers zurückgesetzt werden.
Wenn Sie in ein Produktionsszenario wechseln oder ein Gatewaygerät erstellen möchten, müssen Sie Ihre eigenen Zertifikate bereitstellen.
In diesem Artikel werden die Schritte zum Installieren von Zertifikaten auf Ihren IoT Edge-Geräten veranschaulicht.

Weitere Informationen zu den verschiedenen Arten von Zertifikaten und deren Rollen finden Sie unter [Grundlegendes zur Verwendung von Zertifikaten durch Azure IoT Edge](iot-edge-certs.md).

>[!NOTE]
>Der in diesem Artikel verwendete Begriff „Stamm-CA“ bezieht sich auf das öffentliche Zertifikat der obersten Zertifizierungsstelle in der Zertifikatkette für Ihre IoT-Lösung. Sie müssen nicht den Zertifikatstamm einer syndizierten Zertifizierungsstelle oder den Stamm der Zertifizierungsstelle Ihres Unternehmens verwenden. In vielen Fällen ist es tatsächlich nur ein öffentliches Zertifikat einer Zwischenzertifizierungsstelle.

### <a name="prerequisites"></a>Voraussetzungen

* Ein IoT Edge Gerät.

  Wenn Sie kein IoT Edge-Gerät eingerichtet haben, können Sie eines auf einem virtuellen Azure-Computer erstellen. Führen Sie die Schritte in einem der Schnellstartartikel zu [Erstellen eines virtuellen Linux-Geräts](quickstart-linux.md) oder [Erstellen eines virtuellen Windows-Geräts](quickstart.md) aus.

* Ein Zertifikat einer Stamm-CA, das entweder selbstsigniert ist oder über eine vertrauenswürdige kommerzielle Zertifizierungsstelle wie Baltimore, Verisign, DigiCert oder GlobalSign ausgestellt wurde

  Wenn Sie noch keine Stamm-CA besitzen, aber IoT Edge-Features ausprobieren möchten, für die Produktionszertifikate erforderlich sind (z. B. Gatewayszenarios), können Sie [Demozertifikate erstellen, um Features für IoT Edge-Geräte zu testen](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Erstellen von Produktionszertifikaten

Sie sollten Ihre eigene Zertifizierungsstelle verwenden, um die folgenden Dateien zu erstellen:

* Stamm-CA
* Zertifikat der Gerätezertifizierungsstelle
* Privater Schlüssel des Zertifikats der Gerätezertifizierungsstelle

In diesem Artikel ist mit *Stamm-CA* nicht die oberste Zertifizierungsstelle einer Organisation gemeint. Es ist die oberste Zertifizierungsstelle für das IoT Edge-Szenario gemeint, die das IoT Edge-Hub-Modul, die Benutzermodule und alle Downstreamgeräte verwenden, um eine gegenseitige Vertrauensstellung aufzubauen.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Derzeit verhindert eine Einschränkung in libiothsm die Verwendung von Zertifikaten, die am bzw. nach dem 1. Januar 2038 ablaufen.

:::moniker-end

Ein Beispiel für diese Zertifikate finden Sie in den Skripts zum Erstellen von Demozertifikaten in [Verwalten von Zertifikaten von Testzertifizierungsstellen für Beispiele und Tutorials](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Installieren von Zertifikaten auf dem Gerät

Installieren Sie Ihre Zertifikatkette auf dem IoT Edge-Gerät, und konfigurieren Sie die IoT Edge-Runtime so, dass sie auf die neuen Zertifikate verweist.

Kopieren Sie diese drei Zertifikat und Schlüsseldateien auf Ihr IoT Edge-Gerät. 

Wenn Sie die Beispielskripts zum [Erstellen von Demozertifikaten](how-to-create-test-certificates.md) verwendet haben, befinden sich die drei Zertifikat- und Schlüsseldateien in den folgenden Pfaden:

* Zertifikat der Gerätezertifizierungsstelle: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Privater Schlüssel des Zertifikats der Gerätezertifizierungsstelle: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Stamm-CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

Sie können einen Dienst wie [Azure Key Vault](../key-vault/index.yml) oder eine Funktion wie [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) zum Verschieben der Zertifikatsdateien verwenden. Wenn Sie die Zertifikate auf dem IoT Edge-Gerät selbst erstellt haben, können Sie diesen Schritt überspringen und den Pfad für das Arbeitsverzeichnis verwenden.

Wenn Sie IoT Edge für Linux unter Windows verwenden, müssen Sie Dateiübertragungen zwischen dem Host-Betriebssystem und dem virtuellen Linux-Computer mithilfe des in der Azure IoT Edge-Datei `id_rsa` gespeicherten SSH-Schlüssels authentifizieren. Rufen Sie die IP-Adresse des virtuellen Linux-Computers mit dem Befehl `Get-EflowVmAddr` ab. Dann können Sie ein authentifiziertes SCP mit dem folgenden Befehl ausführen:

   ```powershell
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

### <a name="configure-iot-edge-with-the-new-certificates"></a>Konfigurieren von IoT Edge mit den neuen Zertifikaten

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

# <a name="linux-containers"></a>[Linux-Container](#tab/linux)

1. Öffnen Sie die Konfigurationsdatei des IoT Edge-Sicherheitsdaemons: `/etc/iotedge/config.yaml`

1. Legen Sie die Eigenschaften für **Zertifikat** in „config.yaml“ auf den URI-Pfad zu den Zertifikat- und Schlüsseldateien auf dem IoT Edge-Gerät fest. Entfernen Sie das `#`-Zeichen vor den Zertifikateigenschaften, um die Auskommentierung der vier Zeilen aufzuheben. Stellen Sie sicher, dass der Zeile **certificates:** kein Leerzeichen vorangestellt ist und dass die geschachtelten Zertifikate jeweils um zwei Leerzeichen eingerückt sind. Beispiel:

   ```yaml
   certificates:
      device_ca_cert: "file:///<path>/<device CA cert>"
      device_ca_pk: "file:///<path>/<device CA key>"
      trusted_ca_certs: "file:///<path>/<root CA cert>"
   ```

1. Stellen Sie sicher, dass der Benutzer **iotedge** Leseberechtigungen für das Verzeichnis mit den Zertifikaten hat.

1. Wenn Sie zuvor bereits andere Zertifikate für IoT Edge auf dem Gerät verwendet haben, löschen Sie die Dateien in den folgenden beiden Verzeichnissen, bevor Sie IoT Edge starten oder neu starten:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Starten Sie IoT Edge neu.

   ```bash
   sudo iotedge system restart
   ```

# <a name="windows-containers"></a>[Windows-Container](#tab/windows)

1. Öffnen Sie die Konfigurationsdatei des IoT Edge-Sicherheitsdaemons: `C:\ProgramData\iotedge\config.yaml`

1. Legen Sie die Eigenschaften für **Zertifikat** in „config.yaml“ auf den URI-Pfad zu den Zertifikat- und Schlüsseldateien auf dem IoT Edge-Gerät fest. Entfernen Sie das `#`-Zeichen vor den Zertifikateigenschaften, um die Auskommentierung der vier Zeilen aufzuheben. Stellen Sie sicher, dass der Zeile **certificates:** kein Leerzeichen vorangestellt ist und dass die geschachtelten Zertifikate jeweils um zwei Leerzeichen eingerückt sind. Beispiel:

   ```yaml
   certificates:
      device_ca_cert: "file:///C:/<path>/<device CA cert>"
      device_ca_pk: "file:///C:/<path>/<device CA key>"
      trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
   ```

1. Wenn Sie zuvor bereits andere Zertifikate für IoT Edge auf dem Gerät verwendet haben, löschen Sie die Dateien in den folgenden beiden Verzeichnissen, bevor Sie IoT Edge starten oder neu starten:

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. Starten Sie IoT Edge neu.

   ```powershell
   Restart-Service iotedge
   ```
---

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Öffnen Sie die Konfigurationsdatei des IoT Edge-Sicherheitsdaemons: `/etc/aziot/config.toml`

1. Suchen Sie am Anfang der Datei den Parameter `trust_bundle_cert`. Heben Sie die Auskommentierung dieser Zeile auf, und geben Sie den Datei-URI zum Zertifikat der Stammzertifizierungsstelle auf Ihrem Gerät an.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Suchen Sie in der Datei „config.toml“ nach dem Abschnitt `[edge_ca]`. Heben Sie die Auskommentierung der Zeilen in diesem Abschnitt auf, und geben Sie die Datei-URI-Pfade für die Zertifikat- und Schlüsseldateien auf dem IoT Edge Gerät an.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Stellen Sie sicher, dass der Dienst Leseberechtigungen für die Verzeichnisse mit den Zertifikaten und Schlüsseln hat.

   * Die Datei mit dem privaten Schlüssel sollte im Besitz der Gruppe **aziotks** sein.
   * Die Zertifikatdateien sollten im Besitz der Gruppe **aziotcs** sein.

   >[!TIP]
   >Wenn Ihr Zertifikat schreibgeschützt ist, d. h. Sie es erstellt haben und nicht möchten, dass der IoT Edge-Dienst es rotiert, legen Sie die Datei mit dem privaten Schlüssel auf den Modus „0440“ und die Zertifikatdatei auf den Modus „0444“ fest. Wenn Sie die Anfangsdateien erstellt und dann den Zertifikatdienst so konfiguriert haben, dass das Zertifikat künftig rotiert wird, legen Sie die Datei mit dem privaten Schlüssel auf den Modus „0660“ und die Zertifikatdatei auf den Modus „0664“ fest.

1. Wenn Sie auf dem Gerät vorher andere Zertifikate für IoT Edge verwendet haben, löschen Sie die Dateien im folgenden Verzeichnis. IoT Edge erstellt sie dann mit dem von Ihnen bereitgestellten neuen Zertifizierungsstellenzertifikat neu.

   * `/var/lib/aziot/certd/certs`

1. Übernehmen Sie die Konfigurationsänderungen.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="customize-certificate-lifetime"></a>Anpassen der Gültigkeitsdauer von Zertifikaten

IoT Edge generiert in mehreren Fällen automatisch Zertifikate auf dem Gerät:

* Wenn Sie bei der Installation und Bereitstellung von IoT Edge keine eigenen Produktionszertifikate bereitstellen, generiert der IoT Edge-Sicherheits-Manager automatisch ein **gerätebezogenes Zertifizierungsstellenzertifikat**. Dieses selbstsignierte Zertifikat ist nur für Entwicklungs- und Testszenarien und nicht für die Produktion gedacht. Es läuft nach 90 Tagen ab.
* Der IoT Edge-Sicherheits-Manager generiert auch ein **workloadbezogenes Zertifizierungsstellenzertifikat**, das vom gerätebezogenen Zertifizierungsstellenzertifikat signiert ist.

Weitere Informationen zur Aufgabe der verschiedenen Zertifikate auf einem IoT Edge-Gerät finden Sie unter [Grundlegendes zur Verwendung von Zertifikaten durch Azure IoT Edge](iot-edge-certs.md).

Bei diesen beiden automatisch generierten Zertifikaten können Sie ein Flag in der Konfigurationsdatei festlegen, um die Anzahl der Tage für die Gültigkeitsdauer der Zertifikate zu konfigurieren.

>[!NOTE]
>Es gibt ein drittes automatisch generiertes Zertifikat, das vom IoT Edge-Sicherheits-Manager erstellt wird, das **IoT Edge-Hubserverzertifikat**. Dieses Zertifikat hat stets eine Gültigkeitsdauer von 90 Tagen, wird aber vor Ablauf automatisch verlängert. Der in der Konfigurationsdatei festgelegte Wert für die automatisch generierte Gültigkeitsdauer der Zertifizierungsstelle wirkt sich nicht auf dieses Zertifikat aus.

Bei Ablauf nach der angegebenen Anzahl von Tagen muss IoT Edge neu gestartet werden, um das Zertifikat der Gerätezertifizierungsstelle erneut zu generieren. Das Zertifikat der Gerätezertifizierungsstelle wird nicht automatisch erneuert.

<!-- 1.1. -->
:::moniker range="iotedge-2018-06"

# <a name="linux-containers"></a>[Linux-Container](#tab/linux)

1. Wenn Sie für das Ablaufdatum des Zertifikats einen Wert konfigurieren möchten, der nicht dem Standardwert von 90 Tagen entspricht, fügen Sie im Abschnitt **certificates** der Konfigurationsdatei den Wert in Tagen hinzu.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Derzeit verhindert eine Einschränkung in libiothsm die Verwendung von Zertifikaten, die am oder nach dem 1. Januar 2038 ablaufen.

1. Löschen Sie den Inhalt des Ordners `hsm`, um alle zuvor generierten Zertifikate zu entfernen.

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Starten Sie den IoT Edge-Dienst neu.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Bestätigen Sie die Einstellung der Gültigkeitsdauer.

   ```bash
   sudo iotedge check --verbose
   ```

   Prüfen Sie die Ausgabe der Prüfung **Produktionsbereitschaft: Zertifikate**, in der die Anzahl der Tage bis zum Ablauf der automatisch generierten gerätebezogenen Zertifizierungsstellenzertifikate aufgelistet ist.

# <a name="windows-containers"></a>[Windows-Container](#tab/windows)

1. Wenn Sie für das Ablaufdatum des Zertifikats einen Wert konfigurieren möchten, der nicht dem Standardwert von 90 Tagen entspricht, fügen Sie im Abschnitt **certificates** der Konfigurationsdatei den Wert in Tagen hinzu.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Derzeit verhindert eine Einschränkung in libiothsm die Verwendung von Zertifikaten, die am oder nach dem 1. Januar 2038 ablaufen.

1. Löschen Sie den Inhalt des Ordners `hsm`, um alle zuvor generierten Zertifikate zu entfernen.

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. Starten Sie den IoT Edge-Dienst neu.

   ```powershell
   Restart-Service iotedge
   ```

1. Bestätigen Sie die Einstellung der Gültigkeitsdauer.

   ```powershell
   iotedge check --verbose
   ```

   Prüfen Sie die Ausgabe der Prüfung **Produktionsbereitschaft: Zertifikate**, in der die Anzahl der Tage bis zum Ablauf der automatisch generierten gerätebezogenen Zertifizierungsstellenzertifikate aufgelistet ist.

---

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Wenn Sie für das Ablaufdatum des Zertifikats einen anderen Wert als den Standardwert „90 Tage“ konfigurieren möchten, fügen Sie diesen Wert im Abschnitt **Edge CA certificate (Quickstart)** (Edge-Zertifizierungsstellenzertifikat (Schnellstart)) der Konfigurationsdatei in Tagen hinzu.

   ```toml
   [edge_ca]
   auto_generated_edge_ca_expiry_days = <value>
   ```

1. Löschen Sie den Inhalt der Ordner `certd` und `keyd`, um alle zuvor generierten Zertifikate zu entfernen: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Übernehmen Sie die Konfigurationsänderungen.

   ```bash
   sudo iotedge config apply
   ```

1. Bestätigen Sie die neue Einstellung für die Gültigkeitsdauer.

   ```bash
   sudo iotedge check --verbose
   ```

   Prüfen Sie die Ausgabe der Prüfung **Produktionsbereitschaft: Zertifikate**, in der die Anzahl der Tage bis zum Ablauf der automatisch generierten gerätebezogenen Zertifizierungsstellenzertifikate aufgelistet ist.
:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>Nächste Schritte

Das Installieren von Zertifikaten auf einem IoT Edge-Gerät ist erforderlich, bevor Sie die Lösung in der Produktionsumgebung bereitstellen. Unter [Vorbereiten der Bereitstellung einer IoT Edge-Lösung für die Produktion](production-checklist.md) finden Sie weitere Informationen.
