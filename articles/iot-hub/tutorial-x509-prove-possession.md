---
title: 'Tutorial: Überprüfen des Besitzes von Zertifizierungsstellen Zertifikaten in Azure IOT Hub | Microsoft-Dokumentation'
description: 'Tutorial: Stellen Sie sich vor, dass Sie ein Zertifizierungsstellen Zertifikat für Azure IOT Hub'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 06/25/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 79b55e938d832602dca87af8ee366cd44aa44a91
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547163"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Tutorial: Nachweis des Besitzes eines ZS-Zertifikats

Wenn Sie das Zertifikat der Stammzertifizierungsstelle oder das Zertifikat der untergeordneten Zertifizierungsstelle in Ihren IoT-Hub hochladen, können Sie es automatisch überprüfen lassen, oder manuell bestätigen, dass Sie das Zertifikat besitzen.

## <a name="verify-certificate-automatically"></a>Automatische Überprüfung des Zertifikats 

1. Navigieren Sie im Azure-Portalzu IoTHub und wählen Sie **Einstellungen > Zertifikate**.

2. Wählen Sie **Hinzufügen** aus, um ein neues ZS-Zertifikat hinzuzufügen.

3. Geben Sie im Feld **Zertifikatname** einen Anzeigenamen ein, und wählen Sie das hinzuzufügende PEM-Zertifikat aus.

4. Um das Zertifikat automatisch zu überprüfen, aktivieren Sie das Kontrollkästchen neben **Zertifikatstatus beim Hochladen auf überprüft festlegen**.

  :::image type="content" source="media/tutorial-x509-prove-possession/skip-pop.png" alt-text="Screenshot, der zeigt, wo sich das Kontrollkästchen zum Überspringen des Besitznachweises befindet":::

5. Wählen Sie **Speichern** aus.  Ihr Zertifikat wird in der Zertifikatliste mit dem Status **Überprüft** angezeigt.

## <a name="verify-certificate-manually-after-upload"></a>Manuelles Überprüfen des Zertifikats nach dem Upload

1. Wenn Sie das Zertifikat während des Uploads nicht automatisch überprüfen möchten, wird Ihr Zertifikat in der Zertifikatliste mit dem Status **Nicht überprüft** angezeigt. 

2. Wählen Sie das Zertifikat aus, um das Dialogfeld **Zertifikat Details** anzuzeigen.

3. Wählen Sie im Dialogfeld **Überprüfungs Code generieren** aus.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Zertifikatdetails Dialogfeld}":::

4. Kopieren Sie den Prüfcode in die Zwischenablage. Sie müssen den Überprüfungs Code als Zertifikat Antragsteller festlegen. Wenn der Überprüfungs Code beispielsweise 75b86466da34d2b04c0c4c9557a119687adae7d4732bddb3 ist, fügen Sie diesen als Betreff des Zertifikats hinzu, wie im nächsten Schritt gezeigt.

5. Es gibt drei Möglichkeiten, ein Überprüfungs Zertifikat zu generieren:

    * Wenn Sie das von Microsoft bereitgestellte PowerShell-Skript verwenden, führen Sie aus, `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` um ein Zertifikat mit dem Namen zu erstellen `VerifyCert4.cer`. Weitere Informationen finden Sie unter [Verwenden der von Microsoft bereitgestellten Skripts](tutorial-x509-scripts.md).

    * Wenn Sie das von Microsoft bereitgestelltem Bash-Skript verwenden, führen Sie aus, `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` um ein Zertifikat mit dem Namen zu erstellen `verification-code.cert.pem`. Weitere Informationen finden Sie unter [Verwenden der von Microsoft bereitgestellten Skripts](tutorial-x509-scripts.md).

    * Wenn Sie die Zertifikate mit OpenSSL generieren, müssen Sie zunächst einen privaten Schlüssel und dann eine Zertifikat Signier Anforderung (Certificate Signing Request, CSR) generieren:

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Erstellen Sie dann ein Zertifikat mithilfe der Konfigurationsdatei der Stamm Zertifizierungsstelle (unten dargestellt) oder der untergeordneten ZS-Konfigurationsdatei und der CSR-Datei.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Weitere Informationen finden Sie unter [Verwenden von OpenSSL zum Erstellen von Test Zertifikaten](tutorial-x509-openssl.md).

6. Wählen Sie in der Ansicht **Zertifikat Details** das neue Zertifikat aus.

7. Klicken Sie nach dem Hochladen des Zertifikats auf **überprüfen**. Der Status des Zertifizierungsstellen Zertifikats sollte in **überprüft** geändert werden.
