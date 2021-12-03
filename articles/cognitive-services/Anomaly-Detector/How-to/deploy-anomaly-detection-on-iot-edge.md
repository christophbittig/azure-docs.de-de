---
title: Ausführen der Anomalieerkennung in IoT Edge
titleSuffix: Azure Cognitive Services
description: Stellen Sie das Modul für die Anomalieerkennung in IoT Edge bereit.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: fa2904768023120fb2b08a52a23bb33e71e4d2f6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219146"
---
# <a name="deploy-an-anomaly-detector-univariate-module-to-iot-edge"></a>Bereitstellen eines Moduls zur Erkennung von univariaten Anomalien in IoT Edge

Hier erfahren Sie, wie Sie das [Anomalieerkennungsmodul](../anomaly-detector-container-howto.md) von Cognitive Services auf einem IoT Edge-Gerät bereitstellen. Nachdem das Modul in IoT Edge bereitgestellt wurde, wird es in IoT Edge zusammen mit anderen Modulen als Containerinstanz ausgeführt. Es macht dieselben APIs wie eine Containerinstanz zur Anomalieerkennung verfügbar, die in einer Docker-Container-Standardumgebung ausgeführt wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Verwenden Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.
* Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli).
* Ein [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md)- und ein [IoT Edge](../../../iot-edge/quickstart-linux.md)-Gerät

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Bereitstellen des Moduls zur Anomalieerkennung am Edge

1. Geben Sie im Azure-Portal **Anomaly Detector on IoT Edge** (Anomalieerkennung in IoT Edge) in das Suchfeld ein, und öffnen Sie das Azure Marketplace-Ergebnis.
2. Sie werden auf die [Seite zu Zielgeräten für IoT Edge-Module](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector) im Azure-Portal umgeleitet. Geben Sie die folgenden erforderlichen Informationen ein.

    1. Wählen Sie Ihr Abonnement aus.

    1. Wählen Sie Ihren IoT-Hub aus.

    1. Wählen Sie **Gerät suchen** aus, und suchen Sie nach einem IoT Edge-Gerät.

3. Wählen Sie die Schaltfläche **Erstellen**.

4. Wählen Sie das Modul **AnomalyDetectoronIoTEdge** aus.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Abbildung der Benutzeroberfläche der IoT Edge-Module mit dem Link AnomalyDetectoronIoTEdge, der mit einem roten Kasten hervorgehoben ist, um anzugeben, dass dies das auszuwählende Element ist":::

5. Navigieren Sie zu den **Umgebungsvariablen**, und geben Sie die folgenden Informationen an.

    1.  Übernehmen Sie den Wert, der den Endbenutzer-Lizenzvertrag **akzeptiert**.

    1. Geben Sie die für die **Abrechnung** erforderlichen Informationen über Ihren Cognitive Services-Endpunkt an.

    1. Geben Sie den für **ApiKey** erforderlichen Wert in Form Ihres Cognitive Services-API-Schlüssels an.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Umgebungsvariablen mit roten Kästen um die Bereiche, für die Werte für den Endpunkt und den API-Schlüssel ausgefüllt werden müssen":::

6. Wählen Sie **Update** (Aktualisieren) aus.

7. Klicken Sie auf **Weiter: Routen**, um Ihre Route zu definieren. Sie legen fest, dass alle Nachrichten von allen Modulen an Azure IoT Hub gesendet werden. Wie Sie eine Route deklarieren, erfahren Sie unter [Einrichten von Routen in IoT Edge](../../../iot-edge/module-composition.md?view=iotedge-2020-11&preserve-view=true).

8. Klicken Sie auf **Weiter: Überprüfen + erstellen**. Sie können eine Vorschau der JSON-Datei anzeigen, die alle Module definiert, die auf Ihrem IoT Edge-Gerät bereitgestellt werden.
    
9. Klicken Sie auf **Erstellen**, um die Modulbereitstellung zu starten.

