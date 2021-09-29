---
title: 'Schnellstart: Erstellen einer Azure Kinect-Anwendung zum Body Tracking'
description: Schritt-für-Schritt-Anweisungen zum Erstellen Ihrer ersten Azure-Kinect-Anwendung zum Body Tracking
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, Azure, Sensor, SDK, Körper, Nachverfolgung, gemeinsam, Anwendung, erste
ms.openlocfilehash: 7667157e90e9920130084e26c23a7628c7f55b11
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755666"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>Schnellstart: Erstellen einer Azure Kinect-Anwendung zum Body Tracking

Machen Sie Ihre ersten Schritte mit dem Body Tracking SDK? Mit dieser Schnellstartanleitung gehen Sie beim Body Tracking gut gerüstet an den Start! Weitere Beispiele finden Sie in diesem [Azure-Kinect-Beispielrepository](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="prerequisites"></a>Voraussetzungen

- [Schnellstart: Einrichten Ihres Azure Kinect DK-Geräts](set-up-azure-kinect-dk.md)
- [Einrichten des Body Tracking SDK](body-sdk-setup.md)
- Exemplarische Schnellstart-Vorgehensweise zum [Erstellen Ihrer ersten Azure Kinect-Anwendung](build-first-app.md).
- Machen Sie sich mit den folgenden Sensor SDK-Funktionen vertraut:
  - [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- Arbeiten Sie die Dokumentation zu den folgenden Funktionen des Body Tracking SDK durch:
  - [k4abt_tracker_create()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>Header

Die Nachverfolgung von Körpern (Body Tracking) verwendet einen einzelnen Header, `k4abt.h`. Schließen Sie diesen Header zusätzlich zu `k4a.h` ein. Achten Sie darauf, dass der Compiler Ihrer Wahl für die Ordner `lib` und `include` sowohl des Sensor SDK als auch des Body Tracking SDK eingerichtet ist. Ferner müssen Sie eine Verknüpfung zu den Dateien `k4a.lib` und `k4abt.lib` herstellen. Zum Ausführen der Anwendung müssen sich `k4a.dll`, `k4abt.dll`, `onnxruntime.dll` und `dnn_model.onnx` im Ausführungspfad der Anwendung befinden.

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>Öffnen des Geräts und Starten der Kamera

Für Ihre erste Anwendung zur Nachverfolgung von Körpern gehen wir von einem einzelnen Azure Kinect-Gerät aus, das mit dem PC verbunden ist.

Die Nachverfolgung von Körpern baut auf dem Sensor SDK auf. Um Body Tracking zu verwenden, müssen Sie das Gerät zunächst öffnen und konfigurieren. Verwenden Sie die Funktion [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113), um das Gerät zu öffnen und es dann mit einem [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)-Objekt zu konfigurieren. Legen Sie für optimale Ergebnisse den Tiefenmodus auf `K4A_DEPTH_MODE_NFOV_UNBINNED` oder `K4A_DEPTH_MODE_WFOV_2X2BINNED` fest. Body Tracking kann nicht ausgeführt werden, wenn der Tiefenmodus auf `K4A_DEPTH_MODE_OFF` oder `K4A_DEPTH_MODE_PASSIVE_IR` festgelegt ist.

Weitere Informationen zum Auffinden und Öffnen des Geräts finden Sie auf [dieser Seite](find-then-open-device.md).

Sie finden weitere Informationen zu den Tiefenmodi von Azure Kinect auf diesen Seiten: [Hardwarespezifikation](hardware-specification.md)- und [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d)-Enumeration.

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>Erstellen der Nachverfolgung

Der erste Schritt zum Abrufen von Ergebnissen der Nachverfolgung von Körpern besteht im Erstellen eines Body Trackers (Modul zur Körpernachverfolgung). Dafür ist die Sensorkalibirierungs-Struktur [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) erforderlich. Die Sensorkalibrierung kann mithilfe der Funktion [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) abgefragt werden.

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Abrufen von Aufzeichnungen vom Azure Kinect-Gerät

Weitere Informationen zum Abrufen von Bilddaten finden Sie auf [dieser Seite](retrieve-images.md).

```C
// Capture a depth frame
k4a_capture_t sensor_capture;
k4a_device_get_capture(device, &sensor_capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Einstellen der Aufzeichnung in die Warteschlange und Abrufen der Ergebnisse

Die Nachverfolgung verwaltet intern eine Eingabewarteschlange und eine Ausgabewarteschlange, um die asynchrone Verarbeitung der Azure Kinect DK-Aufzeichnungen effizienter durchzuführen. Der nächste Schritt besteht im Verwenden der Funktion `k4abt_tracker_enqueue_capture()` zum Hinzufügen einer neuen Aufzeichnung zur Eingabewarteschlange. Verwenden Sie die Funktion `k4abt_tracker_pop_result()`, um ein Ergebnis aus der Ausgabewarteschlange abzurufen. Der Timeoutwert hängt von der Anwendung ab und steuert die Wartezeit in der Warteschlange.

Ihre erste Anwendung zur Nachverfolgung von Körpern verwendet die Echtzeit-Verarbeitungsplattform. Eine detaillierte Erläuterung der weiteren Muster finden Sie unter [Abrufen der Body Tracking-Ergebnisse](get-body-tracking-results.md).

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>Zugriff auf die Body Tracking-Ergebnisdaten

Die Ergebnisse des Body Trackings für die einzelnen Sensoraufzeichnungen sind in einer Body Frame-Struktur [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html) gespeichert. Jeder Body Frame enthält drei Hauptkomponenten: eine Sammlung von Körperstrukturen, eine 2D-Indexkarte und die Eingabeaufzeichnung.

Ihre erste Anwendung zum Nachverfolgen von Körpern greift nur auf die Anzahl der erkannten Körper zu. Eine detaillierte Erläuterung der Daten in einem Body-Frame finden Sie unter [Zugreifen auf Daten im Body-Frame](access-data-body-frame.md).

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>Bereinigung

Der letzte Schritt besteht im Herunterfahren des Body Trackings und im Freigeben des Body Tracking-Objekts. Außerdem müssen Sie das Gerät beenden und schließen.

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>Vollständige Quelle

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Abrufen von Body Tracking-Ergebnissen](get-body-tracking-results.md)
