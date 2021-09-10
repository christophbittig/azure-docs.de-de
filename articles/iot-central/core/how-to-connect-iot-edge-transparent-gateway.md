---
title: Herstellen einer Verbindung zwischen einem transparenten IoT Edge-Gateway und einer Azure IoT Central-Anwendung
description: Hier erfahren Sie mehr über das Verbinden von Geräten über ein transparentes IoT Edge-Gateway mit einer IoT Central-Anwendung.
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: c0e63d0aee60ae81c14fa8971369c3a50f9b2bde
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461398"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Verbinden von Geräten über ein transparentes IoT Edge-Gateway

Ein IoT Edge-Gerät kann als Gateway fungieren, das eine Verbindung zwischen anderen Geräten in einem lokalen Netzwerk und Ihrer IoT Central-Anwendung bereitstellt. Sie verwenden ein Gateway, wenn das Gerät nicht direkt auf Ihre IoT Central-Anwendung zugreifen kann.

IoT Edge unterstützt die [Gatewaymuster *transparent* und *Übersetzung*](../../iot-edge/iot-edge-as-gateway.md). In diesem Artikel wird zusammengefasst, wie das transparente Gatewaymuster implementiert wird. Bei diesem Muster übergibt das Gateway Nachrichten vom untergeordneten Gerät an den IoT Hub-Endpunkt in Ihrer IoT Central-Anwendung.

In diesem Artikel werden virtuelle Computer zum Hosten des untergeordneten Geräts und Gateways verwendet. In einem realen Szenario werden das untergeordnete Gerät und das Gateway auf physischen Geräten in Ihrem lokalen Netzwerk ausgeführt.

## <a name="prerequisites"></a>Voraussetzungen

Zur Durchführung der in diesem Artikel aufgeführten Schritte ist Folgendes erforderlich:

- Ein aktives Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Eine [IoT Central-Anwendung](howto-create-iot-central-application.md), die mit der Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](howto-create-iot-central-application.md).

Laden Sie die folgenden Dateien auf Ihren Computer herunter, um die Schritte in diesem Artikel ausführen zu können:

