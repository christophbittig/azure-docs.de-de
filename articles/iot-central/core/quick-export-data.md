---
title: 'Schnellstart: Exportieren von Daten aus Azure IoT Central'
description: In diesem Schnellstart erfahren Sie, wie Sie das Datenexportfeature von IoT Central für die Integration mit anderen Clouddiensten verwenden.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 0728eed5a1deeee20554998512edd70f5e983491
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492309"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>Schnellstart: Exportieren von Daten aus einer IoT Central-Anwendung

In diesem Schnellstart erfahren Sie, wie Sie Daten mit einem fortlaufenden Export aus Ihrer Azure IoT Central-Anwendung in einen anderen Clouddienst exportieren. Um eine schnelle Einrichtung zu ermöglichen, wird in dieser Schnellstartanleitung [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) verwendet, ein vollständig verwalteter Datenanalysedienst für Echtzeitanalysen. Mit Azure Data Explorer können Sie die Telemetriedaten von Geräten (etwa aus der **IoT Plug & Play**-Smartphone-App) speichern, abfragen und verarbeiten.

In dieser Schnellstartanleitung führen Sie die folgenden Schritte aus:

- Sie verwenden das Datenexportfeature von IoT Central, um die von der Smartphone-App gesendeten Telemetriedaten in eine Azure Data Explorer-Datenbank zu exportieren.
- Sie verwenden Azure Data Explorer zum Ausführen von Abfragen für die Telemetriedaten.

## <a name="prerequisites"></a>Voraussetzungen

- Bevor Sie beginnen, sollten Sie den ersten Schnellstart zum [Erstellen einer Azure IoT Central-Anwendung](./quick-deploy-iot-central.md) abgeschlossen haben. Der zweite Schnellstart zum [Konfigurieren von Regeln und Aktionen für Ihr Gerät](quick-configure-rules.md) ist optional.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="install-azure-services"></a>Installieren von Azure-Diensten

