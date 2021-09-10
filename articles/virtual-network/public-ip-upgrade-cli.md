---
title: 'Upgraden einer öffentlichen IP-Adresse: Azure CLI'
description: In diesem Artikel wird erläutert, wie Sie mithilfe der Azure CLI ein Upgrade für eine öffentliche IP-Adresse der Basic-SKU durchführen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 8408f90bfa4512a5d42c77c13b3648b0bca9646e
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439274"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-cli"></a>Durchführen eines Upgrades für eine öffentliche IP-Adresse mithilfe der Azure CLI

Öffentliche Azure-IP-Adressen werden mit einer SKU (Basic oder Standard) erstellt. Durch die SKU wird ihre Funktionalität festgelegt, darunter Zuordnungsmethode, Featureunterstützung und Ressourcen, denen sie zugeordnet werden können. 

In diesem Artikel wird erläutert, wie Sie über die Azure CLI ein Upgrade für eine statische öffentliche IP-Adresse der Basic-SKU auf die Standard-SKU durchführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Eine **statische** öffentliche IP-Adresse der Basic-SKU in Ihrem Abonnement. Weitere Informationen finden Sie unter [Erstellen einer öffentlichen IP-Adresse im Azure-Portal](create-public-ip-portal.md#create-a-basic-sku-public-ip-address).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="upgrade-public-ip-address"></a>Durchführen eines Upgrades für eine öffentliche IP-Adresse

In diesem Abschnitt verwenden Sie die Azure CLI, um ein Upgrade Ihrer statischen öffentlichen IP-Adresse der Basic-SKU auf die Standard-SKU durchzuführen.

```azurecli-interactive
az network public-ip update \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard

```
> [!NOTE]
> Die öffentliche IP-Adresse der Basic-SKU, für die ein Upgrade ausgeführt werden soll, muss den statischen Zuordnungstyp aufweisen. Wenn Sie versuchen, ein Upgrade für eine dynamisch zugeordnete IP-Adresse durchzuführen, erhalten Sie eine Warnung, dass das Upgrade der IP-Adresse nicht möglich ist.

> [!WARNING]
> Ein Upgrade einer öffentlichen IP-Adresse der Basic-SKU auf die Standard-SKU kann nicht rückgängig gemacht werden. Auch nach dem Upgrade von der Basic-SKU auf die Standard-SKU verfügen die öffentlichen IP-Adressen nicht über garantierte [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="verify-upgrade"></a>Verifizieren des Upgrades

In diesem Abschnitt überprüfen Sie, ob die öffentliche IP-Adresse jetzt der Standard-SKU entspricht.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myBasicPublicIP \
  --query sku \
  --output tsv

```
Mit diesem Befehl sollte **Standard** angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie ein Upgrade einer öffentlichen IP-Adresse der Basic-SKU auf eine Standard-SKU durchgeführt.

Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter:

- [Öffentliche IP-Adressen in Azure](public-ip-addresses.md)
- [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](create-public-ip-portal.md)

