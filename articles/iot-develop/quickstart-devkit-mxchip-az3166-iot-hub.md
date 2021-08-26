---
title: 'Schnellstart: Verbinden eines MXCHIP AZ3166 mit Azure IoT Hub'
description: Mithilfe von eingebetteter Azure RTOS-Software können Sie ein MXCHIP AZ3166-Gerät mit Azure IoT Hub verbinden und Telemetriedaten senden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/09/2021
ms.openlocfilehash: aea20a9f8a6f0725220a60e425b48e22c6aa8794
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862120"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-hub"></a>Schnellstart: Verbinden eines MXCHIP AZ3166-DevKit mit IoT Hub

**Gilt für**: [Entwicklung von eingebetteten Geräten](about-iot-develop.md#embedded-device-development)<br>
**Gesamtdauer**: 30 Minuten

[![Code durchsuchen](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

In diesem Schnellstart verwenden Sie Azure RTOS, um ein MXCHIP AZ3166-IoT-DevKit (nachfolgend als „MXCHIP-DevKit“ bezeichnet) mit Azure IoT zu verbinden. 

Sie verwenden auch IoT Explorer und IoT Plug & Play, um das MXCHIP-DevKit zu verwalten. IoT Plug & Play stellt ein offenes Gerätemodell bereit, mit dem Anwendungen die Funktionen eines Geräts programmgesteuert abfragen und damit interagieren können. Ein Gerät verwendet dieses Modell, um seine Funktionen an eine IoT Plug & Play-fähige Anwendung zu übertragen. Mithilfe dieses Modells können Sie die Aufgaben zum Hinzufügen, Konfigurieren und Verwalten von Geräten optimieren und verbessern. Weitere Informationen finden Sie in der [Dokumentation zu IoT Plug & Play](../iot-develop/index.yml).

Sie führen die folgenden Aufgaben aus:

* Installieren von eingebetteten Entwicklungstools für die Programmierung des MXCHIP-DevKit in C
* Erstellen eines Images und Einspielen auf das MXCHIP-DevKit
* Verwenden von Azure CLI zum Erstellen und Verwalten eines Azure IoT-Hubs, mit dem das MXCHIP-DevKit eine sichere Verbindung herstellt
* Verwenden von Azure IoT Explorer zum Registrieren eines Geräts bei Ihrem IoT Hub, Anzeigen von Geräteeigenschaften und Gerätetelemetriedaten sowie zum Aufrufen direkter Befehle auf dem Gerät

## <a name="prerequisites"></a>Voraussetzungen

* PC mit Microsoft Windows 10
* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* [Git](https://git-scm.com/downloads) zum Klonen des Repositorys
* Azure-Befehlszeilenschnittstelle. In dieser Schnellstartanleitung gibt es zwei Möglichkeiten zum Ausführen von Azure CLI-Befehlen:
    * Verwenden Sie Azure Cloud Shell. Dabei handelt es sich um eine interaktive Shell, mit der CLI-Befehle im Browser ausgeführt werden. Diese Option wird empfohlen, da Sie nichts installieren müssen. Wenn Sie Cloud Shell zum ersten Mal verwenden, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Führen Sie in der [Cloud Shell-Schnellstartanleitung](../cloud-shell/quickstart.md) die Schritte zum **Starten von Cloud Shell** und **Auswählen der Bash-Umgebung** aus.
    * Führen Sie optional die Azure CLI auf dem lokalen Computer aus. Wenn die Azure CLI bereits installiert ist, führen Sie `az upgrade` aus, um die CLI und die Erweiterungen auf die aktuelle Version zu aktualisieren. Informationen zur Installation von Azure CLI finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): Plattformübergreifendes Hilfsprogramm zum Überwachen und Verwalten von Azure IoT 
* Hardware

    * [MXCHIP AZ3166-IoT-DevKit](https://www.seeedstudio.com/AZ3166-IOT-Developer-Kit.html) (MXCHIP-DevKit)
    * WLAN, 2,4 GHz
    * Kabel: USB 2.0 A-Stecker zu Micro-USB-Stecker

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung

Zum Einrichten Ihrer Entwicklungsumgebung klonen Sie zuerst ein GitHub-Repository, in dem alle für den Schnellstart benötigten Ressourcen enthalten sind. Anschließend installieren Sie einige Programmiertools.

### <a name="clone-the-repo-for-the-quickstart"></a>Klonen des Repositorys für den Schnellstart

Klonen Sie das folgende Repository, um den gesamten Beispielgerätecode, die Setupskripts und die Offlineversionen der Dokumentation herunterzuladen. Wenn Sie dieses Repository bereits im Rahmen einer anderen Schnellstartanleitung geklont haben, müssen Sie diesen Schritt nicht erneut ausführen.

Führen Sie den folgenden Befehl aus, um das Repository zu klonen:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Installieren der Tools

Das geklonte Repository enthält ein Setupskript, mit dem die erforderlichen Tools installiert und konfiguriert werden. Wenn Sie diese Tools im Rahmen einer anderen Schnellstartanleitung für ein eingebettetes Gerät installiert haben, müssen Sie diesen Schritt nicht erneut ausführen.

> [!NOTE]
> Mit dem Setupskript werden die folgenden Tools installiert:
> * [CMake](https://cmake.org): Buildvorgang
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): Kompiliervorgang
> * [Termite](https://www.compuphase.com/software_termite.htm): Überwachung der Ausgabe des seriellen Anschlusses für verbundene Geräteressourcen

So installieren Sie die Tools

1. Navigieren Sie im Datei-Explorer zum folgenden Pfad im Repository, und führen Sie das Setupskript mit dem Namen *get-toolchain.bat* aus:

    *getting-started\tools\get-toolchain.bat*

1. Öffnen Sie nach der Installation ein neues Konsolenfenster, um die vom Setupskript vorgenommenen Konfigurationsänderungen zu überprüfen. Verwenden Sie diese Konsole für die Ausführung der restlichen Programmieraufgaben in der Schnellstartanleitung. Sie können die Windows-Eingabeaufforderung, PowerShell oder Git Bash für Windows verwenden.
1. Führen Sie den folgenden Code aus, um zu überprüfen, ob Version 3.14 (oder höher) von CMake installiert ist.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Erstellen der Cloudkomponenten

### <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Sie können über Azure CLI einen IoT-Hub erstellen, der Ereignisse und Messaging für Ihr Gerät verarbeitet.

Erstellen Sie wie folgt einen IoT-Hub:

1. Starten der CLI-App. Kopieren Sie zum Ausführen der CLI-Befehle im restlichen Teil dieses Schnellstarts die Befehlssyntax, fügen Sie sie in Ihre CLI-App ein, bearbeiten Sie die Variablenwerte und drücken Sie Enter.
    - Wenn Sie lieber Cloud Shell verwenden möchten, klicken Sie mit der rechten Maustaste auf den Link für [Azure Cloud Shell](https://shell.azure.com/bash), und wählen Sie die Option zum Öffnen auf einer neuen Registerkarte aus.
    - Wenn Sie die Azure CLI lokal verwenden, starten Sie Ihre CLI-Konsolen-App, und melden Sie sich bei Azure CLI an.

1. Führen Sie [az extension add](/cli/azure/extension?view=azure-cli-latest#az_extension_add&preserve-view=true) aus, um die Erweiterung *azure-iot* zu installieren oder auf die aktuelle Version zu aktualisieren.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. Führen Sie den Befehl [az group create](/cli/azure/group#az-group-create) aus, um eine Ressourcengruppe zu erstellen. Der folgende Befehl erstellt am Standort *centralus* die Ressourcengruppe *myResourceGroup*.

    > [!NOTE] 
    > Optional können Sie einen alternativen `location` (Standort) festlegen. Führen Sie [az account list-locations](/cli/azure/account#az-account-list-locations) aus, um verfügbare Standorte anzuzeigen.

    ```azurecli
    az group create --name MyResourceGroup --location centralus
    ```

1. Führen Sie den Befehl [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) aus, um einen IoT-Hub zu erstellen. Es kann einige Minuten dauern, bis ein IoT-Hub erstellt wurde.

    *YourIotHubName*: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben. Der Name eines IoT-Hubs muss in Azure global eindeutig sein. Dieser Platzhalter wird im restlichen Teil dieses Schnellstarts als eindeutiger Name für Ihren IoT-Hub verwendet.

    Der Parameter `--sku F1` erstellt den IoT-Hub im Free-Tarif. Bei Hubs im Free-Tarif gibt es nur einen begrenzten Featuresatz, und sie werden für Proof of Concept-Anwendungen verwendet. Weitere Informationen zu IoT Hub-Tarifen, -Features und -Preisen finden Sie unter [Azure IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub). 

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName} --sku F1 --partition-count 2
    ```

1. Nachdem der IoT-Hub erstellt wurde, zeigen Sie die JSON-Ausgabe in der Konsole an, und kopieren Sie den Wert `hostName`, der in einem späteren Schritt verwendet wird. Der Wert `hostName` sieht wie im folgenden Beispiel aus:

    `{Your IoT hub name}.azure-devices.net`

### <a name="configure-iot-explorer"></a>Konfigurieren von IoT Explorer

Im weiteren Verlauf dieses Schnellstarts verwenden Sie IoT Explorer zum Registrieren eines Geräts bei Ihrem IoT Hub, zum Anzeigen der Geräteeigenschaften und Telemetriedaten sowie zum Senden von Befehlen an Ihr Gerät. In diesem Abschnitt konfigurieren Sie IoT Explorer, um eine Verbindung mit dem soeben erstellten IoT-Hub herzustellen und Plug & Play-Modelle aus dem öffentlichen Modellrepository zu lesen. 

So fügen Sie Ihrem IoT-Hub eine Verbindung hinzu:

1. Führen Sie in Ihrer CLI-App den Befehl [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) aus, um die Verbindungszeichenfolge für Ihren IoT-Hub abzurufen.

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. Kopieren Sie die Verbindungszeichenfolge ohne die umgebenden Anführungszeichen.
1. Wählen Sie in Azure IoT Explorer im linken Menü **IoT-Hubs** und dann **+ Verbindung hinzufügen** aus.
1. Fügen Sie die Verbindungszeichenfolge im Feld **Verbindungszeichenfolge** ein.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-connection.png" alt-text="Screenshot: Hinzufügen einer Verbindung in IoT Explorer":::

1. Wenn die Verbindung erfolgreich hergestellt wurde, wechselt IoT Explorer zur Ansicht **Geräte**.

So fügen Sie das öffentliche Modellrepository hinzu:

1. Wählen Sie in IoT Explorer **Startseite** aus, um zur Ansicht „Startseite“ zurückzukehren.
1. Wählen Sie im linken Menü die Option **IoT Plug & Play-Einstellungen**, anschließend **+Hinzufügen** und dann im Dropdownmenü die Option **Öffentliches Repository** aus.
1. Unter `https://devicemodels.azure.com` wird ein Eintrag für das öffentliche Modellrepository angezeigt.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-public-repository.png" alt-text="Screenshot: Hinzufügen des öffentlichen Modellrepositorys in IoT Explorer":::

1. Wählen Sie **Speichern** aus.

### <a name="register-a-device"></a>Registrieren eines Geräts

In diesem Abschnitt erstellen Sie eine neue Geräteinstanz und registrieren sie bei dem von Ihnen erstellten IoT-Hub. In einem Abschnitt weiter unten verwenden Sie die Verbindungsinformationen für das neu registrierte Gerät, um eine sichere Verbindung für Ihr physisches Gerät herzustellen.

So registrieren Sie ein Gerät:

1. Wählen Sie in IoT Explorer auf der Startseite die Option **IoT-Hubs** aus.
1. Die zuvor hinzugefügte Verbindung sollte angezeigt werden. Wählen Sie unter den Verbindungseigenschaften die Option **Geräte in diesem Hub anzeigen** aus.
1. Wählen Sie **+ Neu** aus, und geben Sie eine Geräte-ID für Ihr Gerät ein. Beispiel: *mydevice*. Behalten Sie alle anderen Eigenschaften unverändert bei.
1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-created.png" alt-text="Screenshot: Azure IoT Explorer-Geräteidentität":::

1. Verwenden Sie die Schaltflächen zum Kopieren, um die Felder **Geräte-ID** und **Primärschlüssel** zu kopieren und sich zu notieren.

Bevor Sie mit dem nächsten Abschnitt fortfahren, vergewissern Sie sich, dass Sie die folgenden Werte kopiert haben:

* `hostName`
* `deviceId`
* `primaryKey`

## <a name="prepare-the-device"></a>Vorbereiten des Geräts

Zum Verbinden des MXCHIP-DevKit mit Azure bearbeiten Sie eine Konfigurationsdatei mit WLAN- und Azure IoT-Einstellungen, erstellen das Image neu und spielen das Image per Flashvorgang auf dem Gerät ein.

### <a name="add-configuration"></a>Hinzufügen einer Konfiguration

1. Öffnen Sie die folgende Datei in einem Text-Editor:

    *getting-started\MXChip\AZ3166\app\azure_config.h*

1. Kommentieren Sie die folgende Zeile am Anfang der Datei wie hier gezeigt aus:

    ```c
    // #define ENABLE_DPS
    ```

1. Legen Sie die WLAN-Konstanten auf die folgenden Werte Ihrer lokalen Umgebung fest.

    |Konstantenname|Wert|
    |-------------|-----|
    |`WIFI_SSID` |{*Ihre WLAN-SSID*}|
    |`WIFI_PASSWORD` |{*Ihr WLAN-Kennwort*}|
    |`WIFI_MODE` |{*Einer der enumerierten WLAN-Moduswerte in der Datei*}|

1. Legen Sie die Konstanten aus den Azure IoT-Geräteinformationen auf die Werte fest, die Sie nach dem Erstellen der Azure-Ressourcen gespeichert haben.

    |Konstantenname|Wert|
    |-------------|-----|
    |`IOT_HUB_HOSTNAME` |{*Ihr Wert für „Iot hub hostName“* }|
    |`IOT_DPS_REGISTRATION_ID` |{*Ihr Wert für die Geräte-ID*}|
    |`IOT_DEVICE_SAS_KEY` |{*Ihr Wert für den Primärschlüssel*}|

1. Speichern und schließen Sie die Datei.

### <a name="build-the-image"></a>Erstellen des Image

1. Führen Sie in Ihrer Konsole oder im Datei-Explorer das Skript *rebuild.bat* unter dem folgenden Pfad aus, um das Image zu erstellen:

    *getting-started\MXChip\AZ3166\tools\rebuild.bat*

2. Vergewissern Sie sich nach Abschluss des Buildvorgangs, dass die Binärdatei unter dem folgenden Pfad erstellt wurde:

    *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>Einspielen des Images

1. Suchen Sie auf dem MXCHIP-DevKit nach der Taste **Reset** und dem Micro-USB-Anschluss. Sie verwenden diese Komponenten in den folgenden Schritten. Die Komponenten sind in der folgenden Abbildung gekennzeichnet:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/mxchip-iot-devkit.png" alt-text="Ermitteln der wichtigen Komponenten auf dem MXChip-DevKit-Board":::

1. Schließen Sie das Micro-USB-Kabel an den Micro-USB-Anschluss des MXCHIP-DevKit und dann an Ihren Computer an.
1. Suchen Sie im Datei-Explorer nach der Binärdatei, die Sie im vorherigen Abschnitt erstellt haben.
1. Kopieren Sie die Binärdatei *mxchip_azure_iot.bin*.
1. Suchen Sie im Datei-Explorer nach dem MXCHIP-DevKit-Gerät, das mit Ihrem Computer verbunden wurde. Das Gerät wird auf Ihrem System als Laufwerk mit der Bezeichnung **AZ3166** angezeigt.
1. Fügen Sie die Binärdatei in den Stammordner des MXCHIP-DevKit ein. Der Flashvorgang wird automatisch gestartet und ist nach wenigen Sekunden abgeschlossen.

    > [!NOTE]
    > Während des Flashvorgangs leuchtet auf dem MXCHIP-DevKit ein grüne LED auf.

### <a name="confirm-device-connection-details"></a>Bestätigen der Details zur Geräteverbindung

Sie können die **Termite**-App verwenden, um die Kommunikation zu überwachen und sich zu vergewissern, dass Ihr Gerät richtig eingerichtet wurde.

1. Starten Sie **Termite**.
    > [!TIP]
    > Falls Sie für Termite keine Verbindung mit Ihrem DevKit herstellen können, sollten Sie den [ST-LINK-Treiber](https://www.st.com/en/development-tools/stsw-link009.html) installieren und es anschließend erneut versuchen. Weitere Schritte finden Sie auf der Seite zur [Problembehandlung](troubleshoot-embedded-device-quickstarts.md).
1. Wählen Sie **Settings** aus.
1. Überprüfen Sie im Dialogfeld **Serial port settings** (Einstellungen für den seriellen Anschluss) die folgenden Einstellungen, und ändern Sie sie bei Bedarf:
    * **Baud rate** (Baudrate): 115.200
    * **Port** (Anschluss): Der Anschluss, an dem Ihr MXCHIP-DevKit angeschlossen ist. Falls die Dropdownliste mehrere Anschlussoptionen enthält, können Sie hier den richtigen Anschluss auswählen. Öffnen Sie den **Geräte-Manager** von Windows, und ermitteln Sie unter **Anschlüsse**, welcher Anschluss verwendet wird.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/termite-settings.png" alt-text="Screenshot: Einstellungen für serielle Anschlüsse in der Termite-App":::

1. Wählen Sie „OK“ aus.
1. Drücken Sie an Ihrem Gerät die Taste **Reset**. Die Taste ist auf dem Gerät entsprechend beschriftet und befindet sich in der Nähe des Micro-USB-Anschlusses.
1. Überprüfen Sie in der **Termite**-App die folgenden Prüfpunktwerte, um sich zu vergewissern, dass das Gerät initialisiert und mit Azure IoT verbunden wurde.

    ```output
    Starting Azure thread

    Initializing WiFi
        MAC address: C8:93:46:8A:4C:43
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.18
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 157.245.166.169
        SNTP time update: Jun 8, 2021 18:16:50.807 UTC
    SUCCESS: SNTP initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

Lassen Sie Termite geöffnet, damit Sie die Geräteausgabe während der folgenden Schritte verfolgen können.

## <a name="view-device-properties"></a>Anzeigen von Geräteeigenschaften

Mithilfe von Azure IoT Explorer können Sie die Eigenschaften Ihrer Geräte anzeigen und verwalten. In diesem Abschnitt und den folgenden Abschnitten verwenden Sie die in IoT Explorer angezeigten Plug & Play-Funktionen, um das MXCHIP-DevKit zu verwalten und mit ihm zu interagieren. Diese Funktionen basieren auf dem Gerätemodell, das für das MXCHIP-DevKit im öffentlichen Modellrepository veröffentlicht wurde. Sie haben IoT Explorer so konfiguriert, dass dieses Repository weiter oben in diesem Schnellstart nach Gerätemodellen durchsucht wird. In vielen Fällen können Sie dieselbe Aktion ohne Plug & Play ausführen, indem Sie in IoT Explorer im linken Menü Ihres Gerätebereichs dieselbe Aktion auswählen. Die Verwendung von Plug & Play bietet jedoch oft eine erweiterte Funktionalität. Der Grund: IoT Explorer kann das von einem Plug & Play-Gerät angegebene Gerätemodell lesen und spezifische Informationen für dieses Gerät anzeigen.  

So greifen Sie in IoT Explorer auf IoT Plug & Play-Komponenten für das Gerät zu:

1. Wählen Sie in der Startansicht von IoT Explorer die Option **IoT-Hubs** und dann **Geräte in diesem Hub anzeigen** aus.
1. Wählen Sie Ihr Gerät aus.
1. Wählen Sie **IoT Plug & Play-Komponenten** aus.
1. Wählen Sie **Standardkomponente** aus. IoT Explorer zeigt die IoT Plug & Play-Komponenten an, die auf Ihrem Gerät implementiert wurden.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-default-component-view.png" alt-text="Screenshot: MXCHIP DevKit-Standardkomponente in IoT Explorer":::

1. Zeigen Sie auf der Registerkarte **Schnittstelle** in der **Beschreibung** des Gerätemodells den JSON-Inhalt an. Der JSON-Code enthält Konfigurationsdetails für die einzelnen IoT Plug & Play-Komponenten im Gerätemodell.

    Jede Registerkarte in IoT Explorer entspricht einer der IoT Plug & Play-Komponenten im Gerätemodell.

    | Registerkarte | type | Name | BESCHREIBUNG |
    |---|---|---|---|
    | **Interface** | Schnittstelle | `MXCHIP Getting Started Guide` | Beispielmodell für das MXCHIP-DevKit |
    | **Eigenschaften (schreibgeschützt)** | Eigenschaft | -- | Bei dem Modell gibt es zurzeit keine schreibgeschützten Eigenschaften |
    | **Eigenschaften (schreibbar)** | Eigenschaft | `telemetryInterval` | Das Intervall, in dem das Gerät Telemetriedaten sendet |
    | **Befehle** | Befehl | `setLedState` | Zum Ein- oder Ausschalten der LED |
    | **Telemetrie** | Telemetrie | `temperature` | Die Temperatur in Celsius |

So zeigen Sie Geräteeigenschaften mithilfe von Azure IoT Explorer an:

1. Wählen Sie die Registerkarte **Eigenschaften (schreibgeschützt)** aus. Zurzeit gibt es keine schreibgeschützten Eigenschaften, die vom Gerätemodell verfügbar gemacht werden.
1. Wählen Sie die Registerkarte **Eigenschaften (schreibbar)** aus. Sie zeigt das Intervall an, in dem Telemetriedaten gesendet werden.
1. Ändern Sie den Wert für `telemetryInterval` in *5*, und wählen Sie **Gewünschten Wert aktualisieren** aus. Ihr Gerät verwendet dieses Intervall jetzt zum Senden von Telemetriedaten.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-set-telemetry-interval.png" alt-text="Screenshot: Festlegen des Telemetrieintervalls für MXCHIP-DevKit in IoT Explorer":::

1. IoT Explorer antwortet mit einer Benachrichtigung. Sie können das Update auch in Termite beobachten.
1. Legen Sie das Telemetrieintervall wieder auf „10“ fest.
 
So verwenden Sie Azure CLI zum Anzeigen von Geräteeigenschaften:

1. Führen Sie den Befehl [az iot hub device-identity show](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show) aus.

    ```azurecli
    az iot hub device-identity show --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. Überprüfen Sie in der Konsolenausgabe die Eigenschaften für Ihr Gerät.

## <a name="view-telemetry"></a>Anzeigen von Telemetriedaten

Bei Azure IoT Explorer können Sie den Fluss der Telemetriedaten von Ihrem Gerät in die Cloud anzeigen. Optional können Sie dieselbe Aufgabe über die Azure CLI ausführen.

So zeigen Sie Telemetriedaten in Azure IoT Explorer an:

1. Wählen Sie in IoT Explorer im Bereich **IoT Plug & Play-Komponenten** (Standardkomponente) für Ihr Gerät die Registerkarte **Telemetriedaten** aus. Bestätigen Sie, dass für **Use built-in event hub** (Integrierten Event Hub verwenden) der Wert *Yes* (Ja) festgelegt wurde.
1. Wählen Sie **Starten** aus.
1. Zeigen Sie die Telemetriedaten an, während das Gerät Nachrichten in die Cloud sendet.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-telemetry.png" alt-text="Screenshot: Gerätetelemetrie in IoT Central":::

    > [!NOTE]
    > Sie können die Telemetriedaten auch auf dem Gerät überwachen, indem Sie die Termite-App verwenden.

1. Aktivieren Sie das Kontrollkästchen **Modellierte Ereignisse anzeigen,** um die Ereignisse in dem durch das Gerätemodell festgelegten Datenformat anzuzeigen.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-show-modeled-events.png" alt-text="Screenshot: Modellierte Telemetrieereignisse in IoT Explorer":::

1. Wählen Sie **Beenden** aus, um den Empfang von Ereignissen zu beenden.

So verwenden Sie Azure CLI zum Anzeigen von Gerätetelemetriedaten:

1. Führen Sie den Befehl [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) aus. Verwenden Sie die Namen, die Sie zuvor in Azure IoT für Ihr Gerät und Ihren IoT-Hub erstellt haben.

    ```azurecli
    az iot hub monitor-events --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. Zeigen Sie die JSON-Ausgabe in der Konsole an.

    ```json
    {
        "event": {
            "origin": "mydevice",
            "module": "",
            "interface": "dtmi:azurertos:devkit:gsgmxchip;1",
            "component": "",
            "payload": "{\"humidity\":41.21,\"temperature\":31.37,\"pressure\":1005.18}"
        }
    }
    ```

1. Wählen Sie STRG+C aus, um die Überwachung zu beenden.


## <a name="call-a-direct-method-on-the-device"></a>Aufrufen einer direkten Methode auf dem Gerät

Sie können auch mithilfe von Azure IoT Explorer eine direkte Methode aufrufen, die Sie auf Ihrem Gerät implementiert haben. Direkte Methoden haben einen Namen und können optional über JSON-Nutzdaten, eine konfigurierbare Verbindung und ein Methodentimeout verfügen. In diesem Abschnitt rufen Sie eine Methode auf, die eine LED ein- oder ausschaltet. Optional können Sie dieselbe Aufgabe über die Azure CLI ausführen.

So rufen Sie eine Methode in Azure IoT Explorer auf:

1. Wählen Sie in IoT Explorer im Bereich **IoT Plug & Play-Komponenten** (Standardkomponente) für Ihr Gerät die Registerkarte **Befehle** aus.
1. Legen Sie beim Befehl **setLedState** den **Status** auf **true** fest.
1. Wählen Sie **Befehl senden** aus. Im IoT Explorer sollte eine Benachrichtigung angezeigt werden, und die gelbe Benutzer-LED auf dem Gerät sollte eingeschaltet werden.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-invoke-method.png" alt-text="Screenshot: Aufrufen der „setLedState“-Methode im IoT-Explorer":::

1. Legen Sie den **Status** auf **false** fest, und wählen Sie **Befehl senden** aus. Die gelbe Benutzer-LED sollte ausgeschaltet werden.
1. Optional können Sie die Ausgabe in Termite anzeigen, um den Status der Methoden zu überwachen.

So verwenden Sie Azure CLI zum Aufrufen einer Methode:

1. Führen Sie den Befehl [az iot hub invoke-device-method](/cli/azure/iot/hub#az_iot_hub_invoke_device_method) aus, und geben Sie den Methodennamen und die Nutzdaten an. Bei dieser Methode wird durch das Festlegen von `method-payload` auf `true` die LED eingeschaltet, und durch das Festlegen auf `false` wird sie ausgeschaltet.

    ```azurecli
    az iot hub invoke-device-method --device-id mydevice --method-name setLedState --method-payload true --hub-name {YourIoTHubName}
    ```

    Die CLI-Konsole zeigt den Status Ihres Methodenaufrufs auf dem Gerät an, wobei `204` Erfolg bedeutet.

    ```json
    {
        "payload": {},
        "status": 200
    }    
    ```

1. Überprüfen Sie Ihr Gerät, um den LED-Zustand zu bestätigen.

1. Zeigen Sie das Termite-Terminal an, um die Ausgabemeldungen zu bestätigen:

    ```output
    Receive direct method: setLedState
        Payload: true
    LED is turned ON
    Device twin property sent: {"ledState":true}
    ```

## <a name="troubleshoot-and-debug"></a>Problembehandlung und Debuggen

Wenn beim Erstellen des Gerätecodes, beim Einspielen per Flashvorgang auf dem Gerät oder beim Herstellen einer Verbindung Probleme auftreten, helfen Ihnen die Informationen zur [Problembehandlung](troubleshoot-embedded-device-quickstarts.md) weiter.

Informationen zum Debuggen der Anwendung finden Sie unter [Debuggen mit Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Schnellstart erstellten Azure-Ressourcen nicht mehr benötigen, können Sie die Ressourcengruppe und alle zugehörigen Ressourcen über die Azure CLI löschen.

> [!IMPORTANT] 
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen.

So löschen Sie eine Ressourcengruppe anhand des Namens:

1. Führen Sie den Befehl [az group delete](/cli/azure/group#az-group-delete) aus. Die von Ihnen erstellte Ressourcengruppe, der IoT-Hub und die Geräteregistrierung werden entfernt.

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```

1. Führen Sie den Befehl [az group list](/cli/azure/group#az-group-list) aus, um sich zu vergewissern, dass die Ressourcengruppe gelöscht wurde.  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie ein benutzerdefiniertes Image mit Azure RTOS-Beispielcode erstellt und es anschließend per Flashvorgang auf dem MXCHIP DevKit-Gerät eingespielt. Außerdem haben Sie die Azure CLI und/oder IoT Explorer verwendet, um Azure-Ressourcen zu erstellen, das MXCHIP DevKit auf sichere Weise mit Azure zu verbinden, die Telemetriedaten anzuzeigen und Nachrichten zu senden.

Lesen Sie als Nächstes die folgenden Artikel, um mehr darüber zu erfahren, wie Geräte mithilfe der IoT-Geräte-SDKs mit Azure IoT verbunden werden. 

> [!div class="nextstepaction"]
> [Verbinden eines MXCHIP AZ3166-DevKits mit IoT Central](quickstart-devkit-mxchip-az3166.md)
> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Hub](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Mit Azure RTOS werden für OEMs Komponenten bereitgestellt, mit denen diese die Kommunikation schützen, Code erstellen und die Datenisolation sicherstellen können, indem sie die zugrunde liegenden MCU/MPU-Mechanismen für den Hardwareschutz verwenden. Letztendlich ist aber jeder OEM selbst dafür verantwortlich sicherzustellen, dass sein Gerät die sich ständig weiter entwickelnden Sicherheitsanforderungen erfüllt.
