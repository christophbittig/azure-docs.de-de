---
title: Automatisches Verwalten von Geräten mithilfe des Device Provisioning Service
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie einen automatisierten Prozess für die Bereitstellung und Außerbetriebnahme von IoT-Geräten in Azure Digital Twins mit dem Device Provisioning Service (DPS) einrichten.
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7078c136ebb072b3834af07e69fbd7d3c3d82a2b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771655"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Automatisches Verwalten von Geräten in Azure Digital Twins mithilfe des Device Provisioning Service (DPS)

In diesem Artikel erfahren Sie, wie Sie Azure Digital Twins in den [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md) integrieren.

Mithilfe der in diesem Artikel beschriebenen Lösung können Sie den Prozess zum **_Bereitstellen_** und **_Außerbetriebnehmen_** von IoT Hub-Geräten in Azure Digital Twins mithilfe des Device Provisioning Service automatisieren. 

Weitere Informationen zu den Phasen der _Bereitstellung_ und _Außerbetriebnahme_ und zu den Phasen der allgemeinen Geräteverwaltung, die in allen Unternehmens-IoT-Projekten gelten, finden Sie im Abschnitt [Lebenszyklus von Geräten](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) der Dokumentation zur IoT Hub-Geräteverwaltung.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Bereitstellung einrichten können, müssen Sie die folgenden Ressourcen einrichten:
* Eine **Azure Digital Twins-Instanz**. Befolgen Sie zum Erstellen einer Azure Digital Twins-Instanz die Anweisungen unter [Einrichten einer Instanz und der Authentifizierung](how-to-set-up-instance-portal.md). Erfassen Sie den **_Hostnamen_** der Instanz im Azure-Portal ([Anweisungen](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* Einen **IoT-Hub**. Anweisungen finden Sie im Abschnitt „Erstellen eines IoT-Hubs“ des [IoT Hub-Schnellstarts](../iot-hub/quickstart-send-telemetry-cli.md).
* Eine [Azure-Funktion](../azure-functions/functions-overview.md), mit der Digital Twins-Informationen auf Grundlage von IoT-Hub-Daten aktualisiert werden. Befolgen Sie zum Erstellen dieser Azure-Funktion die Anweisungen in [Erfassen von IoT-Hub-Daten](how-to-ingest-iot-hub-data.md). Erfassen Sie den **_Namen_** der Funktion, um ihn in diesem Artikel zu verwenden.

In diesem Beispiel wird auch ein **Gerätesimulator** verwendet, der die Bereitstellung mithilfe des Device Provisioning Service umfasst. Der Gerätesimulator befindet sich hier: [Beispiel für die Integration von Azure Digital Twins und IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Laden Sie das Beispielprojekt auf Ihren Computer herunter, indem Sie zum Beispiellink navigieren und unter dem Titel die Schaltfläche **Code durchsuchen** auswählen. Durch diese Schaltfläche gelangen Sie zum GitHub-Repository für die Beispiele, die Sie als ZIP-Datei herunterladen können, indem Sie die Schaltfläche **Code** und anschließend **ZIP herunterladen** auswählen. 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="Screenshot des Repositorys „digital-twins-iothub-integration“ auf GitHub, in dem die Schritte zum Herunterladen als ZIP-Datei hervorgehoben sind." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

Extrahieren Sie den heruntergeladenen Ordner.

Auf Ihrem Computer muss [Node.js](https://nodejs.org/download) installiert sein. Der Gerätesimulator basiert auf **Node.js**-Version 10.0. x oder höher.

## <a name="solution-architecture"></a>Lösungsarchitektur

Diese Lösung umfasst Schritte zum Bereitstellen und Außerbetriebnehmen eines Geräts in Azure Digital Twins unter Verwendung des Device Provisioning Service.

Um Geräte in der Lösung zuzuordnen, werden Daten zwischen einem Thermostatgerät und dem DPS übertragen. Die Daten fließen dann vom DPS in IoT Hub und über eine Azure-Funktion zu Azure Digital Twins.

Um ein Gerät außer Betrieb zu setzen, werden Daten aus einem manuellen Löschen von Geräten über IoT Hub, Event Hubs und eine Azure-Funktion in Azure Digital Twins übertragen.

Die folgende Abbildung veranschaulicht diese Architektur.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Diagramm eines Geräts und mehrerer Azure-Dienste in einem End-to-End-Szenario, das den Datenfluss zeigt." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Dieser Artikel ist in zwei Abschnitte unterteilt, die sich jeweils auf einen Teil dieser vollständigen Architektur konzentrieren:
* [Bereitstellen eines Geräts mithilfe des Device Provisioning Service](#auto-provision-device-using-device-provisioning-service)
* [Automatisches Außerbetriebnehmen von Geräten mithilfe von IoT Hub-Lebenszyklusereignissen](#auto-retire-device-using-iot-hub-lifecycle-events)

## <a name="auto-provision-device-using-device-provisioning-service"></a>Bereitstellen eines Geräts mithilfe des Device Provisioning Service

In diesem Abschnitt fügen Sie den Device Provisioning Service an Azure Digital Twins an, um Geräte automatisch über den unten beschriebenen Pfad bereitzustellen. Dieses Diagramm ist ein Auszug aus der [oben dargestellten](#solution-architecture) vollständigen Architektur.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="Diagramm des Bereitstellungsflusses: Auszug aus dem Lösungsarchitekturdiagramm, das den Daten von einem Thermostat zu Azure Digital Twins folgt." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

Beschreibung des Prozessflusses:
1. Das Gerät kontaktiert den DPS-Endpunkt und übergibt Informationen, um seine Identität zu bestätigen.
2. DPS überprüft die Geräteidentität, indem die Registrierungs-ID und der Schlüssel anhand der Registrierungsliste überprüft werden, und ruft eine [Azure-Funktion](../azure-functions/functions-overview.md) auf, um die Zuordnung durchzuführen.
3. Die Azure-Funktion erstellt in Azure Digital Twins einen neuen [Zwilling](concepts-twins-graph.md) für das Gerät. Der Zwilling erhält denselben Namen wie die **Registrierungs-ID** des Geräts.
4. DPS registriert das Gerät bei einem IoT-Hub und gibt den gewünschten Zwillingsstatus des Geräts an.
5. Der IoT-Hub gibt Informationen zur Geräte-ID und zur IoT-Hub-Verbindung an das Gerät zurück. Das Gerät kann nun eine Verbindung mit dem IoT-Hub herstellen.

In den folgenden Abschnitten werden die Schritte zum Einrichten dieses Ablaufs für die automatische Bereitstellung von Geräten erläutert.

### <a name="create-a-device-provisioning-service"></a>Erstellen einer Device Provisioning Service-Instanz

Wenn ein neues Gerät mit dem Device Provisioning Service bereitgestellt wird, kann in Azure Digital Twins ein neuer Zwilling für dieses Gerät erstellt werden, dessen Name mit der Registrierungs-ID identisch ist.

Erstellen Sie eine Instanz des Device Provisioning Service, die zum Bereitstellen von IoT-Geräten verwendet wird. Sie können entweder die folgenden Anweisungen für die Azure-Befehlszeilenschnittstelle anwenden oder das Azure-Portal wie unter [Einrichten des IoT Hub Device Provisioning Service über das Azure-Portal](../iot-dps/quick-setup-auto-provision.md) beschrieben verwenden.

Mit dem folgenden Azure CLI Befehl wird ein Device Provisioning Service erstellt. Sie müssen einen Namen, eine Ressourcengruppe und eine Region für den Device Provisioning Service angeben. Informationen zu Regionen mit Unterstützung des Device Provisioning Service finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
Der Befehl kann in [Cloud Shell](https://shell.azure.com) oder lokal ausgeführt werden, wenn die [Azure CLI auf dem Computer installiert](/cli/azure/install-azure-cli) ist.

```azurecli-interactive
az iot dps create --name <Device-Provisioning-Service-name> --resource-group <resource-group-name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Hinzufügen einer mit dem Device Provisioning Service zu verwendenden Funktion

Sie erstellen in der Funktions-App, die Sie im Abschnitt [Voraussetzungen](#prerequisites) erstellt haben, eine neue Funktion für die Verwendung mit dem Device Provisioning Service. Diese Funktion wird vom Device Provisioning Service in einer [benutzerdefinierten Zuweisungsrichtlinie](../iot-dps/how-to-use-custom-allocation-policies.md) zur Bereitstellung eines neuen Geräts verwendet.

Öffnen Sie zunächst auf dem Computer das Funktions-App-Projekt in Visual Studio, und führen Sie die folgenden Schritte aus.

1. Erstellen Sie zuerst im Funktions-App-Projekt in Visual Studio eine neue Funktion vom Typ *HTTP-Trigger*. Anweisungen zum Erstellen dieser Funktion finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project).

2. Fügen Sie dem Projekt ein neues NuGet-Paket hinzu: [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Möglicherweise müssen Sie dem Projekt weitere Pakete hinzufügen, wenn die im Code verwendeten Pakete nicht bereits Teil des Projekts sind.

3. Fügen Sie in der neu erstellten Funktionscodedatei den folgenden Code ein, benennen Sie die Funktion in *DpsAdtAllocationFunc.cs* um, und speichern Sie die Datei.

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

4. Veröffentlichen Sie das Projekt mit der Funktion *DpsAdtAllocationFunc.cs* in einer Funktions-App in Azure. Anweisungen zum Veröffentlichen des Projekts finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Beim erstmaligen Erstellen der Funktions-App im Abschnitt [Voraussetzungen](#prerequisites) haben Sie möglicherweise bereits eine Zugriffsrolle für die Funktion zugewiesen und die Anwendungseinstellungen für den Zugriff der App auf die Azure Digital Twins-Instanz konfiguriert. Diese Schritte müssen für die gesamte Funktions-App einmal ausgeführt werden. Vergewissern Sie sich daher, dass sie in der App durchgeführt wurden, bevor Sie fortfahren. Anweisungen finden Sie im Abschnitt [Konfigurieren der veröffentlichten App](how-to-authenticate-client.md#configure-published-app) des Artikels *Schreiben von App-Authentifizierungscode*.

### <a name="create-device-provisioning-enrollment"></a>Erstellen der Registrierung für den Device Provisioning Service

Als Nächstes müssen Sie eine Registrierung im Device Provisioning Service mithilfe einer **benutzerdefinierten Zuweisungsfunktion** erstellen. Befolgen Sie zum Erstellen einer Registrierung die Anweisungen im Abschnitt [Erstellen der Registrierung](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) des Artikels über benutzerdefinierte Zuordnungsrichtlinien in der Dokumentation zum Device Provisioning Service.

Achten Sie darauf, dass Sie beim Durchführen dieser Schritte die folgenden Optionen auswählen, um die Registrierung mit der Funktion zu verknüpfen, die Sie erstellt haben.

* **Wählen Sie, wie Geräte den Hubs zugewiesen werden sollen**: Benutzerdefiniert (Azure-Funktion verwenden).
* **Wählen Sie die IoT-Hubs aus, denen diese Gruppe zugewiesen werden kann**: Wählen Sie den Namen des IoT-Hubs aus, oder klicken Sie auf die Schaltfläche *Neuen IoT-Hub verknüpfen*, und wählen Sie in der Dropdownliste den IoT-Hub aus.

Klicken Sie als Nächstes auf die Schaltfläche *Neue Funktion auswählen*, um die Funktions-App mit der Registrierungsgruppe zu verknüpfen. Geben Sie anschließend die folgenden Werte ein:

* **Abonnement**: Ihr Azure-Abonnement wird automatisch ausgefüllt. Stellen Sie sicher, dass es das richtige Abonnement ist.
* **Funktions-App**: Wählen Sie den Namen Ihrer Funktions-App aus.
* **Funktion**: Wählen Sie „DpsAdtAllocationFunc“ aus.

Speichern Sie die Informationen.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Screenshot des Fensters „Details der benutzerdefinierten Registrierungsgruppe“ im Azure-Portal." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

Nachdem Sie die Registrierung erstellt haben, wird später in diesem Artikel der **Primärschlüssel** für die Registrierung zum Konfigurieren des Gerätesimulators verwendet.

### <a name="set-up-the-device-simulator"></a>Einrichten des Gerätesimulators

In diesem Beispiel wird ein Gerätesimulator verwendet, der die Bereitstellung mithilfe des Device Provisioning Service umfasst. Der Gerätesimulator befindet sich im [Integrationsbeispiel für Azure Digital Twins und IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/), das Sie im Abschnitt [Voraussetzungen](#prerequisites) heruntergeladen haben.

#### <a name="upload-the-model"></a>Hochladen des Modells

Der Gerätesimulator ist ein Gerät vom Typ „Thermostat“, von dem das Modell mit der folgenden ID verwendet wird: `dtmi:contosocom:DigitalTwins:Thermostat;1`. Sie müssen dieses Modell in Azure Digital Twins hochladen, bevor Sie einen Zwilling dieses Typs für das Gerät erstellen können.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Weitere Informationen zu Modellen finden Sie unter [Verwalten von Modellen](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Konfigurieren und Ausführen des Simulators

Navigieren Sie im Befehlsfenster zu dem heruntergeladenen Beispiel für die *Integration von Azure Digital Twins und IoT Hub*, das Sie zuvor entzippt haben, und dann zum Verzeichnis *device-simulator* . Installieren Sie dann die Abhängigkeiten für das Projekt, indem Sie den folgenden Befehl ausführen:

```cmd
npm install
```

Kopieren Sie als Nächstes die Datei mit der Erweiterung „.env.template“ im Verzeichnis „device-simulator“ in eine neue Datei mit der Erweiterung „.env“, und erfassen Sie die folgenden Werte zum Festlegen der Einstellungen:

* PROVISIONING_IDSCOPE: Um diesen Wert zu erhalten, navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Device Provisioning Service, wählen Sie in den Menüoptionen *Übersicht* aus, und suchen Sie das Feld *ID-Bereich*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Screenshot der Seite „Übersicht“ für den Device Provisioning Service im Azure-Portal, wobei der Wert „ID-Bereich“ hervorgehoben ist." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: Sie können eine Registrierungs-ID für Ihr Gerät auswählen.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: Diese Umgebungsvariable ist der Primärschlüssel für die Registrierung, die Sie zuvor eingerichtet haben. Um diesen Wert erneut zu erhalten, navigieren Sie im Azure-Portal zum Device Provisioning Service, wählen Sie *Registrierungen verwalten* aus, wählen Sie dann die zuvor erstellte Registrierungsgruppe aus, und kopieren Sie den *Primärschlüssel*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Screenshot der Seite „Registrierungen verwalten“ für den Device Provisioning Service im Azure-Portal, wobei der Wert für den SAS-Primärschlüssel hervorgehoben ist." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

Verwenden Sie nun die oben aufgeführten Werte, um die Einstellungen für die ENV-Datei zu aktualisieren.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device-Provisioning-Service-Scope-ID>"
PROVISIONING_REGISTRATION_ID = "<Device-Registration-ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device-Provisioning-Service-enrollment-primary-SAS-key>"
```

Speichern und schließen Sie die Datei.

### <a name="start-running-the-device-simulator"></a>Ausführen des Gerätesimulators

Starten Sie im Verzeichnis *device-simulator* den Gerätesimulator mit dem folgenden Befehl:

```cmd
node .\adt_custom_register.js
```

Sie sollten sehen, dass das Gerät registriert und mit IoT Hub verbunden ist und Nachrichten sendet.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Screenshot des Befehlsfensters mit Geräteregistrierung und gesendeten Nachrichten." lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Überprüfen

Der Flow, den Sie in diesem Artikel eingerichtet haben, führt dazu, dass das Gerät automatisch in Azure Digital Twins registriert wird. Suchen Sie mit dem folgenden [Azure Digital Twins-CLI-Befehl](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) den Zwilling des Geräts in der von Ihnen erstellten Azure Digital Twins-Instanz.

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

Sie sollten den Zwilling des Geräts in der Azure Digital Twins-Instanz sehen.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Screenshot des Befehlsfensters mit dem neu erstellten Zwilling" lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Automatisches Außerbetriebnehmen von Geräten mithilfe von IoT Hub-Lebenszyklusereignissen

In diesem Abschnitt fügen Sie IoT Hub-Lebenszyklusereignisse an Azure Digital Twins an, um Geräte automatisch über den folgenden Ablauf außer Betrieb zu nehmen. Dieses Diagramm ist ein Auszug aus der [oben dargestellten](#solution-architecture) vollständigen Architektur.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Diagramm des Ablaufs zum Außerbetriebnehmen von Geräten: ein Auszug aus dem Lösungsarchitekturdiagramm, das den Daten aus einem Gerätelöschvorgang zu Azure Digital Twins folgt." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

Beschreibung des Prozessflusses:
1. Ein externer oder manueller Prozess löst das Löschen eines Geräts in IoT Hub aus.
2. IoT Hub löscht das Gerät und generiert ein [Gerätelebenszyklusereignis](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle), das an einen [Event Hub](../event-hubs/event-hubs-about.md) weitergeleitet wird.
3. Eine Azure-Funktion löscht den Zwilling des Geräts in Azure Digital Twins.

In den folgenden Abschnitten werden die Schritte zum Einrichten dieses Ablaufs für die automatische Außerbetriebnahme von Geräten erläutert.

### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Als Nächstes erstellen Sie einen [Azure Event Hub](../event-hubs/event-hubs-about.md) zum Empfangen von IoT Hub-Lebenszyklusereignissen. 

Führen Sie die in der Schnellstartanleitung zum [Erstellen eines Event Hubs](../event-hubs/event-hubs-create.md) beschriebenen Schritte aus. Benennen Sie den Event Hub mit *lifecycleevents*. Sie verwenden diesen Event Hub-Namen, wenn Sie in den nächsten Abschnitten eine IoT Hub-Route und eine Azure-Funktion einrichten.

Der folgende Screenshot veranschaulicht die Erstellung des Event Hubs.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Screenshot des Azure-Portal-Fensters, der zeigt, wie ein Event Hub mit dem Namen „lifecycleevents“ erstellt wird." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Erstellen einer SAS-Richtlinie für den Event Hub

Als Nächstes müssen Sie eine [SAS-Richtlinie (Shared Access Signature)](../event-hubs/authorize-access-shared-access-signature.md) erstellen, um den Event Hub mit Ihrer Funktions-App zu konfigurieren.
So erstellen Sie die SAS-Richtlinie
1. Navigieren Sie zu dem Event Hub, die Sie im Azure-Portal erstellt haben, und wählen Sie in den Menüoptionen auf der linken Seite die Option **Freigegebene Zugriffsrichtlinien** aus.
2. Wählen Sie **Hinzufügen** aus. Geben Sie im daraufhin geöffneten Fenster *SAS-Richtlinie hinzufügen* einen Richtliniennamen Ihrer Wahl ein, und aktivieren Sie das Kontrollkästchen *Lauschen*.
3. Klicken Sie auf **Erstellen**.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie man eine Event Hub-SAS-Richtlinie hinzufügt." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Konfigurieren des Event Hubs mit der Funktions-App

Konfigurieren Sie als Nächstes die Azure-Funktions-App, die Sie im Abschnitt [Voraussetzungen](#prerequisites) eingerichtet haben, für die Verwendung mit dem neuen Event Hub. Sie konfigurieren die Funktion, indem Sie in der Funktions-App mit der Verbindungszeichenfolge des Event Hubs eine Umgebungsvariable festlegen.

1. Öffnen Sie die erstellte Richtlinie, und kopieren Sie den Wert von **Verbindungszeichenfolge – Primärschlüssel**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie man den Wert von „Verbindungszeichenfolge – Primärschlüssel“ kopiert." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Fügen Sie mit dem folgenden Azure CLI-Befehl die Verbindungszeichenfolge als Variable in den Funktions-App-Einstellungen hinzu. Der Befehl kann in [Cloud Shell](https://shell.azure.com) oder lokal ausgeführt werden, wenn die [Azure CLI auf dem Computer installiert](/cli/azure/install-azure-cli) ist.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event-Hubs-SAS-connection-string-Listen>" --resource-group <resource-group> --name <your-function-app-name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Hinzufügen einer Funktion zur Außerbetriebnahme mit IoT Hub-Lebenszyklusereignissen

Sie erstellen in dem Funktions-App-Projekt, das Sie im Abschnitt [Voraussetzungen](#prerequisites) erstellt haben, eine neue Funktion, um ein vorhandenes Gerät mithilfe von IoT Hub-Lebenszyklusereignissen außer Betrieb zu nehmen.

Weitere Informationen zu Lebenszyklusereignissen finden Sie unter [Nicht telemetriebezogene Ereignisse in IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Weitere Informationen zur Verwendung von Event Hubs mit Azure-Funktionen finden Sie unter [Azure Event Hubs-Trigger für Azure Functions](../azure-functions/functions-bindings-event-hubs-trigger.md).

Öffnen Sie zunächst auf dem Computer das Funktions-App-Projekt in Visual Studio, und führen Sie die folgenden Schritte aus.

1. Erstellen Sie zuerst im Funktions-App-Projekt in Visual Studio eine neue Funktion vom Typ *Event Hub-Trigger*. Anweisungen zum Erstellen dieser Funktion finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project).

2. Fügen Sie dem Projekt ein neues NuGet-Paket hinzu: [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Möglicherweise müssen Sie dem Projekt weitere Pakete hinzufügen, wenn die im Code verwendeten Pakete nicht bereits Teil des Projekts sind.

3. Fügen Sie in der neu erstellten Funktionscodedatei den folgenden Code ein, benennen Sie die Funktion in *DeleteDeviceInTwinFunc.cs* um, und speichern Sie die Datei.

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

4. Veröffentlichen Sie das Projekt mit der Funktion *DeleteDeviceInTwinFunc.cs* in einer Funktions-App in Azure. Anweisungen zum Veröffentlichen des Projekts finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Beim erstmaligen Erstellen der Funktions-App im Abschnitt [Voraussetzungen](#prerequisites) haben Sie möglicherweise bereits eine Zugriffsrolle für die Funktion zugewiesen und die Anwendungseinstellungen für den Zugriff der App auf die Azure Digital Twins-Instanz konfiguriert. Diese Schritte müssen für die gesamte Funktions-App einmal ausgeführt werden. Vergewissern Sie sich daher, dass sie in der App durchgeführt wurden, bevor Sie fortfahren. Anweisungen finden Sie im Abschnitt [Konfigurieren der veröffentlichten App](how-to-authenticate-client.md#configure-published-app) des Artikels *Schreiben von App-Authentifizierungscode*.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Erstellen einer IoT Hub-Route für Lebenszyklusereignisse

Jetzt richten Sie eine IoT Hub-Route ein, um Lebenszyklusereignisse für das Gerät weiterzuleiten. In diesem Fall lauschen Sie speziell auf Ereignisse, bei denen ein Gerät gelöscht wird. Diese sind durch `if (opType == "deleteDeviceIdentity")` gekennzeichnet. Dieses Ereignis wird das Löschen des Elements für den digitalen Zwilling und den Außerbetriebnahmeprozess eines Geräts und des zugehörigen digitalen Zwillings auslösen.

Zunächst müssen Sie im IoT-Hub einen Event Hub-Endpunkt erstellen. Anschließend fügen Sie im IoT-Hub eine Route hinzu, um Lebenszyklusereignisse an diesen Event Hub-Endpunkt zu senden.
Führen Sie die folgenden Schritte aus, um einen Event Hub-Endpunkt zu erstellen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/)zu dem IoT-Hub, den Sie im Abschnitt [Voraussetzungen](#prerequisites) erstellt haben, und wählen Sie in den Menüoptionen auf der linken Seite **Nachrichtenrouting** aus.
2. Wählen Sie die Registerkarte **Benutzerdefinierte Endpunkte**.
3. Wählen Sie **+ Hinzufügen** und dann **Event Hubs** aus, um einen Endpunkt vom Typ „Event Hubs“ hinzuzufügen.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Screenshot des Visual Studio-Fensters, der zeigt, wie man einen benutzerdefinierten Event Hub-Endpunkt hinzufügt." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. Wählen Sie im daraufhin geöffneten Fenster *Event Hub-Endpunkt hinzufügen* die folgenden Werte aus:
    * **Endpunktname**: Wählen Sie einen Endpunktnamen aus.
    * **Event Hub-Namespace**: Wählen Sie in der Dropdownliste den Event Hub-Namespace aus.
    * **Event Hub-Instanz**: Wählen Sie den Event Hub-Namen aus, den Sie im vorherigen Schritt erstellt haben.
5. Klicken Sie auf **Erstellen**. Lassen Sie dieses Fenster geöffnet, um im nächsten Schritt eine Route hinzuzufügen.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Screenshot des Visual Studio-Fensters, der zeigt, wie man einen Event Hub-Endpunkt hinzufügt." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

Als Nächstes fügen Sie eine Route hinzu, die eine Verbindung mit dem im obigen Schritt erstellten Endpunkt herstellt, mit einer Routingabfrage, die die Löschereignisse sendet. Führen Sie zum Erstellen einer Route die folgenden Schritte aus:

1. Navigieren Sie zur Registerkarte *Routen*, und wählen Sie **Hinzufügen** aus, um eine Route hinzuzufügen.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Screenshot des Visual Studio-Fensters, der zeigt, wie man eine Route zum Senden von Ereignissen hinzufügt." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. Wählen Sie auf der daraufhin geöffneten Seite *Route hinzufügen* die folgenden Werte aus:

   * **Name**: Wählen Sie einen Namen für die Route aus. 
   * **Endpunkt**: Wählen Sie in der Dropdownliste den Event Hubs-Endpunkt aus, den Sie zuvor erstellt haben.
   * **Datenquelle**: Wählen Sie *Ereignisse im Gerätelebenszyklus* aus.
   * **Routingabfrage**: Geben Sie `opType='deleteDeviceIdentity'` ein. Diese Abfrage schränkt das Senden der Gerätelebenszklusereignisse auf Löschereignisse ein.

3. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Screenshot des Azure-Portal-Fensters, der zeigt, wie man eine Route hinzufügt, um Lebenszyklusereignisse zu senden." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

Wenn Sie diese Schritte durchlaufen haben, ist alles für die End-to-End-Außerbetriebnahme der Geräte vorbereitet.

### <a name="validate"></a>Überprüfen

Um die Außerbetriebnahme auszulösen, müssen Sie das Gerät manuell aus IoT Hub löschen.

Sie können das Gerät mit einem [Azure CLI-Befehl](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) oder im Azure-Portal manuell aus IoT Hub löschen. Führen Sie die folgenden Schritte aus, um das Gerät im Azure Portal zu löschen:

1. Navigieren Sie zum IoT-Hub, und wählen Sie in den Menüoptionen auf der linken Seite **IoT-Geräte** aus. 
2. Es wird ein Gerät mit der Geräteregistrierungs-ID angezeigt, die Sie in der [ersten Hälfte dieses Artikels](#auto-provision-device-using-device-provisioning-service) ausgewählt haben. Sie können auch ein beliebiges anderes Gerät zum Löschen auswählen, solange es über einen Zwilling in Azure Digital Twins verfügt, damit Sie überprüfen können, ob der Zwilling nach dem Löschen des Geräts automatisch gelöscht wird.
3. Wählen Sie das Gerät aus, und klicken Sie auf **Löschen**.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie man einen Gerätezwilling aus den IoT-Geräten löscht." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Es kann einige Minuten dauern, bis die Änderungen in Azure Digital Twins angezeigt werden.

Mit dem folgenden [Azure Digital Twins CLI-Befehl](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) können Sie überprüfen, ob der Zwilling des Geräts in der Azure Digital Twins-Instanz gelöscht wurde.

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

Sie sollten sehen, dass der Zwilling des Geräts in der Azure Digital Twins-Instanz nicht mehr gefunden werden kann.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="Screenshot des Befehlsfensters, der zeigt, dass der Zwilling nicht mehr gefunden wird." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Artikel erstellten Ressourcen nicht mehr benötigen, folgen Sie den Schritten unten, um sie zu löschen.

Bei Verwendung von Azure Cloud Shell oder der lokalen Azure CLI können Sie alle Azure-Ressourcen in einer Ressourcengruppe mit dem Befehl [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) löschen. Dieser Befehl entfernt Ressourcengruppe, die Azure Digital Twins-Instanz, den IoT-Hub und die Registrierung des Hubgeräts, das Event Grid-Thema und die zugehörigen Abonnements, den Event Hub-Namespace sowie beide Azure Functions-Apps (einschließlich zugeordneter Ressourcen, z. B. Speicher).

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Löschen Sie dann den Beispielordner des Projekts, den Sie heruntergeladen haben, von Ihrem lokalen Computer.

## <a name="next-steps"></a>Nächste Schritte

Die für die Geräte erstellten digitalen Zwillinge werden als flache Hierarchie in Azure Digital Twins gespeichert, können jedoch für die Organisation mit Modellinformationen und einer Hierarchie mit mehreren Ebenen erweitert werden. Weitere Informationen zu diesem Vorgang finden Sie hier:

* [Digitale Zwillinge und der Zwillingsgraph](concepts-twins-graph.md)

Weitere Informationen zur Verwendung von HTTP-Anforderungen mit Azure-Funktionen finden Sie unter:

* [HTTP-Trigger in Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md)

Sie können eine benutzerdefinierte Logik schreiben, um diese Informationen mithilfe der bereits in Azure Digital Twins gespeicherten Modell- und Diagrammdaten automatisch bereitzustellen. Weitere Informationen zum Verwalten, Aktualisieren und Abrufen von Informationen aus dem Zwillingsgraphen finden Sie in den folgenden Schrittanleitungen:

* [Verwalten eines digitalen Zwillings](how-to-manage-twin.md)
* [Abfragen des Zwillingsgraphen](how-to-query-graph.md)