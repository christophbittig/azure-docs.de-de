---
title: Verschieben von Ressourcen aus Azure Notification Hubs aus einer Region in eine andere
description: Erfahren Sie, wie man Ressourcen eines Azure Notification Hub in eine andere Azure-Region verschiebt.
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: how-to
ms.date: 09/07/2021
ms.custom: template-how-to
ms.openlocfilehash: 0de15ab1f16ab5bc234ad6430eae5f65e608753e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598507"
---
# <a name="move-resources-between-azure-regions"></a>Verschieben von Ressourcen zwischen Azure-Regionen

In diesem Artikel wird beschrieben, wie Sie Ressourcen aus Azure Notification Hubs in eine andere Azure-Region verschieben. Stark zusammengefasst sieht der Vorgang so aus:

1. Erstellen Sie einen Zielnamespace mit einem anderen Namen.
1. Exportieren Sie die Registrierungen aus dem vorherigen Namespace.
1. Importieren Sie die Registrierungen in den neuen Namespace in der gewünschten Region.

## <a name="overview"></a>Übersicht

In einigen Szenarien müssen Sie evtl. Dienstressourcen aus verschiedenen geschäftlichen Gründen zwischen Azure-Regionen verschieben. Diese können zu einer neu verfügbaren Region wechseln. Möglicherweise möchten Sie Features oder Dienste bereitstellen, die nur in einer bestimmten Region verfügbar sind, aufgrund interner Richtlinien- oder Konformitätsanforderungen verschieben oder Kapazitätsprobleme lösen.

Namen eines Azure Notification Hubs Namespace sind eindeutig, und Registrierungen gelten pro Hub. Um also eine solche Verschiebung durchzuführen, müssen Sie einen neuen Hub in der gewünschten Region erstellen und dann die Registrierungen zusammen mit allen anderen relevanten Daten in den neu erstellten Namespace verschieben.

## <a name="create-a-notification-hubs-namespace-with-a-different-name"></a>Erstellen eines Notification Hubs-Namespace mit einem anderen Namen

Führen Sie die folgenden Schritte aus, um einen neuen Notification Hubs Namespace zu erstellen. Geben Sie auf der Registerkarte **Grundlagen** alle erforderlichen Informationen ein, einschließlich der gewünschten Zielregion für den Namespace.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

Nachdem der neue Namespace erstellt wurde, legen Sie die PNS-Anmeldeinformationen im neuen Namespace fest und erstellen Sie entsprechende Richtlinien im neuen Namespace.

## <a name="exportimport-registrations"></a>Exportieren und Ändern von Registrierungen

Nachdem der neue Namespace in der Region erstellt wurde, in die Sie die Ressource verschieben möchten, exportieren Sie alle Registrierungen in einem Massenvorgang und importieren Sie diese in den neuen Namespace. Wie das geht, sehen Sie unter [Export und Import von Registrierungen von Azure Notification Hubs als Massenvorgang](export-modify-registrations-bulk.md).

## <a name="delete-the-previous-namespace-optional"></a>Löschen des vorherigen Namespace (optional)

Nach Abschluss des Registrierungsexports aus Ihrem alten Namespace in den neuen Namespace können Sie auf Wunsch den alten Namespace löschen.

1. Wechseln Sie zum vorhandenen Namespace in der vorherigen Region.

2. Klicken Sie auf **Löschen** und geben Sie dann den Namespacenamen erneut in den Bereich **Namespace löschen** ein.

3. Klicken Sie **Löschen** unten im Bereich **Namespace löschen**.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel sind Beispiele für andere Dienste, für die ein Artikel zum Verschieben von einer Region in die andere vorhanden ist.

- [Verschieben von NSGs in eine andere Region](/azure/virtual-network/move-across-regions-nsg-portal)
- [Verschieben von öffentlichen IP-Adressen in eine andere Region](/azure/virtual-network/move-across-regions-publicip-portal)
- [Verschieben eines Speicherkontos in eine andere Region](/azure/storage/common/storage-account-move?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)
- [Regionsübergreifendes Verschieben von Ressourcen (aus Ressourcengruppe)](/azure/resource-mover/move-region-within-resource-group#:~:text=1%20In%20the%20Azure%20portal%2C%20open%20the%20relevant,you%20want%20to%20move.%20...%20More%20items...%20)
