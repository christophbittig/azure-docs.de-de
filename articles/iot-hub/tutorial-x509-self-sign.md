---
title: 'Tutorial: Verwenden von OpenSSL zum Erstellen selbstsignierter Zertifikate für Azure IoT Hub | Microsoft-Dokumentation'
description: 'Tutorial: Verwenden von OpenSSL zum Erstellen selbstsignierter X.509-Zertifikate für Azure IoT Hub'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: fe08b1dd87c754254067a9fc2ae4d022b9224d3d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551461"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Tutorial: Verwenden von OpenSSL zum Erstellen selbstsignierter Zertifikate

Sie können ein Gerät mit zwei selbstsignierten Gerätezertifikaten bei Ihrer IoT Hub-Instanz authentifizieren. Dies wird manchmal auch als Fingerabdruckauthentifizierung bezeichnet, da die Zertifikate Fingerabdrücke (Hashwerte) enthalten, die Sie an den IoT-Hub übermitteln. In den folgenden Schritten erfahren Sie, wie Sie zwei selbstsignierte Zertifikate erstellen. Dieser Zertifikatstyp wird hauptsächlich für Tests verwendet.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Schritt 1: Erstellen eines Schlüssels für das erste Zertifikat

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Schritt 2: Erstellen einer Zertifikatsignieranforderung für das erste Zertifikat

Geben Sie bei entsprechender Aufforderung die Geräte-ID an.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Schritt 3: Überprüfen der Zertifikatsignieranforderung

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Schritt 4: Selbstsignieren des ersten Zertifikats

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device1.crt
```

## <a name="step-5---create-a-key-for-the-second-certificate"></a>Schritt 5: Erstellen eines Schlüssels für das zweite Zertifikat

```bash
openssl genpkey -out device2.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-6---create-a-csr-for-the-second-certificate"></a>Schritt 6: Erstellen einer Zertifikatsignieranforderung für das zweite Zertifikat

Geben Sie bei entsprechender Aufforderung die gleiche Geräte-ID an wie beim ersten Zertifikat.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:
```

## <a name="step-7---self-sign-certificate-2"></a>Schritt 7: Selbstsignieren des zweiten Zertifikats

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-1"></a>Schritt 8: Abrufen des Fingerabdrucks für das erste Zertifikat

```bash
openssl x509 -in device1.crt -noout -fingerprint
```

## <a name="step-9---retrieve-the-thumbprint-for-certificate-2"></a>Schritt 9: Abrufen des Fingerabdrucks für das zweite Zertifikat

```bash
openssl x509 -in device2.crt -noout -fingerprint
```

## <a name="step-10---create-a-new-iot-device"></a>Schritt 10: Erstellen eines neuen IoT-Geräts

Navigieren Sie im Azure-Portal zu Ihrer IoT Hub-Instanz, und erstellen Sie eine neue IoT-Geräteidentität mit folgenden Merkmalen:

* Geben Sie die **Geräte-ID** an, die dem Antragstellernamen Ihrer beiden Zertifikate entspricht.
* Wählen Sie den Authentifizierungstyp **X.509, selbstsigniert** aus.
* Fügen Sie die Fingerabdrücke (hexadezimale Zeichenfolgen) ein, die Sie aus dem primären und sekundären Zertifikat Ihres Geräts kopiert haben. Stellen Sie sicher, dass die hexadezimalen Zeichenfolgen keine Doppelpunkte als Trennzeichen enthalten.


## <a name="next-steps"></a>Nächste Schritte

Wechseln Sie zum [Testen der Zertifikat Authentifizierung](tutorial-x509-test-certificate.md), um festzustellen, ob Ihr Gerät Ihr Gerät für Ihre IOT Hub authentifizieren kann. Für den Code auf dieser Seite müssen Sie ein PFX-Zertifikat verwenden. Verwenden Sie den folgenden OpenSSL-Befehl, um das CRT-Zertifikat Ihres Geräts in das PFX-Format zu konvertieren.

```bash
openssl pkcs12 -export -in device.crt -inkey device.key -out device.pfx
```
