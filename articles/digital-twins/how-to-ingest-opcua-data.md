---
title: Erfassen von OPC UA-Daten mit Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Schritte zum Abrufen Ihrer Azure OPC UA-Daten in Azure Digital Twins
author: danhellem
ms.author: dahellem
ms.date: 5/20/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b191bdb1303ae0210573d295ffb5b371cc81ddfb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355903"
---
# <a name="ingesting-opc-ua-data-with-azure-digital-twins"></a>Erfassen von OPC UA-Daten mit Azure Digital Twins

Die [OPC Unified Architecture (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/) ist eine plattformunabhängige, dienstorientierte Architektur für den Fertigungsbereich. Sie wird verwendet, um Telemetriedaten von Geräten abzurufen. 

Um OPC UA-Serverdaten in Azure Digital Twins zu übertragen, müssen mehrere Komponenten auf verschiedenen Geräten installiert sein. Benutzerdefinierter Code und einige Einstellungen müssen außerdem konfiguriert werden. 

In diesem Artikel erfahren Sie, wie Sie alle diese Teile miteinander verbinden, um Ihre OPC UA-Knoten in Azure Digital Twins zu übertragen. Sie können anhand dieser Anleitungen Ihre eigenen Lösungen aufbauen.

> [!NOTE]
> In diesem Artikel wird das Konvertieren von OPC UA-Knoten in DTDL nicht behandelt. Es wird nur das Abrufen der Telemetriedaten von Ihrem OPC UA-Server in vorhandene Azure Digital Twins-Instanzen beschrieben. Wenn Sie DTDL-Modelle aus OPC UA-Daten generieren möchten, besuchen Sie die Repositorys [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) und [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie diesen Artikel nachvollziehen können, müssen die folgenden Voraussetzungen erfüllt sein:
* **Herunterladen des Beispiel-Repositorys**: In diesem Artikel werden eine [DTDL-Modelldatei](concepts-models.md) und ein Azure-Funktionstext aus dem [GitHub-Repository „OPC UA to Azure Digital Twins“](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) verwendet. Laden Sie zunächst das Beispielrepository auf Ihren Computer herunter. Sie können die Schaltfläche **Code** für das Repository auswählen, um das Repository zu klonen oder als ZIP-Datei auf Ihren Computer herunterzuladen.

    :::image type="content" source="media/how-to-ingest-opcua-data/download-repo.png" alt-text="Screenshot des Repositorys „digital-twins-samples“ auf GitHub, in dem die Schritte zum Klonen oder Herunterladen des Codes hervorgehoben sind." lightbox="media/how-to-ingest-opcua-data/download-repo.png":::
    
    Wenn Sie das Repository als ZIP-Datei herunterladen, müssen Sie diese entzippen und die Dateien extrahieren.
* **Herunterladen von Visual Studio**: In diesem Artikel wird Visual Studio verwendet, um eine Azure-Funktion zu veröffentlichen. Sie können die neueste Version von Visual Studio unter [Visual Studio-Downloads](https://visualstudio.microsoft.com/downloads/) herunterladen.

## <a name="architecture"></a>Aufbau

Die folgenden Komponenten sind in dieser Lösung enthalten.

 :::image type="content" source="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png" alt-text="Abbildung der Architektur „OPC UA zu Azure Digital Twins“" lightbox="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png":::    

| Komponente | BESCHREIBUNG |
| --- | --- |
| OPC UA-Server | OPC UA-Server von [ProSys](https://www.prosysopc.com/products/opc-ua-simulation-server/) oder [Kepware,](https://www.kepware.com/en-us/products/#KEPServerEX) um die OPC UA-Daten zu simulieren. |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) | IoT Edge ist ein IoT Hub-Dienst, der auf einem lokalen Linux-Gatewaygerät installiert wird. Er ist erforderlich, damit das OPC Publisher-Modul ausgeführt wird und Daten an IoT Hub sendet. |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | Dies ist ein IoT Edge-Modul, das vom Azure Industrial IoT-Team erstellt wurde. Dieses Modul stellt eine Verbindung mit Ihrem OPC UA-Server her und sendet die Knotendaten an Azure IoT Hub. |
| [Azure IoT Hub](../iot-hub/about-iot-hub.md) | OPC Publisher sendet die OPC UA-Telemetriedaten an Azure IoT Hub. Von dort aus können Sie die Daten über eine Azure-Funktion und in Azure Digital Twins verarbeiten. |
| Azure Digital Twins | Die Plattform, mit der Sie eine digitale Darstellung realer Dinge, Orte, Unternehmensprozesse und Personen erstellen können. |
| [Azure-Funktion](../azure-functions/functions-overview.md) | Eine benutzerdefinierte Azure-Funktion wird verwendet, um die Telemetriedaten, die in Azure IoT Hub fließen, zu den richtigen Zwillingen und Eigenschaften in Azure Digital Twins zu verarbeiten. |

## <a name="set-up-edge-components"></a>Einrichten von Edgekomponenten

Der erste Schritt besteht darin, die Geräte und Software in der Edgeumgebung einzurichten. Sie richten die folgenden Edgekomponenten in dieser Reihenfolge ein:
1. [OPC UA-Simulationsserver](#set-up-opc-ua-server)
1. [IoT Hub- und IoT Edge-Gerät](#set-up-iot-edge-device)
1. [Gatewaygerät](#set-up-gateway-device)

In diesem Abschnitt wird das jeweilige Setup kurz erläutert. 

Ausführlichere Informationen zum Installieren der einzelnen Komponenten finden Sie in den folgenden Ressourcen:
* [Schritt-für-Schritt-Anleitung zum Installieren von OPC Publisher auf Azure IoT Edge](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [Installieren von IoT Edge unter Linux](../iot-edge/how-to-install-iot-edge.md) 
* [OPC Publisher auf GitHub](https://github.com/Azure/iot-edge-opc-publisher)
* [Konfigurieren von OPC Publisher](/previous-versions/azure/iot-accelerators/howto-opc-publisher-configure)

### <a name="set-up-opc-ua-server"></a>Einrichten des OPC UA-Servers

Für diesen Artikel benötigen Sie keinen Zugriff auf physische Geräte, auf denen ein echter OPC UA-Server ausgeführt wird. Stattdessen können Sie den kostenlosen [Prosys OPC UA-Simulationsserver](https://www.prosysopc.com/products/opc-ua-simulation-server/) auf einer Windows-VM installieren, um die OPC UA-Daten zu generieren. In diesem Abschnitt wird dieses Setup erläutert.

Wenn Sie bereits über ein physisches OPC UA-Gerät oder einen anderen OPC UA-Simulationsserver verfügen, den Sie verwenden möchten, können Sie mit dem nächsten Abschnitt [Einrichten des IoT Edge-Geräts](#set-up-iot-edge-device) fortfahren.

#### <a name="create-windows-10-virtual-machine"></a>Erstellen eines virtuellen Windows 10-Computers

Die Prosys-Software erfordert eine einfache virtuelle Ressource. Erstellen Sie mithilfe des [Azure-Portals](https://portal.azure.com) [einen virtuellen Windows 10-Computer (VM)](../virtual-machines/windows/quick-create-portal.md) mit den folgenden Spezifikationen:
* **Verfügbarkeitsoptionen**: Keine Infrastrukturredundanz erforderlich
* **Image**: Windows 10 Pro, Version 2004 – Gen2
* **Größe**: Standard_B1s – 1 vcpu, 1 GiB Arbeitsspeicher

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png" alt-text="Screenshot: Azure-Portal mit der Registerkarte „Grundlagen“ des Setups virtueller Windows-Computer." lightbox="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png":::

Ihre VM muss über das Internet erreichbar sein. Der Einfachheit halber können Sie in dieser exemplarischen Vorgehensweise alle Ports öffnen und der VM eine öffentliche IP-Adresse zuweisen. Dies erfolgt auf der Registerkarte **Netzwerk** des Setups des virtuellen Computers.

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-2.png" alt-text="Screenshot: Azure-Portal mit der Registerkarte „Netzwerk“ des Setups des virtuellen Windows-Computers.":::

> [!WARNING]
> Das Öffnen aller Ports für das Internet wird für Produktionslösungen nicht empfohlen, da dies ein Sicherheitsrisiko darstellen kann. Möglicherweise möchten Sie andere Sicherheitsstrategien für Ihre Umgebung in Betracht ziehen.

Erfassen Sie den Wert der **Öffentlichen IP-Adresse**, der im nächsten Schritt verwendet wird.
 
Stellen Sie das VM-Setup fertig.

#### <a name="install-opc-ua-simulation-software"></a>Installieren der OPC UA-Simulationssoftware

Installieren Sie auf Ihrem neuen virtuellen Windows-Computer den [Prosys OPC UA-Simulationsserver](https://www.prosysopc.com/products/opc-ua-simulation-server/).

Starten Sie den Server, nachdem der Download und die Installation abgeschlossen wurden. Es kann einige Augenblicke dauern, bis der OPC UA-Server gestartet wird. Sobald er bereit ist, sollte der Serverstatus als **Wird ausgeführt** angezeigt werden.

Kopieren Sie nun den Wert von **Verbindungsadresse (UA TCP)** . Fügen Sie ihn an einem sicheren Ort ein, um ihn später zu verwenden. Ersetzen Sie im eingefügten Wert den Computernamensteil der Adresse wie folgt durch die **Öffentliche IP-Adresse** Ihrer VM: 

`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`

Sie verwenden diesen aktualisierten Wert später in diesem Artikel.

Zeigen Sie schließlich die Simulationsknoten an, die standardmäßig mit dem Server bereitgestellt werden, indem Sie die Registerkarte **Objekte** auswählen und die Ordner „Objects::FolderType“ und „Simulation::FolderType“ erweitern. Die Simulationsknoten werden mit jeweils einem eigenen eindeutigen `NodeId`-Wert angezeigt. 

Erfassen Sie die `NodeId`-Werte für die simulierten Knoten, die Sie veröffentlichen möchten. Sie benötigen diese IDs später in diesem Artikel, um Daten von diesen Knoten zu simulieren.

> [!TIP]
> Vergewissern Sie sich, dass auf den OPC UA-Server zugegriffen werden kann, indem Sie die Schritte unter „Überprüfen, ob der OPC UA-Dienst ausgeführt wird und erreichbar ist“ in der [Schritt-für-Schritt-Anleitung zum Installieren von OPC Publisher auf Azure IoT Edge](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) ausführen.

#### <a name="verify-completion"></a>Überprüfen der abgeschlossenen Schritte

In diesem Abschnitt richten Sie den OPC UA-Server zum Simulieren von Daten ein. Überprüfen Sie, ob Sie die folgende Prüfliste abgearbeitet haben:

> [!div class="checklist"]
> * Der Prosys-Simulationsserver wurde eingerichtet und wird ausgeführt.
> * Sie haben die UA-TCP-Verbindungsadresse (`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`) kopiert.
> * Sie haben die Liste der `NodeId`s für die simulierten Knoten erfasst, die veröffentlicht werden sollen. 

### <a name="set-up-iot-edge-device"></a>Einrichten eines IoT Edge-Geräts

In diesem Abschnitt richten Sie eine IoT Hub-Instanz und ein IoT Edge-Gerät ein. 

[Erstellen Sie zunächst eine Azure IoT Hub-Instanz](../iot-hub/iot-hub-create-through-portal.md). Für diesen Artikel können Sie eine Instanz im Tarif **F1 – Free** erstellen.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-hub.png" alt-text="Screenshot: Azure-Portal mit den Eigenschaften einer IoT Hub-Instanz.":::

Nachdem Sie die Azure IoT Hub-Instanz erstellt haben, wählen Sie im linken Navigationsmenü der Instanz **IoT Edge** und dann **IoT Edge-Gerät hinzufügen** aus.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-1.png" alt-text="Screenshot: Hinzufügen eines IoT Edge-Geräts im Azure-Portal.":::

Befolgen Sie die Anweisungen zum Erstellen eines neuen Geräts. 

Kopieren Sie nach der Erstellung Ihres Geräts entweder den Wert **Primäre Verbindungszeichenfolge** oder **Sekundäre Verbindungszeichenfolge**. Sie benötigen diese Angaben später, wenn Sie das Edgegerät einrichten.

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-2.png" alt-text="Screenshot: Azure-Portal mit IoT Edge-Geräteverbindungszeichenfolgen.":::

#### <a name="verify-completion"></a>Überprüfen der abgeschlossenen Schritte

In diesem Abschnitt richten Sie IoT Edge und IoT Hub ein, um ein Gatewaygerät zu erstellen. Überprüfen Sie, ob Sie die folgende Prüfliste abgearbeitet haben:
> [!div class="checklist"]
> * Die IoT Hub-Instanz wurde erstellt.
> * Das IoT Edge-Gerät wurde bereitgestellt.

### <a name="set-up-gateway-device"></a>Einrichten eines Gatewaygeräts

Um Ihre OPC UA-Serverdaten in IoT Hub zu übertragen, benötigen Sie ein Gerät, auf dem IoT Edge mit dem OPC Publisher-Modul ausgeführt wird. OPC Publisher lauscht dann auf OPC UA-Knotenaktualisierungen und veröffentlicht die Telemetriedaten im JSON-Format in IoT Hub.

#### <a name="create-ubuntu-server-virtual-machine"></a>Erstellen einer Ubuntu Server-VM

Erstellen Sie mithilfe des [Azure-Portals](https://portal.azure.com) eine Ubuntu Server-VM mit den folgenden Spezifikationen:
* **Verfügbarkeitsoptionen**: Keine Infrastrukturredundanz erforderlich
* **Image**: Ubuntu Server 18.04 LTS – Gen1
* **Größe**: Standard_B1ms – 1 vcpu, 2 GiB Arbeitsspeicher
    - Die Standardgröße (Standard_b1s – vcpu, 1GiB-Arbeitsspeicher) ist für RDP zu langsam. Das Aktualisieren auf 2 GiB Arbeitsspeicher bietet eine bessere RDP-Leistung.

:::image type="content" source="media/how-to-ingest-opcua-data/ubuntu-virtual-machine.png" alt-text="Screenshot: Azure-Portal mit Einstellungen für Ubuntu-VM.":::

> [!NOTE]
> Wenn Sie sich für eine RDP-Verbindung mit Ihrer Ubuntu-VM entscheiden, können Sie die Anweisungen unter [Installieren und Konfigurieren von XRDP für die Verwendung von Remotedesktop mit Ubuntu](../virtual-machines/linux/use-remote-desktop.md) befolgen.

#### <a name="install-iot-edge-container"></a>Installieren des IoT Edge-Containers

Befolgen Sie die Anweisungen zum [Installieren von IoT Edge unter Linux](../iot-edge/how-to-install-iot-edge.md).

Führen Sie nach Abschluss der Installation den folgenden Befehl aus, um den Status Ihrer Installation zu überprüfen:

```bash
admin@gateway:~$ sudo iotedge check
```

Mit diesem Befehl werden mehrere Tests ausgeführt, um sicherzustellen, dass die Installation einsatzbereit ist.

#### <a name="install-opc-publisher-module"></a>Installieren des OPC Publisher-Moduls

Installieren Sie nun das OPC Publisher-Modul auf Ihrem Gatewaygerät. 

Beginnen Sie, indem Sie das Modul aus [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.iotedge-opc-publisher) abrufen.

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-1.png" alt-text="Screenshot: OPC Publisher in Azure Marketplace.":::

Führen Sie dann die Installationsschritte aus, die im [GitHub-Repository „OPC Publisher“](https://github.com/Azure/iot-edge-opc-publisher) dokumentiert sind, um das Modul auf Ihrer Ubuntu-VM zu installieren.

Fügen Sie im Schritt zum [Angeben von Containererstellungsoptionen](https://github.com/Azure/iot-edge-opc-publisher#specifying-container-create-options-in-the-azure-portal) den folgenden JSON-Code hinzu:

```JSON
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=/appdata/publishednodes.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-2.png" alt-text="Screenshot: Optionen zum Erstellen von OPC Publisher-Containern.":::

>[!NOTE]
>Die oben genannten Erstellungsoptionen sollten in den meisten Fällen ohne Änderungen funktionieren. Wenn Sie jedoch Ihr eigenes Gatewaygerät verwenden, das sich von den bisherigen Anleitungen im Artikel unterscheidet, müssen Sie die Einstellungen möglicherweise an Ihre Situation anpassen.

Befolgen Sie die restlichen Eingabeaufforderungen, um das Modul zu erstellen. 

Nach ungefähr 15 Sekunden können Sie den Befehl `iotedge list` auf Ihrem Gatewaygerät ausführen, der alle Module auflistet, die auf Ihrem IoT Edge Gerät ausgeführt werden. Das OPC Publisher-Modul sollte betriebsbereit sein und ausgeführt werden.

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-list.png" alt-text="Screenshot der Ergebnisse von „iotedge list“.":::

Navigieren Sie abschließend zum Verzeichnis `/iiotedge`, und erstellen Sie eine Datei *publishednodes.json*. Die IDs in der Datei müssen mit den `NodeId`-Werten übereinstimmen, die Sie [zuvor von OPC Server erfasst haben](#install-opc-ua-simulation-software). Ihre Datei sollte in etwa wie folgt aussehen:

```JSON
[
    {
        "EndpointUrl": "opc.tcp://20.185.195.172:53530/OPCUA/SimulationServer",
        "UseSecurity": false,
        "OpcNodes": [
            {
                "Id": "ns=3;i=1001"
            },
            {
                "Id": "ns=3;i=1002"
            },
            {
                "Id": "ns=3;i=1003"
            },
            {
                "Id": "ns=3;i=1004"
            },
            {
                "Id": "ns=3;i=1005"
            },
            {
                "Id": "ns=3;i=1006"
            }
        ]
    }
]
```

Speichern Sie die Änderungen in der Datei *publishednodes.json*.

Führen Sie dann den folgenden Befehl aus:

```bash
sudo iotedge logs OPCPublisher -f
```

Der Befehl führt zur Ausgabe der OPC Publisher-Protokolle. Wenn alles ordnungsgemäß konfiguriert ist und ausgeführt wird, wird etwa Folgendes angezeigt:

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-logs.png" alt-text="Screenshot: iotedge-Protokolle im Terminal. Links befindet sich eine Spalte mit Diagnoseinformationsfeldern, auf der rechten Seite eine Spalte mit Werten.":::

Daten sollten nun von einem OPC UA-Server in Ihre IoT Hub-Instanz übertragen werden.

Zum Überwachen der Nachrichten, die in Azure IoT Hub eingehen, können Sie den folgenden Befehl verwenden:

```azurecli-interactive
az iot hub monitor-events -n <iot-hub-instance> -t 0
```

> [!TIP]
> Verwenden Sie [Azure IoT Explorer,](../iot-fundamentals/howto-use-iot-explorer.md) um IoT Hub-Nachrichten zu überwachen.

#### <a name="verify-completion"></a>Überprüfen der abgeschlossenen Schritte

In diesem Abschnitt richten Sie ein Gatewaygerät ein, auf dem IoT Edge ausgeführt wird, das Daten vom OPC UA-Server empfängt. Überprüfen Sie, ob Sie die folgende Prüfliste abgearbeitet haben:
> [!div class="checklist"]
> * Die Ubuntu Server-VM wurde erstellt.
> * IoT Edge wurde installiert und befindet sich auf der Ubuntu-VM.
> * Das OPC Publisher-Modul wurde installiert.
> * Die Datei *publishednodes.json* wurde erstellt und konfiguriert.
> * Das OPC Publisher-Modul wird ausgeführt, und Telemetriedaten werden an IoT Hub übertragen.

Im nächsten Schritt übermitteln Sie diese Telemetriedaten an Azure Digital Twins.

## <a name="set-up-azure-digital-twins"></a>Einrichten von Azure Digital Twins

Nachdem Sie nun Daten vom OPC UA-Server in Azure IoT Hub übertragen haben, besteht der nächste Schritt darin, Azure Digital Twins einzurichten und zu konfigurieren. 

In diesem Beispiel verwenden Sie ein einzelnes Modell und eine einzelne Zwillingsinstanz, um eine Übereinstimmung mit den Eigenschaften auf dem Simulationsserver herzustellen. 

>[!TIP]
>Wenn Sie an einem komplexeren Szenario mit mehr Modellen und Zwillingen interessiert sind, sehen Sie sich das Schokoladenfabrikbeispiel im [GitHub-Repository „OPC UA to Azure Digital Twins](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) an.

### <a name="create-azure-digital-twins-instance"></a>Erstellen einer Azure Digital Twins-Instanz

Stellen Sie zunächst eine neue Azure Digital Twins-Instanz bereit, indem Sie die Anleitung unter [Einrichten einer Instanz und von Authentifizierung](how-to-set-up-instance-portal.md) verwenden.

### <a name="upload-model-and-create-twin"></a>Hochladen des Modells und Erstellen eines Zwillings

Fügen Sie Ihrer Instanz nun ein Modell und einen Zwilling hinzu. Die Modelldatei, die Sie in die Instanz hochladen, ist Teil des Beispielprojekts, das Sie im Abschnitt [Voraussetzungen](#prerequisites) unter *Simulation Example/simulation-dtdl-model.json* heruntergeladen haben.

Sie können den [Azure Digital Twins-Explorer](concepts-azure-digital-twins-explorer.md) verwenden, um das Simulationsmodell hochzuladen und einen neuen Zwilling namens **simulation-1** zu erstellen.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-digital-twins-explorer.png" alt-text="Screenshot: Azure Digital Twins-Explorer mit dem Simulationsmodell und dem Zwilling „simulation-1“.":::

### <a name="verify-completion"></a>Überprüfen der abgeschlossenen Schritte

In diesem Abschnitt richten Sie eine Azure Digital Twins Instanz ein, die ein Modell und einen Zwilling enthält. Überprüfen Sie, ob Sie die folgende Prüfliste abgearbeitet haben:
> [!div class="checklist"]
> * Die Azure Digital Twins-Instanz wurde bereitgestellt.
> * Das Simulationsmodell wurde in die Azure Digital Twins-Instanz hochgeladen.
> * Der Zwilling „simulation-1“ wurde aus dem Simulationsmodell erstellt.

## <a name="set-up-azure-function"></a>Einrichten einer Azure-Funktion

Da Sie nun über die OPC UA-Knoten verfügen, die Daten an IoT Hub senden, und eine Azure Digital Twins-Instanz bereit ist, die Daten zu empfangen, müssen Sie die Daten dem richtigen Zwilling und den Eigenschaften in Azure Digital Twins zuordnen und sie speichern. In diesem Abschnitt richten Sie dies mithilfe einer Azure-Funktion und einer Datei *opcua-mapping.json* ein.

Der Datenfluss in diesem Abschnitt umfasst die folgenden Schritte:

1. Eine Azure-Funktion verwendet ein Ereignisabonnement, um Nachrichten von IoT Hub zu empfangen.
1. Die Azure-Funktion verarbeitet jedes eingehende Telemetrieereignis. Sie extrahiert die `NodeId` aus dem Ereignis und vergleicht sie mit den Elementen in der Datei *opcua-mapping.json*. Die Datei ordnet jede `NodeId` einer bestimmten `twinId` und einer Eigenschaft in Azure Digital Twins zu, in der der Wert des Knotens gespeichert werden soll.
1. Die Azure-Funktion generiert das entsprechende Patchdokument zum Aktualisieren des entsprechenden digitalen Zwillings und führt den Befehl zum Aktualisieren der Zwillingseigenschaft aus.

### <a name="create-opcua-mappingjson-file"></a>Erstellen der Datei „opcua-mapping.json“

Erstellen Sie zunächst ihre Datei *opcua-mapping.json*. Beginnen Sie mit einer leeren JSON-Datei, und geben Sie Einträge ein, die `NodeId`-Werte `twinId`-Werten und Eigenschaften in Azure Digital Twins zuordnen (wie im Beispiel und Schema unten gezeigt). Sie müssen einen Zuordnungseintrag für jede `NodeId` erstellen.

```JSON
[
    {
        "NodeId": "1001",
        "TwinId": "simulation",
        "Property": "Counter",
        "ModelId": "dtmi:com:microsoft:iot:opcua:simulation;1"
    },
    ...
]
```

Dies ist das Schema für die Einträge:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| NodeId | Wert aus dem OPC-UA-Knoten. Beispiel: ns=3;i={Wert} | ✔ |
| TwinId | TwinId ($dtId) des Zwillings, für den Sie den Telemetriewert speichern möchten. | ✔ |
| Eigenschaft | Name der Eigenschaft für den Zwilling zum Speichern des Telemetriewerts. | ✔ |
| ModelID  | Die modelId zum Erstellen des Zwillings, wenn die TwinId nicht vorhanden ist. |  |

> [!TIP]
> Ein vollständiges Beispiel für eine Datei *opcua-mapping.json* finden Sie im [GitHub-Repository „OPC UA to Azure Digital Twins](https://github.com/Azure-Samples/opcua-to-azure-digital-twins).

Wenn Sie mit dem Hinzufügen von Zuordnungen fertig sind, speichern Sie die Datei.

Nachdem Sie nun über Ihre Zuordnungsdatei verfügen, müssen Sie sie an einem Speicherort speichern, auf den die Azure-Funktion zugreifen kann. Ein solcher Speicherort ist [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md).

Befolgen Sie die Anweisungen unter [Erstellen eines Speichercontainers](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container), und importieren Sie die Datei *opcua-mapping.json* in den Container. Sie können Speicherverwaltungsereignisse auch mit [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) ausführen. 

Erstellen Sie als nun eine [Shared Access Signature (SAS) für den Container](../storage/common/storage-sas-overview.md), und speichern Sie diese URL. Später stellen Sie die URL für die Azure-Funktion bereit, damit sie auf die gespeicherte Datei zugreifen kann.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-storage-explorer.png" alt-text="Screenshot: Azure Storage-Explorers mit dem Dialogfeld zum Erstellen eines SAS-Tokens.":::

### <a name="publish-azure-function"></a>Veröffentlichen der Azure-Funktion

In diesem Abschnitt veröffentlichen Sie eine Azure-Funktion, die Sie unter [Voraussetzungen](#prerequisites) heruntergeladen haben, die die OPC UA-Daten verarbeitet und Azure Digital Twins aktualisiert.

1. Navigieren Sie zum heruntergeladenen Projekt [OPC UA to Azure Digital Twins](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) auf Ihrem lokalen Computer und dann zum Ordner *Azure Functions/OPCUAFunctions*. Öffnen Sie die Projektmappe **OPCUAFunctions.sln** in Visual Studio.
2. Veröffentlichen Sie das Projekt in einer Funktions-App in Azure. Anweisungen dazu finden Sie unter [Entwickeln von Azure Functions mit Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

#### <a name="configure-the-function-app"></a>Konfigurieren der Funktions-App

**Weisen Sie dann eine Zugriffsrolle** für die Funktion zu, und **Konfigurieren Sie die Anwendungseinstellungen**, damit sie auf Ihre Azure Digital Twins-Instanz zugreifen kann.

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

#### <a name="add-application-settings"></a>Hinzufügen von Anwendungseinstellungen

Sie müssen auch einige Anwendungseinstellungen hinzufügen, um Ihre Umgebung und die Azure-Funktion vollständig einzurichten. Navigieren Sie zum [Azure-Portal](https://portal.azure.com) und dann zu Ihrer neu erstellten Azure-Funktion, indem Sie in der Suchleiste des Portals nach ihrem Namen suchen.

Wählen Sie im linken Navigationsmenü der Funktion die Option „Konfiguration“ aus. Verwenden Sie die Schaltfläche **+ Neue Anwendungseinstellung**, um mit dem Erstellen neuer Einstellungen zu beginnen.

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-1.png" alt-text="Screenshot: Hinzufügen von Anwendungseinstellungen zu einer Azure-Funktion im Azure-Portal.":::

Es gibt drei Anwendungseinstellungen, die Sie erstellen müssen:

| Einstellung | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| ADT_SERVICE_URL | URL für Ihre Azure Digital Twins-Instanz. Beispiel: `https://example.api.eus.digitaltwins.azure.net` | ✔ |
| JSON_MAPPINGFILE_URL | URL der Shared Access Signature für die Datei „opcua-mapping.json“. | ✔ |
| LOG_LEVEL | Ausführlichkeit des Protokolliergrads. Der Standardwert ist 100. Ausführlich ist 300. | |

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-2.png" alt-text="Screenshot: Anwendungseinstellungen für eine Azure-Funktion im Azure-Portal. Die oben genannten Einstellungen wurden hinzugefügt.":::

> [!TIP]
> Legen Sie die `LOG_LEVEL`-Anwendungseinstellung für die Funktion auf 300 fest, um eine ausführlichere Protokollierung zu ermöglichen. 

### <a name="create-event-subscription"></a>Erstellen eines Ereignisabonnements

Erstellen Sie abschließend ein Ereignisabonnement, um Ihre Funktions-App und die *ProcessOPCPublisherEventsToADT*-Funktion mit Ihrer IoT Hub-Instanz zu verbinden. Das Ereignisabonnement ist erforderlich, damit Daten vom Gatewaygerät über die Funktion in IoT Hub übertragen werden können, die dann Azure Digital Twins aktualisiert.

Befolgen Sie die Anweisungen unter [Verbinden der IoT Hub-Instanz mit der Azure-Funktion](tutorial-end-to-end.md#connect-the-iot-hub-to-the-azure-function) aus dem Azure Digital Twins-*Tutorial: Verbinden einer End-to-End-Lösung*.

Das Ereignisabonnement weist den Endpunkttyp **Azure-Funktion** und den Endpunkt **ProcessOPCPublisherEventsToADT** auf.

:::image type="content" source="media/how-to-ingest-opcua-data/event-subscription.png" alt-text="Screenshot: Erstellung eines neuen Ereignisabonnementsim Azure-Portal.":::

Nach diesem Schritt sollten alle erforderlichen Komponenten installiert sein und ausgeführt werden. Daten sollten von Ihrem OPC UA-Simulationsserver über Azure IoT Hub in Ihre Azure Digital Twins-Instanz übertragen werden. 

### <a name="verify-completion"></a>Überprüfen der abgeschlossenen Schritte

In diesem Abschnitt richten Sie eine Azure-Funktion ein, um die OPC UA-Daten mit Azure Digital Twins zu verbinden. Überprüfen Sie, ob Sie die folgende Prüfliste abgearbeitet haben:
> [!div class="checklist"]
> * Erstellen und Importieren der Datei *opcua-mapping.json* in einen Blobspeichercontainer. 
> * Veröffentlichen der Beispielfunktion *ProcessOPCPublisherEventsToADT* in einer Funktions-App in Azure.
> * Hinzufügen von drei neuen Anwendungseinstellungen zur Azure Functions-App.
> * Erstellen eines Ereignisabonnements zum Senden von IoT Hub-Ereignissen an die Funktions-App.

Der nächste Abschnitt enthält einige Azure CLI-Befehle, die Sie ausführen können, um die Ereignisse zu überwachen und zu überprüfen, ob alles erfolgreich funktioniert.

## <a name="verify-and-monitor"></a>Überprüfen und überwachen

Die Befehle in diesem Abschnitt können in [Azure Cloud Shell](https://shell.azure.com)oder in einem [lokalen Azure CLI-Fenster](/cli/azure/install-azure-cli) ausgeführt werden.

Führen Sie diesen Befehl aus, um IoT Hub-Ereignisse zu überwachen:
```azurecli-interactive
az iot hub monitor-events -n <IoT-hub-name> -t 0
```

Führen Sie den folgenden Befehl aus, um die Ereignisverarbeitung der Azure-Funktion zu überwachen:
```azurecli-interactive
az webapp log tail –name <function-name> --resource-group <resource-group-name>
```

Schließlich können Sie den Azure Digital Twins-Explorer verwenden, um Aktualisierungen von Zwillingseigenschaften manuell zu überwachen. 

:::image type="content" source="media/how-to-ingest-opcua-data/adt-explorer-2.png" alt-text="Screenshot: Verwenden des Azure Digital Twins-Explorers, um Aktualisierungen von Zwillingseigenschaften zu überwachen.":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen vollständigen Datenfluss eingerichtet, um simulierte OPC UA-Serverdaten in Azure Digital Twins zu übertragen, wo eine Eigenschaft für einen digitalen Zwilling aktualisiert wird.

Verwenden Sie nun die folgenden Ressourcen, um mehr über die unterstützenden Tools und Prozesse zu erfahren, die in diesem Artikel verwendet wurden:

* [Schritt-für-Schritt-Anleitung zum Installieren von OPC Publisher auf Azure IoT Edge](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [Installieren von IoT Edge unter Linux](../iot-edge/how-to-install-iot-edge.md) 
* [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher)
* [Konfigurieren von OPC Publisher](/previous-versions/azure/iot-accelerators/howto-opc-publisher-configure)
* [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) 
* [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL)