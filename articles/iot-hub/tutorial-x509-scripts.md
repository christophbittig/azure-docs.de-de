---
title: 'Tutorial: Verwenden von Microsoft-Skripts zum Erstellen von x.509-Testzertifikaten für Azure IoT Hub | Microsoft-Dokumentation'
description: 'Tutorial: Verwenden von benutzerdefinierten Skripts zum Erstellen von Zertifizierungsstellen- und Gerätezertifikaten für Azure IoT Hub'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 06/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 65d511cabda3595828db9926603b6eb20efd2e83
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549943"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Tutorial: Verwenden von Microsoft-Skripts zum Erstellen von Testzertifikaten

Microsoft stellt PowerShell- und Bash-Skripts bereit, die Sie beim Erstellen eigener x.509-Zertifikate und beim Authentifizieren dieser Zertifikate bei einer IoT Hub-Instanz unterstützen. Die Skripts befinden sich in einem [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates). Sie werden nur zu Demonstrationszwecken bereitgestellt. Diese Zertifikate dürfen nicht für die Produktion verwendet werden. Die Zertifikate enthalten hartcodierte Kennwörter („1234“) und laufen nach 30 Tagen ab. Für eine Produktionsumgebung müssen Sie Ihre eigenen bewährten Methoden für die Zertifikaterstellung und die Verwaltung der Lebensdauer verwenden.

## <a name="powershell-scripts"></a>PowerShell-Skripts

### <a name="step-1---setup"></a>Schritt 1: Setup

Stellen Sie OpenSSL für Windows bereit. Unter <https://www.openssl.org/docs/faq.html#MISC4> finden Sie Informationen dazu, wo Sie OpenSSL herunterladen können, und unter <https://www.openssl.org/source/> finden Sie Informationen zum Erstellen auf der Grundlage der Quelle. Führen Sie dann die vorläufigen Skripts aus:

1. Kopieren Sie die Skripts aus [diesem GitHub-Repository](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) in das lokale Verzeichnis, in dem Sie arbeiten möchten. Alle Dateien werden als untergeordnete Elemente dieses Verzeichnisses erstellt.

1. Starten Sie PowerShell als Administrator.

1. Navigieren Sie zu dem Verzeichnis, in das Sie die Skripts geladen haben.

1. Legen Sie in der Befehlszeile die Umgebungsvariable `$ENV:OPENSSL_CONF` auf das Verzeichnis fest, in dem sich die OpenSSL-Konfigurationsdatei (openssl.cnf) befindet.

1. Führen Sie `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` aus, sodass die Skripts von PowerShell ausgeführt werden können.

1. Führen Sie aus `. .\ca-certs.ps1`. Dadurch werden die Funktionen des Skripts in den globalen PowerShell-Namespace eingefügt.

1. Führen Sie aus `Test-CACertsPrerequisites`. PowerShell nutzt den Windows-Zertifikatspeicher für die Zertifikatverwaltung. Mit diesem Befehl wird sichergestellt, dass später keine Namenskonflikte mit vorhandenen Zertifikaten auftreten und dass OpenSSL ordnungsgemäß eingerichtet ist.

### <a name="step-2---create-certificates"></a>Schritt 2: Erstellen von Zertifikaten

Führen Sie aus `New-CACertsCertChain [ecc|rsa]`. Für Zertifizierungsstellenzertifikate wird EEC zwar empfohlen, ist aber nicht erforderlich. Dieses Skript aktualisiert Ihr Verzeichnis und den Windows-Zertifikatspeicher mit den folgenden Zertifizierungstellen- und Zwischenzertifikaten:

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

Fügen Sie nach dem Ausführen des Skripts das neue Zertifizierungsstellenzertifikat (RootCA.pem) zu Ihrer IoT Hub-Instanz hinzu:

1. Navigieren Sie zu Ihrer IoT Hub-Instanz und anschließend zu „Zertifikate“.

1. Wählen Sie **Hinzufügen**.

1. Geben Sie einen Anzeigenamen für das Zertifizierungsstellenzertifikat ein.

1. Zum Überspringen des Besitznachweises aktivieren Sie das Kontrollkästchen neben **Set certificate status to verified on upload** (Zertifikatstatus beim Hochladen auf „überprüft“ festlegen).

1. Laden Sie das Zertifizierungsstellenzertifikat hoch.

1. Wählen Sie **Speichern** aus.

### <a name="optional-step-3---prove-possession"></a>(Optional) Schritt 3: Nachweisen des Besitzes

Wenn Sie sich nicht dafür entscheiden, das Zertifikat während des Uploads automatisch zu überprüfen, weisen Sie den Besitz manuell nach:

1. Wählen Sie das neue Zertifizierungsstellenzertifikat aus.

1. Wählen Sie im Dialogfeld **Zertifikatdetails** die Option **Prüfcode generieren** aus. Weitere Informationen finden Sie unter [Tutorial: Nachweisen des Besitzes eines ZS-Zertifikats](tutorial-x509-prove-possession.md).

1. Erstellen Sie ein Zertifikat, das den Prüfcode enthält. Wenn der Prüfcode beispielsweise `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` lautet, führen Sie Folgendes aus, um ein neues Zertifikat in Ihrem Arbeitsverzeichnis mit dem Antragsteller `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` zu erstellen. Das Skript erstellt ein Zertifikat namens `VerifyCert4.cer`.

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Laden Sie im Dialogfeld **Zertifikatdetails** `VerifyCert4.cer` in Ihre IoT Hub-Instanz hoch.

1. Wählen Sie **Überprüfen** aus.

