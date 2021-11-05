---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/10/2021
ms.author: danlep
ms.openlocfilehash: 4153c44c60e8da560b5523e5263f0f7e0c81fcec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100312"
---
### <a name="connected-registry-module-settings"></a>Einstellungen für verbundene Registrierungsmodule

* Verwenden Sie die Token-Anmeldeinformationen und die Verbindungszeichenfolge aus den vorherigen Abschnitten, um die relevanten JSON-Werte im `env` Knoten zu aktualisieren. 
* Die folgenden Umgebungsvariablen sind optional im `env` Knoten:

    |Variable  |BESCHREIBUNG  |
    |---------|---------|
    | `ACR_REGISTRY_LOGIN_SERVER` |  Gibt einen eindeutigen Hostnamen oder FQDN an. Bei Verwendung akzeptiert die verbundene Registrierung nur Anforderungen, die an diesen Anmeldeserverwert gesendet werden. <br/><br/>Wenn kein Wert angegeben wird, kann auf die verbundene Registrierung mit einem beliebigen Anmeldeserverwert zugegriffen werden.  |
    |`ACR_REGISTRY_CERTIFICATE_VOLUME`     |   Wenn auf Ihre verbundene Registrierung über HTTPS zugegriffen werden kann, verweist sie auf das Verzeichnis, auf dem die HTTPS-Zertifikate gespeichert sind.<br/><br/>Wenn nicht festgelegt, ist der Standardspeicherort `/var/acr/certs`.      |
    |`ACR_REGISTRY_DATA_VOLUME`     |  Überschreibt den Standardspeicherort `/var/acr/data`, an dem die Images von der verbundenen Registrierung gespeichert werden.<br/><br/>Dieser Speicherort muss mit der Verzeichnisbindung für den Container übereinstimmen.       |

    > [!IMPORTANT]
    > Wenn die verbundene Registrierung auf einen anderen Port als 80 und 443 lauscht, muss der Wert `ACR_REGISTRY_LOGIN_SERVER` (sofern angegeben) den Port enthalten. Beispiel: `192.168.0.100:8080`.
* Wenn das API-Proxymodul nicht verwendet wird, sollte eine `HostPort` Bindung für die verbundene Registrierung festgelegt werden. Beispiel:

    ```azurecli
     "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"],\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"8080\"}]}}}"
    ```

### <a name="api-proxy-module-settings"></a>API-Proxymodul Einstellungen

* Der API-Proxy lauscht auf Port 8000, der als `NGINX_DEFAULT_PORT` konfiguriert ist. Weitere Informationen zu den API-Proxyeinstellungen finden Sie im [IoT Edge GitHub Repository](https://github.com/Azure/iotedge/tree/master/edge-modules/api-proxy-module). 

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=REPLACE_WITH_SYNC_TOKEN_PASSWORD;ParentGatewayEndpoint=<REPLACE_WITH_CLOUD_REGISTRY_NAME>.<REPLACE_WITH_CLOUD_REGISTRY_REGION>.data.azurecr.io;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeAPIProxy": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8000/tcp\":[{\"HostPort\":\"8000\"}]}}}"
                        },
                        "type": "docker",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "cloudregistry": {
                                "address": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io",
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
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4",
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
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-hub:1.2.4",
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
