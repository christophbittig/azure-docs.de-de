---
title: Informationen zu Azure Bastion-Konfigurationseinstellungen
description: Erfahren Sie mehr über die verfügbaren Konfigurationseinstellungen für Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71a86e1440d96a37fd72afc67aaaaa7d122c6d4f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426675"
---
# <a name="about-bastion-configuration-settings"></a>Informationen zu Azure Bastion-Konfigurationseinstellungen

In den Abschnitten in diesem Artikel werden die Ressourcen und Einstellungen für Azure Bastion erläutert.

## <a name="skus"></a><a name="skus"></a>SKUs

Eine SKU wird auch als Tarif bezeichnet. Azure Bastion unterstützt zwei SKU-Typen: Basic und Standard. Die SKU wird während des Workflows im Azure-Portal konfiguriert, wenn Sie Bastion konfigurieren. Sie können eine [Basic-SKU zu einer Standard-SKU upgraden](#upgradesku).

* Die **Basic-SKU** bietet Basisfunktionen, die es Azure Bastion ermöglichen, RDP-/SSH-Konnektivität mit virtuellen Computern (VMs) zu verwalten, ohne öffentliche IP-Adressen auf den VMs der Zielanwendung verfügbar zu machen.
* Die Standrad-SKU ermöglicht Premium-Features, durch die Azure Bastion die Remotekonnektivität in größerem Umfang verwalten kann.

Die folgende Tabelle enthält die Features der entsprechenden SKUs. 

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

### <a name="configuration-methods"></a>Konfigurationsmethoden

Während der Vorschauphase müssen Sie das Azure-Portal verwenden, wenn Sie die Standard-SKU angeben möchten. Wenn Sie die Azure CLI oder Azure PowerShell zum Konfigurieren von Bastion verwenden, kann die SKU nicht angegeben werden, und es wird standardmäßig die Basic-SKU verwendet.

| Methode | Wert | Links |
| --- | --- | --- |
| Azure-Portal | Tarif: Basic oder <br>Standard | [Schnellstart: Konfigurieren von Bastion über VM-Einstellungen](quickstart-host-portal.md)<br>[Tutorial: Konfigurieren von Bastion](tutorial-create-host-portal.md) |
| Azure PowerShell | Nur Basic – keine Einstellungen |[Konfiguration von Bastion: PowerShell](bastion-create-host-powershell.md) |
| Azure CLI |  Nur Basic – keine Einstellungen | [Konfigurieren von Bastion: CLI](create-host-cli.md) |

### <a name="upgrade-a-sku"></a><a name="upgradesku"></a>Aktualisieren einer SKU

Azure Bastion unterstützt das Upgrade von einer Basic-SKU auf eine Standard-SKU.

> [!NOTE]
> Eine Herabstufung von einer Standard-SKU auf eine Basic-SKU wird nicht unterstützt. Zum Herabstufen müssen Sie Azure Bastion löschen und neu erstellen.
>

#### <a name="configuration-methods"></a>Konfigurationsmethoden

Sie können diese Einstellung konfigurieren, indem Sie die folgende Methode anwenden:

| Methode | Wert | Links |
| --- | --- | --- |
| Azure-Portal |Tarif  | [Aktualisieren einer SKU](upgrade-sku.md)|

## <a name="instances-and-host-scaling"></a><a name="instance"></a>Instanzen und Hostskalierung

Eine Instanz ist eine optimierte Azure-VM, die erstellt wird, wenn Sie Azure Bastion konfigurieren. Sie wird vollständig von Azure verwaltet und führt alle Prozesse aus, die für Azure Bastion erforderlich sind. Eine Instanz wird auch als Skalierungseinheit bezeichnet. Sie stellen über eine Azure Bastion-Instanz eine Verbindung mit Client-VMs her. Wenn Sie Azure Bastion mithilfe der Basic-SKU konfigurieren, werden zwei Instanzen erstellt. Wenn Sie die Standard-SKU verwenden, können Sie die Anzahl der Instanzen angeben. Dies wird als **Hostskalierung** bezeichnet. 

Jede Instanz kann zehn bis zwölf gleichzeitige RDP-/SSH-Verbindungen unterstützen. Die Anzahl der Verbindungen pro Instanz hängt davon ab, welche Aktionen Sie ausführen, wenn Sie eine Verbindung mit der Client-VM herstellen. Wenn Sie z. B. datenintensiv vorgehen, wird eine größere Last für die zu verarbeitende Instanz erstellt. Sobald die gleichzeitigen Sitzungen überschritten werden, ist eine zusätzliche Skalierungseinheit (Instanz) erforderlich. 

Instanzen werden im AzureBastionSubnet erstellt. Für die Hostskalierung sollte azureBastionSubnet /26 oder größer sein. Die Verwendung eines kleineren Subnetzes schränkt die Anzahl der Instanzen ein, die Sie erstellen können. Weitere Informationen zu AzureBastionSubnet finden Sie im Abschnitt [Subnetz](#subnet) in diesem Artikel.

### <a name="configuration-methods"></a>Konfigurationsmethoden

Sie können diese Einstellung konfigurieren, indem Sie die folgende Methode anwenden:

| Methode | Wert | Links |
| --- | --- | --- |
| Azure-Portal |Anzahl von Instanzen  | [Konfigurieren der Hostskalierung](configure-host-scaling.md)|


## <a name="azure-bastion-subnet"></a><a name="subnet"></a>Azure Bastion-Subnetz

Azure Bastion erfordert ein dediziertes Subnetz: **AzureBastionSubnet**. Dieses Subnetz muss in demselben virtuellen Netzwerk erstellt werden, in dem Azure Bastion bereitgestellt wird. Das Subnetz muss über die folgende Konfiguration verfügen:

* Subnetz: *AzureBastionSubnet*.
* Die Subnetzgröße muss /26 oder größer sein (/25, /24 usw.).
* Für die Hostskalierung wird ein wird ein Subnetz ab /26 empfohlen. Die Verwendung eines kleineren Subnetzbereichs schränkt die Anzahl der Skalierungseinheiten ein. Weitere Informationen finden Sie im Abschnitt [Hostskalierung](#instance) in diesem Artikel.
* Das Subnetz muss sich im selben VNet und in derselben Ressourcengruppe wie der Bastionhost befinden.
* Das Subnetz darf keine zusätzlichen Ressourcen enthalten.

### <a name="configuration-methods"></a>Konfigurationsmethoden

Sie können diese Einstellung konfigurieren, indem Sie die folgende Methoden anwenden:

| Methode | Wert | Links |
| --- | --- |--- |
| Azure-Portal | Subnet  |[Schnellstart: Konfigurieren von Bastion über VM-Einstellungen](quickstart-host-portal.md)<br>[Tutorial: Konfigurieren von Bastion](tutorial-create-host-portal.md)|
| Azure PowerShell | -subnetName|[cmdlet](/powershell/module/az.network/new-azbastion#parameters) |
| Azure CLI |  --subnet-name | [command](/cli/azure/network/vnet#az_network_vnet_create) |

## <a name="public-ip-address"></a><a name="public-ip"></a>Öffentliche IP-Adresse

Azure Bastion erfordert eine öffentliche IP-Adresse. Die öffentliche IP-Adresse muss über die folgende Konfiguration verfügen:

* Die SKU für öffentliche IP-Adressen muss **Standard** sein.
* Die Zuweisungs-/Zuordnungsmethode für öffentliche IP-Adressen muss **Statisch** sein.
* Der Name der öffentlichen IP-Adresse ist der Ressourcenname, mit dem Sie auf diese öffentliche IP-Adresse verweisen möchten.
* Sie können eine öffentliche IP-Adresse verwenden, die Sie bereits erstellt haben, sofern sie die für Azure Bastion erforderlichen Kriterien erfüllt und noch nicht verwendet wird.

### <a name="configuration-methods"></a>Konfigurationsmethoden

Sie können diese Einstellung konfigurieren, indem Sie die folgende Methoden anwenden:

| Methode | Wert | Links |
| --- | --- |--- |
| Azure-Portal | Öffentliche IP-Adresse |[Azure-Portal](https://portal.azure.com)|
| Azure PowerShell | -PublicIpAddress| [cmdlet](/powershell/module/az.network/new-azbastion#parameters)  |
| Azure CLI | --public-ip create |[command](/cli/azure/network/public-ip)
|

## <a name="next-steps"></a>Nächste Schritte

Häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen zu Azure Bastion](bastion-faq.md).
