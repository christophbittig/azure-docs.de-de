---
title: Konfigurieren von OpenSSL für Linux
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie OpenSSL für Linux konfigurieren.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ROBOTS: NOINDEX
ms.openlocfilehash: ec2bd2cb46ff96602ed39cf3c9c4e41ddcf5ab33
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132155990"
---
# <a name="configure-openssl-for-linux"></a>Konfigurieren von OpenSSL für Linux

Mit dem Speech SDK Version 1.19.0 und höher wird [OpenSSL](https://www.openssl.org) dynamisch auf die Version des Hostsystems konfiguriert. In früheren Versionen ist OpenSSL statisch mit der Kernbibliothek des SDKs verknüpft.

Um Konnektivität sicherzustellen, überprüfen Sie, ob OpenSSL-Zertifikate auf Ihrem System installiert wurden. Ausführen eines Befehls:
```bash
openssl version -d
```

Die Ausgabe auf Ubuntu/Debian-basierten Systemen sollte wie folgt aussehen:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Überprüfen Sie, ob ein Unterverzeichnis `certs` unter OPENSSLDIR vorhanden ist. Im Beispiel oben wäre das `/usr/lib/ssl/certs`.

* Wenn `/usr/lib/ssl/certs` vorhanden ist und viele einzelne Zertifikatdateien (mit der Erweiterung `.crt`-oder `.pem`) enthält, ist keine weitere Aktion erforderlich.

* Wenn OPENSSLDIR etwas anderes als `/usr/lib/ssl` ist und/oder eine einzelne Zertifikatpaketdatei anstatt vieler einzelner Dateien vorhanden ist, müssen Sie eine geeignete SSL-Umgebungsvariable festlegen, um anzugeben, wo die Zertifikate zu finden sind.

## <a name="examples"></a>Beispiele

- OPENSSLDIR ist `/opt/ssl`. Es gibt ein Unterverzeichnis `certs` mit vielen `.crt`-oder `.pem`-Dateien.
Legen Sie die Umgebungsvariable `SSL_CERT_DIR` so fest, dass sie auf `/opt/ssl/certs` verweist, bevor Sie ein Programm ausführen, das das Speech SDK verwendet. Beispiel:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR ist `/etc/pki/tls` (wie bei RHEL-/CentOS-basierten Systemen). Es ist ein Unterverzeichnis vom Typ `certs` mit einer Zertifikatpaketdatei vorhanden, z. B. `ca-bundle.crt`.
Legen Sie die Umgebungsvariable `SSL_CERT_FILE` so fest, dass sie auf diese Datei verweist, bevor Sie ein Programm ausführen, das das Speech SDK verwendet. Beispiel:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Überprüfung von Zertifikatswiderrufen
Beim Herstellen einer Verbindung mit dem Speech-Dienst überprüft das Speech SDK, ob das vom Speech-Dienst verwendete TLS-Zertifikat widerrufen wurde. Für diese Überprüfung benötigt das Speech SDK Zugriff auf die CRL-Verteilungspunkte für von Azure verwendete Zertifizierungsstellen. Eine Liste der möglichen URLs für den Download von Zertifikatssperrlisten finden Sie in [diesem Dokument](../../security/fundamentals/tls-certificate-changes.md). Wenn ein Zertifikat widerrufen wurde oder die Zertifikatsperrliste nicht heruntergeladen werden kann, bricht das Speech SDK die Verbindung ab und gibt das Canceled-Ereignis aus.

Falls die Konfiguration des Netzwerks, in dem das Speech SDK verwendet wird, den Zugriff auf die URLs für den Download von Zertifikatssperrlisten nicht zulässt, kann die CRL-Überprüfung deaktiviert oder als erfolgreich festgelegt werden, wenn die Zertifikatsperrliste nicht abgerufen werden kann. Diese Konfiguration erfolgt über das Konfigurationsobjekt, das zum Erstellen eines Recognizer-Objekts verwendet wird.

Um die Verbindung fortzusetzen, wenn keine Zertifikatsperrliste abgerufen werden kann, legen Sie die Eigenschaft „OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE“ fest.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Wenn der Wert auf „true“ festgelegt ist, wird versucht, die Zertifikatsperrliste abzurufen. Bei erfolgreichem Abruf wird das Zertifikat auf eine Sperre überprüft. Wenn der Abruf fehlschlägt, wird das Fortsetzen der Verbindung zugelassen.

Um die Zertifikatsperrüberprüfung vollständig zu deaktivieren, legen Sie die Eigenschaft „OPENSSL_DISABLE_CRL_CHECK“ auf „true“ fest.
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Beachten Sie auch, dass in einigen Linux-Distributionen keine TMP- oder TMPDIR-Umgebungsvariable definiert ist. Dies führt dazu, dass das Speech SDK die Zertifikatsperrliste (Certificate Revocation List, CRL) jedes Mal herunterlädt, statt diese Liste zur Wiederverwendung bis zum Ablauf auf einem Datenträger zwischenzuspeichern. Um die Leistung bei der Verbindungsherstellung zu verbessern, können Sie [eine Umgebungsvariable namens TMPDIR erstellen und auf den Pfad des von Ihnen ausgewählten temporären Verzeichnisses festlegen](https://help.ubuntu.com/community/EnvironmentVariables).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Speech SDK](speech-sdk.md)