- [Thermostat-Gerätemodell](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Manifest für transparentes Gateway](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Hinzufügen von Gerätevorlagen

Sowohl für das untergeordnete Gerät als auch das Gatewaygerät sind Gerätevorlagen in IoT Central erforderlich. Mit IoT Central können Sie die Beziehung zwischen den untergeordneten Geräten und dem Gateway modellieren, damit Sie diese nach dem Herstellen der Verbindung anzeigen und verwalten können.

Erstellen Sie eine Standardgerätevorlage, die die Funktionen Ihres Geräts modelliert, um eine Gerätevorlage für ein untergeordnetes Gerät erstellen zu können. Für das Beispiel in diesem Artikel wird das Thermostat-Gerätemodell verwendet.

So erstellen Sie eine Gerätevorlage für ein untergeordnetes Gerät:

1. Erstellen Sie eine Gerätevorlage, und wählen Sie **IoT-Gerät** als Vorlagentyp aus.

1. Geben Sie auf der Seite **Anpassen** des Assistenten einen Namen wie *Thermostat* für die Gerätevorlage ein.

1. Klicken Sie nach dem Erstellen der Gerätevorlage auf **Modell importieren**. Wählen Sie ein Modell wie die zuvor heruntergeladene Datei *thermostat-1.json* aus.

1. Wählen Sie Ansichten aus, und klicken Sie dann auf **Standardansichten generieren**, um einige Standardansichten für den Thermostat zu generieren.

1. Veröffentlichen der Gerätevorlage

So erstellen Sie eine Gerätevorlage für das transparente IoT Edge-Gateway:

1. Erstellen Sie eine Gerätevorlage, und wählen Sie **Azure IoT Edge** als Vorlagentyp aus.

1. Geben Sie auf der Seite **Anpassen** des Assistenten einen Namen wie *Edge Gateway* für die Gerätevorlage ein.

1. Aktivieren Sie auf der Seite **Anpassen** des Assistenten die Option **Gateway device with downstream devices** (Gatewaygerät mit untergeordneten Geräten).

1. Klicken Sie auf der Seite **Anpassen** des Assistenten auf **Durchsuchen**. Laden Sie die zuvor heruntergeladene Datei *EdgeTransparentGatewayManifest.json* hoch.

1. Fügen Sie unter **Beziehungen** einen Eintrag zur Vorlage für das untergeordnete Gerät hinzu.

Auf dem folgenden Screenshot sehen Sie die Seite **Beziehungen** für ein IoT Edge-Gatewaygerät, das über untergeordnete Geräte verfügt, die die **Thermostat**-Gerätevorlage verwenden:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Screenshot: Beziehung der IoT Edge-Gatewaygerätvorlage mit einer Thermostat-Vorlage für untergeordnete Geräte":::

Auf dem obigen Screenshot sind für die IoT Edge-Gatewaygerätvorlage keine Module definiert. Für ein transparentes Gateway sind keine Module erforderlich, da die IoT Edge-Runtime Nachrichten von den untergeordneten Geräten an IoT Central weiterleitet. Wenn das Gateway selbst Telemetriedaten senden, Eigenschaften synchronisieren oder Befehle verarbeiten muss, können Sie diese Funktionen in der Stammkomponente oder in einem Modul definieren.

Fügen Sie die erforderlichen Cloudeigenschaften und Ansichten hinzu, bevor Sie das Gateway und die Vorlagen für untergeordnete Geräte veröffentlichen.

## <a name="add-the-devices"></a>Hinzufügen der Geräte

Wenn Sie die Geräte zu Ihrer IoT Central-Anwendung hinzufügen, können Sie die Beziehung zwischen den untergeordneten Geräten und dem transparenten Gateway definieren.

So fügen Sie die Geräte hinzu:

1. Navigieren Sie in Ihrer IoT Central-Anwendung zur Seite „Geräte“.

1. Fügen Sie eine Instanz des IoT Edge-Geräts für das transparente Gateway hinzu. In diesem Artikel lautet die Geräte-ID für das Gateway `edgegateway`.

1. Fügen Sie eine oder mehrere Instanzen des untergeordneten Geräts hinzu. In diesem Artikel handelt es sich bei den untergeordneten Geräten um Thermostate mit den IDs `thermostat1` und `thermostat2`.

1. Wählen Sie in der Geräteliste jedes einzelne untergeordnete Gerät aus, und klicken Sie auf **An Gateway anfügen**.

Der folgende Screenshot veranschaulicht, dass Sie die Liste der Geräte, die an ein Gateway angefügt sind, auf der Seite **Downstream Devices** (Untergeordnete Geräte) anzeigen können:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Screenshot: Liste der untergeordneten Geräte, die mit einem transparenten Gateway verbunden sind":::

Bei einem transparenten Gateway stellen die untergeordneten Geräte eine Verbindung mit dem Gateway selbst und nicht mit einem benutzerdefinierten Modul her, das vom Gateway gehostet wird.

Bevor Sie die Geräte bereitstellen, benötigen Sie Folgendes:

- **ID-Bereich** der IoT Central-Anwendung
- Werte für die **Geräte-ID** für das Gateway und die untergeordneten Geräte
- Werte für den **Primärschlüssel** für das Gateway und die untergeordneten Geräte

Navigieren Sie zu jedem Gerät in der Geräteliste, und klicken Sie auf **Verbinden**, um diese Werte zu ermitteln. Notieren Sie sich diese Werte, bevor Sie fortfahren.

## <a name="deploy-the-gateway-and-devices"></a>Bereitstellen des Gateways und der Geräte

Die folgenden Schritte veranschaulichen das Bereitstellen des Gateways und der untergeordneten Geräte auf virtuellen Azure-Computern, damit Sie dieses Szenario testen können. In einem realen Szenario werden das untergeordnete Gerät und das Gateway auf physischen Geräten in Ihrem lokalen Netzwerk ausgeführt.

Klicken Sie auf die folgende Schaltfläche, um zwei virtuelle Linux-Computer bereitzustellen und somit das Szenario mit dem transparenten Gateway testen zu können. Bei einem virtuellen Computer handelt es sich um ein transparentes IoT Edge-Gateway, bei dem anderen um ein untergeordnetes Gerät, das einen Thermostat simuliert:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Stellen Sie sicher, dass nach dem Bereitstellen und Ausführen der beiden virtuellen Computer das IoT Edge-Gatewaygerät auf der `edgegateway`-VM ausgeführt wird:

1. Navigieren Sie in Ihrer IoT Central-Anwendung zur Seite **Geräte**. Wenn das IoT Edge-Gatewaygerät eine Verbindung mit IoT Central hergestellt hat, lautet der Status **Bereitgestellt**.

1. Öffnen Sie das IoT Edge-Gatewaygerät, und überprüfen Sie den Status des Moduls auf der Seite **Module**. Wenn die IoT Edge-Runtime erfolgreich gestartet wurde, lautet der Status der beiden Module **$edgeAgent** und **$edgeHub** dann **Wird ausgeführt**:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Screenshot: Ausführung der Module „$edgeAgent“ und „$edgeHub“ auf dem IoT Edge-Gateway":::

> [!TIP]
> Sie müssen möglichweise mehrere Minuten warten, bis der virtuelle Computer gestartet und das Gerät in Ihrer IoT Central-Anwendung bereitgestellt wurde.

## <a name="configure-the-gateway"></a>Konfigurieren des Gateways

Damit Ihr IoT Edge-Gerät als transparentes Gateway fungieren kann, benötigt es einige Zertifikate, um den untergeordneten Geräten seine Identität nachzuweisen. In diesem Artikel werden Demozertifikate verwendet. Verwenden Sie in einer Produktionsumgebung Zertifikate von Ihrer Zertifizierungsstelle.

So können Sie die Demozertifikate generieren und auf dem Gatewaygerät installieren:

1. Verwenden Sie Secure Shell (SSH), um eine Verbindung mit Ihrem virtuellen Computer für das Gatewaygerät herzustellen und sich bei diesem anzumelden.

1. Führen Sie die folgenden Befehle aus, um das IoT Edge-Repository zu klonen und die Demozertifikate zu generieren:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Nachdem Sie die obigen Befehle ausgeführt haben, können Sie in den nächsten Schritten die folgenden Dateien verwenden:

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* – Dies ist das Zertifikat der Stammzertifizierungsstelle, das zum Erstellen aller anderen Demozertifikate für das Testen eines IoT Edge-Szenarios verwendet wird.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* – Dies ist ein Zertifikat der Gerätezertifizierungsstelle, auf das in der Datei *config.yaml* verwiesen wird. In einem Gatewayszenario bestätigt das IoT Edge-Gerät den untergeordneten Geräten seine Identität mit diesem Zertifikat der Zertifizierungsstelle.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* – Dies ist der private Schlüssel, der dem Zertifikat der Gerätezertifizierungsstelle zugeordnet ist.

    Weitere Informationen zu diesen Demozertifikaten finden Sie unter [Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten](../../iot-edge/how-to-create-test-certificates.md).

1. Öffnen Sie die Datei *config.yaml* in einem Text-Editor. Beispiel:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Suchen Sie die `Certificate settings`-Einstellungen. Heben Sie die Auskommentierung auf, und ändern Sie die Zertifikateinstellungen wie folgt:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    Im obigen Beispiel wird davon ausgegangen, dass Sie als **AzureUser** angemeldet sind und ein Zertifikat der Gerätezertifizierungsstelle namens „mycacert“ erstellt haben.

1. Speichern Sie die Änderungen, und starten Sie die IoT Edge-Runtime neu:

    ```bash
    sudo systemctl restart iotedge
    ```

Wenn die IoT Edge-Runtime nach Ihren Änderungen erfolgreich gestartet wird, wird der Status der Module **$edgeAgent** und **$edgeHub** für Ihr Gatewaygerät in IoT Central auf der Seite **Module** in **Wird ausgeführt** geändert.

Überprüfen Sie die in *config.yaml* vorgenommenen Änderungen, und lesen Sie den Artikel [Behandeln von Problemen bei Ihrem IoT Edge-Gerät](../../iot-edge/troubleshoot.md), wenn die Runtime nicht gestartet wird.

Ihr transparentes Gateway ist nun konfiguriert und kann Telemetriedaten von untergeordneten Geräten weiterleiten.

## <a name="provision-a-downstream-device"></a>Bereitstellen eines untergeordneten Geräts

Derzeit kann IoT Edge kein untergeordnetes Gerät automatisch in Ihrer IoT Central-Anwendung bereitstellen. Die folgenden Schritte veranschaulichen, wie Sie das `thermostat1`-Gerät bereitstellen. In Ihrer Umgebung muss Python 3.6 (oder höher) installiert sein, und Sie müssen mit dem Internet verbunden sein, um diese Schritte ausführen zu können. In [Azure Cloud Shell](https://shell.azure.com/) ist Python 3.7 vorinstalliert:

1. Führen Sie den folgenden Befehl aus, um das `azure.iot.device`-Modul zu installieren:

    ```bash
    pip install azure.iot.device
    ```

1. Führen Sie den folgenden Befehl aus, um das Python-Skript für die Bereitstellung herunterzuladen:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Führen Sie die folgenden Befehle aus, um `{your application id scope}` und `{your device primary key}` zu ersetzen und das untergeordnete Gerät `thermostat1` in Ihrer IoT Central-Anwendung bereitzustellen:

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

Stellen Sie in Ihrer IoT Central-Anwendung sicher, dass der **Gerätestatus** für das Gerät „thermostat1“ jetzt **Bereitgestellt** lautet. 

## <a name="configure-a-downstream-device"></a>Konfigurieren eines nachgeschalteten Geräts

Im vorherigen Abschnitt haben Sie den virtuellen Computer `edgegateway` mit den Demozertifikaten konfiguriert, damit dieser als Gateway ausgeführt werden kann. Der virtuelle Computer `leafdevice` ermöglicht Ihnen nun das Installieren eines Thermostatsimulators, der das Gateway zum Herstellen einer Verbindung mit IoT Central verwendet.

Der virtuelle Computer `leafdevice` benötigt eine Kopie des Zertifikats der Stammzertifizierungsstelle, das Sie auf der VM `edgegateway` erstellt haben. Kopieren Sie die Datei */home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem* von der VM `edgegateway` in Ihr Basisverzeichnis auf der VM `leafdevice`. Sie können den **scp**-Befehl verwenden, um Dateien von und auf virtuelle Linux-Computer zu kopieren.

Informationen zum Überprüfen der Verbindung des untergeordneten Geräts mit dem Gateway finden Sie unter [Testen der Gatewayverbindung](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

So führen Sie den Thermostatsimulator auf der `leafdevice`-VM aus:

1. Laden Sie das Python-Beispiel in Ihr Basisverzeichnis herunter:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Installieren Sie das Python-Modul für das Azure IoT-Gerät:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Legen Sie die Umgebungsvariablen zum Konfigurieren des Beispiels fest. Ersetzen Sie `{your device shared key}` durch den primären Schlüssel von `thermostat1`, den Sie sich zuvor notiert haben. Für diese Variablen wird angenommen, dass der Name der Gateway-VM `edgegateway` lautet und `thermostat1` die ID des Thermostatgeräts ist:

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Beachten Sie, dass die Verbindungszeichenfolge den Namen des Gatewaygeräts und nicht den Name eines IoT-Hubs verwendet.

1. Verwenden Sie zum Ausführen des Codes den folgenden Befehl:

    ```bash
    python3 simple_thermostat.py
    ```

    Die Ausgabe dieses Befehls sieht wie folgt aus:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Navigieren Sie zur Seite **Übersicht** für das Gerät **thermostat1**, um die Telemetriedaten in IoT Central anzuzeigen:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Screenshot: Telemetriedaten vom untergeordneten Gerät":::

    Auf der Seite **Info** können Sie vom untergeordneten Gerät gesendete Eigenschaftswerte anzeigen und auf der Seite **Befehl** Befehle für das untergeordnete Gerät aufrufen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie ein transparentes Gateway mit IoT Central konfigurieren, besteht der empfohlene nächste Schritt darin, sich weiter über [IoT Edge](../../iot-edge/about-iot-edge.md) zu informieren.
