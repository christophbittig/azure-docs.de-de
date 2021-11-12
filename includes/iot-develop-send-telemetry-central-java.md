---
title: Datei einfügen
description: Datei einfügen
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 11/02/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 142f8a9dd1c62442c76f35f53a3cb762c6948704
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521170"
---
[![Code durchsuchen](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

In dieser Schnellstartanleitung lernen Sie einen einfachen Entwicklungsworkflow für Azure IoT-Anwendungen kennen. Zunächst erstellen Sie eine Azure IoT Central-Anwendung zum Hosten von Geräten. Dann verwenden Sie ein Azure IoT Device SDK-Beispiel, um einen Temperaturregler zu erstellen, ihn sicher mit IoT Central zu verbinden und Telemetriedaten zu senden. Die Beispielanwendung für den Temperaturregler läuft auf Ihrem lokalen Computer und erzeugt simulierte Sensordaten, die an die IoT-Zentrale gesendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Diese Schnellstartanleitung wird unter Windows, Linux und Raspberry Pi ausgeführt. Sie wurde mit den folgenden Betriebssystem- und Geräteversionen getestet:

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi OS Version 10 (Buster), ausgeführt auf einem Raspberry Pi 3 Model B+

Installieren Sie die folgenden Komponenten auf dem Entwicklungscomputer:

- [Git](https://git-scm.com/downloads).

Installieren Sie die übrigen erforderlichen Komponenten für Ihr Betriebssystem.

### <a name="windows"></a>Windows

Um diese Schnellstartanleitung unter Windows ausführen zu können, installieren Sie die folgende Software:

- Java SE Development Kit 8 oder höher. Sie können das Java 8 JDK (LTS) für mehrere Plattformen unter [Herunterladen von Zulu-Builds von OpenJDK](https://www.azul.com/downloads/zulu-community/) herunterladen. Wählen Sie im Installationsprogramm die Option **Zu PATH hinzufügen** aus.

- [Apache Maven 3](https://maven.apache.org/download.cgi) Nachdem Sie den Download in einen lokalen Ordner extrahiert haben, fügen Sie der Windows-Variablen `PATH` den vollständigen Pfad zum Maven-Ordner */bin* hinzu.

### <a name="linux-or-raspberry-pi-os"></a>Betriebssystem Linux oder Raspberry Pi

Installieren Sie die folgende Software zum Ausführen dieser Schnellstartanleitung unter Linux oder Raspberry Pi:

> [!NOTE]
> Die Schritte in diesem Abschnitt basieren auf Linux Ubuntu-/Debian-Distributionen. (Raspberry Pi OS basiert auf Debian.) Wenn Sie eine andere Linux-Distribution verwenden, müssen Sie die Schritte entsprechend ändern.

- OpenJDK (Open Java Development Kit) 8 oder höher. Sie können mit dem Befehl `java -version` die auf Ihrem System installierte Java-Version überprüfen. Stellen Sie sicher, dass das JDK und nicht nur die Java-Runtime (JRE) installiert ist.

    1. Geben Sie die folgenden Befehle ein, um das OpenJDK für Ihr System zu installieren:

        So installieren Sie die Standardversion von OpenJDK für Ihr System (OpenJDK 11 für Ubuntu 20.04 und Raspberry Pi OS 10 zum Zeitpunkt der Artikelerstellung):

        ```bash
        sudo apt update
        sudo apt install default-jdk
        ```

        Alternativ können Sie eine Version des zu installierenden JDK angeben. Zum Beispiel:

        ```bash
        sudo apt update
        sudo apt install openjdk-8-jdk
        ```

    1. Wenn auf Ihrem System mehrere Java-Versionen installiert sind, können Sie die Standardversionen (automatisch) von Java und dem Java-Compiler anhand der folgenden Befehle konfigurieren.

        ```bash
        update-java-alternatives --list          #list the Java versions installed
        sudo update-alternatives --config java   #set the default Java version
        sudo  update-alternatives --config javac #set the default Java compiler version
        ```

    1. Legen Sie die Umgebungsvariable `JAVA_HOME` auf den Pfad Ihrer JDK-Installation fest. (Dies ist im Allgemeinen ein Unterverzeichnis mit Versionsangabe im Verzeichnis **/usr/lib/jvm**.)

        ```bash
        export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")
        ```

        > [!IMPORTANT]
        > Dieser Befehl legt die Variable `JAVA_HOME` in Ihrer aktuellen Shellumgebung fest. Wir empfehlen, den Befehl Ihrer Datei `~/.bashrc` oder `/etc/profile` hinzuzufügen, damit er verfügbar ist, wenn Sie eine neue Shell öffnen.

    1. Stellen Sie bei der installierten Version des Java-JDK (und der JRE) sicher, dass Ihre Java-Compilerversion mit der JDK-Version übereinstimmt und dass die Umgebungsvariable `JAVA_HOME` ordnungsgemäß festgelegt wurde.

        ```bash
        java -version
        javac -version
        echo $JAVA_HOME
        ```

- Apache Maven 3 Sie können mit dem Befehl `mvn --version` die auf Ihrem System installierte Maven-Version überprüfen.

    1. Geben Sie zum Installieren von Maven die folgenden Befehle ein:

        ```bash
        sudo apt-get update
        sudo apt-get install maven
        ```

    1. Geben Sie zum Überprüfen Ihrer Installation den folgenden Befehl ein.

        ```bash
        mvn --version
        ```

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-device"></a>Ausführen eines Geräts

In diesem Abschnitt konfigurieren Sie Ihre lokale Umgebung, installieren das Azure IoT-Java-Geräte-SDK und führen ein Beispiel aus, mit dem ein Temperaturregler erstellt wird.

### <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

1. Öffnen Sie eine Konsole, etwa die Windows-Eingabeaufforderung oder Bash.

    **Linux und Raspberry Pi OS**

    Vergewissern Sie sich, dass die JAVA_HOME-Umgebungsvariable (`echo $JAVA_HOME`) festgelegt wurde. Diese Umgebungsvariable muss so festgelegt sein, dass das SDK und die Beispiele erfolgreich erstellt werden. Informationen zum Festlegen von JAVA_HOME finden Sie unter [Voraussetzungen für Linux/Raspberry Pi](#linux-or-raspberry-pi-os).

1. Legen Sie mit den entsprechenden Befehle für Ihre Konsole die folgenden Umgebungsvariablen fest. Das Gerät verwendet diese Werte für die Verbindung mit der IoT-Zentrale. Verwenden Sie für `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` und `IOTHUB_DEVICE_DPS_DEVICE_ID` die Geräteverbindungswerte, die Sie zuvor gespeichert haben.

    **Windows-Eingabeaufforderung**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > In den Windows-CMD-Befehlen sind die Variablenwerte nicht in Anführungszeichen eingeschlossen.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="build-and-run-the-code"></a>Erstellen und Ausführen des Codes

1. Klonen Sie das Java-Geräte-SDK von Azure IoT auf Ihren lokalen Computer.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```

1. Navigieren Sie zum Stammordner des SDK, und führen Sie den folgenden Befehl aus, um das SDK zu erstellen und die Beispiele zu aktualisieren.

    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```

    Dieser Vorgang dauert mehrere Minuten.

1. Navigieren Sie zum Beispielverzeichnis.

    **Windows**

    ```console
    cd device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample
    ```

    **Linux oder Raspberry Pi OS**

    ```bash
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```

1. Führen Sie das folgende Codebeispiel aus dem SDK aus. Das Beispiel erstellt einen Temperaturregler mit Thermostatsensoren.

    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```

    Nachdem Ihr Gerät eine Verbindung mit der IoT Central-Anwendung hergestellt hat, stellt es eine Verbindung mit der Geräteinstanz her, die Sie in der Anwendung erstellt haben, und beginnt mit dem Senden von Telemetriedaten. Nach einigen anfänglichen Bereitstellungsdetails beginnt die Konsole mit der Ausgabe der Telemetriedaten für den Temperaturregler.

    ```output
    2021-05-13 15:39:26.411 DEBUG Mqtt:253 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
    2021-05-13 15:39:26.428 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.432 DEBUG TemperatureController:427 - Property: Update - component = "deviceInformation" is COMPLETED.
    2021-05-13 15:39:26.436 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.438 DEBUG TemperatureController:438 - Telemetry: Sent - {"workingSet": 1024.0KiB }
    2021-05-13 15:39:26.439 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.439 DEBUG TemperatureController:446 - Property: Update - {"serialNumber": SR-123456} is COMPLETED
    2021-05-13 15:39:26.447 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.447 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 44.4░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```