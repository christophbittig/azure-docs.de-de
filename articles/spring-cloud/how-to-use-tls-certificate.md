---
title: Verwenden von TLS/SSL-Zertifikaten in Ihrer Anwendung in Azure Spring Cloud
titleSuffix: Azure Spring Cloud
description: Verwenden von TLS/SSL-Zertifikaten in einer Anwendung.
author: karlerickson
ms.author: jieshe
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/08/2021
ms.custom: devx-track-java
ms.openlocfilehash: 9a8671e6a52210bc7f49b6654383e12fd3090b63
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258105"
---
# <a name="use-tlsssl-certificates-in-your-application-in-azure-spring-cloud"></a>Verwenden von TLS/SSL-Zertifikaten in Ihrer Anwendung in Azure Spring Cloud

In diesem Artikel erfahren Sie, wie Sie öffentliche Zertifikate in Azure Spring Cloud für Ihre Anwendung verwenden. Ihre App kann als Client fungieren und auf einen externen Dienst mit erforderlicher Zertifikatauthentifizierung zugreifen oder ggf. kryptografische Aufgaben ausführen.  

Wenn Sie Ihre TLS-/SSL-Zertifikate über Azure Spring Cloud verwalten, können Sie die Zertifikate und Ihren Anwendungscode separat verwalten und Ihre sensiblen Daten schützen. Ihr App-Code kann auf die öffentlichen Zertifikate zugreifen, die Sie Ihrer Azure Spring Cloud-Instanz hinzufügen.

> [!NOTE]
> Informationen zur Azure CLI und Terraform-Unterstützung sowie -Beispiele werden in Kürze in diesem Artikel verfügbar sein.

## <a name="prerequisites"></a>Voraussetzungen

- Eine in Azure Spring Cloud bereitgestellte Anwendung. Weitere Informationen finden Sie unter [Schnellstart: Bereitstellen Ihrer ersten Anwendung in Azure Spring Cloud](./quickstart.md), oder verwenden Sie eine vorhandene App.
- Entweder eine Zertifikatdatei mit der Erweiterung *CRT*, *CER*, *PEM* oder *DER* oder eine bereitgestellte Instanz von Azure Key Vault einem privaten Zertifikat.

## <a name="import-a-certificate"></a>Importieren eines Zertifikats

Sie können Ihr Zertifikat entweder aus Key Vault in eine Azure Spring Cloud-Instanz importieren oder eine lokale Zertifikatdatei verwenden.

### <a name="import-a-certificate-from-key-vault"></a>Importieren eines Zertifikats aus Key Vault

Sie müssen Azure Spring Cloud Zugriff auf Ihren Schlüsseltresor gewähren, bevor Sie Ihr Zertifikat mit den folgenden Schritten importieren:

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Schlüsseltresore** und dann die Key Vault-Instanz aus, aus der Sie Ihr Zertifikat importieren möchten.
1. Wählen Sie im linken Navigationsbereich die Option **Zugriffsrichtlinien** und dann **+ Zugriffsrichtlinie hinzufügen** aus.
1. Wählen Sie **Zertifikatberechtigungen** und dann **Abrufen** und **Auflisten** aus.
1. Wählen Sie unter **Prinzipal auswählen** die Option **Keine ausgewählt** aus, und wählen Sie dann Ihren Sicherheitsprinzipal aus.
1. Wählen Sie **Auswählen** und dann **Hinzufügen** aus.

:::image type="content" source="media/use-tls-certificates/grant-key-vault-permission.png" alt-text="Screenshot: Gewähren des Zugriffs auf den Schlüsseltresor vor dem Importieren eines Zertifikats" lightbox="media/use-tls-certificates/grant-key-vault-permission.png":::

Nachdem Sie Zugriff auf Ihren Schlüsseltresor gewährt haben, können Sie Ihr Zertifikat mithilfe der folgenden Schritte importieren:

1. Navigieren Sie zu Ihrer Dienstinstanz.
1. Wählen Sie im linken Navigationsbereich Ihrer Instanz die Option **TLS/SSL-Einstellungen** aus.
1. Wählen Sie **Key Vault-Zertifikat importieren** im Abschnitt **Zertifikate für öffentliche Schlüssel** aus.
1. Wählen Sie Ihre Key Vault-Instanz unter **Schlüsseltresor** und das Zertifikat unter **Zertifikat** aus. Wählen Sie dann **Auswählen** und **Anwenden** aus.
1. Wenn das Zertifikat erfolgreich importiert wurde, wird es in der Liste „Zertifikate für öffentliche Schlüssel“ angezeigt.

### <a name="import-a-local-certificate-file"></a>Importieren einer lokalen Zertifikatdatei

Sie können eine lokal gespeicherte Zertifikatdatei importieren, indem Sie die folgenden Schritte ausführen:

1. Navigieren Sie zu Ihrer Dienstinstanz.
1. Wählen Sie im linken Navigationsbereich Ihrer Instanz die Option **TLS/SSL-Einstellungen** aus.
1. Wählen Sie **Öffentliches Zertifikat hochladen** im Abschnitt **Zertifikate für öffentliche Schlüssel** aus.
1. Wenn das Zertifikat erfolgreich importiert wurde, wird es in der Liste „Zertifikate für öffentliche Schlüssel“ angezeigt.

## <a name="load-a-certificate"></a>Laden eines Zertifikats

Beginnen Sie mit den folgenden Schritten, um ein Zertifikat in Ihre Anwendung in Azure Spring Cloud zu laden:

1. Navigieren Sie zu Ihrer Anwendungsinstanz.
1. Wählen Sie im linken Navigationsbereich Ihrer App die Option **Zertifikatverwaltung** aus.
1. Wählen Sie **Zertifikat hinzufügen** aus, um Zertifikate auszuwählen, auf die durch die App zugegriffen werden kann.

:::image type="content" source="media/use-tls-certificates/load-certificate.png" alt-text="Screenshot: Laden eines Zertifikats in Ihre Anwendung" lightbox="media/use-tls-certificates/load-certificate.png":::

### <a name="load-a-certificate-from-code"></a>Laden eines Zertifikats aus Code

Ihre geladenen Zertifikate sind im Ordner */etc/azure-spring-cloud/certs/public* verfügbar. Verwenden Sie den folgenden Java-Code, um ein öffentliches Zertifikat in eine Anwendung in Azure Spring Cloud zu laden.

```java
CertificateFactory factory = CertificateFactory.getInstance("X509");
FileInputStream is = new FileInputStream("/etc/azure-spring-cloud/certs/public/<certificate name>");
X509Certificate cert = (X509Certificate) factory.generateCertificate(is);

// use the loaded certificate
```

### <a name="load-a-certificate-into-the-trust-store"></a>Laden eines Zertifikats in den Vertrauensspeicher

Für eine Java-Anwendung können Sie **In Vertrauensspeicher laden** für das ausgewählte Zertifikat auswählen. Das Zertifikat wird den Java-Standard-TrustStores automatisch hinzugefügt, um einen Server mit SSL-Authentifizierung zu authentifizieren.

Das folgende Protokoll aus Ihrer App zeigt, dass das Zertifikat erfolgreich geladen wurde.

```
Load certificate from specific path. alias = <certificate alias>, thumbprint = <certificate thumbprint>, file = <certificate name>
```

## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren von End-to-End-TLS (Transport Layer Security)](./how-to-enable-end-to-end-tls.md)
* [Zugreifen auf den Konfigurationsserver und die Dienstregistrierung](./how-to-access-data-plane-azure-ad-rbac.md)
