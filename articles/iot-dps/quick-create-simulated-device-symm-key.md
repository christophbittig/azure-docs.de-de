---
title: 'Schnellstart: Bereitstellen eines simulierten Geräts mit symmetrischem Schlüssel für Microsoft Azure IoT Hub'
description: Es wird beschrieben, wie Sie ein Gerät bereitstellen, bei dem für die Authentifizierung bei Azure IoT Hub Device Provisioning Service (DPS) ein symmetrischer Schlüssel verwendet wird.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: 43c6dc8e9d8a6438468c44fd2b8cc31d04a92c2f
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276859"
---
# <a name="quickstart-provision-a-simulated-symmetric-key-device"></a>Schnellstart: Bereitstellen eines simulierten Geräts mit symmetrischem Schlüssel

In dieser Schnellstartanleitung erstellen Sie ein simuliertes Gerät auf Ihrem Windows-Computer. Das simulierte Gerät wird für die Verwendung des Mechanismus [Nachweis des symmetrischen Schlüssels](concepts-symmetric-key-attestation.md) für die Authentifizierung konfiguriert. Nachdem Sie Ihr Gerät konfiguriert haben, stellen Sie es mithilfe des Azure IoT Hub Device Provisioning Service für Ihren IoT-Hub bereit.

Wenn Sie mit dem Prozess der Bereitstellung noch nicht vertraut sind, sollten Sie zunächst die Übersicht zur [Bereitstellung](about-iot-dps.md#provisioning-process) lesen.

In dieser Schnellstartanleitung wird die Vorgehensweise anhand einer Projektmappe für eine Windows-Arbeitsstation veranschaulicht. Sie können die Schritte aber auch unter Linux ausführen. Ein Beispiel für Linux finden Sie unter [Bereitstellen für Mehrinstanzenfähigkeit](how-to-provision-multitenant.md).

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

* Führen Sie die Schritte im Artikel [Einrichten des IoT Hub Device Provisioning Service im Azure-Portal](./quick-setup-auto-provision.md) aus.
::: zone pivot="programming-language-ansi-c"

* Installieren Sie bei Verwendung einer Windows-Entwicklungsumgebung [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 mit der aktivierten Workload [„Desktopentwicklung mit C++“](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development). Visual Studio 2015 und Visual Studio 2017 werden ebenfalls unterstützt. Informationen zu Linux oder macOS finden Sie in der SDK-Dokumentation im entsprechenden Abschnitt unter [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md).

::: zone-end

::: zone pivot="programming-language-csharp"

* Installieren Sie das [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download) oder höher auf Ihrem Windows-Computer. Sie können den folgenden Befehl ausführen, um Ihre Version zu überprüfen:

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* Installieren Sie [Node.js v4.0+](https://nodejs.org).

::: zone-end

::: zone pivot="programming-language-python"

* Installieren Sie [Python 3.7](https://www.python.org/downloads/) oder höher auf Ihrem Windows-Computer. Durch Ausführen von `python --version` können Sie überprüfen, welche Python-Version verwendet wird.

::: zone-end

::: zone pivot="programming-language-java"

* Installieren Sie das [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) oder höher auf Ihrem Computer.

* Laden Sie [Maven](https://maven.apache.org/install.html) herunter, und installieren Sie es.

::: zone-end

* Installieren Sie die aktuelle Version von [Git](https://git-scm.com/download/). Stellen Sie sicher, dass Git den Umgebungsvariablen hinzugefügt wurde, auf die das Befehlsfenster Zugriff hat. Unter den [Git-Clienttools von Software Freedom Conservancy](https://git-scm.com/download/) finden Sie die neueste Version der zu installierenden `git`-Tools. Hierzu zählt auch die Befehlszeilen-App *Git Bash*, über die Sie mit Ihrem lokalen Git-Repository interagieren können.


<a id="setupdevbox"></a>

## <a name="prepare-your-development-environment"></a>Vorbereiten Ihrer Entwicklungsumgebung

::: zone pivot="programming-language-ansi-c"

In diesem Abschnitt bereiten Sie eine Entwicklungsumgebung vor, die zum Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet wird. Mit dem Beispielcode wird versucht, das Gerät während der Startsequenz des Geräts bereitzustellen.

1. Laden Sie das neueste [CMake-Buildsystem](https://cmake.org/download/) herunter.

    >[!IMPORTANT]
    >Vergewissern Sie sich **vor** Beginn der Installation von `CMake`, dass die erforderlichen Visual Studio-Komponenten (Visual Studio und die Workload „Desktopentwicklung mit C++“) auf dem Computer installiert sind. Sobald die Voraussetzungen erfüllt sind und der Download überprüft wurde, installieren Sie das CMake-Buildsystem. Beachten Sie auch, dass ältere Versionen des CMake-Buildsystems die in diesem Artikel verwendete Projektmappendatei nicht generieren können. Stellen Sie sicher, dass Sie die neueste Version von CMake verwenden.

2. Öffnen Sie einen Webbrowser, und wechseln Sie zur [Releaseseite für das Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

3. Wählen Sie oben auf der Seite die Registerkarte **Tags** aus.

4. Kopieren Sie den Tagnamen für das aktuelle Release des Azure IoT C SDK.

5. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie die folgenden Befehle aus, um die neueste Version des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) im GitHub-Repository zu klonen (`<release-tag>` durch das Tag ersetzen, das Sie im vorherigen Schritt kopiert haben).

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Dieser Vorgang kann mehrere Minuten dauern.

6. Führen Sie nach Abschluss des Vorgangs die folgenden Befehle aus dem Verzeichnis `azure-iot-sdk-c` aus:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

7. Im Codebeispiel wird ein symmetrischer Schlüssel verwendet, um den erforderlichen Nachweis zu erbringen. Erstellen Sie mit dem folgenden Befehl eine spezifische SDK-Version für Ihre Entwicklungsclientplattform, die den Client für die Gerätebereitstellung enthält.

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    >[!TIP]
    >Falls `cmake` Ihren C++-Compiler nicht findet, treten beim Ausführen des obigen Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](/dotnet/framework/tools/developer-command-prompt-for-vs) aus.

8. Nach der erfolgreichen Erstellung ähneln die letzten Ausgabezeilen der folgenden Ausgabe:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 16 2019
    -- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19042.
    -- The C compiler identification is MSVC 19.29.30040.0
    -- The CXX compiler identification is MSVC 19.29.30040.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
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

2. Klonen Sie mithilfe des folgenden Befehls das GitHub-Repository mit dem [Azure IoT SDK für Node.js](https://github.com/Azure/azure-iot-sdk-node.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Öffnen Sie eine Git CMD- oder Git Bash-Befehlszeilenumgebung.

2. Klonen Sie mit dem folgenden Befehl das GitHub-Repository mit dem [Azure IoT SDK für Python](https://github.com/Azure/azure-iot-sdk-python.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

3. Navigieren Sie zum Verzeichnis `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios`, in dem sich die Beispieldatei _provision_symmetric_key.py_ befindet.

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. Installieren Sie die Bibliothek _azure-iot-device_, indem Sie den folgenden Befehl ausführen.

    ```console
    pip install azure-iot-device
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Öffnen Sie eine Git CMD- oder Git Bash-Befehlszeilenumgebung.

2. Klonen Sie mit dem folgenden Befehl das GitHub-Repository mit dem [Azure IoT SDK für Java](https://github.com/Azure/azure-iot-sdk-java.git):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. Navigieren Sie zum Stammverzeichnis `azure-iot-sdk-java`, und erstellen Sie das Projekt, um alle erforderlichen Pakete herunterzuladen.

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. Installieren Sie die Bibliothek _azure-iot-device_, indem Sie den folgenden Befehl ausführen.

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

::: zone-end

## <a name="create-a-device-enrollment"></a>Erstellen einer Geräteregistrierung

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten unterstützt:

* [Registrierungsgruppen:](concepts-service.md#enrollment-group) Für die Registrierung mehrerer verbundener Geräte
* [Individuelle Registrierung:](concepts-service.md#individual-enrollment) Für die Registrierung eines einzelnen Geräts

In diesem Artikel wird veranschaulicht, wie eine individuelle Registrierung für ein einzelnes Gerät, das mit einem IoT-Hub bereitgestellt werden soll, durchgeführt wird.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie Ihre Device Provisioning Service-Instanz aus.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie oben auf der Seite die Option **+ Individuelle Registrierung hinzufügen** aus.

6. Geben Sie auf der Seite **Registrierung hinzufügen** die folgenden Informationen ein.

   * **Mechanismus:** Wählen Sie *Symmetrischer Schlüssel* als Mechanismus für den Nachweis der Identität aus.

   * **Schlüssel automatisch generieren:** Aktivieren Sie dieses Kontrollkästchen.

   * **Registrierungs-ID**: Geben Sie eine Registrierungs-ID ein, um die Registrierung zu identifizieren. Verwenden Sie nur Kleinbuchstaben und Bindestriche („-“). Beispiel: *symm-key-device-007*.

   * **IoT Hub-Geräte-ID**: Geben Sie einen Gerätebezeichner ein.

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png" alt-text="Eingeben der Informationen für die Geräteregistrierung":::

    ::: zone-end

    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-csharp.png" alt-text="Eingeben der Informationen für die Geräteregistrierung (C#)":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-nodejs.png" alt-text="Eingeben der Informationen für die Geräteregistrierung (Node.js)":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-python.png" alt-text="Eingeben der Informationen für die Geräteregistrierung (Python)":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-java.png" alt-text="Eingeben der Informationen für die Geräteregistrierung (Java)":::

    ::: zone-end

7. Wählen Sie **Speichern** aus. Es werden ein **Primärschlüssel** und ein **Sekundärschlüssel** generiert und dem Registrierungseintrag hinzugefügt. Anschließend wird für Sie wieder die Seite **Registrierungen verwalten** angezeigt.

8. Wählen Sie die Registerkarte **Individuelle Registrierungen** aus, um Ihre Registrierungen für das simulierte Gerät mit symmetrischem Schlüssel anzuzeigen.

9. Wählen Sie Ihr Gerät (*symm-key-device-007*) aus.

10. Kopieren Sie den Wert des generierten **Primärschlüssels**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/copy-device-enrollment-primary-key.png" alt-text="Kopieren des Primärschlüssels der Geräteregistrierung":::

<a id="firstbootsequence"></a>

## <a name="prepare-and-run-the-device-provisioning-code"></a>Vorbereiten und Ausführen des Gerätebereitstellungscodes

::: zone pivot="programming-language-ansi-c"

In diesem Abschnitt aktualisieren Sie den Beispielcode für das Gerät, um die Startsequenz des Geräts an Ihre Device Provisioning Service-Instanz zu senden. Diese Startsequenz bewirkt, dass das Gerät erkannt, authentifiziert und einem IoT-Hub zugewiesen wird, der mit der Device Provisioning Service-Instanz verknüpft ist.

Mit dem Beispielcode für die Bereitstellung werden nacheinander die folgenden Aufgaben durchgeführt:

1. Authentifiziert Ihr Gerät mit den folgenden drei Parametern bei Ihrer Device Provisioning-Ressource:

    * ID-Bereich Ihrer Device Provisioning Service-Instanz
    * Registrierungs-ID für Ihre Geräteregistrierung
    * Primärer symmetrischer Schlüssel für Ihre Geräteregistrierung

2. Weist das Gerät dem IoT-Hub zu, der bereits mit Ihrer Device Provisioning Service-Instanz verknüpft ist.

Gehen Sie wie folgt vor, um das Bereitstellungsbeispiel mit Ihren Geräteinformationen zu aktualisieren und auszuführen:

1. Wählen Sie im Hauptmenü Ihrer Device Provisioning Service-Instanz die Option **Übersicht** aus.

2. Kopieren Sie den Wert von **ID-Bereich**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Extrahieren von Informationen zum Device Provisioning Service-Endpunkt":::

3. Öffnen Sie in Visual Studio die Projektmappendatei *azure_iot_sdks.sln*, die zuvor durch das Ausführen von CMake generiert wurde. Die Projektmappendatei befindet sich am folgenden Speicherort:

    ```output

    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln

    ```

    >[!TIP]
    >Wurde die Datei nicht in Ihrem CMake-Verzeichnis erstellt, vergewissern Sie sich, dass Sie eine aktuelle Version des CMake-Buildsystems verwendet haben.

4. Navigieren Sie im Visual Studio-Fenster *Projektmappen-Explorer* zum Ordner **Provision\_Samples**. Erweitern Sie das Beispielprojekt mit dem Namen **prov\_dev\_client\_sample**. Erweitern Sie **Quelldateien**, und öffnen Sie **prov\_dev\_client\_sample.c**.

5. Suchen Sie die Konstante `id_scope`, und ersetzen Sie den Wert durch Ihren **ID-Bereich**-Wert, den Sie zuvor kopiert haben.

    ```c
    static const char* id_scope = "0ne00002193";
    ```

6. Suchen Sie die Definition für die Funktion `main()` in der gleichen Datei. Stellen Sie sicher, dass die Variable `hsm_type` wie unten dargestellt auf `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` festgelegt ist:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

7. Suchen Sie in **prov\_dev\_client\_sample.c** den auskommentierten Aufruf von `prov_dev_set_symmetric_key_info()`.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Heben Sie die Auskommentierung für den Funktionsaufruf auf, und ersetzen Sie die Platzhalterwerte (einschließlich der spitzen Klammern) durch die oben kopierte Registrierungs-ID und den Primärschlüsselwert.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```

8. Speichern Sie die Datei .

9. Klicken Sie mit der rechten Maustaste auf das Projekt **prov\_dev\_client\_sample**, und wählen Sie **Als Startprojekt festlegen** aus.

10. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Wählen Sie in der Aufforderung zum erneuten Erstellen des Projekts **Ja** aus, um das Projekt vor der Ausführung neu zu erstellen.

    Die folgende Beispielausgabe zeigt die erfolgreiche Verbindungsherstellung des Geräts mit der Provisioning Service-Instanz, um einem IoT-Hub zugewiesen zu werden:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

Mit dem Beispielcode für die Bereitstellung werden die folgenden Aufgaben durchgeführt:

1. Authentifiziert Ihr Gerät mit den folgenden drei Parametern bei Ihrer Device Provisioning-Ressource:

    * ID-Bereich Ihrer Device Provisioning Service-Instanz
    * Registrierungs-ID für Ihre Geräteregistrierung
    * Primärer symmetrischer Schlüssel für Ihre Geräteregistrierung

2. Weist das Gerät dem IoT-Hub zu, der bereits mit Ihrer Device Provisioning Service-Instanz verknüpft ist.

3. Sendet eine Testtelemetrienachricht an den IoT-Hub.

Gehen Sie wie folgt vor, um das Bereitstellungsbeispiel mit Ihren Geräteinformationen zu aktualisieren und auszuführen:

1. Wählen Sie im Hauptmenü Ihrer Device Provisioning Service-Instanz die Option **Übersicht** aus.

2. Kopieren Sie den Wert von **ID-Bereich**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Extrahieren von Informationen zum Device Provisioning Service-Endpunkt":::

3. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie im geklonten Beispielrepository zu *SymmetricKeySample*:

    ```cmd
    cd azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample
    ```

4. Öffnen Sie im Ordner *SymmetricKeySample* die Datei *Parameters.cs* in einem Text-Editor. Diese Datei enthält die vom Beispiel unterstützten Parameter. Beim Ausführen des Beispiels werden nur die ersten drei erforderlichen Parameter in diesem Artikel verwendet. Sehen Sie sich den Code in dieser Datei an. Es sind keine Änderungen erforderlich.

    | Parameter                         | Erforderlich | Beschreibung     |
    | :-------------------------------- | :------- | :-------------- |
    | `--s` oder `--IdScope`              | Richtig     | Der ID-Bereich der DPS-Instanz |
    | `--i` oder `--Id`                   | Richtig     | Die Registrierungs-ID bei Verwendung der individuellen Registrierung oder die gewünschte Geräte-ID bei Verwendung der Gruppenregistrierung |
    | `--p` oder `--PrimaryKey`           | Richtig     | Der Primärschlüssel der individuellen Registrierung oder Gruppenregistrierung. |
    | `--e` oder `--EnrollmentType`       | Falsch    | Der Typ der Registrierung: `Individual` oder `Group`. Der Standardwert lautet `Individual`. |
    | `--g` oder `--GlobalDeviceEndpoint` | Falsch    | Der globale Endpunkt, mit dem Geräte eine Verbindung herstellen sollen. Der Standardwert lautet `global.azure-devices-provisioning.net`. |
    | `--t` oder `--TransportType`        | Falsch    | Der Transporttyp, der für die Kommunikation mit der Gerätebereitstellungsinstanz verwendet werden soll. Wird standardmäßig auf `Mqtt` festgelegt. Mögliche Werte: `Mqtt`, `Mqtt_WebSocket_Only`, `Mqtt_Tcp_Only`, `Amqp`, `Amqp_WebSocket_Only`, `Amqp_Tcp_only` und `Http1`|

5. Öffnen Sie im Ordner *SymmetricKeySample* die Datei *ProvisioningDeviceClientSample.cs* in einem Text-Editor. Diese Datei verdeutlicht, wie die [SecurityProviderSymmetricKey](/dotnet/api/microsoft.azure.devices.shared.securityprovidersymmetrickey?view=azure-dotnet&preserve-view=true)-Klasse zusammen mit der [ProvisioningDeviceClient](/dotnet/api/microsoft.azure.devices.provisioning.client.provisioningdeviceclient?view=azure-dotnet&preserve-view=true)-Klasse verwendet wird, um Ihr simuliertes Gerät mit symmetrischem Schlüssel bereitzustellen. Sehen Sie sich den Code in dieser Datei an.  Es sind keine Änderungen erforderlich.

6. Erstellen Sie den Beispielcode, und führen Sie ihn aus, indem Sie den folgenden Befehl verwenden, nachdem Sie die drei Beispielparameter ersetzt haben (`<id-scope>` durch den ID-Bereich Ihrer Device Provisioning Service-Instanz, `<registration-id>` durch die Registrierungs-ID Ihres Geräts und `<primarykey>` durch den Primärschlüssel Ihres Geräts).

    ```console
    dotnet run --s <id-scope> --i <registration-id> --p <primarykey>
    ```

7. Nun sollte in etwa die folgende Ausgabe angezeigt werden. Die Zeichenfolge „TestMessage“ wird als Testnachricht an den Hub gesendet.

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

Mit dem Beispielcode für die Bereitstellung werden nacheinander die folgenden Aufgaben durchgeführt:

1. Authentifiziert Ihr Gerät mit den folgenden vier Parametern bei Ihrer Device Provisioning-Ressource:
    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Weist das Gerät dem IoT-Hub zu, der bereits mit Ihrer Device Provisioning Service-Instanz verknüpft ist.

3. Sendet eine Testtelemetrienachricht an den IoT-Hub.

Gehen Sie wie folgt vor, um das Bereitstellungsbeispiel mit Ihren Geräteinformationen zu aktualisieren und auszuführen:

1. Wählen Sie im Hauptmenü Ihrer Device Provisioning Service-Instanz die Option **Übersicht** aus.

2. Kopieren Sie die Werte für **ID-Bereich** und **Dienstendpunkt**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extrahieren von Informationen zum Device Provisioning Service-Endpunkt":::

3. Öffnen Sie eine Eingabeaufforderung für die Ausführung von Node.js-Befehlen, und navigieren Sie zum folgenden Verzeichnis:

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

4. Öffnen Sie im Ordner *provisioning/device/samples* die Datei *register_symkey.js*, und überprüfen Sie den Code. Beachten Sie, dass mit dem Beispielcode benutzerdefinierte Nutzdaten festgelegt werden:

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    Sie können diesen Code auskommentieren, da er für diese Schnellstartanleitung nicht benötigt wird. Benutzerdefinierte Nutzdaten sind erforderlich, falls Sie eine benutzerdefinierte Zuordnungsfunktion für die Zuweisung Ihres Geräts zu einer IoT Hub-Instanz verwenden möchten. Weitere Informationen finden Sie im [Tutorial: Verwenden benutzerdefinierter Zuordnungsrichtlinien](tutorial-custom-allocation-policies.md).

     Mit der Methode `provisioningClient.register()` wird die Registrierung Ihres Geräts versucht.

    Es sind keine weiteren Änderungen erforderlich.

5. An der Eingabeaufforderung legen Sie nun die folgenden Umgebungsvariablen fest (ersetzen Sie `<id-scope>` durch den ID-Bereich Ihrer Device Provisioning Service-Instanz, `<registration-id>` durch die Registrierungs-ID Ihres Geräts, `<primarykey>` durch den Primärschlüssel Ihres Geräts und `<provisioning-host>` durch die Dienstendpunkt-URL Ihrer Device Provisioning Service-Instanz):

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

6. Erstellen Sie den Beispielcode, und führen Sie ihn aus, indem Sie die folgenden Befehle verwenden:

   ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

7. Nun sollte in etwa die folgende Ausgabe angezeigt werden. Die Zeichenfolge „Hallo Welt“ wird als Testnachricht an den Hub gesendet.

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-python"

Mit dem Beispielcode für die Bereitstellung werden nacheinander die folgenden Aufgaben durchgeführt:

1. Authentifiziert Ihr Gerät mit den folgenden vier Parametern bei Ihrer Device Provisioning-Ressource:

    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Weist das Gerät dem IoT-Hub zu, der bereits mit Ihrer Device Provisioning Service-Instanz verknüpft ist.

3. Sendet eine Testtelemetrienachricht an den IoT-Hub.

Gehen Sie wie folgt vor, um das Bereitstellungsbeispiel mit Ihren Geräteinformationen zu aktualisieren und auszuführen:

1. Wählen Sie im Hauptmenü Ihrer Device Provisioning Service-Instanz die Option **Übersicht** aus.

2. Kopieren Sie die Werte für **ID-Bereich** und **Dienstendpunkt**.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extrahieren von Informationen zum Device Provisioning Service-Endpunkt":::

3. An der Eingabeaufforderung legen Sie nun die folgenden Umgebungsvariablen fest (ersetzen Sie `<id-scope>` durch den ID-Bereich Ihrer Device Provisioning Service-Instanz, `<registration-id>` durch die Registrierungs-ID Ihres Geräts, `<primarykey>` durch den Primärschlüssel Ihres Geräts und `<provisioning-host>` durch die Dienstendpunkt-URL Ihrer Device Provisioning Service-Instanz):

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

4. Navigieren Sie im Eingabeaufforderungsfenster zum folgenden Verzeichnis:

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

5. Führen Sie den Python-Beispielcode in der Datei *_provision_symmetric_key.py_* aus.

    ```console
    python provision_symmetric_key.py
    ```

6. Nun sollte in etwa die folgende Ausgabe angezeigt werden. Zu Testzwecken werden auch einige Beispielnachrichten mit Telemetriedaten zur Windgeschwindigkeit an den Hub gesendet.

     ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```

::: zone-end

::: zone pivot="programming-language-java"

Mit dem Beispielcode für die Bereitstellung werden nacheinander die folgenden Aufgaben durchgeführt:

1. Authentifiziert Ihr Gerät mit den folgenden vier Parametern bei Ihrer Device Provisioning-Ressource:

    * `GLOBAL_ENDPOINT`
    * `SCOPE_ID`
    * `REGISTRATION_ID`
    * `SYMMETRIC_KEY`

2. Weist das Gerät dem IoT-Hub zu, der bereits mit Ihrer Device Provisioning Service-Instanz verknüpft ist.

3. Sendet eine Testtelemetrienachricht an den IoT-Hub.

Gehen Sie wie folgt vor, um das Bereitstellungsbeispiel mit Ihren Geräteinformationen zu aktualisieren und auszuführen:

1. Wählen Sie im Hauptmenü Ihrer Device Provisioning Service-Instanz die Option **Übersicht** aus.

2. Kopieren Sie die Werte für **ID-Bereich** und **Dienstendpunkt**. Dies sind Ihre Werte für `SCOPE_ID` und `GLOBAL_ENDPOINT`.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Extrahieren von Informationen zum Device Provisioning Service-Endpunkt":::

3. Öffnen Sie den Beispielcode für das Java-Gerät zur Bearbeitung. Der vollständige Pfad zum Beispielcode für das Gerät lautet:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

4. Fügen Sie jeweils den Wert der folgenden Variablen für Ihre DPS-Instanz und die Geräteregistrierung ein (ersetzen Sie `<id-scope>` durch den ID-Bereich Ihrer Device Provisioning Service-Instanz, `<registration-id>` durch die Registrierungs-ID Ihres Geräts, `<primarykey>` durch den Primärschlüssel Ihres Geräts und `<provisioning-host>` durch die Dienstendpunkt-URL Ihrer Device Provisioning Service-Instanz):

    ```java
    private static final String SCOPE_ID = "<id-scope>";
    private static final String GLOBAL_ENDPOINT = "<provisioning-host>";
    private static final String SYMMETRIC_KEY = "<primarykey>";
    private static final String REGISTRATION_ID = "<registration-id>";
    ```

5. Öffnen Sie eine Eingabeaufforderung für den Buildvorgang. Navigieren Sie zum Projektordner mit dem Bereitstellungsbeispiel im Java-SDK-Repository.

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

6. Führen Sie den Buildvorgang für das Beispiel durch, und navigieren Sie dann zum Ordner `target`, um die erstellte `.jar`-Datei auszuführen.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

7. Nun sollte in etwa die folgende Ausgabe angezeigt werden.

     ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>Bestätigen der Registrierung der Gerätebereitstellung

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie den IoT-Hub aus, dem Ihr Gerät zugewiesen wurde.

4. Wählen Sie im Menü **Explorer** die Option **IoT-Geräte** aus.

5. Wenn Ihr Gerät erfolgreich bereitgestellt wurde, sollte die Geräte-ID in der Liste mit dem **Status** *Aktiviert* angezeigt werden. Wählen Sie oben auf der Seite **Aktualisieren** aus, falls Ihr Gerät nicht angezeigt wird.

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration.png" alt-text="Geräteregistrierung bei der IoT Hub-Instanz":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-csharp.png" alt-text="Beim IoT-Hub registriertes CSharp-Gerät":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-nodejs.png" alt-text="Beim IoT-Hub registriertes Node.js-Gerät":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-python.png" alt-text="Beim IoT-Hub registriertes Python-Gerät":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-java.png" alt-text="Beim IoT-Hub registriertes Java-Gerät":::

    ::: zone-end

> [!NOTE]
> Wenn Sie den *anfänglichen Gerätezwillingsstatus* im Registrierungseintrag für Ihr Gerät gegenüber dem Standardwert geändert haben, kann der gewünschte Zwillingsstatus vom Hub abgerufen werden, und es können entsprechende Aktionen durchgeführt werden. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Geräteclientbeispiel weiter verwenden und erkunden möchten, sollten Sie die in dieser Schnellstartanleitung erstellten Ressourcen nicht bereinigen. Falls Sie nicht fortfahren möchten, führen Sie die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieser Schnellstartanleitung erstellt wurden.

### <a name="delete-your-device-enrollment"></a>Löschen der Geräteregistrierung

1. Schließen Sie auf Ihrem Computer das Ausgabefenster des Geräteclientbeispiels.

2. Wählen Sie im Azure-Portal im linken Menü die Option **Alle Ressourcen** aus.

3. Wählen Sie Ihre Device Provisioning Service-Instanz aus.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie die Registerkarte **Individuelle Registrierungen** aus.

6. Aktivieren Sie das Kontrollkästchen neben der *REGISTRIERUNGS-ID* des Geräts, das Sie in dieser Schnellstartanleitung registriert haben.

7. Wählen Sie oben auf der Seite die Option **Löschen** aus.

### <a name="delete-your-device-registration-from-iot-hub"></a>Löschen Ihrer Geräteregistrierung aus IoT Hub

1. Wählen Sie im Azure-Portal im linken Menü die Option **Alle Ressourcen** aus.

2. Wählen Sie Ihren IoT Hub aus.

3. Wählen Sie im Menü **Explorer** die Option **IoT-Geräte** aus.

4. Aktivieren Sie das Kontrollkästchen neben der *GERÄTE-ID* des Geräts, das Sie in dieser Schnellstartanleitung registriert haben.

5. Wählen Sie oben auf der Seite die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Bereitstellen eines Geräts mit X.509-Zertifikat:

> [!div class="nextstepaction"]
> [Schnellstart: Bereitstellen eines simulierten X.509-Geräts mithilfe des Azure IoT C SDK](quick-create-simulated-device-x509.md)