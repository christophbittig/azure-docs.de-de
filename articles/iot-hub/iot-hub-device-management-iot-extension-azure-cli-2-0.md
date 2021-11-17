---
title: Azure IoT-Geräteverwaltung mit IoT-Erweiterung für Azure CLI | Microsoft-Dokumentation
description: Verwenden Sie das Tool IoT-Erweiterung für Azure CLI mit den Verwaltungsmethoden für direkte Methoden und gewünschte Eigenschaften von Gerätezwillingen.
author: eross-msft
manager: ''
keywords: Azure Iot-Geräteverwaltung, Azure IoT Hub-Geräteverwaltung, IoT-Geräteverwaltung, IoT Hub-Geräteverwaltung
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: lizross
ms.openlocfilehash: 765dd7f8dd179187c904c5b779609823cd3503af
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555014"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Verwenden der IoT-Erweiterung für Azure CLI für die Verwaltung von Azure IoT Hub-Geräten

![Lückenloses Diagramm](media/iot-hub-get-started-e2e-diagram/2.png)

In diesem Artikel erfahren Sie, wie Sie die IoT-Erweiterung für die Azure CLI mit verschiedenen Verwaltungsoptionen auf Ihrem Entwicklungscomputer verwenden können. [Die IoT-Erweiterung für Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ist eine Open-Source-IoT-Erweiterung, die die Funktionen der [Azure CLI](/cli/azure/overview) ergänzt. Die Azure CLI enthält Befehle zum Interagieren mit Azure Resource Manager- und Verwaltungsendpunkten. So können Sie beispielsweise die Azure CLI verwenden, um einen virtuellen Azure-Computer oder einen IoT Hub zu erstellen. Eine CLI-Erweiterung ermöglicht es einem Azure-Dienst, die Azure-Befehlszeilenschnittstelle zu ergänzen, wodurch Sie Zugriff auf zusätzliche dienstspezifische Funktionen erhalten. Die IoT-Erweiterung ermöglicht IoT-Entwicklern Befehlszeilenzugriff auf alle IoT Hub-, IoT Edge- und IoT Hub Device Provisioning Service-Funktionen.

| Verwaltungsoption          | Aufgabe  |
|----------------------------|-----------|
| Direkte Methoden             | Lassen Sie ein Gerät beispielsweise mit dem Senden von Nachrichten beginnen oder dies beenden, oder starten Sie es neu.                                        |
| Gewünschte Eigenschaften von Gerätezwillingen    | Setzen Sie ein Gerät in bestimmte Status, stellen Sie z.B. das Leuchten einer grünen LED ein, oder legen Sie das Telemetriesendeintervall auf 30 Minuten fest.         |
| Berichtete Eigenschaften von Gerätezwillingen   | Rufen Sie den berichteten Status eines Geräts ab. Das Gerät meldet z.B., das die LED jetzt blinkt.                                    |
| Gerätezwillingstags                  | Speichern gerätespezifischer Metadaten in der Cloud – beispielsweise den Aufstellungsort eines Automaten.                         |
| Gerätezwillingabfragen        | Fragen Sie alle Gerätezwillinge ab, um diejenigen mit beliebigen Bedingungen abzurufen. Identifizieren Sie beispielsweise die zur Verwendung verfügbaren Geräte. |

Eine ausführlichere Erläuterung zu den Unterschieden und Anweisungen zur Verwendung dieser Optionen finden Sie im [Leitfaden zur D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md) und [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md).

Gerätezwillinge sind JSON-Dokumente, in denen Gerätestatusinformationen (Metadaten, Konfigurationen und Bedingungen) gespeichert werden. Von IoT Hub wird für jedes Gerät, das eine Verbindung herstellt, dauerhaft ein Gerätezwilling gespeichert. Weitere Informationen zu Gerätezwillingen finden Sie unter [Erste Schritte mit Gerätezwillingen (Node)](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen das Tutorial [Verbinden des Raspberry Pi-Onlinesimulators mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-web-simulator-get-started.md) oder eines der gerätespezifischen Tutorials abgeschlossen haben. Sie können beispielsweise zu [Verbinden von Raspberry Pi mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-kit-node-get-started.md) oder zu einer der Schnellstartanleitungen zum [Senden von Telemetriedaten](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) wechseln. In diesen Artikeln werden folgende Anforderungen beschrieben:

  * Ein aktives Azure-Abonnement.
  * Ein Azure IoT Hub in Ihrem Abonnement.
  * Eine Clientanwendung, die Nachrichten an Ihren Azure IoT Hub sendet.

* Stellen Sie sicher, dass Ihr Gerät im Verlauf dieses Tutorials mit der Clientanwendung ausgeführt wird.

* [Python 2.7x oder Python 3.x](https://www.python.org/downloads/)

* Die Azure-Befehlszeilenschnittstelle Installationsinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). Ihre Azure CLI-Version muss mindestens 2.0.70 lauten. Verwenden Sie `az –version`, um dies zu überprüfen.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Installieren Sie die IoT-Erweiterung. Die einfachste Möglichkeit ist die Ausführung von `az extension add --name azure-iot`. In der [Infodatei zur IoT-Erweiterung](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) werden mehrere Wege zum Installieren der Erweiterung beschrieben.

## <a name="sign-in-to-your-azure-account"></a>Anmelden bei Ihrem Azure-Konto

Melden Sie sich mithilfe des folgenden Befehls bei Ihrem Azure-Konto an:

```azurecli
az login
```

## <a name="direct-methods"></a>Direkte Methoden

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Gewünschte Eigenschaften von Gerätezwillingen

Legen Sie mit folgendem Befehl für die gewünschte Eigenschaft ein Intervall von 3.000 fest:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval=3000
```

Diese Eigenschaft kann von Ihrem Gerät gelesen werden.

## <a name="device-twin-reported-properties"></a>Gemeldete Eigenschaften von Gerätezwillingen

Zeigen Sie mithilfe des folgenden Befehls die berichteten Eigenschaften des Geräts an:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Eine der vom Zwilling gemeldeten Eigenschaften ist „$metadata.$lastUpdated“, die anzeigt, wann die Geräte-App das letzte Mal ihren gemeldeten Eigenschaftssatz aktualisiert hat.

## <a name="device-twin-tags"></a>Tags von Gerätezwillingen

Zeigen Sie mithilfe des folgenden Befehls die Tags und Eigenschaften des Geräts an:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Fügen Sie mit folgendem Befehl dem Gerät eine Feldrolle „temperature&humidity“ hinzu:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags='{"role":"temperature&humidity"}'
```

## <a name="device-twin-queries"></a>Gerätezwillingabfragen

Fragen Sie mit folgendem Befehl Geräte mit einem Rollentag „temperature&humidity“ ab:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Fragen Sie mit folgendem Befehl alle Geräte außer denen mit einem Rollentag „temperature&humidity“ ab:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, Gerät-zu-Cloud-Nachrichten zu überwachen und Cloud-zu-Gerät-Nachrichten zwischen dem IoT-Gerät und Azure IoT Hub zu senden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]