---
title: Migrieren einer klassischen reservierten IP-Adresse zu einer öffentlichen IP-Adresse
titleSuffix: Azure Virtual Network
description: In diesem Artikel erfahren Sie, wie Sie eine reservierte IP-Adresse des klassischen Bereitstellungsmodells auf eine öffentliche Azure Resource Manager-IP-Adresse aktualisieren.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: a07f18f0bb340b095f8b3d2b3392824e6a7bbf1d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251127"
---
# <a name="migrate-a-classic-reserved-ip-address-to-a-public-ip-address"></a>Migrieren einer klassischen reservierten IP-Adresse zu einer öffentlichen IP-Adresse

Wenn Sie die neuen Funktionen in Azure Resource Manager nutzen möchten, können Sie vorhandene öffentliche statische IP-Adressen (reservierte IPs) vom klassischen Bereitstellungsmodell zu Azure Resource Manager migrieren.  Die migrierte öffentliche IP-Adresse hat dann den SKU-Typ „Basic“. 

In diesem Artikel erfahren Sie, wie Sie eine klassische reservierte IP-Adresse auf eine grundlegende öffentliche IP-Adresse aktualisieren.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Ihr Azure-Abonnement muss für die Migration registriert sein. Weitere Informationen finden Sie unter [Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von PowerShell](../../virtual-machines/migration-classic-resource-manager-ps.md).
* Eine reservierte IP-Adresse des klassischen Bereitstellungsmodells
* Das Azure PowerShell-Dienstverwaltungsmodul muss für PowerShell installiert sein. Weitere Informationen finden Sie unter [Installieren des Azure PowerShell-Dienstverwaltungsmoduls](/powershell/azure/servicemanagement/install-azure-ps).
* Die klassische Azure CLI muss für Azure CLI-Anweisungen installiert sein. Weitere Informationen finden Sie unter [Installieren der klassischen Azure CLI](/cli/azure/install-classic-cli).

## <a name="azure-powershell-service-management-module"></a>Azure PowerShell-Dienstverwaltungsmodul

In diesem Abschnitt verwenden Sie das Azure PowerShell-Dienstverwaltungsmodul, um eine klassische reservierte IP-Adresse zu einer statischen öffentlichen Azure Resource Manager-IP-Adresse zu migrieren.

> [!NOTE]
> Die reservierte IP-Adresse muss aus jedem Clouddienst entfernt werden, dem die IP-Adresse zugeordnet ist.

```azurepowershell-interactive
$validate = Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Validate
$validate.ValidationMessages

```

Der vorhergehende Befehl zeigt ggf. Warnungen und Fehler an, die die Migration blockieren. Wenn die Validierung erfolgreich ist, können Sie mit den folgenden Schritten zum **Vorbereiten** und **Committen** der Migration fortfahren:

```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Prepare
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Commit
```
In Azure Resource Manager wird eine neue Ressourcengruppe mit dem Namen der migrierten reservierten IP erstellt. Im obigen Beispiel heißt die Ressourcengruppe **myReservedIP-Migrated**.

## <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle

In diesem Abschnitt verwenden Sie die klassische Azure CLI, um eine klassische reservierte IP-Adresse zu einer statischen öffentlichen Azure Resource Manager-IP-Adresse zu migrieren.

> [!NOTE]
> Die reservierte IP-Adresse muss aus jedem Clouddienst entfernt werden, dem die IP-Adresse zugeordnet ist.

```azurecli-interactive
azure network reserved-ip validate migration myReservedIP

```
Der vorhergehende Befehl zeigt ggf. Warnungen und Fehler an, die die Migration blockieren. Wenn die Validierung erfolgreich ist, können Sie mit den folgenden Schritten zum **Vorbereiten** und **Committen** der Migration fortfahren:

```azurecli-interactive
azure network reserved-ip prepare-migration myReservedIP
azure network reserved-ip commit-migration myReservedIP
```
In Azure Resource Manager wird eine neue Ressourcengruppe mit dem Namen der migrierten reservierten IP erstellt. Im obigen Beispiel heißt die Ressourcengruppe **myReservedIP-Migrated**.

## <a name="next-steps"></a>Nächste Schritte


Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie in den folgenden Artikeln:

- [Öffentliche IP-Adressen in Azure](public-ip-addresses.md)
- [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](./create-public-ip-portal.md)