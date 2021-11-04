---
title: Erstellen eines Azure CDN-Profils (Content Delivery Network) und eines entsprechenden Endpunkts mithilfe der Azure CLI
description: Azure CLI-Beispielskripts zum Erstellen eines Azure CDN-Profils, eines CDN-Endpunkts, einer CDN-Ursprungsgruppe, eines CDN-Ursprungs und einer benutzerdefinierten Domäne.
author: duongau
ms.author: duau
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 46966ed6e4e0db1be50919d368bf1ff2fd0fa85a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476850"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Erstellen eines Azure CDN-Profils und eines entsprechenden Endpunkts mithilfe der Azure CLI

Alternativ zum Azure-Portal können Sie diese Azure CLI-Beispielskripts für folgende CDN-Vorgänge verwenden:

- Erstellen eines CDN-Profils
- Erstellen eines CDN-Endpunkts
- Erstellen einer CDN-Ursprungsgruppe und Festlegen als Standardgruppe
- Erstellen eines CDN-Ursprungs
- Erstellen einer benutzerdefinierten Domäne und Aktivieren von HTTPS

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Beispielskripts

Sollten Sie noch nicht über eine Ressourcengruppe für Ihr CDN-Profil verfügen, erstellen Sie sie mithilfe des Befehls `az group create`:

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

Das folgende Azure CLI-Skript erstellt ein CDN-Profil und einen CDN-Endpunkt:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

Das folgende Azure CLI-Skript erstellt eine CDN-Ursprungsgruppe, legt die Standardursprungsgruppe für einen Endpunkt fest und erstellt einen neuen Ursprung:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

Das folgende Azure CLI-Skript erstellt eine benutzerdefinierte CDN-Domäne und aktiviert HTTPS. Bevor Sie eine benutzerdefinierte Domäne einem Azure CDN-Endpunkt zuordnen können, müssen Sie zunächst einen CNAME-Eintrag (Canonical Name, kanonischer Name) bei Azure DNS oder bei Ihrem DNS-Anbieter erstellen, um auf Ihren CDN-Endpunkt zu verweisen. Weitere Informationen finden Sie unter [Erstellen eines CNAME-DNS-Eintrags](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie nach Ausführung der Beispielskripts den folgenden Befehl, um die Ressourcengruppe und alle zugeordneten Ressourcen zu entfernen:

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>In diesem Artikel verwendete Azure CLI-Befehle

- [az cdn endpoint create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az cdn endpoint update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az cdn origin create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origin-group create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az cdn profile create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az cdn custom-domain create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az cdn custom-domain enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
