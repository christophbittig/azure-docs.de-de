---
title: Erste Schritte mit der Azure IoT Hub-Geräteverwaltung (Java) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Azure IoT Hub-Geräteverwaltung den Neustart eines Remotegeräts initiieren. Sie verwenden das Azure IoT-Geräte-SDK für Java, um eine simulierte Geräte-App zu implementieren, die eine direkte Methode enthält, und das Azure IoT-Dienst-SDK für Java, um eine Dienst-App zu implementieren, die die direkte Methode aufruft.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-java, devx-track-azurecli
ms.openlocfilehash: c11c15c5dd206a201c9f535bb49b571c5807514e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458622"
---
# <a name="get-started-with-device-management-java"></a>Erste Schritte mit der Geräteverwaltung (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Verwenden des Azure-Portals zum Erstellen einer IoT Hub-Instanz und einer Geräteidentität im IoT Hub

* Erstellen Sie eine simulierte Geräte-App, die eine direkte Methode zum Neustarten des Geräts implementiert. Direkte Methoden werden aus der Cloud aufgerufen.

* Erstellen Sie eine App, die über Ihre IoT Hub-Instanz eine direkte Neustartmethode in der simulierten Geräte-App aufruft. Diese App überwacht dann die vom Gerät gemeldeten Eigenschaften, um den Abschluss des Neustartvorgangs zu ermitteln.

Am Ende dieses Tutorials verfügen Sie über zwei Java-Konsolen-Apps:

**simulated-device** – diese App erfüllt folgende Aufgaben:

* Herstellen einer Verbindung von der zuvor erstellten Geräteidentität mit Ihren IoT Hub

* Empfangen eines direkten Methodenanrufs für einen Neustart

* Simulieren eines physischen Neustarts

* Melden der Zeit des letzten Neustarts über eine gemeldete Eigenschaft

**trigger-reboot** – diese App erfüllt folgende Aufgaben:

* Abrufen einer direkten Methode in der simulierten Geräte-App

* Anzeigen der Antwort auf den direkten Methodenaufruf vom simulierten Gerät

* Anzeigen der aktualisierten gemeldeten Eigenschaften

