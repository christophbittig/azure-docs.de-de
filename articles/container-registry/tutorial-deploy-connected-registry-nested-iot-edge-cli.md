---
title: Tutorial - Bereitstellen einer verbundenen Registrierung für ein verschachteltes IoT Edge-Gerät
description: Verwenden Sie Azure CLI-Befehle, um eine verbundene Azure-Container-Registry in einer verschachtelten Azure IoT Edge-Hierarchie bereitzustellen.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3652a87de4e517f2f40821a5e6e7367c1c88b078
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101706"
---
# <a name="tutorial-deploy-a-connected-registry-to-a-nested-iot-edge-hierarchy"></a>Verwenden Sie Azure CLI-Befehle, um eine verbundene Azure-Container-Registry in einer verschachtelten Azure IoT Edge-Hierarchie bereitzustellen.

In diesem Tutorial verwenden Sie Azure CLI-Befehle, um eine zweischichtige Hierarchie von Azure IoT Edge-Geräten zu erstellen und eine [verbundene Registrierung](intro-connected-registry.md) als ein Modul auf jeder Schicht bereitzustellen.

Einen Überblick über die Verwendung einer verbundenen Registrierung mit IoT Edge finden Sie unter [Verwendung einer verbundenen Registrierung mit Azure IoT Edge](overview-connected-registry-and-iot-edge.md). In diesem Szenario kommuniziert ein Gerät in der [oberen Schicht](overview-connected-registry-and-iot-edge.md#top-layer) mit einem Cloud-Register, und ein Gerät in der [unteren Schicht](overview-connected-registry-and-iot-edge.md#top-layer) kommuniziert mit seinem angeschlossenen übergeordneten Register in der oberen Schicht. 

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub. Schritte zur Bereitstellung finden Sie unter [Erstellen eines IoT-Hubs über das Azure-Portal](../iot-hub/iot-hub-create-through-portal.md).
* Zwei verbundene Registry-Ressourcen in Azure. Informationen zu den Bereitstellungsschritten finden Sie unter Schnellstarts mit der [Azure CLI][quickstart-connected-registry-cli] oder dem [Azure Portal][quickstart-connected-registry-portal]. 

    * Für die oberste Ebene kann die angeschlossene Registrierung entweder im ReadWrite- oder im ReadOnly-Modus sein. Dieser Artikel geht vom ReadWrite-Modus aus, und der Name der verbundenen Registrierung wird in der Umgebungsvariablen *$CONNECTED_REGISTRY_RW* gespeichert.
    * Für die untere Schicht muss die angeschlossene Registrierung im ReadOnly-Modus sein. Dieser Artikel geht davon aus, dass der Name der verbundenen Registrierung in der Umgebungsvariablen *$CONNECTED_REGISTRY_RO* gespeichert ist.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>Abrufen der verbundenen Registrierungskonfiguration 

Um jede verbundene Registrierung auf dem IoT Edge-Gerät in der Hierarchie bereitzustellen, müssen Sie die Konfigurationseinstellungen von der verbundenen Registrierungsressource in Azure abrufen. Führen Sie bei Bedarf den Befehl [az acr connected-registry get-settings][az-acr-connected-registry-get-settings] für jede verbundene Registrierung aus, um die Konfiguration abzurufen. 

Standardmäßig enthalten die Einstellungsinformationen nicht das [Sync-Token](overview-connected-registry-access.md#sync-token)-Kennwort, das ebenfalls für die Bereitstellung der verbundenen Registrierung benötigt wird. Optional können Sie eines der Passwörter generieren, indem Sie den Parameter `--generate-password 1` oder `generate-password 2` übergeben. Speichern Sie das generierte Passwort an einem sicheren Ort. Sie kann nicht wieder abgerufen werden.

> [!WARNING]
> Durch die Neuerstellung eines Kennworts werden die Sync-Token-Anmeldeinformationen ausgetauscht. Wenn Sie ein Gerät mit dem vorherigen Passwort konfiguriert haben, müssen Sie die Konfiguration aktualisieren.

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

# Run the command for each registry resource in the hierarchy

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --parent-protocol https
```

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-deployment-manifests-for-the-nested-iot-edge-devices"></a>Konfigurieren Sie Bereitstellungsmanifeste für die verschachtelten IoT Edge-Geräte

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module auf einem IoT Edge-Gerät bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](../iot-edge/module-composition.md).

Um das verbundene Registrierungsmodul auf jedem IoT Edge-Gerät mithilfe der Azure CLI bereitzustellen, speichern Sie die folgenden Bereitstellungsmanifeste lokal als JSON-Dateien. Verwenden Sie die Informationen aus den vorherigen Abschnitten, um die relevanten JSON-Werte in jedem Manifest zu aktualisieren. Sie werden die Dateipfade im nächsten Abschnitt verwenden, wenn Sie den Befehl ausführen, um die Konfiguration auf Ihr Gerät anzuwenden.

### <a name="deployment-manifest-for-the-top-layer"></a>Bereitstellungsmanifest für die oberste Schicht

Erstellen Sie für das Gerät auf der obersten Ebene eine Bereitstellungsmanifestdatei `deploymentTopLayer.json` mit dem folgenden Inhalt. Dieses Manifest ähnelt dem, das in [Quickstart verwendet wird: Bereitstellen einer verbundenen Registrierung auf einem IoT Edge-Gerät](quickstart-deploy-connected-registry-iot-edge-cli.md). 

> [!NOTE]
> Wenn Sie bereits eine angeschlossene Registrierung auf einem IoT-Edge-Gerät der obersten Ebene mithilfe von [Schnellstart](quickstart-deploy-connected-registry-iot-edge-cli.md) bereitgestellt haben, können Sie sie auf der obersten Ebene einer verschachtelten Hierarchie verwenden. Sie müssen die Verteilungsschritte in diesem Lernprogramm ändern, um sie in der Hierarchie zu konfigurieren (nicht gezeigt).

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

### <a name="deployment-manifest-for-the-lower-layer"></a>Bereitstellungsmanifest für die untere Schicht

Erstellen Sie für das Gerät auf der unteren Ebene eine Bereitstellungsmanifestdatei `deploymentLowerLayer.json` mit dem folgenden Inhalt.

Insgesamt ähnelt die Bereitstellungsdatei der unteren Schicht der Bereitstellungsdatei der oberen Schicht. Folgende Unterschiede bestehen: 

- Es zieht die erforderlichen Bilder aus dem mit der obersten Schicht verbundenen Register und nicht aus dem Cloud-Register. 
    
    Wenn Sie die mit der obersten Ebene verbundene Registrierung einrichten, stellen Sie sicher, dass sie alle erforderlichen Bilder lokal synchronisiert (`azureiotedge-agent`, `azureiotedge-hub`, `azureiotedge-api-proxy`, `acr/connected-registry`). Das IoT-Gerät der unteren Schicht muss diese Bilder von der angeschlossenen Registrierung der oberen Schicht beziehen.
- Sie verwendet das auf der unteren Schicht konfigurierte Sync-Token, um sich bei der mit der oberen Schicht verbundenen Registrierung zu authentifizieren.
- Er konfiguriert den übergeordneten Gateway-Endpunkt mit der IP-Adresse oder dem FQDN der mit der obersten Schicht verbundenen Registrierung und nicht mit dem FQDN der Cloud-Registrierung. 

> [!IMPORTANT]
> Im folgenden Bereitstellungsmanifest wird `$upstream` als IP-Adresse oder FQDN des Geräts verwendet, das die übergeordnete verbundene Registrierung hostet. Allerdings wird `$upstream` in einer Umgebungsvariablen nicht unterstützt. Die angeschlossene Registrierung muss die Umgebungsvariable `ACR_PARENT_GATEWAY_ENDPOINT` lesen, um den Endpunkt des übergeordneten Gateways zu erhalten. Anstatt `$upstream` zu verwenden, unterstützt die angeschlossene Registrierung die dynamische Auflösung der IP-Adresse oder des FQDN aus einer anderen Umgebungsvariablen. Auf dem verschachtelten IoT Edge gibt es auf der unteren Ebene eine Umgebungsvariable `$IOTEDGE_PARENTHOSTNAME`, die der IP-Adresse oder dem FQDN des übergeordneten Geräts entspricht. Ersetzen Sie die Umgebungsvariable manuell als Wert von `ParentGatewayEndpoint` in der Verbindungszeichenfolge, um die Festcodierung der übergeordneten IP-Adresse oder des FQDN zu vermeiden. Da auf dem übergeordneten Gerät in diesem Beispiel nginx an Port 8000 läuft, übergeben Sie `$IOTEDGE_PARENTHOSTNAME:8000`. Sie müssen auch das richtige Protokoll in `ParentEndpointProtocol` auswählen.

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "$upstream:8000/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=<REPLACE_WITH_SYNC_TOKEN_PASSWORD>;ParentGatewayEndpoint=$IOTEDGE_PARENTHOSTNAME:8000;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeApiProxy": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\": \"8000\"}]}}}"
                        },
                        "type": "docker",
                        "version": "1.0",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "NGINX_CONFIG_ENV_VAR_LIST": {
                                    "value": "NGINX_DEFAULT_PORT,BLOB_UPLOAD_ROUTE_ADDRESS,CONNECTED_ACR_ROUTE_ADDRESS,IOTEDGE_PARENTHOSTNAME,DOCKER_REQUEST_ROUTE_ADDRESS"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "startupOrder": 3
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "connectedregistry": {
                                "address": "$upstream:8000",
                                "password": "<REPLACE_WITH_SYNC_TOKEN_PASSWORD>",
                                "username": "<REPLACE_WITH_SYNC_TOKEN_NAME>"
                            }
                        }
                    },
                    "type": "docker"
                },
                "schemaVersion": "1.1",
                "systemModules": {
                    "edgeAgent": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-agent:1.2.4",
                            "createOptions": ""
                        },
                        "type": "docker",
                        "env": {
                            "SendRuntimeQualityTelemetry": {
                                "value": "false"
                            }
                        }
                    },
                    "edgeHub": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-hub:1.2.4",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                        },
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always"
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "routes": {
                    "route": "FROM /messages/* INTO $upstream"
                },
                "schemaVersion": "1.1",
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 7200
                }
            }
        }
    }
}
```

## <a name="set-up-and-deploy-connected-registry-modules-on-nested-iot-edge-devices"></a>Einrichten und Bereitstellen von verbundenen Registrierungsmodulen auf verschachtelten IoT-Edge-Geräten

Die folgenden Schritte sind an das [Tutorial angepasst: Erstellen einer Hierarchie von IoT Edge-Geräten](../iot-edge/tutorial-nested-iot-edge.md) und sind spezifisch für die Bereitstellung verbundener Registrierungsmodule in der IoT Edge-Hierarchie. Einzelheiten zu den einzelnen Schritten finden Sie in dieser Anleitung.

### <a name="create-top-layer-and-lower-layer-devices"></a>Erstellen von Geräten der oberen und unteren Schicht

Erstellen Sie VMs der oberen und unteren Schicht unter Verwendung einer vorhandenen [ARM-Vorlage](https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json). Mit der Vorlage wird auch der IoT Edge-Agent installiert. Wenn Sie stattdessen von Ihren eigenen Geräten aus bereitstellen möchten, siehe [Tutorial: Azure IoT Edge für Linux installieren oder deinstallieren](../iot-edge/how-to-install-iot-edge.md), um zu erfahren, wie Sie das Gerät manuell einrichten.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie für den späteren Zugriff auf die auf dem Gerät der obersten Schicht installierten Module die folgenden eingehenden Ports öffnen: 8000, 443, 5671, 8883. Die Konfigurationsschritte finden Sie unter [Öffnen von Ports für eine virtuelle Maschine mit dem Azure-Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="create-and-configure-the-hierarchy"></a>Erstellen und Konfigurieren der Hierarchie

Verwenden Sie das `iotedge-config` Tool, um Ihre Hierarchie zu erstellen und zu konfigurieren, indem Sie die folgenden Schritte in der Azure CLI oder Azure Cloud Shell ausführen:

1. Laden Sie das Konfigurationstool herunter.
    ```bash
    mkdir nestedIotEdgeTutorial
    cd ~/nestedIotEdgeTutorial
    wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
    tar -xvf iotedge_config.tar
    ```

    In diesem Schritt wird der Ordner `iotedge_config_cli_release` in Ihrem Lehrgangsverzeichnis erstellt. Die Vorlagendatei, die zum Erstellen Ihrer Gerätehierarchie verwendet wird, ist die Datei `iotedge_config.yaml` unter `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`. Im selben Verzeichnis befinden sich zwei Deployment-Manifeste für die obere und untere Schicht: `deploymentTopLayer.json` und `deploymentLowerLayer.json` Dateien. 

1. Bearbeiten Sie `iotedge_config.yaml` mit Ihren Informationen. Dazu gehören die Dateinamen der Bereitstellungsmanifeste `iothub_hostname` und `iot_name` für die oberste und die unterste Schicht sowie die Client-Token-Anmeldeinformationen, die Sie erstellt haben, um Bilder aus den Upstream-Bereichen der einzelnen Schichten zu beziehen. Nachfolgend ein Beispiel für eine Konfiguration

    ```yaml
    config_version: "1.0"

    iothub:
        iothub_hostname: <REPLACE_WITH_HUB_NAME>.azure-devices.net
        iothub_name: <REPLACE_WITH_HUB_NAME>
        ## Authentication method used by IoT Edge devices: symmetric_key or x509_certificate
        authentication_method: symmetric_key 

        ## Root certificate used to generate device CA certificates. Optional. If not provided a self-signed CA will be generated
        # certificates:
        #   root_ca_cert_path: ""
        #   root_ca_cert_key_path: ""

        ## IoT Edge configuration template to use
    configuration:
        template_config_path: "./templates/tutorial/device_config.toml"
        default_edge_agent: "$upstream:8000/azureiotedge-agent:1.2.4"

        ## Hierarchy of IoT Edge devices to create
    edgedevices:
        device_id: top-layer
        edge_agent: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4" ## Optional. If not provided, default_edge_agent will be used
        deployment: "./templates/tutorial/deploymentTopLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
            # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
        container_auth: ## The token used to pull the image from cloud registry
            serveraddress: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io"
            username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_TOP_LAYER>"
            password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_TOP_LAYER>"
        child:
            - device_id: lower-layer
              deployment: "./templates/tutorial/deploymentLowerLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
               # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
              container_auth: ## The token used to pull the image from parent connected registry
                serveraddress: "$upstream:8000"
                username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_LOWER_LAYER>"
                password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_LOWER_LAYER>"
    ```


1. Bereiten Sie die Bereitstellungsdateien der oberen und unteren Schicht vor (deploymentTopLayer.json und deploymentLowerLayer.json). Kopieren Sie die zuvor in diesem Artikel erstellten [Bereitstellungsmanifestdateien](#configure-deployment-manifests-for-the-nested-iot-edge-devices) in den folgenden Ordner: `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`.

1. Navigieren Sie zu Ihrem `iotedge_config_cli_release` Verzeichnis und führen Sie das Tool aus, um Ihre Hierarchie von IoT Edge-Geräten zu erstellen.

    ```bash
    cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
    ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
    ```

    Mit dem Flag `--output` erstellt das Tool die Gerätezertifikate, Zertifikatpakete und eine Protokolldatei in einem Verzeichnis Ihrer Wahl. Wenn das Flag `-f` angegeben ist, sucht das Tool automatisch nach vorhandenen IoT Edge-Geräten auf Ihrer IoT Hub-Instanz und entfernt diese, um Fehler zu vermeiden und für Ihren Hub den bereinigten Zustand sicherzustellen.

    Das Programm kann mehrere Minuten lang laufen.

1. Kopieren Sie die im vorherigen Schritt generierten `top-layer.zip` und `lower-layer.zip` Dateien mit `scp`。 in die entsprechenden VMs der oberen und unteren Schicht.

    ```bash
    scp <PATH_TO_CONFIGURATION_BUNDLE>   <USER>@<VM_IP_OR_FQDN>:~
    ```

1. Stellen Sie eine Verbindung mit dem Gerät der obersten Schicht her, um das Konfigurationspaket zu installieren.
    1. Entpacken Sie das Konfigurationsbündel. Sie müssen zuerst zip installieren.

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip top-layer.zip
        ```
    1. Führen Sie `sudo ./install.sh` aus, geben Sie die IP-Adresse oder den Hostnamen ein. Wir empfehlen die Verwendung der IP-Adresse.
    1. Führen Sie `sudo iotedge list` aus, um zu bestätigen, dass alle Module ausgeführt werden.

1. Stellen Sie eine Verbindung mit dem Gerät der unteren Schicht her, um das Konfigurationspaket zu installieren.
    1. Entpacken Sie das Konfigurationsbündel. Sie müssen zuerst zip installieren.

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip lower-layer.zip
        ```
    1. Führen Sie `sudo ./install.sh` aus, geben Sie die IP-Adressen oder Hostnamen des Geräts und des übergeordneten Systems ein. Wir empfehlen die Verwendung der IP-Adressen.
    1. Führen Sie `sudo iotedge list` aus, um zu bestätigen, dass alle Module ausgeführt werden.
 
    
Wenn Sie keine Bereitstellungsdatei für die Gerätekonfiguration angegeben haben oder wenn Probleme bei der Bereitstellung auftreten, z. B. ein ungültiges Bereitstellungsmanifest auf dem Gerät der obersten oder unteren Schicht, stellen Sie die Module manuell bereit. Informationen hierzu finden Sie im nächsten Abschnitt.

## <a name="manually-deploy-the-connected-registry-module"></a>Manuelle Bereitstellung des verbundenen Registrierungsmoduls

Verwenden Sie den folgenden Befehl, um das verbundene Registrierungsmodul manuell auf einem IoT Edge-Gerät bereitzustellen:

```azurecli
az iot edge set-modules \
  --device-id <device-id> \
  --hub-name <hub-name> \
  --content <deployment-manifest-filename>
```

Einzelheiten finden Sie unter [Bereitstellung von Azure IoT Edge-Modulen mit Azure CLI](../iot-edge/how-to-deploy-modules-cli.md).

Nach erfolgreicher Bereitstellung weist die verbundene Registrierung den Status `Online` auf.

Um den Status der verbundenen Registrierung zu überprüfen, verwenden Sie den folgenden Befehl [az acr connected-registry show][az-acr-connected-registry-show]:

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --output table
```

Möglicherweise müssen Sie einige Minuten warten, bis die Einrichtung der verbundenen Registrierung abgeschlossen ist.

Nach erfolgreicher Bereitstellung weist die verbundene Registrierung den Status `Online` auf.

Führen Sie `iotedge check` auf dem betroffenen Gerät aus, um eine Fehlersuche durchzuführen. Weitere Informationen finden Sie unter [Problembehandlung](../iot-edge/tutorial-nested-iot-edge.md#troubleshooting).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie eine verbundene Registrierung für ein verschachteltes IoT Edge-Gerät bereitstellen. Fahren Sie mit der nächsten Anleitung fort, um zu erfahren, wie Sie Bilder aus der neu eingerichteten verbundenen Registrierung abrufen.

> [!div class="nextstepaction"]
> [Bilder aus einer angeschlossenen Registratur holen][pull-images-from-connected-registry]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registr#az_acr_connected_registry_show
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-acr-token-credential-generate]: /cli/azure/acr/credential#az-acr-token-credential-generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