### <a name="step-4---create-a-new-device"></a>Schritt 4: Erstellen eines neuen Geräts

Erstellen Sie ein Gerät für Ihre IoT Hub-Instanz:

1. Navigieren Sie in Ihrer IoT Hub-Instanz zum Abschnitt **IoT-Geräte**.

1. Fügen Sie ein neues Gerät mit der ID `mydevice` hinzu.

1. Wählen Sie für die Authentifizierung die Option **X.509, durch Zertifizierungsstelle signiert** aus.

1. Führen Sie `New-CACertsDevice mydevice` aus, um ein neues Gerätezertifikat zu erstellen. Dadurch werden die folgenden Dateien in Ihrem Arbeitsverzeichnis erstellt:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Schritt 5: Testen des Gerätezertifikats

Navigieren Sie zu [Testen der Zertifikatauthentifizierung](tutorial-x509-test-certificate.md), um festzustellen, ob sich Ihr Gerätezertifikat bei der IoT Hub-Instanz authentifizieren kann. Sie benötigen die PFX-Version Ihres Zertifikats: `mydevice.pfx`.

### <a name="step-6---cleanup"></a>Schritt 6: Bereinigen

Öffnen Sie im Startmenü die Option **Computerzertifikate verwalten**, und navigieren Sie zu **Zertifikate – Lokaler Computer > Eigene Zertifikate**. Entfernen Sie alle von der Azure IoT-Zertifizierungsstelle „TestOnly“ ausgestellten Zertifikate. Entfernen Sie auf ähnliche Weise die entsprechenden Zertifikate unter **Vertrauenswürdige Stammzertifizierungsstelle > Zwischenzertifizierungsstellen > Zertifikate**.

## <a name="bash-scripts"></a>Bash-Skripts

### <a name="step-1---setup"></a>Schritt 1: Setup

1. Starten Sie Bash.

1. Navigieren Sie zu dem Verzeichnis, in dem Sie arbeiten möchten. Alle Dateien werden in diesem Verzeichnis erstellt.

1. Kopieren Sie `*.cnf` und `*.sh` in Ihr Arbeitsverzeichnis.

### <a name="step-2---create-certificates"></a>Schritt 2: Erstellen von Zertifikaten

1. Führen Sie aus `./certGen.sh create_root_and_intermediate`. Dadurch werden die folgenden Dateien im Verzeichnis **certs** erstellt:

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Navigieren Sie zu Ihrer IoT Hub-Instanz und anschließend zu **Zertifikate**.

1. Wählen Sie **Hinzufügen**.

1. Geben Sie einen Anzeigenamen für das Zertifizierungsstellenzertifikat ein.

1. Laden Sie nur das Zertifizierungsstellenzertifikat in Ihre IoT Hub-Instanz hoch. Der Name des Zertifikats lautet `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Wählen Sie **Speichern** aus.

### <a name="step-3---prove-possession"></a>Schritt 3: Nachweisen des Besitzes

1. Wählen Sie das neue Zertifizierungsstellenzertifikat aus, das Sie im vorherigen Schritt erstellt haben.

1. Wählen Sie im Dialogfeld **Zertifikatdetails** die Option **Prüfcode generieren** aus. Weitere Informationen finden Sie unter [Tutorial: Nachweisen des Besitzes eines ZS-Zertifikats](tutorial-x509-prove-possession.md).

1. Erstellen Sie ein Zertifikat, das den Prüfcode enthält. Wenn der Prüfcode beispielsweise `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` lautet, führen Sie Folgendes aus, um ein neues Zertifikat in Ihrem Arbeitsverzeichnis namens `verification-code.cert.pem` mit dem Antragsteller `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` zu erstellen.

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Laden Sie im Dialogfeld **Zertifikatdetails** das Zertifikat in Ihre IoT Hub-Instanz hoch.

1. Wählen Sie **Überprüfen** aus.

### <a name="step-4---create-a-new-device"></a>Schritt 4: Erstellen eines neuen Geräts

Erstellen Sie ein Gerät für Ihre IoT Hub-Instanz:

1. Navigieren Sie in Ihrer IoT Hub-Instanz zum Abschnitt „IoT-Geräte“.

1. Fügen Sie ein neues Gerät mit der ID `mydevice` hinzu.

1. Wählen Sie für die Authentifizierung die Option **X.509, durch Zertifizierungsstelle signiert** aus.

1. Führen Sie `./certGen.sh create_device_certificate mydevice` aus, um ein neues Gerätezertifikat zu erstellen. Dadurch werden zwei Dateien mit den Namen `new-device.cert.pem` und `new-device.cert.pfx` in Ihrem Arbeitsverzeichnis erstellt.

### <a name="step-5---test-your-device-certificate"></a>Schritt 5: Testen des Gerätezertifikats

Navigieren Sie zu [Testen der Zertifikatauthentifizierung](tutorial-x509-test-certificate.md), um festzustellen, ob sich Ihr Gerätezertifikat bei der IoT Hub-Instanz authentifizieren kann. Sie benötigen die PFX-Version Ihres Zertifikats: `new-device.cert.pfx`.

### <a name="step-6---cleanup"></a>Schritt 6: Bereinigen

Das Bash-Skript erstellt einfach Zertifikate in Ihrem Arbeitsverzeichnis. Löschen Sie diese daher, wenn Sie die Tests abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

Navigieren Sie zum Testen Ihres Zertifikats zu [Testen der Zertifikatauthentifizierung](tutorial-x509-test-certificate.md), um festzustellen, ob Ihr Gerät mit dem Zertifikat bei der IoT Hub-Instanz authentifiziert werden kann.