> [!NOTE]
> Informationen zu den verschiedenen SDKs, mit denen Sie Anwendungen für Geräte sowie das zugehörige Lösungs-Back-End erstellen können, finden Sie unter [Azure IoT SDKs](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Voraussetzungen

* [Java SE Development Kit 8](/java/azure/jdk/) Wählen Sie unter **Langfristiger Support** unbedingt **Java 8** aus, um zu den Downloads für JDK 8 zu gelangen.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Auslösen eines Remoteneustarts auf dem Gerät über eine direkte Methode

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, die folgende Aufgaben ausführt:

1. Aufrufen der direkten reboot-Methode in der simulierten Geräte-App

2. Anzeigen der Antwort

3. Abfragen der gemeldeten Eigenschaften, die vom Gerät gesendet werden, um zu bestimmen, wann der Neustart abgeschlossen ist

Diese Konsolen-App stellt eine Verbindung mit Ihrem IoT Hub her, um die direkte Methode aufzurufen und die gemeldeten Eigenschaften zu lesen.

1. Erstellen Sie einen leeren Ordner mit dem Namen **dm-get-started**.

2. Erstellen Sie im Ordner **dm-get-started** ein Maven-Projekt namens **trigger-reboot**, indem Sie an der Eingabeaufforderung den folgenden Befehl ausführen:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Navigieren Sie an der Eingabeaufforderung zum Ordner **trigger-reboot**.

4. Öffnen Sie mit einem Text-Editor die Datei **pom.xml** im Ordner **trigger-reboot**, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Mit dieser Abhängigkeit können Sie das Paket „iot-service-client“ in Ihrer App zum Kommunizieren mit Ihrem IoT-Hub verwenden:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Sie finden die aktuelle Version von **iot-service-client** mithilfe der [Maven-Suche](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Fügen Sie den Knoten **build** hinter dem Knoten **dependencies** hinzu. Diese Konfiguration weist Maven an, Java 1.8 zu verwenden, um die App zu erstellen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Speichern und schließen Sie die Datei **pom.xml**.

7. Öffnen Sie die Quelldatei **trigger-reboot\src\main\java\com\mycompany\app\App.java** mit einem Text-Editor.

8. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Fügen Sie die folgenden Variablen auf Klassenebene der **App** -Klasse die folgende Variable auf Klassenebene hinzu. Ersetzen Sie `{youriothubconnectionstring}` durch die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um einen Thread zu implementieren, der die vom Gerätezwilling gemeldeten Eigenschaften alle 10 Sekunden liest:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Ändern Sie die Signatur der **main**-Methode, um die folgende Ausnahme auszulösen:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Ersetzen Sie den Code in der **main**-Methode durch den folgenden Code, um die direkte reboot-Methode auf dem simulierten Gerät aufzurufen:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Fügen Sie der **main**-Methode den folgenden Code hinzu, um den Thread zum Abrufen der vom simulierten Gerät gemeldeten Eigenschaften zu starten:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Fügen Sie der **main**-Methode den folgenden Code hinzu, damit Sie die App beenden können:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Speichern und schließen Sie die Datei **trigger-reboot\src\main\java\com\mycompany\app\App.java**.

16. Erstellen Sie die Back-End-App **trigger-reboot**, und korrigieren Sie etwaige Fehler. Navigieren Sie an der Eingabeaufforderung zum Ordner **trigger-reboot**, und führen Sie den folgenden Befehl aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, die ein Gerät simuliert. Die App lauscht auf den Aufruf der direkten Methode für den Neustart aus Ihrer IoT Hub-Instanz und reagiert sofort auf diesen Aufruf. Anschließend wechselt die App für eine Weile in den Ruhezustand, um den Neustartvorgang zu simulieren, bevor sie eine gemeldete Eigenschaft verwendet, um die Back-End-App **trigger-reboot** darüber zu informieren, dass der Neustart abgeschlossen ist.

1. Erstellen Sie im Ordner **dm-get-started** ein Maven-Projekt namens **simulated-device**, indem Sie an der Eingabeaufforderung den folgenden Befehl ausführen:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigieren Sie an der Eingabeaufforderung zum Ordner **simulated-device**.

3. Öffnen Sie mit einem Text-Editor die Datei **pom.xml** im Ordner **simulated-device**, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Mit dieser Abhängigkeit können Sie das Paket „iot-service-client“ in Ihrer App zum Kommunizieren mit Ihrem IoT-Hub verwenden:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Sie finden die aktuelle Version von **iot-device-client** mithilfe der [Maven-Suche](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Mit dieser Abhängigkeit wird ein NOP für die Apache [SLF4J](https://www.slf4j.org/)-Protokollierungsfassade konfiguriert, die vom Geräteclient-SDK zum Implementieren der Protokollierung verwendet wird. Diese Konfiguration ist optional, aber wenn Sie sie weglassen, wird in der Konsole beim Ausführen der App möglicherweise eine Warnung angezeigt. Weitere Informationen zur Protokollierung im Geräteclient-SDK finden Sie unter [Logging](https://github.com/Azure/azure-iot-sdk-java/blob/main/device/iot-device-samples/readme.md#logging) (Protokollierung) in der Infodatei *Samples for the Azure IoT device SDK for Java* (Beispiele für das Azure IoT-Geräte-SDK für Java).

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Fügen Sie den Knoten **build** hinter dem Knoten **dependencies** hinzu. Diese Konfiguration weist Maven an, Java 1.8 zu verwenden, um die App zu erstellen:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Speichern und schließen Sie die Datei **pom.xml**.

7. Öffnen Sie die Quelldatei **simulated-device\src\main\java\com\mycompany\app\App.java** mit einem Text-Editor.

8. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Fügen Sie die folgenden Variablen auf Klassenebene der **App** -Klasse die folgende Variable auf Klassenebene hinzu. Ersetzen Sie `{yourdeviceconnectionstring}` durch die Geräteverbindungszeichenfolge, die Sie im Abschnitt [Registrieren eines neuen Geräts beim IoT-Hub](#register-a-new-device-in-the-iot-hub) notiert haben:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um einen Rückrufhandler für Statusereignisse der direkten Methode zu implementieren:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um einen Rückrufhandler für Statusereignisse des Gerätezwillings zu implementieren:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um einen Rückrufhandler für Eigenschaftenereignisse zu implementieren:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um einen Thread für das Simulieren des Geräteneustarts zu implementieren. Der Thread wartet fünf Sekunden und legt dann die gemeldete **lastReboot**-Eigenschaft fest:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. Fügen Sie der **App**-Klasse die folgende geschachtelte Klasse hinzu, um auf dem Gerät eine direkte Methode zu implementieren. Wenn die simulierte App einen Aufruf der direkten Methode **reboot** empfängt, gibt sie eine Bestätigung an den Aufrufer zurück und startet dann einen Thread zum Verarbeiten des Neustarts:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Ändern Sie die Signatur der **main**-Methode, um die folgenden Ausnahmen auszulösen:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Ersetzen Sie den Code in der **main**-Methode durch den folgenden Code, um einen **DeviceClient** zu instanziieren:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Um mit dem Lauschen auf Aufrufe der direkten Methode zu beginnen, fügen Sie der **main**-Methode den folgenden Code hinzu:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Fügen Sie der **main**-Methode den folgenden Code zum Herunterfahren des Gerätesimulators hinzu:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Speichern und schließen Sie die Datei „simulated-device\src\main\java\com\mycompany\app\App.java“.

20. Erstellen Sie die App **simulated-device**, und korrigieren Sie etwaige Fehler. Navigieren Sie an der Eingabeaufforderung zum Ordner **simulated-device**, und führen Sie den folgenden Befehl aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Ausführen der Apps

Jetzt können Sie die Apps ausführen.

1. Führen Sie an einer Eingabeaufforderung im Ordner **simulated-device** den folgenden Befehl aus, um mit dem Lauschen auf Aufrufe der reboot-Methode aus Ihrer IoT Hub-Instanz zu beginnen:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Simulierte Java-IoT Hub-Geräte-App zum Lauschen auf Aufrufe der direkten reboot-Methode](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Führen Sie an einer Eingabeaufforderung im Ordner **trigger-reboot** den folgenden Befehl aus, um aus Ihrer IoT Hub-Instanz die reboot-Methode auf Ihrem simulierten Gerät aufzurufen:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java-IoT Hub-Dienst-App zum Aufrufen der direkten reboot-Methode](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Das simulierte Gerät reagiert auf den Aufruf der direkten reboot-Methode:

    ![Simulierte Java IoT Hub-Geräte-App reagiert auf den Aufruf einer direkter Methode](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