10. Nachdem Sie die Modulbereitstellung abgeschlossen haben, navigieren Sie zurück zur Seite IoT Edge Ihres IoT-Hubs. Wählen Sie Ihr Gerät in der Liste der IoT Edge-Geräte aus, um die zugehörigen Details anzuzeigen.

11. Scrollen Sie nach unten, und sehen Sie sich die aufgeführten Module an. Überprüfen Sie, ob der Laufzeitstatus für Ihr neues Modul ausgeführt wird. 

Um den Laufzeitstatus Ihres IoT Edge-Geräts zu überprüfen, lesen Sie den [Leitfaden zur Fehlerbehebung](../../../iot-edge/troubleshoot.md).

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Testen der Anomalieerkennung auf einem IoT Edge-Gerät

Sie führen einen HTTP-Aufruf für das Azure IoT Edge-Gerät aus, auf dem der Azure Cognitive Services-Container ausgeführt wird. Der Container stellt REST-basierte Endpunkt-APIs bereit. Verwenden Sie den Host `http://<your-edge-device-ipaddress>:5000` für Modul-APIs.

Alternativ können Sie [einen Modulclient erstellen, indem Sie die Anomalieerkennung-Clientbibliothek](../quickstarts/client-libraries.md?tabs=linux&pivots=programming-language-python) auf dem Azure IoT Edge-Gerät verwenden und dann den ausgeführten Azure Cognitive Services-Container am Edge aufrufen. Verwenden Sie den Hostendpunkt `http://<your-edge-device-ipaddress>:5000`, und lassen Sie den Hostschlüssel leer. 

Wenn Ihr Edgegerät die eingehende Kommunikation an Port 5000 noch nicht zulässt, müssen Sie eine neue **Regel für den eingehenden Port** erstellen. 

Für eine Azure-VM kann diese unter **Virtual Machine** > **Einstellungen** > **Netzwerk** > **Regel für eingehenden Port** > **Regel für eingehenden Port hinzufügen** festgelegt werden.

Es gibt mehrere Möglichkeiten zu überprüfen, ob ein Modul aktiv ist. Suchen Sie die *externe IP*-Adresse und den verfügbar gemachten Port des betreffenden Edgegeräts, und öffnen Sie Ihren bevorzugten Webbrowser. Verwenden Sie die verschiedenen URLs der untenstehenden Anforderungen, um zu überprüfen, ob der Container ausgeführt wird. Die unten aufgeführten Beispiel-URLs für Anforderungen sind `http://<your-edge-device-ipaddress:5000`, aber Ihr spezifischer Container kann variieren. Beachten Sie, dass Sie die *externe IP-Adresse* Ihres Edgegeräts verwenden müssen.

| Anforderungs-URL | Zweck |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | Der Container stellt eine Startseite bereit. |
| `http://<your-edge-device-ipaddress>:5000/status` | Dies ermöglicht auch eine Überprüfung mit GET-Anforderung, dass der zum Starten des Containers verwendete API-Schlüssel gültig ist, ohne dass eine Endpunktabfrage veranlasst wird. Diese Anforderung kann für [Live- und Bereitschaftstests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) von Kubernetes verwendet werden. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | Der Container stellt eine umfassende Dokumentation für die Endpunkte sowie die Funktion **Jetzt ausprobieren** bereit. Diese Funktion ermöglicht Ihnen die Eingabe Ihrer Einstellungen in einem webbasierten HTML-Formular, sodass Sie die Abfrage ausführen können, ohne Code schreiben zu müssen. Nach der Rückgabe der Abfrage wird ein cURL-Beispielbefehl bereitgestellt, der das erforderliche Format für HTTP-Header und -Text veranschaulicht. |

![Homepage des Containers](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Abrufen des Containerimages per Pull und Ausführen des Containers finden Sie unter [Konfigurieren von Containern für die Anomalieerkennung](../anomaly-detector-container-configuration.md).
* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](../anomaly-detector-container-configuration.md).
* [Erfahren Sie mehr über den Anomalieerkennungs-API-Dienst.](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
