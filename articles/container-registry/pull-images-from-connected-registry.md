---
title: Abrufen von Images aus einer verbundenen Registrierung per Pull
description: Verwenden Sie CLI-Befehle für die Azure Container Registry, um ein Clienttoken zu konfigurieren und Images per Pull aus einer verbundenen Registrierung auf einem IoT Edge-Gerät abzurufen.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5409afea3ab1ab1ebedaed87adbafd2b15fe9418
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029450"
---
# <a name="pull-images-from-a-connected-registry-on-iot-edge-device"></a>Abrufen von Images aus einer verbundenen Registrierung auf einem IoT Edge-Gerät per Pull

Um Images per Pull aus einer [verbundenen Registrierung](intro-connected-registry.md) abzurufen, konfigurieren Sie ein [Clienttoken](overview-connected-registry-access.md#client-tokens), und übergeben Sie die Tokenanmeldeinformationen für den Zugriff auf den Inhalt der Registrierung.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
* Verbundene Registrierungsressource in Azure. Bereitstellungsschritte finden Sie unter [Schnellstart: Erstellen einer verbundenen Registrierung mithilfe der Azure CLI][quickstart-connected-registry-cli].
* Instanz der verbundenen Registrierung, die auf einem IoT Edge-Gerät bereitgestellt ist. Bereitstellungsschritte finden Sie unter [Schnellstart: Bereitstellen einer verbundenen Registrierung auf einem IoT Edge-Gerät](quickstart-deploy-connected-registry-iot-edge-cli.md) oder [Tutorial: Bereitstellen einer verbundenen Registrierung auf geschachtelten IoT Edge-Geräten](tutorial-deploy-connected-registry-nested-iot-edge-cli.md). In den Befehlen in diesem Artikel ist der Name der verbundenen Registrierung in der Umgebungsvariablen *$CONNECTED_REGISTRY_RW* gespeichert.

## <a name="create-a-scope-map"></a>Erstellen einer Bereichszuordnung

Verwenden Sie den Befehl [az acr scope-map create][az-acr-scope-map-create], um eine Bereichszuordnung für den Lesezugriff auf das Repository `hello-world` zu erstellen:

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr scope-map create \
  --name hello-world-scopemap \
  --registry $REGISTRY_NAME \
  --repository hello-world content/read \
  --description "Scope map for the connected registry."
```

## <a name="create-a-client-token"></a>Erstellen eines Clienttokens

Verwenden Sie den Befehl [az acr token create][az-acr-token-create], um ein Clienttoken zu erstellen und mit der neu erstellten Bereichszuordnung zu verknüpfen:

```azurecli
az acr token create \
  --name myconnectedregistry-client-token \
  --registry $REGISTRY_NAME \
  --scope-map hello-world-scopemap
```

Der Befehl gibt Details zum neu generierten Token zurück, einschließlich Kennwörtern.

  > [!IMPORTANT]
  > Stellen Sie sicher, dass Sie die generierten Kennwörter speichern. Es handelt sich um einmalige Kennwörter, die nicht erneut abgerufen werden können. Sie können mit dem Befehl [az acr token credential generate][az-acr-token-credential-generate] neue Kennwörter generieren.

## <a name="update-the-connected-registry-with-the-client-token"></a>Aktualisieren der verbundenen Registrierung mit dem Clienttoken

Verwenden Sie den Befehl [az acr connected-registry update][az-acr-connected-registry-update], um die verbundene Registrierung mit dem neu erstellten Clienttoken zu aktualisieren. 

```azurecli
az acr connected-registry update \
  --name $CONNECTED_REGISTRY_RW \
  --registry $REGISTRY_NAME \
  --add-client-token myconnectedregistry-client-token
```

## <a name="pull-an-image-from-the-connected-registry"></a>Abrufen eines Images aus der verbundenen Registrierung per Pull

Verwenden Sie auf einem Computer mit Zugriff auf das IoT Edge-Gerät den folgenden Beispielbefehl, um sich mithilfe der Clienttokenanmeldeinformationen bei der verbundenen Registrierung anzumelden. Best Practices zur Verwaltung von Anmeldeinformationen finden Sie in der Befehlsreferenz [Docker-Anmeldung](https://docs.docker.com/engine/reference/commandline/login/).

> [!CAUTION]
> Wenn Ihre verbundene Registrierung als unsichere Registrierung eingerichtet wurde, aktualisieren Sie die Liste der unsicheren Registrierungen in der Docker-Daemonkonfiguration so, dass sie die IP-Adresse (oder den FQDN) und den Port Ihrer verbundenen Registrierung auf dem IoT Edge-Gerät enthält. Diese Konfiguration sollte nur zu Testzwecken verwendet werden. Weitere Informationen finden Sie unter [Testen einer unsicheren Registrierung](https://docs.docker.com/registry/insecure/).

```
docker login --username myconnectedregistry-client-token \
  --password <token_password> <IP_address_or_FQDN_of_connected_registry>:<port>
```

Stellen Sie in IoT Edge-Szenarien sicher, dass Sie den Port angeben, der zum Erreichen der verbundenen Registrierung auf dem Gerät verwendet wird. Beispiel:

```
docker login --username myconnectedregistry-client-token \
  --password xxxxxxxxxxx 192.0.2.13:8000
```

Verwenden Sie dann den folgenden Befehl, um das Image `hello-world` per Pull abzurufen:

```
docker pull <IP_address_or_FQDN_of_connected_registry>:<port>/hello-world
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Token im Repositorybereich](container-registry-repository-scoped-permissions.md).
* Erfahren Sie mehr über den [Zugriff auf eine verbundene Registrierung](overview-connected-registry-access.md).

<!-- LINKS - internal -->
[az-acr-scope-map-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential#az_acr_token_credential_generate
[az-acr-connected-registry-update]: /cli/azure/acr/connect-registry#az_acr_connected_registry_update] 
[container-registry-intro]: container-registry-intro.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
