---
title: 'Tutorial: Grundlegendes zu X.509-Zertifikaten für öffentliche Schlüssel für Azure IoT Hub | Microsoft-Dokumentation'
description: 'Tutorial: Grundlegendes zu X.509-Zertifikaten für öffentliche Schlüssel für Azure IoT Hub'
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
ms.openlocfilehash: 718ed8c89fbd5bccaff94b41e80b9b462faba1ee
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555658"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Tutorial: Grundlegendes zu X. 509-Zertifikaten für öffentliche Schlüssel

X. 509-Zertifikate sind digitale Dokumente, die einen Benutzer, einen Computer, einen Dienst oder ein Gerät darstellen. Sie werden von einer Zertifizierungsstelle (Certification Authority, CA), einer untergeordneten Zertifizierungsstelle oder von einer Registrierungsstelle ausgestellt und enthalten den öffentlichen Schlüssel des Zertifikatantragstellers. Der private Schlüssel des Antragstellers, der sicher gespeichert werden muss, ist nicht enthalten. Zertifikate für öffentliche Schlüssel werden durch [RFC 5280](https://tools.ietf.org/html/rfc5280) dokumentiert. Sie sind digital signiert und enthalten im Allgemeinen die folgenden Informationen:

* Informationen zum Zertifikatantragsteller
* Der öffentliche Schlüssel, der dem privaten Schlüssel des Antragstellers entspricht
* Informationen über die ausstellende Zertifizierungsstelle
* Die unterstützten Verschlüsselungs- und/oder digitalen Signaturalgorithmen
* Informationen zum Bestimmen des Sperr- und Gültigkeitsstatus des Zertifikats

## <a name="certificate-fields"></a>Zertifikatfelder

Im Laufe der Zeit hat es drei Zertifikatversionen gegeben. Jede Version fügt der vorherigen Felder hinzu. Version 3 ist die aktuelle, und sie enthält zusätzlich zu den Feldern der Versionen 1 und 2 die Felder der Version 3. In Version 1 wurden die folgenden Felder definiert:

* **Version**: ein Wert (1, 2 oder 3), der die Versionsnummer des Zertifikats angibt
* **Seriennummer**: eine eindeutige Zahl für jedes Zertifikat, das von einer Zertifizierungsstelle ausgestellt wurde
* **Signaturalgorithmus der Zertifizierungsstelle**: der Name des Algorithmus, den die Zertifizierungsstelle zum Signieren der Zertifikatinhalte verwendet
* **Name des Ausstellers**: der Distinguished Name (DN) der Zertifizierungsstelle, die das Zertifikat ausgestellt hat
* **Gültigkeitsdauer**: der Zeitraum, in dem das Zertifikat als gültig gilt
* **Name des Antragstellers**: Name der durch das Zertifikat dargestellten Entität
* **Informationen zum öffentlichen Schlüssel des Antragstellers**: öffentlicher Schlüssel im Besitz des Zertifikatantragstellers

In Version 2 wurden die folgenden Felder mit Informationen über den Zertifikataussteller hinzugefügt. Diese Felder werden jedoch selten verwendet

* **Eindeutige ID des Zertifikatausstellers**: ein eindeutiger Bezeichner für die ausstellende Zertifizierungsstelle gemäß Definition durch sie selbst
* **Eindeutige ID des Antragstellers**: ein eindeutiger Bezeichner für den Zertifikatantragsteller gemäß Definition durch die ausstellende Zertifizierungsstelle

Für Zertifikate der Version 3 wurden die folgenden Erweiterungen hinzugefügt:

* **Schlüsselbezeichner der Zertifizierungsstelle**: dieser kann einen der beiden folgenden Werte annehmen:
  * Der Antragsteller der Zertifizierungsstelle, die dieses Zertifikat ausgestellt hat, und die Seriennummer des Zertifikats
  * Ein Hash des öffentlichen Schlüssels der Zertifizierungsstelle, die dieses Zertifikat ausgestellt hat
* **Schlüsselbezeichner des Antragstellers**: Hash des öffentlichen Schlüssels des aktuellen Zertifikats
* **Schlüsselverwendung**: definiert den Dienst, für den ein Zertifikat verwendet werden kann. Dies kann einen der folgenden Werte annehmen:
  * **Digitale Signatur**
  * **Unleugbarkeit**
  * **Schlüsselverschlüsselung**
  * **Datenchiffrierung**
  * **Schlüsselübereinstimmung**
  * **Schlüsselzertifikatsignatur**
  * **Zertifikatsperrlistensignatur**
  * **Nur verschlüsseln**
  * **Nur entschlüsseln**
* **Verwendungszeitraum des privaten Schlüssels**: Gültigkeitsdauer für den privaten Teil eines Schlüsselpaars
* **Zertifikatrichtlinien**: Richtlinien zum Überprüfen des Zertifikatantragstellers
* **Richtlinienzuordnungen**: ordnet eine Richtlinie in einer Organisation zur Richtlinie in einer anderen Organisation zu
* **Alternativer Antragstellername**: Liste der alternativen Namen für den Antragsteller
* **Alternativer Ausstellername**: Liste der alternativen Namen für die ausstellende Zertifizierungsstelle
* **Dir-Attribut des Antragstellers**: Attribute aus einem X.500- oder LDAP-Verzeichnis
* **Grundlegende Einschränkungen**: Hiermit kann das Zertifikat festlegen, ob es für eine Zertifizierungsstelle oder für einen Benutzer, einen Computer, ein Gerät oder einen Dienst ausgestellt wird. Diese Erweiterung umfasst auch eine Einschränkung der Pfadlänge, durch die die Anzahl der untergeordneten Zertifizierungsstellen beschränkt wird, die vorhanden sein können.
* **Namenseinschränkungen**: Hiermit wird festgelegt, welche Namespaces in einem von einer Zertifizierungsstelle ausgestellten Zertifikat zulässig sind
* **Richtlinieneinschränkungen**: können verwendet werden, um Richtlinienzuordnungen zwischen Zertifizierungsstellen zu unterbinden
* **Erweiterte Schlüsselverwendung**: gibt an, wie der öffentliche Schlüssel eines Zertifikats über die in der Erweiterung **Schlüsselverwendung** angegebenen Zwecke hinaus verwendet werden kann
* **CRL-Verteilungspunkte**: enthält mindestens eine URL, in der die Zertifikatsperrliste (CRL) für das Basiszertifikat veröffentlicht wird
* **Unterbinden von anyPolicy**: unterbindet die Verwendung der OID **Alle Ausstellungsrichtlinien** (2.5.29.32.0) in untergeordneten ZS-Zertifikaten
* **Aktuelle CRL**: enthält mindestens eine URL, in der die Deltasperrliste der ausstellenden Zertifizierungsstelle veröffentlicht wird
* **Zugriff auf ZS-Informationen**: enthält mindestens eine URL, in der das Zertifikat der ausstellenden Zertifizierungsstelle veröffentlicht wird
* **Zugriff auf Antragstellerinformationen**: enthält Informationen darüber, wie weitere Details über einen Zertifikatantragsteller abgerufen werden können

## <a name="certificate-formats"></a>Zertifikatformate

Zertifikate können in einer Vielzahl von Formaten gespeichert werden. Azure IoT Hub-Authentifizierung verwendet in der Regel das PEM- und PFX-Format,

### <a name="binary-certificate"></a>Binäres Zertifikat

Dies enthält ein Binärzertifikat mit Rohdaten in DER ASN.1-Codierung.

### <a name="ascii-pem-format"></a>ASCII-PEM-Format

Ein PEM-Zertifikat (Erweiterung PEM) enthält ein in Base64 codiertes Zertifikat, das mit -----BEGIN CERTIFICATE----- beginnt und mit -----END CERTIFICATE----- endet. Das PEM-Format wird sehr weit verbreitet wird von IoT Hub beim Hochladen bestimmter Zertifikate benötigt.

### <a name="ascii-pem-key"></a>ASCII (PEM)-Schlüssel

Enthält einen in Base64 codierten DER-Schlüssel möglicherweise mit zusätzlichen Metadaten über den Algorithmus, die für den Kennwortschutz verwendet werden.

### <a name="pkcs7-certificate"></a>PKCS #7-Zertifikat

Ein Format, das für den Transport von signierten oder verschlüsselten Daten konzipiert ist. Es wird durch [RFC 2315](https://tools.ietf.org/html/rfc2315) definiert. Es kann die gesamte Zertifikatskette enthalten.

### <a name="pkcs8-key"></a>PKCS # 8-Schlüssel

Das Format für einen privaten Schlüsselspeicher, der durch [RFC 5208](https://tools.ietf.org/html/rfc5208) definiert wird.

### <a name="pkcs12-key-and-certificate"></a>PKCS # 12-Schlüssel und -Zertifikat

Ein komplexes Format, das einen Schlüssel und die gesamte Zertifikatskette speichern und schützen kann. Es wird häufig mit der Erweiterung PFX verwendet. PKCS # 12 ist ein Synonym für das PFX-Format.

## <a name="for-more-information"></a>Weitere Informationen finden Sie unter

Weitere Informationen finden Sie unter den folgenden Themen:

* [Leitfaden zur Fachsprache für X. 509-Zertifikate für Laien](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Konzeptgrundlagen der X.509-Zertifizierungsstellenzertifikate in der IoT-Branche](./iot-hub-x509ca-concept.md)

## <a name="next-steps"></a>Nächste Schritte

Für den Fall, dass Sie Testzertifikate generieren möchten, mit denen Sie Geräte für Ihren IoT Hub authentifizieren können, finden Sie weitere Informationen in den folgenden Themen:

* [Verwenden von Microsoft-Skripts zum Erstellen von Testzertifikaten](tutorial-x509-scripts.md)
* [Verwenden von OpenSSL zum Erstellen von Testzertifikaten](tutorial-x509-openssl.md)
* [Verwenden von OpenSSL zum Erstellen von selbstsignierten Testzertifikaten](tutorial-x509-self-sign.md)

Falls Sie über ein Zertifikat einer Zertifizierungsstelle (ZS) bzw. einer untergeordneten Zertifizierungsstelle verfügen und es auf Ihren IoT-Hub hochladen und beweisen möchten, dass es sich in Ihrem Besitz befindet, helfen Ihnen die Informationen unter [Nachweisen des Besitzes eines Zertifizierungsstellenzertifikats](tutorial-x509-prove-possession.md) weiter.