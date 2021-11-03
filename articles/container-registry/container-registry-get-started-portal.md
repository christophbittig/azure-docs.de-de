---
title: 'Schnellstart: Erstellen einer Registrierung im Portal'
description: Hier lernen Sie, wie Sie schnell eine private Azure-Containerregistrierung über das Azure-Portal erstellen.
ms.date: 06/23/2021
ms.topic: quickstart
ms.custom: mvc, mode-portal, contperf-fy21q4
ms.openlocfilehash: 8ac194e2343120dea1daec688bf383162846bdfd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043545"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Schnellstart: Erstellen einer Azure-Containerregistrierung über das Azure-Portal

Azure Container Registry ist ein privater Registrierungsdienst zum Erstellen, Speichern und Verwalten von Containerimages und verwandten Artefakten. In dieser Schnellstartanleitung erstellen Sie eine Azure Container Registry-Instanz über das Azure-Portal. Übertragen Sie anschließend mithilfe von Docker-Befehlen ein Containerimage per Push in die Registrierung. Rufen Sie abschließend das Image per Pull aus der Registrierung ab, und führen Sie es aus.

Für diese Schnellstartanleitung müssen Sie die Azure CLI (mindestens Version 2.0.55 empfohlen) ausführen, um sich bei der Registrierung anmelden und Containerimages verwenden zu können. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

Darüber hinaus muss Docker lokal installiert sein. Für Docker sind Pakete erhältlich, mit denen Docker problemlos auf einem [Mac][docker-mac]-, [Windows][docker-windows]- oder [Linux][docker-linux]-System konfiguriert werden kann.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Klicken Sie auf **Ressource erstellen** > **Container** > **Container Registry**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Navigieren zur Containerregistrierung im Portal":::

Geben Sie auf der Registerkarte **Grundlagen** Werte für **Ressourcengruppe** und **Registrierungsname** ein. Der Registrierungsname muss innerhalb von Azure eindeutig sein und zwischen 5 und 50 alphanumerische Zeichen enthalten. Erstellen Sie im Rahmen dieser Schnellstartanleitung eine neue Ressourcengruppe namens `myResourceGroup` am Standort `West US`, und wählen Sie für **SKU** die Option „Basic“ aus.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Erstellen einer Containerregistrierung im Portal":::

Übernehmen Sie für die übrigen Einstellungen die Standardwerte. Wählen Sie dann **Überprüfen + erstellen** aus. Überprüfen Sie die Einstellungen, und wählen Sie anschließend **Erstellen** aus.

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

Wenn die Meldung **Bereitstellung erfolgreich** erscheint, wählen Sie die Containerregistrierung im Portal aus. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Containerregistrierungsübersicht im Portal":::

Notieren Sie sich den Registrierungsnamen und den Wert des **Anmeldeservers**, bei dem es sich um einen vollqualifizierten Namen handelt, der auf `azurecr.io` in der Azure-Cloud endet. Sie verwenden diese Werte in den folgenden Schritten bei den Push- und Pullvorgängen für Images mit Docker.

## <a name="log-in-to-registry"></a>Anmelden bei der Registrierung

Bevor Sie Push- und Pullvorgänge für Containerimages ausführen können, müssen Sie sich bei der Registrierungsinstanz anmelden. [Melden Sie sich auf dem lokalen Computer bei der Azure CLI an][get-started-with-azure-cli], und führen Sie dann den Befehl [az acr login][az-acr-login] aus. Geben Sie beim Anmelden bei der Azure CLI nur den Namen der Registrierungsressource an. Verwenden Sie nicht den vollqualifizierten Namen des Anmeldeservers.

```azurecli
az acr login --name <registry-name>
```

Beispiel:

```azurecli
az acr login --name mycontainerregistry
```

Der Befehl gibt nach Abschluss des Vorgangs `Login Succeeded` zurück. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Auflisten von Containerimages

Navigieren Sie zum Auflisten der Images in Ihrer Registrierung im Portal zu Ihrer Registrierung, und wählen Sie **Repositorys** und dann das Repository **hello-world** aus, das Sie mit `docker push` erstellt haben.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Auflisten von Containerimages im Portal":::

Wenn Sie das Repository **hello-world** auswählen, wird unter **Tags** das mit `v1` gekennzeichnete Image angezeigt.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Navigieren Sie zum Bereinigen von Ressourcen im Portal zur Ressourcengruppe **myResourceGroup**. Klicken Sie nach dem Laden der Ressourcengruppe auf **Ressourcengruppe löschen**, um die Ressourcengruppe, die Containerregistrierung und die dort gespeicherten Containerimages zu entfernen.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Löschen der Ressourcengruppe im Portal":::


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie im Azure-Portal eine Azure Container Registry-Instanz erstellt, ein Containerimage per Push übertragen und das Image per Pull aus der Registrierung abgerufen und ausgeführt. Fahren Sie mit den Azure Container Registry-Tutorials fort, um eingehendere Informationen zu ACR zu erhalten.

> [!div class="nextstepaction"]
> [Tutorials zu Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutorials zu Azure Container Registry Tasks][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
