---
title: Datei einfügen
description: Datei einfügen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5813ec99666e7e758dd1d58a639211d3d0ca0c94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448942"
---
- Derzeit nur in der Region USA, Westen-Mitte verfügbar.
- Wird nur für Datenfestplatten unterstützt.
- Festplatten, die kleiner als 4 TiB sind, können nicht ohne Ausfallzeit auf 4 TiB oder mehr erweitert werden.
    - Wenn Sie eine Festplatte auf 4 TiB oder mehr vergrößern, kann sie ohne Ausfallzeit erweitert werden.
- Installieren und Verwenden Sie entweder:
    - Die [aktuelle Azure CLI](/cli/azure/install-azure-cli)
    - Das [aktuelle Azure PowerShell-Modul](/powershell/azure/install-az-ps)
    - Das Azure-Portal bei Zugriff über [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize)
    - Oder eine Azure Resource Manager-Vorlage mit API-Version 2021-04-01 oder höher