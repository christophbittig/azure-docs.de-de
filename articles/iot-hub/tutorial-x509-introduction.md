---
title: 'Tutorial: Grundlegendes zur Kryptografie und zu X.509-Zertifikaten für Azure IoT Hub | Microsoft-Dokumentation'
description: 'Tutorial: Grundlegendes zur Kryptografie und X.509-Public Key-Infrastruktur für Azure IoT Hub'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: e79d367cf93354506198d2d317c759b714c55d7f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555696"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Tutorial: Grundlegendes zur Public Key-Kryptografie und X.509-Public Key-Infrastruktur

Sie können X.509-Zertifikate verwenden, um Geräte für eine Azure IoT Hub-Instanz zu authentifizieren. Ein Zertifikat ist ein digitales Dokument, das den öffentlichen Schlüssel (Public Key) des Geräts enthält und mit dem die Identität des Geräts verifiziert werden kann. X.509-Zertifikate und -Zertifikatssperrlisten (Certificate Revocation Lists, CRLs) sind unter [RFC 5280](https://tools.ietf.org/html/rfc5280) dokumentiert. Zertifikate stellen aber nur einen Teil einer X.509-Public Key-Infrastruktur (PKI) dar. Um diese Art von Infrastruktur verstehen zu können, müssen Sie sich mit kryptografischen Algorithmen, kryptografischen Schlüsseln, Zertifikaten und Zertifizierungsstellen (Certification Authorities, CAs) vertraut machen:

* Mit **Algorithmen** wird definiert, wie die ursprünglichen Klartextdaten in Chiffretext und anschließend wieder in Klartext umgewandelt werden.
* **Schlüssel** sind zufällige oder pseudozufällige Datenzeichenfolgen, die als Eingabe für einen Algorithmus verwendet werden.
* **Zertifikate** sind digitale Dokumente, die den öffentlichen Schlüssel einer Entität enthalten. Hiermit können Sie ermitteln, ob es sich beim Antragsteller des Zertifikats wirklich um die vorgegebene Entität handelt.
* **Zertifizierungsstellen** dienen zum Beweisen der Authentizität von Zertifikatantragstellern.

Sie können ein Zertifikat bei einer Zertifizierungsstelle (Certification Authority, CA) erwerben. Darüber hinaus haben Sie die Möglichkeit, eine selbstsignierte Stammzertifizierungsstelle zu erstellen, wenn Sie Tests oder Entwicklungsaufgaben durchführen möchten oder in einer eigenständigen Umgebung arbeiten. Wenn Sie beispielsweise ein oder mehrere Geräte besitzen und die Authentifizierung von IoT-Hubs testen, können Sie Ihre Stammzertifizierungsstelle selbst signieren und zum Ausstellen von Gerätezertifikaten verwenden. Sie können auch selbstsignierte Gerätezertifikate ausstellen. Hierauf wird in den nachfolgenden Artikeln ausführlicher eingegangen.

Bevor die X.509-Zertifikate genauer beschrieben und zur Authentifizierung von Geräten für einen IoT-Hub genutzt werden, soll der zugrunde liegende kryptografische Ansatz der Zertifikate erläutert werden.

## <a name="cryptography"></a>Kryptografie

Kryptografie dient als Schutz für Informationen und die Kommunikation. Hierfür werden normalerweise kryptografische Verfahren verwendet, bei denen Klartext (regulärer Text) in Chiffretext (codierter Text) und anschließend wieder in Klartext umgewandelt wird. Dieser Umwandlungsprozess wird als „Verschlüsselung“ bezeichnet. Die Rückumwandlung wird als „Entschlüsselung“ bezeichnet. Mithilfe von Kryptografie soll Folgendes erreicht werden:

* **Vertraulichkeit**: Die Informationen können nur von der beabsichtigten Zielgruppe gelesen werden.
* **Integrität**: Die Informationen können während der Speicherung oder Übertragung nicht geändert werden.
* **Nichtabstreitbarkeit**: Der Ersteller von Informationen kann die Erstellung später nicht abstreiten.
* **Authentifizierung**: Der Absender und der Empfänger können jeweils die Identität der anderen Partei bestätigen.

## <a name="encryption"></a>Verschlüsselung

Für den Verschlüsselungsprozess werden ein Algorithmus und ein Schlüssel benötigt. Mit dem Algorithmus wird definiert, wie Daten aus Klartext in Chiffretext und wieder zurück umgewandelt werden. Bei einem Schlüssel handelt es sich um eine zufällige Datenzeichenfolge, die als Eingabe für den Algorithmus verwendet wird. Die gesamte Sicherheit des Prozesses hängt von diesem Schlüssel ab. Aus diesem Grund muss der Schlüssel sicher gespeichert werden. Die Details der gängigsten Algorithmen sind dagegen öffentlich verfügbar.

Es gibt zwei Arten von Verschlüsselung. Bei der symmetrischen Verschlüsselung wird sowohl für die Verschlüsselung als auch für die Entschlüsselung derselbe Schlüssel verwendet. Bei der asymmetrischen Verschlüsselung kommen für die Ver- und Entschlüsselung unterschiedliche Schlüssel zum Einsatz, die aber mathematisch verwandt sind.

### <a name="symmetric-encryption"></a>Symmetrische Verschlüsselung

Bei der symmetrischen Verschlüsselung wird derselbe Schlüssel verwendet, um Klartext als Chiffretext zu verschlüsseln und diesen dann wieder in Klartext zu entschlüsseln. Die erforderliche Länge des Schlüssels wird vom Algorithmus bestimmt und als Bit-Anzahl ausgedrückt. Nachdem der Schlüssel zum Verschlüsseln des Klartexts verwendet wurde, wird die verschlüsselte Nachricht an den Empfänger gesendet, bei dem der Chiffretext dann entschlüsselt wird. Der symmetrische Schlüssel muss auf sichere Weise an den Empfänger übermittelt werden. Das Senden des Schlüssels stellt bei der Verwendung eines symmetrischen Algorithmus das größte Sicherheitsrisiko dar.

![Beispiel für die symmetrische Verschlüsselung](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Asymmetrische Verschlüsselung

Wenn nur die symmetrische Verschlüsselung genutzt wird, besteht das Problem darin, dass alle Parteien des Nachrichtenaustauschs den privaten Schlüssel besitzen müssen. Hierbei besteht aber die Gefahr, dass nicht autorisierte Dritte den Schlüssel während der Übertragung an autorisierte Benutzer abfangen. Verwenden Sie stattdessen die asymmetrische Verschlüsselung oder einen kryptografischen Ansatz mit öffentlichen Schlüsseln.

Bei der asymmetrischen Verschlüsselung verfügt jeder Benutzer über zwei Schlüssel, die mathematisch verwandt sind und als Schlüsselpaar bezeichnet werden. Das Schlüsselpaar besteht aus einem öffentlichen und einem privaten Schlüssel. Mit dem Schlüsselpaar wird sichergestellt, dass nur der Empfänger Zugriff auf den privaten Schlüssel hat, der zum Entschlüsseln der Daten benötigt wird. In der folgenden Abbildung ist der Prozess einer asymmetrischen Verschlüsselung dargestellt.

![Beispiel für die asymmetrische Verschlüsselung](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Der Empfänger erstellt ein Schlüsselpaar mit einem öffentlichen und einem privaten Schlüssel und sendet den öffentlichen Schlüssel an eine Zertifizierungsstelle. Die Zertifizierungsstelle erstellt für den öffentlichen Schlüssel ein Paket mit einem X.509-Zertifikat.

1. Der Absender erhält den öffentlichen Schlüssel des Empfängers von der Zertifizierungsstelle.

1. Der Absender verschlüsselt die Klartextdaten mit einem Verschlüsselungsalgorithmus. Für die Verschlüsselung wird der öffentliche Schlüssel des Empfängers genutzt.

1. Der Absender überträgt den Chiffretext an den Empfänger. Das Senden des Schlüssels ist nicht erforderlich, weil der Empfänger bereits den privaten Schlüssel besitzt, den er zum Entschlüsseln des Chiffretexts benötigt.

1. Der Empfänger entschlüsselt den Chiffretext mit dem angegebenen asymmetrischen Algorithmus und dem privaten Schlüssel.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Kombinieren der symmetrischen und asymmetrischen Verschlüsselung

Die symmetrische und asymmetrische Verschlüsselung kann kombiniert werden, um die jeweiligen Vorteile zu nutzen. Für die symmetrische Verschlüsselung wird deutlich weniger Zeit als für die asymmetrische Verschlüsselung benötigt, aber sie ist auch weniger sicher, weil private Schlüssel an andere Parteien gesendet werden müssen. Beim Kombinieren der beiden Verfahren kann die symmetrische Verschlüsselung genutzt werden, um Klartext in Chiffretext umzuwandeln. Die asymmetrische Verschlüsselung wird dann eingesetzt, um den symmetrischen Schlüssel auszutauschen. Dies ist im folgenden Diagramm dargestellt.

![Symmetrische und asymmetrische Verschlüsselung](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Der Absender erhält den öffentlichen Schlüssel des Empfängers.

1. Der Absender generiert einen symmetrischen Schlüssel und verwendet diesen, um die Originaldaten zu verschlüsseln.

1. Der Absender verwendet den öffentlichen Schlüssel des Empfängers, um den symmetrischen Schlüssel zu verschlüsseln.

1. Der Absender überträgt den verschlüsselten symmetrischen Schlüssel und den Chiffretext an den gewünschten Empfänger.

1. Der Empfänger verwendet den privaten Schlüssel, der zum öffentlichen Schlüssel des Empfängers passt, um den symmetrischen Schlüssel des Absenders zu entschlüsseln.

1. Der Empfänger verwendet den symmetrischen Schlüssel, um den Chiffretext zu entschlüsseln.

### <a name="asymmetric-signing"></a>Asymmetrisches Signieren

Asymmetrische Algorithmen können verwendet werden, um Daten vor Manipulation zu schützen und die Identität des Erstellers zu beweisen. In der folgenden Abbildung ist dargestellt, wie die Identität des Absenders durch das asymmetrische Signieren bewiesen werden kann.

![Beispiel für asymmetrisches Signieren](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Der Absender wendet auf Klartextdaten einen Algorithmus für die asymmetrische Verschlüsselung an und nutzt hierbei den privaten Schlüssel, um die Daten zu verschlüsseln. Beachten Sie, dass die Verwendung des privaten und öffentlichen Schlüssels im Vergleich zum vorherigen Abschnitt, in dem die asymmetrische Verschlüsselung beschrieben wurde, nun umgekehrt ist.

1. Der sich ergebende Chiffretext wird an den Empfänger gesendet.

1. Der Empfänger erhält den öffentlichen Schlüssel des Absenders aus einem entsprechenden Verzeichnis.

1. Der Empfänger entschlüsselt den Chiffretext mit dem öffentlichen Schlüssel des Absenders. Die erfolgreiche Umwandlung in den Klartext ist der Beweis für die Identität des Absenders, weil nur der Absender Zugriff auf den privaten Schlüssel hat, mit dem der Originaltext ursprünglich verschlüsselt wurde.

## <a name="signing"></a>Signieren

Mithilfe von digitalen Signaturen kann ermittelt werden, ob Daten während der Übertragung oder im Ruhezustand geändert wurden. Zu diesem Zweck wird auf die Daten ein Hashalgorithmus angewendet. Hierbei handelt es sich um eine unidirektionale Funktion, mit der für die jeweilige Nachricht ein mathematisches Ergebnis berechnet wird. Das Ergebnis wird als *Hashwert*, *Nachrichtenhash*, *Digest*, *Signatur* oder *Fingerabdruck* bezeichnet. Es ist nicht möglich, einen Hashwert „umzukehren“, um wieder die ursprüngliche Nachricht zu erhalten. Da eine geringfügige Änderung der Nachricht zu einer signifikanten Änderung des *Fingerabdrucks* führt, kann anhand des Hashwerts ermittelt werden, ob eine Nachricht manipuliert wurde. In der folgenden Abbildung ist dargestellt, wie mithilfe der asymmetrischen Verschlüsselung und von Hashalgorithmen verifiziert werden kann, ob eine Nachricht geändert wurde.

![Beispiel für Signierung](media/tutorial-x509-introduction/signing.png)

1. Der Absender erstellt eine Klartextnachricht.

1. Der Absender wendet einen Hashalgorithmus auf die Klartextnachricht an, um einen Nachrichtenhash zu erstellen.

1. Der Absender verschlüsselt den Nachrichtenhash mit einem privaten Schlüssel.

1. Der Absender sendet die Klartextnachricht und den verschlüsselten Nachrichtenhash an den gewünschten Empfänger.

1. Der Empfänger entschlüsselt den Nachrichtenhash mit dem öffentlichen Schlüssel des Absenders.

1. Der Empfänger führt den gleichen Hashalgorithmus aus, der vom Absender für die Nachricht verwendet wurde.

1. Der Empfänger vergleicht die sich ergebende Signatur mit der entschlüsselten Signatur. Wenn die Nachrichtenhashs identisch sind, wurde die Nachricht während der Übertragung nicht geändert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Feldern eines Zertifikats finden Sie unter [Grundlegendes zu X.509-Zertifikaten mit öffentlichem Schlüssel](tutorial-x509-certificates.md).

Falls Sie sich mit X.509-Zertifikaten bereits gut auskennen und Testversionen für die Authentifizierung bei Ihrem IoT-Hub generieren möchten, helfen Ihnen die folgenden Themen weiter:

* [Verwenden von Microsoft-Skripts zum Erstellen von Testzertifikaten](tutorial-x509-scripts.md)
* [Verwenden von OpenSSL zum Erstellen von Testzertifikaten](tutorial-x509-openssl.md)
* [Verwenden von OpenSSL zum Erstellen von selbstsignierten Testzertifikaten](tutorial-x509-self-sign.md)

Falls Sie über ein Zertifikat einer Zertifizierungsstelle (ZS) bzw. einer untergeordneten Zertifizierungsstelle verfügen und es auf Ihren IoT-Hub hochladen und beweisen möchten, dass es sich in Ihrem Besitz befindet, helfen Ihnen die Informationen unter [Nachweisen des Besitzes eines Zertifizierungsstellenzertifikats](tutorial-x509-prove-possession.md) weiter.