Damit Sie Daten aus Ihrer IoT Central-Anwendung exportieren können, benötigen Sie einen Azure Data Explorer-Cluster und eine Datenbank. In dieser Schnellstartanleitung verwenden Sie die Bash-Umgebung in [Azure Cloud Shell](https://shell.azure.com), um diese Komponenten zu erstellen und konfigurieren.

Führen Sie in Azure Cloud Shell das folgende Skript aus. Ersetzen Sie den Wert `clustername` durch einen eindeutigen Namen für Ihren Cluster, bevor Sie das Skript ausführen:

> [!IMPORTANT]
> Die Ausführung des Skripts dauert mindestens zehn Minuten.

```azurecli
clustername="<A unique name for your cluster>"
databasename="phonedata"
location="eastus"
resourcegroup="IoTCentralExportData"

az extension add -n kusto

# Create a resource group for the Azure Data Explorer cluster
az group create --location $location \
    --name $resourcegroup

# Create the Azure Data Explorer cluster
# This command takes at least 10 minutes to run
az kusto cluster create --cluster-name $clustername \
    --sku name="Standard_D11_v2"  tier="Standard" \
    --enable-streaming-ingest=true \
    --enable-auto-stop=true \
    --resource-group $resourcegroup --location $location

# Crete a database in the cluster
az kusto database create --cluster-name $clustername \
    --database-name $databasename \
    --read-write-database location=$location soft-delete-period=P365D hot-cache-period=P31D \
    --resource-group $resourcegroup

# Create a service principal to use when authenticating from IoT Central
SP_JSON=$(az ad sp create-for-rbac --skip-assignment --name spforiotcentral)

az kusto database-principal-assignment create --cluster-name $clustername \
                                              --database-name $databasename \
                                              --principal-id $(jq -r .appId <<< $SP_JSON) \
                                              --principal-assignment-name spforiotcentral \
                                              --resource-group $resourcegroup \
                                              --principal-type App \
                                              --role Admin

echo "Azure Data Explorer URL: $(az kusto cluster show --name $clustername --resource-group $resourcegroup --query uri -o tsv)"
echo "Client ID: $(jq -r .appId <<< $SP_JSON)"
echo "Tenant ID: $(jq -r .tenant <<< $SP_JSON)"
echo "Client secret: $(jq -r .password <<< $SP_JSON)" 
```

Notieren Sie sich die Werte für **Azure Data Explorer-URL**, **Client-ID**, **Mandanten-ID** und **Geheimer Clientschlüssel**. Diese Werte werden später in der Schnellstartanleitung benötigt.

## <a name="configure-the-database"></a>Konfigurieren der Datenbank

Fügen Sie wie folgt eine Tabelle in der Datenbank hinzu, um die Daten des Beschleunigungsmessers aus der **IoT Plug & Play**-Smartphone-App zu speichern:

1. Verwenden Sie die **Azure Data Explorer-URL** aus dem vorherigen Abschnitt, um zu Ihrer Azure Data Explorer-Umgebung zu navigieren.

1. Erweitern Sie den Clusterknoten, und wählen Sie die Datenbank **phonedata** aus. Der Bereich des Abfragefensters ändert sich in `Scope:yourclustername.eastus/phonedata`.

1. Fügen Sie das folgende Kusto-Skript in den Abfrage-Editor ein, und wählen Sie **Ausführen** aus:

    ```kusto
    .create table acceleration (
      EnqueuedTime:datetime,
      Device:string,
      X:real,
      Y:real,
      Z:real
    );
    ```

    Das Ergebnis sieht in etwa wie auf dem folgenden Screenshot aus:

    :::image type="content" source="media/quick-export-data/azure-data-explorer-create-table.png" alt-text="Screenshot: Ergebnisse der Tabellenerstellung in Azure Data Explorer":::

1. Öffnen Sie in Azure Data Explorer eine neue Registerkarte, und fügen Sie das folgende Kusto-Skript ein. Mit dem Skript wir das eingehende Streaming für die Tabelle **acceleration** aktiviert:

    ```kusto
    .alter table acceleration policy streamingingestion enable;
    ```

Lassen Sie die Azure Data Explorer-Seite in Ihrem Browser geöffnet. Nach dem Konfigurieren des Datenexports in Ihrer IoT Central-Anwendung führen Sie eine Abfrage aus, um die in der Tabelle **acceleration** gespeicherten Telemetriedaten des Beschleunigungsmessers anzuzeigen.

## <a name="configure-data-export"></a>Konfigurieren des Datenexports

So konfigurieren Sie das Datenexportziel in IoT Central

1. Navigieren Sie in Ihrer IoT Central-Anwendung zur Seite **Datenexport**.
1. Wählen Sie die Registerkarte **Ziele** und dann **Ziel hinzufügen** aus.
1. Geben Sie als Zielnamen *Azure Data Explorer* ein. Wählen Sie als Zieltyp **Azure Data Explorer** aus.
1. Geben Sie unter **Cluster-URL** die zuvor notierte **Azure Data Explorer-URL** ein.
1. Geben Sie unter **Datenbankname** den Namen *phonedata* ein.
1. Geben Sie unter **Tabellenname** den Namen *acceleration* ein.
1. Geben Sie unter **Client-ID** die zuvor notierte **Client-ID** ein.
1. Geben Sie unter **Mandanten-ID** die zuvor notierte **Mandanten-ID** ein.
1. Geben Sie unter **Geheimer Clientschlüssel** den zuvor notierten **geheimen Clientschlüssel** ein.
1. Wählen Sie **Speichern** aus.

So konfigurieren Sie den Datenexport

1. Wählen Sie auf der Seite **Datenexport** die Registerkarte **Exporte** und dann **Export hinzufügen** aus.
1. Geben Sie als Exportnamen *Phone accelerometer* (Telefonbeschleunigungsmesser) ein.
1. Wählen Sie als Typ der zu exportierenden Daten **Telemetrie** aus.
1. Fügen Sie unter Verwendung der Informationen in der folgenden Tabelle zwei Filter hinzu:

    | Name | Operator | Wert |
    | ---- | -------- | ----- |
    | Gerätevorlage | Equals | IoT Plug and Play mobile |
    | Sensoren/Beschleunigung/X | Exists | – |

    Stellen Sie sicher, dass die Option festgelegt ist, die dafür sorgt, dass die Daten exportiert werden, wenn alle Bedingungen erfüllt sind.

1. Fügen Sie **Azure Data Explorer** als Ziel hinzu.
1. Fügen Sie dem Ziel eine Datentransformation hinzu. Fügen Sie die folgende Abfrage auf der Seite **Datentransformation** in das Feld **2. Build transformation query** (2. Transformationsabfrage erstellen) ein:

    ```json
    import "iotc" as iotc;
    {
        Device: .device.id,
        EnqueuedTime: .enqueuedTime,
        X: .telemetry | iotc::find(.name == "accelerometer").value.x,
        Y: .telemetry | iotc::find(.name == "accelerometer").value.y,
        Z: .telemetry | iotc::find(.name == "accelerometer").value.z
    }
    ```

    :::image type="content" source="media/quick-export-data/data-transformation-query.png" alt-text="Screenshot: Datentransformationsabfrage für den Export":::

    Wenn Sie die Funktionsweise der Transformation sehen und mit der Abfrage experimentieren möchten, fügen Sie die folgende Beispieltelemetrienachricht in **1. Add your input message** (1. Ihre Eingabenachricht hinzufügen) ein:

    ```json
    {
      "messageProperties": {},
      "device": {
        "id": "8hltz8xa7n",
        "properties": {
          "reported": []
        },
        "approved": true,
        "types": [],
        "name": "8hltz8xa7n",
        "simulated": false,
        "provisioned": true,
        "modules": [],
        "templateId": "urn:modelDefinition:vlcd3zvzdm:y425jkkpqzeu",
        "templateName": "IoT Plug and Play mobile",
        "organizations": [],
        "cloudProperties": [],
        "blocked": false
      },
      "component": "sensors",
      "applicationId": "40a97c91-50cc-44f0-9f63-71386613facc",
      "messageSource": "telemetry",
      "telemetry": [
        {
          "id": "dtmi:azureiot:PhoneSensors:__accelerometer;1",
          "name": "accelerometer",
          "value": {
            "x": 0.09960123896598816,
            "y": 0.09541380405426025,
            "z": 9.907781600952148
          }
        }
      ],
      "enqueuedTime": "2021-11-12T10:01:30.588Z",
      "enrichments": {}
    }
    ```

1. Speichern Sie die Transformation. Speichern Sie dann die Datenexportdefinition.

Warten Sie, bis der Exportstatus **Fehlerfrei** angezeigt wird:

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="Screenshot eines ausgeführten Datenexports mit dem Status „Fehlerfrei“":::

## <a name="query-exported-data"></a>Abfragen exportierter Daten

Öffnen Sie in Azure Data Explorer eine neue Registerkarte, fügen Sie die folgende Kusto-Abfrage ein, und wählen Sie dann **Ausführen** aus, um die Telemetriedaten des Beschleunigungsmessers zu zeichnen:

```kusto
['acceleration'] 
    | project EnqueuedTime, Device, X, Y, Z
    | render timechart 
```

Unter Umständen müssen Sie einige Minuten warten, um genügend Daten zu sammeln. Ändern Sie die Ausrichtung Ihres Telefons, um zu sehen, wie sich die Telemetriewerte verändern:

:::image type="content" source="media/quick-export-data/acceleration-plot.png" alt-text="Screenshot: Abfrageergebnisse für die Telemetriedaten des Beschleunigungsmessers":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

Um die Azure Data Explorer-Instanz zur Vermeidung unnötiger Kosten aus Ihrem Abonnement zu entfernen, löschen Sie die Ressourcengruppe **IoTCentralExportData** im [Azure-Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups), oder führen Sie den folgenden Befehl in Azure Cloud Shell aus:

```azurecli
az group delete --name IoTCentralExportData
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie erfahren, wie Sie Daten fortlaufend aus IoT Central in einen anderen Azure-Dienst exportieren.

Nachdem Sie nun wissen, wie Sie Ihre Daten exportieren, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Einrichten und Verwalten einer Gerätevorlage (Previewfunktionen)](howto-set-up-template.md)
