---
title: Erste Schritte mit der Azure IoT Hub-Gerätezwillingen (Java) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure IoT Hub-Gerätezwillingen Tags hinzufügen und eine IoT Hub-Abfrage verwenden. Sie verwenden das Azure IoT-Geräte-SDK für Java, um eine Geräte-App zu implementieren, und das Azure IoT-Dienst-SDK für Java, um eine Dienst-App zu implementieren, die die Tags hinzufügt und die IoT Hub-Abfrage ausführt.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 044eb2543c2476cd53d73e78a6ac810b2bafa1ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355895"
---
# <a name="get-started-with-device-twins-java"></a>Erste Schritte mit Gerätezwillingen (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

In diesem Tutorial erstellen Sie zwei Java-Konsolen-Apps:

* **add-tags-query** ist eine Java-Back-End-App, die Tags hinzufügt und Gerätezwillinge abfragt.
* **simulated-device** ist eine Java-Geräte-App, die eine Verbindung mit Ihrem IoT Hub herstellt und seine Konnektivitätsbedingung mit einer gemeldeten Eigenschaft meldet.

> [!NOTE]
> Im Artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) finden Sie Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Geräte- als auch Back-End-Apps erstellen können.

## <a name="prerequisites"></a>Voraussetzungen

* [Java SE Development Kit 8](/java/azure/jdk/) Wählen Sie unter **Langfristiger Support** unbedingt **Java 8** aus, um zu den Downloads für JDK 8 zu gelangen.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Erstellen der Dienst-App

In diesem Abschnitt erstellen Sie eine Java-App, die dem Gerätezwilling in IoT Hub, der **myDeviceId** zugeordnet ist, Standortmetadaten als Tag hinzufügt. Die App fragt IoT Hub zuerst nach Geräten in den USA ab, und dann nach Geräten, die eine Mobilfunknetzverbindung melden.

1. Erstellen Sie auf dem Entwicklungscomputer einen leeren Ordner mit dem Namen **iot-java-twin-getstarted**.

2. Erstellen Sie im Ordner **iot-java-twin-getstarted** ein Maven-Projekt namens **add-tags-query**, indem Sie an der Eingabeaufforderung den folgenden Befehl ausführen:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Navigieren Sie an der Eingabeaufforderung zum Ordner **add-tags-query**.

4. Öffnen Sie mit einem Text-Editor die Datei **pom.xml** im Ordner **add-tags-query**, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Mit dieser Abhängigkeit können Sie das Paket **iot-service-client** in Ihrer App zum Kommunizieren mit Ihrem IoT Hub verwenden:

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

5. Fügen Sie den Knoten **build** hinter dem Knoten **dependencies** hinzu. Diese Konfiguration weist Maven an, zum Erstellen der App Java 1.8 zu verwenden.

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

7. Öffnen Sie die Datei **add-tags-query\src\main\java\com\mycompany\app\App.java** in einem Text-Editor.

8. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Fügen Sie die folgenden Variablen auf Klassenebene der **App** -Klasse die folgende Variable auf Klassenebene hinzu. Ersetzen Sie `{youriothubconnectionstring}` durch die IoT-Hub-Verbindungszeichenfolge, die Sie unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Aktualisieren Sie die Signatur der **main**-Methode und schließen Sie die folgende `throws`-Klausel ein:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Ersetzen Sie den Code in der **main**-Methode durch den folgenden Code zum Erstellen der Objekte **DeviceTwin** und **DeviceTwinDevice**. Das **DeviceTwin**-Objekt führt die Kommunikation mit Ihrem IoT Hub durch. Das **DeviceTwinDevice**-Objekt stellt den Gerätezwilling mit seinen Eigenschaften und Tags dar:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Fügen Sie den folgenden `try/catch`-Block der **main**-Methode hinzu:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Fügen Sie zum Aktualisieren der Gerätezwillingstags **region** und **plant** in Ihrem Gerätezwilling folgenden Code im `try`-Block hinzu:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Um die Gerätezwillinge in IoT Hub abzufragen, fügen Sie den folgenden Code dem `try`-Block nach dem Code hinzu, den Sie im vorherigen Schritt hinzugefügt haben. Der Code führt zwei Abfragen aus. Jede Abfrage gibt maximal 100 Geräte zurück.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Speichern und schließen Sie die Datei **add-tags-query\src\main\java\com\mycompany\app\App.java**.

16. Erstellen Sie die App **add-tags-query**, und korrigieren Sie etwaige Fehler. Navigieren Sie an der Eingabeaufforderung zum Ordner **add-tags-query**, und führen Sie den folgenden Befehl aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Erstellen einer Geräte-App

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, die einen gemeldeten Eigenschaftswert festlegt, der an IoT Hub gesendet wird.

1. Erstellen Sie im Ordner **iot-java-twin-getstarted** ein Maven-Projekt namens **simulated-device**, indem Sie an der Eingabeaufforderung den folgenden Befehl ausführen:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigieren Sie an der Eingabeaufforderung zum Ordner **simulated-device**.

