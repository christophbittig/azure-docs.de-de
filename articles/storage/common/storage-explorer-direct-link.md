---
title: Direkter Link in Azure Storage-Explorer | Microsoft-Dokumentation
description: Dokumentation zum direkten Link in Azure Storage-Explorer
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 283ee2dd27d22e16177efe34d4f456eca40d997d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451491"
---
# <a name="azure-storage-explorer-direct-link"></a>Direkter Link in Azure Storage-Explorer

Unter Windows und macOS unterstützt Storage-Explorer URIs mit dem Protokoll `storageexplorer://`. Diese URIs werden als „direkte Links“ bezeichnet. Ein direkter Link verweist auf eine Azure Storage-Ressource in Storage-Explorer. Wenn Sie einem direkten Link folgen, wird Storage-Explorer geöffnet und zu der Ressource navigiert, auf die er verweist. In diesem Artikel wird beschrieben, wie direkte Links funktionieren und wie Sie sie verwenden können.

## <a name="how-direct-links-work"></a>Funktionsweise von direkten Links

Storage-Explorer verwendet die Strukturansicht zum Visualisieren von Ressourcen in Azure. Ein direkter Link enthält die hierarchischen Informationen für den verknüpften Ressourcenknoten in der Struktur. Wenn einem direkten Link gefolgt wird, wird Storage-Explorer geöffnet, und er empfängt die Parameter im direkten Link. Storage-Explorer verwendet dann diese Parameter, um zur verknüpften Ressource in der Strukturansicht zu navigieren.

> [!IMPORTANT]
> Sie müssen angemeldet sein und die erforderlichen Berechtigungen für den Zugriff auf die verknüpfte Ressource haben, damit ein direkter Link funktioniert.

## <a name="parameters"></a>Parameter

Ein direkter Link in Storage-Explorer beginnt immer mit dem Protokoll `storageexplorer://`. In der folgenden Tabelle wird jeder der möglichen Parameter in einem direkten Link erläutert.

Parameter | BESCHREIBUNG
:---------| :---------
`v`         | Version des Protokolls für den direkten Link.
`accountid` | Die Azure Resource Manager-Ressourcen-ID des Speicherkontos für die verknüpfte Ressource. Wenn die verknüpfte Ressource ein Speicherkonto ist, ist diese ID die Azure Resource Manager-Ressourcen-ID für dieses Speicherkonto. Andernfalls ist diese ID die Azure Resource Manager-Ressourcen-ID für das Speicherkonto, zu dem die verknüpfte Ressource gehört.
`resourcetype` | Optional. Wird nur verwendet, wenn die verknüpfte Ressource ein Blobcontainer, eine Dateifreigabe, eine Warteschlange oder eine Tabelle ist. Muss „Azure.BlobContainer“, „Azure.FileShare“, „Azure.Queue“ oder „Azure.FileShare“ sein.
`resourcename` | Optional. Wird nur verwendet, wenn die verknüpfte Ressource ein Blobcontainer, eine Dateifreigabe, eine Warteschlange oder eine Tabelle ist. Der Name der verknüpften Ressource.

Hier ist ein Beispiel für einen direkten Link zu einem Blobcontainer. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Abrufen eines direkten Links aus Storage-Explorer

Sie können über Storage-Explorer einen direkten Link für eine Ressource abrufen. Öffnen Sie das Kontextmenü des Knotens für die Ressource in der Strukturansicht. Kopieren Sie dann den direkten Link mithilfe der Aktion „Copy Direct Link“ (Direkten Link kopieren) in die Zwischenablage.

## <a name="direct-link-limitations"></a>Einschränkungen für direkte Links

Direkte Links werden nur bei Ressourcen unter Abonnementknoten unterstützt. Außerdem werden nur die folgenden Ressourcentypen unterstützt:

- Speicherkonten
- Blobcontainer
- Warteschlangen
- Dateifreigaben
- Tabellen
