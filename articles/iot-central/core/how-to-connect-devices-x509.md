---
title: Verbinden von Geräten mit X.509-Zertifikaten in einer Azure IoT Central-Anwendung
description: Informationen zum Verbinden von Geräten mit X.509-Zertifikaten mithilfe des Node.js-Geräte-SDKs für IoT Central-Anwendung
author: dominicbetts
ms.author: dobett
ms.date: 06/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 308cd5186d97e87ff044db496809b04def41265b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667122"
---
# <a name="how-to-connect-devices-with-x509-certificates-to-iot-central-application"></a>Verbinden von Geräten mit X.509-Zertifikaten mit einer Azure IoT Central-Anwendung

IoT Central unterstützt sowohl SAS (Shared Access Signature) als auch X.509-Zertifikate zum Absichern der Kommunikation zwischen einem Gerät und Ihrer Anwendung. Im Tutorial [Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md) wird SAS verwendet. In diesem Artikel erfahren Sie, wie Sie das Codebeispiel so ändern können, dass X.509-Zertifikate verwendet werden. In Produktionsumgebungen werden X.509-Zertifikate empfohlen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure IoT Central](./concepts-get-connected.md).

In diesem Leitfaden werden zwei Möglichkeiten zur Verwendung von X.509-Zertifikaten gezeigt – [Gruppenregistrierungen](how-to-connect-devices-x509.md#use-group-enrollment), die normalerweise in einer Produktionsumgebung verwendet werden, und [individuelle Registrierungen](how-to-connect-devices-x509.md#use-individual-enrollment), die hilfreich bei Tests sind. In diesem Artikel wird auch das [Rollen von Gerätezertifikaten](#roll-x509-device-certificates) beschrieben, um die Konnektivität aufrechtzuerhalten, wenn die Zertifikate ablaufen.

Dieser Leitfaden baut auf den Beispielen im Tutorial [Erstellen und Verbinden einer Clientanwendung mit Ihrer Azure IoT Central-Anwendung](tutorial-connect-device.md) auf, die C#, Java, JavaScript und Python verwenden. Ein Beispiel, das die Programmiersprache C verwendet, finden Sie unter [Bereitstellen mehrerer X.509-Geräte mithilfe von Registrierungsgruppen](../../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md).

## <a name="prerequisites"></a>Voraussetzungen

Absolvieren Sie das Tutorial [Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md). Dies umfasst die Installation der Voraussetzungen für die Programmiersprache Ihrer Wahl.

In dieser Anleitung generieren Sie einige X.509-Testzertifikate. Um diese Zertifikate generieren zu können, benötigen Sie Folgendes:

- Einen Entwicklungscomputer, auf dem mindestens Version 6 von [Node.js](https://nodejs.org/) installiert ist. Sie können `node --version` in der Befehlszeile ausführen, um Ihre Version zu überprüfen. Bei den Anweisungen in diesem Tutorial wird davon ausgegangen, dass Sie den Befehl **node** an der Windows-Eingabeaufforderung ausführen. Node.js kann jedoch unter einer Reihe anderer Betriebssysteme verwendet werden.
- Eine lokale Kopie des GitHub-Repositorys mit dem [Microsoft Azure IoT SDK für Node.js](https://github.com/Azure/azure-iot-sdk-node), das die Skripte zum Generieren der X.509-Testzertifikate enthält. Laden Sie über den folgenden Link eine Kopie des Repositorys herunter: [ZIP herunterladen](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Entzippen Sie anschließend die Datei an einem geeigneten Speicherort auf Ihrem lokalen Computer.

## <a name="use-group-enrollment"></a>Verwenden der Gruppenregistrierung

Verwenden Sie X.509-Zertifikate bei einer Gruppenregistrierung in einer Produktionsumgebung. In einer Gruppenregistrierung fügen Sie Ihrer IoT Central-Anwendung ein X.509-Stamm- oder -Zwischenzertifikat hinzu. Geräte mit Blattzertifikaten, die vom Stamm- oder Zwischenzertifikat abgeleitet wurden, können eine Verbindung mit Ihrer Anwendung herstellen.

### <a name="generate-root-and-device-certificates"></a>Generieren von Stamm- und Gerätezertifikaten

In diesem Abschnitt verwenden Sie ein X.509-Zertifikat zum Verbinden eines Geräts mit einem vom Zertifikat der IoT Central-Registrierungsgruppe abgeleiteten Zertifikat.

> [!WARNING]
> Diese Methode zum Erstellen von X.509-Zertifikaten dient nur zu Testzwecken. Für eine Produktionsumgebung sollten Sie Ihren offiziellen, sicheren Mechanismus für Zertifikatgenerierung verwenden.

1. Navigieren Sie zum Zertifikatgeneratorskript im Microsoft Azure IoT SDK für Node.js, das Sie heruntergeladen haben. Installieren Sie die erforderlichen Pakete:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Erstellen Sie ein Stammzertifikat, und leiten Sie dann ein Gerätezertifikat durch Ausführen des Skripts ab:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Eine Geräte-ID kann Buchstaben, Ziffern und das Zeichen `-` enthalten.

Mit diesen Befehlen werden das folgende Stamm- und das Gerätezertifikat erzeugt.

| filename | Inhalt |
| -------- | -------- |
| mytestrootcert_cert.pem | Der öffentliche Teil des X.509-Stammzertifikats |
| mytestrootcert_key.pem | Der private Schlüssel für das X.509-Stammzertifikat |
| mytestrootcert_fullchain.pem | Der gesamte Schlüsselbund für das X.509-Stammzertifikat |
| sampleDevice01_cert.pem | Der öffentliche Teil des X.509-Gerätezertifikats |
| sampleDevice01_key.pem | Der private Schlüssel für das X.509-Gerätezertifikat |
| sampleDevice01_fullchain.pem | Der gesamte Schlüsselbund für das X.509-Gerätezertifikat. |

Notieren Sie sich den Speicherort dieser Dateien. Sie benötigen ihn später.

### <a name="create-a-group-enrollment"></a>Erstellen einer Gruppenregistrierung

1. Öffnen Sie Ihre IoT Central Anwendung, navigieren Sie im linken Bereich zu **Verwaltung**, und wählen Sie **Geräteverbindung** aus.

1. Wählen Sie **+ Neu** aus, und erstellen Sie die neue Registrierungsgruppe _MyX509Group_ mit dem Nachweistyp **Zertifikate (X.509)** .

1. Öffnen Sie die von Ihnen erstellte Registrierungsgruppe, und wählen Sie **Primäre verwalten** aus.

1. Wählen Sie „Dateioption“ aus, um die von Ihnen zuvor generierte Stammzertifikatdatei _mytestrootcert_cert.pem_ hochzuladen:

    ![Zertifikatupload](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Zum Abschluss der Überprüfung generieren Sie den Prüfcode, kopieren Sie ihn, und erstellen Sie dann damit an der Eingabeaufforderung ein X.509-Verifizierungszertifikat:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Wählen Sie **Überprüfen** aus, um das signierte Verifizierungszertifikat _verification_cert.pem_ hochzuladen, um die Überprüfung abzuschließen:

    ![Überprüftes Zertifikat](./media/how-to-connect-devices-x509/verified.png)

Jetzt können Sie Geräte verbinden, die ein X.509-Zertifikat haben, das von diesem primären Stammzertifikat abgeleitet wurde.

Nachdem Sie die Registrierungsgruppe gespeichert haben, notieren Sie sich den ID-Bereich.

### <a name="run-sample-device-code&quot;></a>Ausführen des Beispiels für Gerätecode

:::zone pivot=&quot;programming-language-csharp&quot;

Wenn Sie Windows verwenden, müssen sich die X.509-Zertifikate im Windows-Zertifikatspeicher befindet, damit das Beispiel funktioniert. So fügen Sie die Zertifikate dem Speicher hinzu:

1. Erstellen Sie mit `openssl` PFX-Dateien aus den PEM-Dateien. Wenn Sie diese Befehle ausführen, werden Sie aufgefordert, ein Kennwort zu erstellen. Notieren Sie sich das Kennwort, da Sie es im nächsten Schritt benötigen:

    ```bash
    openssl pkcs12 -inkey sampleDevice001_key.pem -in sampleDevice001_cert.pem -export -out sampledevice001.pfx
    openssl pkcs12 -inkey mytestrootcert_key.pem -in mytestrootcert_cert.pem -export -out mytestrootcert.pfx
    ```

1. Doppelklicken Sie im Windows Explorer auf die jeweilige PFX-Datei. Wählen Sie im **Zertifikatimport-Assistent** die Option **Aktueller Benutzer** als Speicherort aus, geben Sie das Kennwort aus dem vorherigen Schritt ein, und lassen Sie den Zertifikatspeicher-Assistenten automatisch auswählen. Der Assistent importiert die Zertifikate in den persönlichen Speicher des aktuellen Benutzers.

So ändern Sie den Beispielcode für die Verwendung der Zertifikate:

1. Öffnen Sie in der Visual Studio-Projektmappe **IoTHubDeviceSamples** die Datei *Parameter.cs* im Projekt **TemperatureController**.

1. Fügen Sie die beiden folgenden Parameterdefinitionen der Klasse hinzu:

    ```csharp
    [Option(
        'x',
        &quot;CertificatePath&quot;,
        HelpText = &quot;(Required if DeviceSecurityType is \"dps\"). \nThe device PFX file to use during device provisioning." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_CERT\".")]
    public string CertificatePath { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_CERT");

    [Option(
        'p',
        "CertificatePassword",
        HelpText = "(Required if DeviceSecurityType is \"dps\"). \nThe password of the PFX certificate file." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_PASSWORD\".")]
    public string CertificatePassword { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_PASSWORD");
    ```

    Speichern Sie die Änderungen.

1. Öffnen Sie in der Visual Studio-Projektmappe **IoTHubDeviceSamples** die Datei *Program.cs* im Projekt **TemperatureController**.

1. Fügen Sie die folgenden `using` -Anweisungen ein:

    ```csharp
    using System.Security.Cryptography.X509Certificates;
    using System.IO;
    ```

1. Fügen Sie der Klasse die folgende Methode hinzu:

    ```csharp
    private static X509Certificate2 LoadProvisioningCertificate(Parameters parameters)
    {
        var certificateCollection = new X509Certificate2Collection();
        certificateCollection.Import(
            parameters.CertificatePath,
            parameters.CertificatePassword,
            X509KeyStorageFlags.UserKeySet);

        X509Certificate2 certificate = null;

        foreach (X509Certificate2 element in certificateCollection)
        {
            Console.WriteLine($"Found certificate: {element?.Thumbprint} {element?.Subject}; PrivateKey: {element?.HasPrivateKey}");
            if (certificate == null && element.HasPrivateKey)
            {
                certificate = element;
            }
            else
            {
                element.Dispose();
            }
        }

        if (certificate == null)
        {
            throw new FileNotFoundException($"{parameters.CertificatePath} did not contain any certificate with a private key.");
        }

        Console.WriteLine($"Using certificate {certificate.Thumbprint} {certificate.Subject}");

        return certificate;
    }
    ```

1. Ersetzen Sie in der `SetupDeviceClientAsync`-Methode den Codeblock für `case "dps"` durch den folgenden Code:

    ```csharp
    case "dps":
        s_logger.LogDebug($"Initializing via DPS");
        Console.WriteLine($"Loading the certificate...");
        X509Certificate2 certificate = LoadProvisioningCertificate(parameters);
        DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, certificate, cancellationToken);
        var authMethod = new DeviceAuthenticationWithX509Certificate(dpsRegistrationResult.DeviceId, certificate);
        deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
        break;
    ```

1. Ersetzen Sie die `ProvisionDeviceAsync`-Methode durch folgenden Code:

    ```csharp
    private static async Task<DeviceRegistrationResult> ProvisionDeviceAsync(Parameters parameters, X509Certificate2 certificate, CancellationToken cancellationToken)
    {
        SecurityProvider security = new SecurityProviderX509Certificate(certificate);
        ProvisioningTransportHandler mqttTransportHandler = new ProvisioningTransportHandlerMqtt();
        ProvisioningDeviceClient pdc = ProvisioningDeviceClient.Create(parameters.DpsEndpoint, parameters.DpsIdScope, security, mqttTransportHandler);

        var pnpPayload = new ProvisioningRegistrationAdditionalData
        {
            JsonData = PnpConvention.CreateDpsPayload(ModelId),
        };
        return await pdc.RegisterAsync(pnpPayload, cancellationToken);
    }
    ```

    Speichern Sie die Änderungen.

1. Fügen Sie dem Projekt die folgenden Umgebungsvariablen hinzu:

    - `IOTHUB_DEVICE_X509_CERT`: `<full path to folder that contains PFX files>sampleDevice01.pfx`
    - `IOTHUB_DEVICE_X509_PASSWORD`: Das Kennwort, das Sie beim Erstellen der Datei *sampleDevice01.pfx* festgelegt haben.

1. Erstellen Sie die Anwendung, und führen Sie sie aus. Vergewissern Sie sich, dass die Gerätebereitstellung erfolgreich war.

:::zone-end

:::zone pivot="programming-language-java"

1. Navigieren Sie zum Ordner _azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample_, der die Datei *pom.xml* und den Ordner *src* für das Temperaturreglerbeispiel enthält.

1. Bearbeiten Sie die Datei *pom.xml*, um die folgende Abhängigkeitskonfiguration im `<dependencies>`-Knoten hinzuzufügen:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.sdk.iot.provisioning.security</groupId>
        <artifactId>${x509-provider-artifact-id}</artifactId>
        <version>${x509-provider-version}</version>
    </dependency>
    ```

    Speichern Sie die Änderungen.

1. Öffnen Sie die Datei *src/main/java/samples/com/microsoft/azure/sdk/iot/device/TemperatureController.java* in Ihrem Text-Editor.

1. Ersetzen Sie den `SecurityProviderSymmetricKey`-Import durch die folgenden Importe:

    ```java
    import com.microsoft.azure.sdk.iot.provisioning.security.SecurityProvider;
    import com.microsoft.azure.sdk.iot.provisioning.security.hsm.SecurityProviderX509Cert;
    import com.microsoft.azure.sdk.iot.provisioning.security.exceptions.SecurityProviderException;
    ```

1. Fügen Sie den folgenden Import hinzu:

    ```java
    import java.nio.file.*;
    ```

1. Fügen Sie `SecurityProviderException` der Liste der Ausnahmen hinzu, die die `main`-Methode auslöst:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, ProvisioningDeviceClientException, InterruptedException, SecurityProviderException {
    ```

1. Ersetzen Sie die `initializeAndProvisionDevice`-Methode durch folgenden Code:

    ```java
    private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException, SecurityProviderException {
        String deviceX509Key = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_KEY"))));
        String deviceX509Cert = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_CERT"))));
        SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(deviceX509Cert, deviceX509Key, null);
        ProvisioningDeviceClient provisioningDeviceClient;
        ProvisioningStatus provisioningStatus = new ProvisioningStatus();

        provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityProviderX509);

        AdditionalData additionalData = new AdditionalData();
        additionalData.setProvisioningPayload(com.microsoft.azure.sdk.iot.provisioning.device.plugandplay.PnpHelper.createDpsPayload(MODEL_ID));

        provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

        while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
        {
            if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
            {
                provisioningStatus.exception.printStackTrace();
                System.out.println("Registration error, bailing out");
                break;
            }
            System.out.println("Waiting for Provisioning Service to register");
            Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
        }

        ClientOptions options = new ClientOptions();
        options.setModelId(MODEL_ID);

        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
            System.out.println("IotHUb Uri : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri());
            System.out.println("Device ID : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId());

            String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
            String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

            log.debug("Opening the device client.");
            deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityProviderX509, IotHubClientProtocol.MQTT, options);
            deviceClient.open();
        }
    }
    ```

    Speichern Sie die Änderungen.

1. Fügen Sie in der Shellumgebung die folgenden zwei Umgebungsvariablen hinzu. Geben Sie unbedingt den vollständigen Pfad zu den PEM-Dateien an, und verwenden Sie das richtige Pfadtrennzeichen für Ihr Betriebssystem:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Die anderen erforderlichen Umgebungsvariablen haben Sie beim Durcharbeiten des Tutorials [ Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md) festgelegt.

1. Erstellen Sie die Anwendung, und führen Sie sie aus. Vergewissern Sie sich, dass die Gerätebereitstellung erfolgreich war.

:::zone-end

:::zone pivot="programming-language-javascript"

1. Navigieren Sie zum Ordner _azure-iot-sdk-node/device/samples/pnp_, der die Anwendung **pnpTemperatureController.js** enthält, und führen Sie den folgenden Befehl aus, um das X.509-Paket zu installieren:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Öffnen Sie die Datei **pnpTemperatureController.js** in einem Text-Editor.

1. Bearbeiten Sie die `require`-Anweisungen so, dass sie folgenden Code enthalten:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Fügen Sie dem Abschnitt „DPS connection information“ die folgenden vier Zeilen hinzu, um die Variable `deviceCert` zu initialisieren:

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Bearbeiten Sie die Funktion `provisionDevice`, die den Client erstellt, indem Sie die erste Zeile durch folgenden Code ersetzen:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. Ändern Sie in derselben Funktion die Zeile, in der die Variable `deviceConnectionString` festgelegt wird, wie folgt:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. Fügen Sie in der Funktion `main` nach der Zeile, über die `Client.fromConnectionString` aufgerufen wird, die folgende Zeile hinzu:

    ```javascript
    client.setOptions(deviceCert);
    ```

    Speichern Sie die Änderungen.

1. Fügen Sie in der Shellumgebung die folgenden zwei Umgebungsvariablen hinzu. Geben Sie unbedingt den vollständigen Pfad zu den PEM-Dateien an, und verwenden Sie das richtige Pfadtrennzeichen für Ihr Betriebssystem:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Die anderen erforderlichen Umgebungsvariablen haben Sie beim Durcharbeiten des Tutorials [ Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md) festgelegt.

1. Führen Sie das Skript aus, und überprüfen Sie, ob das Gerät erfolgreich bereitgestellt wurde:

    ```cmd/sh
    node pnpTemperatureController.js
    ```

:::zone-end

:::zone pivot="programming-language-python"

1. Navigieren Sie zum Ordner _azure-iot-device/samples/pnp_, und öffnen Sie die Datei **temp_controller_with_thermostats.py** in einem Text-Editor.

1. Fügen Sie die folgende `from`-Anweisung hinzu, um die X.509-Funktionalität zu importieren:

    ```python
    from azure.iot.device import X509
    ```

1. Ändern Sie den ersten Teil der `provision_device`-Funktion wie folgt:

    ```python
    async def provision_device(provisioning_host, id_scope, registration_id, x509, model_id):
        provisioning_device_client = ProvisioningDeviceClient.create_from_x509_certificate(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            x509=x509,
        )
    ```

1. Ersetzen Sie in der `main`-Funktion die Zeile, in der die Variable `symmetric_key` festgelegt wird, durch folgenden Code:

    ```python
    x509 = X509(
        cert_file=os.getenv("IOTHUB_DEVICE_X509_CERT"),
        key_file=os.getenv("IOTHUB_DEVICE_X509_KEY"),
    )
    ```

1. Ersetzen Sie in der `main`-Funktion den Aufruf der `provision_device`-Funktion durch folgenden Code:

    ```python
    registration_result = await provision_device(
        provisioning_host, id_scope, registration_id, x509, model_id
    )
    ```

1. Ersetzen Sie in der `main`-Funktion den Aufruf der `IoTHubDeviceClient.create_from_symmetric_key`-Funktion durch folgenden Code:

    ```python
    device_client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=registration_result.registration_state.assigned_hub,
        device_id=registration_result.registration_state.device_id,
        product_info=model_id,
    )
    ```

    Speichern Sie die Änderungen.

1. Fügen Sie in der Shellumgebung die folgenden zwei Umgebungsvariablen hinzu. Geben Sie unbedingt den vollständigen Pfad zu den PEM-Dateien an, und verwenden Sie das richtige Pfadtrennzeichen für Ihr Betriebssystem:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > Die anderen erforderlichen Umgebungsvariablen haben Sie beim Durcharbeiten des Tutorials [ Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md) festgelegt.

1. Führen Sie das Skript aus, und überprüfen Sie, ob das Gerät erfolgreich bereitgestellt wurde:

    ```cmd/sh
    python temp_controller_with_thermostats.py
    ```

:::zone-end

Überprüfen Sie, ob in der Geräteansicht Ihrer IoT Central-Anwendung Telemetriedaten angezeigt werden:

![Screenshot: In Ihrer IoT Central-Anwendung eingehende Telemetriedaten.](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-individual-enrollment"></a>Verwenden der individuellen Registrierung

Verwenden Sie X.509-Zertifikate mit einer individuellen Registrierung zum Testen Ihres Geräts und Ihrer Lösung. Bei einer individuellen Registrierung gibt es in Ihrer IoT Central-Anwendung kein X.509-Stamm- oder -Zwischenzertifikat. Geräte verwenden ein selbstsigniertes X.509-Zertifikat zum Herstellen einer Verbindung mit Ihrer Anwendung.

### <a name="generate-self-signed-device-certificate"></a>Generieren eines selbstsignierten Gerätezertifikats

In diesem Abschnitt verwenden Sie ein selbstsigniertes X.509-Zertifikat zum Verbinden von Geräten für individuelle Registrierung, die zum Registrieren eines einzelnen Geräts verwendet werden. Selbstsignierte Zertifikate dienen nur zu Testzwecken.

> [!WARNING]
> Diese Methode zum Erstellen von X.509-Zertifikaten dient nur zu Testzwecken. Für eine Produktionsumgebung sollten Sie Ihren offiziellen, sicheren Mechanismus für Zertifikatgenerierung verwenden.

Erstellen Sie ein selbstsigniertes X.509-Gerätezertifikat durch Ausführen folgender Befehle:

```cmd/sh
  cd azure-iot-sdk-node/provisioning/tools
  node create_test_cert.js device mytestselfcertprimary
  node create_test_cert.js device mytestselfcertsecondary 
```

> [!TIP]
> Eine Geräte-ID kann Buchstaben, Ziffern und das Zeichen `-` enthalten.

### <a name="create-individual-enrollment"></a>Erstellen einer individuellen Registrierung

1. Wählen Sie in der Azure IoT Central-Anwendung die Option **Geräte** aus, und erstellen Sie aus der Gerätevorlage „Thermostat“ ein neues Gerät mit der **Geräte-ID** als _mytestselfcertprimary_. Notieren Sie sich den **ID-Bereich**. Sie werden ihn später verwenden.

1. Öffnen Sie das erstellte Gerät, und wählen Sie **Verbinden** aus.

1. Wählen Sie **Einzelne Registrierungen** als **Connect-Methode** und **Zertifikate (X.509)** als Mechanismus aus:

    ![Individuelle Registrierung](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Wählen Sie unter „primär“ die Option „Datei“ aus, und laden Sie die Zertifikatdatei _mytestselfcertprimary_cert.pem_ hoch, die Sie zuvor generiert haben.

1. Wählen Sie die Dateioption für das sekundäre Zertifikat aus, und laden Sie die Zertifikatdatei _mytestselfcertsecondary_cert.pem_ hoch. Wählen Sie dann **Speichern** aus:

    ![Individuelle Registrierung – Zertifikatupload](./media/how-to-connect-devices-x509/individual-enrollment.png)

Das Gerät wird jetzt mit dem X.509-Zertifikat bereitgestellt.

### <a name="run-a-sample-individual-enrollment-device"></a>Ausführen eines Beispiels für ein Gerät mit individueller Registrierung

1. Kopieren Sie die Dateien _mytestselfcertprimary_key.pem_ und _mytestselfcertprimary_cert.pem_ in den Ordner _azure-iot-sdk-node/device/samples/pnp_, der die Anwendung **pnpTemperatureController.js** enthält. Sie haben diese Anwendung beim Durcharbeiten des Tutorials [Verbinden eines Geräts (JavaScript)](./tutorial-connect-device.md) verwendet.

1. Ändern Sie die im Beispiel oben verwendeten Umgebungsvariablen wie folgt:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Führen Sie das Skript aus, und überprüfen Sie, ob das Gerät erfolgreich bereitgestellt wird:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Sie können auch überprüfen, ob Telemetriedaten in der Geräteansicht angezeigt werden.

    ![Telemetrie – Individuelle Registrierung](./media/how-to-connect-devices-x509/telemetry-primary.png)

Sie können die vorstehenden Schritte auch für das Zertifikat _mytestselfcertsecondary_ wiederholen.

## <a name="connect-an-iot-edge-device"></a>Verbinden mit einem IoT Edge-Gerät

In diesem Abschnitt wird davon ausgegangen, dass Sie eine Gruppenregistrierung verwenden, um Ihr IoT Edge-Gerät zu verbinden. Führen Sie die Schritte in den vorherigen Abschnitten zu folgenden Zwecken aus:

- [Generieren von Stamm- und Gerätezertifikaten](#generate-root-and-device-certificates)
- [Erstellen einer Gruppenregistrierung](#create-a-group-enrollment) <!-- No slightly different type of enrollment group - UPDATE!! -->

So verbinden Sie das IoT Edge-Gerät mithilfe des X.509-Gerätezertifikats mit IoT Central:

- Kopieren Sie das Gerätezertifikat und Schlüsseldateien auf Ihr IoT Edge-Gerät. Im vorherigen Gruppenregistrierungsbeispiel hießen diese Dateien **sampleDevice01_key.pem** und **sampleDevice01_cert.pem**.
- Bearbeiten Sie auf dem IoT Edge-Gerät den Abschnitt `provisioning` in der Konfigurationsdatei **/etc/iotedge/config.yaml** wie folgt:

    ```yaml
    # DPS X.509 provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "<SCOPE_ID>"
      attestation:
        method: "x509"
    #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
        identity_cert: "file:///<path>/sampleDevice01_cert.pem"
        identity_pk: "file:///<path>/sampleDevice01_key.pem"
    #  always_reprovision_on_startup: true
    #  dynamic_reprovisioning: false
    ```

    > [!TIP]
    > Sie müssen keinen Wert für die `registration_id` hinzufügen. IoT Edge kann den **CN**-Wert aus dem X.509-Zertifikat verwenden.

- Führen Sie den folgenden Befehl aus, um die IoT Edge-Runtime neu zu starten:

    ```bash
    sudo systemctl restart iotedge
    ```

Weitere Informationen finden Sie unter [Erstellen und Bereitstellen von IoT Edge-Geräten im großen Stil unter Linux mithilfe von X.509-Zertifikaten](../../iot-edge/how-to-provision-devices-at-scale-linux-x509.md).

## <a name="connect-an-iot-edge-leaf-device"></a>Herstellen einer Verbindung mit einem IoT Edge-Blattgerät

IoT Edge verwendet X.509-Zertifikate, um die Verbindung zwischen Blattgeräten und einem IoT Edge-Gerät zu schützen, das als Gateway fungiert. Weitere Informationen zum Konfigurieren dieses Szenarios finden Sie unter [Verbinden eines nachgeschalteten Geräts mit einem Azure IoT Edge-Gateway](../../iot-edge/how-to-connect-downstream-device.md).

## <a name="roll-x509-device-certificates"></a>Rollen von X.509-Gerätezertifikaten

Während des Lebenszyklus Ihrer IoT Central-Anwendung müssen Sie Ihre X.509-Zertifikate rollieren. Zum Beispiel:

- Bei einer Sicherheitsverletzung ist das Rollen von Zertifikaten eine bewährte Sicherheitsmethode zum Schutz Ihres Systems.
- X.509-Zertifikate haben Ablaufdaten. Die Häufigkeit, mit der Sie die Zertifikate rollen, hängt von den Sicherheitsanforderungen Ihrer Lösung ab. Kunden, deren Lösungen sensible Daten beinhalten, rollen Zertifikate möglicherweise täglich, während andere ihre Zertifikate alle zwei Jahre rollen.

Für unterbrechungsfreie Konnektivität können Sie mit IoT Central primäre und sekundäre X.509-Zertifikate konfigurieren. Wenn die Ablaufdaten von primärem und sekundärem Zertifikat unterschiedlich sind, können Sie das abgelaufene Zertifikat rollen, während Geräte weiterhin Verbindungen mit dem anderen Zertifikat herstellen.

Weitere Informationen finden Sie unter [Assume Breach Methodology](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf) (Methodik beim Ausgehen von einer Sicherheitsverletzung).

In diesem Abschnitt wird beschrieben, wie Sie die Zertifikate in IoT Central rollen. Wenn Sie ein Zertifikat in IoT Central rollen, müssen Sie auch das neue Gerätezertifikat auf Ihre Geräte kopieren.

### <a name="obtain-new-x509-certificates"></a>Abrufen von neuen X.509-Zertifikaten

Rufen Sie neue X.509-Zertifikate von Ihrem Zertifikatanbieter ab. Sie können Ihre eigenen X.509-Zertifikate mit einem Tool wie OpenSSL erstellen. Dieser Ansatz eignet sich zum Testen von X.509-Zertifikaten, bietet jedoch wenig Sicherheitsgarantien. Verwenden Sie nur diesen Ansatz für Tests – es sei denn, Sie können als Ihr eigener ZS-Anbieter agieren.

### <a name="enrollment-groups-and-security-breaches"></a>Registrierungsgruppen und Sicherheitsverletzungen

Zum Aktualisieren einer Gruppenregistrierung als Reaktion auf eine Sicherheitsverletzung sollten Sie den folgenden Ansatz verwenden, bei dem das aktuelle Zertifikat sofort aktualisiert wird. Führen Sie diese Schritte für das primäre und das sekundäre Zertifikat aus, wenn beide kompromittiert wurden:

1. Navigieren Sie im linken Bereich zu **Verwaltung**, und wählen Sie **Geräteverbindung** aus.

2. Wählen Sie **Registrierungsgruppen** und dann in der Liste den Gruppennamen aus.

3. Wählen Sie zum Aktualisieren des Zertifikats **Primäres verwalten** oder **Sekundäres verwalten** aus.

4. Fügen Sie das X.509-Stammzertifikat in der Registrierungsgruppe hinzu, und überprüfen Sie es.

### <a name="individual-enrollments-and-security-breaches"></a>Individuelle Registrierungen und Sicherheitsverletzungen

Wenn Sie Zertifikate als Reaktion auf eine Sicherheitsverletzung rollen, verwenden Sie den folgenden Ansatz zum sofortigen Aktualisieren des aktuellen Zertifikats. Führen Sie diese Schritte für das primäre und das sekundäre Zertifikat aus, wenn beide kompromittiert wurden:

1. Wählen Sie **Geräte** und dann das gewünschte Gerät aus.

1. Wählen Sie **Verbinden** und dann als **Individuelle Registrierung** die Methode „Connect“ aus.

1. Wählen Sie als Mechanismus **Zertifikate (X.509)** aus.

1. Wählen Sie für die Zertifikataktualisierung das Ordnersymbol und dann das neue Zertifikat aus, das für den Registrierungseintrag hochgeladen werden soll. Wählen Sie **Speichern**.

### <a name="enrollment-groups-and-certificate-expiration"></a>Registrierungsgruppen und Zertifikatablauf

Verwenden Sie zum Behandeln von Zertifikatabläufen den folgenden Ansatz zum sofortigen Aktualisieren des aktuellen Zertifikats:

1. Navigieren Sie im linken Bereich zu **Verwaltung**, und wählen Sie **Geräteverbindung** aus.

2. Wählen Sie **Registrierungsgruppen** und dann in der Liste den Gruppennamen aus.

3. Wählen Sie zum Aktualisieren des Zertifikats **Primäres verwalten** aus.

4. Fügen Sie das X.509-Stammzertifikat in der Registrierungsgruppe hinzu, und überprüfen Sie es.

5. Wenn das sekundäre Zertifikat zu einem späteren Zeitpunkt abgelaufen ist, kehren Sie zurück, und aktualisieren Sie dieses Zertifikat.

### <a name="individual-enrollments-and-certificate-expiration"></a>Individuelle Registrierungen und Zertifikatablauf

Wenn Sie Zertifikate rollen, weil Zertifikate ablaufen, sollten Sie die Konfiguration des sekundären Zertifikats wie folgt verwenden, um die Downtime für Geräte zu reduzieren, die sich bereitzustellen versuchen.

Wenn das sekundäre Zertifikat bald abläuft und gerollt werden muss, können Sie zur Verwendung der primären Konfiguration wechseln. Ein solcher Wechsel zwischen primärem und sekundärem Zertifikat reduziert die Downtime für Geräte, die sich bereitzustellen versuchen.

1. Wählen Sie **Geräte** und dann das gewünschte Gerät aus.

2. Wählen Sie **Verbinden** und dann als **Individuelle Registrierung** die Methode „Connect“ aus.

3. Wählen Sie als Mechanismus **Zertifikate (X.509)** aus.

4. Wählen Sie für die Aktualisierung des sekundären Zertifikats das Ordnersymbol und dann das neue Zertifikat aus, das für den Registrierungseintrag hochgeladen werden soll. Wählen Sie **Speichern**.

5. Wenn das primäre Zertifikat zu einem späteren Zeitpunkt abgelaufen ist, kehren Sie zurück, und aktualisieren Sie dieses Zertifikat.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Geräte mithilfe von X.509-Zertifikaten verbinden können, sollten Sie sich als Nächstes über das Thema [Überwachen der Gerätekonnektivität per Azure CLI](howto-monitor-devices-azure-cli.md) informieren.