3. Öffnen Sie mit einem Text-Editor die Datei **pom.xml** im Ordner **simulated-device**, und fügen Sie dem Knoten **dependencies** die folgenden Abhängigkeiten hinzu. Mit dieser Abhängigkeit können Sie das Paket **iot-device-client** in Ihrer App zum Kommunizieren mit Ihrem IoT-Hub verwenden.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Sie finden die aktuelle Version von **iot-device-client** mithilfe der [Maven-Suche](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Mit dieser Abhängigkeit wird ein NOP für die Apache [SLF4J](https://www.slf4j.org/)-Protokollierungsfassade konfiguriert, die vom Geräteclient-SDK zum Implementieren der Protokollierung verwendet wird. Diese Konfiguration ist optional, aber wenn Sie sie weglassen, wird in der Konsole beim Ausführen der App möglicherweise eine Warnung angezeigt. Weitere Informationen zur Protokollierung im Geräteclient-SDK finden Sie unter [Logging](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) (Protokollierung) in der Infodatei *Samples for the Azure IoT device SDK for Java* (Beispiele für das Azure IoT-Geräte-SDK für Java).

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

7. Öffnen Sie die Datei **simulated-device\src\main\java\com\mycompany\app\App.java** mit einem Text-Editor.

8. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Fügen Sie die folgenden Variablen auf Klassenebene der **App** -Klasse die folgende Variable auf Klassenebene hinzu. Ersetzen Sie `{yourdeviceconnectionstring}` durch die Geräteverbindungszeichenfolge, die Sie unter [Registrieren eines neuen Geräts beim IoT-Hub](#register-a-new-device-in-the-iot-hub) kopiert haben.

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Diese Beispiel-App verwendet beim Instanziieren eines **DeviceClient**-Objekts die **protocol**-Variable.

10. Fügen Sie die der **App**-Klasse folgende Methode hinzu, um Informationen zu Zwillingsupdates zu drucken:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Ersetzen Sie den Code in der **main**-Methode durch den folgenden Code:

    * Erstellen Sie einen Geräteclient zur Kommunikation mit IoT Hub.

    * Erstellen Sie ein **Device**-Objekt, das die Gerätezwillingseigenschaften speichert.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. Fügen Sie der **main**-Methode den folgenden Code hinzu, um eine gemeldete **connectivityType**-Eigenschaft zu erstellen und an den IoT Hub zu senden:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. Fügen Sie den folgenden Code am Ende der **main**-Methode hinzu. Das Warten auf die **EINGABETASTE** lässt IoT Hub genügend Zeit, um den Status der Gerätezwillingsvorgänge zu melden.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Ändern Sie die Signatur der **main** -Methode, um die Ausnahmen wie folgt einzufügen:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Speichern und schließen Sie die Datei **simulated-device\src\main\java\com\mycompany\app\App.java**.

16. Erstellen Sie die App **simulated-device**, und korrigieren Sie etwaige Fehler. Navigieren Sie an der Eingabeaufforderung zum Ordner **simulated-device**, und führen Sie den folgenden Befehl aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Ausführen der Apps

Sie können nun die Konsolen-Apps ausführen.

1. Führen Sie an der Eingabeaufforderung im Ordner **add-tags-query** den folgenden Befehl aus, um die Dienst-App **add-tags-query** auszuführen:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Der Screenshot zeigt die Ausgabe aus dem Befehl zur Ausführung der Dienst-App „add-tags-query“.](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Sie sehen die Tags **plant** und **region**, die dem Gerätezwilling hinzugefügt wurden. Die erste Abfrage gibt das Gerät zurück, die zweite jedoch nicht.

2. Führen Sie an der Eingabeaufforderung im Ordner **simulated-device** den folgenden Befehl aus, um dem Gerätezwilling die gemeldete **connectivityType**-Eigenschaft hinzuzufügen:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Der Geräteclient fügt die gemeldete Eigenschaft „connectivityType“ hinzu.](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Führen Sie an der Eingabeaufforderung im Ordner **add-tags-query** den folgenden Befehl aus, um die Dienst-App **add-tags-query** ein zweites Mal auszuführen:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub-Dienst-App zum Aktualisieren von Tagwerten und Ausführen von Geräteabfragen](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Nachdem Ihr Gerät nun die **connectivityType**-Eigenschaft an IoT Hub gesendet hat, gibt die zweite Abfrage das Gerät zurück.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie im Azure-Portal einen neuen IoT-Hub konfiguriert und anschließend in der Identitätsregistrierung des IoT-Hubs eine Geräteidentität erstellt. Sie haben Gerätemetadaten aus einer Back-End-App als Tags hinzugefügt und eine Geräte-App zum Melden von Gerätekonnektivitätsinformationen im Gerätezwilling geschrieben. Sie haben zudem erfahren, wie die Gerätezwillingsinformationen mithilfe der SQL-ähnlichen IoT Hub-Abfragesprache abgefragt werden können.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* Senden von Telemetriedaten von Geräten im Tutorial [Erste Schritte mit IoT Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java).

* Interaktives Steuern von Geräten (z. B. Einschalten eines Lüfters über eine benutzergesteuerte App) mit dem Schnellstart [Verwenden von direkten Methoden](./quickstart-control-device.md?pivots=programming-language-java).