---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 374fcb6470ab22bfd3531d9ef543bf3cc06e17ac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576786"
---
## <a name="generate-device-identity-certificates"></a>Generieren von Geräteidentitätszertifikaten

Die manuelle Bereitstellung mit X.509-Zertifikaten erfordert die IoT Edge-Version 1.0.10 oder höher.

Wenn Sie ein IoT Edge-Gerät mit X.509-Zertifikaten bereitstellen, verwenden Sie das sogenannte *Geräteidentitätszertifikat*. Dieses Zertifikat wird nur für die Bereitstellung eines IoT Edge-Geräts und die Authentifizierung des Geräts bei Azure IoT Hub verwendet. Es handelt sich dabei um ein Blattzertifikat, mit dem keine anderen Zertifikate signiert werden können. Das Geräteidentitätszertifikat hängt nicht mit den Zertifikaten der Zertifizierungsstelle zusammen, die das IoT Edge-Gerät Modulen oder Downstreamgeräten zur Überprüfung präsentiert.

Bei der Authentifizierung mit einem X.509-Zertifikat werden die Informationen zur Authentifizierung der einzelnen Geräte in Form eines *Fingerabdrucks* bereitgestellt, der den Geräteidentitätszertifikaten entnommen wurde. Diese Fingerabdrücke werden IoT Hub zum Zeitpunkt der Geräteregistrierung bereitgestellt, damit der Dienst das Gerät erkennen kann, wenn es eine Verbindung herstellt.

Weitere Informationen zur Aufgabe der verschiedenen Zertifikate der Zertifizierungsstelle auf IoT Edge-Geräten finden Sie unter [Grundlegendes zur Verwendung von Zertifikaten durch Azure IoT Edge](../articles/iot-edge/iot-edge-certs.md).

Für die manuelle Bereitstellung mit X.509 ist Folgendes erforderlich:

* Zwei Geräteidentitätszertifikate mit ihren entsprechenden Zertifikaten mit privaten Schlüsseln im CER- oder PEM-Format.

  Eine Gruppe von Zertifikat-/Schlüsseldateien wird für die IoT Edge-Runtime bereitgestellt. Wenn Sie Geräteidentitätszertifikate erstellen, legen Sie den allgemeinen Namen (Common Name, CN) des Zertifikats auf die Geräte-ID fest, die das Gerät in Ihrem IoT-Hub haben soll.

* Fingerabdrücke von beiden Geräteidentitätszertifikaten.

  Die Fingerabdruckwerte bestehen aus 40 Hexadezimalzeichen für SHA-1-Hashes oder 64 Hexadezimalzeichen für SHA-256-Hashes. Beide Fingerabdrücke werden zum Zeitpunkt der Geräteregistrierung für IoT Hub bereitgestellt.

Sollten Sie über keine Zertifikate verfügen, können Sie [Demozertifikate zum Testen von IoT Edge-Gerätefeatures erstellen](../articles/iot-edge/how-to-create-test-certificates.md). Gehen Sie wie in dem Artikel beschrieben vor, um Zertifikaterstellungsskripts einzurichten und ein Zertifikat der Stammzertifizierungsstelle sowie zwei IoT Edge-Geräteidentitätszertifikate zu erstellen.

Der Fingerabdruck kann beispielsweise mithilfe des folgenden OpenSSL-Befehls aus einem Zertifikat abgerufen werden:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```
