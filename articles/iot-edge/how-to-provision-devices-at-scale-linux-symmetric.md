---
title: Erstellen und Bereitstellen von loT Edge-Geräten mit symmetrischen Schlüsseln unter Linux – Azure loT Edge | Microsoft-Dokumentation
description: Verwenden Sie den Nachweis mit symmetrischen Schlüsseln, um die Bereitstellung von Linux-Geräten im großen Stil für Azure loT Edge mit dem Gerätebereitstellungsdienst zu testen.
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a0b1a73c45c5c08eaf3c172900ade531c6fedda
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505940"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>Erstellen und Bereitstellen von loT Edge-Geräten in großem Umfang unter Linux mit symmetrischem Schlüssel

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

In diesem Artikel finden Sie eine umfassende Anleitung für die automatische Bereitstellung mit symmetrischen Schlüsseln von einem oder mehreren loT Edge-Geräte unter Linux. Sie können Azure loT Edge-Geräte mit dem [Azure loT Hub Gerätebereitstellungsdienst](../iot-dps/index.yml) (Device Provisioning Service, DPS) automatisch bereitstellen. Wenn Sie mit dem Prozess der automatischen Bereitstellung nicht vertraut sind, lesen Sie die [Übersicht zur Bereitstellung](../iot-dps/about-iot-dps.md#provisioning-process), bevor Sie den Vorgang fortsetzen.

Aufgaben:

1. Erstellen Sie entweder eine **Einzelregistrierung** für ein einzelnes Gerät oder eine **Gruppenregistrierung** für eine Gruppe von Geräten.
1. Installieren Sie die IoT Edge-Runtime, und verbinden Sie das Gerät mit dem IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Verwenden Sie für eine vereinfachte Benutzeroberfläche das [Azure IoT Edge-Konfigurationstool](https://github.com/azure/iot-edge-config). Dieses Befehlszeilentool, das sich zurzeit in der öffentlichen Vorschau befindet, installiert IoT Edge auf Ihrem Gerät und stellt es mithilfe von DPS und dem Nachweis des symmetrischen Schlüssels zur Verfügung.
:::moniker-end

Der Nachweis mit symmetrischen Schlüsseln ist eine einfache Methode zum Authentifizieren eines Geräts mit einer Instanz für den Gerätebereitstellungsdienst. Diese Nachweismethode stellt eine „Hallo Welt“-Umgebung für Entwickler bereit, die noch nicht mit der Gerätebereitstellung vertraut sind oder keine strengen Sicherheitsanforderungen haben. Die Gerätebestätigung bzw. der Nachweis mithilfe eines [TPM](../iot-dps/concepts-tpm-attestation.md) (Trusted Platform Module) oder von [X.509-Zertifikaten](../iot-dps/concepts-x509-attestation.md) ist sicherer und sollte verwendet werden, wenn striktere Sicherheitsanforderungen gelten.

## <a name="prerequisites"></a>Voraussetzungen

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge-Installation

Ein physisches oder virtuelles Gerät, das das IoT Edge-Gerät sein soll.

Sie müssen eine *eindeutige* **Registrierungs-ID** definieren, um jedes Gerät zu identifizieren. Sie können die MAC-Adresse, Seriennummer oder eindeutige Informationen vom Gerät verwenden. So könnten Sie beispielsweise mithilfe einer Kombination aus MAC-Adresse und Seriennummer diese Zeichenfolge für eine Registrierungs-ID bilden: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Gültige Zeichen sind alphanumerische Kleinbuchstaben und Bindestriche (`-`).

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Bereitstellen des Geräts mit seiner Cloud-Identität

Konfigurieren Sie Ihr Gerät mit den Informationen, die es zum Herstellen einer Verbindung mit dem Gerätebereitstellungsdienst und IoT Hub verwendet, sobald die Runtime auf Ihrem Gerät installiert ist.

Halten Sie die folgenden Informationen bereit:

* Den Wert für den DPS-**ID-Bereich**
* Die von Ihnen erstellte **Registrierungs-ID** für das Gerät
* Entweder der **Primärschlüssel** aus einer individuellen Registrierung oder ein [abgeleiteter Schlüssel](#derive-a-device-key) für Geräte, die eine Gruppenregistrierung verwenden.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Suchen Sie den Abschnitt zu Bereitstellungskonfigurationen für die Datei. Heben Sie die Auskommentierung der Zeilen für die Bereitstellung des symmetrischen DPS-Schlüssels auf, und vergewissern Sie sich, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   Der Zeile `provisioning:` sollte kein Leerzeichen vorangestellt und geschachtelte Elemente sollten um zwei Leerzeichen eingerückt sein.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Aktualisieren Sie die Werte `scope_id`, `registration_id`und `symmetric_key` mit Ihren DPS- und Geräteinformationen.

1. Verwenden Sie optional die Zeilen `always_reprovision_on_startup` oder `dynamic_reprovisioning`, um das Verhalten bei der erneuten Bereitstellung Ihres Geräts zu konfigurieren. Wenn für ein Gerät die erneute Bereitstellung beim Start festgelegt wurde, wird es immer zuerst versuchen, mit DPS bereitzustellen, und bei einem Fehler auf die Bereitstellungssicherung zurückgreifen. Wurde für ein Gerät festgelegt, dass es sich selbst dynamisch erneut bereitstellt, wird IoT Edge neu gestartet und erneut bereitgestellt, wenn ein erneutes Bereitstellungsereignis erkannt wird. Weitere Informationen finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](../iot-dps/concepts-device-reprovision.md).

1. Starten Sie die IoT Edge-Runtime neu, damit alle am Gerät vorgenommenen Konfigurationsänderungen erfasst werden.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Erstellen Sie eine Konfigurationsdatei für Ihr Gerät basierend auf einer Vorlagendatei, die im Rahmen der IoT Edge-Installation bereitgestellt wird.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Suchen Sie den Abschnitt **Provisioning** (Bereitstellung) der Datei. Heben Sie die Auskommentierung der Zeilen für die DPS-Bereitstellung mit symmetrischem Schlüssel auf, und vergewissern Sie sich, dass alle anderen Bereitstellungszeilen auskommentiert sind.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. Aktualisieren Sie die Werte `id_scope`, `registration_id`und `symmetric_key` mit Ihren DPS- und Geräteinformationen.

   Der symmetrische Schlüsselparameter kann einen Wert eines Inline-Schlüssels, eines Datei-URIs oder eines PKCS#11-URIs akzeptieren. Heben Sie die Auskommentierung nur einer symmetrischen Schlüsselzeile auf, basierend auf dem von Ihnen verwendeten Format.

   Wenn Sie PKCS#11-URIs verwenden, suchen Sie in der Konfigurationsdatei den Abschnitt **PKCS#11**, und stellen Sie Informationen zu Ihrer Konfiguration von PKCS#11 bereit.

1. Speichern und schließen Sie die Datei „config.toml“.

1. Wenden Sie die an IoT Edge vorgenommenen Konfigurationsänderungen an.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, können Sie zu Ihrem IoT Hub navigieren und mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät beginnen.

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

Sie können überprüfen, ob die individuelle Registrierung, die Sie im Gerätebereitstellungsdienst erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Instanz für den Gerätebereitstellungsdienst. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte individuelle Registrierung. Beachten Sie, dass der Status der Registrierung **Zugewiesen** lautet und die Geräte-ID aufgeführt ist.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

Sie können überprüfen, ob die Gruppenregistrierung, die Sie im Gerätebereitstellungsdienst erstellt haben, verwendet wurde. Navigieren Sie im Azure-Portal zu Ihrer Instanz für den Gerätebereitstellungsdienst. Öffnen Sie die Registrierungsdetails für die von Ihnen erstellte Gruppenregistrierung. Wechseln Sie zur Registerkarte **Registrierungsdatensätze**, um alle in dieser Gruppe registrierten Geräte anzuzeigen.

---

Mit den folgenden Befehlen können Sie auf Ihrem Gerät überprüfen, ob IoT Edge erfolgreich installiert und gestartet wurde.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Überprüfen Sie den Status des IoT Edge-Diensts.

```cmd/sh
systemctl status iotedge
```

Untersuchen Sie die Dienstprotokolle.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Führen Sie ausgeführte Module auf.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Überprüfen Sie den Status des IoT Edge-Diensts.

```cmd/sh
sudo iotedge system status
```

Untersuchen Sie die Dienstprotokolle.

```cmd/sh
sudo iotedge system logs
```

Führen Sie ausgeführte Module auf.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Gerätebereitstellungsdiensts ermöglicht es Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts festzulegen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md) oder [Verwenden der Azure CLI](how-to-deploy-cli-at-scale.md).
