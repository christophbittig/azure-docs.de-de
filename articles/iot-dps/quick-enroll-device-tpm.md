---
title: 'Schnellstart: Registrieren eines einzelnen Geräts bei Azure Device Provisioning Service mithilfe des TPM-Nachweises'
description: 'Schnellstart: Registrieren eines einzelnen Geräts bei Azure IoT Hub Device Provisioning Service (DPS) mithilfe des TPM-Nachweises'
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/20/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
zone_pivot_groups: iot-dps-set2
ms.openlocfilehash: d13a4b8224fc5ebe3eccf5f569af0efaed9b4101
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293825"
---
# <a name="quickstart-enroll-individual-device-to-iot-hub-device-provisioning-service-using-tpm-attestation"></a>Schnellstart: Registrieren eines einzelnen Geräts bei IoT Hub Device Provisioning Service mithilfe des TPM-Nachweises

In diesem Artikel wird veranschaulicht, wie Sie programmgesteuert eine individuelle Registrierung für ein TPM-Gerät in Azure IoT Hub Device Provisioning Service erstellen, indem Sie das [Microsoft Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md) und eine Beispielanwendung verwenden. Mit diesem Eintrag für die individuelle Registrierung können Sie ein simuliertes TPM-Gerät optional für den Bereitstellungsdienst registrieren. 

Diese Schritte funktionieren für Windows- und Linux-Computer, aber in diesem Artikel wird nur ein Windows-Entwicklungscomputer verwendet.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Führen Sie die Schritte im Artikel [Einrichten des IoT Hub Device Provisioning Service im Azure-Portal](./quick-setup-auto-provision.md) aus.

:::zone pivot="programming-language-csharp"

* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/vs/).

