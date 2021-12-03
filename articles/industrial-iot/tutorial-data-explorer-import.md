---
title: Pullen von Daten in Azure Data Explorer mithilfe von Industrial IoT
description: In diesem Tutorial erfahren Sie, wie Sie IIoT-Daten in ADX verschieben und ein Dashboard erstellen, um sie anzuzeigen.
author: eross-msft
ms.author: lizross
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 6/16/2021
ms.openlocfilehash: 4ddd2e14d3be90c211d0e278a11e45bd51154fa1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550019"
---
# <a name="tutorial-using-industrial-iot-to-pull-data-into-azure-data-explorer"></a>Tutorial: Pullen von Daten in Azure Data Explorer mithilfe von Industrial IoT

Die IIoT-Plattform (Azure Industrial IoT) kombiniert Edge-Module und cloudbasierte Microservices mit Azure Platform-as-a-Service (PaaS), um Funktionen für die Ermittlung von Industrial-Assets und die Erfassung von Assetdaten zu ermöglichen. In einer Industrieumgebung ist der de-facto-Standard für diese Aufgabe OPC UA. Dieses Tutorial veranschaulicht anhand eines einfachen Fallbeispiels, nämlich cloudbasierter Zustandsüberwachung, wie Sie Azure für die Verwendung mit OPC UA konfigurieren.

In diesem Tutorial lernen Sie Folgendes:
- Erhalten eines Azure-Kontos und Bereitstellen eines IoT Hub
- Bereitstellen von IoT Edge
- Installieren und Konfigurieren von OPC Publisher
- Einrichten einer Zeitreihendatenbank
- Erstellen eines Dashboards
- Erstellen eines Graphen eingehender Daten

## <a name="requirements"></a>Anforderungen

