---
title: 'Schnellstart: Bereitstellen eines simulierten Geräts mit X.509-Zertifikat in Microsoft Azure IoT Hub'
description: Erfahren Sie, wie Sie ein simuliertes Gerät bereitstellen, das sich mit einem X.509-Zertifikat beim Azure IoT Hub Device Provisioning Service authentifiziert.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/07/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: c0c3d486e2a886a49c51e7ed78ed935c1a1c5c22
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276695"
---
# <a name="quickstart-provision-an-x509-certificate-simulated-device"></a>Schnellstart: Bereitstellen eines simulierten Geräts mit X.509-Zertifikat

In dieser Schnellstartanleitung erstellen Sie ein simuliertes Gerät auf Ihrem Windows-Computer. Das simulierte Gerät wird für die Verwendung des Mechanismus des [X.509-Zertifikatnachweises](concepts-x509-attestation.md) für die Authentifizierung konfiguriert. Nachdem Sie Ihr Gerät konfiguriert haben, werden Sie es mithilfe des Azure IoT Hub Device Provisioning Service für Ihren IoT-Hub bereitstellen.

Wenn Sie mit dem Prozess der Bereitstellung noch nicht vertraut sind, lesen Sie zunächst die Übersicht zur [Bereitstellung](about-iot-dps.md#provisioning-process).  Vergewissern Sie sich außerdem, dass Sie die Schritte unter [Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md) ausgeführt haben, bevor Sie fortfahren.

In dieser Schnellstartanleitung wird eine Projektmappe für eine Windows-Arbeitsstation gezeigt. Sie können die Verfahren allerdings auch unter Linux ausführen. Ein Beispiel für Linux finden Sie unter [Bereitstellen für Mehrinstanzenfähigkeit](how-to-provision-multitenant.md).

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

* Führen Sie die Schritte im Artikel [Einrichten des IoT Hub Device Provisioning Service im Azure-Portal](./quick-setup-auto-provision.md) aus.

Die folgenden Voraussetzungen gelten für eine Windows-Entwicklungsumgebung. Informationen zu Linux oder macOS finden Sie in der SDK-Dokumentation im entsprechenden Abschnitt unter [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md).

::: zone pivot="programming-language-ansi-c"

* Installieren Sie [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) mit der aktivierten Workload [Desktopentwicklung mit C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development). Visual Studio 2015 und Visual Studio 2017 werden ebenfalls unterstützt. Informationen zu Linux oder macOS finden Sie in der SDK-Dokumentation im entsprechenden Abschnitt unter [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md).

::: zone-end

::: zone pivot="programming-language-csharp"

* Installieren Sie das [.NET Core 3.1 SDK oder höher](https://dotnet.microsoft.com/download) auf Ihrem Windows-Computer. Sie können den folgenden Befehl ausführen, um Ihre Version zu überprüfen:

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* Installieren Sie [Node.js v4.0 oder höher](https://nodejs.org) auf Ihrem Computer.

* Installieren Sie das Toolkit [OpenSSL](https://www.openssl.org/) auf Ihrem Computer, und fügen Sie es den Umgebungsvariablen hinzu, auf die das Befehlsfenster Zugriff hat. Diese Bibliothek kann entweder über die Quelle erstellt und installiert oder über einen [Drittanbieter](https://wiki.openssl.org/index.php/Binaries) (z.B. [hier](https://sourceforge.net/projects/openssl/)) heruntergeladen und installiert werden.

::: zone-end

::: zone pivot="programming-language-python"

* [Python 3.6 oder höher](https://www.python.org/downloads/) auf Ihrem Computer.

* Installieren Sie das Toolkit [OpenSSL](https://www.openssl.org/) auf Ihrem Computer, und fügen Sie es den Umgebungsvariablen hinzu, auf die das Befehlsfenster Zugriff hat. Diese Bibliothek kann entweder über die Quelle erstellt und installiert oder über einen [Drittanbieter](https://wiki.openssl.org/index.php/Binaries) (z.B. [hier](https://sourceforge.net/projects/openssl/)) heruntergeladen und installiert werden.

::: zone-end

::: zone pivot="programming-language-java"

* Installieren Sie das [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) oder höher auf Ihrem Computer.

* Laden Sie [Maven](https://maven.apache.org/install.html) herunter, und installieren Sie es.

::: zone-end

* Installieren Sie die aktuelle Version von [Git](https://git-scm.com/download/). Stellen Sie sicher, dass Git den Umgebungsvariablen hinzugefügt wurde, auf die das Befehlsfenster Zugriff hat. Unter den [Git-Clienttools von Software Freedom Conservancy](https://git-scm.com/download/) finden Sie die neueste Version der zu installierenden `git`-Tools. Hierzu zählt auch die Befehlszeilen-App *Git Bash*, über die Sie mit Ihrem lokalen Git-Repository interagieren können.

## <a name="prepare-your-development-environment"></a>Vorbereiten Ihrer Entwicklungsumgebung

::: zone pivot="programming-language-ansi-c"

In diesem Abschnitt bereiten Sie eine Entwicklungsumgebung vor, die zum Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet wird. Der Beispielcode versucht, das Gerät während der Startsequenz des Geräts bereitstellen.

1. Laden Sie das neueste [CMake-Buildsystem](https://cmake.org/download/) herunter.

    >[!IMPORTANT]
    >Vergewissern Sie sich **vor** Beginn der Installation von `CMake`, dass die Voraussetzungen für Visual Studio (Visual Studio und die Workload „Desktopentwicklung mit C++“) auf dem Computer installiert sind. Sobald die Voraussetzungen erfüllt sind und der Download überprüft wurde, installieren Sie das CMake-Buildsystem. Beachten Sie auch, dass ältere Versionen des CMake-Buildsystems die in diesem Artikel verwendete Projektmappendatei nicht generieren können. Stellen Sie sicher, dass Sie die neueste Version von CMake verwenden.

2. Öffnen Sie einen Webbrowser, und wechseln Sie zur [Releaseseite für das Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

3. Wählen Sie oben auf der Seite die Registerkarte **Tags** aus.

4. Kopieren Sie den Tagnamen für das aktuelle Release des Azure IoT C SDK.

5. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie die folgenden Befehle zum Klonen des aktuellen Releases des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-GitHub-Repositorys aus. (Ersetzen Sie `<release-tag>` durch das Tag, das Sie im vorherigen Schritt kopiert haben.)

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Dieser Vorgang kann mehrere Minuten dauern.

6. Wenn der Vorgang abgeschlossen ist, führen Sie die folgenden Befehle aus dem Verzeichnis `azure-iot-sdk-c` aus:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

7. Das Codebeispiel verwendet ein X.509-Zertifikat für den Nachweis mittels X.509-Authentifizierung. Erstellen Sie mithilfe des folgenden Befehls eine spezifische SDK-Version für Ihre Entwicklungsplattform, die den Gerätebereitstellungsclient enthält. Im `cmake`-Verzeichnis wird eine Visual Studio-Projektmappe für das simulierte Gerät generiert.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    >[!TIP]
    >Falls `cmake` Ihren C++-Compiler nicht findet, treten beim Ausführen des obigen Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](/dotnet/framework/tools/developer-command-prompt-for-vs) aus.

8. Nach erfolgreicher Erstellung ähneln die letzten Ausgabezeilen der folgenden Ausgabe:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Öffnen Sie eine Git CMD- oder Git Bash-Befehlszeilenumgebung.

2. Klonen Sie mithilfe des folgenden Befehls das GitHub-Repository mit den [Azure IoT-Beispielen für C#](https://github.com/Azure-Samples/azure-iot-samples-csharp):

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Öffnen Sie eine Git CMD- oder Git Bash-Befehlszeilenumgebung.

2. Klonen Sie mithilfe des folgenden Befehls das GitHub-Repository [Azure IoT Samples for Node.js](https://github.com/Azure/azure-iot-sdk-node.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Öffnen Sie eine Git CMD- oder Git Bash-Befehlszeilenumgebung.

2. Klonen Sie mithilfe des folgenden Befehls das GitHub-Repository [Azure IoT Samples for Python](https://github.com/Azure/azure-iot-sdk-node.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Öffnen Sie eine Git CMD- oder Git Bash-Befehlszeilenumgebung.

2. Klonen Sie mithilfe des folgenden Befehls das GitHub-Repository [Azure IoT Samples for Java](https://github.com/Azure/azure-iot-sdk-java.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. Navigieren Sie zum Stammverzeichnis „`azure-iot-sdk-`java“, und erstellen Sie das Projekt, um alle erforderlichen Pakete herunterzuladen.

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

4. Navigieren Sie zum Projekt für den Zertifikat-Generator, und erstellen Sie es.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

::: zone-end

## <a name="create-a-self-signed-x509-device-certificate"></a>Erstellen eines selbstsignierten X.509-Gerätezertifikats

In diesem Abschnitt verwenden Sie Beispielcode aus dem Azure IoT SDK, um ein selbstsigniertes X.509-Zertifikat zu erstellen. Dieses Zertifikat muss in Ihren Bereitstellungsdienst hochgeladen und vom Dienst überprüft werden.

> [!CAUTION]
> Verwenden Sie Zertifikate, die mit den SDK-Tools sind ausschließlich zur Verwendung für Tests in der Entwicklung erstellt wurden.
> Verwenden Sie diese Zertifikate nicht in der Produktionsumgebung.
> Die vom SDK generierten Zertifikate enthalten hartcodierte Kennwörter wie *1234* und laufen nach 30 Tagen ab.
> Informationen zum Abrufen von Zertifikaten, die für den Einsatz in der Produktion geeignet sind, finden Sie in der Azure IoT Hub-Dokumentation unter [Abrufen eines X.509-Zertifizierungsstellenzertifikats](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate).
>

::: zone pivot="programming-language-csharp"

Die Geräte-ID des IoT-Geräts ist der allgemeine Antragstellername des Zertifikats. Verwenden Sie unbedingt einen Antragstellernamen, der die [Anforderungen für Geräte-ID-Zeichenfolgen](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties) erfüllt.

::: zone-end

So erstellen Sie das X.509-Zertifikat:

::: zone pivot="programming-language-ansi-c"

### <a name="clone-the-azure-iot-c-sdk"></a>Klonen des Azure IoT C SDK

Das [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) enthält Testtools zum Erstellen einer X.509-Zertifikatkette, zum Hochladen eines Stamm- oder Zwischenzertifikats aus dieser Kette und zum Durchführen eines Eigentumsnachweises über den Dienst, um das Zertifikat zu überprüfen.

Wenn Sie das neueste Release des GitHub-Repositorys für das [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) bereits geklont haben, fahren Sie mit dem [nächsten Abschnitt](#create-a-test-certificate) fort.

1. Öffnen Sie einen Webbrowser, und wechseln Sie zur [Releaseseite für das Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

2. Kopieren Sie den Tagnamen für das aktuelle Release des Azure IoT C SDK.

3. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie die folgenden Befehle zum Klonen des aktuellen Releases des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-GitHub-Repositorys aus. (Ersetzen Sie `<release-tag>` durch das Tag, das Sie im vorherigen Schritt kopiert haben.)

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Dieser Vorgang kann mehrere Minuten dauern.

4. Die Testtools sollten sich jetzt im geklonten Repository unter *azure-iot-sdk-c/tools/CACertificates* befinden.

### <a name="create-a-test-certificate"></a>Erstellen eines Testzertifikats

Führen Sie die Schritte unter [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (Verwalten von Zertifizierungsstellen-Testzertifikaten für Beispiele und Tutorials) aus.

Zusätzlich zu den Tools im C SDK veranschaulicht das [Beispiel zum Überprüfen des Gruppenzertifikats](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) im *Microsoft Azure IoT SDK für .NET* das Ausführen eines Eigentumsnachweises in C# für ein vorhandenes X.509-Zwischen- oder -Stammzertifikat einer Zertifizierungsstelle.

::: zone-end

::: zone pivot="programming-language-csharp"

1. Wechseln Sie an einer PowerShell-Eingabeaufforderung zum Projektverzeichnis für das X.509-Gerätebereitstellungsbeispiel.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Der Beispielcode ist für die Verwendung von X.509-Zertifikaten konzipiert, die in einer kennwortgeschützten Datei im PKCS12-Format (`certificate.pfx`) gespeichert sind. Darüber hinaus benötigen Sie im weiteren Verlauf dieser Schnellstartanleitung eine Zertifikatdatei mit einem öffentlichen Schlüssel (`certificate.cer`), um eine individuelle Registrierung erstellen zu können. Führen Sie den folgenden Befehl aus, um das selbstsignierte Zertifikat und die dazugehörigen `.cer`- und `.pfx`-Dateien zu generieren:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. Das Skript fordert Sie zur Eingabe eines PFX-Kennworts auf. Merken Sie sich dieses Kennwort, da Sie es später zum Ausführen des Beispiels benötigen. Optional können Sie `certutil` ausführen, um das Zertifikat zu sichern und den Antragstellernamen zu überprüfen.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Öffnen Sie eine Eingabeaufforderung, wechseln Sie zum Skript für den Zertifikat-Generator, und erstellen Sie das Projekt:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

2. Erstellen Sie das _untergeordnete_ X.509-Zertifikat, indem Sie das Skript mit Ihrem eigenen Zertifikatnamen (_certificate-name_) ausführen. Der allgemeine Name des untergeordneten Zertifikats wird zur [Registrierungs-ID](./concepts-service.md#registration-id). Achten Sie daher darauf, dass Sie nur Kleinbuchstaben, Zahlen und Bindestriche verwenden.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Führen Sie an der Git Bash-Eingabeaufforderung den folgenden Befehl aus:

    # <a name="windows"></a>[Windows](#tab/windows)

    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > Der zusätzliche Schrägstrich für den Antragstellernamen (`//CN=Python-device-01`) ist nur erforderlich, um die Zeichenfolge für Git auf Windows-Plattformen mit einem Escapezeichen zu versehen.

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```

    ---

2. Wenn die Aufforderung **PEM-Passphrase eingeben:** angezeigt wird, verwenden Sie die Passphrase `1234`.

3. Wenn Sie zum **Bestätigen der PEM-Passphrase** aufgefordert werden, geben Sie die Passphrase `1234` erneut ein.

Eine Testzertifikatdatei (*python-device.pem*) und eine Datei für den privaten Schlüssel (*python-device.key.pem*) sollten in dem Verzeichnis generiert werden, in dem Sie den Befehl `openssl` ausgeführt haben.

::: zone-end

::: zone pivot="programming-language-java"

1. Wechseln Sie an der Eingabeaufforderung, die Sie in den vorherigen Schritten verwendet haben, zum Ordner `target`.

2. Führen Sie die JAR-Datei aus, die Sie im vorherigen Abschnitt erstellt haben.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

3. Beantworten Sie die Frage _Do you want to input common name_ (Möchten Sie einen allgemeinen Namen eingeben?) mit **N**. 

4. Kopieren Sie die Ausgabe von `Client Cert` (von *-----BEGIN CERTIFICATE-----* bis einschließlich *-----END CERTIFICATE-----* ) in die Zwischenablage.

   ![Generator für individuelles Zertifikat](./media/quick-create-simulated-device-x509/cert-generator-java.png)

5. Erstellen Sie eine Datei mit namens *_X509individual.pem_* auf dem Windows Computer.

6. Öffnen Sie *_X509individual.pem_* in einem Editor Ihrer Wahl, und kopieren Sie den Inhalt der Zwischenablage in diese Datei. 

7. Speichern Sie die Datei, und schließen Sie den Editor.

8. Beantworten Sie an der Eingabeaufforderung die Frage _Do you want to input Verification Code_ (Möchten Sie einen Prüfcode eingeben?) mit **N**, und lassen Sie die Programmausgabe für später geöffnet. Kopieren Sie die Werte `Client Cert` und `Client Cert Private Key`, um sie im nächsten Abschnitt zu verwenden.

::: zone-end

## <a name="create-a-device-enrollment"></a>Erstellen einer Geräteregistrierung

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:

* [Registrierungsgruppen:](concepts-service.md#enrollment-group) Für die Registrierung mehrerer verbundener Geräte
* [Individuelle Registrierung:](concepts-service.md#individual-enrollment) Für die Registrierung eines einzelnen Geräts

In diesem Artikel wird veranschaulicht, wie eine individuelle Registrierung für ein einzelnes Gerät, das mit einem IoT-Hub bereitgestellt werden soll, durchgeführt wird.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie Ihren Gerätebereitstellungsdienst aus.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie oben auf der Seite die Option **+ Individuelle Registrierung hinzufügen** aus.

::: zone pivot="programming-language-ansi-c"

6. Geben Sie auf der Seite **Registrierung hinzufügen** die folgenden Informationen ein.

    * **Mechanismus**: Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
    * **Primäres Zertifikat (PEM- oder CER-Datei)** : Wählen Sie mit der Option **Datei auswählen** die im vorherigen Abschnitt erstellte Zertifikatdatei *X509testcert.pem* aus.
    * **IoT Hub-Geräte-ID**: Geben Sie *test-docs-cert-device* ein, um dem Gerät eine ID zuzuweisen.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Hinzufügen des Geräts als individuelle Registrierung mit X.509-Nachweis.":::

::: zone-end

::: zone pivot="programming-language-csharp"

6. Geben Sie auf der Seite **Registrierung hinzufügen** die folgenden Informationen ein.

    * **Mechanismus**: Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
    * **Primäres Zertifikat (PEM- oder CER-Datei)** : Wählen Sie mit der Option **Datei auswählen** die im vorherigen Abschnitt erstellte Zertifikatdatei *certificate.cer* aus.
    * Lassen Sie das Feld **IoT Hub-Geräte-ID** leer. Bei der Bereitstellung Ihres Geräts wird die Geräte-ID auf den allgemeinen Namen (Common Name, CN) aus dem X.509-Zertifikat festgelegt (in diesem Fall: *iothubx509device1*). Dieser allgemeine Name wird auch für die Registrierungs-ID des Eintrags für die individuelle Registrierung verwendet.
    * Optional können Sie die folgenden Informationen angeben:
        * Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
        * Aktualisieren Sie **Initial device twin state** (Anfänglicher Gerätezwillingsstatus) mit der gewünschten Anfangskonfiguration für das Gerät.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Hinzufügen des Geräts als individuelle Registrierung mit X.509-Nachweis.":::

::: zone-end

::: zone pivot="programming-language-nodejs"

6. Geben Sie auf der Seite **Registrierung hinzufügen** die folgenden Informationen ein.

    * **Mechanismus**: Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
    * **Primäres Zertifikat (PEM- oder CER-Datei)** : Wählen Sie mit der Option **Datei auswählen** die im vorherigen Abschnitt erstellte Zertifikatdatei *{certificate-name}_cert.pem* aus.
    * Optional können Sie die folgenden Informationen angeben:
        * Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
        * Geben Sie eine eindeutige Geräte-ID ein. Achten Sie darauf, dass Sie beim Benennen Ihres Geräts keine sensiblen Daten angeben.
        * Aktualisieren Sie **Initial device twin state** (Anfänglicher Gerätezwillingsstatus) mit der gewünschten Anfangskonfiguration für das Gerät.
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Hinzufügen des Geräts als individuelle Registrierung mit X.509-Nachweis.":::

::: zone-end

::: zone pivot="programming-language-python"

6. Geben Sie auf der Seite **Registrierung hinzufügen** die folgenden Informationen ein.

    * **Mechanismus**: Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
    * **Primäres Zertifikat (PEM- oder CER-Datei)** : Wählen Sie mit der Option **Datei auswählen** die Zertifikatdatei *python-device.pem* aus, sofern Sie das zuvor erstellte Testzertifikat nutzen.
    * Optional können Sie die folgenden Informationen angeben:
        * Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
        * Aktualisieren Sie **Initial device twin state** (Anfänglicher Gerätezwillingsstatus) mit der gewünschten Anfangskonfiguration für das Gerät.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Hinzufügen des Geräts als individuelle Registrierung mit X.509-Nachweis.":::

::: zone-end

::: zone pivot="programming-language-java"

6. Geben Sie im Bereich **Registrierung hinzufügen** die folgenden Informationen ein:
   * Wählen Sie **X.509** als *Mechanismus* für den Nachweis der Identität.
   * Wählen Sie unter *Primary certificate .pem or .cer file* (PEM- oder CER-Datei des primären Zertifikats) die Option *Datei auswählen* und anschließend die in den vorherigen Schritten erstellte Zertifikatdatei *X509individual.pem* aus.  
   * Optional können Sie die folgenden Informationen angeben:
     * Wählen Sie einen IoT Hub aus, der mit Ihrem Bereitstellungsdienst verknüpft ist.
     * Geben Sie eine eindeutige Geräte-ID ein. Achten Sie darauf, dass Sie beim Benennen Ihres Geräts keine sensiblen Daten angeben. 
     * Aktualisieren Sie **Initial device twin state** (Anfänglicher Gerätezwillingsstatus) mit der gewünschten Anfangskonfiguration für das Gerät.
    
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="Hinzufügen des Geräts als individuelle Registrierung mit X.509-Nachweis.":::

::: zone-end
    
7. Wählen Sie **Speichern** aus. Sie werden zu **Registrierungen verwalten** zurückgeleitet.

8. Wählen Sie **Individuelle Registrierungen** aus. Ihr X.509-Registrierungseintrag sollte in der Registrierungstabelle angezeigt werden.

## <a name="prepare-and-run-the-device-provisioning-code"></a>Vorbereiten und Ausführen des Gerätebereitstellungscodes

::: zone pivot="programming-language-ansi-c"

In diesem Abschnitt aktualisieren Sie den Beispielcode, um die Startsequenz des Geräts an Ihre Device Provisioning Service-Instanz zu senden. Diese Startsequenz bewirkt, dass das Gerät, die erkannt und einem mit der Instanz des Device Provisioning-Diensts verknüpften IoT Hub zugewiesen wird.

1. Wählen Sie im Azure-Portal die Registerkarte **Übersicht** für Ihre Device Provisioning Service-Instanz.

2. Kopieren Sie den Wert **_ID-Bereich_**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="Kopieren des Werts „ID-Bereich“ aus dem Portal.":::

3. Navigieren Sie im Visual Studio-Fenster *Projektmappen-Explorer* zum Ordner **Provision\_Samples**. Erweitern Sie das Beispielprojekt mit dem Namen **prov\_dev\_client\_sample**. Erweitern Sie **Quelldateien**, und öffnen Sie **prov\_dev\_client\_sample.c**.

4. Suchen Sie die Konstante `id_scope`, und ersetzen Sie den Wert durch Ihren **ID-Bereich**-Wert, den Sie zuvor kopiert haben. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Suchen Sie die Definition für die Funktion `main()` in der gleichen Datei. Stellen Sie sicher, dass die Variable `hsm_type` wie unten dargestellt auf `SECURE_DEVICE_TYPE_X509` anstelle von `SECURE_DEVICE_TYPE_TPM` festgelegt ist.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Klicken Sie mit der rechten Maustaste auf das Projekt **prov\_dev\_client\_sample**, und wählen Sie **Als Startprojekt festlegen** aus.

7. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Wählen Sie in der Eingabeaufforderung zum Neuerstellen des Projekts **Ja**, um das Projekt vor der Ausführung neu zu erstellen.

    Die folgende Ausgabe ist ein Beispiel dafür, wie der Gerätebereitstellungsclient erfolgreich hochfährt und sich mit der Instanz des Bereitstellungsdiensts verbindet, um IoT Hub-Informationen zu erhalten und sich zu registrieren:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Wählen Sie im Azure-Portal die Registerkarte **Übersicht** für Ihre Device Provisioning Service-Instanz.

2. Kopieren Sie den Wert **_ID-Bereich_**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="Kopieren des Werts „ID-Bereich“ aus dem Portal.":::

3. Öffnen Sie ein Eingabeaufforderungsfenster.

4. Geben Sie den folgenden Befehl ein, um das Beispiel für die X.509-Gerätebereitstellung zu erstellen und auszuführen (ersetzen Sie den Wert für `<IDScope>` durch den ID-Bereich, den Sie im vorherigen Abschnitt kopiert haben). Die Zertifikatdatei wird standardmäßig unter *./certificate.pfx* erstellt, und Sie werden zur Eingabe des PFX-Kennworts aufgefordert. Geben Sie Ihr Kennwort ein.

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Wenn Sie alle Angaben als Parameter übergeben möchten, können Sie das folgende Beispielformat verwenden:

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```

5. Das Gerät stellt jetzt eine Verbindung mit DPS her und wird einer IoT Hub-Instanz zugewiesen. Danach sendet das Gerät eine Telemetriemeldung an den Hub.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Wählen Sie im Azure-Portal die Registerkarte **Übersicht** für Ihre Device Provisioning Service-Instanz. 

2. Kopieren Sie die Werte für **_ID-Bereich_** und **Globaler Geräteendpunkt**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Kopieren des Werts „ID-Bereich“ aus dem Portal.":::

3. Kopieren Sie Ihr _Zertifikat_ und den _Schlüssel_ in den Beispielordner.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

4. Navigieren Sie zum Gerätetestskript, und erstellen Sie das Projekt.

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

5. Bearbeiten Sie die Datei **register\_x509.js**, und nehmen Sie folgende Änderungen vor:

    * Ersetzen Sie `provisioning host` durch den **_Globalen Geräteendpunkt_** aus **Schritt 1** oben.
    * Ersetzen Sie `id scope` durch den **_ID-Bereich_** aus **Schritt 1** oben. 
    * Ersetzen Sie `registration id` durch die **_Registrierungs-ID_** aus dem vorherigen Abschnitt.
    * Ersetzen Sie `cert filename` und `key filename` durch die Dateien, die Sie oben in **Schritt 2** kopiert haben.

6. Speichern Sie die Datei .

7. Führen Sie das Skript aus, und überprüfen Sie, ob das Gerät erfolgreich bereitgestellt wurde.

    ```cmd/sh
    node register_x509.js
    ``` 

>[!TIP]
>Die Verwendung des [Node.js-Geräte-SDK für Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-node) ist eine einfache Möglichkeit zum Simulieren eines Geräts. Weitere Informationen finden Sie unter [Gerätekonzepte](./concepts-service.md).

::: zone-end

::: zone pivot="programming-language-python"

Das Python-Bereitstellungsbeispiel [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) befindet sich im Verzeichnis `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios`. In diesem Beispiel werden sechs Umgebungsvariablen für die Authentifizierung und Bereitstellung eines IoT-Geräts per DPS genutzt. Dies sind die folgenden Umgebungsvariablen:

| Variablenname              | BESCHREIBUNG                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Dieser Wert steht für den globalen Endpunkt für die Verbindungsherstellung mit Ihrer DPS-Ressource. |    
| `PROVISIONING_IDSCOPE`     |  Dieser Wert steht für den ID-Bereich für Ihre DPS-Ressource. |    
| `DPS_X509_REGISTRATION_ID` |  Dieser Wert ist die ID für Ihr Gerät. Er muss auch mit dem Antragstellernamen auf dem Gerätezertifikat übereinstimmen. |    
| `X509_CERT_FILE`           |  Der Dateiname Ihres Gerätezertifikats. |    
| `X509_KEY_FILE`            |  Der Name Ihrer Datei mit dem privaten Schlüssel für Ihr Gerätezertifikat. |
| `PASS_PHRASE`              |  Die Passphrase, die Sie zum Verschlüsseln des Zertifikats und der Datei für den privaten Schlüssel (`1234`) verwendet haben. |    

1. Wählen Sie im Azure-Portal die Registerkarte **Übersicht** für Ihre Device Provisioning Service-Instanz.

2. Kopieren Sie die Werte für **_ID-Bereich_** und **Globaler Geräteendpunkt**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Kopieren des Werts „ID-Bereich“ aus dem Portal.":::

3. Verwenden Sie an Ihrer Git Bash-Eingabeaufforderung die folgenden Befehle, um die Umgebungsvariablen für den globalen Geräteendpunkt und den ID-Bereich hinzuzufügen.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

4. Die Registrierungs-ID für das IoT-Gerät muss mit dem Antragstellernamen im Gerätezertifikat übereinstimmen. Wenn Sie ein selbstsigniertes Testzertifikat generiert haben, ist `Python-device-01` sowohl der Antragstellername als auch die Registrierungs-ID für das Gerät.

    Falls Sie bereits über ein Gerätezertifikat verfügen, können Sie mit `certutil` den allgemeinen Namen des für Ihr Gerät verwendeten Antragstellers überprüfen, wie im Folgenden gezeigt:

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

5. Legen Sie an der Git Bash-Eingabeaufforderung die Umgebungsvariable für die Registrierungs-ID wie folgt fest:

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

6. Legen Sie an der Git Bash-Eingabeaufforderung die Umgebungsvariablen für die Zertifikatdatei, die Datei mit dem privaten Schlüssel und die Passphrase fest.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

7. Sehen Sie sich den Code für [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) an. Wenn Sie nicht die **Python-Version 3.7** oder höher verwenden, führen Sie die [hier](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) beschriebene Codeänderung durch, um `asyncio.run(main())` zu ersetzen.

8. Speichern Sie die Änderungen.

9. Führen Sie das Beispiel aus. Im Beispiel wird eine Verbindung hergestellt und das Gerät auf einem Hub bereitgestellt, und anschließend werden einige Testnachrichten an den Hub gesendet.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Wählen Sie im Azure-Portal die Registerkarte **Übersicht** für Ihre Device Provisioning Service-Instanz.

2. Kopieren Sie die Werte für **_ID-Bereich_** und **Globaler Geräteendpunkt**.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="Kopieren des Werts „ID-Bereich“ aus dem Portal.":::

3. Öffnen Sie eine Eingabeaufforderung. Navigieren Sie zum Beispielprojektordner des Java SDK-Repositorys.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

4. Geben Sie in Ihrem Code den Bereitstellungsdienst und die X.509-Identitätsinformationen ein. Diese werden während der Bereitstellung als Nachweis für das simulierte Gerät verwendet, bevor das Gerät registriert wird:

   * Bearbeiten Sie die Datei `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, um die zuvor notierten Werte für den _ID-Bereich_ und den _globalen Endpunkt für den Bereitstellungsdienst_ einzufügen. Fügen Sie außerdem die im vorherigen Abschnitt notierten Werte für _Client Cert_ und _Client Cert Private Key_ ein.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   * Verwenden Sie beim Kopieren/Einfügen Ihres Zertifikats und Ihres privaten Schlüssels das folgende Format:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

5. Erstellen Sie das Beispiel, wechseln Sie dann zum Ordner `target`, und führen Sie die erstellte JAR-Datei aus.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>Bestätigen der Registrierung der Gerätebereitstellung

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den IoT-Hub aus, dem Ihr Gerät zugewiesen wurde.

4. Wählen Sie im Menü **Explorer** die Option **IoT-Geräte** aus.

5. Wenn Ihr Gerät erfolgreich bereitgestellt wurde, sollte die Geräte-ID in der Liste mit dem **Status** *aktiviert* angezeigt werden. Wenn Ihr Gerät nicht angezeigt wird, wählen Sie oben auf der Seite **Aktualisieren** aus.

   :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration.png" alt-text="Geräteregistrierung bei der IoT Hub-Instanz":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-csharp.png" alt-text="Beim IoT-Hub registriertes CSharp-Gerät":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-nodejs.png" alt-text="Beim IoT-Hub registriertes Node.js-Gerät":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-python.png" alt-text="Beim IoT-Hub registriertes Python-Gerät":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-java.png" alt-text="Beim IoT-Hub registriertes Java-Gerät":::

    ::: zone-end


::: zone pivot="programming-language-csharp,programming-language-nodejs,programming-language-python,programming-language-java"

>[!IMPORTANT]
>Wenn Sie den *anfänglichen Gerätezwillingsstatus* im Registrierungseintrag für Ihr Gerät gegenüber dem Standardwert geändert haben, kann der gewünschte Zwillingsstatus vom Hub abgerufen werden, und es können entsprechende Aktionen durchgeführt werden. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Geräteclientbeispiel weiter verwenden und erkunden möchten, bereinigen Sie die in dieser Schnellstartanleitung erstellten Ressourcen nicht. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieser Schnellstartanleitung erstellt wurden.

### <a name="delete-your-device-enrollment"></a>Löschen der Geräteregistrierung

1. Schließen Sie auf Ihrem Computer das Ausgabefenster des Geräteclientbeispiels.

2. Wählen Sie im Azure-Portal im linken Menü die Option **Alle Ressourcen** aus.

3. Wählen Sie Ihren Gerätebereitstellungsdienst aus.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie die Registerkarte **Individuelle Registrierungen** aus.

6. Aktivieren Sie das Kontrollkästchen neben der *REGISTRIERUNGS-ID* des Geräts, das Sie in dieser Schnellstartanleitung registriert haben.

7. Wählen Sie oben auf der Seite die Option **Löschen** aus.

### <a name="delete-your-device-registration-from-iot-hub"></a>Löschen Ihrer Geräteregistrierung aus IoT Hub

1. Wählen Sie im Azure-Portal im linken Menü die Option **Alle Ressourcen** aus.

2. Wählen Sie Ihren IoT Hub aus.

3. Wählen Sie im Menü **Explorer** die Option **IoT-Geräte** aus.

4. Aktivieren Sie das Kontrollkästchen neben der *GERÄTE-ID* des Geräts, das Sie in dieser Schnellstartanleitung registriert haben.

5. Wählen Sie oben auf der Seite die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit der folgenden Schnellstartanleitung fort, um zu lernen, wie Sie Ihr X.509-Gerät programmgesteuert registrieren:

> [!div class="nextstepaction"]
> [Azure-Schnellstart: Registrieren von X.509-Geräten bei Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509.md)