* Installieren Sie das [.NET Core 3.1 SDK oder höher](https://dotnet.microsoft.com/download) auf Ihrem Windows-Computer. Sie können den folgenden Befehl ausführen, um Ihre Version zu überprüfen:

    ```bash
    dotnet --info
    ```

* (Optional) Wenn Sie am Ende dieser Schnellstartanleitung ein simuliertes Gerät registrieren möchten, können Sie die Schritte unter [Schnellstart: Bereitstellen eines simulierten TPM-Geräts](quick-create-simulated-device-tpm.md) bis zu dem Schritt ausführen, in dem Sie einen Endorsement Key für das Gerät abrufen. Speichern Sie den Endorsement Key, die Registrierungs-ID und optional die Geräte-ID.

:::zone-end

:::zone pivot="programming-language-nodejs"

* Installieren Sie [Node.js v4.0+](https://nodejs.org).

* (Optional) Erstellen Sie einen Endorsement Key. Befolgen Sie die Schritte unter [Erstellen und Bereitstellen eines simulierten Geräts](quick-create-simulated-device-tpm.md), bis Sie den Schlüssel erhalten haben.

:::zone-end

:::zone pivot="programming-language-python"

* Laden Sie [Python 2.x oder 3.x](https://www.python.org/downloads/) herunter, und installieren Sie es. Stellen Sie je nach Einrichtung sicher, dass die 32-Bit- bzw. die 64-Bit-Installation verwendet wird. Fügen Sie Python Ihren plattformspezifischen Umgebungsvariablen hinzu, wenn Sie während der Installation dazu aufgefordert werden. 

* Installieren Sie [Pip](https://pip.pypa.io/en/stable/installing/), falls das Programm nicht in Ihrer Python-Distribution enthalten ist.

> [!IMPORTANT]
> Dieser Artikel gilt nur für das veraltete Python-SDK V1. Geräte- und Dienstclients für IoT Hub Device Provisioning Service sind in V2 noch nicht verfügbar. Das Team arbeitet zurzeit mit vollem Einsatz an der Featureparität für V2.

:::zone-end

:::zone pivot="programming-language-java"

* Installieren Sie [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure). In dieser Schnellstartanleitung wird weiter unten das [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) installiert. Es funktioniert sowohl unter Windows als auch unter Linux. In dieser Schnellstartanleitung wird Windows verwendet.

* Installieren Sie [Maven 3](https://maven.apache.org/download.cgi).

* Installieren Sie [Git](https://git-scm.com/download/), und stellen Sie sicher, dass der Pfad der Umgebungsvariablen `PATH` hinzugefügt wurde.


:::zone-end

> [!NOTE]
> Führen Sie die Schritte zum Erstellen einer individuellen Registrierung mit dem Azure-Portal nicht aus.


:::zone pivot="programming-language-python"

<a id="prepareenvironment"></a>

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung

* Wählen Sie zum Herunterladen und Installieren des [Python Provisioning Service SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) eine der folgenden Optionen aus:
    
    - Erstellen und kompilieren Sie das **Azure IoT Python SDK**. Eine Anleitung für die Erstellung der Python-Pakete finden Sie [hier](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md). Installieren Sie bei Verwendung des Windows-Betriebssystems auch das [Visual C++ Redistributable Package](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads), um die Verwendung nativer DLLs aus Python zu ermöglichen.
    
    - [Installieren oder aktualisieren Sie *pip*, das Python-Paketverwaltungssystem](https://pip.pypa.io/en/stable/installing/), und installieren Sie das Paket mit dem folgenden Befehl:
        
        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

### <a name="copy-symmetric-and-tpm-enrollment-keys"></a>Kopieren von symmetrischen Schlüsseln und TPM-Registrierungsschlüsseln

In dieser Schnellstartanleitung werden sowohl symmetrische Schlüssel als auch TPM-Registrierungen veranschaulicht. Sie müssen entweder Ihre eigenen Schlüssel erstellen oder die bereitgestellten Testschlüssel verwenden:
 
# <a name="symmetric-key"></a>[Symmetrischer Schlüssel](#tab/symmetrickey)

Für Registrierungen simulierter Geräte mit symmetrischem Schlüssel benötigen Sie einen primären und sekundären Schlüssel für Ihr Gerät. Wenn Sie nicht über einen gültigen symmetrischen Schlüssel verfügen, können Sie die folgenden Testschlüssel für dieses Beispiel verwenden:

*Primärer symmetrischer Schlüssel*

```
UmorGiEVPNIQuaWGXXbe8v9gWayS7XtOZmNMo6DEaEXP65GvhuK3OeRf8RVZ9BymBCHxNg3oRTey0pUHUwwYKQ==
```

*Sekundärer symmetrischer Schlüssel*

```
Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
```

# <a name="tpm"></a>[TPM](#tab/tpm)

Für TPM-Registrierungen benötigen Sie den Endorsement Key für Ihr Gerät. Falls Sie die Schritte der Schnellstartanleitung unter [Erstellen und Bereitstellen eines simulierten Geräts mithilfe von IoT Hub Device Provisioning-Diensten](quick-create-simulated-device-tpm.md) befolgt haben, um ein simuliertes TPM-Gerät zu erstellen, können Sie den für dieses Gerät erstellten Schlüssel verwenden. Andernfalls können Sie den folgenden Endorsement Key verwenden, der mit dem SDK bereitgestellt wird:

```
AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
```

---

:::zone-end


:::zone pivot="programming-language-java"

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung 

### <a name="set-up-environment-variables"></a>Einrichten von Umgebungsvariablen

So richten Sie Umgebungsvariablen ein

1. Die Variable `PATH` sollte den vollständigen Pfad zum Verzeichnis *jdk1.8.x\bin* enthalten. Falls dies die erste Java-Installation Ihres Computers ist, sollten Sie eine neue Umgebungsvariable mit dem Namen `JAVA_HOME` erstellen und dafür auf den vollständigen Pfad zum Verzeichnis *jdk1.8.x* verweisen. Auf einem Windows-Computer befindet sich dieses Verzeichnis im Ordner *C:\\Programme\\Java\\* . Sie können Umgebungsvariablen erstellen oder bearbeiten, indem Sie in der **Systemsteuerung** Ihres Windows-Computers nach **Systemumgebungsvariablen bearbeiten** suchen.

    Sie können überprüfen, ob die Java-Einrichtung auf Ihrem Computer erfolgreich war, indem Sie im Befehlsfenster den folgenden Befehl ausführen:

    ```cmd\sh
    java -version
    ```

2. Bearbeiten Sie die Umgebungsvariable `PATH` so, dass sie auf den Ordner *apache-maven-3.x.x\\bin* in dem Ordner verweist, in den Sie Maven extrahiert haben. Sie können sicherstellen, dass die Installation von Maven erfolgreich war, indem Sie diesen Befehl in Ihrem Befehlsfenster ausführen:

    ```cmd\sh
    mvn --version
    ```

3. Stellen Sie sicher, dass [git](https://git-scm.com/download/) auf Ihrem Computer installiert ist und der Umgebungsvariablen `PATH` hinzugefügt wurde.

### <a name="clone-git-repository-for-azure-iot-java-sdk"></a>Klonen eines Git-Repositorys für das Azure IoT Java SDK

So klonen Sie das Azure IoT Java SDK

1. Öffnen Sie eine Eingabeaufforderung. 

2. Klonen Sie das GitHub-Repository für das Codebeispiel zur Geräteregistrierung, indem Sie das [Java-Dienst-SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) verwenden:

    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

:::zone-end

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Abrufen der Verbindungszeichenfolge für Ihren Bereitstellungsdienst

Für das Beispiel in dieser Schnellstartanleitung müssen Sie die Verbindungszeichenfolge für Ihren Bereitstellungsdienst kopieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

3. Wählen Sie Ihre Device Provisioning Service-Instanz aus.

4. Wählen Sie im Menü **Einstellungen** die Option **Richtlinien für gemeinsamen Zugriff** aus.

5. Wählen Sie die Zugriffsrichtlinie aus, die Sie verwenden möchten.

6. Kopieren Sie im Bereich **Zugriffsrichtlinie** die Primärschlüssel-Verbindungszeichenfolge, und speichern Sie sie.

    :::image type="content" source="./media/quick-enroll-device-tpm/get-service-connection-string.png" alt-text="Abrufen der Verbindungszeichenfolge für den Bereitstellungsdienst aus dem Portal":::

## <a name="create-the-individual-enrollment-sample"></a>Erstellen des Beispiels für die individuelle Registrierung

:::zone pivot="programming-language-csharp"

Dieser Abschnitt zeigt, wie Sie eine .NET Core-Konsolen-App erstellen, die Ihrem Bereitstellungsdienst eine individuelle Registrierung für ein TPM-Gerät hinzufügt. Wenn Sie diese Schritte etwas abwandeln, können Sie damit auch eine [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-Konsolen-App zum Hinzufügen der individuellen Registrierung erstellen. Weitere Informationen zum Entwickeln mit IoT Core finden Sie in der [Dokumentation zu Windows für IOT](/windows/iot-core/).

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.

2. Wählen Sie im Bereich **Neues Projekt erstellen** die Option **Konsolenanwendung* aus.

3. Wählen Sie **Weiter** aus.

4. Geben Sie unter **Projektname** den Namen *CreateEnrollmentGroup* ein.

5. Wählen Sie **Weiter** aus. Übernehmen Sie den Standardwert für **Zielframework**.

6. Wählen Sie **Erstellen** aus.

7. Wenn die Projektmappe im Bereich des **Projektmappen-Explorers** geöffnet wird, klicken Sie mit der rechten Maustaste auf das Projekt **CreateEnrollmentGroup**, und wählen Sie dann **NuGet-Pakete verwalten** aus.

8. Wählen Sie in **NuGet Package Manager** die Option **Durchsuchen** aus.

9. Geben Sie *Microsoft.Azure.Devices.Provisioning.Service* ein, und wählen Sie den Eintrag aus.

10. Wählen Sie **Installieren** aus.

    ![Fenster „NuGet-Paket-Manager“](media//quick-enroll-device-tpm/add-nuget.png)

    In diesem Schritt wird das NuGet-Paket mit dem [Client-SDK für Azure IoT Provisioning Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

11. Fügen Sie am Anfang von `Program.cs` nach den anderen `using`-Anweisungen die folgenden `using`-Anweisungen hinzu:
  
    ```csharp
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

12. Fügen Sie die folgenden Felder zur `Program`-Klasse hinzu, und nehmen Sie die aufgeführten Änderungen vor.  

    ```csharp
    private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
    private const string RegistrationId = "sample-registrationid-csharp";
    private const string TpmEndorsementKey =
        "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
        "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
        "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
        "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
        "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
        
    // Optional parameters
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

13. Ersetzen Sie den Platzhalterwert `ProvisioningServiceConnectionString` durch die Verbindungszeichenfolge des Bereitstellungsdiensts, die Sie im vorherigen Abschnitt kopiert haben.

14. Wenn Sie diesen Schnellstart zusammen mit der Schnellstartanleitung unter [Schnellstart: Bereitstellen eines simulierten TPM-Geräts](quick-create-simulated-device-tpm.md) verwenden, um ein simuliertes Gerät bereitzustellen, ersetzen Sie den Endorsement Key und die Registrierungs-ID durch die Werte, die Sie sich in diesem Schnellstart notiert haben. Die Geräte-ID können Sie durch den in der Schnellstartanleitung vorgeschlagenen Wert ersetzen, auf einen eigenen Wert festlegen oder den Standardwert in diesem Beispiel verwenden.

15. Fügen Sie der `Program`-Klasse die folgende Methode hinzu.  Dieser Code erstellt einen individuellen Registrierungseintrag und ruft dann die `CreateOrUpdateIndividualEnrollmentAsync`-Methode für `ProvisioningServiceClient` auf, um die individuelle Registrierung zum Bereitstellungsdienst hinzuzufügen.

    ```csharp
    public static async Task RunSample()
    {
        Console.WriteLine("Starting sample...");
    
        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
        {
            #region Create a new individualEnrollment config
            Console.WriteLine("\nCreating a new individualEnrollment...");
            Attestation attestation = new TpmAttestation(TpmEndorsementKey);
            IndividualEnrollment individualEnrollment =
                    new IndividualEnrollment(
                            RegistrationId,
                            attestation);
    
            // The following parameters are optional. Remove them if you don't need them.
            individualEnrollment.DeviceId = OptionalDeviceId;
            individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
            #endregion
    
            #region Create the individualEnrollment
            Console.WriteLine("\nAdding new individualEnrollment...");
            IndividualEnrollment individualEnrollmentResult =
                await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
            Console.WriteLine("\nIndividualEnrollment created with success.");
            Console.WriteLine(individualEnrollmentResult);
            #endregion
        
        }
    }
    ```

16. Ersetzen Sie schließlich die Methode `Main` durch die folgenden Zeilen:

    ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
    ```

17. Erstellen Sie die Projektmappe.

:::zone-end

:::zone pivot="programming-language-nodejs"

1. Führen Sie in einem Befehlsfenster in Ihrem Arbeitsordner Folgendes aus:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Erstellen Sie mit einem Text-Editor in Ihrem Arbeitsordner die Datei _create_individual_enrollment.js_. Fügen Sie der Datei den folgenden Code hinzu:

    ```Java
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

3. Speichern Sie die Datei .

:::zone-end

:::zone pivot="programming-language-python"

1. Erstellen Sie mit einem Text-Editor die neue Datei *Enrollment.py*.

2. Fügen Sie die folgenden `import`-Anweisungen und Variablen am Anfang der Datei *Enrollment.py* hinzu. (Ersetzen Sie `{dpsConnectionString}` durch die zuvor kopierte Verbindungszeichenfolge, den Zertifikatplatzhalter durch das Zertifikat, das unter [Kopieren von symmetrischen Schlüsseln und TPM-Registrierungsschlüsseln](#copy-symmetric-and-tpm-enrollment-keys) erstellt wurde, und `{registrationid}` durch ein eindeutiges `registrationid`-Element, das nur aus Kleinbuchstaben und Bindestrichen besteht.)


    # <a name="symmetric-key"></a>[Symmetrischer Schlüssel](#tab/symmetrickey)

    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism
    from provisioningserviceclient.protocol.models import SymmetricKeyAttestation

    CONNECTION_STRING = "Enter your DPS connection string"
    PRIMARY_KEY = "Add a valid key"
    SECONDARY_KEY = "Add a valid key"
    REGISTRATION_ID = "Enter a registration ID"
    ```

    # <a name="tpm"></a>[TPM](#tab/tpm)
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "Enter your DPS connection string"
    ENDORSEMENT_KEY = "Enter the endorsement key for your device"
    REGISTRATION_ID = "Enter a registration ID"
    ```

    ---

3. Fügen Sie den folgenden Python-Code hinzu, um die Erstellung der individuellen Registrierung zu implementieren:

     # <a name="symmetric-key"></a>[Symmetrischer Schlüssel](#tab/symmetrickey)

    ```python
    def main():
        print ( "Starting individual enrollment..." )
    
        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
    
        symAtt = SymmetricKeyAttestation(primary_key=PRIMARY_KEY, secondary_key=SECONDARY_KEY)
        att = AttestationMechanism(type="symmetricKey", symmetric_key=symAtt)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)
    
        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

    # <a name="tpm"></a>[TPM](#tab/tpm)

    ```python
    def main():
        print ( "Starting individual enrollment..." )
    
        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
    
        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)
    
        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

    ---

4. Speichern und schließen Sie die Datei *Enrollment.py*.

:::zone-end

:::zone pivot="programming-language-java"

1. Navigieren Sie im heruntergeladenen Quellcode zum Beispielordner *_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_* . Öffnen Sie die Datei *_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_*.

2. Ersetzen Sie `[Provisioning Connection String]` durch die Verbindungszeichenfolge, die Sie unter [Abrufen der Verbindungszeichenfolge für Ihren Bereitstellungsdienst](#get-the-connection-string-for-your-provisioning-service) kopiert haben.

    ```Java
    private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
    ```

   2. Fügen Sie die Details des TPM-Geräts hinzu:
       1. Rufen Sie die *Registrierungs-ID* und den *TPM Endorsement Key* für eine TPM-Gerätesimulation ab, indem Sie die Schritte ausführen, die zum Abschnitt [Simulate TPM device](quick-create-simulated-device-tpm.md#simulatetpm) (Simulieren des TPM-Geräts) führen.
       2. Verwenden Sie die **_Registrierungs-ID_** und den **_Endorsement Key_** aus der Ausgabe des vorherigen Schritts, um die Elemente `[RegistrationId]` und `[TPM Endorsement Key]` in der Beispielcodedatei **_ServiceEnrollmentSample.java_** zu ersetzen:
    
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

3. Fahren Sie mit dem nächsten Schritt fort, um Ihren Bereitstellungsdienst im Beispielcode zu konfigurieren. Falls Sie den Bereitstellungsdienst nicht konfigurieren möchten, sollten Sie die folgenden Anweisungen in der Datei _ServiceEnrollmentSample.java_ auskommentieren oder löschen:

    ```Java
    / / The following parameters are optional. Remove it if you don't need.
    individualEnrollment.setDeviceId(DEVICE_ID);
    individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
    individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
    ```

4. In diesem Schritt wird gezeigt, wie Sie Ihren Bereitstellungsdienst im Beispielcode konfigurieren.

    1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

    2. Wählen Sie im Menü auf der linken Seite oder auf der Portalseite die Option **Alle Ressourcen** aus.

    3. Wählen Sie Ihre Device Provisioning Service-Instanz aus.

    4. Kopieren Sie im Bereich **Übersicht** den Hostnamen des *Dienstendpunkts*.  Ersetzen Sie im Quellcodebeispiel `[Host name]` durch den kopierten Hostnamen.

    ```Java
    private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
    ```

5. Sehen Sie sich den Beispielcode näher an. Er bewirkt, dass eine individuelle Registrierung eines TPM-Geräts erstellt, aktualisiert, abgefragt und gelöscht wird. Um zu überprüfen, ob die Registrierung im Portal erfolgreich war, können Sie die folgenden Codezeilen am Ende der Datei _ServiceEnrollmentSample.java_ vorübergehend auskommentieren:

    ```Java
    // *********************************** Delete info of individualEnrollment ************************************
    System.out.println("\nDelete the individualEnrollment...");
    provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
    ```

6. Speichern Sie die Datei _ServiceEnrollmentSample.java_.

:::zone-end

## <a name="run-the-individual-enrollment-sample"></a>Ausführen des Beispiels für die individuelle Registrierung

:::zone pivot="programming-language-csharp"

1. Führen Sie das Beispiel in Visual Studio aus, um die Registrierung zu erstellen. Ein Befehlsfenster wird angezeigt, in dem Bestätigungsmeldungen angezeigt werden.

2. Bei erfolgreicher Erstellung werden im Befehlsfenster die Eigenschaften der neuen Registrierung angezeigt.

:::zone-end

:::zone pivot="programming-language-nodejs"

Zum Ausführen des Beispiels benötigen Sie die Verbindungszeichenfolge für Ihren Bereitstellungsdienst, die Sie im vorherigen Abschnitt kopiert haben, sowie den Endorsement Key für das Gerät. Falls Sie die Schritte der Schnellstartanleitung unter [Schnellstart: Bereitstellen eines simulierten TPM-Geräts](quick-create-simulated-device-tpm.md) befolgt haben, um ein simuliertes TPM-Gerät zu erstellen, können Sie den für dieses Gerät erstellten Schlüssel verwenden. Andernfalls können Sie zum Erstellen eines Beispiels für die individuelle Registrierung den folgenden Endorsement Key nutzen, der über das [Node.js-Dienst-SDK](https://github.com/Azure/azure-iot-sdk-node) bereitgestellt wird:

```bash
AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
```

1. Führen Sie den folgenden Befehl aus (einschließlich der Anführungszeichen der Befehlsargumente), um eine individuelle Registrierung für Ihr TPM-Gerät zu erstellen:

     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```

2. Bei erfolgreicher Erstellung werden im Befehlsfenster die Eigenschaften der neuen Registrierung angezeigt.

:::zone-end

:::zone pivot="programming-language-python"

1. Öffnen Sie eine Eingabeaufforderung, und führen Sie das folgende Skript aus:

    ```cmd/sh
    python Enrollment.py
    ```

2. Bei erfolgreicher Erstellung werden im Befehlsfenster die Eigenschaften der neuen Registrierung angezeigt.

:::zone-end

:::zone pivot="programming-language-java"

1. Öffnen Sie ein Befehlsfenster im Administratormodus, und navigieren Sie zum Ordner *_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_*.

2. Führen Sie den folgenden Befehl an der Eingabeaufforderung aus:

    ```cmd\sh
    mvn install -DskipTests
    ```

    Mit diesem Befehl wird das Maven-Paket [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) auf Ihren Computer heruntergeladen. Das Paket enthält die Binärdateien für das Java-Dienst-SDK, das mit dem Code erstellt werden soll. Wenn Sie im vorherigen Abschnitt das Tool _X.509-Zertifikatgenerator_ ausgeführt haben, wurde dieses Paket bereits auf Ihren Computer heruntergeladen.

3. Führen Sie das Skript an der Eingabeaufforderung aus:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. Bei erfolgreicher Erstellung werden im Befehlsfenster die Eigenschaften der neuen Registrierung angezeigt.


:::zone-end

So überprüfen Sie, ob die Registrierungsgruppe erstellt wurde

1. Wählen Sie im Azure-Portal Ihre Device Provisioning Service-Instanz aus.

2. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

3. Wählen Sie **Individuelle Registrierungen** aus. Daraufhin sollte ein neuer Registrierungseintrag mit der im Beispiel verwendeten Registrierungs-ID angezeigt werden.

:::zone pivot="programming-language-csharp"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-csharp.png" alt-text="Überprüfen der Registrierung für ein einzelnes C#-Gerät im Portal":::

:::zone-end

:::zone pivot="programming-language-nodejs"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-nodejs.png" alt-text="Überprüfen der Registrierung für ein einzelnes Node.js-Gerät im Portal":::

:::zone-end

:::zone pivot="programming-language-python"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-python.png" alt-text="Überprüfen der Registrierung für ein einzelnes Python-Gerät im Portal":::

:::zone-end

:::zone pivot="programming-language-java"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-java.png" alt-text="Überprüfen der Registrierung für ein einzelnes Java-Gerät im Portal":::

:::zone-end


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die [Tutorials zu Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md) erkunden möchten, sollten Sie die in dieser Schnellstartanleitung erstellten Ressourcen nicht bereinigen. Führen Sie andernfalls die folgenden Schritte aus, um alle Ressourcen zu löschen, die im Rahmen dieser Schnellstartanleitung erstellt wurden.

1. Schließen Sie das Beispielausgabefenster auf Ihrem Computer.

2. Wählen Sie im Azure-Portal im linken Menü die Option **Alle Ressourcen** aus.

3. Wählen Sie Ihre Device Provisioning Service-Instanz aus.

4. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

5. Wählen Sie die Registerkarte **Individuelle Registrierungen** aus.

6. Aktivieren Sie das Kontrollkästchen neben der *REGISTRIERUNGS-ID* des Geräts, das Sie in dieser Schnellstartanleitung registriert haben.

7. Wählen Sie oben auf der Seite die Option **Löschen** aus.

8. Wählen Sie im Azure-Portal in Ihrer Device Provisioning Service-Instanz die Option **Zertifikate** aus.

9. Wählen Sie das Zertifikat aus, das Sie für diese Schnellstartanleitung hochgeladen haben.

10. Wählen Sie am oberen Rand von **Zertifikatdetails** die Option **Löschen** aus.  

11. Falls Sie die Schritte unter [Schnellstart: Bereitstellen eines simulierten TPM-Geräts](quick-create-simulated-device-tpm.md) ausgeführt haben, um ein simuliertes TPM-Gerät zu erstellen, gehen Sie wie folgt vor:

    1. Schließen Sie das TPM-Simulatorfenster und das Beispielausgabefenster für das simulierte Gerät.

    2. Navigieren Sie im Azure-Portal zu der IoT Hub-Instanz, in der Ihr Gerät bereitgestellt wurde. 

    3. Wählen Sie im Menü unter **Explorer** die Option **IoT-Geräte** aus.

    4. Aktivieren Sie das Kontrollkästchen neben der *GERÄTE-ID* des Geräts, das Sie in dieser Schnellstartanleitung registriert haben.

    5. Wählen Sie am oberen Rand des Bereichs die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie programmgesteuert einen individuellen Registrierungseintrag für ein TPM-Gerät erstellt. Optional haben Sie auf Ihrem Computer ein simuliertes TPM-Gerät erstellt und es mithilfe von Azure IoT Hub Device Provisioning Service für Ihren IoT-Hub bereitgestellt. Ausführlichere Informationen zur Gerätebereitstellung finden Sie im Tutorial zur Einrichtung des Device Provisioning-Diensts über das Azure-Portal.

> [!div class="nextstepaction"]
> [Tutorials für den Azure IoT Hub Device Provisioning-Dienst](./tutorial-set-up-cloud.md)