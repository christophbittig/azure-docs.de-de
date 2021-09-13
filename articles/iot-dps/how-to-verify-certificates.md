---
title: Überprüfen von X.509-Zertifizierungsstellenzertifikaten mit dem Azure IoT Hub Device Provisioning-Dienst
description: Nachweis des Besitzes für X.509-Zertifizierungsstellenzertifikate mit dem Azure IoT Hub Device Provisioning-Dienst (Device Provisioning Service, DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: c1c4490b89a960d89cb1678c96391faf0ac4cc5b
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108546"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Nachweis des Besitzes für X.509-Zertifizierungsstellenzertifikate mit Ihrem Device Provisioning-Dienst

Ein überprüftes X.509-Zertifizierungsstellenzertifikat (ZS) ist ein ZS-Zertifikat, das auf Ihren Bereitstellungsdienst hochgeladen und zertifiziert wurde und einen Besitznachweis durch den Dienst durchlaufen hat. 

Überprüfte Zertifikate spielen bei Verwendung von Registrierungsgruppen eine wichtige Rolle. Der Nachweis des Zertifikatbesitzes bietet eine zusätzliche Sicherheitsschicht, indem sichergestellt wird, dass die hochladende Person eines Zertifikats im Besitz des privaten Schlüssels des Zertifikats ist. Die Überprüfung verhindert, dass ein böswilliger Akteur, der Ihren Datenverkehr abfängt, ein Zwischenzertifikat extrahiert und mit diesem Zertifikat eine Registrierungsgruppe im eigenen Bereitstellungsdienst erstellt, um sich letztlich Ihre Geräte anzueignen. Durch den Nachweis der Besitzes des Stammzertifikats oder eines Zwischenzertifikats in einer Zertifikatkette weisen Sie nach, dass Sie berechtigt sind, untergeordnete Zertifikate für die Geräte zu generieren, die in dieser Registrierungsgruppe Registrierungen ausführen. Daher muss es sich bei dem in einer Registrierungsgruppe konfigurierten Stamm- oder Zwischenzertifikat um ein verifiziertes Zertifikat handeln, oder ihm muss ein verifiziertes Zertifikat in der Zertifikatkette, die ein Gerät bei der Authentifizierung beim Dienst vorlegt, folgen. Weitere Informationen zum X.509-Zertifikatnachweis finden Sie unter [X.509-Zertifikate](concepts-x509-attestation.md) und [Steuern des Gerätezugriffs auf den Bereitstellungsdienst mit X.509-Zertifikaten](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="automatic-verification-of-intermediate-or-root-ca-through-self-attestation"></a>Automatische Überprüfung der Zwischen- oder Stammzertifizierungsstelle durch Selbstnachweis
Wenn Sie eine vertrauenswürdige Zwischen- oder Stammzertifizierungsstelle verwenden und wissen, dass Sie im vollständigen Besitzer des Zertifikats sind, können Sie selbst nachweisen, dass Sie das Zertifikat überprüft haben.

Führen Sie diese Schritte aus, um ein automatisch überprüftes Zertifikat hinzuzufügen:

1. Navigieren Sie im Azure-Portal zu Ihrem Bereitstellungsdienst, und öffnen Sie im linken Menü **Zertifikate**. 
2. Klicken Sie auf **Hinzufügen**, um ein neues Zertifikat hinzuzufügen.
3. Geben Sie einen Anzeigenamen für Ihr Zertifikat ein. Navigieren Sie zu der CER- oder PEM-Datei, die den öffentlichen Teil des X.509-Zertifikats darstellt. Klicken Sie auf **Hochladen**.
4. Aktivieren Sie das Kontrollkästchen neben **Set certificate status to verified on upload** (Zertifikatstatus beim Hochladen auf „überprüft“ festlegen).

    ![Hochladen eines überprüften Zertifikats](./media/how-to-verify-certificates/add-certificate-with-verified.png)

1. Klicken Sie auf **Speichern**.
1. Ihr Zertifikat wird auf der Zertifikatregisterkarte mit dem Status *Überprüft* angezeigt.
  
    ![Zertifikatstatus](./media/how-to-verify-certificates/certificate-status.png)

## <a name="manual-verification-of-intermediate-or-root-ca"></a>Manuelle Überprüfung der Zwischen- oder Stammzertifizierungsstelle

Der Besitznachweis umfasst die folgenden Schritte:
1. Abrufen eines eindeutigen Prüfcodes, der durch den Bereitstellungsdienst für Ihr X.509-ZS-Zertifikat generiert wurde. Dies können Sie über das Azure-Portal erledigen.
2. Erstellen eines X.509-Überprüfungszertifikats mit dem Prüfcode als Betreff und Signieren des Zertifikats mit dem privaten Schlüssel, der Ihrem X.509-ZS-Zertifikat zugeordnet ist.
3. Hochladen des signierten Verifizierungszertifikats in den Dienst. Der Dienst überprüft das Verifizierungszertifikat mithilfe des öffentlichen Teils des zu überprüfenden Zertifizierungsstellenzertifikats und weist dadurch nach, dass sich der private Schlüssel des ZS-Zertifikats in Ihrem Besitz befindet.


### <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrieren des öffentlichen Teils eines X.509-Zertifikats und Abrufen eines Prüfcodes

Um ein Zertifizierungsstellenzertifikat bei Ihrem Bereitstellungsdienst zu registrieren und einen Prüfcode, den Sie während des Besitznachweises verwenden können, abzurufen, führen Sie die folgenden Schritte aus. 

1. Navigieren Sie im Azure-Portal zu Ihrem Bereitstellungsdienst, und öffnen Sie im linken Menü **Zertifikate**. 
2. Klicken Sie auf **Hinzufügen**, um ein neues Zertifikat hinzuzufügen.
3. Geben Sie einen Anzeigenamen für Ihr Zertifikat ein. Navigieren Sie zu der CER- oder PEM-Datei, die den öffentlichen Teil des X.509-Zertifikats darstellt. Klicken Sie auf **Hochladen**.
4. Wenn Sie eine Benachrichtigung erhalten, dass das Zertifikat erfolgreich hochgeladen wurde, klicken Sie auf **Speichern**.

    ![Hochladen des Zertifikats](./media/how-to-verify-certificates/add-new-cert.png)  

   Das Zertifikat wird in der Liste **Zertifikat-Explorer** angezeigt. Beachten Sie, dass der **STATUS** dieses Zertifikats *Nicht überprüft* lautet.

5. Klicken Sie auf das Zertifikat, das Sie im vorherigen Schritt hinzugefügt haben.

6. Klicken Sie unter **Zertifikatdetails** auf **Prüfcode generieren**.

7. Der Bereitstellungsdienst erstellt einen **Prüfcode**, mit dem Sie den Besitz des Zertifikats überprüfen können. Kopieren Sie den Code in die Zwischenablage. 

   ![Überprüfen des Zertifikats](./media/how-to-verify-certificates/verify-cert.png)  

### <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Digitales Signieren des Prüfcodes zum Erstellen eines Verifizierungszertifikats

Nun müssen Sie den *Prüfcode* mit dem privaten Schlüssel signieren, der Ihrem X.509-Zertifikat zugeordnet ist, und eine Signatur generieren. Dies wird als [Besitznachweis](https://tools.ietf.org/html/rfc5280#section-3.1) bezeichnet und führt zu einem signierten Verifizierungszertifikat.

Microsoft bietet Tools und Beispiele, die Ihnen dabei helfen können, ein signiertes Verifizierungszertifikat zu erstellen: 

- Das **Azure IoT Hub C SDK** bietet PowerShell- (Windows) und Bash-Skripts (Linux), mit denen Sie Zertifizierungsstellen- und untergeordnete Zertifikate für die Entwicklung und zum Nachweis des Besitzes mit einem Prüfcode erstellen können. Sie können die entsprechenden [Dateien](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) für Ihr System in einen Arbeitsordner herunterladen und die Anweisungen in der [Infodatei zum Verwalten von ZS-Zertifikaten](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) befolgen, um einen Besitznachweis für ein Zertifizierungsstellenzertifikat auszuführen. 
- Das **Azure IoT Hub C# SDK** enthält ein [Beispiel zum Überprüfen des Gruppenzertifikats](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), mit dem Sie einen Besitznachweis ausführen können.
 
> [!IMPORTANT]
> Zusätzlich zum Ausführen des Besitznachweises ermöglichen die zuvor genannten PowerShell- und Bash-Skripts auch, Stammzertifikate, Zwischenzertifikate und untergeordnete Zertifikate zu erstellen, um Geräte zu authentifizieren und bereitzustellen. Diese Zertifikate sollten nur für die Entwicklung verwendet werden. Sie sollten nie in einer Produktionsumgebung verwendet werden. 

Die in der Dokumentation und den SDKs bereitgestellten PowerShell- und Bash-Skripts benötigen [OpenSSL](https://www.openssl.org/). Sie können auch OpenSSL oder andere Tools von Drittanbietern verwenden, um den Besitznachweis auszuführen. Ein Beispiel für die Verwendung der mit den SDKs bereitgestellten Tools finden Sie unter [Erstellen einer X.509-Zertifikatkette](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain). 


### <a name="upload-the-signed-verification-certificate"></a>Hochladen des signierten Verifizierungszertifikats

1. Laden Sie die resultierende Signatur als Verifizierungszertifikat in Ihren Bereitstellungsdienst im Portal hoch. Verwenden Sie unter **Zertifikatdetails** im Azure-Portal das Symbol _Datei-Explorer_ neben dem Feld **PEM- oder CER-Verifizierungszertifikatsdatei**, um das signierte Verifizierungszertifikat von Ihrem System hochzuladen.

2. Nachdem das Zertifikat hochgeladen wurde, klicken Sie auf **Überprüfen**. Der **STATUS** Ihres Zertifikats in der Liste **Zertifikate** wechselt zu **_Überprüft_**. Klicken Sie auf **Aktualisieren**, wenn es nicht automatisch aktualisiert wird.

   ![Hochladen der Zertifikatprüfung](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen einer Registrierungsgruppe mit dem Portal finden Sie unter [Verwalten von Geräteregistrierungen mit dem Azure-Portal](how-to-manage-enrollments.md).
- Weitere Informationen zum Erstellen einer Registrierungsgruppe mit den Dienst-SDKs finden Sie unter [Verwalten von Geräteregistrierungen mit Dienst-SDKs](./quick-enroll-device-x509-java.md).
