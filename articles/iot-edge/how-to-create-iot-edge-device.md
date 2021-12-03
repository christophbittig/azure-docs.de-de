---
title: Erstellen eines IoT Edge-Geräts – Azure IoT Edge | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Plattform- und Bereitstellungsoptionen zum Erstellen eines IoT Edge-Geräts.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: kgremban
ms.openlocfilehash: 6fdfbd937e767cde118ed80476e73d6207c657e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495351"
---
# <a name="create-an-iot-edge-device"></a>Erstellen eines IoT Edge-Geräts

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Dieser Artikel bietet eine Übersicht über die verfügbaren Optionen zum Installieren und Bereitstellen von IoT Edge auf Ihren Geräten.

Dieser Artikel bietet einen Einblick in alle Optionen für Ihre IoT Edge-Lösung und hilft Ihnen bei folgenden Aufgaben:

* [Auswählen einer Plattform](#choose-a-platform)
* [Auswählen der Bereitstellung von Geräten](#choose-how-to-provision-your-devices)
* [Auswählen einer Authentifizierungsmethode](#choose-an-authentication-method)

Am Ende dieses Artikels werden Sie ein klares Bild davon haben, welche Plattform-, Bereitstellungs- und Authentifizierungsoptionen Ihnen für Ihre IoT Edge-Lösung zur Verfügung stehen.

## <a name="get-started"></a>Erste Schritte

Wenn Sie wissen, welche Art von Plattform-, Bereitstellungs- und Authentifizierungsoptionen Sie für die Erstellung eines IoT Edge-Geräts wünschen, können Sie über die Links in der folgenden Tabelle loslegen.

Wenn Sie mehr Informationen darüber möchten, wie Sie sich für die richtige Option entscheiden, lesen Sie diesen Artikel weiter, um mehr zu erfahren.

<!--1.1-->
:::moniker range="iotedge-2018-06"

|    | Linux-Container auf Linux-Hosts | Linux-Container auf Windows-Hosts | Windows-Container auf Windows-Hosts |
|--| ----- | ---------------- | ------- |
| **Manuelle Bereitstellung (Einzelgerät)** | [X.509-Zertifikate](how-to-provision-single-device-linux-x509.md)<br><br>[Symmetrische Schlüssel](how-to-provision-single-device-linux-symmetric.md) | [X.509-Zertifikate](how-to-provision-single-device-linux-on-windows-x509.md)<br><br>[Symmetrische Schlüssel](how-to-provision-single-device-linux-on-windows-symmetric.md) | [X.509-Zertifikate](how-to-provision-single-device-windows-x509.md)<br><br>[Symmetrische Schlüssel](how-to-provision-single-device-windows-symmetric.md) |
| **Automatische Bereitstellung (Geräte im gewünschten Umfang)** | [X.509-Zertifikate](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[Symmetrische Schlüssel](how-to-provision-devices-at-scale-linux-symmetric.md) | [X.509-Zertifikate](how-to-provision-devices-at-scale-linux-on-windows-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)<br><br>[Symmetrische Schlüssel](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md) | [X.509-Zertifikate](how-to-provision-devices-at-scale-windows-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-windows-tpm.md)<br><br>[Symmetrische Schlüssel](how-to-provision-devices-at-scale-windows-symmetric.md) |

:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Die folgende Tabelle enthält die unterstützten Szenarien für die IoT Edge-Version 1.2. Inhalte zu Windows-Geräten enthält die Version dieses Artikels zu [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true).

|    | Linux-Container auf Linux-Hosts |
|--| ----- |
| **Manuelle Bereitstellung (Einzelgerät)** | [X.509-Zertifikate](how-to-provision-single-device-linux-x509.md)<br><br>[Symmetrische Schlüssel](how-to-provision-single-device-linux-symmetric.md) |
| **Automatische Bereitstellung (Geräte im gewünschten Umfang)** | [X.509-Zertifikate](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[Symmetrische Schlüssel](how-to-provision-devices-at-scale-linux-symmetric.md) |

:::moniker-end

## <a name="terms-and-concepts"></a>Begriffe und Konzepte

Falls Sie mit der IoT-Edge-Terminologie noch nicht vertraut sind, hier einige wichtige Begriffe:

**IoT Edge-Runtime**: Die [IoT Edge-Runtime](iot-edge-runtime.md) ist eine Sammlung von Programmen, durch die ein Gerät zum IoT Edge-Gerät wird. In ihrer Gesamtheit ermöglichen die IoT Edge-Runtimekomponenten IoT Edge-Geräten das Ausführen Ihrer IoT Edge-Module.

**Bereitstellung**: Jedes IoT Edge-Gerät muss bereitgestellt werden. Die Bereitstellung erfolgt in zwei Schritten. Der erste Schritt ist die Registrierung des Geräts in einem IoT-Hub, der eine Cloudidentität erstellt, mit der das Gerät die Verbindung mit seinem Hub herstellt. Der zweite Schritt besteht im Konfigurieren des Geräts mit seiner Cloudidentität. Die Bereitstellung kann manuell auf Einzelgerätebasis oder mit dem [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) in großem Umfang erfolgen.

**Authentifizierung**: Ihre IoT Edge-Geräte müssen ihre Identität bestätigen, wenn sie eine Verbindung mit IoT Hub herstellen. Sie können die gewünschte Authentifizierungsmethode wählen, z. B. Kennwörter mit symmetrischem Schlüssel, Zertifikatfingerabdrücke oder TPMs (Trusted Platform Modules).

## <a name="choose-a-platform"></a>Auswählen einer Plattform

Container- und Hostbetriebssystem verweisen auf die Plattformoptionen. Das Containerbetriebssystem ist das Betriebssystem, das innerhalb Ihrer IoT Edge-Runtime- und Modulcontainer verwendet wird. Das Hostbetriebssystem ist das Betriebssystem des Geräts, auf dem die IoT Edge-Runtimecontainer und -module ausgeführt werden.

Für Ihre IoT Edge-Geräte gibt es drei Plattformoptionen.

* **Linux-Container auf Linux-Hosts**: Linux-basierte IoT Edge-Container werden direkt auf einem Linux-Host ausgeführt. In der gesamten IoT Edge-Dokumentation wird diese Option der Einfachheit halber auch als **Linux** und **Linux-Container** bezeichnet.

* **Linux-Container auf Windows-Hosts**: Linux-basierte IoT-Edge-Container in einer Linux-VM werden auf einem Windows-Host ausgeführt. In der gesamten IoT Edge-Dokumentation wird diese Option auch als **Linux unter Windows**, **IoT Edge für Linux unter Windows** und **EFLOW** bezeichnet.

* **Windows-Container auf Windows-Hosts**: Windows-basierte IoT Edge-Container werden direkt auf einem Windows-Host ausgeführt. In der gesamten IoT Edge-Dokumentation wird diese Option der Einfachheit halber auch als **Windows** und **Windows-Container** bezeichnet.

Neueste Informationen dazu, welche Betriebssysteme zurzeit für Produktionsszenarien unterstützt werden, finden Sie unter [Von Azure IoT Edge unterstützte Systeme](support.md#operating-systems).

### <a name="linux-containers-on-linux"></a>Linux-Container unter Linux

Bei Linux-Geräten wird die IoT Edge-Runtime direkt auf dem Hostgerät installiert.

IoT Edge unterstützt X64-, ARM32- und ARM64-Linux-Geräte. Microsoft bietet Installationspakete für die Betriebssysteme Ubuntu Server 18.04 und Raspberry Pi OS Stretch.

Die Unterstützung für ARM64-Geräte befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="linux-containers-on-windows"></a>Linux-Container unter Windows

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge für Linux unter Windows hostet auf Ihrem Window-Gerät einen virtuellen Linux-Computer. Der virtuelle Computer wird mit der IoT Edge-Runtime vorinstalliert. Updates werden über Microsoft Update verwaltet.

Für die Ausführung von IoT Edge auf Windows-Geräten wird die Verwendung von IoT Edge für Linux unter Windows empfohlen. Weitere Informationen finden Sie unter [Was ist Azure IoT Edge für Linux unter Windows?](iot-edge-for-linux-on-windows.md).

Derzeit unterstützt IoT Edge für Linux unter Windows die Version 1.2 von Azure IoT Edge nicht.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Derzeit unterstützt die IoT Edge-Version 1.2 nicht IoT Edge für Linux unter Windows. Weitere Informationen zu IoT Edge für Linux unter Windows finden Sie in der Version dieses Artikels zu [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true).
:::moniker-end

### <a name="windows-containers-on-windows"></a>Windows-Container unter Windows

<!--1.1-->
:::moniker range="iotedge-2018-06"
Bei Windows-Geräten wird die IoT Edge-Runtime direkt auf dem Hostgerät installiert. Diese Plattform ermöglicht Ihnen, Ihre IoT-Edge-Module als Windows-Container zu erstellen, bereitzustellen und auszuführen.

   > [!NOTE]
   > Windows-Container werden für die Ausführung von IoT Edge auf Windows-Geräten nicht empfohlen, da sie nicht über Version 1.1 von Azure IoT Edge hinaus unterstützt werden.
   >
   > Erwägen Sie IoT Edge für Linux unter Windows, das in künftigen Versionen unterstützt wird.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Die IoT Edge-Version 1.2 unterstützt keine Windows-Container. Windows-Container werden nicht über Version 1.1 hinaus unterstützt. Weitere Informationen zu IoT Edge mit Windows-Containern finden Sie in der Version dieses Artikels zu [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true).
:::moniker-end

## <a name="choose-how-to-provision-your-devices"></a>Auswählen der Bereitstellung von Geräten

Sie können je nach Anforderungen Ihrer IoT-Edge-Lösung ein einzelnes Gerät oder mehrere Geräte im gewünschten Umfang bereitstellen.

Die zur Authentifizierung der Kommunikation zwischen Ihren IoT Edge-Geräten und IoT-Hubs verfügbaren Optionen hängen von der gewählten Bereitstellungsmethode ab. Weitere Informationen zu diesen Optionen finden Sie im Abschnitt [Auswählen einer Authentifizierungsmethode](#choose-an-authentication-method).

### <a name="single-device"></a>Einzelgerät

Die Bereitstellung eines Einzelgeräts entspricht der Bereitstellung eines IoT-Edgegeräts ohne Unterstützung durch den [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) (DPS). Die Bereitstellung von Einzelgeräten wird auch als **manuelle Bereitstellung** bezeichnet.

Bei der Bereitstellung von Einzelgeräten müssen Sie die Bereitstellungsinformationen, z. B. eine Verbindungszeichenfolge, manuell auf Ihren Geräten eingeben. Die manuelle Bereitstellung lässt sich schnell und einfach für nur wenige Geräte einrichten, wobei jedoch der Aufwand mit der Anzahl der Geräte steigt. Beachten Sie dies, wenn Sie die Skalierbarkeit Ihrer Lösung in Betracht ziehen.

Die Authentifizierungsmethoden **Symmetrischer Schlüssel** und **X.509, selbstsigniert** sind für die manuelle Bereitstellung verfügbar. Weitere Informationen zu diesen Optionen finden Sie im Abschnitt [Auswählen einer Authentifizierungsmethode](#choose-an-authentication-method).

### <a name="devices-at-scale"></a>Mehrere Geräte im gewünschten Umfang

Die Gerätebereitstellung im gewünschten Umfang entspricht der Bereitstellung eines oder mehrerer IoT Edge-Geräte mithilfe des [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) (DPS). Die Bereitstellung im gewünschten Umfang wird auch als **automatische Bereitstellung** bezeichnet.

Wenn Ihre IoT Edge-Lösung mehrere Geräte benötigt, erspart Ihnen die automatische Bereitstellung mit DPS die manuelle Eingabe von Bereitstellungsinformationen in die Konfigurationsdateien der einzelnen gewünschten Geräte. Dieses automatisierte Modell erlaubt eine Skalierung auf Millionen von IoT Edge-Geräten. Sie können den automatisierten Bereitstellungsablauf im Abschnitt [„Abläufe im Hintergrund“ des Artikels „Was ist Azure IoT Hub Device Provisioning Service?“](../iot-dps/about-iot-dps.md#behind-the-scenes) verfolgen.

Sie können Ihre IoT Edge-Lösung mit der Authentifizierungsmethode Ihrer Wahl schützen. Die Authentifizierungsmethoden **Symmetrischer Schlüssel**, **X.509-Zertifikate** und **TPM-Nachweis (Trusted Platform Module)** sind für die Bereitstellung von Geräten im gewünschten Umfang verfügbar. Weitere Informationen zu diesen Optionen finden Sie im Abschnitt [Auswählen einer Authentifizierungsmethode](#choose-an-authentication-method).

Weitere Informationen zu den Features von DPS finden Sie im [Abschnitt „Features“ der Übersichtsseite](../iot-dps/about-iot-dps.md#features-of-the-device-provisioning-service).

## <a name="choose-an-authentication-method"></a>Auswählen einer Authentifizierungsmethode

### <a name="symmetric-keys-attestation"></a>Nachweis des symmetrischen Schlüssels

Der Nachweis des symmetrischen Schlüssels ist eine einfache Methode zum Authentifizieren eines Geräts. Diese Nachweismethode stellt eine „Hallo Welt“-Umgebung für Entwickler bereit, die noch nicht mit der Gerätebereitstellung vertraut sind oder keine strengen Sicherheitsanforderungen haben.

Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, erstellt der Dienst zwei Schlüssel. Ein Schlüssel befindet sich auf dem Gerät, den es bei der Authentifizierung in IoT Hub vorzeigt.

Diese Authentifizierungsmethode lässt sich zwar schneller einrichten, ist aber nicht so sicher. Die Gerätebereitstellung mit TPM oder X.509-Zertifikaten ist sicherer und sollte für Lösungen mit strengeren Sicherheitsanforderungen gewählt werden.

### <a name="x509-certificate-attestation"></a>X.509-Zertifikatnachweis

Die Verwendung von X.509-Zertifikaten als Nachweismechanismus ist eine exzellente Möglichkeit, die Produktion zu skalieren und die Gerätebereitstellung zu vereinfachen. X.509-Zertifikate werden normalerweise in einer Zertifikatvertrauenskette angeordnet. Beginnend mit einem selbst signierten oder vertrauenswürdigen Stammzertifikat signiert jedes Zertifikat in der Kette das nächstniedrigere Zertifikat. Dieses Muster erstellt eine delegierte Vertrauenskette vom Stammzertifikat bis zu jedem Zwischenzertifikat und dem endgültigen „Blattzertifikat“, das auf einem Gerät installiert ist.

Sie erstellen zwei X. 509-Identitätszertifikate und bewahren diese auf dem Gerät auf. Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, geben Sie Fingerabdrücke aus beiden Zertifikaten an. Wenn sich das Gerät bei IoT Hub authentifiziert, übergibt es ein Zertifikat, und IoT Hub überprüft, ob das Zertifikat mit dem Fingerabdruck übereinstimmt.

Diese Authentifizierungsmethode ist sicherer als symmetrische Schlüssel und wird in Produktionsszenarien empfohlen.

### <a name="trusted-platform-module-tpm-attestation"></a>TPM-Nachweis (Trusted Platform Module)

Der TPM-Nachweis ist die sicherste Methode der Gerätebereitstellung, da sie Authentifizierungsfunktionen sowohl in der Software als auch der Hardware bietet. Jeder TPM-Chip verwendet einen eindeutigen Endorsement Key, um seine Authentizität zu bestätigen.

Der TPM-Nachweis ist nur für die Bereitstellung in großem Umfang mit DPS verfügbar und unterstützt nur individuelle und keine Gruppenregistrierungen. Gruppenregistrierungen sind aufgrund der gerätebezogenen Natur von TPM nicht möglich.

TPM 2.0 ist erforderlich, wenn Sie den TPM-Nachweis beim Device Provisioning Service verwenden.

Diese Authentifizierungsmethode ist sicherer als symmetrische Schlüssel und wird in Produktionsszenarien empfohlen.

## <a name="next-steps"></a>Nächste Schritte

Anhand des Inhaltsverzeichnisses können Sie zum entsprechenden Gesamtleitfaden für die Erstellung eines IoT Edge-Geräts für die Plattform-, Bereitstellungs- und Authentifizierungsanforderungen Ihrer IoT Edge-Lösung navigieren.

Sie können auch über die unten aufgeführten Links zu den entsprechenden Artikeln gelangen.

### <a name="linux-containers-on-linux-hosts"></a>Linux-Container auf Linux-Hosts

**Manuelles Bereitstellen eines einzelnen Geräts**:

* [Bereitstellen eines einzelnen Linux-Geräts mit X.509-Zertifikaten](how-to-provision-single-device-linux-x509.md)
* [Bereitstellen eines einzelnen Linux-Geräts mit symmetrischen Schlüsseln](how-to-provision-single-device-linux-symmetric.md)

**Bereitstellen mehrerer Geräte im gewünschten Umfang**:

* [Bereitstellen von Linux-Geräten im gewünschten Umfang mit X.509-Zertifikaten](how-to-provision-devices-at-scale-linux-x509.md)
* [Bereitstellen von Linux-Geräten im gewünschten Umfang mithilfe des TPM-Nachweises](how-to-provision-devices-at-scale-linux-tpm.md)
* [Bereitstellen von Linux-Geräten im gewünschten Umfang mit symmetrischen Schlüsseln](how-to-provision-devices-at-scale-linux-symmetric.md)

<!--1.1-->
:::moniker range="iotedge-2018-06"
### <a name="linux-containers-on-windows-hosts"></a>Linux-Container auf Windows-Hosts

**Manuelles Bereitstellen eines einzelnen Geräts**:

* [Bereitstellen eines einzelnen Linux-Geräts unter Windows mit X.509-Zertifikaten](how-to-provision-single-device-linux-on-windows-x509.md)
* [Bereitstellen eines einzelnen Linux-Geräts unter Windows mit symmetrischen Schlüsseln](how-to-provision-single-device-linux-on-windows-symmetric.md)

**Bereitstellen mehrerer Geräte im gewünschten Umfang**:

* [Bereitstellen von Linux-Geräten unter Windows im gewünschten Umfang mit X.509-Zertifikaten](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
* [Bereitstellen von Linux-Geräten unter Windows im gewünschten Umfang mithilfe des TPM-Nachweises](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)
* [Bereitstellen von Linux-Geräten unter Windows im gewünschten Umfang mit symmetrischen Schlüsseln](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

### <a name="windows-containers-on-windows-hosts"></a>Windows-Container auf Windows-Hosts

**Manuelles Bereitstellen eines einzelnen Geräts**:

* [Bereitstellen eines einzelnen Windows-Geräts mit X.509-Zertifikaten](how-to-provision-single-device-windows-x509.md)
* [Bereitstellen eines einzelnen Windows-Geräts mit symmetrischen Schlüsseln](how-to-provision-single-device-windows-symmetric.md)

**Bereitstellen mehrerer Geräte im gewünschten Umfang**:

* [Bereitstellen von Windows-Geräten im gewünschten Umfang mit X.509-Zertifikaten](how-to-provision-devices-at-scale-windows-x509.md)
* [Bereitstellen von Windows-Geräten im gewünschten Umfang mithilfe des TPM-Nachweises](how-to-provision-devices-at-scale-windows-tpm.md)
* [Bereitstellen von Windows-Geräten im gewünschten Umfang mit symmetrischen Schlüsseln](how-to-provision-devices-at-scale-windows-symmetric.md)
:::moniker-end