Bevor Sie mit diesem Tutorial beginnen, benötigen Sie ein Azure-Konto. Falls Sie noch keines haben, [klicken Sie hier](https://azure.microsoft.com/free/), um kostenlos eines zu erhalten.

## <a name="deploy-an-s1-iot-hub"></a>Bereitstellen eines S1 IoT Hub

Der erste Schritt, den Sie mit Ihrem Azure-Abonnement machen müssen, besteht darin, zum [Portal](../azure-portal/azure-portal-quickstart-center.md) zu navigieren und [einen S1 IoT Hub zu erstellen](../iot-hub/iot-hub-create-through-portal.md). Ihr kostenloses Azure-Konto bietet Ihnen 400.000 4K-Nachrichten pro Tag. Da Sie etwa 15 OPC UA PubSub-Datentags in einer einzelnen 4K-Nachricht unterbringen können, verfügen Sie über 6 Millionen Datentags pro Tag, was für diesen Anwendungsfall ausreicht. Nachdem der IoT Hub bereitgestellt wurde, kopieren Sie die primäre Verbindungszeichenfolge *iothubowner* (unter „Freigegebene Zugriffsrichtlinien“, „iothubowner“). Sie benötigen sie für die Installation von IoT Edge.

## <a name="deploy-iot-edge"></a>Bereitstellen von IoT Edge

Stellen Sie IoT Edge mithilfe des [IoT Edge Installers](https://lnkd.in/ga6Ew4X) auf einem PC oder einer VM mit Windows 10 bereit. Dieser PC bzw. diese VM muss über eine Internetverbindung verfügen und zudem in der Lage sein, auf die Computer bzw. die zu verwendende Software für Industriekonnektivitätsadapter (dataFEED, Zenon, Kepware usw.) zuzugreifen. Sie können IoT Edge-Geräte schachteln („Daisy-Chain“), falls Ihre Einrichtung mehrere Netzwerkebenen von Ihrem OT-Netzwerk (wo sich Ihre Computer befinden) zu Ihrer Internetverbindung verwendet. Sie können IoT Edge auch nativ unter Linux ausführen, indem Sie das Installationsprogramm [auf GitHub](https://github.com/Azure/Industrial-IoT-Gateway-Installer/blob/master/Releases/Linux.zip) verwenden.

Aktivieren Sie beim Ausführen des IoT Edge-Installationsprogramms das Kontrollkästchen **Use Docker Desktop**, lassen Sie das Optionsfeld **Configure IoT Edge for Azure Industrial Cloud Platform (PaaS)** aktiviert, und fügen Sie die Verbindungszeichenfolge *iothubowner* in das bereitgestellte Textfeld ein. Das Installationsprogramm füllt automatisch den Namen des PCs, auf dem die Ausführung erfolgt, als Edge-Instanznamen auf. Sie können ihn jedoch bei Bedarf ändern. Klicken Sie auf **Installieren**. Wenn Docker Desktop nicht erkannt wird, wird es vom Installationsprogramm automatisch heruntergeladen. Installieren Sie es, und stellen Sie sicher, dass Sie es mit Windows-Subsystem für Linux V2 (WSL2) konfigurieren, um eine optimale Leistung zu erzielen. Sobald die Installation von Docker Desktop abgeschlossen ist, starten Sie das IoT Edge-Installationsprogramm neu, konfigurieren Docker Desktop erneut und klicken ein zweites Mal auf „Installieren“. Wechseln Sie dann im Azure-Portal auf der **IoT Hub**-Seite zur Registerkarte **IoT Edge**, und warten Sie, bis das neu installierte IoT Edge-Gerät angezeigt wird (möglicherweise müssen Sie die Seite einige Male aktualisieren).

## <a name="install-and-configure-opc-publisher"></a>Installieren und Konfigurieren von OPC Publisher

Als Nächstes installieren und konfigurieren Sie OPC Publisher, das zum Standardisieren und Normalisieren aller Telemetriedaten mithilfe von OPC UA PubSub am Edge verwendet wird. Wir verwenden die OPC UA JSON-Codierung, da sie direkt in Clouddatenbanken erfasst werden kann, ohne dass das Binärformat zunächst mit einem separaten Clouddienst wieder in etwas übersetzt werden muss, das für die Clouddatenbank verständlich ist. Die Verwendung dieses branchenüblichen Standardformats reduziert den Verwaltungsaufwand und die Kosten in der Cloud. Dies ist ein wichtiger Punkt: Viele Leute werden behaupten, sie hätten eine effizientere Telemetriecodierung gefunden, unterschätzen aber grundsätzlich den Kosten- und Verwaltungsaspekt der bedarfsgerechten Übersetzung von Telemetriedaten in ein clouddatenbankfähiges Format in der Cloud. 

Wechseln Sie nun zur Seite **Erste Schritte** für OPC Publisher, und befolgen Sie die Anweisungen. Geben Sie unter **Optionen für Containererstellung** Folgendes an.

```
{
    "Hostname": "OPCPublisher",
    "Cmd": [
        "PkiRootPath=/appdata/pki", 
        "--lf=/appdata/publisher.log", 
        "--pf=/appdata/publishednodes.json",
        "--aa",
        "--di=60",
        "--me=Json",
        "--mm=PubSub"
    ],
    "HostConfig": {
        "Binds": [
            "/c/IoTEdgeMapping:/appdata"
        ]
    }
}
```

Konfigurieren Sie als Nächstes die OPC UA-Datenknoten über die Computer (oder die Konnektivitätsadaptersoftware), auf denen die Cloudzeitreihen-Telemetrienachrichten generiert werden. Kopieren Sie hierzu die Vorlagendatei ***publishednodes.jsin** _ von hier nach _*_C:\IoTEdgeMapping_*_, und füllen Sie sie mit der _ *EndpointURL* des OPC UA-Servers und den OPC UA-Knoten-IDs der Daten aus, die Sie an die Cloud senden möchten. Wenn Sie nicht wissen, was gesendet werden soll, ist der Datenknoten mit der ID ns=0;i=2258 die aktuelle Zeit des Servers. Sie ändert sich jede Sekunde. OPC UA veröffentlicht Datenänderungen nur dann, wenn sich die Daten geändert haben. OPC Publisher unterstützt jedoch auch das Lesen und Senden von Daten in bestimmten Intervallen, auch wenn sich die Daten nicht geändert haben.

Nachdem Sie die Datei bearbeitet haben, starten Sie OPC Publisher mit dem Befehlszeilenbefehl „**iotedge restart OPCPublisher**“ neu. OPC Publisher versucht dann, eine Verbindung mit allen angegebenen OPC UA-Servern herzustellen und alle aufgelisteten Knoten zu veröffentlichen. Die Erfahrung hat gezeigt, dass Sie zuerst einen OPC UA-Client wie UA Expert auf dem PC verwenden sollten, auf dem IoT Edge ausgeführt wird, um sicherzustellen, dass Sie eine Verbindung mit den OPC UA-Servern herstellen können. Außerdem sollten Sie die IP-Adresse des OPC UA-Servers anstelle des Hostnamens in der EndpointURL ausprobieren, falls Ihr DNS nicht funktioniert. Wechseln Sie nun zurück zu Ihrer **IoT Hub** Seite im Azure-Portal und überprüfen Sie noch einmal, ob empfangene **Gerät-zu-Cloud**-Nachrichten auf der Registerkarte **Übersicht** angezeigt werden.

## <a name="set-up-the-time-series-database"></a>Einrichten einer Zeitreihendatenbank

Richten Sie nun die Zeitreihendatenbank in der Cloud ein, von der die OPC UA-Telemetrie empfangen werden soll. Es ist am besten, dies mithilfe von Azure Data Explorer zu tun, da es mehrere Datenbankebenen verwendet, um eine optimale Leistung zu erzielen. Zudem bietet es umfassende Analyse- und Dashboardfunktionen und unterstützt sogar Vorhersagen und Anomalieerkennung. Erstellen Sie eine Instanz über das Azure-Portal, wählen Sie das Dev/Test-Workload-SKU aus, und vergessen Sie nicht, die Streaming-Erfassung unter „Konfigurationen“ zu aktivieren.

OPC UA identifiziert ein Telemetriedatentag eindeutig über die **DatasetWriterID** des OPC UA-Servers und die **ExpandedNodeID** (die sowohl den Namespace als auch die Knoten-ID enthält). Darüber hinaus wird der Zeitstempel, der generiert wird, wenn die Daten vom OPC UA-Server gelesen werden, bevor sie einem verbundenen Client zur Verfügung gestellt werden, als *SourceTimeStamp* bezeichnet. Um also eine Zeitreihe von OPC UA-Telemetriedaten zu erstellen, müssen die **DatasetWriterID,** die **ExpandedNodeID,** der gelesene Datenwert und der **SourceTimeStamp** in einer Datenbanktabelle bereitgestellt werden. Diese erstellen Sie jetzt mithilfe von Azure Data Explorer. Beachten Sie, dass der Datenwerttyp (float, int usw.) auch in OPC UA PubSub-Nachrichten enthalten sein kann. Dies wird in der OPC UA-Spezifikation als *umkehrbare OPC UA* bezeichnet.

Erstellen Sie zunächst eine Datenbank mit der standardmäßigen Datenaufbewahrungsdauer von 10 Jahren, die für diesen Anwendungsfall ausreichen sollte. Konfigurieren Sie dann die Streaming-Erfassung direkt über Ihren IoT Hub, indem Sie auf **Datenverbindung erstellen** klicken und **IoT Hub** auswählen. Geben Sie der Verbindung einen Namen, und wählen Sie ihren IoT Hub in der Dropdownliste aus. Wählen Sie unter **SAS-Richtlinie** die Option *iothubowner* und unter *Consumergruppe* die Option **$Default** aus. Lassen Sie **Tabellenname** vorerst leer, wählen Sie unter *Datenformat* die Option **MULTILINE JSON** aus, und geben Sie *opcua_mapping* als **Zuordnungsname** ein. Klicken Sie auf **Erstellen**.

## <a name="create-the-database-tables"></a>Erstellen der Datenbanktabellen

Erstellen Sie die erforderlichen Tabellen in der neuen Datenbank. Erstellen Sie zunächst eine Stagingtabelle mit einer einzelnen Spalte, um die unformatierten OPC UA PubSub-JSON-Telemetrienachrichten zu empfangen. Azure Data Explorer verwendet die integrierte Kusto-Abfragesprache (Kusto Query Language, KQL), die hier dokumentiert ist. Wählen Sie die Registerkarte **Abfrage** aus, und geben Sie Folgendes ein.

```
.create table opcua_raw(payload: dynamic)

```

Erstellen Sie mit dem folgenden Befehl unter Verwendung des zuvor eingerichteten IoT Hub eine Zuordnung für die *opcua_mapping*-Telemetriedatenerfassung.

```
.create table opcua_raw ingestion json mapping "opcua_mapping" @'[{"column":"payload","path":"$","datatype":"dynamic"}]'

```

Aktualisieren Sie die Telemetriedatenerfassung mit dem Tabellennamen der Tabelle, die Sie gerade erstellt haben. Klicken Sie dazu auf die Registerkarte **Datenerfassung**, wählen Sie den Namen Ihrer Datenerfassung aus, klicken Sie auf **Bearbeiten** auswählen, geben Sie *opcua_raw* als **Tabellenname** ein, und klicken Sie auf **Aktualisieren**. Wechseln Sie dann zurück zur Registerkarte **Abfrage**, und warten Sie einige Minuten, bis die ersten Telemetrienachrichten in die Tabelle gelangen. Sie können dies überprüfen, indem Sie 10 Zeilen in der Tabelle abfragen.

```
opcua_raw
| take 10
```

OPC UA PubSub unterstützt die Batchverarbeitung von Telemetriedaten in einer einzelnen Nachricht durch die Verwendung von Datasets, die durch die **dataSetWriterID** identifiziert werden. Diese Datasets müssen in separaten Zeilen in Ihrer Datenbank, also nicht im Batch, verarbeitet werden. Hierzu verwenden Sie den Operator *mv-expand* und erweitern den JSON-Code, der in einer neuen Zwischentabelle erfasst wird, die Sie zuerst erstellen müssen.

```
.create table opcua_intermediate(DataSetWriterID: string, payload: dynamic)
```

Erstellen Sie nun die folgende Funktion, um die Erweiterung vorzunehmen.

```
.create-or-alter function OPCUARawExpand() {
    opcua_raw
    |mv-expand records = payload.Messages
    |project 
        DataSetWriterID = tostring(records["DataSetWriterId"]),
        Payload = todynamic(records["Payload"])
}
```

Aktualisieren Sie dann die Zwischentabelle, indem Sie die neue Funktion anwenden.

```
.alter table opcua_intermediate policy update @'[{"Source": "opcua_raw", "Query": "OPCUARawExpand()", "IsEnabled": "True"}]'
```

Überprüfen Sie, ob die neue Tabelle ordnungsgemäß aufgefüllt wird.

```
opcua_intermediate
| take 10
```

 Erstellen Sie die letzte OPC UA-Telemetrietabelle, und füllen Sie sie auf, indem Sie jeden Eintrag im OPC UA erweitern.

```
.create table opcua_telemetry (DataSetWriterID: string, ExpandedNodeID: string, Value: dynamic, SourceTimestamp: datetime)
```

Erstellen Sie die unten gezeigte Funktion.

```
.create-or-alter function OPCUADatasetExpand() {
    opcua_intermediate
    | mv-apply payload on (
        extend key = tostring(bag_keys(payload)[0])
        | extend p = payload[key]
        | project ExpandedNodeId = key, Value = todynamic(p.Value), SourceTimestamp = todatetime(p.SourceTimestamp)
    )
}
```

Aktualisieren Sie die dritte und letzte Tabelle, indem Sie die neue Funktion anwenden.

```
.alter table opcua_telemetry policy update @'[{"Source": "opcua_intermediate", "Query": "OPCUADatasetExpand()", "IsEnabled": "True"}]'
```

Verwenden Sie den folgenden Befehl, um die OPC UA-Telemetrietabelle anzuzeigen.

```
opcua_telemetry
| take 10
```

## <a name="create-line-graph-of-the-data"></a>Erstellen eines Liniendiagramms der Daten

Erstellen Sie nun ein Liniendiagramm der Daten, indem Sie alles in Gleitkommazahlen umwandeln. Wenn die Umwandlung fehlschlägt, werden die Daten ignoriert. Verwenden Sie hierzu das gehostete Azure Data Explorer-Dashboard. Klicken Sie auf **In Webbenutzeroberfläche öffnen** und dann auf **Dashboards**. Wählen Sie **Neues Dashboard erstellen** aus, und klicken Sie dann auf **Kachel hinzufügen** aus. Wählen Sie dann **Datenquelle** aus, und geben Sie den Namen der Azure Data Explorer-Instanz in das Formular `https://<YourInstanceName>.<Your RegionName>.kusto.windows.net` ein. Wählen Sie Ihre Datenbank aus, und klicken Sie auf **Anwenden**. Geben Sie dann die folgende Abfrage ein.

```
opcua_telemetry
| project SourceTimestamp, yaxis = todouble(Value), DataSetWriterID,  ExpandedNodeID
```

Klicken Sie auf die Registerkarte **Visuelles Element**, und wählen Sie *yaxis* für die **Y-Spalten**, *SourceTimeStamp* für die **X-Spalten** und *DataSetWriterID* für die **Reihenspalten** aus.

Nun verfügen Sie über ein gutes Telemetriedashboard für Ihre Daten zum Anwendungsfall Zustandsüberwachung in der Cloud.